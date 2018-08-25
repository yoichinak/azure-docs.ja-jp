---
title: 予測メンテナンスのソリューション アクセラレータの概要 - Azure | Microsoft Docs
description: Azure IoT 予測メンテナンスのソリューション アクセラレータの概要。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: dobett
ms.openlocfilehash: e7c6c8d017e4371919088ec414d3108939ca4a19
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036256"
---
# <a name="predictive-maintenance-solution-accelerator-overview"></a>予測メンテナンスのソリューション アクセラレータの概要

予測メンテナンスのソリューション アクセラレータは、障害が発生する可能性があるポイントを予測するビジネス シナリオに対応したエンド ツー エンド ソリューションです。 このソリューション アクセラレータを使用すると、メンテナンスの最適化などのアクティビティを先手を打って実行できます。 このソリューションは、IoT Hub、Stream Analytics、[Azure Machine Learning][lnk-machine-learning] ワークスペースなど、主要な Azure IoT ソリューション アクセラレータ サービスを組み合わせたものです。 このワークスペースには、公開されているサンプル データ セットに基づいて航空機エンジンの残存耐用年数 (RUL) を予測するモデルが含まれています。 このソリューションでは、固有のビジネス要件を満たすソリューションを計画および実装するための開始地点として使用できる、IoT ビジネス シナリオが完全に実装されています。

## <a name="logical-architecture"></a>論理アーキテクチャ

次の図は、ソリューション アクセラレータの論理コンポーネントの概要を示したものです。

![][img-architecture]

青色の項目は、ソリューション アクセラレータをデプロイしたリージョンにプロビジョニングされた Azure サービスです。 ソリューション アクセラレータをデプロイできるリージョンの一覧は、[プロビジョニング ページ][lnk-azureiotsuite]に表示されます。

