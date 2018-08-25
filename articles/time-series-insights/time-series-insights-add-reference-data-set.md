---
title: Azure Time Series Insights 環境に参照データ セットを追加する方法
description: この記事では、参照データ セットを追加して、Azure Time Series Insights 環境内のデータを増幅する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/15/2018
ms.openlocfilehash: 2cf9c8baf715acf0eef36d640c22f355435c9ca5
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631046"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Azure Portal を使用して Time Series Insights 環境の参照データ セットを作成する

この記事では、Azure Time Series Insights 環境に参照データ セットを追加する方法について説明します。 参照データは、ソース データに結合することにより値を増幅するのに役立ちます。

参照データ セットは、イベント ソースからのイベントを増幅する項目の集まりです。 イベント ソースから受信した各イベントは、Time Series Insights のイングレス エンジンによって、指定した参照データ セット内の対応するデータ行と結合されます。 こうして増幅されたイベントをクエリで利用することができます。 この結合操作は、参照データ セットに定義されている主キー列に基づいて行われます。

参照データは、遡及的に結合されることはありません。 つまり、データが構成されてアップロードされると、現在および将来のイングレス データのみが対応付けられ、参照日付セットに結合されます。

## <a name="add-a-reference-data-set"></a>参照データ セットを追加する

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. 既存の Time Series Insights 環境を見つけます。 Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックします。 Time Series Insights 環境を選択します。

3. **[概要]** ページを選択します。 **[Time Series Insights エクスプローラーの URL]** を見つけ、リンクを開きます。  

   TSI 環境のエクスプローラーを参照します。

4. TSI エクスプローラーで、環境セレクターを展開します。 アクティブな環境を選択します。 エクスプローラー ページの右上にある参照データ アイコンを選択します。

   ![参照データの追加](media/add-reference-data-set/add_reference_data.png)

5. **[+ データ セットの追加]** ボタンをクリックして、新しいデータ セットの追加を開始します。

   ![データ セットの追加](media/add-reference-data-set/add_data_set.png)

6. **[新しい参照データ セット]** ページで、データの形式を選択します。 
   - **[CSV]**(コンマ区切りのデータ) を選択します。 最初の行は、ヘッダー行として扱われます。 
   - **[JSON 配列]**(javascript オブジェクト表記法 (JSON) 形式のデータ) を選択します。

   ![データ形式を選択します。](media/add-reference-data-set/add_data.png)

7. 次のいずれかの方法を使用して、データを指定します。
   - データをテキスト エディターに貼り付けます。 その後、**[参照データの解析]** ボタンをクリックします。
   - **[ファイルの選択]** ボタンをクリックして、ローカル テキスト ファイルからデータを追加します。 

   たとえば、CSV データを貼り付けます。![貼り付けられた CSV データ](media/add-reference-data-set/csv_data_pasted.png)

   たとえば、JSON 配列データを貼り付けます。![JSON データを貼り付ける](media/add-reference-data-set/json_data_pasted.png)

   データ値の解析中にエラーが発生した場合は、ページの下部に赤色でエラーが表示されます (たとえば、`CSV parsing error, no rows extracted`)。

8. データが正常に解析されると、データ グリッドが表示され、データを表す行と列が表示されます。  データ グリッドをレビューし、内容が正しいこと確認してください。

   ![参照データの追加](media/add-reference-data-set/parse_data.png)

9. 各列をレビューして、仮定されたデータ型を確認し、必要であればデータ型を変更します。  列見出しにあるデータ型のシンボルを選択します。**#** はdouble (数値データ)、**T|F** はブール型、**Abc** は文字列を表します。

   ![列見出しでデータ型を選択します。](media/add-reference-data-set/choose_datatypes.png)

10. 必要であれば、列見出しの名前を変更します。 キー列の名前は、イベント ソース内の対応するプロパティに結合させるために必要です。 参照データのキー列名が、大文字小文字の区別も含め、受信データへのイベント名と正確に一致することを確認してください。 非キー列の名前は、受信データを対応する参照データの値で増幅するために使用されます。

11. 必要であれば、**[行の追加]** または **[列の追加]** をクリックして参照データの値を追加します。

12. 必要であれば、**[Filter the rows...]\(行をフィルター...\)** フィールドに値を入力して、特定の行をレビューします。 フィルターはデータをレビューするのに便利ですが、データのアップロード中には適用されません。
 
13. データ グリッドの上の **[データ セット名]** フィールドに値を入力して、データ セットに名前を付けます。

   ![データ セットに名前を付けます。](media/add-reference-data-set/name_reference_dataset.png)

14. データ グリッドの上にあるドロップダウン リストを選択して、データ セット内の **[主キー]** 列を指定します。

   ![キー列を選択します。](media/add-reference-data-set/set_primary_key.png)

   必要であれば、**[+]** ボタンをクリックして、セカンダリ キー列を (複合主キーとして) 追加します。 選択を元に戻す必要がある場合は、ドロップダウン リストから空の値を選択して、セカンダリ キーを削除します。

15.  データをアップロードするには、**[アップロード]** ボタンをクリックします。

   ![アップロード](media/add-reference-data-set/upload_rows.png)

   完了したアップロードが確認され、**[データセットが正常にアップロードされました]** というメッセージが表示されます。

## <a name="next-steps"></a>次の手順
* プログラムで[参照データを管理](time-series-insights-manage-reference-data-csharp.md)する。
* 詳細な API リファレンスについては、[参照データ API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api) に関するドキュメントを参照してください。
