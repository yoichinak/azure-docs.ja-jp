---
title: Azure の予約割引の概要 | Microsoft Docs
description: 実行中の SQL データベースに予約割引がどのように適用されるかについて説明します。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: yashar
ms.openlocfilehash: 3a5339200309a50eace5b48dfbd9127b7eb1dae6
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39632970"
---
# <a name="understand-how-an-azure-reservation-discount-is-applied-to-sql-databases"></a>SQL データベースに対する Azure の予約割引の適用方法

Azure SQL Database の予約容量を購入すると、予約の属性や数量に合致する SQL データベースに対して予約割引が自動的に適用されます。 予約購入分は、ご利用の SQL データベースの計算コストに充当されます。 ソフトウェア、ストレージ、ネットワークについては、通常料金が適用されます。 [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を SQL データベースのライセンス コストに充当することができます。

予約仮想マシン インスタンスについては、「[Azure 予約 VM インスタンスの割引について](billing-understand-vm-reservation-charges.md)」を参照してください。

## <a name="reservation-discount-applied-to-sql-databases"></a>SQL データベースに適用される予約割引

 SQL Database の予約容量割引は、実行中の SQL データベースに 1 時間単位で適用されます。 購入した予約は、実行中の SQL データベースによる計算の使用量と照合されます。 実行時間が 1 時間に満たない SQL データベースの場合、その予約は予約の属性に一致する他の SQL データベースに自動的に適用されます。 この割引は、同時に実行されている SQL データベースに適用されます。 予約の属性に一致する SQL データベースのうち、実行時間が 1 時間を超える SQL データベースがない場合は、その時間について予約割引の特典を完全に活用することができません。

次の例は、購入したコア数と実行する時間に応じて、SQL Database の予約容量割引がどのように適用されるかを示しています。

- シナリオ 1: 8 コア SQL データベース用に SQL Database の予約容量を購入します。 予約の他の属性と一致する 16 コア SQL データベースを実行しています。 SQL データベースの計算使用量のうち 8 コア分には従量課金制の料金が適用されます。 8 コア SQL データベースの計算使用量の 1 時間分には予約割引が適用されます。

以降の例では、購入する SQL Database の予約容量は、16 コア SQL データベース用であり、残りの予約の属性は実行中の SQL データベースと一致するものとします。

- シナリオ 2: それぞれ 8 コアの SQL データベース 2 つを 1 時間実行します。 16 コアの予約割引は、8 コア SQL データベースの両方の計算使用量に適用されます。
- シナリオ 3: 午後 1 時から午後 1 時 30 分まで 1 つの 16 コア SQL データベースを実行します。 午後 1 時 30 分から午後 2 時まで、別の 16 コア SQL データベースを実行します。 いずれも予約割引が適用されます。
- シナリオ 4: 午後 1 時から午後 1 時 45 分まで 1 つの 16 コア SQL データベースを実行します。 午後 1 時 30 分から午後 2 時まで、別の 16 コア SQL データベースを実行します。 15 分間の重復分には、従量課金制の料金が適用されます。 残りの時間の計算使用量には、予約割引が適用されます。

Azure の予約の適用状況を把握し、課金の使用状況レポートで確認する方法については、[Azure の予約の使用状況](https://go.microsoft.com/fwlink/?linkid=862757)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](billing-save-compute-costs-reservations.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database の容量が予約された SQL Database 計算リソースの前払い](../sql-database/sql-database-reserved-capacity.md)
- [Azure の予約の管理](billing-manage-reserved-vm-instance.md)
- [従量課金制サブスクリプションの予約使用量について](billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)
- [CSP サブスクリプションの予約の使用状況について](https://docs.microsoft.com/partner-center/azure-reservations)


## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

まだ他に質問がある場合は、問題を迅速に解決できるよう [サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ください。
