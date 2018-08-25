---
title: Azure Data Factory を使用して SAP BW からデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、SAP Business Warehouse からサポートされているシンク データ ストアへデータをコピーする方法について説明します。
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
ms.date: 08/07/2018
ms.author: jingwang
ms.openlocfilehash: 52bbf93d73af281f3959e056a4d5b959e7286cb5
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590332"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Azure Data Factory を使用して SAP Business Warehouse からデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [現在のバージョン](connector-sap-business-warehouse.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、SAP Business Warehouse (BW) からデータコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

SAP Business Warehouse のデータを、サポートされる任意のシンク データ ストアにコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この SAP Business Warehouse コネクタは以下をサポートします。

- SAP Business Warehouse **バージョン 7.x**。
- MDX クエリを使用した、**InfoCubes および QueryCubes** (BEx クエリなど) からのデータのコピー。
- 基本認証を使用したデータのコピー。

## <a name="prerequisites"></a>前提条件

この SAP Business Warehouse コネクタを使用するには、次の作業を行う必要があります。

- セルフホステッド統合ランタイムをセットアップする。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。
- **SAP NetWeaver ライブラリ**を統合ランタイム コンピューターにインストールします。 SAP Netweaver ライブラリは SAP 管理者から入手します。また、[SAP ソフトウェアのダウンロード センター](https://support.sap.com/swdc)から直接取得することもできます。 最新のバージョンをダウンロードできる場所は、"**SAP Note #1025361**" を検索して確認してください。 Integration Runtime のインストールに一致する **64 ビット** SAP NetWeaver ライブラリが選択されていることを確認します。 SAP Note に従って、SAP NetWeaver RFC SDK に含まれるすべてのファイルをインストールします。 SAP NetWeaver ライブラリは、SAP Client Tools のインストールにも含まれます。

>[!TIP]
>SAP BW に関する接続の問題をトラブルシューティングするには、次のことを確認してください。
>- NetWeaver RFC SDK から抽出された依存関係のライブラリはすべて、%windir%\system32 フォルダーにあります。 通常、ここには icudt34.dll、icuin34.dll、icuuc34.dll、libicudecnumber.dll、librfc32.dll、libsapucum.dll、sapcrypto.dll、sapcryto_old.dll、sapnwrfc.dll が含まれています。
>- 必要なポートは、セルフホステッド IR マシン上で有効にされた SAP サーバーに接続するために使用されます。通常、これらはポート 3300 と 3201 です。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、SAP Business Warehouse コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SAP Business Warehouse (BW) のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **SapBw** に設定する必要があります。 | [はい] |
| [サーバー] | SAP BW インスタンスが存在するサーバーの名前。 | [はい] |
| systemNumber | SAP BW システムのシステムの数。<br/>使用できる値: 文字列として表される 2 桁の 10 進数。 | [はい] |
| clientId | SAP BW システム内のクライアントのクライアント ID。<br/>使用できる値: 文字列として表される 3 桁の 10 進数。 | [はい] |
| userName | SAP サーバーにアクセスできるユーザーの名前。 | [はい] |
| password | ユーザーのパスワード。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | [はい] |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |[はい] |

**例:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、SAP BW データセット でサポートされるプロパティの一覧を示します。

SAP BW からデータをコピーするには、データセットの type プロパティを **RelationalTable** に設定します。 しかるに、RelationalTable 型の SAP BW データセットに対して、サポートされている型固有のプロパティはありません。

**例:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SAP BW ソースでサポートされるプロパティの一覧を示します。

### <a name="sap-bw-as-source"></a>ソースとしての SAP BW

SAP BW からデータをコピーするには、コピー アクティビティのソース タイプを **RelationalSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **RelationalSource** に設定する必要があります。 | [はい] |
| query | SAP BW インスタンスからデータを読み取る MDX クエリを指定します。 | [はい] |

**例:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw"></a>SAP BW のデータ型マッピング

SAP BW からデータをコピーするとき、SAP BW のデータ型から Azure Data Factory の中間データ型への、以下のマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SAP BW データ型 | Data Factory の中間データ型 |
|:--- |:--- |
| ACCP | int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| CUKY | String |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | int |
| LANG | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | String |
| ユニット | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
