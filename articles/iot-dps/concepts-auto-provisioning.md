---
title: IoT Hub Device Provisioning Service - 自動プロビジョニングの概念
description: この記事では、IoT Device Provisioning Service、IoT Hub、およびクライアント SDK を使用した、デバイスの自動プロビジョニングのフェーズの概要について説明します。
author: wesmc7777
ms.author: wesmc
ms.date: 06/01/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 025e447995d302c24ab2a7d1c8668857cb47ffdd
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2018
ms.locfileid: "42146228"
---
# <a name="auto-provisioning-concepts"></a>自動プロビジョニングの概念

Device Provisioning Service は、[概要](about-iot-dps.md)に関するページで説明したように、人間の介入を必要とせずに、IoT ハブにデバイスをジャストインタイムでプロビジョニングできるようにするヘルパー サービスです。 プロビジョニングが正常に完了すると、デバイスは指定された IoT Hub に直接接続されます。 このプロセスは自動プロビジョニングと呼ばれ、デバイスの簡単に操作できる登録と初期構成のエクスペリエンスが提供されます。

## <a name="overview"></a>概要

Azure IoT 自動プロビジョニングは、次の 3 つのフェーズに分けることができます。

1. **サービスの構成** - Azure IoT Hub と IoT Hub Device Provisioning Service インスタンスの 1 回限りの構成。これらを確立し、これらの間のリンケージを作成します。

   > [!NOTE]
   > IoT ソリューションのサイズに関係なく、何百万ものデバイスのサポートを計画している場合でも、これは **1 回限りの構成**です。

