---
title: Azure Functions の zip プッシュ デプロイ | Microsoft Docs
description: Kudu デプロイ サービスの .zip ファイル デプロイ機能を使用して、Azure Functions を発行します。
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/29/2018
ms.author: glenga
ms.openlocfilehash: 3ff02816cdd5641cdcd78a12206b80be6d518373
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423710"
---
# <a name="zip-push-deployment-for-azure-functions"></a>Azure Functions の zip プッシュ デプロイ 
この記事では、関数アプリ プロジェクト ファイルを .zip (圧縮) ファイルから Azure にデプロイする方法について説明します。 Azure CLI を使用する方法と REST API を使用する方法の両方について、プッシュ デプロイの方法を学習します。 

Azure Functions には、Azure App Service によって提供されている、さまざまな継続的デプロイと統合オプションが含まれています。 詳細については、「[Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)」を参照してください。 

開発時の反復処理を迅速化する必要がある場合は、通常、関数アプリ プロジェクト ファイルを、圧縮された .zip ファイルから直接デプロイしたほうが作業が容易になります。 この .zip ファイル デプロイでは、Kudu サービスを使用することで、継続的な統合ベース デプロイを効率化できます。たとえば、次のような作業を効率化できます。

+ 以前のデプロイから残っているファイルの削除。
+ デプロイのカスタマイズ (デプロイ スクリプトの実行など)。
+ デプロイ ログ。
+ [従量課金プラン](functions-scale.md)関数アプリ内の関数トリガーの同期。

詳細については、「[Azure Functions の継続的なデプロイ](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)」をご覧ください。 

## <a name="deployment-zip-file-requirements"></a>デプロイ .zip ファイルの要件
プッシュ デプロイに使用する .zip ファイルには、関数アプリ内のすべてのプロジェクト ファイル (関数コードを含む) を含める必要があります。 

>[!IMPORTANT]
> .zip プッシュ デプロイを実行した場合、既存のデプロイに含まれているファイルのうち、.zip ファイルに含まれていないものはすべて、関数アプリから削除されます。  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

関数アプリには、`wwwroot` ディレクトリ内のすべてのファイルとフォルダーが含まれています。 .zip ファイルの展開には、`wwwroot` ディレクトリの内容が含まれますが、ディレクトリ自体は含まれません。  

## <a name="download-your-function-app-files"></a>関数アプリ ファイルをダウンロードする

開発作業をローカル コンピューター上で行う場合は、開発用コンピューター上の関数アプリ プロジェクト フォルダー内に .zip ファイルを作成すると、作業が簡単です。 

ただし、Azure Portal 内のエディターを使用して関数を作成した場合、 既存の関数アプリ プロジェクトは、次のいずれかの方法でダウンロードできます。 

+ **Azure portal から:** 

    1. [Azure Portal](https://portal.azure.com) にサインインし、関数アプリに移動します。

    2. **[概要]** タブで、**[アプリのコンテンツのダウンロードド]** を選択します。 ダウンロード オプションを選択し、**[ダウンロード]** を選択します。     

        ![関数アプリ プロジェクトのダウンロード](./media/deployment-zip-push/download-project.png)

    ダウンロードした .zip ファイルは、.zip プッシュ デプロイを使用して関数アプリに再発行するための適切な形式になっています。 ポータルのダウンロードでは、Visual Studio で関数アプリを直接開くために必要なファイルを追加することもできます。

+ **REST API の使用:** 

    `<function_app>` プロジェクトからファイルをダウンロードするには、以下の展開の GET API を使用します。 

        https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/

    `/site/wwwroot/` を含めると、zip ファイルには関数アプリ プロジェクト ファイルのみが含まれ、サイト全体は含まれません。 Azure にまだサインインしていない場合は、サインインするように求められます。 このトピックで説明する zip 展開方法を優先するため、`api/zip/` API に POST 要求を送信することは推奨されません。 

.zip ファイルは GitHub リポジトリからダウンロードすることもできます。 GitHub リポジトリを .zip ファイルとしてダウンロードする場合は、分岐用のフォルダー レベルが追加されることに注意してください。 この追加のフォルダー レベルは、.zip ファイルを GitHub からダウンロードする際、その .zip ファイルを直接デプロイすることはできないということを意味します。 GitHub リポジトリを使用して関数アプリを管理している場合は、[継続的インテグレーション](functions-continuous-deployment.md)を使用してアプリをデプロイする必要があります。  

## <a name="cli"></a>Azure CLI を使用したデプロイ

プッシュ デプロイは、Azure CLI を使用してトリガーすることもできます。 その場合は、[az functionapp deployment source config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) コマンドを使用して、.zip ファイルを関数アプリにプッシュ デプロイします。 このコマンドを使用するには、Azure CLI バージョン 2.0.21 以降を使用する必要があります。 使用している Azure CLI のバージョンを確認するには、`az --version` コマンドを使用します。

次のコマンドでは、`<zip_file_path>` プレース ホルダーを .zip ファイルの場所へのパスに置き換えてください。 また、`<app_name>` を、お使いの関数アプリの一意の名前に置き換えてください。 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
このコマンドを実行すると、ダウンロードした .zip ファイル内のプロジェクト ファイルが、Azure 内の関数アプリにデプロイされます。 その後、アプリが再起動されます。 この関数アプリに対するデプロイの一覧を表示するには、REST API を使用する必要があります。

Azure CLI をローカル コンピューター上で使用している場合、`<zip_file_path>` にはお使いのコンピューター上の .zip ファイルへのパスを指定します。 Azure CLI は [Azure Cloud Shell](../cloud-shell/overview.md) 内で実行することもできます。 Cloud Shell を使用する場合は、まず、Cloud Shell に関連付けられた Azure Files アカウントに .zip ファイルをアップロードする必要があります。 その場合は、`<zip_file_path>` には、Cloud Shell アカウントで使用している保存場所を指定します。 詳細については、「[Azure Cloud Shell でファイルを永続化する](../cloud-shell/persisting-shell-storage.md)」をご覧ください。


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
