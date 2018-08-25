---
title: Azure Site Recovery で Active Directory と DNS を保護する | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して Active Directory 用のディザスター リカバリー ソリューションを実装する方法について説明します。
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/19/2018
ms.author: manayar
ms.openlocfilehash: 3a2ad35a5382394a6886ed14dcc4f659762f2833
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172240"
---
# <a name="use-azure-site-recovery-to-protect-active-directory-and-dns"></a>Azure Site Recovery を使用して Active Directory と DNS を保護する

SharePoint、Dynamics AX、SAP などのエンタープライズ アプリケーションは、Active Directory と DNS インフラストラクチャを基盤として正常な動作が実現されています。 アプリケーションのディザスター リカバリーを設定するときは、多くの場合、他のアプリケーション コンポーネントを復旧する前に Active Directory と DNS を復旧する必要があります。そうすることで、アプリケーションが正しく機能します。

[Site Recovery](site-recovery-overview.md) を使用して、Active Directory 用のディザスター リカバリー プランを作成できます。 中断が発生した場合に、フェールオーバーを開始できます。 数分以内に Active Directory を稼働させることができます。 プライマリ サイトで複数のアプリケーション (たとえば、SharePoint と SAP) 用に Active Directory をデプロイしている場合、サイト全体をフェールオーバーする必要があることがあります。 Site Recovery を使用して最初に Active Directory をフェールオーバーした後、 アプリケーションごとの復旧プランを使用して他のアプリケーションをフェールオーバーすることができます。

この記事では、Active Directory 用のディザスター リカバリー ソリューションを作成する方法について説明します。 前提条件と、フェールオーバーの手順についても説明します。 始める前に、Active Directory と Site Recovery について理解しておく必要があります。

## <a name="prerequisites"></a>前提条件

* Azure にレプリケートする場合、サブスクリプション、Azure Virtual Network、ストレージ アカウント、Recovery Services コンテナーなどの [Azure リソースを準備](tutorial-prepare-azure.md)する。
* すべてのコンポーネントの[サポート要件](site-recovery-support-matrix-to-azure.md)を確認する。

## <a name="replicate-the-domain-controller"></a>ドメイン コントローラーのレプリケート

