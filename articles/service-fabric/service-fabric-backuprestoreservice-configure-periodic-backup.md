---
title: Azure Service Fabric の定期バックアップ構成を理解する | Microsoft Docs
description: アプリケーションデータの定期バックアップを可能にする Service Fabric の定期バックアップと復元機能を使用します。
services: service-fabric
documentationcenter: .net
author: hrushib
manager: timlt
editor: hrushib
ms.assetid: FAA45B4A-0258-4CB3-A825-7E8F70F28401
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: hrushib
ms.openlocfilehash: 8cfa0e2a5aa1d7f560fe84f4eda18349f5d1d8b4
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992483"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Azure Service Fabric の定期バックアップ構成を理解する

Reliable Stateful Services または Reliable Actors の定期バックアップの構成は、次のステップで構成されます。

1. **バックアップ ポリシーの作成**: このステップでは、要件に応じて 1 つまたは複数のバックアップ ポリシーを作成します。

2. **バックアップの有効化**: このステップでは、**ステップ 1** で作成したバックアップ ポリシーを、目的のエンティティ、_アプリケーション_、_サービス_、または、 _パーティション_に関連付けます。

## <a name="create-backup-policy"></a>バックアップ ポリシーの作成

バックアップ ポリシーは、次の構成で構成されます。

* **データ損失時の自動復元**: パーティションでデータ損失が発生した場合に、利用可能な最新のバックアップを使用して自動的に復元をトリガーするかどうかを指定します。

* **増分バックアップの最大数**: 2 回の完全バックアップの合間に実行される増分バックアップの最大数を定義します。 増分バックアップの最大数は、上限を指定します。 次の条件のいずれかに該当する場合は、指定した回数の増分バックアップが完了する前に、完全バックアップを実行できます。

    1. レプリカがプライマリになった後、完全バックアップが一回も実行されていない。

    2. 最後のバックアップの、ログ レコードの一部が切り捨てられている。

    3. レプリカが MaxAccumulatedBackupLogSizeInMB 制限を超えた。

* **バックアップ スケジュール**: 定期バックアップを実行する時刻または頻度。 指定した間隔または固定された時刻 (毎日/毎週) にバックアップを繰り返すようにスケジュールできます。

    1. **頻度ベースのバックアップ スケジュール**: 一定の間隔でデータのバックアップを実行する必要がある場合は、このスケジュールの種類を使用します。 2 つの連続するバックアップの間の目的の時間間隔を、ISO8601 形式を使用して定義します。 頻度ベースのバックアップ スケジュールでは、分単位の間隔がサポートされます。
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **時刻ベースのバックアップ スケジュール**: 1 日または 1 週間のうちの特定の時刻にデータのバックアップを実行する必要がある場合は、このスケジュールの種類を使用します。 スケジュールする頻度の種類は、毎日または毎週が可能です。
        1. **"_日次_" ベースのバックアップ スケジュール**: 1 日のうちの特定の時刻にデータのバックアップを実行する必要がある場合は、このスケジュールの種類を使用します。 これを指定するには、`ScheduleFrequencyType` を _Daily_ に設定し、`RunTimes` に 1 日のうちの目的の時刻の一覧を ISO8601 形式で設定します (時刻と共に指定された日付は無視されます)。 たとえば、`0001-01-01T18:00:00` は、毎日の "_午前 6 時_" を表し、日付部分の _0001-01-01_ は無視されます。 次の例は、日次バックアップを "_午前 9 時_" と "_午後 6 時_" にトリガーする構成を示しています。

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **"_週次_" ベースのバックアップ スケジュール**: 1 日のうちの特定の時刻にデータのバックアップを実行する必要がある場合は、このスケジュールの種類を使用します。 これを指定するには、`ScheduleFrequencyType` を _Weekly_ に設定し、`RunDays` にバックアップをトリガーする必要がある曜日の一覧を設定し、`RunTimes` に 1 日のうちの目的の時刻の一覧を ISO8601 形式で設定します (時刻と共に指定された日付は無視されます)。 定期バックアップをトリガーする曜日の一覧。 次の例は、月曜日から金曜日の "_午前 9 時_" と "_午後 6 時_" に日次バックアップをトリガーする構成を示しています。

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **バックアップ ストレージ**: バックアップをアップロードする場所を指定します。 ストレージは、Azure BLOB ストアまたはファイル共有が可能です。
    1. **Azure BLOB ストア**: Azure で生成されたバックアップを格納する必要がある場合は、このストレージの種類を選択します。 _スタンドアロン_ クラスターと_Azure ベースの_クラスターは、両方ともこのストレージの種類を使用できます。 このストレージの種類の記述では、接続文字列と、バックアップをアップロードする必要があるコンテナーの名前が必要です。 指定した名前のコンテナーが使用できない場合は、バックアップのアップロード時に作成されます。
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **ファイル共有**: データをオンプレミスで格納する必要がある場合は、_スタンドアロン_ クラスター用のこのストレージの種類を選択します。 このストレージの種類の記述では、バックアップをアップロードする必要があるファイル共有パスが必要です。 ファイル共有へのアクセスは、次のオプションのいずれかを使用して構成できます。
        1. "_統合 Windows 認証_"。ファイル共有へのアクセスは、Service Fabric クラスターに属するすべてのコンピューターに提供されます。 この場合は、次のフィールドを設定して、"_ファイル共有_" ベースのバックアップ ストレージを構成します。

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. "_ユーザー名とパスワードを使用したファイル共有の保護_"。ファイル共有へのアクセスは、特定のユーザーに提供されます。 ファイル共有の指定では、プライマリ ユーザー名とプライマリ パスワードでの認証が失敗した場合に、フォールバック資格情報を提供するためのセカンダリ ユーザー名とセカンダリ パスワードを指定することもできます。 この場合は、次のフィールドを設定して、"_ファイル共有_" ベースのバックアップ ストレージを構成します。
            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> ストレージの信頼性が、バックアップ データの信頼性要件を満たしているか、それを上回っていることを確認してください。
