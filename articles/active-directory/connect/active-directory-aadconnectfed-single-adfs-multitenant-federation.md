---
title: 複数の Azure AD と単一の AD FS とのフェデレーション | Microsoft Docs
description: このドキュメントでは、複数の Azure AD と単一の AD FS とを認証連携させる方法について説明します。
keywords: フェデレーション, ADFS, AD FS, 複数のテナント, 単一の AD FS, 1 つの ADFS, マルチテナント フェデレーション, マルチ フォレスト ADFS, AAD Connect, 認証連携, クロステナント フェデレーション
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 91436af0aa26c079d5cd2cc19d16a1ff0354e860
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126594"
---
# <a name="federate-multiple-instances-of-azure-ad-with-single-instance-of-ad-fs"></a>複数の Azure AD インスタンスと単一の AD FS インスタンスとのフェデレーション

複数のフォレスト間に双方向の信頼関係があれば、単一の高可用性 AD FS ファームで、それらのフォレストを認証連携させることができます。 その複数のフォレストに、同じ Azure Active Directory との対応関係があっても、なくてもかまいません。 この記事では、異なる Azure AD に対して同期された複数のフォレストと単一の AD FS デプロイとの間にフェデレーションを構成する方法について説明しています。

![単一の AD FS とのマルチテナント フェデレーション](media/active-directory-aadconnectfed-single-adfs-multitenant-federation/concept.png)
 
> [!NOTE]
> デバイスの書き戻しとデバイスの自動参加は、このシナリオではサポートされません。

> [!NOTE]
> このシナリオのフェデレーションを構成する目的で Azure AD Connect を使用することはできません。Azure AD Connect で構成できるフェデレーションは、単一 Azure AD 内のドメインが対象です。

## <a name="steps-for-federating-ad-fs-with-multiple-azure-ad"></a>複数の Azure AD との間で AD FS を認証連携させる手順

Azure Active Directory contoso.onmicrosoft.com 内のドメイン contoso.com が、オンプレミス Active Directory 環境の contoso.com にインストールされたオンプレミスの AD FS と既に認証連携しているとします。 fabrikam.com は、fabrikam.onmicrosoft.com という Azure Active Directory 内のドメインです。

## <a name="step-1-establish-a-two-way-trust"></a>手順 1: 双方向の信頼を確立する
 
contoso.com の AD FS が fabrikam.com 内のユーザーを認証するためには、contoso.com と fabrikam.com との間に双方向の信頼が必要です。 [こちらの記事](https://technet.microsoft.com/library/cc816590.aspx)のガイドラインに従って双方向の信頼を作成してください。
 
## <a name="step-2-modify-contosocom-federation-settings"></a>手順 2: contoso.com のフェデレーション設定を編集する 
 
AD FS との間で認証連携された単一ドメインに使用される既定の発行者設定は "http://ADFSServiceFQDN/adfs/services/trust" です (例: "http://fs.contoso.com/adfs/services/trust")。 Azure Active Directory では、認証連携させるドメインごとに固有の発行者が必要となります。 同じ AD FS で 2 つのドメインのフェデレーションを行うことになるので、発行者の値は、Azure Active Directory との間で認証連携させるドメインごとに固有となるように編集する必要があります。 
 
AD FS サーバーで Azure AD PowerShell を開いて、次の手順を実行します。
 
ドメイン contoso.com を含んだ Azure Active Directory に接続 (Connect-MsolService) し、contoso.com のフェデレーション設定を更新します (Update-MsolFederatedDomain -DomainName contoso.com –SupportMultipleDomain)。
 
ドメイン フェデレーションの発行者設定が "http://contoso.com/adfs/services/trust" に変更され、Azure AD の証明書利用者信頼によって UPN サフィックスに基づく正しい issuerId 値が発行されるよう、発行要求規則が追加されます。
 
## <a name="step-3-federate-fabrikamcom-with-ad-fs"></a>手順 3: fabrikam.com と AD FS とを認証連携させる
 
Azure AD PowerShell セッションで、次の手順を実行します。まず、ドメイン fabrikam.com を含んだ Azure Active Directory に接続します。

    Connect-MsolService
fabrikam.com のマネージド ドメインをフェデレーション ドメインに変換します。

    Convert-MsolDomainToFederated -DomainName fabrikam.com -Verbose -SupportMultipleDomain
 
上記の操作によって、同じ AD FS との間で fabrikam.com ドメインが認証連携されます。 両方のドメインに Get-MsolDomainFederationSettings を使用してその設定を検証できます。

## <a name="next-steps"></a>次の手順
[Active Directory を Azure Active Directory と接続する](active-directory-aadconnect.md)
