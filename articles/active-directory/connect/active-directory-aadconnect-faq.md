---
title: Azure Active Directory Connect に関する FAQ | Microsoft Docs
description: この記事では、Azure AD Connect についてよく寄せられる質問に回答します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4f3ffb3c769adeabd1edc10ce064653f7b336330
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42144621"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect に関する FAQ

## <a name="general-installation"></a>一般的なインストール
**Q: Azure Active Directory (Azure AD) 全体管理者が 2 要素認証 (2FA) を有効にしている場合、インストールは機能しますか。**  
2016 年 2 月のビルド時点で、このシナリオがサポートされています。

**Q: Azure AD Connect を無人インストールする方法はありますか。**  
インストール ウィザードを使用する場合にのみ、Azure AD Connect のインストールはサポートされます。 サイレント モードでの無人インストールはサポートされていません。

**Q: ドメインに接続できないフォレストがあります。Azure AD Connect をインストールにはどうすればよいですか。**  
2016 年 2 月のビルド時点で、このシナリオがサポートされています。

**Q: Azure Active Directory Domain Services (Azure AD DS) ヘルス エージェントはサーバー コア上で動作しますか。**  
はい。 エージェントをインストールした後、次の PowerShell コマンドレットを使って登録プロセスを実行できます。 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Q: Azure AD Connect では、2 つのドメインから Azure AD への同期がサポートされていますか。**  
はい、このシナリオはサポートされています。 [複数のドメイン](active-directory-aadconnect-multiple-domains.md)に関するページを参照してください。
 
**Q: Azure AD Connect では同じ Active Directory ドメインに対して複数のコネクタを使用できますか。**  
いいえ、同じ AD ドメインの複数のコネクタはサポートされていません。 

**Q: Azure AD Connect データベースをローカル データベースからリモート SQL Server インスタンスに移行できますか。**   
はい、次の手順では、この移行方法に関する一般的なガイダンスを提供しています。 現在、より詳細なドキュメントの作成に取り組んでいます。
1. LocalDB ADSync データベースをバックアップします。
この最も簡単な方法は、Azure AD Connect と同じコンピューターにインストールされている SQL Server Management Studio を使用することです。 *(localdb)\.\ADSync* に接続し、ADSync データベースをバックアップします

2. リモート SQL Server インスタンスに ADSync データベースを復元します

