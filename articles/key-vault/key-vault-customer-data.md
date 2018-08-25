---
title: Azure Key Vault の顧客データ機能 | Microsoft Docs
description: Key Vault 内の顧客データについて説明します
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/22/2018
ms.author: barclayn
ms.openlocfilehash: 1ddc74b1960095509a77d4b3072017847df42d90
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38235535"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault の顧客データ機能

Azure Key Vault は、資格情報コンテナー、キー、シークレット、証明書、および管理されたストレージ アカウントの作成および更新中に顧客データを受信します。 この顧客データは、Azure Portal および REST API を使用して直接表示できます。 顧客データは、データを含むオブジェクトを更新または削除することにより編集または削除できます。

ユーザーまたはアプリケーションが Azure Key Vault にアクセスするとシステム のアクセス ログが生成されます。 詳細なアクセス ログは、Azure Insights から確認できます。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>顧客データの識別

次の情報を使用して、Azure Key Vault 内の顧客データを識別できます。

- Azure Key Vault のアクセス ポリシーには、ユーザー、グループ、またはアプリケーションを表すオブジェクト ID が含まれています。
- 証明書の件名には、電子メール アドレス、またはその他のユーザーまたは組織の ID が含まれていることがあります。
- 証明書の連絡先には、ユーザーの電子メール アドレス、氏名、または電話番号が含まれている可能性があります。
- 証明書の発行者には、電子メール アドレス、名前、電話番号、アカウントの資格情報、および組織の詳細情報が含まれている可能性があります。
- Azure Key Vault 内のオブジェクトには任意のタグを適用できます。 これらのオブジェクトには、資格情報コンテナー、キー、シークレット、証明書、およびストレージ アカウントが含まれます。 使用されるタグに個人データが含まれている可能性があります。
- Azure Key Vault のアクセス ログには、オブジェクトの ID、[UPN](../active-directory/connect/active-directory-aadconnect-userprincipalname.md)、および各 REST API 呼び出しの IP アドレスが含まれます。
- Azure Key Vault の診断ログには、オブジェクトの ID および各 REST API 呼び出しの IP アドレスが含まれます。

## <a name="deleting-customer-data"></a>顧客データの削除

資格情報コンテナー、キー、シークレット、証明書、および管理されたストレージ アカウントを作成するために使用したものと同じ REST API、ポータル エクスペリエンス、SDK を使用して、これらのオブジェクトを更新および削除できます。

論理的な削除では、削除後 90 日間は削除されたデータを回復することができます。 論理的な削除の場合、パージ操作を実行することによって 90 日の保有期限が切れる前に、データを完全に削除できます。 パージ操作をブロックするよう資格情報コンテナーまたはサブスクリプションが構成されている場合は、スケジュールされた保有期間が経過するまで、データを完全に削除することはできません。

## <a name="exporting-customer-data"></a>顧客データのエクスポート

資格情報コンテナー、キー、シークレット、証明書、および管理されたストレージ アカウントを作成するために使用したものと同じ REST API、ポータル エクスペリエンス、SDK を使用して、これらのオブジェクトを表示およびエクスポートできます。

Azure Key Vault のログ記録機能はオプション機能で、オンにすると REST API を呼び出すごとにログが生成されます。 これらのログは、組織の要件を満たす保有ポリシーが適用されたサブスクリプションのストレージ アカウントに転送されます。

個人データが含まれている Azure Key Vault 診断ログは、ユーザー プライバシー ポータルでエクスポート要求を行うことで取得できます。 この要求は、テナント管理者が実行する必要があります。

## <a name="next-steps"></a>次の手順

- [Azure Key Vault のログ記録](key-vault-logging.md)

- [Azure Key Vault の論理的な削除の概要](key-vault-soft-delete-cli.md)

- [資格情報コンテナー](https://docs.microsoft.com/rest/api/keyvault/vaults)

- [Azure Key Vault のキー操作](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Azure Key Vault のシークレット操作](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Azure Key Vault の証明書とポリシー](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [証明書の発行者](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers)

- [Azure Key Vault ストレージ アカウント キー操作](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
