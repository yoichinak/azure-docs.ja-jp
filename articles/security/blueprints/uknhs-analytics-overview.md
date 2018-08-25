---
title: Azure のセキュリティとコンプライアンスのブループリント - UK NHS のデータ分析
description: Azure のセキュリティとコンプライアンスのブループリント - UK NHS のデータ分析
services: security
author: jomolesk
ms.assetid: 103dff31-e262-44a6-9b50-3b3467c0cb3a
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: 8885eba0d69c869ad5d298094b835f0351d8d94d
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2018
ms.locfileid: "37343283"
---
# <a name="azure-security-and-compliance-blueprint-data-analytics-for-uk-nhs"></a>Azure のセキュリティとコンプライアンスのブループリント: UK NHS のデータ分析

## <a name="overview"></a>概要

この「Azure のセキュリティとコンプライアンスのブループリント」では、ヘルスケア データの収集、保存、分析、および取得に適したデータ分析ソリューションのための参照アーキテクチャとガイダンスを提供します。 このソリューションは、お客様が、英国 (UK) の保険社会福祉省 (DHSC) のパートナーである [NHS Digital](https://digital.nhs.uk/) によって発行された「[Cloud Security Good Practice Guide](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide)」(クラウド セキュリティ適正実践ガイド) に提示されているガイダンスに従うための方法を示しています。 「Cloud Security Good Practice Guide」(クラウド セキュリティ実践規範ガイド) は、英国 (UK) の国家サイバー セキュリティ センター (NCSC) によって発行された 14 の「[Cloud Security Principles](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)」(クラウド セキュリティ原則) に基づいています。

この参照アーキテクチャ、実装ガイド、および脅威モデルは、お客様固有の要件に適合させるための基礎として使用されることを目的としており、追加の構成なしで運用環境でそのまま使用することはできません。 お客様は、このアーキテクチャを使用してビルドしたソリューションの適切なセキュリティとコンプライアンスの評価を実施する責任を負います。要件は、お客様の実装によって変化する可能性があるからです。

## <a name="architecture-diagram-and-components"></a>アーキテクチャ ダイアグラムとコンポーネント

このソリューションは、独自の分析ツールをビルドできる分析プラットフォームを提供します。 参照アーキテクチャは、SQL/データ管理者によるデータの一括インポートまたは操作ユーザーによる運用データの更新を通してデータを入力する一般的なユース ケースの要点を示しています。 どちらの作業ストリームでも、Azure SQL Database にデータをインポートするために Azure Functions を組み込みます。また、すべての外部接続には TLSv1.2 が必要です。 Azure Functions は、お客様の分析要件に固有のインポート タスクを処理するために、Azure portal 経由でお客様が構成する必要があります。

Azure では、多様なレポート サービスおよび分析サービスを提供していますが、このソリューションでは、迅速にデータを参照し、お客様のデータの高度なモデリングによって迅速に結果を出すために、Azure SQL Database と連動する Azure Analysis Services を組み込みます。 Azure Analytics Services は、データセット間の新しいリレーションシップを見つけることで、クエリ速度を高めることを意図した機械学習の 1 つの形態です。 複数の統計関数を使ってデータがトレーニングされた後は、クエリ ワークロードを分散させて応答時間を削減するために、同じ表形式モデルを使って最大 7 個の追加のクエリ プール (顧客のサーバーを含めた場合、合計 8 個) を同期できます。

分析とレポートを強化するために、Azure SQL Database を列ストア インデックスを使って構成できます。 Azure Analytics Services および Azure SQL Database は両方とも、お客様の用途に対応するために、スケールアップ、スケールダウン、または完全な切り離しが可能です。 すべての SQL トラフィックは、自己署名証明書を含めることによって、SSL で暗号化されます。 ベスト プラクティスとして、Azure では、セキュリティ強化のために信頼された証明書機関を利用することをお勧めします。

データが Azure SQL Database にアップロードされ Azure Analysis Services によってトレーニングされた後、そのデータは Power BI を利用する操作ユーザーと SQL/データ管理者の両方によって使用されます。 Power BI はデータを直感的に表示し、複数のデータセットに及ぶ情報を同時にプルして、より優れた分析情報を描画します。 Power BI の高度な適応性と Azure SQL Database との容易な連携によって、業務ニーズに応じた幅広いシナリオを処理するように Power BI を構成できます。

このソリューションでは、Azure Storage アカウントを使用します。お客様は、Storage Service Encryption を使用して保存データの機密性を保持するように、Azure Storage アカウントを構成できます。 Azure は、回復性のために、お客様が選択したデータセンター内にデータの 3 つのコピーを保存します。 geo 冗長ストレージにより、データは数百マイル離れたセカンダリ データセンターにレプリケートされ、そのデータセンター内に 3 つのコピーとして再度保存されます。これにより、プライマリ データ センターでの有害事象によってデータが失われるのを防ぐことができます。

セキュリティ強化のために、このソリューションのすべてのリソースは、Azure Resource Manager を通じてリソース グループとして管理されます。 デプロイされたリソースへのアクセス (Azure Key Vault 内のキーへのアクセスを含む) を 制御するために、Azure Active Directory のロールベースのアクセス制御が使用されます。 システムの正常性は、Azure Security Center と Azure Monitor によって監視されます。 両方の監視サービスを構成してログをキャプチャし、簡単にナビゲートできる単一のダッシュボードにシステムの正常性を表示できします。

Azure SQL Database は一般的に、SQL Server Management Studio 経由で管理されます。SQL Server Management Studio は、セキュアな VPN または ExpressRoute 接続経由で Azure SQL Database にアクセスするように構成されたローカル コンピューターから実行されます。 **Microsoft では参照アーキテクチャのリソース グループに対する管理とデータ インポートのために、VPN または ExpressRoute 接続を構成することを推奨しています**。

![UK NHS に対する分析の参照アーキテクチャの図](images/uknhs-analytics-architecture.png?raw=true "UK NHS に対する分析の参照アーキテクチャの図")

このソリューションでは、次の Azure サービスを使用します。 デプロイ アーキテクチャの詳細については、「[デプロイメント アーキテクチャ](#deployment-architecture)」セクションを参照してください。

- Azure Active Directory
- Azure Analysis Service
- Azure Automation
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1) /16 ネットワーク
    - (2) /24 ネットワーク
    - (2) ネットワーク セキュリティ グループ
- Power BI ダッシュボード

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ

次のセクションで、デプロイと実装の要素について詳しく説明します。

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) は、インフラストラクチャを明示的にプロビジョニングまたは管理することなく、オンデマンドでコードを実行できるサーバーレス コンピューティング サービスです。 Azure Functions を使用すると、各種のイベントに応じてスクリプトまたはコードの一部を実行できます。

