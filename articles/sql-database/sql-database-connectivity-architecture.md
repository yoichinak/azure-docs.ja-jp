---
title: Azure SQL Database 接続アーキテクチャ | Microsoft Docs
description: この文書では、Azure SQLDB の接続アーキテクチャを Azure 内外から説明します。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: carlrab
ms.openlocfilehash: afc82ea666fdbef89348e7453df92b8d8e1adc86
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493674"
---
# <a name="azure-sql-database-connectivity-architecture"></a>Azure SQL Database 接続アーキテクチャ 

この記事では Azure SQL Database の接続アーキテクチャについて説明し、さまざまなコンポーネントがどのように機能し、トラフィックが Azure SQL Database インスタンスに送信されるか説明します。 これらの Azure SQL Database 接続コンポーネントが機能し、Azure 内外からクライアントが接続する Azure データベースにネットワーク トラフィックが送信されます。 この記事では、接続方法を変更するためのスクリプト サンプルと、既定の接続設定の変更に関連する考慮事項も提供します。 

## <a name="connectivity-architecture"></a>接続のアーキテクチャ

次の図は、Azure SQL Database の接続アーキテクチャの概要です。

![アーキテクチャの概要](./media/sql-database-connectivity-architecture/architecture-overview.png)


次の手順で、Azure SQL Database ソフトウェア ロード バランサー (SLB) と Azure SQL Database ゲートウェイを利用して Azure SQL への接続が確立されます。

- Azure 内外のクライアントは SLB に接続します。SLB にはパブリック IP アドレスが与えられており、ポート 1433 で待ち受けます。
- SLB は Azure SQL Database ゲートウェイにトラフィックを送信します。
- ゲートウェイは適切なプロキシ ミドルウェアにトラフィックをリダイレクトします。
- プロキシ ミドルウェアは適切な Azure SQL データベースにトラフィックをリダイレクトします。

> [!IMPORTANT]
> これらの各コンポーネントには、ネットワーク層とアプリケーション層で、DDoS (分散型サービス拒否) 保護が組み込まれています。
>

## <a name="connectivity-from-within-azure"></a>Azure 内からの接続

Azure 内から接続する場合、接続には既定で **Redirect** の接続ポリシーが与えられます。 **Redirect** のポリシーとは、TCP セッションが Azure SQL Database に対して確立された後の接続で、宛先の仮想 IP を Azure SQL Database ゲートウェイの IP からプロキシ ミドルウェアの IP に変更して、クライアント セッションをプロキシ ミドルウェアにリダイレクトすることを意味します。 その後、すべての後続パケットは Azure SQL Database ゲートウェイを迂回し、プロキシ ミドルウェア経由で直接送信されます。 次の図にこのトラフィックの流れを示します。

