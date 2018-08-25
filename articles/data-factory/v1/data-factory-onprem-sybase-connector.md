---
title: Azure Data Factory を使用した Sybase からのデータ移動 | Microsoft Docs
description: Azure Data Factory を使用して Sybase データベースからデータを移動する方法を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: b379ee10-0ff5-4974-8c87-c95f82f1c5c6
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 75fbfe1e682f0022385bb5c406d772bb217180fd
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045271"
---
# <a name="move-data-from-sybase-using-azure-data-factory"></a>Azure Data Factory を使用して Sybase からデータを移動する
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-onprem-sybase-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-sybase.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の Sybase コネクタ](../connector-sybase.md)に関するページをご覧ください。

この記事では、Azure Data Factory のコピー アクティビティを使って、オンプレミスの Sybase データベースからデータを移動させる方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。

オンプレミスの Sybase ストアから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](data-factory-data-movement-activities.md#supported-data-stores-and-formats)の表をご覧ください。 Data Factory は、現時点では Sybase データ ストアから他のデータ ストアへのデータ移動のみをサポートし、他のデータ ストアから Sybase データ ストアへのデータ移動に関してはサポートしていません。 

## <a name="prerequisites"></a>前提条件
Data Factory のサービスでは、Data Management Gateway を使用したオンプレミスの Sybase ソースへの接続をサポートします。 Data Management Gateway の詳細およびゲートウェイの設定手順については、「 [オンプレミスの場所とクラウド間のデータ移動](data-factory-move-data-between-onprem-and-cloud.md) 」を参照してください。

Sybase データベースが Azure IaaS VM でホストされている場合でも、ゲートウェイは必須です。 ゲートウェイはデータ ストアと同じ IaaS VM にインストールできるほか、ゲートウェイがデータベースに接続できれば別の VM にインストールしてもかまいません。

> [!NOTE]
> 接続/ゲートウェイに関する問題のトラブルシューティングのヒントについては、 [ゲートウェイの問題のトラブルシューティング](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) に関するセクションをご覧ください。

## <a name="supported-versions-and-installation"></a>サポートされているバージョンとインストール
Data Management Gateway で Sybase データベースに接続するには、[Sybase iAnywhere.Data.SQLAnywhere のデータ プロバイダー](http://go.microsoft.com/fwlink/?linkid=324846)の 16 以降を Data Management Gateway と同じシステムにインストールする必要があります。 

SAP Sybase SQL Anywhere (ASA) バージョン 16 以降がサポートされます。IQ および ASE はサポートされません。

## <a name="getting-started"></a>使用の開始
さまざまなツールまたは API を使用して、オンプレミスの Cassandra データ ストアからデータを移動するコピー アクティビティでパイプラインを作成できます。 

- パイプラインを作成する最も簡単な方法は、**コピー ウィザード**を使うことです。 データのコピー ウィザードを使用してパイプラインを作成する簡単な手順については、「 [チュートリアル: コピー ウィザードを使用してパイプラインを作成する](data-factory-copy-data-wizard-tutorial.md) 」をご覧ください。 
- 次のツールを使ってパイプラインを作成することもできます。**Azure Portal**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、**.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。 

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。
2. コピー操作用の入力データと出力データを表す**データセット**を作成します。 
3. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。 

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。  オンプレミスの Sybase データ ストアからデータをコピーするために使用する Data Factory エンティティに関する JSON 定義のサンプルについては、この記事のセクション、「[JSON の使用例: Sybase から Azure Blob へのデータのコピー](#json-example-copy-data-from-sybase-to-azure-blob)」をご覧ください。 

次のセクションでは、Sybase データ ストアに固有のデータ ファクトリ エンティティの定義に使用される JSON プロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、Sybase のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティを **OnPremisesSybase** |[はい] |
| [サーバー] |Sybase サーバーの名前です。 |[はい] |
| [データベース] |Sybase データベースの名前です。 |[はい] |
| schema |データベース内のスキーマの名前です。 |いいえ  |
| authenticationType |Sybase データベースへの接続に使用される認証の種類です。 Anonymous、Basic、Windows のいずれかの値になります。 |[はい] |
| username |Basic または Windows 認証を使用している場合は、ユーザー名を指定します。 |いいえ  |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 |いいえ  |
| gatewayName |Data Factory サービスが、オンプレミスの Sybase データベースへの接続に使用するゲートウェイの名前です。 |[はい] |

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **RelationalTable** 型のデータセット (Sybase データセットを含む) の **typeProperties** セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する Sybase データベース インスタンスのテーブルの名前です。 |いいえ (**RelationalSource** の **クエリ** が指定されている場合) |

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプラインの作成](data-factory-create-pipelines.md)に関する記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

source の種類が **RelationalSource** (Sybase を含む) である場合は、**typeProperties** セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| クエリ |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 例: Select * from MyTable。 |いいえ (**データセット**の **tableName** が指定されている場合) |


## <a name="json-example-copy-data-from-sybase-to-azure-blob"></a>JSON の使用例: Sybase から Azure Blob へのデータのコピー
次の例は、[Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)、または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 これらの例は、Sybase データベースから Azure BLOB ストレージにデータをコピーする方法を示しています。 ただし、Azure Data Factory のコピー アクティビティを使用して、 [こちら](data-factory-data-movement-activities.md#supported-data-stores-and-formats) に記載されているシンクのいずれかにデータをコピーすることができます。   

このサンプルでは、次の Data Factory のエンティティがあります。

1. [OnPremisesSybase](data-factory-onprem-sybase-connector.md#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 型のリンクされたサービス。
3. [RelationalTable](data-factory-onprem-sybase-connector.md#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [RelationalSource](data-factory-onprem-sybase-connector.md#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは Sybase データベースのクエリ結果のデータを BLOB に 1 時間ごとにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

最初の手順として、データ管理ゲートウェイを設定します。 設定手順は、 [オンプレミスの場所とクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md) に関する記事に記載されています。

**Sybase のリンクされたサービス:**

```JSON
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Azure BLOB ストレージのリンクされたサービス:**

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Sybase の入力データセット:**

このサンプルでは、Sybase で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestamp」という名前の列が含まれているものと想定しています。

"external": true の設定により、このデータセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。 リンクされたサービスの**型**は **RelationalTable** に設定されることに注意してください。

```JSON
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (頻度: 時間、間隔: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```JSON
{
    "name": "AzureBlobSybaseDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**コピー アクティビティのあるパイプライン:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **RelationalSource** に設定され、**sink** 型が **BlobSink** に設定されています。 **query** プロパティに指定された SQL クエリは、データベースの DBA.Orders テーブルからデータを選択します。

```JSON
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from DBA.Orders"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "SybaseDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobSybaseDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SybaseToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-sybase"></a>Sybase の型マッピング
[データ移動アクティビティ](data-factory-data-movement-activities.md) に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

Sybase では、T-SQL と T-SQL 型をサポートします。 sql 型から .NET 型のマッピング テーブルについては、「 [Azure SQL コネクタ](data-factory-azure-sql-connector.md) 」の記事を参照してください。

## <a name="map-source-to-sink-columns"></a>ソース列からシンク列へのマップ
ソース データセット列のシンク データセット列へのマッピングの詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページをご覧ください。

## <a name="repeatable-read-from-relational-sources"></a>リレーショナル ソースからの反復可能読み取り
リレーショナル データ ストアからデータをコピーする場合は、意図しない結果を避けるため、再現性に注意する必要があります。 Azure Data Factory では、スライスを手動で再実行できます。 障害が発生したときにスライスを再実行できるように、データセットの再試行ポリシーを構成することもできます。 いずれかの方法でスライスが再実行された際は、何度スライスが実行されても同じデータが読み込まれることを確認する必要があります。 [リレーショナル ソースからの反復可能読み取り](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)に関するページをご覧ください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。
