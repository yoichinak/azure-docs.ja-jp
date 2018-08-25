---
title: Azure Automation の Runbook の種類
description: 'Azure Automation で使用できる Runbook の種類、および使用する種類を決定するときの考慮事項について説明します。 '
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/29/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7958042ccb2f55e9b6021f7d804a0dcd090695c5
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109319"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation の Runbook の種類
Azure Automation がサポートする数種類の Runbook について次の表で簡単に説明します。  その後のセクションでは、使用するときの考慮事項など、各種類に関して詳しく説明します。

| type | 説明 |
|:--- |:--- |
| [グラフィカル](#graphical-runbooks) |Windows PowerShell に基づいており、Azure ポータルのグラフィカル エディターで完全に作成および編集されます。 |
| [グラフィカル PowerShell ワークフロー](#graphical-runbooks) |Windows PowerShell ワークフローに基づいており、Azure ポータルのグラフィカル エディターで完全に作成および編集されます。 |
| [PowerShell](#powershell-runbooks) |Windows PowerShell スクリプトに基づくテキスト Runbook です。 |
| [PowerShell ワークフロー](#powershell-workflow-runbooks) |Windows PowerShell ワークフローに基づくテキスト Runbook です。 |
| [Python](#python-runbooks) |Python に基づくテキスト Runbook です。 |

## <a name="graphical-runbooks"></a>グラフィック Runbook
[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) とグラフィカル PowerShell ワークフロー Runbookは、Azure ポータルのグラフィカル エディターで作成および編集します。  ファイルにエクスポートして別の Automation アカウントにインポートできますが、別のツールで作成または編集することはできません。  グラフィカル Runbook は PowerShell のコードを生成しますが、そのコードを直接表示または変更することはできません。 グラフィカル Runbook をいずれかの [テキスト形式](automation-runbook-types.md)に変換すること、またはテキスト Runbook をグラフィカル形式に変換することはできません。 インポート時には、グラフィカル Runbook からグラフィカル PowerShell ワークフロー Runbook への変換、およびこの逆の変換を行うことができます。

### <a name="advantages"></a>長所
* 視覚的な insert-link-configure 作成モデル  
* プロセスを介したデータの流れに専念  
* 管理プロセスを視覚的に表現  
* 他の Runbook を子 Runbook として含めることで、高度なワークフローが作成可能  
* モジュール プログラミングを促進  


### <a name="limitations"></a>制限事項
* Azure ポータルの外部では Runbook を編集できません。
* 複雑なロジックを実行するために、PowerShell ワークフローのコードを含むコード アクティビティが必要になる場合があります。
* グラフィカル ワークフローによって作成された PowerShell コードを表示したり、直接編集したりすることはできません。 コード アクティビティで作成したコードは表示できます。

## <a name="powershell-runbooks"></a>PowerShell Runbook
PowerShell Runbook は、Windows PowerShell に基づきます。  Azure ポータルのテキスト エディターを使用して、Runbook のコードを直接編集します。  また、任意のオフライン テキスト エディターを使用したり、Azure Automation に [Runbook をインポート](automation-creating-importing-runbook.md) したりすることもできます。

### <a name="advantages"></a>長所
* すべての複雑なロジックを PowerShell コードで実装でき、PowerShell ワークフローに関する複雑さが加わることはありません。 
* 実行前にコンパイルする必要がないため、PowerShell ワークフロー Runbook より開始に時間がかかりません。

### <a name="limitations"></a>制限事項
* 作成者は、PowerShell スクリプトに熟知している必要があります。
* [並列処理](automation-powershell-workflow.md#parallel-processing) を使用して複数のアクションを並列に実行することはできません。
* エラーが発生した場合、 [チェックポイント](automation-powershell-workflow.md#checkpoints) を使用して Runbook を再開することはできません。
* PowerShell ワークフロー Runbook とグラフィカル Runbook を子 Runbook として組み込むには、新しいジョブを作成する Start-AzureAutomationRunbook コマンドレットを使用する必要があります。

### <a name="known-issues"></a>既知の問題
PowerShell Runbook に関する現在の既知の問題は次のとおりです。

* PowerShell Runbook では、null 値を含む暗号化されていない[変数資産](automation-variables.md)は取得できません。
* PowerShell Runbook では、名前に [変数資産](automation-variables.md) が含まれる *~* は取得できません。
* PowerShell Runbook のループ内の Get-Process が、約 80 回反復した後でクラッシュする可能性があります。 
* PowerShell Runbook は、非常に大量のデータを一度に出力ストリームに書き込もうとした場合、失敗する可能性があります。   通常は、大きいオブジェクトを使用する場合、必要な情報だけを出力することによってこの問題を回避できます。  たとえば、*Get-Process* などを出力するのではなく、*Get-Process | Select ProcessName, CPU* で必要なフィールドだけを出力できます。

## <a name="powershell-workflow-runbooks"></a>PowerShell ワークフロー Runbook
PowerShell ワークフロー Runbook は、 [Windows PowerShell ワークフロー](automation-powershell-workflow.md)に基づくテキスト Runbook です。  Azure ポータルのテキスト エディターを使用して、Runbook のコードを直接編集します。  また、任意のオフライン テキスト エディターを使用したり、Azure Automation に [Runbook をインポート](automation-creating-importing-runbook.md) したりすることもできます。

### <a name="advantages"></a>長所
* すべての複雑なロジックを PowerShell ワークフローのコードで実装できます。
* エラーが発生した場合、 [チェックポイント](automation-powershell-workflow.md#checkpoints) を使用して Runbook を再開できます。
* [並列処理](automation-powershell-workflow.md#parallel-processing) を使用して複数のアクションを並列に実行できます。
* 高度なワークフローを作成するために、他のグラフィカル Runbook や PowerShell ワークフロー Runbook を子 Runbook として組み込むことができます。

### <a name="limitations"></a>制限事項
* 作成者は、PowerShell ワークフローについて熟知する必要があります。
* [逆シリアル化されたオブジェクト](automation-powershell-workflow.md#code-changes)など、PowerShell ワークフローに関する別の複雑さに対応する必要があります。
* グラフィカル Runbook は、実行する前にコンパイルする必要があるため、PowerShell Runbook より開始に時間がかかります。
* PowerShell Runbook を子 Runbook として組み込むには、新しいジョブを作成する Start-AzureAutomationRunbook コマンドレットを使用する必要があります。

## <a name="python-runbooks"></a>Python Runbook
Python Runbook は Python 2 でコンパイルします。  Azure Portal でテキスト エディターを使用して Runbook のコードを直接編集したり、任意のオフライン テキスト エディターを使用して Azure Automation に [Runbook をインポート](automation-creating-importing-runbook.md)したりできます。

### <a name="advantages"></a>長所
* Python の堅牢な標準ライブラリを利用します。

### <a name="limitations"></a>制限事項
* 作成者は、Python スクリプトを使い慣れている必要があります。
* 現時点では Python 2 のみがサポートされているため、Python 3 の特定の機能は失敗します。

### <a name="known-issues"></a>既知の問題
Python Runbook に関する現在の既知の問題は次のとおりです。

* サード パーティ製のライブラリを利用するには、Runbook を開始する前にマシンにそのライブラリをインストールした状態で、Runbook を [Windows Hybrid Runbook Worker](https://docs.microsoft.com/azure/automation/automation-windows-hrw-install) または [Linux Hybrid Runbook Worker](https://docs.microsoft.com/azure/automation/automation-linux-hrw-install) で実行する必要があります。

## <a name="considerations"></a>考慮事項
特定の Runbook に使用する種類を決定するときは、さらに次のことを考慮する必要があります。

* グラフィカル Runbook からテキスト Runbook への変換、およびこの逆の変換を行うことはできません。
* 異なる種類の Runbook を子 Runbook として使用する場合は制限があります。  詳細については、「 [Azure Automation での子 Runbook](automation-child-runbooks.md) 」をご覧ください。

## <a name="next-steps"></a>次の手順
* グラフィカル Runbook 作成の詳細については、「 [Azure Automation でのグラフィカル作成](automation-graphical-authoring-intro.md)
* Runbook 用の PowerShell と PowerShell ワークフローとの違いについては、「 [Windows PowerShell ワークフローについて](automation-powershell-workflow.md)
* Runbook を作成またはインポートする方法については、「 [Runbook の作成またはインポート](automation-creating-importing-runbook.md)

