---
title: Azure Data Factory を使用して DB2 のデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、DB2 のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
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
ms.date: 03/28/2018
ms.author: jingwang
ms.openlocfilehash: 11647c231db3ff7beb2fed641dc72ff339b2b45a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046243"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Azure Data Factory を使用して DB2 からデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-onprem-db2-connector.md)
> * [最新バージョン](connector-db2.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、DB2 データベースからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

DB2 データベースのデータを、サポートされているシンク データ ストアにコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この DB2 コネクタは、分散型リレーショナル データベース アーキテクチャ (DRDA) SQL アクセス マネージャー (SQLAM) バージョン 9、10、および 11 に対応した、次の IBM DB2 のプラットフォームとバージョンをサポートしています。

* IBM DB2 for z/OS 11.1
* IBM DB2 for z/OS 10.1
* IBM DB2 for i 7.2
* IBM DB2 for i 7.1
* IBM DB2 for LUW 11
* IBM DB2 for LUW 10.5
* IBM DB2 for LUW 10.1

> [!TIP]
> "SQL ステートメント実行要求に対応するパッケージが見つかりませんでした。 SQLSTATE=51002 SQLCODE=-805" というエラー メッセージは、その OS で通常のユーザー用に必要なパッケージが作成されなかったために表示されます。 DB2 サーバーの種類に応じて、次の手順に従います。
> - DB2 for i (AS400): コピー アクティビティを使用する前に、パワー ユーザーにログイン ユーザー用のコレクションを作成できるようにします。 コマンド: `create collection <username>`
> - DB2 for Z/OS または LUW: 高い特権を持つアカウント (パッケージの権限および BIND、BINDADD、GRANT EXECUTE TO PUBLIC アクセス許可を持つパワー ユーザーまたは管理者) を使用して、コピー アクティビティを 1 回実行します。コピー中に、必要なパッケージが自動的に作成されます。 その後、通常のユーザーに切り替えて後続のコピー操作を実行することができます。

## <a name="prerequisites"></a>前提条件

パブリックにアクセスできない DB2 データベースからコピーしたデータを使用するには、セルフホステッド統合ランタイムを設定する必要があります。 セルフホステッド統合ランタイムの詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事を参照してください。 統合ランタイムには DB2 ドライバーが組み込まれているため、DB2 からデータをコピーするときにドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、DB2 コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

DB2 のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **Db2** に設定する必要があります。 | [はい] |
| [サーバー] |DB2 サーバーの名前です。 |[はい] |
| [データベース] |DB2 データベースの名前です。 |[はい] |
| authenticationType |DB2 データベースへの接続に使用される認証の種類です。<br/>使用可能な値: **Basic**。 |[はい] |
| username |DB2 データベースに接続するユーザー名を指定します。 |[はい] |
| password |ユーザー名に指定したユーザー アカウントのパスワードを指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |[はい] |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 セルフホステッド統合ランタイムまたは Azure 統合ランタイム (データ ストアがパブリックにアクセスできる場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ  |

**例:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername>",
            "database": "<dbname>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、DB2 データセット でサポートされるプロパティの一覧を示します。

DB2 からデータをコピーするには、データセットの type プロパティを **RelationalTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは **RelationalTable** に設定する必要があります。 | [はい] |
| tableName | DB2 データベースのテーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、DB2 ソースでサポートされるプロパティの一覧を示します。

### <a name="db2-as-source"></a>ソースとしての DB2

DB2 からデータをコピーするには、コピー アクティビティのソースの種類を **RelationalSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **RelationalSource** に設定する必要があります。 | [はい] |
| クエリ | カスタム SQL クエリを使用してデータを読み取ります。 たとえば、「 `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`」のように入力します。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-db2"></a>DB2 のデータ型のマッピング

DB2 からデータをコピーするとき、次の DB2 のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| DB2 データベース型 | Data Factory の中間データ型 |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| BLOB |Byte[] |
| Char |String |
| Clob |String |
| 日付 |DateTime |
| DB2DynArray |String |
| DbClob |String |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |String |
| 整数 |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |String |
| LongVarGraphic |String |
| Numeric |Decimal |
| Real |Single |
| SmallInt |Int16 |
| Time |timespan |
| Timestamp |Datetime |
| VarBinary |Byte[] |
| VarChar |String |
| VarGraphic |String |
| xml |Byte[] |


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
