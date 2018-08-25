---
title: Azure で運用可能なセキュリティのチェックリスト | Microsoft Docs
description: この記事では、Azure で運用可能なセキュリティのチェックリスト一式を提供します。
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 481fcb869004a6fca83fdf19c32c41010057de32
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008197"
---
# <a name="azure-operational-security-checklist"></a>Azure で運用可能なセキュリティのチェックリスト
Azure でのアプリケーションのデプロイは、すばやく簡単に、高いコスト効率で行うことができます。 運用環境にクラウド アプリケーションをデプロイする前に、検討する必須および推奨の運用上のセキュリティ アクションの一覧に照らしてアプリケーションを評価する際に役立つチェックリストがあると便利です。

## <a name="introduction"></a>はじめに

Azure では、アプリケーションのデプロイに使用できるインフラストラクチャ サービスのスイートが提供されます。 Azure で運用可能なセキュリティとは、ユーザーのデータ、アプリケーション、および Microsoft Azure にあるその他の資産を保護するために使用できる、サービス、コントロール、機能を指します。

-   クラウド プラットフォームから最大限のメリットを得るには、Azure サービスを活用し、チェックリストに従うことをお勧めします。
-   起動の前に時間とリソースを費やしてアプリケーションの運用の準備状況を評価している組織は、それをしない組織よりも満足度がはるかに高くなっています。 この作業を実行するときに、チェックリストは、アプリケーションが一貫して総合的に評価されることを保証する非常に重要なメカニズムとなります。
-   運用アセスメントのレベルは、組織のクラウドの成熟度レベルと、アプリケーションの開発フェーズ、可用性のニーズ、データの機密要件によって異なります。

## <a name="checklist"></a>チェック リスト

このチェックリストは、Azure に高度なエンタープライズ アプリケーションをデプロイするときにさまざまな運用上のセキュリティについて検討する企業を支援するためのものです。 組織がセキュリティで保護されたクラウド移行と運用の戦略を構築する際にも使用できます。

