---
title: Azure CLI 2.0 を使用して Azure DNS にドメイン ゾーン ファイルをインポートまたはエクスポートする | Microsoft Docs
description: Azure CLI 2.0 を使用して Azure DNS との間で DNS ゾーン ファイルをインポートおよびエクスポートする方法を説明します
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: f5797782-3005-4663-a488-ac0089809010
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: victorh
ms.openlocfilehash: 7578d078b147b5c4bf42f5343d3fdfdf6f0bc42e
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171607"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli-20"></a>Azure CLI 2.0 を使用した DNS ゾーン ファイルのインポートとエクスポート 

この記事では、Azure CLI 2.0 を使用し、Azure DNS の DNS ゾーン ファイルをインポートまたはエクスポートする方法について段階的に説明します。

## <a name="introduction-to-dns-zone-migration"></a>DNS ゾーンの移行に関する概要

DNS ゾーン ファイルは、ゾーン内のすべてのドメイン ネーム システム (DNS) レコードに関する詳細が含まれるテキスト ファイルです。 このファイルは標準の形式に従っているため、DNS システム間で DNS レコードを転送するのに適しています。 ゾーン ファイルを使用すれば、Azure DNS との間で DNS ゾーンを迅速に、確実に、かつ簡単に転送することができます。

Azure DNS では、Azure コマンドライン インターフェイス (CLI) を使用したゾーン ファイルのインポートおよびエクスポートをサポートしています。 Azure PowerShell または Azure Portal を使用したゾーン ファイルのインポートは、現時点では**サポートされていません**。

