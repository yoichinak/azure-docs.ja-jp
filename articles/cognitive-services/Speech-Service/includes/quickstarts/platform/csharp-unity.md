---
title: クイック スタート:Speech SDK for C# Unity プラットフォームの設定 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech サービス SDK を使用して C# Unity のプラットフォームを設定するには、このガイドを使用します。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 895943b00ef2c2541b7de6cc81648877c9a73c0d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749984"
---
このガイドでは、[Unity](https://unity3d.com/) 用 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します。

> [!NOTE]
> Unity 用 Speech SDK では、Windows Desktop (x86 および x64) またはユニバーサル Windows プラットフォーム (x86、x64、ARM、ARM64)、Android (x86、ARM32 または 64)、iOS (x64 シミュレーターおよび ARM64) がサポートされています

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

- Windows では、お使いのプラットフォームに対応した [Microsoft Visual Studio 2019 の Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0)が必要です。 これを初めてインストールする場合、再起動が必要になる場合があります。
- [Unity 2018.3 以降](https://store.unity.com/)および [UWP ARM64 のサポートを追加する Unity 2019.1](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)。
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。 Visual Studio 2017 バージョン 15.9 以降も許容されます。
- Windows ARM64 をサポートするため、[ARM64 用のオプションのビルド ツール、および ARM64 用の Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) をインストールします。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

Unity 用 Speech SDK をインストールするには、次の手順を実行します。

1. Unity アセット パッケージ (.unitypackage) としてパッケージ化され、既に Unity に関連付けられている [Unity 用 Speech SDK](https://aka.ms/csspeech/unitypackage) をダウンロードして開きます。 アセット パッケージを開くと、**[Import Unity Package]\(Unity パッケージのインポート\)** ダイアログ ボックスが表示されます。 この手順を実行するには、必要に応じて空のプロジェクトを作成して開く必要があります。

   [![Unity エディターの [Import Unity Package]\(Unity パッケージのインポート\) ダイアログ ボックス](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. すべてのファイルが選択されていることを確認したら、**[Import]\(インポート\)** を選択します。 しばらくすると、Unity アセット パッケージがプロジェクトにインポートされます。

Unity にアセット パッケージをインポートする方法の詳細については、[Unity のドキュメント](https://docs.unity3d.com/Manual/AssetPackages.html)を参照してください。

これで、下記の「[次の手順](#next-steps)」に進むことができます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [windows](../quickstart-list.md)]
