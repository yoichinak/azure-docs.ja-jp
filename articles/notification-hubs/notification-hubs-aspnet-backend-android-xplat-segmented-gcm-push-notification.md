---
title: Azure Notification Hubs と Google Cloud Messaging を使用して特定の Android デバイスにプッシュ通知を送信する | Microsoft Docs
description: Notification Hubs を使用し、Azure Notification Hubs と Google Cloud Messaging を使用して特定の Android デバイスにプッシュ通知を送信する方法について説明します。
services: notification-hubs
documentationcenter: android
author: dimazaid
manager: kpiteira
editor: spelluru'
ms.assetid: 3c23cb80-9d35-4dde-b26d-a7bfd4cb8f81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/06/2018
ms.author: dimazaid
ms.openlocfilehash: 1751071aa37665b5cea51f7be76990020ad569ab
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2018
ms.locfileid: "41919555"
---
# <a name="tutorial-push-notifications-to-specific-android-devices-using-azure-notification-hubs-and-google-cloud-messaging"></a>チュートリアル: Azure Notification Hubs と Google Cloud Messaging を使用して特定の Android デバイスにプッシュ通知を送信する
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Azure Notification Hubs を使用して Android アプリにニュース速報通知をブロードキャストする方法を示します。 完了すると、興味のあるニュース速報カテゴリに登録し、それらのカテゴリのプッシュ通知だけを受信できるようになります。 このシナリオは、以前に関心があることを宣言しているユーザーのグループに通知を送信する必要のある多くのアプリ (RSS リーダーや、音楽ファン向けのアプリなど) にとって一般的なパターンです。

ブロードキャスト シナリオは、通知ハブでの登録の作成時に 1 つ以上の "*タグ*" を追加することで有効にします。 通知がタグに送信されると、タグに登録されたすべてのデバイスが通知を受信します。 タグは文字列にすぎないため、事前にプロビジョニングする必要はありません。 タグの詳細については、「[Notification Hubs のルーティングとタグ式](notification-hubs-tags-segment-push-message.md)」を参照してください。

このチュートリアルでは、次のアクションを実行します。 

> [!div class="checklist"]
> * モバイル アプリにカテゴリ選択を追加します。
> * タグを使用して通知に登録します。 
> * タグ付けされた通知を送信します。 
> * アプリケーションをテストする

## <a name="prerequisites"></a>前提条件
このチュートリアルは、「[チュートリアル: Azure Notification Hubs と Google Cloud Messaging を使用して Android デバイスにプッシュ通知を送信する][get-started]」で作成したアプリに基づいて構築されています。 このチュートリアルを開始する前に、「[チュートリアル: Azure Notification Hubs と Google Cloud Messaging を使用して Android デバイスにプッシュ通知を送信する][get-started]」を完了してください。

## <a name="add-category-selection-to-the-app"></a>アプリケーションにカテゴリ選択を追加する
最初の手順として、既存のメイン アクティビティに UI 要素を追加して、ユーザーが登録するカテゴリを選択できるようにします。 ユーザーにより選択されるカテゴリは、デバイスに格納されます。 アプリが起動すると、通知ハブにデバイス登録が作成され、選択されたカテゴリがタグとして追加されます。

