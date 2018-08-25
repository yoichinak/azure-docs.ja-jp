---
title: Computer Vision API を使用したリアルタイムのビデオ分析 | Microsoft Docs
description: ライブ ビデオ ストリームから取得したフレームに対して、Cognitive Services の Computer Vision API を使用して、ほぼリアルタイムに分析を実行します。
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/20/2017
ms.author: kefre
ms.openlocfilehash: d75b1a887e5e4557d5464d8062e1bde628e7adab
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374168"
---
# <a name="how-to-analyze-videos-in-real-time"></a>リアルタイムでビデオを分析する方法
このガイドでは、ライブ ビデオ ストリームから取得したフレームに対して、ほぼリアルタイムに分析を実行する方法を示します。 そのようなシステムの基本コンポーネントは、以下のとおりです。
- ビデオ ソースからフレームを取得する
- 分析するフレームを選択する
- API にこれらのフレームを送信する
- API 呼び出しから返される各分析結果を利用する

これらのサンプルは C# で記述されており、コードは GitHub ([https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)) にあります。

## <a name="the-approach"></a>アプローチ
ビデオ ストリームに対してほぼリアルタイムの分析を実行する際の問題を解決する方法は複数あります。 難しさのレベルが低い方から高い方へ、3 つのアプローチの概要を説明してゆきます。

### <a name="a-simple-approach"></a>シンプルなアプローチ
ほぼリアルタイムの分析システムに向けた最もシンプルな設計は、無限ループです。ループでは、繰り返しごとにフレームを取り込み、分析してから結果を使用します。
```CSharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```
分析が軽量のクライアント側アルゴリズムで構成されている場合は、このアプローチが適しています。 ただし、分析がクラウドで行われるときは、それに伴う待ち時間のために、API 呼び出しに数秒かかることがあり、その間はイメージのキャプチャは行われず、スレッドは基本的に何もしません。 最大フレーム レートは、API 呼び出しの待ち時間によって制限されます。

### <a name="parallelizing-api-calls"></a>API 呼び出しの並列化
シンプルな単一スレッドのループは、軽量のクライアント側アルゴリズムに適していますが、クラウド API 呼び出しに伴う待ち時間にはうまく適合しません。 この問題の解決策は、実行時間の長い API 呼び出しを、フレームの取り込みと並列に実行できるようにすることです。 C# の場合は、タスクベースの並列化を使用してこれを実現できます。
```CSharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () => 
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```
これで、各分析が別個のタスクで開始され、新しいフレームを取り込んでいる間にそれらをバックグラウンドで実行することができます。 このため、API 呼び出しが返されるまで待つ間、メイン スレッドはブロックされなくなります。ただし、シンプルなバージョンで提供されている保証の一部は失われます。複数の API 呼び出しが並列に行われて、正しくない順序で結果が返されることがあります。 また、これにより、複数のスレッドが同時に ConsumeResult() 関数に入る可能性も生じますが、関数がスレッドセーフでない場合は、それが危険である可能性があります。 最後に、このシンプルなコードは作成されるタスクを追跡しないので、例外はメッセージを残さずに消失します。 したがって、追加する最終的な構成要素は、分析タスクを追跡し、例外を発生させ、実行時間の長いタスクを強制終了し、一度に 1 つずつ正しい順序で結果が使用されるようにする "コンシューマー" スレッドです。

