---
title: Azure AD Node.js Web アプリの概要 | Microsoft Docs
description: サインインのために Azure AD と連携する Node.js Express MVC Web アプリを構築する方法を説明します。
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/20/2018
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 1f8f19944f64a5dfd5421a99734c5fd0fc3be1bc
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "42144384"
---
# <a name="azure-ad-nodejs-web-app-getting-started"></a>Azure AD Node.js Web アプリの概要
ここでは、Passport を使用して次の操作を行います。

* Azure Active Directory (Azure AD) を使用してユーザーをアプリにサインインします。
* ユーザーについての情報を表示します。
* ユーザーをアプリからサインアウトします。

Passport は Node.js 用の認証ミドルウェアです。 Passport は、柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Resitify Web アプリケーションに、支障をきたすことなくドロップされます。 包括的な認証手法セットにより、ユーザー名とパスワードによる認証のほか、Facebook や Twitter などを使用する認証をサポートします。 Microsoft Azure Active Directory 用の戦略が開発されています。 ここでは、このモジュールをインストールした後、Microsoft Azure Active Directory `passport-azure-ad` プラグインを追加します。

そのためには、次の手順を実行します。

1. アプリを登録します。
2. `passport-azure-ad` 戦略を使用するようにアプリを設定します。
3. Passport を使用して、サインイン要求とサインアウト要求を Azure AD に発行する
4. ユーザーに関するデータを出力します。

このチュートリアルのコードは、 [GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS)で管理されています。 追加の参考資料として、[アプリのスケルトン (.zip ファイル) をダウンロード](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)したり、次のようにスケルトンを複製したりすることができます。

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

完成したアプリケーションは、このチュートリアルの終わりにも示しています。

