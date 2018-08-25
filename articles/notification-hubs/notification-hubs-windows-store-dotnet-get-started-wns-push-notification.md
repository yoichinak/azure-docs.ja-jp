---
title: Azure Notification Hubs を使用してユニバーサル Windows プラットフォーム アプリに通知を送信する | Microsoft Docs
description: このチュートリアルでは、Azure Notification Hubs を使用して Windows ユニバーサル プラットフォーム アプリケーションにプッシュ通知を送信する方法について学習します。
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: c3bb170800508d5a546573850f445b2a8991ea8c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38597746"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-by-using-azure-notification-hubs"></a>チュートリアル: Azure Notification Hubs を使用してユニバーサル Windows プラットフォーム アプリに通知を送信する

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

このチュートリアルでは、ユニバーサル Windows プラットフォーム (UWP) アプリにプッシュ通知を送信するための通知ハブを作成します。 Windows プッシュ通知サービス (WNS) を使用してプッシュ通知を受信する空の Windows ストア アプリを作成します。 その後、通知ハブを使用して、アプリを実行しているすべてのデバイスにプッシュ通知をブロードキャストできます。

> [!NOTE]
> このチュートリアルの完成したコードについては、[GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal) を参照してください。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * Windows ストアでアプリを作成する
> * 通知ハブを作成する
> * サンプルの Windows アプリを作成する
> * テスト通知を送信する


## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) 以降。
- [UWP アプリ開発ツールがインストールされている](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
- アクティブな Windows ストア アカウント

このチュートリアルを完了することは、UWP アプリに関する他のすべての Notification Hubs チュートリアルを行うための前提条件になっています。

## <a name="create-an-app-in-windows-store"></a>Windows ストアでアプリを作成する
UWP アプリにプッシュ通知を送信するには、アプリを Windows ストアに関連付けます。 次に、WNS に統合するために通知ハブを構成します。

1. [Windows デベロッパー センター](https://dev.windows.com/overview)に移動し、Microsoft アカウントでサインインしてから、**[新しいアプリの作成]** を選択します。

    ![[New app] (新しいアプリ) ボタン](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
1. アプリの名前を入力し、**[Reserve product name] (製品名を予約)** を選択します。 これでアプリの新しい Windows ストア登録が作成されます。

    ![ストア アプリ名](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
1. **[アプリの管理]** を展開して、**[WNS/MPNS]** を選択し、**[WNS/MPNS]** を選択してから、**[Live Services site] (Live Services サイト)** を選択します。 Microsoft アカウントにサインインする。 **アプリケーション登録ポータル**が新しいタブで開きます。あるいは、[アプリケーション登録ポータル](http://apps.dev.microsoft.com)に直接移動し、アプリケーション名を選択してこのページを表示できます。

    ![[WNS MPNS] ページ](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
1.   **アプリケーション シークレット** パスワードと**パッケージ セキュリティ ID (SID)** をメモします。

        >[!WARNING]
        >アプリケーション シークレットおよびパッケージ SID は、重要なセキュリティ資格情報です。 これらの値は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。

## <a name="create-a-notification-hub"></a>通知ハブを作成する
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


### <a name="configure-wns-settings-for-the-hub"></a>WNS の設定をハブ用に構成する

1. **[通知設定]** カテゴリで、**[Windows (WNS)]** を選択します。 
2. 前のセクションからメモした**パッケージ SID** と**セキュリティ キー**の値を入力します。 
3. ツールバーの **[保存]** を選択します。

    ![[パッケージ SID] ボックスと [セキュリティ キー] ボックス](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

これで、通知ハブが WNS と連携するように構成されました。 接続文字列を使用してアプリを登録し、通知を送信できます。

## <a name="create-a-sample-windows-app"></a>サンプルの Windows アプリを作成する
1. Visual Studio で、**[ファイル]** を選択し、**[新規]** をポイントして **[プロジェクト]** を選択します。 
2. **[新しいプロジェクト]** ダイアログ ボックスで、次の操作を行います。 

    1. **[Visual C#]** を展開します。
    2. **[Windows ユニバーサル]** を選択します。 
    3. **[Blank App (Universal Windows)] (空のアプリ (ユニバーサル Windows))** を選択します。 
    4. プロジェクトの **名前** を入力します。 
    5. **[OK]** を選択します。 

        ![[新しいプロジェクト] ダイアログ](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)
1. **ターゲット**と**最小**プラットフォーム バージョンの既定値を受け入れ、**[OK]** を選択します。 
2. ソリューション エクスプローラーで Windows ストア アプリ プロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順に選択します。 **アプリケーションを Windows ストアと関連付ける** ウィザードが表示されます。
3. ウィザードで、自分の Microsoft アカウントでサインインします。
4. 手順 2. で登録したアプリを選択し、**[次へ]**、**[関連付け]** の順に選択します。 この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。
5. Visual Studio でソリューションを右クリックし、**[NuGet パッケージの管理]** を選択します。 **[NuGet パッケージの管理]** ウィンドウが開きます。
6. 検索ボックスに「**WindowsAzure.Messaging.Managed**」と入力し、**[インストール]** を選択して、使用条件に同意します。
   
    ![[NuGet パッケージの管理] ウィンドウ][20]
   
    この操作によって、[Microsoft.Azure.NotificationHubs NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)を使用して Windows 用 Azure Notification Hubs ライブラリがダウンロード、インストールされ、ライブラリへの参照が追加されます。

3. App.xaml.cs プロジェクト ファイルを開き、次の `using` ステートメントを追加します。 
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

4. さらに App.xaml.cs で、次の **InitNotificationsAsync** メソッドの定義を **App** クラスに追加します。
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
   
        }
   
    このコードにより、WNS からアプリケーションのチャネル URI が取得され、そのチャネル URI が通知ハブに登録されます。
   
    >[!NOTE]
    >* **ハブ名**プレースホルダーは、Azure Portal に表示される通知ハブの名前に置き換えてください。 
    >* さらに、接続文字列プレースホルダーを、前のセクションで通知ハブの **[アクセス ポリシー]** ページから取得した **DefaultListenSharedAccessSignature** 接続文字列に置き換えてください。
   > 
   > 
5. App.xaml.cs 内で、**OnLaunched** イベント ハンドラーの先頭に、次に示す新しい **InitNotificationsAsync** メソッドへの呼び出しを追加します。
   
        InitNotificationsAsync();
   
    これにより、アプリケーションが起動するたびに必ずチャネル URI が通知ハブに登録されます。

6. アプリを実行するには、**F5** キーを押します。 登録キーを示すダイアログ ボックスが表示されます。 ダイアログを閉じるには、**[OK]** を選択します。 

    ![登録が成功しました](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

これで、アプリケーションがトースト通知を受信する準備が整いました。

## <a name="send-test-notifications"></a>テスト通知を送信する
[Azure Portal](https://portal.azure.com/) で通知を送信することで、通知の受信をすばやくテストできます。 

1. Azure Portal で、[概要] タブに切り替えてから、ツールバーの **[テスト送信]** を選択します。     

    ![[テスト送信] ボタン](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. **[テスト送信]** ウィンドウで、次のアクションを実行します。 
    1. **[プラットフォーム]** として、**[Windows]** を選択します。
    2. **通知の種類** として、**[トースト]** を選択します。 
    3. **[送信]** を選択します。 
    
        ![[テスト送信] ウィンドウ](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. ウィンドウの一番下の **[結果]** 一覧で、[送信] 操作の結果を確認します。 アラート メッセージも表示されます。 

    ![[送信] 操作の結果](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
1. 通知メッセージが表示されます。デスクトップ上の**テスト メッセージ**。 

    ![通知メッセージ](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)


## <a name="next-steps"></a>次の手順
このチュートリアルでは、ポータルまたはコンソール アプリを使用して、すべての Windows デバイスにブロードキャスト通知を送信しました。 特定のデバイスにプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
>[特定のデバイスにプッシュ通知を送信する](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)


<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[toast catalog]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 
