---
title: Azure Data Factory で Hadoop Pig アクティビティを使用してデータを変換する | Microsoft Docs
description: Azure データ ファクトリで Pig アクティビティを使用して、オンデマンドまたは独自の HDInsight クラスターで Pig スクリプトを実行する方法について説明します。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 9e769cc436011defe89b12680150e6f9c3b3faf8
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049319"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Azure Data Factory で Hadoop Pig アクティビティを使用してデータを変換する
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-pig-activity.md)
> * [現在のバージョン](transform-data-using-hadoop-pig.md)

Data Factory [パイプライン](concepts-pipelines-activities.md)の HDInsight Pig アクティビティでは、[独自](compute-linked-services.md#azure-hdinsight-linked-service)または[オンデマンド](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)の HDInsight クラスターで Pig クエリを実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](transform-data.md) に関する記事に基づいています。

Azure Data Factory の使用経験がない場合は、この記事を読む前に、「[Azure Data Factory の概要](introduction.md)」を参照し、[データの変換のチュートリアル](tutorial-transform-data-spark-powershell.md)を実行してください。 

## <a name="syntax"></a>構文

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }   
}
```
## <a name="syntax-details"></a>構文の詳細

| プロパティ            | 説明                              | 必須 |
| ------------------- | ---------------------------------------- | -------- |
| name                | アクティビティの名前                     | [はい]      |
| description          | アクティビティの用途を説明するテキストです。 | いいえ        |
| type                | Hive アクティビティの場合、アクティビティの種類は HDinsightPig です | [はい]      |
| linkedServiceName   | Data Factory のリンクされたサービスとして登録されている HDInsight クラスターへの参照。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。 | [はい]      |
| scriptLinkedService | 実行される Pig スクリプトの格納に使用される Azure Storage のリンクされたサービスへの参照。 このリンクされたサービスを指定していない場合は、HDInsight のリンクされたサービスで定義されている Azure Storage のリンクされたサービスが使用されます。 | いいえ        |
| scriptPath          | scriptLinkedService で参照される Azure Storage に格納されているスクリプト ファイルへのパスを指定します。 ファイル名は大文字と小文字が区別されます。 | いいえ        |
| getDebugInfo        | HDInsight クラスターで使用されている Azure Storage または scriptLinkedService で指定された Azure Storage にログ ファイルがコピーされるタイミングを指定します。 使用できる値: None、Always、または Failure。 既定値: None。 | いいえ        |
| arguments           | Hadoop ジョブの引数の配列を指定します。 引数はコマンド ライン引数として各タスクに渡されます。 | いいえ        |
| defines             | Pig スクリプト内で参照するキーと値のペアとしてパラメーターを指定します。 | いいえ        |

## <a name="next-steps"></a>次の手順
別の手段でデータを変換する方法を説明している次の記事を参照してください。 

* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning バッチ実行アクティビティ](transform-data-using-machine-learning.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