## <a name="step-1-register-an-app"></a>手順 1: アプリを登録する
1. [Azure Portal](https://portal.azure.com) にサインインします。

2. ページ上部のメニューから、自分のアカウントを選択します。 **[ディレクトリ]** の一覧から、アプリケーションを登録する Active Directory テナントを選択します。

3. 画面の左側にあるメニューで **[すべてのサービス]** を選択し、**[Azure Active Directory]** を選択します。

4. **[アプリの登録]** を選択し、**[追加]** を選択します。

5. 画面の指示に従い、**Web アプリケーション**または **WebAPI** (またはその両方) を作成します。
  * アプリケーションの **[名前]** には、ユーザーがアプリケーションの機能を把握できるような名前を設定します。

  * **[サインオン URL]** は、アプリのベース URL です。 スケルトンの既定値は、 `http://localhost:3000/auth/openid/return`です。

6. 登録が完了すると、Azure AD によってアプリに一意のアプリケーション ID が割り当てられます。 この値は次のセクションで必要になるので、アプリケーション ページからコピーしておきます。
7. アプリケーションの **[設定]**  ->  **[プロパティ]** ページで、アプリ ID URI を更新します。 **[アプリケーション ID/URI]** は、アプリケーションの一意識別子です。 `https://<tenant-domain>/<app-name>` の形式を使用します (例: `https://contoso.onmicrosoft.com/my-first-aad-app`)。

8. アプリケーションの **[設定]** -> **[応答 URL]** ページで、手順 5 の [サインオン URL] で追加した URL を追加して、[保存] をクリックします。

9. シークレット キーを作成するために、[「web APl にアクセスするための　アプリケーション資格情報またはアクセス許可を追加するには」](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-application-credentials-or-permissions-to-access-web-apis)の手順4に従います。

   > [!IMPORTANT]
   > アプリケーション キーの値をコピーします。 値は、これは、下記の**手順 3** に必要な`clientSecret`値です。 

## <a name="step-2-add-prerequisites-to-your-directory"></a>手順 2: ディレクトリに前提条件を追加する
1. コマンド ラインから、ディレクトリをルート フォルダーに移動し (まだルート フォルダーでない場合)、次のコマンドを実行します。

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. さらに、`passport-azure-ad` が必要です。
    * `npm install passport-azure-ad`

これにより、`passport-azure-ad` が依存するライブラリがインストールされます。

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>手順 3: passport-node-js 戦略を使用するようにアプリを設定する
ここでは、OpenID Connect 認証プロトコルを使用するように Express を構成します。 Passport は、サインイン要求とサインアウト要求の発行、ユーザー セッションの管理、ユーザーに関する情報の取得などを行うために使用されます。

1. 最初に、プロジェクトのルートにある `config.js` ファイルを開いて、`exports.creds` セクションでアプリの構成値を入力します。

  * `clientID` は、登録ポータルでアプリに割り当てられた**アプリケーション ID** です。

  * `returnURL` は、ポータルで入力した**応答 URL** です。

  * `clientSecret` は、ポータルで生成したシークレットです。

2. 次に、プロジェクトのルートにある `app.js` ファイルを開きます。 次の呼び出しを追加して、`passport-azure-ad` に付属する `OIDCStrategy` 戦略を呼び出します。

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. その後、今参照した戦略を使用してサインイン要求を処理します。

    ```JavaScript
    // Use the OIDCStrategy within Passport. (Section 2)
    //
    //   Strategies in passport require a `validate` function that accepts
    //   credentials (in this case, an OpenID identifier), and invokes a callback
    //   with a user object.
    passport.use(new OIDCStrategy({
        callbackURL: config.creds.returnURL,
        realm: config.creds.realm,
        clientID: config.creds.clientID,
        clientSecret: config.creds.clientSecret,
        oidcIssuer: config.creds.issuer,
        identityMetadata: config.creds.identityMetadata,
        skipUserProfile: config.creds.skipUserProfile,
        responseType: config.creds.responseType,
        responseMode: config.creds.responseMode
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
        if (!profile.email) {
        return done(new Error("No email found"), null);
        }
        // asynchronous verification, for effect...
        process.nextTick(function () {
        findByEmail(profile.email, function(err, user) {
            if (err) {
            return done(err);
            }
            if (!user) {
            // "Auto-registration"
            users.push(profile);
            return done(null, profile);
            }
            return done(null, user);
        });
        });
    }
    ));
    ```
   Passport は、すべての戦略ライターが従うすべての戦略 (Twitter や Facebook など) に対して類似するパターンを使用します。 戦略を調べると、それは、パラメーターとして token と done を持つ関数が渡されることがわかります。 戦略は、その処理を終えると戻ってきます。 戦略が戻ったら、再度要求しなくてもいいように、ユーザーを保存し、トークンを隠します。

   > [!IMPORTANT]
   > 前のコードでは、サーバーに認証を求めたすべてのユーザーを受け入れています。 これは、自動登録と呼ばれます。 最初に、指定したプロセスを経て登録しない限り、どのユーザーにも運用サーバーに対する認証をさせないようにすることをお勧めします。 これは、コンシューマー アプリで通常見られるパターンです。たとえば、Facebook への登録は許可しても、その後で追加情報の提供を求めます。 これがサンプル アプリケーションでなければ、返されるトークン オブジェクトからユーザーの電子メールを抽出した後、そのユーザーに追加情報の入力を要求できます。 これはテスト サーバーなので、メモリ内データベースにユーザーを追加します。


4. 次に、Passport で必要な、サインインしているユーザーの追跡を可能にするメソッドを追加します。 これには、ユーザーの情報のシリアル化と逆シリアル化が含まれます。

    ```JavaScript
    // Passport session setup. (Section 2)

    //   To support persistent sign-in sessions, Passport needs to be able to
    //   serialize users into the session and deserialize them out of the session. Typically,
    //   this is done simply by storing the user ID when serializing and finding
    //   the user by ID when deserializing.
    passport.serializeUser(function(user, done) {
        done(null, user.email);
    });

    passport.deserializeUser(function(id, done) {
        findByEmail(id, function (err, user) {
            done(err, user);
        });
    });

    // array to hold signed-in users
    var users = [];

    var findByEmail = function(email, fn) {
        for (var i = 0, len = users.length; i < len; i++) {
            var user = users[i];
            log.info('we are using user: ', user);
            if (user.email === email) {
                return fn(null, user);
            }
        }
        return fn(null, null);
    };
    ```

5. 次に、Express エンジンを読み込むコードを追加します。 ここでは、Express が提供する既定の /views と /routes のパターンを使用します。

    ```JavaScript
    // configure Express (section 2)

      var app = express();
      app.configure(function() {
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.logger());
      app.use(express.methodOverride());
      app.use(cookieParser());
      app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
      app.use(bodyParser.urlencoded({ extended : true }));
      // Initialize Passport!  Also use passport.session() middleware, to support
      // persistent login sessions (recommended).
      app.use(passport.initialize());
      app.use(passport.session());
      app.use(app.router);
      app.use(express.static(__dirname + '/../../public'));
    });
    ```

6. 最後に、実際のサインイン要求を `passport-azure-ad` エンジンに渡すルートを追加します。

    ```JavaScript
    // Our Auth routes (section 3)

    // GET /auth/openid
    //   Use passport.authenticate() as route middleware to authenticate the
    //   request. The first step in OpenID authentication involves redirecting
    //   the user to their OpenID provider. After authenticating, the OpenID
    //   provider redirects the user back to this application at
    //   /auth/openid/return.
    app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
        log.info('Authentication was called in the Sample');
        res.redirect('/');
    });

    // GET /auth/openid/return
    //   Use passport.authenticate() as route middleware to authenticate the
    //   request. If authentication fails, the user is redirected back to the
    //   sign-in page. Otherwise, the primary route function is called,
    //   which, in this example, redirects the user to the home page.
    app.get('/auth/openid/return',
      passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        log.info('We received a return from AzureAD.');
        res.redirect('/');
      });

    // POST /auth/openid/return
    //   Use passport.authenticate() as route middleware to authenticate the
    //   request. If authentication fails, the user is redirected back to the
    //   sign-in page. Otherwise, the primary route function is called,
    //   which, in this example, redirects the user to the home page.
    app.post('/auth/openid/return',
      passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        log.info('We received a return from AzureAD.');
        res.redirect('/');
      });
    ```


## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>手順 4: Passport を使用してサインイン要求とサインアウト要求を Azure AD に発行する
OpenID Connect 認証プロトコルを使用してエンドポイントと通信するように、アプリが適切に構成されました。 `passport-azure-ad` により、認証メッセージの作成、Azure AD から受け取ったトークンの検証、ユーザー セッションの維持のためのすべての処理が実行されました。 あとは、サインインとサインアウトの方法をユーザーに示し、サインインしているユーザーに関する追加情報を収集するだけです。

1. まず、既定のメソッド、sign-in メソッド、account メソッド、sign-out メソッドを `app.js` ファイルに追加します。

    ```JavaScript
    //Routes (section 4)

    app.get('/', function(req, res){
      res.render('index', { user: req.user });
    });

    app.get('/account', ensureAuthenticated, function(req, res){
      res.render('account', { user: req.user });
    });

    app.get('/login',
      passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        log.info('Login was called in the Sample');
        res.redirect('/');
    });

    app.get('/logout', function(req, res){
      req.logout();
      res.redirect('/');
    });
    ```

2. これらについて詳しく説明しましょう。

  * `/` ルートは、index.ejs ビューにリダイレクトして、要求内のユーザーを渡します (存在する場合)。
  * `/account` ルートは、"*ユーザーが認証されていることを確認*" した後 (次で実装します)、ユーザーに関する追加情報を取得できるように、要求内のユーザーを渡します。
  * `/login` ルートは、`passport-azuread` から azuread-openidconnect authenticator を呼び出し、 これが成功しなかった場合は、ユーザーを /login にリダイレクトして戻します。
  * `/logout` ルートは、クッキーをクリアする logout.ejs (およびルート) を呼び出した後、ユーザーを index.ejs に返します。

3. `app.js` の最後の部分に、上記の `/account` で使用される **EnsureAuthenticated** メソッドを追加します。

    ```JavaScript
    // Simple route middleware to ensure user is authenticated. (section 4)

    //   Use this route middleware on any resource that needs to be protected. If
    //   the request is authenticated (typically via a persistent sign-in session),
    //   the request proceeds. Otherwise, the user is redirected to the
    //   sign-in page.
    function ensureAuthenticated(req, res, next) {
      if (req.isAuthenticated()) { return next(); }
      res.redirect('/login')
    }
    ```

4. 最後に、`app.js` でサーバー自体を作成します。

```JavaScript
app.listen(3000);
```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>手順 5: Web サイトにユーザーを表示するための Express のビューとルートを作成する
`app.js` が完成しました。 次に実行する必要があるのは、取得した情報をユーザーに表示するルートとビューを追加し、作成した `/logout` ルートと `/login` ルートを処理することだけです。

1. ルート ディレクトリの下に `/routes/index.js` ルートを作成します。

    ```JavaScript
    /*
     * GET home page.
     */

    exports.index = function(req, res){
      res.render('index', { title: 'Express' });
    };
    ```

2. ルート ディレクトリの下に `/routes/user.js` ルートを作成します。

    ```JavaScript
    /*
     * GET users listing.
     */

    exports.list = function(req, res){
      res.send("respond with a resource");
    };
    ```

 これらのルートは、要求とユーザー (存在する場合) をビューに渡します。

3. ルート ディレクトリの下に `/views/index.ejs` ビューを作成します。 これは、login メソッドと logout メソッドを呼び出し、アカウント情報を取得できるようにする単純なページです。 条件付きの `if (!user)` を使用できることに注目してください。要求でユーザーが渡されることは、サインインしているユーザーがいるという証拠です。

    ```JavaScript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
        <h2>Hello, <%= user.displayName %>.</h2>
        <a href="/account">Account Info</a></br>
        <a href="/logout">Log Out</a>
    <% } %>
    ```

4. ルート ディレクトリの下に `/views/account.ejs` ビューを作成し、`passport-azure-ad` がユーザー要求の中に配置された追加情報を表示できるようにします。

    ```Javascript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
    <p>displayName: <%= user.displayName %></p>
    <p>givenName: <%= user.name.givenName %></p>
    <p>familyName: <%= user.name.familyName %></p>
    <p>UPN: <%= user._json.upn %></p>
    <p>Profile ID: <%= user.id %></p>
  ##Next steps  <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

5. レイアウトを追加して、見やすくします。 ルート ディレクトリの下に `/views/layout.ejs` ビューを作成します。

    ```HTML

    <!DOCTYPE html>
    <html>
        <head>
            <title>Passport-OpenID Example</title>
        </head>
        <body>
            <% if (!user) { %>
                <p>
                <a href="/">Home</a> |
                <a href="/login">Log In</a>
                </p>
            <% } else { %>
                <p>
                <a href="/">Home</a> |
                <a href="/account">Account</a> |
                <a href="/logout">Log Out</a>
                </p>
            <% } %>
            <%- body %>
        </body>
    </html>
    ```

## <a name="next-steps"></a>次の手順
最後に、アプリをビルドして実行します。 `node app.js` を実行し、`http://localhost:3000` に移動します。

Microsoft の個人または職場/学校アカウントのいずれかでサインインすると、ユーザーの ID が /account リストにどのように反映されるかがわかります。 これで、Web アプリが業界標準のプロトコルで保護され、個人および職場/学校アカウントの両方でユーザーを認証できるようになりました。

参考のため、完成済みサンプル (構成値を除く) が [.zip ファイルとして提供されています](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/master.zip)。 または、GitHub から複製することもできます。

```git clone --branch master https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

これ以降は、さらに高度なトピックに進むことができます。 以下のトピックを参照してください。

[Azure AD を使用することによる Web API の保護](quickstart-v1-nodejs-webapi.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
