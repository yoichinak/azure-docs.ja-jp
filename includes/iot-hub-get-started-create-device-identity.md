## <a name="create-a-device-identity"></a>デバイス ID の作成

このセクションでは、[iothub-explorer][iot-hub-explorer] という Node.js ツールを使用して、このチュートリアル用のデバイス ID を作成します。 デバイス ID には大文字と小文字の区別があります。

1. コマンドライン環境で次のコマンドを実行します。

    `npm install -g iothub-explorer@latest`

1. その後、次のコマンドを実行してハブにサインインします。 このとき、`{iot hub connection string}` を前にコピーしておいた IoT Hub の接続文字列と置き換えてください。

    `iothub-explorer login "{iot hub connection string}"`

1. 最後に、次のコマンドを実行して、`myDeviceId` という新しいデバイス ID を作成します。

    `iothub-explorer create myDeviceId --connection-string`

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

結果として得られたデバイスの接続文字列をメモしておきます。 このデバイス接続文字列は、デバイス アプリからデバイスとして IoT Hub に接続する際に使用します。

![][img-identity]

デバイス ID をプログラムで作成する方法については、[IoT Hub の概要][lnk-getstarted]に関するページを参照してください。

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/quickstart-send-telemetry-dotnet.md
