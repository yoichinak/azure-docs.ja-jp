---
title: Azure File Sync のオンプレミスのファイアウォール設定とプロキシ設定 | Microsoft Docs
description: Azure File Sync のオンプレミスのネットワーク構成
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: fauhse
ms.component: files
ms.openlocfilehash: 44bfdd192f846b710e378b1f00799eda304cec1e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522766"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Azure File Sync のプロキシとファイアウォールの設定
Azure File Sync は、オンプレミスのサーバーを Azure Files に接続することで、マルチサイトの同期とクラウドの階層化の機能を実現します。 そのため、オンプレミスのサーバーがインターネットに接続されている必要があります。 サーバーから Azure Cloud Services に到達するための最適なパスは、IT 管理者が決める必要があります。

この記事では、ご利用のサーバーを Azure File Sync に正しく安全に接続するための具体的な要件と選択肢についての分析情報を提供します。

> [!Important]
> Azure File Sync はまだ、ストレージ アカウント用のファイアウォールおよび仮想ネットワークをサポートしていません。

## <a name="overview"></a>概要
Azure File Sync は、Windows Server と Azure ファイル共有など各種 Azure サービスとの間のオーケストレーション サービスとして機能し、同期グループ内の定義に従ってデータを同期します。 Azure File Sync を正しく機能させるためには、次の Azure サービスと通信するための構成をサーバーに対して行う必要があります。

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- 認証サービス

> [!Note]  
> クラウド サービスに対する要求はすべて、Windows Server 上の Azure File Sync エージェントによって開始されます。したがってファイアウォールの観点から考慮する必要があるのは送信トラフィックだけです。 <br /> Azure File Sync エージェントへの接続が Azure サービス側から開始されることはありません。

## <a name="ports"></a>ポート
Azure File Sync は HTTPS のみを使ってファイル データとメタデータを移動するため、送信方向のポート 443 を開放する必要があります。
結果的にすべてのトラフィックが暗号化されます。

## <a name="networks-and-special-connections-to-azure"></a>Azure への特別な接続とネットワーク
Azure File Sync エージェントと Azure とのチャンネルに関して特別な要件 ([ExpressRoute](../../expressroute/expressroute-introduction.md) など) はありません。

Azure File Sync は、Azure に到達さえできれば、その接続手段を問わず正しく機能します。さまざまなネットワーク特性 (帯域幅、待ち時間など) に応じた自動調整のほか、管理制御による微調整の手段も用意されています。 一部の機能については、現時点では利用することができません。 構成の対象として希望する具体的な動作がある場合は、[Azure Files の UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670) でお聞かせください。

## <a name="proxy"></a>プロキシ
Azure File Sync では、アプリ固有のプロキシ設定とマシン全体のプロキシ設定がサポートされています。

マシン全体のプロキシ設定は、Azure File Sync エージェントに対して透過的です。このプロキシではサーバーのトラフィック全体がルーティングされるためです。

アプリ固有のプロキシ設定を使用すると、Azure File Sync のトラフィック専用のプロキシを構成できます。 アプリ固有のプロキシ設定はエージェント バージョン 3.0.12.0 以降でサポートされ、エージェントのインストール中に構成するか、または Set-StorageSyncProxyConfiguration PowerShell コマンドレットを使用して構成できます。

アプリ固有のプロキシ設定を構成する PowerShell コマンドを次に示します。
```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```

## <a name="firewall"></a>ファイアウォール
前のセクションで述べたように、送信方向のポート 443 を開放する必要があります。 さらに、ご利用のデータセンターやブランチ、リージョンのポリシーによっては、このポート上のトラフィックを特定のドメインに制限することが望ましい、または必須となる場合もあります。

次の表で、通信に必要なドメインについて説明します。

| Service | ドメイン | 使用法 |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | 初回サーバー登録呼び出しを含め、すべてのユーザー呼び出し (PowerShell など) は、この URL に向かうか、この URL を経由します。 |
| **Azure Active Directory** | https://login.windows.net | Azure Resource Manager の呼び出しは、認証済みのユーザーが行う必要があります。 成功するためには、この URL を使用してユーザー認証を行う必要があります。 |
| **Azure Active Directory** | https://graph.windows.net/ | Azure File Sync をデプロイする過程で、サブスクリプションの Azure Active Directory のサービス プリンシパルを作成する必要があります。 この URL は、その際に使用されます。 このプリンシパルは、最小限の権限一式を Azure File Sync サービスに委任する目的で使用されます。 Azure File Sync の初回セットアップは、サブスクリプション所有者の権限を持った認証済みユーザーが実行する必要があります。 |
| **Azure Storage** | &ast;.core.windows.net | サーバーがファイルをダウンロードするとき、ストレージ アカウント内の Azure ファイル共有との間で直接通信を行った方が、データの移動を効率よく実行することができます。 このサーバーには、対象のファイル共有へのアクセスのみが許可された SAS キーが与えられます。 |
| **Azure File Sync** | &ast;.one.microsoft.com | サーバーの初回登録後、そのサーバーには、特定のリージョン内の Azure File Sync サービス インスタンスに使用されるリージョン固有の URL が送信されます。 サーバーは、この URL を使って、その同期処理を行うインスタンスと直接かつ効率的に通信を行います。 |

