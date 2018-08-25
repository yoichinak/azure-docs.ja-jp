---
title: スコープ フィルターを使用してアプリをプロビジョニングする | Microsoft Docs
description: スコープ フィルターを使用して、自動ユーザー プロビジョニングをサポートするアプリ内のオブジェクトが、ビジネス要件を満たしていないのにプロビジョニングされてしまうことを防ぐ方法について説明します。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: barbkess
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9f28c97fed2a5fa4990c1310e8389868c6b7dc20
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368550"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>スコープ フィルターを使用した属性ベースのアプリケーション プロビジョニング
この記事では、スコープ フィルターを使用して属性ベースのルールを定義する方法について説明します。このルールで、アプリケーションに対してプロビジョニングするユーザーを指定します。

## <a name="scoping-filter-use-cases"></a>スコープ フィルターの使用例

スコープ フィルターを利用すると、Azure Active Directory (Azure AD) プロビジョニング サービスで、ユーザーに特定の値に一致する属性が与えられているとき、そのユーザーを追加したり、除外したりできます。 たとえば、営業チームが利用する SaaS アプリケーションに Azure AD からユーザーをプロビジョニングするとき、"Sales" の "Department" 属性を持つユーザーのみがプロビジョニングの範囲に入るように指定できます。

スコープ フィルターは、プロビジョニング コネクタの種類によって異なる方法で使用できます。

* **Azure AD から SaaS アプリケーションへの外向きプロビジョニング**。 Azure AD がソース システムのとき、プロビジョニングに含めるユーザーを決定する方法として最も一般的なものが[ユーザーとグループの割り当て](manage-apps/assign-user-or-group-access-portal.md)です。 ユーザーとグループの割り当てはシングル サインオンの有効化にも利用され、1 つの方法でアクセスとプロビジョニングを管理できます。 スコープ フィルターを割り当てに加えて (任意で)、あるいは割り当ての代わりに利用し、属性値に基づいてユーザーを絞り込むことができます。

    >[!TIP]
    > エンタープライズ アプリケーションの割り当てに基づいてプロビジョニングを無効にできます。プロビジョニング設定の [[スコープ]](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) を **[すべてのユーザーとグループを同期する]** に設定します。 このオプションと属性基準のスコープ フィルターを利用することで、グループ基準の割り当てよりパフォーマンスが速くなります。  

* **HCM アプリケーションから Azure AD と Active Directory への内向きプロビジョニング**。 [Workday などの HCM アプリケーション](saas-apps/workday-tutorial.md)がソース システムのとき、HCM アプリケーションから Active Directory または Azure AD にプロビジョニングするユーザーを決定する主要な方法がスコープ フィルターです。

既定では、Azure AD プロビジョニング コネクタには属性基準のスコープ フィルターが設定されていません。 

## <a name="scoping-filter-construction"></a>スコープ フィルターの構造

スコープ フィルターは 1 つまたは複数の*句*で構成されます。 句は、各ユーザーの属性を評価することによって、スコープ フィルターを通過できるユーザーを決定します。 たとえば、ユーザーの "State" 属性が New York と等しいことを求める句が指定されていると、New York 在住のユーザーのみがアプリケーションにプロビジョニングされます。 

1 つの句は、1 つの属性値の 1 つの条件を定義します。 1 つのスコープ フィルターに複数の句が作成されている場合は、"AND" ロジックでまとめて評価されます。 つまり、ユーザーをプロビジョニングするには、すべての句の評価結果が "true" になる必要があります。

最後に、複数のスコープ フィルターを 1 つのアプリケーションに対して作成できます。 複数のスコープ フィルターがある場合は、"OR" ロジックでまとめて評価されます。 つまり、いずれかのスコープ フィルターのすべての句の評価結果が "true" であれば、そのユーザーがプロビジョニングされます。

Azure AD プロビジョニング サービスによって処理されるユーザーまたはグループは常に個別に各スコープ フィルターに対して評価されます。

例として、次のスコープ フィルターを検討します。

