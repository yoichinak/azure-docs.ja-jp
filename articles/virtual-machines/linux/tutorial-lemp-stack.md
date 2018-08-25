---
title: チュートリアル - Azure 内の Linux 仮想マシンに LEMP を展開する | Microsoft Docs
description: このチュートリアルでは、Azure 内の Linux 仮想マシンに LEMP スタックをインストールする方法について説明します
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: danlep
ms.openlocfilehash: 1f4c74ab96fc9494d1b3c7dd626265516533330b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38606349"
---
# <a name="tutorial-install-a-lemp-web-server-on-a-linux-virtual-machine-in-azure"></a>チュートリアル: Azure 内の Linux 仮想マシンに LEMP Web サーバーをインストールする

この記事では、NGINX Web サーバー、MySQL、PHP (LEMP スタック) を Azure　上の Ubuntu VM にデプロイする方法について説明します。 LEMP スタックは、同様に Azure にインストールすることができる、一般的な [LAMP スタック](tutorial-lamp-stack.md) の代替品です。 LEMP サーバーの動作を確認するために、WordPress サイトをインストールし、構成することもできます。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Ubuntu 仮想マシン (LEMP スタックで 'L') を作成する
> * Web トラフィック用にポート 80 を開く
> * NGINX、MySQL、および PHP をインストールする
> * インストールと構成を確認する
> * LEMP サーバーに WordPress をインストールする

このセットアップは、簡単なテストまたは概念実証のためのものです。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-nginx-mysql-and-php"></a>NGINX、MySQL、および PHP をインストールする

次のコマンドを実行して、Ubuntu パッケージ ソースを更新し NGINX、MySQL、および PHP をインストールします。 

```bash
sudo apt update && sudo apt install nginx mysql-server php-mysql php php-fpm
```

パッケージやその他の依存関係をインストールすることが求められます。 メッセージが表示されたら、MySQL のルート パスワードを設定し、Enter キーを押して続行します。 残りの指示に従います。 このプロセスにより、MySQL で PHP を使用するための必要最小限の PHP 拡張機能がインストールされます。 

![MySQL ルート パスワード ページ][1]

## <a name="verify-installation-and-configuration"></a>インストールと構成を確認する


### <a name="nginx"></a>NGINX

次のコマンドで NGINX のバージョンを確認します。
```bash
nginx -v
```

NGINX がインストールされ、VM に対しポート 80 が開かれると、Web サーバーにインターネットからアクセスできるようになります。 NGINX のウェルカム ページを表示するには、Web ブラウザーを開いて、VM のパブリック IP アドレスを入力します。 VM に対する SSH で使用したパブリック IP アドレスを使用します。

![NGINX の既定のページ][3]


### <a name="mysql"></a>MySQL

次のコマンドを使用して MySQL のバージョンを確認します (大文字の `V` パラメーターに注意)。

```bash
mysql -V
```

MySQL のインストールのセキュリティ保護を強化するには、`mysql_secure_installation` スクリプトを実行します。 一時的なサーバーをセットアップするだけの場合は、このステップを省略できます。 

```bash
mysql_secure_installation
```

MySQL のルート パスワードを入力し、環境のセキュリティ設定を構成します。

MySQL の機能 (MySQL データベースの作成、ユーザーの追加、構成設定の変更) を試したい場合は、MySQL にログインします。 このチュートリアルを実行するには、このステップは必要ありません。 


```bash
mysql -u root -p
```

完了したら「`\q`」と入力して、mysql プロンプトを終了します。

### <a name="php"></a>PHP

次のコマンドで PHP のバージョンを確認します。

```bash
php -v
```

PHP FastCGI Process Manager (PHP-FPM) を使用するよう NGINX を構成します。 次のコマンドを実行して元の NGINX サーバー ブロック構成ファイルをバックアップし、任意のエディターで元のファイルを編集してください。

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default_backup

sudo sensible-editor /etc/nginx/sites-available/default
```

エディターで、`/etc/nginx/sites-available/default` の内容を次のコードに置き換えます。 設定を説明するコメントを参照してください。 *yourPublicIPAddress* を VM のパブリック IP アドレス置き換え、その他の設定はそのままにします。 そのうえでファイルを保存します。

```
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    # Homepage of website is index.php
    index index.php;

    server_name yourPublicIPAddress;

    location / {
        try_files $uri $uri/ =404;
    }

    # Include FastCGI configuration for NGINX
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }
}
```

NGINX 構成に構文エラーがないか確認します。

```bash
sudo nginx -t
```

構文が正しい場合は、次のコマンドを使用して NGINX を再起動します。

```bash
sudo service nginx restart
```

さらに詳しくテストする場合は、簡単な PHP 情報ページを作成して、ブラウザーで表示します。 次のコマンドは、PHP 情報ページを作成します。

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```



ここで作成した PHP 情報ページを確認できます。 Web ブラウザーを開いて､`http://yourPublicIPAddress/info.php` に移動します。 IP アドレスは、実際の VM のパブリック IP アドレスに置き換えてください。 以下の画像のようなページが表示されます。

![PHP 情報ページ][2]


[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure に LEMP サーバーを展開しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Ubuntu VM を作成する
> * Web トラフィック用にポート 80 を開く
> * NGINX、MySQL、および PHP をインストールする
> * インストールと構成を確認する
> * LEMP スタックに WordPress をインストールする

SSL 証明書を使用して Web サーバーをセキュリティ保護する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [SSL による Web サーバーのセキュリティ保護](tutorial-secure-web-server.md)

[1]: ./media/tutorial-lemp-stack/configmysqlpassword-small.png
[2]: ./media/tutorial-lemp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lemp-stack/nginx.png
