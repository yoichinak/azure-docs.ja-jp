---
title: Azure Active Directory Identity Protection で検出される脆弱性 | Microsoft Docs
description: Azure Active Directory Identity Protection で検出される脆弱性の概要
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud Discovery, アプリケーションの管理, セキュリティ, リスク, リスク レベル, 脆弱性, セキュリティ ポリシー
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 680e52fefd8256b3ac270e8d721f27645ced49eb
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004260"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection で検出される脆弱性
脆弱性は、攻撃者によって悪用される可能性のある環境内の弱点です。 これらの脆弱性に対処して組織のセキュリティ対策を強化し、攻撃者による脆弱性の悪用を防ぐことをお勧めします。


![脆弱性](./media/vulnerabilities/101.png "vulnerabilities")



次のセクションでは、Identity Protection で報告される脆弱性の概要を示します。

## <a name="multi-factor-authentication-registration-not-configured"></a>多要素認証の登録が構成されていない
この脆弱性は、組織内の Azure Multi-Factor Authentication のデプロイの制御に役立ちます。 

Azure Multi-Factor Authentication は、ユーザー認証に対して第 2 のセキュリティ層を提供します。 シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することが可能です。 電話やテキスト メッセージ、モバイル アプリによる通知のほか、確認コードやサード パーティの OATH トークンなど、一連の簡単な照合方法を通じて確実な認証を行うことができます。

ユーザーのサインインに対して Azure Multi-Factor Authentication を要求することをお勧めします。多要素認証は、Identity Protection で使用可能なリスクに基づく条件付きアクセス ポリシーにおいて重要な役割を果たします。

詳細については、「[Azure Multi-Factor Authentication とは](../authentication/multi-factor-authentication.md)」を参照してください

## <a name="unmanaged-cloud-apps"></a>管理されていないクラウド アプリ
この脆弱性は、組織内にある管理されていないクラウド アプリの識別に役立ちます。

現代の企業では、IT 部門が、組織のユーザーが作業のために使用しているクラウド アプリケーションを部分的にしか認識できていないことがよくあります。 管理者が企業データへの不正アクセスを心配している理由は容易に理解できます。データの漏洩やその他のセキュリティ リスクが発生するおそれがあるからです。 

Cloud Discovery をデプロイしてアンマネージド クラウド アプリケーションを検出し、Azure Active Directory を使用してこれらのアプリケーションを管理することをお勧めします。

詳細については、「[Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)」を参照してください。

## <a name="security-alerts-from-privileged-identity-management"></a>Privileged Identity Management からのセキュリティ アラート
この脆弱性は、組織内の特権 ID に関するアラートを検出して解決するのに役立ちます。  

ユーザーが特権操作を実行できるよう、組織はユーザーに Azure AD、Azure や Office 365 のリソース、他の SaaS アプリへの一時的または永続的な特権アクセスを付与することが必要になる場合があります。 このような特権ユーザーにより組織の攻撃対象領域が増えます。 この脆弱性は、不要な特権アクセスを持つユーザーを特定し、それらがもたらすリスクを軽減または除去するための適切なアクションを実施するのに役立ちます。 

Azure AD Privileged Identity Management を使用して、特権 ID と、Azure AD および他の Microsoft オンライン サービス (Office 365 や Microsoft Intune など) のリソースへの特権 ID のアクセスを管理、制御、監視することをお勧めします。

詳しくは、[Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) に関するページを参照してください。 

## <a name="see-also"></a>関連項目

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