### <a name="a-producer-consumer-design"></a>プロデューサー/コンシューマー デザイン
最後の "プロデューサー/コンシューマー" システムには、前述した無限ループによく似たプロデューサー スレッドがあります。 ただし、プロデューサーは、利用できるようになるとすぐに分析結果を使用するのではなく、単純にタスクをキューに入れて追跡します。
```CSharp
// Queue that will contain the API call tasks. 
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();
     
// Producer thread. 
while (true)
{
    // Grab a frame. 
    Frame f = GrabFrame();
 
    // Decide whether to analyze the frame. 
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread. 
        var analysisTask = Task.Run(async () => 
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }
        
        // Push the task onto the queue. 
        taskQueue.Add(analysisTask);
    }
}
```
また、コンシューマー スレッドもあり、これはタスクをキューから取り出し、タスクが完了するのを待機して、結果を表示するか、スローされた例外を発生させます。 キューの使用により、システムの最大フレーム レートを制限せずに一度に 1 つずつ正しい順序で結果が使用されることを保証できます。
```CSharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
    var output = await analysisTask;
     
    // Consume the exception or result. 
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>ソリューションの実装
### <a name="getting-started"></a>Getting Started (概要)
できるだけ迅速にアプリを稼働させるために、多くのシナリオを実装するのに十分な柔軟性を確保し、同時に使いやすくなるように考慮して、前述したシステムを実装しています。 コードにアクセスするには、[https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis) に移動します。

ライブラリには、FrameGrabber クラスが含まれています。これは Web カメラからのビデオ フレームを処理するために、前述したプロデューサー/コンシューマー システムを実装しています。 ユーザーは正確なフォームの API 呼び出しを指定でき、クラスでは、新しいフレームが獲得されたこと、または新しい分析結果が利用可能になったことを呼び出し側のコードに通知するためにイベントを使用します。

いくつかの可能性を示すため、このライブラリを使用するサンプル アプリが 2 つ用意されています。 1 つはシンプルなコンソール アプリで、これを簡略化したバージョンを下に示しています。 これは既定の Web カメラからのフレームを取得し、それらを顔検出のために Face API に送信します。
```CSharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face API Client. Insert your Face API key here.
            FaceServiceClient faceClient = new FaceServiceClient("<subscription key>");

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```
2 つ目のサンプル アプリはさらに少し興味深いもので、ビデオ フレームに対してどの API を呼び出すかの選択が可能になっています。 アプリの左側には、ライブ ビデオのプレビューが表示され、右側には、対応するフレームにオーバーレイされる最新の API 結果が表示されます。

ほとんどのモードで、左のライブ ビデオと右に表示された分析との間に視覚的な遅延が生じます。 この遅延は、API 呼び出しにかかった時間です。 これに該当しないのは、"EmotionsWithClientFaceDetect" モードになっているときで、このモードでは、Cognitive Services にイメージを送信する前に、OpenCV を使用してクライアント コンピューターでローカルに顔検出が実行されます。 こうすることで、検出した顔をすぐに表示し、その後 API 呼び出しが戻ったときに後から感情を更新できます。 これは "ハイブリッド" アプローチの可能性を示しており、クライアント上で単純な処理をいくつか実行してから、必要に応じて Cognitive Services APIs を使用し、より高度な分析によって処理を強化することができます。

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>コードベースへの統合
このサンプルを開始するには、次の手順に従います。

1. Vision API の API キーを[サブスクリプション](https://azure.microsoft.com/try/cognitive-services/)から取得します。 ビデオ フレーム分析の場合、適用可能な API は次のとおりです。
    - [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Emotion API](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub リポジトリを複製します

3. Visual Studio 2015 でサンプルを開き、サンプル アプリケーションをビルドして実行します。
    - BasicConsoleSample の場合、Face API キーは、[BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) 内に直接ハードコードされています。
    - LiveCameraSample の場合、アプリの設定ウィンドウにキーを入力する必要があります。 これらは、セッションを移動してもユーザー データとして残されます。
        

統合する準備ができたら、**自分のプロジェクトから単純に VideoFrameAnalyzer ライブラリを参照します。** 



## <a name="developer-code-of-conduct"></a>開発者の倫理規定
どの Cognitive Services を使用する場合でも同じですが、Microsoft の API とサンプルを使用して開発を行う開発者は、[Microsoft Cognitive Services 開発者の倫理規定](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)に従う必要があります。 


VideoFrameAnalyzer のイメージ、音声、ビデオ、またはテキストの解釈機能は、Microsoft Cognitive Services を使用しています。 Microsoft は、開発者が (このアプリから) アップロードするイメージ、音声、ビデオ、およびその他のデータを受け取り、サービス向上の目的でそれらを使用する場合があります。 アプリによって Microsoft Cognitive Services にデータが送信される人々の保護に、開発者の方のご協力をお願いします。 


## <a name="summary"></a>まとめ
このガイドでは、Face API、Computer Vision API、および Emotion API を使用してライブ ビデオ ストリームに対するほぼリアルタイムの分析を実行する方法と、サンプル コードを使用して開発作業を開始する方法について説明しました。 [Microsoft Cognitive Services のサインアップ ページ](https://azure.microsoft.com/try/cognitive-services/)で取得した無料の API キーを使用して、アプリの構築を開始できます。 

[GitHub リポジトリ](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)では、お気軽にフィードバックや提案を提供してください。API に関するより幅広いフィードバックについては、[UserVoice サイト](https://cognitive.uservoice.com/)にお寄せください。

