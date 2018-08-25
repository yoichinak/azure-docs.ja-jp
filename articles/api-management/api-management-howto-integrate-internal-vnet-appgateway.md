---
title: 仮想ネットワークで Application Gateway と Azure API Management を使用する方法 | Microsoft Docs
description: 内部仮想ネットワークで Application Gateway (WAF) をフロントエンドとして使用して Azure API Management をセットアップおよび構成する方法について説明します
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: sasolank
ms.openlocfilehash: ce4fd27c89f529b9c12999689152c3025648d2ce
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038397"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>内部 VNET 内の API Management と Application Gateway の統合

##<a name="overview"> </a> 概要

API Management サービスは、内部モードで仮想ネットワーク内に構成できます。これにより、API Management サービスを仮想ネットワーク内からのみアクセスできるようにします。 Azure Application Gateway は、レイヤー 7 のロード バランサーを提供する PAAS サービスです。 リバースプロキシ サービスとしての役目を果たすものであり、その機能の 1 つとして Web アプリケーション ファイアウォール (WAF) を備えています。

内部 VNET にプロビジョニング済みの API Management と Application Gateway フロントエンドを組み合わせることにより、次のシナリオが実現されます。

* 内部コンシューマーと外部コンシューマーの両方における消費用に同一の API Management リソースを使用する。
* 単一の API Management リソースを使用しながら、API Management 内で定義した API の一部を外部コンシューマーが利用できるようにする。
* ターンキーを使用して、パブリック インターネットから API Management へのアクセスのオン/オフを切り替える。

## <a name="prerequisites"></a>前提条件

この記事で説明されている手順に従うには、以下が必要です。

* 有効な Azure サブスクリプション

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* 証明書 - API ホスト名の場合は pfx および cer、開発者ポータルのホスト名の場合は pfx です。

##<a name="scenario"> </a> シナリオ

この記事では、内部コンシューマーと外部コンシューマーの両方で単一の API Management サービスを使用し、オンプレミスとクラウド双方の API の単一フロントエンドとして機能させる方法について説明します。 さらに、Application Gateway のルーティング機能を使用して、これらの API のうち外部消費用に公開するものを一部のみ (例の中で緑色で記載しています) に制限する方法について説明します。

最初のセットアップ例では、すべての API の管理が仮想ネットワーク内から行われます。 内部コンシューマー (オレンジ色で記載しています) は、内部 API と外部 API のすべてにアクセスできます。 トラフィックがインターネット上に出ていくことはなく、ExpressRoute 回線を介して高いパフォーマンスが実現します。

![url ルート](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> 開始する前に

* Azure PowerShell の最新バージョンを使用していることを確認します。 詳細については、「 [Resource Manager での Windows PowerShell の使用](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager)」をご覧ください。

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>API Management と Application Gateway の統合作成に必要なもの

* **バックエンド サーバー プール:** これは、API Management サービスの内部仮想 IP アドレスです。
* **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、Cookie ベースのアフィニティなどの設定があります。 これらの設定は、プール内のすべてのサーバーに適用されます。
* **フロントエンド ポート:** これは、Application Gateway で開かれたパブリック ポートです。 このポートにヒットしたトラフィックは、バックエンド サーバーのいずれかにリダイレクトされます。
* **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https で、値には大文字小文字の区別あり)、SSL 証明書名 (オフロードの SSL を構成する場合) があります。
* **ルール:** このルールによって、リスナーがバックエンド サーバー プールにバインドされます。
* **カスタムの正常性プローブ:** 既定では、Application Gateway は、IP アドレス ベースのプローブを使用して、BackendAddressPool 内でアクティブなサーバーを見つけます。 API Management サービスは適切なホスト ヘッダーを持つ要求だけに応答するため、既定のプローブでは失敗します。 サービスが有効であり要求を転送する必要があることを Application Gateway が判定できるようにするには、カスタムの正常性プローブを定義する必要があります。
* **カスタムのドメイン証明書:** インターネットから API Management にアクセスするには、ホスト名と Application Gateway のフロントエンド DNS 名との間の CNAME マッピングを作成する必要があります。 これにより、API Management に転送される、Application Gateway に送信されたホスト名のヘッダーと証明書を、APIM が有効であると識別できるようになります。 この例では、2 つの証明書 (バックエンド用と開発者ポータル用) を使用します。  

## <a name="overview-steps"> </a> API Management と Application Gateway を統合するために必要な手順

