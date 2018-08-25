---
title: チュートリアル - Azure CLI 2.0 を使用したスケール セットへのアプリケーションのインストール | Microsoft Docs
description: Azure CLI 2.0 とカスタム スクリプト拡張機能を使用して仮想マシン スケール セットにアプリケーションをインストールする方法について説明します
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fe1fd957176762c5cc04145f56559b50667c476c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38606527"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-the-azure-cli-20"></a>チュートリアル: Azure CLI 2.0 を使用した仮想マシン スケール セットへのアプリケーションのインストール
スケール セット内の仮想マシン (VM) インスタンスでアプリケーションを実行する　には、まず、アプリケーション コンポーネントと必要なファイルをインストールする必要があります。 前のチュートリアルでは、カスタム VM イメージを作成および使用して VM インスタンスをデプロイする方法について学習しました。 このカスタム イメージには、手動によるアプリケーションのインストールと構成が含まれていました。 このほか、各 VM インスタンスがデプロイされた後のスケール セットへのアプリケーションのインストールを自動化したり、既にスケール セットで実行されているアプリケーションを更新したりできます。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * スケール セットへのアプリケーションの自動インストール
> * Azure カスタム スクリプト拡張機能の使用
> * スケール セットで実行中のアプリケーションの更新

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.29 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 


## <a name="what-is-the-azure-custom-script-extension"></a>Azure カスタム スクリプト拡張機能とは
カスタム スクリプト拡張機能は、Azure VM でスクリプトをダウンロードし、実行します。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 スクリプトは、Azure ストレージや GitHub からダウンロードできます。また、拡張機能の実行時に Azure Portal に提供することもできます。

カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合されており、Azure CLI 2.0、Azure PowerShell、Azure Portal、または REST API を介して使用することもできます。 詳細については、「[Windows のカスタム スクリプト拡張機能](../virtual-machines/linux/extensions-customscript.md)」を参照してください。

カスタム スクリプト拡張機能を Azure CLI で使用するには、取得するファイルと実行するコマンドが定義された JSON ファイルを作成します。 これらの JSON 定義は、一貫したアプリケーション インストールを適用するためにスケール セット デプロイメント全体で再利用することができます。


## <a name="create-custom-script-extension-definition"></a>カスタム スクリプト拡張機能の定義の作成
カスタム スクリプト拡張機能が動作していることを確認するには、NGINX Web サーバーをインストールしてスケール セット VM インスタンスのホスト名を出力する機能を備えたスケール セットを作成します。 次のカスタム スクリプト拡張機能の定義によって、GitHub からサンプル スクリプトがダウンロードされ、必要なパッケージがインストールされた後、VM インスタンスのホスト名が基本的な HTML ページに書き込まれます。

現在のシェルで、*customConfig.json* というファイルを作成し、次の構成を貼り付けます。 たとえば、ローカル コンピューター上にない Cloud Shell でファイルを作成します。 任意のエディターを使用することができます。 Cloud Shell で `sensible-editor cloudConfig.json` を入力し、ファイルを作成して使用可能なエディターの一覧を確認します。

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```


## <a name="create-a-scale-set"></a>スケール セットを作成する
[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

ここでは、[az vmss create](/cli/azure/vmss#create) を使って仮想マシン スケール セットを作成します。 次の例では、*myScaleSet* という名前のスケール セットを作成し、存在しない場合は SSH キーを生成します。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


## <a name="apply-the-custom-script-extension"></a>カスタム スクリプト拡張機能の適用
[az vmss 拡張機能セット](/cli/azure/vmss/extension#set)を使用して、カスタム スクリプト拡張機能構成をスケール セット内の VM インスタンスに適用します。 次の例では、*customConfig.json* 構成を *myResourceGroup* という名前のリソース グループ内の *myScaleSet* VM インスタンスに適用します。

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings @customConfig.json
```

スケール セット内の各 VM インスタンスが、GitHub からスクリプトをダウンロードして実行します。 より複雑な例では、複数のアプリケーション コンポーネントおよびファイルをインストールできます。 スケール セットがスケールアップされた場合、新しい VM インスタンスで同じカスタム スクリプト拡張機能定義が自動的に適用され、必要なアプリケーションがインストールされます。


## <a name="test-your-scale-set"></a>スケール セットのテスト
トラフィックが Web サーバーに到達できるようにするには、[az network lb rule creat](/cli/azure/network/lb/rule#create) を使用してロード バランサー ルールを作成します。 次の例では、*myLoadBalancerRuleWeb* という名前の規則を作成します。

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Web サーバーが動いていることを確認するには、[az network public-ip show](/cli/azure/network/public-ip#show) でロード バランサーのパブリック IP アドレスを取得します。 次の例では、スケール セットの一部として作成された *myScaleSetLBPublicIP* の IP アドレスを取得します。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

ロード バランサーのパブリック IP アドレスを Web ブラウザーに入力します。 ロード バランサーは、次の例に示すように、VM インスタンスのいずれかにトラフィックを配分します。

![Nginx の基本的な Web ページ](media/tutorial-install-apps-cli/running-nginx.png)

次の手順で更新されたバージョンを確認できるように、Web ブラウザーを開いたままにしておきます。


## <a name="update-app-deployment"></a>アプリのデプロイの更新
スケール セットのライフサイクル全体にわたり、アプリケーションの更新されたバージョンをデプロイする必要があります。 カスタム スクリプト拡張機能を使用すると、更新されたデプロイ スクリプトを参照し、拡張機能をスケール セットに再適用することができます。 前の手順で作成したスケール セットでは、`--upgrade-policy-mode` が *automatic* に設定されていました。 この設定により、スケール セット内の VM インスタンスは、自動的にアプリケーションを更新して最新バージョンを適用できます。

現在のシェルで、*customConfigv2.json* というファイルを作成し、次の構成を貼り付けます。 この定義では、アプリケーションのインストール スクリプトの更新された *v2* バージョンを実行します。

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

[az vmss extension set](/cli/azure/vmss/extension#set) を使用して、もう一度カスタム スクリプト拡張機能構成をスケール セット内の VM インスタンスに適用します。 *customConfigv2.json* は、アプリケーションの更新されたバージョンを適用するために使用されています。

```azurecli-interactive
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfigv2.json
```

スケール セット内のすべての VM インスタンスが、サンプル Web ページの最新バージョンで自動的に更新されます。 更新されたバージョンを確認するには、ブラウザーで Web サイトを更新します。

![Nginx の更新された Web ページ](media/tutorial-install-apps-cli/running-nginx-updated.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ
スケール セットと追加のリソースを削除するには、[az group delete](/cli/azure/group#az_group_delete) を使用して、リソース グループとそのすべてのリソースを削除します。 `--no-wait` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。 `--yes` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure CLI 2.0 を使用して自動的にアプリケーションをスケール セットにインストールし、更新する方法について学習しました。

> [!div class="checklist"]
> * スケール セットへのアプリケーションの自動インストール
> * Azure カスタム スクリプト拡張機能の使用
> * スケール セットで実行中のアプリケーションの更新

次のチュートリアルに進み、スケール セットを自動的にスケーリングする方法を学習してください。

> [!div class="nextstepaction"]
> [スケール セットを自動的にスケーリングする](tutorial-autoscale-cli.md)