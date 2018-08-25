---
title: エンタープライズに適用される Azure の予約の使用状況について | Microsoft Docs
description: 使用状況を読み、エンタープライズ加入契約に Azure の予約がどのように適用されているかを把握する方法について説明します。
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
ms.openlocfilehash: 5ce0103315f297996ed3f3bd88b5e53558e22e14
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628234"
---
# <a name="understand-azure-reservation-usage-for-your-enterprise-enrollment"></a>エンタープライズ加入契約に適用される Azure の予約の使用状況について

予約の使用状況を評価するには、**[予約]** ページ の [ReservationId](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) と [EA ポータル](https://ea.azure.com)の使用状況ファイルを使用します。 予約の使用状況は、[EA ポータル](https://ea.azure.com)の使用状況の概要セクションで確認することもできます。

従量課金制の課金コンテキストで予約を購入した場合は、[従量課金制サブスクリプションの予約の使用状況](billing-understand-reserved-instance-usage.md)に関するページを参照してください。

## <a name="usage-for-reserved-virtual-machines-instances"></a>予約仮想マシン インスタンスの使用状況

以降のセクションでは、Standard_D1_v2 Windows VM が米国東部リージョンで実行され、予約情報が次の表のようになっていることを想定して説明します。

| フィールド | 値 |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|数量 |1|
|SKU | Standard_D1|
|リージョン | eastus |

デプロイされている VM が予約の属性と合致していることで特典が適用されるのは、VM のハードウェア部分です。 予約によって充当されない Windows ソフトウェアについては、「[Azure 予約インスタンスに含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)」を参照してください。

### <a name="usage-in-csv-file-for-reserved-vm-instances"></a>予約 VM インスタンスの CSV ファイルの使用状況

エンタープライズの使用状況の CSV ファイルは Enterprise Portal からダウンロードできます。 CSV ファイルで、**Additional Info** にフィルターを適用し、自分の **ReservationID** を入力します。 次のスクリーンショットは、予約に関連したフィールドを示しています。

![Azure の予約の Enterprise Agreement (EA) CSV](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **Additional Info** フィールドの **ReservationId** は、VM に適用されている予約を表します。
2. **ConsumptionMeter** は、VM の測定 ID です。
3. **Meter Id** は、コストが $0 の予約の測定です。 実行中の VM のコストは、予約 VM インスタンスによって支払われます。
4. Standard_D1 は、vCPU を 1 個搭載した VM です。この VM は、Azure ハイブリッド特典なしでデプロイされます。 そのため、Windows ソフトウェアに対する追加料金は、この測定で充当されます。 D シリーズの 1 コア VM に対応する測定の確認方法については、「[Azure 予約インスタンスに含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)」を参照してください。  Azure ハイブリッド特典を利用している場合、この追加料金は適用されません。

## <a name="usage-for-sql-database-reserved-capacity-reservations"></a>SQL Database の予約容量の予約の使用状況

以降のセクションでは、SQL Database Gen 4 が米国東部リージョンで実行され、予約情報が次の表のようになっていることを想定して説明します。

| フィールド | 値 |
|---| --- |
|ReservationId |8244e673-83e9-45ad-b54b-3f5295d37cae|
|数量 |2|
|Product| SQL Database Gen 4 (2 Core)|
|リージョン | eastus |

### <a name="usage-in-csv-file-for-sql-database-reserved-capacity"></a>SQL Database の予約容量の CSV ファイルの使用状況

**Additional Info** にフィルターを適用し、自分の**予約 ID** を入力します。 次のスクリーンショットは、予約に関連したフィールドを示しています。

![SQL Database 予約容量の Enterprise Agreement (EA) の CSV](./media/billing-understand-reserved-instance-usage-ea/billing-ea-sql-db-reserved-capacity-csv.png)

1. **Additional Info** フィールドの **ReservationId** は、SQL データベース リソースに適用された予約です。
2. **ConsumptionMeter** は、SQL データベース リソースの測定 ID です。
3. **Meter Id** は、コストが $0 の予約の測定です。 予約が適用される SQL データベース リソースの場合、CSV ファイルにこの測定 ID が表示されます。

## <a name="usage-summary-page-in-enterprise-portal"></a>Enterprise Portal の使用状況の概要ページ

Azure の予約の使用状況は、Enterprise Portal の使用状況の概要セクションにも表示されます。![Enterprise Agreement (EA) の使用状況の概要](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. VM のハードウェア コンポーネントは、予約によって充当されるため、料金は発生しません。 SQL Database の予約の場合、Azure SQL Database の予約容量として **Service Name** 行が表示されます。
2. この例では、Azure ハイブリッド特典がないため、VM で使用される Windows ソフトウェアには課金されます。

## <a name="next-steps"></a>次の手順

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](billing-save-compute-costs-reservations.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database の容量が予約された SQL Database 計算リソースの前払い](../sql-database/sql-database-reserved-capacity.md) 
- [Azure の予約の管理](billing-manage-reserved-vm-instance.md)
- [予約割引の適用方法について](billing-understand-vm-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](billing-understand-reserved-instance-usage.md)
- [予約に含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。