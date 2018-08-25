---
title: Azure SQL Database サービスとは | Microsoft Docs
description: 'SQL Database の概要: クラウド内の Microsoft のリレーショナル データベース管理システム (RDBMS) の技術の詳細と機能について説明します。'
keywords: sql の概要,sql の紹介,sql database とは
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.topic: overview
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: f7a314b3de112ad1fa7a5a356c2325846e9371b1
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413384"
---
# <a name="what-is-the-azure-sql-database-service"></a>Azure SQL Database サービスとは 

SQL Database は、リレーショナル データ、JSON、空間、XML などの構造をサポートする、Microsoft Azure における汎用リレーショナル データベース管理サービスです。 SQL Database は、2 つの異なる購入モデル ([仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)と [DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)) の中で動的かつスケーラブルなパフォーマンスを実現します。 SQL Database は、徹底的な解析的分析とレポートを行うための[列ストア インデックス](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview)や、極度のトランザクション処理を行うための[インメモリ OLTP](sql-database-in-memory.md) などのオプションを備えています。 SQL コード ベースに対するパッチの適用と更新を Microsoft がすべてシームレスで処理するため、基になるインフラストラクチャの管理はすべて不要になります。 

Azure SQL Database には、そのデータベースのデプロイに関して次の選択肢があります。
- 単一データベースとしてデプロイし、それ専用の各種リソースを論理サーバーを介して管理する。 
- プールされているデータベースとして[エラスティック プール](sql-database-elastic-pool.md)にデプロイし、各種の共有リソースを論理サーバーを介して管理する。
- システム データベースとユーザー データベースを含んだデータベースのコレクションに含める形でデプロイする。このコレクションは、[マネージド インスタンス](sql-database-managed-instance.md) (パブリック プレビュー) と呼ばれ、各種リソースを共有します。

これらのデプロイの選択肢を次の図に示します。

![デプロイの選択肢](./media/sql-database-technical-overview/deployment-options.png) 

SQL Database は、そのコード ベースを [Microsoft SQL Server データベース エンジン](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation)と共有しています。 Microsoft のクラウド優先戦略に基づく SQL Server の最新機能のリリースは SQL Database から始まり、その後 SQL Server 自体に対してリリースされます。 この方法によって、修正プログラムの適用やアップグレードなしで SQL Server の最新の機能をユーザーに提供することができ、数百万のデータベースでこれらの新機能をテストすることができます。 発表される新しい機能の詳細については、以下を参照してください。

- **[SQL Database に関するAzure ロードマップ](https://azure.microsoft.com/roadmap/?category=databases)**: 最新情報や今後の予定を知ることができます。 
- **[Azure SQL Database のブログ](https://azure.microsoft.com/blog/topics/database)**: SQL Database に関するニュースと機能についての SQL Server 製品チームのメンバーのブログです。 

> [!IMPORTANT]
> SQL Database と SQL Server の機能の違いについては、[SQL の機能](sql-database-features.md)に関するページを参照してください。 

SQL Database は、パフォーマンスを予測できる複数のサービス レベルで提供され、ダウンタイムがない動的なスケーラビリティ、組み込みのインテリジェントな最適化、グローバルなスケーラビリティと可用性、および高度なセキュリティ オプションを備えています。それらはすべてほぼ管理する必要がありません。 これらの機能を使用すると、貴重な時間とリソースを仮想マシンとインフラストラクチャの管理に奪われることなく、迅速なアプリケーション開発や、製品化に要する時間の短縮化に専念することができます。 SQL Database サービスは、現在、世界中の 38 か所のデータ センターで提供されていますが、新しいデータ センターが定期的に開設されているため、ご利用のデータベースを最寄りのデータ センターで実行することができます。

> [!NOTE]
> SQL Database Managed Instance は現在プレビュー段階であり、単一サービス レベルでの利用に限られます。 詳細については、[SQL Database Managed Instance](sql-database-managed-instance.md) に関するページを参照してください。
>

## <a name="scalable-performance-and-pools"></a>スケーラブルなパフォーマンスとプール

SQL Database では、各データベースは互いに分離しており、移植可能です。各データベースは、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)または[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)内に独自のサービス レベルを備え、パフォーマンス レベルを保証しています。 SQL Database には、さまざまなニーズに対応するための複数のパフォーマンス レベルがあり、リソースを最大限に利用してコストの節約するためにデータベースをプールすることができます。

SQL Database Managed Instance では、各インスタンスが他のインスタンスから分離され、リソースが保証されます。 詳細については、[SQL Database Managed Instance](sql-database-managed-instance.md) に関するページを参照してください 

### <a name="adjust-performance-and-scale-without-downtime"></a>ダウンタイムなしでのパフォーマンスの調整とスケール

SQL Database には、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)と[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md) があります。 
- DTU ベースの購入モデルには、データベースの軽量ワークロードから重量ワークロードまでをサポートする、計算リソース、メモリ リソース、および IO リソースの組み合わせがそれぞれ異なる、Basic、Standard、Premium の 3 つのサービス レベルがあります。 各レベルにおけるパフォーマンス レベルでは、これらのリソースのさまざまな組み合わせが提供され、ストレージ リソースを追加することができます。
- 仮想コアベースの購入モデルでは、仮想コアの数、メモリの量、およびストレージの量と速度を選択できます。

