---
title: Azure IoT Central アプリケーションでデバイス セットを使用する | Microsoft Docs
description: オペレーターとして、Azure IoT Central アプリケーションでデバイス セットを使用する方法。
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: d0b802842d60d68bab36e87913a84c5e40b8e431
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206073"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Azure IoT Central アプリケーションでデバイス セットを使用する

この記事では、オペレーターとして、Microsoft Azure IoT Central アプリケーションでデバイス セットを使用する方法について説明します。

デバイス セットは、指定されたいくつかの条件にすべてが一致しているためにグループ化されたデバイスの一覧です。 デバイス セットは、デバイスをより小さい論理グループにグループ化することで、デバイスを大規模に管理、視覚化、および分析するのに役立ちます。 たとえば、シアトルのすべてのエアコン デバイスの一覧を作成して、シアトルの技術者が自分の担当するすべてのデバイスを検索できるようにできます。 この記事では、デバイス セットを作成および構成する方法を説明します。

## <a name="create-a-device-set"></a>デバイス セットの作成

デバイス セットを作成する場合:

1. 左側のナビゲーション メニューで **[Device Sets] (デバイス セット)** を選択します。

1. **[+ 新規]** をクリックします。

    ![新しいデバイス セット](media/howto-use-device-sets/image1.png)

1. デバイス セットにアプリケーション全体で一意な名前を付けます。 説明を追加することもできます。 デバイス セットに含めることができるのは、1 つのデバイス テンプレートのデバイスだけです。 このセットに使用するデバイス テンプレートを選択します。

1. プロパティ、比較演算子、および値を選択して、デバイス セットのデバイスを識別するクエリを作成します。 デバイス セットに配置する**すべての**条件を満たす複数のクエリとデバイスを追加できます。 作成したデバイス セットには、アプリケーションにアクセスできるユーザーはだれでもアクセスできるので、だれでもデバイス セットを表示、変更、または削除できます。

    ![デバイス セットのクエリ](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > デバイス セットは動的なクエリです。 デバイスの一覧を表示するたびに、一覧内のデバイスが異なることがあります。 一覧は、現在クエリの条件を満たしているデバイスによって異なります。

1. **[保存]** を選択します。

## <a name="configure-the-dashboard-for-your-device-set"></a>デバイス セットのダッシュボードの構成

デバイス セットを作成した後は、その**ダッシュボード**を構成することができます。 **ダッシュボード**は、イメージとリンクを配置できるホームページです。 デバイス セットのデバイスを一覧表示するグリッドを追加することもできます。

1. 左側のナビゲーション メニューで **[Device Sets] (デバイス セット)** を選択します。

1. **[ダッシュボード]** タブを選択します。

1. **[Design Mode] (デザイン モード)** をオンにします。

    ![[Design Mode] (デザイン モード) オン](media/howto-use-device-sets/image3.png)

1. イメージの追加については、「[イメージを準備して Azure IoT Central アプリケーションにアップロードする](howto-prepare-images.md)」を参照してください。

1. リンク タイルを追加します。
    1. 右側のウィンドウで **[Link] (リンク)** を選択します。

        ![リンクの選択](media/howto-use-device-sets/image6.png)

    1. リンクに**タイトル**を指定します。
    1. リンクをクリックしたときに開く URL を選択します。
    1. **[Title] (タイトル)** の下に表示される説明をリンクに指定します。
    1. **[保存]** を選択します。

        ![リンクの保存](media/howto-use-device-sets/image7.png)

    1. **ダッシュボード**のリンク タイルは、移動したりサイズを変更したりできます。

1. グリッドを追加します。 グリッドはデバイス セットのデバイスの表で、選択可能な列があります。
    1. 右側のウィンドウで **[Grid] (グリッド)** を選択します。

        ![グリッドの選択](media/howto-use-device-sets/image8.png)

    1. グリッドに**タイトル**を指定します。
    1. 設定ボタンを選択して、表示する列を選択します。 ポップアップ表示されるパネルで、表示する列を選び、右の矢印を選んで選択します。
    1. **[OK]** を選択します。
    1. **[保存]** を選択します。

        ![グリッドの保存](media/howto-use-device-sets/image9.png)

    1. グリッドをドラッグ アンド ドロップして**ダッシュボード**に配置します。

    > [!NOTE]
    > 複数のイメージ、リンク、およびグリッドを追加することができます。
  
    1. **[Design Mode] (デザイン モード)** をオフにします。

    ![[Design Mode] (デザイン モード) オフ](media/howto-use-device-sets/image10.png)


### <a name="configuring-location-map-in-your-device-sets-dashboard"></a>デバイス セット ダッシュボードでの場所マップの構成 
場所マップを追加して、マップでデバイス セットの場所を視覚化することができます。 

デバイス セット ダッシュボードに場所マップを追加するには、デバイス テンプレートで場所プロパティを構成している必要があります (「[Create a Location Property powered by Azure Maps](howto-set-up-template.md)」 (Azure Maps を利用する場所プロパティを作成する) を参照)。


1. デバイス セット ダッシュボードで、ライブラリからマップを選択します。 

    ![デバイス セット ダッシュボードのマップ](media/howto-use-device-sets/LocationMaps1.png)


2. タイトルを付け、以前にデバイス プロパティの一部として構成した場所プロパティを選択します。

    ![ダッシュボード マップの構成](media/howto-use-device-sets/LocationMaps2.png)

3. 保存すると、デバイス セットにデバイスの場所を示すマップ タイルが表示されます。

    ![ダッシュボード マップの保存](media/howto-use-device-sets/LocationMaps3.png)


5. これで、オペレーターがデバイス セット ダッシュボードを表示したときに、場所マップを含む、構成済みのすべてのタイルが表示され、すべてのデバイスの場所がひとめでわかるようになります。 

    ![ダッシュボード マップのオペレーター ビュー](media/howto-use-device-sets/LocationMaps4.png)

    マップは任意のサイズに変更することができます。

    マップでピンをクリックすると、デバイスの情報、名前と場所が表示されます。 ポップアップをクリックして、デバイスのプロパティ ページに移動できます。  


## <a name="configure-the-list-for-your-device-set"></a>デバイス セットのリストの構成

デバイス セットを作成した後は、その**リスト**を構成することができます。 **リスト**ではデバイス セットのすべてのデバイスが表で表示され、選択可能な列があります。

1. 左側のナビゲーション メニューで **[Device Sets] (デバイス セット)** を選択します。

1. **[List] (リスト)** タブを選択します。

1. **[Column Options] (列のオプション)** を選択します。

    ![列のオプション](media/howto-use-device-sets/image11.png)

1. 表示する列を選択し、右の矢印を選んで選択して、表示する列を選びます。

    ![列の選択](media/howto-use-device-sets/image12.png)

1. **[OK]** を選択します。

## <a name="analytics"></a>Analytics

デバイス セットの分析は、左側のナビゲーション メニューにあるメインの分析タブと同じです。 分析については、[分析を作成する方法](howto-create-analytics.md)に関する記事に詳細情報が記載されています。

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Central アプリケーションでデバイス セットを使用する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [テレメトリのルールを作成する方法](howto-create-telemetry-rules.md)
