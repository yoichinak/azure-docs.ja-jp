---
title: Key Vault 証明書の概要
description: 次のシナリオでは、キー コンテナー内に最初の証明書を作成するために必要な追加の手順を含め、Key Vault の証明書管理サービスの主な使用方法をいくつか概説します。
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a788b958-3acb-4bb6-9c94-4776852aeea1
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: f1a1a2fa083dd1bf02132e08981d736a17a2c58f
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109486"
---
# <a name="get-started-with-key-vault-certificates"></a>Key Vault 証明書の概要
次のシナリオでは、キー コンテナー内に最初の証明書を作成するために必要な追加の手順を含め、Key Vault の証明書管理サービスの主な使用方法をいくつか概説します。

以下の概要を示します。
- 最初の Key Vault 証明書の作成
- Key Vault と提携している証明機関での証明書の作成
- Key Vault と提携していない証明機関での証明書の作成
- 証明書のインポート

## <a name="certificates-are-complex-objects"></a>証明書は複合オブジェクトである
証明書は、Key Vault 証明書として互いにリンクされている相互に関連付けられた 3 つのリソース (証明書メタデータ、キー、シークレット) から構成されます。


![証明書は複合的である](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>最初の Key Vault 証明書の作成  
 証明書を Key Vault (KV) に作成するには、その前に前提条件手順 1. と 2. を正常に完了する必要があり、キー コンテナーがこのユーザー/組織に存在する必要があります。  

**手順 1** - 証明機関 (CA) プロバイダー  
-   特定の会社 (たとえば、 Contoso など) の IT 管理者、PKI 管理者または CA のアカウントを管理する任意のユーザーとしてのオンボーディングは、Key Vault 証明書を使用するための前提条件です。  
    次の CA は、現在 Key Vault と提携しているプロバイダーです。  
    -   DigiCert - Key Vault は、DigiCert による OV SSL 証明書を提供します。  
    -   GlobalSign - Key Vault は、GlobalSign による OV SSL 証明書を提供します  
    -   WoSign - Key Vault は、顧客が WoSign ポータルの自分の WoSign アカウントで構成した設定に基づいて WoSign による OV SSL または EV SSL 証明書を提供します。  

**手順 2** - CA プロバイダーのアカウント管理者は、Key Vault が使用する資格証明を作成して、Key Vault を介して SSL 証明書を登録、更新、使用します。

**手順 3** - Contoso 管理者と証明書を所有する Contoso 従業員 (Key Vault ユーザー) は、CA に応じて、証明書を管理者から取得するか、CA のアカウントから直接取得できます。  

-   [証明書の発行者](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers)リソースを作成することで、キー コンテナーへの資格情報の追加操作を開始します。 
    -   例: MyDigiCertIssuer  
        -   プロバイダー  
        -   資格情報 - CA アカウント資格情報。 各 CA が固有の特定のデータを持ちます。  

     CA プロバイダーでのアカウントの作成について詳しくは、[Key Vault のブログ](http://aka.ms/kvcertsblog)で関連する投稿をご覧ください。  

**手順 3.1** - 通知用の[証明書連絡先](https://docs.microsoft.com/rest/api/keyvault/certificate-contacts)を設定します。 これは、Key Vault ユーザーの連絡先です。 Key Vault はこの手順を適用しません。  

注 - 手順 3.1 までのこのプロセスは、1 回のみの操作です。  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Key Vault と提携している CA での証明書の作成

![Key Vault と提携している証明機関での証明書の作成](media/certificate-authority-2.png)

**手順 4** - 次の説明は、前の図の緑の番号付きの手順に対応しています。  
  (1) - 上の図で、アプリケーションは証明書を作成しています。これは、キー コンテナーにキーを作成することで内部的に開始します。  
  (2) - Key Vault は、SSL 証明書の要求を CA に送信します。  
  (3) - アプリケーションは、ループおよび待機プロセスで Key Vault に証明書の完了をポーリングします。 Key Vault が CA の応答で x509 証明書を受信すると、証明書の作成が完了します。  
  (4) - CA は、X509 SSL 証明書で Key Vault の SSL 証明書の要求に応答します。  
  (5) - 新しい証明書の作成は、CA の X509 証明書の合併で完了します。  

  Key Vault ユーザー - ポリシーを指定することで証明書を作成します

  -   必要に応じて繰り返します  
  -   ポリシー制約  
      -   X509 のプロパティ  
      -   キーのプロパティ  
      -   プロバイダー リファレンス - > 例: MyDigiCertIssure  
      -   更新情報 - > 例: 有効期限まで 90 日  

  - 証明書の作成プロセスは、通常は非同期プロセスで、キー コンテナーへの証明書の作成操作の状態のポーリングが含まれます。  
[証明書の取得操作](https://docs.microsoft.com/en-us/rest/api/keyvault/getcertificateoperation)  
      -   状態: 完了、エラー情報ありで失敗、またはキャンセル済み  
      -   作成の遅延のため、キャンセル操作を開始できます。 キャンセルは、有効な場合と有効でない場合とがあります。  

## <a name="import-a-certificate"></a>証明書のインポート  
 代わりに、証明書を Key Vault にインポートできます (PFX または PEM)。  

 PEM 形式について詳しくは、「[キー、シークレット、証明書について](about-keys-secrets-and-certificates.md)」をご覧ください。  

 証明書のインポート - PEM または PFX がディスク上に必要で、秘密キーが必要です。 
-   コンテナー名と証明書名 (ポリシーは省略可能) を指定する必要があります

-   PEM/PFX ファイルには、KV が解析して証明書のポリシーの設定に使用できる属性が含まれています。 証明書のポリシーが既に指定されている場合、KV は PFX/PEM ファイルからデータを照合しようとします。  

-   インポートが完了すると、後続の操作では新しいポリシー (新しいバージョン) が使用されます。  

-   それ以降の操作がない場合、Key Vault はまず有効期限通知を送信します。 

-   また、ユーザーはポリシーを編集できます。これはインポート時に機能しますが、インポート時に情報が指定されなかった場合は既定値が含まれています。 例: 発行者情報なし  

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Key Vault と提携していない CA での証明書の作成  
 この方法では、Key Vault の提携プロバイダー以外の CA を使用できます。つまり、組織は任意の CA を使用できます。  

![独自の証明機関の証明書の作成](media/certificate-authority-1.png)  

 次の手順の説明は、前の図の緑の文字の手順に対応しています。  

  (1) - 上の図で、アプリケーションは証明書を作成しています。これは、キー コンテナーにキーを作成することで内部的に開始します。  

  (2) - Key Vault はアプリケーションに証明書署名要求 (CSR) を返します。  

  (3) - アプリケーションは、選択した CA に CSR を渡します。  

  (4) - 選択した CA は、X509 証明書で応答します。  

  (5) - アプリケーションは、CA からの X509 証明書の合併で新しい証明書の作成を完了します。

## <a name="see-also"></a>関連項目
- [証明書の操作](/rest/api/keyvault/certificate-operations)
- [キー、シークレット、証明書について](about-keys-secrets-and-certificates.md)
