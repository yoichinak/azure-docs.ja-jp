---
title: Azure Portal によるデバイスの管理 | Microsoft Docs
description: Azure Portal を使用してデバイスを管理する方法を説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: fd810f6e5a4211f64044a88ae6ca6bb6dc044e28
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39416115"
---
# <a name="managing-devices-using-the-azure-portal"></a>Azure Portal によるデバイスの管理


Azure Active Directory (Azure AD) のデバイス管理を使用して、ユーザーがセキュリティとコンプライアンスの基準と一致するデバイスからリソースにアクセスしていることを保証できます。 

この記事の内容は次のとおりです。

- [Azure Active Directory でのデバイス管理の概要](overview.md)を理解していることを前提とします

- Azure Portal を使用してデバイスを管理する方法について説明します

## <a name="manage-devices"></a>デバイスを管理する 

Azure Portal は、お客様のデバイスを一元的に管理する場所を提供します。 この場所に移動するには、[直接リンク](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)を使用するか、または次の手動の手順に従います。

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。

2. 左側のナビゲーション バーで、**[Active Directory]** をクリックします。

    ![デバイス設定の構成](./media/device-management-azure-portal/01.png)

3. **[管理]** セクションで、**[デバイス]** をクリックします。

    ![デバイス設定の構成](./media/device-management-azure-portal/11.png)
 
**[デバイス]** ページでは、次の操作が可能です。

- デバイス管理設定の構成

- デバイスの検索

- デバイス管理タスクの実行

- デバイス管理に関連する監査ログの確認  
  

## <a name="configure-device-settings"></a>デバイス設定の構成

