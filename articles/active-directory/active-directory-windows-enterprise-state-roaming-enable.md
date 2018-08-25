---
title: Azure Active Directory の Enterprise State Roaming を有効にする | Microsoft Docs
description: Windows デバイスの Enterprise State Roaming の設定に関してよく寄せられる質問について取り上げます。 Enterprise State Roaming によって複数の Windows デバイスの使用環境が統一され、新しいデバイスを構成するために必要な時間が短縮されます。
services: active-directory
keywords: Enterprise State Roaming, Windows クラウド, Enterprise State Roaming を有効にする方法
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.component: devices
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.openlocfilehash: bb2210619e481189fc88ca3bb6b8044a8f5d7e14
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39262950"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Azure Active Directory の Enterprise State Roaming を有効にする
Enterprise State Roaming は、Azure AD Premium または Enterprise Mobility + Security (EMS) ライセンスを所有しているすべての組織が利用できます。 Azure AD サブスクリプションの取得方法の詳細について、[Azure AD の製品ページ](https://azure.microsoft.com/services/active-directory)を参照してください。

Enterprise State Roaming を有効にすると、組織には、Azure Information Protection の Azure Rights Management 保護を制限付きで無料使用できるライセンスが自動的に付与されます。 この無料サブスクリプションは、Enterprise State Roaming サービスで同期されたエンタープライズ設定データおよびアプリケーション データの暗号化と暗号化の解除に限定されます。 Azure Rights Management サービスのすべての機能を使用するには、[有料サブスクリプション](https://azure.microsoft.com/pricing/details/information-protection/)が必要です。

## <a name="to-enable-enterprise-state-roaming"></a>Enterprise State Roaming を有効にするには

1. [Azure AD 管理センター](https://aad.portal.azure.com/)にサインインします。

2. **[Azure Active Directory]** &gt; **[デバイス]** &gt; **[デバイスの設定]** の順に選択します。

3. **[デバイス間での設定とアプリ データの同期が許可されるユーザー]** を選択します。 詳細については、[デバイス設定の構成方法](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal)に関するページをご覧ください。
  
  ![[デバイス間での設定とアプリ データの同期が許可されるユーザー] のデバイス設定のイメージ](./media/active-directory-windows-enterprise-state-roaming-enable/device-settings.png)
  
Windows 10 デバイスで Enterprise State Roaming サービスを使用するには、そのデバイスを Azure AD の ID で認証する必要があります。 デバイスが Azure AD に参加している場合、そのユーザーの主要なサインイン ID は Azure AD の ID です。追加の構成は必要ありません。 デバイスがオンプレミスの Active Directory を使用している場合、IT 管理者は [Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する](active-directory-azureadjoin-devices-group-policy.md)必要があります。

## <a name="data-storage"></a>データ ストレージ
Enterprise State Roaming のデータは、Azure Active Directory のインスタンスに設定されている国/リージョンの値と最も適合する 1 つ以上の [Azure リージョン](https://azure.microsoft.com/regions/)でホストされます。 Enterprise State Roaming データは、北米、EMEA、および APAC という主な 3 つの地域リージョンに基づいてパーティション分割されます。 テナントの Enterprise State Roaming データは、地域リージョンでローカルに配置されますが、リージョン間ではレプリケートされません。  例: 
国/リージョンの値 | データがホストされる場所
---------------------|-------------------------
EMEA。"フランス"、"ザンビア" など | ヨーロッパ内の 1 つ以上の Azure リージョン 
北米。"米国"、"カナダ" など | 米国内の 1 つ以上の Azure リージョン
APAC。"オーストラリア"、"ニュージーランド" など | アジア内の 1 つ以上の Azure リージョン
南米/南極地域 | 米国内の 1 つ以上の Azure リージョン

国/リージョンの値は Azure AD ディレクトリの作成プロセスで設定され、後から変更することはできません。 データの保管場所についてさらに詳しい情報が必要な場合は、[Azure サポート](https://azure.microsoft.com/support/options/)にチケットを提出してください。

## <a name="view-per-user-device-sync-status"></a>ユーザーごとのデバイス同期状態の表示
次の手順に従って、ユーザーごとのデバイス同期状態のレポートを表示します。

1. [Azure AD 管理センター](https://aad.portal.azure.com/)にサインインします。

2. **[Azure Active Directory]** &gt; **[ユーザー]** &gt; **[すべてのユーザー]** の順に選択します。

3. ユーザーを選択し、**[デバイス]** を選択します。

4. **[表示]** で、**[Devices syncing settings and app data]\(設定やアプリ データを同期しているデバイス)** を選択して、同期状態を表示します。
  
  ![デバイス同期データ設定のイメージ](./media/active-directory-windows-enterprise-state-roaming-enable/sync-status.png)
  
5. このユーザーに対して同期しているデバイスがある場合は、そのデバイスは次のように表示されます。
  
  ![デバイス同期列指向データのイメージ](./media/active-directory-windows-enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>データの保持
Enterprise State Roaming を使用して Azure と同期されたデータは、手動で削除されるまで、または該当するデータが古いデータとして判断されるまで保持されます。 

### <a name="explicit-deletion"></a>明示的な削除
明示的な削除とは、Azure 管理者がユーザーまたはディレクトリを削除するか、データを削除することを明示的に要求したときの削除です。

* **ユーザーの削除**: Azure AD でユーザーが削除された場合、ユーザー アカウント ローミング データは 90 ～ 180 日後に削除されます。 
* **ディレクトリの削除**: Azure AD からディレクトリ全体を削除した場合は、その操作が直ちに実行されます。 そのディレクトリに関連付けられた設定データはすべて、90 ～ 180 日後に削除されます。 
* **要求による削除**: Azure AD 管理者の方は、特定のユーザーのデータまたは設定データを手動で削除する必要がある場合、 [Azure サポート](https://azure.microsoft.com/support/)にチケットを提出してください。 

### <a name="stale-data-deletion"></a>古いデータの削除
1 年間 ("リテンション期間") にわたってアクセスされていないデータは古いデータと見なされ、Azure から削除される場合があります。 リテンション期間は変更されることがありますが、90 日未満になることはありません。 Windows またはアプリケーションの特定の設定のまとまりや、ユーザーの全設定が古いデータと見なされることもあります。 例: 

* 特定の設定全体にデバイスからのアクセスがない場合 (アプリケーションがデバイスから削除された場合や、特定のユーザーの全デバイスで "テーマ" などの設定グループが無効にされた場合など)、リテンション期間の経過後その設定全体が古いと見なされ、削除される可能性があります。 
* ユーザーがそのすべてのデバイスで設定の同期をオフにした場合、いずれの設定データもアクセスされなくなり、リテンション期間の経過後にそのユーザーのすべての設定データが古いと見なされ、削除される可能性があります。 
* Azure AD ディレクトリ管理者がディレクトリ全体の Enterprise State Roaming をオフにした場合、そのディレクトリ内のすべてのユーザーについて設定の同期が中止され、リテンション期間後すべてのユーザーの全設定データが古いデータとなって、削除される可能性があります。 

### <a name="deleted-data-recovery"></a>削除したデータの回復
データ保持ポリシーを構成することはできません。 一度完全に削除されたデータは復旧できません。 ただし、設定データが削除されるのは Azure からのみです。エンド ユーザーのデバイスからは削除されません。 その後、いずれかのデバイスから Enterprise State Roaming サービスに再接続すると、設定が再同期され、Azure に保存されます。

## <a name="related-topics"></a>関連トピック
* [Enterprise State Roaming の概要](active-directory-windows-enterprise-state-roaming-overview.md)
* [設定とデータのローミングに関する FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
* [設定を同期させるためのグループ ポリシーと MDM の設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 ローミング設定リファレンス](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [トラブルシューティング](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
