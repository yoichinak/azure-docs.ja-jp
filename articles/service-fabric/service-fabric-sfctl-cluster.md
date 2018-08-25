---
title: Azure Service Fabric CLI- sfctl cluster | Microsoft Docs
description: Service Fabric CLI sfctl cluster のコマンドについて説明します。
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
ms.openlocfilehash: 4b0491d59e4ac495750a338ad743aab69ff47a4e
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494245"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Service Fabric クラスターの選択、管理、および操作を行います。

## <a name="commands"></a>コマンド

|コマンド|説明|
| --- | --- |
| code-versions | Service Fabric クラスターでプロビジョニングされている Fabric のコード バージョンの一覧を取得します。 |
| config-versions | Service Fabric クラスターでプロビジョニングされている Fabric の構成バージョンの一覧を取得します。 |
| health | Service Fabric クラスターの正常性を取得します。 |
| manifest | Service Fabric クラスター マニフェストを取得します。 |
| operation-cancel | ユーザーが誘発したフォールト操作を取り消します。 |
| operation-list | 指定した入力でフィルター処理されたユーザーが誘発したフォールト操作の一覧を取得します。 |
| provision | Service Fabric クラスターのコードまたは構成パッケージをプロビジョニングします。 |
| recover-system | 現在クォーラム損失の状態に陥っているシステム サービスの復旧を試みる必要があることを Service Fabric クラスターに示します。 |
| report-health | Service Fabric クラスターの正常性レポートを送信します。 |
| select | Service Fabric クラスターのエンドポイントに接続します。 |
| show-connection | この sfctl インスタンスが接続されている Service Fabric クラスターを表示します。 |
| unprovision | Service Fabric クラスターのコードまたは構成パッケージをプロビジョニング解除します。 |
| upgrade | Service Fabric クラスターのコードまたは構成バージョンのアップグレードを開始します。 |
| upgrade-resume | クラスターのアップグレードを次のアップグレード ドメインに進めます。 |
| upgrade-rollback | Service Fabric クラスターのアップグレードをロールバックします。 |
| upgrade-status | 現在のクラスターのアップグレードの進行状況を取得します。 |
| upgrade-update | Service Fabric クラスター アップグレードのアップグレード パラメーターを更新します。 |

## <a name="sfctl-cluster-code-versions"></a>sfctl cluster code-versions
Service Fabric クラスターでプロビジョニングされている Fabric のコード バージョンの一覧を取得します。

クラスターでプロビジョニングされている Fabric のコード バージョンに関する情報の一覧を取得します。 CodeVersion パラメーターを使用すると、必要に応じて、出力をその特定のバージョンのみにフィルター処理できます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --code-version | Service Fabric の製品バージョン。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-config-versions"></a>sfctl cluster config-versions
Service Fabric クラスターでプロビジョニングされている Fabric の構成バージョンの一覧を取得します。

クラスターでプロビジョニングされている Fabric の構成バージョンに関する情報の一覧を取得します。 ConfigVersion パラメーターを使用すると、必要に応じて、出力をその特定のバージョンのみにフィルター処理できます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --config-version | Service Fabric の構成バージョン。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-health"></a>sfctl cluster health
Service Fabric クラスターの正常性を取得します。

