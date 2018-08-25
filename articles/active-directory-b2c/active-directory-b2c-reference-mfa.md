---
title: Azure Active Directory B2C の Multi-Factor Authentication | Microsoft Docs
description: Azure Active Directory B2C によってセキュリティ保護されているコンシューマー向けアプリケーションで Multi-factor Authentication を有効にする方法。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3d18e1b2e45aba4e83989e29c533cfc7bf5033fc
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442710"
---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure Active Directory B2C: コンシューマー向けアプリケーションで Multi-factor Authentication を有効にする
Azure Active Directory (Azure AD) B2C は [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) と直接統合しているので、コンシューマー向けアプリケーションのサインアップおよびサインイン エクスペリエンスに第 2 のセキュリティ層を簡単に追加できます。 また、コードを 1 行も記述することなく、これを実現できます。 現時点では、電話かテキスト メッセージによる検証がサポートされています。 サインアップおよびサインイン ポリシーを既に作成していても、Multi-Factor Authentication を有効にできます。

> [!NOTE]
> Multi-Factor Authentication は、既存のポリシーを編集することによってだけでなく、サインアップ ポリシーやサインイン ポリシーの作成時に有効にすることもできます。
> 
> 

この機能を利用すると、次のようなシナリオをアプリケーションで処理するのに役立ちます。

* あるアプリケーションにアクセスするには Multi-Factor Authentication は必要ないが、別のアプリケーションにアクセスするには必要な場合。 たとえば、コンシューマーは自動車保険アプリケーションにはソーシャルまたはローカル アカウントでサインインできますが、同じディレクトリに登録されている住宅保険アプリケーションにアクセスするには事前に電話番号の確認が必要であるような場合です。
* アプリケーションへの通常のアクセスには Multi-Factor Authentication は必要ないが、アプリケーション内の機密性が高い部分へのアクセスには必要である場合。 たとえば、コンシューマーはソーシャルまたはローカル アカウントを使用して銀行取引アプリケーションにサインインし、口座の残高を確認できますが、ネット送金を行うには事前に電話番号の確認が必要になるような場合です。

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>サインアップ ポリシーを変更して Multi-factor Authentication を有効にする
1. [この手順に従って、Azure Portal で B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
2. **[サインアップ ポリシー]** をクリックします。
3. クリックしてサインアップ ポリシーを開きます (例: "B2C_1_SiUp")。
4. **[Multi-Factor Authentication]** をクリックし、**[状態]** を **[オン]** にします。 Click **OK**.
5. ブレードの上部にある **[保存]** をクリックします。

ポリシーで [今すぐ実行] 機能を使用して、コンシューマー エクスペリエンスを確認できます。 次のことを確認します。

Multi-Factor Authentication の手順が実行される前に、コンシューマー アカウントがディレクトリに作成されます。 この手順の過程で、コンシューマーは自分の電話番号を示し、確認することを求められます。 検証が成功した場合、後で使用できるように電話番号がコンシューマー アカウントに関連付けられます。 コンシューマーがキャンセルまたは中止した場合であっても、次にサインインするときに電話番号の確認を再度求められる場合があります (Multi-Factor Authentication が有効になっている場合)。

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>サインイン ポリシーを変更して Multi-factor Authentication を有効にする
1. [この手順に従って、Azure Portal で B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
2. **[サインイン ポリシー]** をクリックします。
3. クリックしてサインイン ポリシーを開きます (例: "B2C_1_SiIn")。 ブレードの上部にある **[編集]** をクリックします。
4. **[Multi-Factor Authentication]** をクリックし、**[状態]** を **[オン]** にします。 Click **OK**.
5. ブレードの上部にある **[保存]** をクリックします。

ポリシーで [今すぐ実行] 機能を使用して、コンシューマー エクスペリエンスを確認できます。 次のことを確認します。

コンシューマーがソーシャルまたはローカル アカウントを使用してサインインするとき、検証済みの電話番号がコンシューマー アカウントに関連付けられている場合は、コンシューマーはその確認を求められます。 コンシューマー アカウントに関連付けられている電話番号がない場合は、コンシューマーは電話番号を示し、確認することを求められます。 検証が成功した場合、後で使用できるように電話番号がコンシューマー アカウントに関連付けられます。

## <a name="multi-factor-authentication-on-other-policies"></a>他のポリシーでの Multi-Factor Authentication
前のサインアップ/サインイン ポリシーで説明したように、サインアップまたはサインイン ポリシーおよびパスワード リセット ポリシーで Multi-Factor Authentication を有効にすることもできます。 プロファイル編集ポリシーでもまもなく利用できるようになります。

