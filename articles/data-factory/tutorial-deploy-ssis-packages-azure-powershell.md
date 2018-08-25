---
title: PowerShell を使用した Azure-SSIS 統合ランタイムのプロビジョニング | Microsoft Docs
description: Azure 上で SSIS パッケージをデプロイして実行できるように、PowerShell を使用して Azure-SSIS 統合ランタイムを Azure Data Factory にプロビジョニングする方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: hero-article
ms.date: 06/27/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 8dc41eb3507368bb810c30a7680b73d0d1f26408
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085413"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>PowerShell を使用して Azure-SSIS 統合ランタイムを Azure Data Factory にプロビジョニングする
このチュートリアルでは、Azure-SSIS 統合ランタイム (IR) を Azure Data Factory にプロビジョニングする手順について説明します。 その後、SQL Server Data Tools (SSDT) または SQL Server Management Studio (SSMS) を使用して、Azure 上のこのランタイムに SQL Server Integration Services (SSIS) パッケージをデプロイして実行できます。 このチュートリアルでは、次の手順を実行します。

> [!NOTE]
> この記事では、Azure PowerShell を使用して Azure SSIS IR をプロビジョニングします。 Data Factory ユーザー インターフェイス (UI) を使用して Azure SSIS IR をプロビジョニングするには、[Azure SSIS 統合ランタイムを作成する方法のチュートリアル](tutorial-create-azure-ssis-runtime-portal.md)に関するページを参照してください。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure-SSIS 統合ランタイムを作成します
> * Azure SSIS 統合ランタイムを起動します
> * SSIS パッケージをデプロイします
> * 完全なスクリプトを確認します

## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。 Azure-SSIS IR の概念については、[Azure-SSIS 統合ランタイムの概要](concepts-integration-runtime.md#azure-ssis-integration-runtime)に関する記事をご覧ください。 
- **Azure SQL Database サーバー**。 まだデータベース サーバーをお持ちでない場合は、あらかじめ Azure Portal でデータベース サーバーを作成しておいてください。 このサーバーは、SSIS カタログ データベース (SSISDB) をホストします。 このデータベース サーバーは、統合ランタイムと同じ Azure リージョンに作成することをお勧めします。 この構成により、統合ランタイムは、Azure リージョンにまたがることなく SSISDB に実行ログを書き込むことができます。 
    - SSISDB は、選択したデータベース サーバーに基づいて、1 つのデータベースやエラスティック プールの一部として、または Managed Instance (プレビュー) 上に自動的に作成できます。SSISDB には、パブリック ネットワークで、または仮想ネットワークに参加することでアクセスできます。 SSISDB をホストするためにデータベース サーバーの種類を選択する際のガイダンスについては、「[Compare SQL Database and Managed Instance (Preview)](create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview)」(SQL Database およびマネージド インスタンスを比較する (プレビュー)) を参照してください。 仮想ネットワーク サービス エンドポイント/マネージド インスタンス (プレビュー) で Azure SQL Database を使用して、SSISDB をホストするか、オンプレミスのデータにアクセスする必要がある場合は、Azure-SSIS IR を仮想ネットワークに参加させる必要があります。[仮想ネットワークでの Azure-SSIS IR の作成](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime)に関するページを参照してください。 
    - データベース サーバーで **[Azure サービスへのアクセスを許可]** の設定が **[オン]** であることを確認します。 仮想ネットワーク サービス エンドポイント/マネージド インスタンス (プレビュー) で Azure SQL Database を使用して SSISDB をホストする場合、この設定は適用されません。 詳細については、「[Azure SQL データベースのセキュリティ保護](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)」を参照してください。 PowerShell を使用してこの設定を有効にするには、「[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1)」を参照してください。 
    - クライアント マシンの IP アドレス、またはクライアント マシンの IP アドレスを含む IP アドレスの範囲を、データベース サーバーのファイアウォール設定にあるクライアント IP アドレスの一覧に追加します。 詳細については、「[Azure SQL Database のサーバーレベルとデータベースレベルのファイアウォール規則](../sql-database/sql-database-firewall-configure.md)」を参照してください。 
    - SQL 認証とサーバー管理の資格情報、または Azure Active Directory (AAD) 認証と Azure Data Factory マネージド サービス ID (MSI) を使用して、データベース サーバーに接続できます。  後者の場合、データベース サーバーへのアクセス許可が割り当てられている AAD グループに Data Factory MSI を追加する必要があります。[AAD 認証を使用する場合の Azure-SSIS IR の作成](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime)に関するページを参照してください。 
    - Azure SQL Database サーバーに SSIS カタログ (SSISDB データベース) がないことを確認します。 Azure SSIS IR のプロビジョニングでは、既存の SSIS カタログの使用がサポートされていません。 