**Azure Analysis Service**: [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) は、エンタープライズ データのモデリングと、Azure データ プラットフォーム サービスとの統合を提供します。 Azure Analysis Service は、複数のソースのデータを単一のデータ モデルに統合することで、大量のデータの参照を高速化します。

### <a name="virtual-network"></a>Virtual network

このアーキテクチャは、10.200.0.0/16 のアドレス空間 を使用してプライベート仮想ネットワークを定義します。

**ネットワーク セキュリティ グループ**: [ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)には、仮想ネットワーク内のトラフィックを許可または拒否するアクセス制御リストが含まれています。 ネットワーク セキュリティ グループを使用して、サブネットまたは個々の仮想マシン レベルでトラフィックを保護できます。 次のネットワーク セキュリティ グループが存在します。

  - Active Directory 用の 1 つのネットワーク セキュリティ グループ
  - ワークロード用の 1 つのネットワーク セキュリティ グループ

各ネットワーク セキュリティ グループでは、ソリューションが安全かつ適切に機能できるように、固有のポートとプロトコルが開かれます。 さらに、各ネットワーク セキュリティ グループで次の構成を使用できます。

- [診断ログとイベント](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)が有効化され、ストレージ アカウントに格納される
- Log Analytics が[ネットワーク セキュリティ グループの診断ログ](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)に接続される

**サブネット**: 各サブネットは、対応するネットワーク セキュリティ グループに関連付けられています。

### <a name="data-in-transit"></a>転送中のデータ

Azure では、Azure データセンターとの間のすべての通信を既定で暗号化します。 Azure Portal での Azure Storage に対するすべてのトランザクションは HTTPS 経由で行われます。

### <a name="data-at-rest"></a>保存データ

このアーキテクチャでは、暗号化、データベース監査などの手段によって保存データを保護します。

**Azure Storage**: 暗号化された保存データの要件を満たすために、すべての [Azure Storage](https://azure.microsoft.com/services/storage/) で [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption) が使用されます。 これは、NHS Digital によって定義されている組織のセキュリティ コミットメントとコンプライアンス要件のサポートにおいてデータを保護するために役立ちます。

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) は、Windows の BitLocker 機能を活用して、OS とデータ ディスクのボリュームを暗号化します。 このソリューションは、ディスクの暗号化キーを制御および管理できるように、Azure Key Vault と統合されています。

**Azure SQL Database**: Azure SQL Database インスタンスは、次のデータベース セキュリティ対策を使用します。

