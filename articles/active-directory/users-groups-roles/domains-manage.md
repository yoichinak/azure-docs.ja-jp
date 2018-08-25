---
title: Azure Active Directory でのカスタム ドメイン名の管理 | Microsoft Docs
description: Azure Active Directory でのドメイン名の管理の概念と方法
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.openlocfilehash: b503ef4c5cd922d4f7b58940cfc98a83a78ae986
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449046"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Azure Active Directory のカスタム ドメイン名の管理
ドメイン名は、多くのディレクトリ リソースの識別子の重要な部分です。ユーザーのユーザー名または電子メール アドレスの一部であり、グループのアドレスの一部であり、アプリケーションのアプリ ID URI の一部になることもあります。 Azure Active Directory (Azure AD) のリソースには、リソースを格納するディレクトリによって所有されていることが既に確認済みのドメイン名を含めることができます。 Azure AD でドメイン管理タスクを実行できるのは、グローバル管理者のみです。

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Azure AD ディレクトリのプライマリ ドメイン名の設定
ディレクトリを作成すると、"contoso.onmicrosoft.com" などの初期ドメイン名がプライマリ ドメイン名に設定されます。 プライマリ ドメインは、新しいユーザーを作成したときにそのユーザーの既定のドメイン名になります。 プライマリ ドメイン名の設定によって、管理者がポータルでユーザーを新規作成するプロセスが効率化されます。 プライマリ ドメイン名を変更するには、次の手順に従います。

1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Azure Active Directory]** を選択します。
3. **[カスタム ドメイン名]** を選択します。
     
   ![ユーザー管理を開く](./media/domains-manage/add-custom-domain.png)
4. プライマリ ドメインにするドメインの名前を選びます。
5. **[プライマリにする]** コマンドを選びます。 メッセージが表示されたら、選択を確定します。
   
   ![ドメイン名をプライマリにする](./media/domains-manage/make-primary-domain.png)

ディレクトリのプライマリ ドメイン名を変更して、フェデレーションされていない検証済みカスタム ドメインを指定することができます。 ディレクトリのプライマリ ドメインを変更しても、既存のユーザーのユーザー名は変更されません。

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Azure AD テナントへのカスタム ドメイン名の追加
管理対象ドメインの名前は最大 900 件追加できます。 オンプレミス Active Directory とのフェデレーションをすべてのドメインに構成する場合、各ディレクトリに最大 450 件のドメイン名を追加できます。 詳細については、「[フェデレーション ドメイン名および管理対象ドメイン名](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names)」を参照してください。

## <a name="add-subdomains-of-a-custom-domain"></a>カスタム ドメインのサブドメインの追加
ディレクトリに europe.contoso.com などの第 3 レベル ドメイン名を追加する場合は、最初に、contoso.com などの第 2 レベル ドメインを追加して、確認する必要があります。 サブドメインは、Azure AD によって自動的に確認されます。 追加したサブドメインが確認済みであることを確かめるには、ブラウザーでドメインが一覧表示されるページの表示を更新します。

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>カスタム ドメイン名の DNS レジストラーを変更する場合にすべきこと
カスタム ドメイン名の DNS レジストラーを変更しても、中断したり追加の構成タスクを実施したりすることなく、Azure AD でそのカスタム ドメイン名を引き続き使用できます。 Office 365 や Intune など、Azure AD のカスタム ドメイン名を使用するサービスで、自社のカスタム ドメイン名を使用する場合は、各サービスのマニュアルを参照してください。

## <a name="delete-a-custom-domain-name"></a>カスタム ドメイン名を削除する
カスタム ドメイン名が使用されなくなった場合やそのドメイン名を別の Azure AD で使用する必要が生じた場合、Azure AD からドメイン名を削除することができます。

カスタム ドメイン名を削除する場合は、そのドメイン名を使用しているリソースがディレクトリ内にないことを事前に確認する必要があります。 次の状況に当てはまる場合、ディレクトリからドメイン名を削除することはできません。

* ユーザーのユーザー名、電子メール アドレス、またはプロキシ アドレスにドメイン名が含まれている。
* グループに付与された電子メール アドレスまたはプロキシ アドレスにドメイン名が含まれている。
* Azure AD 内のアプリケーションに付与されたアプリ ID URI にドメイン名が含まれている。

Azure AD ディレクトリ内にこのようなリソースがある場合は、カスタム ドメイン名を削除する前に、そのリソースを変更するか削除する必要があります。

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>PowerShell または Graph API を使用してドメイン名を管理する
Azure Active Directory のドメイン名に関する管理作業の多くは、Microsoft PowerShell を使用するか、プログラムから Azure AD Graph API を使用して行うこともできます。

* [PowerShell を使用して Azure AD のドメイン名を管理する](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Graph API を使用して Azure AD のドメイン名を管理する](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>次の手順
* [カスタム ドメイン名を追加する](../fundamentals/add-custom-domain.md)

