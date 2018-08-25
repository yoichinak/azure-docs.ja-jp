---
title: Azure Active Directory ポータルのリスクの高いサインイン レポート | Microsoft Docs
description: Azure Active Directory ポータルのリスクの高いサインイン レポートについて説明します。
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/14/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 4546734cd1b5bf2f4aaddc6477310128c9e62d51
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "42142142"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルのリスクの高いサインイン レポート

Azure Active Directory (Azure AD) のセキュリティ レポートでは、環境内でユーザー アカウントが侵害されている確率を調べることができます。 

Azure AD は、ユーザー アカウントに関連する疑わしい動作を検出します。 検出された動作ごとに、"*リスク イベント*" と呼ばれるレコードが作成されます。 詳細については、「[Azure Active Directory risk events (Azure Active Directory リスク イベント)](concept-risk-events.md)」を参照してください。 

検出されたリスク イベントは、以下のものの計算に使用されます。

- **リスクの高いサインイン** - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 詳細については、「[Risky sign-ins (リスクの高いサインイン)](../identity-protection/overview.md#risky-sign-ins)」を参照してください。 

- **リスクのフラグ付きユーザー** - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。 詳細については、「[Users flagged for risk (リスクのフラグ付きユーザー)](../identity-protection/overview.md#users-flagged-for-risk)」を参照してください。  

[Azure Portal](https://portal.azure.com) では、**[Azure Active Directory]** ブレードの **[セキュリティ]** セクションで、セキュリティ レポートを確認できます。 

![リスクの高いサインイン](./media/concept-risky-sign-ins/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>セキュリティ レポートにアクセスするために必要な Azure AD ライセンス  

"危険なサインイン" レポートは、Azure Active Directory の全エディションで利用できます。  
ただしエディションによってレポートの粒度が異なります。 

- 危険なサインインは、**Azure Active Directory の Free エディションと Basic エディション**でも一覧表示できます。 

- **Azure Active Directory Premium 1** エディションではこのモデルが拡張され、各レポートについて検出された、基になるリスク イベントの一部を調べることができます。 

- **Azure Active Directory Premium 2** エディションでは、基になるすべてのリスク イベントについて最も詳しい情報が得られます。また、構成されているリスク レベルに対して自動的に対応するセキュリティ ポリシーを構成することができます。



## <a name="azure-active-directory-free-and-basic-edition"></a>Azure Active Directory の Free および Basic エディション

Azure Active Directory の Free および Basic エディションでは、管理対象ユーザーに関して検出された、リスクの高いサインインの一覧を提供します。 このレポートに表示される内容は次のとおりです。

- **ユーザー** - サインイン操作中に使用されたユーザーの名前
- **IP** - Azure Active Directory への接続に使用されたデバイスの IP アドレス
- **場所** - Azure Active Directory への接続に使用された場所
- **サインイン時刻** - サインインが実行された時刻
- **状態** - サインインの状態


![リスクの高いサインイン](./media/concept-risky-sign-ins/01.png)

リスクの高いサインインの調査に基づき、次の対処方法の形式で Azure Active Directory にフィードバックを提出できます。

- 解決
- 誤検知に設定
- 無視
- 再度有効にする

![リスクの高いサインイン](./media/concept-risky-sign-ins/21.png)

詳細については、「[リスク イベントの手動クローズ](../identity-protection/overview.md#closing-risk-events-manually)」を参照してください。

このレポートから次の操作を行うことができます。

- リソースの検索
- レポート データのダウンロード


![リスクの高いサインイン](./media/concept-risky-sign-ins/93.png)


## <a name="azure-active-directory-premium-editions"></a>Azure Active Directory Premium エディション

Azure Active Directory Premium エディションのリスクの高いサインイン レポートで提供される情報を以下に示します。

- 検出された[リスク イベントの種類](concept-risk-events.md)に関する集計情報

- レポートをダウンロードするオプション


![リスクの高いサインイン](./media/concept-risky-sign-ins/456.png)


リスク イベントを選択すると、そのリスク イベントの詳細なレポート ビューが表示されます。ここから次の機能を利用できます。

- [ユーザー リスク修復ポリシー](../identity-protection/overview.md#user-risk-security-policy)を構成するオプション  

- リスク イベントの検出のタイムラインを確認する  

- このリスク イベントが検出されたユーザーの一覧を確認する

- [リスク イベントを手動で閉じる](../identity-protection/overview.md#closing-risk-events-manually)、または手動で終了したリスク イベントを再アクティブ化する 


![リスクの高いサインイン](./media/concept-risky-sign-ins/457.png)

ユーザーを選択すると、そのユーザーの詳細なレポート ビューが表示されます。ここから次の機能を利用できます。

- [All sign-ins (すべてのサインイン)] ビューを開く

- ユーザーのパスワードをリセットする

- すべてのイベントを閉じる

- そのユーザーについて報告されたリスク イベントを調査する 


![リスクの高いサインイン](./media/concept-risky-sign-ins/324.png)


リスク イベントを調査するには、一覧からリスク イベントを 1 つ選択します。  
このリスク イベントの **[詳細]** ブレードが開きます。 **[詳細]** ブレードで、[リスク イベントを手動で閉じる](../identity-protection/overview.md#closing-risk-events-manually)か、手動で閉じたリスク イベントを再アクティブ化することができます。 


![リスクの高いサインイン](./media/concept-risky-sign-ins/325.png)





## <a name="next-steps"></a>次の手順

- Azure Active Directory Identity Protection の詳細については、「[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)」を参照してください。

