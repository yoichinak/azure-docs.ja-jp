---
title: Azure Data Factory を使用して Oracle をコピー元またはコピー先としてデータをコピーする | Microsoft Docs
description: Data Factory を使用して、サポートされるソース データ ストアから Oracle データベースにデータをコピーしたり、Oracle からサポートされるシンク ストアにコピーしたりする方法を説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: jingwang
ms.openlocfilehash: 5039399ac875add02319e1a745d99344956c7bee
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860216"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Azure Data Factory を使用した Oracle をコピー元またはコピー先とするデータのコピー
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-onprem-oracle-connector.md)
> * [現在のバージョン](connector-oracle.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Oracle データベースをコピー先またはコピー元としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Oracle データベースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 サポートされている任意のデータ ソース ストアから Oracle データベースにデータをコピーすることもできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Oracle コネクタは、次のバージョンの Oracle データベースをサポートします。 基本認証または OID の認証もサポートしています。

- Oracle 12c R1 (12.1)
- Oracle 11g R1、R2 (11.1、11.2)
- Oracle 10g R1、R2 (10.1、10.2)
- Oracle 9i R1、R2 (9.0.1、9.2)
- Oracle 8i R3 (8.1.7)

> [!Note]
> Oracle プロキシ サーバーはサポートされていません。

## <a name="prerequisites"></a>前提条件

パブリックにアクセスできない Oracle データベースをコピー元またはコピー先としてデータをコピーするには、セルフホステッド統合ランタイムを設定する必要があります。 統合ランタイムの詳細については、「[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)」を参照してください。 統合ランタイムには、組み込みの Oracle ドライバーがあります。 そのため、Oracle をコピー元またはコピー先としてデータをコピーするときに、ドライバーを手動でインストールする必要はありません。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Oracle コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Oracle のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **Oracle** に設定する必要があります。 | [はい] |
| connectionString | Oracle Database インスタンスに接続するために必要な情報を指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。<br><br>**サポートされる接続の種類**: **Oracle SID** または **Oracle サービス名**を使用してデータベースを識別できます。<br>- SID を使用する場合: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- サービス名を使用する場合: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | [はい] |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 セルフホステッド統合ランタイムまたは Azure 統合ランタイム (データ ストアがパブリックにアクセスできる場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ  |

>[!TIP]
>"ORA 01025: UPI パラメータの値が有効範囲外です" というエラーが発生し、Oracle がバージョン 8i である場合、`WireProtocolMode=1` を接続文字列に追加してもう一度やり直してください。

**例:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Oracle データセットでサポートされるプロパティの一覧を示します。

Oracle をコピー元またはコピー先としてデータをコピーするには、データセットの type プロパティを **OracleTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは **OracleTable** に設定する必要があります。 | [はい] |
| tableName |リンクされたサービスが参照する Oracle データベースのテーブルの名前です。 | [はい] |

**例:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Oracle のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="oracle-as-a-source-type"></a>ソース タイプとしての Oracle

Oracle からデータをコピーするは、コピー アクティビティのソースの種類を **OracleSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **OracleSource** に設定する必要があります。 | [はい] |
| oracleReaderQuery | カスタム SQL クエリを使用してデータを読み取ります。 例: `"SELECT * FROM MyTable"`。 | いいえ  |

"oracleReaderQuery" を指定しない場合は、データセットの "structure" セクションに定義された列を使用して、Oracle データベースに対して実行するクエリ (`select column1, column2 from mytable`) が作成されます。 データセット定義に "構造" がない場合は、すべての列がテーブルから選択されます。

**例:**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-a-sink-type"></a>シンクの種類としての Oracle

Oracle にデータをコピーするには、コピー アクティビティのシンクの種類を **OracleSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは **OracleSink** に設定する必要があります。 | [はい] |
| writeBatchSize | バッファー サイズが writeBatchSize に達したときに SQL テーブルにデータを挿入します。<br/>使用可能な値: 整数 (行数)。 |いいえ (既定値は 10,000) |
| writeBatchTimeout | タイムアウトする前に一括挿入操作の完了を待つ時間です。<br/>使用可能な値: 期間。 たとえば "00:30:00" (30 分) を指定できます。 | いいえ  |
| preCopyScript | コピー アクティビティの毎回の実行で、データを Oracle に書き込む前に実行する SQL クエリを指定します。 このプロパティを使用して、事前に読み込まれたデータをクリーンアップできます。 | いいえ  |

**例:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-oracle"></a>Oracle のデータ型マッピング

Oracle をコピー元またはコピー先としてデータをコピーするとき、次の Oracle のデータ型から Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Oracle データ型 | Data Factory の中間データ型 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(Oracle 10g 以上でのみサポート) |
| CHAR |String |
| CLOB |String |
| DATE |Datetime |
| FLOAT |Decimal、String (有効桁数が 28 を超える場合) |
| INTEGER |Decimal、String (有効桁数が 28 を超える場合) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal、String (有効桁数が 28 を超える場合) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| 符号なし INTEGER |NUMBER |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> INTERVAL YEAR TO MONTH データ型と INTERVAL DAY TO SECOND データ型はサポートされません。


## <a name="next-steps"></a>次の手順
Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
