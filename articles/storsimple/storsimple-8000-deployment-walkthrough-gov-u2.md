---
title: Government ポータルでの StorSimple 8000 シリーズ デバイスのデプロイ | Microsoft Docs
description: Update 3 以降を実行する StorSimple 8000 シリーズ デバイスおよびサービスを Azure Government ポータルでデプロイするための手順とベスト プラクティスを説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: alkohli
ms.openlocfilehash: 5a622eb5ae14a6c6b0c2dd4eceb6ffdb9733dcff
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38681538"
---
# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal"></a>Government ポータルでのオンプレミスの StorSimple デバイスのデプロイ

## <a name="overview"></a>概要
Microsoft Azure StorSimple デバイスのデプロイへようこそ。 デプロイに関するこれらのチュートリアルは、Azure Government ポータルで Update 3 以降のソフトウェアを実行している StorSimple 8000 シリーズに適用されます。 このチュートリアル シリーズには、構成チェック リスト、構成の前提条件の一覧、および StorSimple デバイスを構成するための詳細な手順が含まれています。

これらのチュートリアルの情報は、ユーザーが安全上の注意を確認していること、および StorSimple デバイスのパッケージを展開してラックに配置し、配線していることを想定しています。 これらのタスクを実行する必要がある場合は、最初に [安全性に関する注意事項](storsimple-safety.md)を確認してください。 デバイス固有の指示に従って、デバイスの開梱、ラック取付け、ケーブル接続を行ってください。

* [8100 デバイスの開梱、ラック取り付け、およびケーブル接続](storsimple-8100-hardware-installation.md)
* [8600 デバイスの開梱、ラック取り付け、およびケーブル接続](storsimple-8600-hardware-installation.md)

セットアップと構成のプロセスを完了するには、管理者特権が必要です。 開始する前に、構成チェック リストを確認することをお勧めします。 デプロイと構成のプロセスは、完了するまでに時間がかかることがあります。

