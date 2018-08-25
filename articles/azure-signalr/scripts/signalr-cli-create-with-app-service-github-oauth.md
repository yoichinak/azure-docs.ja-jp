---
title: Azure CLI スクリプトのサンプル - SignalR Service と GitHub 認証を使用した Web アプリを作成する | Microsoft Docs
description: Azure CLI スクリプトのサンプル - SignalR Service と GitHub 認証を使用した Web アプリを作成する
services: signalr
documentationcenter: signalr
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/22/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 76094a816fc7698994b77507c2a3dcf3dcf9880b
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2018
ms.locfileid: "41919546"
---
# <a name="create-a-web-app-that-uses-signalr-service-and-github-authentication"></a>SignalR Service と GitHub 認証を使用した Web アプリを作成する

このサンプル スクリプトでは、リアルタイムのコンテンツ更新をクライアントにプッシュするために使用される、新しい Azure SignalR Service リソースを作成します。 また、SignalR サービスを使用した ASP.NET Core Web アプリをホストするための、新しい Web アプリと App Service プランも追加します。 この Web アプリは、新しい SignalR サービス リソースに接続し、[GitHub 認証](https://developer.github.com/v3/guides/basics-of-authentication/)による認証を行うためのアプリ設定を使用して構成されます。 また、この Web アプリはローカル Git リポジトリのデプロイ ソースを使うように構成されます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトでは、Azure CLI の *signalr* 拡張機能を使います。 このサンプル スクリプトを使う前に、次のコマンドを実行して Azure CLI の *signalr* 拡張機能をインストールしてください。

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service-github-oauth/create-signalr-with-app-service-github-oauth.sh "Create a new SignalR Service and Web App configured to use SignalR, GitHub OAuth, and local Git repository deployment source.")]

新しいリソース グループに対して生成された実際の名前はメモしておいてください。 これは出力に表示されます。 このリソース グループ名は、すべてのグループ リソースを削除するときに使います。

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

表内の各コマンドは、それぞれのドキュメントにリンクされています。 このスクリプトでは以下のコマンドを使用します。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az signalr create](/cli/azure/ext/signalr/signalr#ext-signalr-az-signalr-create) | Azure SignalR Service リソースを作成します。 |
| [az signalr key list](/cli/azure/ext/signalr/signalr/key#ext-signalr-az-signalr-key-list) | キーを一覧表示します。これらのキーは、SignalR でリアルタイム コンテンツの更新をプッシュする際、アプリケーションによって使われます。 |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Web アプリをホストするための Azure App Service プランを作成します。 |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | App Service ホスティング プランを使用して Azure Web アプリを作成します。 |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Web アプリ用の新しいアプリ設定を追加します。 これらのアプリ設定は、SignalR の接続文字列と GitHub OAuth のアプリ シークレットを格納するために使われます。 |
| [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) | デプロイ資格情報を更新します。 |
| [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) | Web アプリのデプロイ用に複製してプッシュするための、git リポジトリ エンドポイントの URL を取得します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

Azure SignalR Service のその他の CLI サンプル スクリプトについては、[Azure SignalR サービスのドキュメント](../signalr-cli-samples.md)をご覧ください。
