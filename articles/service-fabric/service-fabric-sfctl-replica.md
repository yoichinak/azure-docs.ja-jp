---
title: Azure Service Fabric CLI- sfctl replica| Microsoft Docs
description: Service Fabric CLI sfctl replica のコマンドについて説明します。
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
ms.openlocfilehash: 6c16cd95fce7d3f367f0ded73c3635d8cefea7a0
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493989"
---
# <a name="sfctl-replica"></a>sfctl replica
サービス パーティションに属しているレプリカを管理します。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| deployed | Service Fabric ノードにデプロイされているレプリカの詳細を取得します。 |
| deployed-list | Service Fabric ノードにデプロイされているレプリカの一覧を取得します。 |
| health | Service Fabric ステートフル サービス レプリカまたはステートレス サービス インスタンスの正常性を取得します。 |
| info | Service Fabric パーティションのレプリカに関する情報を取得します。 |
| list | Service Fabric サービス パーティションのレプリカに関する情報を取得します。 |
| remove | ノード上で実行されているサービス レプリカを削除します。 |
| report-health | Service Fabric レプリカの正常性レポートを送信します。 |
| restart | ノード上で実行されている永続化されたサービスのサービス レプリカを再起動します。 |

## <a name="sfctl-replica-deployed"></a>sfctl replica deployed
Service Fabric ノードにデプロイされているレプリカの詳細を取得します。

Service Fabric ノードにデプロイされているレプリカの詳細を取得します。 情報には、サービスの種類、サービス名、現在のサービス操作、現在のサービス操作の開始日時、パーティション ID、レプリカ/インスタンス ID、報告された負荷およびその他の情報が含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --node-name    [必須] | ノード名。 |
| --partition-id [必須] | パーティションの ID。 |
| --replica-id   [必須] | レプリカの識別子。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-replica-deployed-list"></a>sfctl replica deployed-list
Service Fabric ノードにデプロイされているレプリカの一覧を取得します。

Service Fabric ノードにデプロイされたレプリカに関する情報を含むリストを取得します。 情報には、パーティション ID、レプリカ ID、レプリカの状態、サービスの名前、サービスの種類の名前、その他の情報が含まれます。 これらのパラメーターの指定した値と一致するデプロイ済みレプリカに関する情報を取得するには、PartitionId または ServiceManifestName クエリ パラメーターを使用します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --application-id [必須] | アプリケーションの ID。 これは通常、"fabric\:" URI スキームのないアプリケーションの完全な名前です。 バージョン 6.0 以降では、階層名は "\~" 文字で区切られます。 たとえば、アプリケーション名が "fabric\:/myapp/app1" の場合、アプリケーション ID は、6.0 以降では "myapp\~app1" になり、それより前のバージョンでは "myapp/app1" になります。 |
| --node-name    [必須] | ノード名。 |
| --partition-id | パーティションの ID。 |
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

## <a name="sfctl-replica-health"></a>sfctl replica health
Service Fabric ステートフル サービス レプリカまたはステートレス サービス インスタンスの正常性を取得します。

Service Fabric レプリカの正常性を取得します。 正常性状態に基づいてレプリカで報告される正常性イベントのコレクションをフィルター処理するには、EventsHealthStateFilter を使用します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --partition-id    [必須] | パーティションの ID。 |
| --replica-id      [必須] | レプリカの識別子。 |
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

## <a name="sfctl-replica-info"></a>sfctl replica info
Service Fabric パーティションのレプリカに関する情報を取得します。

応答には、ID、ロール、状態、正常性、ノード名、アップタイム、およびその他のレプリカに関する詳細が含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --partition-id [必須] | パーティションの ID。 |
| --replica-id   [必須] | レプリカの識別子。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-replica-list"></a>sfctl replica list
Service Fabric サービス パーティションのレプリカに関する情報を取得します。

GetReplicas エンドポイントは、指定したパーティションのレプリカに関する情報を返します。 応答には、ID、ロール、状態、正常性、ノード名、アップタイム、およびその他のレプリカに関する詳細が含まれます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --partition-id [必須] | パーティションの ID。 |
| --continuation-token | 継続トークンのパラメーターは、次の結果セットを取得するために使用されます。 システムからの結果が 1 つの応答に収まらない場合は、空以外の値を持つ継続トークンが API の応答に含まれます。 この値が次の API 呼び出しに渡されると、API が次の結果セットを返します。 それ以上の結果がない場合は、継続トークンに値が含まれません。 このパラメーターの値を URL にエンコードすることはできません。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-replica-remove"></a>sfctl replica remove
ノード上で実行されているサービス レプリカを削除します。

この API は、Service Fabric クラスターからレプリカを削除することで、Service Fabric レプリカの障害をシミュレートします。 削除により、レプリカが閉じられ、ロールが None のレプリカに移行され、レプリカのすべての状態情報がクラスターから削除されます。 この API は、レプリカの状態の削除パスをテストし、クライアント API を介してレポート フォールトの永続的なパスをシミュレートします。 警告: この API を使用すると、安全性チェックは実行されません。 この API の不適切な使用は、ステートフル サービスのデータの損失につながる場合があります。 さらに、forceRemove フラグは、同じプロセスでホストされている他のすべてのレプリカに影響します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --node-name    [必須] | ノード名。 |
| --partition-id [必須] | パーティションの ID。 |
| --replica-id   [必須] | レプリカの識別子。 |
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