Azure Portal を使ってデバイスを管理するには、デバイスが Azure AD に[登録されているか参加している](overview.md#getting-devices-under-the-control-of-azure-ad)必要があります。 管理者は、デバイスの設定を構成することによって、デバイスの登録および参加のプロセスを調整できます。 

![デバイス設定の構成](./media/device-management-azure-portal/22.png)

デバイス設定ページでは、以下の構成を行うことができます。

![Intune デバイスの管理](./media/device-management-azure-portal/21.png)


- **[ユーザーはデバイスを Azure AD に参加させることができます]** - この設定では、Azure AD に[デバイスを参加させる](overview.md#azure-ad-joined-devices)ことができるユーザーを選ぶことができます。 既定値は **[すべて]** です。

- **[Azure AD 参加済みデバイスの追加のローカル管理者]** - デバイスに対するローカル管理者権限が付与されるユーザーを選択できます。 ここに追加されたユーザーは、Azure AD の "*デバイス管理者*" ロールに追加されます。 Azure AD のグローバル管理者とデバイスの所有者には、既定でローカル管理者権限が付与されます。 このオプションは、Azure AD Premium や Enterprise Mobility Suite (EMS) などの製品を通じて使用できる Premium Edition 機能です。 

- **[ユーザーはデバイスを Azure AD に登録できます]** - デバイスを Azure AD に[登録](overview.md#azure-ad-registered-devices)できるようにするには、この設定を構成する必要があります。 **[なし]** を選択すると、デバイスは Azure AD 参加済みかハイブリッド Azure AD 参加済みでない場合に、登録を許可されません。 Microsoft Intune または Mobile Device Management (MDM) for Office 365 への登録には、この登録が必要です。 これらのサービスのいずれかを構成した場合は、**[すべて]** が選択され、**[なし]** は選択できなくなります。

- **[デバイスを参加させるには Multi-factor Auth が必要]** - デバイスを Azure AD に[参加](overview.md#azure-ad-joined-devices)させるときに、ユーザーが 2 番目の認証要素の提供を求められるようにするかどうかを選ぶことができます。 既定値は **[いいえ]** です。 デバイスの登録時に多要素認証を必要とすることをお勧めします。 このサービスの多要素認証を有効にする前に、デバイスを登録するユーザーに対して多要素認証が構成されていることを確認する必要があります。 他の Azure Multi-Factor Authentication サービスの詳細については、[Azure Multi-Factor Authentication の概要](../authentication/concept-mfa-whichversion.md)に関するページを参照してください。 

- **[デバイスの最大数]** - この設定では、Azure AD でユーザーが持つことができるデバイスの最大数を選択できます。 ユーザーがこのクォータに達した場合、1 つ以上の既存のデバイスを削除するまでデバイスを追加できなくなります。 デバイス クォータには、現時点で Azure AD 参加済みまたは Azure AD 登録済みのすべてのデバイスがカウントされます。 既定値は **20** です。

- **[デバイス間での設定とアプリ データの同期が許可されるユーザー]** - 既定では、この設定は **[なし]** に設定されています。 特定のユーザーまたはグループ、あるいは [すべて] を選択すると、ユーザーの設定とアプリ データを Windows 10 デバイス間で同期させることができます。 Windows 10 の同期のしくみの詳細を学習してください。
このオプションは、Azure AD Premium や Enterprise Mobility Suite (EMS) などの製品を通じて使用できる Premium 機能です。
 




## <a name="locate-devices"></a>デバイスの検索

登録済みおよび参加済みのデバイスを検索するには、次の 2 つのオプションがあります。

- **[デバイス]** ページの **[管理]** セクションにある **[すべてのデバイス]**  

    ![すべてのデバイス](./media/device-management-azure-portal/41.png)


- **[ユーザー]** ページの **[管理]** セクションにある **[デバイス]**
 
    ![すべてのデバイス](./media/device-management-azure-portal/43.png)



どちらのオプションでも、次のようなビューが表示されます。


- 表示名をフィルターとして使用してデバイスを検索できる。

- 登録済みおよび参加済みデバイスの概要情報を確認できる

- 一般的なデバイス管理タスクを実行できる
   

![すべてのデバイス](./media/device-management-azure-portal/51.png)

一部の iOS デバイスの名前で使用されているアポストロフィは、アポストロフィに見える別の文字である可能性があります。 このため、このようなデバイスを検索するときは少し注意が必要です。正しい検索結果が表示されない場合は、検索文字列のアポストロフィが、正しいアポストロフィ文字であることを確認してください。

## <a name="device-management-tasks"></a>デバイス管理タスク

管理者は、登録済みまたは参加済みデバイスを管理することができます。 このセクションでは、一般的なデバイス管理タスクについて説明します。


### <a name="manage-an-intune-device"></a>Intune デバイスの管理

Intune 管理者の場合は、**Microsoft Intune** としてマークされているデバイスを管理することができます。 管理者にはその他のデバイスも表示されます 

![Intune デバイスの管理](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>Azure AD デバイスを有効/無効にする

デバイスを有効/無効にするには、次の 2 つのオプションがあります。

- **[すべてのデバイス]** ページのタスク メニュー [...]

    ![Intune デバイスの管理](./media/device-management-azure-portal/71.png)

- **[デバイス]** ページのツール バー

    ![Intune デバイスの管理](./media/device-management-azure-portal/32.png)


**解説:**

- デバイスを有効/無効にするには、Azure AD のグローバル管理者である必要があります。 
- デバイスを無効にすると、デバイスは Azure AD リソースにアクセスできなくなります。 



### <a name="delete-an-azure-ad-device"></a>Azure AD デバイスを削除する

デバイスを削除するには、次の 2 つのオプションがあります。

- **[すべてのデバイス]** ページのタスク メニュー [...]

    ![Intune デバイスの管理](./media/device-management-azure-portal/72.png)

- **[デバイス]** ページのツール バー

    ![デバイスを削除する](./media/device-management-azure-portal/34.png)


**解説:**

- デバイスを削除するには、Azure AD のグローバル管理者または Intune 管理者である必要があります。

- デバイスを削除する場合、以下の点に注意してください。
 
    - デバイスは Azure AD リソースにアクセスできなくなります。 

    - Windows デバイスの BitLocker キーなど、デバイスに関連付けられているすべての詳細が削除されます。  

    - 削除は回復不可能な操作であり、必須の場合以外は推奨されません。

デバイスが別の管理機関 (Microsoft Intune など) によって管理されている場合は、Azure AD でデバイスを削除する前に、デバイスがワイプ/使用中止されているかどうかを確認してください。

 


### <a name="view-or-copy-device-id"></a>デバイス ID を表示またはコピーする

デバイス ID を使うと、デバイス上で、またはトラブルシューティング中に PowerShell を使用して、デバイス ID の詳細を確認することができます。 コピー オプションにアクセスするには、デバイスをクリックします。

![デバイス ID の表示](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>BitLocker キーを表示またはコピーする

暗号化されたドライブのユーザーによる復旧を支援するため、BitLocker キーを表示またはコピーできます。 これらのキーを使用できるのは、暗号化され、キーが Azure AD に格納されている Windows デバイスだけです。 デバイスの詳細にアクセスする場合は、これらのキーをコピーすることができます。
 
![BitLocker キーの表示](./media/device-management-azure-portal/36.png)

BitLocker キーを表示またはコピーするには、デバイスの所有者であるか、次のロールが 1 つ以上割り当てられているユーザーである必要があります。

- グローバル管理者
- ヘルプデスク管理者
- セキュリティ管理者
- セキュリティ閲覧者
- Intune サービス管理者

> [!NOTE]
> ハイブリッド Azure AD 参加済みの Windows 10 デバイスには、所有者がありません。 そのため、所有者でデバイスを検索していて、見つからなかった場合は、デバイス ID で検索してください。


## <a name="audit-logs"></a>監査ログ


デバイスのアクティビティは、アクティビティ ログを通じて利用できます。 これには、デバイス登録サービスおよびユーザーによってトリガーされる、次のようなアクティビティが含まれます。

- デバイスの作成と、デバイスへの所有者/ユーザーの追加

- デバイス設定への変更

- デバイスの削除や更新などのデバイス操作
 
監査データへのエントリ ポイントは、**[デバイス]** ページの **[アクティビティ]** セクションの **[監査ログ]** です。

![監査ログ](./media/device-management-azure-portal/61.png)


監査ログには、次のものを示す既定のリスト ビューがあります。

- 発生の日付と時刻

- ターゲット

- アクティビティのイニシエーターまたはアクター ("だれが" を指す)

- アクティビティ ("どうした" を指す)

![監査ログ](./media/device-management-azure-portal/63.png)

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。
 
![監査ログ](./media/device-management-azure-portal/64.png)


報告されるデータを有用なものだけに絞り込むために、次のフィールドを使用して監査データをフィルター処理できます。

- Category
- アクティビティのリソースの種類
- アクティビティ
- 期間
- ターゲット
- 開始者 (アクター)

フィルターだけでなく、特定のエントリを検索することができます。

![監査ログ](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>次の手順

* [Azure Active Directory のデバイス管理の概要](overview.md)



