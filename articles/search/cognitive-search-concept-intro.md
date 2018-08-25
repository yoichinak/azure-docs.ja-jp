---
title: Azure Search でのデータ抽出、自然言語 AI 処理のためのコグニティブ検索 | Microsoft Docs
description: 認知スキルと AI アルゴリズムを使用したコンテンツ抽出、自然言語処理 (NLP) および画像処理を実行することにより、Azure Search インデックスで検索可能なコンテンツを作成します。
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: heidist
ms.openlocfilehash: 72d1630ecaeada3acf8b49952a31ccd3ae8634aa
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617960"
---
# <a name="what-is-cognitive-search"></a>コグニティブ検索とは

コグニティブ検索では、インデックス パイプラインに AI アルゴリズムを追加することにより、検索不可能なコンテンツから検索可能な情報を作成します。 AI 統合は、検索インデックスへのルートにソース ドキュメントをエンリッチメントする*認知スキル*を使って実行されます。 

**自然言語処理**スキルには、[エンティティ認識](cognitive-search-skill-named-entity-recognition.md)、言語検出、[キー フレーズ抽出](cognitive-search-skill-keyphrases.md)、テキスト操作、およびセンチメント検出が含まれます。 これらのスキルによって、構造化されていないテキストが構造化され、インデックス内の検索とフィルターが可能なフィールドにマップされます。

**画像処理**には、[OCR](cognitive-search-skill-ocr.md) および[ビジュアル フィーチャー](cognitive-search-skill-image-analysis.md)の特定 (例: 顔検出、画像の解釈、画像の認識 (有名な人やランドマーク)、色や画像の向きなどの属性) が含まれます。 Azure Search のすべてのクエリ機能を使用して、検索可能な画像コンテンツのテキスト表現を作成できます。

![コグニティブ検索パイプライン ダイアグラム](./media/cognitive-search-intro/cogsearch-architecture.png "コグニティブ検索パイプラインの概要")

Azure Search における認知スキルは、[名前付きエンティティ認識 API](cognitive-search-skill-named-entity-recognition.md)、[キー フレーズ抽出 API](cognitive-search-skill-keyphrases.md)、[OCR API](cognitive-search-skill-ocr.md) などの、Cognitive Services APIs で使用されているものと同じ AI アルゴリズムに基づいています。 

自然言語および画像処理はデータ インジェスト フェーズで適用され、結果は Azure Search における検索可能なインデックス内のドキュメントの構成の一部になります。 データは Azure データ セットとして調達され、必要な[組み込みのスキル](cognitive-search-predefined-skills.md)を使用してインデックス パイプライン経由でプッシュされます。 アーキテクチャは拡張可能なため、組み込みのスキルでは不十分な場合は、[カスタム スキル](cognitive-search-create-custom-skill-example.md)を作成して追加し、カスタム処理を統合できます。 例としては、金融、科学出版物、医療などの専門分野を対象としたカスタム エンティティ モジュールまたはドキュメント分類子が挙げられます。

> [!NOTE]
> 認知検索はパブリック プレビューの段階にあり、スキルセットの実行は現在無料で提供されています。 この機能の価格は後日発表される予定です。 

## <a name="components-of-cognitive-search"></a>コグニティブ検索のコンポーネント

コグニティブ検索は [Azure Search](search-what-is-azure-search.md) のプレビュー機能で、米国中南部および西ヨーロッパのすべての階層で使用できます。 

コグニティブ検索パイプラインは、データ ソースをクロールし、エンドツーエンドのインデックス処理を提供する、[Azure Search *インデクサー*](search-indexer-overview.md)を使用します。 インデクサーに接続されたスキルは、ユーザーが定義したスキルセットに従ってドキュメントをインターセプトし、エンリッチメントします。 インデックスが作成されると、[Azure Search でサポートされているすべての種類のクエリ](search-query-overview.md)から検索を要求してコンテンツにアクセスできます。  インデクサーを使い慣れていない場合は、このセクションで手順を示します。

### <a name="source-data-and-document-cracking-phase"></a>ソース データとドキュメント解読フェーズ

パイプラインの先頭には、非構造化テキストまたはテキスト以外のコンテンツ (画像、スキャンされたドキュメントの JPEG ファイルなど) があります。 データは、インデクサーによってアクセスできる Azure のデータ ストレージ サービスに存在する必要があります。 インデクサーは、ソース ドキュメントを "解読" してソース データからテキストを抽出できます。

