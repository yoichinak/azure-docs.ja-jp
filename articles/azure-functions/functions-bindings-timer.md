---
title: Azure Functions のタイマー トリガー
description: Azure Functions でタイマー トリガーを使用する方法について説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
keywords: Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/08/2018
ms.author: glenga
ms.custom: ''
ms.openlocfilehash: 6712fb0865284ccc2b84e3c2fcd49972f541f69b
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004217"
---
# <a name="timer-trigger-for-azure-functions"></a>Azure Functions のタイマー トリガー 

この記事では、Azure Functions でタイマー トリガーを使用する方法について説明します。 タイマー トリガーを使用すると、スケジュールに基づいて関数を実行できます。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>パッケージ - Functions 1.x

タイマー トリガーは、[Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet パッケージ、バージョン 2.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub リポジトリにあります。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>パッケージ - Functions 2.x

タイマー トリガーは、[Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet パッケージ、バージョン 3.x で提供されます。 パッケージのソース コードは、[azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub リポジトリにあります。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>例

言語固有の例をご覧ください。

* [C#](#trigger---c-example)
* [C# スクリプト (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>C# の例

次の例は、5 分ごとに実行される [C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>C# スクリプトの例

次の例は、*function.json* ファイルのタイマー トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数では、この関数呼び出しがスケジュールのミスの発生によるものかどうかを示すログが書き込まれます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

C# スクリプト コードを次に示します。

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>F# の例

次の例は、*function.json* ファイルのタイマー トリガー バインドと、そのバインドが使われる [F# スクリプト関数](functions-reference-fsharp.md)を示しています。 この関数では、この関数呼び出しがスケジュールのミスの発生によるものかどうかを示すログが書き込まれます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

F# スクリプト コードを次に示します。

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>JavaScript の例

次の例は、*function.json* ファイルのタイマー トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数では、この関数呼び出しがスケジュールのミスの発生によるものかどうかを示すログが書き込まれます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

JavaScript スクリプト コードを次に示します。

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="attributes"></a>属性

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs)を使用します。

属性のコンストラクターは、CRON 式または `TimeSpan` を受け取ります。 `TimeSpan` は、関数アプリが App Service プランで実行している場合にのみ使うことができます。 次の例では CRON 式を示します。

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if (myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
 ```

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `TimerTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 該当なし | "timerTrigger" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。|
|**direction** | 該当なし | "in" に設定する必要があります。 このプロパティは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 該当なし | 関数コード内のタイマー オブジェクトを表す変数の名前。 | 
|**schedule**|**ScheduleExpression**|[CRON 式](#cron-expressions)または [TimeSpan](#timespan) 値。 `TimeSpan` は、App Service プランで実行している関数アプリに対してのみ使うことができます。 スケジュール式をアプリ設定に含めて、たとえば "%ScheduleAppSetting%" のように、**%** 記号で囲まれたアプリ設定名にこのプロパティを設定できます。 |
|**runOnStartup**|**runOnStartup**|`true` の場合、関数はランタイムの開始時に呼び出されます。 たとえば、ランタイムが開始するのは、関数アプリが非アクティブになってアイドル状態に移行した後で起動したとき、 関数が変化したために関数アプリが再起動するとき、関数アプリがスケールアウトするときなどです。したがって、予期しない時にコードが実行される可能性が高くなるので、**runOnStartup** を `true` に設定することはほとんどありません。|
|**useMonitor**|**UseMonitor**|`true` または `false` に設定し、スケジュールを監視する必要があるかどうかを示します。 スケジュールの監視はスケジュールの発生を維持し、関数アプリのインスタンスが再起動するときでもスケジュールが正しく維持されることを保証するのに役立ちます。 このプロパティを明示的に設定しない場合、繰り返し間隔が 1 分より長いスケジュールの既定値は `true` です。 1 分間に 2 回以上トリガーするスケジュールの既定値は `false` です。

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用法

タイマー トリガー関数が呼び出されると、[タイマー オブジェクト](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)が関数に渡されます。 次の JSON は、タイマー オブジェクトの 1 つの表現例です。 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

現在の関数の呼び出しがスケジュールより遅い場合、`IsPastDue` プロパティは `true` になります。 たとえば、関数アプリが再起動すると、呼び出しが行われない可能性があります。

## <a name="cron-expressions"></a>CRON 式 

Azure Functions では、CRON 式を解釈するのに [NCronTab](https://github.com/atifaziz/NCrontab) ライブラリが使用されます。 CRON 式には、次の 6 つのフィールドが含まれます。

`{second} {minute} {hour} {day} {month} {day-of-week}`

各フィールドは、次の種類の値のいずれかを持つことができます。

|type  |例  |トリガーのタイミング  |
|---------|---------|---------|
|特定の値 |<nobr>"0 5 * * * *"</nobr>|hh:05:00。hh は毎時です (1 時間に 1 回)|
|すべての値 (`*`)|<nobr>"0 * 5 * * *"</nobr>|毎日 5:mm:00。mm はその時間の毎分です (1 日に 60 回)|
|範囲 (`-` 演算子)|<nobr>"5-7 * * * * *"</nobr>|hh:mm:05、hh:mm:06、hh:mm:07。hh:mm は毎時の毎分です (1 分間に 3 回)|  
|値のセット (`,` 演算子)|<nobr>"5,8,10 * * * * *"</nobr>|hh:mm:05、hh:mm:08、hh:mm:10。hh:mm は毎時の毎分です (1 分間に 3 回)|
|間隔値 (`/` 演算子)|<nobr>"0 */5 * * * *"</nobr>|hh:05:00、hh:10:00、hh:15:00、... hh:55:00 まで。hh は毎時です (1 時間に 12 回)|

月や曜日を指定するには、数値、名前、または名前の省略形を使用できます。

* 曜日については、数値は 0 から 6 で指定します (0 は日曜日です)。
* 名前は英語で指定します。 例: `Monday`, `January`。
* 名前の大文字と小文字は区別されません。
* 名前は省略形でも指定できます。 省略形は 3 文字にすることをお勧めします。  例: `Mon`, `Jan`。 

### <a name="cron-examples"></a>CRON の例

Azure Functions のタイマー トリガーに使用できる CRON 式の例をいくつか示します。

|例|トリガーのタイミング  |
|---------|---------|
|`"0 */5 * * * *"`|5 分ごとに 1 回|
|`"0 0 * * * *"`|毎正時に 1 回|
|`"0 0 */2 * * *"`|2 時間に 1 回|
|`"0 0 9-17 * * *"`|午前 9 時から午後 5 時ま、1 時間に 1 回|
|`"0 30 9 * * *"`|毎日午前 9 時 30 分|
|`"0 30 9 * * 1-5"`|平日の毎日午前 9 時 30 分|
|`"0 30 9 * Jan Mon"`|1 月の毎週月曜日の午前 9時 30分|
>[!NOTE]   
>CRON 式の例はオンラインで見つかりますが、その多くでは `{second}` フィールドが省略されています。 それらのいずれかをコピーして、欠けている `{second}` フィールドを追加します。 通常、そのフィールドにはアスタリスクではなく 0 を設定します。

### <a name="cron-time-zones"></a>CRON のタイム ゾーン

CRON 式に出現する値は、時間間隔ではなく時刻と日付を示します。 たとえば、`hour` フィールドの 5 は、5 時間ごとではなく、午前 5 時 00 分を示します。

CRON 式で使用する既定のタイム ゾーンは、協定世界時 (UTC) です。 別のタイム ゾーンに基づく CRON 式を使うには、Function App 用に `WEBSITE_TIME_ZONE` という名前のアプリ設定を作成します。 この値を、[Microsoft のタイム ゾーン インデックス](https://technet.microsoft.com/library/cc749073)に関するページに示されている目的のタイム ゾーンの名前に設定します。 

たとえば、"*東部標準時*" は UTC-05:00 です。 タイマー トリガーが毎日東部標準時の 10:00 AM に発生するように設定するには、UTC タイム ゾーンを考慮した次の CRON 式を使用できます。

```json
"schedule": "0 0 15 * * *"
``` 

または、Function App のアプリ設定を `WEBSITE_TIME_ZONE` という名前で作成し、その値を "**東部標準時**" に設定します。  そして、次の CRON 式を使います。 

```json
"schedule": "0 0 10 * * *"
``` 

`WEBSITE_TIME_ZONE` を使用すると、夏時間などの特定のタイムゾーンでの時間変更に対応するように、時刻が調整されます。 

## <a name="timespan"></a>timespan

 `TimeSpan` は、App Service プランで実行している関数アプリに対してのみ使うことができます。

CRON 式とは異なり、`TimeSpan` の値は各関数呼び出しの間の時間間隔を指定します。 指定された間隔より長く実行した後で関数が完了すると、タイマーすぐに関数を再び呼び出します。

`TimeSpan` は文字列として表され、`hh` が 24 未満のときの形式は `hh:mm:ss` です。 最初の 2 つの数字が 24 以上のときの形式は `dd:hh:mm` です。 次に例をいくつか示します。

|例 |トリガーのタイミング  |
|---------|---------|
|"01:00:00" | 1 時間ごと        |
|"00:01:00"|1 分ごと         |
|"24:00:00" | 24 日ごと        |

## <a name="scale-out"></a>スケールアウト

関数アプリが複数のインスタンスにスケールアウトする場合は、タイマーによってトリガーされる関数の 1 つのインスタンスだけがすべてのインスタンスで実行されます。

## <a name="function-apps-sharing-storage"></a>ストレージを共有する関数アプリ

ストレージ アカウントを複数の関数アプリで共有している場合は、各関数アプリの *host.json* の `id` がそれぞれ異なることを確認してください。 `id` プロパティは省略することができます。または、各関数アプリの `id` を手動でそれぞれ異なる値に設定することもできます。 1 つの関数アプリが複数のインスタンスにスケール アウトする場合、タイマー インスタンスが 1 しか存在しないようにするために、タイマー トリガーではストレージ ロックが使用されます。 2 つの関数アプリが同じ `id` を共有していて、それぞれタイマー トリガーを使用している場合は、1 つのタイマーのみが実行されます。

## <a name="retry-behavior"></a>再試行の動作

キュー トリガーとは異なり、タイマー トリガーは関数が失敗した後で再試行しません。 失敗した関数は、次のスケジュール時刻まで再び呼び出されることはありません。

## <a name="troubleshooting"></a>トラブルシューティング

タイマー トリガーが期待どおりに機能しない場合の対処方法については、[タイマー トリガーが設定された関数が発生しない問題の調査と報告](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [タイマー トリガーを使用するクイックスタートに進む](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)
