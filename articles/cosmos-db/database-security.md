---
title: データベースのセキュリティ - Azure Cosmos DB | Microsoft Docs
description: Azure Cosmos DB がデータベースの保護とデータのセキュリティを提供する方法について説明します。
keywords: NoSQL データベースのセキュリティ, 情報のセキュリティ, データのセキュリティ, データベースの暗号化, データベースの保護, セキュリティ ポリシー, セキュリティ テスト
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: rafats
ms.openlocfilehash: d74aa6b7ef762b7971fd67654e7fc1a23a8d83cc
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038452"
---
# <a name="azure-cosmos-db-database-security"></a>Azure Cosmos DB データベースのセキュリティ

この記事では、データベースのセキュリティに関するベスト プラクティスと、データベース侵害を防止、検出、および対応するために役立つ Azure Cosmos DB の主な機能について説明します。
 
## <a name="whats-new-in-azure-cosmos-db-security"></a>Azure Cosmos DB のセキュリティの新機能

保存時の暗号化が、すべての Azure リージョンの Azure Cosmos DB に保存されたドキュメントとバックアップに利用できるようになりました。 保存時の暗号化は、これらのリージョンの新規顧客と既存の顧客の両方に自動的に適用されます。 何も構成する必要はありません。従来通り、保存時の暗号化によってデータが安全にセキュリティで保護されるメリットと同様に素晴らしい待機時間、スループット、可用性、および機能を手に入れることができます。

## <a name="how-do-i-secure-my-database"></a>データベースをセキュリティ保護する方法 

