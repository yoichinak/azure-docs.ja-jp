---
title: Machine Learning Studio にデータをインポートする | Microsoft Docs
description: さまざまなデータ ソースから Azure Machine Learning Studio にデータをインポートする方法 サポートされているデータ型とデータ形式に関する説明
keywords: データのインポート、データ形式、データ型、データ ソース、トレーニング データ
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: c194ee3b-838c-4efe-bb2a-c1d052326216
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: a5750555802489b41b007831164767beb953ebc4
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "34837465"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>さまざまなデータ ソースから Azure Machine Learning Studio にトレーニング データをインポートする
Machine Learning Studio で独自のデータを使用して予測分析ソリューションを開発し、トレーニングする場合、次の操作を実行できます。 

* ハード ドライブの **ローカル ファイル** を事前にアップロードし、ワークスペースにデータセット モジュールを作成する
* [データのインポート][import-data] モジュールで実験を実行している間に、いずれかの**オンライン データ ソース**からデータにアクセスする 
* **データセット**として保存された他の Azure Machine Learning 実験のデータを使用する
* オンプレミスの **SQL Server Database** からデータをコピーする

これらの各オプションについては、下にあるメニューの各トピックを参照してください。 各トピックでは、多様なデータ ソースのデータをインポートして Machine Learning Studio で使用する方法が説明されています。 

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

> [!NOTE]
> Machine Learning Studio には、トレーニング データで使用できるさまざまなサンプル データセットが用意されています。 詳細については、「 [Azure Machine Learning Studio におけるサンプル データセットの使用](use-sample-datasets.md)」を参照してください。
> 
> 

この概要のトピックでは、Machine Learning Studio で使用できるようにデータを準備する方法と、サポートされるデータ形式とデータ型についても説明します。 

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

## <a name="get-data-ready-for-use-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio で使用できるようにデータを準備する
Machine Learning Studio は、区切られたテキスト データやデータベースからの構造化されたデータなど、四角形のデータや表形式のデータで作業するように設計されています。場合によっては、四角形以外のデータが使用されることもあります。

比較的クリーンなデータが最適です。 具体的には、実験にデータをアップロードする前に、引用符で囲まれていない文字列などを処理しておくことをお勧めします。

ただし、Machine Learning Studio には、実験内でデータを操作できるモジュールが用意されています。 使用する機械学習アルゴリズムに応じて、値の欠落やスパース データなどのデータ構造上の問題を処理する際に、その対処方法を決定する必要があります。そのような場合に、これらのモジュールが役に立ちます。 これらの関数を実行するモジュールは、モジュール パレットの **[Data Transformation]** セクションで確認します。

実験中は、出力ポートをクリックすることで、モジュールで生成されたデータをいつでも表示またはダウンロードできます。 モジュールによっては、使用可能なダウンロードのオプションが異なる場合があります。また、Web ブラウザーのデータを Machine Learning Studio で視覚化できる場合もあります。

## <a name="data-formats-and-data-types-supported"></a>サポートされるデータ形式とデータ型
データのインポートに使用するメカニズムや、データの送信元に応じて、さまざまなデータ型を実験にインポートできます。

* プレーン テキスト (.txt)
* コンマ区切り値 (CSV) - ヘッダー付き (.csv) またはヘッダーなし (.nh.csv)
* タブ区切り値 (TSV) - ヘッダー付き (.tsv) またはヘッダーなし (.nh.tsv)
* Excel ファイル
* Azure テーブル
* Hive テーブル
* SQL データベース テーブル
* OData 値
* SVMLight データ (.svmlight) (形式の詳細については、「 [SVMLight の定義](http://svmlight.joachims.org/) 」をご覧ください)。
* 属性関係ファイル形式 (ARFF) データ (.arff) (「 [ARFF の定義](http://weka.wikispaces.com/ARFF) 」をご覧ください)。
* Zip ファイル (.zip)
* R オブジェクトまたはワークスペース ファイル (.RData)

メタデータを含む ARFF などの形式でデータをインポートする場合、Machine Learning Studio はこのメタデータを使用して各列の見出しとデータ型を定義します。

このメタデータが含まれていない TSV や CSV 形式などのデータをインポートする場合、Machine Learning Studio は、データをサンプリングすることによって、各列のデータ型を推論します。 また、データに列見出しがない場合、Machine Learning Studio は既定の名前を提供します。

[メタデータの編集][edit-metadata]を使用して、列の見出しやデータ型を明示的に指定または変更できます。

次の **データ型** は、Machine Learning Studio によって認識されます。

* String
* 整数
* Double
* ブール
* Datetime
* timespan

Machine Learning Studio は***データ テーブル***と呼ばれる内部データ型を使用し、モジュール間でデータを渡します。 [データセットへの変換][convert-to-dataset]モジュールを使用して、データ テーブル形式にデータを明示的に変換できます。

データ テーブル以外の形式を受け取るどのモジュールでも、次のモジュールに渡す前に、サイレント モードでデータをデータ テーブルに変換します。

必要に応じて、その他の変換モジュールを使用して CSV、TSV、ARFF、SVMLight 形式にデータ テーブル形式をもう一度変換できます。
これらの関数を実行するモジュールについては、モジュール パレットの **[Data Format Conversions]** セクションを確認してください。

<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
