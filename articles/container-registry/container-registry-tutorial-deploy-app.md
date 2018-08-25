---
title: Azure Container Registry チュートリアル - Azure Container Registry からの Web アプリのデプロイ
description: Linux ベースの Web アプリを、geo レプリケーション Azure Container Registry からコンテナー イメージを使用してデプロイします。 3 部構成のシリーズのパート 2。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 04/30/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 6347c460ab88929152424d301445a219720b98e8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38582303"
---
# <a name="tutorial-deploy-web-app-from-azure-container-registry"></a>チュートリアル: Azure Container Registry からの Web アプリのデプロイ

これは 3 部構成のチュートリアル シリーズの第 2 部です。 [第 1 部](container-registry-tutorial-prepare-registry.md)では、プライベートの geo レプリケーション コンテナー レジストリを作成し、ソースからコンテナー イメージを構築してレジストリにプッシュしました。 この記事では、2 つの異なる Azure リージョンの 2 つの Web アプリ インスタンスにコンテナーをデプロイすることで、ネットワーク上の近い場所で操作できるという geo レプリケーション レジストリの利点を活用します。 各インスタンスは、最も近いレジストリから、コンテナー イメージをプルします。

シリーズの第 2 部であるこのチュートリアルでは、次のことを行います。

> [!div class="checklist"]
> * コンテナー イメージを 2 つの *Web App for Containers* インスタンスにデプロイする
> * デプロイしたアプリケーションを確認する

geo レプリケーション レジストリの作成と、コンテナー化されたサンプル アプリケーションのイメージのレジストリへのプッシュをまだ行っていない場合は、シリーズの前のチュートリアル「[geo レプリケーション Azure Container Registry の準備](container-registry-tutorial-prepare-registry.md)」に戻ってください。

シリーズの次の部分では、アプリケーションを更新した後で、新しいコンテナー イメージをレジストリにプッシュします。 最後に、実行中の各 Web アプリ インスタンスを参照して、両方のインスタンスで変更が自動的に反映されていることを確認します (これは、Azure Container Registry の geo レプリケーションと webhook が機能していることを示します)。

## <a name="automatic-deployment-to-web-apps-for-containers"></a>Web App for Containers への自動デプロイ

Azure Container Registry では、コンテナー化されたアプリケーションの [Web App for Containers](../app-service/containers/index.yml) への直接デプロイがサポートされています。 このチュートリアルでは、Azure Portal を使用して、前のチュートリアルで作成したコンテナー イメージを異なる Azure リージョンにある 2 つの Web アプリ プランにデプロイします。

レジストリのコンテナー イメージから Web アプリをデプロイし、同じリージョンに geo レプリケーション レジストリがある場合、Azure Container Registry はイメージのデプロイの [webhook](container-registry-webhook.md) を自動的に作成します。 コンテナー リポジトリに新しいイメージをプッシュすると、webhook によって変更が取得され、新しいコンテナー イメージが Web アプリに自動的にデプロイされます。

## <a name="deploy-a-web-app-for-containers-instance"></a>Web App for Containers インスタンスのデプロイ

この手順では、*米国西部*リージョンに Web App for Containers インスタンスを作成します。

