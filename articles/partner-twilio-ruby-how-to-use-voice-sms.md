---
title: 音声および SMS に Twilio を使用する方法 (Ruby) | Microsoft Docs
description: Azure で Twilio API サービスを使用して通話や SMS メッセージの送信を行う方法について学習します。 コード サンプルは Ruby で記述されています。
services: ''
documentationcenter: ruby
author: devinrader
manager: twilio
editor: ''
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 41b5383dd319f2cb6fad4316e963f86dd7a4bc61
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036610"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Ruby で音声および SMS 機能に Twilio を使用する方法
このガイドでは、Azure の Twilio API サービスを使用して一般的なプログラミング タスクを実行する方法を紹介します。 電話の発信と Short Message Service (SMS) メッセージの送信の各シナリオについて説明します。 Twilio の詳細、およびアプリケーションで音声と SMS を使用する方法については、「 [次のステップ](#NextSteps) 」を参照してください。

## <a id="WhatIs"></a>Twilio とは
Twilio は、既存の Web 言語およびスキルを使用して音声および SMS アプリケーションの作成を可能にするテレフォニー Web サービス API です。 Twilio は、サードパーティ製のサービスです (Azure の機能および Microsoft 製品ではありません)。

**Twilio Voice** を使用すると、アプリケーションで音声通話の発着信処理を行うことができます。 **Twilio SMS** を使用すると、アプリケーションで SMS メッセージの送受信を行うことができます。 **Twilio Client** を使用すると、アプリケーションに (モバイル接続を含む) 既存のインターネット接続を使用した音声通信を組み込むことができます。

## 
  <a id="Pricing">
  </a>Twilio の料金および特別プラン
Twilio の料金については、[Twilio の料金に関するページ][twilio_pricing]でご確認ください。 Azure ユーザーには、[特別プラン][special_offer]として、1,000 件のテキストまたは 1,000 分の着信通話相当の無料クレジットが用意されています。 この特別プランにサインアップする方法と詳細については、[http://ahoy.twilio.com/azure][special_offer] を参照してください。  

## <a id="Concepts"></a>概念
Twilio API は、アプリケーションに音声および SMS 機能を提供する REST ベースの API です。 クライアント ライブラリはさまざまな言語で用意されています。言語の一覧については、[Twilio API ライブラリ][twilio_libraries]に関するページをご覧ください。

### <a id="TwiML"></a>TwiML
TwiML は、通話または SMS をどのように処理するかを Twilio に通知する XML ベースの命令のセットです。

たとえば、次の TwiML は、テキスト **Hello World** を音声に変換します。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

すべての TwiML ドキュメントには、 `<Response>` がルート要素として存在します。 ここから、Twilio 動詞を使用してアプリケーションの動作を定義します。

### <a id="Verbs"></a>TwiML 動詞
Twilio 動詞は、何を **実行する**かを Twilio に通知する XML タグです。 たとえば、**&lt;Say&gt;** 動詞は、メッセージを音声で返すことを Twilio に指示します。 

Twilio 動詞の一覧を次に示します。

* **&lt;Dial&gt;**: 呼び出し元を別の電話に接続します。
* **&lt;Gather&gt;**: 電話キーパッドに入力された数字を収集します。
* **&lt;Hangup&gt;**: 通話を終了します。
* **&lt;Play&gt;**: 音声ファイルを再生します。
* **&lt;Pause&gt;**: 何も行わずに指定された秒数待機します。
* **&lt;Record&gt;**: 呼び出し元の声を録音し、声が録音されたファイルの URL を返します。
* **&lt;Redirect&gt;**: 通話または SMS の制御を別の URL の TwiML に転送します。
* **&lt;Reject&gt;**: Twilio 番号への着信通話を拒否します。課金はされません。
* **&lt;Say&gt;**: テキストを音声に変換して返します。
* **&lt;Sms&gt;**: SMS メッセージを送信します。

Twilio の動詞と属性、TwiML の詳細については、[TwiML][twiml] に関するページをご覧ください。 Twilio API の詳細については、[Twilio API][twilio_api] に関するページをご覧ください。

## <a id="CreateAccount"></a>Twilio アカウントを作成する
Twilio アカウントを取得する準備ができたら、[Twilio のサインアップ ページ][try_twilio]でサインアップします。 無料アカウントで始め、後でアカウントをアップグレードすることができます。

Twilio アカウントにサインアップすると、アプリケーションで使用できる無料の電話番号が与えられます。 さらに、アカウント SID と認証トークンも受け取ります。 Twilio API を呼び出すには、この両方が必要になります。 自分のアカウントが不正にアクセスされないように、認証トークンを安全に保管してください。 アカウント SID と認証トークンは、[Twilio アカウント ページ][twilio_account]の **[ACCOUNT SID]** フィールドと **[AUTH TOKEN]** フィールドでそれぞれ確認できます。

### <a id="VerifyPhoneNumbers"></a>電話番号を確認する
Twilio から与えられた番号の他に、自分で管理している番号 (携帯電話や自宅の電話番号) を確認してアプリケーションで使用することもできます。 

電話番号を確認する方法の詳細については、「[Manage Numbers (番号の管理)][verify_phone]」を参照してください。

## <a id="create_app"></a>Ruby アプリケーションを作成します。
Twilio サービスを使用し、Azure で動作している Ruby アプリケーションと、Twilio サービスを使用するその他の Ruby アプリケーションに違いはありません。 Twilio サービスは REST ベースであり、Ruby から複数の方法で呼び出すことができますが、この記事では、Twilio サービスの呼び出しに [Ruby 用 Twilio ヘルパー ライブラリ][twilio_ruby]を使用する方法について重点的に説明します。

まず、新しい Ruby Web アプリケーションのホストとして動作する[新しい Azure Linux VM をセットアップ][azure_vm_setup]します。 Rails アプリケーションの作成に関する手順は無視し、VM のセットアップだけを行ってください。 作成するエンドポイントは、外部ポートを 80、内部ポートを 5000 としてください。

以下の例では、Ruby 用の非常にシンプルな Web フレームワークである [Sinatra][sinatra] を使用しています。 もちろん、Ruby on Rails などの他の Web フレームワークで Twilio ヘルパー ライブラリを使用することもできます。

新しい VM に SSH で接続し、新しいアプリケーション用のディレクトリを作成します。 そのディレクトリの中に Gemfile というファイルを作成し、次のコードをファイルにコピーします。

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

コマンド ラインで、 `bundle install`を実行します。 これにより、上に示した依存関係がインストールされます。 次に、 `web.rb`というファイルを作成します。 これが Web アプリケーションのコードを記述する場所になります。 このファイルに次のコードを貼り付けます。

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

この時点で、 `ruby web.rb -p 5000`コマンドを実行できます。 これにより、単純な Web サーバーがポート 5000 で動き始めます。 Azure VM 用に設定した URL にブラウザーでアクセスすると、このアプリケーションを参照できます。 ブラウザーで Web アプリケーションにアクセスできたら、Twilio アプリケーションの作成を始める準備ができています。

## <a id="configure_app"></a>Twilio を使用するようにアプリケーションを構成する
Twilio ライブラリを使用するように Web アプリケーションを構成するには、 `Gemfile` を更新して次の行を追加します。

    gem 'twilio-ruby'

コマンド ラインで、 `bundle install`を実行します。 `web.rb` を開き、先頭に次の行を追加します。

    require 'twilio-ruby'

これで、Ruby 用の Twilio ヘルパー ライブラリを Web アプリケーションで使用するための設定が完了しました。

## <a id="howto_make_call"></a>方法: 発信通話する
次のコードでは、発信通話を行う方法を示します。 ここで重要となるのは、Ruby 用の Twilio ヘルパー ライブラリを使用した REST API の呼び出しと、TwiML のレンダリングです。 コードを実行する前に、**From** および **To** の電話番号の値を置き換えて、Twilio アカウントの **From** の電話番号を確認します。

次の関数を `web.md`に追加します。

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";

    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);

      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

ブラウザーで `http://yourdomain.cloudapp.net/make_call` を開くと、通話を発信する Twilio API の呼び出しがトリガーされます。 `client.account.calls.create` の最初の 2 つのパラメーターは、文字どおり、呼び出し元 (`from`) と呼び出し先 (`to`) の番号です。 

3 つ目のパラメーター (`url`) は、通話がつながったときの動作に関する指示を受け取るために Twilio が要求する URL です。 この例では、単純な TwiML ドキュメントを返す URL (`http://yourdomain.cloudapp.net`) を設定し、テキストを音声に変換する `<Say>` 動詞を使用して、通話の受信者に "Hello Monkey" という音声を届けます。

## <a id="howto_recieve_sms"></a>方法: SMS メッセージを受信する
前の例では、 **発信** 通話を開始しました。 今度は、Twilio へのサインアップ時に受け取った電話番号を使用して、 **受信** SMS メッセージを処理します。

まず、[Twilio ダッシュボード][twilio_account]にログインします。 上部のナビゲーションで [Numbers] をクリックし、自分の Twilio 番号をクリックします。 構成できる URL が 2 つ表示されます。 Voice Request URL と、SMS Request URL です。 これらは、該当する番号に電話がかかってきたとき、または SMS メッセージが送信されたときに Twilio から呼び出される URL です。 これらの URL は "Web フック" とも呼ばれます。

今回は受信 SMS メッセージを処理するので、URL を `http://yourdomain.cloudapp.net/sms_url`に更新します。 ページの下部にある [Save Changes] をクリックします。 `web.rb` に戻り、アプリケーションをプログラミングして次の処理を追加します。

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

変更を加えた後は、必ず Web アプリケーションを再起動してください。 次に、電話を使用して Twilio 番号に SMS を送信します。 "Hey, thanks for the ping! Twilio and Azure rock!" という SMS 応答がすぐに返されます。

## <a id="additional_services"></a>方法: その他の Twilio サービスを使用する
ここに示す例以外にも、Twilio が提供する Web ベースの API を使用して、Azure アプリケーションからその他の Twilio 機能を利用することができます。 詳細については、[Twilio API に関するドキュメント][twilio_api_documentation]を参照してください。

### <a id="NextSteps"></a>次のステップ
これで、Twilio サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先をご覧ください。

* [Twilio に関するセキュリティ ガイドラインのページ][twilio_security_guidelines]
* [Twilio に関する方法とコード例のページ][twilio_howtos]
* [Twilio のクイック スタート チュートリアルのページ][twilio_quickstarts] 
* [GitHub 上の Twilio に関するページ][twilio_on_github]
* [Twilio に関するサポートへの連絡のページ][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: https://docs.microsoft.com/azure/virtual-machines/linux/classic/ruby-rails-web-app
