---
title: Azure で API Management と Service Fabric を統合する | Microsoft Docs
description: このチュートリアルでは、Azure API Management と Service Fabric の基本的な使用方法を簡単に説明します。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 1b0588e25c0d156080a2e879185b76714d8691b2
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113376"
---
# <a name="tutorial-integrate-api-management-with-service-fabric-in-azure"></a>チュートリアル: Azure で API Management と Service Fabric を統合する

このチュートリアルは、シリーズの第 4 部です。  Service Fabric での Azure API Management のデプロイは高度なシナリオです。  API Management は、バックエンドの Service Fabric サービスのルーティング規則を豊富に備えた API を公開する必要があるときに便利です。 通常、クラウド アプリケーションには、ユーザー、デバイス、またはその他のアプリケーションに単一の受信ポイントを提供するフロントエンド ゲートウェイが必要です。 Service Fabric では、ASP.NET Core アプリケーション、Event Hubs、IoT Hub、Azure API Management など、トラフィック イングレス用に設計された任意のステートレス サービスをゲートウェイとして使用できます。

このチュートリアルでは、Service Fabric を使用して [Azure API Management](../api-management/api-management-key-concepts.md) をセットアップし、Service Fabric のバックエンド サービスにトラフィックをルーティングする方法について示します。  完了すると、トラフィックをバックエンド ステートレス サービスに送信するよう API 操作が構成された状態で、API Management が VNET にデプロイされます。 Service Fabric を使用する Azure API Management のその他のシナリオについては、[概要](service-fabric-api-management-overview.md)を参照してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * API Management をデプロイする
> * API Management を構成する
> * API 操作の作成
> * バックエンド ポリシーの構成
> * API を製品に追加する

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * テンプレートを使用して、セキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)または [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md)を Azure に作成する
> * [クラスターをスケールインまたはスケールアウトする](service-fabric-tutorial-scale-cluster.md)
> * [クラスターのランタイムをアップグレードする](service-fabric-tutorial-upgrade-cluster.md)
> * Service Fabric を使用して API Management をデプロイする

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
* [Azure PowerShell モジュールのバージョン 4.1 以上](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)または [Azure CLI 2.0](/cli/azure/install-azure-cli) をインストールします。
* セキュリティで保護された [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)または [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md)を Azure に作成します。
* Windows クラスターをデプロイする場合は、Windows 開発環境を設定します。 [Visual Studio 2017](http://www.visualstudio.com)、**Azure 開発**ワークロード、**ASP.NET および Web 開発**ワークロード、**.NET Core クロス プラットフォーム開発**ワークロードをインストールします。  その後、[.NET 開発環境](service-fabric-get-started.md)をセットアップします。
* Linux クラスターをデプロイする場合は、Java 開発環境を [Linux](service-fabric-get-started-linux.md) または [MacOS](service-fabric-get-started-mac.md) にセットアップします。  [Service Fabric CLI](service-fabric-cli.md) をインストールします。

## <a name="network-topology"></a>ネットワーク トポロジ

この時点までに、セキュリティで保護されている [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)または [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md)が Azure 上にあり、API Management をサブネットの仮想ネットワーク (VNET) と、API Management 用に設計された NSG にデプロイしました。 このチュートリアルで使用する API Management の Resource Manager テンプレートは、前の [Windows クラスター チュートリアル](service-fabric-tutorial-create-vnet-and-windows-cluster.md)または [Linux クラスター チュートリアル](service-fabric-tutorial-create-vnet-and-linux-cluster.md)で設定した VNET、サブネット、および NSG の名前を使用するようあらかじめ構成されています。 このチュートリアルでは、次のトポロジを Azure にデプロイします。Azure の同じ Virtual Network のサブネット内に Azure API Management と Service Fabric があります。

 ![キャプション][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Azure にサインインしてサブスクリプションを選択する

Azure アカウントにサインインし、Azure のコマンドを実行する前にサブスクリプションを選択します。

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Service Fabric のバックエンド サービスのデプロイ

Service Fabric バックエンド サービスにトラフィックがルーティングされるよう API Management を構成する前に、まずは要求を受け入れる実行中のサービスが必要です。  [Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md)を以前に作成している場合、.NET Service Fabric サービスをデプロイします。  [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md)を以前に作成している場合、Java Service Fabric サービスをデプロイします。

### <a name="deploy-a-net-service-fabric-service"></a>.NET Service Fabric サービスをデプロイする

このチュートリアルでは、既定の Web API プロジェクト テンプレートを使用して、ASP.NET Core の基本的なステートレス リライアブル サービスを作成します。 これにより、サービスの HTTP エンドポイントが作成され、Azure API Management を通じて公開することができます。

Visual Studio を管理者として起動し、ASP.NET Core サービスを作成します。

 1. Visual Studio で、[ファイル] の [新しいプロジェクト] を選択します。
 2. [クラウド] の下にある Service Fabric アプリケーション テンプレートを選択し、**"ApiApplication"** という名前を付けます。
 3. ステートレス ASP.NET Core サービス テンプレートを選択し、プロジェクトに **"WebApiService"** という名前を付けます。
 4. Web API ASP.NET Core 2.0 プロジェクト テンプレートを選択します。
 5. プロジェクトが作成されたら、`PackageRoot\ServiceManifest.xml` を開いて、エンドポイント リソースの構成から `Port` 属性を削除します。

    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    ポートを削除すると、Service Fabric がアプリケーションのポート範囲 (クラスターの Resource Manager テンプレートのネットワーク セキュリティ グループを通じて開かれたもの) から動的にポートを指定できるようになり、そこに API Management からのトラフィックが流れるようになります。

 6. Visual Studio で F5 キーを押して、Web API がローカルで使用できることを確認します。

    Service Fabric Explorer を開き、ASP.NET Core サービスの特定のインスタンスまでドリルダウンして、サービスがリッスンするベース アドレスを表示します。 `/api/values` をベース アドレスに追加し、ブラウザーで開くと、Web API テンプレートの ValuesController で Get メソッドが呼び出されます。 このメソッドは、テンプレートで指定される既定の応答として、2 つの文字列を格納する JSON 配列を返します。

    ```json
    ["value1", "value2"]`
    ```

    これが、Azure の API Management を通じて公開するエンドポイントになります。

 7. 最後に、アプリケーションを Azure のクラスターにデプロイします。 Visual Studio で、Application プロジェクトを右クリックして **[公開]** を選択します。 クラスター エンドポイント (`mycluster.southcentralus.cloudapp.azure.com:19000` など) を指定して、アプリケーションを Azure の Service Fabric クラスターにデプロイします。

`fabric:/ApiApplication/WebApiService` という名前の ASP.NET Core ステートレス サービスが、Azure の Service Fabric クラスターで実行されているはずです。

### <a name="create-a-java-service-fabric-service"></a>Java Service Fabric サービスを作成する

このチュートリアルでは、ユーザーにメッセージをエコー バックする基本的な Web サーバーをデプロイします。 このエコー サーバー サンプル アプリケーションは、Azure API Management を通じて公開することができる、サービスの HTTP エンドポイントを含みます。

1. Java の入門サンプルを複製します。

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   cd service-fabric-java-getting-started/reliable-services-actor-sample
   ```

2. *Services/EchoServer/EchoServer1.0/EchoServerApplication/EchoServerPkg/ServiceManifest.xml* を編集します。 サービスがポート 8081 でリッスンするようエンドポイントを変更します。

   ```xml
   <Endpoint Name="WebEndpoint" Protocol="http" Port="8081" />
   ```

3. *ServiceManifest.xml* を保存し、EchoServer1.0 アプリケーションをビルドします。

   ```bash
   cd Services/EchoServer/EchoServer1.0/
   gradle
   ```

4. アプリケーションをクラスターにデプロイします。

   ```bash
   cd Scripts
   sfctl cluster select --endpoint https://mycluster.southcentralus.cloudapp.azure.com:19080 --pem <full_path_to_pem_on_dev_machine> --no-verify
   ./install.sh
   ```

   `fabric:/EchoServerApplication/EchoServerService` という名前の Java ステートレス サービスが、Azure の Service Fabric クラスターで実行されているはずです。

5. ブラウザーを開き、「http://mycluster.southcentralus.cloudapp.azure.com:8081/getMessage」と入力すると、"[version 1.0]Hello World!!!" と 表示されます。

## <a name="download-and-understand-the-resource-manager-templates"></a>Resource Manager テンプレートのダウンロードと理解

次の Resource Manager テンプレートとパラメーター ファイルをダウンロードして保存します。

* [network-apim.json][network-arm]
* [network-apim.parameters.json][network-parameters-arm]
* [apim.json][apim-arm]
* [apim.parameters.json][apim-parameters-arm]

*network-apim.json* テンプレートを使用すると、Service Fabric クラスターがデプロイされている仮想ネットワーク内に新しいサブネットとネットワーク セキュリティ グループをデプロイできます。

次のセクションでは、*apim.json* テンプレートによって定義されるリソースについて説明します。 詳細については、各セクション内のリンク先のテンプレート リファレンス ドキュメントを参照してください。 *apim.parameters.json* パラメーター ファイルで定義されている構成可能なパラメーターは、この記事の後半で設定します。

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) は、API Management サービス インスタンス (名前、SKU または階層、リソース グループの場所、パブリッシャー情報、および仮想ネットワーク) を記述します。

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates

[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) は、API Management セキュリティを構成します。 API Management は、Service Fabric クラスターへのアクセス権を持つクライアント証明書を使用して、サービス検出の際にそのクラスターで認証する必要があります。 このチュートリアルでは、[Windows クラスター](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor)または [Linux クラスター](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor)を作成したときに指定した証明書と同じものを使用します。この証明書は、クラスターにアクセスするときに既定で使用できます。

このチュートリアルでは、クライアント認証とクラスター ノード間のセキュリティに同じ証明書を使用します。 Service Fabric クラスターにアクセスするよう構成した別のクライアント証明書がある場合は、その証明書を使用してもかまいません。 Service Fabric クラスターを作成したときに指定したクラスター証明書の秘密キー ファイル (.pfx) の**名前**、**パスワード**、および**データ** (Base 64 エンコード文字列) を指定します。

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends

[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) は、トラフィックが転送されるバックエンド サービスを記述します。

Service Fabric のバックエンドの場合は、特定の Service Fabric サービスではなく、Service Fabric クラスターがバックエンドとなります。 これにより、単一のポリシーでクラスター内の複数のサービスにルーティングすることができます。 この **url** フィールドはクラスター内のサービスの完全修飾サービス名であり、バックエンド ポリシーにサービス名が指定されていない場合に、既定ですべての要求がこのサービスにルーティングされます。 フォールバック サービスが不要の場合は、"fabric:/fake/service" などの仮のサービス名を使用できます。 **resourceId** は、クラスター管理エンドポイントを指定します。  **clientCertificateThumbprint** と **serverCertificateThumbprints** は、クラスターでの認証に使用される証明書を識別します。

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products

[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) は製品を作成します。 Azure API Management の成果物には、少なくとも 1 つの API に加え、使用量クォータや使用条件が含まれます。 成果物が発行されると、開発者は成果物をサブスクライブして、成果物の API の利用を開始できます。

製品のわかりやすい **displayName** と **description** を入力します。 このチュートリアルでは、サブスクリプションは必須ですが、管理者によるサブスクリプションの承認は必須ではありません。  この製品の **state** が "公開" され、サブスクライバーに表示します。

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis

[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) は API を作成します。 API Management における API は、クライアント アプリケーションから呼び出すことのできる一連の操作を表します。 操作を追加し、API を成果物に追加すると、API を発行できます。 API が発行されると、開発者はそれをサブスクライブして使用できます。

* **displayName** には、API の任意の名前を指定できます。 このチュートリアルの場合、"Service Fabric App" を使用します。
* **name** は、"service-fabric-app" などの API に対する一意のわかりやすい名前です。 開発者ポータルとパブリッシャー ポータルには、この名前が表示されます。
* **serviceUrl** は、API が実装されている HTTP サービスを参照します。 要求は、API Management によってこのアドレスに転送されます。 Service Fabric のバックエンドの場合、この URL の値は使用されません。 任意の値を入力できます。 このチュートリアルでは、たとえば "http://servicefabric" を使用します。
* **path** が API Management サービスのベース URL に付加されます。 ベース URL は、API Management サービス インスタンスによってホストされるすべての API に共通です。 API Management では API がサフィックスによって識別されるため、サフィックスは、特定の発行者のすべての API で一意である必要があります。
* **protocols** により、API へのアクセスに使用できるプロトコルが決まります。 このチュートリアルでは、**http** と **https** をリストします。
* **path** は API のサフィックスです。 このチュートリアルでは、"myapp" を使用します。

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations

[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) では、API Management でAPI を使用する前に、操作をその API に追加する必要があります。  外部クライアントでは、操作を使用して、Service Fabric クラスターで実行中の ASP.NET Core ステートレス サービスと通信します。

フロントエンド API 操作を追加するには、値を入力します。

* **displayName** と **description** で操作を説明します。 このチュートリアルでは、"Values" を使用します。
* **method** は HTTP 動詞を指定します。  このチュートリアルでは、**GET** を指定します。
* **urlTemplate** は、API のベース URL に付加され、単一の HTTP 操作を識別します。  このチュートリアルでは、`/api/values` (.NET バックエンド サービスを追加した場合) または `getMessage` (Java バックエンド サービスを追加した場合) を使用します。  既定では、ここで指定した URL パスが、バックエンドの Service Fabric サービスに送信される URL パスになります。 サービスで使用するのと同じ URL パス ("/api/values" など) を指定すると、何も変更しなくても操作が正常に機能します。 バックエンドの Service Fabric サービスで使用するのとは異なる URL パスを指定することもできますが、その場合は後ほど操作ポリシー内でパスの再生成を指定する必要があります。

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies

[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) は、すべてを 1 つにまとめるバックエンド ポリシーを作成します。 このポリシーを使って、要求のルーティング先となるバックエンドの Service Fabric サービスを構成します。 このポリシーは任意の API 操作に適用できます。  詳細については、[ポリシーの概要](/azure/api-management/api-management-howto-policies)に関するページをご覧ください。

[Service Fabric のバックエンド構成](/azure/api-management/api-management-transformation-policies#SetBackendService)では、次の要求ルーティングをコントロールできます。

* サービス インスタンスの選択。Service Fabric サービス インスタンス名として、ハードコートされた名前 (`"fabric:/myapp/myservice"` など) か、HTTP 要求から生成される名前 (`"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]` など) のいずれかを指定します。
* パーティションの解決。任意の Service Fabric パーティション構成を使用して、パーティション キーを生成します。
* ステートフル サービスのレプリカの選択。
* 解決の再試行の条件。サービスの場所を再解決して要求を再送信するための条件を指定できます。

**policyContent** は、ポリシーの JSON エスケープ XML コンテンツです。  このチュートリアルでは、前にデプロイした .NET または Java ステートレス サービスに直接要求をルーティングするバックエンド ポリシーを作成します。 受信ポリシーの下に `set-backend-service` ポリシーを追加します。  *sf-service-instance-name* の値を `fabric:/ApiApplication/WebApiService` (前に .NET バックエンド サービスをデプロイした場合) または `fabric:/EchoServerApplication/EchoServerService` (Java サービスをデプロイした場合) に置き換えます。  *backend-id* は、バックエンド リソース (この場合は、*apim.json* テンプレートで定義されている `Microsoft.ApiManagement/service/backends` リソース) を参照します。 *backend-id* を使用して、API Management API を使用して作成された別のバックエンド リソースを参照することもできます。 このチュートリアルでは、*backend-id* を *service_fabric_backend_name* パラメーターの値に設定します。

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Service Fabric バックエンド ポリシーの全属性については、[API Management バックエンドに関するドキュメント](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)を参照してください。

## <a name="set-parameters-and-deploy-api-management"></a>パラメーターの設定と API Management のデプロイ

次のように、デプロイ用の *apim.parameters.json* で空のパラメーターに値を入力します。

|パラメーター|値|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|開発者|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6|
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;base-64 エンコード文字列&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|https://mysfcluster.southcentralus.cloudapp.azure.com:19080|
|inbound_policy|&lt;XML 文字列&gt;|

*certificatePassword* と *serviceFabricCertificateThumbprint* は、クラスターの設定に使用するクラスター証明書と一致する必要があります。

*serviceFabricCertificate* は base-64 エンコード文字列としての証明書で、次のスクリプトを使用して生成できます。

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

*inbound_policy* で、*sf-service-instance-name* の値を `fabric:/ApiApplication/WebApiService` (前に .NET バックエンド サービスをデプロイした場合) または `fabric:/EchoServerApplication/EchoServerService` (Java サービスをデプロイした場合) に置き換えます。 *backend-id* は、バックエンド リソース (この場合は、*apim.json* テンプレートで定義されている `Microsoft.ApiManagement/service/backends` リソース) を参照します。 *backend-id* を使用して、API Management API を使用して作成された別のバックエンド リソースを参照することもできます。 このチュートリアルでは、*backend-id* を *service_fabric_backend_name* パラメーターの値に設定します。

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

次のスクリプトを使用して、API Management 用の Resource Manager テンプレートとパラメーター ファイルをデプロイします。

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json
```

## <a name="test-it"></a>テストする

これで、[Azure Portal](https://portal.azure.com) から直接、API Management を通じて Service Fabric のバックエンド サービスに要求を送信できるようになりました。

 1. API Management サービスで、**[API]** を選択します。
 2. 前の手順で作成した **Service Fabric App** API で、**[テスト]** タブ、**[Values]** 操作の順に選択します。
 3. **[送信]** ボタンをクリックして、バックエンド サービスにテスト要求を送信します。  次のような HTTP 応答が表示されます。

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT


    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

クラスターは、クラスター リソース自体に加え、その他の Azure リソースで構成されます。 クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。

Azure にログインして、クラスターを削除するサブスクリプション ID を選択します。  サブスクリプション ID は、[Azure Portal](http://portal.azure.com) にログインして確認できます。 リソース グループとそのグループのクラスター リソースすべてを削除するには、[Remove-AzureRMResourceGroup cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドレットを使用します。

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * API Management をデプロイする
> * API Management を構成する
> * API 操作の作成
> * バックエンド ポリシーの構成
> * API を製品に追加する

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
