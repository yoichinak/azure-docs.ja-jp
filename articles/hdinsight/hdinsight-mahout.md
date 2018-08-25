---
title: Mahout と HDInsight を使用した PowerShell からのレコメンデーションの生成 - Azure
description: クライアント上で実行される PowerShell スクリプトから Apache Mahout 機械学習ライブラリを使用して HDInsight (Hadoop) で映画のリコメンデーションを生成する方法について説明します。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: jasonh
ms.openlocfilehash: 587ea8d9082a696853d8e25a36d9536c762d0582
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599991"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>HDInsight で Apache Mahout と Hadoop を使用して映画のリコメンデーションを生成する (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

[Apache Mahout](http://mahout.apache.org) 機械学習ライブラリを使用して Azure HDInsight で映画のリコメンデーションを生成する方法について説明します。 このドキュメントの例では、Azure PowerShell を使用して、Mahout ジョブを実行します。

## <a name="prerequisites"></a>前提条件

* Linux ベースの HDInsight クラスター。 作成の詳細については、「[Hadoop チュートリアル: HDInsight で Linux ベースの Hadoop を使用する][getstarted]」を参照してください。

    > [!IMPORTANT]
    > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Azure PowerShell を使用したリコメンデーションの生成

> [!WARNING]
> このセクションのジョブは Azure PowerShell を使用することによって機能します。 Mahout で提供されるクラスの多くは Azure PowerShell では現在動作しません。 Azure PowerShell で動作しないクラスの一覧については、「[トラブルシューティング](#troubleshooting)」セクションを参照してください。
>
> SSH を使用した HDInsight への接続とクラスターでの Mahout サンプルの直接的な実行の例については、[Mahout と HDInsight を使用した映画のリコメンデーションの生成 (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md)に関するページを参照してください。

Mahout で提供される機能の 1 つが、リコメンデーション エンジンです。 データは、`userID`、`itemId`、`prefValue` (項目に対するユーザーの嗜好) の形式で受け付けられます。 Mahout はデータを使用して、項目の嗜好が似ているユーザーを特定します。これはリコメンデーションの作成に使用できます。

次の例は、リコメンデーション処理のしくみを示す簡略化されたチュートリアルです。

* **共起**: Joe、Alice、Bob は全員、好きな映画として「*Star Wars (スター ウォーズ)*」、「*The Empire Strikes Back (帝国の逆襲)*」、「*Return of the Jedi (ジェダイの帰還)*」を挙げました。 Mahout では、これらの映画のいずれかを好きなユーザーが他の 2 作品も好きであると判断します。

* **共起**: Bob と Alice は「*The Phantom Menace (ファントム メナス)*」、「*Attack of the Clones (クローンの攻撃)*」、「*Revenge of the Sith (シスの復讐)*」も好きな映画として選びました。 Mahout では、この例の最初の 3 作品を好きなユーザーがこれらの作品も好きであると判断します。

* **類似性のリコメンデーション**: Joe は、この例の最初の 3 作品を好きな映画として選びました。Mahout では、嗜好が似ている他のユーザーが好きな映画の中で、Joe がまだ観ていない映画を調べます (好み/順位)。 この場合、Mahout では、「*The Phantom Menace (ファントム メナス)*」、「*Attack of the Clones (クローンの攻撃)*」、「*Revenge of the Sith (シスの復讐)*」を推薦します。

### <a name="understanding-the-data"></a>データの説明

[GroupLens Research][movielens] では、Mahout と互換性のある形式で映画の評価データを提供します。 このデータは、クラスターの既定の記憶域 ( `/HdiSamples/HdiSamples/MahoutMovieData`) にあります。

2 つのファイル `moviedb.txt` (映画に関する情報) と `user-ratings.txt` があります。 `user-ratings.txt` ファイルは分析時に使用されます。 `moviedb.txt` ファイルは、分析の結果を表示するときにわかりやすいテキストを提供するために使用されます。

user-ratings.txt に含まれているデータの構造は `userID`、`movieID`、`userRating`、および `timestamp` です。これは、各ユーザーが映画にどれだけ高い評価を付けているか示しています。 次にデータの例を示します。

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>ジョブを実行する

次の Windows PowerShell スクリプトを使用して、映画データで Mahout リコメンデーション エンジンを使用するジョブを実行します。

> [!NOTE]
> このファイルは、HDInsight クラスターに接続してジョブを実行するために使用される情報の入力を求めます。 ジョブが完了し、output.txt ファイルがダウンロードされるまで、数分かかる場合があります。

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]
> Mahout ジョブは、ジョブの処理中に作成された一時データを削除しません。 このサンプル ジョブでは `--tempDir` パラメーターを指定し、一時ファイルを特定のディレクトリに分離します。

Mahout ジョブは出力を STDOUT に返しません。 代わりに、指定された出力ディレクトリに **part-r-00000**として格納します。 このファイルは、ワークステーションの現在のディレクトリ内の **output.txt** にダウンロードされます。

次のテキストは、ファイルの内容の例です。

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

最初の列は `userID`です。 "[" と "]" に含まれる値は `movieId`:`recommendationScore` です。

このスクリプトは、出力を読みやすい形式にするために必要な `moviedb.txt` ファイルと `user-ratings.txt` ファイルのダウンロードも実行します。

### <a name="view-the-output"></a>出力を表示する

生成された出力はアプリケーションで使用できるものですが、わかりやすいものではありません。 サーバーの `moviedb.txt` を使用して、`movieId`を映画名に解決できます。 次の PowerShell スクリプトを使用して、映画名とリコメンデーションを表示します。

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

リコメンデーションをわかりやすい形式で表示するには、次のコマンドを使用します。 

```powershell
.\show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt
```

出力は次のテキストのようになります。

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="cannot-overwrite-files"></a>ファイルを上書きできない

Mahout ジョブは、処理中に作成された一時ファイルをクリーンアップしません。 さらに、このジョブは既存の出力ファイルを上書きしません。

Mahout ジョブの実行中のエラーを回避するには、実行と実行の間で一時ファイルと出力ファイルを削除します。 このドキュメントのここまでのスクリプトによって作成されたファイルを削除するには、次の PowerShell スクリプトを使用します。

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard,
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Azure PowerShell で動作しないクラス

次のクラスを使用する Mahout ジョブを Windows PowerShell から使用すると、さまざまなエラー メッセージが返されます。

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

これらのクラスを使用するジョブを実行するには、SSH を使用して HDInsight クラスターに接続し、コマンド ラインからジョブを実行します。 SSH を使用した Mahout ジョブの実行例については、[Mahout と HDInsight を使用した映画のリコメンデーションの作成 (SSH)](hadoop/apache-hadoop-mahout-linux-mac.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

ここまで、Mahout の使用方法を学習し、HDInsight でデータを操作するその他の方法を確認してきました。

* [HDInsight での Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight での Pig の使用](hadoop/hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hadoop/hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
