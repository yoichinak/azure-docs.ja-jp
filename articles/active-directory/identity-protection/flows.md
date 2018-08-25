---
title: Azure AD Identity Protection を使用したサインイン エクスペリエンス| Microsoft Docs
description: Identity Protection でユーザーの侵害が和らげられたか修正された場合、またはポリシーにより多要素認証が要求される場合のユーザー エクスペリエンスの概要を示します。
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, アプリケーションの管理, セキュリティ, リスク, リスク レベル, 脆弱性, セキュリティ ポリシー
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 3c1c681e2c7ccd6b5fd3eaa3639853d99cb1e0b7
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005213"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Azure AD Identity Protection を使用したサインイン エクスペリエンス
Azure Active Directory Identity Protection を使用すると、次の操作を行うことができます。

* 多要素認証での登録をユーザーに要求する
* リスクの高いサインインと侵害されたユーザーを処理する

ユーザー名とパスワードを入力して直接サインインすることは不可能になるため、こうした問題に対するシステムの応答はユーザーのサインイン操作に影響を及ぼします。 ユーザーが安全に業務に戻ることができるようにする追加のステップが必須になります。

この記事では、発生する可能性があるすべてのケースについて、ユーザーのサインイン エクスペリエンスの概要を示します。

**多要素認証**

* 多要素認証の登録

**リスクのあるサインイン**

* リスクの高いサインインの復旧
* ブロックされたリスクの高いサインイン
* リスクの高いサインインの間の多要素認証の登録

**リスクのあるユーザー**

* 侵害されたアカウントの復旧
* ブロック済みの侵害されたアカウント

## <a name="multi-factor-authentication-registration"></a>多要素認証の登録
侵害されたアカウントの復旧フローでも、リスクの高いサインインのフローでも、最善のユーザー エクスペリエンスは、ユーザーが自分で復旧できる場合です。 ユーザーは、多要素認証で登録された場合、セキュリティ チャレンジを解くために使用できる電話番号が既にアカウントに関連付けられています。 アカウントの侵害から復旧するために、ヘルプ デスクまたは管理者の介入は必要ありません。 したがって、ユーザーを多要素認証に登録させることを強くお勧めします。 

管理者は、追加のセキュリティ検証用にアカウントを設定するようユーザーに要求するポリシーを設定できます。 このポリシーでは、ユーザーは多要素認証の登録を最大で 14 日間省略できます。 14 日の猶予期間は構成できません。

**多要素認証の登録には 3 つのステップがあります。**

1. 最初の手順では、ユーザーはアカウントを多要素認証に設定するように求める通知を受け取ります。 
   
    ![修復](./media/flows/140.png "Remediation")
2. 多要素認証を設定するには、システムに自分への連絡方法を設定する必要があります。
   
    ![修復](./media/flows/141.png "Remediation")
3. システムがチャレンジを送信するので、対応します。
   
    ![修復](./media/flows/142.png "Remediation")

## <a name="risky-sign-in-recovery"></a>リスクの高いサインインの復旧
管理者がサインイン リスクのポリシーを構成してある場合、影響を受けたユーザーはサインインを試みると通知を受け取ります。 

**高リスク サインインのフローには 2 つのステップがあります。** 

1. ユーザーは、新しい場所、デバイス、アプリからのサインインなど、サインインに関して異常が検出されたことの通知を受け取ります。 
   
    ![修復](./media/flows/120.png "Remediation")
2. ユーザーは、セキュリティ チャレンジを解くことによって自分の ID を証明するように要求されます。 多要素認証に登録しているユーザーは、電話番号にセキュリティ コードを折り返す必要があります。 これは単にリスクの高いサインインであり、侵害されたアカウントではないので、このフローではユーザーがパスワードを変更する必要はありません。 
   
    ![修復](./media/flows/121.png "Remediation")

## <a name="risky-sign-in-blocked"></a>ブロックされたリスクの高いサインイン
管理者は、サインイン リスク ポリシーを設定し、リスク レベルに応じてサインイン時にユーザーをブロックすることもできます。 ブロックを解除するには、エンドユーザーは管理者またはヘルプ デスクに連絡する必要があります。または、既知の場所またはデバイスからサインインを試みることもできます。 この場合、多要素認証を解決することによる自己復旧は利用できません。

![修復](./media/flows/200.png "Remediation")

## <a name="compromised-account-recovery"></a>侵害されたアカウントの復旧
ユーザーのリスク セキュリティ ポリシーが構成されている場合、ポリシーで指定されているユーザー リスク レベルを満たす (したがって侵害されているものと考えられる) ユーザーは、ユーザー侵害復旧フローを通過してからでないと、サインインできません。 

**ユーザー侵害復旧フローには、3 つのステップがあります。**

1. 不審なアクティビティまたは漏洩した資格情報のためにアカウントのセキュリティにリスクがあることが、ユーザーに通知されます。
   
    ![修復](./media/flows/101.png "Remediation")
2. ユーザーは、セキュリティ チャレンジを解くことによって自分の ID を証明するように要求されます。 ユーザーが多要素認証に登録されている場合、ユーザーは侵害状態から自力で復旧できます。 ユーザーは、セキュリティ コードを電話番号に折り返させる必要があります。 
   
   ![修復](./media/flows/110.png "Remediation")
3. 最後に、誰かがアカウントにアクセスした可能性があるため、ユーザーはパスワードの変更を強制されます。 
   このエクスペリエンスのスクリーンショットは次のとおりです。
   
   ![修復](./media/flows/111.png "Remediation")

## <a name="compromised-account-blocked"></a>ブロック済みの侵害されたアカウント
ユーザーのリスク セキュリティ ポリシーによってブロックされたユーザーをブロック解除するには、ユーザーが管理者またはヘルプ デスクに連絡する必要があります。 この場合、多要素認証を解決することによる自己復旧は利用できません。

![修復](./media/flows/104.png "Remediation")

## <a name="reset-password"></a>[パスワードのリセット]
侵害されたユーザーがサインインからブロックされている場合、管理者はそのユーザー用に一時的なパスワードを生成できます。 ユーザーは、次にサインインするときにパスワードを変更する必要があります。

![修復](./media/flows/160.png "Remediation")

## <a name="see-also"></a>関連項目
* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) 