> [!NOTE]
> Microsoft Azure の Web サイトで発行されている StorSimple のデプロイに関する情報は、StorSimple 8000 シリーズ デバイスのみに適用されます。 7000 シリーズ デバイスについて詳しくは、[http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com) をご覧ください。 7000 シリーズのデプロイについては、『 [StorSimple システム クイック スタート ガイド](http://onlinehelp.storsimple.com/111_Appliance/)』を参照してください。


## <a name="deployment-steps"></a>デプロイメントの手順
StorSimple デバイスを構成し、StorSimple デバイス マネージャー サービスに接続するには、次の必須手順を実行します。 必須手順に加えて、デプロイ中に完了することが必要となる可能性がある省略可能な手順が存在します。 デプロイの詳細な手順では、どの時点でこれらの省略可能な手順を実行するかを示しています。

| 手順 | 説明 |
| --- | --- |
| **前提条件** |これらの前提条件は、今回のデプロイの準備として完了する必要があります。 |
| [デプロイの構成チェック リスト](#deployment-configuration-checklist) |このチェック リストを使用して、デプロイ前およびデプロイ中に情報を収集し、記録します。 |
| [デプロイメントの前提条件](#deployment-prerequisites) |これらの前提条件を使用して、デプロイに対する環境の準備が完了していることを確認します。 |
|  | |
| **デプロイの手順** |運用環境に StorSimple デバイスをデプロイするには、次の手順を実行します。 |
| [手順 1. 新しいサービスを作成する](#step-1-create-a-new-service) |StorSimple デバイス用にクラウド管理とストレージを設定します。 *既に他の StorSimple デバイス用のサービスがある場合は、この手順をスキップしてください。* |
| [手順 2. サービス登録キーを取得する](#step-2-get-the-service-registration-key) |このキーを使用して、StorSimple デバイスを管理サービスに登録し、接続します。 |
| [手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し登録する](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |管理サービスを使用して、デバイスをネットワークに接続して Azure に登録し、セットアップを完了します。 |
| [手順 4. デバイスの最小セットアップを完了する](#step-4-complete-minimum-device-setup) </br>省略可能: StorSimple デバイスを更新する |管理サービスを使用して、デバイスのセットアップを完了し、ストレージを提供できるようにします。 |
| [手順 5. ボリューム コンテナーを作成する](#step-5-create-a-volume-container) |ボリュームをプロビジョニングするためのコンテナーを作成します。 ボリューム コンテナーでは、そこに含まれるすべてのボリュームのストレージ アカウント、帯域幅、暗号化が設定されています。 |
| [手順 6. ボリュームを作成する](#step-6-create-a-volume) |サーバーの StorSimple デバイスでストレージ ボリュームをプロビジョニングします。 |
| [手順 7. ボリュームをマウント、初期化、フォーマットする](#step-7-mount-initialize-and-format-a-volume) </br>省略可能: MPIO を構成する |デバイスによって提供される iSCSI ストレージにサーバーを接続します。 必要に応じて、サーバーがリンク、ネットワーク、およびインターフェイスの障害を許容できるように MPIO を構成します。 |
| [手順 8. バックアップを取得する](#step-8-take-a-backup) |データを保護するためのバックアップ ポリシーを設定します。 |
|  | |
| **その他の手順** |ソリューションのデプロイ中に、これらの手順を参照する必要が生じる場合があります。 |
| [サービスの新しいストレージ アカウントを構成する](#configure-a-new-storage-account-for-the-service) | |
| [PuTTY を使用してデバイスのシリアル コンソールに接続する](#use-putty-to-connect-to-the-device-serial-console) | |
| [更新プログラムをスキャンして適用する](#scan-for-and-apply-updates) | |
| [Windows Server ホストの IQN を取得する](#get-the-iqn-of-a-windows-server-host) | |
| [手動バックアップの作成](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>デプロイの構成チェック リスト
StorSimple デバイスをデプロイする前に、デバイスにソフトウェアを構成するための情報を収集する必要があります。 事前にこれらの情報の一部を準備することで、環境内に StorSimple デバイスをデプロイするプロセスを効率化できます。 このチェック リストをダウンロードし、デバイスのデプロイ時に構成の詳細情報をメモするために使用してください。

[StorSimple デプロイ構成チェックリストをダウンロードする](http://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>デプロイの前提条件
ここでは、StorSimple デバイス マネージャー サービスと StorSimple デバイスの構成の前提条件について説明します。

### <a name="for-the-storsimple-device-manager-service"></a>StorSimple デバイス マネージャー サービスの場合
開始する前に次の点を確認します。

* アクセスの資格情報を持つ Microsoft アカウントがあること。
* アクセスの資格情報を持つ Microsoft Azure のストレージ アカウントがあること。
* Microsoft Azure サブスクリプションが StorSimple デバイス マネージャー サービスに対して有効である。 サブスクリプションは [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)を通じて購入する必要があります。
* PuTTY などのターミナル エミュレーション ソフトウェアにアクセスできる。

### <a name="for-the-device-in-the-datacenter"></a>データセンターのデバイスの場合
デバイスを構成する前に次の点を確認します。

* 以下のページの説明に従って、デバイスが完全に開梱され、ラックに取り付けられ、電源、ネットワーク、およびシリアル アクセスのケーブルが完全に接続されている。
  
  * [8100 デバイスの開梱、ラック取り付け、ケーブル接続](storsimple-8100-hardware-installation.md)
  * [8600 デバイスの開梱、ラック取り付け、ケーブル接続](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>データセンターのネットワークの場合
開始する前に次の点を確認します。

* 「 [StorSimple デバイスのネットワーク要件](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)」で説明するとおり、データセンターのファイアウォールでポートを開くと、iSCSI とクラウドのトラフィックが許可されます。

## <a name="step-by-step-deployment"></a>デプロイの手順
StorSimple デバイスをデータセンター内にデプロイするには、次の詳細な手順を実行します。

## <a name="step-1-create-a-new-service"></a>手順 1. 新しいサービスを作成する
StorSimple デバイス マネージャー サービスでは、複数の StorSimple デバイスを管理できます。 StorSimple デバイス マネージャー サービスの新しいインスタンスを作成するには、次の手順を実行します。

[!INCLUDE [storsimple-8000-create-new-service-gov](../../includes/storsimple-8000-create-new-service-gov.md)]

> [!IMPORTANT]
> サービスでストレージ アカウントの自動作成を有効にしていない場合は、サービスの作成が完了してから、1 つ以上のストレージ アカウントを作成する必要があります。 このストレージ アカウントは、ボリューム コンテナーを作成するときに使用します。
> 
> * ストレージ アカウントを自動的に作成していない場合は、「 [サービスの新しいストレージ アカウントを構成する](#configure-a-new-storage-account-for-the-service) 」に移動して詳細な手順をご確認ください。
> * ストレージ アカウントの自動作成を有効にしている場合は、「 [手順 2. サービス登録キーを取得する](#step-2-get-the-service-registration-key)」に進みます。


## <a name="step-2-get-the-service-registration-key"></a>手順 2. サービス登録キーを取得する
StorSimple デバイス マネージャー サービスが稼働したら、サービス登録キーを取得する必要があります。 このキーを使用して StorSimple デバイスを登録し、サービスに接続します。

Government ポータルで、次の手順を実行します。

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>手順 3. StorSimple 用 Windows PowerShell を使用してデバイスを構成し登録する
次の手順の説明に従い、StorSimple 用 Windows PowerShell を使用して StorSimple デバイスの初期セットアップを完了します。 この手順を完了するには、ターミナル エミュレーション ソフトウェアを使用する必要があります。 詳細については、「 [PuTTY を使用してデバイスのシリアル コンソールに接続する](#use-putty-to-connect-to-the-device-serial-console)」を参照してください。

[!INCLUDE [storsimple-8000-configure-and-register-device-gov](../../includes/storsimple-8000-configure-and-register-device-gov-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>手順 4. デバイスの最小セットアップを完了する
StorSimple デバイスの最小構成を完了するには、次の手順を実行する必要があります。

* デバイスの表示名を指定します。
* デバイスのタイム ゾーンを設定します。
* 両方のコントローラーに固定の IP アドレスを割り当てます。

デバイスの最小セットアップを完了するには、Azure Government ポータルで次の手順を実行します。

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

## <a name="step-5-create-a-volume-container"></a>手順 5. ボリューム コンテナーを作成する
ボリューム コンテナーでは、そこに含まれるすべてのボリュームのストレージ アカウント、帯域幅、暗号化が設定されています。 StorSimple デバイス上のボリュームのプロビジョニングを開始する前に、ボリューム コンテナーを作成する必要があります。

ボリューム コンテナーを作成するには、Government ポータルで次の手順を実行します。

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>手順 6. ボリュームを作成する
ボリューム コンテナーを作成したら、サーバーの StorSimple デバイスでストレージ ボリュームをプロビジョニングできます。 ボリュームを作成するには、Government ポータルで次の手順を実行します。

> [!IMPORTANT]
> StorSimple デバイス マネージャーは、仮想プロビジョニングされたボリュームのみを作成できます。  ただし、部分的にプロビジョニングされたボリュームは作成できません。

[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>手順 7. ボリュームをマウント、初期化、フォーマットする
Windows Server ホスト上で次の手順を実行します。

> [!IMPORTANT]
> * StorSimple ソリューションの高可用性を実現するため、iSCSI を構成する前に、ホスト サーバーで MPIO を構成することをお勧めします (省略可能)。 ホスト サーバーに MPIO を構成すると、サーバーはリンク、ネットワーク、またはインターフェイスの障害を許容できるようになります。
> * Windows Server ホストでの MPIO と iSCSI のインストールと構成の手順については、「 [StorSimple デバイスの MPIO の構成](storsimple-configure-mpio-windows-server.md)」をご覧ください。 このページには、StorSimple ボリュームのマウント、初期化、フォーマットを実行する手順も記載されています。
> * Linux ホストでの MPIO と iSCSI のインストールと構成の手順については、「 [StorSimple Linux ホストの MPIO の構成](storsimple-configure-mpio-on-linux.md)

MPIO を構成しない場合は、次の手順に従い、Windows Server ホストに StorSimple ボリュームをマウント、初期化、フォーマットします。

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>手順 8. バックアップを取得する
バックアップにより、特定の時点のボリュームを保護し、復元時間を最小限に抑えながら回復性を向上させることができます。 StorSimple デバイスでは、ローカル スナップショットとクラウド スナップショットという 2 種類のバックアップを実行できます。 どちらの種類のバックアップも、**[スケジュール設定]** または **[手動]** で実行できます。

スケジュールされたバックアップを作成するには、Government ポータルで次の手順を実行します。

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

手動バックアップはいつでも実行できます。 手順については、「 [手動バックアップの作成](#create-a-manual-backup)」を参照してください。

## <a name="configure-a-new-storage-account-for-the-service"></a>サービスの新しいストレージ アカウントを構成する
これは省略可能な手順で、サービスでストレージ アカウントの自動作成を有効にしていない場合のみ実行する必要があります。 StorSimple ボリューム コンテナーを作成するには、Microsoft Azure ストレージ アカウントが必要です。

別のリージョンで Azure のストレージ アカウントを作成する必要がある場合の詳細な手順については、「 [Azure ストレージ アカウントについて](../storage/common/storage-create-storage-account.md) 」を参照してください。

Government ポータルの **[StorSimple デバイス マネージャー サービス]** ページで次の手順を実行します。

[!INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>PuTTY を使用してデバイスのシリアル コンソールに接続する
StorSimple 用 Windows PowerShell に接続するには、PuTTY などのターミナル エミュレーション ソフトウェアを使用する必要があります。 シリアル コンソールから直接デバイスにアクセスするか、またはリモート コンピューターから telnet セッションを開いて PuTTY を使用できます。

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>更新プログラムをスキャンして適用する
デバイスの更新には、数時間かかることがあります。 最新の更新プログラムをインストールする詳しい手順については、[Update 4 のインストール](storsimple-8000-install-update-4.md)に関するページを参照してください。

## <a name="get-the-iqn-of-a-windows-server-host"></a>Windows Server ホストの IQN を取得する
Windows Server® 2012 を実行する Windows ホストの ISCSI 修飾名 (IQN) を取得するには、次の手順を実行します。

[!INCLUDE [Get IQN of your Windows Server host](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>手動バックアップの作成
StorSimple デバイスの 1 つのボリュームに対し、オンデマンドの手動バックアップを作成するには、Government ポータルで次の手順を実行します。

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>次の手順
* [仮想デバイス](storsimple-8000-cloud-appliance-u2.md)を構成します。
* [StorSimple デバイス マネージャー サービス](storsimple-8000-manager-service-administration.md)を使用して StorSimple デバイスを管理します。