[Azure Portal](https://portal.azure.com) にサインインし、前のチュートリアルで作成したレジストリに移動します。

**[リポジトリ]** > **[acr-helloworld]** を選択し、**[タグ]** の下の **[v1]** タグを右クリックして **[Deploy to web app]\(Web アプリにデプロイ\)** を選択します。

![Azure Portal の [App Service へのデプロイ]][deploy-app-portal-01]

表示される **[Web App for Containers]** で、各設定に次の値を設定します。

| Setting | 値 |
|---|---|
| **サイト名** | Web アプリのグローバルに一意の名前。 この例では、Web アプリのデプロイ元のレジストリとリージョンを簡単に識別できるように、`<acrName>-westus` という形式を使用します。 |
| **リソース グループ** | **[既存のものを使用]** > `myResourceGroup` |
| **App Service プラン/場所** | **[米国西部]** リージョンに `plan-westus` という名前の新しいプランを作成します。 |
| **イメージ** | `acr-helloworld:v1`

**[作成]** を選択して、*[米国西部]* リージョンに Web アプリをプロビジョニングします。

![Azure Portal での Web app on Linux の構成][deploy-app-portal-02]

## <a name="view-the-deployed-web-app"></a>デプロイされた Web アプリの表示

デプロイが完了したら、ブラウザーでアプリケーションの URL に移動して、実行中のアプリケーションを表示できます。

ポータルで **[App Services]** を選択し、前の手順でプロビジョニングした Web アプリを選択します。 この例では、Web アプリの名前は *uniqueregistryname-westus* です。

**App Service** の [概要] の右上で Web アプリのハイパーリンク URL を選択して、実行中のアプリケーションをブラウザーで表示します。

![Azure Portal での Web app on Linux の構成][deploy-app-portal-04]

geo レプリケーション コンテナー レジストリから Docker イメージがデプロイされると、コンテナー レジストリをホストしている Azure リージョンを表すイメージがサイトに表示されます。

![ブラウザーに表示された、デプロイされた Web アプリケーション][deployed-app-westus]

## <a name="deploy-second-web-app-for-containers-instance"></a>2 つめの Web App for Containers インスタンスのデプロイ

前のセクションで説明した手順を使用して、2 つめの Web アプリを*米国東部*リージョンにデプロイします。 **[Web App for Containers]** で、次の値を指定します。

| Setting | 値 |
|---|---|
| **サイト名** | Web アプリのグローバルに一意の名前。 この例では、Web アプリのデプロイ元のレジストリとリージョンを簡単に識別できるように、`<acrName>-eastus` という形式を使用します。 |
| **リソース グループ** | **[既存のものを使用]** > `myResourceGroup` |
| **App Service プラン/場所** | **[米国東部]** リージョンに `plan-eastus` という名前の新しいプランを作成します。 |
| **イメージ** | `acr-helloworld:v1`

**[作成]** を選択して、*[米国東部]* リージョンに Web アプリをプロビジョニングします。

![Azure Portal での Web app on Linux の構成][deploy-app-portal-06]

## <a name="view-the-deployed-web-app"></a>デプロイされた Web アプリの表示

前と同様に、ブラウザーでアプリケーションの URL に移動して、実行中のアプリケーションを表示できます。

ポータルで **[App Services]** を選択し、前の手順でプロビジョニングした Web アプリを選択します。 この例では、Web アプリの名前は *uniqueregistryname-eastus* です。

**App Service の [概要]** の右上で Web アプリのハイパーリンク URL を選択して、実行中のアプリケーションをブラウザーで表示します。

![Azure Portal での Web app on Linux の構成][deploy-app-portal-07]

geo レプリケーション コンテナー レジストリから Docker イメージがデプロイされると、コンテナー レジストリをホストしている Azure リージョンを表すイメージがサイトに表示されます。

![ブラウザーに表示された、デプロイされた Web アプリケーション][deployed-app-eastus]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、geo レプリケーション Azure Container Registry から 2 つの Web App for Containers インスタンスをデプロイしました。

次のチュートリアルに進み、更新を行った後で新しいコンテナー イメージをコンテナー レジストリにデプロイして、両方のリージョンで実行中の Web アプリが自動的に更新されたことを確認します。

> [!div class="nextstepaction"]
> [geo レプリケーション コンテナー イメージへの更新のデプロイ](./container-registry-tutorial-deploy-update.md)

<!-- IMAGES -->
[deploy-app-portal-01]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-01.png
[deploy-app-portal-02]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-02.png
[deploy-app-portal-03]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-03.png
[deploy-app-portal-04]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-04.png
[deploy-app-portal-05]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-05.png
[deploy-app-portal-06]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-06.png
[deploy-app-portal-07]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-07.png
[deployed-app-westus]: ./media/container-registry-tutorial-deploy-app/deployed-app-westus.png
[deployed-app-eastus]: ./media/container-registry-tutorial-deploy-app/deployed-app-eastus.png