- [Active Directory 認証と承認](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)を使用して、データベース ユーザーの ID 管理と他の Microsoft サービスを一元管理できます。
- [SQL Database の監査](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started)では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。
- Azure SQL Database は、[Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) を使用するように構成されます。Transparent Data Encryption は、データベース、関連付けられたバックアップ、およびトランザクション ログ ファイルのリアルタイムの暗号化と暗号化解除を実行して保存情報を保護します。 Transparent Data Encryption は、保存されているデータが未承認のアクセスの対象になっていないことを保証します。
- [ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)は、適切なアクセス許可が付与されていない限り、データベース サーバーへのすべてのアクセスを阻止します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。
- [SQL の脅威検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)は、不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃、および異常なデータベース アクセス パターンに対するセキュリティの警告を提供することで、潜在的な脅威の検出とそれらの脅威への対応を可能にします。
- [暗号化された列](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)により、データベース システム内で機密データがプレーンテキストとして表示されないことが保証されます。 データ暗号化を有効にした後は、キーへのアクセス権を持つクライアント アプリケーションまたはアプリケーション サーバーのみが、プレーンテキスト データにアクセスできます。
- [SQL Database 動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)は、特権のないユーザーまたはアプリケーションに対してデータをマスクすることで、機密データの露出を制限します。 動的データ マスクは、潜在的な機密データを自動的に検出し、適用する適切なマスクを提案できます。 これは、不正アクセスによるデータベースの終了が発生しないように、データへのアクセスを識別して削減するのに役立ちます。 お客様は、お使いのデータベース スキーマに準拠するように動的データ マスクの設定を調整する必要があります。

### <a name="identity-management"></a>ID 管理

次のテクノロジによって、Azure 環境でのデータへのアクセスを管理する機能が提供されます。

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) は、Microsoft が提供する、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスです。 このソリューションのすべてのユーザー (Azure SQL Database にアクセスするユーザーを含む) は、Azure Active Directory で作成されています。
- アプリケーションに対する認証は Azure Active Directory を使用して行われます。 詳細については、「[Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)」を参照してください。 さらに、データベース列の暗号化では、Azure SQL Database に対してアプリケーションを認証するために Azure Active Directory が使用されます。 詳細については、[SQL Database で機微なデータを保護する](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)方法に関するページを参照してください。
- [Azure ロールベースのアクセス制御](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)によって、管理者は、ジョブの遂行に必要なアクセスのみをユーザーに付与するきめの細かいアクセス許可を定義できます。 すべてのユーザーに Azure リソースへの無制限のアクセス許可を付与する代わりに、管理者は、データにアクセスするための特定のアクションのみを許可できます。 サブスクリプションへのアクセスは、サブスクリプションの管理者に制限されます。
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) により、特定の情報にアクセスできるユーザーの数を最小限に抑えることができます。 管理者は、Azure Active Directory Privileged Identity Management を使用して、特権 ID と特権 ID によるリソースへのアクセスを検出、制限、および監視できます。 この機能を使用して、必要に応じて、オンデマンドの Just-In-Time 管理アクセスを適用することもできます。
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) は、組織の ID に影響を及ぼす可能性のある脆弱性を検出し、組織の ID に関連する検出された不審なアクションに対する自動応答を構成します。さらに、不審なインシデントを調査し、適切なアクションを実行してそれらを解決します。

### <a name="security"></a>セキュリティ

