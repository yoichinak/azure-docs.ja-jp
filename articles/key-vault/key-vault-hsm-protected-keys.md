---
title: Azure Key Vault の HSM保護キーを生成し、転送する方法 | Microsoft Docs
description: この記事は Azure Key Vault と共に使用する独自の HSM 保護キーを計画、生成、転送する際に役立ちます。 これは、BYOK (Bring Your Own Key) とも呼ばれます。
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 51abafa1-812b-460f-a129-d714fdc391da
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: barclayn
ms.openlocfilehash: 774fd4ca6bbae0d02f5733269f091d325f4c776d
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038548"
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Azure Key Vault の HSM 保護キーを生成し、転送する方法
## <a name="introduction"></a>はじめに
Azure Key Vault の使用時にさらに安心感を高める場合、ハードウェア セキュリティ モジュール (HSM) でキーをインポートしたり、生成したりできます。キーは HSM の境界内から出ることはありません。 このシナリオは、多くの場合、*Bring Your Own Key* または BYOK と呼ばれています。 HSM は、FIPS 140-2 レベル 2 で検証済みです。 Azure Key Vault は HSM の Thales nShield ファミリを使用してキーを保護します。

このトピックの情報は Azure Key Vault と共に使用する独自の HSM 保護キーを計画、生成、転送する際に役立ちます。

この機能は Azure China では使用できません。

> [!NOTE]
> Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](key-vault-whatis.md)  
>
> HSM 保護キーの Key Vault 作成を含む入門チュートリアルについては、「 [Azure Key Vault の概要](key-vault-get-started.md)」を参照してください。
>
>

HSM 保護キーを生成し、インターネットで転送する方法:

* 攻撃を減らすオフライン ワークステーションからキーを生成します。
* キーは Key Exchange Key (KEK) で暗号化されます。Azure Key Vault HSM に転送されるまで暗号化が維持されます。 暗号化されたキーだけが元のワークステーションを離れます。
* ツールセットにより、キーを Azure Key Vault セキュリティ ワールドにバインディングするテナント キーのプロパティが設定されます。 そのため、Azure Key Vault HSM がキーを受け取り、復号化すると、これらの HSM のみでそれを使用できます。 キーはエクスポートできません。 このバインディングは Thales HSM により適用されます。
* キーの暗号化に使用される Key Exchange Key (KEK) は Azure Key Vault HSM 内で生成され、エクスポートできません。 HSM により、HSM の外部では平文の KEK がありません。 また、ツールセットには、KEK はエクスポート不可であり、Thales で作成された本物の HSM 内で生成されたことを示す Thales からの証明が含まれます。
* ツールセットには、Azure Key Vault セキュリティ ワールドも Thales で作成された本物の HSM 内で生成されたことを示す Thales からの証明が含まれます。 これにより、Microsoft が本物のハードウェアを使用していることが証明されます。
* Microsoft では、リージョンごとに個別の KEK と個別のセキュリティ ワールドを使用します。 この分離により、キーを暗号化したリージョンのデータ センターでのみ、そのキーを使用できるようにします。 たとえば、欧州のお客様からのキーは北米やアジアのデータ センターでは使用できません。

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>Thales HSM と Microsoft のサービスの詳細
Thales e-Security は、金融サービス、ハイテク、製造、政府、テクノロジ セクターにデータ暗号化とサイバー セキュリティのソリューションを提供することで世界をリードする企業です。 企業と政府の情報を 40 年間保護してきた実績を持つ Thales ソリューションは、エネルギー/航空宇宙産業の大企業 5 社のうち 4 社で採用されています。 このソリューションは、22 の NATO 加盟国でも利用されており世界中の支払処理の 80% 以上を保護しています。

Microsoft は Thales と連携し、HSM の最新技術を強化しています。 この改良により、キーの管理を放棄することなく、ホステッド サービスの一般的な長所を得ることができます。 具体的には、この改良により、Microsoft があなたの代わりに HSM を管理できます。 クラウド サービスとしては、Azure Key Vault は短期間でスケールアップされ、組織の利用率急増に対応します。 同時に、キーは Microsoft の HSM 内で保護されます。あなたがキーを生成し、それを Microsoft の HSM に転送するため、キーが存続する間、あなたがそれを管理します。

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Azure Key Vault の Bring Your Own Key (BYOK) の実装
独自の HSM 保護キーを生成し、それを Azure Key Vault に転送する場合 (Bring Your Own Key (BYOK) シナリオ)、次の情報と手順を利用します。

