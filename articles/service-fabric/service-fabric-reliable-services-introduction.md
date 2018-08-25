---
title: Service Fabric の Reliable Services プログラミング モデルの概要 | Microsoft Docs
description: Service Fabric の Reliable Service プログラミング モデルについて学び、独自のサービスを作成しましょう。
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek; mani-ramaswamy
ms.assetid: 0c88a533-73f8-4ae1-a939-67d17456ac06
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 474cc78a4ceb872742ca7eb10837eeb89dcc1bdb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213182"
---
# <a name="reliable-services-overview"></a>Reliable Services の概要
Azure Service Fabric により、ステートレスおよびステートフルな Reliable Service の作成と管理が簡素化されます。 このトピックの内容は次のとおりです。

* ステートレス サービスおよびステートフル サービスの Reliable Services プログラミング モデル。
* Reliable Service を作成するうえでの選択肢。
* Reliable Service を使用するタイミングと作成方法のいくつかのシナリオと例。

Reliable Services は、Service Fabric で使用できるプログラミング モデルの 1 つです。 もう 1 つは、Reliable Actor プログラミング モデルで、Reliable Services モデル上に仮想アクター プログラミング モデルを提供します。 Reliable Actors プログラミング モデルの詳細については、「 [Service Fabric 高信頼アクターの概要](service-fabric-reliable-actors-introduction.md)」を参照してください。

Service Fabric では、プロビジョニングとデプロイからアップグレードと削除までのサービスの有効期間が [Service Fabric のアプリケーション管理](service-fabric-deploy-remove-applications.md)で管理されます。

## <a name="what-are-reliable-services"></a>Reliable Services について
Reliable Services は、重要な機能をアプリケーションに組み込むためのシンプルかつ強力な最上位レベルのプログラミング モデルを提供します。 Reliable Services プログラミング モデルを使用すると、以下のことを実現できます。

* 残りの Service Fabric プログラミング API の使用。 [ゲスト実行可能ファイル](service-fabric-guest-executables-introduction.md)としてモデル化された Service Fabric Services とは異なり、Reliable Services は残りの Service Fabric API を直接使用します。 これにより、サービスで次のことが可能になります。
  * システムのクエリを実行する
  * クラスター内のエンティティに関する正常性レポート
  * 構成とコードの変更に関する通知を受け取る
  * 他のサービスを検索し通信する
  * (省略可能) [Reliable Collection](service-fabric-reliable-services-reliable-collections.md) を使用する
  * .. その他の多くの機能にアクセスする。これらの機能はすべて、複数のプログラミング言語の非常に優れたプログラミング モデルから提供されます。
* 使い慣れたプログラミング モデルに似た、独自のコードを実行するためのシンプルなモデル。 コードには、適切に定義されたエントリ ポイントと管理が簡単なライフサイクルが含まれます。
* プラグ可能な通信モデル。 HTTP と [Web API](service-fabric-reliable-services-communication-webapi.md)、WebSocket、カスタム TCP プロトコルなど、あらゆるトランスポートを選んで使用できます。 Reliable Services には、すぐに使用できる優れたオプションも用意されていますが、独自のオプションを指定することもできます。
* ステートフル サービスの場合、Reliable Services プログラミング モデルを使用すると、[Reliable Collection](service-fabric-reliable-services-reliable-collections.md) によって状態がサービス内に一貫して確実に格納されます。 Reliable Collection は、C# コレクションを使用したことがあるユーザーには馴染みのある可用性が高く信頼できる一連のコレクション クラスです。 これまでは、サービスにおいて確かな方法で状態を管理するには、外部システムが必要でした。 Reliable Collection を使用するとユーザーのコンピューターの隣に状態を格納できるようになると同時に、高可用性の外部ストアと同じレベルの高い可用性と確実性を実現できます。 このモデルでは、機能に必要な計算と状態を併置できるため、待機時間も短縮します。

Reliable Services の概要については、Microsoft Virtual Academy の動画 をご覧ください。<center>
<a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=HhD9566yC_4106218965">
<img src="./media/service-fabric-reliable-services-introduction/ReliableServicesVid.png" WIDTH="360" HEIGHT="244" />
</a>
</center>

## <a name="what-makes-reliable-services-different"></a>Reliable Services の特長
Service Fabric の Reliable Service は、以前に作成されたことがあるサービスとは異なります。 Service Fabric は信頼性、可用性、整合性、およびスケーラビリティを提供します。