![アーキテクチャの概要](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Azure 外からの接続

Azure 外から接続する場合、接続には既定で**プロキシ**の接続ポリシーが与えられます。 **プロキシ**のポリシーとは、TCP セッションが Azure SQL Database ゲートウェイ経由で確立し、すべての後続パケットがゲートウェイ経由で送信されることを意味します。 次の図にこのトラフィックの流れを示します。

![アーキテクチャの概要](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

> [!IMPORTANT]
> Azure SQL Database でサービス エンドポイントを使用する場合、ポリシーは既定で**プロキシ**になります。 VNet 内からの接続を有効にするには、次の一覧に指定されているAzure SQL Database ゲートウェイ IP アドレスへの送信接続を許可します。 サービス エンドポイントを使用する場合は、パフォーマンスが向上されるように、接続ポリシーを**リダイレクト**に変更することを強くお勧めします。 接続ポリシーを**リダイレクト**に変更しても、次に示されている Azure SQLDB ゲートウェイ IP への NSG の送信は十分ではありません。すべての Azure SQLDB IP への送信を許可する必要があります。 これは、NSG (ネットワーク セキュリティ グループ) サービス タグを使用して実現できます。 詳細については、「[サービス タグ](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags)」を参照してください。

## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database ゲートウェイ IP アドレス

オンプレミス リソースから Azure SQL データベースに接続するには、自分の Azure リージョンに関して、Azure SQL Database ゲートウェイへの送信ネットワーク トラフィックを許可する必要があります。 プロキシ モードで接続するとき、ゲートウェイ経由でのみ接続されます。プロキシ モードは、オンプレミス リソースから接続するときの既定です。

次の表は、すべてのデータ リージョンの Azure SQL Database ゲートウェイのプライマリ IP とセカンダリ IP を一覧にまとめたものです。 IP アドレスが 2 つのリージョンもあります。 そのようなリージョンでは、プライマリ IP アドレスはゲートウェイの現行 IP アドレスであり、セカンダリ IP アドレスはフェールオーバー IP アドレスになります。 フェールオーバー アドレスは、サービスの高い可用性を維持するためにサーバーを移動するとき、その移動先となるアドレスです。 そのようなリージョンについては、両方の IP アドレスへの送信を許可することが推奨されます。 セカンダリ IP アドレスは Microsoft の所有であり、接続を受け入れるために Azure SQL Database によりアクティベートされるまで、いかなるサービスでもリッスンしません。

| リージョン名 | プライマリ IP アドレス | セカンダリ IP アドレス |
| --- | --- |--- |
| オーストラリア東部 | 191.238.66.109 | 13.75.149.87 |
| オーストラリア東南部 | 191.239.192.109 | 13.73.109.251 |
| ブラジル南部 | 104.41.11.5 | |
| カナダ中部 | 40.85.224.249 | |
| カナダ東部 | 40.86.226.166 | |
| 米国中央部 | 23.99.160.139 | 13.67.215.62 |
| 東アジア | 191.234.2.139 | 52.175.33.150 |
| 米国東部 1 | 191.238.6.43 | 40.121.158.30 |
| 米国東部 2 | 191.239.224.107 | 40.79.84.180 * |
| インド中部 | 104.211.96.159  | |
| インド南部 | 104.211.224.146  | |
| インド西部 | 104.211.160.80 | |
| 東日本 | 191.237.240.43 | 13.78.61.196 |
| 西日本 | 191.238.68.11 | 104.214.148.156 |
| 韓国中部 | 52.231.32.42 | |
| 韓国南部 | 52.231.200.86 |  |
| 米国中北部 | 23.98.55.75 | 23.96.178.199 |
| 北ヨーロッパ | 191.235.193.75 | 40.113.93.91 |
| 米国中南部 | 23.98.162.75 | 13.66.62.124 |
| 東南アジア | 23.100.117.95 | 104.43.15.0 |
| 英国北部 | 13.87.97.210 | |
| 英国南部 1 | 51.140.184.11 | |
| 英国南部 2 | 13.87.34.7 | |
| 英国西部 | 51.141.8.11  | |
| 米国中西部 | 13.78.145.25 | |
| 西ヨーロッパ | 191.237.232.75 | 40.68.37.158 |
| 米国西部 1 | 23.99.34.75 | 104.42.238.205 |
| 米国西部 2 | 13.66.226.202  | |
||||

\* **注:** *米国東部 2* には、`52.167.104.0` の第 3 IP アドレスもあります。

## <a name="change-azure-sql-database-connection-policy"></a>Azure SQL Database 接続ポリシーを変更する

Azure SQL Database サーバーの Azure SQL Database 接続ポリシーを変更するには、[conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) コマンドを使用します。

- 接続ポリシーを**プロキシ**に設定すると、すべてのネットワーク パケットが Azure SQL Database ゲートウェイ経由で送信されます。 この設定の場合、Azure SQL Database ゲートウェイ IP のみに送信を許可する必要があります。 **プロキシ**の設定を利用すると、**リダイレクト**の設定より待ち時間が長くなります。
- 接続ポリシーで**リダイレクト**を設定すると、すべてのネットワーク パケットがミドルウェア プロキシに直接送信されます。 この設定の場合、複数の IP への送信を許可する必要があります。

## <a name="script-to-change-connection-settings-via-powershell"></a>接続の設定を変更する PowerShell のスクリプト

> [!IMPORTANT]
> このスクリプトは [Azure PowerShell モジュール](/powershell/azure/install-azurerm-ps)を必要とします。
>

次の PowerShell スクリプトは、接続ポリシーの変更方法を示しています。

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli-20"></a>接続の設定を変更する Azure CLI 2.0 のスクリプト

> [!IMPORTANT]
> このスクリプトは [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) を必要とします。
>

次の CLI スクリプトは、接続ポリシーの変更方法を示しています。

<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy 
az resource show --ids $id

# Update connection policy 
az resource update --ids $id --set properties.connectionType=Proxy

</pre>

## <a name="next-steps"></a>次の手順

- Azure SQL Database サーバーの Azure SQL Database 接続ポリシーの変更方法については、「[conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)」を参照してください。
- ADO.NET 4.5 以降のバージョンを使用するクライアントの Azure SQL Database 接続動作については、「[ADO.NET 4.5 用の 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」を参照してください。
- 一般的なアプリケーション開発概要情報については、「[SQL Database アプリケーションの開発の概要](sql-database-develop-overview.md)」を参照してください。
