---
title: Azure Data Factory を使用して MongoDB のデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、MongoDB のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
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
ms.date: 06/05/2018
ms.author: jingwang
ms.openlocfilehash: debb27f49c730df4a8bef42b1f1ef9ec50f1faf0
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054060"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Azure Data Factory を使用して MongoDB のデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [現在のバージョン](connector-mongodb.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、MongoDB データベースからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

MongoDB データベースのデータを、サポートされているシンク データ ストアにコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この MongoDB コネクタは以下をサポートします。

- MongoDB **バージョン 2.4、2.6、3.0、3.2、3.4、3.6**。
- **基本**または**匿名**認証を使用したデータのコピー。

## <a name="prerequisites"></a>前提条件

パブリックにアクセスできない MongoDB データベースからデータをコピーするには、セルフホステッド統合ランタイムを設定する必要があります。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。 Integration Runtime には MongoDB ドライバーが組み込まれているため、MongoDB からデータをコピーするときにドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、MongoDB コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

MongoDB のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |type プロパティを **MongoDb** に設定する必要があります。 |[はい] |
| [サーバー] |MongoDB サーバーの IP アドレスまたはホスト名。 |[はい] |
| ポート |MongoDB サーバーがクライアント接続のリッスンに使用する TCP ポート。 |いいえ (既定値は 27017) |
| databaseName |アクセスする MongoDB データベースの名前。 |[はい] |
| authenticationType | MongoDB データベースへの接続に使用される認証の種類です。<br/>使用できる値は **Basic** および **Anonymous** です。 |[はい] |
| username |MongoDB にアクセスするためのユーザー アカウント。 |はい (基本認証が使用される場合)。 |
| password |ユーザーのパスワード。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |はい (基本認証が使用される場合)。 |
| authSource |認証のために資格情報を確認する際に使用する MongoDB データベースの名前。 |いいえ。 基本認証の場合、既定では管理者アカウントと、databaseName プロパティで指定されたデータベースが使用されます。 |
| enableSsl | SSL を使用して、サーバーへの接続を暗号化するかどうかを指定します。 既定値は false です。  | いいえ  |
| allowSelfSignedServerCert | サーバーからの自己署名証明書を許可するかどうかを指定します。 既定値は false です。  | いいえ  |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 セルフホステッド統合ランタイムまたは Azure 統合ランタイム (データ ストアがパブリックにアクセスできる場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ  |

**例:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、MongoDB データセット でサポートされるプロパティの一覧を示します。

MongoDB からデータをコピーするには、データセットの type プロパティを **MongoDbCollection** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**MongoDbCollection** に設定する必要があります。 | [はい] |
| collectionName |MongoDB データベースのコレクション名前。 |[はい] |

**例:**

```json
{
     "name":  "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、MongoDB ソースでサポートされるプロパティの一覧を示します。

### <a name="mongodb-as-source"></a>ソースとしての MongoDB

MongoDB からデータをコピーするには、コピー アクティビティのソースの種類を **MongoDbSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **MongoDbSource** に設定する必要があります。 | [はい] |
| クエリ |カスタム SQL-92 クエリを使用してデータを読み取ります。 例: Select * from MyTable。 |いいえ (データセットに "collectionName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> SQL クエリを指定する場合は、DateTime 形式に注意してください。 たとえば、`SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` や、次のパラメーターを使用する場合 `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Data Factory によるスキーマ

Azure Data Factory サービスは、MongoDB コレクション内の**最新のドキュメント 100 個**を使用してスキーマを推論します。 この 100 個のドキュメントにスキーマが完全には含まれていない場合は、コピー操作中に無視される列が生じる可能性があります。

## <a name="data-type-mapping-for-mongodb"></a>MongoDB のデータ型マッピング

MongoDB からデータをコピーするとき、次の MongoDB のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| MongoDB のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| Binary |Byte[] |
| ブール |ブール |
| 日付 |Datetime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| String |String |
| UUID |Guid |
| オブジェクト |入れ子の区切り文字に "_" を使用してフラット化された列に再正規化されます。 |

> [!NOTE]
> 仮想テーブルを使用した配列に対するサポートの詳細については、[仮想テーブルを使用した複合型のサポート](#support-for-complex-types-using-virtual-tables)セクションをご覧ください。
>
> 現時点では、次の MongoDB データ型はサポートされていません: DBPointer、JavaScript、Max/Min key、Regular Expression、Symbol、Timestamp、Undefined。

## <a name="support-for-complex-types-using-virtual-tables"></a>仮想テーブルを使用した複合型のサポート

Azure Data Factory では、ビルトインの ODBC ドライバーを使用して、MongoDB データベースへの接続や、MongoDB データベースからのデータのコピーを行います。 複数のドキュメントのさまざまな型が含まれた配列やオブジェクトなどの複合型については、ODBC ドライバーによって、対応する仮想テーブルへのデータの再正規化が行われます。 具体的には、テーブルにそのような列が含まれている場合に、ドライバーによって次の仮想テーブルが生成されます。

* 実テーブルと同じデータ (複合型列を除く) を含む **ベース テーブル**。 ベース テーブルには、それが表す実テーブルと同じ名前が使用されます。
* 複合型列ごとの**仮想テーブル**。入れ子になったデータが展開されます。 仮想テーブルの名前は、実テーブルの名前、区切り文字の "_"、配列またはオブジェクトの名前を使用して付けられます。

仮想テーブルは、非正規化データへのドライバーのアクセスを有効にして、実テーブルのデータを参照します。  仮想テーブルのクエリや結合によって、MongoDB の配列の内容にアクセスできます。

### <a name="example"></a>例

例としてここに挙げる "ExampleTable" は、各セルにオブジェクトの配列が 1 つ含まれた列 Invoices と、スカラー型の配列が 1 つ含まれた列 Ratings で構成された MongoDB テーブルです。

| _id | Customer Name | Invoices | Service Level | Ratings |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"toaster", price:"456", discount:"0.2"}, {invoice_id:"124", item:"oven", price: "1235", discount: "0.2"}] |シルバー |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"fridge", price: "12543", discount: "0.0"}] |ゴールド |[1,2] |

ドライバーによって、この単一のテーブルを表す複数の仮想テーブルが生成されます。 最初の仮想テーブルは、次の例に示す "ExampleTable" という名前のベース テーブルです。 ベース テーブルには元のテーブルのすべてのデータが含まれますが、配列のデータは省略され、仮想テーブルで展開されます。

| _id | Customer Name | Service Level |
| --- | --- | --- |
| 1111 |ABC |シルバー |
| 2222 |XYZ |ゴールド |

次のテーブルは、例における元の配列を表す仮想テーブルを示しています。 これらのテーブルには、以下が含まれます。

* 元の配列の行に対応する元のプライマリ キー列への参照 (_Id 列を使用) 
* 元の配列内のデータの位置の指定
* 配列内の各要素の展開されたデータ

**テーブル "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Discount |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |toaster |456 |0.2 |
| 1111 |1 |124 |oven |1235 |0.2 |
| 2222 |0 |135 |fridge |12543 |0.0 |

**テーブル "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
