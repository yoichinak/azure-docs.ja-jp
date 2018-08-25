---
title: Azure Security の概要 | Microsoft Docs
description: Azure のセキュリティ、サービス、およびそのしくみについて説明します。
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: a1cd2ecc738434fc8d5de03ef538be3ea896fa2b
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971008"
---
# <a name="introduction-to-azure-security"></a>Azure セキュリティの概要
## <a name="overview"></a>概要
セキュリティはクラウドの最優先の課題であり、Azure セキュリティについての正確でタイムリーな情報を得ることがどれだけ重要かを、私たちは認識しています。 アプリケーションとサービスに Azure を使用する最大の理由の 1 つは、さまざまなセキュリティ ツールや機能を活用できることです。 これらのツールや機能により、Azure プラットフォーム上にセキュリティで保護されたソリューションを作成できるようになります。 Microsoft Azure では、透過的な説明責任を実現しつつ、顧客データの機密性、整合性、および可用性を提供しています。

Microsoft Azure に実装されている多数のセキュリティ制御について、お客様側と Microsoft 側の運用上の観点からご理解いただくために、このホワイト ペーパー「Azure セキュリティの概要」では、Microsoft Azure で提供されるセキュリティについて総合的に説明します。

### <a name="azure-platform"></a>Azure プラットフォーム
Azure は、オペレーティング システム、プログラミング言語、フレームワーク、ツール、データベース、デバイスにおいて幅広い選択肢をサポートするパブリック クラウド サービス プラットフォームです。 Docker を統合した Linux コンテナーの実行、JavaScript、Python、.NET、PHP、Java、Node.js によるアプリの構築、iOS、Android、Windows の各デバイスに対応したバックエンドの構築を行えます。

Azure パブリック クラウド サービスでは、何百万人もの開発者や IT プロフェッショナルから現在信頼が寄せられているのと同じテクノロジがサポートされています。 IT 資産を構築し、パブリック クラウド サービス プロバイダーに移行したとしましょう。このとき、移行したアプリケーションやデータをどこまで保護できるかは、採用したプロバイダーがクラウド ベースの資産のセキュリティ管理のためにどのようなサービスと体制を用意しているかに応じて変わってきます。

Azure のインフラストラクチャでは、数百万の顧客を同時にホストできるように施設からアプリケーションまでが設計されており、ビジネスのセキュリティ要件を満たす信頼性の高い基盤となっています。

また、Azure には構成可能な幅広いセキュリティ オプションと制御機能が用意されており、組織によるデプロイの独自の要件を満たすようにセキュリティをカスタマイズできます。 このドキュメントでは、Azure のセキュリティ機能を使用して、これらの要件をどのように満たすことができるかをわかりやすく説明します。

