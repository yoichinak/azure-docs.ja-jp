---
title: Azure Active Directory セルフ サービス パスワードの詳細情報
description: セルフサービスによるパスワードのリセットの動作
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 8c0810c4a1b92f14e510d005eaf1b6945a058dd7
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413105"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>機能: Azure AD のセルフ サービスによるパスワードのリセット

セルフサービスによるパスワードのリセット (SSPR) はどのように動作するのでしょうか? そのオプションはインターフェイスでは何を意味するのでしょうか? 以降では、Azure Active Directory (Azure AD) SSPR の詳細について説明します。

|     |
| --- |
| Azure AD のセルフ サービスによるパスワードのリセットの方法であるモバイル アプリ通知およびモバイル アプリ コードは、Azure Active Directory のパブリック プレビューの機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

## <a name="how-does-the-password-reset-portal-work"></a>パスワード リセット ポータルのしくみ

ユーザーがパスワード リセット ポータルに移動すると、次の点を決定するワークフローが開始します。

   * ページはどのようにローカライズする必要があるか?
   * ユーザー アカウントは有効か?
   * ユーザーが属しているのはどのような組織か?
   * ユーザーのパスワードはどこで管理されるか?
   * ユーザーにはこの機能を使用するライセンスがあるか?

パスワード リセット ページの背後にあるロジックの詳細については、次の手順をお読みください。

1. ユーザーは、**[アカウントにアクセスできません]** リンクを選ぶか、直接 [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) に移動します。
   * ブラウザーのロケールに基づいて、エクスペリエンスが適切な言語で表示されます。 パスワード リセットのエクスペリエンスは、Office 365 でサポートされている言語と同じ言語にローカライズされています。
   * パスワードのリセット ポータルを別のローカライズ言語で表示するには、パスワードのリセット URL の末尾に「?mkt=」を追加します。[https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) は、スペイン語へのローカライズの例を示しています。
