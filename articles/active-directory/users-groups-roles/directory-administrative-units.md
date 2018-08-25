---
title: Azure Active Directory の管理単位の管理のプレビュー
description: 管理単位を使用した Azure Active Directory での細かいレベルの権限の委任
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.component: users-groups-roles
ms.workload: identity
ms.date: 08/17/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 0884726e59d9ab3f5a5cfe7bb0608f6b5a5da250
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449022"
---
# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Azure AD の管理単位 - パブリック プレビュー
この記事では、管理単位について説明します。管理単位は、管理権限をユーザーのサブセットに委任したり、ユーザーのサブセットにポリシーを適用したりするために使用できる、Azure Active Directory の新しいリソース コンテナーです。 Azure Active Directory では、全体管理者が管理単位を使用して、各地域の管理者に権限を委任したり、細かなレベルでポリシーを設定したりできます。

これは、部門が独立している組織、たとえば、互いに独立している多数の自律的なスクール (ビジネス スクール、エンジニアリング スクールなど) で構成される大きな大学で役に立ちます。 このような部門には、その部門のアクセスの制御、ユーザーの管理、およびポリシーの設定を行う IT 管理者が存在します。 全体管理者は、これらの部門の管理者に、特定の部門のユーザーに適用される権限を与えることができます。 前の例を使用して具体的に説明すると、全体管理者は、特定のスクール (ビジネス スクール) の管理単位を作成し、その中にビジネス スクールのユーザーだけを含めることができます。 その後、全体管理者は、ビジネススクールの IT スタッフに限定されたロールを追加できます。つまり、ビジネス スクールの IT スタッフに、ビジネス スクールという管理単位にのみ適用される権限を与えます。

> [!IMPORTANT]
> 管理単位を使用するには、管理単位の対象管理者に Azure Active Directory Premium ライセンスが必要です。また、管理単位のすべてのユーザーに Azure Active Directory Basic ライセンスが必要です。 詳細については、「 [Azure AD Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。
>


全体管理者の観点から見ると、管理単位は、作成してリソースを設定できるディレクトリ オブジェクトです。 **このプレビュー リリースでは、設定できるリソースはユーザーだけです。** 管理単位を作成して設定した後、与えられた権限の適用先を管理単位に含まれているリソースに限定するスコープとして使用できます。

## <a name="managing-administrative-units"></a>管理単位の管理
このプレビュー リリースでは、Azure Active Directory Module for Windows PowerShell コマンドレットを使用して、管理単位の作成と管理を実行できます。 その方法に関する詳細については、「[管理単位の操作](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)」をご覧ください。

Azure AD モジュールのソフトウェア要件とインストールの詳細、管理単位を管理するための Azure AD モジュールのコマンドレット (構文、パラメーターの説明、例) については、「[Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)」をご覧ください。

## <a name="next-steps"></a>次の手順
[Azure Active Directory のエディション](../fundamentals/active-directory-whatis.md)
