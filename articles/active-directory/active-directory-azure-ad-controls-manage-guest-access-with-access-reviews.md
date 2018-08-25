---
title: Azure AD のアクセス レビューによるゲスト アクセスの管理 | Microsoft Docs
description: Azure Active Directory アクセス レビューを使用し、ゲスト ユーザーをグループのメンバーとして、またはアプリケーションに割り当てられたユーザーとして管理します。
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
ms.openlocfilehash: 3f7580b39e0ce9b1b83b5bc8ee0ed897b77e030a
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620009"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Azure AD のアクセス レビューによるゲスト アクセスの管理


Azure Active Directory (Azure AD) では、組織の境界を越えたコラボレーションを [Azure AD B2B 機能](active-directory-b2b-what-is-azure-ad-b2b.md)で簡単に実現できます。 [管理者](active-directory-b2b-admin-add-users.md)または[それ以外のユーザー](active-directory-b2b-how-it-works.md)が他のテナントからゲスト ユーザーを招待できます。 Microsoft アカウントなどのソーシャル ID も、この機能の対象となります。

ゲスト ユーザーに適切なアクセス権を確保することも簡単にできます。 ゲスト自身または意思決定者に対し、アクセス レビューに参加してゲストのアクセス権を再確認 (証明) するよう求めることができます。 レビュー担当者は、Azure AD からの提案に基づいて、各ユーザーの継続的なアクセスのニーズを評価することができます。 アクセス レビューが完了したら、変更を加え、不要になったアクセス権をゲストから削除することができます。

> [!NOTE]
> このドキュメントでは、ゲスト ユーザーのアクセス レビューについて重点的に説明します。 ゲストに限らず全ユーザーのアクセス権をレビューする必要がある場合は、[アクセス レビューでユーザー アクセスを管理する方法](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)に関するページを参照してください。 グローバル管理者などの管理者ロールのユーザーのメンバーシップを確認する場合は、[Azure AD Privileged Identity Management でアクセス レビューを開始する方法](privileged-identity-management/pim-how-to-start-security-review.md)に関するページを参照してください。 
>
>

## <a name="prerequisites"></a>前提条件 


アクセス レビューは、Microsoft Enterprise Mobility + Security E5 に含まれる Premium P2 エディションの Azure AD でご利用いただけます。 詳細については、「 [Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。 この機能 (レビューの作成、レビューへの入力、自分のアクセスの確認などを含む) を操作する各ユーザーにはライセンスが必要です。 

ゲスト ユーザーに自身のアクセス権をレビューするよう依頼する場合は、ゲスト ユーザーのライセンスについて確認しておいてください。 詳細については、[Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)に関するページを参照してください。

## <a name="create-and-perform-an-access-review-for-guests"></a>ゲストのアクセス レビューの作成と実行

まず、レビュー担当者のアクセス パネルにアクセス レビューが表示されるようにします。 グローバル管理者またはユーザー アカウント管理者として[アクセス レビュー ページ](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)に移動します。 

ゲスト ユーザーのレビューに関して、Azure AD はいくつかのシナリオに対応しています。

次のいずれかを選択してください。

 - Azure AD 内のグループのメンバーとして 1 人以上のゲストが存在する。
 - Azure AD に接続済みのアプリケーションに対して 1 人以上のゲスト ユーザーが割り当てられている。 

そのうえで、各ゲストに自分自身のアクセスをレビューしてもらうか、各ゲストのアクセスを 1 人以上のユーザーにレビューしてもらうかを決めてください。

 これらの各シナリオについて以降の各セクションで説明します。

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>ゲストに自身のグループ メンバーシップをレビューするよう依頼する

アクセス レビューを使用すると、グループに招待されて追加されていたユーザーが今もアクセス権を必要としていることを確かめることができます。 ゲストに自身のグループ メンバーシップをレビューしてもらうのは簡単です。

1. グループのアクセス レビューを開始するには、ゲスト ユーザーのメンバーだけをレビュー対象とし、メンバーに自身でレビューしてもらうよう選択します。 詳細については、[アクセス レビューの作成](active-directory-azure-ad-controls-create-access-review.md)に関するページをご覧ください。

2. 各ゲストに自身のメンバーシップをレビューするよう依頼します。 既定では、招待状を受け取った各ゲストには、アクセス レビューへのリンクが記載されたメールが Azure AD から届きます。 Azure AD のゲスト向けの手順については、[アクセス権をレビューする方法](active-directory-azure-ad-controls-perform-access-review.md)に関するページを参照してください。

3. レビュー担当者が結果を入力したら、アクセス レビューを停止して変更を適用します。 詳細については、[アクセス レビューの完了](active-directory-azure-ad-controls-complete-access-review.md)に関するページをご覧ください。

4. アクセス権の継続が不要であると自分で判断したユーザーに加え、回答が得られなかったユーザーも削除した方がよいでしょう。 回答がないユーザーはもうメールを受け取っていないと考えられます。

5. グループがアクセス管理に使用されていない場合も、招待状が届かなかったことが理由でレビューの対象外となったユーザーを削除した方がよいでしょう。 招待状が届かなかった場合は、招待されたユーザーのメール アドレスに誤りがあった可能性があります。 グループが配布リストとして使用されている場合、連絡先オブジェクトであるためにレビューの対象外となったゲスト ユーザーも存在する可能性があります。

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>責任者にゲストのグループ メンバーシップをレビューするよう依頼する

ゲストのグループ メンバーシップが引き続き必要であるかどうかをレビューするよう責任者 (グループの所有者など) に依頼することができます。

1. グループのアクセス レビューを開始するには、ゲスト ユーザー メンバーだけをレビュー対象として選択します。 そのうえで、レビュー担当者を 1 人以上指定します。 詳細については、[アクセス レビューの作成](active-directory-azure-ad-controls-create-access-review.md)に関するページをご覧ください。

2. レビュー担当者に確認を依頼します。 既定では、各レビュー担当者には、アクセス パネルへのリンクが記載されたメールが Azure AD から届き、そこで[アクセス レビューを実行](active-directory-azure-ad-controls-perform-access-review.md)します。

3. レビュー担当者が入力したら、アクセス レビューを停止し、変更を適用します。 詳細については、[アクセス レビューの完了](active-directory-azure-ad-controls-complete-access-review.md)に関するページをご覧ください。

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>ゲストに自身のアプリケーション アクセスをレビューするよう依頼する

アクセス レビューを使用すると、特定のアプリケーションに招待されていたユーザーが今もアクセス権を必要としていることを確かめることができます。 アクセスの必要性をゲスト自身にレビューしてもらうのは簡単です。

1. アプリケーションのアクセス レビューを開始するには、ゲストだけをレビュー対象とし、ユーザーに自身のアクセス権をレビューしてもらうよう選択します。 詳細については、[アクセス レビューの作成](active-directory-azure-ad-controls-create-access-review.md)に関するページをご覧ください。

2. 各ゲストに自身のアプリケーション アクセスをレビューするよう依頼します。 既定では、招待状を受け取った各ゲストには、組織のアクセス パネル内のアクセス レビューへのリンクが記載されたメールが Azure AD から届きます。 Azure AD のゲスト向けの手順については、[アクセス権をレビューする方法](active-directory-azure-ad-controls-perform-access-review.md)に関するページを参照してください。

3. レビュー担当者が結果を入力したら、アクセス レビューを停止して変更を適用します。 詳細については、「[アクセス レビューの完了](active-directory-azure-ad-controls-complete-access-review.md)」を参照してください。

4. アクセス権の継続が不要であると自分で判断したユーザーに加え、回答が得られなかったゲスト ユーザーも削除した方がよいでしょう。 回答がないユーザーはもうメールを受け取っていないと考えられます。 また、レビューの対象外となったゲスト ユーザーも、特にそのゲストが最近招待されていないようであれば、削除することをお勧めします。 招待状を受け取っていないようであれば、そのようなユーザーにアプリケーションへのアクセス権はありません。 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>責任者にアプリケーションへのゲスト アクセスをレビューするよう依頼する

ゲストによるアプリケーション アクセスが引き続き必要であるかどうかをレビューするよう責任者 (アプリケーションの所有者など) に依頼することができます。

1. アプリケーションのアクセス レビューを開始するには、ゲストだけをレビュー対象として選択します。 そのうえで、レビュー担当者としてユーザーを 1 人以上指定します。 詳細については、[アクセス レビューの作成](active-directory-azure-ad-controls-create-access-review.md)に関するページをご覧ください。

2. レビュー担当者に確認を依頼します。 既定では、各レビュー担当者には、アクセス パネルへのリンクが記載されたメールが Azure AD から届き、そこで[アクセス レビューを実行](active-directory-azure-ad-controls-perform-access-review.md)します。

3. レビュー担当者が入力したら、アクセス レビューを停止し、変更を適用します。 詳細については、[アクセス レビューの完了](active-directory-azure-ad-controls-complete-access-review.md)に関するページをご覧ください。

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>ゲストに全般的なアクセスの必要性をレビューするよう依頼する

組織によっては、ゲストが自分のグループ メンバーシップを把握していないこともあります。

> [!NOTE]
> 以前のバージョンの Azure Portal では、UserType が Guest であるユーザーの管理アクセスが認められていませんでした。 ディレクトリの管理者が PowerShell を使ってゲストの UserType の値を Member に変更している場合があります。 過去にそのような変更がご利用のディレクトリで行われていた場合、前述したクエリでは、以前管理アクセス権を持っていたゲスト ユーザーが対象から漏れる場合があります。 その場合は、ゲストの UserType を変更するか、該当するゲストをグループ メンバーシップに手動で追加する必要があります。

1. ゲストをメンバーとするセキュリティ グループを Azure AD に作成します (適切なグループがまだ存在しない場合)。 たとえば、手動で管理されたゲストのメンバーシップを基にグループを作成します。 または、UserType 属性の値が Guest である Contoso テナントのユーザー用に "Guests of Contoso" などの名前の動的グループを作成してもよいでしょう。  効率を高めるために、そのグループが主にゲストで構成されるようにしてください。レビューが不要なユーザーを含むグループを選択しないようにしてください。

2. そのグループのアクセス レビューを開始するには、メンバー自身をレビュー担当者として選択します。 詳細については、[アクセス レビューの作成](active-directory-azure-ad-controls-create-access-review.md)に関するページをご覧ください。

3. 各ゲストに自身のメンバーシップをレビューするよう依頼します。 既定では、招待状を受け取った各ゲストには、組織のアクセス パネル内のアクセス レビューへのリンクが記載されたメールが Azure AD から届きます。 Azure AD のゲスト向けの手順については、[アクセス権をレビューする方法](active-directory-azure-ad-controls-perform-access-review.md)に関するページを参照してください。  招待状を受け取っていないゲストは、レビューの結果に [未通知] として表示されます。

4. レビュー担当者が結果を入力したら、アクセス レビューを停止します。 詳細については、[アクセス レビューの完了](active-directory-azure-ad-controls-complete-access-review.md)に関するページをご覧ください。

5. 拒否されたゲスト、レビューが完了していないゲスト、招待状を受け取っていないゲストについては、ゲスト アクセスを削除します。 レビューの対象として選ばれた連絡先がゲストの中に存在する場合や、ゲストが過去に招待状を受け取っていない場合は、そのアカウントは、Azure Portal または PowerShell を使って無効にしてかまいません。 そのゲストがアクセス権をもう必要としておらず、連絡先でもない場合は、ゲスト ユーザー オブジェクトを削除するために、該当するユーザー オブジェクトを Azure Portal または PowerShell を使ってディレクトリから削除してください。

## <a name="next-steps"></a>次の手順

[グループのメンバーまたはアプリケーションへのアクセスのアクセス レビューを作成する](active-directory-azure-ad-controls-create-access-review.md)