最初にアプリを月数ドルの小さな 1 つのデータベースでビルドし、後でいつでもソリューションのニーズに合わせて手動またはプログラムでサービス レベルを変更することができます。 アプリにも顧客にもダウンタイムを発生させずにパフォーマンスを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができ、必要なときに必要な分のリソースにのみ課金されます。

動的スケーラビリティは自動スケールとは異なります。 自動スケールは、基準に基づいてサービスが自動的にスケールされるのに対し、動的スケーラビリティでは、ダウンタイムなしで手動スケールすることができます。 単一の Azure SQL Database は、手動の動的スケーラビリティをサポートしますが、自動スケールはサポートしていません。 *自動*操作を増やすには、エラスティック プールの使用を検討してください。エラスティック プールを使用すると、データベースが個々のデータベースのニーズに基づいてプール内のリソースを共有できます。 ただし、単一の Azure SQL Database のスケーラビリティを自動化できるスクリプトがあります。 例については、「[PowerShell を使用して単一の SQL データベースを監視およびスケーリングする](scripts/sql-database-monitor-and-scale-database-powershell.md)」を参照してください。 

SQL Database Managed Instance はプレビュー段階であり、単一サービス レベルでの提供となります。 詳細については、[SQL Database Managed Instance](sql-database-managed-instance.md) に関するページを参照してください

### <a name="elastic-pools-to-maximize-resource-utilization"></a>リソース使用率を最大化するためのエラスティック プール

