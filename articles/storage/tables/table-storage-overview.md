---
title: Table Storage の概要 - Azure のオブジェクト ストレージ | Microsoft Docs
description: NoSQL データ ストアである Azure Table Storage を使用して構造化データをクラウドに格納します。
services: storage
author: SnehaGunda
ms.service: storage
ms.devlang: dotnet
ms.topic: overview
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: 316e10d56e5c235c4d64dde0fde4dcb9429629c8
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527789"
---
# <a name="introduction-to-table-storage-in-azure"></a>Azure の Table Storage の概要

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage は、NoSQL の構造化データをクラウド内に格納するサービスです。スキーマレスのデザインでキー/属性ストアを実現します。 Table Storage はスキーマがないため、アプリケーションの進化のニーズに合わせてデータを容易に修正できます。 Table Storage のデータ アクセスは、多くの種類のアプリケーションにとって高速でコスト効率に優れ、また一般に、従来の SQL と比較して、同様の容量のデータを低コストで保存することができます。

Table Storage を使用すると、Web アプリケーションのユーザー データ、アドレス帳、デバイス情報、サービスに必要なその他の種類のメタデータなど、柔軟なデータセットを格納できます。 ストレージ アカウントの容量の上限を超えない限り、テーブルには任意の数のエンティティを保存でき、ストレージ アカウントには任意の数のテーブルを含めることができます。

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>次の手順

* [Microsoft Azure ストレージ エクスプローラー](../../vs-azure-tools-storage-manage-with-storage-explorer.md)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。

* [.Net での Azure Table Storage の概要](../../cosmos-db/table-storage-how-to-use-dotnet.md)

* 利用可能な API の詳細については、Table service のリファレンス ドキュメントを参照してください。

    * [.NET 用ストレージ クライアント ライブラリ リファレンス](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [REST API リファレンス](http://msdn.microsoft.com/library/azure/dd179355)
