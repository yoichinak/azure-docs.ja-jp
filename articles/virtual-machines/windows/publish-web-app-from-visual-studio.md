---
title: Visual Studio から Azure VM に Web アプリを発行する | Microsoft Docs
description: Visual Studio から Azure 仮想マシンへ ASP.NET Web アプリケーションを発行する
services: virtual-machines-windows
author: ghogen
manager: douge
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 561de3ac9073fe5cfdfadf2dc61d3f7807bf9e46
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42143802"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Visual Studio から Azure 仮想マシンへ ASP.NET Web アプリケーションを発行する

このドキュメントでは、Visual Studio 2017 の **Microsoft Azure Virtual Machines** 発行機能を使用して、Azure 仮想マシン (VM) へ ASP.NET Web アプリケーションを発行する方法について説明します。  

## <a name="prerequisites"></a>前提条件
Azure VM へ ASP.NET プロジェクトを発行するには、Visual Studio を使用するために VM を正しくセットアップする必要があります。

- コンピューターは、ASP.NET Web アプリケーションを実行するよう構成し、WebDeploy をインストールする必要があります。

- VM に DNS 名が構成されている必要があります。 詳細については、「[Azure Portal で Windows VM の完全修飾ドメイン名を作成する](portal-create-fqdn.md)」を参照してください。

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Visual Studio を使用して Azure VM に ASP.NET Web アプリを発行します。
次のセクションでは、Azure の仮想マシンに既存の ASP.NET Web アプリケーションを発行する方法について説明します。

1. Visual Studio 2017 で Web アプリケーション ソリューションを開きます。
2. ソリューション エクスプローラーでプロジェクトを右クリックし、**[発行]** を選択します。
3. ページの右側の矢印を使用して、**Microsoft Azure Virtual Machines** が表示されるまで発行オプションをスクロールします。  

   ![[発行] ページ - 右矢印]

4. **Microsoft Azure Virtual Machines** アイコンを選択し、**[発行]** を選択します。

   ![[発行] ページ - Microsoft Azure Virtual Machine Services アイコン]

5. (仮想マシンに接続されている Azure サブスクリプション) の適切なアカウントを選択します。  
   - Visual Studio にサインインしている場合は、アカウントの一覧に、認証されたすべてのアカウントが表示されます。  
   - サインインしていない場合、または必要なアカウントが表示されない場合は、[アカウントを追加] を選択して画面の表示に従ってログインします。  
   ![Azure アカウント セレクター]  

6. 既存の仮想マシンの一覧から適切な VM を選択します。

   > [!Note]
   > この一覧に表示されるまで、時間がかかることがあります。

   ![Azure VM セレクター]

7. 発行を開始するには、[OK] をクリックします。

8. 資格情報が表示されたら、発行権限 (通常は VM の作成時に使用された管理者のユーザー名とパスワード) で構成されたターゲット VM 上のユーザー アカウントのユーザー名とパスワードを入力します。  

   ![WebDeploy ログイン]

9. セキュリティ証明書を適用します。

   ![証明書のエラー]

10. 発行操作の進行状況を確認するには、出力ウィンドウをご覧ください。

    ![Output Window]

11. 発行が成功した場合は、新たに発行されたサイトの URL がブラウザーで開かれます。

**成功です**

これで Web アプリを Azure の仮想マシンに正常に発行しました。

## <a name="publish-page-options"></a>発行ページのオプション

発行ウィザードを完了すると、[発行] ページが、選択した新しい発行プロファイルと共にドキュメントで開きます。

### <a name="re-publish"></a>再発行

Web アプリケーションに更新内容を発行するには、[発行] ページの **[発行]** ボタンをクリックします。  
- メッセージが表示されたら、ユーザー名とパスワードを入力します。  
- 発行はすぐに開始されます。

![[発行] ページ - [発行] ボタン]

### <a name="modify-publish-profile-settings"></a>発行プロファイル設定の変更

発行プロファイルを表示して設定を変更するには、**[設定]** を選択します。  

![[発行] ページ - [設定] ボタン]

結果は次のようになります。  

![発行設定 - [接続] ページ]

#### <a name="save-user-name-and-password"></a>ユーザー名とパスワードの保存
- 発行するたびに認証情報を入力しなくてもすむように、**[ユーザー名]** と **[パスワード]** フィールドに入力して、**[パスワードを保存する]** をオンにできます。
- 使用して、**[接続の検証]** ボタンをクリックして、適切な情報を入力したことを確認します。

#### <a name="deploy-to-clean-web-server"></a>クリーンな Web サーバーへの展開

- 毎回、Web アプリケーションのクリーンなコピーを　Web サーバーに確実にアップロードする (および過去のデプロイから残っている他のファイルがない) には、**[設定]** タブの **[発行先の追加ファイルを削除する]** チェック ボックスをオンにします。

- 警告: この設定を使用して発行すると、Web サーバー (wwwroot ディレクトリ) に存在するすべてのファイルが削除されます。 このオプションを有効にして発行する前に、必ずマシンの状態を確認してください。 

![発行設定 - 設定 ページ]

## <a name="next-steps"></a>次の手順

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Azure VM への自動展開の CI/CD を設定する

Visual Studio Team Service に継続的デリバリーのパイプラインを設定するには、「[Windows 仮想マシンへのデプロイ](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups)」を参照してください。

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[[発行] ページ - 右矢印]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[[発行] ページ - Microsoft Azure Virtual Machine Services アイコン]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Azure アカウント セレクター]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Azure VM セレクター]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[WebDeploy ログイン]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[証明書のエラー]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[出力ウィンドウ]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[発行 ページ - 発行 ボタン]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[発行 ページ - 設定 ボタン]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[発行設定 - 接続 ページ]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[発行設定 - 設定 ページ]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
