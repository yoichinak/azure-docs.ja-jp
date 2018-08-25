---
title: 完了したジョブやタスクから結果やログをデータ ストアに保持する - Azure Batch | Microsoft Docs
description: Batch タスクやジョブから出力データを保持するさまざまなオプションについて説明します。 データは、Azure Storage、または別のデータ ストアに保持することができます。
services: batch
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b578abfa6fc0a10edc5daab40f8a0eea5e6653d9
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115064"
---
# <a name="persist-job-and-task-output"></a>ジョブとタスク出力を保持する

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

一般的なタスク出力の例には次のものがあります。

- タスクによって入力データが処理されて作成されるファイル。
- タスクの実行に関連したログ ファイル。 

この記事では、タスクの出力を保持するためのさまざまなオプションと、各オプションに最も適したシナリオについて説明します。   

## <a name="about-the-batch-file-conventions-standard"></a>Batch ファイル規則の標準について

Batch では、Azure Storage 内でのタスク出力ファイルの名前付けについて、任意選択の規則セットが定義されています。 これらの規則については、[Batch ファイル規則の標準に関するページ](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions)を参照してください。 ファイル規則の標準では、特定の出力ファイルについて Azure Storage 内での保存先コンテナーと BLOB パスの名前を決める際に、ジョブとタスクの名前を基にします。

出力データ ファイルの名前付けでファイル規則の標準を採用するかどうかは、開発者が自分で決めることができます。 また、保存先コンテナーや BLOB の名前を、自分で自由に付けることもできます。 出力ファイルの名前付けにファイル規則の標準を使用した場合は、[Azure Portal][portal] で出力ファイルを表示できます。

ファイル規則の標準は、いくつかの方法で使用することができます。

- 出力ファイルを保持するために Batch サービス API を使用している場合は、ファイル規則の標準に従って保存先コンテナーと BLOB の名前を付けることを選択できます。 Batch サービス API を利用すると、タスク アプリケーションを変更せずに、クライアント コードから出力ファイルを保持することができます。
- .NET で開発している場合は、[.NET 用の Azure Batch ファイル規則ライブラリ][nuget_package]を使用することができます。 このライブラリを使用することの利点は、ID や目的に応じて出力ファイルのクエリを実行する機能がサポートされている点です。 組み込みのクエリ機能によって、クライアント アプリケーションまたは他のタスクから出力ファイルに簡単にアクセスできます。 ただし、ファイル規則ライブラリを呼び出すように、タスクのアプリケーションを変更する必要があります。 詳細については、[.NET 用のファイル規則ライブラリ](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.aspx)に関するリファレンスを参照してください。
- .NET 以外の言語で開発している場合は、自分のアプリケーション内でファイル規則の標準を実装することができます。

## <a name="design-considerations-for-persisting-output"></a>出力の保持を設計する際の考慮事項 

Batch ソリューションを設計する際は、ジョブとタスクの出力に関連する次の事項を考慮してください。

* **コンピューティング ノードの有効期間**: コンピューティング ノードは多くの場合、一時的なものです (特に自動スケールが有効なプールの場合)。 ノード上で実行されているタスクの出力は、そのノードが存在している間のみ、かつ開発者がタスクに設定したファイルのリテンション期間内にのみ使用可能です。 タスクの生成する出力がタスクの完了後に必要になる可能性がある場合、タスクは、Azure Storage などの持続性のあるストアに出力ファイルをアップロードする必要があります。

* **出力の格納**: タスク出力のデータ ストアとして Azure Storage をお勧めしますが、持続性のある記憶域であれば他のものでも使用できます。 Azure Storage へのタスクの出力の書き込みは、Batch サービス API に統合されています。 別の形式の持続ストレージを使用する場合は、タスク出力を保持するためのアプリケーション ロジックを開発者が自分で作成する必要があります。   

* **出力の取得**: タスク出力は、プール内のコンピューティング ノードから直接取得するか、タスク出力を保持した場合は Azure Storage や別のデータ ストアから取得することができます。 タスクの出力をコンピューティング ノードから直接取得するには、ファイル名とノード上の出力場所が必要です。 タスク出力を Azure Storage に保持する場合は、Azure Storage SDK を使用して出力ファイルをダウンロードするために、Azure Storage 内のファイルへの完全なパスが必要になります。