- **Azure PowerShell**。 [Azure PowerShell のインストールと構成の方法](/powershell/azure/install-azurerm-ps)に関するページに記載されている手順に従います。 SSIS パッケージをクラウドで実行する Azure-SSIS 統合ランタイムは、スクリプトを実行してプロビジョニングします。PowerShell はその実行に使用します。 

> [!NOTE]
> - 現在 Data Factory が利用できる Azure リージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」ページで目的のリージョンを選択し、**[分析]** を展開して **[Data Factory]** を探してください。 
> - 現在 Azure-SSIS 統合ランタイムが利用できる Azure リージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」ページで目的のリージョンを選択し、**[分析]** を展開して **[SSIS Integration Runtime]** を探してください。

## <a name="launch-windows-powershell-ise"></a>Windows PowerShell ISE の起動
管理特権を使用して **Windows PowerShell ISE** を起動します。 

## <a name="create-variables"></a>変数の作成
次のスクリプトをコピーして貼り付け、変数の値を指定します。 Azure SQL Database でサポートされている**価格レベル**の一覧については、[SQL Database のリソース制限](../sql-database/sql-database-resource-limits.md)に関するページを参照してください。

```powershell
# Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "[Azure subscription name]"
$ResourceGroupName = "[Azure resource group name]"
# Data factory name. Must be globally unique
$DataFactoryName = "[Data factory name]"
$DataFactoryLocation = "EastUS"

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[Specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[Specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS"
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B". For standard/premium/Elastic Pool tiers, specify "S0", "S1", "S2", "S3", etc.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>)]"
```

## <a name="validate-the-connection-to-database"></a>データベースへの接続の検証
Azure SQL Database サーバー (`<servername>.database.windows.net`) を検証するには、次のスクリプトを追加します。 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

スクリプトの一環として Azure SQL データベースを作成するには、次の例を参照してください。 

まだ定義されていない変数の値を設定します。 たとえば、SSISDBServerName や FirewallIPAddress です。 

```powershell
New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="log-in-and-select-subscription"></a>ログインとサブスクリプションの選択
ログインして Azure サブスクリプションを選択するには、スクリプトに次のコードを追加します。 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>リソース グループの作成
[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドで [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 次の例では、`myResourceGroup` という名前のリソース グループを `westeurope` の場所に作成します。

リソース グループが既に存在する場合は、このコードをスクリプトにコピーしないでください。 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Data Factory を作成する。
Data Factory を作成するには、次のコマンドを実行します。

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>統合ランタイムの作成
Azure 内で SSIS パッケージを実行する Azure-SSIS 統合ランタイムを作成するには、次のコマンドを実行します。 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
  
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogAdminCredential $serverCreds `
                                           -CatalogPricingTier $SSISDBPricingTier