正常性状態に基づいてクラスターで報告される正常性イベントのコレクションをフィルター処理するには、EventsHealthStateFilter を使用します。 同様に、集計された正常性の状態に基づいて返されるノードとアプリケーションのコレクションをフィルターするには、NodesHealthStateFilter と ApplicationsHealthStateFilter を使用します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --applications-health-state-filter | 正常性状態に基づくクラスター正常性クエリの結果で返されるアプリケーション正常性状態オブジェクトのフィルター処理を可能にします。 このパラメーターに指定可能な値には、HealthStateFilter 列挙型のメンバーまたはメンバーのビットごとの演算から取得した整数値が含まれます。 フィルターに一致するアプリケーションのみが返されます。 集計された正常性の状態を評価するには、すべてのアプリケーションが使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState 値が OK (2) と Warning (4) のアプリケーションの正常性状態が返されます。  <br> - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。  <br> - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。  <br> - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。  <br> - Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。  <br> - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。  <br> - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。 |
| --events-health-state-filter | 正常性の状態に基づいて返された HealthEvent オブジェクトのコレクションをフィルター処理できます。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するイベントのみが返されます。 すべてのイベントが集計された正常性の状態を評価するために使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState の値が OK (2) と Warning (4) のすべてのイベントが返されます。  <br> - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。  <br> - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。  <br> - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。  <br> - Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。  <br> - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。  <br> - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。 |
| --exclude-health-statistics | 正常性の統計情報をクエリ結果の一部として返すかどうかを示します。 既定では false です。 統計情報は、正常性の状態が Ok、Warning、および Error の子エンティティの数を示します。 |
| --include-system-application-health-statistics | 正常性の統計情報を fabric\:/System アプリケーション正常性統計に含めるかどうかを示します。 既定では false です。 IncludeSystemApplicationHealthStatistics が true に設定されている場合、正常性の統計情報には fabric\:/System アプリケーションに属しているエンティティが含まれます。 そうでない場合、クエリの結果には、ユーザー アプリケーションのみの正常性の統計情報が含まれます。 このパラメーターを適用するには、クエリの結果に正常性の統計情報が含まれている必要があります。 |
| --nodes-health-state-filter | 正常性状態に基づくクラスター正常性クエリの結果で返されるノード正常性状態オブジェクトのフィルター処理を可能にします。 このパラメーターに指定できる値には、次の正常性の状態のいずれかの整数値が含まれます。 フィルターに一致するノードのみが返されます。 すべてのノードが集計された正常性の状態を評価するために使用されます。 指定しない場合、すべてのエントリが返されます。 状態値はフラグベースの列挙型であるため、値はビット演算子 'OR' を使用して取得したこれらの値の組み合わせが可能です。 たとえば、指定した値が 6 の場合、HealthState 値が OK (2) と Warning (4) のノードの正常性の状態が返されます。  <br> - Default - 既定値。 任意の HealthState と一致します。 値は 0 です。  <br> - None - どの HealthState 値とも一致しないフィルター。 状態の特定のコレクションの結果が返されないようにするために使用されます。 値は 1 です。  <br> - Ok - HealthState 値が Ok の入力に一致するフィルター。 値は 2 です。  <br> - Warning - HealthState 値が Warning の入力に一致するフィルター。 値は 4 です。  <br> - Error - HealthState 値が Error の入力に一致するフィルター。 値は 8 です。  <br> - All - 任意の HealthState 値の入力に一致するフィルター。 値は 65535 です。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-manifest"></a>sfctl cluster manifest
Service Fabric クラスター マニフェストを取得します。

Service Fabric クラスター マニフェストを取得します。 クラスター マニフェストには、クラスターでのさまざまなノードの種類、セキュリティの構成、障害、アップグレード ドメイン トポロジなどを含む、クラスターのプロパティが含まれています。これらのプロパティは、スタンドアロンのクラスターをデプロイするときに、ClusterConfig.JSON ファイルの一部として指定されます。 ただし、クラスター マニフェスト内のほとんどの情報は、他のデプロイ シナリオ (たとえば、Azure portal の使用時) でクラスターのデプロイ中に、Service Fabric によって内部的に生成されます。 クラスター マニフェストの内容は情報提供だけを目的としており、ユーザーがファイルの内容またはその解釈の形式で依存関係を取得することを想定していません。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-operation-cancel"></a>sfctl cluster operation-cancel
ユーザーが誘発したフォールト操作を取り消します。

StartDataLoss、StartQuorumLoss、StartPartitionRestart、StartNodeTransition の各 API は、CancelOperation を使用してキャンセルされた可能性のあるフォールト操作を開始できます。 force が false の場合、指定されたユーザーが誘発した操作は適切に停止され、クリーンアップされます。  force が true の場合、このコマンドは中止され、一部の内部状態は取り残される可能性があります。  force を true として指定する際は、注意してください。 force を true に設定した状態でこの API を呼び出すことは、最初に force を false に設定した状態で同じテスト コマンドに対してこの API の呼び出しが完了しているか、テスト コマンドで既に OperationState.RollingBack の OperationState が設定されていなければ許可されません。 

 説明\: OperationState.RollingBack は、コマンドの実行によって生じた内部システム状態をシステムがクリーンアップすることを意味します。 テスト コマンドがデータ損失の原因だった場合、データは復元されません。  たとえば、StartDataLoss を呼び出した後にこの API を呼び出した場合、システムによってクリーンアップされるのは、コマンドの実行による内部状態のみです。 データ損失を引き起こすほどコマンドが進行した場合、対象パーティションのデータは復元されません。 