* **信頼性** - コンピューターがダウンしたりネットワークの問題が発生したりした場合や、サービスそのものでエラーが発生したりクラッシュしたり失敗したりした場合でも、サービスは起動した状態を保ちます。 ステートフル サービスの場合、ネットワークまたはその他の障害が発生した場合でも、状態は保持されます。
* **可用性** - サービスはアクセス可能で、高い応答性があります。 Service Fabric では、実行中のコピーを任意の数保持します。
* **スケーラビリティ** - 特定のハードウェアからサービスを分離し、必要に応じて、ハードウェア リソースまたはその他のリソースを追加または削除することで拡張または縮小できます。 サービスは、サービスをスケーリングしたり部分的な障害に対応したりできるように簡単に分割できます (特にステートフル サービスの場合)。 サービスは、コードを使用して動的に作成および削除できるので、必要に応じて多くのインスタンスをスピンアップしたり、顧客の要求に応答することができます。 最後に、Service Fabric では、サービスが軽量になる傾向があります。 Service Fabric では、数千単位のサービスを、すべての OS インスタンスやプロセスを単一のサービス インスタンスに使用するのではなく、1 つのプロセス内でプロビジョニングできます。
* **整合性** - このサービスに格納されているすべての情報の一貫性が保証されます。 これは、サービス内の複数の Reliable Collection 間であっても当てはまります。 トランザクションによるアトミックな方法で、サービス内のコレクションに対して変更を行うことができます。

## <a name="service-lifecycle"></a>サービスのライフサイクル
サービスがステートフルかステートレスかにかかわらず、Reliable Services は、コードをすばやく追加して使用開始できるというシンプルなライフサイクルを提供します。  サービスの稼動を開始するために実装する必要があるメソッドは 1 つまたは 2 つしかありません。

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** : このメソッドでは、サービスで使用される通信スタックが定義されます。 この通信スタック ([Web API](service-fabric-reliable-services-communication-webapi.md)など) によって、サービスをリッスンするエンドポイント (クライアントによるサービスのアクセス方法) が定義されます。 また、表示されるメッセージがサービス コードの残りの部分とやり取りする方法も定義されます。
* **RunAsync** - このメソッドでは、サービスがそのビジネス ロジックを実行し、サービスの有効期間中に実行する必要があるすべてのバックグラウンド タスクを開始します。 ここで提供されているキャンセル トークンは、そのサービス実行を停止する信号となります。 たとえば、サービスで Reliable Queue からメッセージを取得して処理する必要がある場合は、ここでサービスが実行されます。

Reliable Services をはじめて学習している場合は、読み進めてください。 Reliable Services のライフサイクルの詳細なチュートリアルを探している場合は、[この記事](service-fabric-reliable-services-lifecycle.md)に進んでください。

## <a name="example-services"></a>サービスの例
このプログラミング モデルを理解した上で、2 つのサービスを見ながら、これらがどのように機能するかを説明します。

### <a name="stateless-reliable-services"></a>ステートレス Reliable Service
ステートレス サービスとは、複数の呼び出しにまたがるサービス内で状態が維持されないサービスです。 表示される任意の状態は、完全に破棄可能で、同期、レプリケーション、永続化、または高可用性を必要としません。

たとえば、メモリのない電卓が、同時に処理する必要があるすべての項と演算を受け取る場合を考えてみてください。

