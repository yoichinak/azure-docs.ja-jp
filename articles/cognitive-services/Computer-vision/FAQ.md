---
title: Computer Vision API に関する FAQ | Microsoft Docs
description: Microsoft Cognitive Services の Computer Vision API についてよく寄せられる質問とその回答を紹介します。
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/26/2017
ms.author: kefre
ms.openlocfilehash: 5c862dd2fb26a005f4e785673a4e9358ecf9286f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372952"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Computer Vision API についてよく寄せられる質問
### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>この FAQ で質問に対する回答が見つからない場合は、[StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) の Computer Vision API コミュニティに質問するか、[UserVoice のヘルプとサポート](https://cognitive.uservoice.com/)にご連絡ください

-----

**質問**: *カスタム タグを使用するように Computer Vision API をトレーニングできますか?たとえば、ネコの品種の写真をフィードして AI を "トレーニング" し、AI 要求に対して品種値を受け取りたいと思います。*

**回答**: この機能は、現在使用できません。 ただし、マイクロソフトのエンジニアは、Computer Vision へのこの機能の追加に取り組んでいます。

-----

**質問**: *Computer Vision をインターネット接続なしでローカルに使用することはできますか?*

**回答**: 現在、オンプレミスまたはローカルのソリューションは提供していません。

-----

**質問**: *Computer Vision ではどの言語がサポートされますか?*

**回答**: 以下の言語がサポートされています。

| | | サポートされている言語 | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| デンマーク語 (da-DK)  | オランダ語 (nl-NL)     | 英語           | フィンランド語 (fi-FI)            |フランス語 (fr-FR)
| ドイツ語 (de-DE)  | ギリシャ語 (el-GR)     | ハンガリー語 (hu-HU) | イタリア語 (it-IT)            | 日本語 (ja-JP)
| 韓国語 (ko-KR)  | ノルウェー語 (nb-NO) | ポーランド語 (pl-PL)    | ポルトガル語 (pt-BR) (pt-PT) | ロシア語 (ru-RU)
| スペイン語 (es-ES)   | スウェーデン語 (sv-SV)     | トルコ語 (tr-TU)   |                            |

-----

**質問**: *Computer Vision をナンバー プレートの読み取りに使用できますか?*

**回答**: Vision API は、OCR による優れたテキスト検出を提供しますが、ナンバー プレート用には現在最適化されていません。 マイクロソフトではサービスの向上に努めており、自動ナンバー プレート認識用の OCR を機能要求の一覧に追加しました。

-----

**質問:** *手書き認識ではどの言語がサポートされますか?*

**回答**: 現在、英語のみがサポートされています。

-----

**質問**: *手書き認識ではどのような種類の筆記面がサポートされますか?*

**回答**: ホワイトボード、白紙、黄色の付箋など、さまざまな種類の筆記面で動作します。

-----

**質問**: *手書き認識操作にかかる時間はどれくらいですか?*

**回答**: 所要時間は、テキストの長さによって異なります。 長いテキストの場合は数秒かかることがあります。 したがって、手書きテキストの認識操作の完了後、手書きテキスト操作の結果の取得操作を使用して結果を取得する前に待機することが必要な場合があります。

-----

**質問**: *手書き認識テクノロジは、行の途中にキャレットを使用して挿入されたテキストをどのように処理しますか?*

**回答**: このようなテキストは、手書き認識操作によって別々の行として返されます。

-----

**質問**: *手書き認識テクノロジでは、線で消された単語または行はどのように処理されますか?*

**回答**: 単語が認識できないことを示すために複数の線で消されている場合、手書き認識操作はそれらを認識対象としません。 一方、単語が 1 本の線を使用して消されている場合、線はノイズと見なされ、単語は手書き認識操作によって認識対象となります。

-----

**質問**: *手書き認識テクノロジではどのようなテキスト方向がサポートされますか?*

**回答**: 最大 30° から 40° の角度が付いたテキストは手書き認識操作によって認識対象となります。

-----
