---
title: Azure Data Lake Store と Azure Storage BLOB の比較 | Microsoft Docs
description: Azure Data Lake Store と Azure BLOB ストレージの比較
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 0b374e92a1e1d9828bc8c095e29e1dfdfd13275b
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492913"
---
# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>Azure Data Lake Store と Azure BLOB ストレージの比較
この記事の表には、ビッグ データ処理の重要な側面に沿った Azure Data Lake Store と Azure BLOB ストレージの違いがまとめられています。 Azure BLOB ストレージは、さまざまなストレージ シナリオ向けに設計されたスケーラブルな汎用オブジェクト ストアです。 Azure Data Lake Store は、ビッグ データ分析ワークロードに最適化されたハイパースケール リポジトリです。

|  | Azure Data Lake Store | Azure Blob Storage |
| --- | --- | --- |
| 目的 |ビッグ データ分析ワークロードに最適化されたストレージ |さまざまなストレージ シナリオ (ビッグ データ分析を含む) のための汎用オブジェクト ストア |
| ユース ケース |バッチ、対話型、ストリーミング分析、および機械学習データ (ログ ファイル、IoT データ、クリック ストリーム、大規模なデータセットなど) |あらゆる種類のテキストまたはバイナリ データ (アプリケーション バックエンド、バックアップ データ、ストリーミング用のメディア ストレージ、汎用データなど)。 さらに、分析ワークロードに対するフル サポート。バッチ、対話型、ストリーミング分析、および機械学習データ (ログ ファイル、IoT データ、クリック ストリーム、大規模なデータセットなど) |
| 主要な概念 |Data Lake Store アカウントにはフォルダーが含まれ、フォルダーにはファイルとして保存されたデータが含まれます。 |ストレージ アカウントにはコンテナーが含まれ、コンテナーには BLOB の形でデータが含まれます。 |
| Structure |階層型ファイル システム |フラットな名前空間を使用するオブジェクト ストア |
| API |HTTPS 経由の REST API |HTTP/HTTPS 経由の REST API |
| サーバー側 API |[WebHDFS 互換の REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Azure BLOB ストレージ REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Hadoop ファイル システム クライアント |[はい] |[はい] |
| データ操作 - 認証 |[Azure Active Directory ID](../active-directory/develop/authentication-scenarios.md) |共有シークレット ([アカウント アクセス キー](../storage/common/storage-create-storage-account.md#manage-your-storage-account)と [Shared Access Signature](../storage/common/storage-dotnet-shared-access-signature-part-1.md) キー) に基づきます。 |
| データ操作 - 認証プロトコル |OAuth 2.0。 呼び出しには、Azure Active Directory によって発行された有効な JWT (JSON Web トークン) が含まれている必要があります。 |ハッシュベース メッセージ認証コード (HMAC)。 呼び出しには、HTTP 要求の一部に対する Base64 でエンコードされた SHA-256 ハッシュが含まれている必要があります。 |
| データ操作 - 承認 |POSIX アクセス制御リスト (ACL)。  Azure Active Directory ID に基づく ACL は、ファイルおよびフォルダー レベルで設定できます。 |アカウントレベルの承認には、[アカウント アクセス キー](../storage/common/storage-create-storage-account.md#manage-your-storage-account)を使用します<br>アカウント、コンテナー、または BLOB の承認には、[Shared Access Signature キー](../storage/common/storage-dotnet-shared-access-signature-part-1.md)を使用します |
| データ操作 - 監査 |使用可能。 詳細については、 [こちら](data-lake-store-diagnostic-logs.md) をご覧ください。 |使用可能 |
| 保存データの暗号化 |<ul><li>透過的、サーバー側</li> <ul><li>サービスによって管理されるキーを使用</li><li>ユーザーによって Azure KeyVault で管理されるキーを使用</li></ul></ul> |<ul><li>透過的、サーバー側</li> <ul><li>サービスによって管理されるキーを使用</li><li>ユーザーによって Azure KeyVault で管理されるキーを使用 (プレビュー)</li></ul><li>クライアント側暗号化</li></ul> |
| 管理操作 (アカウントの作成など) |[ロールベースのアクセス制御](../role-based-access-control/overview.md) (RBAC) |[ロールベースのアクセス制御](../role-based-access-control/overview.md) (RBAC) |
| Developer SDK |.NET、Java、Python、Node.js |.Net、Java、Python、Node.js、C++、Ruby、PHP、Go、Android、iOS |
| 分析ワークロードのパフォーマンス |並列分析ワークロードに最適化されたパフォーマンス。 高スループットおよび高 IOPS。 |並列分析ワークロードに最適化されたパフォーマンス。 |
| サイズ制限 |アカウント サイズ、ファイル サイズ、ファイル数に制限はありません。 |具体的な制限については、[こちら](../storage/common/storage-scalability-targets.md)。 より大きなアカウント制限については [Azure サポート](https://azure.microsoft.com/support/faq/)への問い合わせで入手可能 |
| geo 冗長 |ローカル冗長 (1 つの Azure リージョンにデータの複数のコピー) |ローカル冗長 (LRS)、ゾーン冗長 (ZRS)、geo 冗長 (GRS)、読み取りアクセス geo 冗長 (RA-GRS)。 詳細については、 [こちら](../storage/common/storage-redundancy.md) をご覧ください。 |
| サービスの状態 |一般公開 |一般公開 |
| リージョン別の提供状況 |詳細については、 [こちら](https://azure.microsoft.com/regions/#services) |すべての Azure リージョンで使用可能 |
| 料金 |詳細については、 [価格](https://azure.microsoft.com/pricing/details/data-lake-store/) |詳細については、 [価格](https://azure.microsoft.com/pricing/details/storage/) |


