---
title: Red Hat Update Infrastructure | Microsoft Docs
description: Microsoft Azure のオンデマンド Red Hat Enterprise Linux インスタンス用の Red Hat Update Infrastructure について説明します
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: borisb
ms.openlocfilehash: 570b820e21df6db70b9cadf33d5a120132be62ed
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426753"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Azure のオンデマンド Red Hat Enterprise Linux VM 用 Red Hat Update Infrastructure
 クラウド プロバイダー (Azure など) は、[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) を使用して、Red Hat でホストされているリポジトリのコンテンツのミラーリング、Azure 固有のコンテンツを使用したカスタム リポジトリの作成、およびエンド ユーザーの VM での使用を実行できます。

Red Hat Enterprise Linux (RHEL) 従量課金制 (PAYG) イメージには、Azure RHUI にアクセスするための構成が事前に設定されています。 追加の構成は必要ありません。 最新の更新プログラムを取得するには、RHEL インスタンスの準備ができてから `sudo yum update` を実行します。 このサービスは、RHEL PAYG ソフトウェア料金の一部として含まれています。

## <a name="important-information-about-azure-rhui"></a>Azure RHUI に関する重要な情報
* 現時点では、Azure RHUI は、各 RHEL ファミリ (RHEL6 または RHEL7) の最新のマイナー リリースのみをサポートしています。 RHUI に接続された RHEL VM インスタンスを最新のミラー バージョンにアップグレードするには、`sudo yum update` を実行します。

    たとえば、RHEL 7.2 PAYG イメージから VM をプロビジョニングして `sudo yum update` を実行した場合は、RHEL 7.4 VM (RHEL7 ファミリの最新のミラー バージョン) に更新されます。

    この動作を回避するには、[Azure 用の Red Hat ベースの仮想マシンの作成とアップロード](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事の説明に従って、独自のイメージを作成する必要があります。 その後、別の更新インフラストラクチャに接続する必要があります ([直接 Red Hat のコンテンツ配信サーバー](https://access.redhat.com/solutions/253273)または [Red Hat Satellite サーバーに](https://access.redhat.com/products/red-hat-satellite))。

* Azure でホストされている RHUI へのアクセスは、RHEL PAYG イメージの料金に含まれています。 Azure でホストされている RHUI から PAYG RHEL VM の登録を解除した場合は、仮想マシンが Bring-Your-Own-License (BYOL: ライセンス持ち込み) タイプの VM に変換されません。 そのため、別の更新ソースに同じ VM を登録した場合は、_間接_料金が二重に発生する可能性があります。 1 つ目は Azure RHEL ソフトウェア料金に対するものです。 2 つ目は、以前に購入した Red Hat のサブスクリプションに対するものです。 Azure でホストされている RHUI 以外の更新インフラストラクチャを常に使用する必要がある場合は、独自の (BYOL タイプの) イメージを作成してデプロイすることを検討してください。 このプロセスについては、[Azure 用の Red Hat ベースの仮想マシンの作成とアップロード](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。

* Azure での RHEL PAYG イメージの 2 つのクラス (RHEL for SAP HANA および RHEL for SAP Business Applications) は、SAP 認証で必要な特定の RHEL ミラー バージョンである専用の RHUI チャネルに接続されます。 

* Azure でホストされている RHUI へのアクセスは、[データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)内の VM に限定されます。 すべての VM トラフィックをオンプレミスのネットワーク インフラストラクチャ経由でプロキシ処理している場合は、RHEL PAYG VM 用のユーザー定義のルートを設定して Azure RHUI にアクセスしなければならない場合があります。

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>RHUI コンテンツ配信サーバーの IP アドレス

RHUI は、RHEL のオンデマンド イメージが提供されているすべてのリージョンで利用できます。 現時点では、[Azure の状態ダッシュボード](https://azure.microsoft.com/status/) ページに掲載されているすべてのパブリック リージョン、Azure US Government リージョン、および Microsoft Azure Germany リージョンが含まれます。 

ネットワーク構成を使用して RHEL PAYG VM からのアクセスをさらに制限している場合、現在の環境に応じて `yum update` が動作するよう次の IP が許可されていることを確認してください。 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="rhui-azure-infrastructure-update"></a>RHUI Azure インフラストラクチャの更新

2016 年 9 月に、更新済みの Azure RHUI をデプロイしました。 2017 年 4 月に、以前の Azure RHUI をシャットダウンしました。 2016 年 9 月以降から RHEL PAYG イメージ (またはそれらのスナップショット) を使用している場合、新しい Azure RHUI への接続は自動的に行われています。 ただし、VM に以前のスナップショットがある場合、Azure RHUI にアクセスするには、以降のセクションの説明に従って構成を手動で更新する必要があります。

新しい Azure RHUI サーバーは、[Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) を使用してデプロイされます。 Traffic Manager では、リージョンに関係なく、どの VM からも 1 つのエンドポイント (rhui-1.microsoft.com) を使用できます。 

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Azure RHUI への接続に関する問題のトラブルシューティング
Azure RHEL PAYG VM から Azure RHUI への接続で問題が発生した場合は、次の手順に従います。

1. Azure RHUI エンドポイントの VM 構成を確認します。

    a. `/etc/yum.repos.d/rh-cloud.repo` ファイルの `[rhui-microsoft-azure-rhel*]` セクションの `baseurl` に `rhui-[1-3].microsoft.com` への参照が含まれているかどうかを確認します。 含まれていれば、新しい Aure RHUI を使用していることになります。

    b. 次のパターン `mirrorlist.*cds[1-4].cloudapp.net` の場所をポイントしている場合、構成の更新が必要です。 古い VM スナップショットが使用されているため、新しい Azure RHUI をポイントするように更新する必要があります。

1. Azure でホストされている RHUI へのアクセスは、[Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)内の VM に限定されます。
 
1. 新しい構成を使用し、VM が Azure IP 範囲から接続していることを確認したが、それでも Azure RHUI に接続できない場合は、Microsoft または Red Hat にサポート ケースを提出してください。

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Azure RHUI サーバーを使用するための手動での更新手順
この手順は参照用にのみ提供されています。 RHEL PAYG イメージには既に、Azure RHUI に接続するための適切な構成があります。 Azure RHUI サーバーを使用するために構成を手動で更新するには、次の手順を実行します。

1. curl を使用して、公開キー署名をダウンロードします。

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
   ```

1. ダウンロードしたキーの有効性を確認します。

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

1. 出力を調べ、`keyid` と `user ID packet` を確認します。

   ```bash
   Version: GnuPG v1.4.7 (GNU/Linux)
   :public key packet:
           version 4, algo 1, created 1446074508, expires 0
           pkey[0]: [2048 bits]
           pkey[1]: [17 bits]
           keyid: EB3E94ADBE1229CF
   :user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
   :signature packet: algo 1, keyid EB3E94ADBE1229CF
           version 4, created 1446074508, md5len 0, sigclass 0x13
           digest algo 2, begin of digest 1a 9b
           hashed subpkt 2 len 4 (sig created 2015-10-28)
           hashed subpkt 27 len 1 (key flags: 03)
           hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
           hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
           hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
           hashed subpkt 30 len 1 (features: 01)
           hashed subpkt 23 len 1 (key server preferences: 80)
           subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
           data: [2047 bits]
   ```

1. 公開キーをインストールします。

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

1. クライアントの RPM Package Manager (RPM) をダウンロード、確認、およびインストールします。
    
    >[!NOTE]
    >パッケージ バージョンは変わります。 Azure RHUI に手動で接続する場合は、ギャラリーから最新のイメージをプロビジョニングすることで、各 RHEL ファミリ用のクライアント パッケージの最新バージョンを見つけることができます。
  
   a. ダウンロードします。 
   
    - RHEL 6 の場合:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
        ```
    
    - RHEL 7 の場合:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
        ```

   b. 確認します。

   ```bash
   rpm -Kv azureclient.rpm
   ```

   c. 出力を調べ、パッケージの署名が正しいことを確認します。

   ```bash
   azureclient.rpm:
       Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
       Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
       V3 RSA/SHA256 Signature, key ID be1229cf: OK
       MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
   ```

   d. RPM をインストールします。

    ```bash
    sudo rpm -U azureclient.rpm
    ```

1. 完了したら、VM から Azure RHUI にアクセスできることを確認します。

## <a name="next-steps"></a>次の手順
Azure Marketplace PAYG イメージから Red Hat Enterprise Linux VM を作成し、Azure でホストされる RHUI を使用する場合は、[Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/) にアクセスしてください。 