* **出力の表示**: Azure Portal で Batch のタスクに移動し、**[ノード上のファイル]** を選択すると、目的の出力ファイルだけでなく、タスクに関連するすべてのファイルが表示されます。 繰り返しになりますが、コンピューティング ノード上のファイルは、そのノードが存在し、タスクに設定したファイルのリテンション期間内である場合のみ使用可能です。 Azure Storage に保持したタスク出力を表示するには、Azure Portal を使用するか、[Azure Storage エクスプローラー][storage_explorer]などの Azure Storage クライアント アプリケーションを使用することができます。 Azure Storage 内の出力データを Portal または別のツールで表示するには、ファイルの場所を知っていて、その場所に直接移動できる必要があります。

## <a name="options-for-persisting-output"></a>出力を保持する際のオプション

タスクの出力を保持するには、シナリオに応じて、いくつかの異なるアプローチがあります。

- Batch サービス API を使用する。  
- .NET 用の Batch ファイル規則のライブラリを使用する。  
- アプリケーション内に Batch ファイル規則の標準を実装する。
- カスタムのファイル移動ソリューションを実装する。

以下のセクションでは、それぞれのアプローチについて詳しく説明します。

### <a name="use-the-batch-service-api"></a>Batch サービス API を使用する

2017-05-01 バージョンから、Batch サービスには、[タスクをジョブに追加する](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job)時点、または[タスクのコレクションをジョブに追加する](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job)時点で、タスク データ用に Azure Storage 内に出力ファイルを指定するためのサポートが追加されました。

Batch サービス API では、仮想マシンの構成で作成されたプールから Azure Storage アカウントにタスク データを保持する機能がサポートされています。 Batch サービス API を使用すると、タスクを実行するアプリケーションを変更することなしに、タスクのデータを保持できます。 必要に応じて、Azure Storage に保持されるファイルに、[Batch ファイル規則の標準](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions)に準拠して名前を付けることもできます。 

次のような場合に、Batch サービス API を使用してタスク出力を保持します。

- 仮想マシンの構成で作成されたプール内の Batch タスクおよびジョブ マネージャー タスクからデータを保持する。
- 任意の名前の Azure Storage コンテナーにデータを保持する。
- [Batch ファイル規則の標準](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions)に準拠した名前の Azure Storage コンテナーにデータを保持する。 

> [!NOTE]
> Batch サービス API は、クラウド サービスの構成で作成されたプール内で実行されているタスクからのデータの保持はサポートしていません。 クラウド サービス構成が実行されているプールからの出力を保持するタスクについては、「[Persist job and task data to Azure Storage with the Batch File Conventions library for .NET to persist](batch-task-output-file-conventions.md) (保持のための .NET 用 Batch ファイル規則ライブラリを使用してジョブやタスクのデータを Azure Storage に保持する)」を参照してください。
> 
> 

Batch サービス API を使用してタスク出力を保持することの詳細については、「[Persist task data to Azure Storage with the Batch service API](batch-task-output-files.md) (Batch サービス API を使用してタスクのデータを Azure Storage に保持する)」を参照してください。 .NET 用の Batch クライアント ライブラリを使用して持続性のあるストレージにタスク出力を保持する方法については、GitHub にある [PersistOutputs][github_persistoutputs] サンプル プロジェクトを参照してください。

### <a name="use-the-batch-file-conventions-library-for-net"></a>.NET 用の Batch ファイル規則ライブラリの使用

C# と .NET で Batch ソリューションを作成している開発者は、[.NET 用のファイル規則ライブラリ][nuget_package]を使用し、[Batch ファイル規則の標準](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions)に従って、タスクのデータを Azure Storage アカウントに保持することができます。 ファイル規則ライブラリでは、出力ファイルの Azure Storage への移動や、移動先のコンテナーと BLOB の名前付けが、よく知られた方法で処理されます。

