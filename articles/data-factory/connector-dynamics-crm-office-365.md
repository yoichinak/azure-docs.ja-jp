---
title: Azure Data Factory を使用して Dynamics CRM または Dynamics 365 (Common Data Service) をコピー元またはコピー先としてデータをコピーする | Microsoft Docs
description: Data Factory パイプラインでコピー アクティビティを使用して、Microsoft Dynamics CRM または Microsoft Dynamics 365 (Common Data Service) からサポートされているシンク データ ストアに、またはサポートされているソース データ ストアから Dynamics CRM または Dynamics 365 にデータをコピーする方法について説明します。
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
ms.date: 05/02/2018
ms.author: jingwang
ms.openlocfilehash: 3f45f9337a5522f490c268bbdae3ef1a41205175
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859376"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Azure Data Factory を使用して Dynamics 365 (Common Data Service) または Dynamics CRM をコピー元またはコピー先としてデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、Microsoft Dynamics 365 または Microsoft Dynamics CRM をコピー元またはコピー先としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Dynamics 365 (Common Data Service) または Dynamics CRM から、サポートされている任意のシンク データ ストアにデータをコピーできます。 サポートされている任意のソース データ ストアから Dynamics 365 (Common Data Service) または Dynamics CRM にデータをコピーすることもできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページの表をご覧ください。

この Dynamics コネクタは、次の Dynamics のバージョンと認証の種類をサポートします  (IFD は Internet Facing Deployment の省略形です)。

