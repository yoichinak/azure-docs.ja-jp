---
title: Azure Application Insights でのデータ保持と保存 | Microsoft Docs
description: データ保持およびプライバシー ポリシー ステートメント
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: mbullwin
ms.openlocfilehash: 897671ef592ac691402a4e452f7a0baa04aa228a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129059"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Application Insights でのデータの収集、保持、保存


アプリに [Azure Application Insights][start] SDK をインストールすると、アプリに関するテレメトリがクラウドに送信されます。 当然ながら、担当開発者は、送信されるデータ、データに対して発生すること、それを制御する方法について知りたいと考えます。 特に、機密データの送信、その保存先、安全性が重要です。 

まず、手短に言えば次のようになります。

* 「難しい設定なしで」動く標準の製品利用統計情報モジュールは、多くの場合、機密データをサービスに送信しません。 製品利用統計情報は、負荷指標、パフォーマンス指標、利用率指標、例外レポート、その他の診断データに関連します。 診断レポートに表示される主なユーザー データは URL ですが、いかなる場合も、アプリは URL にプレーンテキストで機密データを入力するべきではありません。
* 診断と監視利用に役立つ追加のカスタム製品利用統計情報を送信するコードを記述できます。 (この拡張機能は Application Insights の優れた機能です。)手違いにより、このコードを記述し、個人データやその他の機密データが含まれてしまうことはあります。 アプリケーションがそのようなデータを利用する場合、記述するあらゆるコードに徹底したレビュー プロセスを適用してください。
* アプリを開発し、テストするとき、SDK により送信される内容は簡単に調査できます。 データは IDE とブラウザーのデバッグ出力ウィンドウに表示されます。 
* データは米国またはヨーロッパの [Microsoft Azure](http://azure.com) サーバーに保管されます。 (ただし、アプリは、場所を問わず実行できます)。Azure には[強力なセキュリティ プロセスがあり、広範囲のコンプライアンス標準を満たします](https://azure.microsoft.com/support/trust-center/)。 あなたとあなたが指名したチームだけがあなたのデータにアクセスできます。 Microsoft のスタッフには、特定の状況下でのみ、あなたに通知した上で限られたアクセスが与えられます。 転送中は暗号化されます。サーバーに保管されているときは暗号化されません。

この記事の残りの部分では、以上の答えについてもっと詳しく説明します。 設計は自己完結型であり、直近のチームに入っていない同僚にも見せることができます。

## <a name="what-is-application-insights"></a>Application Insights とは何か?
[Azure Application Insights][start] はライブ アプリケーションのパフォーマンスと使いやすさを改善する Microsoft 提供サービスです。 テスト中と公開後またはデプロイ後の両方で、実行中のアプリケーションを常時監視します。 Application Insights が作成するグラフや表を見ると、たとえば、1 日の中でユーザー数が最も多い時間帯、アプリの反応性、アプリが依存している外部サービスのサービス性能などがわかります。 クラッシュ、エラー、パフォーマンス問題が発生した場合、製品利用統計情報データを詳しく調査し、原因を診断できます。 アプリの可用性やパフォーマンスに変化があった場合、サービスからメールが届きます。

この機能を入手するには、アプリケーションに Application Insights SDK をインストールします。インストールすることでそのコードの一部になります。 アプリの実行中、SDK はその操作を監視し、製品利用統計情報を Application Insights サービスに送信します。 これは [Microsoft Azure](http://azure.com) がホストするクラウド サービスです。 (ただし、Application Insights は、Azure にホストされているアプリケーションだけでなく、あらゆるアプリケーションで動作します。)

![アプリの SDK は製品利用統計情報を Application Insights サービスに送信します。](./media/app-insights-data-retention-privacy/01-scheme.png)

Application Insights サービスは製品利用統計情報を保存し、分析します。 分析を確認し、保存されている製品利用統計情報を検索するには、Azure アカウントにサインインし、アプリケーションの Application Insights リソースを開きます。 データへのアクセスをチームの他のメンバーや指定した Azure サブスクライバーと共有することもできます。

Application Insights サービスから、たとえば、データベースや外部ツールにデータをエクスポートできます。 サービスから取得した特別なキーを各ツールに提供します。 キーは必要に応じて取り消すことができます。 

Application Insights SDK はさまざまなアプリケーション タイプに利用できます。独自の J2EE サーバー、ASP.NET サーバー、Azure にホストされている Web サービス、Web クライアント (Web ページで実行されるコード)、デスクトップのアプリとサービス、デバイス アプリ (Windows Phone、iOS、Android) などです。 これらはすべて同じサービスに製品利用統計情報を送信します。

## <a name="what-data-does-it-collect"></a>どのようなデータが収集されますか。
### <a name="how-is-the-data-is-collected"></a>データはどのような方法で収集されますか。
データ ソースは 3 つあります。

* SDK は[開発時](app-insights-asp-net.md)または[実行時](app-insights-monitor-performance-live-website-now.md)にアプリと統合します。 アプリケーションの種類が違えば SDK も違います。 [Web ページ用の SDK](app-insights-javascript.md) もあります。ページと共にエンドユーザーのブラウザーに読み込まれます。
  
  * それぞれの SDK にはさまざまな [モジュール](app-insights-configuration-with-applicationinsights-config.md)があり、さまざまな手法でさまざまな種類の製品利用統計情報を収集します。
  * デプロイ時に SDK をインストールする場合、標準のモジュールに加え、その API を利用し、独自の製品利用統計情報を送信できます。 このカスタム製品利用統計情報にあらゆるデータを含め、送信できます。
* 一部の Web サーバーには、アプリと共に実行され、CPU、メモリ、ネットワーク占有率に関する製品利用統計情報を送信するエージェントもあります。 たとえば、Azure VM、Docker ホスト、 [J2EE サーバー](app-insights-java-agent.md) にそのようなエージェントがあります。
* [可用性テスト](app-insights-monitor-web-app-availability.md) は Microsoft が実行するプロセスであり、定期的な間隔で要求を Web アプリに送信します。 結果は Application Insights サービスに送信されます。

### <a name="what-kinds-of-data-are-collected"></a>どのような種類のデータが収集されますか。
主なカテゴリは次のとおりです。

* [Web サーバー製品利用統計情報](app-insights-asp-net.md) -HTTP 要求。  URI、要求の処理にかかる時間、応答コード、クライアント IP アドレス。 セッション ID。
* [Web ページ](app-insights-javascript.md) - ページ、ユーザーとセッションの数。 ページの読み込み時間。 例外。 AJAX 呼び出し。
* パフォーマンス カウンター - メモリ、CPU、IO、ネットワーク占有率。
* クライアントとサーバーのコンテキスト - OS、ロケール、デバイスの種類、ブラウザー、画面の解像度。
* [例外](app-insights-asp-net-exceptions.md) とクラッシュ - **スタック ダンプ**、ビルド ID、CPU タイプ。 
* [依存関係](app-insights-asp-net-dependencies.md) - REST、SQL、AJAX など、外部サービスの呼び出し。 URI または接続文字列、期間、成功、コマンド。
* [可用性テスト](app-insights-monitor-web-app-availability.md) - テストとステップの期間、応答。
* [トレース ログ](app-insights-asp-net-trace-logs.md)と[カスタム テレメトリ](app-insights-api-custom-events-metrics.md) - **コード化してログまたはテレメトリに入れるすべて**。

[詳細](#data-sent-by-application-insights)。

## <a name="how-can-i-verify-whats-being-collected"></a>収集された内容はどのような方法で検証しますか。
Visual Studio でアプリを開発している場合、デバッグ モードでアプリを実行してください (F5)。 製品利用統計情報は出力ウィンドウに表示されます。 そこから、それをコピーし、JSON として書式設定すれば、調査が簡単になります。 

![](./media/app-insights-data-retention-privacy/06-vs.png)

[診断] ウィンドウには、さらに読みやすいビューもあります。

Web ページの場合、ブラウザーのデバッグ ウィンドウを開きます。

![F12 を押し、[ネットワーク] タブを開きます。](./media/app-insights-data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>送信前に製品利用統計情報を絞り込むコードを記述できますか。
[製品利用統計情報プロセッサ プラグイン](app-insights-api-filtering-sampling.md)を記述することで可能です。

## <a name="how-long-is-the-data-kept"></a>データはどれだけの期間保持されますか。
生データ ポイント (つまり、Analytics でクエリを実行したり Search で調べることができる項目) は、最大 90 日間保持されます。 それ以上長くデータを保持する必要がある場合は、 [連続エクスポート](app-insights-export-telemetry.md) を使用してストレージ アカウントにコピーすることができます。

集計されたデータ (つまり、メトリックス エクスプローラーに表示されるカウント、平均、その他の統計データ) は、1 分の詳細度であれば 90 日の期間にわたって保持されます。

## <a name="who-can-access-the-data"></a>誰がデータにアクセスできますか。
お客様と、組織アカウントを持っている場合はチーム メンバーが、データを見ることができます。 

お客様とチーム メンバーはデータをエクスポートできます。また、他の場所にデータをコピーしたり、第三者にデータを渡したりすることもできます。

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>アプリから Application Insights に送信された情報を Microsoft はどのように利用しますか。
Microsoft は、お客様にサービスを提供する目的でのみデータを使用します。

## <a name="where-is-the-data-held"></a>データが保持されている場所はどこですか。
* 米国、ヨーロッパ、または東南アジアです。 新しい Application Insights リソースを作成するときに場所を選択できます。 


#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>それは、アプリを米国、ヨーロッパ、または東南アジアでホストする必要があるという意味ですか。
* いいえ。 アプリは、独自のオンプレミスのホストでもクラウドでも、場所を問わず実行できます。

## <a name="how-secure-is-my-data"></a>データのセキュリティは保たれますか。
Application Insights は Azure サービスのひとつです。 セキュリティ ポリシーについては、[Azure のセキュリティ、プライバシー、およびコンプライアンスに関するホワイト ペーパー](http://go.microsoft.com/fwlink/?linkid=392408)をご覧ください。

データは、Microsoft Azure サーバーに保管されます。 Azure Portal のアカウントの場合、アカウントの制限は [Azure のセキュリティ、プライバシー、コンプライアンスの文書](http://go.microsoft.com/fwlink/?linkid=392408)に記載されています。

Microsoft のスタッフによるデータへのアクセスは制限されます。 Microsoft は、Application Insights の使用をサポートするために必要であれば、ユーザーからアクセス許可を得た上でデータにアクセスします。 

このほか、Application Insights の機能の向上に役立てるために、すべてのお客様のアプリケーションのデータ (データ レート、トレースの平均サイズなど) を集計のうえ使用します。

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>だれかのテレメトリが Application Insights データに干渉する可能性はありますか。
Web ページのコード内にインストルメンテーション キーがある場合には、だれかがそれを使用してお客様のアカウントに追加のテレメトリを送信することができます。 追加データの量が多いと、アプリのパフォーマンスと使用状況がメトリックに適切に示されなくなる可能性があります。

コードを他のプロジェクトと共有する場合は、インストルメンテーション キーを必ず削除してください。

## <a name="is-the-data-encrypted"></a>データは暗号化されますか。
現時点で、サーバー内では暗号化されません。

データをデータ センター間で移動するときは、すべてのデータが暗号化されます。

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>アプリケーションから Application Insights サーバーに送信されるときにデータは暗号化されますか。
はい。ポータルからほぼすべての SDK (Web サーバー、デバイス、HTTPS Web ページを含みます) への送信に https が使用されます。 唯一の例外は、プレーンな HTTP Web ページから送信されるデータです。

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>TLS 1.2 を使用して Application Insight にデータを送信するにはどうすればよいですか。

Application Insight エンドポイントへのデータの転送時のセキュリティを保証するため、少なくとも Transport Layer Security (TLS) 1.2 を使用するようにアプリケーションを構成することを強くお勧めします。 以前のバージョンの TLS/SSL (Secure Sockets Layer) は脆弱であることが確認されています。現在、これらは下位互換性を維持するために使用可能ですが、**推奨されていません**。さらに、業界はこれらの以前のプロトコルのサポートを中止する方向へ急速に動いています。 

[PCI Security Standards Council](https://www.pcisecuritystandards.org/) は、[2018 年 6 月 30 日を期限として](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf)、TLS/SSL の以前のバージョンを無効にし、より安全なプロトコルにアップグレードすることを求めています。 Azure がレガシー サポートを廃止した場合、アプリケーション/クライアントが TLS 1.2 以上で通信できないと Application Insight にデータを送信できなくなります。 アプリケーションの TLS のサポートをテストおよび検証する方法は、オペレーティング システム/プラットフォームのほか、アプリケーションで使用する言語/フレームワークによって異なります。

アプリケーションで TLS 1.2 のみを使用するように明示的に設定することは、絶対に必要な場合を除いてお勧めしません。なぜなら、そうすることで、TLS 1.3 などのより新しいよくより安全なプロトコルを自動的に検出して利用できるようにするプラットフォーム レベルのセキュリティ機能が無効になる可能性があるためです。 アプリケーションのコードを徹底的に監査して、特定の TLS/SSL バージョンのハードコーディングを確認することをお勧めします。

### <a name="platformlanguage-specific-guidance"></a>プラットフォーム/言語に固有のガイダンス

|プラットフォーム/言語 | サポート | 詳細情報 |
| --- | --- | --- |
| Azure App Service  | サポートされています。構成が必要な場合があります。 | サポートは 2018 年 4 月に発表されました。 [構成の詳細](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/)のお知らせを参照してください。  |
| Azure Function App | サポートされています。構成が必要な場合があります。 | サポートは 2018 年 4 月に発表されました。 [構成の詳細](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/)のお知らせを参照してください。 |
|.NET | サポートされています。構成はバージョンによって異なります。 | .NET 4.7 およびそれ以前のバージョンの詳細な構成情報については、[これらの手順](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12)を参照してください。  |
|Status Monitor | サポートされています。構成が必要です | Status Monitor は、TLS 1.2 をサポートするために [OS 構成](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings) + [.NET 構成](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12)に依存します。
|Node.js |  サポートされています。v10.5.0 では構成が必要な場合があります。 | アプリケーションに固有の構成については、[公式の Node.js TLS/SSL ドキュメント](https://nodejs.org/api/tls.html)を使用してください。 |
|Java | サポートされています。JDK の TLS 1.2 のサポートは、[JDK 6 更新プログラム 121](http://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) および [JDK 7](http://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html) で追加されました。 | JDK 8 では、[既定で TLS 1.2](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default) が使用されます。  |
|Linux | Linux ディストリビューションでは、TLS 1.2 のサポートに関して [OpenSSL](https://www.openssl.org) に依存する傾向があります。  | [OpenSSL の Changelog](https://www.openssl.org/news/changelog.html) を参照して、使用している OpenSSL のバージョンがサポートされていることを確認してください。|
| Windows 8.0 - 10 | サポートされています。既定で有効になっています。 | [既定の設定](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings)を使用していることを確認するには。  |
| Windows Server 2012 - 2016 | サポートされています。既定で有効になっています。 | [既定の設定](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings)を使用していることを確認するには |
| Windows 7 SP1 および Windows Server 2008 R2 SP1 | サポートされていますが、既定では有効になっていません。 | 有効にする方法の詳細については、「[トランスポート層セキュリティ (TLS) のレジストリ設定](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings)」を参照してください。  |
| Windows Server 2008 SP2 | TLS 1.2 のサポートには、更新プログラムが必要です。 | Windows Server 2008 SP2 に [TLS 1.2 のサポートを追加する更新プログラム](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s)に関するページを参照してください。 |
|Windows Vista | サポートされていません。 | 該当なし

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Linux ディストリビューションで実行されている OpenSSL のバージョンを確認する

インストールされている OpenSSL のバージョンを確認するには、ターミナルを開き、次のコマンドを実行します。

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Linux 上でテスト TLS 1.2 トランザクションを実行する

Linux システムが TLS 1.2 で通信できるかどうかを確認する基本的な予備テストを実行するには、 ターミナルを開き、次のコマンドを実行します。

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Application Insights に格納される個人データ

この件については、[Application Insights の個人データに関する記事](app-insights-customer-data.md)に詳しく説明されています。

#### <a name="can-my-users-turn-off-application-insights"></a>ユーザーは Application Insights を無効にできますか。
直接無効にすることはできません。 ユーザーが Application Insights を無効にするために操作できるスイッチはありません。

ただし、アプリケーションでそのような機能を実装することはできます。 すべての SDK には、テレメトリの収集を無効にする API 設定が含まれています。 

## <a name="data-sent-by-application-insights"></a>Application Insights によって送信されるデータ
SDK はプラットフォームごとに異なり、インストールできるコンポーネントは複数あります  ([Application Insights の概要][start]に関するページをご覧ください)。各コンポーネントは、それぞれ異なるデータを送信します。

#### <a name="classes-of-data-sent-in-different-scenarios"></a>さまざまなシナリオで送信されるデータのクラス
| 操作 | 収集されるデータのクラス (次の表を参照) |
| --- | --- |
| [Application Insights SDK を .NET Web プロジェクトに追加する][greenbrown] |ServerContext<br/>Inferred<br/>Perf counters<br/>Requests<br/>**Exceptions**<br/>Session<br/>users |
| [Status Monitor を IIS にインストールする][redfield] |依存関係<br/>ServerContext<br/>Inferred<br/>Perf counters |
| [Application Insights SDK を Java Web アプリに追加する][java] |ServerContext<br/>Inferred<br/>要求<br/>Session<br/>users |
| [JavaScript SDK を Web ページに追加する][client] |ClientContext  <br/>Inferred<br/>ページ<br/>ClientPerf<br/>Ajax |
| [既定のプロパティを定義する][apiproperties] |**Properties** (すべての標準イベントおよびカスタム イベント) |
| [TrackMetric を呼び出す][api] |数値<br/>**Properties** |
| [Track* を呼び出す][api] |イベント名<br/>**Properties** |
| [TrackException を呼び出す][api] |**Exceptions**<br/>Stack dump<br/>**Properties** |
| SDK はデータを収集できません。 例:  <br/> - パフォーマンス カウンターにアクセスできない<br/> - テレメトリ初期化子で例外が発生した |SDK diagnostics |

[他のプラットフォームの SDK][platforms] については、該当するドキュメントを参照してください。

#### <a name="the-classes-of-collected-data"></a>収集されるデータのクラス
| 収集されるデータのクラス | 含まれるデータ (網羅的なリストではありません) |
| --- | --- |
| **Properties** |**コードによって決まる任意のデータ** |
| DeviceContext |Id、IP、ロケール、デバイス モデル、ネットワーク、ネットワークの種類、OEM の名前、画面解像度、ロール インスタンス、ロール名、デバイスの種類 |
| ClientContext  |OS、ロケール、言語、ネットワーク、ウィンドウの解像度 |
| Session |セッション ID |
| ServerContext |コンピューター名、ロケール、OS、デバイス、ユーザー セッション、ユーザー コンテキスト、操作 |
| Inferred |IP アドレス、タイムスタンプ、OS、ブラウザーからの geo ロケーション |
| メトリック |メトリックの名前と値 |
| イベント |イベントの名前と値 |
| PageViews |URL とページ名または画面名 |
| Client perf |URL/ページ名、ブラウザーの読み込み時間 |
| Ajax |Web ページからサーバーへの HTTP 呼び出し |
| Requests |URL、期間、応答コード |
| 依存関係 |種類 (SQL、HTTP、...)、接続文字列または URI、同期または非同期、期間、成功、SQL ステートメント (Status Monitor による) |
| **Exceptions** |種類、 **メッセージ**、呼び出し履歴、ソース ファイルと行の番号、スレッド ID |
| Crashes |プロセス ID、親プロセスの ID、クラッシュ スレッドの ID。アプリケーションの修正プログラム、ID、ビルド。例外の種類、アドレス、理由。難読化されたシンボルとレジスタ、バイナリの開始アドレスと終了アドレス、バイナリ名とパス、CPU の種類 |
| Trace |**メッセージ** と重大度レベル |
| Perf counters |プロセッサ時間、使用可能なメモリ、要求レート、例外レート、プロセスのプライベート バイト、IO レート、要求の期間、要求のキューの長さ |
| 可用性 |Web テストの応答コード、各テスト ステップの期間、テスト名、タイムスタンプ、成功、応答時間、テストの場所 |
| SDK diagnostics |トレース メッセージまたは例外 |

[ApplicationInsights.config を編集して、データの一部を無効化][config]できます

## <a name="credits"></a>謝辞
この製品には、MaxMind によって作成された GeoLite2 データが含まれています。MaxMind は [http://www.maxmind.com](http://www.maxmind.com) から入手できます。



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

