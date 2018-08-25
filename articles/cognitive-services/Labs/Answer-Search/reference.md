---
title: Project Answer Search のリファレンス - Microsoft Cognitive Services | Microsoft Docs
description: Project Answer Search エンドポイントのリファレンス。
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: a12761c2d913cd7ffaa2cbc2cd42576c6bc96434
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866986"
---
# <a name="project-answer-search-v7-reference"></a>Project Answer Search v7 のリファレンス

Bing Answer Search API は、クエリ パラメーターを受け取り、`answerType` が `facts` または `entities` の `searchResponse` を返します。 

Answer Search API を使用するアプリケーションでは、プレビューする URL をクエリ パラメーターに含めてエンドポイントに要求を送信します。  要求には、`q=searchTerm` パラメーターと *Ocp-Apim-Subscription-Key* ヘッダーが含まれている必要があります。   

JSON 応答は、検索の対象に関する詳細が含まれたファクトとエンティティについて解析できます。

## <a name="endpoint"></a>エンドポイント
Answer Search の結果を要求するには、次のエンドポイントに要求を送信します。 ヘッダーと URL パラメーターを使用して、さらに詳細を定義します。

エンドポイントに対する GET: 
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

````

要求には HTTPS プロトコルが使用される必要があるほか、次のクエリ パラメーターが含まれている必要があります。
-  q=<URL> - 検索の対象を特定するクエリ

