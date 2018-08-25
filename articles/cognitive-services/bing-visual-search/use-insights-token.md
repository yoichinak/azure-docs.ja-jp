---
title: Bing Visual Search API での分析情報トークンの使用 | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: 画像の分析情報トークンを Visual Search API で使用して画像についての分析情報を取得する方法を説明します。
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 569ae89a712d14fb36989e756f99725dce398c0a
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126560"
---
# <a name="using-an-insights-token-to-get-insights-about-an-image"></a>分析情報トークンを使用して画像についての分析情報を取得する

Bing Visual Search API は、提供された画像に関する情報を返します。 画像を提供するには、画像の URL または分析情報トークンを使用するか、画像をアップロードします。 これらのオプションについて詳しくは、「[Bing Visual Search API とは](overview.md)」をご覧ください。 この記事では、分析情報トークンの使用方法を示します。 画像をアップロードして分析情報を取得する方法の例については、クイック スタート ([C#](quickstarts\csharp.md) | [Java](quickstarts\java.md) | [Node.js](quickstarts\nodejs.md) | [Python](quickstarts\python.md)) をご覧ください。


画像のトークンまたは URL を Visual Search に送信する場合は、次に示すフォーム データを POST の本文に含める必要があります。 フォーム データには Content-Disposition ヘッダーが含まれ、その `name` パラメーターは "knowledgeRequest" に設定されている必要があります。 `imageInfo` オブジェクトについて詳しくは、「[要求](#the-request)」をご覧ください。

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

この記事の例では、分析情報トークンを使う方法を示します。 分析情報トークンは、/images/search API 応答の Image オブジェクトから取得します。 分析情報トークンの取得については、[Bing Image Search API](../Bing-Image-Search/overview.md) に関するページをご覧ください。

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFEDF3D40...",
    }
}

--boundary_1234-abcd--
```


分析情報トークンを使用する例については、[C#](#using-csharp) | [Java](#using-java) | [Node.js](#using-nodejs) | [Python](#using-python) をご覧ください。

<a name="using-csharp" />

## <a name="using-c"></a>C# の使用

### <a name="prerequisites"></a>前提条件

このコードを Windows 上で実行するには、[Visual Studio 2017](https://www.visualstudio.com/downloads/) が必要です  (無料の Community Edition でかまいません。)

このクイック スタートでは、[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)サブスクリプション キーまたは有料サブスクリプション キーを使用できます。

## <a name="running-the-application"></a>アプリケーションの実行

このアプリケーションを実行するには、次の手順に従います。

1. Visual Studio で新しいコンソール ソリューションを作成します。
1. `Program.cs` の内容をこのクイックスタートで示すコードに置き換えます。
2. `accessKey` 値を、サブスクリプション キーに置き換えます。
2. `insightsToken` の値を、/images/search 応答からの分析情報トークンに置き換えます。
3. プログラムを実行します。

```csharp
using System;
using System.Text;
using System.Net;
using System.IO;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading.Tasks;
using System.Threading;

namespace VisualSearchInsightsToken
{

    class Program
    {
        // Replace the accessKey string value with your valid subscription key.
        const string accessKey = "<yoursubscriptionkeygoeshere>";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";

        // Update with an insights token from an image object that the /images/search API endpoint returns.
        static string insightsToken = @"ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

        static string BoundaryTemplate = "batch_{0}";

        static void Main(string[] args)
        {
            try { 
                Console.WriteLine("Getting image insights using insights token: " + insightsToken);

                var knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";
                var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());

                var json = BingVisualSearch(knowledgeRequest, boundary, uriBase, accessKey);

                Console.WriteLine("\nJSON Response:\n");
                Console.WriteLine(JsonPrettyPrint(json));

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }

        }


        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response with insights.
        /// </summary>
        static string BingVisualSearch(string knowledgeRequest, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new StringContent(knowledgeRequest, Encoding.UTF8, "application/json"), "knowledgeRequest");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }


            return json;
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            char last = ' ';
            int offset = 0;
            int indentLength = 2;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\\':
                        if (quote && last != '\\') ignore = true;
                        break;
                }

                if (quote)
                {
                    sb.Append(ch);
                    if (last == '\\' && ignore) ignore = false;
                }
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (quote || ch != ' ') sb.Append(ch);
                            break;
                    }
                }
                last = ch;
            }

            return sb.ToString().Trim();
        }
    }
}
```

<a name="using-java" />

## <a name="using-java"></a>Java の使用

### <a name="prerequisites"></a>前提条件

このコードをコンパイルして実行するには、[JDK 7 または 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) が必要です。 好みの Java IDE がある場合はそれを使用してもかまいませんが、テキスト エディターで十分です。

このクイック スタートでは、[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)サブスクリプション キーまたは有料サブスクリプション キーを使用できます。

## <a name="running-the-application"></a>アプリケーションの実行

このアプリケーションを実行するには、次の手順に従います。

1. [gson ライブラリ](https://github.com/google/gson)をダウンロードまたはインストールします。 また、Maven 経由で入手することもできます。
2. 普段使用している IDE またはエディターで新しい Java プロジェクトを作成します。
3. `VisualSearch.java` という名前のファイルで提供されているコードを追加します。
4. `subscriptionKey` 値を、サブスクリプション キーに置き換えます。
5. プログラムを実行します。

```java
package insightstoken;

