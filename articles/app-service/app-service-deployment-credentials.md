---
title: Azure App Service のデプロイ資格情報 | Microsoft Docs
description: Azure App Service のデプロイ資格情報を使用する方法について説明します。
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/05/2016
ms.author: dariagrigoriu
ms.openlocfilehash: a17260770f0b2e0a73585ce4108bd5625ac22229
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436150"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Azure App Service のデプロイ資格情報の構成
[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) では、[ローカル Git デプロイ](app-service-deploy-local-git.md)と [FTP/S デプロイ](app-service-deploy-ftp.md)デプロイ用の 2 種類の資格情報をサポートしています。 これらは Azure Active Directory の資格情報とは異なります。

* **ユーザー レベルの資格情報**: Azure アカウント全体の資格情報セットです。 これを使用して、Azure アカウントがアクセス許可を持っているすべてのアプリをサブスクリプションに関係なく App Service にデプロイできます。 これは、**[App Services]**  >  **&lt;app_name >**  >  **[デプロイ資格情報]** をクリックして構成する既定の資格情報セットです。 ポータルの GUI (アプリの[リソース ページ](../azure-resource-manager/resource-group-portal.md#manage-resources)の **[概要]** や **[プロパティ]** など) に表示される既定のセットでもあります。

    > [!NOTE]
    > Azure リソースへのアクセスがロール ベースのアクセス制御 (RBAC) または共同管理者のアクセス許可を使用して委任されている場合、アプリへのアクセス権を受け取った各 Azure ユーザーは、アクセス権が取り消されるまで、各自のユーザー レベルの資格情報を使用できます。 これらのデプロイ資格情報は、他の Azure ユーザーと共有できません。
    >
    >

* **アプリ レベルの資格情報**: アプリごとの資格情報セットです。 そのアプリのみにデプロイするために使用できます。 各アプリの資格情報は、アプリの作成時に自動的に生成され、アプリの発行プロファイルで確認できます。 この資格情報は手動で構成することはできませんが、いつでもリセットできます。

    > [!NOTE]
    > ロール ベースのアクセス制御 (RBAC) を使用して他のユーザーにこの資格情報へのアクセスを許可するには、そのユーザーを Web アプリの共同作成者以上にする必要があります。 閲覧者は発行を許可されていないため、この資格情報にアクセスできません。
    >
    >

## <a name="userscope"></a>ユーザー レベルの資格情報の設定とリセット

ユーザー レベルの資格情報は、任意のアプリの[リソース ページ](../azure-resource-manager/resource-group-portal.md#manage-resources)で構成できます。 どのアプリで構成した場合でも、これらの資格情報は、Azure アカウント内のすべてのアプリのすべてのサブスクリプションに適用されます。 

ユーザー レベルの資格情報を構成するには:

1. [Azure Portal](https://portal.azure.com) で、[App Service] > **&lt;任意のアプリ>**  >  **[デプロイ資格情報]** をクリックします。

    > [!NOTE]
    > ポータルで、[デプロイ資格情報] ページにアクセスするには、少なくとも 1 つのアプリが必要です。 ただし、[Azure CLI](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) を使用する場合、ユーザー レベルの資格情報は既存のアプリなしで構成できます。

2. ユーザー名とパスワードを入力し、**[保存]** をクリックします。

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

デプロイ資格情報を設定すると、*Git* デプロイのユーザー名がアプリの **[概要]** に表示されます。

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

さらに、*FTP* デプロイ ユーザー名が、アプリの **[プロパティ]** に表示されます。

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure では、ユーザー レベルのデプロイ パスワードを表示しません。 パスワードを忘れた場合、それを取得することはできません。 ただし、このセクションの手順に従って、資格情報をリセットできます。
>
>  

## <a name="appscope"></a>アプリ レベルの資格情報の設定とリセット
App Service 内のアプリごとに、アプリ レベルの資格情報が XML 発行プロファイルに保存されます。

アプリ レベルの資格情報を取得するには:

1. [Azure Portal](https://portal.azure.com) で、[App Service] > **&lt;任意のアプリ>**  >  **[概要]** をクリックします。

2. **[...More (...その他)]**  >  **[発行プロファイルの取得]** をクリックして、.PublishSettings ファイルのダウンロードを開始します。

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. .PublishSettings ファイルを開いて、属性が `publishMethod="FTP"`である `<publishProfile>` タグを見つけます。 次に、`userName` 属性と `password` 属性を取得します。
これらがアプリ レベルの資格情報です。

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    ユーザー レベルの資格情報に似ていますが、FTP デプロイ ユーザー名の形式は `<app_name>\<username>` であり、Git デプロイ ユーザー名の形式は `<username>` であり、`<app_name>\` が先行しません。

アプリ レベルの資格情報をリセットするには:

1. [Azure Portal](https://portal.azure.com) で、[App Service] > **&lt;任意のアプリ>**  >  **[概要]** をクリックします。

2. **[...More (...その他)]**  >  **[発行プロファイルのリセット]** をクリックします。 **[はい]** をクリックして変更を確定します。

    リセット操作は、前にダウンロードしたすべての .PublishSettings ファイルを無効にします。

## <a name="next-steps"></a>次の手順

これらの資格情報を使用して、[ローカル Git](app-service-deploy-local-git.md) から、または [FTP/S](app-service-deploy-ftp.md) を使用してアプリをデプロイする方法を確認します。
