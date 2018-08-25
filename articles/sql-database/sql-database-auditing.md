---
title: Azure SQL Database 監査の使用 | Microsoft Docs
description: Azure SQL Database 監査を使用して、データベースイベントを追跡し、監査ログに書き込みます。
services: sql-database
author: giladmit
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 06/24/2018
ms.author: giladm
ms.openlocfilehash: f187a5fe1541f5508e55443abe80fc295ee63c87
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081457"
---
# <a name="get-started-with-sql-database-auditing"></a>SQL Database 監査の使用
Azure SQL Database 監査では、データベース イベントを追跡し、Azure ストレージ アカウントの監査ログにイベントを書き込みます。 また、監査によって以下を行うことができます。

* 規定コンプライアンスの維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。

* コンプライアンスを保証するものではありませんが、標準へのコンプライアンスを強化します。 標準準拠をサポートする Azure プログラムの詳細については、 [Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/compliance/)のページを参照してください。


## <a id="subheading-1"></a>Azure SQL Database 監査の概要
SQL Database 監査を使用して、以下を行うことができます。


* **保持** 。 監査するデータベース活動のカテゴリを定義できます。
* **レポート** 。 事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
* **分析** 。 疑わしいイベント、異常な活動、および傾向を発見できます。

「[データベースに対する監査を設定する](#subheading-2)」セクションの説明にあるように、さまざまな種類のイベント カテゴリの監査を構成できます。

> [!IMPORTANT]
> 監査ログは Azure サブスクリプションの Azure Blob Storage 内にある**追加 BLOB** に書き込まれます。
>
> * **Premium Storage** は現在、追加 BLOB では**サポートされていません**。
> * **VNet 内の Storage** は現在**サポートされていません**。

## <a id="subheading-8"></a>サーバー レベルおよびデータベース レベルの監査ポリシーを定義する

特定のデータベースに対して、または既定のサーバー ポリシーとして、監査ポリシーを定義できます。

* サーバー ポリシーがサーバー上にある既存と新規作成のすべてのデータベースに適用されます。

* "*サーバー BLOB 監査が有効*" な場合は、"*常にデータベースに適用*" されます。 データベース監査設定に関係なく、データベースが監査されます。

* サーバー上だけでなくデータベース上でも BLOB 監査を有効にした場合であっても、サーバーの BLOB 監査の設定がオーバーライドされたり変更されたりすることは "*ありません*"。 どちらの監査も並行して存在します。 つまり、データベースは並行して 2 回監査されることになります (1 回はサーバー ポリシー、もう 1 回はデータベース ポリシーによって監査されます)。

   > [!NOTE]
   > 次の場合を除き、サーバー BLOB 監査とデータベース BLOB 監査の両方を有効にすることは避けてください。
    > * 特定のデータベースに対して異なる "*ストレージ アカウント*" または "*リテンション期間*" を使用する場合。
    > * 特定のデータベースの監査対象とするイベントの種類またはカテゴリが、このサーバー上の他のデータベースの管理対象と異なる場合。 たとえば、特定のデータベースに対してのみ監査が必要なテーブルの挿入がある場合など。
   >
   > これらに該当しない場合は、サーバー レベルの BLOB 監査のみを有効にし、すべてのデータベースに対してデータベース レベルの監査を無効にすることをお勧めします。


## <a id="subheading-2"></a>データベースに対する監査を設定する
以下のセクションでは、Azure Portal を使用した監査の構成について説明します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. SQL データベース/サーバー ペインの [セキュリティ] 見出しの下にある **[監査]** に移動します。

    <a id="auditing-screenshot"></a> ![ナビゲーション ウィンドウ][1]
3. サーバーの監査ポリシーを設定する場合は、データベース監査ブレードの **[サーバー設定を表示]** リンクを選択できます。 そうすると、サーバー監査設定を表示または修正することができます。 サーバー監視ポリシーは、このサーバー上にある既存のデータベースと新規作成されたデータベースのすべてに適用されます。

    ![ナビゲーション ウィンドウ][2]
4. データベース レベルで監査を有効にする場合は、**[監査]** を  **[ON]\(オン\)** に切り替えます。

    サーバーの監査が有効になっている場合、データベース構成監査とサーバー監査が並行して存在します。

    ![ナビゲーション ウィンドウ][3]
5. **[監査ログの容量]** ブレードを開くには、**[容量の詳細]** を選択します。 ログを保存する Azure ストレージ アカウントを選択し、リテンション期間を選択します。 古いログは削除されます。 次に、 **[OK]** をクリックします

    <a id="storage-screenshot"></a> ![ナビゲーション ウィンドウ][4]
6. 監査対象イベントをカスタマイズする場合は、[PowerShell コマンドレット](#subheading-7)または [REST API](#subheading-9) を使用して行います。
7. 監査設定を構成した後に、新しい脅威の検出機能をオンにし、電子メールを構成してセキュリティの警告を受信します。 脅威の検出を使用すると、セキュリティ上の脅威になる可能性がある異常なデータベース アクティビティに対するプロアクティブ アラートを受信できます。 詳細については、[脅威の検出の概要](sql-database-threat-detection-get-started.md)に関するページを参照してください。
8. **[Save]** をクリックします。





## <a id="subheading-3"></a>監査ログとレポートを分析する
監査ログは、設定時に選択した Azure ストレージ アカウントで集計されます。 [Azure ストレージ エクスプローラー](http://storageexplorer.com/)などのツールを使用して監査ログを調査できます。

BLOB 監査ログは **sqldbauditlogs** という名前のコンテナー内に BLOB ファイルのコレクションとして保存されます。

ストレージ フォルダーの階層、命名規則、およびログ形式の詳細については、[BLOB 監査ログ形式のリファレンス](https://go.microsoft.com/fwlink/?linkid=829599)を参照してください。

BLOB 監査ログを表示するには、いくつかの方法が使用できます。

* [Azure Portal](https://portal.azure.com) を使用します。  関連するデータベースを開きます。 データベースの **[監査と脅威検出]** ブレードの上部にある **[監査ログの表示]** をクリックします。

    ![ナビゲーション ウィンドウ][7]

    **[監査レコード]** ブレードが開きます。ここからログを参照できます。

    - **[監査レコード]** ブレードの上部にある **[フィルター]** をクリックすると、特定の日付を表示できます。
    - **[監査対象]** を切り替えると、"*サーバー監視ポリシー*" で作成された監査レコードと "*データベース監査ポリシー*" で作成された監査レコードを切り替えることができます。
    - **[Show only audit records for SQL injections]\(SQL インジェクションの監査レコードのみを表示する\)** チェックボックスをオンにすると、SQL インジェクション関連の監査レコードのみを表示できます。

       ![ナビゲーション ウィンドウ][8]

* システム関数 **sys.fn_get_audit_file** (T-SQL) を使用して、表形式で監査ログ データを返します。 この関数の使用方法の詳細については、[sys.fn_get_audit_file のドキュメント](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)を参照してください。


* SQL Server Management Studio (SSMS 17 以降) で **[監査ファイルの統合]** を使用します。
    1. SSMS のメニューから、**[ファイル]** > **[開く]** > **[監査ファイルの統合]** を選択します。

        ![ナビゲーション ウィンドウ][9]
    2. **[監査ファイルの追加]** ダイアログ ボックスが表示されます。 **[追加]** オプションのいずれかを選択して、ローカル ディスクから監査ファイルをマージするか、Azure Storage からインポートするかを選択します。 Microsoft Azure Storage の詳細とアカウント キーを提供する必要があります。

    3. 統合するすべてのファイルを追加した後に、**[OK]** をクリックして統合の操作を完了します。

    4. 統合されたファイルを SSMS で開くと、ファイルを表示および分析し、XEL または CSV ファイルまたはテーブルにエクスポートすることができます。

* 作成した[同期アプリケーション](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)を使用します。 アプリケーションは Azure で実行され、Log Analytics のパブリック API を使用して SQL 監査ログを Log Analytics にプッシュします。 同期アプリケーションは Log Analytics ダッシュボード経由で、Log Analytics へ SQL 監査ログをプッシュして使用します。

* Power BI を使用します。 Power BI で監査ログのデータを表示および分析できます。 [Power BI の詳細を確認し、ダウンロード可能なテンプレートにアクセスします](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/)。

* ポータル経由で、あるいは [Azure ストレージ エクスプローラー](http://storageexplorer.com/)などのツールを利用して Azure Storage BLOB コンテナーからログ ファイルをダウンロードします。
    * ログ ファイルをローカルでダウンロードした後に、ファイルをダブルクリックし、SSMS でログを開き、表示し、分析できます。
    * また、Azure ストレージ エクスプ ローラーを使用して、同時に複数のファイルをダウンロードすることもできます。 特定のサブフォルダーを右クリックし、**[名前を付けて保存]** を選択してローカル フォルダーに保存します。

* 他の方法:
   * 複数のファイルまたはログ ファイルが含まれるサブフォルダーをダウンロードした後、前述の SSMS 監査ファイルの統合の指示に従って、ローカルでマージすることができます。

   * BLOB 監査ログをプログラムで表示します。

     * [拡張イベント リーダー](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) (C# ライブラリ) を使用します。
     * PowerShell を使用して[拡張イベント ファイルにクエリを実行します](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)。




## <a id="subheading-5"></a>運用方法
<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">geo レプリケーション対応データベースの監査</a>
Geo レプリケーション データベースでは、プライマリ データベースの監査を有効にすると、セカンダリ データベースにも同一の監査ポリシーが適用されます。 プライマリ データベースとは別に**セカンダリ サーバー**で監査を有効にすることで、セカンダリ データベースに対する監査を設定することもできます。

* サーバーレベル (**推奨**): **プライマリ サーバー**と**セカンダリ サーバー**の両方で監査を有効にします。プライマリ データベースとセカンダリ データベースは、それぞれのサーバーレベル ポリシーに基づいて個別に監査されます。

* データベースレベル: セカンダリ データベースのデータベースレベルの監査は、プライマリ データベースの監査設定から構成する必要があります。
   * 監査は、サーバーではなく "*プライマリ データベース自体*" で有効にする必要があります。
   * プライマリ データベースで監査を有効にすると、セカンダリ データベースでも有効になります。

    >[!IMPORTANT]
    >データベースレベルの監査では、セカンダリ データベースのストレージ設定はプライマリ データベースと同じになるため、リージョンをまたいだトラフィックが発生します。 サーバー レベルの監査のみを有効にし、すべてのデータベースでデータベース レベルの監査を無効なままにしておくことをお勧めします。
<br>

### <a id="subheading-6">ストレージ キーの再生成</a>
運用環境では、ストレージ キーを最新の情報に定期的に更新することが推奨されます。 キーを最新の情報に更新する場合は、監査ポリシーを再度保存する必要があります。 このプロセスは次のとおりです。

1. **[容量の詳細]** ブレードを開きます。 **[ストレージ アクセス キー]** ボックスで **[セカンダリ]** をクリックし、**[OK]** をクリックします。 監査構成ブレードの上部にある **[保存]** をクリックします。

    ![ナビゲーション ウィンドウ][5]
2. ストレージ構成ブレードに移動し、プライマリ アクセス キーを再生成します。

    ![ナビゲーション ウィンドウ][6]
3. 監査構成ブレードに戻り、[ストレージ アクセス キー] を [セカンダリ] から [プライマリ] に切り替え、**[OK]** をクリックします。 監査構成ブレードの上部にある **[保存]** をクリックします。
4. ストレージ構成ブレードに戻り、セカンダリ アクセス キーを再生成 (次のキー更新サイクルの準備として) します。

## <a name="additional-information"></a>追加情報

* ログの形式、ストレージ フォルダーの階層、および命名規則の詳細については、[BLOB 監査ログ形式のリファレンス](https://go.microsoft.com/fwlink/?linkid=829599)に関するドキュメントを参照してください。

    > [!IMPORTANT]
    > Azure SQL Database Audit では、監査レコードの文字列フィールドに 4,000 文字のデータを格納します。 監査可能なアクションから返された、**statement** または **data_sensitivity_information** 値に 4,000 を超える文字が含まれる場合、最初の 4,000 文字以降のすべてのデータは、**切り捨てられ、監査されません**。

* 監査ログは Azure サブスクリプションの Azure Blob Storage 内にある**追加 BLOB** に書き込まれます。
    * **Premium Storage** は現在、追加 BLOB では**サポートされていません**。
    * **VNet 内の Storage** は現在**サポートされていません**。

* 既定の監査ポリシーには、すべてのアクションと次のアクション グループのセットが含まれます。これは、データベースに対して実行されたすべてのクエリとストアド プロシージャに加えて、成功および失敗したログインを監査します。

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    「[Azure PowerShell を使用して SQL Database の監査を管理する](#subheading-7)」セクションで説明されているように、PowerShell を使用してさまざまな種類のアクションおよびアクション グループの監査を構成することができます。

## <a id="subheading-7"></a>Azure PowerShell を使用して SQL Database の監査を管理する

**PowerShell コマンドレット**:

* [データベース BLOB 監査ポリシーを作成または更新する (Set-AzureRMSqlDatabaseAuditing)][105]
* [サーバー BLOB 監査ポリシーを作成または更新する (Set-AzureRMSqlServerAuditing)][106]
* [データベース監査ポリシーを取得する (Get-AzureRMSqlDatabaseAuditing)][101]
* [サーバー BLOB 監査ポリシーを取得する (Get-AzureRMSqlServerAuditing)][102]

スクリプトの例については、[PowerShell を使用した監査と脅威検出の構成](scripts/sql-database-auditing-and-threat-detection-powershell.md)に関するページを参照してください。

## <a id="subheading-9"></a>REST API を使用して SQL Database の監査を管理する

**REST API - BLOB 監査**:

* [データベース BLOB 監査ポリシーの作成または更新](https://docs.microsoft.com/en-us/rest/api/sql/database%20auditing%20settings/createorupdate)
* [サーバー BLOB 監査ポリシーの作成または更新](https://docs.microsoft.com/en-us/rest/api/sql/server%20auditing%20settings/createorupdate)
* [データベース BLOB 監査ポリシーの取得](https://docs.microsoft.com/en-us/rest/api/sql/database%20auditing%20settings/get)
* [サーバー BLOB 監査ポリシーの取得](https://docs.microsoft.com/en-us/rest/api/sql/server%20auditing%20settings/get)

WHERE 句のサポートによってフィルタリングを強化した拡張ポリシー:
* [データベース "*拡張*" BLOB 監査ポリシーの作成または更新](https://docs.microsoft.com/en-us/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
* [サーバー "*拡張*" BLOB 監査ポリシーの作成または更新](https://docs.microsoft.com/en-us/rest/api/sql/server%20extended%20auditing%20settings/createorupdate)
* [データベース "*拡張*" BLOB 監査ポリシーの取得](https://docs.microsoft.com/en-us/rest/api/sql/database%20extended%20auditing%20settings/get)
* [サーバー "*拡張*" BLOB 監査ポリシーの取得](https://docs.microsoft.com/en-us/rest/api/sql/server%20extended%20auditing%20settings/get)

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png

[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditing
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditing
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditing
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditing
