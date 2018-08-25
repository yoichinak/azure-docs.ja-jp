---
title: Azure リソース向けの Privileged Identity Management でアクセス レビューを実行する | Microsoft Docs
description: このドキュメントでは、リソースのロールに基づいて Azure リソース向けの PIM でアクセス レビューを実行する方法について説明します。
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
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 336ad1d7eff8eee0f2370e206eb98f20b4efb45b
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617226"
---
# <a name="perform-an-access-review-in-pim-according-to-resource-role"></a>リソースのロールに従って PIM でアクセス レビューを実行する
Azure リソース向けの Privileged Identity Management (PIM) は、企業が Azure 内のリソースへの特権アクセスを管理する方法を簡略化します。 

既に管理者ロールに割り当てられているユーザーは、組織の特権ロール管理者から、自分の業務にそのロールがまだ必要であるかどうかを定期的に確認するよう求められることがあります。 リンクが記載された電子メールが届く場合もあれば、直接 [Azure Portal](https://portal.azure.com)にアクセスすることもできます。 この記事に記載された手順に従って、割り当てられたロールの自己レビューを実行することができます。

特権ロール管理者としてアクセス レビューに関心がある場合は、 [アクセス レビューを開始する方法](pim-resource-roles-start-access-review.md)に関するページで詳細を確認できます。

## <a name="add-the-privileged-identity-management-application"></a>Privileged Identity Management アプリケーションの追加
[Azure Portal](https://portal.azure.com/) で Azure Active Directory (Azure AD) PIM アプリケーションを使用して、レビューを実行できます。 ポータルにアプリケーションがない場合、以下の手順を実行し開始します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Portal の右上隅に表示されているユーザー名をクリックし、操作するディレクトリを選択します。
3. **[すべてのサービス]** を選択し、**[フィルター]** ボックスを使用して *Azure AD Privileged Identity Management* を検索します。
4. **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** を選択します。 PIM アプリケーションが開きます。

## <a name="approve-or-deny-access"></a>アクセスの承認または拒否
アクセスを承認または拒否する場合、このロールをまだ使用するかどうかをレビュー担当者にのみ通知します。 ロールにとどまる場合は **[承認]** を選択します。また、アクセスが不要な場合は **[拒否]** を選択します。 状態は、レビュー担当者が結果が適用した場合にのみ変わります。

アクセス レビューを検索して完了するには、次の手順に従います。
1. Azure AD PIM アプリケーションを探します。
2. **[アクセスのレビュー]** ブレードを選択します。

   ![[アクセスのレビュー] ブレードが選択された PIM アプリケーションのスクリーンショット](media/azure-pim-resource-rbac/rbac-access-review-complete.png)

3. 完了するレビューを選択します。 
4. **[承認]** または **[拒否]** のいずれかを選択します。 **[理由の提供]** ボックスで決定の理由を含めることが必要になる場合があります。

   ![[レビューの詳細] ページのスクリーンショット](media/azure-pim-resource-rbac/rbac-access-review-choice.png)
