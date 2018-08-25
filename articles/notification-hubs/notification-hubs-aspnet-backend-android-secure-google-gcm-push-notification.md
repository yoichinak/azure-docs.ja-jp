---
title: Azure Notification Hubs でのセキュリティ保護されたプッシュ通知の送信
description: セキュリティで保護されたプッシュ通知を Azure から Android アプリに送信する方法について説明します。 コード サンプルは Java と C# で記述されています。
documentationcenter: android
keywords: プッシュ通知,プッシュ通知,プッシュ メッセージ,Android プッシュ通知
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 04/25/2018
ms.author: dimazaid
ms.openlocfilehash: 58f6967c59a5060baa10ff83752b9c6ed08226cb
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38698030"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Azure Notification Hubs でのセキュリティ保護されたプッシュ通知の送信
> [!div class="op_single_selector"]
> * [Windows ユニバーサル](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>概要
> [!IMPORTANT]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started)を参照してください。
> 
> 

Microsoft Azure でプッシュ通知がサポートされたことで、マルチプラットフォームに対応し、簡単に使用できる、スケールアウトされたプッシュ メッセージ インフラストラクチャを利用できるようになりました。これにより、モバイル プラットフォーム向けアプリケーション (コンシューマー用途およびエンタープライズ用途) にプッシュ通知機能を実装する作業が大幅に簡略化されます。

規制やセキュリティの制約により、アプリケーションでは、標準のプッシュ通知インフラストラクチャからは転送できないものを通知に含める必要がある場合があります。 このチュートリアルでは、クライアントの Android デバイスとアプリ バックエンドの間の安全で認証された接続を通して機密情報を送信することによって、同じエクスペリエンスを実現する方法について説明します。

大まかには、フローは次のようになります。

1. アプリケーションのバックエンド:
   * バックエンド データベースに安全なペイロードを格納します。
   * この通知の ID を Android デバイスに送信します (安全でない情報は送信しません)。
2. 通知を受け取ったときのデバイスのアプリケーション:
   * Android デバイスは、安全なペイロードを要求するバックエンドにアクセスします。
   * アプリケーションはデバイスに通知としてペイロードを表示できます。

前のフロー (およびこのチュートリアル) では、ユーザーがログインした後、デバイスが認証トークンをローカル ストレージに格納することを前提にしていることに注意する必要があります。 このアプローチにより、デバイスはこのトークンを使用して通知のセキュリティ保護されたペイロードを取得できるため、シームレスなエクスペリエンスが保証されます。 アプリケーションがデバイスに認証トークンを格納しない、またはそれらのトークンが期限切れの場合、デバイスのアプリは、プッシュ通知を受け取ったときにアプリの起動を促す一般的な通知を表示する必要があります。 その後、アプリケーションはユーザーを認証し、通知ペイロードを表示します。

このチュートリアルでは、セキュリティ保護されたプッシュ通知を送信する方法について説明します。 このチュートリアルは、 [ユーザーへの通知](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) チュートリアルに基づいて記述されているため、先にそのチュートリアルでの手順を完了してください (まだ完了していない場合)。

> [!NOTE]
> このチュートリアルでは、「 [Notification Hubs の使用 (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md)」での説明に従って通知が作成され、構成されていると想定しています。
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Android プロジェクトを変更する
これで、プッシュ通知の *ID* のみを送信するようにアプリ バックエンドが変更されたので、その通知を処理し、バックエンドをコールバックして、表示されるセキュリティ保護されたメッセージを取得するように Android アプリを変更する必要があります。
そのためには、Android アプリケーションが、プッシュ通知を受け取ったときにバックエンドでそれ自体を認証する方法を知っている必要があります。

ここでは、認証ヘッダー値をアプリの共有設定に保存するために *login* フローを変更します。 類似のメカニズムを使用すると、ユーザー資格情報を要求せずにアプリが使用する必要がある任意の認証トークン (OAuth トークンなど) を格納できます。

1. Android アプリケーション プロジェクトで、 **MainActivity** クラスの先頭に次の定数を追加します。
   
        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
2. **MainActivity** クラスでも、`getAuthorizationHeader()` メソッドを更新して、次のコードが含まれるようにします。
   
        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
   
            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();
   
            return basicAuthHeader;
        }
3. **MainActivity** ファイルの先頭に次の `import` ステートメントを追加します。
   
        import android.content.SharedPreferences;

次に、通知が受信されたときに呼び出されるハンドラーを変更します。

1. **MyHandler** クラスで、次のコードを含むように `OnReceive()` メソッドを変更します。
   
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }
2. 次に、`retrieveNotification()` メソッドを追加して、プレースホルダー `{back-end endpoint}` をバックエンドのデプロイ時に取得したバックエンド エンドポイントに置き換えます。
   
        private void retrieveNotification(final String secureMessageId) {
            SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);
   
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                        request.addHeader("Authorization", "Basic "+authorizationHeader);
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error retrieving secure notification");
                        }
                        String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                        JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                        sendNotification(secureNotification.getString("Payload"));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }

このメソッドは、共有設定に格納された資格情報によってアプリケーション バックエンドを呼び出して通知コンテンツを取得し、正常な通知として表示します。 この通知は、アプリケーションのユーザーにとっては、他のプッシュ通知とまったく同じように見えます。

認証ヘッダーのプロパティが見つからない場合や、バックエンドによって拒否された場合に対処することをお勧めします。 これらの場合への具体的な対処は、ほぼターゲット ユーザーのエクスペリエンスに依存します。 1 つのオプションとしては、ユーザーに通知と共に認証を求める一般的なプロンプトを表示して、実際の通知を取得する方法があります。

## <a name="run-the-application"></a>アプリケーションの実行
アプリケーションを実行するには、次のアクションを実行します。

1. **AppBackend** が Azure にデプロイされていることを確認します。 Visual Studio を使用している場合は、 **AppBackend** Web API アプリケーションを実行します。 ASP.NET Web ページが表示されます。
2. Eclipse で、物理的な Android デバイスまたはエミュレーターでアプリケーションを実行します。
3. Android アプリケーションの UI で、ユーザー名とパスワードを入力します。 文字列は任意ですが、値は同じである必要があります。
4. Android アプリケーションの UI で、 **[ログイン]** をクリックします。 次に、 **[プッシュを送信する]** をクリックします。

