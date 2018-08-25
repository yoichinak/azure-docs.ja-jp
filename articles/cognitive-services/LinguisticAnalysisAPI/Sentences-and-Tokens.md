---
title: Linguistic Analysis API の文とトークン | Microsoft Docs
description: Cognitive Services の Linguistic Analysis API での文の分割とトークン化について説明します。
services: cognitive-services
author: DavidLiCIG
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/21/2016
ms.author: davl
ms.openlocfilehash: 78e539f365728ad540308e9cfb07af44bf6d8fe7
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084044"
---
# <a name="sentence-separation-and-tokenization"></a>文の分割とトークン化

## <a name="background-and-motivation"></a>背景と動機

テキスト本文が提供されたら、言語分析の第一段階としてテキスト本文を文とトークンに分割します。

### <a name="sentence-separation"></a>文の分割

一見したところ、テキストを文に分割することは単純な処理のように思えます。文末のマーカーを探して、そこで分割するだけです。
ただし、多くの場合、そのような記号は複雑であいまいです。

次のテキストについて考えてみてください。

> What did you say?!? I didn't hear about the director's "new proposal." It's important to Mr. and Mrs. Smith.

このテキストには、次の 3 つの文が含まれています。

- What did you say?!?
- I didn't hear about the director's "new proposal."
- It's important to Mr. and Mrs. Smith.

文末がまったく異なる方法で示されていることに注意してください。
最初の文の末尾は、疑問符と感嘆符の組み合わせです (感嘆修辞疑問符とも呼ばれます)。
2 番目の文の末尾はピリオドであり、その後にある引用符は前の文に含まれます。
3 番目の文では、同じピリオドが、省略を示すためにも使用できることがわかります。
句読点のみを確認すると有効な候補セットを得ることができますが、文の本当の境界を識別するにはそれ以上の処理が必要です。

### <a name="tokenization"></a>トークン化

次のタスクは、これらの文をトークンに分割することです。
ほとんどの場合、英語のトークンは空白で区切られています。
(トークンすなわち単語を見つけることは、英語では中国語よりもはるかに簡単です。中国語では単語の間に空白が使用されることはほとんどありません。
最初の文であれば "Whatdidyousay?" のように記述されます。)

困難なケースもいくつかあります。
まず、多くの場合、句読点を周囲のコンテキストから切り離す必要があります (常にではありません)。
次に、英語には "didn't" や "it's" のような "*短縮形*" があり、複数の単語が短く省略されています。 トークナイザーの目的は、文字のシーケンスを単語に分割することです。

上記の文の例に戻りましょう。
ここで、個別のトークンの間に "中点" (&middot;) を配置しました。

- What &middot; did &middot; you &middot; say &middot; ?!?
- I &middot; did &middot; n't &middot; hear &middot; about &middot; the &middot; director &middot; 's &middot; " &middot; new &middot; proposal &middot; . &middot; "
- It &middot; 's &middot; important &middot; to &middot; Mr. &middot; and &middot; Mrs.&middot; Smith &middot; .

ほとんどのトークンは辞書で見つけることができる単語です (例: *important*、*director*)。
それ以外は句読点です。
さらに、*not*に対応する *n't* や、所有格を表す *'s*など短縮形を表す、一般的ではないトークンがあります。このようなトークン化によって、たとえば単語 *didn't* と句 *did not* を一貫性の高い方法で処理できるようになります。

## <a name="specification"></a>仕様

文およびトークンを何が構成するかについて、一貫性がある決定を行うことが重要です。
ここでは [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42) の仕様に依存しています (詳細については、ftp://ftp.cis.upenn.edu/pub/treebank/public_html/tokenization.html で入手できます)。
