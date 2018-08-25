---
title: Azure Functions でサポートされている言語
description: どの言語がサポートされているか (GA) と、どの言語が試験段階またはプレビューの段階であるかを説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 2de2ebdea41ca35e853b37ab804e516eb7f4df9f
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494450"
---
# <a name="supported-languages-in-azure-functions"></a>Azure Functions でサポートされている言語

この記事では、Azure Functions で使用できる言語のサポートのレベルについて説明します。

## <a name="levels-of-support"></a>サポートのレベル

サポートのレベルは、次の 3 つです。

* **一般公開 (GA)** - 完全にサポートされ、運用環境用に承認されています。
* **プレビュー** - まだサポートされていませんが、今後 GA 状態に達すると想定されています。
* **試験段階** - サポートされておらず、今後廃止される可能性があります。最終的にプレビューまたは GA 状態になる保証はありません。

## <a name="languages-in-runtime-1x-and-2x"></a>ランタイム 1.x および 2.x の言語

[2 つのバージョンの Azure Functions ランタイム](functions-versions.md)を利用できます。 1.x ランタイムは GA です。 これは、運用アプリケーション用に承認されている唯一のランタイムです。 2.x ランタイムは現在プレビューであるため、サポートされている言語もプレビューです。 次の表は、各ランタイム バージョンでどの言語がサポートされているかを示しています。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>試験段階の言語

バージョン 1.x の試験段階の言語は、スケーリングが適切に行われず、一部のバインドがサポートされていません。 たとえば、Python の場合、Functions ランタイムが関数呼び出しごとに *python.exe* を実行するため、低速になります。 また、Python では HTTP バインドがサポートされていますが、要求オブジェクトにアクセスできません。

PowerShell の試験段階のサポートは、バージョン 5.1 に制限されています。これは、関数アプリが実行される VM に既定でインストールされるのがこのバージョンであるためです。 PowerShell スクリプトを実行する場合は、[Azure Automation](https://azure.microsoft.com/services/automation/) を検討してください。

1.x だけで使用できるいずれかの言語を使用する場合は、1.x ランタイムを使用し続けてください。 ただし、信頼性が大切である場合には、試験段階の言語は使用しないでください。それらの言語には公式なサポートがありません。 [GitHub で問題を作成する](https://github.com/Azure/azure-webjobs-sdk-script/issues)ことによって支援を依頼することができますが、試験段階の言語に関する問題についてはサポート ケースを開かないでください。 

バージョン 2.x ランタイムでは、試験段階の言語はサポートされていません。 新しい言語のサポートは、運用環境でその言語をサポートできる場合にのみ追加されます。 

### <a name="language-extensibility"></a>言語拡張

2.x ランタイムは、[言語拡張](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)を提供するように設計されています。 この拡張モデルに基づく最初の言語として、Java などがあります。Java は、2.x ではプレビューです。

## <a name="next-steps"></a>次の手順

Azure Functions で GA またはプレビューであるいずれかの言語を使用する方法の詳細については、以下のリソースを参照してください。

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)
