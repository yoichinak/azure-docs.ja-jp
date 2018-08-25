---
title: Azure Service Fabric mesh | Microsoft Docs
description: Azure Service Fabric mesh について説明します。 Service Fabric mesh では、アプリケーションのインフラストラクチャに関するニーズについて心配することなく、アプリケーションをデプロイしてスケーリングできます。
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 06/27/2018
ms.topic: overview
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 65a9b1afcc0e1e6d4fcbb60a38ab0764e6fe2f18
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226446"
---
# <a name="what-is-service-fabric-mesh"></a>Service Fabric mesh とは

Azure Service Fabric mesh は、仮想マシン、ストレージ、ネットワークを管理することなく開発者がマイクロサービス アプリケーションをデプロイできるフル マネージド サービスです。 Service Fabric mesh でホストされたアプリケーションは、その基盤となるインフラストラクチャについて心配しなくとも、実行され、スケーリングされます。  Service Fabric mesh は数千台のマシンのクラスターで構成されます。  すべてのクラスター操作は開発者から見えなくなっています。 コードをアップロードして、必要なリソース、可用性の要件、リソースの制限を指定するだけかまいません。  Service Fabric mesh では、アプリケーションのデプロイによって要求されるインフラストラクチャが自動的に割り当てられ、インフラストラクチャの障害も処理されます。これにより、アプリケーションの高可用性が確保されます。 アプリケーションの正常性と応答性さえ管理すればよく、インフラストラクチャを管理する必要がありません。  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

この記事では、Service Fabric mesh の主なベネフィットについて概説します。

## <a name="great-developer-experience"></a>優れた開発者エクスペリエンス

Service Fabric mesh では、コンテナーで実行できる任意のプログラミング言語またはフレームワークがサポートされます。 Visual Studio 2017 および Visual Studio Code ツール サポートによって、.NET アプリケーション用および .NET Core アプリケーション用の強力な編集およびデバッグ エクスペリエンスが提供されます。 

Service Fabric mesh では、以下が可能です。

- 現在のアプリケーションを最新化して大規模に実行するために、既存のアプリケーションをコンテナーに "リフト アンド シフト" する。 
- Azure で新しいマイクロサービス アプリケーションを構築して大規模にデプロイする。  コンテナーで実行されている他の Azure サービスや既存のアプリケーションと統合します。 各マイクロサービスは、CPU コアやメモリ、ディスク領域などについて定義されたリソース ガバナンス ポリシーがあり、ネットワークから分離された安全なアプリケーションの一部です。
- 既存のアプリケーションに変更を加えることなく、それらのアプリケーションと統合して拡張する。 独自の仮想ネットワークを使用して、既存のアプリケーションを新しいアプリケーションに接続します。  
- Service Fabric mesh に移行して、既存の Cloud Services アプリケーションを最新化する。  

## <a name="simple-operational-lifecycle"></a>シンプルな運用ライフサイクル

運用環境におけるアプリケーションの実行 (アプリケーションのアップグレードやバージョン管理など)、アプリケーションの監視、デバッグを容易に管理できます。 これらのアプリケーションは、独自のネットワーク内に分離された単一または複数のマイクロサービスで構成できます。 デプロイ、配置、フェールオーバーにかかる時間が短く、アプリケーションは効率的に実行されます。

Service Fabric mesh では、以下が可能です。

- インフラストラクチャを明示的にプロビジョニングして管理することなく、アプリケーションのデプロイと管理を行う。  Service Fabric mesh では、基盤となるインフラストラクチャのプロビジョニング、アップグレード、パッチ適用、メンテナンスが自動で行われます。
- アプリケーションを簡単にパッケージ化してデプロイできる統合されたツールを使用して、継続的インテグレーションを設定する。
- Azure Resource Manager リソースの機能 (監査証跡や[ロールベースのアクセス制御 (RBAC)](/azure/role-based-access-control/overview) など) をすべて利用する。これは、Azure で SF mesh サービスにデプロイするすべてのリソースが Azure Resource Manager リソースであるためです。 
- [Azure portal](https://portal.azure.com)、Resource Manager テンプレート、または Azure CLI/PowerShell ライブラリを使用して、リソースのデプロイと管理を行う。
- [Application Insights](/azure/application-insights/) (または好きなツール) を使用して操作の監視とアラートを設定し、プラットフォームから操作と診断のトレースを収集する。 
- [Application Insights](/azure/application-insights/) または好きなツールを使用して、アプリケーション モデルから出力されたアプリケーション診断情報にアクセスする。
- アプリケーション定義でサービスの自動スケーリング規則を指定して、リソース使用率を最適化する。  (近日対応予定)
- アプリケーションについてネットワークの分離とセキュリティ境界を作成する。これは、Hyper-V コンテナーと組み合わされた場合の強力な機能です。 サービスごとの複数の IP とアプリケーションごとに分離された仮想ネットワークを使用して、サービスとの間のネットワーク トラフィックを分離します。  (近日対応予定) 


## <a name="mission-critical-platform-capabilities"></a>ミッション クリティカルなプラットフォーム機能

Service Fabric mesh では、[Azure 可用性ゾーン](/azure/availability-zones/az-overview)/地政学的リージョンの境界にまたがる一連のクラスターが作成されます。 アプリケーションは、スケールやハードウェア要件、持続性要件、セキュリティ ポリシーなど、意図のセットで説明されます。  アプリケーションのデプロイ時、Service Fabric mesh によって最適な実行場所が検出されます。

Service Fabric mesh では、以下が可能です。

- 高可用性、スケールイン/スケールアウト、検出可能性、オーケストレーション、メッセージ ルーティング、信頼できるメッセージング、ダウンタイムのないアップグレード、セキュリティ/シークレット管理、ディザスター リカバリー、状態管理、構成管理、分散トランザクションを活用する。
- アプリケーションの作成時に複数の中からアプリケーション モデルを選択する。
- Swagger で生成された言語固有のバインドを使用して、REST エンドポイントを通じて公開されているプラットフォーム機能を使用する。
- 地域の信頼性を高めるために、[可用性ゾーン](/azure/availability-zones/az-overview)と複数のリージョンをまたいでアプリケーションをデプロイする。
- Azure によって提供されるすべてのセキュリティ機能とコンプライアンス機能を使用する。

## <a name="next-steps"></a>次の手順

Visual Studio でのサンプル プロジェクトのデプロイに必要な手順はわずか数ステップです。 詳細については、[ASP.NET Core Web サイトの作成](service-fabric-mesh-quickstart-dotnet-core.md)に関する記事を参照してください。 


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