この場合は、サービスの `RunAsync()` (C#) または `runAsync()` (Java) を空にしておくことができます。これは、サービスが実行する必要のあるバック グラウンドのタスク処理がないためです。 電卓サービスを作成すると、`ICommunicationListener` (C#) または `CommunicationListener` (Java) (たとえば [Web API](service-fabric-reliable-services-communication-webapi.md)) が返され、このメソッドが任意のポートでリッスン エンドポイントを開始します。 このリッスン エンドポイントは、電卓のパブリック API を定義するさまざまな計算メソッド (例: "Add (n1, n2)") に接続します。

クライアントから呼び出しが行われた場合は、適切なメソッドが呼び出され、電卓サービスは提供されたデータに対する演算を実行して、結果を返します。 この処理では、状態はまったく保存されません。

内部の状態がまったく保存されないため、この電卓の例はシンプルです。 ただし、大半のサービスは実際にはステートレスではありません。 状態が外部ストアに格納されているためです (たとえば、セッション状態をバッキング ストアまたはキャッシュに保持している任意の Web アプリはステートレスではありません)。

Service Fabric でのステートレス サービスの使用方法を示す一般的な例は、Web アプリケーションの公開 API を公開するフロントエンド サービスです。 フロントエンド サービスは、ステートフル サービスと通信してユーザーの要求を完了します。 この場合、クライアントからの呼び出しは、ポート 80 など、ステートレス サービスがリッスンしている既知のポートに送られます。 このステートレス サービスは呼び出しを受信し、その呼び出しが信頼できる利用者からのものかどうかと、どのサービスに向けて送信されたのかを判断します。  次にステートレス サービスは、その呼び出しをステートフル サービスの正しいパーティションに転送して、応答を待ちます。 ステートレス サービスは、応答を受信すると、元のクライアントに応答します。 このようなサービスの例は、[C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/Actors/VisualObjectActor/VisualObjectWebService) のサンプルにあります。 これは、サンプルに含まれているこのパターンの 1 例にすぎません。その他のサンプルには他の例も含まれています。

### <a name="stateful-reliable-services"></a>ステートフル Reliable Service
ステートフル サービスは、サービス提供するために、状態の一部の整合性を維持して永続化しておかなければならないサービスです。 受け取る最新情報に基づいて値の移動平均を常に計算するサービスを考えてみてください。 このサービスを提供するには、最新の平均と、処理する必要がある一連の現在の受信要求が必要になります。 情報を取得、処理して、外部ストア (現時点では Azure BLOB やテーブル ストアなど) に格納するサービスはどれもステートフル サービスです。 その状態は外部の状態ストアに保持されます。

現在のほとんどのサービスでは状態が外部ストアに格納されますが、その理由は、その状態の信頼性、可用性、スケーラビリティ、および整合性を提供できるのが外部ストアであるためです。 Service Fabric では、サービスが外部に状態を格納する必要はありません。 サービス コードとサービス状態の両方の要件に Service Fabric が対応します。

たとえば、イメージを処理するサービスを記述するとします。 これを行うには、サービスはイメージを取り込んで、そのイメージに対して一連の変換を実行します。 このサービスは、`ConvertImage(Image i, IList<Conversion> conversions)` のような API を公開する通信リスナー (WebAPI と仮定しましょう) を返します。 サービスは要求を受信すると、`IReliableQueue` に格納して、要求を追跡できるように、クライアントになんらかの ID を返します。

このサービスでは、`RunAsync()` がもっと複雑になる可能性があります。 サービスの `RunAsync()` の中にループがあり、`IReliableQueue` から要求を取り出して、要求された変換を実行します。 結果は `IReliableDictionary` に格納されるので、クライアントは戻ったときに、変換されたイメージを取り出すことができます。 何らかの障害が発生した場合でもイメージが失われないように、この Reliable Service では情報がキューから取得され、変換された後にすべての結果が 1 つのトランザクションに格納されます。 この場合、キューからメッセージが削除され、変換が完了したときにだけ結果が結果ディクショナリに格納されます。 代わりに、サービスが、イメージをキューから取り出して、すぐにリモートのストアに格納することもできます。 こうすると、サービスが管理しなければならない状態の量は減りますが、リモート ストアを管理するために必要なメタデータをサービスが保持しなければならないため複雑さが増します。 いずれの方法でも、途中で何かが失敗した場合は、要求は処理待ちの状態でキューに残ります。

このサービスの注目すべき点は、これらの処理が通常の .NET サービスに似ているということです。 唯一異なる点は、使用されているデータ構造 (`IReliableQueue` および `IReliableDictionary`) が Service Fabric によって提供されているため、信頼性、可用性、および整合性が高いということです。

## <a name="when-to-use-reliable-services-apis"></a>Reliable Services API を使用するタイミング
ご使用のアプリケーション サービスのニーズが次のいずれかに当てはまる場合は、Reliable Services API を検討してください。

* サービスのコード (および必要に応じて状態) の可用性と信頼性を高めたい。
* 複数の状態にまたがるトランザクション上の保証が必要 (たとえば、注文や注文品目)。
* アプリケーションの状態を、Reliable Dictionary と Reliable Queue として自然にモデル化することができる。
* アプリケーション コードまたは状態で、待機時間が短い読み取りと書き込みの可用性を高める必要がある。
* アプリケーションでは、1 つ以上の Reliable Collection に対して、トランザクション操作の同時実行または粒度を制御する必要がある。
* サービスの通信を管理するか、パーティション構成を制御したい。
* コードにはフリー スレッドの実行時環境が必要である。
* アプリケーションで、実行時に Reliable Dictionary または Reliable Queue またはサービス全体を動的に作成または破棄する必要がある。
* サービスの状態について、Service Fabric が提供するバックアップ機能および復元機能をプログラムによって制御する必要がある。
* アプリケーションで、複数の状態に関する変更履歴を維持する必要がある。
* カスタム状態プロバイダーを開発するか、サード パーティが開発したカスタム状態プロバイダーを使用したい。

## <a name="next-steps"></a>次の手順
* [Reliable Service の概要](service-fabric-reliable-services-quick-start.md)
* [Reliable Collection](service-fabric-reliable-services-reliable-collections.md)
* [Reliable Actors プログラミング モデル](service-fabric-reliable-actors-introduction.md)
