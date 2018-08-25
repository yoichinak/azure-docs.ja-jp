---
title: Azure Portal でストレージ アカウントを作成、管理、削除する方法 | Microsoft Docs
description: Azure Portal で、新しいストレージ アカウントの作成、アカウント アクセス キーの管理、ストレージ アカウントの削除を実行します。 Standard および Premium ストレージ アカウントについて説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: get-started-article
f1_keywords:
- sql13.swb.windowsazurestorage.connect.f1
ms.date: 10/11/2017
ms.author: tamram
ms.component: common
ms.openlocfilehash: 980053adc89cc121f3218233d521c8db4f075eb7
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523650"
---
# <a name="about-azure-storage-accounts"></a>Azure ストレージ アカウントについて

[!INCLUDE [storage-selector-portal-create-storage-account](../../../includes/storage-selector-portal-create-storage-account.md)]

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>概要
Azure ストレージ アカウントは、Azure Storage データ オブジェクトの格納およびアクセスのための一意の名前空間を提供します。 ストレージ アカウント内のすべてのオブジェクトは、グループとしてまとめて課金されます。 既定では、アカウントのデータはアカウント所有者だけが使用できます。

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>ストレージ アカウントの課金

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

> [!NOTE]
> Azure の仮想マシンを作成する場合、デプロイ場所にまだストレージ アカウントがない状況では、その場所に自動的にストレージ アカウントが作成されます。 このため、使用する仮想マシンのディスク用にストレージ アカウントを作成する場合、必ずしも以下の手順に従う必要はありません。 ストレージ アカウント名は仮想マシン名を基にして付けられます。 詳細については、 [Azure Virtual Machines のドキュメント](https://azure.microsoft.com/documentation/services/virtual-machines/) を参照してください。
> 
> 

## <a name="storage-account-endpoints"></a>ストレージ アカウント エンドポイント
Azure Storage に格納するすべてのオブジェクトには一意の URL アドレスが設定されています。 ストレージ アカウント名は、そのアドレスのサブドメインになります。 サブドメインとドメイン名の組み合わせは、各サービスに固有であり、ストレージ アカウントの *エンドポイント* になります。

たとえば、ストレージ アカウントの名前が *mystorageaccount* の場合、ストレージ アカウントの既定のエンドポイントは次のようになります。

* Blob service: http://*mystorageaccount*.blob.core.windows.net
* Table service: http://*mystorageaccount*.table.core.windows.net
* Queue サービス: http://*mystorageaccount*.queue.core.windows.net
* File サービス: http://*mystorageaccount*.file.core.windows.net

> [!NOTE]
> BLOB ストレージ アカウントは、Blob service エンドポイントのみを公開します。
> 
> 

ストレージ アカウント内のオブジェクトにアクセスするための URL は、ストレージ アカウント内のオブジェクトの場所をエンドポイントに追加して作成します。 たとえば、BLOB アドレスは、 http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob* のような形式になります。

また、カスタム ドメイン名もストレージ アカウントを使用するように構成することができます。 詳細については、「[Configure a custom domain Name for your Blob Storage Endpoint (BLOB ストレージ エンドポイントのカスタム ドメイン名の構成)](../blobs/storage-custom-domain-name.md)」を参照してください。 また、PowerShell で構成することもできます。 詳細については、「 [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) 」を参照してください。  


## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. Azure Portal で左側のメニューを展開してサービスのメニューを開き、**[その他のサービス]** を選択します。 次に、下へスクロールして **[ストレージ]** の **[ストレージ アカウント]** を選択します。 表示された **[ストレージ アカウント]** ウィンドウで **[追加]** を選択します。
3. ストレージ アカウントの名前を入力します。 Azure Storage 内のオブジェクトを指すためにストレージ アカウント名がどのように使用されるかについては、「 [ストレージ アカウント エンドポイント](#storage-account-endpoints) 」を参照してください。
   
   > [!NOTE]
   > ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。
   > 
   > ストレージ アカウント名は Azure 内で一意である必要があります。 選択したストレージ アカウント名が既に使用されているかどうかが、Azure ポータルによって示されます。
   > 
   > 
4. 使うデプロイメント モデル (**[Resource Manager]** または **[クラシック]**) を指定します。 **[リソース マネージャー]** が、推奨されるデプロイ モデルです。 詳細については、「 [リソース マネージャー デプロイと従来のデプロイを理解する](../../azure-resource-manager/resource-manager-deployment-model.md)」を参照してください。
   
   > [!NOTE]
   > BLOB ストレージ アカウントを作成するには、Resource Manager デプロイ モデルを使用する必要があります。

5. ストレージ アカウントの種類として、**[汎用]** または **[BLOB ストレージ]** を選択します。 **[汎用]** です。
   
    **[汎用]** を選択した場合は、パフォーマンス レベルとして **[Standard]** または **[Premium]** を指定します。 既定値は **[Standard]** です。 Standard Storage アカウントと Premium Storage アカウントの詳細については、「[Microsoft Azure Storage の概要](storage-introduction.md)」と「[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../../virtual-machines/windows/premium-storage.md)」を参照してください。
   
    **[BLOB ストレージ]** を選択した場合は、アクセス層として **[ホット]** または **[クール]** を指定します。 既定値は **[ホット]** です。 詳細については、「 [Azure Blob Storage: クール層とホット層](../blobs/storage-blob-storage-tiers.md) 」を参照してください。
6. ストレージ アカウントのレプリケーション オプション (**[LRS]**、**[GRS]**、**[RA-GRS]**、または **[ZRS]**) を選択します。 既定値は **[RA-GRS]** です。 Azure Storage のレプリケーション オプションの詳細については、 [Azure Storage のレプリケーション](storage-redundancy.md)に関するページをご覧ください。
7. 新しいストレージ アカウントを作成するサブスクリプションを選択します。
8. 新しいリソース グループを指定するか、既定のリソース グループを選択します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/resource-group-overview.md)」をご覧ください。
9. ストレージ アカウントの地理的な場所を選択します。 どのリージョンでどのサービスを使用できるかについては、「 [Azure のリージョン](https://azure.microsoft.com/regions/#services) 」を参照してください。
10. **[作成]** をクリックしてストレージ アカウントを作成します。

## <a name="manage-your-storage-account"></a>ストレージ アカウントの管理
### <a name="change-your-account-configuration"></a>アカウント構成の変更
ストレージ アカウントの作成後、アカウントで使用するレプリケーション オプションの変更や、BLOB ストレージ アカウントのアクセス層の変更など、そのアカウント構成を変更できます。 [Azure ポータル](https://portal.azure.com)でお使いのストレージ アカウントに移動し、**[設定]** で **[構成]** を見つけてクリックすると、アカウント構成を表示したり変更したりできます。

> [!NOTE]
> ストレージ アカウントの作成時に選択したパフォーマンス レベルによっては、一部のレプリケーション オプションが使用できない場合があります。
> 
> 

レプリケーション オプションを変更すると価格が変更されます。 詳細については、「 [Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/) 」を参照してください。

BLOB ストレージ アカウントでは、アクセス層を変更すると、料金の変更の他に、変更に対する課金が発生することがあります。 詳細については、 [BLOB ストレージ アカウントの価格と課金](storage-account-options.md#pricing-and-billing) に関するセクションを参照してください。

### <a name="manage-your-storage-access-keys"></a>ストレージ アクセス キーの管理
ストレージ アカウントを作成するときに、Azure によって 2 つの 512 ビット ストレージ アクセス キーが生成されます。これらは、ストレージ アカウントにアクセスするときに認証の目的で使用されます。 Azure によって 2 つのストレージ アクセス キーが提供される結果、ストレージ サービスやサービスへのアクセスを中断することなく、これらのキーを再生成できます。

> [!NOTE]
> ストレージ アクセス キーは、他の人と共有しないことをお勧めします。 アクセス キーを入力しないでストレージ リソースにアクセスする場合は、 *Shared Access Signature*が利用できます。 Shared Access Signature を使用すると、指定した期間、指定した権限で、アカウント内のリソースにアクセスできるようになります。 詳細については、「 [Using Shared Access Signatures (SAS) (Shared Access Signature (SAS) の使用)](storage-dotnet-shared-access-signature-part-1.md) 」を参照してください。
> 
> 
<a id="view-and-copy-storage-access-keys"/></a>
#### <a name="view-and-copy-storage-access-keys"></a>ストレージ アクセス キーの表示とコピー
[Azure Portal](https://portal.azure.com) で、お使いのストレージ アカウントに移動し、**[すべての設定]**、**[アクセス キー]** の順にクリックすると、アカウントのアクセス キーを表示、コピー、再生成できます。 また、 **[アクセス キー]** ブレードには、コピーしてアプリケーションで使用できるプライマリ キーとセカンダリ キーを使用してあらかじめ構成された接続文字列も含まれています。

#### <a name="regenerate-storage-access-keys"></a>ストレージ アクセス キーの再生成
ストレージ接続のセキュリティを維持するために、ストレージ アカウントのアクセス キーを定期的に変更することをお勧めします。 アクセス キーは 2 つ割り当てられるため、一方のアクセス キーでストレージ アカウントに接続したまま、もう一方のアクセス キーを再生成できます。

> [!WARNING]
> アクセス キーを再生成すると、そのストレージ アカウントに依存する Azure のサービスと独自のアプリケーションに影響する場合があります。 アクセス キーを使用してストレージ アカウントにアクセスするすべてのクライアントを更新し、新しいキーが使用されるようにする必要があります。
> 
> 

**メディア サービス** - ストレージ アカウントに依存するメディア サービスがある場合は、アクセス キーを再生成した後で、そのキーをメディア サービスと再同期する必要があります。

**アプリケーション** - ストレージ アカウントを使用する Web アプリケーションまたはクラウド サービスがある場合は、キーを切り替えない限り、キーを再生成すると接続が失われます。

**ストレージ エクスプローラー** - いずれかの [ストレージ エクスプローラー アプリケーション](storage-explorers.md)を使用している場合は、通常、それらのアプリケーションで使用されるストレージ キーを更新する必要があります。

次に、ストレージ アクセス キーの交換プロセスを示します。

1. ストレージ アカウントのセカンダリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。
2. ストレージ アカウントのプライマリ アクセス キーを再生成します。 **[アクセス キー]** ブレードで、**[Key1 の再生成]** をクリックし、確認画面で **[はい]** をクリックして新しいキーを生成します。
3. 新しいプライマリ アクセス キーを参照するようにアプリケーション コードの接続文字列を更新します。
4. 同様に、セカンダリ アクセス キーを再生成します。

## <a name="delete-a-storage-account"></a>ストレージ アカウントを削除する
使わなくなったストレージ アカウントを削除するには、 [Azure ポータル](https://portal.azure.com)でストレージ アカウントに移動し、 **[削除]** をクリックします。 ストレージ アカウントを削除すると、アカウント内のすべてのデータを含む、アカウント全体が削除されます。

> [!WARNING]
> 削除したストレージ アカウントを復元することも、削除前にアカウントに含まれていたコンテンツを取得することもできません。 アカウントを削除する前に、保存する必要のあるデータを必ずバックアップしてください。 これはアカウントのどのリソースにも当てはまります。BLOB、テーブル、キュー、またはファイルを削除すると、完全に削除されます。
> 

Azure 仮想マシンに関連付けられているストレージ アカウントを削除しようとすると、まだ使用しているストレージ アカウントに関するエラー メッセージが表示されることがあります。 このエラーのトラブルシューティングについては、「[Troubleshoot disks attached to Azure VMs」 (Azure VM にアタッチされたディスクのトラブルシューティング)](../blobs/storage-troubleshoot-vhds.md) を参照してください。

## <a name="next-steps"></a>次の手順
* [Microsoft Azure ストレージ エクスプローラー](../../vs-azure-tools-storage-manage-with-storage-explorer.md)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。
* [Azure Blob Storage: クール層とホット層](../blobs/storage-blob-storage-tiers.md)
* [Azure Storage のレプリケーション](storage-redundancy.md)
* [Azure Storage の接続文字列を構成する](../storage-configure-connection-string.md)
* [AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md)
* [Azure Storage チームのブログ](http://blogs.msdn.com/b/windowsazurestorage/)

