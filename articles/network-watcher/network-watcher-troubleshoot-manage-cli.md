---
title: Azure 仮想ネットワーク ゲートウェイと接続のトラブルシューティング - Azure CLI 2.0 | Microsoft Docs
description: このページでは Azure Network Watcher を使用して Azure CLI 2.0 のトラブルシューティングを行う方法を説明します。
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: 5f843b42a108968e2fbefacddcd22f331a04691e
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091103"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli-20"></a>Azure Network Watcher Azure CLI 2.0 を使用した仮想ネットワーク ゲートウェイと接続のトラブルシューティング

> [!div class="op_single_selector"]
> - [ポータル](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher は、Azure 内のネットワーク リソースの把握に関する多くの機能を提供します。 これらの機能の 1 つが、リソースのトラブルシューティングです。 リソースのトラブルシューティングは、ポータル、PowerShell、CLI、または REST API から呼び出すことができます。 呼び出されると、Network Watcher は仮想ネットワーク ゲートウェイまたは接続の正常性を検査し、その結果を返します。

この記事では、Azure CLI 2.0 を使用します。Azure CLI 2.0 は、Resource Manager デプロイメント モデルを対象とする次世代 CLI であり、Windows、Mac、Linux で利用できます。

この記事の手順を実行するには、[Mac、Linux、Windows 用の Azure コマンドライン インターフェイス (Azure CLI) をインストール](https://docs.microsoft.com/cli/azure/install-az-cli2)する必要があります。

## <a name="before-you-begin"></a>開始する前に

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関するページの手順を参照して、Network Watcher を作成済みであることを前提としています。

サポートされるゲートウェイの種類の一覧については、「[Supported Gateway types (サポートされるゲートウェイの種類)](network-watcher-troubleshoot-overview.md#supported-gateway-types)」を参照してください。

## <a name="overview"></a>概要

リソースのトラブルシューティングを使用すると、仮想ネットワーク ゲートウェイと接続によって生じる問題をトラブルシューティングできます。 リソースのトラブルシューティングに対する要求が行われると、ログのクエリが実行され、検査されます。 検査が完了すると、結果が返されます。 リソースのトラブルシューティングの要求は長時間実行される要求であり、結果が返るまで数分かかることがあります。 トラブルシューティングのログは、指定したストレージ アカウントのコンテナーに格納されます。

## <a name="retrieve-a-virtual-network-gateway-connection"></a>仮想ネットワーク ゲートウェイ接続の取得

この例では、リソースのトラブルシューティングは接続上で実行されています。 仮想ネットワーク ゲートウェイを渡すこともできます。 次のコマンドレットは、リソース グループ内の VPN 接続を一覧表示します。

```azurecli
az network vpn-connection list --resource-group resourceGroupName
```

接続の名前を確認したら、このコマンドを実行してアカウントのリソース ID を取得できます。

```azurecli
az network vpn-connection show --resource-group resourceGroupName --ids vpnConnectionIds
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

リソースのトラブルシューティングではリソースの正常性に関するデータを返すとともに、レビュー用にストレージ アカウントにログを保存します。 この手順では、ストレージ アカウントを作成します。既存のストレージ アカウントが存在する場合は、それを使用できます。

1. ストレージ アカウントの作成

    ```azurecli
    az storage account create --name storageAccountName --location westcentralus --resource-group resourceGroupName --sku Standard_LRS
    ```

1. ストレージ アカウント キーの取得

    ```azurecli
    az storage account keys list --resource-group resourcegroupName --account-name storageAccountName
    ```

1. コンテナーの作成

    ```azurecli
    az storage container create --account-name storageAccountName --account-key {storageAccountKey} --name logs
    ```

## <a name="run-network-watcher-resource-troubleshooting"></a>Network Watcher のリソースのトラブルシューティングの実行

リソースのトラブルシューティングは、`az network watcher troubleshooting` コマンドレットを使用して行います。 このコマンドレットに、リソース グループ、Network Watcher の名前、接続の ID、ストレージ アカウントの ID、トラブルシューティングの結果を格納する BLOB のパスを渡します。

```azurecli
az network watcher troubleshooting start --resource-group resourceGroupName --resource resourceName --resource-type {vnetGateway/vpnConnection} --storage-account storageAccountName  --storage-path https://{storageAccountName}.blob.core.windows.net/{containerName}
```

コマンドレットを実行すると、Network Watcher によってリソースがレビューされて正常性が検証されます。 結果はシェルに戻され、指定したストレージ アカウントに結果のログが保存されます。

## <a name="understanding-the-results"></a>結果について

アクション テキストは、問題を解決する方法の一般的なガイダンスを提供します。 問題に対してアクションを実施できる場合は、追加のガイダンスのリンクが提供されます。 追加のガイダンスがない場合は、サポート ケースを開くための URL が応答で提供されます。  応答のプロパティと内容の詳細については、[Network Watcher のトラブルシューティングの概要](network-watcher-troubleshoot-overview.md)に関するページを参照してください。

Azure ストレージ アカウントからファイルをダウンロードする方法については、「[.NET を使用して Azure BLOB ストレージを使用する](../storage/blobs/storage-dotnet-how-to-use-blobs.md)」を参照してください。 使用できるツールとして他に Storage Explorer があります。 ストレージ エクスプローラーの詳細については、[ストレージ エクスプローラー](http://storageexplorer.com/)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

設定が変更されて VPN 接続が停止される場合は、[ネットワーク セキュリティ グループの管理](../virtual-network/manage-network-security-group.md)に関する記事を参照して、問題がある可能性のあるネットワーク セキュリティ グループとセキュリティ規則を詳しく調べます。
