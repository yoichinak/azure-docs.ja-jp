---
title: Azure Log Analytics データ コレクター API によるデータ パイプラインの作成 | Microsoft Docs
description: Log Analytics HTTP データ コレクター API を使用すると、REST API を呼び出すことのできるクライアントから POST JSON データを Log Analytics リポジトリに追加できます。 この記事では、ファイルに格納されたデータを自動的にアップロードする方法について説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 5f2674aeb83fbb7679d9d0c2574a93484a352e9d
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "40161834"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>データ コレクター API によるデータ パイプラインの作成

Log Analytics には、[Log Analytics データ コレクター API](log-analytics-data-collector-api.md) を使ってあらゆるカスタム データをインポートすることができます。 唯一の要件は、データが JSON 形式であること、また 30 MB 未満のセグメントに分割されていることです。 きわめて柔軟なメカニズムとなっており、アプリケーションにより直接送信されるデータから、必要なときに 1 回限り行われるアップロードまで、さまざまな方法で接続することができます。 この記事では、一般的なシナリオを想定し、ごく基本的な事柄について取り上げています。つまりファイルに格納されているデータを自動化された方法で定期的にアップロードするニーズを想定しています。 ここで紹介しているパイプラインはパフォーマンスや最適化の点で一番優れているわけではありません。運用環境のパイプラインを独自に構築するための出発点となることを意図しています。

## <a name="example-problem"></a>問題の例
以降この記事では、Application Insights のページ ビュー データを詳しく見ていきます。 ここではあるシナリオを想定しています。Application Insights SDK により既定で収集された地理的情報を、世界各国の人口が格納されているカスタム データに関連付けるというものです。マーケティングの予算をどこに一番多く費やすべきかを見極めるのがその目的です。 