>

## <a name="enable-periodic-backup"></a>定期バックアップを有効にする
データのバックアップ要件を満たすバックアップ ポリシーを定義した後、バックアップ ポリシーを "_アプリケーション_"、"_サービス_"、または "_パーティション_" に適切に関連付ける必要があります。

### <a name="hierarchical-propagation-of-backup-policy"></a>バックアップ ポリシーの階層的な伝達
Service fabric では、アプリケーション、サービス、およびパーティション間のリレーションシップは、[アプリケーション モデル](./service-fabric-application-model.md)で説明されているように階層構造になっています。 バックアップ ポリシーは、階層内の "_アプリケーション_"、"_サービス_"、または "_パーティション_" に関連付けることができます。 バックアップ ポリシーは、次のレベルの階層に伝達されます。 1 つだけ作成されたバックアップ ポリシーが "_アプリケーション_" に関連付けられていると仮定した場合、"_アプリケーション_" のすべての _Reliable Stateful Services_ と _Reliable Actors_ に属するすべてのステートフル パーティションが、そのバックアップ ポリシーを使用してバックアップされます。 バックアップ ポリシーが _Reliable Stateful Services_ に関連付けられている場合は、すべてのパーティションが、そのバックアップ ポリシーを使用してバックアップされます。

### <a name="overriding-backup-policy"></a>バックアップ ポリシーの上書き
アプリケーションのすべてのサービスに対して同じバックアップ スケジュールでデータをバックアップする必要があるが、特定のサービスに対するもっと高い頻度でのデータのバックアップや、別のストレージ アカウントやファイル共有に対するバックアップの実行が必要なシナリオが存在する場合があります。 このようなシナリオに対処するために、バックアップ復元サービスには、サービスとパーティションのスコープでポリシーを上書きできる機能が提供されています。 バックアップ ポリシーが "_サービス_" または "_パーティション_" に関連付けられた場合、伝達されたバックアップ ポリシーは上書きされます (存在する場合)。

### <a name="example"></a>例