多くのビジネスとアプリケーションにとって、特に使用パターンが比較的予測可能である場合、単一データベースを作成し、要求に応じてパフォーマンスを調整することができれば、それで十分です。 しかし、使用パターンが予測できない場合、コストおよびビジネス モデルを管理するのが難しくなる可能性があります。 [エラスティック プール](sql-database-elastic-pool.md)は、この問題を解決するように設計されています。 概念は単純です。 パフォーマンス リソースを個々のデータベースではなくプールに割り当て、課金は単一のデータベースのパフォーマンスではなくプールの全体的なパフォーマンス リソースに対して行われます。 

   ![エラスティック プール](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

エラスティック プールを使用すると、リソースの需要が変動しても、データベース パフォーマンスの調整に気を配る必要がなくなります。 プールされたデータベースは、必要に応じて、エラスティック プールのパフォーマンス リソースを使用します。 しかし、プールされたデータベースの使用は、プールの上限を超えることはありません。したがって、個々のデータベースの使用状況が予測できなくても、コストが予測可能なことに変わりはありません。 さらに、[プールに対してデータベースの追加および削除を行う](sql-database-elastic-pool-manage-portal.md)ことで、完全に設定予算内で、アプリケーションを数個のデータベースから何千ものデータベースに及ぶ範囲でスケーリングすることができます。 また、プール内のデータベースが使用できるリソースの下限と上限を制御して、プール内のいずれかのデータベースがプールのすべてのリソースを使用してしまわないようにしたり、プールされているすべてのデータベースに最小限のリソースが保証されるようにしたりすることもできます。 エラスティック プールを使用する SaaS アプリケーションの設計パターンの詳細については、「[マルチテナント SaaS アプリケーションと Azure SQL Database の設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)」を参照してください。 

スクリプトは、エラスティック プールの監視とスケールに役立ちます。 例については、「[PowerShell を使用し、Azure SQL Database の SQL エラスティック プールを監視し、スケーリングする](scripts/sql-database-monitor-and-scale-pool-powershell.md)」を参照してください。

> [!IMPORTANT]
> SQL Database Managed Instance では、エラスティック プールはサポートされません。

### <a name="blend-single-databases-with-pooled-databases"></a>単一データベースとプールされたデータベースの組み合わせ

単一データベースとエラスティック プールのどちらの方法を採っても、その方法に固定されることはありません。 単一データベースをエラスティック プールと組み合わせると、状況に合わせて単一データベースとエラスティック プールのサービス レベルをすばやく簡単に変更することができます。 Azure の強力さと幅広さを利用して、他の Azure サービスを SQL Database とうまく組み合わせることにより、独自のアプリ設計のニーズを満たし、コストとリソースの効率性を向上させ、新たなビジネス チャンスを開くことができます。

### <a name="extensive-monitoring-and-alerting-capabilities"></a>広範囲に及ぶ監視とアラートの機能

しかし、単一データベースとエラスティック プールの相対的なパフォーマンスは、どのような方法で比較すればよいでしょうか? パフォーマンスの高低を調整するとき、何に基づいて右クリックでストップをかけますか? [組み込みのパフォーマンス監視](sql-database-performance.md)および[アラート](sql-database-insights-alerts-portal.md) ツールと、パフォーマンス評価とを組み合わせて使用します。 これらのツールを使用すると、現在または今後のパフォーマンスのニーズに基づいて、スケールアップとスケールダウンの影響をすばやく評価することができます。 詳細については、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)と[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)に関する記事を参照してください。

さらに、SQL Database では、監視を容易にするための[メトリックと診断ログを出力](sql-database-metrics-diag-logging.md)することができます。 リソース使用率、ワーカーとセッション、および接続性を次の Azure リソースのいずれかに格納するように SQL Database を構成することができます。

