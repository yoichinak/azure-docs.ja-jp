---
title: '保存時のデータベースの暗号化: Azure Cosmos DB | Microsoft Docs'
description: Azure Cosmos DB ですべてのデータが既定で暗号化されるしくみについて説明します。
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: rafats
ms.openlocfilehash: 2b54f8c7d9f6427f3104d3c64c65cc555f68738a
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038420"
---
# <a name="azure-cosmos-db-database-encryption-at-rest"></a>保存時の Azure Cosmos DB データベースの暗号化

保存時の暗号化という語句は、一般的にはソリッド ステート ドライブ (SSD) やハード ディスク (HDD) などの不揮発性メモリ デバイスでデータを暗号化することを意味します。 Cosmos DB では、プライマリ データベースを SSD に保存します。 そのメディア添付ファイルとバックアップは Azure Blob ストレージに保存され、このストレージは通常 HDD でバックアップされます。 Cosmos DB の保存時の暗号化のリリースにより、すべてのデータベース、メディア添付ファイル、バックアップが暗号化されます。 データの転送中 (ネットワーク経由) と保存時 (非揮発性メモリ) にデータが暗号化されることで、エンド ツー エンドの暗号化が実現しました。

PaaS サービスである Cosmos DB は、非常に簡単に使用できます。 Cosmos DB に保存されているすべてのユーザー データは、保存時と転送時に暗号化されているため、操作は何も必要ありません。 つまり、保存時の暗号化機能は、既定で "オン" になっています。 オンまたはオフにするコントロールはありません。 [可用性とパフォーマンスの SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) を引き続き満たしながら、この機能を提供します。

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Azure Cosmos DB に保存時の暗号化の実装

保存時の暗号化は、セキュリティで保護されたキー ストレージ システム、暗号化ネットワーク、暗号化 API など、多くのセキュリティ テクノロジを使用して実装されています。 データの暗号化を解除して処理するシステムは、キーを管理するシステムと通信する必要があります。 図には、暗号化されたデータの格納とキーの管理がどのように分離されているかを示しています。 

![設計の図](./media/database-encryption-at-rest/design-diagram.png)

ユーザー要求の基本的なフローは、次のとおりです。
- ユーザー データベース アカウントが準備され、ストレージ キーが管理サービス リソース プロバイダーへの要求を通じて取得されます。
- ユーザーは、HTTPS/セキュリティで保護された転送を通じて、Cosmos DB への接続を作成します。 (SDK が詳細を抽象化します。)
- ユーザーは、先に作成したセキュリティで保護された接続経由で、格納対象である JSON ドキュメントを送信します。
- ユーザーがインデックスを無効にしていない限り、JSON ドキュメントのインデックスが作成されます。
- JSON ドキュメントとインデックス データの両方が、セキュリティで保護されたストレージに書き込まれます。
- データはセキュリティで保護されたストレージから定期的に読み取られ、Azure の暗号化された Blob ストアにバックアップされます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>Q: Storage Service Encryption を有効にすると、Azure Storage の料金はどれくらい増えますか。
A: 追加コストはかかりません。

### <a name="q-who-manages-the-encryption-keys"></a>Q: だれが暗号化キーを管理するのですか。
A: キーは Microsoft によって管理されます。

### <a name="q-how-often-are-encryption-keys-rotated"></a>Q: 暗号化キーが交換される頻度はどの程度ですか。
A: マイクロソフトには、暗号化キーのローテーションに関する一連の内部ガイドラインがあり、Cosmos DB はそれに従っています。 個々のガイドラインは公開されていません。 Microsoft が公開している[セキュリティ開発ライフ サイクル (SDL)](https://www.microsoft.com/sdl/default.aspx) は、内部用ガイダンスのサブセットと見なされ、開発者に便利なベスト プラクティスが収められています。

### <a name="q-can-i-use-my-own-encryption-keys"></a>Q: 独自の暗号化キーを使用できますか。
A: Cosmos DB は PaaS サービスであり、Microsoft はこのサービスの使いやすさを維持するために努力してきました。 この質問は、PCI-DSS などのコンプライアンス要件のクリアに関する代替質問として頻繁に寄せられることがわかりました。 この機能を構築する際に、コンプライアンス監査者の協力を得て、Cosmos DB を使用する顧客が自身でキーを管理しなくても要件を満たすことができるようにしました。

### <a name="q-what-regions-have-encryption-turned-on"></a>Q: どのリージョンで暗号化が有効になっていますか。
A: すべての Azure Cosmos DB リージョンで、すべてのユーザー データに対して暗号化が有効になっています。

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>Q: 暗号化はパフォーマンス (待機時間およびスループット) の SLA に影響しますか。
A: パフォーマンスの SLA への影響や変化はないため、既存および新規のすべてのアカウントで保存時の暗号化が有効になります。 最新の保証の詳細については、「[Cosmos DB の SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)」 ページを参照してください。

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>Q: ローカル エミュレーターは、保存時の暗号化に対応していますか。
A: エミュレーターは、スタンドアロンの開発/テスト ツールであり、管理された Cosmos DB サービスで使用するキー管理サービスを使用していません。 機密性の高いエミュレーター テスト データを格納するドライブで BitLocker を有効にすることをお勧めします。 [エミュレーターは既定のデータ ディレクトリの変更](local-emulator.md)と、よく知られた場所の使用をサポートしています。

## <a name="next-steps"></a>次の手順

Cosmos DB のセキュリティと最新の機能強化の概要については、「[Azure Cosmos DB データベースのセキュリティ](database-security.md)」を参照してください。
マイクロソフトが取得している認証の詳細については、[Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/)に関するページを参照してください。