要求の実行方法を示す例については、[C# のクイック スタート](c-sharp-quickstart.md)または [Java のクイック スタート](java-quickstart.md)を参照してください。 

以下のセクションでは、検索結果に影響するクエリ パラメーターおよびヘッダーと、応答オブジェクトに関する技術的な詳細について説明します。 
  
要求に含める必要があるヘッダーについては、「[ヘッダー](#headers)」を参照してください。  
  
要求に含める必要があるクエリ パラメーターについては、「[クエリ パラメーター](#query-parameters)」を参照してください。  
  
応答に含まれる JSON オブジェクトについては、「[応答オブジェクト](#response-objects)」を参照してください。

クエリ URL の最大長は 2,048 文字です。 URL の長さが上限を超えないよう、クエリ パラメーターの最大長は 1,500 文字未満にする必要があります。 URL が 2,048 文字を超えた場合、サーバーが 404 Not found を返します。  

結果の使用と表示に関する許可については、[使用と表示の要件](use-display-requirements.md)に関するページを参照してください。 

> [!NOTE]
> 他の検索 API で有効な一部の要求ヘッダーは、URL Preview に影響しません
> - Pragma – 呼び出し元は URL Preview でキャッシュが使用されるかどうかを制御できません
> - Cache-Control – 呼び出し元は URL Preview でキャッシュが使用されるかどうを制御できません
> - User-Agent

> さらに、一部のパラメーターは現在 URL Preview API で有効ではありません。ただし、これらのパラメーターは、使用範囲が拡大されて使用できるようになる可能性があります。 
 
## <a name="headers"></a>headers  
要求と応答に含まれる可能性があるヘッダーを次に示します。  
  
|ヘッダー|説明|  
|------------|-----------------|  
|Accept|省略可能な要求ヘッダー。<br /><br /> 既定のメディアの種類は application/json です。 応答で [JSON-LD](http://json-ld.org/) が使用されるよう指定するには、Accept ヘッダーを application/ld+json に設定します。|  
|<a name="acceptlanguage" />Accept-Language|省略可能な要求ヘッダー。<br /><br /> ユーザー インターフェイス文字列に使用する言語のコンマ区切りリストです。 リストでは優先度の高いものから順に指定します。 有効な形式など、詳細については [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) を参照してください。<br /><br /> このヘッダーと [setLang](#setlang) クエリ パラメーターは相互に排他的なので、両方は指定しないでください。<br /><br /> このヘッダーを設定する場合は、[cc](#cc) クエリ パラメーターも指定する必要があります。 結果が返される市場を特定するために、Bing によってリストから検出された最初のサポート対象言語が使用され、それが `cc` パラメーター値と組み合わされます。 サポート対象言語がリストに含まれていない場合、要求がサポートされる最も近い言語と市場が Bing によって検出されるか、集計された市場または既定の市場が結果に使用されます。 Bing によって使用された市場を確認するには、BingAPIs-Market ヘッダーを調べます。<br /><br /> このヘッダーと `cc` クエリ パラメーターは、複数の言語を指定する場合にのみ使用します。 それ以外の場合は、[mkt](#mkt) クエリ パラメーターおよび [setLang](#setlang) クエリ パラメーターを使用します。<br /><br /> ユーザー インターフェイス文字列は、ユーザー インターフェイスでラベルとして使われる文字列です。 JSON 応答オブジェクトには、いくつかのユーザー インターフェイス文字列があります。 応答オブジェクト内の Bing.com プロパティへのリンクには、指定された言語が適用されます。|  
|<a name="market" />BingAPIs-Market|応答ヘッダー。<br /><br /> 要求で使用された市場。 形式は \<言語コード\>-\<国番号\> です。 たとえば、en-US などです。|  
|<a name="traceid" />BingAPIs-TraceId|応答ヘッダー。<br /><br /> 要求の詳細が含まれたログ エントリの ID。 エラーが発生した場合、この ID をキャプチャします。 問題を特定して解決できない場合は、その他の情報と共にこの ID をサポート チームに提供します。|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|必須の要求ヘッダー。<br /><br /> [Cognitive Services](https://www.microsoft.com/cognitive-services/) でこのサービスにサインアップしたときに受け取ったサブスクリプション キー。|  
|<a name="pragma" />Pragma|省略可能な要求ヘッダー<br /><br /> 既定では、Bing はキャッシュされたコンテンツがある場合にそれを返します。 キャッシュされたコンテンツを Bing が返さないようにするには、Pragma ヘッダーを no-cache に設定します (例: Pragma: no-cache)。
|<a name="useragent" />User-Agent|省略可能な要求ヘッダー。<br /><br /> 要求送信元のユーザー エージェント。 Bing では、モバイル ユーザーに最適なエクスペリエンスを提供するためにユーザー エージェントが使用されます。 省略可能ですが、このヘッダーは常に指定することをお勧めします。<br /><br /> ユーザーエージェントは、よく使用されるブラウザーによって送信されるのと同じ文字列にする必要があります。 ユーザー エージェントについては、[RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) を参照してください。<br /><br /> ユーザーエージェント文字列の例を次に示します。<br /><ul><li>Windows Phone &mdash; Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android &mdash; Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone &mdash; Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC &mdash; Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad &mdash; Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|省略可能な要求および応答ヘッダー。<br /><br /> このヘッダーは、Bing API の呼び出し間で一貫性のある動作をユーザーに提供するために Bing によって使用されます。 Bing によって、新しい機能と改善点が頻繁にフライト化されます。そして、トラフィックを異なるフライトに割り当てるためのキーとして、クライアント ID が使用されます。 複数の要求に対してユーザーの同じクライアント ID を使用しないと、ユーザーが複数の競合するフライトに割り当てられる可能性があります。 複数の競合するフライトに割り当てられると、ユーザー エクスペリエンスの一貫性がなくなる場合があります。 たとえば、2 番目の要求に 1 番目とは異なるフライトが割り当てられていると、エクスペリエンスが予期しないものになる可能性があります。 また、クライアント ID は、そのクライアント ID の検索履歴に合わせて Web の結果を調整し、ユーザーにリッチなエクスペリエンスを提供するために Bing によって使用される場合があります。<br /><br /> このヘッダーは、クライアント ID で生成されたアクティビティを分析して結果の順位付けを向上させるために Bing によって使用されることもあります。 関連性の向上は、Bing API によって提供される結果の品質向上に役立ち、API コンシューマーのクリックスルー率の向上を実現します。<br /><br /> **重要:** このヘッダーは省略可能ですが、必須であると考える必要があります。 同じエンド ユーザーとデバイスの組み合わせによる複数の要求に対してクライアント ID を保持することで、1) API コンシューマーが一貫性のあるユーザー エクスペリエンスを受け取ることができ、2) Bing API からの結果の品質向上を通じてクリックスルー率の向上が実現します。<br /><br /> このヘッダーに適用される基本的な使用規則を次に示します。<br /><ul><li>デバイスでアプリケーションを使用する各ユーザーは、Bing によって生成された一意のクライアント ID を持っている必要があります。<br /><br/>このヘッダーを要求に含めない場合、Bing によって ID が生成され、それが X-MSEdge-ClientID 応答ヘッダーで返されます。 このヘッダーを要求に含めるべきでない唯一の場合は、ユーザーがそのデバイスでアプリを初めて使用するときです。<br /><br/></li><li>そのユーザーのためにアプリによってデバイスで実行される各 Bing API 要求で、クライアント ID を使用します。<br /><br/></li><li>**注意:** このクライアント ID は認証可能なユーザー アカウント情報にリンクできないようにする必要があります。</li><br/><li>クライアント ID を保持します。 ブラウザー アプリで ID を保持するには、永続的な HTTP Cookie を使用して、ID がすべてのセッションで使用されるようにします。 セッション Cookie は使用しないようにしてください。 モバイル アプリなど、他のアプリの場合は、デバイスの永続的ストレージを使用して ID を保持します。<br /><br/>次にそのデバイスでユーザーがアプリを使用するときに、保持したクライアント ID を取得します。</li></ul><br /> **注:** Bing の応答には、このヘッダーが含まれる場合と含まれない場合があります。 このヘッダーが応答に含まれる場合、クライアント ID をキャプチャして、ユーザーのためにそのデバイスで実行される後続のすべての Bing 要求でそれを使用します。<br /><br /> **注:** X-MSEdge-ClientID を含める場合、要求には Cookie を含めないようにしてください。|  
|<a name="clientip" />X-MSEdge-ClientIP|省略可能な要求ヘッダー。<br /><br /> クライアント デバイスの IPv4 アドレスまたは IPv6 アドレス。 IP アドレスは、ユーザーの位置情報の検出に使用されます。 位置情報は、安全な検索動作を決定するために Bing によって使用されます。<br /><br /> **注:** 省略可能ですが、このヘッダーと X-Search-Location ヘッダーは常に指定することをお勧めします。<br /><br /> (最後のオクテットを 0 に変更するなど) アドレスを難読化しないようにしてください。 アドレスを難読化すると、デバイスの実際の場所から離れた場所が検出され、Bing から誤った結果が提供される可能性があります。|  
|<a name="location" />X-Search-Location|省略可能な要求ヘッダー。<br /><br /> クライアントの地理的な場所を示す、キーと値のペアのセミコロン区切りリストです。 位置情報は、安全な検索動作を決定して関連するローカル コンテンツを返すために、Bing によって使用されます。 キーと値のペアは、\<キー\>:\<値\> の形式で指定します。 ユーザーの場所の指定に使用するキーは次のとおりです。<br /><br /><ul><li>lat &mdash; クライアントの場所の緯度 (度単位)。 緯度は、-90.0 以上、+90.0 以下である必要があります。 負の値は南半球の緯度を示し、正の値は北半球の緯度を示します。<br /><br /></li><li>long &mdash; クライアントの場所の経度 (度単位)。 経度は、-180.0 以上、+180.0 以下である必要があります。 負の値は西半球の経度を示し、正の値は東半球の経度を示します。<br /><br /></li><li>re &mdash; 座標の水平方向の精度を指定する半径 (m)。 デバイスの位置情報サービスによって返される値を渡します。 一般的な値は、GPS/Wi-Fi の 22 m、携帯電話基地局の三角測量の 380 m、IP 逆引き参照の 18,000 m などです。<br /><br /></li><li>ts &mdash; クライアントがその場所にあったときの UTC UNIX タイムスタンプ  (UNIX タイムスタンプは、1970 年 1 月 1 日からの経過秒数です)。<br /><br /></li><li>head &mdash; 省略可能。 クライアントの相対的な先頭方向または移動方向。 移動方向は、真北を基準として時計回りに 0 から 360 度で指定します。 このキーは、`sp` キーが 0 以外の場合にのみ指定します。<br /><br /></li><li>sp &mdash; クライアント デバイスが移動している水平方向の速度 (m/秒)。<br /><br /></li><li>alt &mdash; クライアント デバイスの高度 (m)。<br /><br /></li><li>are &mdash; 省略可能。 座標の垂直方向の精度を指定する半径 (m)。 半径の既定値は 50 km です。 このキーは、`alt` キーを指定する場合にのみ指定します。<br /><br /></li></ul> **注:** これらのキーは省略可能ですが、提供する情報が多ければ多いほど、位置情報の結果が正確になります。<br /><br /> **注:** ユーザーの地理的な場所は常に指定することをお勧めします。 位置情報を提供することは、クライアントの IP アドレスがユーザーの物理的な場所を正確に反映していない場合 (たとえば、クライアントによって VPN が使用されている場合) に特に重要です。 最適な結果を得るには、このヘッダーと X-MSEdge-ClientIP ヘッダーを含める必要がありますが、最低限、このヘッダーを含めることをお勧めします。|

> [!NOTE] 
> 利用規約ですべての該当法規 (これらのヘッダーの使用に関するものなど) への準拠が要求されていることに注意してください。 たとえば、ヨーロッパなどの特定の地域では、特定の追跡デバイスをユーザー デバイスに組み込む前に、ユーザーの同意を得る必要があります。
  

## <a name="query-parameters"></a>クエリ パラメーター  
要求には次のクエリ パラメーターを含めることができます。 必須のパラメーターについては、必須の列を参照してください。 クエリ パラメーターは URL エンコードする必要があります。  
  
  
|Name|値|type|必須|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|結果の取得元の市場。 <br /><br />可能な市場の値の一覧については、[市場コード](#market-codes)に関するページを参照してください。<br /><br /> **注**: 現在 URL Preview API でサポートされるのは en-us の市場と言語のみです。<br /><br />|String|[はい]|  
|<a name="query" />q|プレビューする URL|String|[はい]|  
|<a name="responseformat" />responseFormat|応答に使用するメディアの種類。 次の値を指定できます。大文字と小文字は区別されません。<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> 既定値は JSON です。 応答に含まれる JSON オブジェクトについては、「[応答オブジェクト](#response-objects)」を参照してください。<br /><br />  JsonLd を指定すると、検索結果が含まれている JSON-LD オブジェクトが応答本文に含まれます。 JSON-LD については、[JSON-LD](http://json-ld.org/) に関するページを参照してください。|String|いいえ |  
|<a name="safesearch" />safeSearch|成人向けコンテンツのフィルター処理に使用されるフィルター。 次のフィルター値を指定できます。大文字と小文字は区別されません。<br /><ul><li>Off &mdash; 成人向けのテキスト、画像、ビデオが含まれた Web ページを返します。<br /><br/></li><li>Moderate &mdash; 成人向けのテキストが含まれているものの、成人向けの画像またはビデオは含まれていない Web ページを返します。<br /><br/></li><li>Strict &mdash; 成人向けのテキスト、画像、ビデオが含まれた Web ページを返しません。</li></ul><br /> 既定値は Moderate です。<br /><br /> **注:** `safeSearch` が Strict に設定されるよう Bing の成人向けコンテンツ ポリシーによって強制される市場が要求元の場合、Bing によって `safeSearch` の値が無視され、Strict が使用されます。<br/><br/>**注:** `site:` クエリ演算子を使用している場合、`safeSearch` クエリ パラメーターの設定にかかわらず、成人向けのコンテンツが応答に含まれることがあります。 `site:` は、そのサイト上のコンテンツがわかっていて、成人向けコンテンツが含まれていても問題のないシナリオの場合にのみ使用してください。 |String|いいえ |  
|<a name="setlang" />setLang|ユーザー インターフェイス文字列に使用する言語。 ISO 639-1 の 2 文字言語コードを使って言語を指定します。 たとえば、英語の言語コードは EN です。 既定値は EN (英語) です。<br /><br /> 省略可能ですが、常に言語を指定することをお勧めします。 ユーザー インターフェイス文字列が別の言語で表示されることをユーザーが望まない限り、通常、`setLang` は `mkt` で指定されるのと同じ言語に設定します。<br /><br /> このパラメーターと [Accept-Language](#acceptlanguage) ヘッダーは相互に排他的なので、両方は指定しないでください。<br /><br /> ユーザー インターフェイス文字列は、ユーザー インターフェイスでラベルとして使われる文字列です。 JSON 応答オブジェクトには、いくつかのユーザー インターフェイス文字列があります。 また、応答オブジェクト内の Bing.com プロパティへのリンクには、指定された言語が適用されます。|String|いいえ | 


## <a name="response-objects"></a>応答オブジェクト  
Web Search API では、応答スキーマは [WebPage] と ErrorResponse のいずれかです。 要求が失敗した場合、最上位レベルのオブジェクトは [ErrorResponse](#errorresponse) オブジェクトになります。


|オブジェクト|説明|  
|------------|-----------------|  
|[WebPage]|プレビューの属性が含まれている最上位の JSON オブジェクト。|  
|[Fact]|ファクトが含まれている最上位の JSON オブジェクト。| 
|[Entities]|エンティティの詳細が含まれている最上位の JSON オブジェクト。| 

  
### <a name="error"></a>Error  
発生したエラーを定義します。  
  
|要素|説明|type|  
|-------------|-----------------|----------|  
|<a name="error-code" />code|エラーのカテゴリを特定するエラー コード。 考えられるコードの一覧については、「[エラー コード](#error-codes)」を参照してください。|String|  
|<a name="error-message" />message|エラーの説明。|String|  
|<a name="error-moredetails" />moreDetails|エラーに関する追加情報を提供する説明。|String|  
|<a name="error-parameter" />parameter|エラーを引き起こした要求内のクエリ パラメーター。|String|  
|<a name="error-subcode" />subCode|エラーを特定するエラー コード。 たとえば、`code` が InvalidRequest の場合、`subCode` は ParameterInvalid か ParameterInvalidValue の場合があります。 |String|  
|<a name="error-value" />value|有効でなかったクエリ パラメーター値。|String|  
  

### <a name="errorresponse"></a>ErrorResponse  
要求が失敗したときの応答に含まれている最上位レベルのオブジェクト。  
  
|Name|値|type|  
|----------|-----------|----------|  
|_type|種類のヒント。|String|  
|<a name="errors" />errors|要求が失敗した理由を示すエラーの一覧。|[Error](#error)[]|  

  
  
### <a name="license"></a>License  
テキストまたは写真の使用が許可されるライセンスを定義します。  
  
|Name|値|type|  
|----------|-----------|----------|  
|name|ライセンスの名前。|String|  
|url|ライセンスに関する詳しい情報を取得できる Web サイトへの URL。<br /><br /> 名前と URL を使用してハイパーリンクを作成します。|String|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
ライセンス属性の契約規則を定義します。  
  
|Name|値|type|  
|----------|-----------|----------|  
|_type|種類のヒント。これは LicenseAttribution に設定されます。|String|  
|license|コンテンツの使用が許可されるライセンス。|[License](#license)|  
|licenseNotice|ターゲットのフィールドの横に表示されるライセンス。 たとえば、"Text under CC-BY-SA license" などです。<br /><br /> `license` フィールドのライセンスの名前と URL を使用して、ライセンスの詳細が説明されている Web サイトへのハイパーリンクを作成します。 次に、`licenseNotice` 文字列のライセンス名 (例: CC-BY-SA) を、作成したハイパーリンクに置き換えます。|String|  
|mustBeCloseToContent|規則の内容が、その規則の適用されるフィールドのすぐ近くに配置される必要があるかどうかを決定するブール値。 **true** の場合、内容はすぐ近くに配置される必要があります。 **false** の場合、またはこのフィールドが存在しない場合、内容は呼び出し元の裁量で配置できます。|ブール|  
|targetPropertyName|規則が適用されるフィールドの名前。|String|  
  

### <a name="link"></a>Link  
ハイパーリンクのコンポーネントを定義します。  
  
|Name|値|type|  
|----------|-----------|----------|  
|_type|種類のヒント。|String|  
|text|表示テキスト。|String|  
|url|URL。 この URL と表示テキストを使用してハイパーリンクを作成します。|String|  
  

### <a name="linkattribution"></a>LinkAttribution  
リンク属性の契約規則を定義します。  
  
|Name|値|type|  
|----------|-----------|----------|  
|_type|種類のヒント。これは LinkAttribution に設定されます。|String|  
|mustBeCloseToContent|規則の内容が、その規則の適用されるフィールドのすぐ近くに配置される必要があるかどうかを決定するブール値。 **true** の場合、内容はすぐ近くに配置される必要があります。 **false** の場合、またはこのフィールドが存在しない場合、内容は呼び出し元の裁量で配置できます。|ブール|  
|targetPropertyName|規則が適用されるフィールドの名前。<br /><br /> ターゲットが指定されていない場合、属性はエンティティ全体に適用されます。また、エンティティの表示のすぐ後に表示される必要があります。 ターゲットが指定されていないテキスト属性とリンク属性の規則が複数ある場合、"Data from: " ラベルを使ってそれらを連結して表示する必要があります。 たとえば、"Data from <プロバイダー名 1\> &#124; <プロバイダー名 2\>" のようになります。|String|  
|text|属性のテキスト。|String|  
|url|プロバイダーの Web サイトへの URL。 `text` と URL を使用してハイパーリンクを作成します。|String|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
メディア属性の契約規則を定義します。  
  
|Name|値|type|  
|----------|-----------|----------|  
|_type|種類のヒント。これは MediaAttribution に設定されます。|String|  
|mustBeCloseToContent|規則の内容が、その規則の適用されるフィールドのすぐ近くに配置される必要があるかどうかを決定するブール値。 **true** の場合、内容はすぐ近くに配置される必要があります。 **false** の場合、またはこのフィールドが存在しない場合、内容は呼び出し元の裁量で配置できます。|ブール|  
|targetPropertyName|規則が適用されるフィールドの名前。|String|  
|url|メディア コンテンツのハイパーリンクの作成に使用する URL。 たとえば、ターゲットが画像の場合、URL を使用して画像をクリックできるようにします。|String|  
  
  
  
### <a name="organization"></a>Organization  
公開元を定義します。  
  
公開元は、名前、Web サイト、またはその両方を提供する場合があることに注意してください。  
  
|Name|値|type|  
|----------|-----------|----------|  
|name|公開元の名前。|String|  
|url|公開元の Web サイトへの URL。<br /><br /> 公開元が Web サイトを提供しない場合があることに注意してください。|String|  
  
  

### <a name="webpage"></a>WebPage  
プレビューの Web ページに関する情報を定義します。  
  
|Name|値|type|  
|----------|-----------|----------|
|name|ページ タイトル (必ずしも HTML タイトルではありません)|String|
|url|実際にクロールされた URL (要求はリダイレクトされた可能性があります)|String|  
|description |ページとコンテンツの簡単な説明|String|  
|isFamilyFriendly|Web インデックスの項目で最も正確です。この検出は、ページのコンテンツではなく URL のみに基づいて、リアルタイム フェッチによって実行されます|ブール値|
|primaryImageOfPage/contentUrl|プレビューに含まれる代表的な画像への URL|String| 
  
  
### <a name="querycontext"></a>QueryContext  
Bing によって要求に使用されたクエリ コンテキストを定義します。  
  
|要素|説明|type|  
|-------------|-----------------|----------|  
|adultIntent|指定されたクエリに成人の意図が含まれているかどうかを示すブール値。 クエリに成人の意図が含まれている場合には値が **true** になり、含まれていない場合には **false** になります。|ブール|  
|alterationOverrideQuery|Bing による元の文字列の使用を強制するために使用するクエリ文字列。 たとえば、クエリ文字列が *saling downwind* である場合、上書きクエリ文字列は *+saling downwind* になります。 必ず、結果が *%2Bsaling+downwind* となるクエリ文字列をエンコードしてください。<br /><br /> このフィールドは、元のクエリ文字列にスペル ミスがある場合にのみ含まれます。|String|  
|alteredQuery|クエリを実行するために Bing によって使用されるクエリ文字列。 元のクエリ文字列にスペル ミスがあった場合、変更されたクエリ文字列が Bing によって使用されます。 たとえば、クエリ文字列が `saling downwind` の場合、変更されたクエリ文字列は `sailing downwind` になります。<br /><br /> このフィールドは、元のクエリ文字列にスペル ミスがある場合にのみ含まれます。|String|  
|askUserForLocation|正確な結果の提供を目的として、Bing からユーザーの位置情報が要求されているかどうかを示すブール値。 [X-MSEdge-ClientIP](#clientip) ヘッダーと [X-Search-Location](#location) ヘッダーを使用してユーザーの位置情報を指定した場合、このフィールドは無視できます。<br /><br /> 正確な結果を得るためにユーザーの位置情報が必要な位置情報対応クエリ ("today's weather" や "restaurants near me" など) では、このフィールドは **true** に設定されます。<br /><br /> 位置情報が含まれている位置情報対応クエリ ("Seattle weather" など) では、このフィールドは **false** に設定されます。 このフィールドは、位置情報に未対応のクエリ ("best sellers" など) でも **false** に設定されます。|ブール|  
|originalQuery|要求で指定されたとおりのクエリ文字列。|String|  

### <a name="identifiable"></a>Identifiable
|Name|値|type|  
|-------------|-----------------|----------|
|id|リソース識別子|String|
 
### <a name="rankinggroup"></a>RankingGroup
検索結果グループ (メインラインなど) を定義します。
|Name|値|type|  
|-------------|-----------------|----------|
|items|グループで表示される検索結果の一覧。|RankingItem|

### <a name="rankingitem"></a>RankingItem
表示される検索結果項目を定義します。
|Name|値|type|  
|-------------|-----------------|----------|
|resultIndex|表示される回答内の項目のゼロベース インデックス。 このフィールドが項目に含まれていない場合、回答のすべての項目が表示されます。 たとえば、News 回答ではすべてのニュース記事が表示されます。|整数|
|answerType|表示される項目が含まれている回答。 たとえば、News などです。<br /><br />種類を使用して SearchResponse オブジェクト内の回答を探します。 種類は SearchResponse フィールドの名前です。<br /><br /> ただし、このオブジェクトに value フィールドが含まれている場合にのみ回答の種類を使用し、そうでない場合は無視します。|String|
|textualIndex|表示される textualAnswers 内の回答のインデックス。| 符号なし整数|
|value|表示される回答と表示される回答の項目のいずれかを特定する ID。 ID によって回答が特定される場合、回答の項目がすべて表示されます。|Identifiable|

### <a name="rankingresponse"></a>RankingResponse  
検索結果ページでコンテンツが配置される場所と順序を定義します。  
  
|Name|値|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|メインラインに表示される検索結果。|  
|<a name="ranking-pole" />pole|最も目立つ処理を受ける検索結果 (たとえば、メインラインとサイドバーの上に表示されます)。|  
|<a name="ranking-sidebar" />sidebar|サイドバーに表示される検索結果。| 


### <a name="searchresponse"></a>SearchResponse  
要求が成功したときの応答に含まれている最上位レベルのオブジェクトを定義します。  
  
サービスによってサービス拒否攻撃が疑われている場合、要求は成功する (HTTP 状態コードは 200 OK) ものの、応答の本文は空になることに注意してください。  
  
|Name|値|type|  
|----------|-----------|----------|  
|_type|種類のヒント。これは SearchResponse に設定されます。|String|  
|WebPage|プレビューを定義する JSON オブジェクト|文字列|  
  
  
### <a name="textattribution"></a>TextAttribution  
プレーンテキスト属性の契約規則を定義します。  
  
|Name|値|type|  
|----------|-----------|----------|  
|_type|種類のヒント。これは TextAttribution に設定されます。|String|  
|text|属性のテキスト。<br /><br /> テキスト属性はエンティティ全体に適用されます。また、エンティティの表示のすぐ後に表示される必要があります。 ターゲットが指定されていないテキスト属性またはリンク属性の規則が複数ある場合、"Data from: " ラベルを使ってそれらを連結して表示する必要があります。|String| 


## <a name="error-codes"></a>エラー コード

要求によって返される可能性のある HTTP 状態コードを次に示します。  
  
|状態コード|説明|  
|-----------------|-----------------|  
|200|成功。|  
|400|クエリ パラメーターの 1 つが欠落しているか無効です。|  
|401|サブスクリプション キーが見つからないか、無効です。|  
|403|(たとえば、有効なサブスクリプション キーを使用して) ユーザーは認証されたものの、要求されたリソースへのアクセス許可がありません。<br /><br /> また、呼び出し元が 1 か月あたりのクエリ数のクォータを超えた場合にも、Bing はこの状態を返します。|  
|410|HTTPS プロトコルではなく HTTP プロトコルが使用された要求。 サポートされるプロトコルは HTTPS のみです。|  
|429|呼び出し元が 1 秒あたりのクエリ数のクォータを超えました。|  
|500|予期しないサーバー エラー。|

要求が失敗すると、応答に [ErrorResponse](#errorresponse) オブジェクトが含まれます。このオブジェクトには、エラーの原因を示す [Error](#error) オブジェクトの一覧が含まれています。 エラーがパラメーターに関連している場合、`parameter` フィールドで、問題であるパラメーターが特定されます。 エラーがパラメーター値に関連している場合、`value` フィールドで、無効な値が特定されます。

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

考えられるエラー コードとサブエラー コードの値を次に示します。

|コード|サブコード|説明
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|HTTP 状態コードは 500 です。
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>ブロック|要求の一部が有効でない場合に Bing は InvalidRequest を返します。 たとえば、必要なパラメーターが不足している場合や、パラメーター値が無効な場合です。<br/><br/>エラーが ParameterMissing または ParameterInvalidValue の場合、HTTP 状態コードは 400 です。<br/><br/>HTTPS プロトコルではなく HTTP プロトコルを使用すると、Bing は HttpNotAllowed を返し、HTTP 状態コードは 410 になります。
|RateLimitExceeded|No sub-codes|1 秒あたりのクエリ数 (QPS) または 1 か月あたりのクエリ数 (QPM) のクォータを超えると、Bing は RateLimitExceeded を返します。<br/><br/>QPS を超えた場合、Bing は HTTP 状態コード 429 を返します。また、QPM を超えた場合、Bing は 403 を返します。
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing は、呼び出し元を認証できない場合に InvalidAuthorization を返します。 たとえば、`Ocp-Apim-Subscription-Key` ヘッダーがない場合や、サブスクリプション キーが無効な場合です。<br/><br/>冗長性は、複数の認証方法を指定した場合に発生します。<br/><br/>エラーが InvalidAuthorization の場合、HTTP 状態コードは 401 です。
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|呼び出し元がリソースに対するアクセス許可を備えていない場合、Bing は InsufficientAuthorization を返します。 これは、サブスクリプション キーが無効になっているか、期限が切れている場合に発生することがあります。 <br/><br/>エラーが InsufficientAuthorization の場合、HTTP 状態コードは 403 です。

## <a name="next-steps"></a>次の手順
- [C# のクイック スタート](c-sharp-quickstart.md)
- [Java のクイック スタート](java-quickstart.md)
- [Node のクイック スタート](node-quickstart.md)
- [Python のクイック スタート](python-quickstart.md)

