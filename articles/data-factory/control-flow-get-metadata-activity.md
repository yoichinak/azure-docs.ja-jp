---
title: Azure Data Factory の GetMetadata アクティビティ | Microsoft Docs
description: SQL Server ストアド プロシージャ アクティビティを使用して、Data Factory パイプラインから Azure SQL Database または Azure SQL Data Warehouse でストアド プロシージャを呼び出す方法について説明します。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: shlo
ms.openlocfilehash: c24bec7366ea62b3dd8f7a301c9d2d62c6dd6c7d
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859280"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Azure Data Factory の GetMetadata アクティビティ
GetMetadata アクティビティを使用すると、Azure Data Factory で任意のデータの**メタデータ**を取得できます。 このアクティビティは、次のシナリオで使用できます。

- 任意のデータのメタデータ情報を検証する
- データが準備完了/使用可能になったらパイプラインをトリガーする

制御フローでは、次の機能を使用できます。

- GetMetadata アクティビティからの出力を条件式で使用することによって検証を実行できます。
- Do-Until ループによって条件が満たされたら、パイプラインをトリガーできます。

## <a name="supported-capabilities"></a>サポートされる機能

GetMetadata アクティビティは必須の入力としてデータセットを受け取り、使用可能なメタデータ情報をアクティビティ出力として出力します。 現時点では、対応する取得可能なメタデータを持つ次のコネクターがサポートされ、サポートされるメタデータの最大サイズは **1 MB** です。

>[!NOTE]
>セルフホステッドの Integration Runtime で GetMetadata アクティビティを実行する場合、最新の機能はバージョン 3.6 以降でサポートされます。 

### <a name="supported-connectors"></a>サポートされているコネクタ

**File Storage**

| コネクタ/メタデータ | itemName<br>(ファイル/フォルダー) | itemType<br>(ファイル/フォルダー) | size<br>(ファイル) | created<br>(ファイル/フォルダー) | lastModified<br>(ファイル/フォルダー) |childItems<br>(フォルダー) |contentMD5<br>(ファイル) | structure<br/>(ファイル) | columnCount<br>(ファイル) | exists<br>(ファイル/フォルダー) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Amazon S3 | √/√ | √/√ | √ | x/x | √/√* | √ | ○ | √ | √ | √/√* |
| Azure BLOB | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| Azure Data Lake Store | √/√ | √/√ | √ | x/x | √/√ | √ | ○ | √ | √ | √/√ |
| Azure File Storage | √/√ | √/√ | √ | √/√ | √/√ | √ | ○ | √ | √ | √/√ |
| ファイル システム | √/√ | √/√ | √ | √/√ | √/√ | √ | ○ | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | ○ | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | ○ | √ | √ | √/√ |

- Amazon S3 の場合、`lastModified` はバケットとキーには適用されますが、仮想フォルダーには適用されません。`exists` はバケットとキーには適用されますが、プレフィックスまたは仮想フォルダーには適用されません。
- Azure Blob の場合、`lastModified` はコンテナーと BLOB には適用されますが、仮想フォルダーには適用されません。

**リレーショナル データベース**

| コネクタ/メタデータ | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| Azure SQL Database | √ | √ | √ |
| Azure SQL Data Warehouse | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>メタデータのオプション

GetMetadata アクティビティのフィールド リストで、次のメタデータの種類を指定して取得することができます。

| メタデータの種類 | 説明 |
|:--- |:--- |
| itemName | ファイルまたはフォルダーの名前です。 |
| itemType | ファイルまたはフォルダーの種類です。 出力値は `File` または `Folder` です。 |
| size | ファイルのサイズ (バイト単位) です。 ファイルのみに適用されます。 |
| created | ファイルまたはフォルダーの作成日時です。 |
| lastModified | ファイルまたはフォルダーが最後に変更された日時です。 |
| childItems | 特定のフォルダー内のサブフォルダーとファイルの一覧です。 フォルダーにのみ適用されます。 出力値は、各子項目の名前と種類の一覧です。 |
| contentMD5 | ファイルの MD5 です。 ファイルのみに適用されます。 |
| structure | ファイルまたはリレーショナル データベース テーブル内のデータ構造です。 出力値は、列名と列の型の一覧です。 |
| columnCount | ファイルまたはリレーショナル テーブル内の列の数です。 |
| exists| ファイル/フォルダー/テーブルが存在するかどうかを示します。 GetaMetadata フィールド リストで "exists" が指定される場合、項目 (ファイル/フォルダー/テーブル) が存在しなくてもアクティビティは失敗せずに、`exists: false` が出力に返されることに注意してください。 |

>[!TIP]
>ファイル/フォルダー/テーブルが存在するかどうかを検証するには、`exists` を GetMetadata アクティビティのフィールド リストに指定した後、アクティビティの出力で `exists: true/false` を確認することができます。 フィールド リストで `exists` が設定されていない場合、オブジェクトが見つからないと GetMetadata アクティビティは失敗します。

## <a name="syntax"></a>構文

**GetMetadata アクティビティ**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Dataset**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>型のプロパティ

現在、GetMetadata アクティビティは、次の種類のメタデータ情報をフェッチできます。

プロパティ | 説明 | 必須
-------- | ----------- | --------
fieldList | 必要なメタデータ情報のタイプを一覧表示します。 サポートされているメタデータに関する詳細は、[メタデータ オプション](#metadata-options) セクションをご覧ください。 | [はい] 
dataset | GetMetadata アクティビティによってメタデータ アクティビティが取得される参照データセット。 サポートされているコネクタに関する詳細は、[サポートされる機能](#supported-capabilities)セクションをご覧になり、データセット構文の詳細に関するコネクタ トピックを参照してください。 | [はい]

## <a name="sample-output"></a>サンプル出力

GetMetadata の結果は、アクティビティの出力に表示されます。 以下は、フィールド リストに参照として選択された完全なメタデータ オプションの 2 つの例です。 後続のアクティビティで結果を使用するには、パターン `@{activity('MyGetMetadataActivity').output.itemName}` を使用します。

### <a name="get-a-files-metadata"></a>ファイルのメタデータを取得する

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>フォルダーのメタデータを取得する

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>次の手順
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。 

- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