Azure CLI 2.0 は、Azure サービスを管理するためのクロスプラットフォーム コマンド ライン ツールです。 Azure CLI は Windows、Mac、Linux のプラットフォームに対応しており、[Azure ダウンロード ページ](https://azure.microsoft.com/downloads/)から入手できます。 このクロスプラットフォームのサポートは、ゾーン ファイルのインポートおよびエクスポートで重要となります。それは最も一般的なネーム サーバー ソフトウェアである [BIND](https://www.isc.org/downloads/bind/) が、通常、Linux 上で実行されるからです。


## <a name="obtain-your-existing-dns-zone-file"></a>既存の DNS ゾーン ファイルの取得

Azure DNS に DNS ゾーン ファイルをインポートするには、事前にゾーン ファイルのコピーを取得しておく必要があります。 このファイルのソースは、DNS ゾーンが現在ホストされている場所によって異なります。

* DNS ゾーンがパートナーのサービス (ドメイン レジストラー、専用の DNS ホスティング プロバイダー、代替クラウド プロバイダーなど) でホストされている場合、そのサービスには、DNS ゾーン ファイルをダウンロードする機能があるはずです。
* DNS ゾーンが Windows DNS にホストされている場合、ゾーン ファイルは既定のフォルダー **%systemroot%\system32\dns** にあります。 各ゾーン ファイルへの完全なパスは、DNS コンソールの **[全般]** タブにも表示されます。
* DNS ゾーンが BIND を使用してホストされている場合、各ゾーンのゾーン ファイルの場所は、 **named.conf**という名前の BIND 構成ファイルに指定されています。

> [!NOTE]
> GoDaddy からダウンロードされるゾーン ファイルは標準形式と若干異なります。 これらのゾーン ファイルを Azure DNS にインポートする前に修正する必要があります。
>
> 各 DNS レコードの RDATA にある DNS 名は、完全修飾名として指定されますが、接尾辞 "." がありません。このため、このような DNS 名は他の DNS システムで相対名として解釈されます。 Azure DNS にインポートする前に、ゾーン ファイルを編集してその名前に接尾辞 "." を付加する必要があります。
>
> たとえば、CNAME レコード "www 3600 IN CNAME contoso.com" は "www 3600 IN CNAME contoso.com."
> (接尾辞 "." 付き) に変更する必要があります。

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Azure DNS への DNS ゾーン ファイルのインポート

ゾーン ファイルのインポートを実行すると、Azure DNS にゾーンが存在しない場合、新しいゾーンが作成されます。 ゾーンが既に存在する場合は、ゾーン ファイル内のレコード セットを既存のレコード セットにマージする必要があります。

### <a name="merge-behavior"></a>マージ動作

* 既定では、既存のレコード セットと新規のレコード セットがマージされます。 マージされたレコード セット内の一致するレコードは重複除去されます。
* レコード セットをマージした場合、既存のレコード セットの TTL (Time to Live) が使用されます。
* SOA (Start of Authority) パラメーター (`host` を除く) は常に、インポートされたゾーン ファイルから取得されます。 同様に、ゾーンの頂点にあるネーム サーバー レコード セットの場合も、TTL は常に、インポートされたゾーン ファイルから取得されます。
* インポートされた CNAME レコードが同じ名前を持つ既存の CNAME レコードに取って代わることはありません。  
* CNAME レコードと、名前は同じでも形式が異なる別のレコードとの間で競合が生じた場合 (どちらが既存のレコードでどちらが新規のレコードかに関係なく)、既存のレコードが保持されます。 

### <a name="additional-information-about-importing"></a>インポートに関する追加情報

次に、ゾーンのインポート プロセスに関するその他の技術的詳細を説明します。

* `$TTL` ディレクティブは省略可能であり、サポートされています。 `$TTL` ディレクティブを指定しなかった場合、明示的な TTL を持たないレコードは、既定の TTL である 3600 秒が設定されてインポートされます。 同じレコード セット内の 2 つのレコードで別々の TTL を指定した場合は、小さい方の値が使用されます。
* `$ORIGIN` ディレクティブは省略可能であり、サポートされています。 `$ORIGIN` を設定しないと、コマンドラインで指定したゾーン名 (に加えて接尾辞 ".") が既定値として使用されます。
* `$INCLUDE` と `$GENERATE` ディレクティブはサポートされていません。
* A、AAAA、CNAME、MX、NS、SOA、SRV、および TXT のレコード タイプはサポートされています。
* ゾーンの作成時には、Azure DNS によって SOA レコードが自動的に作成されます。 ゾーン ファイルのインポート時には、`host` パラメーターを "*除き*"、すべての SOA パラメーターがゾーン ファイルから取得されます。 このパラメーターは Azure DNS から提供された値を使用します。 ‘host’ パラメーターは、Azure DNS で指定されたプライマリ ネーム サーバーを参照する必要があるからです。
* ゾーンの作成時には、ゾーンの頂点のネーム サーバー レコード セットも Azure DNS によって自動的に作成されます。 このレコード セットの TTL のみがインポートされます。 これらのレコードには、Azure DNS によって提供されたネーム サーバー名が格納されています。 レコード データは、インポートされたゾーン ファイルに含まれる値によって上書きされることはありません。
* パブリック プレビュー時、Azure DNS では単一行文字列の TXT レコードしかサポートしません。 複数行文字列の TXT レコードは連結され、255 文字に切り捨てられます。

### <a name="cli-format-and-values"></a>CLI 形式と値

DNS ゾーンをインポートする Azure CLI コマンドの形式は次のとおりです。

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

値:

* `<resource group>` : Azure DNS のゾーンのリソース グループの名前です。
* `<zone name>` : ゾーンの名前です。
* `<zone file name>` : インポートするゾーン ファイルのパス/名前です。

リソース グループ内にこの名前のゾーンが存在しない場合は、自動的に作成されます。 ゾーンが既に存在する場合、インポートされたレコード セットは既存のレコード セットとマージされます。 


### <a name="step-1-import-a-zone-file"></a>手順 1. ゾーン ファイルをインポートする

ゾーン **contoso.com**のゾーン ファイルをインポートするには。

1. まだ存在しない場合は、リソース マネージャー リソース グループを作成する必要があります。

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. ファイル **contoso.com.txt** のゾーン **contoso.com** を、リソース グループ **myresourcegroup** 内の新しい DNS ゾーンにインポートするには、コマンド `az network dns zone import` を実行します。<BR>このコマンドは、ゾーン ファイルを読み込み、解析します。 このコマンドは、Azure DNS サービスに対して一連のコマンドを実行することで、ゾーンとゾーン内のすべてのレコード セットを作成します。 このコマンドでは、コンソール ウィンドウに進捗状況をレポートします。発生している場合、エラーまたは警告もレポートします。 レコード セットは順番に作成されるので、大規模なゾーン ファイルをインポートするのに数分かかる場合があります。

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>手順 2. ゾーンを確認する

次のいずれかの方法で、ファイルをインポートした後に DNS ゾーンを確認できます。

* 次の Azure CLI コマンドを使用してレコードを一覧表示できます。

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* PowerShell コマンドレット `Get-AzureRmDnsRecordSet`でレコードを一覧表示できます。
* `nslookup` を使用し、レコードの名前解決を確認できます。 ゾーンはまだ委任されていないので、適切な Azure DNS ネーム サーバーを明示的に指定する必要があります。 次の例では、ゾーンに割り当てられているネーム サーバー名を取得する方法を示します。 `nslookup` を使用して "www" レコードを照会する方法も示します。

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="step-3-update-dns-delegation"></a>手順 3. DNS 委任を更新する

ゾーンが正しくインポートされたことを確認したら、Azure DNS ネーム サーバーを指すように DNS 委任を更新します。 詳細については、 [DNS 委任の更新に関する記事](dns-domain-delegation.md)を参照してください。

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Azure DNS からの DNS ゾーン ファイルのエクスポート

DNS ゾーンをインポートする Azure CLI コマンドの形式は次のとおりです。

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

値:

* `<resource group>` : Azure DNS のゾーンのリソース グループの名前です。
* `<zone name>` : ゾーンの名前です。
* `<zone file name>` : エクスポートするゾーン ファイルのパス/名前です。

ゾーンのインポートと同様に、まずサインインし、サブスクリプションを選択して、Resource Manager モードを使用するように Azure CLI を構成します。

### <a name="to-export-a-zone-file"></a>ゾーン ファイルをエクスポートするには

リソース グループ **myresourcegroup** 内の既存の Azure DNS ゾーン **contoso.com** をファイル **contoso.com.txt** (現在のフォルダーにある) にエクスポートするには、`azure network dns zone export` を実行します。 このコマンドは、Azure DNS サービスを呼び出すことで、ゾーン内のレコード セットを列挙し、その結果を BIND と互換性のあるゾーン ファイルにエクスポートします。

    ```
    az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```
