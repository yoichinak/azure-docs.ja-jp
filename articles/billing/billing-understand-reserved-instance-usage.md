---
title: 従量課金制サブスクリプションに適用される Azure の予約の使用状況について | Microsoft Docs
description: 使用状況を読み、従量課金制サブスクリプションに Azure の予約がどのように適用されているかを把握する方法について説明します。
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: manshuk
ms.openlocfilehash: cf1d7c67fe6033bf41317e75a33349ae07ecf643
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627885"
---
# <a name="understand-azure-reservation-usage-for-your-pay-as-you-go-subscription"></a>従量課金制サブスクリプションに適用される Azure の予約の使用状況について

予約の使用状況を評価するには、[[予約] ページ](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) の ReservationId と [Azure アカウント ポータル](https://account.azure.com)の使用状況ファイルを使用します。

Enterprise Agreement をご利用のお客様は、[エンタープライズ加入契約に適用される Azure の予約の使用状況](billing-understand-reserved-instance-usage-ea.md)に関するページを参照してください。

この記事は、単一のサブスクリプションに予約が適用されていることを前提として書かれています。 複数のサブスクリプションに予約が適用されている場合、複数の使用状況ファイル (CSV) に予約の特典がまたがる場合があります。

## <a name="usage-for-reserved-virtual-machine-instances"></a>予約仮想マシン インスタンスの使用状況

以降のセクションでは、Standard_DS1_v2 Windows VM が米国東部リージョンで実行され、予約 VM インスタンス情報が次の表のようになっていることを想定して説明します。

| フィールド | 値 |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|数量 |1|
|SKU | Standard_DS1_v2|
|リージョン | eastus |

デプロイされている VM が予約の属性と合致していることで特典が適用されるのは、VM のハードウェア部分です。 予約 VM インスタンスによって充当されない Windows ソフトウェアについては、「[Azure 予約インスタンスに含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)」を参照してください。

### <a name="statement-section-of-csv-file-for-vms"></a>VM の CSV ファイルの Statement セクション

CSV ファイルのこのセクションには、予約の合計使用量が表示されます。 **Meter Sub-category** フィールドに対して "**Reservation-**" を含むフィルターを適用します。 次のスクリーンショットのように表示されます。

![フィルター処理された予約の使用状況の詳細と料金のスクリーンショット](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

**Reservation-Base VM** 行には、予約によって充当される合計時間が表示されます。 予約によって充当されるため、この行は $0.00 です。 **Reservation-Windows Svr (1 Core)** 行は、Windows ソフトウェアのコストに充当されます。

### <a name="daily-usage-section-of-csv-file"></a>CSV ファイルの Daily Usage セクション

**Additional Info** にフィルターを適用し、自分の**予約 ID** を入力します。 次のスクリーンショットは、予約に関連したフィールドを示しています。

![毎日の使用状況の詳細と料金のスクリーンショット](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **Additional Info** フィールドの **ReservationId** は、VM に適用されている予約です。
2. **ConsumptionMeter** は、VM の測定 ID です。
3. **Meter Sub-category** の **Reservation-Base VM** 行は、Statement セクションの $0 コストを表します。 この VM の実行コストは、予約による前払いが済んでいます。
4. **Meter Id** は予約の測定 ID です。 この測定のコストは $0 です。 この測定 ID は、予約割引が適用される VM の場合に表示されます。
5. Standard_DS1_v2 は、vCPU を 1 個搭載した VM です。この VM は、Azure ハイブリッド特典なしでデプロイされます。 そのため、Windows ソフトウェアに対する追加料金は、この測定で充当されます。 D シリーズの 1 コア VM に対応する測定の確認方法については、「[Azure 予約インスタンスに含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)」を参照してください。 Azure ハイブリッド特典を利用している場合、この追加料金は適用されません。

## <a name="usage-for-sql-database-reserved-capacity-reservations"></a>SQL Database の予約容量の予約の使用状況

以降のセクションでは、SQL Database Gen 4 が米国東部リージョンで実行され、予約情報が次の表のようになっていることを想定して説明します。

| フィールド | 値 |
|---| --- |
|ReservationId |446ec809-423d-467c-8c5c-bbd5d22906b1|
|数量 |2|
|Product| SQL Database Gen 4 (2 Core)|
|リージョン | eastus |

### <a name="statement-section-of-csv-file"></a>CSV ファイルの Statement セクション

**Reserved Instance Usage** という測定名でフィルターを適用します。 次のスクリーンショットのように表示されます。

![SQL Database の予約容量の CSV ファイル](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

**Reserved Instance Usage** 行には、予約によって充当される合計コア時間が表示されます。 予約によってコストが充当されるため、この行の料金は $0 です。

### <a name="detail-section-of-csv-file"></a>CSV ファイルの Detail セクション

**Additional Info** にフィルターを適用し、自分の**予約 ID** を入力します。 次のスクリーンショットは、SQL Database 予約容量の予約に関連したフィールドを示しています。

![SQL Database の予約容量の CSV ファイル](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. **Additional Info** フィールドの **ReservationId** は、SQL データベース リソースに適用された SQL Database 予約容量の予約です。
2. **ConsumptionMeter** は、SQL データベース リソースの測定 ID です。
3. **Meter Id** は予約の測定です。 この測定のコストは $0 です。 予約割引が適用される SQL データベース リソースの場合、CSV ファイルにこの測定 ID が表示されます。

## <a name="next-steps"></a>次の手順

予約について詳しくは、次の記事を参照してください。

- [Azure の予約とは](billing-save-compute-costs-reservations.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database の容量が予約された SQL Database 計算リソースの前払い](../sql-database/sql-database-reserved-capacity.md)
- [Azure での予約の管理](billing-manage-reserved-vm-instance.md)
- [予約割引の適用方法について](billing-understand-vm-reservation-charges.md)
- [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)
- [予約に含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。
