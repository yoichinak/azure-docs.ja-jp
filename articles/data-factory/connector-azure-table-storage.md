---
title: Data Factory を使用して Azure Table Storage をコピー先またはコピー元としてデータをコピーする | Microsoft Docs
description: Data Factory を使用して、サポートされるソース ストアから Azure Table Storage にデータをコピーしたり、Table Storage からサポートされるシンク ストアにコピーしたりする方法を説明します。
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
ms.openlocfilehash: 24954cfc128834313bf13a1917e67d5c1812cf66
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37055529"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Azure Data Factory を使用した Azure Table Storage との間でのデータのコピー
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-table-connector.md)
> * [現在のバージョン](connector-azure-table-storage.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Table Storage をコピー先またはコピー元としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Table Storage には、サポートされているソース データ ストアからデータをコピーすることができます。 サポートされているシンク データ ストアに対して、Table Storage からデータをコピーすることもできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Azure Table コネクタは、アカウント キーとサービスの Shared Access Signature 認証を使用して、データのコピーをサポートします。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Table Storage に固有の Data Factory エンティティの定義に使用されるプロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

### <a name="use-an-account-key"></a>アカウント キーの使用

Azure Storage のリンクされたサービスは、アカウント キーを使用して作成できます。 これによりデータ ファクトリは、Storage にグローバルにアクセスすることができます。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **AzureStorage** に設定する必要があります。 |[はい] |
| connectionString | connectionString プロパティのために Storage に接続するために必要な情報を指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |[はい] |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ  |

**例:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-service-shared-access-signature-authentication"></a>サービスの Shared Access Signature 認証を使用する

Shared Access Signature を使用して、Storage のリンクされたサービスを作成することもできます。 これによって、Data Factory は、ストレージ内のすべてまたは特定のリソースへのアクセスが制限付きまたは期限付きになります。

Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間とアクセス許可セットを指定してクライアントに付与できます。 アカウントのアクセス キーを共有する必要はありません。 Shared Access Signature とは、ストレージ リソースへの認証アクセスに必要なすべての情報をクエリ パラメーター内に含む URI です。 クライアントは、Shared Access Signature 内で適切なコンストラクターまたはメソッドに渡すだけで、Shared Access Signature でストレージ リソースにアクセスできます。 Shared Access Signature の詳細については、[Shared Access Signature モデルの概要](../storage/common/storage-dotnet-shared-access-signature-part-1.md)に関するページを参照してください。

> [!NOTE]
> Data Factory で、サービスの Shared Access Signature とアカウントの Shared Access Signature がサポートされるようになりました。 この 2 種類の SAS とその作成方法について詳しくは、「[共有アクセス署名の種類](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures)」を参照してください。 

> [!TIP]
> ストレージ アカウントに使用するサービスの Shared Access Signature を生成するには、次の PowerShell コマンドを実行します。 プレースホルダーを置き換えたうえで、必要なアクセス許可を付与してください。
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

サービスの Shared Access Signature 認証の使用には、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **AzureStorage** に設定する必要があります。 |[はい] |
| sasUri | BLOB、コンテナー、テーブルなどの Storage リソースへの Shared Access Signature URI を指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |[はい] |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ  |

**例:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Shared Access Signature の URI を作成する際は、次の点に注意してください。

- リンクされたサービス (読み取り、書き込み、読み取り/書き込み) がデータ ファクトリ内でどのように使用されているかに応じて、オブジェクトに対する適切な読み取り/書き込みアクセス許可を設定します。
- **有効期限**を適切に設定します。 Storage オブジェクトへのアクセスがパイプラインのアクティブな期間内に期限切れにならないことを確認します。
- URI は、必要に応じて、適切なテーブル レベルで作成する必要があります。

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Azure Table データセットでサポートされるプロパティの一覧を示します。

Azure Table をコピー先またはコピー元としてデータをコピーするには、データセットの type プロパティを **AzureTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**AzureTable** に設定する必要があります。 |[はい] |
| tableName |リンクされたサービスが参照する Table Storage データベース インスタンスのテーブルの名前です。 |[はい] |

**例:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Data Factory によるスキーマ

Azure Table などのスキーマのないデータ ストアの場合、Data Factory は次のいずれかの方法でスキーマを推論します。

* データセット定義で **structure** プロパティを使用してデータの構造を指定した場合、Data Factory はスキーマとしてこの構造を優先します。 この場合、行に列の値が含まれていないと、null 値が指定されます。
* データセット定義で **structure** プロパティを使用してデータの構造を指定しなかった場合、Data Factory はデータの最初の行を使用してスキーマを推論します。 この場合、最初の行に完全なスキーマが含まれていないと、コピー操作の結果で一部の列が欠落します。

スキーマのないデータ ソースでは、**structure** プロパティを使用してデータの構造を指定するのがベスト プラクティスです。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure Table のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-table-as-a-source-type"></a>ソースの種類として Azure Table を設定する

Azure Table からデータをコピーする場合は、コピー アクティビティのソースの種類を **AzureTableSource** を設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **AzureTableSource** に設定する必要があります。 |[はい] |
| AzureTableSourceQuery |カスタム Table Storage クエリを使用してデータを読み取ります。 次のセクションの例を参照してください。 |いいえ  |
| azureTableSourceIgnoreTableNotFound |テーブルが存在しないという例外を受け入れるかどうかを示します。<br/>使用可能な値: **True**、および **False** (既定値)。 |いいえ  |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery の例

Azure Table の列が datetime 型の場合:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Azure Table の列が string 型の場合:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

パイプラインのパラメーターを使用している場合は、前のサンプルに従って、datetime 値を適切なフォーマットにキャストします。

### <a name="azure-table-as-a-sink-type"></a>シンクの種類として Azure Table を設定する

Azure Table にデータをコピーする場合は、コピー アクティビティのシンクの種類を **AzureTableSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティを **AzureTableSink** に設定する必要があります。 |[はい] |
| azureTableDefaultPartitionKeyValue |シンクで使用できる既定のパーティション キー値です。 |いいえ  |
| azureTablePartitionKeyName |値をパーティション キーとして使用する列の名前を指定します。 指定しない場合、AzureTableDefaultPartitionKeyValue がパーティション キーとして使用されます。 |いいえ  |
| azureTableRowKeyName |値を行キーとして使用する列の名前を指定します。 指定しない場合、各行に GUID を使用します。 |いいえ  |
| azureTableInsertType |Azure Table にデータを挿入する方法です。 このプロパティは、一致するパーティションと列キーを持つ出力テーブル内の既存の行で、値を置換するか結合するかを制御します。 <br/><br/>使用可能な値: **マージ** (既定値) および**置換**。 <br/><br> この設定は、テーブル レベルではなく、行レベルで適用されます。 どちらのオプションでも、出力テーブル内の、入力内に存在しない行は削除されません。 結合と置換の設定の機能については、「[Insert or Merge Entity (エンティティの挿入または結合)](https://msdn.microsoft.com/library/azure/hh452241.aspx)」および「[Insert or Replace Entity (エンティティの挿入または置換)](https://msdn.microsoft.com/library/azure/hh452242.aspx)」をご覧ください。 |いいえ  |
| writeBatchSize |writeBatchSize または writeBatchTimeout に達したときに、Azure Table にデータを挿入します。<br/>使用可能な値: 整数 (行数)。 |いいえ (既定値は 10,000) |
| writeBatchTimeout |writeBatchSize または writeBatchTimeout に達したときに、Azure Table にデータを挿入します。<br/>使用可能な値: 期間。 たとえば "00:20:00" (20 分) を指定できます。 |No (既定値は 90 秒 - ストレージ クライアントの既定のタイムアウト値) |

**例:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

azureTablePartitionKeyName として宛先列を使用する前に、**"translator"** プロパティを使用して、ソース列を宛先列にマップします。

次の例では、ソース列の DivisionID が宛先列の DivisionID にマップされます。

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" は、パーティション キーとして指定されます。

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Azure Table のデータ型のマッピング

Azure Table をコピー元またはコピー先としてデータをコピーするとき、次の Azure Table のデータ型から Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

Azure テーブル間でデータの移動時に、次の [Azure Table により定義されたマッピング](https://msdn.microsoft.com/library/azure/dd179338.aspx)が Azure Table の OData 型と .NET 型との間の移動に利用されます。

| Azure Table のデータ型 | Data Factory の中間データ型 | 詳細 |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |バイトの配列 (最大 64 KB)。 |
| Edm.Boolean |bool |ブール値。 |
| Edm.DateTime |Datetime |世界協定時刻 (UTC) を表す 64 ビット値。 サポートされている DateTime 範囲は西暦 1601 年 1 月 1 日 UTC 深夜 12:00 から 始まります。 この範囲は 9999 年 12 月 31 日に終了します。 |
| Edm.Double |double |64 ビットの浮動小数点値。 |
| Edm.Guid |Guid |グローバルで一意となる 128 ビットの識別子。 |
| Edm.Int32 |Int32 |32 ビットの整数。 |
| Edm.Int64 |Int64 |64 ビットの整数。 |
| Edm.String |String |UTF-16 エンコードの値。 文字列値は最大 64 KB になります。 |

## <a name="next-steps"></a>次の手順
Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