1. リソース マネージャーのリソース グループを作成します。
2. Application Gateway の仮想ネットワーク、サブネット、およびパブリック IP を作成します。 API Management 用のサブネットを別途作成します。
3. 上記で作成した VNET サブネットの内部に API Management サービスを作成し、内部モードになっていることを確認します。
4. API Management サービスのカスタム ドメイン名を設定します。
5. Application Gateway 構成オブジェクトを作成します。
6. Application Gateway のリソースを作成します。
7. Application Gateway のパブリック DNS 名から API Management プロキシのホスト名への CNAME を作成します。

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Application Gateway を使用して外部から開発者ポータルを公開する

このガイドでは、Application Gateway を使用して**開発者ポータル**を外部の対象ユーザーにも公開します。 開発者ポータルのリスナー、プローブ、設定、およびルールを作成するには、追加の手順が必要です。 詳細はすべて、それぞれの手順で示されます。

> [!WARNING]
> Application Gateway からアクセスされている開発者ポータルで示されたセットアップでは、AAD とサード パーティの認証の問題が発生する可能性があります。

## <a name="create-a-resource-group-for-resource-manager"></a>リソース マネージャーのリソース グループの作成

### <a name="step-1"></a>手順 1

Azure にログインする

```powershell
Login-AzureRmAccount
```

資格情報を使用して認証を行います。

### <a name="step-2"></a>手順 2.

目的のサブスクリプションを選択します。

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzureRmSubscription -Subscriptionid $subscriptionId | Select-AzureRmSubscription
```

### <a name="step-3"></a>手順 3.

リソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzureRmResourceGroup -Name $resGroupName -Location $location
```

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。 アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Application Gateway の仮想ネットワークとサブネットを作成する

次の例では、リソース マネージャーを使用して仮想ネットワークを作成する方法を示します。

### <a name="step-1"></a>手順 1

仮想ネットワークの作成時に Application Gateway 用に使用するサブネット変数に、アドレス範囲 10.0.0.0/24 を割り当てます。

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>手順 2.

仮想ネットワークの作成時に API Management 用に使用するサブネット変数に、アドレス範囲 10.0.1.0/24 を割り当てます。

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>手順 3.

米国西部リージョンのリソース グループ **apim-appGw-RG** に **appgwvnet** という名前の仮想ネットワークを作成します。 サブネット 10.0.0.0/24 および 10.0.1.0/24 と共に、プレフィックス 10.0.0.0/16 を使用します。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>手順 4.

次の手順のためにサブネット変数を割り当てます。

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>内部モードで構成された VNET 内に API Management サービスを作成する

次の例は、内部アクセス専用に構成された VNET に API Management サービスを作成する方法を示しています。

### <a name="step-1"></a>手順 1

上記の手順で作成したサブネット $apimsubnetdata を使用して、API Management の仮想ネットワーク オブジェクトを作成します。

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location $location -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>手順 2.

仮想ネットワーク内に API Management サービスを作成します。

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzureRmApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

