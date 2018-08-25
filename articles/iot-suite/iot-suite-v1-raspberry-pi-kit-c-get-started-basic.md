---
title: C を使用して Raspberry Pi を Azure IoT Suite に接続しセンサーから送信する | Microsoft Docs
description: Raspberry Pi 3 と Azure IoT Suite に対応した Microsoft Azure IoT スタート キットを使用します。 C を使用して Raspberry Pi をリモート監視ソリューションに接続し、センサーから受信したテレメトリをクラウドに送信して、ソリューション ダッシュボードから呼び出されたメソッドに応答します。
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 635eb9d4e85eaf43dc83f2bd5a0d0f7c32620d98
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611309"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-telemetry-from-a-real-sensor-using-c"></a>C を使用して Raspberry Pi 3 をリモート監視ソリューションに接続し、センサーから受信したテレメトリを送信する

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

このチュートリアルでは、Raspberry Pi 3 に対応した Microsoft Azure IoT スタート キットを使用して以下を行う方法について説明します。 このチュートリアルでは以下を使用します。

- サンプル デバイスを実装するための Raspbian OS、C プログラミング言語、C 用 Microsoft Azure IoT SDK。
- クラウド ベース バックエンドとしての IoT Suite リモート監視構成済みソリューション。

## <a name="overview"></a>概要

このチュートリアルでは、次の手順を実行します。

- リモート監視構成済みソリューションのインスタンスを Azure サブスクリプションにデプロイします。 この手順では、複数の Azure サービスが自動的にデプロイおよび構成されます。
- お使いのコンピューターやリモート監視ソリューションと通信するようにデバイスとセンサーを設定します。
- サンプル デバイス コードを更新することでリモート監視ソリューションに接続し、テレメトリを送信してソリューションのダッシュボードに表示できるようにします。

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> リモート監視ソリューションによって、Azure サブスクリプションの Azure サービスのセットがプロビジョニングされます。 デプロイによって現実のエンタープライズ アーキテクチャが反映されます。 不要な Azure 使用料金が発生しないよう、作業が終わったら azureiotsuite.com にある構成済みソリューションのインスタンスを削除します。 構成済みソリューションがもう一度必要になった場合は、簡単に再作成できます。 リモート監視ソリューション実行中の使用料金を削減する方法の詳細については、「[Configuring Azure IoT Suite preconfigured solutions for demo purposes (デモの目的で Azure IoT Suite 構成済みソリューションを構成する)][lnk-demo-config]」をご覧ください。

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>サンプルをダウンロードして構成する

Raspberry Pi のリモート監視クライアント アプリケーションをダウンロードして構成できます。

### <a name="clone-the-repositories"></a>リポジトリの複製

まだレポジトリを複製していない場合は、Pi 上のターミナルで次のコマンドを実行して必要なレポジトリを複製します。

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
git clone --recursive https://github.com/WiringPi/WiringPi.git
```

### <a name="update-the-device-connection-string"></a>デバイスの接続文字列を更新する

次のコマンドを使用して、**nano** エディターのサンプル ソース ファイルを開きます。

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/remote_monitoring/remote_monitoring.c
```

次の行を見つけます。

```c
static const char* deviceId = "[Device Id]";
static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
```

プレースホルダーの値を、このチュートリアルの最初で作成し保存したデバイスと IoT Hub の情報に置き換えます。 変更を保存し (**Ctrl + O** キー、**Enter** キー)、エディターを終了します (**Ctrl + X** キー)。

## <a name="build-the-sample"></a>サンプルをビルドする

Raspberry Pi 上のターミナルで次のコマンドを実行して、C 用 Microsoft Azure IoT device SDK の前提条件となるパッケージをインストールします。

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Raspberry Pi 上で更新されたサンプル ソリューションのビルドができます。

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/basic/build.sh
```

Raspberry Pi 上でサンプル プログラムを実行できます。 次のコマンドを入力します。

```sh
sudo ~/cmake/remote_monitoring/remote_monitoring
```

次のサンプル出力は、Raspberry Pi 上のコマンド プロンプトに表示される出力の例です。

![Raspberry Pi アプリからの出力][img-raspberry-output]

**Ctrl + C** キーを押してプログラムを終了します (終了のタイミングは任意)。

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry.md)]

## <a name="next-steps"></a>次の手順

Azure IoT のその他のサンプルとドキュメントについては、「[Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot/)」をご覧ください。

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-basic/appoutput.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