> [!NOTE]
> この API は force==true を指定して呼び出した場合、内部状態は取り残される可能性があります。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --operation-id [必須] | この API の呼び出しを識別する GUID。  これは対応する GetProgress API に渡されます。 |
| --force | ユーザーが誘発した操作の実行によって変更された内部システム状態を適切にロールバックしてクリーンアップするかどうかを示します。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-operation-list"></a>sfctl cluster operation-list
指定した入力でフィルター処理されたユーザーが誘発したフォールト操作の一覧を取得します。

指定した入力でフィルター処理されたユーザーが誘発したフォールト操作の一覧を取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --state-filter | ユーザーが誘発した操作の OperationState をフィルター処理する際に使用します。 <br> 65535 - すべて選択 <br> 1 - Running の選択 <br> 2 - RollingBack の選択 <br>8 - Completed の選択 <br>16 - Faulted の選択 <br>32 - Cancelled の選択 <br>64 - ForceCancelled の選択  <br>既定値\: 65535。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |
| --type-filter | ユーザーが誘発した操作の OperationType をフィルター処理する際に使用します。 <br> 65535 - すべて選択 <br> 1 - PartitionDataLoss の選択 <br> 2 - PartitionQuorumLoss の選択 <br> 4 - PartitionRestart の選択 <br> 8 - NodeTransition の選択  <br> 既定値\: 65535。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-provision"></a>sfctl cluster provision
Service Fabric クラスターのコードまたは構成パッケージをプロビジョニングします。

Service Fabric クラスターのコードまたは構成パッケージを評価してプロビジョニングします。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --cluster-manifest-file-path | クラスター マニフェスト ファイルのパス。 |
| --code-file-path | クラスター コード パッケージ ファイルのパス。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-recover-system"></a>sfctl cluster recover-system
現在クォーラム損失の状態に陥っているシステム サービスの復旧を試みる必要があることを Service Fabric クラスターに示します。

現在クォーラム損失の状態に陥っているシステム サービスの復旧を試みる必要があることを Service Fabric クラスターに示します。 この操作は、停止しているレプリカを復旧できないことがわかっている場合にのみ実行してください。 この API を不適切に使用すると、データ損失が発生する可能性があります。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-report-health"></a>sfctl cluster report-health
Service Fabric クラスターの正常性レポートを送信します。

レポートには、正常性レポートのソースとレポートの対象となるプロパティに関する情報が含まれる必要があります。 このレポートは Service Fabric ゲートウェイ ノードに送信され、これにより正常性ストアに転送されます。 レポートは、ゲートウェイで受け入れられても、追加の検証後に正常性ストアによって拒否される可能性があります。 たとえば、正常性ストアは、古いシーケンス番号などの無効なパラメーターが原因でレポートを拒否する場合があります。 正常性ストアでレポートが適用されたかどうかを確認するには、クラスターの HealthEvents にレポートが表示されていることを確認してください。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --health-property [必須] | 正常性情報のプロパティ。 <br><br> エンティティには、さまざまなプロパティの正常性レポートを含めることができます。 このプロパティは固定値列挙型ではなく文字列で、これにより、レポーターは、レポートをトリガーする状態の条件を柔軟に分類できます。 たとえば、SourceId が "LocalWatchdog" のレポーターは、ノード上で使用可能なディスクの状態を監視できるため、そのノードの "AvailableDisk" プロパティをレポートできます。 同じレポーターは、ノードの接続性を監視できるため、同じノードの "Connectivity" プロパティをレポートできます。 正常性ストアでは、これらのレポートは、指定されたノードの個々の正常性イベントとして扱われます。 このプロパティは SourceId と共に、正常性情報を一意に識別します。 |
| --health-state    [必須] | 指定可能な値\: "Invalid"、"Ok"、"Warning"、"Error"、"Unknown"。 |
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

