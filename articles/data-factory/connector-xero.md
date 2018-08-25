---
title: Azure Data Factory を使用して Xero からデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Xero のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
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
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: 17341e8431ffd5cc41fdda86a7511688dcabaf45
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045386"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Azure Data Factory を使用して Xero からデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、Xero からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!IMPORTANT]
> このコネクタは、現在プレビューの段階です。 実際にお試しいただき、フィードバックをお寄せください。 ソリューションでプレビュー版コネクタの依存関係を取得したい場合、[Azure サポート](https://azure.microsoft.com/support/)にお問い合わせください。

## <a name="supported-capabilities"></a>サポートされる機能

Xero から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Xero コネクタは以下をサポートします。

- Xero [プライベート アプリケーション](https://developer.xero.com/documentation/getting-started/api-application-types) (パブリック アプリケーションは非サポート)。
- "Reports" を除くすべて Xero テーブル (API エンドポイント)。 

Azure Data Factory では接続を有効にする組み込みのドライバーが提供されるので、このコネクタを使用してドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Xero コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Xero のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **Xero** に設定する必要があります。 | [はい] |
| host | Xero サーバーのエンドポイント (`api.xero.com`)。  | [はい] |
| consumerKey | Xero アプリケーションに関連付けられているコンシューマー キー。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | [はい] |
| privateKey | Xero プライベート アプリケーション用に生成された .pem ファイルの秘密キー (「[Create a public/private key pair (公開/秘密キー ペアの作成)](https://developer.xero.com/documentation/api-guides/create-publicprivate-key)」を参照してください)。 `openssl genrsa -out privatekey.pem 512` を使用し、numbits に 512 を指定して、**privatekey.pem を生成します。** 1024 はサポートされていません。 Unix の改行文字 (\n) も含め、.pem ファイルのすべてのテキストを含めます (以下のサンプルを参照してください)。<br/><br/>このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | [はい] |
| useEncryptedEndpoints | データ ソースのエンドポイントが HTTPS を使用して暗号化されるかどうかを指定します。 既定値は true です。  | いいえ  |
| useHostVerification | SSL 経由で接続するときに、サーバーの証明書内のホスト名がサーバーのホスト名と一致する必要があるかどうかを指定します。 既定値は true です。  | いいえ  |
| usePeerVerification | SSL 経由で接続するときに、サーバーの ID を検証するかどうかを指定します。 既定値は true です。  | いいえ  |

**例:**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

**サンプル秘密キー値:**

Unix の改行文字 (\n) も含め、.pem ファイルのすべてのテキストを含めます。

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Xero データセットでサポートされるプロパティの一覧を示します。

Xero からデータをコピーするには、データセットの type プロパティを **XeroObject** に設定します。 この種類のデータセットに追加の種類固有のプロパティはありません。

**例**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Xero ソースでサポートされるプロパティの一覧を示します。

### <a name="xero-as-source"></a>ソースとしての Xero

Xero からデータをコピーするは、コピー アクティビティのソースの種類を **XeroSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **XeroSource** に設定する必要があります。 | [はい] |
| クエリ | カスタム SQL クエリを使用してデータを読み取ります。 たとえば、「 `"SELECT * FROM Contacts"`」のように入力します。 | [はい] |

**例:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Xero クエリを指定する際には、次の点に注意してください。

- 複雑な項目を含むテーブルは、複数のテーブルに分割されます。 たとえば、銀行トランザクションには複雑なデータ構造 "LineItems" が含まれるため、銀行トランザクションのデータはテーブル `Bank_Transaction` および `Bank_Transaction_Line_Items` にマップされ、それらをリンクするための外部キーとして `Bank_Transaction_ID` が使用されます。

- Xero データは 2 つのスキーマを通じて使用できます。`Minimal` (既定) と `Complete` です。 Complete スキーマには、目的のクエリを実行する前に追加データ (例: ID 列) を必要とする、前提条件呼び出しテーブルが含まれます。

次のテーブルは、Minimal スキーマと Complete スキーマで同じ情報を持ちます。 API 呼び出しの数を減らすには、Minimal スキーマ (既定) を使用します。

- Bank_Transactions
- Contact_Groups 
- 連絡先 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Invoices 
- Invoices_Credit_Notes
- Invoices_ Prepayments 
- Invoices_Overpayments 
- Manual_Journals 
- Overpayments 
- Overpayments_Allocations 
- Prepayments 
- Prepayments_Allocations 
- Receipts 
- Receipt_Validation_Errors 
- Tracking_Categories

次のテーブルは、Complete スキーマでのみ照会できます。

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ Persons 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ Payments 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="next-steps"></a>次の手順
コピー アクティビティによってサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