この例では、2 つのアプリケーション (_MyApp_A_ と _MyApp_B_) の設定を使用します。 アプリケーション _MyApp_A_ には 2 つの Reliable Stateful Services (_SvcA1_ & _SvcA3_) と、1 つの Reliable Actor サービス (_ActorA2_) が含まれています。 _SvcA1_ には 3 つのパーティションが、_ActorA2_ と _SvcA3_ にはそれぞれ 2 つのパーティションが含まれています。  アプリケーション _MyApp_B_ には 3 つの Reliable Stateful Services (_SvcB1_、_SvcB2_、および_SvcB3_) が含まれています。 _SvcB1_ と _SvcB2_ には、それぞれ 2 つのパーティションが、_SvcB3_ には 3 つのパーティションが含まれています。

これらのアプリケーションのデータのバックアップ要件は以下であると仮定します。

1. MyApp_A
    1. このアプリケーションに属するすべての _Reliable Stateful Services_ と _Reliable Actors_ のすべてのパーティションのデータの日次バックアップを作成します。 バックアップ データは、場所 _BackupStore1_ にアップロードします。

    2. サービスの 1 つである _SvcA3_ では、データのバックアップを 1 時間ごとに実行する必要があります。

    3. パーティション _SvcA1_P2_ 内のデータのサイズが予想を超えているため、そのバックアップ データは、別の保存場所 _BackupStore2_ に格納する必要があります。

2. MyApp_B
    1. 毎日曜日の午前 8 時に、_SvcB1_ サービスのすべてのパーティションのデータのバックアップを作成します。 バックアップ データは、場所 _BackupStore1_ にアップロードします。

    2. 毎日曜日の午前 8 時に、パーティション _SvcB2_P1_のデータのバックアップを作成します。 バックアップ データは、場所 _BackupStore1_ にアップロードします。

これらのデータのバックアップ要件に対処するには、バックアップ ポリシー BP_1 から BP_5 を作成し、以下のようにバックアップを有効にします。
1. MyApp_A
    1. バックアップ ポリシー _BP_1_ を作成します。これは頻度ベースのバックアップ スケジュールであり、頻度は 24 時間間隔に設定され、 バックアップ ストレージは保存場所 _BackupStore1_ を使用するように構成されます。 [Enable Application Backup](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-enableapplicationbackup) API を使用して、アプリケーション _MyApp_A_ に対してこのポリシーを有効にします。 このアクションによって、アプリケーション _MyApp_A_ に属する _Reliable Stateful Services_ と _Reliable Actors_ のすべてのパーティションに対して、バックアップ ポリシー _BP_1_ を使用したデータのバックアップが有効になります。

    2. バックアップ ポリシー _BP_2_ を作成します。これは頻度ベースのバックアップ スケジュールであり、頻度は 1 時間間隔に設定され、 バックアップ ストレージは保存場所 _BackupStore1_ を使用するように構成されます。 [Enable Application Backup](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-enableservicebackup) API を使用して、サービス _SvcA3_ に対してこのポリシーを有効にします。 このアクションによって、伝達されたポリシー_BP_1_ が、サービス _SvcA3_ のすべてのパーティションに対して明示的に有効化されたバックアップ ポリシー _BP_2_ によって上書きされ、これらのパーティションでバックアップ ポリシー _BP_2_ が使用されるようになります。

    3. バックアップ ポリシー _BP_3_ を作成します。これは頻度ベースのバックアップ スケジュールであり、頻度は 24 時間間隔に設定され、 バックアップ ストレージは保存場所 _BackupStore2_ を使用するように構成されます。 [Enable Partition Backup](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-enablepartitionbackup) API を使用して、パーティション _SvcA1_P2_ に対してこのポリシーを有効にします。 このアクションによって、伝達されたポリシー _BP_1_ が、パーティション _SvcA1_P2_ に対して明示的に有効化されたバックアップ ポリシー _BP_3_ によって上書きされます。

