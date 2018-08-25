---
title: Visual Studio を使用して Web ジョブを開発してデプロイする - Azure
description: Visual Studio を使用して Azure WebJobs を Azure App Service にデプロイする方法について説明します。
services: app-service
documentationcenter: ''
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 09/12/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 64fdb6dceb1ca10e68411f95c310fdd9a2e25202
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444133"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Visual Studio を使用して Web ジョブを開発してデプロイする - Azure App Service

## <a name="overview"></a>概要

このトピックでは、Visual Studio を使用して、コンソール アプリケーション プロジェクトを [Azure Web ジョブ](http://go.microsoft.com/fwlink/?LinkId=390226)として [App Service](app-service-web-overview.md) の Web アプリにデプロイする方法について説明します。 [Azure ポータル](https://portal.azure.com)を使用して Web ジョブをデプロイする方法については、「[Web ジョブでバックグラウンド タスクを実行する](web-sites-create-web-jobs.md)」を参照してください。

Visual Studio では Web ジョブ対応のコンソール アプリケーション プロジェクトをデプロイする場合に、2 つのタスクを実行します。

* ランタイム ファイルを Web アプリの適切なフォルダーにコピーします (継続的な Web ジョブの場合は *App_Data/jobs/continuous*、スケジュールされたオンデマンドの Web ジョブの場合は *App_Data/jobs/triggered*)。
* 特定の時間に実行するようにスケジュールされる Web ジョブに [Azure Scheduler ジョブ](https://docs.microsoft.com/azure/scheduler/)を設定します  (これは継続的な Web ジョブでは必要ありません)。

Web ジョブ対応のプロジェクトでは、次の項目が追加されています。

* [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet パッケージ。
* デプロイメントとスケジューラの設定を含む [webjob-publish-settings.json](#publishsettings) ファイル。 

![Diagram showing what is added to a Console App to enable deployment as a WebJob](./media/websites-dotnet-deploy-webjobs/convert.png)

これらの項目を既存のコンソール アプリケーション プロジェクトに追加することもできれば、テンプレートを使用して新しい Web ジョブ対応のコンソール アプリケーション プロジェクトを作成することもできます。 

プロジェクト自体を Web ジョブとしてデプロイできます。また、Web プロジェクトをデプロイしたときに常に自動的にデプロイされるように、プロジェクトを Web プロジェクトにリンクすることもできます。 プロジェクトをリンクする場合、Visual Studio で、Web プロジェクト内の [webjobs-list.json](#webjobslist) ファイルに Web ジョブ対応のプロジェクトの名前が含められます。

![Diagram showing WebJob project linking to web project](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>前提条件

Visual Studio 2015 を使用している場合は、[Azure SDK for .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/) をインストールします。

Visual Studio 2017 を使用している場合は、[Azure 開発ワークロード](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads)をインストールします。

## <a id="convert"></a>既存のコンソール アプリケーション プロジェクトに対する Web ジョブのデプロイを有効にする

2 つのオプションがあります。

* [Web プロジェクトを使用した自動デプロイメントを有効にする](#convertlink)。

  Web プロジェクトをデプロイしたときに自動的に Web ジョブとしてデプロイされるように、既存のコンソール アプリケーション プロジェクトを構成します。 Web ジョブを、関連する Web アプリケーションが実行する Web アプリケーションと同じアプリケーションで実行する場合に、このオプションを使用します。

* [Web プロジェクトなしでデプロイメントを有効にする](#convertnolink)。

  Web プロジェクトへリンクせず、既存のコンソール アプリケーション プロジェクトがそれ自身で Web ジョブとしてデプロイされるように構成します。 Web アプリケーションで Web アプリケーションを実行せずに、Web ジョブ自身によって Web アプリケーション内で実行されようにする場合に、このオプションを使用します。 このオプションは、Web アプリケーション リソースとは関係なく Web ジョブ リソースを拡張する必要がある場合などに使用できます。

### <a id="convertlink"></a> Web プロジェクトを使用した Web ジョブの自動デプロイメントを有効にする

1. **ソリューション エクスプローラー**で Web プロジェクトを右クリックし、**[追加]** > **[既存のプロジェクトを Azure Web ジョブとして]** をクリックします。
   
    ![[既存のプロジェクトを Azure Web ジョブとして]](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    [[Azure Web ジョブの追加]](#configure) ダイアログ ボックスが表示されます。
2. **[プロジェクト名]** ボックスの一覧で、コンソール アプリケーション プロジェクトを選択し、Web ジョブとして追加します。
   
    ![Selecting project in Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. [[Azure Web ジョブの追加]](#configure) ダイアログで必要な設定を完了し、 **[OK]** をクリックします。 

### <a id="convertnolink"></a> Web プロジェクトなしで Web ジョブ デプロイメントを有効にする
1. **ソリューション エクスプローラー**でコンソール アプリケーション プロジェクトを右クリックし、**[Azure WebJob として発行する]** をクリックします。 
   
    ![[Azure Web ジョブとして発行]](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    [[Azure Web ジョブの追加]](#configure) ダイアログ ボックスが表示され、 **[プロジェクト名]** ボックスに選択されたプロジェクトが示されます。
2. [[Azure Web ジョブの追加]](#configure) ダイアログ ボックスで必要な設定を完了し、 **[OK]** をクリックします。
   
   **Web の発行** ウィザードが表示されます。  すぐに発行しない場合は、ウィザードを閉じます。 [プロジェクトをデプロイする](#deploy)ときのために、入力した設定値は保存されます。

## <a id="create"></a>新しい Web ジョブ対応のプロジェクトを作成する
新しい Web ジョブ対応のプロジェクトを作成するために、 [前のセクション](#convert)で説明したように、コンソール アプリケーション プロジェクト テンプレートを使用して、Web ジョブのデプロイメントを有効にすることができます。 または、次のように Web ジョブの新しいプロジェクト テンプレートを使用できます。

* [独立した Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する](#createnolink)
  
    プロジェクトを作成し、Web プロジェクトへはリンクせずに、それ自身で Web ジョブとしてデプロイされるように構成します。 Web アプリケーションで Web アプリケーションを実行せずに、Web ジョブ自身によって Web アプリケーション内で実行されようにする場合に、このオプションを使用します。 このオプションは、Web アプリケーション リソースとは関係なく Web ジョブ リソースを拡張する必要がある場合などに使用できます。
* [Web プロジェクトにリンクされた Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する](#createlink)
  
    同じソリューション内の Web プロジェクトがデプロイされたときに Web ジョブとして自動的にデプロイされるように構成して、プロジェクトを作成します。 Web ジョブを、関連する Web アプリケーションが実行する Web アプリケーションと同じアプリケーションで実行する場合に、このオプションを使用します。

> [!NOTE]
> WebJobs の new-project テンプレートは、NuGet パッケージを自動的にインストールし、 *WebJobs SDK* 用にコードを [Program.cs](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)に含めます。 WebJobs SDK を使用しない場合は、*Program.cs* 内の `host.RunAndBlock` ステートメントを削除または変更します。
> 
> 

### <a id="createnolink"></a> 独立した Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する
1. **[ファイル]** > **[新しいプロジェクト]** の順にクリックし、**[新しいプロジェクト]** ダイアログ ボックスで **[クラウド]** > **[Azure WebJob (.NET Framework)]** の順にクリックします。
   
    ![New Project dialog showing WebJob template](./media/websites-dotnet-deploy-webjobs/np.png)
2. 前に示された指示に従い、 [コンソール アプリケーション プロジェクトを独立した Web ジョブ プロジェクトにします](#convertnolink)。

### <a id="createlink"></a> Web プロジェクトにリンクされた Web ジョブ用に Web ジョブの新しいプロジェクト テンプレートを使用する
1. **ソリューション エクスプローラー**で Web プロジェクトを右クリックし、**[追加]** > **[新しい Azure Web ジョブ プロジェクト]** をクリックします。
   
    ![New Azure WebJob Project menu entry](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    [[Azure Web ジョブの追加]](#configure) ダイアログ ボックスが表示されます。
2. [[Azure Web ジョブの追加]](#configure) ダイアログ ボックスで必要な設定を完了し、 **[OK]** をクリックします。

## <a id="configure"></a>[Azure Web ジョブの追加] ダイアログ ボックス
**[Azure WebJob の追加]** ダイアログを使用して、Web ジョブの Web ジョブ名と実行モード設定を入力できます。 

![Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw2.png)

このダイアログのフィールドは、Azure ポータルの **[Web ジョブの追加]** ダイアログのフィールドに対応しています。 詳細については、「 [Web ジョブでのバックグラウンド タスクの実行](web-sites-create-web-jobs.md)」を参照してください。

> [!NOTE]
> * コマンド ライン デプロイメントについては、「 [Enabling Command-line or Continuous Delivery of Azure WebJobs (Azure Web ジョブのコマンド ラインによる配信または継続的配信を有効にする)](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)」を参照してください。
> * Web ジョブをデプロイした後、その Web ジョブの種類を変更して再デプロイすることに決めた場合は、*webjobs-publish-settings.json* ファイルを削除する必要があります。 これにより発行オプションが再度表示され、Web ジョブの種類を変更できます。
> * Web ジョブをデプロイし、後から実行モードを継続的から継続的以外、またはその逆に変更した場合、Visual Studio は再デプロイ時に Azure 内に新しい Web ジョブを作成します。 その他のスケジュール設定を変更し、実行モードを同じままにするか、または [スケジュール済み] と [オンデマンド] の間で切り替えた場合は、Visual Studio は新しいジョブを作成するのではなく、既存のジョブを更新します。
> 
> 

## <a id="publishsettings"></a>webjob-publish-settings.json
Web ジョブのデプロイメント用にコンソール アプリケーションを構成すると、Visual Studio によって [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet パッケージがインストールされ、Web ジョブ プロジェクトの *Properties* フォルダーにある *webjob-publish-settings.json* ファイルにスケジュール情報が格納されます。 次にこのファイルの例を示します。

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

このファイルは直接編集でき、Visual Studio で IntelliSense を使用できます。 ファイル スキーマは [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) に格納され、そこで表示できます。  

## <a id="webjobslist"></a>webjobs-list.json
Web ジョブ対応のプロジェクトを Web プロジェクトにリンクすると、Visual Studio は Web ジョブ プロジェクトの名前を Web プロジェクトの *Properties* フォルダー内の *webjobs-list.json* ファイルに格納します。 次の例に示すように、一覧には複数の WebJobs プロジェクトが含まれる場合があります。

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

このファイルは直接編集でき、Visual Studio で IntelliSense を使用できます。 ファイル スキーマは [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) に格納され、そこで表示できます。

## <a id="deploy"></a>Web ジョブ プロジェクトをデプロイする
Web プロジェクトにリンクされた Web ジョブ プロジェクトは、Web プロジェクトと共に自動的にデプロイされます。 Web プロジェクトのデプロイメントについては、左のナビゲーションにある **[How-to guides]\(ハウツー ガイド\)** の  > **[Deploy app]\(アプリをデプロイする\)** を参照してください。

WebJobs プロジェクトがそれ自身でデプロイされるように指定するには、**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[Azure WebJob として発行する]** をクリックします。 

![[Azure Web ジョブとして発行]](./media/websites-dotnet-deploy-webjobs/paw.png)

独立した Web ジョブの場合は、Web プロジェクトで使用されたウィザードと同じ **Web の発行** ウィザードが表示されますが、変更可能な設定値の数は少なくなります。
