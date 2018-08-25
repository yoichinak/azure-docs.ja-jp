---
title: Azure Service Fabric CLI- sfctl service | Microsoft Docs
description: Service Fabric CLI sfctl service のコマンドについて説明します。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 84c2faaf137e19d78e7e17527feb50baebf8041b
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494576"
---
# <a name="sfctl-service"></a>sfctl service
サービス、サービスの種類、およびサービス パッケージを作成、削除、管理します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| app-name | サービスの Service Fabric アプリケーションの名前を取得します。 |
| code-package-list | Service Fabric ノードにデプロイされているコード パッケージの一覧を取得します。 |
| create | 指定された Service Fabric サービスを作成します。 |
| 削除 | 既存の Service Fabric サービスを削除します。 |
| deployed-type | Service Fabric クラスター内のノードにデプロイされたアプリケーションの指定されたサービスの種類に関する情報を取得します。 |
| deployed-type-list | Service Fabric クラスター内のノードにデプロイされたアプリケーションからサービスの種類に関する情報を含む一覧を取得します。 |
| description | 既存の Service Fabric サービスの説明を取得します。 |
| get-container-logs | Service Fabric ノードに展開されているコンテナーのコンテナー ログを取得します。 |
| health | 指定した Service Fabric サービスの正常性を取得します。 |
| info | Service Fabric アプリケーションに属する特定のサービスに関する情報を取得します。 |
| list | アプリケーション ID で指定されたアプリケーションに属するすべてのサービスに関する情報を取得します。 |
| manifest | サービスの種類を記述するマニフェストを取得します。 |
| package-deploy | 指定されたサービス マニフェストに関連付けられているパッケージを、指定されたノードのイメージ キャッシュにダウンロードします。 |
| package-health | Service Fabric ノードとアプリケーションに対してデプロイされた特定のアプリケーションのサービス パッケージの正常性に関する情報を取得します。 |
| package-info | 指定された名前と完全に一致する Service Fabric ノードにデプロイされているサービス パッケージの一覧を取得します。 |
| package-list | Service Fabric ノードにデプロイされているサービス パッケージの一覧を取得します。 |
| recover | 現在クォーラム損失の状態に陥っている指定されたサービスの復旧を試みる必要があることを Service Fabric クラスターに示します。 |
| report-health | Service Fabric サービスの正常性レポートを送信します。 |
| resolve | Service Fabric パーティションを解決します。 |
| type-list | Service Fabric クラスターでプロビジョニングされたアプリケーションの種類でサポートされているサービスの種類に関する情報を含む一覧を取得します。 |
| update | 特定の更新プログラムの説明を使用して、指定したサービスを更新します。 |

## <a name="sfctl-service-app-name"></a>sfctl service app-name
サービスの Service Fabric アプリケーションの名前を取得します。