2. MyApp_B
    1. バックアップ ポリシー _BP_4_ を作成します。これは時刻ベースのバックアップ スケジュールであり、スケジュールの頻度の種類は週次に設定され、実行日は日曜日に設定され、実行時刻は午前 8 時に設定されます。 バックアップ ストレージは、保存場所 _BackupStore1_ を使用するように構成されます。 [Enable Application Backup](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-enableservicebackup) API を使用して、サービス _SvcB1_ に対してこのポリシーを有効にします。 このアクションによって、サービス _SvcB1_ のすべてのパーティションに対して、バックアップ ポリシー _BP_4_ を使用したデータのバックアップが有効になります。

    2. バックアップ ポリシー _BP_5_ を作成します。これは時刻ベースのバックアップ スケジュールであり、スケジュールの頻度の種類は日次に設定され、実行時刻は午前 8 時に設定されます。 バックアップ ストレージは、保存場所 _BackupStore1_ を使用するように構成されます。 [Enable Partition Backup](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-enablepartitionbackup) API を使用して、パーティション _SvcB2_P1_ に対してこのポリシーを有効にします。 このアクションによって、パーティション _SvcB2_P1_ に対して、バックアップ ポリシー _BP_5_ を使用したデータのバックアップが有効になります。

次の図は、明示的に有効になっているバックアップ ポリシーと、伝達されたバックアップ ポリシーを示しています。

![Service Fabric アプリケーションの階層][0]

