---
title: Azure Notification Hubs を使用して Xamarin.iOS アプリにプッシュ通知を送信する | Microsoft Docs
description: このチュートリアルでは、Azure Notification Hubs を使用して Xamarin.iOS アプリケーションにプッシュ通知を送信する方法について学習します。
services: notification-hubs
keywords: ios push notifications、プッシュ メッセージ、プッシュ通知、プッシュ メッセージ
documentationcenter: xamarin
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: ac8e5240f4544ccbb7256acbc6e70f00eff15092
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "41920085"
---
# <a name="tutorial-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>チュートリアル: Azure Notification Hubs を使用して Xamarin.iOS アプリにプッシュ通知を送信する
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Azure Notification Hubs を使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。 [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) を使用してプッシュ通知を受信する空の Xamarin.iOS アプリを作成します。 

完了すると、通知ハブを使用して、アプリを実行しているすべてのデバイスにプッシュ通知をブロードキャストできます。 完成したコードは、[NotificationHubs アプリケーション][GitHub] サンプルで参照できます。

このチュートリアルでは、次のタスクを実行するコードを作成および更新します。 

> [!div class="checklist"]
> * 証明書の署名要求ファイルを生成する
> * アプリケーションをプッシュ通知に登録する
> * アプリケーションのプロビジョニング プロファイルを作成する
> * iOS プッシュ通知向けに通知ハブを構成する
> * テスト プッシュ通知を送信する

## <a name="prerequisites"></a>前提条件

- **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- 最新バージョンの [XCode][Install Xcode]
- iOS 10 以降のバージョンに対応するデバイス
- [Apple Developer Program](https://developer.apple.com/programs/) メンバーシップ
- [Visual Studio for Mac]
  
  > [!NOTE]
  > iOS Push Notifications の構成要件に従って、サンプル アプリケーションのデプロイとテストは、シミュレーターではなく、物理的な iOS デバイス (iPhone または iPad) で行う必要があります。

このチュートリアルを完了することは、Xamarin.iOS アプリの他のすべての Notification Hubs チュートリアルの前提条件です。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>iOS プッシュ通知向けに通知ハブを構成する
このセクションでは、以前に作成した **.p12** プッシュ証明書を使用して、新しい通知ハブを作成し、APNS での認証を構成する手順について説明します。 既に作成した通知ハブを使用する場合は、手順 5. に進んでください。

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-ios-settings-for-the-notification-hub"></a>iOS の設定を通知ハブ用に構成する
1. **[通知設定]** グループで、**[Apple (APNS)]** を選択します。 
2. **[証明書]** を選択し、**ファイル** アイコンをクリックして、前にエクスポートした **.p12** ファイルを選択します。 
3. 証明書の**パスワード**を指定します。 
4. **[サンドボックス]** モードを選択します。 **[Production] (運用)** モードは、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合にのみ使用します。

    ![Azure Portal で APNS を構成する][6]

    ![Azure Portal で APNS 証明書を構成する][7]

これで、通知ハブが APNS と連動するように構成されました。接続文字列にアプリケーションを登録し、プッシュ通知を送信できます。

## <a name="connect-your-app-to-the-notification-hub"></a>通知ハブにアプリケーションを接続する
#### <a name="create-a-new-project"></a>新しいプロジェクトを作成する
1. Visual Studio で、新しい iOS プロジェクトを作成し、**[単一ビュー アプリ]** テンプレートを選択し、**[次へ]** をクリックします。
   
     ![Visual Studio - アプリケーションの種類を選択する][31]

2. アプリケーション名と組織の識別子を入力し、**[次へ]**、**[作成]** の順にクリックします。

3. ソリューション ビューから *[Info.plist]* をダブルクリックし、**[ID]** で、バンドル識別子がプロビジョニング プロファイルを作成するときに使用したものに一致することを確認します。 **[署名]** で、**[チーム]** に Developer アカウントを選択し、[Automatically manage signing]\(署名を自動的に管理する\) をオンにし、署名証明書とプロビジョニング プロファイルが自動的に選択されることを確認します。

    ![Visual Studio - iOS アプリの構成][32]

4. [ソリューション] ビューで、*Entitlements.plist* をダブル クリックして、[プッシュ通知を有効にする] がオンになっていることを確認します。

    ![Visual Studio - iOS エンタイトルメントの構成][33]

5. Azure Messaging パッケージを追加します。 ソリューション ビューでプロジェクトを右クリックし、**[追加]** > **[NuGet パッケージの追加]** を選択します。 **Xamarin.Azure.NotificationHubs.iOS** を検索し、パッケージをプロジェクトに追加します。

6. 新しいファイルをクラスに追加し、**Constants.cs** と名前を付け、次の変数を追加し、文字列リテラルのプレースホルダーを*ハブ名*とメモしておいた *DefaultListenSharedAccessSignature* に置き換えます。
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ListenConnectionString = "<Azure connection string>";
        public const string NotificationHubName = "<Azure hub path>";
    ```

7. **AppDelegate.cs**で、次の using ステートメントを追加します。
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

8. **SBNotificationHub**のインスタンスを宣言します。
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

9.  **AppDelegate.cs** で、次のコードに一致するように **FinishedLaunching()** を更新します。
  
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
            {
                UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Sound,
                                                                      (granted, error) =>
                {
                    if (granted)
                        InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
                });
            } else if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
    ```

10. **AppDelegate.cs** で **RegisteredForRemoteNotifications()** メソッドをオーバーライドします。
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ListenConnectionString, Constants.NotificationHubName);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
    ```

11. **AppDelegate.cs** で **ReceivedRemoteNotification()** メソッドをオーバーライドします。
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

12. **AppDelegate.cs** で次の **ProcessNotification()** メソッドを作成します。
   
    ```csharp
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
    ```
   > [!NOTE]
   > ネットワーク接続がないなどの状況に対処するために、 **FailedToRegisterForRemoteNotifications()** をオーバーライドすることを選択できます。 これは、ユーザーがオフラインモード (Airplane など) でアプリケーションを起動し、アプリケーション固有のプッシュメッセージングシナリオを処理する場合に特に重要です。
  

13. デバイスでアプリケーションを実行します。

## <a name="send-test-push-notifications"></a>テスト プッシュ通知を送信する
アプリの通知の受信をテストするには、[Azure Portal] の *[テスト送信]* オプションを使用します。 これは、デバイスにテスト プッシュ通知を送信します。

![Azure Portal - テスト送信][30]

プッシュ通知は通常、互換性のあるライブラリを使用して Mobile Apps などのバックエンド サービスや ASP.NET に送信されます。 ライブラリがバックエンドに使用できない場合は、REST API を直接使用して通知メッセージを送信することもできます。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、バックエンドに登録されているすべての iOS デバイスにブロードキャスト通知を送信しました。 特定の iOS デバイスにプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
>[特定のデバイスにプッシュ通知を送信する](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio for Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure Portal]: https://portal.azure.com
