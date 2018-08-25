---
title: チュートリアル - Azure で cloud-init を使用して Linux VM をカスタマイズする | Microsoft Docs
description: このチュートリアルでは、Azure での Linux VM の初回の起動時に cloud-init と Key Vault を使用してそれらをカスタマイズする方法を説明します
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 754a5011bb139b313d3b0ffcf4e6560fde9a9474
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928473"
---
# <a name="tutorial---how-to-use-cloud-init-to-customize-a-linux-virtual-machine-in-azure-on-first-boot"></a>チュートリアル - Azure での Linux 仮想マシンの初回の起動時に cloud-init を使用してカスタマイズする方法

前のチュートリアルでは、仮想マシン (VM) に SSH 接続して NGINX を手動でインストールする方法について説明しました。 VM を迅速かつ一貫した方法で作成するには、一般的に、何らかの形で自動化することが必要です。 VM を初回起動時にカスタマイズする一般的なアプローチには、[cloud-init](https://cloudinit.readthedocs.io) を使用する方法があります。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * cloud-init 構成ファイルを作成する
> * cloud-init ファイルを使用する VM を作成する
> * VM の作成後に実行されている Node.js アプリを表示する
> * Key Vault を使用して証明書を安全に格納する
> * cloud-init を使用して NGINX のセキュリティで保護されたデプロイを自動化する

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。

## <a name="cloud-init-overview"></a>cloud-init の概要
[cloud-Init](https://cloudinit.readthedocs.io) は、Linux VM を初回起動時にカスタマイズするために広く使用されているアプローチです。 cloud-init を使って、パッケージをインストールしてファイルを書き込んだり、ユーザーとセキュリティを構成したりすることができます。 初回起動処理中に cloud-init が実行されるので、構成を適用するために追加の手順や必要なエージェントはありません。

cloud-init はディストリビューション全体でも有効です。 たとえば、パッケージをインストールするときに **apt-get install** や **yum install** は使用しません。 代わりに、cloud-init ではインストールするパッケージの一覧をユーザーが定義できます。 cloud-init によって、選択したディストリビューションに対してネイティブのパッケージ管理ツールが自動的に使用されます。

Microsoft ではパートナーと協力して、パートナーから Azure に提供されたイメージに cloud-init を含めて、使用できるようにしています。 次の表は、Azure プラットフォーム イメージでの最新の cloud-init の可用性の概要を示しています。

| エイリアス | 発行元 | プラン | SKU | バージョン |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |16.04 LTS |latest |
| UbuntuLTS |Canonical |UbuntuServer |14.04.5-LTS |latest |
| CoreOS |CoreOS |CoreOS |安定版 |latest |
| | OpenLogic | CentOS | 7-CI | latest |
| | RedHat | RHEL | 7-RAW-CI | latest |


## <a name="create-cloud-init-config-file"></a>cloud-init 構成ファイルを作成する
cloud-init が動作していることを確認するには、NGINX をインストールして単純な "Hello World" Node.js アプリを実行する VM を作成します。 次の cloud-init 構成によって、必要なパッケージのインストール、Node.js アプリの作成、アプリの初期化と起動が行われます。

現在のシェルで、*cloud-init.txt* というファイルを作成し、次の構成を貼り付けます。 たとえば、ローカル コンピューター上にない Cloud Shell でファイルを作成します。 任意のエディターを使用することができます。 `sensible-editor cloud-init.txt` を入力し、ファイルを作成して使用可能なエディターの一覧を確認します。 cloud-init ファイル全体 (特に最初の行) が正しくコピーされたことを確認してください。

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

cloud-init 構成オプションの詳細については、[cloud-init の構成例](https://cloudinit.readthedocs.io/en/latest/topics/examples.html)に関するページを参照してください。

## <a name="create-virtual-machine"></a>仮想マシンの作成
VM を作成する前に、[az group create](/cli/azure/group#az-group-create) を使用してリソース グループを作成します。 次の例では、*myResourceGroupAutomate* という名前のリソース グループを場所 *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroupAutomate --location eastus
```

ここで [az vm create](/cli/azure/vm#az-vm-create) を使用して VM を作成します。 `--custom-data` パラメーターを使用して、cloud-init 構成ファイルを渡します。 現在の作業ディレクトリの外部に構成ファイル *cloud-init.txt* を保存していた場合には、このファイルの完全パスを指定します。 次の例では、*myAutomatedVM* という名前の VM を作成します。

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

VM が作成され、パッケージがインストールされて、アプリが開始されるには、数分かかります。 Azure CLI がプロンプトに戻った後にも引き続き実行するバック グラウンド タスクがあります。 アプリにアクセスできるようになるには、さらに数分かかる場合があります。 VM が作成されたら、Azure CLI によって表示される `publicIpAddress` をメモしてください。 このアドレスは、Web ブラウザーから Node.js アプリにアクセスするために使用します。

Web トラフィックが VM にアクセスできるようにするには、[az vm open-port](/cli/azure/vm#az-vm-open-port) を使用してインターネットからポート 80 を開きます。

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myVM
```

## <a name="test-web-app"></a>Web アプリのテスト
Web ブラウザーを開き、アドレス バーに「*http://<publicIpAddress>*」と入力できるようになりました。 VM 作成処理で取得した独自のパブリック IP アドレスを指定します。 Node.js アプリは次の例のように表示されます。

![実行中の NGINX サイトの表示](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Key Vault の証明書の挿入
省略可能なこのセクションでは、証明書を Azure Key Vault に安全に格納し、VM のデプロイ時に挿入する方法を説明します。 組み込みの証明書を含むカスタム イメージを使用するのではなく、この処理を使用することによって、初回起動時に最新の証明書が VM に挿入されます。 処理の際に、証明書が Azure プラットフォームから流出したり、スクリプト、コマンドラインの履歴、またはテンプレートで公開されたりすることはありません。

Azure Key Vault では、証明書やパスワードなどの暗号化キーと秘密が保護されます。 Key Vault は、キー管理プロセスを合理化し、データにアクセスして暗号化するキーの制御を維持するのに役立ちます。 このシナリオでは、証明書を作成して使用するための Key Vault の概念をいくつか紹介します。ただし、これは Key Vault の使用方法に関する網羅的な概要ではありません。

以下の手順では、次の操作方法を説明します。

- Azure Key Vault を作成する
- 証明書を生成したり、Key Vault にアップロードしたりする
- 証明書からシークレットを作成して VM に挿入する
- VM を作成して証明書を挿入する

### <a name="create-an-azure-key-vault"></a>Azure Key Vault を作成する
最初に、[az keyvault create](/cli/azure/keyvault#az-keyvault-create) を使用して Key Vault を作成し、VM をデプロイするときに使用できるようにします。 各 Key Vault には一意の名前が必要であり、その名前はすべて小文字にする必要があります。 次の例の *mykeyvault* は一意の Key Vault 名で置き換えてください。

```azurecli-interactive
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>証明書を生成して Key Vault に格納する
実際の運用では、[az keyvault certificate import](/cli/azure/keyvault/certificate#az-keyvault-certificate-import) を使用して、信頼できるプロバイダーによって署名された有効な証明書をインポートする必要があります。 このチュートリアルでは、[az keyvault certificate create](/cli/azure/keyvault/certificate#az-keyvault-certificate-create) で、既定の証明書ポリシーを使用する自己署名証明書を生成する方法を次の例に示します。

```azurecli-interactive
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>VM で使用する証明書を準備する
VM の作成処理の際に証明書を使用するには、[az keyvault secret list-versions](/cli/azure/keyvault/secret#az-keyvault-secret-list-versions) を使用して証明書の ID を取得します。 VM は、ブート時に挿入するための特定の形式の証明書を必要とするため、[az vm secret format](/cli/azure/vm#az-vm-secret-format) を使って証明書を変換します。 次の例では、以降の手順で使用しやすくするために、コマンドの出力を変数に割り当てています。

```azurecli-interactive
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secret "$secret")
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>NGINX をセキュリティで保護する cloud-init 構成を作成する
VM を作成するとき、証明書とキーは、保護された */var/lib/waagent/* ディレクトリに格納されます。 VM への証明書の追加と NGINX の構成を自動化するために、前の例の更新された cloud-init 構成を使用できます。

*cloud-init-secured.txt* というファイルを作成し、次の構成を貼り付けます。 ここでも、Cloud Shell を使用する場合は、ローカル コンピューター上ではなく、その場所に cloud-init 構成ファイルを作成します。 `sensible-editor cloud-init-secured.txt` を使用し、ファイルを作成して使用可能なエディターの一覧を確認します。 cloud-init ファイル全体 (特に最初の行) が正しくコピーされたことを確認してください。

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>セキュリティで保護された VM を作成する
ここで [az vm create](/cli/azure/vm#az-vm-create) を使用して VM を作成します。 証明書のデータは、`--secrets` パラメーターを使用して Key Vault から挿入されます。 前の例のように、`--custom-data` パラメーターを使用して cloud-init 構成も渡します。

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

VM が作成され、パッケージがインストールされて、アプリが開始されるには、数分かかります。 Azure CLI がプロンプトに戻った後にも引き続き実行するバック グラウンド タスクがあります。 アプリにアクセスできるようになるには、さらに数分かかる場合があります。 VM が作成されたら、Azure CLI によって表示される `publicIpAddress` をメモしてください。 このアドレスは、Web ブラウザーから Node.js アプリにアクセスするために使用します。

セキュリティで保護された Web トラフィックが VM にアクセスできるようにするには、[az vm open-port](/cli/azure/vm#az-vm-open-port) を使用してインターネットからポート 443 を開きます。

```azurecli-interactive
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --port 443
```

### <a name="test-secure-web-app"></a>セキュリティで保護された Web アプリをテストする
Web ブラウザーを開き、アドレス バーに「*https://<publicIpAddress>*」と入力できるようになりました。 前の VM 作成プロセスの出力で示されているように、独自のパブリック IP アドレスを提供します。 自己署名証明書を使用した場合は、セキュリティ警告を受け入れます。

![Web ブラウザーのセキュリティ警告を受け入れる](./media/tutorial-automate-vm-deployment/browser-warning.png)

セキュリティで保護された NGINX サイトと Node.js アプリは、次の例のように表示されます。

![セキュリティで保護された実行中の NGINX サイトの表示](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>次の手順
このチュートリアルでは、VM の初回の起動時に cloud-init を使用してカスタマイズしました。 以下の方法について学習しました。

> [!div class="checklist"]
> * cloud-init 構成ファイルを作成する
> * cloud-init ファイルを使用する VM を作成する
> * VM の作成後に実行されている Node.js アプリを表示する
> * Key Vault を使用して証明書を安全に格納する
> * cloud-init を使用して NGINX のセキュリティで保護されたデプロイを自動化する

次のチュートリアルに進み、カスタムの VM イメージを作成する方法を学習してください。

> [!div class="nextstepaction"]
> [カスタム VM イメージを作成する](./tutorial-custom-images.md)
