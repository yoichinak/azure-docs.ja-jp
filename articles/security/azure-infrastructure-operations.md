---
title: Azure 実稼働環境での運用と管理
description: この記事では、Azure 実稼働環境のネットワークの管理と運用に関する一般的な説明を行います。
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 21ae81f1d8423a9d05208ec6d8c4f31d909d2f9f
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173161"
---
# <a name="azure-production-operations-and-management"></a>Azure 実稼働環境での運用と管理    
Azure 実稼働環境のネットワークの管理と運用は、Azure と Azure SQL Database の運用チーム間での調整による作業です。 チームは、環境内でシステムとアプリケーションの複数のパフォーマンス監視ツールを使用します。 さらに、適切なツールを使用して、ネットワーク デバイス、サーバー、サービス、およびアプリケーション プロセスを監視します。

次に示すアクションをはじめ、Azure 環境で実行されるサービスの安全な実行を保証するために、運用チームは、複数のレベルの監視、ログ記録、およびレポート作成を実装します。

- 基本的に、Microsoft Monitoring Agent (MMA) では、ファブリック コントローラー (FC) やルート オペレーティング システム (OS) を含め多数の場所から監視と診断のログ情報を収集して、ログ ファイルに書き込みます。 エージェントは、最終的に、情報を集約したサブセットを、事前構成された Azure Storage アカウントにプッシュします。 さらに、独立した監視および診断サービスが、さまざまな監視と診断のログ データを読み取り、その情報をまとめます。 監視および診断サービスは、統合されたログに情報を書き込みます。 Azure では、Azure 監視システムの拡張機能である、カスタム ビルドされた Azure セキュリティ監視を使用します。 ASM は、プラットフォームのさまざまなポイントからセキュリティ関連イベントの観察、分析、およびレポートを行うコンポーネントです。

- Azure SQL Database の Windows Fabric プラットフォームは、Azure SQL Database の管理、デプロイ、開発、および運用の監督サービスを提供します。 このプラットフォームでは、分散型の複数の手順によるデプロイ サービス、正常性の監視、自動修復、およびサービス バージョンのコンプライアンスを提供します。 また、以下のようなサービスを備えています。

   - 信頼性の高い開発環境によるサービス モデリング機能 (データセンター クラスターは高価かつ希少です)。
   - サービスのブートストラップとメンテナンスのための、1 回のクリックによるデプロイとアップグレードのワークフロー。
   - 自動化された修復ワークフローによって自己修復を可能にする正常性レポート機能。
   - 分散システムの複数のノードにわたる、リアルタイムの監視、警告、およびデバッグ機能。
   - 分散した根本原因の分析とサービスの分析情報に対する運用データとメトリックの、集中型のコレクション機能。
   - デプロイ、変更管理、および監視の運用ツール。
   - 継続的に実行され、リアルタイムで監視を行う Azure SQL Database の Windows Fabric プラットフォームとウォッチドッグ スクリプト。

異常が発生した場合は、Azure インシデント トリアージ チームが準拠するインシデント応答プロセスがアクティブ化されます。 適切な Azure サポート担当者が、事案に対応するよう通知を受けます。 問題の追跡と解決策がドキュメント化され、一元化されたチケット制のシステムで管理されます。 システム稼働時間のメトリックは、秘密保持契約書 (NDA) および要請の下で利用可能です。

## <a name="corporate-network-and-multi-factor-access-to-production"></a>企業ネットワークと実稼働環境への多要素アクセス
企業ネットワークのユーザー ベースには、Azure のサポート担当者が含まれます。 企業ネットワークは、企業内部の機能をサポートし、Azure カスタマー サポートに使用される内部アプリケーションへのアクセスを含みます。 企業ネットワークは、Azure 実稼働環境のネットワークから論理および物理の両面で分離されています。 Azure 担当者は、Azure ワークステーションとノート パソコンを使用して、企業ネットワークにアクセスします。 すべてのユーザーは、企業ネットワーク リソースにアクセスするために、ユーザー名とパスワードを含む Azure Active Directory (Azure AD) アカウントを持っている必要があります。 企業ネットワークのアクセスでは、すべての Microsoft 担当者、契約者、ベンダーに発行され、Microsoft Information Technology によって管理される Azure AD アカウントを使用します。 一意のユーザー ID が、Microsoft での従業員ステータスに基づいて、担当者を区別します。

内部の Azure アプリケーションへのアクセスは、Active Directory フェデレーション サービス (AD FS) による認証を経由して、制御されています。 AD FS は、安全なトークンとユーザー要求を適用することで企業ネットワーク ユーザーの認証を提供している、Microsoft Information Technology でホストされているサービスです。 AD FS を利用することで、内部の Azure アプリケーションは、Microsoft の企業の Active Directory ドメインに対してユーザーを認証できます。 企業ネットワーク環境から実稼働環境のネットワークにアクセスするには、ユーザーは多要素認証を使用して、認証する必要があります。

## <a name="next-steps"></a>次の手順
Microsoft が提供する Azure アーキテクチャの保護の詳細については、以下を参照してください。

- [Azure ファシリティ、プレミス、および物理的なセキュリティ](azure-physical-security.md)
- [Azure インフラストラクチャの可用性](azure-infrastructure-availability.md)
- [Azure 情報システムのコンポーネントと境界](azure-infrastructure-components.md)
- [Azure ネットワーク アーキテクチャ](azure-infrastructure-network.md)
- [Azure 実稼働環境のネットワーク](azure-production-network.md)
- [Azure SQL Database のセキュリティ機能](azure-infrastructure-sql.md)
- [Azure インフラストラクチャの監視](azure-infrastructure-monitoring.md)
- [Azure インフラストラクチャの整合性](azure-infrastructure-integrity.md)
- [Azure での顧客データの保護](azure-protection-of-customer-data.md)