![スコープ フィルター](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

このスコープ フィルターに従うと、ユーザーをプロビジョニングするには、そのユーザーは次の条件を満たす必要があります。

* New York 在住である。
* エンジニアリング部門で働いている。
* 社員 ID の番号が 1,000,000 から 2,000,000 までに入る。
* 役職が null でも空でもない。

## <a name="create-scoping-filters"></a>スコープ フィルターを作成する
スコープ フィルターは各 Azure AD ユーザー プロビジョニング コネクタの属性マッピングで設定されます。 次の手順では、[サポートされているアプリケーションの 1 つ](saas-apps/tutorial-list.md)に自動プロビジョニングを既に設定しており、それにスコープ フィルターを追加するものと想定しています。

### <a name="create-a-scoping-filter"></a>スコープ フィルターを作成する
1. [Azure ポータル](https://portal.azure.com)で、**[Azure Active Directory]** > **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** セクションに移動します。

2. 自動プロビジョニングを設定しているアプリケーションを選択します (例: "ServiceNow")。

3. **[プロビジョニング]** タブを選択します。

4. **[マッピング]** セクションで、スコープ フィルターを構成するマッピングを選択します (例: "Synchronize Azure Active Directory Users to ServiceNow")。

5. **[ソース オブジェクト スコープ]** メニューを選択します。

6. **[スコープ フィルターの追加]** を選択します。

7. 照合対象としてソースの **[属性名]**、**[演算子]**、**[属性値]** を選択し、句を定義します。 次の演算子がサポートされています。

   a. **EQUALS**。 評価される属性が (大文字と小文字の区別を含めて) 入力文字列値と完全に一致する場合、句は "true" を返します。

   b. **NOT EQUALS**。 評価される属性が (大文字と小文字の区別を含めて) 入力文字列値と一致しない場合、句は "true" を返します。

   c. **IS TRUE**。 評価される属性にブール値 true が含まれる場合、句は "true" を返します。

   d. **IS FALSE**。 評価される属性にブール値 false が含まれる場合、句は "true" を返します。

   e. **IS NULL**。 評価される属性が空の場合、句は "true" を返します。

   f. **IS NOT NULL**。 評価される属性が空でない場合、句は "true" を返します。

   g. **REGEX MATCH**。 評価される属性が正規表現パターンと一致する場合、句は "true" を返します。 例: ([1-9][0-9]) は 10 から 99 までのあらゆる数字と一致します。

   h. **NOT REGEX MATCH**。 評価される属性が正規表現パターンと一致しない場合、句は "true" を返します。

8. **[新しいスコープ句の追加]** を選択します。

9. 必要に応じて、手順 7 ～ 8 を繰り返してスコープ句を追加します。

10. **[スコープ フィルターのタイトル]** で、スコープ フィルターの名前を追加します。

11. **[OK]** を選択します。

12. **[スコープ フィルター]** 画面でもう一度 **[OK]** を選択します。 必要に応じて、手順 6 ～ 11 を繰り返して別のスコープ フィルターを追加します。

13. **[属性マッピング]** 画面で **[保存]** を選択します。 

>[!IMPORTANT] 
> 新しいスコープ フィルターを保存すると、アプリケーションの完全同期が新たに開始されます。ソース システムのすべてのユーザーが新しいスコープ フィルターに対してもう一度評価されます。 アプリケーションのユーザーが以前はプロビジョニングの範囲に入っていたが、範囲から外れた場合、アプリケーションでそのアカウントが無効になるか、プロビジョニングが解除されます。


## <a name="related-articles"></a>関連記事
* [Azure Active Directory のアプリケーション構成の管理に関する記事の索引](active-directory-apps-index.md)
* [SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)
* [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)
* [属性マッピングの式の書き方](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [アカウント プロビジョニング通知](active-directory-saas-account-provisioning-notifications.md)
* [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](manage-apps/use-scim-to-provision-users-and-groups.md)
* [SaaS アプリを統合する方法に関するチュートリアルの一覧](saas-apps/tutorial-list.md)