## <a name="sfctl-replica-report-health"></a>sfctl replica report-health
Service Fabric レプリカの正常性レポートを送信します。

指定された Service Fabric レプリカの正常性の状態をレポートします。 レポートには、正常性レポートのソースとレポートの対象となるプロパティに関する情報が含まれる必要があります。 このレポートは Service Fabric ゲートウェイ レプリカに送信され、これにより正常性ストアに転送されます。 レポートは、ゲートウェイで受け入れられても、追加の検証後に正常性ストアによって拒否される可能性があります。 たとえば、正常性ストアは、古いシーケンス番号などの無効なパラメーターが原因でレポートを拒否する場合があります。 正常性ストアでレポートが適用されたかどうかを確認するには、イベント セクションにレポートが表示されていることを確認してください。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --health-property [必須] | 正常性情報のプロパティ。 <br><br> エンティティには、さまざまなプロパティの正常性レポートを含めることができます。 このプロパティは固定値列挙型ではなく文字列で、これにより、レポーターは、レポートをトリガーする状態の条件を柔軟に分類できます。 たとえば、SourceId が "LocalWatchdog" のレポーターは、ノード上で使用可能なディスクの状態を監視できるため、そのノードの "AvailableDisk" プロパティをレポートできます。 同じレポーターは、ノードの接続性を監視できるため、同じノードの "Connectivity" プロパティをレポートできます。 正常性ストアでは、これらのレポートは、指定されたノードの個々の正常性イベントとして扱われます。 このプロパティは SourceId と共に、正常性情報を一意に識別します。 |
| --health-state    [必須] | 指定可能な値\: "Invalid"、"Ok"、"Warning"、"Error"、"Unknown"。 |
| --partition-id    [必須] | パーティションの ID。 |
| --replica-id      [必須] | パーティションの ID。 |
| --source-id       [必須] | 正常性に関する情報を生成したクライアント/ウォッチドッグ/システム コンポーネントを識別するソース名。 |
| --description | 正常性情報の説明。 <br><br> レポートについて人間が判読できる情報を追加するために使用するフリー テキストを表します。 この説明の文字列の最大長は 4,096 文字です。 指定された文字列が長い場合は、自動的に切り捨てられます。 切り捨てられると、説明の末尾の文字には "[切り捨て]" というマーカーが含まれ、文字列の合計サイズが 4,096 文字になります。 このマーカーの存在は、切り捨てが発生したことをユーザーに示します。 切り捨てられた場合、説明に含まれるのは元の文字列のうち 4,096 文字未満であることに注意してください。 |
| --immediate | レポートをすぐに送信する必要があるかどうかを示すフラグ。 <br><br> 正常性レポートは Service Fabric ゲートウェイ アプリケーションに送信され、これにより正常性ストアに転送されます。 Immediate が true に設定された場合、HTTP ゲートウェイ アプリケーションで使用されているファブリック クライアント設定に関係なく、このレポートはすぐに HTTP ゲートウェイから正常性ストアに送信されます。 これは、できるだけ早く送信する必要がある重要なレポートに役立ちます。 タイミングやその他の条件に応じて、レポートの送信は依然として失敗する可能性があります。たとえば、HTTP ゲートウェイが閉じている場合やメッセージがゲートウェイに到達しない場合です。 Immediate が false に設定された場合、レポートは、正常性クライアントの設定に基づいて HTTP ゲートウェイから送信されます。 その結果、HealthReportSendInterval の構成に従ってバッチ処理されます。 この設定が推奨されているのは、正常性クライアントが正常性ストアへの正常性レポート メッセージに加えて正常性レポート処理を最適化できるようになるためです。 既定では、レポートはすぐに送信されません。 |
| --remove-when-expired | 有効期限が切れたときにレポートを正常性ストアから削除するかどうかを示す値。 <br><br> true に設定した場合、レポートは、有効期限が切れると正常性ストアから削除されます。 false に設定した場合、レポートは、有効期限が切れるとエラーとして処理されます。 既定では、このプロパティの値は false です。 クライアントは、定期的にレポートする場合、RemoveWhenExpired を false (既定値) に設定する必要があります。 こうすることで、レポーターで問題 (デッドロックなど) が発生していてレポートできない場合に、正常性レポートの有効期限が切れると、エンティティはエラーと評価されます。 これにより、このエンティティの正常性はエラー状態と見なされます。 |
| --sequence-number | 数値文字列で表された、この正常性レポートのシーケンス番号。 <br><br> レポートのシーケンス番号は、古いレポートを検出するために正常性ストアによって使用されます。 指定しない場合、シーケンス番号は、レポートが追加されたときに正常性クライアントによって自動的に生成されます。 |
| --service-kind | 正常性レポート対象のサービス レプリカの種類 (ステートレスまたはステートフル)。 指定できる値\: "Stateless"、"Stateful"。  既定値\: Stateful。 |
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

## <a name="sfctl-replica-restart"></a>sfctl replica restart
ノード上で実行されている永続化されたサービスのサービス レプリカを再起動します。

ノード上で実行されている永続化されたサービスのサービス レプリカを再起動します。 警告: この API を使用すると、安全性チェックは実行されません。 この API の不適切な使用は、ステートフル サービスの可用性の損失につながる場合があります。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --node-name    [必須] | ノード名。 |
| --partition-id [必須] | パーティションの ID。 |
| --replica-id   [必須] | レプリカの識別子。 |
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