> [!Note]
> ここでは、アプリケーションやサービスをカスタマイズしてセキュリティを強化できる顧客向けの制御機能に重点を置いています。
>
> 概要情報だけでなく Microsoft で Azure プラットフォーム自体のセキュリティを保護する方法の詳細について確認するには、 [Microsoft セキュリティ センター](https://www.microsoft.com/TrustCenter/default.aspx) を参照してください。

### <a name="abstract"></a>要約
当初、パブリック クラウドの移行は、コストの削減と素早い導入によって決定されていました。 セキュリティは、これまで長い間パブリック クラウドの移行にとって重大な懸念事項であり、致命的な問題でもありました。 しかし、パブリック クラウドのセキュリティはいつしか重大な懸念事項からクラウド移行の原動力へと変化していきました。 この背景には、アプリケーションを保護する大規模なパブリック クラウド サービス プロバイダーの優れた能力と、クラウド ベースの資産データがあります。

Azure のインフラストラクチャでは、数百万の顧客を同時にホストできるように施設からアプリケーションまでが設計されており、ビジネスのセキュリティ ニーズを満たす信頼性の高い基盤となっています。 また、Azure には構成可能な幅広いセキュリティ オプションと制御機能が用意されており、デプロイの独自の要件を満たすようにセキュリティをカスタマイズできるだけでなく、IT 制御ポリシーや外部規制の遵守も実現します。

このホワイト ペーパーでは、Microsoft Azure クラウド プラットフォームでの Microsoft のセキュリティ対策について、次の点を中心に概説します。
* Azure インフラストラクチャ、顧客データ、およびアプリケーションのセキュリティを確保するために Microsoft で実装されるするセキュリティ機能。
* サービスのセキュリティおよび Azure サブスクリプション内のデータをユーザーが管理できるようにする Azure のサービスとセキュリティ機能。

## <a name="summary-azure-security-capabilities"></a>Azure のセキュリティ機能の概要
次の表に、Azure インフラストラクチャ、顧客データ、および安全なアプリケーションのセキュリティを確保するために Microsoft で実装されているセキュリティ機能の概要を示します。
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Azure プラットフォームのセキュリティを確保するために実装されるセキュリティ機能
次に一覧された機能で、Azure プラットフォームが安全な方法で管理されていることを確認できます。 Microsoft が安全なプラットフォーム、プライバシー管理、コンプライアンス、透明性という 4 つの領域においてお客様の信頼に関する質問にどのように対処しているかの詳細については、リンクをクリックすることで確認できます。


| [安全なプラットフォーム](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [プライバシー管理](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[コンプライアンス](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [透明性](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [セキュリティ開発サイクル](https://www.microsoft.com/en-us/sdl/)、内部監査 | [データの常時管理](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [トラスト センター](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Microsoft が Azure サービスで顧客データの安全性を確保する方法](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [必須のセキュリティ トレーニング、バック グラウンド チェック](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [データ保管場所の管理](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [共通管理ハブ](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Microsoft が Azure サービスでデータの保管場所を管理する方法](http://azuredatacentermap.azurewebsites.net/)|
| [侵入テスト](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx)、[不正侵入検出、DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement)、[監査、ログ記録](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [条件に応じたアクセス権の付与](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [クラウド サービス向けデリジェンス チェックリスト](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[データにアクセスできるユーザーとその条件](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [最新のデータ センター、](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)、物理的なセキュリティ、[セキュリティで保護されたネットワーク](https://docs.microsoft.com/azure/security/security-network-overview) | [法執行機関への対応](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [サービス、場所、および業界ごとのコンプライアンス](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Microsoft が Azure サービスで顧客データの安全性を確保する方法](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [セキュリティ インシデント対応](http://aka.ms/SecurityResponsepaper)、[責任の分担](http://aka.ms/sharedresponsibility) |[厳格なプライバシー基準](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Azure サービスの証明書確認、Transparency Hub](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>データおよびアプリケーションのセキュリティを確保するために Azure で提供されるセキュリティ機能
アプリケーションやサービスのセキュリティを管理する担当者の責任範囲は、クラウド サービス モデルによって異なります。 ビルトイン機能および Azure サブスクリプションに展開可能なパートナー ソリューションに、これらの責任範囲をカバーする Azure プラットフォームで使用可能な機能があります。

ビルトイン機能は、運用、アプリケーション、ストレージ、ネットワーク、コンピューティング、ID という 6 つの領域で構成されています。 Azure プラットフォームのこれら 6 つの領域で使用できる機能の詳細については、概要情報に記載されています。

## <a name="operations"></a>[操作]
このセクションでは、セキュリティ操作を行う上で重要な機能と、これらの機能についての概要情報に関する追加の情報を提供します。

### <a name="operations-management-suite-security-and-audit-dashboard"></a>Operations Management Suite の Security and Audit ダッシュボード
[OMS の Security and Audit ソリューション](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)では、注意を必要とする重要な問題向けの[組み込みの検索クエリ](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/)と共に、組織の IT セキュリティ対策への包括的な視点が提供されます。 [[Security and Audit]](https://technet.microsoft.com/library/mt484091.aspx) \(セキュリティと監査) ダッシュボードは、OMS におけるすべてのセキュリティ関連機能のホーム画面です。 コンピューターのセキュリティ状態に関する高度な洞察を提供します。 また、過去の 24 時間、7 日間、またはそれ以外のカスタム期間に発生したすべてのイベントを表示する機能も含まれています。

さらに、OMS のセキュリティとコンプライアンスを構成して、特定のイベントが検出されたときに[特定のアクションを自動的に実行](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/)します。

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) を使用すると、ソリューション内の複数のリソースを 1 つのグループにまとめて作業できます。 ソリューションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。 デプロイには [Azure Resource Manager テンプレート](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/)を使用します。このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。 Resource Manager には、デプロイ後のリソースの管理に役立つ、セキュリティ、監査、タグ付けの機能が用意されています。

Azure Resource Manager のテンプレート ベースのデプロイにより、Azure にデプロイされたソリューションのセキュリティが向上します。これは、標準的なセキュリティ制御設定によるもので、標準化されたテンプレート ベースのデプロイに統合できます。 これにより、手動によるデプロイ時に発生する可能性のあるセキュリティ構成エラーのリスクが軽減されます。

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/) は、Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 Application Insights でライブ Web アプリケーションを監視し、パフォーマンス上の問題を自動的に検出することができます。 Application Insights には強力な分析ツールが組み込まれているため、問題の診断や、ユーザーがアプリを使用して実行している操作を把握できます。 テスト中と公開後またはデプロイ後の両方で、実行中のアプリケーションを常時監視します。

Application Insights が作成するグラフや表を見ると、たとえば、1 日の中でユーザー数が最も多い時間帯、アプリの反応性、アプリが依存している外部サービスのサービス性能などがわかります。

クラッシュ、エラー、パフォーマンス問題が発生した場合、製品利用統計情報データを詳しく調査し、原因を診断できます。 アプリの可用性やパフォーマンスに変化があった場合は、サービスからメールが届きます。 Application Insights は、機密性、整合性、および可用性というセキュリティの 3 本柱の中の可用性に役立つ、貴重なセキュリティ ツールとなります。

### <a name="azure-monitor"></a>Azure Monitor

  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) は、Azure インフラストラクチャ ([アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)) と個々の Azure リソース ([診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)) の両方から得られたデータの視覚化、クエリ、ルーティング、アラート、自動スケール、自動化を行います。 Azure Monitor を使用して、Azure ログで生成されたセキュリティ関連のイベントについて通知を作成できます。

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) ([Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) の一部) – Azure リソースだけでなく、オンプレミスやサードパーティ製のクラウド インフラストラクチャ (AWS など) にも使える IT 管理ソリューションです。 Log Analytics には Azure Monitor のデータを直接ルーティングできるため、環境全体のメトリックとログを 1 か所で確認できます。

Log Analytics は、フォレンジック分析などのセキュリティ分析に便利なツールで、セキュリティ関連の項目が大量にあっても柔軟なクエリ方法により迅速に検索を行うことができます。 さらに、オンプレミスの[ファイアウォールおよびプロキシ ログを Azure にエクスポートして、Log Analytics を使用した分析に使用することができます。](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall)

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](https://docs.microsoft.com/azure/advisor/) は、Azure のデプロイの最適化に役立つ、個人用に設定されたクラウド コンサルタントです。 Azure Advisor では、リソース構成と使用量テレメトリを分析します。 次に、[Azure の全体的な使用量を削減する](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations)機会を探すと同時に、[パフォーマンス](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations)、[セキュリティ](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations)、リソースの[高可用性](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations)の向上に役立つソリューションを提案します。 Azure Advisor では、Azure にデプロイするソリューションの全体的なセキュリティの状況を大幅に改善することができる提案を行います。 これらの提案は [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) で実施されるセキュリティ分析に基づいています。

### <a name="azure-security-center"></a>Azure Security Center
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) は、Azure リソースのセキュリティを高度に視覚化し、制御することで脅威を回避、検出し、それに対応できるようにします。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

さらに、Azure Security Center は、すぐに対応できるようにアラートや推奨事項が表示された単一のダッシュ ボードを提供することで、セキュリティ操作に役立てることができます。 多くの場合、Azure Security Center コンソール内で 1 回クリックすれば問題を修復することができます。
## <a name="applications"></a>[アプリケーション]
このセクションでは、アプリケーション セキュリティの重要な機能と、これらの機能についての概要情報に関する追加の情報を提供します。

### <a name="web-application-vulnerability-scanning"></a>Web アプリケーションの脆弱性のスキャン
[App Service アプリ](https://docs.microsoft.com/azure/app-service/app-service-web-overview)の脆弱性のテストを開始する最も簡単な方法の 1 つは、[Tinfoil Security との統合](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)を使用して 1 回のクリックでアプリに対する脆弱性のスキャンを実行することです。 テスト結果はわかりやすいレポートで表示され、詳しい手順に従ってそれぞれの脆弱性を修正する方法が説明されます。

### <a name="penetration-testing"></a>侵入テスト
独自の侵入テストを実行するか、別のスキャナー スイートまたはプロバイダーを使用する場合は、 [Azure 侵入テストの承認プロセス](https://docs.microsoft.com/azure/security/azure-security-pen-testing ) に従い、事前の承認を得たうえで目的の侵入テストを実行する必要があります。

### <a name="web-application-firewall"></a>Web アプリケーション ファイアウォール
[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) の Web アプリケーション ファイアウォール (WAF) は、SQL インジェクション、クロスサイト スクリプティング攻撃、セッション ハイジャックなどの一般的な Web ベースの攻撃から Web アプリケーションを保護するのに役立ちます。 このファイアウォールには、[Open Web Application Security Project (OWASP) により一般的な脆弱性の上位 10 種](https://msdn.microsoft.com/library/)と特定された脅威からの保護が事前に構成されています。

### <a name="authentication-and-authorization-in-azure-app-service"></a>Azure App Service での認証および承認
[App Service の認証と承認](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview)は、アプリのバックエンドでコードを変更する必要がないように、アプリケーションでユーザーをサインインさせる方法を提供する機能です。 これにより、アプリケーションの保護が容易になり、またユーザーごとのデータにも対応できるようになります。

### <a name="layered-security-architecture"></a>複数層セキュリティ アーキテクチャ
[App Service Environments](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-intro) は、[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) にデプロイされる分離されたランタイム環境です。開発者は、セキュリティ アーキテクチャを階層化し、アプリケーションの層ごとにネットワーク アクセスのレベルに違いを設けることができます。 一般に、API バックエンドは通常のインターネット アクセスから隠し、アップストリームの Web アプリにのみ API の呼び出しを許可することが望ましいと考えられています。 App Service Environment を含んだ Azure Virtual Network サブネットに対して[ネットワーク セキュリティ グループ (NSG)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) を使用することで、API アプリケーションへのパブリック アクセスを制限することができます。

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Web サーバー診断とアプリケーション診断
App Service Web Apps は、Web サーバーと Web アプリケーションの両方のログ情報を診断する機能を備えています。 これらは論理的に [Web サーバー診断](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log)と[アプリケーション診断](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx)に分けられます。 Web サーバーでは、サイトおよびアプリケーションの診断とトラブルシューティングに 2 つの大きな進展があります。

1 つ目の新機能は、アプリケーション プール、ワーカー プロセス、サイト、アプリケーション ドメイン、および実行中の要求に関するリアルタイムの状態情報です。 新しい 2 つ目の強みは、完全な要求-応答プロセスによって要求を追跡する詳細なトレース イベントです。

これらのトレース イベントのコレクションを有効にするには、エラー応答コードまたは経過時間に基づいた特定の要求に対して、XML 形式で全トレース ログを自動的にキャプチャするように IIS 7 を設定できます。

#### <a name="web-server-diagnostics"></a>Web サーバー診断
次の種類のログを有効または無効にできます。

-   詳細なエラー ログ - 障害 (状態コード 400 以上) を示す HTTP 状態コードの詳細なエラー情報。 このログには、サーバーがエラー コードを返した理由を特定するために役立つ情報が記録されている場合があります。

-   失敗した要求トレース - 要求の処理に使用された IIS コンポーネントのトレースや各コンポーネントにかかった時間など、失敗した要求の詳細情報。 このログが便利なのは、サイトのパフォーマンスを向上させたり、特定の HTTP エラーが返される理由を特定したりする場合です。

-   Web サーバーのログ記録 - W3C 拡張ログ ファイル形式を使用した、HTTP トランザクションに関する情報。 このレポートが便利なのは、全体的なサイト メトリック、たとえば、サイトで処理された要求の数や、特定の IP アドレスからの要求の数を特定するときです。

#### <a name="application-diagnostics"></a>アプリケーション診断
[アプリケーション診断](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log)では、Web アプリケーションによって生成された情報を取り込むことができます。 ASP.NET アプリケーションは、 [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) クラスを使用して、情報をアプリケーション診断ログに記録できます。 アプリケーション診断では、アプリケーションの障害やエラーに関連するイベントとアプリケーションのパフォーマンスに関連するイベントの、主に 2 つのイベント タイプがあります。 障害やエラーは、さらに接続性、セキュリティ、および障害問題に分かれます。 障害問題とは一般的に、アプリケーション コードでの問題に関連します。

アプリケーション診断では、次の方法でグループ化されたイベントを表示できます。

-   すべて (すべてのイベントを表示)
-   アプリケーション エラー (例外イベントを表示)
-   パフォーマンス (パフォーマンス イベントを表示)

## <a name="storage"></a>Storage
このセクションでは、Azure ストレージのセキュリティの重要な機能と、これらの機能についての概要情報に関する追加の情報を提供します。

### <a name="role-based-access-control-rbac"></a>ロール ベースのアクセス制御 (RBAC)
ロールベースのアクセス制御 (RBAC) を使用して、ストレージ アカウントをセキュリティで保護できます。 データ アクセスにセキュリティ ポリシーを適用する組織では、[必知事項](https://en.wikipedia.org/wiki/Need_to_know)と[最小権限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)のセキュリティ原則に基づいてアクセスを制限することが不可欠です。 これらのアクセス権は、グループおよびアプリケーションに適切な RBAC ロールを特定のスコープで割り当てることによって付与します。 [組み込み RBAC ロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)(ストレージ アカウントの共同作成者など) を使用して、ユーザーに権限を割り当てることができます。 [Azure Resource Manager](https://docs.microsoft.com/azure/storage/storage-security-guide) モデルを使用したストレージ アカウントのストレージ キーに対するアクセス権は、ロールベースのアクセス制御 (RBAC) で制御できます。

### <a name="shared-access-signature"></a>Shared Access Signature
[shared access signature (SAS)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 SAS により、ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間とアクセス許可セットを指定してクライアントに付与できます。 この制限付きアクセス許可を付与するとき、アカウント アクセス キーを共有する必要はありません。

### <a name="encryption-in-transit"></a>転送中の暗号化
転送中の暗号化は、ネットワーク間でデータを転送するときにデータを保護するメカニズムです。 Azure Storage では、以下を使用してデータをセキュリティ保護できます。
-   [トランスポートレベルの暗号化](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit)(Azure Storage の内外にデータを転送する場合の HTTPS など)。

-   [ワイヤ暗号化](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares) ([Azure ファイル共有](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files)の [SMB 3.0 暗号化](https://docs.microsoft.com/azure/storage/storage-security-guide)など)。

-   クライアント側の暗号化 (ストレージにデータを転送する前にデータを暗号化し、ストレージからデータを転送した後にデータを復号化します)。

### <a name="encryption-at-rest"></a>保存時の暗号化
多くの組織にとって、データ プライバシー、コンプライアンス、データ主権を確保する上で保存時のデータの暗号化は欠かせません。 Azure Storage には、“保存時の“ データの暗号化を提供する機能が 3 つあります。

-   [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption) を使用すると、ストレージ サービスが Azure Storage にデータを書き込むときに自動的に暗号化するように要求できます。

-   [クライアント側の暗号化](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) には、保存時の暗号化機能もあります。

-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) を使用すると、IaaS 仮想マシンに使用される OS ディスクとデータ ディスクを暗号化できます。

### <a name="storage-analytics"></a>Storage Analytics
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) では、ログが記録され、ストレージ アカウントのメトリック データを得ることができます。 このデータを使用して、要求のトレース、使用傾向の分析、ストレージ アカウントの問題の診断を行うことができます。 Storage Analytics は、ストレージ サービスに対する要求の成功と失敗についての詳細な情報をログに記録します。 この情報を使って個々の要求を監視したり、ストレージ サービスに関する問題を診断したりできます。 要求は、ベスト エフォートでログに記録されます。 次のタイプの認証済み要求が記録されます。
-   成功した要求

-   失敗した要求 (タイムアウト、帯域幅調整、ネットワーク、承認などに関する各種エラー)

-   Shared Access Signature (SAS) を使用した要求 (失敗した要求と成功した要求を含む)

-   データの分析要求

### <a name="enabling-browser-based-clients-using-cors"></a>CORS を使用したブラウザーベースのクライアントの有効化
[クロス オリジン リソース共有 (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) は、ドメインどうしが互いのリソースへのアクセスを許可するメカニズムです。 ユーザー エージェントは、特定のドメインから読み込まれた JavaScript コードが別のドメインにあるリソースへのアクセスに使用できることを確認する追加のヘッダーを送信します。 次に、後者のドメインが元のドメイン リソースへのアクセスを許可または拒否する追加のヘッダーを使用して応答します。

Azure Storage サービスで CORS がサポートされるようになりました。これにより、サービスに CORS ルールを設定すると、別のドメインからサービスに対して行われた適切な認証要求が評価され、指定したルールに従って許可するかどうかを決定します。
## <a name="networking"></a>ネットワーク
このセクションでは、Azure ネットワーク セキュリティの重要な機能と、これらの機能の概要情報に関する追加の情報を提供します。

### <a name="network-layer-controls"></a>ネットワーク層制御
ネットワーク アクセス制御は、特定のデバイスまたはサブネットとの間の接続を制限する機能で、ネットワーク セキュリティの中核をなすものです。 ネットワーク アクセス制御は、アクセスを許可したユーザーとデバイスのみが、仮想マシンとサービスにアクセスできるようにすることを目的としています。

#### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ
[ネットワーク セキュリティ グループ (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) とは基本的なステートフル パケット フィルタリング ファイアウォールであり、[5 タプル](https://www.techopedia.com/definition/28190/5-tuple)に基づいてアクセスを制御します。 NSG はアプリケーション層検査も、認証済みのアクセス制御も提供しません。 NSG を使用して、Azure Virtual Network 内のサブネット間および Azure Virtual Network とインターネットの間を移動するトラフィックを制御できます。

#### <a name="route-control-and-forced-tunneling"></a>ルート制御と強制トンネリング
Azure Virtual Network におけるルーティング動作を制御する機能は、ネットワーク セキュリティとアクセス制御の重要な機能です。 たとえば Azure Virtual Network との間のすべてのトラフィックに仮想セキュリティ アプライアンスを経由させたいと考えている場合は、ルーティング動作を制御したりカスタマイズしたりできるようにする必要があります。 これは、Azure でユーザー定義ルートを構成することで実現します。

[ユーザー定義ルート](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)によって、個々の仮想マシンまたはサブネットの間を移動するトラフィックの受信および送信パスをカスタマイズし、最も安全なルートを確保できるようになります。 [強制トンネリング](https://www.petri.com/azure-forced-tunneling)は、サービスがインターネット上のデバイスとの接続を開始する許可を得られないようにするメカニズムです。

これは、着信接続を受け入れて、それに応答することとは異なりますのでご注意ください。 フロントエンド Web サーバーは、インターネット ホストからの要求に応答する必要があります。したがって、インターネットからのトラフィックがこれらの Web サーバーに着信することも、Web サーバーが応答することも許可されます。

強制トンネリングは一般的に、インターネットへの送信トラフィックが、オンプレミスのセキュリティ プロキシおよびファイアウォールを強制的に経由するために使用されます。

#### <a name="virtual-network-security-appliances"></a>仮想ネットワークのセキュリティ アプライアンス
ネットワーク セキュリティ グループ、ユーザー定義ルート、強制トンネリングにより [OSI モデル](https://en.wikipedia.org/wiki/OSI_model)のネットワーク層とトランスポート層のレベルでセキュリティ保護を行うことはできますが、より高いレベルのスタックでセキュリティを有効にする場合は時間がかかることがあります。 Azure パートナー ネットワーク セキュリティ アプライアンス ソリューションを使用すれば、これらの拡張ネットワーク セキュリティ機能を利用できます。 最新の Azure パートナー ネットワーク セキュリティ ソリューションについては、[Azure Marketplace](https://azure.microsoft.com/marketplace/) にアクセスし、"security" および "network security" と検索すると見つかります。

### <a name="azure-virtual-network"></a>Azure Virtual Network

Azure 仮想ネットワーク (VNet) は、クラウド内のユーザー独自のネットワークを表すものです。 サブスクリプション専用に Azure ネットワーク ファブリックが論理的に分離されています。 このネットワークでは、IP アドレス ブロック、DNS 設定、セキュリティ ポリシー、およびルート テーブルを完全に制御できます。 VNet をサブネットに分割し、Azure IaaS 仮想マシン (VM) や[クラウド サービス (PaaS ロール インスタンス)](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) を配置できます。

また、Azure のいずれかの[接続オプション](https://docs.microsoft.com/azure/vpn-gateway/)を使用し、仮想ネットワークをオンプレミスのネットワークに接続することができます。 つまり、Azure が提供するエンタープライズ規模のメリットを享受しながら、IP アドレス ブロックを完全に制御して、ネットワークを Azure に拡張できます。

Azure のネットワークは、セキュリティで保護されたリモート アクセスのさまざまなシナリオをサポートしています。 たとえば、次のようなシナリオがあります。

-   [個々のワークステーションから Azure Virtual Network への接続](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-   [オンプレミス ネットワークから Azure Virtual Network への VPN による接続](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-   [オンプレミス ネットワークから Azure Virtual Network への専用 WAN リンクによる接続](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-   [Azure Virtual Network どうしの接続](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="vpn-gateway"></a>VPN Gateway
Azure Virtual Network とオンプレミスのサイトとの間でネットワーク トラフィックを送信する場合は、Azure Virtual Network 用の VPN ゲートウェイを作成する必要があります。 [VPN ゲートウェイ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)は、パブリック接続で暗号化されたトラフィックを送信する仮想ネットワーク ゲートウェイの一種です。 VPN ゲートウェイを使用すると、Azure ネットワーク ファブリックを経由して Azure Virtual Network 間でトラフィックを送信することもできます。

### <a name="express-route"></a>ExpressRoute
Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) は専用の WAN リンクです。これにより接続プロバイダーが提供する専用プライベート接続で、オンプレミスのネットワークを Microsoft クラウドに拡張できます。

![ExpressRoute](./media/azure-security/azure-security-fig1.png)

ExpressRoute では、Microsoft Azure、Office 365、CRM Online などの Microsoft クラウド サービスへの接続を確立できます。 接続には、任意の環境間 (IP VPN) 接続、ポイントツーポイントのイーサネット接続、共有施設での接続プロバイダーによる仮想交差接続があります。

ExpressRoute 接続はパブリック インターネットを経由しないため、VPN ベースのソリューションよりも安全であると考えられています。 それにより、ExpressRoute 接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。


### <a name="application-gateway"></a>Application Gateway
Microsoft [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) によって[アプリケーション配信コントローラー (ADC) ](https://en.wikipedia.org/wiki/Application_delivery_controller)がサービスとして提供され、さまざまなレイヤー 7 負荷分散機能がアプリケーションで利用できるようになります。

![Application Gateway](./media/azure-security/azure-security-fig2.png)

これにより、CPU を集中的に使用する SSL の終了を Application Gateway にオフロードし (“SSL オフロード” または “SSL ブリッジ” とも呼ばれる)、Web ファームの生産性を最適化できます。 また、着信トラフィックのラウンド ロビン分散、Cookie ベースのセッション アフィニティ、URL パス ベースのルーティング、単一の Application Gateway の背後で複数の Web サイトをホストする機能など、その他のレイヤー 7 ルーティング機能も用意されています。 Azure Application Gateway はレイヤー 7 のロード バランサーです。

クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。

Application Gateway は、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、[Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell) オフロード、カスタムの正常性プローブ、マルチサイトのサポートなどの多くのアプリケーション配信コントローラー (ADC) 機能を備えています。

### <a name="web-application-firewall"></a>Web アプリケーション ファイアウォール
Web アプリケーション ファイアウォール (WAF) は [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) の機能で、標準のアプリケーション配信コントロール (ADC) 機能に対してアプリケーション ゲートウェイを使用して、Web アプリケーションを保護します。 Web アプリケーション ファイアウォールは、OWASP の上位 10 件の一般的 Web 脆弱性の大部分に対する保護を提供することで、これを実現します。

![Web アプリケーション ファイアウォール](./media/azure-security/azure-security-fig1.png)

-   SQL インジェクションからの保護

-   一般的な Web 攻撃からの保護 (コマンド インジェクション、HTTP 要求スマグリング、HTTP レスポンス スプリッティング、リモート ファイル インクルード攻撃など)

-   HTTP プロトコル違反に対する保護

-   HTTP プロトコル異常に対する保護 (ホスト ユーザー エージェントと承認ヘッダーが見つからない場合など)

-   ボット、クローラー、スキャナーの防止

-   一般的なアプリケーション構成ミスの検出 (Apache、IIS など)


Web 攻撃に対する保護を提供する Web アプリケーション ファイアウォールを一元化することで、セキュリティの管理がはるかに簡単になり、侵入の脅威からアプリケーションがより確実に保護されます。 また、WAF のソリューションは、1 か所に既知の脆弱性の修正プログラムを適用することで、個々の Web アプリケーションをセキュリティで保護する場合と比較して、さらに迅速にセキュリティの脅威に対応できます。 既存のアプリケーション ゲートウェイは、Web アプリケーション ファイアウォールを備えたアプリケーション ゲートウェイに簡単に変換できます。
### <a name="traffic-manager"></a>Traffic Manager
[Microsoft Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) では、さまざまなデータ センターのサービス エンドポイントへのユーザー トラフィックの分散を制御できます。 Traffic Manager でサポートされるサービス エンドポイントには、Azure VM、Web アプリケーション、およびクラウド サービスが含まれます。 Azure 以外の外部エンドポイントで Traffic Manager を使用することもできます。 Traffic Manager では、ドメイン ネーム システム (DNS) を使用して、[トラフィック ルーティング方法](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods)とエンドポイントの正常性に基づいて最適なエンドポイントにクライアント要求を送信します。

Traffic Manager には、さまざまなアプリケーション ニーズ、エンドポイントの正常性、[監視](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)、自動フェールオーバーに対応する、さまざまなトラフィック ルーティング方法が備わっています。 Traffic Manager は Azure リージョン全体の障害などの障害に対応します。
### <a name="azure-load-balancer"></a>Azure Load Balancer
[Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) は、高可用性と優れたネットワーク パフォーマンスをアプリケーションに提供します。 Azure Load Balancer は、負荷分散セットで定義されているサービスの正常なインスタンス間で着信トラフィックを分散する、レイヤー 4 (TCP、UDP) ロード バランサーです。 Azure Load Balancer は次のように構成できます。

-   仮想マシンへの着信インターネット トラフィックを負荷分散します。 この構成は、 [インターネットに接続する負荷分散](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview)と呼ばれます。

-   仮想ネットワーク内の仮想マシン間、クラウド サービス内の仮想マシン間、クロスプレミスの仮想ネットワーク内のオンプレミスのコンピューターと仮想マシン間で、トラフィックを負荷分散します。 この構成は、 [内部負荷分散](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview)と呼ばれます。 

- 外部トラフィックを特定の仮想マシンに転送します。

### <a name="internal-dns"></a>内部 DNS
VNet で使用される DNS サーバーの一覧は、管理ポータルまたはネットワーク構成ファイルで管理できます。 VNet ごとに最大 12 台の DNS サーバーを追加できます。 DNS サーバーを指定する際は、環境に適した順で DNS サーバーが一覧表示されているか確認することが重要です。 DNS サーバーの一覧はラウンド ロビンに対応していません。 指定した順序で使用されます。 一覧の先頭にある DNS サーバーに到達できる場合は、DNS サーバーが正しく動作しているかどうかに関係なく、その DNS サーバーを使用します。 仮想ネットワーク用に DNS サーバーの順序を変更するには、該当する DNS サーバーを一覧からいったん削除し、希望の順序になるように再度追加します。 DNS では、セキュリティの 3 つの柱、"CIA" (機密性、整合性、可用性) の中の可用性がサポートされています。

### <a name="azure-dns"></a>Azure DNS
[ドメイン ネーム システム (DNS)](https://technet.microsoft.com/library/bb629410.aspx) は、Web サイトまたはサービスの名前をその IP アドレスに変換する (または解決する) 役割を担います。 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 DNS では、セキュリティの 3 つの柱、"CIA" (機密性、整合性、可用性) の中の可用性がサポートされています。
### <a name="log-analytics-nsgs"></a>Log Analytics NSG
NSG に対して、以下の診断ログ カテゴリを有効にできます。
-   イベント: MAC アドレスに基づいた、VM とインスタンス ロールに適用される NSG ルールに関するエントリが含まれます。 これらのルールの状態は 60 秒ごとに収集されます。

-   ルール カウンター: トラフィックを許可または拒否するために各 NSG ルールが適用された回数に関するエントリが含まれます。

### <a name="azure-security-center"></a>Azure Security Center
Security Center は、脅威の回避、検出、対応に役立つサービスで、Azure リソースのセキュリティを高度に視覚化して制御できます。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。 ネットワークに関する推奨事項は、ファイアウォール、ネットワーク セキュリティ グループ、受信トラフィック規則の構成などが中心です。

ネットワークに関する利用可能な推奨事項は次のとおりです。

-   [次世代のファイアウォールの追加](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall)セキュリティ保護を強化するために、Microsoft パートナーの次世代ファイアウォール (NGFW) を追加することをお勧めします。

-   [NGFW 経由のみでトラフィックをルーティングする](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only)。 VM への受信トラフィックを必ず NGFW 経由にするようにネットワーク セキュリティ グループ (NSG) の規則を構成することをお勧めします。

-   [サブネットまたは仮想マシンでのネットワーク セキュリティ グループの有効化](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups)サブネットまたは VM で NSG を有効にすることをお勧めします。

-   [インターネットに接続するエンドポイント経由のアクセスを制限する](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints)。NSG の着信トラフィック規則を構成することをお勧めします。


## <a name="compute"></a>コンピューティング

このセクションでは、この領域の重要な機能と、これらの機能についての概要情報に関する追加の情報を提供します。

### <a name="antimalware--antivirus"></a>マルウェア対策とウイルス対策ソフトウェア
Azure IaaS では、Microsoft、Symantec、Trend Micro、McAfee、Kaspersky などのセキュリティ ベンダーが提供するマルウェア対策ソフトウェアを利用できます。これにより、悪意のあるファイルやアドウェアなどの脅威から仮想マシンを保護できます。 Azure Cloud Services および Azure Virtual Machines に対する [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)は、ウイルス、スパイウェアなどの悪意のあるソフトウェアの特定や駆除に役立つ保護機能です。 Microsoft Antimalware は、既知の悪意あるまたは望ましくないソフトウェアが Azure システム上に自動でインストールまたは実行されそうになった場合に、構成可能なアラートを提供します。 Microsoft Antimalwareは、Azure Security Center を使用してデプロイすることもできます。

### <a name="hardware-security-module"></a>ハードウェア セキュリティ モジュール
暗号化と認証は、キー自体が保護されない限り、セキュリティを向上させません。 大切な秘密情報とキーを [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) に格納することで、それらの管理とセキュリティ保護をシンプルにできます。 Key Vault では、オプションとして、キーを保管するためのハードウェア セキュリティ モジュール (HSM) が提供されています。HSM は FIPS 140-2 レベル 2 標準に準拠しています。 バックアップまたは [Transparent Data Encryption](https://msdn.microsoft.com/library/bb934049.aspx) 用の SQL Server 暗号化キーに加えて、アプリケーションのすべてのキーや秘密情報を Key Vault に格納できます。 保護されたこれらのアイテムに対するアクセス許可とアクセスは、[Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/) を通して管理されます。

### <a name="virtual-machine-backup"></a>仮想マシンのバックアップ
[Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) は、設備投資なしで、また最小限の運用コストでアプリケーション データを保護できるソリューションです。 アプリケーション エラーが発生するとデータが破損するおそれがあり、ヒューマン エラーが生じればアプリケーションにバグが生まれてセキュリティ上の問題につながる危険があります。 Azure Backup により、Windows と Linux で実行されている仮想マシンが保護されます。

### <a name="azure-site-recovery"></a>Azure Site Recovery
組織の[ビジネス継続性/ディザスター リカバリー (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) 戦略において重要となるのは、計画済みおよび計画外の停止が発生した場合に企業のワークロードとアプリを継続して実行する方法を見極めることです。 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) は、ワークロードとアプリのレプリケーション、フェールオーバー、および復旧の調整に役立ちます。これにより、1 次拠点がダウンした場合でも 2 次拠点からワークロードとアプリを利用できます。

### <a name="sql-vm-tde"></a>SQL VM TDE
[透過的なデータ暗号化 (TDE)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault)、および列レベルの暗号化 (CLE)が SQL Server の暗号化機能です。 これらの形態の暗号化では、暗号化に利用する暗号鍵を管理し、保存する必要があります。

Azure Key Vault (AKV) サービスは、セキュリティを強化し、安全かつ可用性の高い場所で鍵を管理できるように設計されています。 SQL Server コネクタ を利用すると、SQL Server で Azure Key Vault にある鍵を利用できるようになります。

SQL Server をオンプレミス コンピューターで実行している場合、いくつかの手順を踏んでオンプレミスの SQL Server コンピューターから Azure Key Vault にアクセスできます。 ただし、Azure VM の SQL Server の場合、 Azure Key Vault の統合機能を利用することで時間を節約できます。 いくつかの Azure PowerShell コマンドレットでこの機能を有効にし、SQL VM が Key Vault にアクセスするために必要な構成を自動化できます。

### <a name="vm-disk-encryption"></a>VM ディスクの暗号化
[Azure Disk Encryption ](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) は、Windows および Linux IaaS 仮想マシン ディスクを暗号化するのに役立つ新機能です。 この機能では、OS およびデータ ディスクのボリュームを暗号化するために、Windows の業界標準である BitLocker 機能と Linux の DM-Crypt 機能が使用されます。 このソリューションは Azure Key Vault と統合されており、ディスクの暗号化キーと秘密は Key Vault サブスクリプションで制御および管理できます。 またこのソリューションでは、仮想マシン ディスク上のすべてのデータが、Azure Storage での保存時に暗号化されます。

### <a name="virtual-networking"></a>仮想ネットワーク
仮想マシンには、ネットワーク接続が必要です。 その要件に対応するため、Azure では、仮想マシンによる Azure Virtual Network への接続が必要となります。 Azure Virtual Network は、物理的な Azure ネットワーク ファブリック上に構築される論理的な構築物です。 各論理 [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) は、他のすべての Azure Virtual Network から分離されています。 この分離は、他の Microsoft Azure ユーザーによるデプロイ内のネットワーク トラフィックへのアクセスを防ぐ上で役立ちます。

### <a name="patch-updates"></a>修正プログラム
修正プログラムは潜在的な問題を見つけて修正するための基盤となるだけでなく、社内でデプロイする必要のあるソフトウェア更新プログラムの数を削減し、コンプライアンスを監視する機能を強化することにより、ソフトウェア更新管理プロセスを簡略化します。

### <a name="security-policy-management-and-reporting"></a>セキュリティ ポリシーの管理とレポート
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) は、脅威の回避、検出、対応に役立つサービスで、Azure リソースのセキュリティを高度に視覚化して制御できます。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

### <a name="azure-security-center"></a>Azure Security Center
Security Center は、Azure リソースのセキュリティを高度に視覚化し、制御することで脅威を回避、検出し、それに対応することに役立ちます。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

## <a name="identify-and-access-management"></a>ID 管理とアクセス管理

システム、アプリケーション、およびデータのセキュリティ保護は、ID ベースのアクセス制御から始まります。 Microsoft のビジネス製品およびサービスに組み込まれている ID およびアクセス管理機能は、正規ユーザーが必要とするときはいつでもどこでも利用できるようにする一方で、組織および個人情報を不正アクセスから保護します。

### <a name="secure-identity"></a>セキュリティ保護された ID
Microsoft では、複数のセキュリティ上の方法およびテクノロジを製品やサービスに使用して、ID とアクセスを管理します。
-   [多要素認証](https://azure.microsoft.com/services/multi-factor-authentication/)では、複数のメソッドを使用してクラウドのオンプレミス環境にアクセスする必要があります。 幅広い簡単な検証オプションによって強力な認証を提供する一方で、簡単なサインイン プロセスでユーザーの要求に応えます。

-   [Microsoft Authenticator](https://aka.ms/authenticator) は Microsoft Azure Active Directory と Microsoft アカウントの両方で機能するわかりやすい多要素認証機能で、ウェアラブルな承認や指紋ベースの承認がサポートされています。

-   [パスワード ポリシーの適用](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/)によって、長さと複雑さの要件や定期的な変更を強制したり、認証試行の失敗後にアカウントをロックしたりすることで、従来のパスワードのセキュリティが強化されています。

-   [トークン ベースの認証](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/)によって、Azure Active Directory 経由の認証を有効にします。

-   [ロールベースのアクセス制御 (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) によって、ユーザーに割り当てられたロールに基づいたアクセス権の付与が可能になります。これにより、ユーザーの職務実行に必要なアクセス権のみを簡単に付与できるようになります。 RBAC は、組織のビジネス モデルやリスク許容度に応じてカスタマイズできます。

-   [ID 管理 (ハイブリッド ID) の統合](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)により、すべてのリソースに対する認証および承認用に単一のユーザー ID を作成して、内部のデータ センターとクラウド プラットフォームでのユーザーのアクセス権制御を維持することができます。

### <a name="secure-apps-and-data"></a>アプリおよびデータのセキュリティ保護
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) は、ID およびアクセス権を管理する包括的なクラウド ソリューションです。これにより、サイト上やクラウド内のアプリケーション データへのアクセスを保護し、ユーザーおよびグループの管理を簡素化します。 Azure Active Directory はコア ディレクトリ サービス、高度な ID ガバナンス、セキュリティ、アプリケーションへのアクセス管理を組み合わせたもので、開発者はポリシーベースの ID 管理をアプリケーションに簡単に組み込むことができます。 Azure Active Directory を強化するには、Azure Active Directory Basic、Premium P1、Premium P2 の各エディションを使用して有料の機能を追加します。

| 無料/共通機能     | Basic の機能    |Premium P1 の機能 |Premium P2 の機能 | Azure Active Directory Join – Windows 10 のみの関連機能|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [ディレクトリ オブジェクト](https://docs.microsoft.com/azure/active-directory/active-directory-editions#directory-objects)、[ユーザーとグループの管理 (追加/更新/削除)、ユーザーベースのプロビジョニング、デバイスの登録](https://docs.microsoft.com/azure/active-directory/active-directory-editions#usergroup-management-addupdatedelete-user-based-provisioning-device-registration)、[シングル サインオン (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#single-sign-on-sso)、[クラウド ユーザーに対するセルフ サービスのパスワードの変更](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-change-for-cloud-users)、[Connect (Azure Active Directory にオンプレミスのディレクトリを拡張する同期エンジン)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory)、[セキュリティと使用状況に関するレポート](https://docs.microsoft.com/azure/active-directory/active-directory-editions#securityusage-reports)       |   [グループベースのアクセス管理/プロビジョニング](https://docs.microsoft.com/azure/active-directory/active-directory-editions#group-based-access-managementprovisioning)、[クラウド ユーザーに対するセルフ サービスのパスワード リセット](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-reset-for-cloud-users)、[会社のブランド設定 (ログオン ページやアクセス パネルのカスタマイズ)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#company-branding-logon-pagesaccess-panel-customization)、[アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/active-directory-editions#application-proxy)、[SLA 99.9%](https://docs.microsoft.com/azure/active-directory/active-directory-editions#sla-999) |  [セルフ サービスのグループおよびアプリケーション管理/セルフ サービスのアプリケーション追加/動的グループ](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-group)、[セルフ サービスのパスワード リセット/変更/ロック解除とオンプレミスの書き戻し](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-resetchangeunlock-with-on-premises-write-back)、[Multi-Factor Authentication (クラウドおよびオンプレミス (MFA サーバー))](https://docs.microsoft.com/azure/active-directory/active-directory-editions#multi-factor-authentication-cloud-and-on-premises-mfa-server)、[MIM CAL + MIM サーバー](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mim-cal-mim-server)、[Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-editions#cloud-app-discovery)、[Connect Health](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-health)、[グループ アカウントのパスワードの自動ロール オーバー](https://docs.microsoft.com/azure/active-directory/active-directory-editions#automatic-password-rollover-for-group-accounts)|   [ID の保護](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)、[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|  [Azure AD へのデバイスの結合、デスクトップ SSO、 Azure AD 用の Microsoft Passport、管理者による Bitlocker の復旧](https://docs.microsoft.com/azure/active-directory/active-directory-editions#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery)、[MDM 自動登録、セルフサービスの Bitlocker の復旧、Azure AD Join を介した Windows 10 デバイスへのローカル管理者の追加](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mdm-auto-enrollment)|


- [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) は、Azure Active Directory のプレミアム機能で、組織の従業員が使用しているクラウド アプリケーションを特定することができます。

- [Azure Active Directory ID Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) は、 Azure Active Directory の異常検出機能を使用して、リスク イベントと、組織の ID に影響を与える可能性のある潜在的な脆弱性に対して統合ビューを提供するセキュリティ サービスです。

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) によって、ドメイン コントローラーをデプロイせずに、Azure 仮想マシンをドメインに参加させることができます。 ユーザーは会社の Active Directory 資格情報を使用してこれらの仮想マシンにサインインし、各種リソースにシームレスにアクセスできます。

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) は、数億個もの ID をスケールしてモバイルや Web プラットフォーム間で統合できる、コンシューマー向けアプリケーション用の高可用性グローバル ID 管理サービスです。 お客様は、既存のソーシャル メディア アカウントを使用するカスタマイズ可能な操作ですべてのアプリにサインインすることも、新しいスタンドアロンの資格情報を作成することもできます。

- [Azure Active Directory B2B コラボレーション](https://aka.ms/aad-b2b-collaboration)は、自己管理される ID を使用してパートナーが会社のアプリケーションやデータに選択的にアクセスできるようにすることで会社間のリレーションシップをサポートする、安全なパートナー インテグレーション ソリューションです。

- [Azure Active Directory Join](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) によって、クラウド機能を Windows 10 デバイスに拡張し、集中管理することができます。 ユーザーが Azure Active Directory を介して企業や組織のクラウドに接続できるようになり、アプリやリソースへのアクセスが簡略化されます。

- [Azure Active Directory アプリケーション プロキシ](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/)は、オンプレミスでホストされた Web アプリケーションに対する SSO およびセキュリティ保護されたリモート アクセスを提供します。

## <a name="next-steps"></a>次の手順
- [Microsoft Azure セキュリティの概要](https://docs.microsoft.com/azure/security/azure-security-getting-started)

Azure 内でデータとサービスを保護するために使用できる、Azure のサービスと機能

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

Azure リソースのセキュリティの可視性と管理能力を高めることで脅威を防止、検出して対応可能

- [Azure Security Center でのセキュリティ ヘルスの監視](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

ポリシーに対するコンプライアンスを監視するAzure Security Center の監視機能