1. res/layout/activity_main.xml ファイルを開き、その内容を次の内容に置き換えます。
   
    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.example.breakingnews.MainActivity"
        android:orientation="vertical">

            <CheckBox
                android:id="@+id/worldBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_world" />
            <CheckBox
                android:id="@+id/politicsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_politics" />
            <CheckBox
                android:id="@+id/businessBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_business" />
            <CheckBox
                android:id="@+id/technologyBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_technology" />
            <CheckBox
                android:id="@+id/scienceBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_science" />
            <CheckBox
                android:id="@+id/sportsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_sports" />
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="subscribe"
                android:text="@string/button_subscribe" />
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Hello World!"
                android:id="@+id/text_hello"
            />
    </LinearLayout>
    ```
2. res/values/strings.xml ファイルを開き、次の行を追加します。

    ```xml
    <string name="button_subscribe">Subscribe</string>
    <string name="label_world">World</string>
    <string name="label_politics">Politics</string>
    <string name="label_business">Business</string>
    <string name="label_technology">Technology</string>
    <string name="label_science">Science</string>
    <string name="label_sports">Sports</string>
    ```

    main_activity.xml のグラフィカル レイアウトは、次の図のようになります。
   
    ![][A1]
3. **MainActivity** クラスと同じパッケージ内にクラス `Notifications` を作成します。

    ```java   
    import java.util.HashSet;
    import java.util.Set;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.os.AsyncTask;
    import android.util.Log;
    import android.widget.Toast;
    import android.view.View;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class Notifications {
        private static final String PREFS_NAME = "BreakingNewsCategories";
        private GoogleCloudMessaging gcm;
        private NotificationHub hub;
        private Context context;
        private String senderId;

        public Notifications(Context context, String senderId, String hubName, 
                                String listenConnectionString) {
            this.context = context;
            this.senderId = senderId;

            gcm = GoogleCloudMessaging.getInstance(context);
            hub = new NotificationHub(hubName, listenConnectionString, context);
        }

        public void storeCategoriesAndSubscribe(Set<String> categories)
        {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            settings.edit().putStringSet("categories", categories).commit();
            subscribeToCategories(categories);
        }

        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }

        public void subscribeToCategories(final Set<String> categories) {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(senderId);

                        String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                        hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                            categories.toArray(new String[categories.size()]));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to register - " + e.getMessage());
                        return e;
                    }
                    return null;
                }

                protected void onPostExecute(Object result) {
                    String message = "Subscribed for categories: "
                            + categories.toString();
                    Toast.makeText(context, message,
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }

    }
    ```
       
    このクラスは、このデバイスが受信するニュースのカテゴリを格納するためにローカル ストレージを使用します。 ローカル ストレージには、これらのカテゴリを登録するメソッドも格納されます。
4. **MainActivity** クラスで、**NotificationHub** および **GoogleCloudMessaging** のプライベート フィールドを削除し、**Notifications** に対するフィールドを追加します。

    ```java   
    // private GoogleCloudMessaging gcm;
    // private NotificationHub hub;
    private Notifications notifications;
    ```
5. 次に、**onCreate** メソッドで、**hub** フィールドの初期化と **registerWithNotificationHubs** を削除します。 次に、**Notifications** クラスのインスタンスを初期化する次の行を追加します。 

    ```java
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mainActivity = this;

        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId,
                MyHandler.class);

        notifications = new Notifications(this, NotificationSettings.SenderId, NotificationSettings.HubName, NotificationSettings.HubListenConnectionString);

        notifications.subscribeToCategories(notifications.retrieveCategories());
    }
    ```

    NotificationSettings クラスでハブ名と接続文字列が正しく設定されていることを確認します。

    > [AZURE.NOTE] クライアント アプリケーションを使用して配布される資格情報は一般にセキュリティで保護されないため、クライアント アプリケーションではリッスン アクセス用のキーだけを配布してください。 リッスン アクセスにより、アプリケーションが通知を登録できるようになりますが、既存の登録を変更することはできないため、通知を送信できません。 通知を送信して既存の登録を変更するセキュリティで保護されたバックエンド サービスでは、フル アクセス キーが使用されます。
1. 次に、次のインポートを追加します。

    ```java   
    import android.widget.CheckBox;
    import java.util.HashSet;
    import java.util.Set;
    ```
1. サブスクライブ ボタンのクリック イベントを処理する次の `subscribe` メソッドを追加します。        
   
    ```java
    public void subscribe(View sender) {
        final Set<String> categories = new HashSet<String>();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        if (world.isChecked())
            categories.add("world");
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        if (politics.isChecked())
            categories.add("politics");
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        if (business.isChecked())
            categories.add("business");
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        if (technology.isChecked())
            categories.add("technology");
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        if (science.isChecked())
            categories.add("science");
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        if (sports.isChecked())
            categories.add("sports");

        notifications.storeCategoriesAndSubscribe(categories);
    }
    ```
       
    このメソッドは、カテゴリのリストを作成し、 **Notifications** クラスを使用してそのリストをローカル ストレージに格納し、対応するタグを通知ハブに登録します。 カテゴリが変更されると、新しいカテゴリで登録が再作成されます。

これで、アプリケーションがデバイス上のローカル ストレージに一連のカテゴリを格納したり、ユーザーがカテゴリの選択を変更したときに通知ハブに登録できるようになりました。

## <a name="register-for-notifications"></a>通知を登録する
この手順では、ローカル ストレージに格納されたカテゴリを使用して、起動時に通知ハブに通知します。

> [!NOTE]
> Google Cloud Messaging (GCM) によって割り当てられた registrationId はいつでも変更できるので、通知の失敗を回避するため、通知を頻繁に登録するようにしてください。 この例では、アプリケーションが起動するたびに通知を登録します。 頻繁に実行されるアプリケーションの場合 (1 日に複数回など)、帯域幅を節約するため、前回の登録から 1 日経過していない場合は登録をスキップできます。
> 
> 

1. **MainActivity** クラスの **onCreate** メソッドの最後に次のコードを追加します。
   
    ```java
    notifications.subscribeToCategories(notifications.retrieveCategories());
    ```
   
    このコードは、アプリが確実に、起動するたびにローカル ストレージからカテゴリを取得し、これらのカテゴリへの登録を要求するようにします。 
2. 次に、`MainActivity` クラスの `onStart()` メソッドを次のように更新します。
   
    ```java
    @Override
    protected void onStart() {
   
        super.onStart();
        isVisible = true;
   
        Set<String> categories = notifications.retrieveCategories();
   
        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }
    ```
   
    このコードは、以前に保存されたカテゴリの状態に基づいてメイン アクティビティを更新します。

これで、アプリケーションが完成し、デバイスのローカル ストレージに一連のカテゴリを格納できるようになりました。ローカル ストレージは、ユーザーがカテゴリの選択を変更したときに通知ハブに登録するために使用されます。 次に、このアプリにカテゴリ通知を送信できるバックエンドを定義します。

## <a name="send-tagged-notifications"></a>タグ付けされた通知を送信する
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>アプリケーションをテストする
1. Android Studio で、Android デバイスまたはエミュレーター上でアプリを実行します。 アプリの UI には、購読するカテゴリを選択できる一連の切り替えボタンが表示されます。
2. 1 つ以上のカテゴリ切り替えボタンを有効にし、 **[Subscribe]** をクリックします。 アプリケーションにより、選択されたカテゴリがタグに変換され、選択されたタグの新しいデバイス登録が通知ハブから要求されます。 登録されたカテゴリが返され、トースト通知に表示されます。

    ![カテゴリにサブスクライブする](./media/notification-hubs-aspnet-backend-android-breaking-news/subscribe-for-categories.png)
1. カテゴリごとの通知を送信する .NET コンソール アプリを実行します。 選択されたカテゴリの通知がトースト通知として表示されます。

    ![テクノロジのニュース通知](./media/notification-hubs-aspnet-backend-android-breaking-news/technolgy-news-notification.png)

## <a name="next-steps"></a>次の手順
このチュートリアルでは、カテゴリに登録している特定の Android デバイスにブロードキャスト通知を送信しました。 特定のユーザーにプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
>[特定のユーザーにプッシュ通知を送信する](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md)

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure portal]: https://portal.azure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