そこで、[UN World Population Prospects](https://esa.un.org/unpd/wpp/) などのパブリック データ ソースを使用します。 このデータのスキーマは次のとおり単純です。

![単純なスキーマの例](./media/log-analytics-create-pipeline-datacollector-api/example-simple-schema-01.png)

この例では、一番最近の年のデータが公開された時点ですぐに、そのデータが格納された新しいファイルをアップロードすることを想定しています。

## <a name="general-design"></a>一般的な設計
ここでは、典型的な ETL 型のロジックを使用してパイプラインを設計します。 アーキテクチャの全体像は次のとおりです。

![データ収集パイプラインのアーキテクチャ](./media/log-analytics-create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

この記事では、データを作成する方法や[それを Azure Blob Storage アカウントにアップロード](../storage/blobs/storage-upload-process-images.md)する方法については取り上げません。 ここで注目するのは、BLOB に新しいファイルがアップロードされた直後のフローです。 そこから次の処理が行われます。

1. 新しいデータがアップロードされたことが、いずれかのプロセスによって検出されます。  この例で使用しているのは [Azure Logic Apps](../logic-apps/logic-apps-overview.md) で、BLOB にアップロードされる新しいデータを検出するためのトリガーが用意されています。

2. その新しいデータをプロセッサが読み取り、Log Analytics に必要な形式である JSON に変換します。  この例では、目的の処理コードを実行する軽量かつ費用対効果の高い方法として [Azure Function](../azure-functions/functions-overview.md) を使用します。 この関数は、新しいデータを検出するときにも使ったロジック アプリによって開始されます。

3. 最後に、利用可能な状態になった JSON オブジェクトが Log Analytics に送信されます。 このデータが、同じロジック アプリにより、ビルトインの Log Analytics データ コレクター アクティビティを使って、Log Analytics に送信されます。

Blob Storage、Logic Apps、Azure Functions の詳しい設定については、この記事では取り上げませんが、それぞれの製品のページで詳しい手順をご覧いただけます。

このパイプラインを監視するために、ここでは Application Insights を使って Azure Functions を ([詳細はこちら](../azure-functions/functions-monitoring.md))、Log Analytics を使ってロジック アプリを監視します ([詳細はこちら](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md))。 

## <a name="setting-up-the-pipeline"></a>パイプラインの設定
パイプラインを設定するために、まず BLOB コンテナーが作成され、構成済みであることを確認してください。 同様に、データの送信先となる Log Analytics ワークスペースが作成されていることを確認します。

## <a name="ingesting-json-data"></a>JSON データの取り込み
Logic Apps を使えば JSON データは簡単に取り込むことができ、また変換が不要なので、パイプライン全体を単一のロジック アプリにまとめることができます。 BLOB コンテナーと Log Analytics ワークスペースの両方の構成が済んだら、新しいロジック アプリを作成して次のように構成します。

![ロジック アプリのワークフロー例](./media/log-analytics-create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

ロジック アプリを保存し、そのテストを行います。

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>XML や CSV など各種形式のデータを取り込む
現在の Logic Apps には、XMLや CSV など各種データを JSON 形式に簡単に変換するビルトインの機能はありません。 そのため、こうした変換を行うためには別の手段が必要となります。 この記事では、きわめて軽量かつ費用効果の高い方法として、Azure Functions というサーバーレスの計算機能を使用します。 

この例で解析するのは CSV ファイルですが、その他あらゆる種類のファイルを同様に処理することが可能です。 特定のデータの種類に合った適切なロジックを反映させたければ、その Azure 関数の、逆シリアル化のための機能にだけ変更を加えればよいのです。

1.  新しい Azure 関数を作成します。確認を求められたら、関数ランタイム v1 と使用量ベースを使用してください。  テンプレートには、C# を対象とした **[HTTP トリガー]** を選択します。必要なバインディングが最初から構成されています。 
2.  右側のウィンドウにある **[ファイルの表示]** タブから **project.json** という新しいファイルを作成し、使用している NuGet パッケージから次のコードを貼り付けます。

    ![Azure Functions のプロジェクト例](./media/log-analytics-create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. 右側のウィンドウから **run.csx** に切り替えて、既定のコードを次のコードに置き換えます。 

    >[!NOTE]
    >実際のプロジェクトでは、レコード モデル ("PopulationRecord" クラス) を独自のデータ スキーマに置き換える必要があります。
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. 関数を保存します。
5. 関数をテストして、そのコードが正しく動作することを確認します。 右側のウィンドウで **[テスト]** タブに切り替えて、次のようにテストを構成します。 **[要求本文]** ボックスに、サンプル データが格納されている BLOB へのリンクを配置します。 **[実行]** をクリックすると、**[出力]** ボックスに JSON 出力が表示されます。

    ![Function App のテスト コード](./media/log-analytics-create-pipeline-datacollector-api/functions-test-01.png)

今度は、取り込んだデータを追加して JSON 形式に変換するために、作りかけになっていたロジック アプリに戻って変更を加える必要があります。  ビュー デザイナーを使用してロジック アプリを次のように構成し、保存します。

![Logic Apps ワークフロー全体の例](./media/log-analytics-create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>パイプラインのテスト
では、先ほど構成した BLOB に新しいファイルをアップロードし、ロジック アプリで監視してみましょう。 ほどなくロジック アプリの新しいインスタンスが起動し、Azure 関数を呼び出した後、データを Log Analytics に無事送信したことが確認できると思います。 

>[!NOTE]
>新しい種類のデータを初めて送信するときは、Log Analytics にデータが表示されるまでに最大 30 分程度かかる場合があります。


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Log Analytics と Application Insights 内の他のデータと関連付ける
カスタム データ ソースから取り込んだ人口データに Application Insights のページ ビュー データを関連付けるという目標を達成するために、Application Insights の分析ウィンドウまたは Log Analytics ワークスペースから次のクエリを実行します。

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

出力結果には、2 つのデータ ソースが結合された状態で表示されます。  

![ばらばらのデータを検索結果で関連付ける例](./media/log-analytics-create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>運用環境でパイプラインを使ううえで推奨される改善
この記事で紹介したのは、実用的なプロトタイプであり、その背景にあるロジックは、実際の運用環境レベルのソリューションに応用することができます。 そうした運用環境に耐えるレベルのソリューションとするために推奨される改善点を次に示します。

* ロジック アプリと関数に、エラー処理と再試行ロジックを追加します。
* Log Analytics Ingestion API の呼び出しに関して、その 1 回あたりの上限である 30 MB を超えないようにするためのロジックを追加します。 必要であれば、データをもっと小さいセグメントに分割します。
* Blob Storage に対するクリーンアップ ポリシーを設定します。 Log Analytics への送信に成功したら、生データを保管しておく必要はありません (アーカイブ目的で生データを保存したい場合を除く)。 
* パイプライン全域で監視が有効になっていることを確認します。適宜トレース ポイントやアラートを追加してください。
* ソース管理を活用して、関数とロジック アプリのコードを管理します。
* 適切な変更管理ポリシーへの準拠を徹底します。スキーマに変更が生じた場合には、それに基づいて関数とロジック アプリが変更されるようにしてください。
* 複数の異なる種類のデータをアップロードする場合は、それらを BLOB コンテナー内の個別のフォルダーに分け、データの種類に応じて分散するようにロジックを作成します。 


## <a name="next-steps"></a>次の手順
REST API クライアントから[データ コレクター API](log-analytics-data-collector-api.md) を使って Log Analytics にデータを書き込む方法の詳細を確認します。