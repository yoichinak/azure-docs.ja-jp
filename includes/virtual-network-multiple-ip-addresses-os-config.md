---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: ec1727926f6dbfeead9932004715a8bb1dfbb0cd
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2018
ms.locfileid: "36964537"
---
## <a name="os-config"></a>VM オペレーティング システムに IP アドレスを追加する

複数のプライベート IP アドレスを構成して作成した VM に接続し、サインインします。 VM に追加したプライベート IP アドレスは、プライマリも含め、すべて手動で追加する必要があります。 お使いの VM オペレーティング システムに応じて手順を実行します。

### <a name="windows"></a>Windows

1. コマンド プロンプトで、「 *ipconfig /all*」と入力します。  *プライマリ* の IP アドレス (DHCP 経由) のみを表示できます。
2. コマンド プロンプトで「*ncpa.cpl*」と入力して、**[ネットワーク接続]** ウィンドウを開きます。
3. 適切なアダプターの **[ローカル エリア接続]** のプロパティを開きます。
4. インターネット プロトコル バージョン 4 (IPv4) をダブルクリックします。
5. **[次の IP アドレスを使う]** を選択して、次の値を入力します。

    * **IP アドレス**: *プライマリ* のプライベート IP アドレスを入力します。
    * **サブネット マスク**: 自分のサブネットに基づいて設定します。 たとえば、たとえば、サブネットが/24 サブネットであれば、サブネット マスクは 255.255.255.0 になります。
    * **デフォルト ゲートウェイ**: サブネット内の最初の IP アドレスです。 サブネットが 10.0.0.0/24 の場合、ゲートウェイの IP アドレスは 10.0.0.1 になります。
    * **[次の DNS サーバーのアドレスを使う]** を選択して、次の値を入力します。
        * **[優先 DNS サーバー]**: 独自の DNS サーバーを使用していない場合は、「168.63.129.16」と入力します。  独自の DNS サーバーを使用している場合は、そのサーバーの IP アドレスを入力します。
    * **[詳細]** ボタンを選択して、他の IP アドレスを追加します。 前の手順で Azure ネットワーク インターフェイスに追加した、各セカンダリ プライベート IP アドレスを、Azure ネットワーク インターフェイスに割り当てられたプライマリ IP アドレスが割り当てられている Windows ネットワーク インターフェイスに追加します。

        仮想マシンのオペレーティング システム内で Azure の仮想マシンに割り当てられているパブリック IP アドレスを手動で割り当てないでください。 オペレーティング システム内で IP アドレスを手動で設定する場合は、Azure [ネットワーク インターフェイス](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings)に割り当てられているプライベート IP アドレスと同じアドレスであることを確認してください。そうしないと、仮想マシンへの接続が失われる可能性があります。 詳細については、[プライベート IP アドレス](../articles/virtual-network/virtual-network-network-interface-addresses.md#private)設定に関するページを参照してください。 オペレーティング システム内で Azure パブリック IP アドレスを割り当てないでください。

    * **[OK]** をクリックして TCP/IP 設定を閉じ、もう一度 **[OK]** をクリックしてアダプター設定を閉じます。 これで RDP 接続が再確立されます。

6. コマンド プロンプトで、「 *ipconfig /all*」と入力します。 追加したすべての IP アドレスが表示され、DHCP が無効になります。
7. Azure のプライマリ IP 構成のプライベート IP アドレスが、Windows 用プライマリ IP アドレスとして使用されるように Windows を構成します。 詳細については、「[No Internet access from Azure Windows VM that has multiple IP addresses (複数の IP アドレスを持つ Azure Windows VM からインターネット アクセスできない)](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse)」を参照してください。 

### <a name="validation-windows"></a>検証 (Windows)

関連付けたパブリック IP を使用してセカンダリ IP 構成からインターネットに接続できることを確認するには、上記の手順を使用してパブリック IP を正しく追加した後で、次のコマンドを使用します。

```bash
ping -S 10.0.0.5 hotmail.com
```
>[!NOTE]
>セカンダリ IP 構成でインターネットに ping を実行できるのは、その構成にパブリック IP アドレスが関連付けられている場合だけです。 プライマリ IP 構成では、インターネットに ping を実行するためにパブリック IP アドレスは必要ありません。

### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. ターミナル ウィンドウを開きます。
2. 自身がルート ユーザーになっていることを確認します。 ルート ユーザーでない場合は、次のコマンドを入力します。

    ```bash
    sudo -i
    ```

3. ネットワーク インターフェイスの構成ファイルを更新します (‘eth0’ と仮定)。

    * DHCP の既存の行アイテムを保持します。 プライマリ IP アドレスが以前と同じ構成のまま維持されます。
    * 次のコマンドを使用して、追加の静的 IP アドレスの構成を追加します。

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    .cfg ファイルが表示されます。
4. ファイル を開きます。 ファイルの末尾に次の行が表示されます。

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. このファイルの行の最後に、次の行を追加します。

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    netmask <your subnet mask>
    ```

6. 次のコマンドを使用して、ファイルの内容を保存します。

    ```bash
    :wq
    ```

7. 次のコマンドを使用して、ネットワーク インターフェイスをリセットします。

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > リモート接続を使用する場合は、同じ行で ifdown と ifup の両方を実行します。
    >

8. 次のコマンドを使用して、IP アドレスがネットワーク インターフェイスに追加されたことを確認します。

    ```bash
    ip addr list eth0
    ```

    追加した IP アドレスが、リストの一部として表示されます。

### <a name="linux-red-hat-centos-and-others"></a>Linux (Red Hat、CentOS、その他)

1. ターミナル ウィンドウを開きます。
2. 自身がルート ユーザーになっていることを確認します。 ルート ユーザーでない場合は、次のコマンドを入力します。

    ```bash
    sudo -i
    ```

3. パスワードを入力し、画面の指示に従います。 ルート ユーザーになったら、次のコマンドを使用して、ネットワーク スクリプト フォルダーに移動します。

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. 次のコマンドを使用して、関連する ifcfg ファイルをリストアップします。

    ```bash
    ls ifcfg-*
    ```

    ファイルのうちの 1 つに *ifcfg-eth0* があります。

5. IP アドレスを追加するには、次のように構成ファイルを作成します。 IP 構成ごとに 1 つのファイルを作成する必要があることに注意してください。

    ```bash
    touch ifcfg-eth0:0
    ```

6. 次のコマンドを使って、*ifcfg-eth0:0* ファイルを開きます。

    ```bash
    vi ifcfg-eth0:0
    ```

7. 次のコマンドを使って、ファイルに内容 (ここでは *eth0:0*) を追加します。 目的の IP アドレスに基づいて情報を更新してください。

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. 次のコマンドを使用して、ファイルの内容を保存します。

    ```bash
    :wq
    ```

9. ネットワーク サービスを再起動し、次のコマンドを実行して、変更が成功したかどうかを確認します。

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    返されるリストに、追加した IP アドレス「 *eth0:0*」が表示されることを確認します。

### <a name="validation-linux"></a>検証 (Linux)

関連付けたパブリック IP を使用してセカンダリ IP 構成からインターネットに接続できることを確認するには、次のコマンドを使用します。

```bash
ping -I 10.0.0.5 hotmail.com
```
>[!NOTE]
>セカンダリ IP 構成でインターネットに ping を実行できるのは、その構成にパブリック IP アドレスが関連付けられている場合だけです。 プライマリ IP 構成では、インターネットに ping を実行するためにパブリック IP アドレスは必要ありません。

Linux VM の場合、セカンダリ NIC からの送信接続を検証しようとしたときに、適切なルートの追加が必要になることがあります。 これを行うには多くの方法があります。 Linux ディストリビューションに応じて適切なドキュメントを参照してください。 これを行う方法の 1 つを以下に示します。

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- 必ず以下の置き換えを行ってください。
    - **10.0.0.5** を、パブリック IP アドレスが関連付けられているプライベート IP アドレスに
    - **10.0.0.1** をデフォルト ゲートウェイに
    - **eth2** をセカンダリ NIC の名前に