**シークレットの管理**: ソリューションでは、キーとシークレットの管理に [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を使用します。 Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 次の Azure Key Vault 機能は、そのようなデータの保護とアクセスに役立ちます。

- 必要に応じて、高度なアクセス ポリシーが構成されます。
- Key Vault のアクセス ポリシーは、キーとシークレットに対する最低限必要なアクセス許可で定義されます。
- Key Vault のすべてのキーとシークレットに有効期限があります。
- Key Vault 内のすべてのキーは、特殊なハードウェア セキュリティ モジュールによって保護されます。 キーの種類は、ハードウェア セキュリティ モジュールによって保護された 2048 ビット RSA キーです。
- ロールベースのアクセス制御を使用して、すべてのユーザーと ID に最低限必要なアクセス許可が付与されます。
- Key Vault の診断ログは、少なくとも 365 日のリテンション期間で有効になっています。
- キーに対して許可される暗号化操作は必要なものに制限されます。

**Azure Security Center**: [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) を使用すると、お客様はワークロード全体へのセキュリティ ポリシーの一元的な適用と管理、脅威にさらされる状態の軽減、攻撃の検出とその対応を行うことができます。 さらに、Azure Security Center は、Azure サービスの既存の構成にアクセスして、セキュリティ状況の改善とデータの保護に役立つ、構成とサービスに関する推奨事項を提供します。

Azure Security Center では、さまざまな検出機能を使用して、お客様の環境を対象とする攻撃の可能性を通知します。 これらのアラートには、アラートをトリガーした要因、対象となったリソース、攻撃元に関する重要な情報が含まれています。 Azure Security Center には、一連の[事前定義されたセキュリティ アラート](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)が用意されています。これらは、脅威または疑わしいアクティビティが発生した際にトリガーされます。 お客様は、Azure Security Center の[カスタム アラート ルール](https://docs.microsoft.com/azure/security-center/security-center-custom-alert)を使用して、環境から既に収集されているデータに基づいて新しいセキュリティ アラートを定義できます。

Azure Security Center では、お客様の潜在的なセキュリティの問題の検出と対処が簡単になるように、優先度付けしたセキュリティの警告とインシデントを提供しています。 インシデント対応チームによる脅威の調査と修復を支援するために、検出された脅威ごとに[脅威インテリジェンス レポート](https://docs.microsoft.com/azure/security-center/security-center-threat-report)が生成されます。

### <a name="logging-and-auditing"></a>ログ記録と監査

Azure サービスは、システムの正常性だけではなく、システムとユーザーのアクティビティも詳細に記録します。
- **アクティビティ ログ**: [アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) は、サブスクリプション内のリソースに対して実行された操作に関する分析情報を提供します。 アクティビティ ログは、操作のイニシエーター、発生時刻、および状態の判断に役立ちます。
- **診断ログ**: [診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)には、各リソースによって出力されるすべてのログが含まれます。 これらのログには、Windows イベント システム ログ、Azure Storage ログ、Key Vault 監査ログ、および Application Gateway のアクセス ログとファイアウォール ログが含まれます。 すべての診断ログは、暗号化され、集中管理された Azure Storage アカウントに書き込まれ、アーカイブされます。 リテンション期間には、組織固有の保有要件を満たすために最長 730 日までの日数をユーザーが設定できます。

**Log Analytics**: これらのログは、処理、格納、およびダッシュボードのレポート化を行うために、[Log Analytics](https://azure.microsoft.com/services/log-analytics/) に統合されます。 収集されたデータは、その型ごとに別個のテーブルにまとめられ、すべてのデータがその収集元にかかわらず一斉に分析できる状態になります。 さらに、Azure Security Center を Log Analytics と統合することで、お客様は Log Analytics クエリを使用してセキュリティ イベント データにアクセスして、それを他のサービスからのデータと組み合わせることができます。

このアーキテクチャの一部として、次の Log Analytics [管理ソリューション](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)が含まれます。
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): この Active Directory 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧を提供します。
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): この SQL 正常性チェック ソリューションは、一定の間隔でサーバー環境のリスクと正常性を評価し、デプロイされたサーバー インフラストラクチャに固有の推奨事項を重要度別に示した一覧を提供します。
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Agent Health ソリューションは、デプロイされたエージェント数とその地理的配置に加え、応答しないエージェント数やオペレーショナル データを送信しているエージェント数を報告します。
-   [Activity Logs Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Activity Log Analytics ソリューションは、すべての Azure サブスクリプションにわたる Azure アクティビティ ログの分析に役立ちます。

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) は、Runbook の格納、実行、および管理を行います。 このソリューションでは、Runbook を使用して、Azure SQL Database からログを収集できます。 Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) ソリューションは、お客様が環境の変更を簡単に識別できるようにします。

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) は、監査、アラートの作成、データのアーカイブ、および Azure リソース内の API 呼び出しの追跡を実行できるようにすることで、パフォーマンスの追跡、セキュリティの維持、および傾向の識別を実行できるようにします。

## <a name="threat-model"></a>脅威モデル

