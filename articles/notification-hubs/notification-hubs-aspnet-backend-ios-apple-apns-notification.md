---
title: Azure Notification Hubs を使用して特定のユーザーにプッシュ通知を送信する | Microsoft Docs
description: Azure Notification Hubs を使用して特定のユーザーにプッシュ通知を送信する方法について説明します。
documentationcenter: ios
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/13/2018
ms.author: dimazaid
ms.openlocfilehash: 36d70c40e3de7bd38cdfc566da37060cdcea9060
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38681555"
---
# <a name="tutorial-push-notifications-to-specific-users-using-azure-notification-hubs"></a>チュートリアル: Azure Notification Hubs を使用して特定のユーザーにプッシュ通知を送信する
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

このチュートリアルでは、Azure Notification Hubs を使用して特定のデバイスで特定のアプリケーション ユーザーにプッシュ通知を送信する方法について説明します。 [アプリ バックエンドからの登録管理](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend)に関するガイダンス トピックに示すように、ASP.NET WebAPI バックエンドを使用してクライアントを認証し、通知を生成します。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * Web API プロジェクトを作成する
> * WebAPI バックエンドに対してクライアントを認証する
> * WebAPI バックエンドを使用して通知に登録する
> * WebAPI バックエンドから通知を送信する
> * 新しい WebAPI バックエンドを発行する
> * iOS アプリを変更する
> * アプリケーションをテストする

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、「 [Notification Hubs の使用 (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md)」での説明に従って通知が作成され、構成されていると想定しています。 また、「 [安全なプッシュ (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) 」チュートリアルの前提条件でもあります。
バックエンド サービスとして Mobile Apps を使用する場合は、「 [iOS アプリへのプッシュ通知の追加](../app-service-mobile/app-service-mobile-ios-get-started-push.md)」を参照してください。

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>iOS アプリを変更する
1. 「 [Notification Hubs の使用 (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) 」で作成した単一枠ビュー アプリケーションを開きます。
   
   > [!NOTE]
   > このセクションでは、プロジェクトは空の組織名で構成されていると想定しています。 そうでない場合は、すべてのクラス名の前に組織名を付けてください。
   > 
   > 
2. `Main.storyboard` で、オブジェクト ライブラリから、スクリーンショットに表示されているコンポーネントを追加します。
   
    ![][1]
   
   * **Username**: "*Enter Username*" というプレースホルダー テキストが入っている UI テキスト フィールドです。結果の送信ラベルのすぐ下にあり、左右の余白と、結果の送信ラベルの下の余白による制約が適用されます。
   * **Password**: "*Enter Password*" というプレースホルダー テキストが入っている UI テキスト フィールドです。ユーザー名のテキスト フィールドのすぐ下にあり、左右の余白と、ユーザー名のテキスト フィールドの下の余白による制約が適用されます。 Attributes Inspector で *[Return Key]* の下にある **[Secure Text Entry]** オプションをオンにします。
   * **Log in**: パスワードのテキスト フィールドのすぐ下にあるラベル付きの UI ボタンです。Attributes Inspector の *[Control-Content]* の下にある **[Enabled]** オプションをオフにします。
   * **WNS**: Windows Notification Service に通知を送信するためのスイッチとそのラベルです。ハブ上でセットアップを済ませておく必要があります。 詳しくは、[Windows で通知ハブを使用する方法に関するチュートリアル](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)をご覧ください。
   * **GCM**: Google Cloud Messaging に通知を送信するためのスイッチとそのラベルです。ハブ上でセットアップを済ませておく必要があります。 詳しくは、[Android で通知ハブを使用する方法に関するチュートリアル](notification-hubs-android-push-notification-google-gcm-get-started.md)をご覧ください。
   * **APNS**: Apple Platform Notification Service に通知を送信するためのスイッチとそのラベルです。
   * **Recipient Username**: "*Recipient username tag*" というプレースホルダー テキストが入っている UI テキスト フィールドです。GCM ラベルのすぐ下にあり、左右の余白と、GCM ラベルの下にあることの制約が適用されます。

    「 [Notification Hubs の使用](notification-hubs-ios-apple-push-notification-apns-get-started.md) 」チュートリアル (iOS) では、いくつかのコンポーネントが追加されています。

1. ビューに表示されているコンポーネントを **Ctrl** キーを押しながらドラッグし、ViewController.h まで移動します。これにより、新しいアウトレットが追加されます。
   
    ```obj-c
        @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
        @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
        @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
        @property (weak, nonatomic) IBOutlet UITextField *NotificationField;
   
        // Used to enable the buttons on the UI
        @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
        @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;
   
        // Used to enabled sending notifications across platforms
        @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;
   
        - (IBAction)LogInAction:(id)sender;
    ```
2. ViewController.h で、インポート ステートメントのあとに次の `#define` を追加します。 *<Enter Your Backend Endpoint\>* の部分は、前のセクションでアプリのバックエンドをデプロイする際に使用した URL で置き換えてください。 たとえば、*http://you_backend.azurewebsites.net* です。
   
    ```obj-c
        #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```
3. プロジェクトで、新しく **RegisterClient** という名前の **Cocoa Touch クラス**を作成します。これは、前もって作成しておいた ASP.NET バックエンドとの間のインターフェイスに使用します。 `NSObject` から継承するクラスを作成したら、 RegisterClient.h に以下のコードを追加します。

    ```obj-c   
        @interface RegisterClient : NSObject
   
        @property (strong, nonatomic) NSString* authenticationHeader;
   
        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
            andCompletion:(void(^)(NSError*))completion;
   
        -(instancetype) initWithEndpoint:(NSString*)Endpoint;
   
        @end
    ```
4. `RegisterClient.m` で、`@interface` セクションを更新します。

    ```obj-c   
        @interface RegisterClient ()
   
        @property (strong, nonatomic) NSURLSession* session;
        @property (strong, nonatomic) NSURLSession* endpoint;
   
        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion;
        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion;
        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;
   
        @end
    ```
5. RegisterClient.m の `@implementation` セクションを、次のコードで置き換えます。

    ```obj-c
        @implementation RegisterClient

        // Globals used by RegisterClient
        NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

        -(instancetype) initWithEndpoint:(NSString*)Endpoint
        {
            self = [super init];
            if (self) {
                NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
                _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
                _endpoint = Endpoint;
            }
            return self;
        }

        -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                    andCompletion:(void(^)(NSError*))completion
        {
            [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
        }

        -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                    andCompletion:(void(^)(NSError*))completion
        {
            NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

            NSString *deviceTokenString = [[token description]
                stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
            deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                    uppercaseString];

            [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
                completion:^(NSString* registrationId, NSError *error) {
                NSLog(@"regId: %@", registrationId);
                if (error) {
                    completion(error);
                    return;
                }

                [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                    tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                    if (error) {
                        completion(error);
                        return;
                    }

                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if (httpResponse.statusCode == 200) {
                        completion(nil);
                    } else if (httpResponse.statusCode == 410 && retry) {
                        [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                    } else {
                        NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                        completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }

                }];
            }];
        }

        -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                    tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
        {
            NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                    @"Tags": [tags allObjects]};
            NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                                options:NSJSONWritingPrettyPrinted error:nil];

            NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                                encoding:NSUTF8StringEncoding]);

            NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                    registrationId];
            NSURL* requestURL = [NSURL URLWithString:endpoint];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"PUT"];
            [request setHTTPBody:jsonData];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
            [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                if (!error)
                {
                    completion(response, error);
                }
                else
                {
                    NSLog(@"Error request: %@", error);
                    completion(nil, error);
                }
            }];
            [dataTask resume];
        }

        -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                    completion:(void(^)(NSString*, NSError*))completion
        {
            NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                        objectForKey:RegistrationIdLocalStorageKey];

            if (registrationId)
            {
                completion(registrationId, nil);
                return;
            }

            // request new one & save
            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                    _endpoint, token]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                    self.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSString* registrationId = [[NSString alloc] initWithData:data
                        encoding:NSUTF8StringEncoding];

                    // remove quotes
                    registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                        [registrationId length]-2)];

                    [[NSUserDefaults standardUserDefaults] setObject:registrationId
                        forKey:RegistrationIdLocalStorageKey];
                    [[NSUserDefaults standardUserDefaults] synchronize];

                    completion(registrationId, nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                    userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        @end
    ```

    このコードは、アプリケーション バックエンドへの REST 呼び出しを実行するための NSURLSession、および通信ハブによって返される registrationId をローカルに格納するための NSUserDefaults の使用についてのガイダンス記事「[アプリ バックエンドからの登録](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend)」で説明したロジックを実装します。

    このクラスが適切に機能するためには、プロパティ **authorizationHeader** を設定する必要があります。 このプロパティは、ログイン後に **ViewController** クラスによって設定されます。

1. ViewController.h で、RegisterClient.h の `#import` ステートメントを追加し、 デバイス トークンの宣言と、`@interface` セクションの`RegisterClient` インスタンスに対する参照を追加します。

    ```obj-c   
        #import "RegisterClient.h"
   
        @property (strong, nonatomic) NSData* deviceToken;
        @property (strong, nonatomic) RegisterClient* registerClient;
    ```
2. ViewController.m で、以下のように `@interface` セクションのプライベート メソッドの宣言を追加します。
   
    ```obj-c
        @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>
   
        // create the Authorization header to perform Basic authentication with your app back-end
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
   
        @end
    ```

    > [!NOTE]
    > これは安全な認証スキームではないため、 **createAndSetAuthenticationHeaderWithUsername:AndPassword** : の実装を登録クライアント クラス (OAuth、Active Directory など) によって読み取られる認証トークンを生成する特定の認証メカニズムで置き換えます。
1. 次に、`ViewController.m` の `@implementation` セクションに以下のコードを追加します。これは、デバイス トークンと認証ヘッダーの設定の実装を追加するものです。
   
    ```obj-c
        -(void) setDeviceToken: (NSData*) deviceToken
        {
            _deviceToken = deviceToken;
            self.LogInButton.enabled = YES;
        }
   
        -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                        AndPassword:(NSString*)password;
        {
            NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];
   
            NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];
   
            self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                        encoding:NSUTF8StringEncoding];
        }
   
        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }
    ```
   
    ログイン ボタンを有効にするデバイス トークンの設定方法に注意してください。 これは、ログイン アクションの一部として、View Controller がアプリケーション バックエンドでプッシュ通知を登録するためです。 そのため、デバイス トークンが適切に設定される前にログイン アクションにアクセスできないようにします。 プッシュ登録の前にログインが発生する場合には、プッシュ登録からログインを切り離す必要があります。
2. 以下のスニペットを使って、ViewController.m に **[Log in]** ボタンのアクション メソッドと、ASP.NET バックエンドを使って通知メッセージを送信するためのメソッドを実装します。
   
    ```obj-c
       - (IBAction)LogInAction:(id)sender {
           // create authentication header and set it in register client
           NSString* username = self.UsernameField.text;
           NSString* password = self.PasswordField.text;
   
           [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];
   
           __weak ViewController* selfie = self;
           [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
               andCompletion:^(NSError* error) {
               if (!error) {
                   dispatch_async(dispatch_get_main_queue(),
                   ^{
                       selfie.SendNotificationButton.enabled = YES;
                       [self MessageBox:@"Success" message:@"Registered successfully!"];
                   });
               }
           }];
       }

        - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                    Message:(NSString*)message
        {
            NSURLSession* session = [NSURLSession
                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
                delegateQueue:nil];

            // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
            NSURL* requestURL = [NSURL URLWithString:[NSString
                stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
                usernameTag]];

            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"POST"];

            // Get the mock authenticationheader from the register client
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                self.registerClient.authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

            // Execute the send notification REST API on the ASP.NET Backend
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                        pns, httpResponse.statusCode, error];
                    dispatch_async(dispatch_get_main_queue(),
                    ^{
                        // Append text because all 3 PNS calls may also have information to view
                        [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                    });
                    NSLog(status);
                }

                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];
            [dataTask resume];
        }
    ```

1. **[Send Notification]** ボタンのアクションを更新し、ASP.NET バックエンドを使用すると共に、スイッチで有効になっている PNS があればそれに通知を送信するようにします。

    ```obj-c
        - (IBAction)SendNotificationMessage:(id)sender
        {
            //[self SendNotificationRESTAPI];
            [self SendToEnabledPlatforms];
        }

        -(void)SendToEnabledPlatforms
        {
            NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

            [self.sendResults setText:@""];

            if ([self.WNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

            if ([self.GCMSwitch isOn])
                [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

            if ([self.APNSSwitch isOn])
                [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
        }
    ```

1. **ViewDidLoad**関数で次のコードを追加して、RegisterClient インスタンスをインスタンス化し、テキスト フィールドに対するデリゲートを設定します。
   
    ```obj-c
       self.UsernameField.delegate = self;
       self.PasswordField.delegate = self;
       self.RecipientField.delegate = self;
       self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```
2. **AppDelegate.m** 内の `application:didRegisterForPushNotificationWithDeviceToken:` メソッドの内容をすべて削除して以下で置き換え、View Controller に APNs から取得した最新のデバイス トークンが含まれることを確認します。
   
    ```obj-c
       // Add import to the top of the file
       #import "ViewController.h"
   
       - (void)application:(UIApplication *)application
                   didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
       {
           ViewController* rvc = (ViewController*) self.window.rootViewController;
           rvc.deviceToken = deviceToken;
       }
    ```
3. 最後に、 **AppDelegate.m**に次のメソッドが含まれることを確認してください。

    ```obj-c   
       - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
           NSLog(@"%@", userInfo);
           [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
       }
    ```

## <a name="test-the-application"></a>アプリケーションをテストする
1. XCode を使用して、物理 iOS デバイスでアプリケーションを実行します (プッシュ通知はシミュレーターでは機能しません)。
2. iOS アプリケーションの UI で、ユーザー名とパスワードの両方に同じ値を入力します。 その後、 **[ログイン]** をクリックします。
   
    ![][2]
3. 登録の成功を通知するポップアップが表示されます。 Click **OK**.
   
    ![][3]
4. **Recipient username tag* というテキストが表示されているフィールドに、別のデバイスから登録するときに使用したユーザー名のタグを入力します。
5. 通知メッセージを入力して **[Send Notification]** をクリックします。 入力したタグが登録されているデバイスのみ、通知メッセージを受信します。 通知は、該当するユーザーにのみ送信されます。
   
    ![][4]

## <a name="next-steps"></a>次の手順
このチュートリアルでは、タグが登録に関連付けられている特定のユーザーにプッシュ通知を送信する方法を学習しました。 場所に基づいたプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
>[場所に基づいたプッシュ通知を送信する](notification-hubs-push-bing-spartial-data-geofencing-notification.md)


[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