上記のコマンドが成功したら、[内部 VNET の API Management サービスへのアクセスに必要な DNS 構成](api-management-using-with-internal-vnet.md#apim-dns-configuration)に関するセクションを参照してアクセスします。 この手順には、30 分以上かかる場合があります。

## <a name="set-up-a-custom-domain-name-in-api-management"></a>API Management でカスタム ドメイン名をセットアップする

### <a name="step-1"></a>手順 1

ドメイン用の秘密キーを使用して証明書をアップロードします。 この例では、`api.contoso.net` と `portal.contoso.net` を使用します。  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName $resGroupName -Name $apimServiceName -HostnameType "Proxy" -PfxPath $gatewayCertPfxPath -PfxPassword $gatewayCertPfxPassword -PassThru
$certPortalUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName $resGroupName -Name $apimServiceName -HostnameType "Proxy" -PfxPath $portalCertPfxPath -PfxPassword $portalCertPfxPassword -PassThru
```

### <a name="step-2"></a>手順 2.

証明書がアップロードされたら、プロキシとポータルに対してホスト名構成オブジェクトを作成します。  

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname $gatewayHostname
$portalHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certPortalUploadResult.Thumbprint -Hostname $portalHostname
$result = Set-AzureRmApiManagementHostnames -Name $apimServiceName -ResourceGroupName $resGroupName –PortalHostnameConfiguration $portalHostnameConfig -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>フロントエンド構成のパブリック IP アドレスの作成

リソース グループにパブリック IP リソースの **publicIP01** を作成します。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

サービスが開始すると、Application Gateway に IP アドレスが割り当てられます。

## <a name="create-application-gateway-configuration"></a>Application Gateway 構成の作成

Application Gateway を作成するには、すべての構成項目を設定する必要があります。 次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

### <a name="step-1"></a>手順 1

**gatewayIP01** という名前のアプリケーション ゲートウェイの IP 構成を作成します。 アプリケーション ゲートウェイが起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。 各インスタンスが IP アドレスを 1 つ取得することに注意してください。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>手順 2.

パブリック IP エンドポイントのフロントエンド IP ポートを構成します。 このポートは、エンドユーザーが接続するポートです。

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>手順 3.

パブリック IP エンドポイントでフロント エンド IP を構成します。

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>手順 4.

通過するトラフィックの暗号化解除と再暗号化に使用される、Application Gateway 用に証明書を構成します。

```powershell
$certPwd = ConvertTo-SecureString $gatewayCertPfxPassword -AsPlainText -Force
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortalPwd = ConvertTo-SecureString $portalCertPfxPassword -AsPlainText -Force
$certPortal = New-AzureRmApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>手順 5.

Application Gateway 用の HTTP リスナーを作成します。 フロントエンド IP 構成、ポート、および SSL 証明書をリスナーに割り当てます。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>手順 6.

API Management サービスの `ContosoApi` プロキシのドメイン エンドポイントに対するカスタム プローブを作成します。 パス `/status-0123456789abcdef` は、すべての API Management サービスでホストされている既定の正常性エンドポイントです。 `api.contoso.net` をカスタム プローブのホスト名として設定し、SSL 証明書でセキュリティ保護します。

> [!NOTE]
> ホスト名 `contosoapi.azure-api.net` は、`contosoapi` という名前のサービスがパブリック Azure に作成されたときに構成された既定のプロキシ ホスト名です。
>

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzureRmApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>手順 7.

SSL 対応バックエンド プール リソースで使用する証明書をアップロードします。 これは、前の手順 4 で指定したものと同じ証明書です。

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>手順 8.

Application Gateway の HTTP バックエンド設定を構成します。 これには、バックエンド要求が取り消されるまでのタイムアウト制限の設定が含まれます。 この値はプローブのタイムアウトとは異なります。

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>手順 9.

**apimbackend** という名前のバックエンド IP アドレス プールを、上記で作成した API Management サービスの内部仮想 IP アドレスを指定して構成します。

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>手順 10.

基本のルーティングを使用するように、Application Gateway 用のルールを作成します。

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> 開発者ポータルの特定のページへのアクセスを制限するには、-RuleType とルーティングを変更します。

### <a name="step-11"></a>手順 11.

Application Gateway のインスタンス数とサイズを構成します。 この例では、API Management リソースのセキュリティを強化するために、[WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) を使用しています。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>手順 12.

WAF を "Prevention" モードに構成します。

```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Application Gateway の作成

前述の手順の構成オブジェクトをすべて使用して、Application Gateway を作成します。

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>API Management プロキシのホスト名から Application Gateway リソースのパブリック DNS 名への CNAME を作成する

ゲートウェイを作成したら、次は通信用にフロントエンドを構成します。 パブリック IP を使用する場合、Application Gateway は動的に割り当てられる DNS 名を必要としますが、これを使用するのは必ずしも簡単ではありません。

Application Gateway の DNS 名を使用して、この DNS 名に対して構成した APIM プロキシ ホスト名 (上の例の `api.contoso.net`) を指す CNAME レコードを作成する必要があります。 フロントエンド IP CNAME レコードを構成するには、PublicIPAddress 要素を使用して、Application Gateway の詳細とそれに関連付けられている IP/DNS 名を取得します。 ゲートウェイの再起動時に VIP が変更される可能性があるため、A レコードの使用はお勧めしません。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

##<a name="summary"> </a> まとめ
VNET で構成された Azure API Management は、ホスト先がオンプレミスかクラウドかにかかわらず、すべての構成済みの API に単一のゲートウェイ インターフェイスを提供します。 Application Gateway を API Management と統合すると、インターネット上で特定の API に選択的にアクセスできる柔軟性が提供されるほか、API Management インスタンスのフロントエンドとして Web アプリケーション ファイアウォールを利用できるようになります。

##<a name="next-steps"> </a> 次のステップ
* Azure Application Gateway の詳細を確認する
  * [Application Gateway の概要](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway の Web アプリケーション ファイアウォール](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [パスベースのルーティングを使用して Application Gateway を作成する](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* API Management と VNET の詳細を確認する
  * [内部仮想ネットワークでの Azure API Management サービスの使用](api-management-using-with-internal-vnet.md)
  * [VNET での API Management の使用](api-management-using-with-vnet.md)
