---
title: 'Azure AD Connect: パススルー認証 | Microsoft Docs'
description: この記事では、Azure Active Directory (Azure AD) パススルー認証について説明します。パススルー認証は、オンプレミス Active Directory に対してユーザーのパスワードを検証することで Azure AD のサインインを可能にします。
services: active-directory
keywords: Azure AD Connect パススルー認証とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 12c825143f48b5558ea9b1d49ed8cea59d84f6af
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522783"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Azure Active Directory パススルー認証によるユーザー サインイン

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Azure Active Directory パススルー認証とは

Azure Active Directory (Azure AD) パススルー認証を使用すると、ユーザーは同じパスワードを使用して、オンプレミスのアプリケーションとクラウド ベースのアプリケーションの両方にサインインすることができるようになります。 この機能により、ユーザー エクスペリエンスが向上します。ユーザーは、覚えておくパスワードが 1 つ少なくなり、ユーザーがサインイン方法を忘れる可能性が低くなるため IT ヘルプデスクのコストが削減します。 この機能により、ユーザーが Azure AD を使用してサインインするとき、ユーザーのパスワードがオンプレミスの Active Directory に対して直接検証されます。

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

この機能は、組織にクラウド認証を同じメリットをもたらす、[Azure AD のパスワード ハッシュ同期](active-directory-aadconnectsync-implement-password-hash-synchronization.md)の代わりです。 ただし、自身のオンプレミスの Active Directory のセキュリティとパスワード ポリシーを適用する必要がある特定の組織は、代わりにパススルー認証を使用することを選択できます。 Azure AD の各種サインイン方法の比較および組織に合った適切なサインイン方法の選び方については、[こちらのガイド](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)をご覧ください。

![Azure AD パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

パススルー認証と[シームレス シングル サインオン](active-directory-aadconnect-sso.md)機能は組み合わせることができます。 この方法では、ユーザーが企業ネットワーク内の会社のコンピューター アプリケーションにアクセスしてサインインするときに、パスワードを入力する必要がありません。

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Azure AD パススルー認証を使用する主なメリット

- *優れたユーザー エクスペリエンス*
  - ユーザーは、オンプレミスのアプリケーションとクラウド ベースのアプリケーションの両方に同じパスワードを使用してサインインできます。
  - パスワード関連の問題を解決するのに、ユーザーが IT ヘルプデスクと対話する時間が減ります。
  - ユーザーはクラウドで[セルフ サービスによるパスワード管理](../authentication/active-directory-passwords-overview.md)ができます。
- *簡単にデプロイし、管理できる*
  - 簡単にオンプレミスにデプロイしてネットワーク構成できます。
  - オンプレミスには、軽量エージェントのみをインストールすれば済みます。
  - 管理のオーバーヘッドを排除できます。 エージェントは、機能強化とバグ修正を自動的に受け取ります。
- *セキュリティ保護*
  - オンプレミス パスワードが何らかの形でクラウドに保存されることはありません。
  - エージェントは、ネットワーク内からの送信接続のみを行います。 そのため、DMZ とも呼ばれる、境界ネットワークにエージェントをインストールする必要がありません。
  - 多要素認証 (MFA) を含む、[Azure AD 条件付きアクセス ポリシー](../active-directory-conditional-access-azure-portal.md)と[レガシ認証のブロック](../conditional-access/conditions.md)、[フィルター処理によるブルート フォース パスワード攻撃の除外](../authentication/howto-password-smart-lockout.md)により、作業を中断されずに、ユーザー アカウントを保護できます。
- *高可用性*
  - 追加のエージェントを複数のオンプレミス サーバーにインストールすることで、サインイン要求の高可用性を実現できます。

## <a name="feature-highlights"></a>機能概要

- ユーザーが[先進認証](https://aka.ms/modernauthga)を使用する、すべての Web ブラウザー ベースのアプリケーションおよび Microsoft Office クライアント アプリケーションにサインインすることがサポートされています。
- サインイン ユーザー名には、オンプレミスの既定のユーザー名 (`userPrincipalName`) または Azure AD Connect で構成された別の属性 (`Alternate ID` と呼ばれます) を指定できます。
- この機能は、多要素認証 (MFA) など、ユーザーを保護する[条件付きアクセス](../active-directory-conditional-access-azure-portal.md)とシームレスに連携します。
- クラウド ベースの[セルフサービスのパスワード管理](../authentication/active-directory-passwords-overview.md) (オンプレミスの Active Directory へのパスワード ライトバックや、よく使用されているパスワードの禁止によるパスワードの保護を含む) と統合されています。
- ご使用の AD フォレスト間にフォレストの信頼があり、名前サフィックス ルーティングが正しく構成されていれば、複数フォレスト環境がサポートされます。
- これは無料の機能であり、この機能を使用するために Azure AD の有料エディションは不要です。
- これは、[Azure AD Connect](active-directory-aadconnect.md) を使用して有効にすることができます。
- これでは、パスワード検証要求を待ち受けて応答する、軽量オンプレミス エージェントを使用します。
- 複数のエージェントをインストールすることにより、サインイン要求の高可用性が実現されます。
- これにより、オンプレミス アカウントがクラウドへのブルート フォース パスワード攻撃から[保護](../authentication/howto-password-smart-lockout.md)されます。

## <a name="next-steps"></a>次の手順

- [クイック スタート](active-directory-aadconnect-pass-through-authentication-quick-start.md) - Azure AD パススルー認証を起動および実行します。
- [AD FS からパススルー認証への移行](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx) - AD FS (または他のフェデレーション テクノロジ) からパススルー認証に移行するための詳細なガイドです。
- [スマート ロックアウト](../authentication/howto-password-smart-lockout.md) - ユーザー アカウントを保護するようにテナントのスマート ロックアウト機能を構成します。
- [現在の制限](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - サポートされているシナリオと、サポートされていないシナリオを確認します。
- [技術的な詳細](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - この機能のしくみについて学習します。
- [よく寄せられる質問](active-directory-aadconnect-pass-through-authentication-faq.md) - よく寄せられる質問とその回答です。
- [トラブルシューティング](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - この機能に関する一般的な問題を解決する方法を確認します。
- [セキュリティの詳細](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) - 機能に関する追加の詳細な技術情報です。
- [Azure AD シームレス SSO](active-directory-aadconnect-sso.md) - この補完的な機能の詳細を確認します。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要望を出します。
