---
title: シミュレートされた X.509 デバイスを Java を使用して Azure IoT Hub にプロビジョニングする | Microsoft Docs
description: Azure クイックスタート - IoT Hub Device Provisioning Service 対応の Java デバイス SDK を使用して、シミュレートされた X.509 デバイスを作成してプロビジョニングする
author: wesmc7777
ms.author: wesmc
ms.date: 04/09/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 0c5eefbd6d7758ad2a7640a1fbff3435fcd1d315
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091699"
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service 対応の Java デバイス SDK を使用して、シミュレートされた X.509 デバイスを作成してプロビジョニングする
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

以下の手順では、Windows OS を実行する開発マシン上で X.509 デバイスをシミュレートすると共に、コード サンプルを使って、そのシミュレートされたデバイスを Device Provisioning Service および IoT ハブに接続する方法について説明します。 

自動プロビジョニングの処理に慣れていない場合は、「[自動プロビジョニングの概念](concepts-auto-provisioning.md)」も確認してください。 また、先に進む前に、[Azure Portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するページの手順も済ませておいてください。 

## <a name="prepare-the-environment"></a>環境の準備 

1. [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) がマシンにインストールされていることを確認します。

2. [Maven](https://maven.apache.org/install.html) をダウンロードし、インストールします。

3. マシンに Git がインストールされ、コマンド ウィンドウからアクセスできる環境変数に追加されていることを確認します。 **Git Bash** (ローカル Git リポジトリと対話する際に使用するコマンドライン アプリ) など、インストールする各種 `git` ツールの最新バージョンについては、[Software Freedom Conservancy の Git クライアント ツール](https://git-scm.com/download/)に関するページを参照してください。 

4. コマンド プロンプトを開きます。 デバイス シミュレーション コード サンプルの GitHub リポジトリを複製します。
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. ルート `azure-iot-sdk-`java` ディレクトリに移動し、プロジェクトをビルドして、必要なパッケージすべてをダウンロードします。
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. 証明書ジェネレーター プロジェクトに移動し、プロジェクトをビルドします。 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>自己署名 X.509 デバイス証明書と個々の登録エントリを作成する

このセクションでは、自己署名 X.509 証明書を使用します。次の点に留意することが重要です。

* 自己署名証明書はテスト目的専用であるため、運用環境では使用しないでください。
* 自己署名証明書の既定の有効期限は 1 年間です。

[Azure IoT SDK for Java](https://github.com/Azure/azure-iot-sdk-java.git) のサンプル コードを使用して、シミュレートされたデバイスの個々の登録エントリで使用する証明書を作成します。


1. 前の手順のコマンド プロンプトを使用して `target` フォルダ―に移動し、前の手順で作成した jar ファイルを実行します。

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. _[Do you want to input common name]\(共通名を入力しますか\)_ に「**N**」と入力します。 `Client Cert` の出力の *-----BEGIN CERTIFICATE-----* から *-----END CERTIFICATE-----* までクリップボードにコピーします。

   ![個別証明書ジェネレーター](./media/java-quick-create-simulated-device-x509/individual.png)

3. Windows マシンに **_X509individual.pem_** というファイルを作成して任意のエディターで開き、クリップボードの内容をこのファイルにコピーします。 このファイルを保存して、エディターを閉じます。

4. コマンド プロンプトから _[Do you want to input Verification Code]\(確認コードを入力しますか\)_ に「**N**」と入力し、クイック スタートで後から参照できるようにプログラムの出力を開いたままにします。 その後、次のセクションで使用する `Client Cert` と `Client Cert Private Key` の値をコピーします。

5. [Azure Portal](https://portal.azure.com) にサインインし、左側のメニューの **[すべてのリソース]** をクリックして、Device Provisioning Service インスタンスを開きます。

6. Device Provisioning Service の概要ブレードで、**[Manage enrollments]\(登録の管理\)** を選択します。 **[Individual Enrollments]\(個々の登録\)** タブの上部にある **[追加]** ボタンをクリックします。 

7. **[Add enrollment]\(登録の追加\)** で、次の情報を入力します。
    - ID 構成証明の "*メカニズム*" として **[X.509]** を選択します。
    - *[Primary certificate .pem or .cer file]\(プライマリ証明書 .pem または .cer ファイル\)* の *[ファイルの選択]* をクリックし、前の手順で作成した証明書ファイル **X509individual.pem** を選択します。  
    - 必要に応じて、次の情報を入力することができます。
      - プロビジョニング サービスにリンクされた IoT ハブを選択します。
      - 一意のデバイス ID を入力します。 デバイスに名前を付ける際に機密データを含めないようにしてください。 
      - **[Initial device twin state]\(初期のデバイス ツインの状態\)** をデバイスの目的の初期構成で更新します。
   - 作業が完了したら、**[保存]** をクリックします。 

    [![X.509 構成証明の個々の登録をポータルで追加](./media/quick-create-simulated-device-x509-csharp/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     登録に成功すると、*[個々の登録]* タブの *[登録 ID]* 列に X.509 デバイスが **microsoftriotcore** と表示されます。 



## <a name="simulate-the-device"></a>デバイスをシミュレートする

1. Device Provisioning Service の概要ブレードで、**[概要]** を選択し、_ID スコープ_と _Provisioning Service のグローバル エンドポイント_をメモします。

    ![サービス情報](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. コマンド プロンプトを開きます。 Java SDK リポジトリのサンプル プロジェクト フォルダーに移動します。

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. プロビジョニング サービスと X.509 の ID 情報をコードに入力します。 これは自動プロビジョニング中、デバイス登録の前に、シミュレートされたデバイスの構成証明に使用されます。

   - ファイル `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` を編集して、前にメモした "_ID スコープ_" と "_Provisioning Service のグローバル エンドポイント_" を含めます。 また、前のセクションでメモした "_クライアント証明書_" と "_クライアント証明書の秘密キー_" も含めます。

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - 証明書と秘密キーをコピー/貼り付ける際は、次の形式を使用します。
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

4. サンプルをビルドします。 `target` フォルダーに移動し、作成した jar ファイルを実行します。

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. Azure Portal で、ご利用のプロビジョニング サービスにリンクされている IoT Hub に移動し、**[Device Explorer]** ブレードを開きます。 シミュレートされた X.509 デバイスをハブにプロビジョニングすると、そのデバイス ID が**有効**な "*状態*" として **[Device Explorer]** ブレードに表示されます。  サンプル デバイス アプリケーションを実行する前に既にブレードが開いていた場合は、必要に応じて一番上にある **[最新の情報に更新]** ボタンをクリックしてください。 

    ![IoT ハブに登録されたデバイス](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> *[Initial device twin state]\(初期のデバイス ツインの状態\)* をデバイスの登録エントリの既定値から変更した場合、デバイスはハブから目的のツインの状態をプルし、それに従って動作することができます。 詳細については、「[IoT Hub のデバイス ツインの理解と使用](../iot-hub/iot-hub-devguide-device-twins.md)」を参照してください。
>


## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きデバイス クライアント サンプルを使用する場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 使用する予定がない場合は、次の手順を使用して、このクイックスタートで作成したすべてのリソースを削除してください。

1. マシンに表示されているデバイス クライアント サンプルの出力ウィンドウを閉じます。
2. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、Device Provisioning サービスを選択します。 サービスの **[登録を管理します]** ブレードを開き、**[個々の登録]** タブをクリックします。このクイックスタートで登録したデバイスの*登録 ID* を選択し、上部の **[削除]** ボタンをクリックします。 
3. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、IoT ハブを選択します。 ハブの **[IoT Devices]\(IoT デバイス\)** ブレードを開き、このクイックスタートで登録したデバイスの "*デバイス ID*" を選択し、一番上の **[削除]** ボタンをクリックします。


## <a name="next-steps"></a>次の手順

このクイック スタートでは、シミュレートされた X.509 デバイスを Windows マシンに作成しました。 その登録を Azure IoT Hub Device Provisioning Service で構成した後、デバイスを IoT Hub に自動プロビジョニングしました。 プログラミングによって X.509 デバイスを登録する方法については、X.509 デバイスのプログラミングによる登録のクイックスタートに進みます。 

> [!div class="nextstepaction"]
> [Azure クイックスタート - Azure IoT Hub Device Provisioning Service への X.509 デバイスの登録](quick-enroll-device-x509-java.md)
