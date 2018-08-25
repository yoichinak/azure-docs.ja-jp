---
title: Classic Azure コンテナー レジストリのアップグレード
description: 管理対象外の Classic コンテナー レジストリをアップグレードして、Basic、Standard、Premium の管理対象コンテナー レジストリの拡張機能セットを利用しましょう。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/15/2018
ms.author: marsma
ms.openlocfilehash: 562bd8da54605986e95d8105782ce7ebb9b359ea
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432390"
---
# <a name="upgrade-a-classic-container-registry"></a>Classic コンテナー レジストリのアップグレード

Azure Container Registry (ACR) は、[SKU と呼ばれる](container-registry-skus.md)いくつかのサービス階層で使用できます。 ACR の最初のリリースでは、Classic という SKU のみが提供されていましたが、この SKU には Basic、Standard、Premium の各 SKU (総称: *管理対象*レジストリ) の一部の機能が備わっていません。 この記事では、強化された機能セットを活用するために、管理対象外の Classic レジストリを、いずれかの管理対象 SKU に移行する方法についてご説明します。

## <a name="why-upgrade"></a>アップグレードする理由

Classic の管理対象外レジストリでは機能が制限されているため、すべての Classic レジストリを、Basic、Standard、Premium のいずれかの管理対象レジストリにアップグレードすることをお勧めします。 高いレベルの SKU ほど、レジストリが Azure の機能に深く統合されます。

管理対象のレジストリでは、次の機能を使用できます。

* Azure Active Directory 統合による[個別ログイン](container-registry-authentication.md#individual-login-with-azure-ad)
* イメージとタグの削除のサポート
* [geo レプリケーション](container-registry-geo-replication.md)
* [Webhook](container-registry-webhook.md)

中でも、クラシック レジストリは、レジストリの作成時に、Azure によって Azure サブスクリプションに自動的にプロビジョニングされたストレージ アカウントに依存しています。 これに対し、Basic、Standard、Premium の各 SKU では、Azure の[高度なストレージ機能](container-registry-storage.md)を利用するために、イメージのストレージをユーザーに代わり透過的に処理します。 個々のストレージ アカウントは、ユーザー独自のサブスクリプションでは作成されません。

管理対象レジストリのストレージには、次のような利点があります。

* コンテナー イメージは[保存時に暗号化](container-registry-storage.md#encryption-at-rest)されます。
* イメージは、[geo 冗長ストレージ](container-registry-storage.md#geo-redundant-storage)を使用して格納され、複数リージョンのレプリケーションによって、イメージのバックアップが確保されます。
* 自由に [SKU 間を移動](container-registry-skus.md#changing-skus)できるので、上位の SKU を選択するほど、より高いスループットが可能になります。 各 SKU では、ニーズの拡大に合わせて、ACR がスループット要件を満たすことができます。
* レジストリとそのストレージのセキュリティ モデルが統合されているため、権限の管理が簡単に行えます。 管理するのはコンテナー レジストリの権限だけで、別途ストレージ アカウントの権限も管理する必要はありません。

ACR のイメージ ストレージの詳細については、「[Azure Container Registry へのコンテナー イメージの保存](container-registry-storage.md)」を参照してください。

## <a name="migration-considerations"></a>移行に関する考慮事項

Classic レジストリを管理対象のレジストリに変更すると、Azure が、サブスクリプション内の ACR によって作成されたストレージ アカウントから、既存のコンテナー イメージを、Azure によって管理されているストレージ アカウントにコピーします。 レジストリのサイズによっては、このプロセスには数分から数時間かかることがあります。

変換プロセスの際、`docker push` 操作はすべてブロックされますが、`docker pull` は引き続き機能します。

変換プロセスの進行中、Classic レジストリを使用していたストレージ アカウントの内容を削除したり、変更したりしないでください。 コンテナー イメージの破損を招くおそれがあります。

移行の完了後は、お使いのサブスクリプションで元々 Classic レジストリを使用していたストレージ アカウントは、ACR で使用されなくなります。 移行が問題なく完了したことを確認したら、コストを最小限に抑えるために、このストレージ アカウントを削除することを検討してください。

>[!IMPORTANT]
> Classic からいずれかの管理対象 SKU にアップグレードすることは、**一方向のプロセス**です。 Basic、Standard、または Premium に変換した Classic レジストリを、Classic に戻すことはできません。 ただし、レジストリに対して十分な容量を持つ管理対象の各 SKU 間は、自由に移動できます。

## <a name="how-to-upgrade"></a>アップグレードする方法

管理対象外の Classic レジストリをいずかれの管理対象 SKU にアップグレードする方法は、いくつかあります。 次のセクションで、[Azure CLI][azure-cli] と [Azure Portal][azure-portal] を使用するプロセスをご説明します。

## <a name="upgrade-in-azure-cli"></a>Azure CLI でのアップグレード

Azure CLI で Classic レジストリをアップグレードするには、[az acr update][az-acr-update] コマンドを実行し、レジストリの新しい SKU を指定します。 次の例では、*myclassicregistry* という Classic レジストリを Premium SKU にアップグレードします。

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

移行が完了すると、次のような出力が表示されます。 `sku` が "Premium" に、`storageAccount` が "null," になっていることに注目してください。これは、Azure の管理対象が、このレジストリの画像ストレージになったことを示しています。

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

指定した管理対象レジストリ SKU の最大容量が、Classic レジストリのサイズよりも少ない場合、次のようなエラー メッセージが表示されます。

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

このようなエラーが表示されたら、再度 [az acr update][az-acr-update] コマンドを実行して、推奨される SKU を指定してください。推奨 SKU は、イメージに対応できる、2 番目にレベルの高い SKU です。

## <a name="upgrade-in-azure-portal"></a>Azure Portal でのアップグレード

Azure Portal で Classic レジストリをアップグレードする場合、イメージに対応できる最も低いレベルの SKU が、Azure によって自動で選択されます。 たとえば、レジストリのイメージが 12 GiB の場合、Classic レジストリから Standard (最大 100 GiB) に自動で選択、変換されます。

Azure Portal を使用して Classic レジストリをアップグレードするには、対象のコンテナー レジストリの **[概要]** に移動して、**[管理対象レジストリへのアップグレード]** を選択します。

![Azure Portal UI での Classic レジストリのアップグレード ボタン][update-classic-01-upgrade]


  **[OK]** を選択して、管理対象レジストリにアップグレードすることを確定します。

![Azure Portal UI での Classic レジストリのアップグレード確認][update-classic-02-confirm]

移行中、ポータルには、レジストリの **[プロビジョニング状態]** が *[更新中]* と表示されます。 前述のとおり、移行時は `docker push` 操作は無効になります。また、移行の進行中は、Classic レジストリによって使用されるストレージ アカウントの削除や更新はしないでください。イメージの破損を招くおそれがあります。

![Azure Portal UI での Classic レジストリのアップグレード進行状況][update-classic-03-updating]

移行が完了したら **[プロビジョニング状態]** が *[成功]* になり、再び、レジストリに `docker push` を実行できるようになります。

![Azure Portal UI での Classic レジストリのアップグレード完了状態][update-classic-04-updated]

## <a name="next-steps"></a>次の手順

Classic レジストリから Basic、Standard、または Premium へのアップグレードが完了したら、その Classic レジストリを元々使用していたストレージ アカウントは、Azure で使用されなくなります。 コスト削減のために、このストレージ アカウントを削除すること、またはこのアカウント内で、古いコンテナー イメージが格納されている BLOB コンテナーを削除することを検討してください。

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade\update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade\update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade\update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade\update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com