## <a name="sfctl-cluster-select"></a>sfctl cluster select
Service Fabric クラスターのエンドポイントに接続します。

セキュリティで保護されたクラスターに接続している場合は、証明書 (.crt) とキー ファイル (.key) の絶対パスを指定するか、両方を含む 1 つのファイル (.pem) を指定します。 両方は指定できません。 セキュリティで保護されたクラスターに接続している場合は、必要に応じて、CA バンドル ファイルまたは信頼された CA 証明書のディレクトリの絶対パスも指定します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --endpoint [必須] | ポートと HTTP または HTTPS プレフィックスを含むクラスター エンドポイント URL。 |
| --aad | 認証に Azure Active Directory を使用します。 |
| --ca | 有効なファイルまたは CA バンドル ファイルとして処理する CA 証明書ディレクトリの絶対パス。 |
| --cert | クライアント証明書ファイルの絶対パス。 |
| --key | クライアント証明書キー ファイルの絶対パス。 |
| --no-verify | HTTPS を使用する場合は、証明書の検証を無効にします。注\: これは安全でないオプションのため、運用環境では使用しないでください。 |
| --pem | クライアント証明書 (.pem ファイル) の絶対パス。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-show-connection"></a>sfctl cluster show-connection
この sfctl インスタンスが接続されている Service Fabric クラスターを表示します。

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-unprovision"></a>sfctl cluster unprovision
Service Fabric クラスターのコードまたは構成パッケージをプロビジョニング解除します。

コードと構成のプロビジョニングを個別に解除することができます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --code-version | クラスター コード パッケージのバージョン。 |
| --config-version | クラスター マニフェストのバージョン。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-upgrade"></a>sfctl cluster upgrade
Service Fabric クラスターのコードまたは構成バージョンのアップグレードを開始します。

