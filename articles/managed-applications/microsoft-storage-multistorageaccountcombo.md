---
title: Azure の MultiStorageAccountCombo UI 要素 | Microsoft Docs
description: Azure Portal の Microsoft.Storage.MultiStorageAccountCombo UI 要素について説明します。
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: f5fa81d53e1728e8f566a2a39aed8311828b20c7
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37108707"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI 要素
名前が共通のプレフィックスで始まる複数のストレージ アカウントを作成するコントロールのグループです。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>スキーマ
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>解説
- `defaultValue.prefix` の値は、ストレージ アカウント名のシーケンスを生成するために、1 つまたは複数の整数と連結されます。 たとえば、`defaultValue.prefix` が **sa** で `count` が **2** の場合、ストレージ アカウント名として **sa1** と **sa2** が生成されます。 生成されたストレージ アカウント名は、一意であることが自動的に検証されます。
- ストレージ アカウント名は、`count` に基づいて辞書式に生成されます。 たとえば、`count` が 10 の場合、ストレージ アカウント名は 2 桁の整数 (01、02、03) で終了します。
- `defaultValue.prefix` の既定値は **null** です。`defaultValue.type` の既定値は **Premium_LRS** です。
- `constraints.allowedTypes` で指定されていない型はすべて非表示となり、`constraints.excludedTypes` で指定されていない型はすべて表示されます。 `constraints.allowedTypes` と `constraints.excludedTypes` は両方ともオプションとして使用できますが、同時に使用することはできません。
- `count` は、ストレージ アカウント名を生成するだけでなく、適切な乗数を要素に設定するためにも使用されます。 **2** などの静的な値のほか、他の要素からの `[steps('step1').storageAccountCount]` などの動的な値もサポートします。 既定値は **1** です。

## <a name="sample-output"></a>サンプル出力

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="next-steps"></a>次の手順
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