```

## <a name="start-integration-runtime"></a>統合ランタイムの起動
Azure-SSIS 統合ランタイムを起動するには、次のコマンドを実行します。 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
このコマンドは、完了までに **20 ～ 30 分**かかります。 

## <a name="deploy-ssis-packages"></a>SSIS パッケージのデプロイ
次に、SQL Server Data Tools (SSDT) または SQL Server Management Studio (SSMS) を使って SSIS パッケージを Azure にデプロイします。 SSIS カタログ (SSISDB) をホストする Azure SQL サーバーに接続します。 Azure SQL Database サーバーの名前は `<servername>.database.windows.net` という形式になります。 

SSIS ドキュメントの次の記事をご覧ください。 

- [Azure での SSIS パッケージのデプロイ、実行、監視](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure での SSIS カタログへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure でのパッケージ実行のスケジュール設定](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Windows 認証を使用したオンプレミス データ ソースへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="full-script"></a>完全なスクリプト
このセクション内の PowerShell スクリプトでは、SSIS パッケージを実行するクラウド内の Azure-SSIS 統合ランタイムのインスタンスを構成します。 このスクリプトを正常に実行したら、Azure SQL Database でホストされた SSISDB と共に Microsoft Azure クラウドに SSIS パッケージをデプロイして実行できます。

1. Windows PowerShell Integrated Scripting Environment (ISE) を起動します。
2. ISE のコマンド プロンプトで次のコマンドを実行します。    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. このセクションの PowerShell スクリプトをコピーして ISE に貼り付けます。
4. スクリプトの先頭ですべてのパラメーターに適切な値を指定します。
5. スクリプトを実行します。 スクリプトの最後の方にある `Start-AzureRmDataFactoryV2IntegrationRuntime` コマンドは、実行に **20 ～ 30 分**かかります。

> [!NOTE]
> - このスクリプトでは、Azure SQL Database サーバーに接続して SSIS カタログ データベース (SSISDB) を準備します。

> - Azure-SSIS IR のインスタンスをプロビジョニングすると、Azure Feature Pack for SSIS と Access の再頒布可能パッケージもインストールされます。 これらのコンポーネントは、組み込みのコンポーネントでサポートされるデータ ソースに加えて、Excel ファイルと Access ファイルのほか、さまざまな Azure データ ソースに対する接続を実現するものです。 追加のコンポーネントをインストールすることもできます。 詳細については、「[Azure SSIS 統合ランタイムのカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)」をご覧ください。

Azure SQL Database でサポートされている**価格レベル**の一覧については、[SQL Database のリソース制限](../sql-database/sql-database-resource-limits.md)に関するページを参照してください。 

Azure Data Factory V2 および Azure-SSIS Integration Runtime でサポートされているリージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。 **[データ + 分析]** を展開して、**[Data Factory V2]** と **[SSIS Integration Runtime]** を表示します。

```powershell
# Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "[Azure subscription name]"
$ResourceGroupName = "[Azure resource group name]"
# Data factory name. Must be globally unique
$DataFactoryName = "[Data factory name]"
$DataFactoryLocation = "EastUS"

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[Specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[Specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS"
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B". For standard/premium/Elastic Pool tiers, specify "S0", "S1", "S2", "S3", etc.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>)]"

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
    
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
    
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogAdminCredential $serverCreds `
                                           -CatalogPricingTier $SSISDBPricingTier

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-virtual-network"></a>仮想ネットワークへの Azure-SSIS IR の参加
仮想ネットワーク サービス エンドポイント/仮想ネットワークに参加させたマネージド インスタンス (プレビュー) で Azure SQL Database を使用し、SSISDB をホストする場合は、Azure-SSIS 統合ランタイムを同じ仮想ネットワークに参加させる必要があります。 Azure Data Factory では、Azure-SSIS 統合ランタイムを仮想ネットワークに参加させることができます。 詳細については、「[Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](join-azure-ssis-integration-runtime-virtual-network.md)」を参照してください。

仮想ネットワークに参加させる Azure-SSIS 統合ランタイムを作成する完全なスクリプトについては、[Azure-SSIS 統合ランタイムの作成](create-azure-ssis-integration-runtime.md)に関するページを参照してください。

## <a name="monitor-and-manage-azure-ssis-ir"></a>Azure-SSIS IR の監視と管理
Azure-SSIS IR の監視と管理の詳細については、次の記事をご覧ください。 

- [Azure-SSIS 統合ランタイムの監視](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Azure-SSIS 統合ランタイムの管理](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>次の手順
このチュートリアルで学習した内容は次のとおりです。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure-SSIS 統合ランタイムを作成します
> * Azure SSIS 統合ランタイムを起動します
> * SSIS パッケージをデプロイします
> * 完全なスクリプトを確認します

次のチュートリアルに進んで、オンプレミスからクラウドにデータをコピーする方法について学習しましょう。 

> [!div class="nextstepaction"]
>[クラウドのデータをコピーする](tutorial-copy-data-dot-net.md)