- ドメイン コントローラーまたは DNS をホストする少なくとも 1 つの VM で、[Site Recovery レプリケーション](#enable-protection-using-site-recovery)をセットアップする必要があります。
- 環境に[複数のドメイン コントローラー](#environment-with-multiple-domain-controllers)がある場合は、ターゲット サイトで[追加のドメイン コントローラー](#protect-active-directory-with-active-directory-replication)をセットアップする必要もあります。 追加のドメイン コントローラーは、Azure またはオンプレミス データセンターに配置できます。
- アプリケーションの数が少なく、ドメイン コントローラーが 1 つしかない場合は、サイト全体をまとめてフェールオーバーするとよいでしょう。 このケースでは、Site Recovery を使用して、ターゲット サイト (Azure またはセカンダリ オンプレミス データセンター) にドメイン コントローラーをレプリケートすることをお勧めします。 レプリケートされた同じドメイン コントローラーまたは DNS 仮想マシンを、[テスト フェールオーバー](#test-failover-considerations)にも使用できます。
- - 環境に多数のアプリケーションと複数のドメイン コントローラーがある場合、または少数のアプリケーションを一度にフェールオーバーすることを検討している場合は、Site Recovery でドメイン コントローラーの仮想マシンをレプリケートすることに加え、[追加のドメイン コントローラー](#protect-active-directory-with-active-directory-replication)をターゲット サイト (Azure またはセカンダリ オンプレミス データセンター) にセットアップすることをお勧めします。 [テスト フェールオーバー](#test-failover-considerations)には、Site Recovery によってレプリケートされたドメイン コントローラーを使用できます。 フェールオーバーには、ターゲット サイト上の追加のドメイン コントローラーを使用できます。

## <a name="enable-protection-with-site-recovery"></a>Site Recovery による保護の有効化

Site Recovery を使用して、ドメイン コントローラーまたは DNS をホストする仮想マシンを保護できます。

### <a name="protect-the-vm"></a>VM の保護
Site Recovery を使用してレプリケートされたドメイン コントローラーは、[テスト フェールオーバー](#test-failover-considerations)に使用されます。 次の要件を満たしていることを確認してください。

1. ドメイン コントローラーがグローバル カタログ サーバーである。
2. ドメイン コントローラーが、テスト フェールオーバー中に必要なロールに対応する FSMO ロール所有者である。 そうでない場合、フェールオーバー後にこれらのロールを[強制する](http://aka.ms/ad_seize_fsmo)必要があります。

### <a name="configure-vm-network-settings"></a>VM のネットワーク設定の構成
ドメイン コントローラーまたは DNS をホストする仮想マシンに関して、Site Recovery で、レプリケートされる仮想マシンの **[コンピューティングとネットワーク]** 設定でネットワーク設定を構成します。 これにより、仮想マシンがフェールオーバー後に適切なネットワークに接続されます。

## <a name="protect-active-directory"></a>Active Directory の保護

### <a name="site-to-site-protection"></a>サイト間の保護
セカンダリ サイトでドメイン コントローラーを作成します。 サーバーの役割をドメイン コントローラーに昇格させる場合は、プライマリ サイト側で使用されているのと同じドメイン名を指定してください。 **Active Directory サイトとサービス** スナップインを使用して、サイトの追加先となるサイト リンク オブジェクトに対する設定を構成することができます。 サイト リンクで設定を構成することで、2 つまたはそれ以上のサイト間でレプリケーションを発生させる時間と頻度を制御できます。 詳細については、「[Scheduling replication between sites](https://technet.microsoft.com/library/cc731862.aspx)」(サイト間でのレプリケーションをスケジュールする) をご覧ください。

### <a name="site-to-azure-protection"></a>サイトと Azure 間の保護
最初に、Azure 仮想ネットワークでドメイン コントローラーを作成します。 サーバーの役割をドメイン コントローラーに昇格させる場合は、プライマリ サイト側と同じドメイン名を指定してください。

その後、Azure の DNS サーバーを使用するように仮想ネットワークの DNS サーバーを再構成します。

![Azure ネットワーク](./media/site-recovery-active-directory/azure-network.png)

### <a name="azure-to-azure-protection"></a>Azure 間の保護
最初に、Azure 仮想ネットワークでドメイン コントローラーを作成します。 サーバーの役割をドメイン コントローラーに昇格させる場合は、プライマリ サイト側と同じドメイン名を指定してください。

その後、Azure の DNS サーバーを使用するように仮想ネットワークの DNS サーバーを再構成します。

## <a name="test-failover-considerations"></a>テスト フェールオーバーの考慮事項
運用環境のワークロードに影響が生じないように、テスト フェールオーバーは、運用ネットワークから分離されたネットワークで行われます。

ほとんどのアプリケーションでは、ドメイン コントローラーまたは DNS サーバーが存在する必要があります。 そのため、アプリケーションでフェールオーバーを実行する前に、テスト フェールオーバーに使用する分離されたネットワークでドメイン コントローラーを作成する必要があります。 これを行う最も簡単な方法は、Site Recovery を使用して、ドメイン コントローラーまたは DNS をホストする仮想マシンをレプリケートすることです。 その後で、アプリケーションの復旧プランのテスト フェールオーバーを実行する前に、ドメイン コントローラー仮想マシンのテスト フェールオーバーを実行します。 その方法は次のとおりです。

1. Site Recovery を使用して、ドメイン コントローラーまたは DNS をホストする仮想マシンを[レプリケート](vmware-azure-tutorial.md)します。
2. 分離されたネットワークを作成します。 既定では、Azure に作成するすべての仮想ネットワークは、その他のネットワークから分離されます。 このネットワークの IP アドレス範囲は、運用ネットワークと同じものを使用することをお勧めします。 このネットワーク上でサイト間接続を有効化しないでください。
3. 分離されたネットワークに DNS の IP アドレスを指定します。 DNS 仮想マシンに取得させる IP アドレスを使用します。 Azure にレプリケートする場合は、フェールオーバーで使用される仮想マシンの IP アドレスを指定します。 IP アドレスを入力するには、レプリケートされる仮想マシンの **[コンピューティングとネットワーク]** 設定で、**[ターゲット IP]** 設定を選択します。

    ![Azure テスト ネットワーク](./media/site-recovery-active-directory/azure-test-network.png)

    > [!TIP]
    > Site Recovery は、仮想マシンの **[コンピューティングとネットワーク]** 設定で指定されているのと同じ IP アドレスを使用して、同じ名前のサブネットに、テスト仮想マシンの作成を試みます。 テスト フェールオーバー用に指定された Azure 仮想ネットワークで同じ名前のサブネットを使用できない場合は、アルファベット順で最初のサブネットにテスト仮想マシンが作成されます。
    >
    > ターゲット IP が選択したサブネットの一部である場合、Site Recovery は、ターゲット IP アドレスを使用してテスト フェールオーバー仮想マシンの作成を試みます。 ターゲット IP が選択したサブネットの一部でない場合、テスト フェールオーバー仮想マシンは、選択したサブネット内の使用可能な次の IP を使用して作成されます。
    >
    >

### <a name="test-failover-to-a-secondary-site"></a>セカンダリ サイトへのテスト フェールオーバー

1. 別のオンプレミス サイトにレプリケートしていて、かつ DHCP を使用している場合は、[テスト フェールオーバー用に DNS と DHCP をセットアップ](hyper-v-vmm-test-failover.md#prepare-dhcp)します。
2. 分離されたネットワークで実行されているドメイン コントローラー仮想マシンのテスト フェールオーバーを行います。 テスト フェールオーバーの実行には、ドメイン コントローラー仮想マシンにある*アプリケーション整合性*復旧ポイントのうち最新のものを使用します。
3. アプリケーションが実行されている仮想マシンを含む復旧プランのテスト フェールオーバーを実行します。
4. テストが完了したら、ドメイン コントローラー仮想マシンの*テスト フェールオーバーをクリーンアップ*します。 この手順によって、テスト フェールオーバーのために作成されたドメイン コントローラーが削除されます。


### <a name="remove-references-to-other-domain-controllers"></a>他のドメイン コントローラーへの参照の削除
テスト フェールオーバーを開始するときは、テスト ネットワークにすべてのドメイン コントローラーを含めないでください。 運用環境に存在する他のドメイン コントローラーへの参照を削除するには、場合によっては、使用しないドメイン コントローラーに [Active Directory の FSMO ロールを強制](http://aka.ms/ad_seize_fsmo)し、[メタデータのクリーンアップ](https://technet.microsoft.com/library/cc816907.aspx)を実行する必要があります。


### <a name="issues-caused-by-virtualization-safeguards"></a>仮想化のセーフガードが原因で発生する問題

> [!IMPORTANT]
> このセクションで説明する構成の一部は、標準または既定のドメイン コントローラー構成ではありません。 運用環境のドメイン コントローラーにこれらの変更を加えたくない場合は、Site Recovery のテスト フェールオーバー専用のドメイン コントローラーを作成することもできます。 そのドメイン コントローラーにのみ、これらの変更を加えます。  
>
>

Windows Server 2012 以降では、[Active Directory Domain Services (AD DS)に追加のセーフガードが組み込まれています](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)。 土台となるハイパーバイザー プラットフォームが **VM-GenerationID** に対応している場合、これらのセーフガードによって USN ロールバックから仮想ドメイン コントローラーを保護できます。 Azure は **VM-GenerationID** に対応しています。 そのため、Azure 仮想マシン上で Windows Server 2012 以降を実行しているドメイン コントローラーには、これらの追加のセーフガードが備わっています。


**VM-GenerationID** がリセットされると、AD DS データベースの **InvocationID** 値もリセットされます。 さらに、RID プールが破棄され、SYSVOL が権限なしとしてマークされます。 詳細については、[Active Directory Domain Services の仮想化の概要](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)に関するページと、「[Safely virtualizing DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/)」(DFSR の安全な仮想化) をご覧ください。

Azure にフェールオーバーすると、**VM-GenerationID** がリセットされることがあります。 **VM-GenerationID** がリセットされると、Azure でのドメイン コントローラー仮想マシンの起動時に追加のセーフガードがトリガーされます。 これが原因で、ユーザーがドメイン コントローラー仮想マシンにログインできるまでに*かなり時間がかかる*可能性があります。

このドメイン コントローラーはテスト フェールオーバー専用であるため、仮想化のセーフガードは必要ありません。 ドメイン コントローラー仮想マシンの **VM-GenerationID** が変更されないようにするには、オンプレミス ドメイン コントローラーで次の DWORD の値を **4** に変更します。


`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`


#### <a name="symptoms-of-virtualization-safeguards"></a>仮想化のセーフガードの兆候

テスト フェールオーバー後に仮想化のセーフガードがトリガーされた場合、次の兆候が 1 つ以上見られます。  

* **GenerationID** 値の変更

    ![生成 ID の変更](./media/site-recovery-active-directory/Event2170.png)

* **InvocationID** 値の変更

    ![呼び出し ID の変更](./media/site-recovery-active-directory/Event1109.png)

* SYSVOL 共有と NETLOGON 共有が使用不可

    ![SYSVOL 共有](./media/site-recovery-active-directory/sysvolshare.png)

    ![NtFrs SYSVOL](./media/site-recovery-active-directory/Event13565.png)

* DFSR データベースが削除

    ![DFSR データベースが削除](./media/site-recovery-active-directory/Event2208.png)


### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>テスト フェールオーバーの間のドメイン コントローラーの問題のトラブルシューティング

> [!IMPORTANT]
> このセクションで説明する構成の一部は、標準または既定のドメイン コントローラー構成ではありません。 運用環境のドメイン コントローラーにこれらの変更を加えたくない場合は、Site Recovery のテスト フェールオーバー専用のドメイン コントローラーを作成することもできます。 その専用ドメイン コントローラーにのみ、変更を加えます。  
>
>

1. コマンド プロンプトで次のコマンドを実行して、SYSVOL フォルダーと NETLOGON フォルダーが共有されているかどうかを確認します。

    `NET SHARE`

2. コマンド プロンプトで次のコマンドを実行して、ドメイン コントローラーが正常に機能していることを確認します。

    `dcdiag /v > dcdiag.txt`

3. 出力ログで、次のテキストを探します。 このテキストにより、ドメイン コントローラーが正常に機能していることを確認できます。

    * "passed test Connectivity"
    * "passed test Advertising"
    * "passed test MachineAccount"

上記の条件が満たされている場合、ドメイン コントローラーは正常に機能していると考えられます。 満たされていない場合は、次の手順を実行します。

1. ドメイン コントローラーの Authoritative Restore を実行します。 次の情報を考慮してください。
    * [FRS レプリケーション](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/)は推奨されませんが、FRS レプリケーションを使用している場合は、次の手順に従って Authoritative Restore を行います。 プロセスについては、「[BurFlags レジストリ キーを使用してファイル レプリケーション サービスのレプリカ セットを再初期化する](https://support.microsoft.com/kb/290762)」をご覧ください。

        BurFlags の詳細については、ブログ記事「[D2 and D4: What is it for?](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/)」(D2 と D4: 何のために使用するか?) をご覧ください。
    * DFSR レプリケーションを使用している場合は、次の手順で Authoritative Restore を行います。 プロセスについては、「[Force an authoritative and non-authoritative sync for DFSR-replicated SYSVOL (like "D4/D2" for FRS)](https://support.microsoft.com/kb/2218556)」(DFSR でレプリケートされた SYSVOL (FRS の "D4/D2" など) の権限のある/権限のない同期を強制実行する) をご覧ください。

        PowerShell 関数を使用することもできます。 詳細については、「[DFSR-SYSVOL authoritative/non-authoritative restore PowerShell functions](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/)」(DFSR-SYSVOL の権限のある/権限のない復元の PowerShell 関数) をご覧ください。

2. オンプレミス ドメイン コントローラーで次のレジストリ キーを **0** に設定して、初期同期要件をバイパスします。 この DWORD 値が存在しない場合は、**Parameters** ノードの下に作成できます。

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

    詳細については、「[Troubleshoot DNS Event ID 4013: The DNS server was unable to load AD integrated DNS zones](https://support.microsoft.com/kb/2001093)」(DNS イベント ID 4013: DNS サーバーは AD 統合された DNS ゾーンを読み込めませんでした) をご覧ください。

3. ユーザーのログインを検証するためにグローバル カタログ サーバーを利用可能にする要件を無効にします。 これを行うには、オンプレミス ドメイン コントローラーで、次のレジストリ キーを **1** に設定します。 この DWORD 値が存在しない場合は、**Lsa** ノードの下に作成できます。

    `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

    詳細については、「[Disable the requirement that a global catalog server be available to validate user logons](http://support.microsoft.com/kb/241789)」(ユーザーのログオンを有効にするためにグローバル カタログ サーバーを利用可能にする要件を無効にする) をご覧ください。

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS とドメイン コントローラーが異なるマシン上に存在する場合

同じ VM 上でドメイン コントローラーと DNS を実行している場合は、この手順を省略できます。


DNS がドメイン コントローラーと同じ VM 上にない場合は、テスト フェールオーバー用の DNS VM を作成する必要があります。 新規の DNS サーバーを使用し、必要なすべてのゾーンを作成することができます。 たとえば、Active Directory ドメインが contoso.com である場合には、contoso.com という名前で DNS ゾーンを作成することができます。 Active Directory に対応するエントリは DNS で次のように更新する必要があります。

1. 復旧計画内の他のすべての仮想マシンが起動する前に、次の設定が有効になっているようにします。
   * ゾーンは、フォレスト ルート名に従って名前を付ける必要があります。
   * ゾーンは、ファイルに格納される必要があります。
   * ゾーンは、セキュリティ保護された更新とセキュリティ保護されていない更新の両方に対して有効である必要があります。
   * ドメイン コントローラーをホストする仮想マシンのリゾルバーは、DNS 仮想マシンの IP アドレスをポイントする必要があります。

2. ドメイン コントローラーをホストする仮想マシンで、次のコマンドを実行します。

    `nltest /dsregdns`

3. 次のコマンドを実行して、DNS サーバー上にゾーンを追加し、セキュリティで保護されていない更新を許可し、そのゾーン用のエントリを DNS に追加します。

    `dnscmd /zoneadd contoso.com  /Primary`

    `dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1`

    `dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>`

    `dnscmd /config contoso.com /allowupdate 1`

## <a name="next-steps"></a>次の手順
[Azure Site Recovery を使用したエンタープライズ ワークロードの保護](site-recovery-workload.md)に関する詳細を確認します。
