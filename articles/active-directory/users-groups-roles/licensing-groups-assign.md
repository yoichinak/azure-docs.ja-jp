---
title: Azure Active Directory でのグループへのライセンス割り当て | Microsoft Docs
description: Azure Active Directory のグループへのライセンスによってユーザーにライセンスを割り当てる方法
services: active-directory
keywords: Azure AD のライセンス
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7a235851d7172d32d62c64b163e0b7635a1a47fd
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861426"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Azure Active Directory でのグループ メンバーシップによるユーザーへのライセンスの割り当て

この記事では、製品のライセンスを Azure Active Directory (Azure AD) のユーザーのグループに割り当てて、その後、正しくライセンスされていることを確認する手順を説明します。

この例では、テナントには**人事部**というセキュリティ グループが含まれています。 このグループには、人事部のすべてのメンバーが含まれます (約 1,000 人)。 Office 365 Enterprise E3 ライセンスを部署全体に割り当てる必要があります。 製品に含まれている Yammer Enterprise サービスは、部署で使用開始の準備が整うまで、一時的に無効する必要があります。 同じユーザー グループに、Enterprise Mobility + Security ライセンスをデプロイする必要もあります。

> [!NOTE]
> Microsoft サービスの中には、すべての場所では利用できないものもあります。 ライセンスをユーザーに割り当てる前に、管理者はユーザーの [利用場所] プロパティを指定しておく必要があります。

> グループ ライセンスの割り当ての際、利用場所が指定されていないユーザーは、ディレクトリの場所を継承します。 ユーザーが複数の場所にいる場合は、Azure AD でのユーザー作成フローの一環として利用場所を常に設定することをお勧めします (AAD Connect 構成など)。これにより、ライセンス割り当ての結果が常に正しいことが保証され、ユーザーは許可されていない場所でサービスを受けられなくなります。

## <a name="step-1-assign-the-required-licenses"></a>手順 1. 必要なライセンスの割り当て