import java.util.*;
import java.io.*;



/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.2
 *
 * Once you have compiled or downloaded gson-2.8.2.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac BingImageSearch.java -classpath .;gson-2.8.2.jar -encoding UTF-8
 * java -cp .;gson-2.8.2.jar BingImageSearch
 */

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

// http://hc.apache.org/downloads.cgi (HttpComponents Downloads) HttpClient 4.5.5

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.ContentType;
import org.apache.http.entity.mime.MultipartEntityBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClientBuilder;


public class InsightsToken {

    
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "<yoursubscriptionkeygoeshere>";

    // To get an insights, call the /images/search endpoint. Get the token from
    // the imageInsightsToken field in the Image object.
    static String insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
        CloseableHttpClient httpClient = HttpClientBuilder.create().build();
        
        String knowledgeRequest = "{\"imageInfo\" : {\"imageInsightsToken\" : \"" + insightsToken + "\"}}";

        try {
            HttpEntity entity = MultipartEntityBuilder
                .create()
                .addTextBody("knowledgeRequest", knowledgeRequest, ContentType.create("application/json"))
                .build();

            HttpPost httpPost = new HttpPost(endpoint);
            httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            httpPost.setEntity(entity);
            HttpResponse response = httpClient.execute(httpPost);

            InputStream stream = response.getEntity().getContent();
            String json = new Scanner(stream).useDelimiter("\\A").next();

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(json));
        }
        catch (IOException e)
        {
            e.printStackTrace(System.out);
            System.exit(1);
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }
    
    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    
}
```


<a name="using-nodejs" />

## <a name="using-nodejs"></a>Node.js の使用

### <a name="prerequisites"></a>前提条件

このコードを実行するには [Node.js 6](https://nodejs.org/en/download/) が必要です。

このクイック スタートでは、[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)サブスクリプション キーまたは有料サブスクリプション キーを使用できます。

## <a name="running-the-application"></a>アプリケーションの実行

このアプリケーションを実行するには、次の手順に従います。

1. プロジェクト用のフォルダーを作成します (または、お気に入りの IDE やエディターを使用)。
2. コマンド プロンプトまたはターミナルから、先ほど作成したフォルダーに移動します。
3. request モジュールをインストールします。  
  ```  
  npm install request  
  ```  
3. form-data モジュールをインストールします。  
  ```  
  npm install form-data  
  ```  
4. GetVisualInsights.js という名前のファイルを作成し、次のコードを追加します。
5. `subscriptionKey` 値を、サブスクリプション キーに置き換えます。
7. プログラムを実行します。  
  ```
  node GetVisualInsights.js
  ```

```javascript
var request = require('request');
var FormData = require('form-data');

var baseUri = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch';
var subscriptionKey = '<yoursubscriptionkeygoeshere>';

// To get an insights, call the /images/search endpoint. Get the token from
// the imageInsightsToken field in the Image object.
var insightsToken = "ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ";

var knowledgeRequest = {
    "imageInfo" : {
        "imageInsightsToken" : insightsToken
    }
};

var form = new FormData();
form.append('knowledgeRequest', JSON.stringify(knowledgeRequest));

form.getLength(function(err, length){
  if (err) {
    return requestCallback(err);
  }

  var r = request.post(baseUri, requestCallback);
  r._form = form; 
  r.setHeader('Ocp-Apim-Subscription-Key', subscriptionKey);
});

function requestCallback(err, res, body) {
    console.log(JSON.stringify(JSON.parse(body), null, '  '))
}
```


<a name="using-python" />

## <a name="using-python"></a>Python の使用


### <a name="prerequisites"></a>前提条件

このコードを実行するには、[Python 3](https://www.python.org/) が必要です。

このクイック スタートでは、[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)サブスクリプション キーまたは有料サブスクリプション キーを使用できます。

## <a name="running-the-walkthrough"></a>チュートリアルの実行

このアプリケーションを実行するには、次の手順に従います。

1. 普段ご利用の IDE またはエディターで新しい Python プロジェクトを作成します。
2. visualsearch.py という名前のファイルを作成し、このクイック スタートにあるコードを追加します。
3. `SUBSCRIPTION_KEY` 値を、サブスクリプション キーに置き換えます。
4. プログラムを実行します。


```python
"""Bing Visual Search example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json

BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

# To get an insights, call the /images/search endpoint. Get the token from
# the imageInsightsToken field in the Image object.
insightsToken = 'ccid_tmaGQ2eU*mid_D12339146CFEDF3D409CC7A66D2C98D0D71904D4*simid_608022145667564759*thid_OIP.tmaGQ2eUI1yq3yll!_jn9kwHaFZ'

formData = '{"imageInfo":{"imageInsightsToken":"' + insightsToken + '"}}'


file = {'knowledgeRequest' : (None, formData)}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>次の手順

[Bing Visual Search の単一ページ アプリのチュートリアル](tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual Search の概要](overview.md)  
[試してみる](https://aka.ms/bingvisualsearchtryforfree)  
[無料試用版のアクセス キーを入手する](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Search API のリファレンス](https://aka.ms/bingvisualsearchreferencedoc)
