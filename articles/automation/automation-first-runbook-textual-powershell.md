---
title: Azure Automation での初めての PowerShell Runbook
description: 簡単な PowerShell Runbook を作成、テスト、および発行する手順を説明するチュートリアルです。
keywords: Azure PowerShell, PowerShell スクリプトのチュートリアル, PowerShell Automation
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2f5d2f3634545001dc6dc1419530223b5a1a85a3
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435793"
---
# <a name="my-first-powershell-runbook"></a>初めての PowerShell Runbook

> [!div class="op_single_selector"]
> * [グラフィカル](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell ワークフロー](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

このチュートリアルでは、Azure Automation で [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) を作成する手順について説明します。 簡単な Runbook を作成、テスト、発行しながら、Runbook ジョブの状態を追跡する方法を説明します。 次に、実際に Azure リソースを管理するように Runbook を変更します。ここでは Azure 仮想マシンを開始します。 最後に Runbook のパラメーターを追加して、Runbook をさらに堅牢にします。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* [Automation アカウント](automation-offering-get-started.md)。Runbook の保存と Azure リソースの認証に使用します。 このアカウントには、仮想マシンを開始および停止するアクセス許可が必要です。
* Azure 仮想マシン。 マシンを停止して起動するので、運用 VM は使用しないでください。

## <a name="step-1---create-new-runbook"></a>手順 1 - 新しい Runbook を作成する
最初に、*Hello World* というテキストを出力する簡単な Runbook を作成します。

1. Azure ポータルで、Automation アカウントを開きます。

   Automation アカウント ページでは、そのアカウントのリソースを簡単に確認できます。 既に資産がいくつかあります。 これらのほとんどは、新しい Automation アカウントに自動的に含まれるモジュールです。 [前提条件](#prerequisites)で説明されている資格情報資産も必要です。

1. **[プロセス オートメーション]** の **[Runbook]** を選択し、Runbook の一覧を開きます。
2. **[+ Runbook の追加]** ボタンをクリックし、**[新しい Runbook の作成]** をクリックして新しい Runbook を作成します。
3. Runbook に *MyFirstRunbook-PowerShell*という名前を付けます。
4. ここでは、[PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) を作成するため、**[Runbook の種類]** として **[PowerShell]** を選択します。
5. **[作成]** をクリックして Runbook を作成し、テキスト エディターを開きます。

## <a name="step-2---add-code-to-the-runbook"></a>手順 2 - コードを Runbook に追加する
Runbook に直接コードを入力するか、ライブラリ コントロールからコマンドレット、Runbook、資産を選択し、関連するパラメーターを使って Runbook に追加できます。 このチュートリアルでは、Runbook に直接コードを入力します。

1. 作成した Runbook は現在空であるため、「*Write-Output "Hello World."*」と スクリプトの本文に入力します。<br><br> ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. **[保存]** をクリックして Runbook を保存します。

## <a name="step-3---test-the-runbook"></a>手順 3 - Runbook をテストする
Runbook を発行して運用環境で使用できるようにする前に、Runbook をテストして正常に動作することを確認します。 Runbook をテストするときは、 **ドラフト** バージョンを実行し、その出力を対話形式で表示します。

1. **[テスト ウィンドウ]** をクリックして、テスト ウィンドウを開きます。
2. **[開始]** をクリックしてテストを開始します。 有効なオプションはこれだけです。
3. [Runbook ジョブ](automation-runbook-execution.md) が作成され、その状態が表示されます。

   最初のジョブの状態は "*キューに設定*" であり、クラウドの runbook worker が使用できるようになるのを待っていることを示します。 ワーカーがジョブを要求すると "*開始中*" になり、Runbook が実際に実行を開始すると "*実行中*" になります。  

1. Runbook ジョブが完了すると、その出力が表示されます。 この場合は、*Hello World* です。<br><br> ![Test Pane Output](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
2. テスト ウィンドウを閉じてキャンバスに戻ります。

## <a name="step-4---publish-and-start-the-runbook"></a>手順 4 - Runbook を発行して開始する
前の手順で作成した Runbook は、まだドラフト モードです。 運用環境で実行できるようにするには、発行する必要があります。  Runbook を発行するときは、既存の発行済みバージョンをドラフト バージョンで上書きします。 この例では、Runbook を作成したばかりなので、発行済みバージョンはまだありません。

1. **[発行]** をクリックして Runbook を発行し、確認を要求されたら **[はい]** をクリックします。
2. ここで **[Runbook]** ウィンドウで左へスクロールして Runbook を表示すると、**[編集状態]** は **[発行済み]** になっています。
3. 右へスクロールして戻り、 **MyFirstRunbook-PowerShell**のウィンドウを表示します。  
   上部のオプションを使用すると、Runbook の開始、Runbook の表示、将来の開始スケジュールの設定を行ったり、HTTP 呼び出しで開始できるように [webhook](automation-webhooks.md) を作成したりすることができます。
4. Runbook を開始するために **[開始]** をクリックし、[Runbook の開始] ページが開いたら **[OK]** をクリックします。
5. 作成した Runbook ジョブのジョブ ページが開きます。 このウィンドウは閉じてもかまいませんが、ここではジョブの進行状況を確認できるように開いたままにします。
6. ジョブの状態が **[ジョブの概要]** に表示され、Runbook をテストしたときに確認した状態と一致しています。<br><br> ![ジョブの概要](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. Runbook の状態が *[完了]* になったら、**[概要]** の **[出力]** をクリックします。 [出力] ウィンドウが開き、*Hello World* と表示されます。<br><br> ![ジョブの出力](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. [出力] ページを閉じます。
9. **[すべてのログ]** をクリックして、Runbook ジョブのストリーム ウィンドウを開きます。 出力ストリームでは "*Hello World*" だけが表示されますが、Runbook が詳細やエラーに書き込んでいる場合は、これらの Runbook ジョブの他のストリームも表示できます。<br><br> ![[すべてのログ]](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. ストリーム ページとジョブ ページを閉じると、MyFirstRunbook-PowerShell ページに戻ります。
11. **[詳細]** の **[ジョブ]** をクリックして、この Runbook のジョブ ウィンドウを開きます。 この Runbook によって作成されたジョブの一覧が表示されます。 ジョブを 1 回実行しただけであるため、一覧に表示されるジョブは 1 つだけです。<br><br> ![Job List](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. このジョブをクリックすると、Runbook を開始したときに表示されたのと同じジョブ ウィンドウが開きます。 これにより前に戻って、特定の Runbook に対して作成されたジョブの詳細を見ることができます。

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>手順 5 - Azure リソースを管理するための認証を追加する
Runbook をテストして発行しましたが、これまでのところ役に立つことは何もしていません。 Azure リソースを管理させることにします。 ただし、[前提条件](#prerequisites)で示されている資格情報を使用して認証を行わないと、これを実現することはできません。 この操作は、**Connect-AzureRmAccount** コマンドレットを使用して行います。

1. MyFirstRunbook-PowerShell ページで **[編集]** をクリックして、テキスト エディターを開きます。
2. **Write-Output** の行は不要になったので削除します。
3. Automation の実行アカウントを使用して認証を処理する次のコードを入力またはコピーして貼り付けます。

   ```powershell
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > これで **Add-AzureRmAccount** および **Login-AzureRmAccount** が **Connect-AzureRMAccount** のエイリアスになりました。 **Connect-AzureRMAccount** コマンドレットが存在しない場合、**Add-AzureRmAccount** または **Login-AzureRmAccount** を使用したり、Automation アカウントで最新バージョンにモジュールを更新したりできます。

4. **テスト ウィンドウ**をクリックして、Runbook をテストできるようにします。
5. **[開始]** をクリックしてテストを開始します。 テストが完了すると、次のような情報が出力され、アカウントの基本情報が表示されます。 これにより、資格情報が有効であることを確認できます。<br><br> ![認証](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>手順 6 - 仮想マシンを開始するコードを追加する
これで、Runbook で Azure サブスクリプションに対する認証が行われ、リソースを管理できるようになります。 仮想マシンを起動するコマンドを追加します。 Azure サブスクリプション内の任意の仮想マシンを選択し、ここではその名前を Runbook にハードコーディングします。

1. *Connect-AzureRmAccount* の後に、起動する仮想マシンの名前とリソース グループ名を指定して「*Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'*」と入力します。  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Runbook を保存し、**テスト ウィンドウ**をクリックしてテストできるようにします。
3. **[開始]** をクリックしてテストを開始します。 完了したら、仮想マシンが開始されたことを確認します。

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>手順 7 - Runbook に入力パラメーターを追加する
この Runbook では、Runbook にハードコードした仮想マシンを開始しますが、Runbook を開始するときに仮想マシンを指定できるようにすればもっと便利です。 ここで、その機能を備えるために、Runbook に入力パラメーターを追加します。

1. *VMName* と *ResourceGroupName* のパラメーターを Runbook に追加し、次の例に示すように、**Start-AzureRmVM** コマンドレットでこれらの変数を使用します。

   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
1. Runbook を保存してテスト ウィンドウを開きます。 テストで使用される 2 つの入力変数の値を指定できるようになりました。
2. テスト ウィンドウを閉じます。
3. **[発行]** をクリックして、新しいバージョンの Runbook を発行します。
4. 前の手順で開始した仮想マシンを停止します。
5. **[OK]** をクリックして Runbook を開始します。 起動する仮想マシンの **VMName** と **ResourceGroupName** を入力します。<br><br> ![Pass Parameter](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
6. Runbook が完了したら、仮想マシンが開始されたことを確認します。

## <a name="differences-from-powershell-workflow"></a>PowerShell Workflow との違い
PowerShell Runbook のライフサイクル、機能、管理は、PowerShell Workflow Runbook と同じですが、相違点や制限事項がいくつかあります。

1. PowerShell Runbook は、コンパイル手順がないため、PowerShell Workflow Runbook と比較した場合、動作が速くなります。
2. PowerShell Workflow Runbook では、チェックポイントがサポートされています。チェックポイントの使用により、PowerShell Workflow Runbook は、Runbook の任意のポイントから再開できます。それに対して、PowerShell Runbook は最初から再開することしかできません。
3. PowerShell Workflow Runbook では、並列実行と順次実行がサポートされていますが、PowerShell Runbook では、コマンドを順次実行することしかできません。
4. PowerShell Workflow Runbook では、アクティビティ、コマンド、またはスクリプト ブロックは独自の実行空間を持つことができますが、PowerShell Runbook では、スクリプトのすべての内容が 1 つの実行空間で実行されます。 さらに、ネイティブの PowerShell Runbook と PowerShell Workflow Runbook には、 [構文の違い](https://technet.microsoft.com/magazine/dn151046.aspx) もあります。

## <a name="next-steps"></a>次の手順
* グラフィカルな Runbook の使用を開始するには、「 [初めてのグラフィカルな Runbook](automation-first-runbook-graphical.md)
* PowerShell Workflow Runbook を初めて利用するときは、「 [最初の PowerShell Workflow Runbook](automation-first-runbook-textual.md)
* Runbook の種類とそれらの利点や制限事項の詳細については、「 [Azure Automation の Runbook の種類](automation-runbook-types.md)
* PowerShell スクリプトのサポート機能の詳細については、「 [Native PowerShell Script Support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