データのセキュリティは、顧客自身とデータベース プロバイダーの共同責任です。 顧客として選択したデータベース プロバイダーによって、引き受ける責任の範囲は変化する可能性があります。 オンプレミス ソリューションを選択した場合、エンドポイントの保護からハードウェアの物理的なセキュリティに至るまでのすべてを顧客自身が提供する必要がありますが、これは簡単な仕事ではありません。 Azure Cosmos DB などの PaaS クラウド データベース プロバイダーを選択した場合、顧客が関与する範囲は大幅に縮小されます。 マイクロソフトの「[Shared Responsibilities for Cloud Computing (クラウドコンピューティングの共同責任)](https://aka.ms/sharedresponsibility)」ホワイト ペーパーから借用した次の図は、Azure Cosmos DB のような PaaS プロバイダーを使用すると、顧客の責任がどのように減少するかを示しています。

![顧客とデータベース プロバイダーの責任](./media/database-security/nosql-database-security-responsibilities.png)

上の図は高度なクラウドのセキュリティ コンポーネントを示していますが、具体的には、どのような項目をデータベース ソリューションのために考慮する必要があるでしょうか。 また、ソリューションを相互に比較するにはどうすればよいでしょうか。 

次の要件のチェックリストを使用して、データベース システムを比較することをお勧めします。

- ネットワークのセキュリティとファイアウォールの設定
- ユーザー認証ときめ細かいユーザー制御
- 局地的な障害に対応するためにデータをグローバルにレプリケートする能力
- 1 つのデータ センターから別のデータセンターへのフェールオーバーを実行する能力
- データ センター内でのローカルなデータのレプリケーション
- データの自動バックアップ
- バックアップから削除されたデータの復元
- 機密データの保護と分離
- 攻撃の監視
- 攻撃への対応
- データ ガバナンス制約に対してデータをジオフェンスで準拠させる能力
- 保護されたデータセンター内でのサーバーの物理的な保護
- 認定

さらに、最近発生した[大規模なデータベース侵害](http://thehackernews.com/2017/01/mongodb-database-security.html)は、次の単純ではあるが重要な要件を思い出させてくれます。
- 修正プログラムの適用によって常に最新の状態が維持されるサーバー
- 既定/SSL 暗号化による HTTPS
- 強力なパスワードを持つ管理者アカウント

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Azure Cosmos DB がデータベースをセキュリティで保護する方法

上記のリストを検討してみましょう。Azure Cosmos DB が対応しているセキュリティ要件はいくつあるでしょうか。 すべてです。

各要件を掘り下げてみましょう。

|セキュリティ要件|Azure Cosmos DB のセキュリティ手法|
|---|---|---|
|ネットワークのセキュリティ|IP ファイアウォールの使用は、データベースをセキュリティ保護するための第 1 の保護層です。 Azure Cosmos DB は、受信ファイアウォールでのポリシーに基づく IP ベースのアクセス制御をサポートしています。 IP ベースのアクセス制御は、従来のデータベース システムで使用されているファイアウォール ルールに似ていますが、Azure Cosmos DB データベース アカウントは、承認されたコンピューターまたはクラウド サービスからのみアクセスできるように拡張されています。 <br><br>Azure Cosmos DB では、特定の IP アドレス (168.61.48.0)、IP 範囲 (168.61.48.0/8)、および IP アドレスと範囲の組み合わせを有効にすることができます。 <br><br>この許可リストに含まれていないコンピューターから送信されたすべての要求は、Azure Cosmos DB によってブロックされます。 承認されたコンピューターとクラウド サービスから送信された要求がリソースへのアクセス権を取得するには、認証プロセスを完了する必要があります。<br><br>[Azure Cosmos DB のファイアウォール サポート](firewall-support.md)について説明します。|
|Authorization|Azure Cosmos DB は、承認のためにハッシュ ベースのメッセージ認証コード (HMAC) を使用します。 <br><br>各要求は、シークレット アカウント キーを使用してハッシュされ、その結果生じた base 64 エンコード ハッシュが呼び出しと一緒に Azure Cosmos DB に送信されます。 Azure Cosmos DB サービスは、要求を検証するために、適切なシークレット キーとプロパティを使用してハッシュを生成し、その値と要求に含まれる値を比較します。 2 つの値が一致した場合、承認操作は成功し、要求が処理されます。一致しない場合、承認は失敗し、要求は拒否されます。<br><br>[マスター キー](secure-access-to-data.md#master-keys)または[リソース トークン](secure-access-to-data.md#resource-tokens)のいずれかを使用して、ドキュメントなどのリソースに対するきめ細かいアクセス制御を実行できます。<br><br>詳細については、[Azure Cosmos DB のリソースへのアクセスのセキュリティ保護](secure-access-to-data.md)に関するページを参照してください。|
|ユーザーとアクセス許可|アカウントの[マスター キー](#master-key)を使用して、ユーザー リソースとアクセス許可リソースをデータベースごとに作成することができます。 [リソース トークン](#resource-token)は、データベース内のアクセス許可に関連付けられ、ユーザーがデータベース内のアプリケーション リソースにどのようにアクセスできるか (読み取り/書き込み、読み取り専用、またはアクセスなし) を判断します。 アプリケーション リソースには、コンテナー、ドキュメント、添付ファイル、ストアド プロシージャ、トリガー、UDF が含まれます。 リソース トークンは認証中に使用され、リソースへのアクセスが提供されるか拒否されます。<br><br>詳細については、[Azure Cosmos DB のリソースへのアクセスのセキュリティ保護](secure-access-to-data.md)に関するページを参照してください。|
|Active Directory の統合 (RBAC)| この表の後のスクリーンショットに示すように、データベース アカウントへのアクセスは、Azure Portal でアクセス制御 (IAM) を使用して提供することもできます。 IAM は ロールベースのアクセス制御を提供し、Active Directory と統合されています。 次の図に示すように、個人やグループの組み込みのロールまたはカスタム ロールを使用できます。|
|グローバル レプリケーション|Azure Cosmos DB は設定不要のグローバル分散を実行し、ボタンをクリックするだけで Azure の世界中のデータセンターにデータをレプリケートすることができます。 グローバル レプリケーションでは、グローバルなスケールを行い、全世界のデータに低待機時間でアクセスすることができます。<br><br>セキュリティの点では、グローバル レプリケーションは、局地的な障害からデータが保護されることを保証します。<br><br>詳細については、[データのグローバル分散](distribute-data-globally.md)に関するページを参照してください。|
|リージョン間フェールオーバー|複数のデータセンターにデータをレプリケートしているときに、リージョンのデータセンターがオフラインになった場合、Azure Cosmos DB は、操作を自動的にロールオーバーします。 データをレプリケートするリージョンを使用して、フェールオーバーするリージョンの優先度リストを作成することができます。 <br><br>詳細については、[Azure Cosmos DB のリージョン内フェールオーバー](regional-failover.md)に関するページを参照してください。|
|ローカル レプリケーション|Azure Cosmos DB は、1 つのデータセンター内でも、高可用性を維持するためにデータを自動的にレプリケートします。[一貫性レベル](consistency-levels.md)は顧客自身が選択できます。 これにより、すべての単一リージョンのアカウントとすべてのマルチリージョンのアカウントに対する緩やかな整合性の 99.99% の [SLA 可用性](https://azure.microsoft.com/support/legal/sla/cosmos-db)と、すべてのマルチリージョンのデータベース アカウントに対する 99.999% の読み取り可用性が保証されます。|
|オンライン バックアップの自動化|Azure Cosmos DB データベースは定期的にバックアップされ、ジオリダンダント ストアに格納されます。 <br><br>詳細については、「[Azure Cosmos DB での自動オンライン バックアップと復元](online-backup-and-restore.md)」を参照してください。|
|削除されたデータの復元|自動化されたオンライン バックアップを使用して、誤って削除したデータを最大 30 日以内に回復することができます。 <br><br>詳細については、「[Azure Cosmos DB での自動オンライン バックアップと復元](online-backup-and-restore.md)」を参照してください。|
|機密データの保護と分離|「[新機能](#whats-new)」に示されているリージョンのすべてのデータが保存時に暗号化されます。<br><br>個人データと他の機密データを特定のコンテナーと読み取り/書き込みに分離するか、読み取り専用アクセスを特定のユーザーに限定することができます。|
|攻撃の監視|[監査ログとアクティビティ ログ](logging.md)を使用すると、アカウントの正常なアクティビティと異常なアクティビティを監視できます。 この表の後のスクリーンショットに示すように、リソースに対して実行された操作、操作を開始した人物、操作の発生日時、操作の状態などを確認できます。|
|攻撃への対応|Azure のサポートに連絡して攻撃の可能性を報告すると、5 段階のインシデント対応プロセスが開始されます。 この 5 段階のプロセスの目標は、問題が検出され、調査が開始された後、通常のサービスのセキュリティと操作を可能な限り早急に復元することです。<br><br>詳細については、「[Microsoft Azure Security Response in the Cloud (クラウドでの Microsoft Azure のセキュリティへの対応)](https://aka.ms/securityresponsepaper)」を参照してください。|
|ジオフェンス|Azure Cosmos DB は、主権地域 (ドイツ、中国、米国政府など) に対するデータ ガバナンスを保証します。|
|施設の保護|Azure Cosmos DB のデータは、Azure の保護されたデータセンター内の SSD に格納されます。<br><br>詳細については、[Microsoft グローバルデータセンター](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)に関するページを参照してください。|
|HTTPS/SSL/TLS の暗号化|クライアントからサービスへの Azure Cosmos DB のすべての通信は、SSL/TLS 1.2 に対応しています。 また、データセンター内とデータセンター間のすべてのレプリケーションには、SSL/TLS 1.2 が適用されます。|
|保存時の暗号化|Azure Cosmos DB に格納されるすべてのデータは、保存時に暗号化されます。 詳細については、[Azure Cosmos DB の保存時の暗号化](.\database-encryption-at-rest.md)に関するページを参照してください。|
|サーバーへの修正プログラムの適用|管理されたデータベースとして、Azure Cosmos DB は、サーバーの管理と修正プログラムの適用を行う必要性を排除します。顧客に代わって Azure Cosmos DB が自動的に実行します。|
|強力なパスワードを持つ管理者アカウント|今でもこの要件について言及する必要があることは信じ難いことですが、一部の競合他社とは異なり、Azure Cosmos DB では、パスワードを持たない管理アカウントは存在できません。<br><br> SSL と HMAC のシークレット ベースの認証によるセキュリティは、既定で組み込まれています。|
|セキュリティとデータ保護の認証|最新の認定一覧については、全般的な (Azure コンプライアンス サイト)[https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings] と、最新の (Azure コンプライアンス ドキュメント)[https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942] で、すべての認証を確認してください (Cosmos を検索)。 より的を絞った情報については、2018 年 4 月 25 日の投稿 (Azure #CosmosDB: Secure, private, compliant)[https://azure.microsoft.com/blog/azure-cosmosdb-secure-private-compliant/] を参照してください。この投稿には、SOCS 1/2 Type 2、HITRUST、PCI DSS Level 1、ISO 27001、HIPPAA、FedRAMP High などが記載されています。

次のスクリーン ショットは、Azure Portal のアクセス制御 (IAM) を使用した Active Directory の統合 (RBAC) を示しています。![Azure Portal のアクセス制御 (IAM) - データベース セキュリティを示す](./media/database-security/nosql-database-security-identity-access-management-iam-rbac.png)

次のスクリーン ショットは、監査ログとアクティビティ ログを使用して自分のアカウントを監視する方法を示しています。![Azure Cosmos DB のアクティビティ ログ](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>次の手順

マスター キーとリソース トークンの詳細については、「[Azure Cosmos DB のデータへのアクセスのセキュリティ保護](secure-access-to-data.md)」を参照してください。

監査ログの詳細については、「[Azure Cosmos DB diagnostic logging (Azure Cosmos DB の診断ログ)](logging.md)」を参照してください。

マイクロソフトが取得している認証の詳細については、[Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/)に関するページを参照してください。
