| Resource | 既定の制限 | 上限 |
| --- | --- | --- |
| [リソース グループ](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (リソースの種類) あたりのリソース数 |800 |リソースの種類ごとに異なる |
| デプロイ履歴でのリソース グループあたりのデプロイ数 |800 |800 |
| デプロイあたりのリソース数 |800 |800 |
| 管理ロック数 (一意のスコープあたり) |20 |20 |
| タグ数 (リソースまたはリソース グループごと)  |15 |15 |
| タグ キーの長さ |512 |512 |
| タグ値の長さ |256 |256 |


#### <a name="template-limits"></a>テンプレートの制限

| 値 | 既定の制限 | 上限 |
| --- | --- | --- |
| parameters |256 |256 |
| variables |256 |256 |
| リソース (コピー数を含む) |800 |800 |
| 出力 |64 |64 |
| テンプレート式 |24,576 文字 |24,576 文字 |
| エクスポートされたテンプレート内のリソース |200 |200 | 
| テンプレート サイズ |1 MB |1 MB |
| パラメーター ファイル サイズ |64 KB |64 KB |

入れ子になったテンプレートを使用すると、一部のテンプレートの制限を超過することができます。 詳細については、「[Azure リソース デプロイ時のリンクされたテンプレートの使用](../articles/azure-resource-manager/resource-group-linked-templates.md)」を参照してください。 パラメーター、変数、出力の数を減らすために、いくつかの値を 1 つのオブジェクトに結合することができます。 詳しくは、[パラメーターとしてのオブジェクト](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)に関する記事をご覧ください。

リソース グループあたり 800 のデプロイという上限に達した場合、不要になった履歴からデプロイを削除します。 Azure CLI の場合は [az group deployment delete](/cli/azure/group/deployment#az_group_deployment_delete) を、PowerShell では [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) を使用して、履歴からエントリを削除できます。 デプロイ履歴からエントリを削除しても、デプロイ リソースには影響しません。 