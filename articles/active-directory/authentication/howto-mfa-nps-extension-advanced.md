---
title: Azure MFA NPS の拡張機能を構成する | Microsoft Docs
description: NPS の拡張機能をインストールした後に、これらの手順を使用して IP のホワイトリスト登録や UPN の置換などを詳細に構成できます。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: a857732bcbe70cec164cebb54d7c09a1f103a942
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160612"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Multi-Factor Authentication の NPS の拡張機能の詳細構成オプション

ネットワーク ポリシー サーバー (NPS) の拡張機能は、クラウドベースの Azure Multi-Factor Authentication 機能をオンプレミスのインフラストラクチャに拡張します。 この記事では、拡張機能をインストール済みであることを前提に、ニーズに合わせて拡張機能をカスタマイズする方法について説明します。 

## <a name="alternate-login-id"></a>代替ログイン ID

NPS の拡張機能はオンプレミスとクラウドの両方のディレクトリに接続するため、オンプレミスのユーザー プリンシパル名 (UPN) がクラウドの名前と一致しない問題が発生する可能性があります。 この問題を解決するには、代替ログイン ID を使用します。 

NPS の拡張機能内では、Azure Multi-Factor Authentication の UPN の代わりに Active Directory 属性を指定して使用できます。 これにより、オンプレミスの UPN を変更することなく、2 段階認証でオンプレミスのリソースを保護できます。 

代替ログイン ID を構成するには、`HKLM\SOFTWARE\Microsoft\AzureMfa` に移動して次のレジストリ値を編集します。

| Name | Type | 既定値 | 説明 |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | 文字列 | Empty | UPN の代わりに使用する Active Directory 属性の名前を指定します。 この属性は、AlternateLoginId 属性として使用されます。 このレジストリ値が[有効な Active Directory 属性](https://msdn.microsoft.com/library/ms675090.aspx) (メールや displayName など) に設定されている場合、その属性の値がユーザーの UPN の代わりに認証に使用されます。 このレジストリ値が空または構成されていない場合、AlternateLoginId が無効になり、ユーザーの UPN が認証に使用されます。 |
| LDAP_FORCE_GLOBAL_CATALOG | ブール値 | False | このフラグを使用して、AlternateLoginId を検索する LDAP 検索でグローバル カタログの使用を強制します。 グローバル カタログとしてドメイン コントローラーを構成し、AlternateLoginId 属性をグローバル カタログに追加してから、このフラグを有効にします。 <br><br> LDAP_LOOKUP_FORESTS が構成されている (空でない) 場合、レジストリ設定の値に関係なく、**このフラグが true として適用されます**。 この場合、NPS の拡張機能では、各フォレストに対してグローバル カタログが AlternateLoginId 属性で構成される必要があります。 |
| LDAP_LOOKUP_FORESTS | 文字列 | Empty | 検索用にセミコロンで区切られたフォレストの一覧を提供します  (*contoso.com;foobar.com* など)。 このレジストリ値が構成されると、NPS の拡張機能はすべてのフォレストを一覧に表示されていた順番に繰り返し検索し、最初に見つかった AlternateLoginId 値を返します。 このレジストリ値が構成されていない場合、AlternateLoginId の検索は現在のドメインに限定されます。|

代替ログイン ID の問題については、[代替ログイン ID エラー](howto-mfa-nps-extension-errors.md#alternate-login-id-errors)に関する推奨手順を使用して解決していください。

## <a name="ip-exceptions"></a>IP の例外

ワークロードを送信する前にどのサーバーが実行されているかをロード バランサーが確認する場合など、サーバーの可用性を監視する必要がある場合、これらのチェックが確認要求によりブロックされないようにする必要があります。 代わりに、ユーザーが把握しているサービス アカウントで使用されている IP アドレスの一覧を作成し、その一覧に対する Multi-Factor Authentication 要求を無効にします。 

IP のホワイトリストを構成するには、`HKLM\SOFTWARE\Microsoft\AzureMfa` に移動して次のレジストリ値を構成します。 

| Name | Type | 既定値 | 説明 |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | 文字列 | Empty | セミコロンで区切られた IP アドレスの一覧を提供します。 サービス要求の送信元のマシン (NAS/VPN サーバーなど) の IP アドレスが含まれます。 サブネットである IP 範囲はサポートされません  <br><br> (*10.0.0.1;10.0.0.2;10.0.0.3* など)。

ホワイトリストに存在する IP アドレスからの要求である場合、2 段階認証はスキップされます。 IP のホワイトリストが、RADIUS 要求の *ratNASIPAddress* 属性で提供される IP アドレスと比較されます。 RADIUS 要求に ratNASIPAddress 属性がない場合、"P_WHITE_LIST_WARNING::IP Whitelist is being ignored as source IP is missing in RADIUS request in NasIpAddress attribute" という警告がログに記録されます。

## <a name="next-steps"></a>次の手順

[Azure Multi-Factor Authentication の NPS 拡張機能からのエラー メッセージを解決する](howto-mfa-nps-extension-errors.md)