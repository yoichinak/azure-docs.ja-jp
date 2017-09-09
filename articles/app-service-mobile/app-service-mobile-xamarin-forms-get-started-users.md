---
title: "Xamarin Forms アプリでの Mobile Apps の認証の使用 | Microsoft Docs"
description: "Mobile Apps を使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Xamarin Forms アプリのユーザーを認証する方法について説明します。"
services: app-service\mobile
documentationcenter: xamarin
author: panarasi
manager: syntaxc4
editor: 
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 08/07/2017
ms.author: panarasi
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 9e14e95793bcc81ad46783fd50ba223eec4ea360
ms.contentlocale: ja-jp
ms.lasthandoff: 08/08/2017

---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Xamarin Forms アプリに認証を追加する
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Overview
このトピックでは、クライアント アプリケーションから App Service モバイル アプリのユーザーを認証する方法について説明します。 このチュートリアルでは、App Service でサポートされている ID プロバイダーを使用して、Xamarin Forms クイック スタート プロジェクトに認証を追加します。 モバイル アプリによって正常に認証、承認されると、ユーザー ID 値が表示され、制限付きのテーブル データにアクセスできます。

## <a name="prerequisites"></a>前提条件
このチュートリアルで最善の結果が得られるように、最初にチュートリアル「[Xamarin.Forms アプリの作成][1]」を完了しておくことをお勧めします。 このチュートリアルを完了すると、マルチプラットフォーム TodoList アプリである Xamarin Forms プロジェクトを作成できます。

ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、認証拡張機能パッケージをプロジェクトに追加する必要があります。 サーバーの拡張機能パッケージの詳細については、「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作][2]」を参照してください。

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>アプリケーションを認証に登録し、App Services を構成する
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>許可されている外部リダイレクト URL にアプリを追加する

認証をセキュリティで保護するには、アプリ用の新しい URL スキームの定義が必要になります。 これによって、認証プロセスが完了すると認証システムからアプリにリダイレクトできます。 このチュートリアル全体を通して、URL スキーム _appname_ を使用します。 ただし、選択したあらゆる URL スキームを使用できます。 URL スキームは、モバイル アプリに対して一意である必要があります。 サーバー側でリダイレクトを有効にするには、以下の手順に従います。

1. [Azure Portal] で、App Service を選択します。

2. **[認証/承認]** メニュー オプションをクリックします。

3. **[Allowed External Redirect URLs (許可されている外部リダイレクト URL)]** に `url_scheme_of_your_app://easyauth.callback` を入力します。  この文字列の **url_scheme_of_your_app** は、モバイル アプリケーションの URL スキームです。  プロトコルの通常の URL 仕様 (文字と数字のみを使用し、文字で始まる) に従う必要があります。  数か所で URL スキームに合わせてモバイル アプリケーション コードを調整する必要があるため、選択した文字列をメモしておく必要があります。

4. **[OK]**をクリックします。

5. [ **Save**] をクリックします。

## <a name="restrict-permissions-to-authenticated-users"></a>アクセス許可を、認証されたユーザーだけに制限する
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>ポータブル クラス ライブラリに認証を追加する
Mobile Apps では、App Service 認証を使用したユーザーのサインインに、[MobileServiceClient][4] の [LoginAsync][3] 拡張メソッドを使います。 このサンプルでは、アプリにプロバイダーのサインイン インターフェイスが表示される、サーバー側管理認証フローを使用します。 詳細については、「[サーバー側管理認証][5]」を参照してください。 運用アプリのユーザー エクスペリエンスを向上させるためには、代わりに[クライアント側管理認証][6]を使用することを検討してください。

Xamarin Forms プロジェクトで認証するには、アプリのポータブル クラス ライブラリに **IAuthenticate** インターフェイスを定義します。 次に、ポータブル クラス ライブラリで定義されているユーザー インターフェイスに **[サインイン]** ボタンを追加します。このボタンは、認証を開始するためにクリックします。 認証が成功すると、モバイル アプリ バックエンドからデータが読み込まれます。