緑色の項目は、シミュレートされている航空機エンジン デバイスです。 これらのシミュレートされているデバイスの詳細については、「[シミュレートされたデバイス](#simulated-devices)」のセクションを参照してください。

灰色の項目は、"*デバイスの管理*" 機能を実装しているコンポーネントを示します。 予測メンテナンスのソリューション アクセラレータの現在のリリースでは、これらのリソースはプロビジョニングできません。 デバイスの管理の詳細については、[リモート監視ソリューション アクセラレータ][lnk-remote-monitoring]に関するページを参照してください。

## <a name="azure-resources"></a>Azure リソース

Azure Portal で、指定したソリューション名の付いたリソース グループに移動して、プロビジョニングされたリソースを確認します。

![アクセラレータ リソース][img-resource-group]

ソリューション アクセラレータをプロビジョニングすると、Machine Learning ワークスペースへのリンクを含む電子メールが届きます。 この Machine Learning ワークスペースには、プロビジョニングしたソリューションの [Microsoft Azure IoT Solution Accelerators][lnk-azureiotsuite] ページから移動することも可能です。 タイルは、ソリューションが**準備完了**の状態の場合にこのページで使用できます。

![Machine Learning モデル][img-machine-learning]

## <a name="simulated-devices"></a>シミュレートされたデバイス

ソリューション アクセラレータでは、航空機エンジン デバイスをシミュレートしています。 このソリューションでは、1 台の航空機にマッピングされる 2 つのエンジンをプロビジョニングしています。 各エンジンは、4 種類のテレメトリを出力します。センサー 9、センサー 11、センサー 14、センサー 15 は、Machine Learning モデルがそのエンジンの RUL を計算するために必要なデータを提供します。 シミュレートされたデバイスはそれぞれ、次のテレメトリ メッセージを IoT Hub に送信します。

"*サイクル数*"。 サイクルは、2 ～ 10 時間の期間で完了したフライトを表します。 フライト中、テレメトリ データは 30 分ごとにキャプチャされます。

*テレメトリ*。 エンジン属性を示すセンサーは 4 つあります。 センサーは総称的に、センサー 9、センサー 11、センサー 14、およびセンサー 15 とラベル付けされています。 これらの 4 つのセンサーは、RUL モデルから有用な結果を得るために十分なテレメトリを表します。 ソリューション アクセラレータで使用されるモデルは、実際のエンジンのセンサー データなど、公開されているデータ セットから作成されています。 元のデータ セットからこのモデルを作成する方法の詳細については、「[Cortana Intelligence Gallery Predictive Maintenance Template (Cortana Intelligence ギャラリーの予測的なメンテナンス テンプレート)][lnk-cortana-analytics]」を参照してください。

シミュレーション デバイスは、ソリューションの IoT Hub から送信された次のコマンドを処理することができます。

| コマンド | 説明 |
| --- | --- |
| StartTelemetry |シミュレーションの状態を制御します。<br/>デバイスのテレメトリ送信を開始します。 |
| StopTelemetry |シミュレーションの状態を制御します。<br/>デバイスのテレメトリ送信を停止します。 |

IoT Hub は、デバイスのコマンドの受信確認を渡します。

## <a name="azure-stream-analytics-job"></a>Azure Stream Analytics ジョブ

**ジョブ: テレメトリ**は、次の 2 つのステートメントを使用して、デバイスのテレメトリの受信ストリームに対して動作します。

* 1 つ目では、デバイスからのすべてのテレメトリを選択し、このデータを Blob Storage に送信します。 ここから、Web アプリで視覚化されます。
* 2 つ目では、2 分間のスライディング ウィンドウに渡る平均センサー値を計算し、このデータを Event Hub を介して**イベント プロセッサ**に送信します。

## <a name="event-processor"></a>イベント プロセッサ
**イベント プロセッサ ホスト**は、Azure Web Job で実行されます。 **イベント プロセッサ** は、完了したサイクルの平均センサー値を受け取ります。 それらの値は、トレーニングされたモデルを公開する API に渡され、エンジンの RUL が計算されます。 この API は、ソリューションの一部としてプロビジョニングされた Machine Learning ワークスペースによって公開されます。

## <a name="machine-learning"></a>Machine Learning
Machine Learning コンポーネントは、実際の航空機エンジンから収集されたデータから派生したモデルを使用します。 この Machine Learning ワークスペースには、[azureiotsuite.com][lnk-azureiotsuite] ページのソリューションのタイルから移動することも可能です。 このタイルは、ソリューションが**準備完了**の状態の場合に使用できます。

IoT ソリューション アクセラレータ サービスを通じて収集されたデバイスのテレメトリを使用するこれらの機能を示すために、Azure Machine Learning モデルをテンプレートとして利用できます。 Microsoft は、公開されているデータ<sup>\[1\]</sup> を基にして航空機エンジンの[回帰モデル][lnk_regression_model]と、モデルの使用方法に関するステップ バイ ステップ ガイダンスを作成しました。

Azure IoT 予測メンテナンスのソリューション アクセラレータは、このテンプレートから作成された回帰モデルを使用します。 モデルは、Azure サブスクリプションにデプロイされ、自動的に生成される API を通じて公開されます。 ソリューションには、4 つのエンジン (合計 100) および 4 つのセンサー データ ストリーム (合計 21) を表すテスト データのサブセットが含まれます。 このデータは、トレーニング済みのモデルから正確な結果を得るには十分な量です。

*\[1\] A. Saxena と K. Goebel (2008 年)。「Turbofan Engine Degradation Simulation Data Set (ターボファン エンジンの劣化シミュレーション データ セット)」、NASA Ames Prognostics Data Repository (https://c3.nasa.gov/dashlink/resources/139/))、NASA Ames Research Center、カリフォルニア州モフェット フィールド*

## <a name="next-steps"></a>次の手順
予測メンテナンスのソリューション アクセラレータの主要コンポーネントは確認しました。次は、それをカスタマイズします。

IoT ソリューション アクセラレータの他の機能についても学習できます。

* [IoT ソリューション アクセラレータに関してよく寄せられる質問][lnk-faq]
* [IoT の徹底的なセキュリティ][lnk-security-groundup]

[img-architecture]: media/iot-accelerators-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-accelerators-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-accelerators-predictive-walkthrough/machine-learning.png

[lnk-remote-monitoring]: quickstart-predictive-maintenance-deploy.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsolutions.com/
[lnk-faq]: iot-accelerators-faq.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3