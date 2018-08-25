---
title: 複数の Web サイトをホストするアプリケーション ゲートウェイの作成 - Azure Portal | Microsoft Docs
description: Azure Portal を使用して複数の Web サイトをホストするアプリケーション ゲートウェイを作成する方法について説明します。
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/28/2017
ms.author: victorh
ms.openlocfilehash: 8f2e2500c39f42ebd7fefb3cec941088edf126f2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045021"
---
# <a name="create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Azure Portal を使用して複数の Web サイトをホストするアプリケーション ゲートウェイを作成し、構成します。

[アプリケーション ゲートウェイ](overview.md)を作成するときに、Azure Portal を使用して[複数の Web サイト](multiple-site-overview.md)のホスティングを構成できます。 このチュートリアルでは、仮想マシンを使用してバックエンド アドレス プールを定義します。 その後、Web トラフィックがプール内の適切なサーバーに確実に到着するように、所有するドメインに基づいてリスナーと規則を構成します。 このチュートリアルでは、複数のドメインを所有していることを前提として、*www.contoso.com* と *www.fabrikam.com* の例を使用します。

この記事では、次のことについて説明します:

> [!div class="checklist"]
> * アプリケーション ゲートウェイの作成
> * バックエンド サーバー用の仮想マシンの作成
> * バックエンド サーバーでのバックエンド プールの作成
> * バックエンド リスナーの作成
> * ルーティング規則の作成
> * ドメインの CNAME レコードの作成