- **Azure Storage**: 大量のテレメトリを低価格でアーカイブします
- **Azure Event Hub**: SQL Database のテレメトリを、カスタム監視ソリューションまたはホット パイプラインと統合します
- **Azure Log Analytics**: レポート機能、アラート機能、および緩和機能を備えた組み込みの監視ソリューション用です。 これは、[Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) の機能です

    ![アーキテクチャ](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>可用性に関する機能

Microsoft が管理するデータセンターのグローバル ネットワークによって強化された、Azure の業界をリードする可用性 99.99% のサービス レベル アグリーメント [(SLA)](http://azure.microsoft.com/support/legal/sla/) により、アプリケーションの 24 時間 365 日の継続的な稼働が可能になります。 すべての Azure SQL Database は Azure プラットフォームによって完全に管理され、データ損失ゼロおよび高いデータ可用性 (%) が保証されます。 基になるハードウェア、ソフトウェア、ネットワークの障害リスクへの対応や、パッチの適用、バックアップ、レプリケーション、障害検出、バグ修正、フェールオーバー、データベースのアップグレードなど、各種メンテナンス タスクは、Azure によって自動的に処理されます。 Standard の可用性は、計算レイヤーとストレージ レイヤーを分離することで得られます。 Premium の可用性は、計算とストレージを単一ノードに統合してパフォーマンスを確保し、そのうえで、Always On 可用性グループを導入することによって得られます。 Azure SQL Database の高可用性機能の詳細については、[SQL Database の可用性](sql-database-high-availability.md)に関するページを参照してください。 さらに、SQL Database には、次のような、組み込みの[ビジネス継続性とグローバルなスケーラビリティ](sql-database-business-continuity.md)の機能を備えています。

- **[自動バックアップ](sql-database-automated-backups.md)**: SQL Database は、完全バックアップ、差分バックアップ、およびトランザクション ログ バックアップを自動的に実行します。
- **[ポイントインタイム リストア](sql-database-recovery-using-backups.md)**: SQL Database は、自動バックアップのリテンション期間内の任意の時点への回復をサポートします。
- **[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)**: SQL Database を使用すると、同じ Azure データ センターまたは世界各地に分散された Azure データ センター内に、最大 4 つの読み取り可能なセカンダリ データベースを構成することができます。  たとえば、カタログ データベースを使用する SaaS アプリケーションで大量の同時実行の読み取り専用トランザクションが行われる場合は、アクティブ geo レプリケーションを使用してグローバル スケールの読み取りを有効することで、読み取りワークロードによるプライマリ上のボトルネックを取り除くことができます。 
- **[フェールオーバー グループ](sql-database-geo-replication-overview.md)**: SQL Database を使用すると、透過的な geo レプリケーションや大量のデータベースとエラスティック プールのフェールオーバーなどによって、高可用性と負荷分散をグローバル スケールで有効にすることができます。 フェールオーバー グループとアクティブ geo レプリケーションを使用すると、グローバルに分散された SaaS アプリケーションを最小限の管理オーバーヘッドで作成することができ、複雑な監視、ルーティング、およびフェールオーバーのオーケストレーションは、すべて SQL Database にまかせることができます。
- **[ゾーン冗長データベース](sql-database-high-availability.md)**: SQL Database では、Premium または Business Critical のデータベースまたはエラスティック プールを複数の可用性ゾーンにわたってプロビジョニングすることができます。 これらのデータベースとエラスティック プールには、高可用性を目的とする複数の冗長レプリカが存在します。それらのレプリカを複数の可用性ゾーンに配置することで回復性を高め、たとえばデータの損失を生じさせずにデータセンター規模の障害から自動的に復旧することができます。  

## <a name="built-in-intelligence"></a>組み込みのインテリジェンス

SQL Database には、インテリジェンスが組み込まれています。このインテリジェンスによって、データベースの運用コストと管理コストは大幅に削減され、アプリケーションのパフォーマンスとセキュリティの両方が最大化されます。 数百万の顧客のワークロードを 24 時間実行する SQL Database は、水面下で顧客のプライバシーを 100% 尊重しながら、膨大な量のテレメトリ データを収集して処理します。 アプリケーションの状況をサービスが学習し、状況に適応することができるように、さまざまなアルゴリズムによってテレメトリ データが継続的に評価されます。 この分析に基づいて、サービスは、特定のワークロードに合わせたパフォーマンスの向上に関する推奨事項を考え出すことができます。 

### <a name="automatic-performance-monitoring-and-tuning"></a>自動パフォーマンス監視とチューニング

SQL Database は、監視する必要があるクエリの詳細な洞察を提供します。 SQL Database は、データベースのパターンについて学習し、データベース スキーマをワークロードに適応させることができます。 SQL Database は[パフォーマンスのチューニングに関する推奨事項](sql-database-advisor.md)を提示します。チューニング アクションを確認してそれらを適用できます。 

ただし、データベースを常に監視することは厄介で面倒なタスクであり、多数のデータベースを処理する場合は特にそうなります。 [Intelligent Insights](sql-database-intelligent-insights.md) は、スケールで SQL Database のパフォーマンスを自動的に監視することによってこのジョブを行い、パフォーマンスの低下の問題を通知し、問題の根本原因を特定し、可能な場合にはパフォーマンスの向上の推奨事項を提供します。

膨大な数のデータベースを管理することは、SQL Database と Azure ポータルが備えているすべての使用可能なツールとレポートを使用しても、効率的に実行するのは不可能な場合があります。 データベースの監視とチューニングを手動で行う代わりに、[自動チューニング](sql-database-automatic-tuning.md)を使用して、監視とチューニング アクションの一部を SQL Database に委任することを検討できます。 SQL Database は、推奨事項を自動的に適用し、テストを行い、各チューニング アクションを検証して、パフォーマンスが向上していることを確認します。 このように、SQL Database は、制御された安全な方法で、ワークロードに自動的に適応します。 自動チューニングは、データベースのパフォーマンスを注意深く監視し、すべてのチューニング アクションの実行前と実行後のパフォーマンスを比較し、パフォーマンスが向上していない場合はチューニング アクションを取り消すことを意味します。

今日、SQL Database 上で [SaaS マルチテナント アプリ](sql-database-design-patterns-multi-tenancy-saas-applications.md)を実行している当社の多数のパートナーは、パフォーマンスの自動チューニングを信頼して、アプリケーションが常に安定した予測可能なパフォーマンスで実行されるようにしています。 パートナーにとって、この機能は、真夜中にパフォーマンス上の問題が発生するリスクを大幅に軽減するものです。 さらに、顧客ベースの一部では、SQL Server も使用しているため、SQL Database が提示するのと同じインデックスに関する推奨事項を使用して、SQL Server を使用している顧客を支援しています。

[SQL Database では](sql-database-automatic-tuning.md)、次の 2 つの自動チューニングを使用できます。

- **自動インデックス管理**: データベースに追加するインデックスと削除するインデックスを識別します。
- **プランの自動修正**: 問題のあるプランを識別し、SQL プランのパフォーマンスに関する問題を修正します (近日公開予定。SQL Server 2017 では既に利用可能)。

### <a name="adaptive-query-processing"></a>アダプティブ クエリ処理

SQL Database には、[クエリの適応処理機能](/sql/relational-databases/performance/adaptive-query-processing)ファミリも追加されています。この中には、複数ステートメントのテーブル値関数のインターリーブ実行、バッチ モードのメモリ許可のフィードバック、バッチ モードの適応型結合が含まれています。 これらのクエリの適応処理機能には類似の "学習して適応する" 手法が適用されています。この手法は、解決困難なクエリの最適化問題に関連するパフォーマンス問題にさらに深く取り組むために役立っています。

## <a name="advanced-security-and-compliance"></a>高度なセキュリティとコンプライアンス

SQL Database は、アプリケーションがさまざまなセキュリティとコンプライアンスの要件を満たすために役立つ、幅広い[組み込みのセキュリティ機能とコンプライアンス機能](sql-database-security-overview.md)を備えています。 

### <a name="advance-threat-protection"></a>高度な脅威保護

SQL Advanced Threat Protection は、高度な SQL セキュリティ機能のための統合パッケージです。 その機能には、機密データの探索と分類、データベースの脆弱性の管理、データベースへの脅威を示す可能性がある異常なアクティビティの検出などが含まれます。 これらの機能を 1 つの場所で有効にして管理できます。

- [データの検出と分類](sql-database-data-discovery-and-classification.md) (現在プレビュー段階) では、Azure SQL Database に組み込まれる、データベースの機微なデータの検出、分類、ラベル付と保護を行う機能が用意されています。 データベースの分類の状態を把握し、データベース内やその境界を越えて機密データへのアクセスを追跡するために使用できます。
- [脆弱性評価](sql-vulnerability-assessment.md)では、データベースの潜在的な脆弱性を検出、追跡、修復できるサービスを簡単に構成できます。 セキュリティの状態を表示することができ、セキュリティの問題を解決して、データベースのセキュリティを強化するために実行可能な手順が含まれます。
- [脅威検出](sql-database-threat-detection.md)では、データベースにアクセスしたりデータベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティが検出されます。 データベースでの不審なアクティビティを継続的に監視し、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンが見つかるとすぐにセキュリティ通知を提供します。 脅威の検出によるアラートは、不審なアクティビティの詳細と、脅威の調査や危険性の軽減のために推奨される対処方法を提供します。

### <a name="auditing-for-compliance-and-security"></a>コンプライアンスとセキュリティの監査

[SQL Database の監査](sql-database-auditing.md)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。 監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。

### <a name="data-encryption"></a>データの暗号化

SQL Database は、暗号化によってデータのセキュリティを保護します。移動中のデータには[トランスポート層セキュリティ](https://support.microsoft.com/kb/3135244)を使用し、保存データには [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) を使用し、使用中のデータには [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) を使用します。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory との統合と多要素認証

SQL Database では、[Azure Active Directory との統合](sql-database-aad-authentication.md)によって、データベース ユーザーの ID とその他の Microsoft サービスを一元的に管理できます。 この機能は、アクセス許可の管理を簡略化し、セキュリティを強化します。 Azure Active Directory は、[多要素認証](sql-database-ssms-mfa-authentication.md) (MFA) をサポートしています。これにより、シングル サインオン プロセスをサポートすると同時に、データとアプリケーションのセキュリティが強化されます。

### <a name="compliance-certification"></a>コンプライアンス認証

SQL Database は、定期監査に参加し、さまざまなコンプライアンス基準に対する認証を受けています。 詳細については、「[Microsoft Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/)」をご覧ください。ここから最新の [SQL Database コンプライアンス証明書](https://azure.microsoft.com/support/trust-center/services/)の一覧を入手できます。

## <a name="easy-to-use-tools"></a>使いやすいツール

SQL Database は、アプリケーションの開発と管理をより簡単で生産的にします。 SQL Database を使用すると、優れたアプリの構築に注力することができます。 既に所有しているツールとスキルを使用して、SQL Database で管理と開発を行うことができます。

- **[Azure ポータル](https://portal.azure.com/)**: すべての Azure サービスを管理するための Web ベースのアプリケーションです。 
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)**: SQL Server から SQL Database まで、あらゆる SQL インフラストラクチャを管理するための、無料でダウンロードできるクライアント アプリケーションです。
- **[Visual Studio の SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)**: SQL Server リレーショナル データベース、Azure SQL データベース、Integration Services パッケージ、Analysis Services データ モデル、および Reporting Services レポートを開発するための、無料でダウンロードできるクライアント アプリケーションです。
- **[Visual Studio Code](https://code.visualstudio.com/docs)**: Linux、macOS、および Windows 用の無料でダウンロードできるオープン ソースのコード エディターです。Microsoft SQL Server、Azure SQL Database、および SQL Data Warehouse のデータを照会するための [mssql 拡張機能](https://aka.ms/mssql-marketplace)を含む拡張機能をサポートします。

SQL Database は、MacOS、Linux、および Windows での Python、Java、Node.js、PHP、Ruby、および .NET によるアプリケーションの構築をサポートします。 SQL Database は、SQL Server と同じ[接続ライブラリ](sql-database-libraries.md)をサポートします。

## <a name="engage-with-the-sql-server-engineering-team"></a>SQL Server エンジニアリング チームとの交流

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): データベースの管理に関するご質問はこちらへ
- [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-server): 開発に関する質問はこちらへ
- [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): 技術的なご質問はこちらへ
- [フィードバック](http://aka.ms/sqlfeedback): バグの報告や機能リクエスト
- [Reddit](https://www.reddit.com/r/SQLServer/): SQL Server についての意見交換

## <a name="next-steps"></a>次の手順

- 単一データベースとエラスティック プールのコストの比較と計算ツールについては、[価格に関するページ](https://azure.microsoft.com/pricing/details/sql-database/)を参照してください。

- すぐに始めるには、次のクイック スタートをご覧ください。

  - [Azure Portal で SQL データベースを作成する](sql-database-get-started-portal.md)  
  - [Azure CLI で SQL データベースを作成する](sql-database-get-started-cli.md)
  - [PowerShell を使用して SQL データベースを作成する](sql-database-get-started-powershell.md)

- Azure CLI と PowerShell の各種サンプルについては、以下のページをご覧ください。
  - [Azure SQL Database 用の Azure CLI サンプル](sql-database-cli-samples.md)
  - [Azure SQL Database 用の Azure PowerShell サンプル](sql-database-powershell-samples.md)
