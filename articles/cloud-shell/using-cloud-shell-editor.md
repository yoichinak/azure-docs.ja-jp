---
title: Azure Cloud Shell エディターの使用 | Microsoft Docs
description: Azure Cloud Shell エディターの使用方法の概要。
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: juluk
ms.openlocfilehash: caf6e18a9a30654710f5445ed6ab957a5253d62e
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259583"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Azure Cloud Shell エディターの使用

Azure Cloud Shell には、オープンソース [Monaco Editor](https://github.com/Microsoft/monaco-editor) から構築された統合ファイル エディターが含まれています。 Cloud Shell エディターでは、言語の強調表示、コマンド パレット、ファイル エクスプローラーなどの機能をサポートしています。

![Cloud Shell エディター](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>ファイルを開く

単純なファイルの作成および編集の場合は、Cloud Shell ターミナルで `code .` を実行することで、エディターを起動します。 この操作により、ターミナルでエディターが開きアクティブな作業ディレクトリが設定されます。

編集を迅速に行うためにファイルを直接開くには、ファイル エクスプローラーを通さず、`code <filename>` を実行してエディターを開きます。

UI ボタンを使用してエディターを開くには、ツールバーで `{}` エディター アイコンをクリックします。 これにより、エディターが開き、既定ではファイル エクスプローラーが開き `/home/<user>` ディレクトリが表示されます。

## <a name="closing-the-editor"></a>エディターを閉じる

エディターを閉じるには、エディターの右上にある [`...`] アクション パネルを開き、[`Close editor`] を選択します。

![エディターを閉じる](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>コマンド パレット

コマンド パレットを起動するには、エディターにフォーカスが設定されたときに `F1` キーを押します。 アクション パネルからコマンド パレットを開くこともできます。

![コマンド パレット](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Monaco Editor に投稿する

Cloud Shell エディターでの言語強調表示のサポートは、[Monaco Editor](https://github.com/Microsoft/monaco-editor) の Monarch 構文定義の使用ではアップストリーム機能を介してサポートされています。 投稿を行う方法については、[Monaco 共同作成者ガイド](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md)を参照してください。

## <a name="next-steps"></a>次の手順
[Cloud Shell で Bash のクイック スタートを試してみる](quickstart.md)
[統合された Cloud Shell ツールの完全な一覧を表示](features.md)