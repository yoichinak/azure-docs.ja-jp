---
title: Azure サブスクリプションを Azure Active Directory B2C にリンクする方法 | Microsoft Docs
description: Azure AD B2C テナントの Azure サブスクリプションへの課金を有効にするためのステップ バイ ステップ ガイドです。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/05/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 80ba42d7eab1726c7add6c4c9426b7dde3b55480
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445943"
---
# <a name="linking-an-azure-subscription-to-an-azure-ad-b2c-tenant"></a>Azure サブスクリプションを Azure AD B2C テナントにリンクする

> [!IMPORTANT]
> Azure AD B2C の使用量の課金と価格に関する最新情報については、「[Azure Active Directory B2C の価格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)」をご覧ください。

Azure AD B2C の利用料金は、Azure サブスクリプションに課金されます。 Azure AD B2C テナントを作成したら、テナント管理者はその Azure AD B2C テナントを Azure サブスクリプションに明示的にリンクする必要があります。 この記事では、その方法について説明します。

> [!NOTE]
> Azure AD B2C テナントにリンクされたサブスクリプションは、Azure AD B2C の使用量の課金にのみ使用できます。 このサブスクリプションは、"*Azure AD B2C テナント内*" における他の Azure サービスまたは Office 365 ライセンスの追加には使用できません。

 サブスクリプションをリンクするには、ターゲットの Azure サブスクリプション内に Azure AD B2C "リソース" を作成します。 多くの Azure AD B2C "リソース" は、他の Azure リソース (VM、データ ストレージ、LogicApps など) と共に単一の Azure サブスクリプション内で作成できます。 サブスクリプション内のすべてのリソースは、サブスクリプションが関連付けられた Azure AD テナントに移動することで確認できます。

続行するには、有効な Azure サブスクリプションが必要です。

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを作成する

最初に、サブスクリプションのリンク先となる [Azure AD B2C テナントを作成する](active-directory-b2c-get-started.md)必要があります。 Azure AD B2C テナントを既に作成している場合、この手順はスキップしてください。

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Azure サブスクリプションを表示する Azure AD テナントで Azure Portal を開く

Azure サブスクリプションを表示する Azure AD テナントに移動します。 [Azure Portal](https://portal.azure.com) を開き、使用したい Azure サブスクリプションを表示する Azure AD テナントに切り替えます。

![Azure AD テナントへの切り替え](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Azure Marketplace で Azure AD B2C を見つける

**[リソースの作成]** をクリックします。 **[Marketplace を検索]** フィールドに「`B2C`」と入力します。

![強調表示された [追加] ボタンと [Marketplace を検索] フィールド内のテキスト "Azure AD B2C"](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

結果リストで **[Azure AD B2C]** を選択します。

![結果リストで選択された Azure AD B2C](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Azure AD B2C の詳細が表示されます。 新しい Azure Active Directory B2C テナントの構成を開始するには、**[作成]** をクリックします。

リソースの作成画面で、**[既存の Azure AD B2C テナントを Azure サブスクリプションにリンクする]** を選択します。

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Azure サブスクリプション内に Azure AD B2C リソースを作成する

リソースの作成ダイアログのドロップダウンから、Azure AD B2C テナントを選択します。 自分がグローバル管理者であるテナントと、サブスクリプションにまだリンクされていないテナントをすべて確認します。

Azure AD B2C リソース名は、Azure AD B2C テナントのドメイン名に一致するようあらかじめ選択されます。

[サブスクリプション] で、自分が管理者であるアクティブな Azure サブスクリプションを選択します。

[リソース グループ] と [リソース グループの場所] を選択します。 この選択は、Azure AD B2C テナントの場所、パフォーマンス、または課金状態に影響しません。

![B2C リソースの作成](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Azure AD B2C テナント リソースを管理する

Azure サブスクリプション内に Azure AD B2C リソースが正常に作成されたら、種類 "B2C テナント" の新しいリソースが他の Azure リソースの隣に追加されたことがわかります。

このリソースは、以下に使用できます。

- サブスクリプションに移動して課金情報を表示する。
- Azure AD B2C テナントに移動する
- サポート リクエストを送信します
- Azure AD B2C テナント リソースを他の Azure サブスクリプション、または他のリソース グループに移動する。

![B2C リソース設定](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>既知の問題

### <a name="csp-subscriptions"></a>CSP サブスクリプション

現時点では、Azure AD B2C テナントは CSP サブスクリプションにリンク**できません**。

### <a name="self-imposed-restrictions"></a>自己制限

ユーザーが Azure リソースの作成に関してリージョンの制限を定めていることがあります。 この制限によって、Azure AD B2C リソースの作成が妨げられる場合があります。 このような状況に対処するには、この制限を緩和してください。

## <a name="next-steps"></a>次の手順

Azure AD B2C テナントごとにこれらの手順を完了したら、Azure Direct または Enterprise Agreement の詳細に従って Azure サブスクリプションが課金されます。

選択した Azure サブスクリプション内の使用量と課金の詳細を確認できます。 また、[使用状況レポート API](active-directory-b2c-reference-usage-reporting-api.md) を使用して、日々の詳細な使用状況レポートを確認することもできます。