|チェックリストのカテゴリ| 説明|
| ------------ | -------- |
| [<br>セキュリティ ロールとアクセス制御](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)|<ul><li>[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) を使用して、特定のスコープ内のユーザー、グループ、アプリケーションにアクセス許可を割り当てるために使用するユーザーの仕様を提供します。</li></ul> |
| [<br>データの収集と保存](https://docs.microsoft.com/azure/storage/storage-security-guide)|<ul><li>管理プレーンのセキュリティを使用して、[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) でストレージ アカウントを保護します。</li><li>データ プレーンのセキュリティを使用して、[Shared Access Signature (SAS)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) と Stored Access Policy でデータへのアクセスを保護します。</li><li>転送レベルの暗号化の使用 - HTTPS と、[Azure ファイル共有](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files)用の [SMB (サーバー メッセージ ブロック プロトコル) 3.0](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) で使用される暗号化を使用します。</li><li>[クライアント側の暗号化](https://docs.microsoft.com/azure/storage/storage-client-side-encryption)を使用して、暗号化キーを単独で制御する必要がある場合にストレージ アカウントに送信するデータを保護します。 </li><li>[Storage Service Encryption (SSE)](https://docs.microsoft.com/azure/storage/storage-service-encryption) を使用して Azure Storage 内のデータを自動的に暗号化し、[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) を使用してOS とデータ ディスクの仮想マシン ディスク ファイルを暗号化します。</li><li>Azure [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics) を使用して承認の種類を監視します。Blob Storage の場合と同様に、ユーザーが Shared Access Signature またはストレージ アカウント キーを使用していたかどうかを確認できます。</li><li>[クロス オリジン リソース共有 (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) を使用して、別のドメインからストレージ リソースにアクセスします。</li></ul> |
|[<br>セキュリティ ポリシーと推奨事項](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)|<ul><li>[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-install-endpoint-protection) を使用して、エンドポイント ソリューションをデプロイします。</li><li>[Web アプリケーション ファイアウォール (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) を追加して Web アプリケーションを保護します。</li><li>  Microsoft パートナーの[次世代ファイアウォール (NGFW)](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall) を使用して、セキュリティ保護を強化します。 </li><li>Azure サブスクリプションのセキュリティ連絡先の詳細を適用します。[Microsoft Security Response Centre (MSRC)](https://technet.microsoft.com/security/dn528958.aspx) は、顧客データが違法または承認されていないパーティによってアクセスされたことを検出すると、ユーザーに連絡します。</li></ul> |
| [<br>ID およびアクセス管理](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices)|<ul><li>[Azure AD を使用して、オンプレミスのディレクトリとクラウドのディレクトリを同期します](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)。</li><li>[シングル サインオン](https://azure.microsoft.com/resources/videos/overview-of-single-sign-on/)を使用して、ユーザーは、Azure AD 内の組織アカウントに基づいて SaaS アプリケーションにアクセスできます。</li><li>[パスワード リセット登録アクティビティ](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights) レポートを使用して、登録しているユーザーを監視します。</li><li>ユーザーの[多要素認証 (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) を有効にします。</li><li>開発者は、[Microsoft セキュリティ開発ライフサイクル (Security Development Lifecycle: SDL)](https://www.microsoft.com/download/details.aspx?id=12379) などのセキュリティで保護された ID 機能をアプリに使用します。</li><li>Azure AD Premium の異常レポートと [Azure AD Identity Protection 機能](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)を使用して、不審なアクティビティを能動的に監視します。</li></ul> |
|[<br>継続的なセキュリティの監視](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)|<ul><li>Malware Assessment ソリューションである [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) を使用して、インフラストラクチャ内のマルウェア対策の状態についてレポートします。</li><li>[更新プログラムのアセスメント](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)を使用して、潜在的なセキュリティの問題に全体でどの程度さらされているか、これらの更新プログラムが環境にとって重要かどうか、その重要度はどの程度かを判断します。</li><li>[ID とアクセス](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources)はユーザーの概要を提供します。 </li><ul><li>ユーザー ID の状態、</li><li>ログオン試行に失敗した回数、</li><li>    これらの試行中に使用されたユーザーのアカウント、ロックアウトされたアカウント</li> <li>パスワードが変更またはリセットされたアカウント </li><li>現在ログインしているアカウントの数。</li></ul></ul> |
| [<br>Azure Security Center の検出機能](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)|<ul><li>[検出機能](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)使用して、Microsoft Azure のリソースを対象とするアクティブな脅威を識別します。</li><li>グローバルな脅威インテリジェンスを活用して既知の有害アクターを探す[統合された脅威インテリジェンス](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/19/get-threat-intelligence-reports-with-azure-security-center/)を使用します。情報源としては、Microsoft 製品とサービス、[Microsoft Digital Crimes Unit (DCU)](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)、[Microsoft Security Response Center (MSRC)](https://docs.microsoft.com/azure/security/azure-security-response-center)、外部フィードがあります。</li><li>既知のパターンを適用して悪質なビヘイビアーを検出する[行動分析](https://blogs.technet.microsoft.com/enterprisemobility/2016/06/30/ata-behavior-analysis-monitoring/)を使用します。 </li><li>統計プロファイルを使用して過去のベースラインを構築する[異常検出](https://msdn.microsoft.com/library/azure/dn913096.aspx)を使用します。</li></ul> |
| [<br>開発者の操作 (DevOps)](https://docs.microsoft.com/azure/architecture/checklist/dev-ops)|<ul><li>[コードとしてのインフラストラクチャ (IaC)](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) は、ネットワークや仮想マシンの作成と切断の自動化と妥当性確認を有効にして、セキュリティで保護された、安定したアプリケーション ホスティング プラットフォームの提供を支援するプラクティスです。</li><li>[継続的インテグレーションとデプロイ](https://www.visualstudio.com/docs/build/overview)は、実行中のコードのマージとテストを推進します。これにより、障害を早期に検出できるようになります。 </li><li>[Release Management](https://msdn.microsoft.com/library/vs/alm/release/overview) は、パイプラインの各段階で自動化されたデプロイを管理します。</li><li>アプリケーションの正常性と顧客の使用状況に関する、運用環境を含む実行中のアプリケーションの[アプリケーション パフォーマンス監視](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/)は、組織が仮説を形成し、戦略を迅速に検証または誤りを証明するのに役立ちます。</li><li>[ロード テストと自動スケール](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)を使用してアプリ内のパフォーマンスの問題を検出して、デプロイの品質を改善し、ビジネス ニーズに対応するためにアプリが常に稼働するか使用可能であるようにすることができます。</li></ul> |


## <a name="conclusion"></a>まとめ
多くの組織は、Azure にクラウド アプリケーションをうまくデプロイし、運用してきました。 提供しているチェックリストでは、デプロイの成功とストレスのない運用の可能性を高めるのに役立ついくつかの基本的なチェックリストを強調しています。 Azure での既存および新規のアプリケーション デプロイでは、これらの運用と戦略を検討することを強くお勧めします。

## <a name="next-steps"></a>次の手順
このドキュメントでは、OMS のセキュリティと監査ソリューションについて紹介しました。 OMS セキュリティの詳細については、次の記事を参照してください。

- [Operations Management Suite (OMS) の概要](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)。
- [設計および運用面のセキュリティ](https://www.microsoft.com/trustcenter/security/designopsecurity)。
- [Azure Security Center 計画および運用](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)。