| Dynamics のバージョン | 認証の種類 | リンクされたサービスの例 |
|:--- |:--- |:--- |
| Dynamics 365 Online <br> Dynamics CRM Online | Office365 | [Dynamics Online + Office365 認証](#dynamics-365-and-dynamics-crm-online) |
| IFD 対応オンプレミス Dynamics 365 <br> IFD 対応オンプレミス Dynamics CRM 2016 <br> IFD 対応オンプレミス Dynamics CRM 2015 | IFD | [IFD + IFD 認証対応オンプレミス Dynamics](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

具体的には、Dynamics 365 では、次のアプリケーションの種類がサポートされます。

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

その他のアプリケーションの種類 (運用、金融、タレントなど) はサポートされません。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Dynamics に固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Dynamics のリンクされたサービスでは、次のプロパティがサポートされます。

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 および Dynamics CRM Online

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **Dynamics** に設定する必要があります。 | [はい] |
| deploymentType | Dynamics インスタンスの展開の種類。 Dynamics Online を **"Online"** にする必要があります。 | [はい] |
| serviceUri | Dynamics インスタンスのサービス URL (例: `https://adfdynamics.crm.dynamics.com`)。 | [はい] |
| authenticationType | Dynamics サーバーに接続する認証の種類。 Dynamics Online を **"Office365"** に指定します。 | [はい] |
| username | Dynamics に接続するためのユーザー名を指定します。 | [はい] |
| password | username に指定したユーザー アカウントのパスワードを指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | [はい] |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | ソースの場合は「いいえ」、シンクの場合は「はい」 (ソースにリンクされたサービスに統合ランタイムがない場合) |

>[!IMPORTANT]
>Dynamics にデータをコピーする場合は、既定の Azure Integration Runtime を使用してコピーを実行することはできません。 言い換えると、ソースのリンクされたサービスに指定された統合ランタイムがない場合は、Dynamics インスタンスに近い場所に明示的に [Azure Integration Runtime を作成](create-azure-integration-runtime.md#create-azure-ir)します。 次の例のように、Dynamics のリンクされたサービスに関連付けます。

>[!NOTE]
>Dynamics コネクタは、省略可能な "organizationName" プロパティを使用して Dynamics CRM/365 Online インスタンスを識別するために使用されていました。 それは引き続き機能しますが、代わりに新しい "serviceUri" プロパティを指定して、インスタンス検出のパフォーマンスを向上させることをお勧めします。

**例: Office 365 の認証をを使用する Dynamics Online**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>IFD 対応 Dynamics 365 および Dynamics CRM (オンプレミス)

"*Dyanmics Online と比べた場合に追加されたプロパティは、"hostName" と "port" です。*"

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **Dynamics** に設定する必要があります。 | [はい] |
| deploymentType | Dynamics インスタンスの展開の種類。 IFD 対応オンプレミス Dynamics を **"OnPremisesWithIfd"** にする必要があります。| [はい] |
| hostName | オンプレミス Dynamics サーバーのホスト名。 | [はい] |
| ポート | オンプレミス Dynamics サーバーのポート。 | いいえ (既定値は 443) |
| organizationName | Dynamics インスタンスの組織の名前。 | [はい] |
| authenticationType | Dynamics サーバーに接続する認証の種類。 IFD 対応オンプレミス Dynamics を **"Ifd"** に指定します。 | [はい] |
| username | Dynamics に接続するためのユーザー名を指定します。 | [はい] |
| password | username に指定したユーザー アカウントのパスワードを指定します。 このフィールドを SecureString としてマークして ADF に安全に格納するか、Azure Key Vault にパスワードを格納し、データ コピーの実行時にコピー アクティビティでそこからプルするかを選択できます。詳しくは、[Key Vault への資格情報の格納](store-credentials-in-key-vault.md)に関するページをご覧ください。 | [はい] |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | ソースの場合はいいえ、シンクの場合ははい |

>[!IMPORTANT]
>Dynamics にコピーするには、Dynamics インスタンスに近い場所に明示的に [Azure Integration Runtime を作成](create-azure-integration-runtime.md#create-azure-ir)します。 次の例のように、リンクされたサービスに関連付けます。

**例: IFD 認証を使用する IFD 対応オンプレミス Dynamics**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Dynamics データセット でサポートされるプロパティの一覧を示します。

Dynamics をコピー元またはコピー先としてデータをコピーするには、データセットの type プロパティを **DynamicsEntity** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**DynamicsEntity** に設定する必要があります。 |[はい] |
| entityName | 取得するエンティティの論理名。 | ソースの場合はいいえ (アクティビティ ソースの "query" が指定されている場合)、シンクの場合ははい |

> [!IMPORTANT]
>- Dynamics からデータをコピーする場合は、Dynamics データセットに "structure" セクションが必要です。 それは、コピーする Dynamics のデータの列名とデータ型を定義します。 詳細については、「[データセット構造](concepts-datasets-linked-services.md#dataset-structure)」と「[Dynamics のデータ型のマッピング](#data-type-mapping-for-dynamics)」を参照してください。
>- Dynamics にデータをコピーする場合、Dynamics データセットの "structure" セクションは省略可能です。 コピー先の列は、ソース データ スキーマによって決定されます。 ソースがヘッダーのない CSV ファイルの場合、入力データセットに列名とデータ型を "structure" で指定します。 それらは、1 つづつ順番に CSV ファイルのフィールドにマップされます。

**例:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Dynamics ソースとシンクの種類でサポートされるプロパティの一覧を示します。

### <a name="dynamics-as-a-source-type"></a>ソースの種類としての Dynamics

Dynamics からデータをコピーするは、コピー アクティビティのソースの種類を **DynamicsSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **DynamicsSource** に設定する必要があります。 | [はい] |
| クエリ | FetchXML は、Dynamics (オンラインおよびオンプレミス) で使用される独自のクエリ言語です。 次の例を参照してください。 詳細については、「[FetchXML を使用したクエリの構築](https://msdn.microsoft.com/library/gg328332.aspx)」を参照してください。 | いいえ (データセットの "entityName" が指定されている場合) |

>[!NOTE]
>PK 列は、FetchXML クエリで構成する列のプロジェクションに含まれていない場合でも常にコピーされます。

**例:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>FetchXML クエリの例

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>シンクの種類としての Dynamics

Dynamics にデータをコピーするは、コピー アクティビティのシンクの種類を **DynamicsSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは **DynamicsSink** に設定する必要があります。 | [はい] |
| writeBehavior | 操作の書き込み動作。<br/>使用可能な値: **"Upsert"**。 | [はい] |
| writeBatchSize | 各バッチで Dynamics に書き込まれたデータの行数。 | いいえ (既定値は 10) |
| ignoreNullValues | 書き込み操作時に入力データ (キー フィールドを除く) からの null 値を無視するかどうかを示します。<br/>使用可能な値: **true** および **false**。<br>- **True**: upsert /更新操作を行うときに、対象オブジェクト内のデータが変更されないようにします。 挿入操作を実行するときに、定義済みの既定値を挿入します。<br/>- **False**: upsert/更新操作を行うときに、対象オブジェクト内のデータを NULL に更新します。 挿入操作を実行するときに、NULL 値を挿入します。 | いいえ (既定値は false) |

>[!NOTE]
>Dynamics シンクでのシンク "**writeBatchSize**" とコピー アクティビティ "**[parallelCopies](copy-activity-performance.md#parallel-copy)**" の既定値は、どちらも 10 です。 そのため、100 個のレコードが Dynamics に同時に送信されます。

Dynamics 365 オンラインでは、[1 組織あたりの同時バッチ呼び出し数が 2](https://msdn.microsoft.com/en-us/library/jj863631.aspx#Run-time%20limitations) という制限があります。 この制限を超えた場合、最初の要求が実行される前に "サーバー ビジー" 状態が発生します。 "writeBatchSize" を 10 以下に保つと、このような同時呼び出しの制限が回避されます。

"**writeBatchSize**" と "**parallelCopies**" の最適な組み合わせは、エンティティのスキーマによって変わります。たとえば、その呼び出しのためにフックされる列数、行サイズ、プラグイン/ワークフロー/ワークフロー アクティビティの数などです。10 writeBatchSize * 10 parallelCopies という既定の設定は、最高のパフォーマンスではないかもしれませんが、ほとんどの Dynamics エンティティに利用できる Dynamics サービスの推奨値です。 コピー アクティビティ設定の組み合わせを調整することで、パフォーマンスを調整できます。

**例:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Dynamics のデータ型のマッピング

Dynamics からデータをコピーするとき、次の Dynamics のデータ型から Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

次のマッピング テーブルを使用して、ソースの Dynamics データ型に基づき、データセット構造に対応する Data Factory データ型を構成します。

| Dynamics データ型 | Data Factory の中間データ型 | ソースとしてサポート | シンクとしてサポート |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | ブール | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | | 
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (関連付けられている 1 つの型を含む) |
| AttributeType.ManagedProperty | ブール | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> Dynamics データ型の AttributeType.CalendarRules と AttributeType.PartyList はサポートされていません。

## <a name="next-steps"></a>次の手順
Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
