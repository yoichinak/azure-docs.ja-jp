---
title: Azure Log Analytics の IIS ログ | Microsoft Docs
description: インターネット インフォメーション サービス (IIS) は、Log Analytics によって収集されるログ ファイルにユーザーの利用状況を格納します。  この記事では、IIS ログの収集を構成する方法と Log Analytics ワークスペースに作成されるレコードの詳細について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: bwren
ms.comopnent: na
ms.openlocfilehash: 65320e7d3cc97a3d53fd1a00fbbeab5559c02fce
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133550"
---
# <a name="iis-logs-in-log-analytics"></a>Log Analytics の IIS ログ
インターネット インフォメーション サービス (IIS) は、Log Analytics によって収集されるログ ファイルにユーザーの利用状況を格納します。  

![IIS ログ](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>IIS ログの構成
Log Analytics は IIS によって作成されたログ ファイルからエントリを収集するため、[ログを収集するように IIS を構成](https://technet.microsoft.com/library/hh831775.aspx)する必要があります。

Log Analytics でサポートされるのは、W3C 形式で保存された IIS ログ ファイルのみです。カスタム フィールドや IIS 詳細ログはサポートされません。  
NCSA 形式や IIS のネイティブ形式のログは、Log Analytics によって収集されません。

Log Analytics の IIS ログは、[Log Analytics の [設定] の [データ] メニュー](log-analytics-data-sources.md#configuring-data-sources)から構成します。  必要な構成は、 **[Collect W3C format IIS log files]**(W3C 形式の IIS ログ ファイルを収集する) を選択することのみです。


## <a name="data-collection"></a>データ収集
Log Analytics は、ログが閉じられ、新しいログが作成されるたびに、各エージェントから IIS ログ エントリを収集します。 この頻度は、IIS サイトの **[Log File Rollover Schedule]\(ログ ファイルのロールオーバー スケジュール\)** 設定によって制御されます。この設定の既定値は 1 日に 1 回です。 たとえば、設定が **[毎時]** の場合、Log Analytics は毎時間ログを収集します。  たとえば、設定が **[日単位]** の場合、Log Analytics は 24 時間ごとにログを収集します。


## <a name="iis-log-record-properties"></a>IIS ログ レコードのプロパティ
IIS ログ レコードの型は **W3CIISLog** になり、次の表に示すプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| Computer |イベント収集元のコンピューターの名前。 |
| cIP |クライアントの IP アドレス。 |
| csMethod |要求のメソッド (GET、POST など) |
| csReferer |現在のサイトに到達するまでにユーザーがリンクをフォローした元のサイト |
| csUserAgent |クライアントのブラウザーの種類。 |
| csUserName |サーバーにアクセスした認証されたユーザーの名前。 匿名ユーザーはハイフンで示されます。 |
| csUriStem |要求のターゲット (Web ページなど) |
| csUriQuery |クライアントが実行を試行していたクエリ (存在する場合) |
| ManagementGroupName |Operations Manager エージェントの管理グループの名前。  その他のエージェントの場合、これは AOI-\<workspace ID\> です。 |
| RemoteIPCountry |クライアントの IP アドレスの国。 |
| RemoteIPLatitude |クライアントの IP アドレスの緯度。 |
| RemoteIPLongitude |クライアントの IP アドレスの経度。 |
| scStatus |HTTP 状態コード。 |
| scSubStatus |副状態エラー コード。 |
| scWin32Status |Windows 状態コード。 |
| sIP |Web サーバーの IP アドレス。 |
| SourceSystem |OpsMgr |
| sPort |クライアントが接続されているサーバー上のポート。 |
| sSiteName |IIS サイトの名前。 |
| TimeGenerated |エントリがログに記録された日付と時刻。 |
| TimeTaken |要求の処理にかかった時間 (ミリ秒) |

## <a name="log-searches-with-iis-logs"></a>IIS ログのログ検索
次の表は、IIS ログ レコードを取得するログ クエリのさまざまな例をまとめたものです。

| クエリ | 説明 |
|:--- |:--- |
| W3CIISLog |IIS ログのすべてのレコード。 |
| W3CIISLog &#124; where scStatus==500 |戻り状態が 500 であるすべての IIS ログ レコード。 |
| W3CIISLog &#124; summarize count() by cIP |クライアントの IP アドレス別の IIS ログ エントリの数。 |
| W3CIISLog &#124; where csHost=="www.contoso.com" &#124; summarize count() by csUriStem |ホスト www.contoso.com の URL 別の IIS ログ エントリの数。 |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |各 IIS コンピューターによって受信された合計バイト数。 |

## <a name="next-steps"></a>次の手順
* 分析のために別の [データ ソース](log-analytics-data-sources.md) を収集するように Log Analytics を構成します。
* [ログ検索](log-analytics-log-searches.md) について学習し、データ ソースとソリューションから収集されたデータを分析します。
* IIS ログで検出された重要な状態を事前に通知するように、Log Analytics のアラートを構成します。
