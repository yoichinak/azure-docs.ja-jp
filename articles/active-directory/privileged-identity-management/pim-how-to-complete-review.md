---
title: アクセス レビューを完了する方法 | Microsoft Docs
description: Azure AD Privileged Identity Management でのアクセス レビューの開始後に、レビューを完了して結果を確認する方法について説明します
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 3b4135368c2222a08b155c851b384244774ce246
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622532"
---
# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management でアクセス レビューを完了する方法
[アクセス レビューが開始](pim-how-to-start-security-review.md)されると、特権ロール管理者は特権アクセスの状況を確認できるようになります。 ユーザーにアクセスを確認するよう求める電子メールは Azure AD Privileged Identity Management (PIM) によって自動的に送信されます。 電子メールが届かなかったユーザーがいる場合は、[アクセス レビューを実行する方法](pim-how-to-perform-security-review.md)に関する手順を送信できます。

アクセス レビューの期間が終わった後、またはすべてのユーザーが自己レビューを完了した後に、この記事に記載されている手順に従って、レビューを管理し、結果を表示することができます。

## <a name="manage-access-reviews"></a>アクセス レビューを管理する
1. [Azure Portal](https://portal.azure.com/) に移動し、ダッシュボードで **Azure AD Privileged Identity Management** アプリケーションを選択します。
2. ダッシュボードの **[アクセス レビュー]** セクションをクリックします。
3. 管理するアクセス レビューを選択します。

アクセス レビューの [詳細] ブレードには、このレビューを管理するためのオプションが多数あります。

![PIM アクセス レビューのボタン - スクリーンショット](./media/pim-how-to-complete-review/PIM_review_buttons.png)

### <a name="remind"></a>リマインダーを送信
ユーザーが各自をレビューできるようにアクセス レビューが設定された場合、 **[リマインダーを送信]** をクリックすると通知が送信されます。 

### <a name="stop"></a>Stop
すべてのアクセス レビューには終了日が設定されていますが、 **[停止]** ボタンを使用するとレビューを早期に終了することができます。 この時点までにレビューを行っていないユーザーは、レビューが停止されるとレビューを行うことができなくなります。 レビューを停止後に再開することはできません。

### <a name="apply"></a>適用
終了日に達するか手動で停止されたためにアクセス レビューが完了した後で、 **[適用]** ボタンをクリックすると、レビューの結果が適用されます。 レビューでユーザーのアクセスが拒否された場合は、この手順によりそのユーザーのロール割り当てが削除されます。  

### <a name="export"></a>エクスポート
アクセス レビューの結果を手動で適用する場合は、レビューをエクスポートできます。 **[エクスポート]** ボタンをクリックすると、CSV ファイルのダウンロードが開始されます。 Excel など、CSV ファイルを開くことができるプログラムで結果を管理できます。

### <a name="delete"></a>削除
そのレビューが今後も必要なければ、削除します。 **[削除]** ボタンをクリックすると、レビューが PIM アプリケーションから削除されます。

> [!IMPORTANT]
> 削除する前に警告は表示されないため、そのレビューを削除してよいのか確認してください。 

## <a name="next-steps"></a>次の手順
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
