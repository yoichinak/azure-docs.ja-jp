---
title: HDInsight 用の Azure Resource Manager ツールに移行する
description: HDInsight クラスター用の Azure Resource Manager 開発ツールに移行する方法
services: hdinsight
editor: jasonwhowell
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: 92114dcdf7e86a83c43bfbfe07fdeebeef18234b
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "42140353"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>HDInsight クラスター用の Azure Resource Manager ベースの開発ツールに移行する

HDInsight は、HDInsight 用の Azure Service Manager (ASM) ベースのツールを推奨していません。 HDInsight クラスターの操作に Azure PowerShell、Azure CLI、HDInsight .NET SDK を使用している場合、今後は、Azure Resource Manager バージョンの PowerShell、CLI、.NET SDK を使用することをお勧めします。 この記事では、新しい Resource Manager ベースのアプローチに移行する方法を紹介します。 また、HDInsight に対する ASM と Resource Manager のアプローチの違いについても適宜説明します。

> [!IMPORTANT]
> ASM ベースの PowerShell、CLI、.NET SDK のサポートは、 **2017 年 1 月 1 日**に中止される予定です。
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Azure CLI を Azure Resource Manager に移行する

> [!IMPORTANT]
> Azure CLI 2.0 では HDInsight クラスターを操作するためのコマンドは提供されていません。 Azure CLI 1.0 は非推奨となりますが、Azure CLI 1.0 を使用して HDInsight を操作することはできます。

Azure CLI 1.0 を使用して HDInsight を操作するための基本的なコマンドを次に示します。

* `azure hdinsight cluster create` - 新しい HDInsight クラスターを作成します
* `azure hdinsight cluster delete` - 既存の HDInsight クラスターを削除します
* `azure hdinsight cluster show` - 既存のクラスターに関する情報を表示します
* `azure hdinsight cluster list` - Azure サブスクリプションの HDInsight クラスターを一覧表示します

各コマンドの使用できるパラメーターとスイッチを調べるには、 `-h` スイッチを使用します。

### <a name="new-commands"></a>新しいコマンド
Azure Resource Manager では、次の新しいコマンドを使用できます。

* `azure hdinsight cluster resize` - クラスター内のワーカー ノード数を動的に変更します
* `azure hdinsight cluster enable-http-access` - クラスターに対する HTTP アクセスを有効にします (既定は有効)
* `azure hdinsight cluster disable-http-access` - クラスターに対する HTTP アクセスを無効にします
* `azure hdinsight script-action` - クラスターに対するスクリプト アクションを作成または管理するコマンドを表示します
* `azure hdinsight config` - `hdinsight cluster create` コマンドに使用して構成情報を提供することができる構成ファイルを作成するためのコマンドを表示します

### <a name="deprecated-commands"></a>非推奨のコマンド
`azure hdinsight job` コマンドを使用してジョブを HDInsight クラスターに送信する場合、これらのコマンドは Resource Manager コマンドからは使用できません。 プログラムでスクリプトから HDInsight にジョブを送信する必要がある場合は、代わりに HDInsight に用意されている REST API を使用してください。 REST API を使用したジョブの送信については、次のドキュメントを参照してください。

* [Curl を使用して HDInsight の Hadoop で MapReduce ジョブを実行](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Curl を使用した HDInsight の Hadoop での Hive クエリの実行](hadoop/apache-hadoop-use-hive-curl.md)
* [Curl を使用して HDInsight の Hadoop で Pig ジョブを実行](hadoop/apache-hadoop-use-pig-curl.md)

MapReduce、Hive、Pig を対話的に実行する他の方法については、「[HDInsight での MapReduce と Hadoop の使用](hadoop/hdinsight-use-mapreduce.md)」、「[HDInsight で Hadoop と共に Hive と HiveQL を使用して Apache log4j サンプル ファイルを分析する](hadoop/hdinsight-use-hive.md)」、「[HDInsight での Pig と Hadoop の使用](hadoop/hdinsight-use-pig.md)」を参照してください。

### <a name="examples"></a>例
**クラスターを作成する**

* 以前のコマンド (ASM) - `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* 新しいコマンド - `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**クラスターを削除する**

* 以前のコマンド (ASM) - `azure hdinsight cluster delete myhdicluster`
* 新しいコマンド - `azure hdinsight cluster delete mycluster -g myresourcegroup`

**クラスターの一覧表示**

