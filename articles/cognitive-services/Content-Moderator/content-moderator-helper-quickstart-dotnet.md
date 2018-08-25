---
title: Azure Content Moderator SDK for .NET のヘルパー メソッド | Microsoft Docs
description: Azure Content Moderator SDK for .NET を使用して Content Moderator クライアントを取得する方法
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/04/2018
ms.author: sajagtap
ms.openlocfilehash: 36f2124708731f78f34849d8210ed39ea8f59140
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372853"
---
# <a name="helper-code-to-return-a-content-moderator-client"></a>Content Moderator クライアントを返すヘルパー コード

この記事では、Content Moderator SDK for .NET を使用してサブスクリプションに対する Content Moderator クライアントの作成を開始するのに役立つ情報とコード サンプルを提供します。

ライブラリは、このセクションの他のクイック スタートで使用されます。

この記事では Visual Studio と C# に精通していることを前提としています。

> [!IMPORTANT]
> このクラス ライブラリには、デモンストレーションのみを目的としたコードが含まれています。
> このコードを実稼働環境での使用に適応させる場合は、Content Moderator サブスクリプション キーの格納と使用にセキュリティで保護された方法を使用します。

## <a name="sign-up-for-content-moderator-services"></a>Content Moderator サービスにサインアップする

REST API や SDK を通じて Content Moderator サービスを使用するには、サブスクリプション キーが必要です。
キーの取得方法については、[クイック スタート](quick-start.md)をご覧ください。

## <a name="create-your-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. 新しい**クラス ライブラリ (.NET Framework)** プロジェクトを作成します。

   サンプル コードでは、プロジェクトに **ModeratorHelper** という名前を付けました。

1. **System.Configuration** フレームワーク アセンブリへの参照を追加します。

### <a name="install-required-packages"></a>必要なパッケージをインストールする

次の NuGet パッケージをインストールします。

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="create-the-content-moderator-client"></a>Content Moderator クライアントを作成する

ModeratorHelper.cs ファイルの内容を次のコードに置き換えます。

    using Microsoft.CognitiveServices.ContentModerator;

    namespace ModeratorHelper
    {
    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "myRegion";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "myKey";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.BaseUrl = AzureBaseURL;
            return client;
        }
    }
    }


> [!IMPORTANT]
> **AzureRegion** および **CMSubscriptionKey** フィールドをリージョン識別子とサブスクリプション キーの値で更新します。

サブスクリプションの Content Moderator クライアントを簡単に作成できる方法がわかりました。

## <a name="next-steps"></a>次の手順

.NET 用のこのクイック スタートや他の Content Moderator のクイック スタートの [Visual Studio ソリューションをダウンロード](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)し、統合を開始します。
