---
title: ポータルでのクラウド サービスの自動スケール | Microsoft Docs
description: ポータルを使用して、Azure でクラウド サービスの Web ロールおよび worker ロールの自動スケール ルールを構成する方法について説明します。
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 701d4404-5cc0-454b-999c-feb94c1685c0
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 29ee71e7946145e50cc875df96b674abec3e12df
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004338"
---
# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-portal"></a>ポータルでクラウド サービスの自動スケールを構成する方法

スケールインまたはスケールアウトをトリガーするクラウド サービス worker ロールに対して条件を設定することができます。 ロールの条件は、CPU、ディスク、またはロールのネットワークの負荷に基づいて設定できます。 条件はまた、メッセージ キューに基づいて、またはサブスクリプションに関連付けられているその他の Azure リソースのメトリックに基づいて設定することができます。

> [!NOTE]
> この記事では、クラウド サービスの Web ロールと worker ロールについて説明します。 仮想マシンを直接作成した場合 (クラシック)、仮想マシンはクラウド サービスでホストされます。 標準の仮想マシンは、[可用性セット](../virtual-machines/windows/classic/configure-availability-classic.md)に関連付けることでスケールを設定でき、手動でオンまたはオフにすることができます。

## <a name="considerations"></a>考慮事項
アプリケーションの規模の設定を構成する前に、以下に示す情報を考慮してください。

* 規模設定はコア使用量の影響を受けます。

    ロール インスタンスが大きいと、使用するコアも多くなります。 アプリケーションの規模の設定は、サブスクリプションのコア数の制限内でのみ行うことができます。 たとえば、サブスクリプションのコア数が 20 に制限されているとします。 アプリケーションを 2 つの中規模のクラウド サービスで実行している場合 (合計 4 コア)、サブスクリプション内の他のクラウド サービスのデプロイは残りの 16 コアまでしかスケールアップできません。 サイズの詳細については、「[Cloud Service のサイズ](cloud-services-sizes-specs.md)」を参照してください。

* スケールはキュー メッセージのしきい値に基づいて行うことができます。 キューの使用方法の詳細については、 [Queue Storage サービスの使用方法](../storage/queues/storage-dotnet-how-to-use-queues.md)に関するページを参照してください。

* サブスクリプションに関連付けられているその他のリソースについてもスケールを行うことができます。

* アプリケーションの高可用性を可能にするには、アプリケーションを 2 つ以上のロール インスタンスと共にデプロイする必要があります。 詳細については、「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」を参照してください。

* 自動スケールは、すべてのロールが**準備完了**状態の場合にのみ発生します。  


## <a name="where-scale-is-located"></a>スケールの設定
クラウド サービスを選択したら、該当するクラウド サービス ブレードを表示状態にします。

1. クラウド サービス ブレードの **[ロールとインスタンス]** タイルで、クラウド サービスの名前を選択します。   
   **重要**: ロールの下にあるロール インスタンスではなく、クラウド サービス ロールを必ずクリックしてください。

    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. **[スケール]** タイルを選択します。

    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## <a name="automatic-scale"></a>自動スケール
ロールのスケール設定を構成する際は、**手動**または**自動**のいずれかのモードを使用できます。 手動モードでは想定通りにスケールが行われるので、インスタンスの絶対数を設定します。 ただし、自動モードでは、スケールの方法とスケールの程度を制御するルールを設定できます。

**[スケールの基準]** オプションを **[スケジュールおよびパフォーマンス ルール]** に設定します。

![プロファイルとルールを使用したクラウド サービスのスケール設定](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. 既存のプロファイルです。
2. 親プロファイルのルールを追加します。
3. 別のプロファイルを追加します。

**[プロファイルの追加]** を選択します。 プロファイルではスケールで使用するモードを指定します。**[常時]**、**[繰り返し]**、**[固定日付]** という 3 つのモードがあります。

プロファイルとルールを構成したら、上部にある **[保存]** アイコンをクリックします。

#### <a name="profile"></a>プロファイル
プロファイルでは、スケールにおけるインスタンスの最大数と最小数、ならびにこのスケール範囲を有効にするタイミングを設定します。

* **Always (常に)**

    常に、この使用可能なインスタンスの範囲を保持します。  

    ![常時スケールされるクラウド サービス](./media/cloud-services-how-to-scale-portal/select-always.png)
* **定期的なアイテム**

    スケールする曜日セットを選択します。

    ![定期的なスケジュールで行われるクラウド サービスのスケール](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **指定日**

    指定された日付範囲でロールをスケールします。

    ![指定日に行われるクラウド サービスのスケール](./media/cloud-services-how-to-scale-portal/select-fixed.png)

プロファイルを構成したら、プロファイル ブレードの下部にある **[OK]** ボタンを選択します。

#### <a name="rule"></a>ルール
ルールは、プロファイルに追加されて、スケールをトリガーする条件を示します。

ルールのトリガーは、条件値を追加可能なクラウド サービスのメトリック (CPU の使用率、ディスク アクティビティ、またはネットワーク アクティビティ) に基づいて設定されます。 さらに、メッセージ キュー、またはサブスクリプションに関連付けられているその他の Azure リソースのメトリックに基づいてトリガーを設定することができます。

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

ルールを構成したら、ルール ブレードの下部にある **[OK]** ボタンを選択します。

## <a name="back-to-manual-scale"></a>手動スケールに戻る
[[スケールの設定]](#where-scale-is-located) に移動し、**[スケールの基準]** オプションを **[手動で入力したインスタンス数]** に設定します。

![プロファイルとルールを使用したクラウド サービスのスケール設定](./media/cloud-services-how-to-scale-portal/manual-basics.png)

この設定により、ロールから自動スケールが削除され、インスタンス数を直接設定できるようになります。

1. スケール (手動または自動) オプション。
2. スケールするインスタンスを設定するロール インスタンス スライダー。
3. スケールするロールのインスタンス。

スケール設定を構成したら、上部にある **[保存]** アイコンを選択します。