![マルチサイト ルーティングの例](./media/create-multiple-sites-portal/scenario.png)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal ([http://portal.azure.com](http://portal.azure.com)) にログインする

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

作成したリソース間の通信には仮想ネットワークが必要です。 この例では 2 つのサブネットが作成されます。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。 仮想ネットワークは、アプリケーション ゲートウェイを作成するときに同時に作成できます。

1. Azure Portal の左上にある **[新規]** をクリックします。
2. **[ネットワーク]** を選択し、注目のリストで **[Application Gateway]** を選択します。
3. 次のアプリケーション ゲートウェイの値を入力します。

    - *myAppGateway* - アプリケーション ゲートウェイの名前です。
    - *myResourceGroupAG* - 新しいリソース グループの名前です。

    ![新しいアプリケーション ゲートウェイの作成](./media/create-multiple-sites-portal/application-gateway-create.png)

4. 他の設定は既定値をそのまま使用し、**[OK]** をクリックします。
5. **[仮想ネットワークの選択]**、**[新規作成]** の順にクリックし、次の仮想ネットワークの値を入力します。

    - *myVNet* - 仮想ネットワークの名前です。
    - *10.0.0.0/16* - 仮想ネットワークのアドレス空間です。
    - *myAGSubnet* - サブネットの名前です。
    - *10.0.0.0/24* - サブネットのアドレス空間です。

    ![Create virtual network](./media/create-multiple-sites-portal/application-gateway-vnet.png)

6. **[OK]** をクリックして、仮想ネットワークとサブネットを作成します。
7. **[パブリック IP アドレスの選択]**、**[新規作成]** の順にクリックし、パブリック IP アドレスの名前を入力します。 この例では、パブリック IP アドレスの名前は *myAGPublicIPAddress* にします。 他の設定は既定値をそのまま使用し、**[OK]** をクリックします。
8. リスナーの構成は既定値をそのまま使用し、Web アプリケーション ファイアウォールは無効のままにして、**[OK]** をクリックします。
9. 概要ページで設定を確認し、**[OK]** をクリックして、ネットワーク リソースとアプリケーション ゲートウェイを作成します。 アプリケーション ゲートウェイの作成には数分かかる場合があります。デプロイが正常に終了するのを待ち、その後で次のセクションに進みます。

### <a name="add-a-subnet"></a>サブネットの追加

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myVNet]** をクリックします。
2. **[サブネット]**、**[サブネット]** の順にクリックします。

    ![サブネットの作成](./media/create-multiple-sites-portal/application-gateway-subnet.png)

3. サブネットの名前として「*myBackendSubnet*」を入力し、**[OK]** をクリックします。

## <a name="create-virtual-machines"></a>仮想マシンを作成する

この例では、アプリケーション ゲートウェイのバックエンド サーバーとして使用する 2 つの仮想マシンを作成します。 また、IIS を仮想マシンにインストールして、トラフィックが正常にルーティングされていることを確認します。

1. **[新規]** をクリックします。
2. **[コンピューティング]** をクリックし、注目のリストで **[Windows Server 2016 Datacenter]** を選択します。
3. 次の仮想マシンの値を入力します。

    - *contosoVM* - 仮想マシンの名前です。
    - *azureuser* - 管理者のユーザー名です。
    - *Azure123456!* パスワードです。
    - **[既存のものを使用]**、*[myResourceGroupAG]* の順に選択します。

4. Click **OK**.
5. 仮想マシンのサイズとして **[DS1_V2]** を選択し、**[選択]** をクリックします。
6. 仮想ネットワークに対して **[myVNet]** が選択されていること、およびサブネットが **myBackendSubnet** であることを確認します。 
7. **[無効]** をクリックして、ブート診断を無効にします。
8. **[OK]** をクリックし、概要ページの設定を確認して、**[作成]** をクリックします。

### <a name="install-iis"></a>IIS のインストール

1. 対話型シェルを開いて、**PowerShell** に設定されていることを確認します。

    ![カスタム拡張機能のインストール](./media/create-multiple-sites-portal/application-gateway-extension.png)

2. 次のコマンドを実行して、IIS を仮想マシンにインストールします。 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. 2 番目の仮想マシンを作成し、終了したばかりの手順を使用して、IIS をインストールします。 名前および Set-AzureRmVMExtension の VMName の値として、*fabrikamVM* という名前を入力します。

## <a name="create-backend-pools-with-the-virtual-machines"></a>仮想マシンでのバックエンド プールの作成

1. **[すべてのリソース]**、**[myAppGateway]** の順にクリックします。
2. **[バックエンド プール]**、**[追加]** の順にクリックします。
3. *contosoPool* という名前を入力し、**[ターゲットの追加]** を使用して *contosoVM* を追加します。

    ![バックエンド サーバーの追加](./media/create-multiple-sites-portal/application-gateway-multisite-backendpool.png)

4. Click **OK**.
5. **[バックエンド プール]**、**[追加]** の順にクリックします。
6. 終了したばかりの手順を使用して、*fabrikamVM* を含む *fabrikamPool* を作成します。

## <a name="create-backend-listeners"></a>バックエンド リスナーの作成

1. **[リスナー]**、**[マルチサイト]** の順にクリックします。
2. 次のリスナーの値を入力します。
    
    - *contosoListener* - リスナーの名前です。
    - *www.contoso.com* - このホスト名の例をドメイン名に置き換えます。

3. Click **OK**.
4. *fabrikamListener* という名前を使用して 2 番目のリスナーを作成し、2 番目のドメイン名を使用します。 この例では、*www.fabrikam.com* が使用されています。

## <a name="create-routing-rules"></a>ルーティング規則の作成

ルールはリストの順序どおりに処理され、トラフィックは、具体性にかかわらず最初に一致したルールを使用してリダイレクトされます。 たとえば、同一のポート上に基本リスナーを使用するルールとマルチサイト リスナーを使用するルールがある場合、マルチサイトのルールを適切に動作させるには、リストでマルチサイト リスナーのルールを基本リスナーのルールよりも前に配置する必要があります。 

この例では、2 つの新しい規則を作成し、アプリケーション ゲートウェイを作成したときに作成された既定の規則を削除します。 

1. **[ルール]**、**[基本]** の順にクリックします。
2. 名前として「*contosoRule*」と入力します。
3. リスナーとして *[contosoListener]* を選択します。
4. バックエンド プールとして *[contosoPool]* を選択します。

    ![パス ベース ルールの作成](./media/create-multiple-sites-portal/application-gateway-multisite-rule.png)

5. Click **OK**.
6. *fabrikamRule*、*fabrikamListener*、および *fabrikamPool* の名前を使用して 2 番目のルールを作成します。
7. *rule1* という名前の既定のルールをクリックし、**[削除]** をクリックしてこのルールを削除します。

## <a name="create-a-cname-record-in-your-domain"></a>ドメインの CNAME レコードの作成

パブリック IP アドレスを使用してアプリケーション ゲートウェイを作成した後は、DNS アドレスを取得し、これを使用してドメインに CNAME レコードを作成できます。 アプリケーション ゲートウェイを再起動すると VIP が変更される可能性があるため、A レコードの使用はお勧めしません。

1. **[すべてのリソース]**、**[myAGPublicIPAddress]** の順にクリックします。

    ![アプリケーション ゲートウェイの DNS アドレスの記録](./media/create-multiple-sites-portal/application-gateway-multisite-dns.png)

2. DNS アドレスをコピーし、これをドメイン内の新しい CNAME レコードの値として使用します。

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

1. ブラウザーのアドレス バーにドメイン名を入力します。 http://www.contoso.com など。

    ![アプリケーション ゲートウェイの contoso サイトをテストする](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. アドレスをもう 1 つのドメインに変更します。次の例のように表示されます。

    ![アプリケーション ゲートウェイの fabrikam サイトをテストする](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーション ゲートウェイの作成
> * バックエンド サーバー用の仮想マシンの作成
> * バックエンド サーバーでのバックエンド プールの作成
> * バックエンド リスナーの作成
> * ルーティング規則の作成
> * ドメインの CNAME レコードの作成
