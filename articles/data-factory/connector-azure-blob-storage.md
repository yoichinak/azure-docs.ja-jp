---
title: Data Factory を使用して Azure Blob Storage をコピー先またはコピー元としてデータをコピーする | Microsoft Docs
description: Data Factory を使用して、サポートされるソース データ ストアから Azure Blob Storage にデータをコピーしたり、Blob Storage からサポートされるシンク ストアにコピーしたりする方法を説明します。
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: jingwang
ms.openlocfilehash: 3fdece082401ca57beabe6334a0ea0ca292ba298
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052352"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Azure Data Factory を使用した Azure Blob Storage との間でのデータのコピー
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-blob-connector.md)
> * [現在のバージョン](connector-azure-blob-storage.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Blob Storage をコピー先またはコピー元としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Blob Storage には、サポートされているソース データ ストアからデータをコピーすることができます。 サポートされているシンク データ ストアに対して、Blob Storage からデータをコピーすることもできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md)に関する記事の表をご覧ください。

具体的には、この Blob Storage コネクタは、以下をサポートします。

- 汎用 Azure Storage アカウントとホット/クール Blob Storage との間での BLOB のコピー。 
- アカウント キー認証とサービス Shared Access Signature 認証の両方を使用した BLOB のコピー。
- ブロック BLOB、アペンド BLOB、またはページ BLOB からの BLOB のコピーと、ブロック BLOB だけへのデータのコピー。 Azure Premium Storage はページ BLOB によって提供されるため、シンクとしてはサポートされていません。
- そのままの BLOB のコピー、または[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用した BLOB の解析/生成。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Blob Storage に固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

### <a name="use-an-account-key"></a>アカウント キーの使用

Storage のリンクされたサービスは、アカウント キーを使用して作成できます。 これによりデータ ファクトリは、Storage にグローバルにアクセスすることができます。 次のプロパティがサポートされています。

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

Shared Access Signature を使用して、Storage のリンクされたサービスを作成することもできます。 これによって、Data Factory は、ストレージ内のすべてまたは特定のリソース (BLOB/コンテナー) へのアクセスが制限付きまたは期限付きになります。

Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 Shared Access Signature を使用して、ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間を指定してクライアントに付与できます。 アカウントのアクセス キーを共有する必要はありません。 Shared Access Signature とは、ストレージ リソースへの認証アクセスに必要なすべての情報をクエリ パラメーター内に含む URI です。 クライアントは、Shared Access Signature 内で適切なコンストラクターまたはメソッドに渡すだけで、Shared Access Signature でストレージ リソースにアクセスできます。 Shared Access Signature の詳細については、[Shared Access Signature モデルの概要](../storage/common/storage-dotnet-shared-access-signature-part-1.md)に関するページを参照してください。

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
- URI は、必要に応じて、適切なコンテナーや BLOB またはテーブル レベルで作成する必要があります。 Data Factory から特定の BLOB にアクセスするには、その BLOB の Shared Access Signature URI を使用します。 Data Factory で Blob Storage コンテナー内の BLOB を反復処理するには、そのコンテナーの Shared Access Signature URI を使用します。 アクセスするオブジェクトの数を後で変更する場合、または Shared Access Signature URI を更新する場合は必ず、リンクされたサービスを新しい URI で更新してください。

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Blob Storage データセットでサポートされるプロパティの一覧を示します。

Blob Storage をコピー先またはコピー元としてデータをコピーするには、データセットの type プロパティを **AzureBlob** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**AzureBlob** に設定する必要があります。 |[はい] |
| folderPath | BLOB ストレージのコンテナーとフォルダーのパス。 ワイルドカード フィルターはサポートされていません。 (例: myblobcontainer/myblobfolder/)。 |[はい] |
| fileName | 指定された "folderPath" の下にある BLOB の**名前またはワイルドカード フィルター**。 このプロパティの値を指定しない場合、データセットはフォルダー内のすべての BLOB をポイントします。 <br/><br/>フィルターに使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。<br/>- 例 1: `"fileName": "*.csv"`<br/>- 例 2: `"fileName": "???20180427.txt"`<br/>実際のファイル名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。<br/><br/>出力データセットに fileName の指定がなく、アクティビティ シンクに **preserveHierarchy** の指定がない場合、コピー アクティビティは、"*Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]*" というパターンで BLOB 名を自動的に生成します。 たとえば、"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz" などです。 |いいえ  |
| format | ファイルベースのストア間でファイルをそのままコピー (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>ファイルを特定の形式で解析するか生成する場合、次のファイル形式がサポートされます。**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 **format** の **type** プロパティをいずれかの値に設定します。 詳細については、[Text 形式](supported-file-formats-and-compression-codecs.md#text-format)、[Json 形式](supported-file-formats-and-compression-codecs.md#json-format)、[Avro 形式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 形式](supported-file-formats-and-compression-codecs.md#orc-format)、[Parquet 形式](supported-file-formats-and-compression-codecs.md#parquet-format) の各セクションを参照してください。 |いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ  |

>[!TIP]
>フォルダーの下のすべての BLOB をコピーするには、**folderPath** のみを指定します。<br>特定の名前の単一の BLOB をコピーするには、フォルダー部分で **folderPath**、ファイル名で **fileName** を指定します。<br>フォルダーの下の BLOB のサブセットをコピーするには、フォルダー部分で **folderPath**、ワイルドカード フィルターで **fileName** を指定します。 

**例:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Blob Storage のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="blob-storage-as-a-source-type"></a>ソースの種類として Blob Storage を設定する

Blob Storage からデータをコピーするには、コピー アクティビティのソースの種類を **BlobSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **BlobSource** に設定する必要があります |[はい] |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定され、シンクがファイル ベースのストアである場合、空のフォルダーおよびサブフォルダーはシンクでコピーも作成もされないことに注意してください。<br/>使用可能な値: **true** (既定値) および **false**。 | いいえ  |

**例:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>シンクの種類として Blob Storage を設定する

データを Blob Storage にコピーするには、コピー アクティビティのシンクの種類を **BlobSink** に設定します。 **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは **BlobSink** に設定する必要があります。 |[はい] |
| copyBehavior | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>: ファイル階層をターゲット フォルダー内で保持します。 ソース フォルダーに対するソース ファイルの相対パスと、ターゲット フォルダーに対するターゲット ファイルの相対パスが一致します。<br/><b>- FlattenHierarchy</b>: ソース フォルダーのすべてのファイルがターゲット フォルダーの第一レベルに配置されます。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>: ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイルまたは BLOB の名前を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 | いいえ  |

**例:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="some-recursive-and-copybehavior-examples"></a>recursive と copyBehavior の例

このセクションでは、recursive 値と copyBhavior 値の組み合わせごとに、Copy 操作で行われる動作について説明します。

| recursive | copyBehavior | ソースのフォルダー構造 | ターゲットの結果 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、ソースと同じ構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 の自動生成された名前 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2、File3、File4、File5 の内容は 1 つのファイルにマージされて、自動生成されたファイル名が付けられます。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2 の内容は 1 つのファイルにマージされ、自動生成されたファイル名が付けられます。 File1 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |

## <a name="next-steps"></a>次の手順
Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
