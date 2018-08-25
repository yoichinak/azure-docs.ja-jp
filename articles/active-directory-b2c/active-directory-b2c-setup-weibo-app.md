---
title: Azure Active Directory B2C を使用して Weibo アカウントでのサインアップおよびサインインを設定する | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションで Weibo アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2534e3e39d4a6dd1659dced5a1b0342798c0049e
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969131"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Weibo アカウントでのサインアップおよびサインインを設定する

> [!NOTE]
> この機能はプレビュー段階にあります。
> 

## <a name="create-a-weibo-application"></a>Weibo アプリケーションを作成する

Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Weibo アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ Weibo アカウントを持っていない場合は、[http://weibo.com/signup/signup.php?lang=en-us](http://weibo.com/signup/signup.php?lang=en-us) で取得できます。

1. Weibo アカウントの資格情報を使用して[Weibo 開発者ポータル](http://open.weibo.com/)にサインインします。
2. サインインしたら、右上隅にある表示名を選択します。
3. ドロップダウンで **[编辑开发者信息 (開発者情報の編集)]** を選択します。
4. 必要な情報を入力し、 **[提交 (送信)]** を選択します。
5. 電子メールによる確認プロセスを完了します。
6. [ID の確認ページ](http://open.weibo.com/developers/identity/edit)に移動します。
7. 必要な情報を入力し、 **[提交 (送信)]** を選択します。

### <a name="register-a-weibo-application"></a>Weibo アプリケーションを登録する

1. [新しい Weibo アプリの登録ページ](http://open.weibo.com/apps/new)に移動します。
2. 必要なアプリケーション情報を入力します。
3. **[创建 (作成)]** を選択します。
4. **アプリ キー**と**アプリケーション シークレット**の値をコピーします。 ID プロバイダーをテナントに追加するには、これらの両方が必要です。
5. 必要な写真をアップロードし、必要な情報を入力します。
6. **[保存以上信息 (保存)]** を選択します。
7. **[高级信息 (詳細情報)]** を選択します。
8. OAuth2.0 の **[授权设置 (リダイレクト URL)]** フィールドの横の **[编辑 (編集)]** を選択します。
9. OAuth2.0 の **[授权设置 (リダイレクト URL)]** に、「`https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`」と入力します。 たとえば、`tenant_name` が contoso.onmicrosoft.com の場合、URL を `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp` に設定します。
10. **[提交 (送信)]** を選択します。  

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>ID プロバイダーとして Weibo アカウントを構成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Portal の右上隅でディレクトリを切り替えて、Azure AD B2C テナントが含まれるディレクトリを使用していることを確認してください。 サブスクリプション情報を選択し、**[ディレクトリの切り替え]** を選択します。 

    ![Azure AD B2C テナントに切り替え](./media/active-directory-b2c-setup-weibo-app/switch-directories.png)

    テナントが含まれるディレクトリを選択します。

    ![新しいディレクトリを選択する](./media/active-directory-b2c-setup-weibo-app/select-directory.png)

3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、**[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]**、**[追加]** の順に選択します。
5. **[名前]** を入力します。 たとえば、「*Weibo*」と入力します。
6. **[ID プロバイダーの種類]** を選択し、**[Weibo (Preview)]**(Weibo (プレビュー)) を選択して、**[OK]** をクリックします。
7. **[この ID プロバイダーをセットアップします]** を選択し、**[クライアント ID]** として前に記録したアプリ キーを入力し、前に作成した Weibo アプリケーションの **[クライアント シークレット]** として記録したアプリ シークレットを入力します。
8. **[OK]** をクリックし、**[作成]** をクリックして Weibo の構成を保存します。
