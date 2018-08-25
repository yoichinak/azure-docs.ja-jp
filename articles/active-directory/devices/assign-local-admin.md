---
title: Azure AD 参加済みデバイスのローカル管理者グループの管理方法 | Microsoft Docs
description: Windows デバイスのローカル管理者グループに Azure のロールを割り当てる方法について説明します。
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
ms.date: 08/09/2018
ms.author: markvi
ms.reviewer: ravenn
ms.openlocfilehash: b72cab6ce90f0cbb74afd3e00a9aec73fb2908f7
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003782"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Azure AD 参加済みデバイスのローカル管理者グループの管理方法

Windows デバイスを管理するには、ローカル管理者グループのメンバーになる必要があります。 Azure Active Directory (Azure AD) では、Azure AD の参加プロセス時に、デバイス上でのこのグループのメンバーシップが更新されます。 メンバーシップの更新方法は、ビジネス要件に応じてカスタマイズすることもできます。 メンバーシップの更新は、たとえば、デバイスへの管理者権限を要するタスクをヘルプデスク スタッフが実行できるようにするうえで役立ちます。

この記事では、Azure AD 参加中のメンバーシップの更新のしくみと、そのカスタマイズ方法について説明します。 この記事の内容は、**ハイブリッド** Azure AD 参加には適用されません。


## <a name="how-it-works"></a>動作のしくみ

Azure AD 参加を使用して Windows デバイスを Azure AD に接続すると、Azure AD によって、デバイスのローカル管理者グループに次のセキュリティ原則が追加されます。

- Azure AD のグローバル管理者ロール
- Azure AD のデバイス管理者ロール 
- Azure AD 参加を実行するユーザー   

ローカル管理者グループに Azure AD ロールを追加すれば、デバイスに一切変更を加えることなく、デバイスの管理ユーザーを Azure AD でいつでも更新できます。 現時点では、管理者ロールにグループを割り当てることはできません。
また、Azure AD では、最小権限の原則 (PoLP) をサポートするために、Azure AD のデバイス管理者ロールもローカル管理者グループに追加されます。 グローバル管理者だけでなく、デバイス管理者ロール*だけ*を割り当てられたユーザーにも、デバイスの管理を許可することができます。 


## <a name="manage-the-global-administrators-role"></a>グローバル管理者ロールの管理

グローバル管理者ロールのメンバーシップを表示する方法や更新する方法については、次の記事をご覧ください。

- [Azure Active Directory の管理者ロールのすべてのメンバーを表示する](../users-groups-roles/directory-manage-roles-portal.md)

- [Azure Active Directory でユーザーを管理者ロールに割り当てる](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>デバイス管理者ロールの管理 

デバイス管理者ロールは、Azure Portal の **[デバイス]** ページで管理できます。 **[デバイス]** ページを開くには、次に手順を実行します。

1. [Azure Portal](https://portal.azure.com) に、グローバル管理者またはデバイス管理者としてサインインします。
2. 左側のナビゲーション バーで、**[Azure Active Directory]** をクリックします。 
3. **[管理]** セクションで、**[デバイス]** をクリックします。
4. **[デバイス]** ページで、**[デバイスの設定]** をクリックします。

デバイス管理者ロールを変更するには、**[Azure AD 参加済みデバイスの追加のローカル管理者]** を構成します。  

![追加のローカル管理者](./media/assign-local-admin/10.png)

 
デバイス管理者は、すべての Azure AD 参加済みデバイスに割り当てられます。 デバイス管理者の対象範囲を特定のデバイス セットに限定することはできません。 デバイス管理者ロールを更新しても、対象のユーザーにすぐに影響が及ぶとは限りません。 ユーザーが既にサインインしているデバイスの場合、権限の更新は次のタイミングで発生します。
     

- ユーザーがサインオフしたとき。
- 4 時間が経過し、新しいプライマリ更新トークンが発行されたとき。 




## <a name="manage-regular-users"></a>通常のユーザーの管理

既定では、Azure AD 参加を実行するユーザーはデバイスの管理者グループに追加されます。 通常のユーザーがローカル管理者になることを防ぐ必要がある場合は、次のオプションで対応できます。

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) - Windows Autopilot には、参加を実行するプライマリ ユーザーがローカル管理者になるのを防ぐオプションがあります。 これは、[Autopilot プロファイルを作成する](https://docs.microsoft.com/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)ことで達成できます。
 
- [一括登録](https://docs.microsoft.com/intune/windows-bulk-enroll) - 一括登録のコンテキストで実行される Azure AD 参加は、自動作成されたユーザーのコンテキストで発生します。 デバイスの参加後にサインインしたユーザーは、管理者グループには追加されません。   



## <a name="manually-elevate-a-user-on-a-device"></a>デバイスのユーザーを手動で昇格させる 

Azure AD の参加プロセスを使用するのではなく、通常のユーザーを手動で昇格させて、特定のデバイスのローカル管理者にすることもできます。 この手順を実行するには、既にローカル管理者グループのメンバーになっている必要があります。 

**Windows 10 1709** リリース以降では、**[設定] -> [アカウント] -> [その他のユーザー]** から **[職場または学校のユーザーを追加]** を選択することで、このタスクを実行できます。
 
また、コマンド プロンプトを使用してユーザーを追加することもできます。

- テナント ユーザーがオンプレミスの Active Directory から同期された場合は、`net localgroup administrators /add “Contoso\username”` を使用します。

- テナント ユーザーが Azure AD で作成された場合は、`net localgroup administrators /add “AzureAD\UserUpn”` を使用します


## <a name="considerations"></a>考慮事項 

デバイス管理者ロールにグループを割り当てることはできません。割り当てることができるのは、個別のユーザーだけです。

デバイス管理者は、すべての Azure AD 参加済みデバイスに割り当てられます。 対象を特定のデバイス セットに限定することはできません。

デバイス管理者ロールからユーザーを削除しても、ユーザーがデバイスにサインインしている間は、ローカル管理者特権が維持されます。 特権は、次回サインインするときか、4 時間が経過して新しいプライマリ更新トークンが発行されるときに失効します。



## <a name="next-steps"></a>次の手順

- Azure Portal でデバイスを管理する方法の概要については、[Azure Portal によるデバイスの管理](device-management-azure-portal.md)に関するページを参照してください

- デバイスベースの条件付きアクセスについて詳しくは、「[Azure Active Directory 接続アプリケーションに対するデバイスベースの条件付きアクセス ポリシーを設定する方法](../conditional-access/require-managed-devices.md)」を参照してください。


