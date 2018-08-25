---
title: Azure Active Directory テナントの対話の特性 | Microsoft Docs
description: テナントを完全に独立したリソースとして理解することによって Azure Active テナントを管理する
services: active-tenant
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 85f7cddb7231bf9c5e45de87af3c922148f214be
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861442"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>複数の Azure Active Directory テナントが対話する方法を理解する

Azure Active Directory (Azure AD) では、各テナントは完全に独立したリソース、つまり、管理されている他のテナントから論理的に独立したピアです。 テナント間に親子の関係はありません。 このテナント間の独立には、リソースの独立、管理の独立、および同期の独立が含まれます。

## <a name="resource-independence"></a>リソースの独立
* あるテナントでリソースを作成または削除しても、外部ユーザーの部分的な例外を除き、別のテナント内のどのリソースにも影響を与えません。 
* あるテナントでいずれかのドメイン名を使用した場合は、それを他のどのテナントでも使用できません。

## <a name="administrative-independence"></a>管理上の独立
テナント 'Contoso' の管理者以外のユーザーがテスト テナント 'Test' を作成した場合は、次のようになります。

* 既定では、テナントを作成したユーザーはその新しいテナントに外部ユーザーとして追加され、そのテナント内のグローバル管理者ロールが割り当てられます。
* 'Test' の管理者が管理者特権を明示的に付与しない限り、テナント 'Contoso' の管理者にはテナント 'Test' に対する直接の管理者特権はありません。 ただし、'Contoso' の管理者は、'Test' を作成したユーザー アカウントを制御している場合はテナント 'Test' へのアクセスを制御できます。
* あるテナントでユーザーの管理者ロールを追加/削除した場合、その変更は、そのユーザーが別のテナントに持っている管理者ロールに影響を与えません。

## <a name="synchronization-independence"></a>同期の独立
次のいずれか 1 つのインスタンスからデータが同期されるように、各 Azure AD テナントを個別に構成できます。

* Azure AD Connect ツール。データを 1 つの AD フォレストと同期する場合。
* Forefront Identity Manager 用の Azure Active テナント コネクタ。データを 1 つ以上のオンプレミスのフォレストまたは Azure AD 以外のデータ ソース、あるいはその両方と同期する場合。

## <a name="add-an-azure-ad-tenant"></a>Azure AD テナントを追加する
Azure Portal で Azure AD テナントを追加するには、Azure AD グローバル管理者のアカウントで [Azure Portal](https://portal.azure.com) にサインインし、左側の **[新規]** を選びます。

> [!NOTE]
> 他の Azure リソースとは異なり、テナントは Azure サブスクリプションの子リソースではありません。 Azure サブスクリプションが取り消されたり、期限切れになったりした場合でも、Azure PowerShell、Azure Graph API、または Office 365 管理センターを使用してテナント データに引き続きアクセスできます。 また、[テナントに別のサブスクリプションを関連付ける](../fundamentals/active-directory-how-subscriptions-associated-directory.md)こともできます。
>

## <a name="next-steps"></a>次の手順
Azure AD のライセンスに関する問題およびベスト プラクティスの広範囲にわたる概要については、[Azure Active Directory のテナントのライセンス](../fundamentals/active-directory-licensing-whatis-azure-portal.md)に関するページをご覧ください。