アプリでサポートされているプラットフォームごとに **IAuthenticate** インターフェイスを実装します。

1. Visual Studio または Xamarin Studio で、ポータブル クラス ライブラリ プロジェクトである **Portable** というプロジェクトから App.cs を開きます。その後で、次の `using` ステートメントを追加します。

        using System.Threading.Tasks;
2. App.cs に、次の `IAuthenticate` インターフェイス定義を、`App` クラス定義の直前に追加します。

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. インターフェイスをプラットフォーム固有の実装で初期化するように、次の静的メンバーを **App** クラスに追加します。

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. ポータブル クラス ライブラリ プロジェクトから TodoList.xaml を開き、既存のボタンの後ろに、 **buttonsPanel** レイアウト要素の次の *Button* 要素を追加します。

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    このボタンは、モバイル アプリ バックエンドによるサーバー側管理認証をトリガーします。
5. ポータブル クラス ライブラリ プロジェクトから TodoList.xaml.cs を開き、 `TodoList` クラスに次のフィールドを追加します。

        // Track whether the user has authenticated.
        bool authenticated = false;
6. **OnAppearing** メソッドを次のコードに置き換えます。

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    このコードにより、認証の完了後にのみ、データがサービスによって更新されるようになります。
7. **Clicked** イベントの次のハンドラーを **TodoList** クラスに追加します。

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. 変更を保存し、ポータブル クラス ライブラリ プロジェクトをリビルドしてエラーがないことを確認します。

## <a name="add-authentication-to-the-android-app"></a>Android アプリに認証を追加する
このセクションでは、Android アプリ プロジェクト内に **IAuthenticate** インターフェイスを実装する方法について説明します。 Android デバイスをサポートしない場合、このセクションはスキップしてください。

1. Visual Studio または Xamarin Studio で、**droid** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。
2. F5 キーを押してデバッガーでプロジェクトを開始し、アプリの開始後に、状態コード 401 のハンドルされない例外 (許可されていません) が発生することを確認します。 401 コードは、バックエンドへのアクセスが承認済みのユーザーのみに制限されているために生成されます。
3. Android プロジェクトの MainActivity.cs を開き、次の `using` ステートメントを追加します。

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. 次のように、**MainActivity** クラスを更新して **IAuthenticate** インターフェイスを実装します。

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. 次のように、**MobileServiceUser** フィールドのほか、**IAuthenticate** インターフェイスに必要な **Authenticate** メソッドを追加して、**MainActivity** クラスを更新します。

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this, 
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }

    Facebook 以外の ID プロバイダーを使用している場合、[MobileServiceAuthenticationProvider][7] には別の値を選択してください。

6. AndroidManifest.xml の <application> ノード内に以下のコードを追加します。

```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
```

1. `LoadApplication()` の呼び出しの前、次のコードを **MainActivity** クラスの **OnCreate** メソッドに追加します。

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    このコードにより、アプリの読み込み前に Authenticator が初期化されるようになります。
2. アプリをリビルドして実行します。その後で、選択した認証プロバイダーを使用してサインインし、認証されたユーザーとしてデータにアクセスできることを確認します。

## <a name="add-authentication-to-the-ios-app"></a>iOS アプリに認証を追加する
このセクションでは、iOS アプリ プロジェクト内に **IAuthenticate** インターフェイスを実装する方法について説明します。 iOS デバイスをサポートしない場合、このセクションはスキップしてください。

1. Visual Studio または Xamarin Studio で、**iOS** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。
2. F5 キーを押してデバッガーでプロジェクトを開始し、アプリの開始後に、状態コード 401 のハンドルされない例外 (許可されていません) が発生することを確認します。 401 応答は、バックエンドへのアクセスが承認済みのユーザーのみに制限されているために生成されます。
3. iOS プロジェクトの AppDelegate.cs を開き、次の `using` ステートメントを追加します。

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. 次のように、**AppDelegate** クラスを更新して **IAuthenticate** インターフェイスを実装します。

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. 次のように、**MobileServiceUser** フィールドのほか、**IAuthenticate** インターフェイスに必要な **Authenticate** メソッドを追加して、**AppDelegate** クラスを更新します。

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();

            return success;
        }

    Facebook 以外の ID プロバイダーを使用している場合、[MobileServiceAuthenticationProvider] には別の値を選択してください。

