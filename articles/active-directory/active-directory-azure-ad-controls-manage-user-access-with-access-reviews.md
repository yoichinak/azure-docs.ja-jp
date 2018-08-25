---
title: Azure AD のアクセス レビューでユーザー アクセスを管理する | Microsoft Docs
description: Azure Active Directory のアクセス レビューを使用して、グループのメンバーシップやアプリケーションへの割り当てとしてのユーザー アクセスを管理する方法について説明します
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 7b4d3baac791012e18e65c9e2e42272410e73fc1
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621659"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Azure AD のアクセス レビューでユーザー アクセスを管理する

Azure Active Directory (Azure AD) を使用すると、ユーザーに適切なアクセス権を確実かつ容易に設定できます。 ユーザー自身または意思決定者に対し、アクセス レビューに参加してユーザーのアクセス権を再確認 (証明) するよう求めることができます。 レビュー担当者は、Azure AD からの提案に基づいて、各ユーザーの継続的なアクセスのニーズを評価できます。 アクセス レビューが完了したら、変更を加え、不要になったアクセス権をユーザーから削除できます。

> [!NOTE]
> すべての種類のユーザーのアクセス権を確認する必要はなく、ゲスト ユーザーのアクセス権のみを確認する場合は、[アクセス レビューによるゲスト ユーザー アクセスの管理](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)に関するページをご覧ください。 グローバル管理者などの管理者ロールのユーザーのメンバーシップを確認する場合は、[Azure AD Privileged Identity Management でアクセス レビューを開始する方法](privileged-identity-management/pim-how-to-start-security-review.md)に関するページをご覧ください。 
>
>

## <a name="prerequisites"></a>前提条件 


アクセス レビューは、Microsoft Enterprise Mobility + Security E5 に含まれる Premium P2 エディションの Azure AD でご利用いただけます。 詳細については、「 [Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。 この機能 (レビューの作成、レビューへの入力、自分のアクセスの確認などを含む) を操作する各ユーザーにはライセンスが必要です。 

## <a name="create-and-perform-an-access-review"></a>アクセス レビューの作成と実行

1 人または複数のユーザーをアクセス レビューのレビュー担当者にできます。  

1. Azure AD でメンバーがいるグループを選択します。 または、Azure AD に接続され、1 人以上のユーザーが割り当てられているアプリケーションを選択します。 

2. 各ユーザーが自分自身のアクセスを確認するか、1 人以上のユーザーがすべてのユーザーのアクセスを確認するかを決定します。

3. レビュー担当者のアクセス パネルにアクセス レビューが表示されるようにします。 グローバル管理者またはユーザー アカウント管理者として[アクセス レビュー ページ](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)に移動します。

4. アクセス レビューを開始します。 詳細については、[アクセス レビューの作成](active-directory-azure-ad-controls-create-access-review.md)に関するページをご覧ください。

5. レビュー担当者に確認を依頼します。 既定では、各レビュー担当者には、アクセス パネルへのリンクが記載されたメールが Azure AD から届き、そこで[アクセス レビューを実行](active-directory-azure-ad-controls-perform-access-review.md)します。

6. レビュー担当者がまだ入力していない場合は、そのレビュー担当者に通知を送信するよう Azure AD を設定できます。 既定では、終了日まであと半分になった時点で、Azure AD はまだ応答していないレビュー担当者に自動的に通知が送信されます。

7. レビュー担当者が入力したら、アクセス レビューを停止し、変更を適用します。 詳細については、[アクセス レビューの完了](active-directory-azure-ad-controls-complete-access-review.md)に関するページをご覧ください。


## <a name="next-steps"></a>次の手順

[グループのメンバーまたはアプリケーションへのアクセスのアクセス レビューを作成する](active-directory-azure-ad-controls-create-access-review.md)