ファイル規則ライブラリでは、ID または目的のいずれかによる出力ファイルのクエリがサポートされるため、ファイルの完全な URI を指定する必要なしに、容易にファイルを特定することができます。 

.NET 用の Batch ファイル規則ライブラリを使ってタスクの出力を保持する方法は、次のような場合に使用します。

- タスクの実行中にデータを Azure Storage にストリーミングする必要がある。
- クラウド サービス構成または仮想マシン構成のどちらで作成されたプールからもデータを保持する必要がある。
- クライアント アプリケーションや、ジョブ内の他のタスクで、ID または目的によってタスク出力ファイルを特定してダウンロードする必要がある。 
- チェックポイント処理または初期結果の早期アップロードを実行する必要がある。
- タスクの出力を Azure Portal で表示する必要がある。

.NET 用のファイル規則ライブラリを使用してタスク出力を保持することの詳細については、「[Persist job and task data to Azure Storage with the Batch File Conventions library for .NET to persist](batch-task-output-file-conventions.md) (保持のための .NET 用 Batch ファイル規則ライブラリを使用してジョブやタスクのデータを Azure Storage に保持する)」を参照してください。 .NET 用のファイル規則ライブラリを使用して持続性のあるストレージにタスク出力を保持する方法については、GitHub にある [PersistOutputs][github_persistoutputs] サンプル プロジェクトを参照してください。

GitHub にある [PersistOutputs][github_persistoutputs] サンプル プロジェクトでは、.NET 用の Batch クライアント ライブラリを使用して持続性のあるストレージにタスク出力を保持する方法を紹介しています。

### <a name="implement-the-batch-file-conventions-standard"></a>Batch ファイル規則の標準を実装する

.NET 以外の言語を使用している場合は、開発者が自分のアプリケーション内で [Batch ファイル規則の標準](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions)を実装することができます。 

実証済みの名前付けスキームを使用する必要がある場合、または Azure Portal でタスク出力を表示する必要がある場合は、ファイル規則の名前付け標準を自分で実装することもできます。

### <a name="implement-a-custom-file-movement-solution"></a>カスタムのファイル移動ソリューションを実装する

ファイルの移動ソリューション全体を、開発者が自分で実装することもできます。 このアプローチは、次のような場合に使用します。

- Azure Storage 以外のデータ ストアにタスクのデータを保持する必要がある。 Azure SQL や Azure DataLake のようなデータ ストアにファイルをアップロードするには、その場所にアップロードするためのカスタム スクリプトまたは実行可能ファイルを作成します。 作成したファイルは、主要な実行可能ファイルを実行した後に、コマンド ラインで呼び出すことができます。 たとえば、Windows ノードで、これら 2 つのコマンドを次のように呼び出すことができます: `doMyWork.exe && uploadMyFilesToSql.exe`
- チェックポイント処理または初期結果の早期アップロードを実行する必要がある。
- エラー処理を詳細に制御する必要がある。 たとえば、特定のタスク終了コードに基づいて特定のアップロード アクションを実行するなど、タスクと依存関係のあるアクションが必要な場合は、独自のソリューションを実装することができます。 タスクと依存関係のあるアクションの詳細については、「[Create task dependencies to run tasks that depend on other tasks](batch-task-dependencies.md) (タスクの依存関係を作成して他のタスクに依存するタスクを実行する)」を参照してください。 

## <a name="next-steps"></a>次の手順

- Batch サービス API の新機能を使用してタスクのデータを保持する方法について、「[Batch サービス API を使用してタスクのデータを Azure Storage に保持する](batch-task-output-files.md)」を読む。
- .NET 用の Batch ファイル規則ライブラリを使用してタスク出力を保持することについて、「[.NET 用の Batch ファイル規則ライブラリを使用した Azure Storage へのジョブおよびタスクのデータの保持](batch-task-output-file-conventions.md)」を読む。
- .NET 用の Batch クライアント ライブラリと .NET 用のファイル規則ライブラリの両方を使用して持続性のあるストレージにタスク出力を保持する方法について、GitHub にある [PersistOutputs][github_persistoutputs] サンプル プロジェクトを参照する。

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