指定されたアップグレード パラメーターを検証し、パラメーターが有効であれば、Service Fabric クラスターのコードまたは構成バージョンのアップグレードを開始します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --app-health-map | アプリケーション名と異常の最大パーセンテージのペアの JSON でエンコードされたディクショナリ。この異常のパーセンテージを超えるとエラーが発生します。 |
| --app-type-health-map | アプリケーションの種類名と異常の最大パーセンテージのペアの JSON でエンコードされたディクショナリ。このパーセンテージを超えるとエラーが発生します。 |
| --code-version | クラスター コードのバージョン。 |
| --config-version | クラスター構成のバージョン。 |
| --delta-health-evaluation | 各アップグレード ドメインの完了後に、絶対正常性評価ではなく、デルタ正常性評価を有効にします。 |
| --delta-unhealthy-nodes | クラスターのアップグレード時に許容できるノードの正常性低下の最大許容パーセンテージ。  既定値\: 10。 <br><br> 差分は、アップグレードの開始時のノードの状態と、正常性評価の時のノードの状態の間で測定されます。 チェックは、すべてのアップグレード ドメインのアップグレード完了後に実行され、クラスターのグローバル状態が許容範囲内であることを確認します。 |
| --failure-action | 指定可能な値\: "Invalid"、"Rollback"、"Manual"。 |
| --force-restart | 強制的に再起動します。 |
| --health-check-retry | 正常性チェックの再試行タイムアウト (ミリ秒単位)。 |
| --health-check-stable | 正常性チェックの安定期間 (ミリ秒単位)。 |
| --health-check-wait | 正常性チェックの待機期間 (ミリ秒単位)。 |
| --replica-set-check-timeout | アップグレードするレプリカ セット チェックのタイムアウト (秒単位)。 |
| --rolling-upgrade-mode | 指定可能な値\: "Invalid"、"UnmonitoredAuto"、"UnmonitoredManual"、"Monitored"。  既定値\: UnmonitoredAuto。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |
| --unhealthy-applications | エラーを報告する前の異常なアプリケーションの最大許容パーセンテージ。 <br><br> たとえば、アプリケーションの 10% の異常を許容するには、この値を 10 にします。 パーセンテージは、異常な可能性のあるアプリケーションの最大許容パーセンテージを表します。この値を超えるとクラスターはエラーの状態と見なされます。 許容パーセンテージ内であっても、1 つ以上の異常なアプリケーションがある場合は、正常性は Warning として評価されます。 これは、異常なアプリケーションの数をクラスター内のアプリケーション インスタンスの総数で割ることによって計算されます。ApplicationTypeHealthPolicyMap に含まれているアプリケーションの種類のアプリケーションは除外されます。 切り上げ計算が実行され、少数のアプリケーションに対する 1 つのエラーは許容されます。 |
| --unhealthy-nodes | エラーを報告する前の異常なノードの最大許容パーセンテージ。 <br><br> たとえば、ノードの 10% の異常を許容するには、この値を 10 にします。 パーセンテージは、異常な可能性のあるノードの最大許容パーセンテージを表します。この値を超えるとクラスターはエラーの状態と見なされます。 許容パーセンテージ内であっても、1 つ以上の異常なノードがある場合は、正常性は Warning として評価されます。 パーセンテージは、異常なノードの数をクラスター内のノードの総数で割ることによって計算されます。 切り上げ計算が実行され、少数のノードに対する 1 つのエラーは許容されます。 大規模なクラスターでは、ダウンしているか修復を必要とするノードがいくつか必ず存在するため、それが許容されるようにこのパーセンテージを構成する必要があります。 |
| --upgrade-domain-delta-unhealthy-nodes | クラスターのアップグレード時に許容できるアップグレード ドメイン ノードの正常性低下の最大許容パーセンテージ。  既定値\: 15。 <br><br> 差分は、アップグレードの開始時のアップグレード ドメイン ノードの状態と、正常性評価の時のアップグレード ドメイン ノードの状態の間で測定されます。 チェックは、すべてのアップグレード ドメインのアップグレード完了後にすべての完了したアップグレード ドメインに対して実行され、アップグレード ドメインの状態が許容範囲内であることを確認します。 |
| --upgrade-domain-timeout | アップグレード ドメインのタイムアウト (ミリ秒単位)。 |
| --upgrade-timeout | アップグレードのタイムアウト (ミリ秒単位)。 |
| --warning-as-error | Warnings は Error と同じ重大度で扱われます。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl cluster upgrade-resume
クラスターのアップグレードを次のアップグレード ドメインに進めます。

該当する場合は、クラスターのコードまたは構成のアップグレードを次のアップグレード ドメインに進めます。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --upgrade-domain [必須] | このクラスターのアップグレード用の次のアップグレード ドメイン。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl cluster upgrade-rollback
Service Fabric クラスターのアップグレードをロールバックします。

Service Fabric クラスターのコードまたは構成のアップグレードをロールバックします。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-upgrade-status"></a>sfctl cluster upgrade-status
現在のクラスターのアップグレードの進行状況を取得します。

実行中のクラスターのアップグレードの現在の進行状況を取得します。 現在進行中のアップグレードがない場合は、前回のクラスターのアップグレードの最後の状態を取得します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |

### <a name="global-arguments"></a>グローバル引数

|引数|説明|
| --- | --- |
| --debug | すべてのデバッグ ログを表示するため、ログ記録の詳細度を上げます。 |
| --help -h | このヘルプ メッセージを表示して終了します。 |
| --output -o | 出力形式。  使用可能な値\: json、jsonc、table、tsv。  既定値\: json。 |
| --query | JMESPath クエリ文字列。 詳細と例については、http\://jmespath.org/ を参照してください。 |
| --verbose | ログ記録の詳細度を上げます。 すべてのデバッグ ログを得るには --debug を使用します。 |

## <a name="sfctl-cluster-upgrade-update"></a>sfctl cluster upgrade-update
Service Fabric クラスター アップグレードのアップグレード パラメーターを更新します。

### <a name="arguments"></a>引数