1. Administrator アカウントを使用して、[**Azure Portal**](https://portal.azure.com) にサインインします。 ライセンスを管理するアカウントは、グローバル管理者ロールまたはユーザー アカウント管理者である必要があります。

2. 左側のナビゲーション ウィンドウで **[すべてのサービス]** を選択し、**[Azure Active Directory]** を選択します。 このウィンドウは、お気に入りに追加したり、ポータルのダッシュボードにピン留めしたりすることができます。

3. **[Azure Active Directory]** ウィンドウで **[ライセンス]** を選択して、ライセンスを付与できるテナント内の製品すべてを確認および管理できるウィンドウを開きます。

4. **[すべての製品]** で、製品名を選択して Office 365 Enterprise E3 と Enterprise Mobility + Security の両方を選択します。 割り当てを開始するには、ウィンドウの上部にある **[割り当て]** を選択します。

   ![すべての製品、ライセンスの割り当て](./media/licensing-groups-assign/all-products-assign.png)

5. **[ライセンスの割り当て]** ウィンドウで **[ユーザーとグループ]** をクリックして、**[ユーザーとグループ]** ウィンドウを開きます。 "*人事部*" というグループ名を探してグループを選択し、ウィンドウの下部にある **[選択]** をクリックして忘れずに確定します。

   ![グループの選択](./media/licensing-groups-assign/select-a-group.png)

6. **[ライセンスの割り当て]** ウィンドウで **[割り当てオプション (省略可能)]** をクリックすると、先ほど選択した 2 つの製品に含まれているすべてのサービス プランが表示されます。 **Yammer Enterprise** を探して **[オフ]** に設定し、製品ライセンスのこのサービスを無効にします。 **[Assignment options (割り当てオプション)]** の下部にある **[OK]** をクリックして確定します。

   ![割り当てオプション](./media/licensing-groups-assign/assignment-options.png)

7. 割り当てを完了するには、**[ライセンスの割り当て]** ウィンドウで、ウィンドウの下部にある **[割り当て]** をクリックします。

8. 右上隅に通知が表示され、プロセスの状態と結果が示されます。 (たとえば、グループ内の既存のライセンスが原因で) グループへの割り当てを完了できなかった場合は、通知をクリックして失敗の詳細を確認します。

これで、人事部グループのライセンス テンプレートの指定が完了しました。 開始された Azure AD のバックグラウンド プロセスによって、このグループの既存のメンバー全員が処理されます。 グループの現在の規模によっては、この初期操作に時間がかかる場合があります。 次の手順では、プロセスの終了を確認する方法と、問題の解決にさらなる注意が必要かどうかを確認する方法について説明します。

> [!NOTE]
> Azure AD の **[ユーザーとグループ]** からでも同様に割り当てを開始できます。 **[Azure Active Directory]**  >  **[ユーザーとグループ]**  >  **[すべてのグループ]** に移動します。 次にグループを検索して選択し、**[ライセンス]** タブに移動します。ウィンドウの上部にある **[割り当て]** をクリックすると、ライセンス割り当てのウィンドウが開きます。

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>手順 2. 最初の割り当てが終了したことの確認

1. **[Azure Active Directory]**  >  **[ユーザーとグループ]**  >  **[すべてのグループ]** に移動します。 ライセンスを割り当てた "**人事部**" グループを見つけます。

2. **人事部**グループのウィンドウで **[ライセンス]** を選択します。 これで、ライセンスがユーザーに完全に割り当てられていることと、調査が必要なエラーの有無をすばやく確認できます。 利用可能な情報は以下のとおりです。

   - グループに現在割り当てられている製品ライセンスの一覧。 エントリを選択すると、有効にした特定のサービスを表示して変更を行えます。

   - グループに加えられた最新のライセンス変更の状態 (変更が処理中の場合、またはすべてのユーザー メンバーの処理が完了している場合)。

   - ライセンスを割り当てられなかったため、エラー状態にあるユーザーに関する情報。

   ![割り当てオプション](./media/licensing-groups-assign/assignment-errors.png)

3. ライセンスの処理に関する詳細情報は、**[Azure Active Directory]**  >  **[ユーザーとグループ]**  >  "*グループ名*"  >  **[監査ログ]** の順に移動して入手できます。 次のアクティビティに注意してください。

   - アクティビティ: **ユーザーに対するグループ ベースのライセンスの適用を開始する**。 このログは、システムによってグループでのライセンス割り当ての変更が検出され、すべてのユーザー メンバーに対するその変更の適用が開始されたときに、記録されます。 ここには、行われた変更の情報が含まれます。

   - アクティビティ: **ユーザーに対するグループ ベースのライセンスの適用を終了する**。 このログは、システムによるグループ内のユーザー全員の処理が完了したときに記録されます。 ここには、処理が正常に完了したユーザーの数とグループ ライセンスを割り当てることができなかったユーザーの数の概要が含まれます。

   [このセクション](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity)では、グループ ベースのライセンスによって行われた変更を、監査ログを使用して分析する方法を説明しています。

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>手順 3. ライセンスに関する問題のチェックと解決

1. **[Azure Active Directory]**  >  **[ユーザーとグループ]**  >  **[すべてのグループ]** の順に移動して、ライセンスを割り当てた "**人事部**" グループを見つけます。
2. **人事部**グループのウィンドウで **[ライセンス]** を選択します。 ウィンドウの上部の通知で、ライセンスを割り当てられなかったユーザーが 10 人いることが示されます。 これをクリックすると、このグループでライセンスがエラー状態である全ユーザーの一覧が表示されます。
3. **[割り当ての失敗数]** 列から、どちらの製品ライセンスもユーザーに割り当てられなかったことがわかります。 **[エラーの最も大きな原因]** 列には、エラーの原因が記載されています。 この例では **[競合するサービス プラン]** です。

   ![失敗した割り当て](./media/licensing-groups-assign/failed-assignments.png)

4. **[ライセンス]** ウィンドウを開くユーザーを選択します。 このウィンドウには、ユーザーに現在割り当てられているすべてのライセンスが表示されます。 この例では、このユーザーは**キオスク ユーザー** グループから継承された Office 365 Enterprise E1 のライセンスを持っています。 これは、システムが**人事部**グループから適用しようとした E3 ライセンスと競合しています。 結果的にこのユーザーには、そのグループからどのライセンスも割り当てられていません。

   ![ユーザーのライセンスの表示](./media/licensing-groups-assign/user-license-view.png)

5. この競合を解消するには、**キオスク ユーザー** グループからこのユーザーを削除します。 Azure AD によって変更が処理された後、**人事部**のライセンスは正常に割り当てられます。

   ![正常に割り当てられたライセンス](./media/licensing-groups-assign/license-correctly-assigned.png)

## <a name="next-steps"></a>次の手順

グループを使用したライセンス管理の機能セットについては、以下の記事をご覧ください。

* [What is group-based licensing in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md) (Azure Active Directory のグループベースのライセンスとは)
* [Azure Active Directory のグループのライセンスに関する問題の特定と解決](licensing-groups-resolve-problems.md)
* [Azure Active Directory で個別にライセンスを付与されたユーザーをグループベースのライセンスに移行する方法](licensing-groups-migrate-users.md)
* [Azure Active Directory グループベース ライセンスのその他のシナリオ](../active-directory-licensing-group-advanced.md)
