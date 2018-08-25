---
title: 特定のラボ ポリシーに対するアクセス許可をユーザーに付与する | Microsoft Docs
description: 各ユーザーのニーズに基づいて DevTest ラボの特定のラボ ポリシーに対するアクセス許可をユーザーに付与する方法について説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 2b81c23b5cf9ea5d4bfc47d36ae251f762ffad11
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38539692"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>特定のラボ ポリシーに対するアクセス許可をユーザーに付与する
## <a name="overview"></a>概要
この記事では、PowerShell を使用して、特定のラボ ポリシーに対するアクセス許可をユーザーに付与する方法を説明します。 そうすることで、アクセス許可を各ユーザーのニーズに基づいて適用できます。 たとえば、特定のユーザーに、VM ポリシー設定は変更できるがコスト ポリシーは変更できない能力を付与することができます。

## <a name="policies-as-resources"></a>リソースとしてのポリシー
「 [Azure のロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md) 」の記事で説明されているように、RBAC を使用すると、Azure のリソースのアクセスをきめ細かく管理できます。 RBAC を使用して、開発チーム内で職務を分離し、職務に必要なアクセス権のみをユーザーに付与します。

DevTest ラボでは、ポリシーはリソースの種類の 1 つで、RBAC の操作 **Microsoft.DevTestLab/labs/policySets/policies/** を可能にするものです。 各ラボ ポリシーはこの種類のポリシー リソースのリソースであり、RBAC のロールにスコープとして割り当てることができます。

たとえば、**許可される VM サイズ** ポリシーに対する読み取り/書き込みアクセス許可をユーザーに付与するには、**Microsoft.DevTestLab/labs/policySets/policies/*** アクションを扱うカスタム ロールを作成し、**Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab** スコープ内でこのカスタム ロールに適切なユーザーを割り当てます。

RBAC のカスタム ロールの詳細については、[カスタム ロールのアクセス制御](../role-based-access-control/custom-roles.md)に関するページを参照してください。

## <a name="creating-a-lab-custom-role-using-powershell"></a>PowerShell を使用してラボ カスタム ロールを作成する
作業を開始する場合は、Azure PowerShell コマンドレットをインストールして構成する方法を説明する記事 ([https://azure.microsoft.com/blog/azps-1-0-pre](https://azure.microsoft.com/blog/azps-1-0-pre)) をお読みください。

Azure PowerShell コマンドレットを設定すると、次のタスクを実行できるようになります。

* リソース プロバイダーのすべての操作の一覧
* 特定のロールの操作の一覧:
* カスタム ロールの作成

次の PowerShell スクリプトは、これらのタスクを実行する方法の例を示しています。

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>カスタム ロールを使用して特定のポリシーに対しユーザーにアクセス許可を割り当てる
カスタム ロールを定義すると、ユーザーにカスタム ロールを割り当てられるようになります。 カスタム ロールをユーザーに割り当てるには、まず、そのユーザーを表す **ObjectId** を取得する必要があります。 そのためには、 **Get-AzureRmADUser** コマンドレットを使用します。

次の例では、 **SomeUser** ユーザーの *ObjectId* は 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 です。

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

ユーザーの **ObjectId** とカスタム ロール名を取得したら、**New-AzureRmRoleAssignment** コマンドレットを使用してユーザーにそのロールを割り当てることができます。

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

前の例では、 **AllowedVmSizesInLab** ポリシーを使用しました。 次のようなポリシーを使用することもできます。

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次の手順
特定のラボ ポリシーに対するアクセス許可をユーザーに付与した後で実行する手順として、以下のようなものがあります。

* [ラボへのアクセスをセキュリティで保護する](devtest-lab-add-devtest-user.md)
* [ラボのポリシーを設定する](devtest-lab-set-lab-policy.md)
* [ラボ テンプレートを作成する](devtest-lab-create-template.md)
* [VM のカスタム アーティファクトを作成する](devtest-lab-artifact-author.md)
* [VM をラボに追加する](devtest-lab-add-vm.md)

