---
title: Linux 用 Azure Disk Encryption | Microsoft Docs
description: 仮想マシン拡張機能を使用して、Linux 用 Azure Disk Encryption を仮想マシンにデプロイします。
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 36e8875e91e2f04dbb60bab3211f07b2053e78f5
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414774"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Linux 用 Azure Disk Encryption (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>概要

Azure Disk Encryption は、Linux の DM-Crypt サブシステムを活用して、[特定の Azure Linux ディストリビューション](https://aka.ms/adelinux)でディスク全体の暗号化を提供します。  このソリューションは、ディスク暗号化キーとシークレットを管理できるように、Azure Key Vault と統合されています。

## <a name="prerequisites"></a>前提条件

前提条件の詳細な一覧については、[Azure Disk Encryption の前提条件](
../../security/azure-security-disk-encryption-prerequisites.md)に関するセクションを参照してください。

### <a name="operating-system"></a>オペレーティング システム

現在、Azure Disk Encryption は、特定のディストリビューションとバージョンでサポートされています。  サポートされている Linux ディストリビューションの一覧については、「[Azure Disk Encryption に関する FAQ](../../security/azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport)」を参照してください。

### <a name="internet-connectivity"></a>インターネット接続

Linux 用 Azure Disk Encryption では、Active Directory、Key Vault、Storage、パッケージ管理エンドポイントにアクセスするためにインターネット接続が必要です。  詳細については、[Azure Disk Encryption の前提条件](../../security/azure-security-disk-encryption-prerequisites.md)に関するセクションを参照してください。

## <a name="extension-schema"></a>拡張機能のスキーマ

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

### <a name="property-values"></a>プロパティ値

| Name | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | 文字列 |
| type | AzureDiskEncryptionForLinux | 文字列 |
| typeHandlerVersion | 0.1、1.1 (VMSS) | int |
| AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| AADClientSecret | password | 文字列 |
| AADClientCertificate | thumbprint | 文字列 |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 辞書 |
| EncryptionOperation | EnableEncryption、EnableEncryptionFormatAll | 文字列 | 
| KeyEncryptionAlgorithm | 'RSA-OAEP'、'RSA-OAEP-256'、'RSA1_5' | 文字列 |
| KeyEncryptionKeyURL | url | 文字列 |
| KeyVaultURL | url | 文字列 |
| パスフレーズ | password | 文字列 | 
| SequenceVersion | uniqueidentifier | 文字列 |
| VolumeType | OS、Data、All | 文字列 |

## <a name="template-deployment"></a>テンプレートのデプロイ

テンプレートのデプロイの例については、「[Enable Encryption on a running Linux VM (実行中の Linux VM で暗号化を有効にする)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)」を参照してください。

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

手順については、最新の [Azure CLI ドキュメント](/cli/azure/vm/encryption?view=azure-cli-latest)を参照してください。 

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

トラブルシューティングについては、「[Azure Disk Encryption トラブルシューティング ガイド](../../security/azure-security-disk-encryption-tsg.md)」を参照してください。

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次の手順

VM 拡張機能の詳細については、「[Linux 用の仮想マシンの拡張機能とその機能](features-linux.md)」を参照してください。