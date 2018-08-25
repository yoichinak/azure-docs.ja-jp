---
title: Azure Notification Hubs を使用して iOS デバイスにローカライズしたプッシュ通知を送信する | Microsoft Docs
description: Azure Notification Hubs を使用して iOS デバイスにローカライズしたプッシュ通知を送信する方法について説明します。
services: notification-hubs
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 9301291381450d20b387db42fbfc715988b6a149
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "42141145"
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>チュートリアル: Azure Notification Hubs を使用して iOS デバイスにローカライズしたプッシュ通知を送信する 
> [!div class="op_single_selector"]
> * [Windows ストア C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 

このチュートリアルでは、Azure Notification Hubs の[テンプレート](notification-hubs-templates-cross-platform-push-messages.md)機能を使用して、言語およびデバイスごとにローカライズされたニュース速報通知をブロードキャストする方法を示します。 このチュートリアルでは、「[Notification Hubs を使用したニュース速報の送信]」で作成された iOS アプリから始めます。 完了すると、関心のあるカテゴリに登録したり、通知を受信する言語を指定したり、選択されたカテゴリのその言語のプッシュ通知のみを受信したりできます。

このシナリオは次の 2 つに分けられます。

* iOS アプリケーションで、クライアント デバイスが言語を指定し、さまざまなニュース速報カテゴリを購読できるようになります。
* バックエンドは、Azure Notification Hubs の**タグ**および**テンプレート**機能を使用して通知をブロードキャストします。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * アプリのユーザー インターフェイスを更新する
> * iOS アプリを構築する
> * .NET コンソール アプリからローカライズしたテンプレート通知を送信する
> * デバイスからローカライズしたテンプレート通知を送信する


## <a name="overview"></a>概要
「[Notification Hubs を使用したニュース速報の送信]」では、**タグ**を使用してさまざまなニュース カテゴリの通知にサブスクライブするアプリを構築しました。 しかし、多くのアプリケーションは複数の市場をターゲットとしており、ローカライズが必要です。 つまり、通知自体の内容をローカライズし、適切な一連のデバイスに配信する必要があります。 このチュートリアルでは、Notification Hubs の**テンプレート**機能を使用して、ローカライズしたニュース速報通知を簡単に配信する方法を示します。

> [!NOTE]
> ローカライズした通知を送信する方法の 1 つに、各タグの複数のバージョンを作成する方法があります。 たとえば、英語、フランス語、および標準中国語をサポートするには、世界のニュースに関して "world_en"、"world_fr"、"world_ch" の 3 種類のタグが必要になります。 次に、これらの各タグに世界のニュースのローカライズ版を送信する必要があります。 このトピックでは、テンプレートを使用してタグの急増を回避したり、複数のメッセージを送信しなくても済むようにしたりします。

大まかに言えば、テンプレートとは、特定のデバイスが通知をどのように受信するかを特定するための手段です。 テンプレートは、アプリケーション バックエンドにより送信されるメッセージの一部となっているプロパティを参照することで、正確なペイロードを指定します。 このケースでは、サポートされるすべての言語を含む、ロケールにとらわれないメッセージを送信します。

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

次に、各デバイスが確実に、適切なプロパティを参照するテンプレートに登録するようにします。 たとえば、フランス語のニュースに登録しようとする iOS アプリは、次の構文を使用して登録します。

    {
        aps:{
            alert: "$(News_French)"
        }
    }

テンプレートの詳細については、「[テンプレート](notification-hubs-templates-cross-platform-push-messages.md)」の記事を参照してください。

## <a name="prerequisites"></a>前提条件

- このチュートリアルは「[特定の iOS デバイスにプッシュ通知を送信する](notification-hubs-ios-xplat-segmented-apns-push-notification.md)」のチュートリアルのコードに直接基づいて構築されているため、このチュートリアルを完了し、そのコードを使用可能にしてください。
- 必要に応じて Visual Studio 2012 以降を使用できます。

## <a name="update-the-app-user-interface"></a>アプリのユーザー インターフェイスを更新する
このセクションでは、ローカライズしたニュース速報をテンプレートを使用して送信するために、トピック「[Notification Hubs を使用したニュース速報の送信]」で作成したニュース速報アプリを変更します。

MainStoryboard_iPhone.storyboard で、英語、フランス語、標準中国語の 3 つの言語を含む Segmented Control を追加します。

![][13]

次に、次の図に示すように、ViewController.h で IBOutlet を確実に追加します。

![][14]

## <a name="build-the-ios-app"></a>iOS アプリを構築する
1. Notification.h で、*retrieveLocale* メソッドを追加し、次のコードに示すように store および subscribe メソッドを変更します。
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;
   
        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
   
        - (NSSet*) retrieveCategories;
   
        - (int) retrieveLocale;
   
    ```
    Notification.m で、ロケール パラメーターを追加してユーザーの既定の設定に格納することで、 *storeCategoriesAndSubscribe* メソッドを変更します。
   
    ```obj-c
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];
   
            [self subscribeWithLocale: locale categories:categories completion:completion];
        }
    ````
    次に、 *subscribe* メソッドを変更してロケールを追加します。
   
    ```obj-c
        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];
   
            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }
   
            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];
   
            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }
       ```
    You use the method *registerTemplateWithDeviceToken*, instead of *registerNativeWithDeviceToken*. When you register for a template, you have to provide the json template and also a name for the template (as the app might want to register different templates). Make sure to register your categories as tags, as you want to make sure to receive the notifciations for those news.
   
    Add a method to retrieve the locale from the user default settings:
   
    ```obj-c
        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
   
            int locale = [defaults integerForKey:@"BreakingNewsLocale"];
   
            return locale < 0?0:locale;
        }
    ```
2. これで Notifications クラスが変更されたので、ViewController で新しい UISegmentControl が使用されることを確認する必要があります。 *viewDidLoad* メソッドに次の行を追加し、現在選択されているロケールが表示されるようにします。
   
    ```obj-c
        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
     ```  
    次に、*subscribe* メソッドで、*storeCategoriesAndSubscribe* の呼び出しを次のコードに変更します。
   
    ```obj-c
        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];
    ```
3. 最後に、アプリケーションの起動時に登録を正しく更新できるように、AppDelegate.m で *didRegisterForRemoteNotificationsWithDeviceToken* メソッドを更新します。 通知の *subscribe* メソッドの呼び出しを次のコードに変更します。
   
    ```obj-c
        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(省略可能) .NET コンソール アプリからローカライズしたテンプレート通知を送信する
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(省略可能) ローカライズされたテンプレート通知をデバイスから送信する
Visual Studio にアクセスできない場合でも、ローカライズされたテンプレート通知をデバイス上のアプリから直接送信することをテストできます。 前のチュートリアルで定義した `SendNotificationRESTAPI` メソッドに、ローカライズしたテンプレート パラメーターを追加できます。

    ```obj-c
        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }
    ```


## <a name="next-steps"></a>次の手順
このチュートリアルでは、iOS デバイスにローカライズした通知を送信しました。 iOS アプリの特定のユーザーにプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
>[特定のユーザーにプッシュ通知を送信する](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs を使用したニュース速報の送信]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
