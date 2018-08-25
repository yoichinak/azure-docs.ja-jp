---
title: チュートリアル - Web アプリのカスタム Azure DNS レコードの作成
description: このチュートリアルでは、Azure DNS を使用して Web アプリのカスタム ドメイン DNS レコードを作成します。
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/20/2018
ms.author: victorh
ms.openlocfilehash: b39c2c672869bb446e58134a85130d10491fe047
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621115"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>チュートリアル: カスタム ドメインにおける Web アプリの DNS レコードの作成 

Azure DNS を構成して、Web アプリ用にカスタム ドメインをホストすることができます。 たとえば、Azure Web アプリを作成し、www.contoso.com または contoso.com を完全修飾ドメイン名 (FQDN) として使用してユーザーがアクセスできるようにすることができます。

> [!NOTE]
> このチュートリアルでは、contoso.com を例として使用します。 contoso.com を独自のドメイン名に置き換えてください。

これを行うには、3 つのレコードを作成する必要があります。

* contoso.com を指すルート "A" レコード
* 検証用のルート "TXT" レコード
* その A レコードを指す www 名の "CNAME" レコード

Azure の Web アプリ用に A レコードを作成する場合、Web アプリの基になる IP アドレスが変更されると、A レコードを手動で更新する必要があることに注意してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * カスタム ドメインの A および TXT レコードの作成
> * カスタム ドメインの CNAME レコードの作成
> * 新しいレコードのテスト
> * Web アプリのカスタム ホスト名の追加
> * カスタム ホスト名のテスト


Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>前提条件

- [App Service アプリを作成する](../app-service/app-service-web-get-started-html.md)か、別のチュートリアルで作成したアプリを使用します。

- Azure DNS に DNS ゾーンを作成し、レジストラーのゾーンを Azure DNS に委任します。

   1. DNS ゾーンを作成するには、「 [DNS ゾーンの作成](dns-getstarted-create-dnszone.md)」の手順に従います。
   2. ゾーンを Azure DNS に委任するには、[DNS ドメインの委任](dns-domain-delegation.md)に関する記事の手順に従います。

ゾーンを作成し、それを Azure DNS に委任したら、カスタム ドメインのレコードを作成できます。

## <a name="create-an-a-record-and-txt-record"></a>A レコードと TXT レコードの作成

A レコードは、名前をその IP アドレスに対応付けるために使用されます。 次の例では、Web アプリの IPv4 アドレスを使用して "\@" を A レコードとして割り当てます。 通常、\@ はルート ドメインを表します。

### <a name="get-the-ipv4-address"></a>IPv4 アドレスの取得

Azure portal の App Services ページの左側のナビゲーションで、**[カスタム ドメイン]** を選択します。 

![[カスタム ドメイン] メニュー](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**[カスタム ドメイン]** ページで、アプリの IPv4 アドレスをコピーします。

![Azure アプリへのポータル ナビゲーション](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>A レコードを作成する

```powershell
New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>TXT レコードの作成

App Services は、このレコードを、カスタム ドメインの所有者であることを検証するために構成時にのみ使用します。 App Service でカスタム ドメインが検証されて構成された後は、この TXT レコードを削除できます。

```powershell
New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name `"@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>CNAME レコードを作成する

ドメインが既に Azure DNS で管理されている場合 ( [DNS ドメインの委任](dns-domain-delegation.md)に関するページを参照)、次の例を使用して contoso.azurewebsites.net の CNAME レコードを作成できます。

Azure PowerShell を開き、新しい CNAME レコードを作成します。 この例では、Web アプリの別名 contoso.azurewebsites.net を使用して、"contoso.com" という名前の DNS ゾーンに 600 秒の "Time to Live" でレコード セット型 CNAME を作成します。

### <a name="create-the-record"></a>レコードの作成

```powershell
New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -cname "contoso.azurewebsites.net")
```

次の例は応答です。

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

## <a name="test-the-new-records"></a>新しいレコードのテスト

次のように nslookup を使用して "www.contoso.com" と "contoso.com" にクエリを実行することによって、レコードが正しく作成されたかどうかを検証できます。

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>カスタム ホスト名の追加

ここで、Web アプリにカスタム ホスト名を追加できます。

```powershell
set-AzureRmWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>カスタム ホスト名のテスト

ブラウザーを開き、`http://www.<your domainname>` と `http://<you domain name>` を参照します。

> [!NOTE]
> 必ず、`http://` プレフィックスを含めます。そうしないと、ブラウザーが URL の予測を試みることがあります。

両方の URL で同じページが表示されます。 例: 

![Contoso アプリ サービス](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成したリソースが不要になったら、**myresourcegroup** リソース グループを削除できます。

## <a name="next-steps"></a>次の手順

Azure DNS プライベート ゾーンを作成する方法について学習します。

> [!div class="nextstepaction"]
> [PowerShell で Azure DNS プライベート ゾーンの使用を開始する](private-dns-getstarted-powershell.md)