* 以前のコマンド (ASM) - `azure hdinsight cluster list`
* 新しいコマンド - `azure hdinsight cluster list`

> [!NOTE]
> list コマンドの場合、`-g` を使用してリソース グループを指定すると、指定したリソース グループのクラスターのみが返されます。
> 
> 

**クラスター情報を表示する**

* 以前のコマンド (ASM) - `azure hdinsight cluster show myhdicluster`
* 新しいコマンド - `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Azure PowerShell から Azure Resource Manager に移行する
Azure Resource Manager モードの Azure PowerShell の全般情報については、「[Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

Azure PowerShell Resource Manager コマンドレットは、ASM コマンドと同時にインストールできます。 2 つのモードのコマンドレットは、名前で区別できます。  Resource Manager モードのコマンドレット名は *AzureRmHDInsight* であり、ASM モードのコマンドレット名は *AzureHDInsight* です。  たとえば、*New-AzureRmHDInsightCluster* と*New-AzureHDInsightCluster* などです。 Resource Manager では、一部のパラメーター名やスイッチ名が新しくなり、多数のパラメーターが追加されました。  たとえば、一部のコマンドレットでは、 *-ResourceGroupName*という新しいスイッチが必須になりました。 

HDInsight コマンドレットを使用する前に、Azure アカウントに接続し、新しいリソース グループを作成する必要があります。

* Connect-AzureRmAccount または [Select-AzureRmProfile](https://docs.microsoft.com/powershell/module/servicemanagement/azurerm.profile/select-azurermprofile?view=azuresmps-4.0.0)。 「[Azure リソース マネージャーでのサービス プリンシパルの認証](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>名前が変更されたコマンドレット
Windows PowerShell コンソールで HDInsight ASM コマンドレットを一覧表示するには:

    help *azurermhdinsight*

次の表は、ASM コマンドレットと、Resource Manager モードでの対応するコマンドレット名の一覧です。

| ASM コマンドレット | Resource Manager コマンドレット |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightconfigvalues?view=azurermps-6.6.0) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightmetastore?view=azurermps-6.6.0) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightscriptaction?view=azurermps-6.6.0) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightstorage?view=azurermps-6.6.0) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightcluster?view=azurermps-6.6.0) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightjob?view=azurermps-6.6.0) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightjoboutput?view=azurermps-6.6.0) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightproperties?view=azurermps-6.6.0) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess?view=azurermps-6.6.0) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsightrdpservicesaccess?view=azurermps-6.6.0) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/invoke-azurermhdinsighthivejob?view=azurermps-6.6.0) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightclusterconfig?view=azurermps-6.6.0) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsighthivejobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightmapreducejobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightpigjobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightsqoopjobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightstreamingmapreducejobdefinition?view=azurermps-6.6.0) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/remove-azurermhdinsightcluster?view=azurermps-6.6.0) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess?view=azurermps-6.6.0) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsightrdpservicesaccess?view=azurermps-6.6.0) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize?view=azurermps-6.6.0) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightdefaultstorage?view=azurermps-6.6.0) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/start-azurermhdinsightjob?view=azurermps-6.6.0) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/stop-azurermhdinsightjob?view=azurermps-6.6.0) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/use-azurermhdinsightcluster?view=azurermps-6.6.0) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/wait-azurermhdinsightjob?view=azurermps-6.6.0) |

### <a name="new-cmdlets"></a>新しいコマンドレット
Resource Manager モードでのみ使用できる新しいコマンドレットを次に示します。 

**スクリプト操作に関連するコマンドレット:**

* **Get-AzureRmHDInsightPersistedScriptAction**: クラスターの保存されたスクリプト操作を取得して古い順に一覧表示します。または、保存されたスクリプト操作のうち、指定した操作の詳細を取得します。 
* **Get-AzureRmHDInsightScriptActionHistory**: クラスターのスクリプト操作履歴を取得し、新しい順に一覧表示します。または、以前に実行されたスクリプト操作の詳細を取得します。 
* **Remove-AzureRmHDInsightPersistedScriptAction**: 保存されたスクリプト操作を HDInsight クラスターから削除します。
* **Set-AzureRmHDInsightPersistedScriptAction**: 以前に実行されたスクリプト操作を、保存されたスクリプト操作に設定します。
* **Submit-AzureRmHDInsightScriptAction**: 新しいスクリプト操作を Azure HDInsight クラスターに送信します。 

その他の使用方法について詳しくは、「[Script Action を使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

**クラスター ID に関連するコマンドレット:**

* **Add-AzureRmHDInsightClusterIdentity**: クラスター ID をクラスター構成オブジェクトに追加して、HDInsight クラスターが Azure Data Lake Store にアクセスできるようにします。 「[Azure PowerShell を使用して、Data Lake Store を使用する HDInsight クラスターを作成する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)」を参照してください。

### <a name="examples"></a>例
**クラスターの作成**

以前のコマンド (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

新しいコマンド:

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**クラスターの削除**

以前のコマンド (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

新しいコマンド:

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**クラスターの一覧表示**

以前のコマンド (ASM):

    Get-AzureHDInsightCluster

新しいコマンド:

    Get-AzureRmHDInsightCluster 

**クラスターの表示**

以前のコマンド (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

新しいコマンド:

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>その他のサンプル
* [HDInsight クラスターの作成](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Hive ジョブの送信](hadoop/apache-hadoop-use-hive-powershell.md)
* [Pig ジョブの送信](hadoop/apache-hadoop-use-pig-powershell.md)
* [Sqoop ジョブの送信](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>新しい HDInsight .NET SDK に移行する
Azure Service Management ベース [(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) は非推奨になりました。 Azure Resource Management ベースの [ HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) を使用することが推奨されます。 次の ASM ベースの HDInsight パッケージは非推奨となっています。

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

ここでは、Resource Manager ベースの SDK を使用して特定のタスクを実行する方法に関する詳細を紹介します。

| Resource Manager ベースの HDInsight SDK を使用して次の操作を実行 | リンク |
| --- | --- |
| .NET SDK を使用して HDInsight クラスターを作成する |「[.NET SDK を使用して HDInsight クラスターを作成する](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| .NET SDK でスクリプト操作を使用してクラスターをカスタマイズする |「[.NET SDK を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Azure Active Directory と .NET SDK を使用して対話型アプリケーションを認証する |「[.NET SDK を使用した Hive クエリの実行](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)」を参照してください。 この記事のコード スニペットでは、対話型の認証アプローチを使用しています。 |
| Azure Active Directory と .NET SDK を使用して非対話型アプリケーションを認証する |「[非対話型認証 .NET HDInsight アプリケーションを作成する](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| .NET SDK を使用して Hive ジョブを送信する |「[Hive ジョブの送信](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| .NET SDK を使用して Pig ジョブを送信する |「[Pig ジョブの送信](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| .NET SDK を使用して Sqoop ジョブを送信する |「[Sqoop ジョブの送信](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| .NET SDK を使用して HDInsight クラスターを一覧表示する |「[List HDInsight clusters (HDInsight クラスターを一覧表示する)](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| .NET SDK を使用して HDInsight クラスターをスケールする |「[Scale HDInsight clusters (HDInsight クラスターをスケールする)](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| .NET SDK を使用して HDInsight クラスターへのアクセス権を付与する、または取り消す |「[Grant/revoke access to HDInsight clusters (HDInsight クラスターへのアクセス権の付与/取り消し)](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| .NET SDK を使用して HDInsight クラスターの HTTP ユーザーの資格情報を更新する |「[Update HTTP user credentials for HDInsight clusters (HDInsight クラスターの HTTP ユーザーの資格情報を更新する)](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| .NET SDK を使用して HDInsight クラスターの既定のストレージ アカウントを検索する |「[Find the default storage account for HDInsight clusters (HDInsight クラスターの既定のストレージ アカウントを検索する)](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| .NET SDK を使用して HDInsight クラスターを削除する |「[Delete HDInsight clusters (HDInsight クラスターを削除する)](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>例
ASM ベースの SDK を使用して操作を実行する方法と、Resource Manager ベースの SDK の対応するコード スニペットの例を次に示します。

**クラスターの CRUD クライアントを作成する**

* 以前のコマンド (ASM):
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* 新しいコマンド (サービス プリンシパルの承認)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* 新しいコマンド (ユーザーの承認)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**クラスターを作成する**

* 以前のコマンド (ASM):
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* 新しいコマンド
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**HTTP アクセスを有効にする**

* 以前のコマンド (ASM):
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* 新しいコマンド
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**クラスターを削除する**

* 以前のコマンド (ASM):
  
        client.DeleteCluster(dnsName);
* 新しいコマンド
  
        client.Clusters.Delete(resourceGroup, dnsname);

