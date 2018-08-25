---
title: Azure IoT Central で新しいデバイスの種類を定義する | Microsoft Docs
description: このチュートリアルでは、作成者向けに Azure IoT Central アプリケーションで新しいデバイスの種類を定義する方法について説明します。 種類のテレメトリ、状態、プロパティ、設定を定義します。
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: b085911f760693a774d443ca055944268b20f055
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869583"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>チュートリアル: Azure IoT Central アプリケーションで新しいデバイスの種類を定義する

このチュートリアルでは、作成者向けに、デバイス テンプレートを使用して、Microsoft Azure IoT Central アプリケーションでデバイスの新しい種類を定義する方法について説明します。 デバイス テンプレートでは、デバイスの種類のテレメトリ、状態、プロパティ、設定を定義します。

実デバイスを接続する前にアプリケーションをテストできるように、Azure IoT Central では、デバイス テンプレートの作成時にデバイス テンプレートからシミュレートされたデバイスを生成します。

このチュートリアルでは、**Connected Air Conditioner** デバイス テンプレートを作成します。 コネクテッド空調デバイスの仕様は、次のとおりです。

* 温度や湿度などのテレメトリを送信します。
* オンかオフかなどの状態を報告します。
* ファームウェア バージョンやシリアル番号などのプロパティがあります。
* 目標温度やファン速度などの設定があります。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 新しいデバイス テンプレートの作成
> * デバイスへのテレメトリの追加
> * シミュレートされたテレメトリの表示
> * イベントの測定の定義
> * シミュレートされたイベントの表示
> * 状態の測定の定義
> * シミュレートされた状態の表示
> * デバイスのプロパティの使用
> * デバイスの設定の使用
> * コマンドの使用

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、Azure IoT Central アプリケーションが必要です。 「[Azure IoT Central アプリケーションの作成](quick-deploy-iot-central.md)」クイック スタートを完了している場合は、そのクイック スタートで作成したアプリケーションを再利用できます。 それ以外の場合は、次の手順を実行して、空の Azure IoT Central アプリケーションを作成してください。

