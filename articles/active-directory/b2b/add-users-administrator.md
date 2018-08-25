---
title: Azure Portal で B2B コラボレーション ユーザーを追加する - Azure Active Directory | Microsoft Docs
description: 管理者が Azure Active Directory (Azure AD) B2B コラボレーションを使用してパートナー組織からディレクトリにゲスト ユーザーを追加する方法を説明します。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 07/10/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 6dfa1f247a079bf801f28d1083c86d36a74117c5
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004200"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する

グローバル管理者、またはいずれかの制限付き管理者ディレクトリ ロールを割り当てられたユーザーとして、Azure Portal を使用して B2B コラボレーション ユーザーを招待できます。 ゲスト ユーザーをディレクトリ、グループ、またはアプリケーションに招待できます。 これらのメソッドを使用してユーザーを招待すると、招待されたユーザーのアカウントが、"*ゲスト*" というユーザー タイプで Azure Active Directory (Azure AD) に追加されます。 次に、ゲスト ユーザーは、招待に応じてリソースにアクセスします。

ゲスト ユーザーをディレクトリに追加すると、共有アプリへの直接リンクをゲスト ユーザーに送信するか、ゲスト ユーザーが招待メール内の引き換えの URL をクリックできます。 引き換えプロセスの詳細については、[B2B コラボレーションの招待の利用](redemption-experience.md)に関するページを参照してください。

