---
title: Azure クラウド サービスの固定仮想 IP アドレスを保持する方法 | Microsoft Docs
description: Azure クラウド サービスの仮想 IP アドレス (VIP) が変化しないようにする方法について説明します。
services: visual-studio-online
author: ghogen
manager: douge
assetId: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 2f82663f6b53c6d4e32b8d655dcbd67a321d91ed
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42146752"
---
# <a name="retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Azure クラウド サービスの固定仮想 IP アドレスを保持する
Azure でホストされているクラウド サービスを更新するとき、サービスの仮想 IP アドレス (VIP) が変更されないようにしなければならない場合があります。 ドメイン管理サービスの多くは、ドメイン ネーム システム (DNS) を使用してドメイン名の登録を行います。 DNS が正しく機能するためには、VIP が不変であることが必要です。 Azure ツールの **公開ウィザード** を使用すると、クラウド サービスを更新するときに、その VIP が変更されないようにすることができます。 Cloud Services で DNS ドメイン管理を使用する方法の詳細については、「[Azure クラウド サービスのカスタム ドメイン名の構成](cloud-services/cloud-services-custom-domain-name-portal.md)」を参照してください。

## <a name="publish-a-cloud-service-without-changing-its-vip"></a>VIP を変更せずにクラウド サービスを発行する
運用環境など、特定の環境で初めてクラウド サービスを Azure にデプロイするとき、クラウド サービスの VIP が割り当てられます。 この VIP が変更されるのは、デプロイを明示的に削除した場合またはデプロイの更新プロセスによってデプロイが暗黙的に削除された場合だけです。 VIP を維持するには、自らデプロイを削除しないようにするとともに、Visual Studio によって自動的にデプロイが削除されないようにする必要があります。 

その動作は、**公開ウィザード**に用意されている各種オプションで、デプロイの設定を指定できます。 指定できるデプロイとして新規と更新があります。更新デプロイには、増分更新と同時更新とがあります。 どちらの更新デプロイでも、VIP が維持されます。 各種デプロイの定義については、[Azure アプリケーションの公開ウィザード](vs-azure-tools-publish-azure-application-wizard.md)に関するページを参照してください。 さらに、エラーが発生した場合に、それまでのクラウド サービスのデプロイを削除するかどうかを制御できます。 そのオプションが正しく設定されていない場合、VIP が予期せず変化する可能性があります。

## <a name="update-a-cloud-service-without-changing-its-vip"></a>VIP を変更せずにクラウド サービスを更新する
1. Visual Studio で Azure クラウド サービス プロジェクトを開くか新たに作成します。 

2. **Solution Explorer** で、プロジェクト名を右クリックします。 ショートカット メニューで、**[発行]** をクリックします。

    ![[発行] メニュー](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

3. **[Azure アプリケーションの公開]** ダイアログ ボックスで、デプロイ先の Azure サブスクリプションを選択します。 必要に応じてサインインして、**[次へ]** をクリックします。

    ![[Azure アプリケーションの公開] の [サインイン] ページ](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

4. **[共通設定]** タブで、デプロイ先のクラウド サービスの名前、**[環境]**、**[ビルド構成]**、**[サービス構成]** がすべて正しいことを確認します。

    ![[Azure アプリケーションの公開] の [共通設定] タブ](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

5. **[詳細設定]** タブで、**デプロイ ラベル**と**ストレージ アカウント**が正しいことを確認します。 **[失敗時に配置を削除]** チェック ボックスがオフになっていること、**[配置の更新]** チェック ボックスがオンになっていることを確認します。 **[失敗時に配置を削除]** チェック ボックスをオフにすると、デプロイ中にエラーが発生した場合に、VIP が失われることがなくなります。 **[配置の更新]** チェック ボックスをオンにすると、アプリケーションを再発行したときに、デプロイが削除され、VIP が失われることはなくなります。 

    ![[Azure アプリケーションの公開] の [詳細設定] タブ](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

6. ロールの更新方法をさらに詳しく指定するには、**[配置の更新]** の横の **[設定]** をクリックします。 **[増分更新]** または **[同時更新]** を選択して、**[OK]** をクリックします。 **[増分更新]** を選択すると、アプリケーションのインスタンスが 1 つずつ更新されるため、アプリケーションをいつでも利用できます。 **[同時更新]** を選択すると、アプリケーションのすべてのインスタンスが同時に更新されます。 同時更新の方が高速ですが、更新処理中はサービスが利用できない可能性があります。 操作が完了したら、**[次へ]** をクリックします。

    ![[Azure アプリケーションの公開] の [デプロイ設定] ページ](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

7. **[Azure アプリケーションの公開]** ダイアログ ボックスで、**[概要]** ページが表示されるまで **[次へ]** をクリックします。 設定を確認し、**[発行]** をクリックします。
   
    ![[Azure アプリケーションの公開] の [概要] ページ](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>次の手順
- [Visual Studio の Azure アプリケーションの公開ウィザードの使用](vs-azure-tools-publish-azure-application-wizard.md)

