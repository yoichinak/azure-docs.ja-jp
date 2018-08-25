---
title: Excel と SOA 用の HPC Pack クラスター | Microsoft Docs
description: Azure の HPC Pack クラスターで大規模な Excel と SOA ワークロードを実行する
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: 4a6327fcfe6f6e6f3b8b5c6ecbd14b832b4134c5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421214"
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Azure の HPC Pack クラスターで Excel と SOA ワークロードを実行する
この記事では、Azure クイックスタート テンプレートまたは必要に応じて Azure PowerShell デプロイ スクリプトを使用して、Azure Virtual Machines に Microsoft HPC Pack 2012 R2 クラスターをデプロイする方法を示します。 クラスターは、HPC Pack で Microsoft Excel またはサービス指向アーキテクチャ (SOA) のワークロードを実行するように設計されている Azure Marketplace VM イメージを使用します。 クラスターを使用して、オンプレミスのクライアント コンピューターから Excel HPC サービスおよび SOA サービスを実行できます。 Excel の HPC サービスには、Excel ブックのオフロードと Excel ユーザー定義関数、または UDF が含まれます。

> [!IMPORTANT] 
> この記事は、HPC Pack 2012 R2 の機能、テンプレート、およびスクリプトに基づいています。 このシナリオは HPC Pack 2016 では現在サポートされていません。
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

これから作成する HPC Pack クラスターの概要は次の図のようになります。

![Excel ワークロードを実行するノードを含む HPC クラスター][scenario]