この参照アーキテクチャのデータ フロー ダイアグラムを[ダウンロード](https://aka.ms/uknhs-analytics-tm)するか、下記を参照してください。 このモデルは、システム インフラストラクチャ内の潜在的なリスク箇所を把握する上で役立ちます。

![UK NHS 脅威モデルの分析](images/uknhs-analytics-threat-model.png?raw=true "UK NHS 脅威モデルの分析")

## <a name="compliance-documentation"></a>コンプライアンス ドキュメント

「[Azure Security and Compliance Blueprint – UK NHS Customer Responsibility Matrix](https://aka.ms/uknhs-crm)」(Azure のセキュリティとコンプライアンスのブループリント – UK NHS のお客様の責任マトリックス) には、UK NHS で必要とされるすべてのセキュリティ原則の一覧が示されています。 このマトリックスには、各原則の実装が、マイクロソフト、お客様、または両者間での共有の責任のどれに該当するかが、詳細に示されています。

「[Azure Security and Compliance Blueprint – UK NHS Data Analytics Implementation Matrix](https://aka.ms/uknhs-analytics-cim)」(Azure のセキュリティとコンプライアンスのブループリント – UK NHS データ分析実装マトリックス) では、データ分析アーキテクチャが UK NHS のどの要件に対応するかについての情報が提供されています。これには、実装が、対応している各原則の要件をどのように満たすかについての詳しい説明も含まれます。


## <a name="guidance-and-recommendations"></a>ガイダンスと推奨事項

### <a name="vpn-and-expressroute"></a>VPN と ExpressRoute

このデータ分析参照アーキテクチャの一部としてデプロイされるリソースへの接続を安全に確立するために、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) またはセキュリティ保護された VPN トンネルを構成する必要があります。 ExpressRoute または VPN を適切に設定することで、転送中のデータに対して保護のレイヤーを追加できます。

Azure を使用してセキュリティ保護された VPN トンネルを実装することにより、オンプレミス ネットワークと Azure 仮想ネットワークの間で仮想プライベート接続を作成できます。 この接続はインターネットをまたいでいるため、自社ネットワークと Azure の間の暗号化されたリンクの&quot;トンネル&quot;を通して情報を送信できます。 サイト間 VPN は、数十年にわたってあらゆる規模の企業に導入されている安全で成熟したテクノロジです。 このオプションでは、暗号化メカニズムとして [IPsec トンネル モード](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10))が使用されています。

VPN トンネル内のトラフィックはサイト間 VPN を使用してインターネット上を転送されるため、Microsoft は、さらに高いセキュリティで保護されたもう 1 つの接続オプションを提供します。 Azure ExpressRoute は、オンプレミスの場所または Exchange ホスティング プロバイダーと Azure の間に確立される専用 WAN リンクです。 ExpressRoute 接続はインターネットを経由しないため、これらの接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。 さらに、これはお客様の通信プロバイダーの直接接続であり、データがインターネット上を移動しないため、インターネットに公開されることはありません。

オンプレミス ネットワークを Azure に拡張するセキュアなハイブリッド ネットワークを実装するためのベスト プラクティスが[提供されています](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

### <a name="extract-transform-load-process"></a>抽出、変換、読み込みプロセス

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) は、別の抽出、変換、読み込み、インポート ツールなしで Azure SQL Database にデータを読み込むことができます。 PolyBase では、T-SQL クエリを使用してデータにアクセスできます。 Microsoft のビジネス インテリジェンスと分析スタックに加え、SQL Server 対応のサードパーティ ツールを PolyBase で使用できます。

### <a name="azure-active-directory-setup"></a>Azure Active Directory の設定
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) は、デプロイの管理と環境を操作する担当者へのアクセスのプロビジョニングを行うために不可欠です。 既存の Windows Server Active Directory は、[4 回のクリック](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express)で Azure Active Directory と統合できます。 また、デプロイされた Active Directory インフラストラクチャを Azure Active Directory フォレストのサブドメインにすることで、デプロイされた Active Directory インフラストラクチャ (ドメイン コントローラー) を既存の Azure Active Directory に連結できます。

## <a name="disclaimer"></a>免責事項

 - このドキュメントは、参考目的でのみ使用してください。 マイクロソフトは、本文書の情報に対して、明示的、黙示的、法的であるかを問わず、一切の保証を行わないものとします。 このドキュメントは "現状のまま" 提供されます。 このドキュメントに記載された情報と見解は、URL やその他のインターネット Web サイトの参照も含め、予告なく変更する可能性があります。 このドキュメントをお読みになったお客様は、自己責任でドキュメントをご利用ください。
 - このドキュメントは、Microsoft 製品に含まれる知的財産に対するいかなる法的権利もお客様に提供するものではありません。
 - お客様の社内での参照目的に限り、このドキュメントを複製し使用することができます。
 - このドキュメントに記載されている特定の推奨事項に従った結果、Azure でのデータ、ネットワーク、コンピューティング リソースの使用量が増加したり、ライセンス コストやサブスクリプション コストが増加したりする場合があります。
 - このアーキテクチャは、特定の要件に合わせて調整するための基礎として使用されることを目的としており、そのまま運用環境に適用しないでください。
 - このドキュメントは、参照資料として作成されており、お客様が特定のコンプライアンス要件や規制に適合するためのすべての手段を定義するために使用されるべきではありません。 お客様は、承認された顧客実装について、組織の法務課からのアドバイスを受けてください。
