---
title: 既存のカスタム SSL 証明書の Azure Web Apps へのバインド | Microsoft Docs
description: Azure App Service で、Web アプリ、モバイル アプリのバックエンド、または API アプリにカスタム SSL 証明書をバインドする方法について説明します。
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/19/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 6e3679a994a7340fc38bf98f370efd359785fd90
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214967"
---
# <a name="tutorial-bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>チュートリアル: 既存のカスタム SSL 証明書を Azure Web Apps にバインドする

Azure Web Apps では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 このチュートリアルでは、信頼された証明機関から購入したカスタム SSL 証明書を [Azure Web Apps](app-service-web-overview.md) にバインドする方法について説明します。 完了したら、カスタムの DNS ドメインの HTTPS エンドポイントで、Web アプリにアクセスできるようになります。

![カスタム SSL 証明書付きの Web アプリ](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * アプリの価格レベルをアップグレードする
> * カスタム証明書を App Service にバインドする
> * 証明書の更新
> * HTTPS の適用
> * TLS 1.1/1.2 の適用
> * スクリプトを使って TLS 管理を自動化する

> [!NOTE]
> カスタム SSL 証明書を取得する必要がある場合、Azure Portal から直接取得して Web アプリにバインドすることができます。 [App Service 証明書のチュートリアル](web-sites-purchase-ssl-web-site.md)に従ってください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- [App Service アプリを作成する](/azure/app-service/)
- [カスタム DNS 名を Web アプリにマップする](app-service-web-tutorial-custom-domain.md)
- 信頼された証明機関から SSL 証明書を取得する
- SSL 証明書要求の署名に利用した秘密キーを用意する

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>SSL 証明書の必要条件

App Service で証明書を使用するには、証明書が次のすべての要件を満たしている必要があります。

* 信頼された証明機関によって署名されている
* パスワードで保護された PFX ファイルとしてエクスポートされている
* 少なくとも 2048 ビット長の秘密キーが含まれている
* 証明書チェーン内のすべての中間証明書が含まれている

> [!NOTE]
> **楕円曲線暗号 (ECC) 証明書** は、App Service で使用できますが、この記事では説明しません。 ECC 証明書を作成する正確な手順については、証明機関にお問い合わせください。

## <a name="prepare-your-web-app"></a>Web アプリの準備

カスタム SSL 証明書を Web アプリにバインドするには、[App Service プラン](https://azure.microsoft.com/pricing/details/app-service/)が **Basic**、**Standard** または **Premium** のいずれかのレベルである必要があります。 この手順では、Web アプリが、サポートされている価格レベルであることを確認します。

### <a name="log-in-to-azure"></a>Azure にログインする

[Azure Portal](https://portal.azure.com)を開きます。

### <a name="navigate-to-your-web-app"></a>Web アプリに移動する

左側のメニューで **[App Services]** をクリックした後、Web アプリの名前をクリックします。

![Web アプリの選択](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Web アプリの管理ページが表示されます。  

### <a name="check-the-pricing-tier"></a>価格レベルの確認

Web アプリ ページの左側のナビゲーションで **[設定]** セクションまでスクロールし、**[スケール アップ (App Service のプラン)]** を選択します。

![スケール アップ メニュー](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Web アプリが **F1** レベルまたは **D1** レベルに含まれていないことを確認します。 Web アプリの現在のレベルは、ダーク ブルーのボックスで強調表示されます。

![価格レベルの確認](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

カスタム SSL は、**F1** レベルまたは **D1** レベルではサポートされていません。 スケール アップする必要がある場合は、次のセクションの手順に従います。 それ以外の場合は、**[スケール アップ]** ページを閉じて、「[SSL 証明書のバインド](#upload)」に進んでください。

### <a name="scale-up-your-app-service-plan"></a>App Service プランのスケール アップ

非 Free レベルのいずれかを選びます (**B1**、**B2**、**B3**、または**運用**カテゴリのいずれかのレベル)。 その他のオプションについては、**[See additional options]\(その他のオプションを参照する\)** をクリックします。

**[Apply]** をクリックします。

![価格レベルの選択](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

次の通知が表示されたら、スケール操作は完了です。

![スケール アップの通知](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>SSL 証明書のバインド

Web アプリに、SSL 証明書をアップロードする準備ができました。

### <a name="merge-intermediate-certificates"></a>中間証明書を結合する

証明機関から証明書チェーンの複数の証明書を提供された場合は、それらの証明書を順番に結合する必要があります。 

これを行うには、受信した各証明書をテキスト エディターで開きます。 

結合した証明書用に _mergedcertificate.crt_ という名前のファイルを作成します。 テキスト エディターで、このファイルに各証明書の内容をコピーします。 証明書の順序は、証明書チェーンの順番に従う必要があります。自分の証明書から始まり、ルート証明書で終わります。 次の例のようになります。

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>PFX への証明書のエクスポート

結合した SSL 証明書を、証明書の要求と一緒に生成された秘密キーと共にエクスポートします。

証明書の要求の生成に OpenSSL を使用した場合、秘密キー ファイルは作成されています。 証明書を PFX にエクスポートするには、次のコマンドを実行します。 プレースホルダーの _&lt;private-key-file>_ と _&lt;merged-certificate-file>_ をそれぞれ、秘密キーのパスとマージされた証明書ファイルに変更します。

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

メッセージが表示されたら、エクスポートのパスワードを定義します。 このパスワードは、後で SSL 証明書を App Service にアップロードするときに使用します。

IIS または _Certreq.exe_ を使用して証明書の要求を生成した場合は、ローカル コンピューターに証明書をインストールした後で[証明書を PFX にエクスポート](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)します。

### <a name="upload-your-ssl-certificate"></a>SSL 証明書のアップロード

SSL 証明書をアップロードするには、Web アプリの左側のナビゲーションで **[SSL 設定]** をクリックします。

**[証明書のアップロード]** をクリックします。 

**[PFX 証明書ファイル]** で、PFX ファイルを選択します。 **[証明書のパスワード]** で、PFX ファイルのエクスポート時に作成したパスワードを入力します。

**[アップロード]** をクリックします。

![証明書のアップロード](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

App Service による証明書のアップロードが完了すると、**[SSL 設定]** ページにアップロードした証明書が表示されます。

![アップロードされた証明書](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>SSL 証明書のバインド

**[SSL バインディング]** セクションで、**[Add bindings] \(バインドの追加)** をクリックします。

**[SSL バインディングの追加]** ページで、ドロップダウンから保護するドメインの名前と使用する証明書を選択します。

> [!NOTE]
> 証明書をアップロードしたのに **[ホスト名]** ドロップダウンにドメイン名が表示されない場合は、ブラウザのページを最新の情報に更新してみてください。
>
>

**[SSL Type] \(SSL の種類)** で、**[Server Name Indication (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ベースの SSL を使用するか IP ベースの SSL を使用するかを選択します。

- **SNI ベースの SSL** - SNI ベースの SSL バインドを複数追加することができます。 このオプションでは、複数の SSL 証明書を使用して、同一の IP アドレス上の複数のドメインを保護できます。 最新のブラウザーのほとんど (Inernet Explorer、Chrome、Firefox、Opera など) が SNI をサポートしています (ブラウザーのサポートに関するより包括的な情報については、「[Server Name Indication](http://wikipedia.org/wiki/Server_Name_Indication)」を参照してください)。
- **IP ベースの SSL** - IP ベースの SSL バインドを 1 つだけ追加することができます。 このオプションでは、SSL 証明書を 1 つだけ使用して、専用のパブリック IP アドレスを保護します。 複数のドメインを保護するには、同じ SSL 証明書を使用してすべてのドメインを保護する必要があります。 これは、SSL バインドの従来のオプションです。

**[Add Binding] \(バインドの追加)** をクリックします。

![SSL 証明書のバインド](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

App Service による証明書のアップロードが完了すると、**[SSL バインド]** セクションにアップロードした証明書が表示されます。

![Web アプリにバインドされた証明書](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>IP SSL の A レコードの再マップ

Web アプリで IP ベースの SSL を使用していない場合、[カスタム ドメインの HTTPS のテスト](#test)に関するセクションにスキップしてください。

既定では、Web アプリは、共有のパブリック IP アドレスを使用します。 IP ベースの SSL で証明書をバインドすると、Web アプリ用の新規の専用 IP アドレスが App Service によって作成されます。

Web アプリに A レコードをマップした場合は、この新規の専用 IP アドレスでドメイン レジストリを更新します。

Web アプリの **[カスタム ドメイン]** ページが、新規の専用 IP アドレスで更新されます。 [この IP アドレスをコピー](app-service-web-tutorial-custom-domain.md#info)して、この新しい IP アドレスに [A レコードを再マップ](app-service-web-tutorial-custom-domain.md#map-an-a-record)します。

<a name="test"></a>

## <a name="test-https"></a>HTTPS のテスト

この時点で残っている作業は、HTTPS がカスタム ドメインで機能するかどうかを確認することだけです。 さまざまなブラウザーで `https://<your.custom.domain>` にアクセスし、Web アプリの要求を処理できることを確認します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Web アプリで証明書検証エラーが返された場合は、自己署名証明書を使用している可能性があります。
>
> そうでない場合は、証明書を PFX ファイルにエクスポートするときに中間証明書を含めなかった可能性があります。

<a name="bkmk_enforce"></a>

## <a name="renew-certificates"></a>証明書の更新

バインディングを削除すると、着信 IP アドレスが変化する場合があります。そのバインディングが IP ベースであっても同様です。 IP ベースのバインディングに既に存在する証明書を更新するときには、このことが特に重要となります。 アプリの IP アドレスに変更が生じないようにするには、次の手順に従います。

1. 新しい証明書をアップロードします。
2. 古い証明書を削除せずに、新しい証明書を目的のカスタム ドメインにバインドします。 これは、古い証明書を削除する代わりに、バインディングを置き換える操作となります。
3. 古い証明書を削除します。 

## <a name="enforce-https"></a>HTTPS の適用

既定では、どなたでも引き続き HTTP を使用して Web アプリにアクセスできます。 すべての HTTP 要求を HTTPS ポートにリダイレクトできます。

Web アプリ ページで、左側のナビゲーションにある **[SSL 設定]** を選択します。 その後、**[HTTPS のみ]** で、**[On]** を選択します。

![HTTPS の適用](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

操作が完了すると、アプリを指定する HTTP URL のいずれかに移動します。 例: 

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-1112"></a>TLS 1.1/1.2 の適用

アプリでは既定で [TLS 1.0](https://wikipedia.org/wiki/Transport_Layer_Security) が有効です。これは、[PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) などの業界標準によって安全であると見なされなくなっています。 より上位の TLS バージョンを適用するには、次の手順に従います。

Web アプリ ページで、左側のナビゲーションにある **[SSL 設定]** を選択します。 次に、**[TLS version]\(TLS バージョン\)** で、必要な最低限の TLS バージョンを選択します。

![TLS 1.1/1.2 の適用](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

操作が完了すると、アプリは下位の TLS バージョンでの接続をすべて拒否します。

## <a name="automate-with-scripts"></a>スクリプトで自動化する

[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/overview) を使用すると、Web アプリの SSL バインドをスクリプトで自動化することができます。

### <a name="azure-cli"></a>Azure CLI

次のコマンドは、エクスポートした PFX ファイルをアップロードし、拇印を取得します。

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

次のコマンドは、直前のコマンドで取得した拇印を使用して、SNI ベースの SSL バインドを追加します。

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

次のコマンドを実行すると、TLS の最低バージョンが強制的に 1.2 になります。

```bash
az webapp config set \
    --name <app_name> \
    --resource-group <resource_group_name>
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

次のコマンドは、エクスポートした PFX ファイルをアップロードし、SNI ベースの SSL バインドを追加します。

```PowerShell
New-AzureRmWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="public-certificates-optional"></a>パブリック証明書 (省略可能)
証明書認証を必要とする外部のサービスにアクセスできるよう、Web アプリには[パブリック証明書](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/)をアップロードすることができます。  アプリにパブリック証明書を読み込んで使用する方法の詳細については、「[Azure App Service のアプリケーション コードに SSL 証明書を使用する](https://docs.microsoft.com/azure/app-service/app-service-web-ssl-cert-load)」を参照してください。  App Service Environment でも、アプリのパブリック証明書をご利用いただけます。 LocalMachine の証明書ストアに証明書を格納する必要がある場合は、App Service Enviroment で Web アプリを使用する必要があります。 詳細については、[Web アプリに合わせてパブリック証明書を構成する方法](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer)に関するページをご覧ください。

![パブリック証明書のアップロード](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * アプリの価格レベルをアップグレードする
> * カスタム証明書を App Service にバインドする
> * 証明書の更新
> * HTTPS の適用
> * TLS 1.1/1.2 の適用
> * スクリプトを使って TLS 管理を自動化する

次のチュートリアルに進み、Azure Content Delivery Network の使用方法を学習してください。

> [!div class="nextstepaction"]
> [Azure App Service に Content Delivery Network (CDN) を追加する](../cdn/cdn-add-to-web-app.md)

詳細については、「[Use an SSL certificate in your application code in Azure App Service (Azure App Service の SSL 証明書を購入して構成する)](app-service-web-ssl-cert-load.md)」をご覧ください。
