---
title: 'クイック スタート: Azure Service Fabric での定期的なバックアップと復元 (プレビュー) | Microsoft Docs'
description: アプリケーションデータの定期的なバックアップを可能にする Service Fabric の定期的なバックアップと復元機能を使用します。
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAADBCAB-F0CF-4CBC-B663-4A6DCCB4DEE1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2018
ms.author: hrushib
ms.openlocfilehash: 5666ed4226104104b91b6f964abeb0490ef80866
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990746"
---
# <a name="quickstart-periodic-backup-and-restore-in-azure-service-fabric-preview"></a>クイック スタート:Azure Service Fabric での定期的なバックアップと復元 (プレビュー)
> [!div class="op_single_selector"]
> * [Azure 上のクラスター](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [スタンドアロン クラスター](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric は、信頼性に優れた分散型のマイクロサービス ベースのクラウド アプリケーションの開発と管理を簡単にする分散システム プラットフォームです。 ステートレスなマイクロ サービスとステートフルなマイクロ サービスの両方を実行できます。 ステートフル サービスは、要求と応答、または完全なトランザクションを超えて変更可能な信頼できる状態を維持できます。 災害が原因でステートフル サービスの長時間のダウンや情報の損失が発生した場合、再開後にサービスの提供を続行するためには、最新のバックアップの状態を復元する必要があります。

Service Fabric は、複数のノードの状態をレプリケートして、サービスの高可用性を確保します。 クラスター内の 1 つのノードに障害が発生しても、サービスは引き続き利用できます。 ただし、広範な障害が発生した場合でも、サービス データの信頼性を確保しておくことをお勧めします。
 
たとえば、次のような状況を防ぐために、サービスのデータをバックアップすることが望まれます。
- Service Fabric クラスター全体が永久に失われた場合。
- サービス パーティションのレプリカの過半数の完全な損失。
- 状態が誤って削除されたり、破損したりするなどの管理エラー。 例: 十分な権限を持つ管理者が誤ってサービスを削除した。
- データの破損を引き起こすサービスのバグ。 これは、サービス コードのアップグレードにより、Reliable Collection に不正なデータが書き込まれ始めると起こる可能性があります。 このような場合は、コードとデータの両方を、以前の状態に復元する必要があります。
- オフライン データ処理 データを生成するサービスと切り離されて行われるビジネス インテリジェンスのために、データのオフライン処理を用意しておけば便利です。

Service Fabric には、ポイントインタイムの[バックアップと復元](service-fabric-reliable-services-backup-restore.md)を行う API が組み込まれています。 アプリケーション開発者は、これらの API を使用して、サービスの状態を定期的にバックアップできます。 さらに、サービス管理者が特定の時点 (アプリケーションをアップグレードする前など) にサービスの外部からバックアップをトリガーすることを望んでいるのであれば、開発者は、バックアップ (および復元) を API としてサービスから公開する必要があります。 バックアップの管理には、追加コストが発生します。 たとえば、完全バックアップに続けて、30 分ごとに 5 回の増分バックアップを実行するとします。 完全バックアップを実行した後、その前に実行された増分バックアップを削除できます。 このアプローチではコードを追加する必要であり、それによってアプリケーションの開発中に追加コストが発生します。

アプリケーション データの定期的なバックアップは、分散アプリケーションを管理してデータの損失やサービスの長時間にわたる可用性の損失を防ぐための基本的なニーズです。 Service Fabric には、オプションで提供されるバックアップと復元サービスがあります。このサービスを使用すると、追加のコードを記述することなく、ステートフルな Reliable Services の定期的なバックアップを構成できます (Actor Services も対象になります)。 これまでに実行したバックアップも簡単に復元できます。 

> [!NOTE]
> 定期的なバックアップと復元機能は、現在**プレビュー**段階であり、実稼働ワークロードではサポートされません。 
>

Service Fabric には、定期的なバックアップと復元機能に関連する次の機能を実現するための一連の API が用意されています。

- ステートフルな Reliable Services と Reliable Actors の定期的なバックアップのスケジュールを設定する。バックアップの (外部) 保存場所へのアップロードがサポートされます。 サポートされる保存場所
    - Azure Storage
    - ファイル共有 (オンプレミス)
- バックアップを列挙する
- パーティションのアドホック バックアップをトリガーする
- 以前のバックアップを使用してパーティションを復元する
- バックアップを一時停止する
- バックアップの保有期間を管理する (予定)

## <a name="prerequisites"></a>前提条件
* Service Fabric のバージョンが 6.2 以降の Fabric クラスター。 クラスターは、Windows サーバー上にセットアップされている必要があります。 必要なパッケージのダウンロード手順については、[こちらの記事](service-fabric-cluster-creation-for-windows-server.md)を参照してください。
* バックアップを保存するストレージに接続するために必要なシークレットを暗号化する X.509 証明書。 X.509 証明書を取得するか自己署名証明書を作成する方法については、[こちらの記事](service-fabric-windows-cluster-x509-security.md)を参照してください。
* Service Fabric SDK バージョン 3.0 以降を使用してビルドされた Service Fabric Reliable Stateful アプリケーション。 Net Core 2.0 がターゲットであるアプリケーションは、Service Fabric SDK バージョン 3.1 以降を使用してビルドする必要があります。

## <a name="enabling-backup-and-restore-service"></a>バックアップと復元サービスの有効化
最初に、"_バックアップと復元サービス_" をクラスターで有効にする必要があります。 デプロイするクラスター用テンプレートを用意します。 [サンプル テンプレート](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)を使用できます。 次の手順で、"_バックアップと復元サービス_" を有効にします。

1. クラスター構成ファイルで、 `apiversion` が `10-2017` に設定されていることを確認します。設定されていない場合は下のスニペットに示すように更新します。

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. 下のスニペットに示すように、次の `addonFeatures` セクションを `properties` セクションの下に追加することで、"_バックアップと復元サービス_" を有効にします。 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. 資格情報を暗号化する X.509 証明書を構成します。 これは、ストレージに接続するために提供された証明書が永続化の前に暗号化されることを保証するために重要です。 下のスニペットに示すように、次の `BackupRestoreService` セクションを `fabricSettings` セクションの下に追加することで、暗号化証明書を有効にします。 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. 上記の変更でクラスター構成ファイルを更新したら、その変更を適用してデプロイ/アップグレードを完了します。 完了すると、"_バックアップと復元サービス_" がクラスターで実行されます。 このサービスの URI は `fabric:/System/BackupRestoreService` です。サービスは、Service Fabric エクスプローラーでシステム サービス セクションの下に置くことができます。 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Reliable Stateful サービスと Reliable Actors の定期バックアップの有効化
Reliable Stateful サービスと Reliable Actors の定期バックアップを有効にする手順について説明します。 これらの手順は、以下を前提としています。
- クラスターが "_バックアップと復元サービス_" でセットアップされている。
- Reliable Stateful サービスがクラスターにデプロイされている。 このクイックスタート ガイドでは、アプリケーションの URI は `fabric:/SampleApp` であり、このアプリケーションに属する Reliable Stateful サービスの URI は `fabric:/SampleApp/MyStatefulService` です。 このサービスは 1 つのパーティションでデプロイされ、パーティション ID は `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7` です。  

### <a name="create-backup-policy"></a>バックアップ ポリシーを作成する

最初の手順は、バックアップ スケジュール、バックアップ データのターゲット ストレージ、ポリシー名、および完全バックアップをトリガーする前に許可される増分バックアップの最大数を記述するバックアップ ポリシーの作成です。 

バックアップ ストレージ用に、ファイル共有を作成し、そのファイル共有にすべての Service Fabric のノード マシンに対する読み取り/書き込みアクセス権を付与します。 この例では、`BackupStore` という名前の共有が `StorageServer` 上に存在すると想定します。

必要な REST API を呼び出す次の PowerShell スクリプトを実行して、新しいポリシーを作成します。

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

### <a name="enable-periodic-backup"></a>定期バックアップを有効にする
アプリケーションのデータ保護要件を満たすポリシーを定義した後、バックアップ ポリシーをアプリケーションに関連付けする必要があります。 バックアップ ポリシーは、要件に応じて、アプリケーション、サービス、またはパーティションに関連付けることができます。

必要な REST API を呼び出す次の PowerShell スクリプトを実行して、上の手順で作成した `BackupPolicy1` という名前のバックアップ ポリシーを `SampleApp` という名前のアプリケーションに関連付けます。

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.2-preview"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>定期バックアップが動作していることを確認する

アプリケーションのバックアップを有効にすると、アプリケーションの下で Reliable Stateful サービスと Reliable Actors に属しているすべてのパーティションで、バックアップ ポリシーに従って定期バックアップが開始されます。 

![パーティションがバックアップされる正常性イベント][0]

### <a name="list-backups"></a>バックアップを一覧表示する

_GetBackups_ API を使用して、アプリケーションの Reliable Stateful サービスと Reliable Actors に属するすべてのパーティションに関連付けられているバックアップを列挙できます。 要件に応じて、アプリケーション、サービス、またはパーティションのバックアップを列挙できます。

HTTP API を呼び出す次の PowerShell スクリプトを実行して、`SampleApp` アプリケーション内のすべてのパーティションに対して作成されたバックアップを列挙します。

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.2-preview"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
上記を実行したときのサンプル出力:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
FailureError            : 
```

## <a name="preview-limitation-caveats"></a>プレビューでの制限事項/注意事項
- PowerShell コマンドレットが組み込まれた Service Fabric はありません。
- Service Fabric CLI はサポートされません。
-  バックアップの自動消去はサポートされません。 [バックアップ リテンション期間スクリプト](https://github.com/Microsoft/service-fabric-scripts-and-templates/tree/master/scripts/BackupRetentionScript)は、バックアップを消去するためのセットアップ スクリプト ベースの外部オートメーションと呼ばれることがあります。
- Linux 上の Service Fabric クラスターはサポートされません。

## <a name="next-steps"></a>次の手順
- [定期的なバックアップの構成について](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [バックアップと復元用の REST API リファレンス](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent.png

