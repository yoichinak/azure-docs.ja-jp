---
title: Azure Functions で関数を無効にする方法
description: Azure Functions 1.x と 2.x で関数を無効および有効にする方法について説明します。
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: tdykstra
ms.openlocfilehash: be30a97c9c94031ea526385326c220fbf0d316b7
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40007556"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Azure Functions で関数を無効にする方法

この記事では、Azure Functions で関数を無効にする方法について説明します。 関数を*無効にする*には、その関数用に定義された自動トリガーをランタイムが無視するようにします。 そのための方法は、ランタイムのバージョンとプログラミング言語によって異なります。

* Functions 1.x
  * スクリプト言語
  * C# クラス ライブラリ
* Functions 2.x
  * すべての言語について方法は 1 つ
  * C# クラス ライブラリについてはオプションの方法あり

## <a name="functions-1x---scripting-languages"></a>Functions 1.x - スクリプト言語

C# スクリプトや JavaScript などのスクリプト言語の場合は、*function.json* ファイルの `disabled` プロパティを使用して関数をトリガーしないようにランタイムに通知できます。 このプロパティは、`true` またはアプリ設定の名前に設定できます。

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
or 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

2 つ目の例では、`true` または 1 に設定されている IS_DISABLED という名前のアプリ設定がある場合に関数が無効になります。

Azure Portal でファイルを編集するか、または関数の **[管理]** タブで **[関数の状態]** スイッチを使用できます。ポータルのスイッチを機能させるには、*function.json* ファイルを変更します。

![[関数の状態] スイッチ](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Functions 1.x - C# クラス ライブラリ

Functions 1.x のクラス ライブラリでは、`Disable` 属性を使用して、関数がトリガーしないようにします。 次の例に示すように、コンストラクターのパラメーターを指定せずに属性を使用できます。

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

コンストラクターのパラメーターを指定せずに属性を使用するには、プロジェクトを再コンパイルおよび再デプロイして関数の無効状態を変更する必要があります。 より柔軟性に優れた方法でこの属性を使用するには、次の例に示すように、ブール値のアプリ設定を参照するコンストラクターのパラメーターを含めます。

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

この方法では、アプリ設定を変更することで、関数を有効および無効にすることができます。再コンパイルや再デプロイは必要ありません。 アプリ設定を変更すると関数アプリが再起動されるため、無効状態の変更がすぐに認識されます。

> [!IMPORTANT]
> `Disabled` 属性は、クラス ライブラリの関数を無効にする唯一の方法です。 クラス ライブラリの関数用に生成される *function.json* ファイルは直接編集することを意図したものではありません。 そのファイルを編集する場合、`disabled` プロパティに対してどのような処理を行っても効果はありません。
>
> **[管理]** タブの **[関数の状態]** スイッチについても同様です。このスイッチを機能させるには、*function.json* ファイルを変更する必要があるためです。
>
> また、無効でない関数が無効であるとポータルに表示される可能性があることに注意してください。



## <a name="functions-2x---all-languages"></a>Functions 2.x - すべての言語

Functions 2.x で関数を無効にするには、アプリ設定を使用します。 たとえば、`QueueTrigger` という関数を無効にするには、`AzureWebJobs.QueueTrigger.Disabled` という名前のアプリ設定を作成して、それを `true` に設定します。 関数を有効にするには、アプリ設定を `false` に設定します。 関数の **[管理]** タブにある **[関数の状態]** スイッチを使用することもできます。このスイッチを機能させるには、`AzureWebJobs.<functionname>.Disabled` アプリ設定を作成および削除します。

![[関数の状態] スイッチ](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Functions 2.x - C# クラス ライブラリ

Functions 2.x のクラス ライブラリでは、すべての言語に対して有効な方法を使用することをお勧めします。 ただし、必要に応じて、[Functions 1.x の場合と同様の Disable 属性を使用する](#functions-1x---c-class-libraries)ことができます。

## <a name="next-steps"></a>次の手順

この記事は、自動トリガーの無効化に関する記事です。 トリガーについて詳しくは、[トリガーとバインド](functions-triggers-bindings.md)に関するページをご覧ください。