3. 既存の[リモート SQL Database](active-directory-aadconnect-existing-database.md) に対して Azure AD Connect をインストールします。
   この記事では、ローカルの SQL Database を使用して移行する方法について説明します。 リモート SQL Database を使用して移行する場合は、手順 5 で Windows Sync サービスを実行する既存のサービス アカウントも入力する必要があります。 この同期エンジンのサービス アカウントを次に示します。
   
      **既存のサービス アカウントを使用する**: 既定では、同期サービスで使用する仮想サービス アカウントが Azure AD Connect によって使用されます。 リモート SQL Server インスタンスを使用する場合、または認証が必要なプロキシを使用する場合は、ドメイン内のマネージド サービス アカウントまたはサービス アカウントを使用し、パスワードを知っている必要があります。 このような場合は、使用するアカウントを入力します。 サービス アカウントのログイン資格情報を作成できるように、インストールを実行しているユーザーが SQL のシステム管理者であることを確認します。 詳細については、[Azure AD Connect アカウントとアクセス許可](active-directory-aadconnect-accounts-permissions.md#adsync-service-account)に関するページを参照してください。 
   
      最新のビルドでは、SQL 管理者が帯域外でデータベースのプロビジョニングを実行し、データベース所有者権限を持つ Azure AD Connect 管理者がインストールできます。 詳細については、「[SQL によって委任された管理者のアクセス許可を使用した Azure AD Connect のインストール](active-directory-aadconnect-sql-delegation.md)」を参照してください。

シンプルにするために、Azure AD Connect をインストールするユーザーは、SQL のシステム管理者にすることをお勧めします。 ただし、最近のビルドでは、「[SQL によって委任された管理者のアクセス許可を使用した Azure AD Connect のインストール](active-directory-aadconnect-sql-delegation.md)」で説明されているように、委任された SQL 管理者を使用できるようになりました。

## <a name="network"></a>ネットワーク
**Q: ファイアウォールやネットワーク デバイスなど、ネットワーク上で接続を開いたままにすることができる時間を制限するものがあります。Azure AD Connect を使用する場合、クライアント側のタイムアウトしきい値はどのくらいにすればいいでしょうか。**  
すべてのネットワーク ソフトウェアや物理デバイスなど、接続を開ける最大時間を制限するものは、Azure AD Connect クライアントがインストールされているサーバーと Azure Active Directory 間の接続に対して少なくとも 5 分 (300 秒) のしきい値を使用する必要があります。 この推奨事項は、以前リリースされたすべての Microsoft ID 同期ツールにも適用されます。

**Q: シングル ラベル ドメイン (SLD) はサポートされていますか。**  
シングル ラベル ドメインのネットワーク構成が適切に機能している限り、このネットワーク構成に対しては、シングル ラベル ドメインを利用した Azure AD Connect 同期の利用がサポートされることを強くお勧めします ([記事を参照](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains))。

**Q: 切り離された AD ドメインを持つフォレストはサポートされますか。**  
いいえ。Azure AD Connect は、切り離された名前空間を持つオンプレミスのフォレストはサポートしていません。

**Q: "ドット形式" の NetBios 名はサポートされていますか。**  
いいえ。Azure AD Connect では、NetBios 名にドット (.) が含まれているオンプレミスのフォレストまたはドメインをサポートしていません。

**Q: 純粋な IPv6 環境はサポートされますか。**  
いいえ。Azure AD Connect は、純粋な IPv6 環境はサポートしていません。

## <a name="federation"></a>フェデレーション
**Q: Office 365 の証明書を更新するように求める電子メールを受け取った場合はどうすればいいですか。**  
証明書の更新方法については、[証明書の更新](active-directory-aadconnect-o365-certs.md)に関する記事を参照してください。

**Q: Office 365 証明書利用者の "証明書利用者の自動更新" を設定しました。トークン署名証明書が自動的にロールオーバーされるときに、何か必要な操作はありますか。**  
[証明書の更新](active-directory-aadconnect-o365-certs.md)に関する記事に記載されているガイダンスに従ってください。

## <a name="environment"></a>環境
**Q: Azure AD Connect のインストール後のサーバー名の変更はサポートされていますか。**  
いいえ。 サーバー名を変更すると、同期エンジンは SQL Database インスタンスに接続できなくなり、サービスを開始できなくなります。

## <a name="identity-data"></a>ID データ
**Q: Azure AD の userPrincipalName (UPN) 属性がオンプレミス UPN と一致しないのはなぜですか。**  
詳細については、以下の記事を参照してください。

* [Office 365、Azure、Intune におけるユーザー名が、オンプレミスの UPN または代替ログイン ID と一致しない](https://support.microsoft.com/kb/2523192)
* [異なるフェデレーション ドメインを使用するようにユーザー アカウントの UPN を変更した後、Azure Active Directory 同期ツールによって変更が同期されない](https://support.microsoft.com/kb/2669550)

また、「[Azure AD Connect 同期サービスの機能](active-directory-aadconnectsyncservice-features.md)」に記載された手順に従って、同期エンジンによる UPN の更新が許可されるように Azure AD を構成することもできます。

**Q: オンプレミスの Azure AD グループまたは連絡先オブジェクトと既存の Azure AD グループまたは連絡先オブジェクトとのあいまい一致はサポートされていますか。**  
はい。このあいまい一致は proxyAddress に基づいています。 メールが有効でないグループに対して、あいまい一致はサポートされていません。

**Q: 既存の Azure AD グループまたは連絡先オブジェクトに対して手動で設定された ImmutableId 属性とオンプレミスの Azure AD グループまたは連絡先オブジェクトとの完全一致はサポートされていますか。**  
いいえ。既存の Azure AD グループまたは連絡先オブジェクトに対して手動で設定された ImmutableId 属性の完全一致は現在サポートされていません。

## <a name="custom-configuration"></a>カスタム構成
**Q: Azure AD Connect 用の PowerShell コマンドレットのドキュメントはどこにありますか。**  
このサイトに記載されているコマンドレットを除き、Azure AD Connect で使用されている PowerShell コマンドレットは、ユーザーによる使用をサポートしていません。

**Q: Synchronization Service Manager の [サーバーのエクスポート/インポート] オプションを使用して、サーバー間で構成を移動できますか。**  
いいえ。 このオプションはすべての構成設定を取得しないため、使用すべきではありません。 代わりに、2 台目のサーバーでウィザードを使用して基本構成を作成し、同期ルール エディターを使用して PowerShell スクリプトを生成し、サーバー間でカスタム ルールを移動してください。 詳細については、「[スウィング移行](active-directory-aadconnect-upgrade-previous-version.md#swing-migration)」を参照してください。

**Q: Azure サインイン ページではパスワードがキャッシュされますか。また、パスワード入力要素の *autocomplete 属性を "false"* に設定することで、このキャッシュを防ぐことはできますか。**  
現在、オートコンプリート タグを含め、**[パスワード]** フィールドの HTML 属性を変更することはできません。 現在、**[パスワード]** フィールドに属性を追加できるようカスタム Javascript を許可する機能の開発に取り組んでいます。

**Q: Azure サインイン ページでは、以前正常にサインインしたユーザーのユーザー名が表示されますか。また、この動作は無効にできますか。**  
現在、オートコンプリート タグを含め、**[パスワード]** 入力フィールドの HTML 属性を変更することはできません。 現在、**[パスワード]** フィールドに属性を追加できるようカスタム Javascript を許可する機能の開発に取り組んでいます。

**Q: 同時セッションを防ぐ方法はありますか。**  
いいえ。

## <a name="auto-upgrade"></a>自動アップグレード

**Q: 自動アップグレードを使用した場合の利点と結果について教えてください。**  
Microsoft では、すべてのお客様に対し、Azure AD Connect インストールの自動アップグレードを有効にするようお勧めしています。 自動アップグレードの利点は、常に最新の修正プログラムを適用できることです (Azure AD Connect で確認された脆弱性に対応するセキュリティ更新プログラムなど)。 アップグレード プロセスは簡単で、新しいバージョンがリリースされ次第、自動的に実行されます。 Azure AD Connect の何千ものお客様が、新しいリリースのたびに自動アップグレードを使用しています。

自動アップグレード プロセスでは、まずインストールが自動アップグレードの対象かどうかを確認します。 対象の場合は、アップグレードが実行され、テストされます。 このプロセスには、ルールおよび特定の環境要因に対するカスタム変更を検索する処理も含まれています。 テストの結果、アップグレードが失敗していた場合は、旧バージョンが自動的に復元されます。

環境の規模によっては、処理に数時間かかることがあります。 アップグレードの実行中は、Windows Server Active Directory と Azure AD 間の同期は実行されません。

**Q: 自動アップグレードが動作しなくなっているため、新しいバージョンをインストールする必要があるという内容の電子メールが送られてきました。なぜそうしなければならないのですか。**  
昨年リリースされた Azure AD Connect のバージョンでは、特定の状況において、自動アップグレード機能がサーバー上で無効になることがあります。 この問題は、Azure AD Connect バージョン 1.1.750.0 で修正しました。 問題の影響を受けている場合は、PowerShell スクリプトを実行して修復するか、手動で最新バージョンの Azure AD Connect にアップグレードすることで軽減できます。 

PowerShell スクリプトを実行するには、[スクリプトをダウンロード](https://aka.ms/repairaadconnect)して、管理 PowerShell ウィンドウの Azure AD Connect サーバー上で実行します。 スクリプトの実行方法については、[この短い動画](https://aka.ms/repairaadcau)をご覧ください。

手動でアップグレードするには、AADConnect.msi ファイルの最新バージョンをダウンロードし、実行する必要があります。
 
-  現在のバージョンが 1.1.750.0 よりも前の場合は、[最新バージョン](https://www.microsoft.com/download/details.aspx?id=47594)をダウンロードしてアップグレードしてください。
- Azure AD Connect のバージョンが 1.1.750.0 以降の場合、以降の操作は必要ありません。 自動アップグレードの修正プログラムを含むバージョンを既に使用しています。 

**Q: 最新バージョンにアップグレードして、自動アップグレードを再度有効にするよう求める内容の電子メールが送られてきました。バージョン 1.1.654.0 を使用しています。アップグレードする必要はありますか。**  
はい。バージョン 1.1.750.0 以降にアップグレードして、自動アップグレードを再度有効にする必要があります。 [最新バージョンをダウンロードしてアップグレード](https://www.microsoft.com/download/details.aspx?id=47594)してください。

**Q: 最新バージョンにアップグレードして、自動アップグレードを再度有効にするよう求める内容の電子メールが送られてきました。PowerShell を使用して自動アップグレードを有効にしたのですが、最新バージョンをインストールする必要があるのでしょうか。**  
はい。1.1.750.0 以降のバージョンにアップグレードする必要があります。 PowerShell を使用して自動アップグレード サービスを有効にした場合、1.1.750.0 より前のバージョンで見つかった自動アップグレードの問題は回避されません

**Q: 新しいバージョンにアップグレードする必要があるのですが、Azure AD Connect を誰がインストールしたのかわからないため、ユーザー名とパスワードがわかりません。これらの情報は必要ですか。**
最初に Azure AD Connect をアップグレードするときに使用されたユーザー名とパスワードを知っている必要はありません。 全体管理者ロールを持つ任意の Azure AD アカウントを使用してください。

**Q: 現在使用している Azure AD Connect のバージョンはどうすれば確認できますか。**  
サーバーにインストールされている Azure AD Connect のバージョンを確認するには、コントロール パネルに移動し、次のように **[プログラム]** > **[プログラムと機能]** の順に選択して、インストールされている Microsoft Azure AD Connect のバージョンを調べてください。

![コントロール パネルの Azure AD Connect のバージョン](media/active-directory-aadconnect-faq/faq1.png)

**Q: 最新のバージョンの Azure AD Connect にアップグレードするにはどうすればよいですか。**  
最新バージョンにアップグレードする方法については、「[Azure AD Connect: 旧バージョンから最新バージョンにアップグレードする](active-directory-aadconnect-upgrade-previous-version.md)」を参照してください。 

**Q: 昨年、Azure AD Connect を最新バージョンにアップグレードしたのですが、再度アップグレードする必要はありますか。**  
Azure AD Connect チームは、このサービスを頻繁に更新しています。 バグの修正プログラムやセキュリティ更新プログラムだけでなく、新機能を利用するには、サーバーを最新バージョンの状態に保つことが重要です。 自動アップグレードを有効にしておけば、ソフトウェアのバージョンは自動的に更新されます。 Azure AD Connect のバージョン リリース履歴を確認するには、「[Azure AD Connect: バージョンのリリース履歴](active-directory-aadconnect-version-history.md)」を参照してください。

**Q: アップグレードにかかる時間と、ユーザーへの影響について教えてください。**  
アップグレードに必要な時間は、テナントのサイズによって変わります。 大規模な組織の場合は、夜間や週末にアップグレードを実行することをお勧めします。 アップグレード中は、同期アクティビティは実行されません。

**Q: Azure AD Connect のアップグレードは行ったと思うのですが、Office ポータルにはまだ DirSync と表示されます。なぜですか?**  
Office チームでは、Office ポータルに現在の製品名が反映されるようにするべく取り組んでいます。 現状では、ご使用の同期ツールは反映されません。

**Q: 自動アップグレードの状況に "中断" と表示されています。なぜ中断されているのでしょうか。自分で有効化する必要があるのでしょうか。**  
以前のバージョンでは、特定の状況において、自動アップグレードの状態が "中断" に設定されたままになるというバグが確認されています。 技術的には手動で有効にすることはできますが、いくつかの複雑な手順が必要です。 Azure AD Connect の最新バージョンをインストールすることをお勧めします。

**Q: 私の会社では変更管理の要件が厳しく設定されているため、変更がプッシュアウトされるタイミングを制御したいのですが、自動アップグレードが起動されるタイミングを制御することはできますか。**  
いいえ。現在、そのような機能はありません。 この機能は、今後のリリースのために評価されています。

**Q: 自動アップグレードが失敗した場合、電子メールによる通知は送られてきますか。アップグレードが成功したかどうかを確認する方法について教えてください。**  
アップグレードの結果は通知されません。 この機能は、今後のリリースのために評価されています。

**自動アップグレードのプッシュ アウト予定に関するタイム ラインは公表されていますか。**  
自動アップグレードは、新しいバージョンのリリース プロセスの第一歩です。 新しいリリースがあるたびに、アップグレードが自動的にプッシュされます。 Azure AD Connect の新バージョンについては、[Azure AD のロードマップ](../fundamentals/whats-new.md)に関するページで事前に発表されます。

**Q: 自動アップグレードでは Azure AD Connect Health もアップグレードされますか。**  
はい、自動アップグレードでは、Azure AD Connect Health もアップグレードされます

**Q: ステージング モードの Azure AD Connect サーバーも自動アップグレードされますか。**  
はい、ステージング モードの Azure AD Connect サーバーは自動アップグレードされます。

**Q: 自動アップグレードが失敗し、Azure AD Connect サーバーが起動しない場合には、どうすればよいのでしょうか。**  
まれに、アップグレードの実行後、Azure AD Connect サービスが開始されないことがあります。 そのような場合は、サーバーを再起動してください。通常はこれで問題が解決します。 それでも Azure AD Connect サービスが開始されない場合は、サポート チケットを発行してください。 詳細については、[サービス要求を作成して Office 365 サポートに問い合わせる](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)方法に関する記事を参照してください。 

**Q: Azure AD Connect を新しいバージョンにアップグレードする際のリスクがよくわかりません。アップグレードについて電話で説明を受けることはできますか。**  
新しいバージョンの Azure AD Connect にアップグレードする場合は、[サービス要求を作成して Office 365 サポートに問い合わせる](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)方法に関する記事を参照してサポート チケットを開いてください。

## <a name="troubleshooting"></a>トラブルシューティング
**Q: Azure AD Connect に関するヘルプを参照する方法を教えてください。**

[Microsoft サポート技術情報 (KB) の検索](https://www.microsoft.com/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Azure AD Connect のサポートに関する一般的な破損時補償の技術的な解決策について、KB を検索してください。

[Azure Active Directory フォーラム](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* [Azure AD コミュニティ](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)にアクセスして、技術的な質問と回答を探し、質問をすることができます。

[Azure AD のサポートを受ける](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)