## <a name="prerequisites"></a>前提条件
* **クライアント コンピューター** - Excel および SOA のサンプル ジョブをクラスターに送信するには、Windows ベースのクライアント コンピューターが必要です。 また、Azure PowerShell のクラスター デプロイ スクリプト (このデプロイ方法を選択する場合) を実行するには、Windows コンピューターが必要です。
* **Azure サブスクリプション** - Azure サブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。
* **コア クォータ** - 場合によっては、コアのクォータを増やす必要があります。特に、マルチコア VM サイズのクラスター ノードをいくつかデプロイする場合に必要になる可能性があります。 Azure クイックスタート テンプレートを使用する場合、Resource Manager のコア クォータは Azure リージョンごとになります。 その場合は、特定のリージョンのクォータを増やす必要がある可能性があります。 [Azure サブスクリプションの制限、クォータ、制約](../../azure-subscription-service-limits.md)に関するページを参照してください。 クォータを増やすには、 [オンライン カスタマー サポートに申請](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (無料) してください。
* **Microsoft Office ライセンス** - Microsoft Excel を含む Marketplace HPC Pack 2012 R2 VM イメージを使用してコンピューティング ノードをデプロイすると、30 日間の評価バージョンの Microsoft Excel Professional Plus 2013 がインストールされます。 評価期間の後もワークロードを引き続き実行するには、有効な Microsoft Office ライセンスを使用して Excel をアクティブ化する必要があります。 この記事で後述する [Excel のアクティブ化](#excel-activation) を参照してください。 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>手順 1. Azure で HPC Pack クラスターをセットアップする
HPC Pack 2012 R2 クラスターをセットアップする 2 つの方法を説明します。1 番目は Azure クイックスタート テンプレートと Azure Portal を使用する方法で、2 番目は Azure PowerShell デプロイ スクリプトを使用する方法です。

### <a name="option-1-use-a-quickstart-template"></a>方法 1. クイックスタート テンプレートを使用する
Azure クイックスタート テンプレートを使用すると、Azure Portal で HPC Pack クラスターをすばやくデプロイできます。 ポータルでテンプレートを開くと表示される簡単な UI で、クラスターの設定を入力します。 手順は次のようになります。 

> [!TIP]
> 必要に応じて、Excel ワークロード専用と類似したクラスターを作成できる [Azure Marketplace テンプレート](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) を使用します。 この場合、以下の手順とは一部異なります。
> 
> 

1. [GitHub の HPC クラスター テンプレートの作成に関するページ](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)を参照します。 必要な場合は、テンプレートとソース コードに関する情報を確認します。
1. Azure ポータルで **[Azure にデプロイ]** をクリックして、テンプレートによるデプロイを開始します。
   
   ![テンプレートを Azure にデプロイする][github]
1. ポータルで以下の手順に従って、HPC クラスター テンプレートのパラメーターを入力します。
   
   a. **[パラメーター]** ページで、テンプレート パラメーターの値を入力または変更します  (各設定の隣のアイコンをクリックするとヘルプ情報が表示されます)。次の画面に示されているのはサンプルの値です。 この例では、1 つのヘッド ノードと 2 つの計算ノードで構成される *hpc01* という名前のクラスターが、*hpc.local* ドメインに作成されます。 コンピューティング ノードは、Microsoft Excel を含む HPC Pack VM イメージから作成されます。
   
   ![パラメーターを入力する][parameters-new-portal]
   
   > [!NOTE]
   > ヘッド ノードの VM は、Windows Server 2012 R2 上の HPC Pack 2012 R2 の [最新の Marketplace イメージ](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) から自動的に作成されます。 現時点では、イメージは HPC Pack 2012 R2 Update 3 に基づいています。
   > 
   > 計算ノードの VM は、選択した計算ノード ファミリの最新のイメージから作成されます。 Microsoft Excel Professional Plus 2013 の評価版を含む最新の HPC Pack 計算ノード イメージの **ComputeNodeWithExcel** オプションを選択します。 一般的な SOA セッション用または Excel UDF オフロード用にクラスターをデプロイするには、 **ComputeNode** オプションを選択します (Excel はインストールされません)。
   > 
   > 
   
   b. サブスクリプションを選択します。
   
   c. クラスターのリソース グループを作成します (*hpc01RG* など)。
   
   d. リソース グループの場所を選択します (米国中部など)。
   
   e. **[法律条項]** ページで、条項を確認します。 同意する場合は、**[購入]** をクリックします。 テンプレートの値の設定が完了したら、**[作成]** をクリックします。
1. デプロイが完了したら (通常約 30 分かかります)、クラスターのヘッド ノードからクラスターの証明書ファイルをエクスポートします。 後の手順でこのパブリック証明書をクライアント コンピューターにインポートし、セキュリティで保護された HTTP バインディングのサーバー側認証を提供します。
   
   a. Azure ポータルで、ダッシュボードに移動し、ヘッド ノードを選択して、ページの上部にある **[接続]** をクリックし、リモート デスクトップを使用して接続します。
   
    <!-- ![Connect to the head node][connect] -->
   
   b. 標準的な手順で証明書マネージャーを使用して、秘密キーを含まないヘッド ノード証明書 (Cert:\LocalMachine\My の下にあります) をエクスポートします。 この例では、*CN = hpc01.eastus.cloudapp.azure.com* をエクスポートします。
   
   ![証明書をエクスポートする][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>方法 2. HPC Pack IaaS デプロイ スクリプトを使用する
HPC Pack IaaS デプロイ スクリプトは、HPC Pack クラスターをデプロイするためのもう 1 つの汎用性の高い方法です。 クラシック デプロイ モデルでクラスターを作成しながら、テンプレートに Azure Resource Manager デプロイ モデルを使用します。 また、スクリプトは Azure Global サービスまたは Azure China サービスのサブスクリプションと互換性があります。

**追加の前提条件**

* **Azure PowerShell** - [Azure PowerShell をインストールして構成します](/powershell/azure/overview) (バージョン 0.8.10 以降)。
* **HPC Pack IaaS デプロイ スクリプト** - [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949)から最新版のスクリプトをダウンロードし、解凍します。 `New-HPCIaaSCluster.ps1 –Version`を実行して、スクリプトのバージョンを確認します。 この記事はバージョン 4.5.0 以降のスクリプトに基づきます。

**構成ファイルを作成する**

 HPC Pack IaaS デプロイ スクリプトは HPC クラスターのインフラストラクチャについて記載された XML 構成ファイルを入力として使用します。 Microsoft Excel を含むコンピューティング ノード イメージから作成された 1 個のヘッド ノードと 18 個のコンピューティング ノードで構成されるクラスターをデプロイするには、次のサンプル構成ファイルを実際の環境の値に置き換えます。 構成ファイルについて詳しくは、スクリプト フォルダーにある Manual.rtf ファイルおよび[HPC Pack IaaS デプロイ スクリプトを使用した HPC クラスターの作成](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関する記事をご覧ください。

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**構成ファイルに関する注意事項**

* ヘッド ノードの **VMName** は、**ServiceName** と**一致している必要があります**。一致していないと、SOA ジョブの実行に失敗します。
* ヘッド ノード証明書が生成されてエクスポートされるように、 **EnableWebPortal** を必ず指定してください。
* ヘッド ノードで実行する構成後 PowerShell スクリプト PostConfig.ps1 をファイルに指定します。 Azure ストレージ接続文字列の構成、ヘッド ノードからのコンピューティング ノード ロールの削除、デプロイ後の全ノードのオンライン化などのスクリプトのサンプルを次に示します。 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**スクリプトを実行する**

1. 管理者としてクライアント コンピューターで PowerShell を開きます。
1. ディレクトリをスクリプト フォルダー (この例では、E:\IaaSClusterScript) に変更します。
   
   ```
   cd E:\IaaSClusterScript
   ```
1. HPC Pack クラスターをデプロイするには、次のコマンドを実行します。 この例では、構成ファイルは E:\HPCDemoConfig.xml にあるものと想定しています。
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

HPC Pack デプロイ スクリプトの実行には少し時間がかかります。 スクリプトでの処理の 1 つとして、クラスター証明書がエクスポートされてダウンロードされ、クライアント コンピューター上の現在のユーザーの Documents フォルダーに保存されます。 スクリプトでは、次のようなメッセージが生成されます。 次の手順では、適切な証明書ストアに証明書をインポートします。    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>手順 2. Excel ブックをオフロードし、オンプレミスのクライアントから UDF を実行する
### <a name="excel-activation"></a>Excel のアクティブ化
運用ワークロードに ComputeNodeWithExcel VM イメージを使用する場合、有効な Microsoft Office ライセンス キーを使用して計算ノードの Excel をアクティブ化する必要があります。 アクティブ化しないと、評価版の Excel は 30 日後に期限が切れ、実行中の Excel ブックは COMException (0x800AC472) で失敗するようになります。 

Excel の評価期間を 30 日間延長できます。ヘッド ノードにログオンし、HPC Cluster Manager を使用してすべての Excel コンピューティング ノードで clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` を実行します。 この処理は最大 2 回実行できます。 その後は、有効な Office ライセンス キーを入力する必要があります。

VM イメージにインストールされる Office Professional Plus 2013 は、汎用ボリューム ライセンス キー (GVLK) を使用したボリューム エディションです。 アクティブ化には、キー管理サービス (KMS)/Active Directory ベースのアクティブ化 (AD-BA) またはマルチ ライセンス認証キー (MAK) を使用できます。 

    * KMS/AD-BA を使用するには、Microsoft Office 2013 ボリューム ライセンス パックを使用して、既存の KMS サーバーを使用するか、新しいサーバーを設定します  (サーバーをヘッド ノーﾄﾞに設定することもできます)。次に、インターネットまたは電話で KMS ホスト キーをアクティブ化します。 次に、clusrun `ospp.vbs` を実行して KMS サーバーとポートを設定し、すべての Excel の計算ノードで Office をアクティブ化します。 

    * MAK を使用するには、まず clusrun `ospp.vbs` を実行してキーを入力し、インターネットまたは電話ですべての Excel の計算ノードをアクティブ化します。 

> [!NOTE]
> この VM イメージには、Office Professional Plus 2013 の市販のプロダクト キーを使用できません。 この Office Professional Plus 2013 ボリューム エディション以外の Office または Excel エディションの有効なキーとインストール メディアがある場合、代わりにそれらを使用できます。 このボリューム エディションをアンインストールしてから、所有しているエディションをインストールします。 再インストールした Excel コンピューティング ノードは、カスタマイズされた VM イメージとしてキャプチャされ、大規模なデプロイで使用されます。
> 
> 

### <a name="offload-excel-workbooks"></a>Excel ブックをオフロードする
以下の手順に従って、Azure の HPC Pack クラスターで実行するように Excel ブックをオフロードします。 そのためには、Excel 2010 または 2013 がクライアント コンピューターに既にインストールされている必要があります。

1. 手順 1 のいずれかの方法を使用して、Excel コンピューティング ノード イメージを含む HPC Pack クラスターをデプロイします。 クラスター証明書ファイル (.cer) およびクラスターのユーザー名とパスワードを取得します。
1. クライアント コンピューターで、クラスター証明書を Cert:\CurrentUser\Root にインポートします。
1. Excel がインストールされていることを確認します。 次のような内容の Excel.exe.config ファイルを作成し、クライアント コンピューター上の Excel.exe と同じフォルダーに保存します。 この手順により、HPC Pack 2012 R2 の Excel COM アドインが正常に読み込まれます。
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
1. クライアントをセットアップし、HPC Pack クラスターにジョブを送信します。 1 つの方法としては、 [HPC Pack 2012 R2 Update 3 インストール](http://www.microsoft.com/download/details.aspx?id=49922) の完全版をダウンロードして HPC Pack クライアントをインストールします。 別の方法としては、[HPC Pack 2012 R2 Update 3 クライアント ユーティリティ](https://www.microsoft.com/download/details.aspx?id=49923)とコンピューターに適した Visual C++ 2010 再頒布可能パッケージをダウンロードしてインストールします ([x64](http://www.microsoft.com/download/details.aspx?id=14632)、[x86](https://www.microsoft.com/download/details.aspx?id=5555))。
1. この例では、ConvertiblePricing_Complete.xlsb という名前のサンプル Excel ブックを使用します。 [こちら](https://www.microsoft.com/en-us/download/details.aspx?id=2939)からダウンロードできます。
1. Excel ブックを D:\Excel\Run などの作業フォルダーにコピーします。
1. Excel ブックを開きます。 **[開発]** リボンで **[COM アドイン]** をクリックし、HPC Pack Excel COM アドインが正常に読み込まれていることを確認します。
   
   ![HPC Pack 用の Excel アドイン][addin]
1. VBA マクロ HPCControlMacros を Excel で編集し、次のスクリプトで示されているようにコメント行を変更します。 実際の環境に合わせて適切な値に置き換えます。
   
   ![HPC Pack 用の Excel マクロ][macro]
   
   ```
   'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
   Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"
   
   'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
   Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.Initialize ActiveWorkbook
   HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles
   
   'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
   HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
   HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
   ```
1. D:\Excel\Upload などのアップロード ディレクトリに、Excel ブックをコピーします。 このディレクトリは、VBA マクロの HPC_DependsFiles 定数で指定されます。
1. Azure のクラスターでブックを実行するには、ワークシートの **[クラスター]** ボタンをクリックします。

### <a name="run-excel-udfs"></a>Excel UDF を実行する
Excel の UDF を実行するには、前記の手順 1 ～ 3 に従ってクライアント コンピューターを設定します。 Excel UDF の場合、Excel アプリケーションがコンピューティング ノードにインストールされている必要はありません。 そのため、クラスターのコンピューティング ノードを作成する場合は、Excel を含むコンピューティング ノード イメージではなく、通常のコンピューティング ノード イメージを選択できます。

> [!NOTE]
> Excel 2010 および 2013 クラスター コネクタのダイアログ ボックスには 34 文字の制限があります。 このダイアログ ボックスを使用して、UDF を実行するクラスターを指定します。 完全なクラスター名が長い場合は (例: hpcexcelhn01.southeastasia.cloudapp.azure.com)、ダイアログ ボックスに収まりません。 回避策は、長いクラスター名の値に *CCP_IAASHN* などのマシン全体の変数を設定します。 次に、*%CCP_IAASHN%* をダイアログ ボックスにクラスター ヘッドのノード名として入力します。 
> 
> 

クラスターが正常にデプロイされた後、引き続き以下の手順に従って、サンプルの組み込み Excel UDF を実行します。 Excel UDF をカスタマイズした場合は、 [リソース](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) を参考にして、XLL を作成し、IaaS クラスターにそれをデプロイしてください。

1. 新しい Excel ブックを開きます。 **[開発]** リボンで **[アドイン]** をクリックします。次に、ダイアログ ボックスで **[参照]** をクリックし、%CCP_HOME%Bin\XLL32 フォルダーに移動して、サンプルの ClusterUDF32.xll を選択します。 ClusterUDF32 がクライアント コンピューターに存在しない場合は、ヘッド ノードの %CCP_HOME%Bin\XLL32 フォルダーからコピーします。
   
   ![UDF を選択する][udf]
1. **[ファイル]** > 、**[オプション]**、 > **[詳細]** の順にクリックします。 **[数式]** の **[計算クラスターでユーザー定義の XLL 関数を実行できるようにする]** をオンにします。 **[オプション]** をクリックし、**[クラスター ヘッド ノード名]** に完全なクラスター名を入力します  ここでは、長いクラスター名にマシン全体の変数を使用できます。 ここでは、長いクラスター名にマシン全体の変数を使用できます)。
   
   ![UDF を構成する][options]
1. クラスターで UDF の計算を実行するには、値が =XllGetComputerNameC() であるセルをクリックして Enter キーを押します。 この関数は、UDF が実行しているコンピューティング ノードの名前を取得するだけです。 初めて実行したときは、IaaS クラスターに接続するためのユーザー名とパスワードの入力を求める資格情報ダイアログ ボックスが表示されます。
   
   ![UDF を実行する][run]
   
   計算するセルが多い場合は、Alt + Shift + Ctrl + F9 キーを押してすべてのセルに対して計算を実行します。

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>手順 3. オンプレミスのクライアントから SOA ワークロードを実行する
一般的な SOA アプリケーションを HPC Pack IaaS クラスターで実行するには、まず、手順 1 のいずれかの方法に従い、クラスターをデプロイします。 この場合、コンピューティング ノードでは Excel が必要ないため、汎用コンピューティング ノード イメージを指定します。 次に、以下の手順に従います。

1. クラスター証明書を取得した後、クライアント コンピューターで Cert:\CurrentUser\Root にインポートします。
1. [HPC Pack 2012 R2 Update 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) および [HPC Pack 2012 R2 Update 3 クライアント ユーティリティ](https://www.microsoft.com/download/details.aspx?id=49923)をインストールします。 これらのツールを使用すると、SOA クライアント アプリケーションをデプロイして実行できます。
1. HelloWorldR2 [サンプル コード](https://www.microsoft.com/download/details.aspx?id=41633)をダウンロードします。 Visual Studio 2010 または 2012 で HelloWorldR2.sln を開きます。 (このサンプルは、現在より新しいバージョンの Visual Studio には対応していません)。
1. 最初に EchoService プロジェクトをビルドします。 次に、オンプレミスのクラスターにデプロイするのと同じ方法で、IaaS クラスターにサービスをデプロイします。 詳細な手順については、HelloWordR2 の Readme.doc を参照してください。 次のセクションで説明するように HellWorldR2 および他のプロジェクトを変更してビルドし、Azure IaaS クラスター上で実行する SOA クライアント アプリケーションを生成します。

### <a name="use-http-binding-with-azure-storage-queue"></a>Azure ストレージ キューありで Http バインディングを使用する
Azure ストレージ キューで Http バインディングを使用するには、サンプル コードにいくつかの変更を加えます。

* クラスター名を更新します。
  
    ```
  // Before
  const string headnode = "[headnode]";
  // After e.g.
  const string headnode = "hpc01.eastus.cloudapp.azure.com";
  or
  const string headnode = "hpc01.cloudapp.net";
  ```
* 必要に応じて、SessionStartInfo の既定の TransportScheme を使用するか、または Http に明示的に設定します。

```
    info.TransportScheme = TransportScheme.Http;
```

* BrokerClient に既定のバインディングを使用します。
  
    ```
  // Before
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
  // After
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
  ```
  
    または、basicHttpBinding の使用を明示的に設定します。
  
    ```
  BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
  binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
  ```
* 必要に応じて、SessionStartInfo で UseAzureQueue フラグを true に設定します。 これを設定しないと、クラスター名に Azure ドメイン サフィックスが含まれ、TransportScheme が Http の場合は、既定で true に設定されます。
  
    ```
    info.UseAzureQueue = true;
  ```

### <a name="use-http-binding-without-azure-storage-queue"></a>Azure ストレージ キューなしで Http バインディングを使用する
Azure ストレージ キューなしで Http バインディングを使用するには、SessionStartInfo で UseAzureQueue フラグを明示的に false に設定します。

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>NetTcp バインディングを使用する
NetTcp バインディングを使用するための構成は、オンプレミスのクラスターに接続する場合と似ています。 ヘッド ノード VM でいくつかのエンドポイントを開く必要があります。 クラスターを作成する HPC Pack IaaS デプロイ スクリプトを使用した場合、Azure ポータルで次の手順を実行して、エンドポイントを設定します。

1. VM を停止します。
1. セッション用、ブローカー用、ブローカー ワーカー用、Data Services 用に、それぞれ TCP ポート 9090、9087、9091、9094 を追加します。
   
    ![エンドポイントを構成する][endpoint-new-portal]
1. VM を起動します。

SOA クライアント アプリケーションでは、IaaS クラスターの完全な名前にヘッド名を変更する以外の変更は不要です。

## <a name="next-steps"></a>次の手順
* HPC Pack での Excel ワークロードの実行に関する詳細については、 [これらのリソース](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) を参照してください。
* HPC Pack での SOA サービスのデプロイと管理について詳しくは、「 [サービス](https://technet.microsoft.com/library/ff919412.aspx) 」を参照してください。

<!--Image references-->
[scenario]: ./media/excel-cluster-hpcpack/scenario.png
[github]: ./media/excel-cluster-hpcpack/github.png
[template]: ./media/excel-cluster-hpcpack/template.png
[parameters]: ./media/excel-cluster-hpcpack/parameters.png
[parameters-new-portal]: ./media/excel-cluster-hpcpack/parameters-new-portal.png
[create]: ./media/excel-cluster-hpcpack/create.png
[connect]: ./media/excel-cluster-hpcpack/connect.png
[cert]: ./media/excel-cluster-hpcpack/cert.png
[addin]: ./media/excel-cluster-hpcpack/addin.png
[macro]: ./media/excel-cluster-hpcpack/macro.png
[options]: ./media/excel-cluster-hpcpack/options.png
[run]: ./media/excel-cluster-hpcpack/run.png
[endpoint]: ./media/excel-cluster-hpcpack/endpoint.png
[endpoint-new-portal]: ./media/excel-cluster-hpcpack/endpoint-new-portal.png
[udf]: ./media/excel-cluster-hpcpack/udf.png
