---
title: PaaS リソースへのネットワーク アクセスを制限する - Azure CLI | Microsoft Docs
description: この記事では、Azure CLI を使って仮想ネットワーク サービスのエンドポイントで Azure Storage、Azure SQL Database などの Azure リソースへのアクセスを制限する方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f357861a7a44b249e06f091a8693b7f2d8dd5178
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232743"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Azure CLI を使用して仮想ネットワーク サービスのエンドポイントで PaaS リソースへのネットワーク アクセスを制限する

仮想ネットワーク サービスのエンドポイントを使用すると、一部の Azure サービス リソースへのネットワーク アクセスを、仮想ネットワーク サブネットに制限できます。 また、リソースに対するインターネット アクセスを排除することもできます。 サービス エンドポイントにより、使用している仮想ネットワークからサポートされている Azure サービスへの直接接続が提供されるため、ご自身の仮想ネットワークのプライベート アドレス スペースを使用して、Azure サービスにアクセスできるようになります。 サービス エンドポイントを介して Azure リソースに送信されるトラフィックは、常に Microsoft Azure のバックボーン ネットワーク上に留まります。 この記事では、次のことについて説明します:

* 1 つのサブネットを含む仮想ネットワークを作成する
* サブネットを追加し、サービス エンドポイントを有効にする
* Azure リソースを作成し、サブネットからのみネットワーク アクセスできるようにする
* 各サブネットに仮想マシン (VM) をデプロイする
* サブネットからリソースへのアクセスを確認する
* サブネットおよびインターネットからリソースへのアクセスが拒否されたことを確認する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用することを選択する場合、このクイック スタートでは、Azure CLI バージョン 2.0.28 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

仮想ネットワークを作成する前に、仮想ネットワークのリソース グループと、この記事で作成された他のすべてのリソースを作成する必要があります。 [az group create](/cli/azure/group#az_group_create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) で、1 つのサブネットを含む仮想ネットワークを作成します。

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>サービス エンドポイントを有効にする 

サービス エンドポイントをサポートするサービスについてのみ、サービス エンドポイントを有効にできます。 [az network vnet list-endpoint-services](/cli/azure/network/vnet#az_network_vnet_list_endpoint_services) を使って、Azure の場所で使えるサービス エンドポイント対応のサービスを表示します。 次の例では、*eastus* リージョンで使える、サービス エンドポイント対応サービスの一覧が返されます。 返されるサービスの一覧は、サービス エンドポイント対応の Azure サービスが増えるにつれて、時間の経過と共に大きくなります。

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) を使って、追加のサブネットを仮想ネットワークに作成します。 次の例では、*Microsoft.Storage* のサービス エンドポイントをサブネットに作成します。 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>サブネットのネットワーク アクセスを制限する

[az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) で、ネットワーク セキュリティ グループを作成します。 次の例では、*myNsgPrivate* という名前のネットワーク セキュリティ グループを作成します。

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

[az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) を使って、ネットワーク セキュリティ グループを *Private* サブネットに関連付けます。 次の例では、*myNsgPrivate* ネットワーク セキュリティ グループを *Private* サブネットに関連付けます。

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) でセキュリティ規則を作成します。 次の規則は、Azure Storage サービスに割り当てられたパブリック IP アドレスへの送信アクセスを許可します。 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"