2. ユーザーは、ユーザー ID を入力し、CAPTCHA を渡します。
3. Azure AD は、次のチェックを行うことで、ユーザーがこの機能を使用できることを確認します。
   * ユーザーがこの機能を有効にしていて、Azure AD ライセンスが割り当てられていることを確認します。
     * ユーザーがこの機能を有効にしていない、またはライセンスが割り当てられていない場合、そのユーザーは管理者に連絡してパスワードをリセットするように求められます。
   * ユーザーのアカウントで、適切な認証方法が管理者ポリシーに従って定義されていることを確認します。
     * ポリシーで要求される方法が 1 つのみの場合は、管理者ポリシーで有効になっている 1 つ以上の認証方法に対して、ユーザーが適切なデータを定義していることが確認されます。
       * 認証方法が構成されていない場合、そのユーザーは管理者に連絡してパスワードをリセットするように求められます。
     * ポリシーで要求される方法が 2 つの場合は、管理者ポリシーで有効になっている 2 つ以上の認証方法に対して、ユーザーが適切なデータを定義していることが確認されます。
       * 認証方法が構成されていない場合、そのユーザーは管理者に連絡してパスワードをリセットするように求められます。
     * Azure 管理者ロールがユーザーに割り当てられている場合、強力な 2 ゲート パスワード ポリシーが適用されます。 このポリシーの詳細については、「[管理者リセット ポリシーの相違点](concept-sspr-policy.md#administrator-reset-policy-differences)」セクションを参照してください。
   * ユーザーのパスワードが (フェデレーション、パススルー認証、またはパスワード ハッシュ同期された) オンプレミスで管理されているかどうかを確認します。
     * ライトバックがデプロイされていて、ユーザーのパスワードがオンプレミスで管理されている場合、ユーザーは自分のパスワードを認証してリセットできます。
     * ライトバックがデプロイされておらず、ユーザーのパスワードがオンプレミスで管理されている場合、ユーザーは管理者に連絡してパスワードをリセットするように求められます。
4. ユーザーが自分のパスワードを正常にリセットできると判断された場合は、リセット プロセスの説明がユーザーに示されます。

## <a name="authentication-methods"></a>認証方法

SSPR が有効になっている場合は、認証方法として以下のオプションの少なくとも 1 つを選択する必要があります。 これらのオプションは、"ゲート" と呼ばれることもあります。 **2 つ以上の認証方法を選択**することを強くお勧めします。そうすれば、一方の認証方法を利用できない場合でも、必要があれば、ユーザーはもう一方の認証方法を利用できます。

* モバイル アプリ通知 (プレビュー)
* モバイル アプリ コード (プレビュー)
* 電子メール
* 携帯電話
* 会社電話
* セキュリティの質問

ユーザーが自分のパスワードをリセットできるのは、管理者が有効にした認証方法の中にユーザーのデータがある場合のみです。

> [!WARNING]
> Azure 管理者ロールが割り当てられたアカウントは、「[管理者リセット ポリシーの相違点](concept-sspr-policy.md#administrator-reset-policy-differences)」で定義されている方法を使用する必要があります。

![認証][Authentication]

### <a name="number-of-authentication-methods-required"></a>必要な認証方法の数

このオプションは、ユーザーがパスワードをリセットするために通過する必要がある認証方法またはゲートの最小数を決定します。 1 または 2 に設定できます。

管理者が有効にしている認証方法であれば、ユーザーは他の認証方法を指定できます。

ユーザーに必要最低限の方法が登録されていない場合は、管理者にパスワードのリセットを依頼するよう指示するエラー ページが表示されます。

#### <a name="mobile-app-and-sspr-preview"></a>モバイル アプリおよび SSPR (プレビュー)

Microsoft Authenticator アプリなどのモバイル アプリを使用している場合、管理者はパスワードのリセット方法として、次を理解する必要があります。

* 管理者がパスワードのリセットに 1 つの方法の使用を必須にすると、使用できる選択肢は確認コードのみになります。
* 管理者がパスワードのリセットに 2 つの方法の使用を必須にすると、ユーザーは、通知**または**確認コードの**いずれか**と、他の有効な方法を使用できるようになります。

| リセットに必要な方法の数 | 1 つ | 2 つ |
| :---: | :---: | :---: |
| 使用可能なモバイル アプリの機能 | コード | コードまたは通知 |

[https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) からセルフ サービスによるパスワードのリセットを登録すると、ユーザーはモバイル アプリを登録するオプションを選択できません。 ユーザーは、[https://aka.ms/mfasetup](https://aka.ms/mfasetup) またはセキュリティ情報登録プレビュー ([https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)) でモバイル アプリを登録できます。

### <a name="change-authentication-methods"></a>認証方法を変更する

リセットまたはロック解除に必要な認証方法が 1 つしか登録されていないポリシーで開始し、それを 2 つの方法に変更した場合、どうなりますか。

| 登録されている方法の数 | 必要な方法の数 | 結果 |
| :---: | :---: | :---: |
| 1 つ以上 | 1 | リセットまたはロック解除**できる** |
| 1 | 2 | リセットまたはロック解除**できない** |
| 2 以上 | 2 | リセットまたはロック解除**できる** |

ユーザーが使用できる認証方法の種類を変更すると、誤って、使用できるデータが最小量に及ばない場合にユーザーが SSPR を使用できないようにする可能性があります。

例:
1. 元のポリシーには、必要な 2 つの認証方法が構成されています。 会社電話番号とセキュリティの質問のみを使います。 
2. 管理者は、今後セキュリティの質問を使用せず、携帯電話および連絡用メール アドレスを使用できるようにポリシーを変更します。
3. 携帯電話または連絡用メール アドレス フィールドに入力していないユーザーは、パスワードをリセットできません。

## <a name="registration"></a>登録

### <a name="require-users-to-register-when-they-sign-in"></a>サインイン時にユーザーに登録を求める

ユーザーが Azure AD を使用してアプリケーションにサインインする場合、このオプションを有効にするには、ユーザーがパスワード リセットの登録を完了する必要があります。 これには、次のアプリケーションが含まれます。

* Office 365
* Azure ポータル
* アクセス パネル
* フェデレーション アプリケーション
* Azure AD を使用するカスタム アプリケーション

登録の要求が無効になっている場合でも、ユーザーは手動で登録を行うことができます。 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) にアクセスするか、アクセス パネルの **[プロファイル]** タブの下にある **[パスワード リセットの登録]** リンクを使って行うことができます。

> [!NOTE]
> ユーザーは、**[キャンセル]** を選ぶか、ウィンドウを閉じることで、パスワード リセット登録ポータルを終了できます。 ただし、登録を完了するまでは、サインインのたびに登録を求められます。
>
> ユーザーがすでにサインインしている場合、これにより、その接続は切断されません。

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>ユーザーが認証情報を再確認するように求められるまでの日数を設定する

このオプションは、認証情報を設定してから再確認するまでの期間を決定し、**[サインイン時にユーザーに登録を求めますか]** オプションを有効にした場合にのみ表示されます。

有効な値は 0 - 730 日で、"0" はユーザーに認証情報の再確認を求めることがないことを意味します。

## <a name="notifications"></a>通知

### <a name="notify-users-on-password-resets"></a>[パスワードのリセットについてユーザーに通知しますか]

このオプションが **[はい]** に設定されている場合、パスワードをリセットするユーザーは、パスワードが変更されたことを通知するメールを受け取ります。 メールは、SSPR ポータル経由で、Azure AD に登録されているプライマリおよび連絡用メール アドレス宛に送られます。 このリセット イベントは他の誰にも通知されません。

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>[他の管理者が自分のパスワードをリセットしたときに、すべての管理者に通知しますか]

このオプションが **[はい]** に設定されている場合は、"*すべての管理者*" が、Azure AD に登録されているプライマリ メール アドレスでメールを受け取ります。 メールでは、別の管理者が SSPR を使ってパスワードを変更したことが通知されます。

例: 1 つの環境に 4 人の管理者がいます。 管理者 A が SSPR を使ってパスワードをリセットすると、 管理者 B、C、D はパスワード リセットを通知するメールを受け取ります。

## <a name="on-premises-integration"></a>オンプレミスの統合

Azure AD Connect のインストール、構成、有効化を行う場合、オンプレミス統合に関する次の追加オプションが表示されます。 これらのオプションがグレー表示になっている場合、ライトバックが適切に構成されていません。 詳しくは、「[パスワード ライトバックの構成](howto-sspr-writeback.md)」をご覧ください。

![ライトバック][Writeback]

このページには、オンプレミスのライトバック クライアントの簡単な状態が表示されます。現在の構成に基づいて、次のメッセージのいずれかが表示されます。

* お客様のオンプレミスのライトバック クライアントは稼働しています。
* Azure AD Connect はオンラインであり、オンプレミスのライトバック クライアントに接続されていますが、 インストールされている Azure AD Connect のバージョンが古いようです。 最新の接続機能と重要なバグ フィックスを確実に入手するため、[Azure AD Connect のアップグレード](./../connect/active-directory-aadconnect-upgrade-previous-version.md)をご検討ください。
* インストールされている Azure AD Connect のバージョンが古いため、残念ながらオンプレミスのライトバック クライアントの状態を確認できません。 [Azure AD Connect をアップグレードし](./../connect/active-directory-aadconnect-upgrade-previous-version.md)、接続の状態を確認できるようにしてください。
* 申し訳ございません。現在オンプレミスのライトバック クライアントに接続できないようです。 [Azure AD Connect のトラブルシューティングを行い](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)、接続を復元してください。
* パスワード ライトバックが正しく構成されていないため、残念ながらオンプレミスのライトバック クライアントに接続できません。 [パスワード ライトバックを構成し](howto-sspr-writeback.md)、接続を復元してください。
* 申し訳ございません。現在オンプレミスのライトバック クライアントに接続できないようです。 これはマイクロソフト側の一時的な問題が原因の可能性があります。 問題が解決しない場合は、[Azure AD Connect のトラブルシューティングを行い](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity)、接続を復元してください。

### <a name="write-back-passwords-to-your-on-premises-directory"></a>オンプレミス ディレクトリへのパスワード ライトバック

このコントロールにより、このディレクトリに対してパスワード ライトバックが有効になっているかどうかが決まります。 ライトバックがオンの場合、オンプレミスのライトバック サービスの状態を示します。 これは、Azure AD Connect を再構成せずにパスワード ライトバックを一時的に無効にする場合に便利です。

* スイッチが **[はい]** に設定されている場合、ライトバックは有効になり、フェデレーション、パススルー認証、またはパスワード ハッシュ同期のユーザーは、パスワードをリセットできます。
* スイッチが **[いいえ]** に設定されている場合、ライトバックは無効になり、フェデレーション、パススルー認証、またはパスワード ハッシュ同期のユーザーは、パスワードをリセットできません。

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>パスワードをリセットせずにアカウントのロックを解除することをユーザーに許可する

このコントロールは、パスワード リセット ポータルにアクセスするユーザーに、パスワードをリセットせずにオンプレミスの Active Directory アカウントのロックを解除するオプションを表示するかどうかを指定します。 既定では、Azure AD はパスワード リセットを実行するときにアカウントをロック解除します。 これら 2 つの操作を分離するには、この設定を使います。 

* **[はい]** に設定すると、ユーザーは、パスワードをリセットしてアカウントのロックを解除するか、パスワードをリセットせずにアカウントのロックを解除するかを選択できます。
* **[いいえ]** に設定すると、ユーザーはパスワードのリセットとアカウントのロック解除を組み合わせた操作しか実行できません。

## <a name="password-reset-for-b2b-users"></a>B2B ユーザーのパスワードのリセット

パスワードのリセットと変更は、すべての企業間 (B2B) 構成で完全にサポートされています。 B2B ユーザーのパスワード リセットは、次の 3 つの場合にサポートされます。

   * **既存の Azure AD テナントがあるパートナー組織のユーザー**: パートナーを組んでいる組織に既存の Azure AD テナントがある場合は、"*そのテナントで有効になっているパスワード リセット ポリシーが常に尊重されます*"。 パスワード リセットが機能するためにパートナー組織で必要なのは、Azure AD SSPR を有効にすることだけです。 Office 365 の顧客に対する追加料金はなく、[パスワード管理の開始](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords)に関するガイドの手順に従って有効にできます。
   * **セルフ サービス サインアップを使ってサインアップしたユーザー**: パートナーを組んでいる組織が[セルフ サービス サインアップ](../users-groups-roles/directory-self-service-signup.md)機能を使ってテナントに参加している場合は、登録されたメールを使ってパスワードをリセットできます。
   * **B2B ユーザー**: 新しい [Azure AD B2B 機能](../active-directory-b2b-what-is-azure-ad-b2b.md)を使って作成された B2B ユーザーも、招待プロセス中に登録した電子メールを使って自分のパスワードをリセットできます。

このシナリオをテストするには、これらのパートナー ユーザーのいずれかで http://passwordreset.microsoftonline.com に移動します。 連絡用電子メールまたは認証用電子メールが定義されている場合、パスワードのリセットは予想どおりに機能します。

> [!NOTE]
> Azure AD テナントへのゲスト アクセスを許可されている Microsoft アカウント (Hotmail.com、Outlook.com、他の個人メール アドレスからなど) は、Azure AD SSPR を使うことができません。 このようなユーザーは、「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)」の情報を使って、パスワードをリセットする必要があります。

## <a name="next-steps"></a>次の手順

次の記事では、Azure AD によるパスワードのリセットに関する追加情報が得られます。

* [SSPR のロールアウトを正常に完了する方法](howto-sspr-deployment.md)
* [パスワードのリセットまたは変更](../user-help/active-directory-passwords-update-your-own-password.md)
* [セルフサービスのパスワード リセットのための登録](../user-help/active-directory-passwords-reset-register.md)
* [ライセンスに関する質問](concept-sspr-licensing.md)
* [SSPR が使用するデータと、ユーザー用に事前設定が必要なデータ](howto-sspr-authenticationdata.md)
* [ユーザーが使用できる認証方法](concept-sspr-howitworks.md#authentication-methods)
* [SSPR のポリシー オプション](concept-sspr-policy.md)
* [パスワード ライトバックの概要とその必要性](howto-sspr-writeback.md)
* [SSPR でアクティビティをレポートする方法](howto-sspr-reporting.md)
* [SSPR のすべてのオプションとその意味](concept-sspr-howitworks.md)
* [不具合が発生していると思われるSSPR のトラブルシューティング方法](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/sspr-authentication-methods.png "Azure AD の使用できる認証方法と必要な数量"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "オンプレミスの統合のパスワード ライトバックの構成とトラブルシューティングに関する情報"
