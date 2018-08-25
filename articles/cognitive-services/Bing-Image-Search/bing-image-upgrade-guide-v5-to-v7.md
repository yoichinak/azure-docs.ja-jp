---
title: Bing Image Search API v5 から v7 へのアップグレード | Microsoft Docs
description: バージョン 7 を使用するために更新する必要のあるアプリケーションの部分を示します。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b4d785eafe9ced6fb12e2dac3282dfd286849eb6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373333"
---
# <a name="image-search-api-upgrade-guide"></a>Image Search API のアップグレード ガイド

このアップグレード ガイドでは、Bing Image Search API のバージョン 5 とバージョン 7 の間の変更点を示します。 このガイドは、バージョン 7 を使用するために更新する必要のあるアプリケーションの部分を把握するのに役立ちます。

## <a name="breaking-changes"></a>重大な変更

### <a name="endpoints"></a>エンドポイント

- エンドポイントのバージョン番号は、v5 から v7 に変更されました。 たとえば、https://api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search となります。

### <a name="error-response-objects-and-error-codes"></a>エラー応答オブジェクトとエラー コード

- すべての失敗した要求には、応答本文内に `ErrorResponse` オブジェクトが含まれるようになりました。

- `Error` オブジェクトに次のフィールドが追加されました。  
  - `subCode`&mdash;エラー コードを別個のバケットに分割します (可能な場合)
  - `moreDetails` &mdash; `message` フィールドで説明されているエラーに関する追加情報
   

- v5 エラー コードを次の可能な `code` および `subCode` 値に置き換えました。

|コード|サブコード|説明
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|サブコード条件のいずれかが発生するたびに、Bing は ServerError を返します。 HTTP 状態コードが 500 の場合、応答にはこれらのエラーが含まれます。
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>ブロック|要求の一部が有効でない場合に Bing は InvalidRequest を返します。 たとえば、必須のパラメーターが不足している場合や、パラメーター値が無効な場合です。<br/><br/>エラーが ParameterMissing または ParameterInvalidValue の場合は、HTTP 状態コードは 400 です。<br/><br/>エラーが HttpNotAllowed の場合、HTTP 状態コードは 410 です。
|RateLimitExceeded||1 秒あたりのクエリ数 (QPS) または1 か月あたりのクエリ数 (QPM) のクォータを超えると、Bing は RateLimitExceeded を返します。<br/><br/>QPS を超えた場合、Bing は HTTP 状態コード 429 を返し、QPM を超過した場合に 403 を返します。
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing が呼び出し元を認証できない場合、Bing は InvalidAuthorization を返します。 たとえば、`Ocp-Apim-Subscription-Key` ヘッダーがない場合や、サブスクリプション キーが無効な場合が該当します。<br/><br/>冗長性は、複数の認証方法を指定した場合に発生します。<br/><br/>エラーが InvalidAuthorization の場合、HTTP 状態コードは 401 です。
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|呼び出し元がリソースにアクセスする権限を持っていない場合、Bing は InsufficientAuthorization を返します。 これは、サブスクリプション キーが無効になっているか、期限が切れている場合に発生することがあります。 <br/><br/>エラーが InsufficientAuthorization の場合、HTTP 状態コードは 403 です。

- 以前のエラー コードと新しいコードのマッピングを次に示します。 v5 エラー コードに依存していた場合は、コードを適宜更新してください。

|バージョン 5 コード|バージョン 7 コード.サブコード
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
無効|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
ブロック|InvalidRequest.Blocked



### <a name="query-parameters"></a>クエリ パラメーター

- `modulesRequested` クエリ パラメーターの名前が [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules) に変更されました。  
  
- Annotations の名前が Tags に変更されました。 Tags に対する [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules) クエリ パラメーターについての説明を参照してください。  

- ShoppingSources フィルター値をサポートしている市場の一覧が、en-US のみに変更されました。 [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) に関する説明を参照してください。  
 

### <a name="image-insights-changes"></a>イメージの分析情報の変更
  
- [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) の `annotations` フィールドの名前が `imageTags` に変更されました。  
  
- `AnnotationModule` オブジェクトの名前が [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetagsmodule) に変更されました。  
  