1. Azure IoT Central の [[Application Manager]\(アプリケーション マネージャー\)](https://aka.ms/iotcentral) ページに移動します。

2. Azure サブスクリプションへのアクセスに使用するメール アドレスとパスワードを入力します。

   ![所属組織のアカウントを入力](./media/tutorial-define-device-type/sign-in.png)

3. 新しい Azure IoT Central アプリケーションの作成を開始するには、**[New Application]\(新しいアプリケーション\)** を選択します。

    ![Azure IoT Central の [Application Manager]\(アプリケーション マネージャー\) ページ](./media/tutorial-define-device-type/iotcentralhome.png)

4. 新しい Azure IoT Central アプリケーションを作成するには:

    * わかりやすいアプリケーション名を選びます (**Contoso Air Conditioners** など)。 Azure IoT Central によって、一意の URL プレフィックスが自動的に生成されます。 この URL プレフィックスは、もっと覚えやすいものに変更することができます。
    
    * 使用する Azure Active Directory と Azure サブスクリプションを選択します。 ディレクトリとサブスクリプションの詳細については、「[Azure IoT Central アプリケーションの作成](howto-create-application.md)」を参照してください。
    
    * 既存のリソース グループを使用するか、任意の名前の新しいリソース グループを作成します。 たとえば、**contoso-rg** です。
    
    * 地理的に最も近いリージョンを選択します。
    
    * **[Custom Application]\(カスタム アプリケーション\)** アプリケーション テンプレートを選択します。
    
    * **[Free 30 Day Trial Application]\(30 日間無料試用版アプリケーション\)** 支払プランを選択します。
    
    * **[作成]** を選択します。

    ![Azure IoT Central の [Create Application]\(アプリケーションの作成\) ページ](./media/tutorial-define-device-type/iotcentralcreate.png)

詳細については、[Azure IoT Central アプリケーションを作成する方法](howto-create-application.md)に関するページを参照してください。

## <a name="create-a-new-custom-device-template"></a>新しいカスタム デバイス テンプレートの作成

作成者は、アプリケーションでデバイス テンプレートを作成および編集できます。 デバイス テンプレートを作成すると、Azure IoT Central により、テンプレートからシミュレートされたデバイスが生成されます。 シミュレートされたデバイスは、物理デバイスを接続する前にアプリケーションの動作をテストできるように、テレメトリを生成します。

新しいデバイス テンプレートをアプリケーションに追加するには、**[Application Builder]\(アプリケーション ビルダー\)** ページに移動する必要があります。 そのためには、左側のナビゲーション メニューで **[Application Builder]\(アプリケーション ビルダー\)** を選択します。

![[Application Builder]\(アプリケーション ビルダー\) ページ](./media/tutorial-define-device-type/builderhome.png)

## <a name="add-a-device-and-define-telemetry"></a>デバイスの追加およびテレメトリの定義

次の手順では、アプリケーションに温度テレメトリを送信するデバイス用の新しい **Connected Air Conditioner** デバイス テンプレートを作成する方法を示します。

1. **[Application Builder]\(アプリケーション ビルダー\)** ページで、**[Create Device Template]\(デバイス テンプレートの作成\)** を選択します。

    ![[Application Builder]\(アプリケーション ビルダー\) ページ、[Create Device Template]\(デバイス テンプレートの作成\)](./media/tutorial-define-device-type/builderhomedevices.png)

2. **[Device Templates]\(デバイス テンプレート\)** ページで **[カスタム]** を選択します。 **[カスタム]** デバイス テンプレートを使用すると、コネクテッド空調機のすべての特性と動作を定義できます。

    ![デバイス](./media/tutorial-define-device-type/builderhomedevicescustom.png)

3. **[New Device Template]\(新しいデバイス テンプレート\)** ページで、デバイスの名前として「**Connected Air Conditioner**」と入力し、**[作成]** を選択します。 また、Device Explorer で、オペレーターに表示されるデバイスの画像をアップロードすることもできます。

    ![カスタム デバイス](./media/tutorial-define-device-type/createcustomdevice.png)

4. **Connected Air Conditioner** デバイス テンプレートで、テレメトリを定義する **[Measurements]\(測定\)** ページを表示していることを確認します。 定義する各デバイス テンプレートには複数のページがあり、次のことができます。

    * デバイスが送信するテレメトリ、イベント、状態などの測定を指定する。
    
    * デバイスを制御するための設定を定義する。
    
    * デバイスに関する情報を記録するためのプロパティを定義する。
    
    * デバイスに関連付けられたルールを定義する。
    
    * オペレーター用のデバイス ダッシュボードをカスタマイズする。

    ![空調機の測定](./media/tutorial-define-device-type/airconmeasurements.png)

    > [!NOTE]
    > デバイスまたはデバイス テンプレートの名前を変更するには、ページの上部にあるテキストをクリックします。

5. 温度テレメトリの測定を追加するために、**[New Measurement]\(新しい測定\)** を選択します。 次に、測定の種類として **[テレメトリ]** を選択します。

    ![コネクテッド空調機の測定](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. デバイス テンプレートに対して定義するテレメトリの各種類には、次のような[構成オプション](howto-set-up-template.md)があります。

    * 表示オプション。

    * テレメトリの詳細。

    * シミュレーションのパラメーター。

    **Temperature** テレメトリを設定するには、次の表の情報を使用します。

    | Setting              | 値         |
    | -------------------- | -----------   |
    | 表示名         | 気温   |
    | フィールド名           | 温度   |
    | Units                | F             |
    | Min                  | 60            |
    | max                  | 110           |
    | 小数点以下の桁数       | 0             |

    テレメトリの表示色を選択することもできます。 テレメトリの定義を保存するために、**[保存]** を選択します。

    ![温度シミュレーションの構成](./media/tutorial-define-device-type/temperaturesimulation.png)

7. しばらくすると、**[Measurements]\(測定\)** ページに、シミュレートされたコネクテッド空調デバイスからの温度テレメトリのグラフが表示されます。 コントロールを使用して、表示や集計を管理したり、テレメトリの定義を編集したりできます。

    ![温度シミュレーションの表示](./media/tutorial-define-device-type/viewsimulation.png)

8. **[折れ線]**、**[積み上げ]**、**[時間の範囲の編集]** コントロールを使用して、グラフをカスタマイズすることもできます。

    ![グラフのカスタマイズ](./media/tutorial-define-device-type/customizechart.png)

## <a name="define-event-measurement"></a>イベントの測定の定義

[イベント] を使用して、エラーやコンポーネントの障害など、重要なイベントを表すためにデバイスが送信する特定の時点のデータを定義できます。 テレメトリの測定と同様に、Azure IoT Central では、物理デバイスを接続する前に、デバイスのイベントをシミュレートして、アプリケーションの動作をテストすることができます。 **[Measurements]\(測定\)** ビューでデバイスの種類のイベント測定を定義します。

1. **Fan Motor Error** イベント測定を追加するには、**[New Measurement]\(新しい測定\)** を選択します。 次に、測定の種類として **[イベント]** を選択します。

    ![コネクテッド空調機の測定](./media/tutorial-define-device-type/eventnew.png)

2. デバイス テンプレートに対して定義するイベントの各種類には、次のような[構成オプション](howto-set-up-template.md)があります。

   * 表示名。

   * フィールド名。

   * 重大度。

    **Fan Motor Error** イベントを構成するには、次の表の情報を使用します。

    | Setting              | 値             |
    | -------------------- | -----------       |
    | 表示名         | Fan Motor Error   |
    | フィールド名           | fanmotorerr       |
    | 重大度             | エラー             |

    イベントの定義を保存するために、**[保存]** を選択します。

    ![イベントの測定の構成](./media/tutorial-define-device-type/eventconfiguration.png)

3. しばらくすると、**[Measurements]\(測定\)** ページに、シミュレートされたコネクテッド空調デバイスからランダムに生成されたイベントのグラフが表示されます。 コントロールを使用して、表示を管理したり、イベントの定義を編集したりします。

    ![イベント シミュレーションの表示](./media/tutorial-define-device-type/eventview.png)

1. イベントに関する追加の詳細を表示するには、グラフ上でイベントをクリックします。

    ![イベントの詳細の表示](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-state-measurement"></a>状態の測定の定義

[状態] を使用して、一定期間にわたるデバイスまたはそのコンポーネントの状態を定義および視覚化します。 テレメトリの測定と同様に、Azure IoT Central では、物理デバイスを接続する前に、デバイスの状態をシミュレートして、アプリケーションの動作をテストすることができます。 **[Measurements]\(測定\)** ビューでデバイスの種類の状態測定を定義します。

1. **Fan Mode** 測定を追加するために、**[New Measurement]\(新しい測定\)** を選択します。 次に、測定の種類として **[状態]** を選択します。

    ![コネクテッド空調機の状態測定](./media/tutorial-define-device-type/statenew.png)

2. デバイス テンプレートに対して定義する状態の各種類には、次のような[構成オプション](howto-set-up-template.md)があります。

   * 表示名。

   * フィールド名。

   * 値とオプションの表示ラベル。

   * 各値の色。

    **Fan Mode** イベントを構成するには、次の表の情報を使用します。

    | Setting              | 値             |
    | -------------------- | -----------       |
    | 表示名         | Fan Mode          |
    | フィールド名           | fanmode           |
    | 値                | 1                 |
    | 表示ラベル        | 運転中         |
    | 値                | 0                 |
    | 表示ラベル        | 停止済み           |

    状態測定の定義を保存するために、**[保存]** を選択します。

    ![状態の測定の構成](./media/tutorial-define-device-type/stateconfiguration.png)

3. しばらくすると、**[Measurements]\(測定\)** ページに、シミュレートされたコネクテッド空調デバイスからランダムに生成された状態のグラフが表示されます。 コントロールを使用して、表示を管理したり、状態の定義を編集したりします。

    ![状態シミュレーションの表示](./media/tutorial-define-device-type/stateview.png)

4. 短期間にデバイスによって送信されたデータ ポイントが多すぎる場合、状態の測定は、次のように異なるビジュアルで表示されます。 グラフをクリックすると、その期間内のすべてのデータ ポイントが、時系列に表示されます。 時間の範囲を狭くしても、グラフにプロットされる測定を表示できます。

    ![状態の詳細の表示](./media/tutorial-define-device-type/stateviewdetail.png)

## <a name="settings-properties-and-commands"></a>設定、プロパティ、およびコマンド

設定、プロパティとデバイスのプロパティ、およびコマンドはデバイス テンプレートで定義され、個々のデバイスに関連付けられる異なる値です。

* "_設定_" は、アプリケーションからデバイスに構成データを送信するために使用します。 たとえば、オペレーターは設定を使用して、デバイスのテレメトリ間隔を 2 秒から 5 秒に変更できます。 オペレーターが設定を変更すると、設定の変更がデバイスによって確認されるまで、設定は UI で保留中としてマークされます。

* "_プロパティ_" は、アプリケーションでデバイスに関する情報を記録するために使用します。 たとえば、プロパティを使用して、デバイスのシリアル番号やデバイスの製造元の電話番号を記録できます。 プロパティはアプリケーションに保存され、デバイスとは同期しません。 オペレーターはプロパティに値を割り当てることができます。

* "_デバイス プロパティ_" は、デバイスがプロパティ値をアプリケーションに送信できるようにするために使用します。 これらのプロパティは、デバイスのみが変更できます。 オペレーターにとっては、デバイスのプロパティは読み取り専用です。

* _コマンド_は、アプリケーションからデバイスをリモートで管理するために使用します。 クラウドから直接デバイス上でコマンドを実行して、デバイスを制御できます。 たとえば、ある演算子は再起動などのコマンドを実行して、デバイスをすぐに再起動できます。

## <a name="use-settings"></a>設定の使用

"*設定*" を使用して、オペレーターがデバイスに構成データを送信できるようにします。 このセクションでは、**Connected Air Conditioner** デバイス テンプレートに設定を追加して、コネクテッド空調機の目標温度をオペレーターが設定できるようにします。

1. **Connected Air Conditioner** デバイス テンプレートの **[設定]** ページに移動します。

    ![設定を追加するための準備](./media/tutorial-define-device-type/deviceaddsetting.png)

    数値やテキストなど、さまざまな種類の設定を作成できます。

2. **[数値]** を選択して、数値設定をデバイスに追加します。

3. **Set Temperature** 設定を構成するために、次の表の情報を使用します。

    | フィールド                | 値           |
    | -------------------- | -----------     |
    | 表示名         | Set Temperature |
    | フィールド名           | setTemperature  |
    | 測定の単位  | F               |
    | 小数点以下の桁数       | 1               |
    | 最小値        | 20              |
    | 最大値        | 200             |
    | 初期値        | 80              |
    | 説明          | 空調機の目標温度の設定 |

    次に、**[保存]** を選択します。

    ![Set Temperature 設定の構成](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > 設定の変更がデバイスによって確認されると、設定の状態が**同期済み**に変わります。

4. 設定のタイルを移動したり、サイズを変更したりして、**[設定]** ページのレイアウトをカスタマイズできます。

    ![[設定] のレイアウトのカスタマイズ](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties--device-properties"></a>プロパティ/デバイスのプロパティの使用

"*プロパティ*" を使用して、アプリケーションでデバイスに関する情報を格納します。 このセクションでは、**Connected Air Conditioner** デバイス テンプレートに、シリアル番号とファームウェア バージョンを保存するデバイスのプロパティを追加します。 これらは両方ともデバイスによって報告された読み取り専用のプロパティであり、値を割り当てることはできません。 値を割り当てることができるプロパティには、デバイスの位置、所有者情報、デバイスの最後のサービスの日時などがあります。
 
1. **Connected Air Conditioner** デバイス テンプレートの **[プロパティ]** ページに移動します。

    ![プロパティを追加するための準備](./media/tutorial-define-device-type/deviceaddproperty.png)

    数値やテキストなど、さまざまな種類のデバイスのプロパティを作成できます。 シリアル番号プロパティをデバイス テンプレートに追加するために、**[テキスト]** を選択します。

2. シリアル番号プロパティを構成するために、次の表の情報を使用します。

    | フィールド                | 値                |
    | -------------------- | -------------------- |
    | 表示名         | Serial number        |
    | フィールド名           | serialNumber         |
    | 初期値        | cac00001             |
    | 説明          | デバイスのシリアル番号 |

    他のフィールドは既定値のままにします。

    ![デバイスのプロパティの構成](./media/tutorial-define-device-type/configureproperties.png)

    **[保存]** を選択します。

3. ファームウェア バージョンをデバイス テンプレートのデバイスのプロパティに追加するには、**[テキスト]** を選択します。

4. ファームウェア バージョンのデバイスのプロパティを構成するには、次の表の情報を使用します。

    | フィールド                | 値                   |
    | -------------------- | ----------------------- |
    | 表示名         | Firmware version        |
    | フィールド名           | firmwareVersion         |
    | 初期値        | 0.1                     |
    | 説明          | デバイスのファームウェアのバージョン |

    ![デバイスのプロパティの構成](./media/tutorial-define-device-type/configureproperties2.png)

    **[保存]** を選択します。

5. プロパティのタイルを移動したり、サイズを変更したりして、**[プロパティ]** ページのレイアウトをカスタマイズできます。

    ![[プロパティ] のレイアウトのカスタマイズ](./media/tutorial-define-device-type/propertieslayout.png)


## <a name="use-commands"></a>コマンドの使用

_コマンド_を使用して、デバイス上で直接コマンドを実行する演算子を有効にします。 このセクションでは、**Connected Air Conditioner** デバイス テンプレートに、コネクテッド空調機のディスプレイに特定のメッセージをエコーする演算子を有効にする、コマンドを追加します (これは MxChip サンプル コードで機能します)。

1. **Connected Air Conditioner** デバイス テンプレートの **[コマンド]** ページに移動します。

    ![設定を追加するための準備](media/tutorial-define-device-type/commandsecho.png)

    要件に基づいて、さまざまな種類のコマンドを作成できます。 

1. デバイスにコマンドを追加するには、**[New Command]\(新規コマンド\)** をクリックします。

1. 次の表の情報を使用して、新しいコマンドを構成します。

    | フィールド                | 値           |
    | -------------------- | -----------     |
    | 表示名         | Echo Command    |
    | フィールド名           | echo            |
    | [Default Timeout]\(既定のタイムアウト\)      | 30              |
    | 表示名         | Display Text    |
    | [Display Type]\(表示タイプ\)         | text            |  

入力で **+** をクリックすることで、コマンドに追加入力を追加できます。

2. **[保存]** を選択します。

3. コマンドのタイルを移動したり、サイズを変更したりして、**[コマンド]** ページのレイアウトをカスタマイズできます。

    ![[設定] のレイアウトのカスタマイズ](media/tutorial-define-device-type/commandstileresize.png)

## <a name="view-your-simulated-device"></a>シミュレートされたデバイスの表示

**Connected Air Conditioner** デバイス テンプレートを定義したので、**ダッシュボード**をカスタマイズして、定義した測定、設定、プロパティを含めることができます。 その後、オペレーターとしてダッシュボードをプレビューできます。

1. **Connected Air Conditioner** デバイス テンプレートの **[ダッシュボード]** ページを選択します。

    ![コネクテッド空調機のダッシュボード](./media/tutorial-define-device-type/aircondashboards.png)

2. **[折れ線グラフ]** を選択して、コンポーネントを **[ダッシュボード]** に追加します。

    ![[ダッシュボード] のコンポーネント](./media/tutorial-define-device-type/dashboardcomponents1.png)

3. 次の表の情報を使用して、**[折れ線グラフ]** コンポーネントを構成します。

    | Setting      | 値       |
    | ------------ | ----------- |
    | タイトル        | 気温 |
    | 時間の範囲   | 過去 30 分 |
    | 測定 | Temperature (**[Temperature]** の横の **[表示]** を選択します) |

    ![折れ線グラフの設定](./media/tutorial-define-device-type/linechartsettings.png)

    次に、**[保存]** を選択します。

4. 次の表の情報を使用して、**[Event Chart]\(イベント グラフ\)** コンポーネントを構成します。

    | Setting      | 値       |
    | ------------ | ----------- |
    | タイトル        | Events |
    | 時間の範囲   | 過去 30 分 |
    | 測定 | Fan Motor Error (**[Fan Motor Error]** の横の **[表示]** を選択します) |

    ![折れ線グラフの設定](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    次に、**[保存]** を選択します。

5. 次の表の情報を使用して、**[State Chart]\(状態グラフ\)** コンポーネントを構成します。

    | Setting      | 値       |
    | ------------ | ----------- |
    | タイトル        | Fan Mode |
    | 時間の範囲   | 過去 30 分 |
    | 測定 | Fan Mode (**[Fan Mode]** の横の **[表示]** を選択します) |

    ![折れ線グラフの設定](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    次に、**[保存]** を選択します。

6. Set Temperature 設定をダッシュボードに追加するために、**[Settings and Properties]\(設定とプロパティ\)** を選択します。

    ![[ダッシュボード] のコンポーネント](./media/tutorial-define-device-type/dashboardcomponents4.png)

7. 次の表の情報を使用して、**[Settings and Properties]\(設定とプロパティ\)** コンポーネントを設定します。

    | Setting                 | 値         |
    | ----------------------- | ------------- |
    | タイトル                   | Set target temperature |
    | [Settings and Properties]\(設定とプロパティ\) | Set Temperature |

    ![シリアル番号プロパティの設定](./media/tutorial-define-device-type/propertysettings3.png)

    次に、**[保存]** を選択します。

8. デバイスのシリアル番号をダッシュボードに追加するために、**[Settings and Properties]\(設定とプロパティ\)** を選択します。

    ![[ダッシュボード] のコンポーネント](./media/tutorial-define-device-type/dashboardcomponents3.png)

9. 次の表の情報を使用して、**[Settings and Properties]\(設定とプロパティ\)** コンポーネントを設定します。

    | Setting                 | 値         |
    | ----------------------- | ------------- |
    | タイトル                   | Serial number |
    | [Settings and Properties]\(設定とプロパティ\) | Serial Number |

    ![シリアル番号プロパティの設定](./media/tutorial-define-device-type/propertysettings1.png)

    次に、**[保存]** を選択します。

10. デバイスのファームウェアのバージョンをダッシュボードに追加するために、**[Settings and Properties]\(設定とプロパティ\)** を選択します。

    ![[ダッシュボード] のコンポーネント](./media/tutorial-define-device-type/dashboardcomponents4.png)

11. 次の表の情報を使用して、**[Settings and Properties]\(設定とプロパティ\)** コンポーネントを設定します。

    | Setting                 | 値            |
    | ----------------------- | ---------------- |
    | タイトル                   | Firmware version |
    | [Settings and Properties]\(設定とプロパティ\) | Firmware Version |

    ![シリアル番号プロパティの設定](./media/tutorial-define-device-type/propertysettings2.png)

    次に、**[保存]** を選択します。

12. オペレーターとしてダッシュボードを表示するために、ページの右上にある **[Design Mode]\(デザイン モード\)** をオフに切り替えます。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * 新しいデバイス テンプレートの作成
> * デバイスへのテレメトリの追加
> * シミュレートされたテレメトリの表示
> * デバイス イベントの定義
> * シミュレートされたイベントの表示
> * 状態の定義
> * シミュレートされた状態の表示
> * デバイスのプロパティの使用
> * デバイスの設定の使用

ここでは、Azure IoT Central アプリケーションでデバイス テンプレートを定義しました。推奨される次の手順は以下のとおりです。

* [デバイスのルールとアクションを構成する](tutorial-configure-rules.md)
* [オペレーターのビューをカスタマイズする](tutorial-customize-operator.md)