> [!IMPORTANT]
> 組織のプライバシーに関する声明の URL を追加するには、「[How-to: Add your organization's privacy info in Azure Active Directory](https://aka.ms/adprivacystatement)」 (操作方法: Azure Active Directory で、組織のプライバシーに関する情報を追加する) の手順に従う必要があります。 最初の招待の利用プロセスの一環として、招待したユーザーは続行するために、プライバシー条項に同意する必要があります。 

## <a name="add-guest-users-to-the-directory"></a>ゲスト ユーザーをディレクトリに追加する

B2B コラボレーション ユーザーをディレクトリに追加するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. ナビゲーション ペインで、**[Azure Active Directory]** を選択します。
3. **[管理]** にある **[Users]** を選択します。
4. **[新しいゲスト ユーザー]** を選択します。

   ![UI 内の新しいゲスト ユーザーの場所を示す](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
5. **[ユーザー名]** に外部ユーザーの電子メール アドレスを入力します。 必要に応じて、ようこそメッセージを含めます。 例: 

   ![UI 内の新しいゲスト ユーザーの場所を示す](./media/add-users-administrator/InviteGuest.png) 

    > [!NOTE]
    > 一部の電子メール プロバイダーでは、ユーザーはプラス記号 (+) と追加テキストを電子メール アドレスに付け加えて、受信ボックスのフィルター処理などに役立てることができます。 ただし、Azure AD では現在、電子メール アドレスのプラス記号はサポートされていません。 配信の問題を回避するために、プラス記号と、それに続く @ 記号より前の任意の文字を含めません。

6. **[招待]** を選択して、招待をゲスト ユーザーに自動的に送信します。 
 
招待を送信すると、ユーザー アカウントがディレクトリにゲストとして自動的に追加されます。


![ユーザー タイプ "ゲスト" の B2B ユーザーを示す](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>ゲスト ユーザーをグループに追加する
Azure AD 管理者として、B2B コラボレーション ユーザーをグループに手動で追加するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. ナビゲーション ペインで、**[Azure Active Directory]** を選択します。
3. **[管理]** にある **[グループ]** を選択します。
4. グループを選択 (または **[新しいグループ]** をクリックしてグループを新規作成) します。 グループに B2B ゲスト ユーザーが含まれていることをグループの説明に含めることをお勧めします。
5. **[メンバー]** を選択します。 
6. 次のいずれかを実行します。
   - ゲスト ユーザーがディレクトリに既に存在する場合は、B2B ユーザーを探します。 ユーザーを選択し、**[選択]** をクリックしてユーザーをグループに追加します。
   - ゲスト ユーザーがまだディレクトリに存在しない場合は、検索ボックスにメール アドレスを入力し、必要に応じて個人のメッセージを入力し、**[選択]** をクリックしてグループに招待します。 招待が、招待されたユーザーに自動的に送信されます。
     
     ![[招待] ボタンを追加してゲスト メンバーを追加する](./media/add-users-administrator/GroupInvite.png)
   
Azure AD B2B コラボレーションを使って動的グループを使用することもできます。 詳しくは、「[動的グループと Azure Active Directory B2B コラボレーション](use-dynamic-groups.md)」を参照してください。

## <a name="add-guest-users-to-an-application"></a>ゲスト ユーザーをアプリケーションに追加する

Azure AD 管理者として B2B コラボレーション ユーザーをアプリケーションに追加するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. ナビゲーション ペインで、**[Azure Active Directory]** を選択します。
3. **[管理]** で、**[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** を選択します。
4. ゲスト ユーザーを追加するアプリケーションを選択します。
5. アプリケーションのダッシュボードで、**[総ユーザー数]** を選択して **[ユーザーとグループ**] ウィンドウを開きます。

    ![開いているユーザーとグループを追加する [総ユーザー数] ボタン](./media/add-users-administrator/AppUsersAndGroups.png)

6. **[ユーザーの追加]** を選択します。
7. **[割り当ての追加]** で **[ユーザーとグループ]** を選択します。
8. 次のいずれかを実行します。
   - ゲスト ユーザーがディレクトリに既に存在する場合は、B2B ユーザーを探します。 ユーザーを選択し、**[選択]** をクリックし、**[割り当て]** をクリックしてユーザーをアプリに追加します。
   - ゲスト ユーザーがディレクトリに存在しない場合は、**[招待]** を選択します。
           
       ![[招待] ボタンを追加してゲスト メンバーを追加する](./media/add-users-administrator/AppInviteUsers.png)
   
      **[ゲストを招待する]** で、メール アドレスを入力し、必要に応じて個人のメッセージを入力して、**[招待]** を選択します。 **[選択]** をクリックし、**[割り当て]** をクリックしてユーザーをアプリに追加します。 招待されたユーザーに招待が自動的に送信されます。

9. ゲスト ユーザーには **[既定アクセス]** ロールが割り当てられ、アプリケーションの **[ユーザーとグループ]** リストに表示されます。 ロールを変更する場合は、次の手順を実行します。
   - ゲスト ユーザーを選択し、**[編集]** を選択します。 
   - **[割り当ての編集]** で、**[ロールの選択]** をクリックし、選択したユーザーに割り当てるロールを選択します。
   - **[選択]** をクリックします。
   - **[割り当て]** をクリックします。
 
## <a name="resend-invitations-to-guest-users"></a>招待をゲスト ユーザーに再送信する

ゲスト ユーザーがまだ招待に応じていない場合、招待メールを再送信できます。

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. ナビゲーション ペインで、**[Azure Active Directory]** を選択します。
3. **[管理]** にある **[Users]** を選択します。
5. ユーザー アカウントを選択します。
6. **[管理]** で、**[プロファイル]** を選択します。
7. ユーザーがまだ招待に応じていない場合は、**[招待の再送信]** オプションを使用できます。 このボタンを選択して再送信します。

   ![ユーザー プロファイルの [招待の再送信] オプション](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> もともとユーザーを特定のアプリに招待する招待を再送信した場合、新しい招待のリンクによりユーザーは最上位レベルのアクセス パネルに移動されます。

## <a name="next-steps"></a>次の手順

- Azure 以外の AD 管理者が B2B ゲスト ユーザーを追加する方法については、「[インフォメーション ワーカーが B2B コラボレーション ユーザーを Azure Active Directory に追加する方法](add-users-information-worker.md)」をご覧ください。
- 招待メールについて詳しくは、「[B2B コラボレーションの招待メールの要素](invitation-email-elements.md)」を参照してください。