![ドキュメントの解読フェーズ](./media/cognitive-search-intro/document-cracking-phase-blowup.png "ドキュメントの解読")

 サポートされているソースには、Azure Table Storage、Azure Table Storage、Microsoft Azure SQL Database、および Microsoft Azure Cosmos DB などがあります。 テキスト ベースのコンテンツは、PDF、Word、PowerPoint、CSV のファイル タイプから抽出できます。 詳細な一覧については、[サポートされている形式](search-howto-indexing-azure-blob-storage.md#supported-document-formats)に関するページをご覧ください。

### <a name="cognitive-skills-and-enrichment-phase"></a>認知スキルとエンリッチメント フェーズ

エンリッチメントは、アトミック操作を実行する*認知スキル*を使用して実行されます。 たとえば、PDF からテキスト コンテンツを作成した場合、エンティティの認識言語検出またはキー フレーズ抽出を適用して、ソースではネイティブで使用できない新しいフィールドを生成できます。 パイプラインで使用される技術を総称して*スキルセット*と呼びます。  

![エンリッチメント フェーズ](./media/cognitive-search-intro/enrichment-phase-blowup.png "エンリッチメント フェーズ")

スキルセットは、[定義済みの認知スキル](cognitive-search-predefined-skills.md)またはユーザーが定義施設続する[カスタム スキル](cognitive-search-create-custom-skill-example.md)として作成されます。 スキルセットは、単純なものから複雑なものまで設定でき、処理の種類だけでなく、演算の順序も決定します。 スキルセットに加え、インデクサーの一部として定義されたフィールド マッピングによってエンリッチメント パイプラインが指定されます。 これらのコンポーネントの統合方法については、[スキルセットの定義](cognitive-search-defining-skillset.md)に関する記事を参照してください。

内部的には、パイプラインは、エンリッチメントされたドキュメントのコレクションを生成します。 エンリッチメントされたドキュメントのどの部分を、検索インデックス内のインデックスを作成可能なフィールドにマップするかをユーザーが決定できます。 たとえば、キー フレーズ抽出およびエンティティ認識スキルを適用した場合、これらの新しいフィールドはエンリッチメントされたドキュメントの一部になり、インデックス上のフィールドにマッピングできます。 入出力の構造の詳細については、[注釈](cognitive-search-concept-annotations-syntax.md)を参照してください。

### <a name="search-index-and-query-based-access"></a>検索インデックスとクエリ ベースのアクセス

処理が完了したら、エンリッチメントされたドキュメントで構成された、Azure Search で完全にテキスト検索が可能な検索コーパスが取得できます。 [インデックスをクエリ](search-query-overview.md)することで、開発者やユーザーは、パイプラインによって生成されるエンリッチメントされたコンテンツにアクセスできます。 

![[検索] アイコン付きのインデックス](./media/cognitive-search-intro/search-phase-blowup.png "[検索] アイコン付きのインデックス")

インデックスは、Azure Search のために作成する他のコンポーネント同様、カスタム アナライザーで補完したり、ファジー検索クエリを呼び出したり、フィルターを設定して検索したり、スコアリング プロファイルを使って検索結果の形式を調整することができます。

インデックスは、フィールド、属性、スコアリング プロファイルやシノニム マップなどの特定のインデックスに関連づけられているその他のコンストラクトを定義するインデックス スキーマから生成されます。 インデックスが定義され、作成されたら、新規あるいは更新されたソース ドキュメントに対応してインデックスを増分できます。 特定の変更では、完全な再構築が必要です。 スキーマ デザインが安定するまで、小規模のデータ セットを使用することをお勧めします。 詳細については、「[インデックスの再構成と再構築](search-howto-reindex.md)」をご覧ください。

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>主要機能および概念

| 概念 | 説明| リンク |
|---------|------------|-------|
| スキルセット | スキルのコレクションを含む最上位の名前付きリソースです。 スキルセットはエンリッチメント パイプラインです。 スキルセットは、インデクサーによるインデックス作成時に呼び出されます。 | [スキルセットを定義する](cognitive-search-defining-skillset.md) |
| 認知スキル | 認知スキルは、エンリッチメント パイプラインでのアトミック変換です。 多くの場合、構造を抽出または推論し、ユーザーによる入力データの理解を補佐するコンポーネントです。 ほとんどの場合、テキスト形式で出力され、画像入力からテキストを抽出または生成する自然言語処理または画像処理で処理されます。 スキルからの出力は、インデックス内のフィールドにマッピングするか、または下流のエンリッチメントの入力として使用できます。 スキルは、定義済みか、Microsoft が提供、またはカスタム スキルとしてお客様が作成および展開できます。 | [定義済みのスキル](cognitive-search-predefined-skills.md) |
| データの抽出 | 一般的に、そのままでは情報を提供しないソースからデータ (エンティティ) を抽出するために使用される名前付きエンティティ認知スキルであるコグニティブ検索に関連する、さまざまな処理について説明します。 | [名前付きエンティティの認識スキル](cognitive-search-skill-named-entity-recognition.md)| 
| 画像処理 | ランドマークを認識する、あるいは画像からテキストを抽出する能力など、画像からテキストを推測します。 一般的な例としては、スキャンしたドキュメント (JPEG) ファイルから文字を認識する OCR や道路標識を含む写真の番地を認識する能力が含まれます。 | [画像の分析スキル](cognitive-search-skill-image-analysis.md)または[OCR スキル](cognitive-search-skill-ocr.md)
| 自然言語処理 | テキスト入力に関する洞察や情報を取得するテキスト処理。 言語の検出、センチメント分析、およびキー フレーズの抽出は自然言語処理に分類されるスキルです。  | [キー フレーズ抽出スキル](cognitive-search-skill-keyphrases.md)、[言語検出スキル](cognitive-search-skill-language-detection.md)、[感情分析スキル](cognitive-search-skill-sentiment.md) |
| ドキュメントの解読 | インデックス作成時にテキスト以外のソースからテキスト コンテンツを抽出または作成する処理。 光学式文字認識 (OCR) はこの例ですが、インデクサーがアプリケーション ファイルからコンテンツを抽出するため、一般的に、コア インデクサー機能を指します。 ソース ファイルの場所を規定するデータソースと、フィールドをマッピングを規定するインデクサー定義は、いずれも、ドキュメント解読処理で重要な要因です。 | [インデクサー](search-indexer-overview.md)を参照してください。 |
| シェーパー | テキスト フラグメントをより大きな構造に統合したり、逆に大きなテキスト フラグメントを下流プロセスで処理できるよう管理可能なサイズに分割します。 | [シェーパー スキル](cognitive-search-skill-shaper.md)、[テキスト マージャー スキル](cognitive-search-skill-textmerger.md)、[テキスト 分割スキル](cognitive-search-skill-textsplit.md) |
| エンリッチメントされたドキュメント | コード内からは直接アクセスできない、一時的な内部構造です。 処理中にエンリッチメントされたドキュメントが生成されますが、検索インデックスに保存されるのは最終出力のみです。 フィールド マッピングにより、どのデータ要素をインデックスに追加するかが決定されます。 | [エンリッチメントされたドキュメントへのアクセス](cognitive-search-tutorial-blob.md#access-enriched-document)に関する記事を参照してください。 |
| Indexer |  検索可能なデータとメタデータを外部データ ソースから抽出し、ドキュメント解読のために、インデックスとデータ ソース間のフィールド対フィールドのマッピングに基づいてインデックスを作成するクローラーです。 コグニティブ検索エンリッチメントのために、インデクサーはスキルセットを呼び出し、インデックス内のターゲット フィールドとエンリッチメント出力を関連付けるフィールド マッピングを含みます。 インデクサーの定義には、パイプライン操作のためのすべての指示と参照が含まれ、ユーザーがインデクサーを実行すると、パイプラインが呼び出されます。 | [インデクサー](search-indexer-overview.md) |
| [データ ソース]  | Azure でサポートされる型の外部データ ソースに接続するために、インデクサーによって使用されるオブジェクト。 | [インデクサー](search-indexer-overview.md)を参照してください。 |
| Index | フィールド構造と使用状況を定義するインデックス スキーマから構築された Azure Search で永続化された検索コーパスです。 | [Azure Search のインデックス](search-what-is-an-index.md) | 


## <a name="where-do-i-start"></a>どこから始めるか

**手順 1: API を提供するリージョンの検索サービスを作成する** 

+ 米国中南部
+ 西ヨーロッパ

**手順 2: 実際に体験してワークフローを習得する**

+ [クイック スタート (ポータル)](cognitive-search-quickstart-blob.md)
+ [チュートリアル (HTTP 要求)](cognitive-search-tutorial-blob.md)
+ [カスタム スキルの例 (C#)](cognitive-search-create-custom-skill-example.md)

**手順 3: API の確認 (REST のみ)**

現時点では、REST API のみが提供されています。 すべての要求で `api-version=2017-11-11-Preview` を使用します。 次の API を使用して、コグニティブ検索ソリューションを構築します。 コグニティブ検索用に追加または拡張された API は 2 つのみです。 その他の API では、一般的に使用可能なバージョンと同じ構文です。

| REST API | 説明 |
|-----|-------------|
| [データ ソースの作成](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | エンリッチメントされたドキュメントを作成するために使用されるソース データを提供する外部データ ソースを識別するリソースです。  |
| [スキルセットの作成 (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | エンリッチメント パイプラインでインデックス作成時に使用される[定義済みのスキル](cognitive-search-predefined-skills.md)と[カスタム認知スキル](cognitive-search-custom-skill-interface.md)の使用を調整するリソース。 |
| [インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Azure Search インデックスを表すスキーマです。 ソース データ内のフィールドやエンリッチメント フェーズで作成されたフィールドにマッピングされるインデックス内のフィールド (たとえば、エンティティ認識によって作成された組織名のためのフィールド)。 |
| [インデクサーの作成 (api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | データ ソース、スキルセット、ソースからのフィールドの関連付け、ターゲット インデックスまでの中間データ構造、およびインデックス自体などの、インデックス作成時に使用されるコンポーネントを定義するリソースです。 インデクサーを実行すると、データの取り込みやエンリッチメントがトリガーされます。 出力は、インデックス スキーマを基に作成され、ソース データが入力され、スキルセットでエンリッチメントされた検索コーパスです。  |

**一般的なワークフローのチェックリスト**

1. 代表的なサンプルに Azure ソース データをサブセット化します。 インデックスの作成には時間がかかるため、代表的な少量のデータ セットから始め、ソリューションの成熟度に応じて段階的に構築します。

1. データ取得のための接続文字列を指定する[データ ソース オブジェクト](https://docs.microsoft.com/rest/api/searchservice/create-data-source)を Azure Search で作成します。

1. エンリッチメント手順に従って[スキルセット](https://docs.microsoft.com/rest/api/searchservice/create-skillset)を作成します。

1. [インデックス スキーマ](https://docs.microsoft.com/rest/api/searchservice/create-index)を定義します。 *フィールド* コレクションには、ソース データからのフィールドが含まれます。 エンリッチメント中に作成された、コンテンツのために生成された値を保管するための追加フィールドも削除する必要があります。

1. データ ソース、スキルセット、およびインデックスを参照する[インデクサー](https://docs.microsoft.com/rest/api/searchservice/create-skillset)を定義します。

1. インデクサー内に *outputFieldMappings* を追加します。 このセクションでは、(手順 4 の) インデックス スキーマ内の入力フィールドに、(手順 3 の) スキルセットからの出力をマッピングします。

1. Azure Search のインデクサーを表現するため、前の手順で作成した *Create Indexer* 要求を (要求本文にインデクサー定義を含む POST 要求) 送信します。 この手順では、パイプラインを呼び出しインデクサーを実行する方法について説明します。

1. クエリを実行して結果を評価し、スキルセット、スキーマ、またはインデクサー構成を更新するためにコードを編集します。

1. パイプラインを再構築する前に[インデクサーをリセット](search-howto-reindex.md)します。

特定の疑問点または問題に関する詳細については、[トラブルシューティングのヒント](cognitive-search-concept-troubleshooting.md)を参照してください。

## <a name="next-steps"></a>次の手順

+ [コグニティブ検索のドキュメント](cognitive-search-resources-documentation.md)
+ [クイック スタート: ポータルでコグニティブ検索を使ってみる](cognitive-search-quickstart-blob.md)
+ [チュートリアル: コグニティブ検索 API について学習する](cognitive-search-tutorial-blob.md)
