---
title: Azure Data Factory を使用して Azure SQL Data Warehouse との間でデータをコピーする | Microsoft Docs
description: Data Factory を使用して、サポートされるソース ストアのデータを Azure SQL Data Warehouse にコピーしたり、Azure SQL Data Warehouse のデータをサポートされるシンク ストアにコピーしたりできます。
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
ms.date: 07/28/2018
ms.author: jingwang
ms.openlocfilehash: 7a9adc8e9b7bcf69cce6b8ecf00e44477c1b0da3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430741"
---
#  <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure SQL Data Warehouse をコピー先またはコピー元としてデータをコピーする 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you're using:"]
> * [バージョン 1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [現在のバージョン](connector-azure-sql-data-warehouse.md)

この記事では、Azure Data Factory のコピー アクティビティを使って Azure SQL Data Warehouse との間でデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Azure SQL Data Warehouse のデータを、サポートされる任意のシンク データ ストアにコピーできます。 また、Azure SQL Data Warehouse には、サポートされているソース データ ストアからデータをコピーすることができます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表を参照してください。

具体的には、この Azure SQL Data Warehouse コネクタは以下の機能をサポートします。

- SQL 認証を使って、およびサービス プリンシパルまたはマネージド サービス ID (MSI) で Azure Active Directory (Azure AD) アプリケーション トークン認証を使って、データをコピーする。
- ソースとして、SQL クエリまたはストアド プロシージャを使用してデータを取得する。
- シンクとして、PolyBase または一括挿入を使用してデータを読み込む。 コピーのパフォーマンスがよいので、PolyBase をお勧めします。

> [!IMPORTANT]
> PolyBase は SQL 認証のみをサポートし、Azure AD 認証はサポートしないので注意してください。

> [!IMPORTANT]
> Azure Data Factory Integration Runtime を使ってデータをコピーする場合は、Azure サービスがサーバーにアクセスできるように [Azure SQL Server ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)を構成します。
> セルフホステッド IR を使用してデータをコピーする場合は、適切な IP 範囲を許可するように Azure SQL Server ファイアウォールを構成します。 この範囲には、Azure SQL Database への接続に使用されるコンピューターの IP アドレスが含まれています。

## <a name="get-started"></a>作業開始

> [!TIP]
> 最高のパフォーマンスを実現するには、PolyBase を使用して、Azure SQL Data Warehouse にデータを読み込みます。 「 [PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 」セクションに詳細が記載されています。 ユース ケースを使用したチュートリアルについては、[1 TB のデータを Azure Data Factory を使用して 15 分以内に Azure SQL Data Warehouse に読み込む方法](load-azure-sql-data-warehouse.md)に関するページを参照してください。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure SQL Data Warehouse コネクタに固有の Data Factory エンティティを定義するプロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure SQL Data Warehouse のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **AzureSqlDW** に設定する必要があります。 | [はい] |
| connectionString | **connectionString** プロパティには、Azure SQL Data Warehouse インスタンスに接続するために必要な情報を指定します。 このフィールドを **SecureString** としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | [はい] |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| servicePrincipalKey | アプリケーションのキーを取得します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure Portal の右上隅にマウスを置くことで取得できます。 | サービス プリンシパルで Azure AD 認証を使う場合は、はい。 |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure Integration Runtime またはセルフホステッド IR を使用できます (データ ストアがプライベート ネットワークにある場合)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ  |

さまざまな認証の種類の前提条件と JSON サンプルについては、以下のセクションをご覧ください。

- [SQL 認証](#sql-authentication)
- Azure AD アプリケーション トークン認証: [サービス プリンシパル](#service-principal-authentication)
- Azure AD アプリケーション トークン認証: [マネージド サービス ID](#managed-service-identity-authentication)

### <a name="sql-authentication"></a>SQL 認証

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL 認証を使用するリンクされたサービスの例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>サービス プリンシパルの認証

サービス プリンシパル ベースの Azure AD アプリケーション トークン認証を使うには、以下の手順のようにします。

1. Azure portal から **[Azure Active Directory アプリケーションを作成します](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)**。 アプリケーション名と、リンクされたサービスを定義する次の値を記録しておきます。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

1. まだ行っていない場合は、Azure portal で Azure SQL Server の **[Azure Active Directory 管理者をプロビジョニングします](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**。 Azure AD 管理者には、Azure AD ユーザーまたは Azure AD グループを使用できます。 MSI を持つグループに管理者ロールを付与する場合は、手順 3 および 4 をスキップします。 管理者には、データベースへのフル アクセスがあります。

1. サービス プリンシパルの**[包含データベース ユーザーを作成します](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**。 SSMS のようなツールと、少なくとも ALTER ANY USER アクセス許可を持つ Azure AD ID を使用して、データをコピーするデータ ウェアハウスに接続します。 次の T-SQL を実行します。
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

1. SQL ユーザーや他のユーザーに対する通常の方法で、**サービス プリンシパルに必要なアクセス許可を付与**します。 次のコードを実行します。

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

1. Azure Data Factory で、**Azure SQL Data Warehouse のリンクされたサービスを構成します**。


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>サービス プリンシパル認証を使うリンクされたサービスの例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-service-identity-authentication"></a>マネージド サービス ID 認証

データ ファクトリは、特定のファクトリを表す[マネージド サービス ID](data-factory-service-identity.md) に関連付けることができます。 このサービス ID を Azure SQL Data Warehouse 認証に使用できます。 指定されたファクトリは、この ID を使用してデータ ウェアハウスにアクセスし、データを双方向にコピーできます。

> [!IMPORTANT]
> 現在、PolyBase は MSI 認証に対してサポートされていないことに注意してください。

MSI ベースの Azure AD アプリケーション トークン認証を使うには、以下の手順のようにします。

1. **Azure AD でグループを作成します。** ファクトリ MSI をグループのメンバーにします。

    a. Azure portal でデータ ファクトリのサービス ID を調べます。 データ ファクトリの **[プロパティ]** に移動します。 サービス ID をコピーします。

    b. [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) モジュールをインストールします。 `Connect-AzureAD` コマンドを使用してサインインします。 次のコマンドを実行してグループを作成し、データ ファクトリ MSI をメンバーとして追加します。
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

1. まだ行っていない場合は、Azure portal で Azure SQL Server の **[Azure Active Directory 管理者をプロビジョニングします](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**。

1. Azure AD グループの**[包含データベース ユーザーを作成します](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**。 SSMS のようなツールと、少なくとも ALTER ANY USER アクセス許可を持つ Azure AD ID を使用して、データをコピーするデータ ウェアハウスに接続します。 次の T-SQL を実行します。 
    
    ```sql
    CREATE USER [your Azure AD group name] FROM EXTERNAL PROVIDER;
    ```

1. SQL ユーザーや他のユーザーに対する通常の方法で、**Azure AD グループに必要なアクセス許可を付与します**。 たとえば、次のコードを実行します。

    ```sql
    EXEC sp_addrolemember [role name], [your Azure AD group name];
    ```

1. Azure Data Factory で、**Azure SQL Data Warehouse のリンクされたサービスを構成します**。

#### <a name="linked-service-example-that-uses-msi-authentication"></a>MSI 認証を使用するリンクされたサービスの例

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services)に関する記事をご覧ください。 このセクションでは、Azure SQL Data Warehouse データセットでサポートされるプロパティの一覧を示します。

Azure SQL Data Warehouse をコピー元またはコピー先としてデータをコピーするには、データセットの **type** プロパティを **AzureSqlDWTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティは、**AzureSqlDWTable** を設定する必要があります。 | [はい] |
| tableName | リンクされたサービスが参照する Azure SQL Data Warehouse インスタンスのテーブルまたはビューの名前。 | [はい] |

#### <a name="dataset-properties-example"></a>データセットのプロパティの例

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure SQL Data Warehouse のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-sql-data-warehouse-as-the-source"></a>ソースとしての Azure SQL Data Warehouse

Azure SQL Data Warehouse からデータをコピーする場合は、コピー アクティビティのソースで **type** プロパティを **SqlDWSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティは **SqlDWSource** に設定する必要があります。 | [はい] |
| SqlReaderQuery | カスタム SQL クエリを使用してデータを読み取ります。 例: `select * from MyTable`. | いいえ  |
| sqlReaderStoredProcedureName | ソース テーブルからデータを読み取るストアド プロシージャの名前。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 | いいえ  |
| storedProcedureParameters | ストアド プロシージャのパラメーター。<br/>使用可能な値は、名前と値のペアです。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 | いいえ  |

### <a name="points-to-note"></a>注意する点

- **SqlSource** に **sqlReaderQuery** が指定されている場合、コピー アクティビティは、Azure SQL Data Warehouse ソースに対してこのクエリを実行してデータを取得します。 または、ストアド プロシージャを指定できます。 ストアド プロシージャがパラメーターを受け取る場合は、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定します。
- **sqlReaderQuery** または **sqlReaderStoredProcedureName** を指定しない場合は、データセット JSON の **structure** セクションで定義されている列を使用して、クエリが作成されます。 `select column1, column2 from mytable` が Azure SQL Data Warehouse に対して実行されます。 データセット定義に **structure** がない場合は、すべての列がテーブルから選択されます。
- **sqlReaderStoredProcedureName** を使用する場合でも、ダミーの **tableName** プロパティをデータセット JSON に指定する必要があります。

#### <a name="sql-query-example"></a>SQL クエリの例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>ストアド プロシージャの例

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>ストアド プロシージャの定義

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-data-warehouse-as-sink"></a> シンクとしての Azure SQL Data Warehouse

Azure SQL Data Warehouse にデータをコピーする場合は、コピー アクティビティのシンクの種類を **SqlDWSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの **type** プロパティは、**SqlDWSink** に設定する必要があります。 | [はい] |
| allowPolyBase | BULKINSERT メカニズムではなく PolyBase (該当する場合) を使用するかどうかを示します。 <br/><br/> SQL Data Warehouse へのデータの読み込みには、PolyBase を使うことをお勧めします。 制約と詳細については、「[PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む](#use-polybase-to-load-data-into-azure-sql-data-warehouse)」をご覧ください。<br/><br/>使用可能な値: **True**、および **False** (既定値)。  | いいえ  |
| polyBaseSettings | **allowPolybase** プロパティが **true** に設定されているときに指定できるプロパティのグループ。 | いいえ  |
| rejectValue | クエリが失敗するまでに拒否できる行の数または割合を指定します。<br/><br/>PolyBase の拒否オプションについて詳しくは、「[CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx)」の「引数」セクションをご覧ください。 <br/><br/>使用可能な値は、0 (既定値)、1、2 などです。 |いいえ  |
| rejectType | **rejectValue** オプションがリテラル値か割合かを指定します。<br/><br/>使用可能な値は、**Value** (既定値) と **Percentage** です。 | いいえ  |
| rejectSampleValue | 拒否された行の割合が PolyBase で再計算されるまでに取得する行数を決定します。<br/><br/>使用可能な値は、1、2 などです。 | はい (**rejectType** が **percentage** の場合)。 |
| useTypeDefault | PolyBase がテキスト ファイルからデータを取得する場合にどのように区切りテキスト ファイル内の不足値を処理するかを、指定します。<br/><br/>このプロパティの詳細については、[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx) Arguments セクションをご覧ください。<br/><br/>使用可能な値: **True**、および **False** (既定値)。 | いいえ  |
| writeBatchSize | バッファー サイズが **writeBatchSize** に達したら、SQL テーブルにデータを挿入します。 PolyBase が使われていない場合にのみ適用されます。<br/><br/>使用可能な値は **integer** (行数) です。 | いいえ。 既定値は 10000 です。 |
| writeBatchTimeout | タイムアウトする前に一括挿入操作の完了を待つ時間です。PolyBase が使われていない場合にのみ適用されます。<br/><br/>使用可能な値は **timespan** です。 例: "00:30:00" (30 分)。 | いいえ  |
| preCopyScript | コピー アクティビティの毎回の実行で、データを Azure SQL Data Warehouse に書き込む前に実行する SQL クエリを指定します。 前に読み込まれたデータをクリーンアップするには、このプロパティを使います。 | いいえ  | (#repeatability-during-copy)。 | クエリ ステートメント。 | いいえ  |

#### <a name="sql-data-warehouse-sink-example"></a>SQL Data Warehouse のシンクの例

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

次のセクションでは、PolyBase を使用して SQL Data Warehouse への読み込みを効率的に実行する方法について詳しく説明します。

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) を使用すると、高いスループットで Azure SQL Data Warehouse に大量のデータを効率的に読み込むことができます。 既定の BULKINSERT メカニズムではなく PolyBase を使用することで、スループットが大幅に向上することがわかります。 詳細な比較については、「[パフォーマンス リファレンス](copy-activity-performance.md#performance-reference)」をご覧ください。 ユース ケースを使用したチュートリアルについては、[1 TB のデータを Azure SQL Data Warehouse に読み込む方法](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-load-sql-data-warehouse)に関するページをご覧ください。

* ソース データが Azure BLOB ストレージまたは Azure Data Lake Store 内にあり、PolyBase と互換性のある形式の場合は、PolyBase を使用して Azure SQL Data Warehouse に直接コピーします。 詳しくは、「**[PolyBase を使用して直接コピーする](#direct-copy-by-using-polybase)**」をご覧ください。
* ソース データのストアと形式が、本来は PolyBase でサポートされていない形式の場合は、代わりに **[PolyBase を使用したステージング コピー](#staged-copy-by-using-polybase)** を使います。 ステージング コピー機能はスループットも優れています。 PolyBase と互換性のある形式にデータを自動的に変換します。 そして、Azure BLOB ストレージにデータを格納します。 その後、データは SQL Data Warehouse に読み込まれます。

> [!IMPORTANT]
> 現在、PolyBase は MSI ベースの Azure AD アプリケーション トークン認証に対してサポートされていないことに注意してください。

### <a name="direct-copy-by-using-polybase"></a>PolyBase を使用して直接コピーする

SQL Data Warehouse の PolyBase は、Azure BLOB と Azure Data Lake Store を直接サポートします。 ソースとしてサービス プリンシパルを使用し、特定のファイル形式の要件があります。 ソース データがこのセクションで説明する条件を満たす場合は、PolyBase を使用してソース データ ストアから Azure SQL Data Warehouse に直接コピーしてください。 それ以外の場合は、[PolyBase を使用したステージング コピー](#staged-copy-by-using-polybase)を使います。

> [!TIP]
> データを効率的に Data Lake Store から SQL Data Warehouse にコピーするには、「[Azure Data Factory makes it even easier and convenient to uncover insights from data when using Data Lake Store with SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)」(Azure Data Factory を利用すれば、SQL Data Warehouse と共に Data Lake Store を使用する場合にデータからさらに容易かつ便利に情報を引き出せるようになる) を参考にしてください。

要件が満たされない場合は、Azure Data Factory が設定を確認し、データ移動には自動的に BULKINSERT メカニズムが使用されるように戻ります。

1. **ソースのリンクされたサービス**の type が、**AzureStorage** またはサービス プリンシパル認証を使用する **AzureDataLakeStore** であること。
1. **入力データセット**の type が、**AzureBlob** または **AzureDataLakeStoreFile** であること。 `type` プロパティの形式の種類が、**OrcFormat**、**ParquetFormat**、または **TextFormat** であり、次のような構成であること。

   1. `rowDelimiter` が **\n** である。
   1. `nullValue` が**空の文字列** ("") に設定されているか、既定のままになっていて、`treatEmptyAsNull` が false に設定されていない。
   1. `encodingName` が **utf-8** に設定されている。これは既定値です。
   1. `escapeChar`、`quoteChar`、および `skipLineCount` が指定されていない。 PolyBase では、ヘッダー行のスキップがサポートされます。これは、ADF で `firstRowAsHeader` として構成できます。
   1. `compression` が **圧縮なし**、**GZip**、または **Deflate**である。

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8",
           "firstRowAsHeader": <any>
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>PolyBase を使用したステージング コピー

ソース データが前のセクションの条件を満たしていない場合は、中間ステージング Azure BLOB ストレージ インスタンス経由でのデータのコピーを有効にします。 Azure Premium Storage は使用できません。 その場合、Azure Data Factory は、PolyBase のデータ形式要件を満たすように、データの変換を自動的に実行します。 その後、PolyBase を使用してデータを SQL Data Warehouse に読み込みます。 最後に、BLOB ストレージから一時データをクリーンアップします。 ステージング Azure BLOB ストレージ インスタンス経由でのデータのコピーについて詳しくは、「[ステージング コピー](copy-activity-performance.md#staged-copy)」をご覧ください。

この機能を使うには、中間 BLOB ストレージを含む Azure ストレージ アカウントを参照する [Azure Storage のリンクされたサービス](connector-azure-blob-storage.md#linked-service-properties)を作成します。 その後、次のコードで示すように、コピー アクティビティの `enableStaging` プロパティと `stagingSettings` プロパティを指定します。

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-for-using-polybase"></a>PolyBase の使用に関するベスト プラクティス

次のセクションでは、「[Azure SQL Data Warehouse のベスト プラクティス](../sql-data-warehouse/sql-data-warehouse-best-practices.md)」に記載されているもの以外のベスト プラクティスを説明します。

### <a name="required-database-permission"></a>必要なデータベース アクセス許可

PolyBase を使うには、データを SQL Data Warehouse に読み込むユーザーが、ターゲット データベースでの ["CONTROL" アクセス許可](https://msdn.microsoft.com/library/ms191291.aspx)を持っている必要があります。 これを実現する方法の 1 つは、ユーザーを **db_owner** ロールのメンバーとして追加することです。 方法については、[SQL Data Warehouse の概要](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)に関するページをご覧ください。

### <a name="row-size-and-data-type-limits"></a>行のサイズとデータ型の制限

PolyBase の読み込みは、1 MB 未満の行に制限されます。 VARCHR(MAX)、NVARCHAR(MAX)、または VARBINARY(MAX) を読み込むことはできません。 詳しくは、[SQL Data Warehouse サービスの容量制限](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)に関するページをご覧ください。

ソース データに 1 MB を超える行がある場合は、ソース テーブルを複数の小さいテーブルに垂直分割できます。 各行の最大サイズが制限を超えないことを確認します。 その後、この分割した小さいテーブルは、PolyBase を使用して Azure SQL Data Warehouse に読み込み、マージすることができます。

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse リソース クラス

可能な限りスループットを最大化するには、PolyBase で SQL Data Warehouse にデータを読み込むユーザーに、より大きなリソース クラスを割り当てます。

### <a name="tablename-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse での **tableName**

次の表は、JSON データセットで **tableName** プロパティを指定する方法の例です。 スキーマとテーブル名の複数の組み合わせを示します。

| DB スキーマ | テーブル名 | **tableName** JSON プロパティ |
| --- | --- | --- |
| dbo | MyTable | MyTable、dbo.MyTable、または [dbo].[MyTable] |
| dbo1 | MyTable | dbo1.MyTable または [dbo1].[MyTable] |
| dbo | My.Table | [My.Table] または [dbo].[My.Table] |
| dbo1 | My.Table | [dbo1].[My.Table] |

次のエラーが表示される場合は、**tableName** プロパティに指定した値に問題がある可能性があります。 **tableName** JSON プロパティの値を指定する正しい方法については、上の表を参照してください。

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>既定値を持つ列

現在、Data Factory の PolyBase 機能では、ターゲット テーブルと同じ数の列のみを使用できます。 たとえば、4 つの列を含むテーブルがあり、その列の 1 つには既定値が定義されているものとします。 それでも入力データには 4 つの列が必要です。 入力データセットが 3 列の場合は、次のメッセージのようなエラーが発生します。

```
All columns of the table must be specified in the INSERT BULK statement.
```

null 値は、特殊な形式の既定値です。 列が null 許容の場合、その列に対する BLOB 内の入力データが空になる可能性があります。 ただし、入力データセットから欠落することはできません。 PolyBase は、欠落している値に対して null を Azure SQL Data Warehouse に挿入します。

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse のデータ型のマッピング

Azure SQL Data Warehouse をコピー元またはコピー先としてデータをコピーするとき、次の Azure SQL Data Warehouse のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Azure SQL Data Warehouse のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| bigint | Int64 |
| binary | Byte[] |
| ビット | Boolean |
| char | String、Char[] |
| date | Datetime |
| DateTime | Datetime |
| datetime2 | Datetime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| FILESTREAM 属性 (varbinary(max)) | Byte[] |
| Float | Double |
| image | Byte[] |
| int | Int32 |
| money | Decimal |
| nchar | String、Char[] |
| ntext | String、Char[] |
| 数値 | Decimal |
| nvarchar | String、Char[] |
| real | Single |
| rowversion | Byte[] |
| smalldatetime | Datetime |
| smallint | Int16 |
| smallmoney | Decimal |
| sql_variant | Object * |
| text | String、Char[] |
| time | timespan |
| timestamp | Byte[] |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte[] |
| varchar | String、Char[] |
| xml | xml |

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストアと形式](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
