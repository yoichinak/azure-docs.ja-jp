---
title: Azure PowerShell を使用してカスタム ロールを作成する | Microsoft Docs
description: Azure PowerShell を使用して、ロールベースのアクセス制御 (RBAC) のカスタム ロールを作成する方法について説明します。 これには、カスタム ロールを一覧表示、作成、更新、削除する方法が含まれます。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c8e5f34bb6b38a3f187d86a1ebc0c7019c7f1046
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437020"
---
# <a name="create-custom-roles-using-azure-powershell"></a>Azure PowerShell を使用してカスタム ロールを作成する

[組み込みロール](built-in-roles.md)が組織の特定のニーズを満たさない場合は、独自のカスタム ロールを作成することができます。 この記事では、Azure PowerShell を使用し、カスタム ロールを作成して管理する方法について説明します。

## <a name="prerequisites"></a>前提条件

カスタム ロールを作成するには、次のものが必要です。

- [所有者](built-in-roles.md#owner)や[ユーザー アクセス管理者](built-in-roles.md#user-access-administrator)など、カスタム ロールを作成するためのアクセス許可
- ローカルにインストールされた [Azure PowerShell](/powershell/azure/install-azurerm-ps)

## <a name="list-custom-roles"></a>カスタム ロールの一覧表示

特定のスコープで割り当て可能なロールを一覧表示するには、[Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) コマンドを使用します。 次の例では、選択したサブスクリプションで割り当て可能なすべてのロールが一覧表示されます。

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

次の例では、選択したサブスクリプションで割り当て可能なカスタム ロールだけが一覧表示されます。

```azurepowershell
Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

選択したサブスクリプションがロールの `AssignableScopes` にない場合、カスタム ロールは一覧表示されません。

## <a name="create-a-custom-role"></a>カスタム ロールの作成

カスタム ロールを作成するには、[New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) コマンドを使用します。 ロールを構成する方法は 2 つあります。`PSRoleDefinition` オブジェクトを使用するか JSON テンプレートを使用します。 

### <a name="get-operations-for-a-resource-provider"></a>リソース プロバイダー操作を取得する

カスタム ロールを作成するときは、リソース プロバイダーから可能なすべての操作を理解しておくことが重要です。
この情報を取得するには、[リソース プロバイダー操作](resource-provider-operations.md)のリストを表示するか、[Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) コマンドを使用します。
たとえば、仮想マシンで使用可能なすべての操作を確認する場合は、次のコマンドを使います。

```azurepowershell
Get-AzureRMProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>PSRoleDefinition オブジェクトを使用したカスタム ロールの作成

PowerShell を使ってカスタム ロールを作成する場合は、[組み込みのロール](built-in-roles.md)を出発点として使うことも、ゼロから始めることもできます。 このセクションの最初の例では、組み込みのロールから始めて、さらに多くのアクセス許可を持つようにカスタマイズします。 その属性を編集し、必要に応じて `Actions`、`NotActions`、または `AssignableScopes`を追加して、変更内容を新しいロールとして保存します。

以下の例では、[仮想マシンの共同作業者](built-in-roles.md#virtual-machine-contributor)組み込みロールを土台として、*仮想マシン オペレーター*というカスタム ロールを作成しています。 この新しいロールは、*Microsoft.Compute*、*Microsoft.Storage*、*Microsoft.Network* リソース プロバイダーのすべての読み取り操作を許可し、仮想マシンの起動、再起動、監視を許可します。 カスタム ロールは 2 つのサブスクリプションで使用できます。

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzureRmRoleDefinition -Role $role
```

次の例では、*仮想マシン オペレーター* カスタム ロールを作成する別の方法を示しています。 まず、新しい `PSRoleDefinition` オブジェクトを作成します。 このアクション操作は、`perms` 変数で指定され、`Actions` プロパティに設定されます。 `NotActions` プロパティは、[仮想マシンの共同作成者](built-in-roles.md#virtual-machine-contributor)組み込みロールから `NotActions` を読み取ることで設定されます。 [仮想マシンの共同作成者](built-in-roles.md#virtual-machine-contributor)には `NotActions` がないため、この行は必要ありませんが、別のロールから情報を取得する方法を示しています。

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read','Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzureRmRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzureRmRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>JSON テンプレートを使用したカスタム ロールの作成

カスタム ロールのソース定義として JSON テンプレートを使用できます。 次の例では、ストレージと計算リソースへの読み取りアクセス、サポートへのアクセスを許可するカスタム ロールを作成し、そのロールを 2 つのサブスクリプションに追加します。 以下のコード例を含んだ新しいファイル `C:\CustomRoles\customrole1.json` を作成します。 新しい ID が自動的に生成されるため、最初のロール作成では ID を `null` に設定する必要があります。 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

ロールをサブスクリプションに追加するには、次の PowerShell コマンドを実行します。

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>カスタム ロールの更新

カスタム ロールの作成と同様に、`PSRoleDefinition` オブジェクトまたは JSON テンプレートを使用して既存のカスタム ロールを変更できます。

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>PSRoleDefinition オブジェクトを使用したカスタム ロールの更新

カスタム ロールを修正するには、まず [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) コマンドを使用してロール定義を取得します。 次に、必要に応じてロール定義を変更します。 最後に、[Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) コマンドを使用して変更したロール定義を保存します。

次の例では、 `Microsoft.Insights/diagnosticSettings/*` 操作が *仮想マシン オペレーター* カスタム ロールに追加されます。

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzureRmRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

次の例では、Azure サブスクリプションが *仮想マシン オペレーター* カスタム ロールの割り当て可能なスコープに追加されます。

```azurepowershell
Get-AzureRmSubscription -SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzureRmSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzureRmRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

### <a name="update-a-custom-role-with-a-json-template"></a>JSON テンプレートを使用したカスタム ロールの更新

前の JSON テンプレートを使用して、既存のカスタム ロールを簡単に変更して、操作を追加または削除できます。 次の例のように、JSON テンプレートを更新し、ネットワークの読み取り操作を追加します。 テンプレートに示されている定義は、既存の定義に累積的には適用されません。つまり、ロールは、テンプレートに指定したとおりに表れます。 また、Id フィールドをロールの ID で更新する必要もあります。 この値が不明な場合は、[Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) コマンドレットを使用してこの情報を取得できます。

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

既存のロールを更新するには、次の PowerShell コマンドを実行します。

```azurepowershell
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>カスタム ロールの削除

カスタム ロールを削除するには、[Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) コマンドを使用します。

次の例では、 *仮想マシン オペレーター* カスタム ロールが削除されます。

```azurepowershell
Get-AzureRmRoleDefinition "Virtual Machine Operator"
Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>次の手順

- [チュートリアル: Azure PowerShell を使用してカスタム ロールを作成する](tutorial-custom-role-powershell.md)
- [Azure のカスタム ロール](custom-roles.md)
- [Azure Resource Manager のリソース プロバイダー操作](resource-provider-operations.md)