> [!Important]
> &ast;.one.microsoft.com へのトラフィックを許可すると、同期サービスに限らず、サーバーから出て行くそれ以外のトラフィックも許可されます。 サブドメインには、その他多くの Microsoft サービスが提供されています。

&ast;.one.microsoft.com で範囲が広すぎる場合は、通信の許可の対象となる Azure Files Sync サービスのリージョン固有のインスタンスを明示的に指定することで、サーバーの通信を制限することができます。 どのインスタンスを選ぶかは、実際にサーバーのデプロイと登録を行ったストレージ同期サービスのリージョンによって異なります。 そのリージョンを、以下の表では "プライマリ エンドポイント URL" と記述しています。

事業継続とディザスター リカバリー (BCDR) 上の理由から、グローバル冗長ストレージ (GRS) アカウント内の Azure ファイル共有が指定されていることも考えられます。 そのようなケースで、万一長時間にわたる地域的な機能不全が生じた場合には、Azure ファイル共有がペア リージョンにフェールオーバーされます。 Azure File Sync がストレージとして使用するリージョン ペアは変わりません。 そのため、GRS ストレージ アカウントを使用している場合は、サーバーが Azure File Sync のペア リージョンと通信するための URL を別途有効にする必要があります。以下の表では、これを "ペア リージョン" と記述しています。 また、Traffic Manager のプロファイルの URL も有効にする必要があります。 これにより、万一フェールオーバーが発生した場合、ネットワーク トラフィックがペア リージョンに対してシームレスに再ルーティングされます。次の表では、これを "検出 URL" と記述しています。

| リージョン | プライマリ エンドポイント URL | ペア リージョン | 検出 URL | |--------|---------------------------------------||--------||---------------------------------------| | オーストラリア東部 | https://kailani-aue.one.microsoft.com | オーストラリア南東部 | https://kailani-aue.one.microsoft.com | | オーストラリア南東部 | https://kailani-aus.one.microsoft.com | オーストラリア東部 | https://tm-kailani-aus.one.microsoft.com | | カナダ中部 | https://kailani-cac.one.microsoft.com | カナダ東部 | https://tm-kailani-cac.one.microsoft.com | | カナダ東部 | https://kailani-cae.one.microsoft.com | カナダ中部 | https://tm-kailani.cae.one.microsoft.com | | 米国中部 | https://kailani-cus.one.microsoft.com | 米国東部 2 | https://tm-kailani-cus.one.microsoft.com | | 東アジア | https://kailani11.one.microsoft.com | 東南アジア | https://tm-kailani11.one.microsoft.com | | 米国東部 | https://kailani1.one.microsoft.com | 米国西部 | https://tm-kailani1.one.microsoft.com | | 米国東部 2 | https://kailani-ess.one.microsoft.com | 米国中部 | https://tm-kailani-ess.one.microsoft.com | | 北ヨーロッパ | https://kailani7.one.microsoft.com | 西ヨーロッパ | https://tm-kailani7.one.microsoft.com | | 東南アジア | https://kailani10.one.microsoft.com | 東アジア | https://tm-kailani10.one.microsoft.com | | 英国南部 | https://kailani-uks.one.microsoft.com | 英国西部 | https://tm-kailani-uks.one.microsoft.com | | 英国西部 | https://kailani-ukw.one.microsoft.com | 英国南部 | https://tm-kailani-ukw.one.microsoft.com | | 西ヨーロッパ | https://kailani6.one.microsoft.com | 北ヨーロッパ | https://tm-kailani6.one.microsoft.com | | 米国西部 | https://kailani.one.microsoft.com | 米国東部 | https://tm-kailani.one.microsoft.com |

- 使用しているストレージ アカウントがローカル冗長 (LRS) またはゾーン冗長 (ZRS) の場合、有効にする必要があるのは、"プライマリ エンドポイント URL" に記載された URL だけです。

- グローバル冗長ストレージ (GRS) アカウントを使用している場合は、3 つの URL を有効にすることになります。

**例:** ストレージ同期サービスを `"West US"` にデプロイしてそこにサーバーを登録するとします。 この場合、サーバーには、次の URL との通信を許可することになります。

> - https://kailani.one.microsoft.com (プライマリ エンドポイント: 米国西部)
> - https://kailani1.one.microsoft.com (ペア フェールオーバー リージョン: 米国東部)
> - https://tm-kailani.one.microsoft.com (プライマリ リージョンの検出 URL)

## <a name="summary-and-risk-limitation"></a>概要とリスクの制限
このドキュメントで前述したリストは、現時点で Azure File Sync が通信する URL を記載したものです。 ファイアウォールで、これらのドメインに向かうトラフィックを許可する必要があります。 Microsoft は、このリストを最新の内容に保つよう努めます。

ドメインを制限するファイアウォール規則の設定は、セキュリティを強化するための対策になると考えられます。 そうしたファイアウォール構成を使用する場合、時間の経過に伴って URL が追加されること、また場合によっては変更される可能性もあることに留意する必要があります。 こちらの記事を定期的にご確認ください。

## <a name="next-steps"></a>次の手順
- [Azure File Sync のデプロイの計画](storage-sync-files-planning.md)
- [Azure File Sync をデプロイする](storage-sync-files-deployment-guide.md)