6. OpenUrl(UIApplication app, NSUrl url, NSDictionary options) メソッド オーバーロードを追加して、AppDelegate クラスを更新します。

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }

6. `LoadApplication()` の呼び出しの前、次のコード行を **FinishedLaunching** メソッドに追加します。

        App.Init(this);

    このコードにより、アプリの読み込み前に Authenticator が初期化されるようになります。

6. **{url_scheme_of_your_app}** を Info.plist の URL スキームに追加します。

7. アプリをリビルドして実行します。その後で、選択した認証プロバイダーを使用してサインインし、認証されたユーザーとしてデータにアクセスできることを確認します。

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Windows 10 (Phone を含む) アプリ プロジェクトに認証を追加する
このセクションでは、Windows 10 アプリ プロジェクト内に **IAuthenticate** インターフェイスを実装する方法について説明します。 同じ手順をユニバーサル Windows プラットフォーム (UWP) プロジェクトにも適用できますが、**UWP** プロジェクトを使用します (相違点が注記されています)。 Windows デバイスをサポートしない場合、このセクションはスキップしてください。

1. Visual Studio で、**UWP** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。
2. F5 キーを押してデバッガーでプロジェクトを開始し、アプリの開始後に、状態コード 401 のハンドルされない例外 (許可されていません) が発生することを確認します。 401 応答は、バックエンドへのアクセスが承認済みのユーザーのみに制限されているために発生します。
3. Windows アプリ プロジェクトの MainPage.xaml.cs を開き、次の `using` ステートメントを追加します。

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    `<your_Portable_Class_Library_namespace>` を、ポータブル クラス ライブラリの名前空間に置き換えます。
4. 次のように、**MainPage** クラスを更新して **IAuthenticate** インターフェイスを実装します。

        public sealed partial class MainPage : IAuthenticate
5. 次のように、**MobileServiceUser** フィールドのほか、**IAuthenticate** インターフェイスに必要な **Authenticate** メソッドを追加して、**MainPage** クラスを更新します。

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }

            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }

    Facebook 以外の ID プロバイダーを使用している場合、[MobileServiceAuthenticationProvider] には別の値を選択してください。

1. `LoadApplication()` の呼び出しの前、次のコード行を **MainPage** クラスのコンストラクター内に追加します。

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    `<your_Portable_Class_Library_namespace>` を、ポータブル クラス ライブラリの名前空間に置き換えます。

3. **UWP** を使用している場合は、以下の **OnActivated** メソッドのオーバーライドを **App** クラスに追加します。

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(protocolArgs.Uri);
            }

       }

   メソッドのオーバーライドが既に存在する場合は、前のスニペットから条件付きコードを追加してください。  このコードは、ユニバーサル Windows プロジェクトでは必要ありません。

3. **{url_scheme_of_your_app}** を Package.appxmanifest に追加します。 

4. アプリをリビルドして実行します。その後で、選択した認証プロバイダーを使用してサインインし、認証されたユーザーとしてデータにアクセスできることを確認します。

## <a name="next-steps"></a>次のステップ
これで基本的な認証チュートリアルは完了しましたので、引き続き次のいずれかのチュートリアルのご利用を検討してください。

* [アプリへのプッシュ通知の追加](app-service-mobile-xamarin-forms-get-started-push.md)

  アプリにプッシュ通知のサポートを追加して、Azure Notification Hubs を使ってプッシュ通知を送信するようにモバイル アプリ バックエンドを構成する方法について説明します。
* [アプリのオフライン同期の有効化](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  モバイル アプリ バックエンドを使用してオフライン サポートをアプリに追加する方法について説明します。 オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx

