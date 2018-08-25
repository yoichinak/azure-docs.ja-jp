---
title: Azure DNS とは
description: Microsoft Azure の DNS ホスティング サービスの概要です。 Microsoft Azure でドメインをホストします。
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: overview
ms.date: 6/7/2018
ms.author: victorh
ms.openlocfilehash: e95617664ee30f1b9253f1892176fd39649ee2c2
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39174634"
---
# <a name="what-is-azure-dns"></a>Azure DNS とは

Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。

Azure DNS を使用してドメイン名を購入することはできません。 年会費をお支払いになると、[Azure Web Apps](https://docs.microsoft.com/en-us/azure/app-service/custom-dns-web-site-buydomains-web-app#buy-the-domain) またはサードパーティのドメイン名レジストラーを使用して、ドメイン名を購入できます。 購入したドメインは、Azure DNS でホストし、レコードを管理できます。 詳細については、「 [Azure DNS へのドメインの委任](dns-domain-delegation.md) 」を参照してください。

Azure DNS には、次の機能が含まれています。

## <a name="reliability-and-performance"></a>信頼性とパフォーマンス

Azure DNS 内の DNS ドメインは、DNS ネーム サーバーから成る Azure のグローバル ネットワーク上でホストされます。 Azure DNS ではエニーキャスト ネットワークが使用されるため、各 DNS クエリには、使用できる最も近い DNS サーバーが応答します。 これによって、ドメインには高速なパフォーマンスと高可用性の両方が提供されます。

## <a name="security"></a>セキュリティ

この Azure DNS サービスは、Azure Resource Manager に基づいています。 そのため、Resource Manager の次のような機能を利用できます。

* [ロールベースのアクセス制御](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#access-control) - 組織のユーザーがアクセスできるアクションを制御できます。

* [アクティビティ ログ](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#activity-logs) - 組織のユーザーどのようにリソースを変更したかを監視したり、トラブルシューティング時にエラーを見つけたりできます。

* [リソース ロック](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-lock-resources) - サブスクリプション、リソース グループ、またはリソースにロックを適用し、組織の他のユーザーが誤って重要なリソースを削除したり変更したりするのを防止できます。

詳細については、「[DNS ゾーンとレコードを保護する方法](dns-protect-zones-recordsets.md)」を参照してください。 


## <a name="ease-of-use"></a>使いやすさ

Azure DNS サービスでは、Azure サービスの DNS レコードを管理したり、外部リソースに DNS を提供したりできます。 Azure DNS は Azure Portal に統合されており、他の Azure サービスと同じ資格情報、サポート契約、課金を使用します。 

DNS の課金は、Azure でホストされている DNS ゾーンの数と、DNS クエリの数に基づきます。 価格の詳細については、「[Azure DNS の価格](https://azure.microsoft.com/pricing/details/dns/)」を参照してください。

ドメインとレコードは、Azure Portal、Azure PowerShell コマンドレット、およびクロス プラットフォームの Azure CLI を使用して管理できます。 DNS の自動管理を必要とするアプリケーションは、REST API および SDK を使用してサービスと統合できます。

## <a name="customizable-virtual-networks-with-private-domains"></a>プライベート ドメインを持つカスタマイズ可能な仮想ネットワーク

Azure DNS では、プライベート DNS ゾーンもサポートされるようになりました (現在はパブリック プレビュー段階です)。 このため、現在利用可能な Azure 提供の名前ではなく、独自のカスタム ドメイン名をプライベート仮想ネットワーク内で使用できます。

詳しくは、「[プライベート ドメインに Azure DNS を使用する](private-dns-overview.md)」をご覧ください。


## <a name="next-steps"></a>次の手順

* DNS ゾーンとレコードについて学びます ( 「[DNS ゾーンとレコードの概要](dns-zones-records.md)」)。

* Azure DNS 内にゾーンを作成する方法を学びます (「[DNS ゾーンの作成](./dns-getstarted-create-dnszone-portal.md)」)。

* Azure DNS に関してよく寄せられる質問については、「[Azure DNS に関する FAQ](dns-faq.md)」を参照してください。

