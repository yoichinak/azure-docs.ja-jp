---
title: VM のプライベート IP アドレスの構成 - Azure CLI | Microsoft Docs
description: Azure コマンド ライン インターフェイス (CLI) を使用して、仮想マシンのプライベート IP アドレスを構成する方法について説明します。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4f6a40fde23ee70391c5057762f17ce1eb44123
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38561130"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Azure CLI を使用して仮想マシンのプライベート IP アドレスを構成する

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、Resource Manager デプロイ モデルについて説明します。 [クラシック デプロイ モデルで静的プライベート IP アドレスを管理する](virtual-networks-static-private-ip-classic-cli.md)こともできます。

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> 次のサンプル Azure CLI コマンドは、既存の単純な環境を想定しています。 このドキュメントに表示されているコマンドを実行する場合は、まず、[vnet の作成](quick-create-cli.md)に関する記事に示されているテスト環境を構築します。

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>VM 作成時に静的プライベート IP アドレスを指定する

静的プライベート IP *192.168.1.101* を使用して、*TestVNet* という名前の VNet の *FrontEnd* サブネットで *DNS01* という名前の VM を作成するには、次の手順を実行します。

1. まだ行っていない場合は、最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールして構成し、[az login](/cli/azure/reference-index#az_login) を使用して Azure アカウントにログインします。 

2. [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) コマンドを使用して VM のパブリック IP を作成します。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。

    > [!NOTE]
    > 今回の手順とこの後の手順では、環境に応じて引数に異なる値を使用したほうが良い場合や、異なる値を使用する必要がある場合があります。
   
    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    予想される出力:
   
   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`: パブリック IP の作成場所となるリソース グループの名前。
   * `--name`: パブリック IP の名前。
   * `--location`: パブリック IP を作成する Azure リージョン。

3. [az network nic create](/cli/azure/network/nic#az_network_nic_create) コマンドを実行し、静的プライベート IP を使用して NIC を作成します。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    予想される出力:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    パラメーター:

    * `--private-ip-address`: NIC の静的プライベート IP アドレスです。
    * `--vnet-name`: NIC を作成する VNet の名前です。
    * `--subnet`: NIC を作成するためのサブネットの名前です。

4. [azure vm create](/cli/azure/vm/nic#az_vm_nic_create) コマンドを実行し、パブリック IP および上記で作成した NIC を使用して VM を作成します。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    予想される出力:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   基本的な [az vm create](/cli/azure/vm#az_vm_create) パラメーター以外のパラメーター。

   * `--nics`: VM が接続されている NIC の名前。
   
VM のオペレーティング システム内で Azure 仮想マシンに割り当てられるプライベート IP は、[Windows VM に複数の IP アドレスを割り当てる](virtual-network-multiple-ip-addresses-cli.md)場合など、必要でない限り静的に割り当てないことをお勧めします。 実際にオペレーティング システム内でプライベート IP アドレスを手動で設定する場合は、それが Azure [ネットワーク インターフェイス](virtual-network-network-interface-addresses.md#change-ip-address-settings)に割り当てられているプライベート IP アドレスと同じアドレスであるようにしてください。そうしないと、仮想マシンへの接続が失われる可能性があります。 詳細については、[プライベート IP アドレス](virtual-network-network-interface-addresses.md#private)設定に関するページを参照してください。

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>VM の静的プライベート IP アドレス情報を取得する

次の Azure CLI コマンドを実行し、*Private IP alloc-method* と *Private IP アドレス*の値を確認します。

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

予想される出力:

```json
"192.168.1.101"
```

当該 VM の NIC の具体的な IP 情報を表示するには、NIC に具体的なクエリを実行します。

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

結果は次のようになります。

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>VM から静的プライベート IP アドレスを削除する

Azure Resource Manager デプロイ用の Azure CLI では、NIC から静的プライベート IP アドレスを削除することはできません。 前提条件は、以下のとおりです。
- 動的 IP を使用した新しい NIC の作成
- 新しく作成された NIC の VM に NIC を設定します。 

前述のコマンドで使用されている VM の NIC を変更するには、次の手順を実行します。

1. **azure network nic create** コマンドを実行し、動的 IP 割り当てと新しい IP アドレスを使用して、新しい NIC を作成します。 IP アドレスが指定されていないため、割り当て方法は**動的**です。

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    予想される出力:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. **azure vm set** コマンドを実行し、VM によって使用される NIC を変更します。
   
    ```azurecli
    azure vm set -g TestRG -n DNS01 -N TestNIC2
    ```

    予想される出力:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > VM が複数の NIC を持つのに十分な大きさである場合、**azure network nic delete** コマンドを実行して、古い NIC を削除します。

## <a name="next-steps"></a>次の手順

[IP アドレス設定](virtual-network-network-interface-addresses.md)の管理について学習します。