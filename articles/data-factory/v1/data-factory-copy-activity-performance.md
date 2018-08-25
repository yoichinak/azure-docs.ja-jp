---
title: コピー アクティビティのパフォーマンスとチューニングに関するガイド | Microsoft Docs
description: コピー アクティビティを使用する場合に、Azure Data Factory でのデータ移動のパフォーマンスに影響する主な要因について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 505f7345af6224b767d6d3719c123d91f54e48f5
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054294"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>コピー アクティビティのパフォーマンスとチューニングに関するガイド

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-copy-activity-performance.md)
> * [Version 2 (現在のバージョン)](../copy-activity-performance.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[Data Factory のコピー アクティビティのパフォーマンスとチューニングに関するガイド](../copy-activity-performance.md)に関するページを参照してください。

Azure Data Factory コピー アクティビティは、優れたセキュリティで保護された、信頼性とパフォーマンスに優れたデータ読み込みソリューションを提供します。 これにより、数十テラバイトのデータを、さまざまなクラウドおよびオンプレミスのデータ ストアの間で毎日コピーすることができます。 データ読み込みのパフォーマンスを劇的に高めることが、高度な分析ソリューションを構築してすべてのデータから深い洞察を得るという、"ビッグ データ" の中心的問題に集中するための鍵となります。

Azure によりエンタープライズ クラスのデータ ストレージおよびデータ ウェアハウスのソリューション セットが提供されます。また、コピー アクティビティにより、構成とセットアップが簡単な、大幅に最適化されたデータ読み込み環境がもたらされます。 1 つのコピー アクティビティで次のことを実現できます。

* **Azure SQL Data Warehouse** へのデータの読み込み (**1.2 GBps**)。 ユース ケースを使用したチュートリアルについては、[1 TB のデータを Azure Data Factory を使用して 15 分以内に Azure SQL Data Warehouse に読み込む方法](data-factory-load-sql-data-warehouse.md)に関するページを参照してください。
* **Azure Blob Storage** へのデータの読み込み (**1.0 GBps**)
* **Azure Data Lake Store** へのデータの読み込み (**1.0 GBps**)

この記事では、次の内容について説明します。

* [パフォーマンス参照番号](#performance-reference) 
* [クラウド データ移動単位](#cloud-data-movement-units)、[並列コピー](#parallel-copy)、[ステージング コピー](#staged-copy)などのさまざまなシナリオにおけるコピーのスループットを高める機能
* [パフォーマンス チューニング ガイダンス](#performance-tuning-steps) 

> [!NOTE]
> コピー アクティビティ全般に慣れていない場合は、この記事を読む前に「 [コピー アクティビティを使用したデータの移動](data-factory-data-movement-activities.md) 」を参照してください。
>

## <a name="performance-reference"></a>パフォーマンス リファレンス

参考として、社内テストに基づく特定のソースとシンクのペアにおけるコピーのスループット (Mbps 単位) を次の表に示します。 比較のために、[クラウド データの移動単位](#cloud-data-movement-units)または [Data Management Gateway のスケーラビリティ](data-factory-data-management-gateway-high-availability-scalability.md) (複数のゲートウェイ ノード) の異なる設定によってコピーのパフォーマンスがどのように変化するかも示しています。

![パフォーマンス マトリックス](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>Azure Data Factory バージョン 1 では、クラウド間のコピーの最小のクラウドデータ移動単位は 2 です。 指定しない場合、[クラウド データ移動単位](#cloud-data-movement-units)で使用されている既定のデータ移動単位を参照してください。

**注意する点:**
* スループットの計算には、[ソースから読み取られたデータ サイズ]/[コピー アクティビティの実行時間] という数式を使用します。
* 表のパフォーマンスの参照番号は、1 回のコピー アクティビティ実行で [TPC-H](http://www.tpc.org/tpch/) データ セットを使用して測定されました。
* Azure データ ストアでは、ソースとシンクは同じ Azure リージョンにあります。
* オンプレミスとクラウドのデータ ストア間でのハイブリッド コピーでは、各ゲートウェイ ノードは、オンプレミス データ ストアから切り離された次の仕様のコンピューターで実行されました。 ゲートウェイで 1 つのアクティビティが実行されていた場合、コピー操作で使用されたのは、テスト コンピューターの CPU、メモリ、またはネットワーク帯域幅のごく一部だけでした。 詳細については、「[Data Management Gateway に関する考慮事項](#considerations-for-data-management-gateway)」を参照してください。
    <table>
    <tr>
        <td>CPU</td>
        <td>32 コア 2.20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>メモリ</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>ネットワーク</td>
        <td>インターネット インターフェイス: 10 Gbps。イントラネット インターフェイス: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> 既定の最大データ移動単位 (DMU) は、クラウド間のコピー アクティビティの実行では 32 ですが、これよりも大きい DMU を利用すると、より高いスループットを得ることができます。 たとえば、100 DMU にすると、Azure BLOB から Azure Data Lake Store に **1.0 Gbps** でデータをコピーすることができます。 この機能の詳細とサポートされるシナリオについては、「[クラウド データ移動単位](#cloud-data-movement-units)」セクションを参照してください。 DMU の追加依頼は、[Azure サポート](https://azure.microsoft.com/support/)に連絡してください。

## <a name="parallel-copy"></a>並列コピー
ソースからのデータ読み取りまたはターゲットへのデータ書き込みは、 **コピー アクティビティの実行中に並行して**実行できます。 この機能によって、コピー操作のスループットが向上し、データの移動にかかる時間が短縮されます。

この設定は、アクティビティ定義の **concurrency** プロパティとは異なります。 **concurrency** プロパティでは、さまざまなアクティビティ ウィンドウ (午前 1 時から 2 時、午前 2 時から 3 時、午前 3 時から 4 時など) のデータを処理するために、**コピー アクティビティの同時実行**の数を決定します。 この機能は、履歴を読み込む場合に便利です。 並列コピー機能は、 **1 つのアクティビティの実行**に適用されます。

サンプル シナリオを見てみましょう。 次の例では、過去の複数のスライスを処理する必要があります。 Data Factory は、スライスごとにコピー アクティビティ (アクティビティ実行) のインスタンスを実行します。

* 1 番目のアクティビティ ウィンドウのデータ スライス (午前 1 時から午前 2 時) ==> アクティビティの実行 1
* 2 番目のアクティビティ ウィンドウのデータ スライス (午前 2 時から午前 3 時) ==> アクティビティの実行 2
* 3 番目のアクティビティ ウィンドウのデータ スライス (午前 3 時から午前 4 時) ==> アクティビティの実行 3

同様に続きます。

この例では、**concurrency** 値が 2 に設定されると、**アクティビティの実行 1** と**アクティビティの実行 2** が 2 つのアクティビティ ウィンドウからデータを**同時に**コピーできるため、データ移動のパフォーマンスが向上します。 ただし、複数のファイルがアクティビティの実行 1 に関連付けられている場合、データ移動サービスは、ソースからターゲットへのファイルのコピーを 1 度に 1 ファイルずつ行います。

### <a name="cloud-data-movement-units"></a>クラウド データ移動単位
**クラウド データ移動単位 (DMU)** は、Data Factory の 1 つの単位の能力 (CPU、メモリ、ネットワーク リソース割り当ての組み合わせ) を表す尺度です。 DMU はクラウド間のコピー操作では適用できますが、ハイブリッド コピーでは適用されません。

**コピー アクティビティの実行を強化する最小クラウドデータ移動単位は 2 です。** 指定しない場合、次の表に、さまざまなコピー シナリオで使用される既定の DMU を示します。

| コピー シナリオ | サービスによって決定される既定の DMU |
|:--- |:--- |
| ファイル ベースのストア間でのデータのコピー | ファイルの数とサイズに応じて 4 〜 16。 |
| 他のすべてのコピー シナリオ | 4 |

この既定の動作をオーバーライドするには、 **cloudDataMovementUnits** プロパティに次のように値を指定します。 **cloudDataMovementUnits** プロパティに**使用できる値**は、2、4、8、16、32 です。 コピー操作が実行時に使用する **クラウド DMU の実際の数** は、データ パターンに応じて、構成されている値以下になります。 特定のコピー ソースおよびシンクに、より多くの単位を構成した場合に得られるパフォーマンス向上レベルの情報については、「 [パフォーマンス リファレンス](#performance-reference)」を参照してください。

```json
"activities":[  
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

> [!NOTE]
> スループットをより高めるためにさらにクラウド DMU が必要な場合は、 [Azure サポート](https://azure.microsoft.com/support/)にお問い合わせください。 現在、8 以上を設定できるのは、**複数のファイルを、Blob Storage/Data Lake Store/Amazon S3/クラウド FTP/クラウド SFTP から Blob Storage/Data Lake Store/Azure SQL Database にコピーする**場合のみです。
>

### <a name="parallelcopies"></a>parallelCopies
**parallelCopies** プロパティを使用して、コピー アクティビティで使用する並列処理を指定することができます。 このプロパティは、並行してソースからの読み取りまたはシンク データ ストアへの書き込みを行うことができる、コピー アクティビティ内のスレッドの最大数と考えることができます。

コピー アクティビティの実行ごとに、Data Factory は、ソース データ ストアからターゲット データ ストアへのデータ コピーで使用する並列コピーの数を決定します。 使用される並列コピーの既定数は、使用しているソースとシンクの種類によって異なります。  

| ソースとシンク | サービスによって決定される並列コピーの既定数 |
| --- | --- |
| ファイル ベースのストア (Blob Storage、Data Lake Store、Amazon S3、オンプレミスのファイル システム、オンプレミスの HDFS) 間のデータのコピー |1 ～ 32 の範囲。 どの数にするかは、ファイルのサイズと、2 つのクラウド データ ストア間でのデータのコピーで使用されるクラウド データ移動単位の数 (DMU) またはハイブリッド コピー (オンプレミス データ ストアとの間のデータのコピー) で使用されるゲートウェイ コンピューターの物理構成によって決まります。 |
| **任意のソース データ ストアから Azure Table Storage への** |4 |
| その他のすべてのソースとシンクのペア |1 |

通常は、既定の動作で最適なスループットが得られます。 ただし、データ ストアをホストしているコンピューターの負荷を制御したり、コピーのパフォーマンスをチューニングしたりするには、 **parallelCopies** プロパティの値を指定して、既定値をオーバーライドできます。 1 ～ 32 の値 (両端の値を含む) を指定する必要があります。 実行時にコピー アクティビティは、設定された値以下でパフォーマンスが最大になる値を使用します。

```json
"activities":[  
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
注意する点:

* ファイル ベースのストア間でデータをコピーする場合は、**parallelCopies** によってファイル レベルでの並列処理が決まります。 単一ファイル内でのチャンク化は裏で自動的かつ透過的に行われます。また、指定されたソース データ ストアの種類に最適なチャンク サイズを使用し、parallelCopies とは独立に並行してデータを読み込むよう設計されています。 実行時にコピー操作でデータ移動サービスに使用される並列コピーの実際の数は、存在するファイルの数以下となります。 コピー動作が **mergeFile** の場合、コピー アクティビティはファイル レベルでの並列処理を活用できません。
* **parallelCopies** プロパティの値を指定すると、ソースおよびシンク データ ストアで負荷 (ハイブリッド コピーの場合はゲートウェイへの負荷) が増えることを考慮してください。 これは、特に複数のアクティビティがある場合や、同じデータ ストアに対して実行される同じアクティビティの同時実行がある場合によくあります。 データ ストアまたはゲートウェイの負荷の上限に達したことがわかった場合は、 **parallelCopies** の値を減らし、負荷を軽減してください。
* ファイル ベース以外のストアからファイル ベースのストアにデータをコピーする場合、データ移動サービスは **parallelCopies** プロパティを無視します。 並列処理が指定されても、この場合は適用されません。

> [!NOTE]
> ハイブリッド コピーの実行時に **parallelCopies** 機能を使用するには、Data Management Gateway のバージョン 1.11 以降を使用する必要があります。
>
>

これらの 2 つのプロパティをうまく利用し、データ移動のスループットを向上させるには、 [ユース ケースのサンプル](#case-study-use-parallel-copy)を参照してください。 既定の動作を活用する場合は、 **parallelCopies** を構成する必要はありません。 構成しても **parallelCopies** が小さすぎる場合は、複数のクラウド DMU が十分に活用されない可能性があります。  

### <a name="billing-impact"></a>課金への影響
**重要** なのは、コピー操作の合計時間に基づいて料金が請求されることです。 これまで 1 回のコピー ジョブに 1 クラウド単位で 1 時間かかっていたのが、4 クラウド単位で 15 分かかるようになった場合、全体的な請求金額はほぼ同じままです。 たとえば、4 クラウド単位を使用するとします。 1 回のコピー アクティビティの実行で、1 つ目のクラウド単位に 10 分、2 つ目に 10 分、3 つ目に 5 分、4 つ目に 5 分かかります。 コピー (データ移動) の合計時間は 10 + 10 + 5 + 5 で 30 分になり、その分の料金が発生します。 **parallelCopies** を使用しても、課金には影響しません。

## <a name="staged-copy"></a>ステージング コピー
ソース データ ストアからシンク データ ストアにデータをコピーする場合、中間のステージング ストアとして Blob Storage を使用できます。 ステージングは、特に次のような場合に役立ちます。

1. **PolyBase を使ってさまざまなデータ ストアから SQL Data Warehouse にデータを取り込む**。 SQL Data Warehouse は、大量のデータを読み込むための高スループットのメカニズムとして PolyBase を使用します。 ただし、ソース データが Blob Storage にあることと追加の条件を満たすことが必要です。 Blob Storage 以外のデータ ストアからデータを読み込む際は、中間ステージング Blob Storage 経由のデータ コピーを有効にすることができます。 その場合、Data Factory は、PolyBase の要件を満たすために必要なデータ変換を実行します。 その後、PolyBase を使用してデータを SQL Data Warehouse に読み込みます。 詳細については、「 [PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)」を参照してください。 ユース ケースを使用したチュートリアルについては、[1 TB のデータを Azure Data Factory を使用して 15 分以内に Azure SQL Data Warehouse に読み込む方法](data-factory-load-sql-data-warehouse.md)に関するページを参照してください。
2. **ネットワーク接続が遅い場合、ハイブリッド データ移動 (オンプレミス データ ストアとクラウド データ ストアの間でのコピー) の実行に少し時間がかかる場合がある**。 パフォーマンスを向上させるために、オンプレミスのデータを圧縮することで、クラウド内のステージング データ ストアにデータを移動する時間を短縮できます。 その後、データは、ターゲット データ ストアに読み込む前に、ステージング ストアで圧縮を解除できます。
3. **企業の IT ポリシーが理由で、ファイアウォールでポート 80 とポート 443 以外のポートを開きたくない**。 たとえば、オンプレミス データ ストアから Azure SQL Database シンクまたは SQL Data Warehouse シンクにデータをコピーする場合、Windows ファイアウォールと会社のファイアウォールの両方で、ポート 1433 の送信 TCP 通信を有効にする必要があります。 このシナリオでは、ゲートウェイを利用して、まず、データをポート 443 の HTTP または HTTPS 経由で Blob Storage のステージング インスタンスにコピーします。 次に、Blob Storage のステージングから SQL Database または SQL Data Warehouse にデータを読み込みます。 このフローでは、ポート 1433 を有効にする必要はありません。

### <a name="how-staged-copy-works"></a>ステージング コピーのしくみ
ステージング機能を有効にすると、最初にデータがソース データ ストアからステージング データ ストア (独自に用意) にコピーされます。 次に、データは、ステージング データ ストアからシンク データ ストアにコピーされます。 Data Factory では、2 段階のフローが自動的に管理されます。 また、データ移動の完了後、ステージング ストレージから一時データをクリーンアップします。

(ソース データ ストアとシンク データ ストアの両方がクラウドにある) クラウド コピーのシナリオでは、ゲートウェイは使用されません。 コピー操作は、Data Factory サービスによって実行されます。

![Staged copy: Cloud scenario](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

(ソースがオンプレミスにあり、シンクがクラウドにある) ハイブリッド コピーのシナリオでは、ゲートウェイが、ソース データ ストアのデータをステージング データ ストアに移動します。 Data Factory サービスは、データをステージング データ ストアからシンク データ ストアにコピーします。 ステージングによるクラウド データ ストアからオンプレミス データ ストアへのデータのコピーも逆のフローでサポートされています。

![Staged copy: Hybrid scenario](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

ステージング ストアを使用したデータ移動を有効にすると、ソース データ ストアから中間またはステージング データ ストアにデータを移動する前にデータを圧縮し、中間またはステージング データ ストアからシンク データ ストアにデータを移動する前にそのデータの圧縮を解除するかどうかを指定できます。

現時点では、ステージング ストアを使用して 2 つのオンプレミス データ ストア間でデータをコピーすることはできません。 このオプションは、間もなく利用できるようになる予定です。

### <a name="configuration"></a>構成
コピー アクティビティの **enableStaging** 設定を構成して、目的のデータ ストアに読み込む前にデータを Blob Storage にステージングするかどうかを指定します。 **enableStaging** を TRUE に設定した場合は、次の表に記載されている追加のプロパティを指定する必要があります。 ステージング用に Azure Storage または Storage Shared Access Signature のリンクされたサービスがない場合は、作成する必要もあります。

| プロパティ | 説明 | 既定値 | 必須 |
| --- | --- | --- | --- |
| **enableStaging** |中間ステージング ストアを経由してデータをコピーするかどうかを指定します。 |False |いいえ |
| **linkedServiceName** |[AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) または [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) のリンクされたサービスの名前を指定します。これは、中間ステージング ストアとして使用する Storage のインスタンスです。 <br/><br/> PolyBase を使用してデータを SQL Data Warehouse に読み込むために、Shared Access Signature を持つ Storage を使用することはできません。 それ以外のすべてのシナリオでは使用できます。 |該当なし |はい ( **enableStaging** が TRUE に設定されている場合) |
| **path** |ステージング データを格納する Blob Storage のパスを指定します。 パスを指定しないと、一時データを格納するコンテナーがサービスによって作成されます。 <br/><br/> パスを指定するのは、Shared Access Signature を持つ Storage を使用する場合、または一時データを特定の場所に保存する必要がある場合のみです。 |該当なし |いいえ |
| **enableCompression** |データをコピーする前に圧縮するかどうかを指定します。 この設定により、転送するデータの量が減ります。 |False |いいえ |

上の表に記載されているプロパティを持つコピー アクティビティの定義の例を次に示します。

```json
"activities":[  
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>課金への影響
コピーの期間とコピーの種類という 2 つのステップに基づいて課金されます。

* クラウド コピー (クラウド データ ストアから別のクラウド データ ストアへのデータのコピー) でステージングを使用する場合、料金は、"ステップ 1 とステップ 2 のコピー時間の合計" x "クラウド コピーの単価" で計算されます。
* ハイブリッド コピー (オンプレミス データ ストアからクラウド データ ストアへのデータのコピー) でステージングを使用する場合、料金は、"ハイブリッド コピーの時間" x "ハイブリッド コピーの単価" + "クラウド コピーの時間" x "クラウド コピーの単価" で計算されます。

## <a name="performance-tuning-steps"></a>パフォーマンス チューニングの手順
Data Factory サービスとコピー アクティビティのパフォーマンスをチューニングするには、次の手順を実行することをお勧めします。

1. **ベースラインを確立する**。 開発フェーズでは、代表的なデータ サンプルに対してコピー アクティビティを使用して、パイプラインをテストします。 Data Factory の [スライシング モデル](data-factory-scheduling-and-execution.md) を使用することで、操作するデータの量を制限できます。

   **監視と管理アプリ**を使用して、実行時間とパフォーマンス特性を収集します。 Data Factory のホーム ページで、**[監視と管理]** を選択します。 ツリー ビューで、 **出力データセット**を選択します。 **[Activity Windows (アクティビティ ウィンドウ)]** の一覧で、コピー アクティビティの実行を選択します。 **[Activity Windows (アクティビティ ウィンドウ)]** には、コピー アクティビティの期間とコピーされるデータのサイズが表示されます。 スループットは、 **[Activity Window Explorer (アクティビティ ウィンドウ エクスプローラー)]** に一覧表示されます。 このアプリの詳細については、 [新しい監視と管理アプリを使用した Azure Data Factory パイプラインの監視と管理](data-factory-monitor-manage-app.md)に関する記事を参照してください。

   ![アクティビティ実行の詳細](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   この記事の後半で、自分のシナリオのパフォーマンスと構成を、当社のテストに基づくコピー アクティビティの [パフォーマンス リファレンス](#performance-reference) と比較できます。
2. **パフォーマンスを診断して最適化する**。 観測したパフォーマンスが予測どおりでない場合は、パフォーマンスのボトルネックを特定する必要があります。 次に、パフォーマンスを最適化して、ボトルネックの影響を除去するか軽減します。 この記事では、パフォーマンスの診断に関する詳細な説明は省略しますが、いくつかの一般的な考慮事項を次に示します。

   * パフォーマンス機能:
     * [並列コピー](#parallel-copy)
     * [クラウド データ移動単位](#cloud-data-movement-units)
     * [ステージング コピー](#staged-copy)
     * [Data Management Gateway のスケーラビリティ](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Data Management Gateway](#considerations-for-data-management-gateway)
   * [ソース](#considerations-for-the-source)
   * [シンク](#considerations-for-the-sink)
   * [シリアル化と逆シリアル化](#considerations-for-serialization-and-deserialization)
   * [圧縮](#considerations-for-compression)
   * [列マッピング](#considerations-for-column-mapping)
   * [その他の考慮事項](#other-considerations)
3. **構成をデータ セット全体に拡張する**。 実行結果とパフォーマンスに問題がなければ、データ セット全体を網羅するように定義とパイプラインのアクティブな期間を拡張することができます。

## <a name="considerations-for-data-management-gateway"></a>Data Management Gateway に関する考慮事項
**ゲートウェイのセットアップ**: Data Management Gateway をホストする専用のマシンを使用することをお勧めします。 [Data Management Gateway を使用する際の考慮事項](data-factory-data-management-gateway.md#considerations-for-using-gateway)に関する記事を参照してください。  

**ゲートウェイの監視とスケール アップ/スケール アウト**: 1 つまたは複数のゲートウェイ ノードを持つ 1 つの論理ゲートウェイは、同時実行される複数のコピー アクティビティに対応できます。 Azure ポータルで、ゲートウェイ マシンのリソース使用率 (CPU、メモリ、ネットワーク (着信/発信) など) のほぼリアルタイムのスナップショットと、同時実行されているジョブの数と上限を表示できます。「[ポータルでのゲートウェイの監視](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal)」をご覧ください。 ハイブリッド データ移動で大量のコピー アクティビティを同時実行するか大量のデータをコピーする高いニーズがある場合は、リソースをもっと有効に利用したり、コピー能力を高めるためのより多くのリソースのプロビジョニングを実行したりできるように、[ゲートウェイをスケール アップするかスケール アウトする](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations)ことを検討してください。 

## <a name="considerations-for-the-source"></a>ソースに関する考慮事項
### <a name="general"></a>全般
基になるデータ ストアが、そこで実行されるその他のワークロードで手一杯になっていないことを確認します。

Microsoft のデータ ストアの場合は、データ ストアに特化した[監視とチューニングに関するトピック](#performance-reference)を参照してください。このトピックには、データ ストアのパフォーマンス特性に関する説明と、応答時間を短縮しスループットを最大限に高める方法が記載されています。

Blob Storage から SQL Data Warehouse にデータをコピーする場合は、 **PolyBase** を使用してパフォーマンスを向上させることを検討してください。 詳細については、「 [PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) 」を参照してください。 ユース ケースを使用したチュートリアルについては、[1 TB のデータを Azure Data Factory を使用して 15 分以内に Azure SQL Data Warehouse に読み込む方法](data-factory-load-sql-data-warehouse.md)に関するページを参照してください。

### <a name="file-based-data-stores"></a>ファイル ベースのデータ ストア
*"(Blob Storage、Data Lake Store、Amazon S3、オンプレミスのファイル システム、オンプレミスの HDFS など)"*

* **ファイル サイズとファイル数の平均**: コピー アクティビティはデータを 1 ファイルずつ転送します。 移動するデータ量は同じでも、データが少数の大きなファイルでなく、多数の小さなファイルで構成されている場合、ファイルごとにブートストラップ フェーズが存在するため、全体的なスループットは低下します。 したがって、可能であれば、小さなファイルをまとめて大きなファイルとすることで、スループットを高めてください。
* **ファイルの形式と圧縮**: パフォーマンスを向上させるその他の方法については、「[シリアル化と逆シリアル化に関する考慮事項](#considerations-for-serialization-and-deserialization)」と「[圧縮に関する考慮事項](#considerations-for-compression)」を参照してください。
* **Data Management Gateway** が必要な**オンプレミスのファイル システム**のシナリオについては、「[Data Management Gateway に関する考慮事項](#considerations-for-data-management-gateway)」を参照してください。

### <a name="relational-data-stores"></a>リレーショナル データ ストア
*"(SQL Database、SQL Data Warehouse、Amazon Redshift、SQL Server データベースのほか、Oracle、MySQL、DB2、Teradata、Sybase、PostgreSQL データベースなど)"*

* **データ パターン**: テーブル スキーマは、コピーのスループットに影響を与えます。 同じ量のデータをコピーする場合は、行のサイズが小さいよりも大きい方がパフォーマンスは高くなります。 これは、データベースは、データのバッチが少ないほど、また含まれている行が少ないほど、そのデータを効率的に取得できるためです。
* **クエリまたはストアド プロシージャ**: データをより効率的にフェッチできるように、コピー アクティビティ ソースで指定するクエリまたはストアド プロシージャのロジックを最適化します。
* SQL Server や Oracle など、**Data Management Gateway** を使用する必要がある**オンプレミスのリレーショナル データベース**の場合は、「[Data Management Gateway に関する考慮事項](#considerations-on-data-management-gateway)」を参照してください。

## <a name="considerations-for-the-sink"></a>シンクに関する考慮事項
### <a name="general"></a>全般
基になるデータ ストアが、そこで実行されるその他のワークロードで手一杯になっていないことを確認します。

Microsoft のデータ ストアについては、データ ストアに特化した [監視とチューニングに関するトピック](#performance-reference) を参照してください。 これらのトピックでは、データ ストアのパフォーマンス特性に関する説明と、応答時間を短縮しスループットを最大限に高める方法が記載されています。

**Blob Storage** から **SQL Data Warehouse** にデータをコピーする場合は、**PolyBase** を使用してパフォーマンスを向上させることを検討してください。 詳細については、「 [PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) 」を参照してください。 ユース ケースを使用したチュートリアルについては、[1 TB のデータを Azure Data Factory を使用して 15 分以内に Azure SQL Data Warehouse に読み込む方法](data-factory-load-sql-data-warehouse.md)に関するページを参照してください。

### <a name="file-based-data-stores"></a>ファイル ベースのデータ ストア
*"(Blob Storage、Data Lake Store、Amazon S3、オンプレミスのファイル システム、オンプレミスの HDFS など)"*

* **コピー動作**: 別のファイル ベースのデータ ストアからデータをコピーする場合、コピー アクティビティには **copyBehavior** プロパティを使用したオプションが 3 つあります。 それらは、階層の維持、階層の平坦化、およびファイルのマージです。 階層の維持または階層の平坦化では、パフォーマンス オーバーヘッドはほとんどありませんが、ファイルのマージではパフォーマンス オーバーヘッドが増加します。
* **ファイルの形式と圧縮**: パフォーマンスを向上させるその他の方法については、「[シリアル化と逆シリアル化に関する考慮事項](#considerations-for-serialization-and-deserialization)」と「[圧縮に関する考慮事項](#considerations-for-compression)」を参照してください。
* **Blob Storage**: 現在のところ、データ転送およびスループットを最適化するために、ブロック BLOB だけをサポートしています。
* **Data Management Gateway** を使用する必要がある**オンプレミスのファイル システム**のシナリオについては、「[Data Management Gateway に関する考慮事項](#considerations-for-data-management-gateway)」を参照してください。

### <a name="relational-data-stores"></a>リレーショナル データ ストア
*"(SQL Database、SQL Data Warehouse、SQL Server データベース、Oracle データベースなど)"*

* **コピー動作**: コピー アクティビティは、**sqlSink** 用に設定されたプロパティに応じて、さまざまな方法でデータをターゲット データベースに書き込みます。
  * 既定では、データ移動サービスは、一括コピー API を使用して、データを追加モードで挿入します。これにより、最高のパフォーマンスが得られます。
  * シンク内でストアド プロシージャを構成すると、データベースは一括読み込みは行わず、一度に 1 行ずつデータを適用します。 パフォーマンスは大幅に低下します。 データ セットが大きい場合、代わりに **sqlWriterCleanupScript** プロパティを使用すること (適用できる場合) を検討してください。
  * 各コピー アクティビティ実行の **sqlWriterCleanupScript** プロパティを構成すると、サービスによってスクリプトがトリガーされるので、一括コピー API を使用してデータを挿入します。 たとえば、テーブル全体を最新のデータで上書きするには、ソースから新しいデータを一括で読み込む前にすべてのレコードを削除するためのスクリプトを指定することができます。
* **データのパターンとバッチ サイズ**:
  * テーブル スキーマは、コピーのスループットに影響を与えます。 同じ量のデータをコピーする場合、行のサイズが小さいよりも行のサイズが大きい方がパフォーマンスは高くなります。これは、データのバッチが少ない方が、データベースがデータを効率的にコミットできるためです。
  * コピー アクティビティは、一連のバッチでデータを挿入します。 バッチの行数は、 **writeBatchSize** プロパティを使用して設定できます。 データの行が小さい場合は、 **writeBatchSize** プロパティをより大きな値に設定することで、バッチ オーバーヘッドを減らし、スループットを向上させることができます。 データの行サイズが大きい場合は、 **writeBatchSize**を大きくするときに注意が必要です。 値を大きくすると、データベースに過剰な負荷がかかるため、コピーに失敗する可能性があります。
* SQL Server や Oracle など、**Data Management Gateway** を使用する必要がある**オンプレミスのリレーショナル データベース**の場合は、「[Data Management Gateway に関する考慮事項](#considerations-for-data-management-gateway)」を参照してください。

### <a name="nosql-stores"></a>NoSQL ストア
*(Table Storage、Azure Cosmos DB など)*

* **Table Storage**の場合:
  * **パーティション**: インターリーブされたパーティションにデータを書き込むと、パフォーマンスが大幅に低下します。 データが複数のパーティションに順次効率よく挿入されるように、パーティション キーでソース データを並べ替えるか、データが 1 つのパーティションに書き込まれるようにロジックを調整します。
* **Azure Cosmos DB** の場合:
  * **バッチ サイズ**: **writeBatchSize** プロパティは、ドキュメントを作成する Azure Cosmos DB サービスへの並列要求の数を設定します。 **writeBatchSize** を増やすとパフォーマンスがよくなります。Azure Cosmos DB に送信される並列要求の数が増えるためです。 ただし、Azure Cosmos DB に書き込む際は、スロットルに注意してください (エラー メッセージは "要求率が大きいです")。 スロットルは、ドキュメントのサイズ、ドキュメント内の語句の数、ターゲット コレクションの索引作成ポリシーなど、さまざまな要因によって発生する可能性があります。 コピーのスループットを高めるには、より適切なコレクション (S3 など) の使用を検討してください。

## <a name="considerations-for-serialization-and-deserialization"></a>シリアル化と逆シリアル化に関する考慮事項
入力データ セットまたは出力データ セットがファイルであると、シリアル化および逆シリアル化が実行されることがあります。 コピー アクティビティでサポートされているファイル形式について詳しくは、「[サポートされているファイル形式と圧縮形式](data-factory-supported-file-and-compression-formats.md)」をご覧ください。

**コピー動作**:

* ファイル ベースのデータ ストア間でファイルをコピーする:
  * 入力データ セットと出力データ セットが、両方とも同じファイル形式であるか、ファイル形式が設定されていない場合、データ移動サービスは、シリアル化または逆シリアル化を実行することなく、バイナリ コピーを実行します。 ソースとシンクのファイル形式の設定が互いに異なるシナリオと比較して、より高いスループットが示されます。
  * 入力データ セットと出力データ セットは両方ともテキスト形式で、エンコードの種類のみが異なる場合、データ移動サービスはエンコード変換のみを行います。 バイナリ コピーと比較した場合にパフォーマンス オーバーヘッドがいくらか発生するシリアル化と逆シリアル化は実行しません。
  * 入力データ セットと出力データ セットのファイル形式が異なるか、または構成 (区切り記号など) が異なる場合、データ移動サービスは、ソース データをストリームに逆シリアル化し、変換して、指定された出力形式にシリアル化します。 この操作では、他のシナリオと比較して、はるかに大きなパフォーマンス オーバーヘッドが発生します。
* ファイル ベースではないデータ ストアとの間で (たとえば、ファイル ベースのストアからリレーショナル ストアへの) コピーを行う場合、シリアル化または逆シリアル化の手順が必要になります。 この手順により、大幅なパフォーマンスのオーバーヘッドが発生します。

**ファイル形式**: ファイル形式の選択は、コピーのパフォーマンスに影響する場合があります。 たとえば、Avro は、データと共にメタデータを格納するコンパクトなバイナリ形式です。 Hadoop エコシステムでの処理やクエリ実行について、幅広くサポートされています。 ただし、Avro の場合は、シリアル化と逆シリアル化によってコピーのスループットが低下するので、テキスト形式と比較してコストがかかります。 処理フロー全体を考慮して、ファイル形式を選択してください。 まずは、データを格納する形式をソース データ ストアにするのか、外部システムから抽出するのか、ストレージや分析処理やクエリに最適な形式は何か、レポート ツールおよび視覚化ツール用にデータ マートにエクスポートするときの形式は何か、などを考慮する必要があります。 ときには、読み取りおよび書き込みパフォーマンスの点では準最適なファイル形式であっても、分析プロセス全体を考慮するとよい選択である場合があります。

## <a name="considerations-for-compression"></a>圧縮に関する考慮事項
入力データ セットまたは出力データ セットがファイルである場合は、ターゲットにデータを書き込むときに圧縮または圧縮解除を実行するようにコピー アクティビティを設定することができます。 圧縮を選択すると、入力/出力 (I/O) と CPU の間にトレードオフが生じます。 データを圧縮すると、コンピューティング リソースの面では余分なコストがかかります。 代わりに、ネットワーク I/O およびストレージは削減できます。 データによっては、コピーの全体的なスループットが大幅に向上する場合があります。

**コーデック**: コピー アクティビティでは、圧縮の種類として gzip、bzip2、および Deflate がサポートされています。 Azure HDInsight では、3 種類すべてを処理に利用できます。 各圧縮コーデックには、長所があります。 たとえば、bzip2 はコピーのスループットが最も低いのですが、分割して処理できるため、最高の Hive クエリ パフォーマンスを発揮します。 Gzip は最もバランスの取れたオプションであり、最も頻繁に使用されます。 エンド ツー エンドのシナリオに最適なコーデックを選択してください。

**レベル:** 各圧縮コーデックに対して、最速圧縮と最適圧縮という 2 つのオプションのいずれかを選択できます。 最速圧縮オプションでは、可能な限り短時間でデータの圧縮を完了しますが、生成ファイルが最適に圧縮されない場合があります。 最適圧縮オプションでは、より多くの時間をデータ圧縮に費やしますが、データ量を最小限まで圧縮します。 両方のオプションを実際にテストして、どちらが全体的なパフォーマンスで優れているかを確認することができます。

**考慮事項**: オンプレミス ストアとクラウド間で大量のデータをコピーする場合は、中間 Blob Storage と圧縮の使用を検討してください。 企業ネットワークと Azure サービスの帯域幅が制限要因となっていて、入力データ セットと出力データ セットの両方を圧縮されない形式にしたい場合は、中間ストレージを使用すると便利です。 具体的には、1 つのコピー アクティビティを 2 つのコピー アクティビティに分割できます。 最初のコピー アクティビティは、圧縮形式で、ソースから中間またはステージング BLOB へのコピーを行います。 2 番目のコピー アクティビティは、ステージングから圧縮されたデータをコピーし、シンクへの書き込み中に圧縮を解除します。

## <a name="considerations-for-column-mapping"></a>列マッピングに関する考慮事項
コピー アクティビティの **columnMappings** プロパティを設定して、入力列のすべてまたはサブセットを出力列にマップすることができます。 データ移動サービスは、ソースからデータを読み取った後、データをシンクに書き込む前に、データに対して列マッピングを実行する必要があります。 この追加の処理により、コピーのスループットが低下します。

ソース データ ストアがクエリ可能な場合 (たとえば、SQL Database や SQL Server のようなリレーショナル ストアであるか、Table Storage や Azure Cosmos DB のような NoSQL ストアである場合) は、列マッピングを使用するのでなく、列フィルタリングと順序変更ロジックを **query** プロパティにプッシュすることを検討してください。 そうした場合、データ移動サービスがソース データ ストアからデータを読み取る際にプロジェクションが発生し、効率が大幅に向上します。

## <a name="other-considerations"></a>その他の考慮事項
コピーするデータのサイズが大きい場合は、Data Factory のスライス メカニズムを使用してさらにデータを分割するように、ビジネス ロジックを調整することができます。 次に、コピー アクティビティをより頻繁に実行するようにスケジュールして、各コピー アクティビティ実行のデータ サイズを小さくします。

Data Factory が同じデータ ストアに同時に接続することを必要とするデータ セットの数とコピー アクティビティの数に注意してください。 同時コピー ジョブの数が多いと、データ ストアのスロットルが発生し、パフォーマンスの低下、コピー ジョブの内部的な再試行、場合によっては実行の失敗につながるおそれがあります。

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>サンプル シナリオ: オンプレミス SQL Server から Blob Storage へのコピー
**シナリオ**: オンプレミスの SQL Server から Blob Storage に CSV 形式でデータをコピーするパイプラインが構築されています。 コピー ジョブを高速にするために、CSV ファイルは bzip2 形式で圧縮されます。

**テストと分析**: コピー アクティビティのスループットは 2 MBps 未満で、パフォーマンスのベンチマークをかなり下回っています。

**パフォーマンスの分析とチューニング**: パフォーマンスの問題を解決するために、データが処理、移動されるしくみを見ていきます。

1. **データの読み取り**: ゲートウェイは、SQL Server への接続を開き、クエリを送信します。 SQL Server は、イントラネット経由でゲートウェイにデータ ストリームを送信することで応答します。
2. **データのシリアル化と圧縮**: ゲートウェイはデータ ストリームを CSV 形式にシリアル化し、データを bzip2 ストリームに圧縮します。
3. **データの書き込み**: ゲートウェイは bzip2 ストリームをインターネット経由で Blob Storage にアップロードします。

ご覧のとおり、データは SQL Server、LAN、ゲートウェイ、WAN、Blob Storage の順に、ストリーミングで順次処理および移動されています。 **全体的なパフォーマンスは、パイプラインを通じて最低のスループットが上限となっています**。

![Data flow](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

次に示す要因の 1 つ以上がパフォーマンスのボトルネックの原因である可能性があります。

* **ソース**: 負荷が大きいため、SQL Server 自体のスループットが低くなっています。
* **Data Management Gateway**:
  * **LAN**: ゲートウェイは SQL Server コンピューターから離れた場所にあり、低帯域幅で接続されています。
  * **ゲートウェイ**: ゲートウェイは、以下の操作を実行して、負荷の上限に達しています。
    * **シリアル化**: CSV へのデータ ストリームのシリアル化で、スループットが低くなっています。
    * **圧縮**: 低速の圧縮コーデック (たとえば、Core i7 で 2.8 MBps の bzip2) を選択しました。
  * **WAN**: 企業ネットワークと Azure サービス間の帯域幅が小さい状態です (たとえば、T1 = 1,544 kbps、T2 = 6,312 kbps)。
* **シンク**: Blob Storage のスループットが低くなっています  (SLA により最低でも 60 MBps が保証されているため、このシナリオは現実的ではありません)。

この場合、bzip2 データ圧縮がパイプライン全体を遅くしている可能性があります。 gzip 圧縮コーデックに切り替えると、このボトルネックが緩和される場合があります。

## <a name="sample-scenarios-use-parallel-copy"></a>サンプル シナリオ: 並列のコピーを使用します。
**シナリオ l:** オンプレミスのファイル システムから Blob Storage に 1 MB のファイルを 1,000 個コピーする。

**分析とパフォーマンスのチューニング**: たとえば、クアッド コア マシン上にゲートウェイをインストール済みの場合、Data Factory は 16 の並列コピーを使用して、ファイル システムから Blob Storage にファイルを同時に移動します。 この並列実行では、高いスループットが得られます。 並列コピーの数を明示的に指定することもできます。 多数の小さなファイルをコピーする場合、並列コピーではリソースがより効果的に活用されるため、スループットが大幅に向上します。

![シナリオ 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**シナリオ II:** Blob Storage から Data Lake Store Analytics にそれぞれ 500 MB の BLOB を 20 個コピーして、パフォーマンスを調整する。

**分析とパフォーマンスのチューニング**: このシナリオでは、Data Factory は単一のコピー (**parallelCopies** を 1 に設定) と単一クラウド データ移動単位を使用して、データを Blob Storage から Data Lake Store にコピーします。 観察されるスループットは、「 [パフォーマンス リファレンス](#performance-reference)」セクションで説明されているスループットに近くなります。   

![シナリオ 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**シナリオ III**: 個々のファイル サイズが数十 MB より大きく、総量が巨大である。

**分析とパフォーマンスのチューニング**: **parallelCopies** を増やしても、単一クラウド DMU のリソース制限があるため、コピーのパフォーマンスは向上しません。 代わりに、より多くのクラウド DMU を指定して、データ移動を実行するリソースをより多く取得する必要があります。 **parallelCopies** プロパティの値は指定しないでください。 Data Factory が自動的に並列処理を行います。 この場合、 **cloudDataMovementUnits** を 4 に設定すると、スループットが約 4 倍になります。

![シナリオ 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>リファレンス
ここでは、サポートされているいくつかのデータ ストアについて、パフォーマンスの監視とチューニングに関するリファレンス情報をいくつか示します。

* Azure Storage (Blob Storage と Table Storage を含む): [Azure Storage のスケーラビリティのターゲット](../../storage/common/storage-scalability-targets.md)に関する記事と [Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](../../storage/common/storage-performance-checklist.md)に関する記事
* Azure SQL Database: [パフォーマンスを監視](../../sql-database/sql-database-single-database-monitor.md) し、データベース トランザクション ユニット (DTU) の割合を確認できます。
* Azure SQL Data Warehouse: 処理能力は Data Warehouse ユニット (DWU) で測定されます。「[Azure SQL Data Warehouse のコンピューティング能力の管理 (概要)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)」を参照してください。
* Azure Cosmos DB: [Azure Cosmos DB のパフォーマンス レベル](../../cosmos-db/performance-levels.md)に関する記事
* オンプレミスの SQL Server: 「 [Monitor and tune for performance (パフォーマンスの監視とチューニング)](https://msdn.microsoft.com/library/ms189081.aspx)
* オンプレミスのファイル サーバー: 「 [Performance Tuning for File Servers (ファイル サーバーのパフォーマンス チューニング)](https://msdn.microsoft.com/library/dn567661.aspx)