## <a name="prerequisites-for-byok"></a>BYOK の前提条件
Azure Key Vault の Bring Your Own Key (BYOK) の前提条件の一覧については、次の表を参照してください。

| 要件 | 詳細情報 |
| --- | --- |
| Azure のサブスクリプション |Azure Key Vault を作成するには、Azure サブスクリプションが必要です: [無料試用版に登録する](https://azure.microsoft.com/pricing/free-trial/) |
| HSM で保護されたキーをサポートする Azure Key Vault Premium サービス レベル |Azure Key Vault のサービス層と機能に関する詳細については、 [Azure Key Vault 価格](https://azure.microsoft.com/pricing/details/key-vault/) Web サイトを参照してください。 |
| Thales HSM、スマート カード、サポート ソフトウェア |Thales ハードウェア セキュリティ モジュールにアクセスできることと Thales HSM の基本操作知識が必要です。 互換性のあるモデルの一覧については、あるいは所有していない場合に HSM を購入する方法については、「 [Thales ハードウェア セキュリティ モジュール](https://www.thales-esecurity.com/msrms/buy) 」を参照してください。 |
| 次のハードウェアとソフトウェア:<ol><li>Windows 7 以降のオペレーティング システムと、バージョン 11.50 以降の Thales nShield ソフトウェアを搭載したオフラインの x64 ワークステーション。<br/><br/>ワークステーションで Windows 7 を実行する場合は、まず [Microsoft .NET Framework 4.5 をインストール](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe)する必要があります。</li><li>インターネットに接続している、Windows 7 以降および [Azure PowerShell](/powershell/azure/overview) **1.1.0 以降**の Windows オペレーティング システムがインストールされたワークステーション。</li><li>USB ドライブまたは 16 MB 以上の空き領域を持つその他のポータブル ストレージ デバイス。</li></ol> |セキュリティ上の理由から、最初のワークステーションをネットワークに接続しないことをお勧めします。 ただし、プログラムを使用して強制的に接続が切断されることはありません。<br/><br/>次の手順では、このワークステーションを「未接続ワークステーション」と呼んでいることにご注意ください。</p></blockquote><br/>さらに、テナント キーが実稼動ネットワークにある場合は、別の 2 台目のワークステーションを使用してツールセットをダウンロードし、テナント キーをアップロードすることを勧めします。 ただし、テスト目的で1 台目のワークステーションとして同じワークステーションを使用できます。<br/><br/>次の手順では、このワークステーションを「インターネット接続ワークステーション」と呼んでいることにご注意ください。</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>キーを生成し、Azure Key Vault HSM に転送する
次の 5 つの手順でキーを生成し、Azure Key Vault HSM に転送します。

* [手順 1: インターネット接続ワークステーションを準備する](#step-1-prepare-your-internet-connected-workstation)
* [手順 2: 未接続ワークステーションを準備する](#step-2-prepare-your-disconnected-workstation)
* [手順 3: キーを生成する](#step-3-generate-your-key)
* [手順 4: キーの転送準備をする](#step-4-prepare-your-key-for-transfer)
* [手順 5: キーを Azure Key Vault に転送する](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>手順 1: インターネット接続ワークステーションを準備する
この最初の手順では、インターネットに接続されているワークステーションで次の手順を実行します。

### <a name="step-11-install-azure-powershell"></a>手順 1.1: Azure PowerShell をインストールする
インターネット接続ワークステーションから、Azure Key Vault を管理するためのコマンドレットを含む Azure PowerShell をダウンロードしてインストールします。 この作業には 0.8.13 以降のバージョンが必要です。

インストール指示については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」を参照してください。

### <a name="step-12-get-your-azure-subscription-id"></a>手順 1.2: Azure サブスクリプション ID を取得する
Azure PowerShell セッションを開始し、次のコマンドで Azure アカウントにサインインします。

```Powershell
   Add-AzureAccount
```
ポップアップ ブラウザー ウィンドウで、Azure アカウントのユーザー名とパスワードを入力します。 次に [Get-azuresubscription](/powershell/module/servicemanagement/azure/get-azuresubscription?view=azuresmps-3.7.0) コマンドを使用します。

```powershell
   Get-AzureSubscription
```
出力から、Azure Key Vault で使用するサブスクリプションの ID を見つけます。 このサブスクリプション ID は後で必要になります。

Azure PowerShell ウィンドウを閉じないでください。

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>手順 1.3: Azure Key Vault の BYOK ツールセットをダウンロードする
Microsoft ダウンロード センターにアクセスし、自分の地域リージョンまたは Azure のインスタンスの [Azure Key Vault BYOK ツールセットをダウンロード](http://www.microsoft.com/download/details.aspx?id=45345) します。 以下の情報を利用して、ダウンロードするパッケージの名前とそれに対応する SHA-256 パッケージ ハッシュを特定してください。

- - -
**米国:**

KeyVault-BYOK-Tools-UnitedStates.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

- - -
**ヨーロッパ:**

KeyVault-BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

- - -
**アジア:**

KeyVault-BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

- - -
**ラテン アメリカ:**

KeyVault-BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

- - -
**日本:**

KeyVault-BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

- - -
**韓国:**

KeyVault-BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

- - -
**オーストラリア:**

KeyVault-BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

- - -
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

- - -
**米国防総省:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

- - -
**カナダ:**

KeyVault-BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

- - -
**ドイツ:**

KeyVault-BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

- - -
**インド:**

KeyVault-BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

- - -
**フランス:**

KeyVault-BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

- - -
**イギリス:**

KeyVault-BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

- - -

ダウンロードした BYOK ツールセットの整合性を検証するには、Azure PowerShell セッションから、 [Get FileHash](https://technet.microsoft.com/library/dn520872.aspx) コマンドレットを使用します。

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

ツールセットには次が含まれます。

* Key Exchange Key (KEK) パッケージ。この名前は「**BYOK-KEK-pkg-**」から始まります。
* セキュリティ ワールド パッケージ。この名前は「**BYOK-SecurityWorld-pkg-**」から始まります。
* **verifykeypackage.py** という名前の Python スクリプト。
* 「 **KeyTransferRemote.exe** 」という名前のコマンドライン実行可能ファイルと関連 DLL。
* 「**vcredist_x64.exe**」という名前の Visual C++ 再配布可能パッケージ。

USB ドライブまたはその他のポータブル ストレージにパッケージをコピーします。

## <a name="step-2-prepare-your-disconnected-workstation"></a>手順 2: 未接続ワークステーションを準備する
この 2 つ目の手順では、ネットワーク (インターネットまたは内部ネットワーク) に接続されていないワークステーションで次の手順を実行します。

### <a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>手順 2.1: Thales HSM で未接続ワークステーションを準備する
Windows コンピューターに nCipher (Thales) サポート コンピューターをインストールし、そのコンピューターに Thales HSM をアタッチします。

Thales ツールがパスにあることを確認します (**%nfast_home%\bin**)。 たとえば、次を入力します。

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

詳細については、Thales HSM に付属のユーザー ガイドを参照してください。

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>手順 2.2: 未接続ワークステーションに BYOK ツールセットをインストールします。
USB ドライブまたはその他のポータブル ストレージから BYOK ツールセット パッケージをコピーし、次の操作します。

1. ダウンロードしたパッケージから任意のフォルダーにファイルを抽出します。
2. そのフォルダーから vcredist_x64.exe を実行します。
3. 指示に従い、Visual Studio 2013 用の Visual C++ ランタイム コンポーネントをインストールします。

## <a name="step-3-generate-your-key"></a>手順 3: キーを生成する
この 3 つ目の手順では、未接続ワークステーションで次の手順を実行します。 この手順を実行するには、HSM は初期化モードである必要があります。 


### <a name="step-31-change-the-hsm-mode-to-i"></a>手順 3.1: HSM モードを "I" に変更する
Thales nShield Edge を使用している場合、モードを変更するには、次に手順を実行します。1.  モード ボタンを使用して、必要なモードを強調表示します。 2. 数秒以内に、[クリア] ボタンを数秒押したままにします。 モードが変更されると、新しいモードの LED の点滅が止まり、点灯したままになります。 ステータス LED は、数秒間、不規則に点滅することがあります。その後、デバイスの準備が完了すると定期的に点滅します。 それ以外の場合、デバイスは現在のモードのままになり、適切なモード LED が点灯します。

### <a name="step-32-create-a-security-world"></a>手順 3.2: セキュリティ ワールドを作成する
コマンド プロンプトを起動し、Thales new-world プログラムを実行します。

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3
   ```

このプログラムにより **Security World** ファイルが %NFAST_KMDATA%\local\world で作成されます。これは C:\ProgramData\nCipher\Key Management Data\local フォルダーに対応します。 クォーラムにはさまざまな値を使用できますが、今回の例では、3 枚の空白カードと各カードのピンを入力するように求められます。 いずれかの 2 枚のカードがセキュリティ ワールドに完全アクセスを与えます。 その 2 枚のカードが新しいセキュリティ ワールドの**管理者カード セット**になります。

次に、次を実行します。

* ワールド ファイルをバックアップします。 ワールド ファイル、管理者カード、そのピンを保護します。1 人の人間が複数のカードにアクセスできないようにします。

### <a name="step-33-change-the-hsm-mode-to-o"></a>手順 3.3: HSM モードを "O" に変更する
Thales nShield Edge を使用している場合、モードを変更するには、次に手順を実行します。1.  モード ボタンを使用して、必要なモードを強調表示します。 2. 数秒以内に、[クリア] ボタンを数秒押したままにします。 モードが変更されると、新しいモードの LED の点滅が止まり、点灯したままになります。 ステータス LED は、数秒間、不規則に点滅することがあります。その後、デバイスの準備が完了すると定期的に点滅します。 それ以外の場合、デバイスは現在のモードのままになり、適切なモード LED が点灯します。


### <a name="step-34-validate-the-downloaded-package"></a>手順 3.4: ダウンロードしたパッケージを検証する
この手順は省略可能ですが、次を検証できるので推奨されます。

* ツールセットに含まれる Key Exchange Key が本物の Thales HSM から生成されていること。
* ツールセットに含まれているセキュリティ ワールドのハッシュが本物の Thales HSM から生成されていること。
* Key Exchange Key がエクスポート不可であること。

> [!NOTE]
> ダウンロードしたパッケージを検証するには、HSM を接続し、電源を入れる必要があります。また、(たとえば、あなたが先ほど作成した) セキュリティ ワールドが入っている必要があります。
>
>

ダウンロードしたパッケージを検証するには:

1. 地域リージョンまたは Azure のインスタンスに応じて、次のいずれかを入力して、verifykeypackage.py スクリプトを実行します。

   * 北米:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * ヨーロッパ:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * アジア:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * ラテン アメリカ:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * 日本:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * 韓国:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * オーストラリア:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Azure の米国政府インスタンスを使用する [Azure Government](https://azure.microsoft.com/features/gov/)の場合:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * 米国防総省:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * カナダの場合:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * ドイツの場合:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * インドの場合:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * フランスの場合:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * イギリスの場合:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1

     > [!TIP]
     > Thales ソフトウェアの %NFAST_HOME%\python\bin に Python が含まれています。
     >
     >
2. 次の表示を確認します。これは検証の成功を示します: **Result: SUCCESS**

このスクリプトは Thales ルート キーまで署名者のチェーンを検証します。 このルート キーのハッシュがスクリプトに埋め込まれており、その値は **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**になります。 [Thales Web サイト](http://www.thalesesec.com/)にアクセスすると、この値を個別に確認できます。

これで新しいキーを作成する準備が整いました。

### <a name="step-35-create-a-new-key"></a>手順 3.5: 新しいキーを作成する
Thales **generatekey** プログラムを利用してキーを生成します。

次のコマンドを実行してキーを生成します。

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

このコマンドを実行するとき、次の指示に従います。

* *protect* パラメーターの値は、コマンドに示したように **module**に設定する必要があります。 module に設定すると、モジュールで保護されたキーが作成されます。 BYOK ツールセットは、OCS で保護されたキーをサポートしていません。
* **ident** と **plainname** の *contosokey* の値を文字列値に置換します。 管理費を最小限に抑え、エラーを犯す可能性を減らすために、両方に同じ値を使用することをお勧めします。 **Ident** 値には数字、ダッシュ、小文字のみを使用できます。
* pubexp はこの例では空白のまま (既定) ですが、特定の値を指定できます。 詳細については、Thales の文書を参照してください。

このコマンドにより %NFAST_KMDATA%\local フォルダーにトークン化されたキーのファイルが作成されます。この名前は "**key_simple_**" で始まり、コマンドで指定した **ident** が続きます。 (例: **key_simple_contosokey**)。 このファイルには暗号化されたキーが含まれます。

安全な場所でこのトークン化されたキーのファイルをバックアップします。

> [!IMPORTANT]
> 後で Azure Key Vault にキーを転送すると、Microsoft はこのキーをあなたの元にエクスポートできません。そのため、キーとセキュリティ ワールドを安全にバックアップすることが極めて重要です。 キーのバックアップ方法と最良事例については Thales にお問い合わせください。
>
>

これで Azure Key Vault にキーを転送する準備ができました。

## <a name="step-4-prepare-your-key-for-transfer"></a>手順 4: キーの転送準備をする
この 4 つ目の手順では、未接続ワークステーションで次の手順を実行します。

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>手順 4.1: アクセス権が制限されたキーのコピーを作成します。

新しいコマンド プロンプトを開き、現在のディレクトリを、BYOK zip ファイルを解凍した場所に変更します。 キーのアクセス権を制限するには、地域リージョンまたは Azure のインスタンスによって、コマンド プロンプトから次のいずれかを実行します。

* 北米:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* ヨーロッパ:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* アジア:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* ラテン アメリカ:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* 日本:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* 韓国:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* オーストラリア:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Azure の米国政府インスタンスを使用する [Azure Government](https://azure.microsoft.com/features/gov/)の場合:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* 米国防総省:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* カナダの場合:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* ドイツの場合:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* インドの場合:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* フランスの場合:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* イギリスの場合:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1

このコマンドを実行するとき、*contosokey* を、「**キーを生成する**」の「[手順 3.5: 新しいキーを作成する](#step-3-generate-your-key)」で指定した値に置き換えます。

セキュリティ ワールドの管理者カードを差し込むように求められます。

コマンドが完了すると、**Result: SUCCESS** と表示され、アクセス権が制限されたキーのコピーが "key_xferacId_<contosokey>" という名前のファイルに表示されます。

Thales ユーティリティを使用すると、次のコマンドで ACL を確認できます。

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  このコマンドを実行するとき、contosokey を、「[キーを生成する](#step-3-generate-your-key)」の「**手順 3.5: 新しいキーを作成する**」で指定した値に置き換えます。

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>手順 4.2: Microsoft の Key Exchange Key を使用してキーを暗号化する
地域リージョンまたは Azure のインスタンスによって、次のいずれかのコマンドを実行します。

* 北米:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* ヨーロッパ:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* アジア:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* ラテン アメリカ:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 日本:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 韓国:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* オーストラリア:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Azure の米国政府インスタンスを使用する [Azure Government](https://azure.microsoft.com/features/gov/)の場合:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* 米国防総省:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* カナダの場合:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* ドイツの場合:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* インドの場合:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* フランスの場合:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* イギリスの場合:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

このコマンドを実行するとき、次の指示に従います。

* *contosokey* を、「**キーを生成する**」の「[手順 3.5: 新しいキーを作成する](#step-3-generate-your-key)」でキーの生成に使用した ID で置き換えます。
* *SubscriptionID* を Key Vault が含まれる Azure サブスクリプションの ID で置換します。 この値は先に、 **インターネット接続ワークステーションの準備** 手順の「 [手順 1.2: Azure サブスクリプション ID を取得する](#step-1-prepare-your-internet-connected-workstation) 」で取得しました。
* *ContosoFirstHSMKey* を出力ファイル名に使用するラベルで置換します。

完了すると、**Result: SUCCESS** と表示され、"KeyTransferPackage-*ContosoFirstHSMkey*.byok" という名前の新しいファイルが現在のフォルダーに表示されます

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>手順 4.3: キー転送パッケージをインターネット接続ワークステーションにコピーする
USB ドライブまたはその他のポータブル ストレージを使用し、前の手順の出力ファイル (KeyTransferPackage-ContosoFirstHSMkey.byok) をインターネット接続ワークステーションにコピーします。

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>手順 5: キーを Azure Key Vault に転送する
この最後の手順では、インターネット接続ワークステーションで、[Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) コマンドレットを使用し、未接続ワークステーションからコピーしたキー転送パッケージを Azure Key Vault HSM にアップロードします。

   ```powershell
        Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

アップロードされると、追加したキーのプロパティが表示されます。

## <a name="next-steps"></a>次の手順
これでこの HSM 保護キーを Key Vault で使用できます。 詳細については、 **Azure Key Vault の概要** のチュートリアルの「 [ハードウェア セキュリティ モジュール (HSM) を使用する場合](key-vault-get-started.md) 」セクションを参照してください。
