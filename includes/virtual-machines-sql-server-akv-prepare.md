---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 0a5d0f87b31652b1e1ab32c6b1594021937751b6
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "40046963"
---
## <a name="prepare-for-akv-integration"></a>AKV 統合の準備
Azure Key Vault 統合を使用し、SQL Server VM を構成するには、いくつかの前提条件があります。 

1. [Azure PowerShell をインストールする](#install)
2. [Azure Active Directory を作成する](#register)
3. [Key Vault を作成します](#createkeyvault)

次のセクションでは、これらの前提条件と、後に PowerShell コマンドレットを実行するために必要な情報について説明します。

### <a id="install"></a> Azure PowerShell をインストールする
最新の Azure PowerShell SDK がインストールされていることを確認します。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。

### <a id="register"></a> ご利用の Azure Active Directory にアプリケーションを登録する

最初に、サブスクリプションに [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) を追加する必要があります。 特定のユーザーやアプリケーションが Key Vault にアクセスするための許可が与えられるなど、さまざまな利点があります。

次に、アプリケーションを AAD に登録します。 これで VM に必要なキー コンテナーへのアクセス許可を持つサービス プリンシパル アカウントを入手できます。 Azure Key Vault の記事の「[Azure Active Directory にアプリケーションを登録する](../articles/key-vault/key-vault-get-started.md#register)」セクションにこれらの手順があります。あるいは、[このブログ投稿](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx)の「**Get an identity for the application (アプリケーションの ID を取得する)**」セクションのスクリーンショットで手順を確認できます。 これらの手順を完了する前に、後で SQL VM で Azure Key Vault 統合を有効にするときに必要になる次の情報をこの登録中に集める必要があります。

* アプリケーションが追加されたら、**[登録済みのアプリ]** ブレードで **[アプリケーション ID]** を探します。
    アプリケーション ID は後に PowerShell スクリプトの **$spName** (サービス プリンシパル名) パラメーターに割り当てられ、Azure Key Vault 統合を有効にします。

   ![アプリケーション ID](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* これらの手順で、鍵を作成するとき、次のスクリーンショットのように、鍵のシークレットをコピーします。 この鍵シークレットは後に PowerShell スクリプトの **$spSecret** (サービス プリンシパル シークレット) パラメーターに割り当てられます。

   ![AAD シークレット](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* アプリケーション ID とシークレットは、SQL Server で資格情報を作成する場合も使用されます。

* この新しいクライアント ID に権限を与え、アクセス許可 (**get**、**wrapKey**、**unwrapKey**) を与える必要があります。 これは [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) コマンドレットで行われます。 詳細については、「[キーまたはシークレットを使用してアプリケーションを承認する](../articles/key-vault/key-vault-get-started.md#authorize)」を参照してください。

### <a id="createkeyvault"></a> キー コンテナーを作成する
Azure Key Vault を使用して VM の暗号化に使用する鍵を保存するには、Key Vault へのアクセス許可が必要です。 キー コンテナーをまだ設定していない場合、「[Azure Key Vault の概要](../articles/key-vault/key-vault-get-started.md)」記事の手順で作成します。 これらの手順を完了する前に、後で SQL VM で Azure Key Vault 統合を有効にするときに必要になるいくつかの情報をこの設定中に集める必要があります。

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

"Key Vault を作成する" 手順に入ったら、返された **vaultUri** プロパティをメモします。これは Key Vault の URL です。 この手順の例では、下のように、Key Vault の名前は ContosoKeyVault であり、Key Vault URL は https://contosokeyvault.vault.azure.net/ になります。

Key Vault ID は後に PowerShell スクリプトの **$akvURL** パラメーターに割り当てられ、Azure Key Vault 統合を有効にします。

キー コンテナーが作成されたら、Microsoft はキー コンテナーにキーを追加する必要があります。このキーはその後、SQL Server で非対称キーを作成する場合に参照されます。
