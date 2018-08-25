---
title: アプリケーション プロキシのアプリケーション読み込みに時間がかかりすぎる | Microsoft Docs
description: Azure AD アプリケーション プロキシによるページ読み込みパフォーマンスの問題をトラブルシューティングする
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: c78abf05fb28b0370e17107deccd46259df47c47
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39367064"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>アプリケーション プロキシのアプリケーション読み込みに時間がかかりすぎる

この記事では、Azure AD アプリケーション プロキシのアプリケーションが読み込みに時間がかかる理由について説明します。 また、この問題の解決方法について説明します。

## <a name="overview"></a>概要
アプリケーションが動作していても、待ち時間が長いことがあります。 ネットワーク トポロジを調整すれば、待ち時間を短縮できることがあります。 さまざまなトポロジの評価については、[ネットワークに関する考慮事項のマニュアル](manage-apps/application-proxy-network-topology.md)をご覧ください。

ネットワーク トポロジの他には、パフォーマンス調整のために推奨できることは現在ありません。 アプリケーション プロキシ サービスが拡大されるにつれ、データ センターに物理的に近くなることがあります。 近くなることで待ち時間が改善されることがあります。 Azure データ センターの一覧については、[待機時間テスト ページ](http://www.azurespeed.com/Azure/Latency)をご覧ください。 

アプリケーション プロキシ サービスを使用したデータ センターは、[コネクタ ポート テスト ツール](https://aadap-portcheck.connectorporttest.msappproxy.net/)で見つけることができます。 

## <a name="feedback-on-application-proxy-data-center-locations"></a>アプリケーション プロキシ データ センターの場所に関するフィードバック 
今のところはアプリケーション プロキシが含まれていないがお客様にとって待機時間の大幅な短縮につながる Azure データ センターがあるかもしれません。 データ センターの場所を aadapfeedback@microsoft.com に送信します。 Microsoft は皆様からのフィードバックを拡張計画に利用します。

Microsoft は、待機時間を改善するための追加機能に取り組んでいます。 改善策が利用可能になり次第、ドキュメントが更新されます。

## <a name="next-steps"></a>次の手順
[既存のオンプレミス プロキシ サーバーと連携する](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
