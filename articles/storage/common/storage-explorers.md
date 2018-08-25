---
title: Azure Storage 向けのツール | Microsoft Docs
description: Azure Storage のデータの表示/操作を可能にするツールの一覧。
services: storage
author: dineshmurthy
ms.service: storage
ms.topic: article
ms.date: 09/06/2017
ms.author: dineshmurthy
ms.component: common
ms.openlocfilehash: 55fa63e75768662a0f909cef0e3cc04b9c2dae41
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526200"
---
# <a name="azure-storage-client-tools"></a>Azure Storage クライアント ツール
Azure Storage のユーザーのご要望に多いのが、Azure Storage クライアント ツールを使用したデータの表示や処理についてです。 次の表に、これを実現するツールの一覧を示します。 データ抽象化を列挙できる場合、またはデータ抽象化にアクセスできる場合は、各ブロックに "X" を入力しました。 ツールの有料/無料も記載されています。 "試用版" とは、無料試用版があることを示します。ただし、完全な製品は有料です。 "Y/N" は、バージョンの有料/無料を示します。

利用できる Azure Storage クライアント ツールのスナップショットのみを提供しました。 これらのツールは開発が続いており、機能が追加されることがあります。 修正や更新がございましたら、コメントを投稿してお知らせください。 ここに記載するべきツールがある場合もお知らせください。

**Microsoft Azure Storage クライアント ツール**

<table>
  <tr>
    <th rowspan="2">Azure Storage クライアント ツール</th>
    <th rowspan="2">ブロック BLOB</th>
    <th rowspan="2">ページ BLOB</th>
    <th rowspan="2">Append Blob</th>
    <th rowspan="2">テーブル</th>
    <th rowspan="2">キュー</th>
    <th rowspan="2">ファイル</th>
    <th rowspan="2">無料</th>
    <th colspan="4">プラットフォーム</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Microsoft Azure Portal</a></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>Y</td>
    <td>○</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://storageexplorer.com/">Microsoft Azure ストレージ エクスプローラー</a></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>Y</td>
    <td></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Microsoft Visual Studio サーバー エクスプローラー</a></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
</table>

**サードパーティ製 Azure Storage クライアント ツール**

Microsoft では次のサードパーティ製ツールが主張する機能と品質を検証していません。このリストは Microsoft の推薦を暗に示すものではありません。

<table>
  <tr>
    <th rowspan="2">Azure Storage クライアント ツール</th>
    <th rowspan="2">ブロック BLOB</th>
    <th rowspan="2">ページ BLOB</th>
    <th rowspan="2">Append Blob</th>
    <th rowspan="2">テーブル</th>
    <th rowspan="2">キュー</th>
    <th rowspan="2">ファイル</th>
    <th rowspan="2">無料</th>
    <th colspan="4">プラットフォーム</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="http://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>試用版</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgate: Azure Explorer</a></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td></td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Azure Web Storage Explorer</a></td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.cloudberrylab.com/explorer/microsoft-azure.aspx">CloudBerry Explorer</a></td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td></td>
    <td></td>
    <td>○</td>
    <td>Y/N</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gapotchenko.com/cloudcombine">Cloud Combine</a></td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td>○</td>
    <td>○</td>
    <td></td>
    <td>試用版</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://clumsyleaf.com">ClumsyLeaf: AzureXplorer、CloudXplorer、TableXplorer</a></td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>○</td>
    <td>Y</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gladinet.com/Azure-Storage/index.htm">Gladinet Cloud</a></td>
    <td>○</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>試用版</td>
    <td></td>
    <td>○</td>
    <td></td>
    <td></td>
  </tr>
</table>