|引数|説明|
| --- | --- |
| --app-health-map | アプリケーション名と異常の最大パーセンテージのペアの JSON でエンコードされたディクショナリ。この異常のパーセンテージを超えるとエラーが発生します。 |
| --app-type-health-map | アプリケーションの種類名と異常の最大パーセンテージのペアの JSON でエンコードされたディクショナリ。このパーセンテージを超えるとエラーが発生します。 |
| --delta-health-evaluation | 各アップグレード ドメインの完了後に、絶対正常性評価ではなく、デルタ正常性評価を有効にします。 |
| --delta-unhealthy-nodes | クラスターのアップグレード時に許容できるノードの正常性低下の最大許容パーセンテージ。  既定値\: 10。 <br><br> 差分は、アップグレードの開始時のノードの状態と、正常性評価の時のノードの状態の間で測定されます。 チェックは、すべてのアップグレード ドメインのアップグレード完了後に実行され、クラスターのグローバル状態が許容範囲内であることを確認します。 |
| --failure-action | 指定可能な値\: "Invalid"、"Rollback"、"Manual"。 |
| --force-restart | 強制的に再起動します。 |
| --health-check-retry | 正常性チェックの再試行タイムアウト (ミリ秒単位)。 |
| --health-check-stable | 正常性チェックの安定期間 (ミリ秒単位)。 |
| --health-check-wait | 正常性チェックの待機期間 (ミリ秒単位)。 |
| --replica-set-check-timeout | アップグレードするレプリカ セット チェックのタイムアウト (秒単位)。 |
| --rolling-upgrade-mode | 指定可能な値\: "Invalid"、"UnmonitoredAuto"、"UnmonitoredManual"、"Monitored"。  既定値\: UnmonitoredAuto。 |
| --timeout -t | サーバー タイムアウト (秒)。  既定値\: 60。 |
| --unhealthy-applications | エラーを報告する前の異常なアプリケーションの最大許容パーセンテージ。 <br><br> たとえば、アプリケーションの 10% の異常を許容するには、この値を 10 にします。 パーセンテージは、異常な可能性のあるアプリケーションの最大許容パーセンテージを表します。この値を超えるとクラスターはエラーの状態と見なされます。 許容パーセンテージ内であっても、1 つ以上の異常なアプリケーションがある場合は、正常性は Warning として評価されます。 これは、異常なアプリケーションの数をクラスター内のアプリケーション インスタンスの総数で割ることによって計算されます。ApplicationTypeHealthPolicyMap に含まれているアプリケーションの種類のアプリケーションは除外されます。 切り上げ計算が実行され、少数のアプリケーションに対する 1 つのエラーは許容されます。 |
| --unhealthy-nodes | エラーを報告する前の異常なノードの最大許容パーセンテージ。 <br><br> たとえば、ノードの 10% の異常を許容するには、この値を 10 にします。 パーセンテージは、異常な可能性のあるノードの最大許容パーセンテージを表します。この値を超えるとクラスターはエラーの状態と見なされます。 許容パーセンテージ内であっても、1 つ以上の異常なノードがある場合は、正常性は Warning として評価されます。 パーセンテージは、異常なノードの数をクラスター内のノードの総数で割ることによって計算されます。 切り上げ計算が実行され、少数のノードに対する 1 つのエラーは許容されます。 大規模なクラスターでは、ダウンしているか修復を必要とするノードがいくつか必ず存在するため、それが許容されるようにこのパーセンテージを構成する必要があります。 |
| --upgrade-domain-delta-unhealthy-nodes | クラスターのアップグレード時に許容できるアップグレード ドメイン ノードの正常性低下の最大許容パーセンテージ。  既定値\: 15。 <br><br> 差分は、アップグレードの開始時のアップグレード ドメイン ノードの状態と、正常性評価の時のアップグレード ドメイン ノードの状態の間で測定されます。 チェックは、すべてのアップグレード ドメインのアップグレード完了後にすべての完了したアップグレード ドメインに対して実行され、アップグレード ドメインの状態が許容範囲内であることを確認します。 |
| --upgrade-domain-timeout | アップグレード ドメインのタイムアウト (ミリ秒単位)。 |
| --upgrade-kind | 指定可能な値\: "Invalid"、"Rolling"、"Rolling_ForceRestart"。  既定値\: Rolling。 |
| --upgrade-timeout | アップグレードのタイムアウト (ミリ秒単位)。 |
| --warning-as-error | Warnings は Error と同じ重大度で扱われます。 |

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