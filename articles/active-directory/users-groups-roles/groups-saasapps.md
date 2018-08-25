---
title: SaaS アプリケーションへのアクセスをグループで管理する | Microsoft Docs
description: Azure Active Directory と連携する SaaS アプリケーションへのアクセス権を、Azure Active Directory Premium または Basic でグループを使用して割り当てる方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 03/14/2017
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 8c04f2723466ea7abc8ea3c3cc1f1efb953da764
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449385"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>SaaS アプリケーションへのアクセスをグループで管理する
Azure Active Directory (Azure AD) の Azure AD Premium ライセンスまたは Azure AD Basic ライセンスでは、Azure AD と連携する SaaS アプリケーションへのアクセス権を、グループを使って割り当てることができます。 たとえばマーケティング部門を対象に 5 つの SaaS アプリケーションを使用するためのアクセス権を割り当てる場合、同部門のユーザーから成るグループを作成したうえで、マーケティングで必要となる 5 つの SaaS アプリケーションにそのグループを割り当てます。 このようにマーケティング部門のメンバーシップを一元的に管理することで時間を節約することが可能です。 この場合アプリケーションに対するユーザーの割り当ては、そのユーザーがマーケティング グループのメンバーとして追加されたときに行われます。同様に、マーケティング グループからユーザーが削除されると、アプリケーションからもその割り当てが削除されます。 この機能は、Azure AD アプリケーション ギャラリー内から追加できる多数のアプリケーションで利用することができます。

> [!IMPORTANT]
> この機能は、Azure AD Premium の試用版の使用を開始するか、Azure AD Premium または Azure AD Basic のライセンスを購入した後に限り使用できます。 グループ ベースの割り当てがサポートされるのはセキュリティ グループのみです。 現在、アプリケーションに対するグループ ベースの割り当てでは入れ子になったグループ メンバーシップはサポートされていません。

**SaaS アプリケーションにユーザーまたはグループに対するアクセス権を割り当てるには**

1. [Azure AD 管理センター](https://aad.portal.azure.com)で、**[エンタープライズ アプリケーション]** を選択します。
2. アプリケーション ギャラリーから追加したアプリケーションをクリックして開きます。
3. **[ユーザーとグループ]**、**[ユーザーの追加]** の順に選択します。
4. **[割り当ての追加]** で **[ユーザーとグループ]** を選択し、**[ユーザーとグループ]** 選択リストを開きます。
6. 必要な数のグループまたはユーザーを選択して **[選択]** をクリックまたはタップし、**[割り当ての追加]** リストに追加します。 ユーザーに対するロールの割り当ても、この段階で行います。
7. **[割り当て]** を選択し、選択したエンタープライズ アプリケーションにユーザーまたはグループを割り当てます。

### <a name="next-steps"></a>次の手順
次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory グループによるリソースへのアクセス管理](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory のアプリケーション構成の管理に関する記事の索引](../active-directory-apps-index.md)
* [グループの設定を構成するための Azure Active Directory コマンドレット](groups-settings-cmdlets.md)
* [Azure Active Directory とは](../fundamentals/active-directory-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](../connect/active-directory-aadconnect.md)
