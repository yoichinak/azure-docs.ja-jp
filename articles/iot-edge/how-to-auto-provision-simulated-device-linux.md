---
title: DPS を使用して Azure IoT Edge の自動プロビジョニングを行う - Linux | Microsoft Docs
description: Linux VM 上のシミュレートされた TPM を使用して Azure IoT Edge 用のデバイスのプロビジョニングをテストします
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9609aab6c70bc0c2755de142023bd26e7417987a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2018
ms.locfileid: "37347858"
---
# <a name="create-and-provision-an-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Linux 仮想マシン上で仮想 TPM を持つ Edge デバイスの作成とプロビジョニングを行う

Azure IoT Edge デバイスは、[Device Provisioning Service](../iot-dps/index.yml) を使用して、Edge に対応していないデバイスと同じように自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、「[自動プロビジョニングの概念](../iot-dps/concepts-auto-provisioning.md)」を読んでから先に進んでください。 

この記事では、シミュレートされた Edge デバイスの自動プロビジョニングを次の手順でテストする方法を示します。 

* ハードウェアのセキュリティ用のシミュレートされたトラステッド プラットフォーム モジュール (TPM) がある Hyper-V 内に Linux 仮想マシン (VM) を作成する。
* IoT Hub Device Provisioning Service (DPS) のインスタンスを作成する。
* デバイスの個別登録を作成する。
* IoT Edge ランタイムをインストールし、デバイスを IoT Hub に接続する。

この記事の手順は、テスト目的のものです。

## <a name="prerequisites"></a>前提条件

* [Hyper-V 対応の](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) Windows 開発マシン。 この記事では、Ubuntu Server VM を実行中の Windows 10 を使用します。 
* アクティブな IoT Hub。 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>仮想 TPM がある Linux 仮想マシンを作成する

このセクションでは、シミュレートされた TPM を持っている Hyper-V 上に新しい Linux 仮想マシンを作成し、それを使用して IoT Edge の自動プロビジョニングがどのように機能するかをテストできるようにします。 

### <a name="create-a-virtual-switch"></a>仮想スイッチを作成する

仮想スイッチを使用して、仮想マシンを物理ネットワークに接続できます。

1. Windows マシンで Hyper-V を開きます。 

2. **[アクション]** メニューの **[仮想スイッチ マネージャー]** を選択します。 

3. **[外部]** 仮想スイッチを選択し、**[仮想スイッチの作成]** を選択します。 

4. 新しい仮想スイッチに名前を付けます (たとえば **EdgeSwitch**)。 接続の種類が **[外部ネットワーク]** に設定されていることを確認し、**[OK]** を選択します。

5. ネットワーク接続が中断される可能性があることを警告するポップアップが表示されます。 **[はい]** を選択して続行します。 

新しい仮想スイッチの作成中にエラーが発生した場合は、イーサネット アダプターを使用している他のスイッチがないこと、および同じ名前を使用している他のスイッチがないことを確認します。 

### <a name="create-virtual-machine"></a>仮想マシンを作成する