2. **デバイスの加入** - 後で登録するデバイスを Device Provisioning Service インスタンスに知らせるプロセス。 [加入](concepts-service.md#enrollment)するには、プロビジョニング サービスでデバイス ID 情報を構成します。単一デバイスの場合は "個別加入"、複数デバイスの場合は "グループ加入" にします。 ID は、デバイスが使用するように設計されている、以下の[構成証明メカニズム](concepts-security.md#attestation-mechanism)に基づきます。このメカニズムにより、プロビジョニング サービスは登録時にデバイスの信頼性を証明できます。

   - **TPM**: "個別加入" として構成されます。デバイス ID は、TPM 登録 ID と公開保証キーに基づきます。 TPM が[仕様](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)である場合、サービスは TPM の実装 (ハードウェアまたはソフトウェア) に関係なく、仕様ごとの証明だけを想定します。 TPM ベースの証明の詳細については、「[Device provisioning: Identity attestation with TPM (デバイス プロビジョニング: TPM による ID 証明)](https://azure.microsoft.com/blog/device-provisioning-identity-attestation-with-tpm/)」を参照してください。 

   - **X509**: "個別加入" または "グループ加入" として構成されます。デバイス ID は X.509 デジタル証明書に基づき、この証明書は .pem または .cer ファイルとして加入時にアップロードされます。

   > [!IMPORTANT]  
   > Device Provisioning Services を使用するための前提条件ではありませんが、キーや X.509 証明書などの重要なデバイス ID 情報を保存するには、デバイスでハードウェア セキュリティ モジュール (HSM) を使用することを強くお勧めします。

3. **デバイスの登録と構成** - 起動時に登録ソフトウェアによって開始されます。登録ソフトウェアは、デバイスおよび構成証明メカニズムに適した Device Provisioning Service クライアント SDK を使用してビルドされます。 ソフトウェアは、デバイスの認証、およびその後の IoT Hub への登録のために、プロビジョニング サービスへの接続を確立します。 登録が成功すると、デバイスに IoT Hub 固有のデバイス ID と接続情報が提供されるため、初期構成を取得してテレメトリ プロセスを開始することができます。 運用環境では、このフェーズは前の 2 つのフェーズの数週間後または数か月後に発生する場合があります。

## <a name="roles-and-operations"></a>役割と操作

前のセクションで説明したフェーズは、製造時間、出荷、通関業務などの実際的な生産条件のために、数週間または数か月にわたる場合があります。さらに、さまざまな存在が関与している場合、複数の役割にまたがる活動にわたることがあります。 このセクションでは、各フェーズに関連するさまざまな役割と操作を詳しく説明し、フローを流れ図で示します。 

自動プロビジョニングでは、構成証明メカニズムの有効化に固有の、デバイスの製造元に対する要件もあります。 製造操作は、自動プロビジョニング フェーズのタイミングとは無関係に生じる場合もあります。特に、自動プロビジョニングが既に確立された後で新しいデバイスが調達される際に発生します。

一連のクイック スタートが左側の目次に用意されており、ハンズオン エクスペリエンスによる自動プロビジョニングの説明に役立ちます。 学習プロセスを簡易にする目的で、物理デバイスの加入および登録をシミュレートするために、ソフトウェアが使用されます。 一部のクイック スタートでは、クイック スタートのシミュレーションとしての性質のために、複数の役割に対する操作を、存在しない役割に対する操作も含めて、実行する必要があります。

| Role | Operation | 説明 | 
|------| --------- | ------------| 
| Manufacturer | ID と登録 URL をエンコードする | 使用される構成証明メカニズムに基づいて、製造元は、デバイスの ID 情報と Device Provisioning Service の登録 URL をエンコードします。<br><br>**クイック スタート**: デバイスがシミュレートされるため、製造元の役割はありません。 この情報の取得方法の詳細については、開発者の役割を参照してください。この情報は、サンプル登録アプリケーションのコーディングで使用されます。 |  
| | デバイス ID を指定する | 製造元は、デバイス ID 情報の作成者として、情報をオペレーター (または指定されたエージェント) に通知するか、API を通じて Device Provisioning Service への加入操作を直接行う責任があります。<br><br>**クイック スタート**: デバイスがシミュレートされるため、製造元の役割はありません。 デバイス ID を取得する方法の詳細については、オペレーターの役割を参照してください。デバイス ID は、シミュレートされているデバイスを Device Provisioning Service インスタンスに加入させるために使用されます。 | 
| operator | 自動プロビジョニングを構成する | この操作は、自動プロビジョニングの第 1 フェーズに対応します。<br><br>**クイック スタート**: オペレーターの役割として、Azure サブスクリプション内の Device Provisioning Service および IoT Hub のインスタンスを構成します。 | 自動プロビジョニングをセットアップする |
|  | デバイス ID を加入させる | この操作は、自動プロビジョニングの第 2 フェーズに対応します。<br><br>**クイック スタート**: オペレーターの役割として、シミュレートされたデバイスを Device Provisioning Service インスタンスに加入させます。 デバイス ID は、クイック スタートでシミュレートされている構成証明方法 (TPM または X.509) によって決定されます。 構成証明の詳細については、開発者の役割を参照してください。 | 
| Device Provisioning Service、<br>IoT Hub | \<すべての操作\> | 物理デバイスによる運用実装でも、シミュレートされたデバイスによるクイック スタートでも、これらの役割は、Azure サブスクリプションで構成する IoT サービスを通じて実行されます。 IoT サービスのプロビジョニングでは、物理デバイスとシミュレートされたデバイスとの区別がないため、役割/操作は、まったく同じように機能します。 | 
| 開発者 | 登録ソフトウェアをビルド/デプロイする | この操作は、自動プロビジョニングの第 3 フェーズに対応します。 開発者は、適切な SDK を使用して、登録ソフトウェアをビルドし、デバイスにデプロイします。<br><br>**クイック スタート**: ビルドするサンプル登録アプリケーションが、実際のデバイスをシミュレートします。プラットフォームと言語は選択することができ、ワークステーション上で実行されます (物理デバイスにデプロイするのではなく)。 登録アプリケーションは、物理デバイスにデプロイされるアプリケーションと同じ操作をします。 構成証明方法 (TPM または X.509 証明書) と、Device Provisioning Service インスタンスの登録 URL および "ID スコープ" を指定します。 デバイス ID は、指定した以下の方法に基づいて、実行時の SDK 構成証明ロジックによって決められます。 <ul><li>**TPM 構成証明** - 開発ワークステーションが [TPM シミュレーター アプリケーション](how-to-use-sdk-tools.md#trusted-platform-module-tpm-simulator)を実行します。 実行されると、デバイス ID の加入に使用される TPM の "保証キー" と "登録 ID" を展開するために別のアプリケーションが使用されます。 SDK の構成証明ロジックも、登録中にシミュレーターを使用し、認証および加入の検証に対して、署名された SAS トークンを提示します。</li><li>**X509 構成証明** - [証明書を生成する](how-to-use-sdk-tools.md#x509-certificate-generator)ために、ツールを使用します。 生成されたら、加入で使用するために必要な証明書ファイルを作成します。 SDK の構成証明ロジックも、登録中に証明書を使用し、認証および加入の検証に対して提示します。</li></ul> | 
| Device | 起動して登録する | この操作は、自動プロビジョニングの第 3 フェーズに対応し、開発者によってビルドされたデバイス登録ソフトウェアによって実行されます。 詳細については、開発者の役割を参照してください。 最初のブートで、以下のことが実行されます。 <ol><li>アプリケーションは、開発中に指定されたグローバル URL およびサービス "ID スコープ" ごとに Device Provisioning Service インスタンスに接続します。</li><li>接続すると、デバイスは加入時に指定された構成証明方法と ID で認証されます。</li><li>認証されると、プロビジョニング サービス インスタンスによって指定された IoT Hub インスタンスにデバイスが登録されます。</li><li>登録が正常に実行されると、一意のデバイス ID と IoT Hub エンドポイントが登録アプリケーションに返されます。これらは、IoT Hub との通信に使用されます。</li><li> そこから、デバイスは構成用に初期[デバイス ツイン](~/articles/iot-hub/iot-hub-devguide-device-twins.md)状態を入手して、テレメトリ データの報告プロセスを開始することができます。</li></ol>**クイック スタート**: デバイスはシミュレートされるため、登録ソフトウェアは開発ワークステーション上で実行されます。| 

次の図は、デバイスの自動プロビジョニング中の役割と操作の流れをまとめたものです。
<br><br>
[![デバイスの自動プロビジョニングの流れ](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png)](./media/concepts-auto-provisioning/sequence-auto-provision-device-vs.png#lightbox) 

> [!NOTE]
> 必要に応じて、製造元も Device Provisioning Service API を使用して (オペレーター経由ではなく)、"デバイス ID の加入" 操作を実行することができます。 この流れなどの詳細については、ビデオ「[Zero touch device registration with Azure IoT (ゼロ タッチでの Azure IoT へのデバイスの登録)](https://youtu.be/cSbDRNg72cU?t=2460)」(41:00 のマーカーから) を参照してください。

## <a name="next-steps"></a>次の手順

自動プロビジョニングの各クイック スタートを実行する際に、この記事を参照の起点としてお気に入りに登録しておくと便利です。 

まず、自分に適した管理ツールでの「自動プロビジョニングをセットアップする」クイック スタートを実行します。このクイック スタートでは、"サービスの構成" フェーズを実行できます。

- [Azure CLI を使用して自動プロビジョニングをセットアップする](quick-setup-auto-provision-cli.md)
- [Azure Portal を使用して自動プロビジョニングをセットアップする](quick-setup-auto-provision.md)
- [Resource Manager テンプレートを使用して自動プロビジョニングをセットアップする](quick-setup-auto-provision-rm.md)

次に、自分に適したデバイス構成証明メカニズムおよび Device Provisioning Service SDK/言語の「シミュレートされたデバイスの自動プロビジョニング」クイック スタートに進みます。 このクイック スタートでは、"デバイスの加入" と "デバイスの登録および構成" フェーズについて具体的に説明しています。 

|  | シミュレートされたデバイスの構成証明メカニズム | クイック スタート SDK/言語 |  |
|--|--|--|--|
|  | トラステッド プラットフォーム モジュール (TPM) | [C](quick-create-simulated-device.md)<br>[Java](quick-create-simulated-device-tpm-java.md)<br>[C#](quick-create-simulated-device-tpm-csharp.md)<br>[Python](quick-create-simulated-device-tpm-python.md) |  |
|  | X.509 証明書 | [C](quick-create-simulated-device-x509.md)<br>[Java](quick-create-simulated-device-x509-java.md)<br>[C#](quick-create-simulated-device-x509-csharp.md)<br>[Node.js](quick-create-simulated-device-x509-node.md)<br>[Python](quick-create-simulated-device-x509-python.md) |  |




