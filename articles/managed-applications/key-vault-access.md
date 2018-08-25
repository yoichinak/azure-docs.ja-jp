---
title: Azure Key Vault を Managed Applications で使用する | Microsoft Docs
description: Managed Applications のデプロイ時に Azure Key Vault でアクセス シークレットを使用する方法を示します。
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 07/11/2018
ms.author: tomfitz
ms.openlocfilehash: f091ba44a3170dcc4141829f2f4105d6e7993cdf
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035291"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Azure Managed Applications のデプロイ時に Key Vault シークレットにアクセスする

デプロイ時に、セキュリティで保護された値 (パスワードなど) をパラメーターとして渡す必要がある場合は、[Azure Key Vault](../key-vault/key-vault-whatis.md) からその値を取得できます。 Managed Applications のデプロイ時に Key Vault にアクセスするには、**アプライアンス リソース プロバイダー** サービス プリンシパルにアクセス許可を付与する必要があります。 この記事では、Managed Applications を使用するように Key Vault を構成する方法について説明します。

## <a name="enable-template-deployment"></a>テンプレートのデプロイを有効にする

1. ポータルで Key Vault を選択します。

1. **[アクセス ポリシー]** を選択します。   

   ![[アクセス ポリシー] の選択](./media/key-vault-access/select-access-policies.png)

1. **[クリックして高度なアクセス ポリシーを表示する]** を選択します。

   ![高度なアクセス ポリシーの表示](./media/key-vault-access/advanced.png)

1. **[テンプレートの展開に対して Azure Resource Manager へのアクセスを有効にする]** を選択します。 次に、**[保存]** を選択します。

   ![テンプレートのデプロイを有効にする](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>サービスを共同作成者として追加する

1. **[アクセス制御 (IAM)]** を選択します。

   ![[アクセス制御] の選択](./media/key-vault-access/access-control.png)

1. **[追加]** を選択します。

   ![[追加] の選択](./media/key-vault-access/add-access-control.png)

1. ロールとして **[共同作成者]** を選択します。 **アプライアンス リソース プロバイダー**を検索し、使用可能なオプションから選択します。

   ![プロバイダーの検索](./media/key-vault-access/search-provider.png)

1. **[保存]** を選択します。

## <a name="reference-key-vault-secret"></a>Key Vault シークレットを参照する

マネージド アプリケーションで Key Vault からテンプレートにシークレットを渡すには、[リンクされたテンプレート](../azure-resource-manager/resource-group-linked-templates.md)を使用して、そのテンプレートのパラメータ内の Key Vault を参照する必要があります。 Key Vault のリソース ID とシークレットの名前を指定してください。

```json
"resources": [{
  "apiVersion": "2015-01-01",
  "name": "linkedTemplate",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"
          },
          "secretName": "<secret-name>"
        }
      },
      "adminLogin": { "value": "[parameters('adminLogin')]" },
      "sqlServerName": {"value": "[parameters('sqlServerName')]"}
    }
  }
}],
```

## <a name="next-steps"></a>次の手順

マネージド アプリケーションのデプロイ時にアクセスできるように Key Vault を構成しました。

* Key Vault からテンプレート パラメーターとして値を渡す方法については、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/resource-manager-keyvault-parameter.md)」を参照してください。
* マネージド アプリケーションの例については、[Azure マネージド アプリケーションのサンプル プロジェクト](sample-projects.md)に関する記事を参照してください。
* マネージド アプリケーションの UI 定義ファイルの作成する方法については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。