- `Annotation` オブジェクトの名前が [Tag](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#tag) に変更され、`confidence` フィールドが削除されました。  
  
- [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) オブジェクトの `insightsSourcesSummary` フィールドの名前が `insightsMetadata` に変更されました。  
  
- `InsightsSourcesSummary` オブジェクトの名前が [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightsmetadata) に変更されました。  
  
- `https://api.cognitive.microsoft.com/bing/v7.0/images/details` エンドポイントが追加されました。 イメージの分析情報を要求するときは、/images/search エンドポイントの代わりにこのエンドポイントを使用します。 [Image Insights](./image-insights.md) に関する説明を参照してください。 
  
- 次のクエリ文字列パラメーターは、`/images/details` エンドポイントでのみ有効となりました。  
  
    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)  
    -   [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl)  
    -   [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  
    -   [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
    -   [ct](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#ct)  
  
- `ImageInsightsResponse` オブジェクトの名前が [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights) に変更されました。  
  
- [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights) オブジェクト内の次のフィールドのデータ型が変更されました。  
  
    -   `relatedCollections` フィールドの型が `ImageGallery[]` から [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedcollectionsmodule) に変更されました。  
  
    -   `pagesIncluding` フィールドの型が `Image[]` から [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule) に変更されました。  
  
    -   `relatedSearches` フィールドの型が `Query[]` から [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedsearchesmodule) に変更されました。  
  
    -   `recipes` フィールドの型が `Recipe[]` から [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recipesmodule) に変更されました。  
  
    -   `visuallySimilarImages` フィールドの型が `Image[]` から [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule) に変更されました。  
  
    -   `visuallySimilarProducts` フィールドの型が `ProductSummaryImage[]` から [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule) に変更されました。  
  
    -   `ProductSummaryImage` オブジェクトが削除され、製品関連のフィールドが [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) オブジェクトに移動されました。 `Image` オブジェクトに製品関連のフィールドが含まれるのは、イメージ分析情報の応答で、イメージが、視覚的に類似した製品の一部として含まれている場合のみです。  
  
    -   `recognizedEntityGroups` フィールドの型が `RecognizedEntityGroup[]` から [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recognizedentitiesmodule) に変更されました。  
  
-   [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) の `categoryClassification` フィールドの名前が `annotations` に変更され、型が `AnnotationsModule` に変更されました。  

### <a name="images-answer"></a>イメージの応答

-   [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) から displayShoppingSourcesBadges フィールドと displayRecipeSourcesBadges フィールドが削除されました。  
  
-   [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) の `nextOffsetAddCount` フィールドの名前が `nextOffset` に変更されました。 オフセットの使用方法も変更されています。 以前は、[offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) クエリ パラメーターを `nextOffsetAddCount` の値、前の offset の値、および結果内のイメージ数の合計に設定していましたが、 `offset` を `nextOffset` の値に設定するようになりました。  
    
  
## <a name="non-breaking-changes"></a>非破壊的変更

### <a name="query-parameters"></a>クエリ パラメーター
  
- [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) フィルターに使用可能な値として、Transparent が追加されました。 Transparent フィルターは、透明な背景を持つイメージのみを返します。

- [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) フィルターに使用可能な値として、Any が追加されました。 Any フィルターは、ライセンスを受けているイメージのみを返します。
  
- [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) および [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) クエリ パラメーターが追加されました。 これらのフィルターを使用すると、ファイル サイズが範囲内であるイメージが返されます。  
  
- [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxheight)、[minHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minheight)、[maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxwidth)、[minWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minwidth) の各クエリ パラメーターが追加されました。 これらのフィルターを使用すると、高さと幅が範囲内であるイメージが返されます。  

### <a name="object-changes"></a>オブジェクトの変更
  
- [Offer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer) オブジェクトに `description` フィールドと `lastUpdated` フィールドが追加されました。  
  
- [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagegallery) オブジェクトに `name` フィールドが追加されました。  
  
- [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) オブジェクトに `similarTerms` フィールドが追加されました。 このフィールドには、ユーザーのクエリ文字列と意味的に類似している用語のリストが含まれます。  