## <a name="disable-backup"></a>バックアップを無効にする
データをバックアップする必要がない場合は、バックアップ ポリシーを無効にできます。 "_アプリケーション_" で有効なバックアップ ポリシーは、[Disable Application Backup](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-disableapplicationbackup) API を使用して、同じ "_アプリケーション_" でのみ無効にでき、"_サービス_" で有効なバックアップ ポリシーは、[Disable Service Backup](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-disableservicebackup) API を使用して、同じ "_サービス_" で無効にでき、"_パーティション_" で有効なバックアップ ポリシーは、 [Disable Partition Backup](https://docs.microsoft.com/en-in/rest/api/servicefabric/sfclient-api-disablepartitionbackup) API を使用して、同じ "_パーティション_" で無効にできます。

* "_アプリケーション_" に対するバックアップ ポリシーを無効にすると、Reliable Stateful Services パーティションまたは Reliable Actors パーティションへのバックアップ ポリシーの伝達の結果発生するすべてのデータの定期バックアップが停止します。

* "_サービス_" に対するバックアップ ポリシーを無効にすると、その "_サービス_" のパーティションへのバックアップ ポリシーの伝達の結果発生するすべてのデータの定期バックアップが停止します。

* "_パーティション_" に対するバックアップ ポリシーを無効にすると、パーティションのバックアップ ポリシーによって発生するすべてのデータの定期バックアップが停止します。

## <a name="suspend--resume-backup"></a>バックアップの中断と再開
データの定期バックアップを一時的に中断しなければならない特定の状況が発生する場合があります。 このような状況では、要件に応じて、"_アプリケーション_"、"_サービス_"、または "_パーティション_" で Suspend Backup API を使用できます。 定期バックアップの中断は、中断が適用されたアプリケーションの階層からサブツリーに伝達されます。 

* [Suspend Application Backup](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) API を使用して "_アプリケーション_" に中断を適用した場合は、そのアプリケーションの下のすべてのサービスとパーティションで、データの定期バックアップが中断されます。

* [Suspend Service Backup](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-suspendservicebackup) API を使用して "_サービス_" に中断を適用した場合は、そのサービスの下のすべてのパーティションで、データの定期バックアップが中断されます。

* [Suspend Partition Backup](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) API を使用して "_パーティション_" に中断を適用した場合は、そのサービスの下のパーティションで、データの定期バックアップが中断されます。

中断する必要がなくなったら、該当する Resume Backup API を使用して、データの定期バックアップを復元できます。 定期バックアップは、中断された "_アプリケーション_"、"_サービス_"、または "_パーティション_ " で再開する必要があります。

* 中断が "_アプリケーション_" に適用された場合は、[Resume Application Backup](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) API を使用して再開する必要があります。 

* 中断が "_サービス_" に適用された場合は、[Resume Service Backup](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-resumeservicebackup) API を使用して再開する必要があります。

* 中断が "_パーティション_" に適用された場合は、[Resume Partition Backup](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-resumepartitionbackup) API を使用して再開する必要があります。

## <a name="auto-restore-on-data-loss"></a>データ損失の自動復元
予期しない障害によって、サービス パーティションでデータ損失が発生する場合があります。 たとえば、パーティションの 3 つのレプリカのうちの 2 つのディスク (プライマリ レプリカを含む) が破損または消去された場合です。

Service Fabric は、パーティションでデータ損失が発生したことを検出すると、パーティションで `OnDataLossAsync` インターフェイス メソッドを呼び出して、パーティションで必要なアクションを実行してデータ損失の状態を脱することを期待します。 この状況では、パーティションの有効なバックアップ ポリシーで `AutoRestoreOnDataLoss` フラグが `true` に設定されている場合、このパーティションに対して利用できる最新のバックアップを使用して、復元が自動的にトリガーされます。

## <a name="get-backup-configuration"></a>バックアップ構成を取得する
"_アプリケーション_"、"_サービス_"、および "_パーティション_" スコープでのバックアップ構成情報を取得するための API を使用できます。 [Get Application Backup Configuration Info](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo)[Get Service Backup Configuration Info](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)および [Get Partition Backup Configuration Info](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) が、それぞれに該当する API です。 主に、これらの API は、適切なバックアップ ポリシー、バックアップ ポリシーが適用されるスコープ、およびバックアップの中断の詳細を返します。 これらの API から返される結果についての簡単な説明を次に示します。

- アプリケーションのバックアップ情報: アプリケーションに適用されたバックアップ ポリシーと、そのアプリケーションに属するサービスとパーティションのすべての上書きポリシーの詳細を示します。 アプリケーション、サービス、およびパーティションの中断情報も含まれます。

- サービスのバックアップ構成情報: サービスで有効なバックアップ ポリシーの詳細、そのポリシーが適用されたスコープ、およびサービスのパーティションのすべての上書きポリシーを示します。 サービスとそのパーティションの中断情報も含まれます。

- パーティションのバックアップ構成情報: パーティションで有効なバックアップ ポリシーの詳細と、そのポリシーが適用されたスコープを示します。 パーティションの中断情報も含まれます。

## <a name="list-available-backups"></a>使用可能なバックアップを一覧表示する

Get Backup List API を使用して、使用可能なバックアップを表示できます。 API 呼び出しの結果には、適切なバックアップ ポリシー内に構成されるバックアップ ストレージで利用できるすべてのバックアップに関連するバックアップ情報の項目が含まれます。 アプリケーション、サービス、またはパーティションに属している使用可能なバックアップを一覧表示する、この API のさまざまなバリエーションが提供されています。 これらの API は、すべての適切なパーティションで利用できる "_最新の_" バックアップの取得、または "_開始日_" と "_終了日"_ に基づくバックアップのフィルター処理をサポートします。

これらの API は、結果の改ページもサポートし、_MaxResults_ パラメーターがゼロ以外の正の整数に設定されている場合は、最大 _MaxResults_ 個のバックアップ情報項目を返します。 _MaxResults_ の値を超える数のバックアップ情報項目がある場合は、継続トークンが返されます。 有効な継続トークンのパラメーターを使用して、後続の結果セットを取得できます。 有効な継続トークンの値が後続の API の呼び出しに渡され、API は、後続の結果セットを返します。 使用可能なすべての結果が返される場合、応答に継続トークンは含まれません。

サポートされているバリエーションに関する簡単な情報を次に示します。

- [Get Application Backup List](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): 特定の Service Fabric アプリケーションに属するすべてのパーティションで使用できるバックアップの一覧を返します。

- [Get Service Backup List](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-getservicebackuplist): 特定の Service Fabric アプリケーションに属するすべてのサービスで使用できるバックアップの一覧を返します。
 
- [Get Partition Backup List](https://docs.microsoft.com/en-us/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): 指定されたパーティションで使用できるバックアップの一覧を返します。

## <a name="next-steps"></a>次の手順
- [バックアップと復元用の REST API リファレンス](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/BackupPolicyAssociationExample.png