Each network security group contains several [default security rules](security-overview.md#default-security-rules). The rule that follows overrides a default security rule that allows outbound access to all public IP addresses. The `destination-address-prefix "Internet"` option denies outbound access to all public IP addresses. The previous rule overrides this rule, due to its higher priority, which allows access to the public IP addresses of Azure Storage.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"

The following rule allows SSH traffic inbound to the subnet from anywhere. The rule overrides a default security rule that denies all inbound traffic from the internet. SSH is allowed to the subnet so that connectivity can be tested in a later step.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>リソースへのネットワーク アクセスを制限する

サービス エンドポイントを有効にした Azure サービスを介して作成されたリソースへのネットワーク アクセスを制限するために必要な手順は、サービスによって異なります。 各サービスの具体的な手順については、それぞれのサービスのドキュメントをご覧ください。 この記事の残りの部分では、例として、Azure ストレージ アカウントのネットワーク アクセスを制限する手順を示します。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[az storage account create](/cli/azure/storage/account#az_storage_account_create) で Azure ストレージ アカウントを作成します。 `<replace-with-your-unique-storage-account-name>` を Azure 全体で一意の名前 (3 ～ 24 文字で、数字と小文字のみを使用) に置き換えます。

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

ストレージ アカウントを作成した後、[az storage account show-connection-string](/cli/azure/storage/account#az_storage_account_show_connection_string) を使って、ストレージ アカウントの接続文字列を変数に取得します。 接続文字列は、後の手順でファイル共有を作成するときに使います。

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>変数の内容を表示し、出力で返された **AccountKey** の値を書き留めます。後の手順でこの値を使います。

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>ストレージ アカウントにファイル共有を作成する

[az storage share create](/cli/azure/storage/share#az_storage_share_create) を使って、ストレージ アカウントにファイル共有を作成します。 後の手順では、このファイル共有をマウントして、それへのネットワーク アクセスを確認します。

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>ストレージ アカウントへのすべてのネットワーク アクセスを拒否する

既定では、ストレージ アカウントは、任意のネットワーク上のクライアントからのネットワーク接続を受け入れます。 選んだネットワークへのアクセスを制限するには、[az storage account update](/cli/azure/storage/account#az_storage_account_update) で既定のアクションを *Deny* に変更します。 ネットワーク アクセスが拒否されると、ストレージ アカウントには、どのネットワークからもアクセスできなくなります。

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>サブネットからのネットワーク アクセスを有効にする

[az storage account network-rule add](/cli/azure/storage/account/network-rule#az_storage_account_network_rule_add) を使って、*Private* サブネットからストレージ アカウントへのネットワーク アクセスを許可します。

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>仮想マシンを作成する

ストレージ アカウントへのネットワーク アクセスをテストするには、各サブネットに VM をデプロイします。

### <a name="create-the-first-virtual-machine"></a>最初の仮想マシンを作成する

[az vm create](/cli/azure/vm#az_vm_create) を使用して、*パブリック* サブネット内に VM を作成します。 既定のキーの場所にまだ SSH キーが存在しない場合は、コマンドを使って SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

VM の作成には数分かかります。 VM が作成されると、Azure CLI によって次の例のような情報が表示されます。 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

返された出力の **publicIpAddress** を書き留めておきます。 このアドレスは、後の手順で、インターネットから VM にアクセスするときに使います。

### <a name="create-the-second-virtual-machine"></a>2 番目の仮想マシンを作成する

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

VM の作成には数分かかります。 作成された後、返された出力の **publicIpAddress** を書き留めておきます。 このアドレスは、後の手順で、インターネットから VM にアクセスするときに使います。

## <a name="confirm-access-to-storage-account"></a>ストレージ アカウントへのアクセスを確認する

*myVmPrivate* VM に SSH で接続します。 *<publicIpAddress>* を *myVmPrivate* VM のパブリック IP アドレスに置き換えます。

```bash 
ssh <publicIpAddress>
```

マウント ポイントのフォルダーを作成します。

```bash
sudo mkdir /mnt/MyAzureFileShare
```

作成したディレクトリに Azure のファイル共有をマウントします。 次のコマンドを実行する前に、`<storage-account-name>` をアカウント名に置き換え、`<storage-account-key>` を「[ストレージ アカウントの作成](#create-a-storage-account)」で取得したキーに置き換えます。

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

`user@myVmPrivate:~$` というプロンプトが表示されます。 Azure ファイル共有は */mnt/MyAzureFileShare* に正常にマウントされました。

VM から他のパブリック IP アドレスへの送信接続がないことを確認します。

```bash
ping bing.com -c 4
```

応答はありません。これは、*Private* サブネットに関連付けられているネットワーク セキュリティ グループでは、Azure Storage サービスに割り当てられているアドレス以外のパブリック IP アドレスへの発信アクセスが許可されていないためです。

*myVmPrivate* VM への SSH セッションを終了します。

## <a name="confirm-access-is-denied-to-storage-account"></a>ストレージ アカウントへのアクセスが拒否されたことを確認する

次のコマンドを使って、*myVmPublic* VM との SSH セッションを作成します。 `<publicIpAddress>` を *myVmPublic* VM のパブリック IP アドレスに置き換えます。 

```bash 
ssh <publicIpAddress>
```

マウント ポイント用のディレクトリを作成します。

```bash
sudo mkdir /mnt/MyAzureFileShare
```

作成したディレクトリに Azure ファイル共有のマウントを試みます。 この記事では、最新バージョンの Ubuntu を展開してあるものとします。 以前のバージョンの Ubuntu を使っている場合は、「[Linux でのマウント](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」でファイル共有のマウントに関する追加説明を参照してください。 次のコマンドを実行する前に、`<storage-account-name>` をアカウント名に置き換え、`<storage-account-key>` を「[ストレージ アカウントの作成](#create-a-storage-account)」で取得したキーに置き換えます。

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

*myVmPublic* VM が *Public* サブネットに展開されているため、アクセスは拒否されて、`mount error(13): Permission denied` エラーを受け取ります。 *Public* サブネットでは、Azure Storage に対してサービス エンドポイントが有効になっていません。ストレージ アカウントが許可しているのは *Public* サブネットからではなく、*Private* サブネットからのネットワーク アクセスです。

*myVmPublic* VM への SSH セッションを終了します。

お使いのコンピューターから、[az storage share list](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) を使って、ストレージ アカウントの共有の表示を試みます。 `<account-name>` をストレージ アカウント名に置き換え、`<account-key>` を「[ストレージ アカウントの作成](#create-a-storage-account)」で取得したキーに置き換えます。

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

アクセスが拒否され、"*This request is not authorized to perform this operation (この要求には、この操作を実行する権限がありません)*" というエラーが発生します。これは、使っているコンピューターが *MyVirtualNetwork* 仮想ネットワークの *Private* サブネットにないためです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、[az group delete](/cli/azure#az_group_delete) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次の手順

この記事では、仮想ネットワーク サブネットのサービス エンドポイントを有効にしました。 複数の Azure サービスでデプロイされているリソースに対して、サービス エンドポイントを有効にできることを学習しました。 Azure ストレージ アカウントを作成し、そのストレージ アカウントへのネットワーク アクセスを、仮想ネットワーク サブネット内のリソースだけに制限しました。 サービス エンドポイントの詳細については、[サービス エンドポイントの概要](virtual-network-service-endpoints-overview.md)と[サブネットの管理](virtual-network-manage-subnet.md)に関するページをご覧ください。

アカウントに複数の仮想ネットワークがある場合は、各仮想ネットワーク内のリソースが相互に通信できるように、2 つの仮想ネットワークを接続することもできます。 方法については、[仮想ネットワークの接続](tutorial-connect-virtual-networks-cli.md)に関するページをご覧ください。