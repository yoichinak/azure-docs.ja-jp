---
title: Raspberry Pi から クラウドへ (C) - Raspberry Pi の Azure IoT Hub への接続 | Microsoft Docs
description: このチュートリアルでは、Raspberry Pi を Azure IoT Hub に接続し、Raspberry Pi で Azure クラウド プラットフォームにデータを送信する方法について説明します。
author: rangv
manager: ''
keywords: azure iot raspberry pi, raspberry pi iot hub, raspberry pi でクラウドにデータを送信する raspberry pi からクラウドへ
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 15fd21a25648925d8f78645c49e4127271a9352d
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187804"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Raspberry Pi の Azure IoT Hub への接続 (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

このチュートリアルでは、まず Raspbian を実行する Raspberry Pi の操作の基礎について説明します。 次に、[Azure IoT Hub](about-iot-hub.md) を使って、デバイスをクラウドにシームレスに接続する方法について説明します。 Windows 10 IoT Core サンプルについては、[Windows デベロッパー センター](http://www.windowsondevices.com/)を参照してください。

キットをお持ちでない場合は、 [Raspberry Pi オンライン シミュレーター](iot-hub-raspberry-pi-web-simulator-get-started.md)をお試しください。 または、[こちら](https://azure.microsoft.com/develop/iot/starter-kits)で新しいキットを購入してください。

## <a name="what-you-do"></a>作業内容

* IoT Hub を作成します。
* Pi のデバイスを IoT Hub に登録します。
* Raspberry Pi をセットアップします。
* Pi でサンプル アプリケーションを実行し、センサー データを IoT Hub に送信します。

作成した IoT Hub に Raspberry Pi を接続します。 次に、BME280 センサーから気温と湿度のデータを収集するために、Pi のサンプル アプリケーションを実行します。 最後に、センサー データを IoT Hub に送信します。

## <a name="what-you-learn"></a>学習内容

* Azure IoT Hub を作成し、新しいデバイス接続文字列を取得する方法。
* Pi と BME280 センサーを接続する方法。
* Pi のサンプル アプリケーションを実行して、センサー データを収集する方法。
* センサー データを IoT Hub に送信する方法。

## <a name="what-you-need"></a>必要なもの

![必要なもの](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Raspberry Pi 2 または Raspberry Pi 3 ボード。
* 有効な Azure サブスクリプション Azure アカウントがない場合は、[無料試用版の Azure アカウント](https://azure.microsoft.com/free/)を数分で作成できます。
* モニター、USB キーボード、Pi に接続するマウス。
* Mac PC または Windows か Linux をインストールした PC。
* インターネット接続。
* 16 GB 以上の microSD カード。
* USB-SD アダプターまたは microSD カード (microSD カードに オペレーティング システム イメージを書き込むため)。
* 5V 2A の AC アダプターと約 2 m の micro USB ケーブル。

省略可能な品目を次に示します。

* 温度、気圧、および湿度用の組み立て済み Adafruit BME280 センサー。
* ブレッドボード
* 6 つの F/M ジャンパー ワイヤ。
* 拡散型 10 mm LED。


> [!NOTE] 
サンプル コードはセンサー データのシミュレーションをサポートしているため、これらの品目は省略可能です。


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>Raspberry Pi のセットアップ

### <a name="install-the-raspbian-operating-system-for-pi"></a>Pi の Raspbian オペレーティング システムのインストール

microSD カードに Raspbian イメージをインストールするための準備をします。

1. Raspbian をダウンロードします。
   1. [Raspbian Jessie with Desktop](https://www.raspberrypi.org/downloads/raspbian/) (.zip ファイル) をダウンロードします。
   1. コンピューター上のフォルダーに Raspbian イメージを抽出します。
1. microSD カードに Raspbian をインストールします。
   1. [Etcher SD カード書き込みユーティリティをダウンロードしてインストールします](https://etcher.io/)。
   1. Etcher を実行し、手順 1. で抽出した Raspbian イメージを選択します。
   1. microSD カード ドライブを選択します。 適切なドライブが既に選択されている場合があります。
   1. [Flash (フラッシュ)] をクリックして、microSD カードに Raspbian をインストールします。
   1. インストールが完了したら、コンピューターから microSD カードを取り出します。 Etcher では完了時に microSD カードを自動的に取り出すか、マウント解除するため、microSD カードを直接取り出しても問題ありません。
   1. microSD カードを Pi に挿入します。

### <a name="enable-ssh-and-spi"></a>SSH および SPI の有効化

1. Pi にモニター、キーボード、マウスを接続し、Pi を起動してから、`pi` をユーザー名として、`raspberry` をパスワードとして使用して Raspbian にログインします。
1. Raspberry アイコン > **[Preferences]\(設定)** > **[Raspberry Pi Configuration]\(Raspberry Pi 構成)** の順にクリックします。

   ![[Raspbian Preferences] (Raspbian 設定)メニュー](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. **[Interfaces]** タブで、**[SPI]** と **[SSH]** を **[Enable]** に設定し、**[OK]** をクリックします。 物理センサーがなく、シミュレートされたセンサー データを使用する場合は、この手順は省略可能です。

   ![Raspberry Pi で SPI と SSH を有効にする](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
SSH および SPI を有効にする場合は、[raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) および [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md) でさらに参考ドキュメントを見つけることができます。

### <a name="connect-the-sensor-to-pi"></a>センサーを Pi に接続する

ブレッドボードとジャンパー ワイヤを使用して、次のように LED と BME280 を Pi に接続します。 センサーがない場合は、[このセクションをスキップ](#connect-pi-to-the-network)します。

![Raspberry Pi とセンサーの接続](media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

BME280 センサーでは、温度と湿度のデータを収集できます。 また、デバイスとクラウドとの間で通信が行われると、LED が点滅します。 

センサーの各ピンで、次のように接続します。

| 開始 (センサーと LED)     | 終了 (ボード)            | ケーブルの色   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (ピン 5G)         | GPIO 4 (ピン 7)         | 白いケーブル   |
| LED GND (ピン 6G)         | GND (ピン 6)            | 黒いケーブル   |
| VDD (ピン 18F)            | 3.3V PWR (ピン 17)      | 白いケーブル   |
| GND (ピン 20F)            | GND (ピン 20)           | 黒いケーブル   |
| SCK (ピン 21F)            | SPI0 SCLK (ピン 23)     | オレンジ色のケーブル  |
| SDO (ピン 22F)            | SPI0 MISO (ピン 21)     | 黄色のケーブル  |
| SDI (ピン 23F)            | SPI0 MOSI (ピン 19)     | 緑のケーブル   |
| CS (ピン 24F)             | SPI0 CS (ピン 24)       | 青いケーブル    |

クリックすると [Raspberry Pi 2 & 3 Pin mappings](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) が表示されて参照できます。

BME280 が正常に Raspberry Pi に接続されると、下の図のようになります。

![接続された Pi と BME280](media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Pi のネットワークへの接続

micro USB ケーブルと AC アダプターを使って、Pi の電源を入れます。 イーサネット ケーブルを使用して Pi を有線ネットワークに接続するか、[Raspberry Pi Foundation の手順](https://www.raspberrypi.org/learning/software-guide/wifi/)に従って、Pi をワイヤレス ネットワークに接続します。 Pi がネットワークに正常に接続されたら、[Pi の IP アドレス](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)をメモしておく必要があります。

![接続先の有線ネットワーク](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Pi でのサンプル アプリケーションの実行

### <a name="login-to-your-raspberry-pi"></a>Raspberry Pi にログインする

1. 次の SSH クライアントのいずれかを使用して、ホスト コンピューターから Raspberry Pi に接続します。
   
   **Windows ユーザー**
   1. Windows 版の [PuTTY](http://www.putty.org/) をダウンロードしてインストールします。 
   1. Pi の IP アドレスをホスト名 (または IP アドレス) セクションにコピーし、接続の種類として SSH を選択します。
   
   ![PuTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac ユーザーおよび Ubuntu ユーザー**
   
   Ubuntu または macOS に組み込まれている SSH クライアントを使用します。 SSH を使用して Pi を接続するには、`ssh pi@<ip address of pi>` を実行する必要がある場合があります。
   > [!NOTE] 
   既定のユーザー名は `pi` で、パスワードは`raspberry` です。


### <a name="configure-the-sample-application"></a>サンプル アプリケーションを構成する

1. 次のコマンドを実行して、サンプル アプリケーションを複製します。

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. セットアップ スクリプトを実行します。

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > **BME280 が物理的にない**場合は、コマンド ライン パラメーターとして "--simulated-data" を使用して、温度と湿度のデータをシミュレートできます。 `sudo ./setup.sh --simulated-data`

### <a name="build-and-run-the-sample-application"></a>サンプル アプリケーションをビルドして実行する

1. 次のコマンドを実行して、サンプル アプリケーションをビルドします。

   ```bash
   cmake . && make
   ```
   ![ビルド出力](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. 次のコマンドを実行して、サンプル アプリケーションを実行します。

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   デバイスの接続文字列をコピーして貼り付け、必ず一重引用符で囲んでください。


IoT Hub に送信されるセンサー データとメッセージを示す次の出力が表示されます。

![出力 - Raspberry Pi から IoT Hub に送信されるセンサー データ](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>次の手順

サンプル アプリケーションを実行してセンサー データを収集し、IoT Hub に送信します。 コマンド ライン インターフェイスで Raspberry Pi にメッセージを送信したり、Raspberry Pi から IoT Hub に送信されたメッセージを表示したりする方法については、[iothub-explorer を使用したクラウド デバイス メッセージングの管理に関するチュートリアル](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)を参照してください。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