1. 仮想マシンで使用するディスク イメージ ファイルをダウンロードし、ローカルに保存します。 例: [Ubuntu server](https://www.ubuntu.com/download/server) 

2. HYPER-V をもう一度開きます。 **[アクション]** メニューの **[新規]** > **[仮想マシン]** を選択します。

3. **新しい仮想マシン ウィザード**を、次の構成で完了します。

   1. **世代の指定**: **[世代 2]** を選択します。
   2. **ネットワークの構成**: **[接続]** の値を、前のセクションで作成した仮想スイッチに設定します。 
   3. **インストール オプション**: **[Install an operating system from a bootable image file]\(起動可能イメージ ファイルからオペレーティング システムをインストールする\)** を選択し、ローカルに保存したディスク イメージ ファイルを参照します。

新しい VM の作成には数分かかる可能性があります。 

### <a name="enable-virtual-tpm"></a>仮想 TPM を有効にする

1. VM が作成されたら、その設定を開きます。 
2. **[セキュリティ]** に移動します。 
3. **[セキュア ブートを有効にする]** をオフにします。
4. **[トラステッド プラットフォーム モジュールを有効にする]** をオンにします。 
5. **[OK]** をクリックします。  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>仮想マシンを起動し、TPM データを収集する

仮想マシンで、デバイスの**登録 ID** と**保証キー**を取得するために使用できる C SDK ツールをビルドします。 

1. VM を起動し、それに接続してインストール プロセスを完了します。 

2. VM で、[Linux 開発環境の設定](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)に関するセクションの手順に従って、Azure IoT device SDK for C をインストールしてビルドします。 

3. 次のコマンドを実行して、デバイスのプロビジョニング情報を取得する C SDK ツールをビルドします。 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

3. **登録 ID**と**保証キー**の値をコピーします。 これらの値を使用して、DPS でのデバイスの個別登録を作成します。 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service を設定する

Azure 内に IoT Hub Device Provisioning Service の新しいインスタンスを作成し、それを自分の IoT Hub にリンクします。 [IoT Hub DPS の設定](../iot-dps/quick-setup-auto-provision.md)に関する記事の手順に従うことができます。

Device Provisioning Service を実行した後、概要ページから **[ID スコープ]** の値をコピーします。 この値は、IoT Edge ランタイムを構成するときに使用します。 

## <a name="create-a-dps-enrollment"></a>DPS の登録を作成する

仮想マシンからプロビジョニング情報を取得し、それを使用して Device Provisioning Service 内に個別登録を作成します。 

DPS 内に登録を作成するときに、**デバイス ツインの初期状態**を宣言する機会があります。 デバイス ツインでは、ソリューションで必要な任意のメトリック (リージョン、環境、場所、デバイスの種類など) によってデバイスをグループ化するためのタグを設定できます。 これらのタグは、[自動展開](how-to-deploy-monitor.md)を作成するために使用されます。 


1. [Azure Portal](https://portal.azure.com) で、IoT Hub Device Provisioning Service インスタンスに移動します。 

2. **[設定]** の下の **[登録の管理]** を選択します。 

3. **[Add individual enrollment]\(個別登録の追加\)** を選択し、登録を構成する次の手順を完了します。  

   1. **[メカニズム]** で **[TPM]** を選択します。 
   2. 仮想マシンからコピーした**保証キー**と**登録 ID**を挿入します。
   3. **[有効にする]** を選択して、この仮想マシンが IoT Edge デバイスであることを宣言します。 
   4. デバイスの接続先になるリンクされた **IoT Hub** を選択します。 
   5. 必要に応じて、デバイス ID を指定します。 デバイス ID を使用して、個々のデバイスをモジュール展開のターゲットにすることができます。 
   6. 必要に応じて、**[デバイス ツインの初期状態]** にタグ値を追加します。 タグを使用して、デバイス グループをモジュール展開のターゲットにすることができます。 
   7. **[保存]** を選択します。 


## <a name="install-the-iot-edge-runtime"></a>IoT Edge ランタイムをインストールする

IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 そのコンポーネントはコンテナー内で実行されるため、デバイスに追加のコンテナーを展開して、Edge でコードを実行できるようにすることができます。 IoT Edge ランタイムを仮想マシンにインストールします。 

デバイスの種類に合った記事を参照する前に、DPS の **ID スコープ**とデバイスの**登録 ID** を確認してください。 Ubuntu サーバーの例をインストールした場合は、**x64** の手順を使用してください。 IoT Edge ランタイムの構成が、手動プロビジョニングではなく、自動プロビジョニングになっていることを確認してください。 

* [Linux (x64)](how-to-install-iot-edge-linux.md)
* [Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>IoT Edge に TPM へのアクセス権を付与する

IoT Edge ランタイムがデバイスを自動的にプロビジョニングするには、TPM アクセスにアクセスする必要があります。 

次の手順を使用して、TPM へのアクセス権を付与します。 または、systemd 設定を上書きして *iotedge* サービスをルートとして実行できるようにすることで、同じことを実現できます。 

1. デバイスで、TPM ハードウェア モジュールへのファイル パスを探してローカル変数として保存します。 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. IoT Edge ランタイムに tpm0 へのアクセス権を付与する新しいルールを作成します。 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. 規則ファイルを開きます。 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. 次のアクセス情報を規則ファイルにコピーします。 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. ファイルを保存して終了します。 

6. udev system をトリガーして、新しい規則を評価します。 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. 規則が正常に適用されたことを確認します。

   ```bash
   ls -l /dev/tpm0
   ```

   成功の出力は次のようになります。

   ```output
   crw------- 1 root root 10, 224 Jun 28 22:34 /dev/tpm0
   ```

8. IoT Edge ランタイム上書きファイルを開きます。 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. TPM 環境変数を確立する次のコードを追加します。

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

9. 上書きが成功したことを確認します。

   ```bash
   sudo systemctl cat iotedge.service
   ```

   成功の出力では、**iotedge** の既定のサービス変数が表示された後、**override.conf** に設定した環境変数が表示されます。 

12. 設定を再度読み込みます。

   ```bash
   sudo systemctl daemon-reload
   ```

## <a name="restart-the-iot-edge-runtime"></a>IoT Edge ランタイムを再起動する

デバイスで行ったすべての構成の変更が反映されるように、IoT Edge ランタイムを再起動します。 

   ```bash
   sudo systemctl restart iotedge
   ```

IoT Edge ランタイムが実行されていることを確認します。 

   ```bash
   sudo systemctl status iotedge
   ```

プロビジョニング エラーが発生した場合は、構成の変更がまだ有効になっていない可能性があります。 IoT Edge を再起動をもう一度試してください。 

   ```bash
   sudo systemctl daemon-reload
   ```
   
または、変更が新たな開始時に有効になる場合は、仮想マシンの再起動を試してください。 

## <a name="verify-successful-installation"></a>インストールの成功を確認する

ランタイムが正常に起動した場合は、IoT Hub に移動して、新しいデバイスが自動的にプロビジョニングされ、IoT Edge モジュールを実行する準備ができていることを確認できます。 

IoT Edge デーモンの状態を確認します。

```cmd/sh
systemctl status iotedge
```

デーモンのログを調べます。

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

実行中のモジュールを一覧表示します。

```cmd/sh
iotedge list
```


## <a name="next-steps"></a>次の手順

Device Provisioning Service の登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-monitor-cli.md)」を参照してください。