指定されたサービスのアプリケーションの名前を取得します。 指定されたサービス ID を持つサービスが存在しない場合は、404 FABRIC_E_SERVICE_DOES_NOT_EXIST エラーが返されます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --service-id [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-code-package-list"></a>sfctl service code-package-list
Service Fabric ノードにデプロイされているコード パッケージの一覧を取得します。

特定のアプリケーションの Service Fabric ノードにデプロイされているコード パッケージの一覧を取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id [必須] | アプリケーションの ID。 これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --node-name    [必須] | ノード名。 |
| --code-package-name | Service Fabric クラスターでアプリケーションの種類の一部として登録されているサービス マニフェストで指定されているコード パッケージの名前。 |
| --service-manifest-name | Service Fabric クラスターでアプリケーションの種類の一部として登録されているサービス マニフェストの名前。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-create"></a>sfctl service create
指定された Service Fabric サービスを作成します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --app-id       [必須] | アプリケーションの ID。 これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --name         [必須] | サービスの名前。 これは、アプリケーション ID の子である必要があります。これは、`fabric\:` URI を含む完全な名前です。 たとえば、サービス `fabric\:/A/B` はアプリケーション `fabric\:/A` の子です。 |
| --service-type [必須] | サービスの種類の名前。 |
| --activation-mode | サービス パッケージのアクティブ化モード。 |
| --constraints | 文字列としての配置の制約。 配置の制約はノード プロパティ上のブール式で、サービスの要件に基づいて特定のノードにサービスを制限することができます。 たとえば、NodeType が blue のノードにサービスを配置するには、"NodeColor == blue" を指定します。 |
| --correlated-service | 関連付けるターゲット サービスの名前。 |
| --correlation | 配置アフィニティを使用して既存のサービスにサービスを関連付けます。 |
| --dns-name | 作成するサービスの DNS 名。 この設定のために Service Fabric DNS システム サービスを有効にする必要があります。 |
| --instance-count | インスタンス数。 これはステートレス サービスにのみ適用されます。 |
| --int-scheme | ある範囲の符号なし整数にサービスを均一にパーティション分割する必要があることを示します。 |
| --int-scheme-count | 整数キーの範囲内で作成するパーティションの数 (均一な整数パーティション構成を使用している場合)。 |
| --int-scheme-high | キー整数の範囲の末尾 (均一な整数パーティション構成を使用している場合)。 |
| --int-scheme-low | キー整数の範囲の先頭 (均一な整数パーティション構成を使用している場合)。 |
| --load-metrics | サービスをノード間で負荷分散するときに使用されるメトリックの JSON でエンコードされた一覧。 |
| --min-replica-set-size | レプリカ セットの最小サイズ (数)。 これはステートフル サービスにのみ適用されます。 |
| --move-cost | サービスの移動コストを指定します。 指定可能な値\: "Zero"、"Low"、"Medium"、"High"。 |
| --named-scheme | サービスが複数の名前付きパーティションを持つ必要があることを示します。 |
| --named-scheme-list | 名前付きパーティション構成を使用している場合、サービスを分割するパーティションの名前の JSON でエンコードされた一覧。 |
| --no-persisted-state | true の場合、サービスで永続化された状態がローカル ディスクに格納されていないこと、つまり、メモリには状態のみが格納されていることを示します。 |
| --placement-policy-list | サービスの配置ポリシーと関連するすべてのドメイン名の JSON でエンコードされた一覧。 ポリシーには、`NonPartiallyPlaceService`、`PreferPrimaryDomain`、`RequireDomain`、`RequireDomainDistribution` の 1 つ以上を指定できます。 |
| --quorum-loss-wait | パーティションがクォーラム損失の状態であることを許可する最大期間 (秒)。 これはステートフル サービスにのみ適用されます。 |
| --replica-restart-wait | レプリカがダウンしてから新しいレプリカが作成されるまでの期間 (秒)。 これはステートフル サービスにのみ適用されます。 |
| --scaling-policies | このサービスのスケーリング ポリシーの JSON でエンコードされた一覧。 |
| --singleton-scheme | サービスに 1 つのパーティションを持つ必要があるか、サービスを非分割にする必要があるかを示します。 |
| --stand-by-replica-keep | スタンバイ レプリカが削除されるまでに保持される最大期間 (秒)。 これはステートフル サービスにのみ適用されます。 |
| --stateful | サービスがステートフル サービスであることを示します。 |
| --stateless | サービスがステートレス サービスであることを示します。 |
| --target-replica-set-size | ターゲット レプリカ セットのサイズ (数)。 これはステートフル サービスにのみ適用されます。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-delete"></a>sfctl service delete
既存の Service Fabric サービスを削除します。

サービスを削除するには、その前にサービスが作成されている必要があります。 既定では Service Fabric は、安全な方法でサービスのレプリカを閉じてからサービスを削除しようとします。 ただし、サービスでレプリカを正常に終了できない問題が発生している場合は、削除操作に時間がかかったり、動作しなくなったりする可能性があります。 正常に閉じるシーケンスをスキップし、サービスを強制的に削除するには、省略可能な ForceRemove フラグを使用します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --service-id [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --force-remove | 正常なシャットダウン シーケンスを経由せずに、Service Fabric アプリケーションまたはサービスを強制的に削除します。 このパラメーターを使用すると、レプリカの正常な終了を妨げるサービス コードの問題によって削除がタイムアウトしたアプリケーションまたはサービスを強制的に削除することができます。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-deployed-type"></a>sfctl service deployed-type
Service Fabric クラスター内のノードにデプロイされたアプリケーションの指定されたサービスの種類に関する情報を取得します。

Service Fabric クラスター内のノードにデプロイされたアプリケーションから特定のサービスの種類に関する情報を含む一覧を取得します。 応答には、サービスの種類名、登録状態、それを登録したコード パッケージ、サービス パッケージのアクティブ化 ID が含まれています。 各エントリは、サービスの種類の 1 回のアクティブ化を表し、アクティブ化 ID で区別されます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id    [必須] | アプリケーションの ID。 これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --node-name         [必須] | ノード名。 |
| --service-type-name [必須] | Service Fabric サービスの種類の名前を指定します。 |
| --service-manifest-name | デプロイされたサービスの種類の情報の一覧をフィルター処理するためのサービス マニフェストの名前。 指定した場合、応答には、このサービス マニフェストで定義されているサービスの種類に関する情報のみが含まれます。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-deployed-type-list"></a>sfctl service deployed-type-list
Service Fabric クラスター内のノードにデプロイされたアプリケーションからサービスの種類に関する情報を含む一覧を取得します。

Service Fabric クラスター内のノードにデプロイされたアプリケーションからサービスの種類に関する情報を含む一覧を取得します。 応答には、サービスの種類名、登録状態、それを登録したコード パッケージ、サービス パッケージのアクティブ化 ID が含まれています。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id [必須] | アプリケーションの ID。 これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --node-name    [必須] | ノード名。 |
| --service-manifest-name | デプロイされたサービスの種類の情報の一覧をフィルター処理するためのサービス マニフェストの名前。 指定した場合、応答には、このサービス マニフェストで定義されているサービスの種類に関する情報のみが含まれます。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-description"></a>sfctl service description
既存の Service Fabric サービスの説明を取得します。

既存の Service Fabric サービスの説明を取得します。 サービスの説明を取得するには、サービスが作成されている必要があります。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --service-id [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-get-container-logs"></a>sfctl service get-container-logs
Service Fabric ノードに展開されているコンテナーのコンテナー ログを取得します。

特定のコード パッケージの Service Fabric ノードにデプロイされているコンテナーのコンテナー ログを取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id        [必須] | アプリケーションの ID。 これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --code-package-name     [必須] | Service Fabric クラスターでアプリケーションの種類の一部として登録されているサービス マニフェストで指定されているコード パッケージの名前。 |
| --node-name             [必須] | ノード名。 |
| --service-manifest-name [必須] | Service Fabric クラスターでアプリケーションの種類の一部として登録されているサービス マニフェストの名前。 |
| --previous | コード パッケージ インスタンスの終了/デッド コンテナーからコンテナー ログを取得するかどうかを指定します。 |
| --tail | ログの末尾から表示する行数。 既定値は 100 です。 ログ全体を表示する場合は "all" です。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-health"></a>sfctl service health
指定した Service Fabric サービスの正常性を取得します。

指定したサービスの正常性情報を取得します。 正常性状態に基づいてサービスで報告される正常性イベントのコレクションをフィルター処理するには、EventsHealthStateFilter を使用します。 返されるパーティションのコレクションをフィルター処理するには、PartitionsHealthStateFilter を使用します。 正常性ストアに存在しないサービスを指定した場合、この要求からエラーが返されます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --service-id          [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --events-health-state-filter | 正常性の状態に基づいて返された HealthEvent オブジェクトのコレクションをフィルター処理できます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するイベントのみが返されます。 すべてのイベントが集計された正常性の状態を評価するために使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のすべてのイベントが返されます。  <br> - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。  <br> - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。  <br> - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。  <br> - Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。  <br> - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。  <br> - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。 |
| --exclude-health-statistics | 正常性の統計情報をクエリ結果の一部として返すかどうかを示します。 既定では false です。 統計情報は、正常性の状態が Ok、Warning、および Error の子エンティティの数を示します。 |
| --partitions-health-state-filter | 正常性の状態に基づくサービス正常性クエリの結果で返される、パーティションの正常性状態オブジェクトのフィルター処理を可能にします。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するパーティションのみが返されます。 集計された正常性の状態を評価するには、すべてのパーティションが使用されます。 指定しない場合、すべてのエントリが返されます。 状態の値はフラグベースの列挙であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせとなる可能性があります。 たとえば、指定した値が 6 の場合は、HealthState 値が OK (2) と Warning (4) のパーティションの正常性の状態が返されます。  <br> - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。  <br> - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。  <br> - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。  <br> - Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。  <br> - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。  <br> - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-info"></a>sfctl service info
Service Fabric アプリケーションに属する特定のサービスに関する情報を取得します。

指定された Service Fabric アプリケーションに属する指定されたサービスに関する情報を返します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id [必須] | アプリケーションの ID。 これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --service-id     [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-list"></a>sfctl service list
アプリケーション ID で指定されたアプリケーションに属するすべてのサービスに関する情報を取得します。

アプリケーション ID で指定されたアプリケーションに属するすべてのサービスに関する情報を返します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id [必須] | アプリケーションの ID。 これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --continuation-token | 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらない場合は、空以外の値を持つ継続トークンが API の応答に含まれます。 この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。 |
| --service-type-name | クエリを実行するサービスをフィルター処理するために使用されるサービスの種類名。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-manifest"></a>sfctl service manifest
サービスの種類を記述するマニフェストを取得します。

サービスの種類を記述するマニフェストを取得します。 応答には、サービス マニフェスト XML が文字列として含まれています。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-type-name    [必須] | アプリケーションの種類名。 |
| --application-type-version [必須] | アプリケーションの種類のバージョン。 |
| --service-manifest-name    [必須] | Service Fabric クラスターでアプリケーションの種類の一部として登録されているサービス マニフェストの名前。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-package-deploy"></a>sfctl service package-deploy
指定されたサービス マニフェストに関連付けられているパッケージを、指定されたノードのイメージ キャッシュにダウンロードします。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --app-type-name         [必須] | 対応する要求されたサービス マニフェストのアプリケーション マニフェストの名前。 |
| --app-type-version      [必須] | 対応する要求されたサービス マニフェストのアプリケーション マニフェストのバージョン。 |
| --node-name             [必須] | ノード名。 |
| --service-manifest-name [必須] | ダウンロードされるパッケージに関連付けられているサービス マニフェストの名前。 |
| --share-policy | 共有ポリシーの JSON でエンコードされた一覧。 共有ポリシー要素はそれぞれ、"name" と "scope" で構成されます。 name は、共有するコード、構成、またはデータ パッケージの名前に対応します。 scope には、"None"、"All"、"Code"、"Config"、または "Data" のいずれかを指定できます。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-package-health"></a>sfctl service package-health
Service Fabric ノードとアプリケーションに対してデプロイされた特定のアプリケーションのサービス パッケージの正常性に関する情報を取得します。

Service Fabric ノードにデプロイされた特定のアプリケーションのサービス パッケージの正常性に関する情報を取得します。 EventsHealthStateFilter を使用すると、必要に応じて、正常性の状態に基づき、デプロイされたサービス パッケージで報告された HealthEvent オブジェクトのコレクションをフィルター処理できます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id       [必須] | アプリケーションの ID。 これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --node-name            [必須] | ノード名。 |
| --service-package-name [必須] | サービス パッケージの名前。 |
| --events-health-state-filter | 正常性の状態に基づいて返された HealthEvent オブジェクトのコレクションをフィルター処理できます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するイベントのみが返されます。 すべてのイベントが集計された正常性の状態を評価するために使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のすべてのイベントが返されます。  <br> - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。  <br> - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。  <br> - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。  <br> - Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。  <br> - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。  <br> - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-package-info"></a>sfctl service package-info
指定された名前と完全に一致する Service Fabric ノードにデプロイされているサービス パッケージの一覧を取得します。

特定のアプリケーションの Service Fabric ノードにデプロイされているサービス パッケージに関する情報を返します。 これらは、パラメーターとして指定されたサービス パッケージ名と完全に一致する名前の付いたサービス パッケージの結果です。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id       [必須] | アプリケーションの ID。 これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --node-name            [必須] | ノード名。 |
| --service-package-name [必須] | サービス パッケージの名前。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-package-list"></a>sfctl service package-list
Service Fabric ノードにデプロイされているサービス パッケージの一覧を取得します。

特定のアプリケーションの Service Fabric ノードにデプロイされているサービス パッケージに関する情報を返します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id [必須] | アプリケーションの ID。 これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --node-name    [必須] | ノード名。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-recover"></a>sfctl service recover
現在クォーラム損失の状態に陥っている指定されたサービスの復旧を試みる必要があることを Service Fabric クラスターに示します。

現在クォーラム損失の状態に陥っている指定されたサービスの復旧を試みる必要があることを Service Fabric クラスターに示します。 この操作は、停止しているレプリカを復旧できないことがわかっている場合にのみ実行してください。 この API を不適切に使用すると、データ損失が発生する可能性があります。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --service-id [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-report-health"></a>sfctl service report-health
Service Fabric サービスの正常性レポートを送信します。

指定された Service Fabric サービスの正常性の状態をレポートします。 レポートには、正常性レポートのソースとレポートの対象となるプロパティに関する情報が含まれる必要があります。 このレポートは Service Fabric ゲートウェイ サービスに送信され、これにより正常性ストアに転送されます。 レポートは、ゲートウェイで受け入れられても、追加の検証後に正常性ストアによって拒否される可能性があります。 たとえば、正常性ストアは、古いシーケンス番号などの無効なパラメーターが原因でレポートを拒否する場合があります。 正常性ストアでレポートが適用されたかどうかを確認するには、サービスの正常性イベントにレポートが表示されていることを確認してください。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --health-property [必須] | 正常性情報のプロパティ。 <br><br> エンティティには、さまざまなプロパティの正常性レポートを含めることができます。 このプロパティは固定値列挙型ではなく文字列で、これにより、レポーターは、レポートをトリガーする状態の条件を柔軟に分類できます。 たとえば、SourceId が "LocalWatchdog" のレポーターは、ノード上で使用可能なディスクの状態を監視できるため、そのノードの "AvailableDisk" プロパティをレポートできます。 同じレポーターは、ノードの接続性を監視できるため、同じノードの "Connectivity" プロパティをレポートできます。 正常性ストアでは、これらのレポートは、指定されたノードの個々の正常性イベントとして扱われます。 このプロパティは SourceId と共に、正常性情報を一意に識別します。 |
| --health-state    [必須] | 指定可能な値\: "Invalid"、"Ok"、"Warning"、"Error"、"Unknown"。 |
| --service-id      [必須] | サービスの ID。 <br><br> これは通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --source-id       [必須] | 正常性に関する情報を生成したクライアント/ウォッチドッグ/システム コンポーネントを識別するソース名。 |
| --description | 正常性情報の説明。 <br><br> レポートについて人間が判読できる情報を追加するために使用するフリー テキストを表します。 この説明の文字列の最大長は 4,096 文字です。 指定された文字列が長い場合は、自動的に切り捨てられます。 切り捨てられると、説明の末尾の文字には "[切り捨て]" というマーカーが含まれ、文字列の合計サイズが 4,096 文字になります。 このマーカーの存在は、切り捨てが発生したことをユーザーに示します。 切り捨てられた場合、説明に含まれるのは元の文字列のうち 4,096 文字未満であることに注意してください。 |
| --immediate | レポートをすぐに送信する必要があるかどうかを示すフラグ。 <br><br> 正常性レポートは Service Fabric ゲートウェイ アプリケーションに送信され、これにより正常性ストアに転送されます。 Immediate が true に設定された場合、HTTP ゲートウェイ アプリケーションで使用されているファブリック クライアント設定に関係なく、このレポートはすぐに HTTP ゲートウェイから正常性ストアに送信されます。 これは、できるだけ早く送信する必要がある重要なレポートに役立ちます。 タイミングやその他の条件に応じて、レポートの送信は依然として失敗する可能性があります。たとえば、HTTP ゲートウェイが閉じている場合やメッセージがゲートウェイに到達しない場合です。 Immediate が false に設定された場合、レポートは、正常性クライアントの設定に基づいて HTTP ゲートウェイから送信されます。 その結果、HealthReportSendInterval の構成に従ってバッチ処理されます。 この設定が推奨されているのは、正常性クライアントが正常性ストアへの正常性レポート メッセージに加えて正常性レポート処理を最適化できるようになるためです。 既定では、レポートはすぐに送信されません。 |
| --remove-when-expired | 有効期限が切れたときにレポートを正常性ストアから削除するかどうかを示す値。 <br><br> true に設定した場合、レポートは、有効期限が切れると正常性ストアから削除されます。 false に設定した場合、レポートは、有効期限が切れるとエラーとして処理されます。 既定では、このプロパティの値は false です。 クライアントは、定期的にレポートする場合、RemoveWhenExpired を false (既定値) に設定する必要があります。 こうすることで、レポーターで問題 (デッドロックなど) が発生していてレポートできない場合に、正常性レポートの有効期限が切れると、エンティティはエラーと評価されます。 これにより、このエンティティの正常性はエラー状態と見なされます。 |
| --sequence-number | 数値文字列で表された、この正常性レポートのシーケンス番号。 <br><br> レポートのシーケンス番号は、古いレポートを検出するために正常性ストアによって使用されます。 指定しない場合、シーケンス番号は、レポートが追加されたときに正常性クライアントによって自動的に生成されます。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |
| --ttl | この正常性レポートの有効期間。 このフィールドでは、この期間の指定に ISO8601 形式を使用しています。 <br><br> クライアントは、定期的にレポートする場合、レポートの送信頻度を有効期間よりも高くする必要があります。 クライアントは、遷移時にレポートする場合、有効期間を無限に設定できます。 有効期間が期限切れになると、正常性に関する情報を含む正常性イベントは、正常性ストアから削除される (RemoveWhenExpired が true の場合) か、エラーと評価されます (RemoveWhenExpired が false の場合)。 指定しない場合、有効期間の既定値は無限になります。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-resolve"></a>sfctl service resolve
Service Fabric パーティションを解決します。

サービス レプリカのエンドポイントを取得するために Service Fabric サービス パーティションを解決します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --service-id [必須] | サービスの ID。 この ID は通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --partition-key-type | パーティションのキーの種類。 このパラメーターは、サービスのパーティション構成が Int64Range または Named の場合に必要です。 指定できる値は次のとおりです。 - None (1) - PartitionKeyValue パラメーターが指定されていないことを示します。 これは、パーティション構成が Singleton のパーティションに有効です。 これが既定値です。 値は 1 です。 Int64Range (2) - PartitionKeyValue パラメーターが int64 パーティション キーであることを示します。 これは、パーティション構成が Int64Range のパーティションに有効です。 値は 2 です。 - Named (3) - PartitionKeyValue パラメーターがパーティションの名前であることを示します。 これは、パーティション構成が Named のパーティションに有効です。 値は 3 です。 |
| --partition-key-value | パーティション キー。 これは、サービスのパーティション構成が Int64Range または Named の場合に必要です。 |
| --previous-rsp-version | 以前に受け取った応答のバージョン フィールドの値。 これは、以前に取得した結果が古くなっていることをユーザーが知っている場合に必要です。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-type-list"></a>sfctl service type-list
Service Fabric クラスターでプロビジョニングされたアプリケーションの種類でサポートされているサービスの種類に関する情報を含む一覧を取得します。

Service Fabric クラスターでプロビジョニングされたアプリケーションの種類でサポートされているサービスの種類に関する情報を含む一覧を取得します。 指定されたアプリケーションの種類が存在する必要があります。 そうしないと、404 状態が返されます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-type-name    [必須] | アプリケーションの種類名。 |
| --application-type-version [必須] | アプリケーションの種類のバージョン。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-service-update"></a>sfctl service update
特定の更新プログラムの説明を使用して、指定したサービスを更新します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --service-id   [必須] | サービスの ID。 これは通常、"fabric\:" URI スキームのないサービスの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、サービス名が "fabric\:/myapp/app1/svc1" の場合、サービス ID は、6.0 以降では "myapp\~app1\~svc1" になり、それより前のバージョンでは "myapp/app1/svc1" になります。 |
| --constraints | 文字列としての配置の制約。 配置の制約はノード プロパティ上のブール式で、サービスの要件に基づいて特定のノードにサービスを制限することができます。 たとえば、NodeType が blue のノードにサービスを配置するには、"NodeColor == blue" を指定します。 |
| --correlated-service | 関連付けるターゲット サービスの名前。 |
| --correlation | 配置アフィニティを使用して既存のサービスにサービスを関連付けます。 |
| --instance-count | インスタンス数。 これはステートレス サービスにのみ適用されます。 |
| --load-metrics | ノード間で負荷分散するときに使用されるメトリックの JSON でエンコードされた一覧。 |
| --min-replica-set-size | レプリカ セットの最小サイズ (数)。 これはステートフル サービスにのみ適用されます。 |
| --move-cost | サービスの移動コストを指定します。 指定可能な値\: "Zero"、"Low"、"Medium"、"High"。 |
| --placement-policy-list | サービスの配置ポリシーと関連するすべてのドメイン名の JSON でエンコードされた一覧。 ポリシーには、`NonPartiallyPlaceService`、`PreferPrimaryDomain`、`RequireDomain`、`RequireDomainDistribution` の 1 つ以上を指定できます。 |
| --quorum-loss-wait | パーティションがクォーラム損失の状態であることを許可する最大期間 (秒)。 これはステートフル サービスにのみ適用されます。 |
| --replica-restart-wait | レプリカがダウンしてから新しいレプリカが作成されるまでの期間 (秒)。 これはステートフル サービスにのみ適用されます。 |
| --scaling-policies | このサービスのスケーリング ポリシーの JSON でエンコードされた一覧。 |
| --stand-by-replica-keep | スタンバイ レプリカが削除されるまでに保持される最大期間 (秒)。 これはステートフル サービスにのみ適用されます。 |
| --stateful | ターゲット サービスがステートフル サービスであることを示します。 |
| --stateless | ターゲット サービスがステートレス サービスであることを示します。 |
| --target-replica-set-size | ターゲット レプリカ セットのサイズ (数)。 これはステートフル サービスにのみ適用されます。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |


## <a name="next-steps"></a>次の手順
- Service Fabric CLI を[セットアップ](service-fabric-cli.md)します。
- [サンプル スクリプト](/azure/service-fabric/scripts/sfctl-upgrade-application)を使用して、Service Fabric CLI の使用方法を学習します。
