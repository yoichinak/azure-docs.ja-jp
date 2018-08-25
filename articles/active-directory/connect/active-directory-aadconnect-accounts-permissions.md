---
title: 'Azure AD Connect: アカウントとアクセス許可 | Microsoft Docs'
description: このトピックでは、使用および作成されるアカウントと、必要なアクセス許可について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: cfb6fb512ecb7d57cf411a31b2e04726bfc4b743
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263404"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: アカウントとアクセス許可

## <a name="accounts-used-for-azure-ad-connect"></a>Azure AD Connect に使用されるアカウント

![](media/active-directory-aadconnect-accounts-permissions/account5.png)

Azure AD Connect では、オンプレミス (Windows Server Active Directory) から Azure Active Directory に情報を同期させるために 3 つのアカウントが使用されます。  それらのアカウントを次に示します。

- **AD DS コネクタ アカウント**:     Windows Server Active Directory からの情報の読み取りと Windows Server Active Directory への情報の書き込みに使用します。

- **ADSync サービス アカウント**:      同期サービスを実行したり SQL データベースにアクセスしたりする目的で使用します。

- **Azure AD コネクタ アカウント**:     Azure AD への情報の書き込みに使用します。

Azure AD Connect を実行するためのこれら 3 つのアカウントに加え、Azure AD Connect をインストールするには、次のアカウントが別途必要となります。  次のとおりです。

- **AD DS エンタープライズ管理者アカウント**:      Azure AD Connect のインストールに使用します。
- **Azure AD 全体管理者アカウント**:  Azure AD コネクタ アカウントを作成したり、Azure AD を構成したりする目的で使用します。

- **SQL SA アカウント (任意)**:     通常版の SQL Server を使用して ADSync データベースを作成する目的で使用します。  この SQL Server は、Azure AD Connect のインストール環境に対してローカルでもリモートでもかまいません。  このアカウントは、エンタープライズ管理者と同じアカウントにすることもできます。  現在では、SQL 管理者が帯域外でデータベースのプロビジョニングを実行し、データベース所有者権限を持つ Azure AD Connect 管理者がインストールできるようになっています。  詳細については、「[Install Azure AD Connect using SQL delegated administrator permissions (SQL によって委任された管理者の権限を使用した Azure AD Connect のインストール)]()」を参照してください。

## <a name="installing-azure-ad-connect"></a>Azure AD Connect のインストール
Azure AD Connect インストール ウィザードには次の 2 つの別の設定からアクセスできます。

* 簡単設定のウィザードには、より多くの特権が必要です。  これは、ユーザーを作成したりアクセス許可を構成したりする必要なく、構成を簡単に設定できるようにするためです。
* カスタム設定のウィザードでは、より多くの選択肢とオプションが提供されます。 ただし、状況によっては設定を行うユーザー自身が適切なアクセス許可を持っていることが必要になります。



## <a name="express-settings-installation"></a>簡単設定を使用したインストール
簡易設定のインストール ウィザードでは、次の情報の入力が求められます。

  - AD DS エンタープライズ管理者の資格情報
  - Azure AD 全体管理者の資格情報

### <a name="ad-ds-enterprise-admin-credentials"></a>AD DS エンタープライズ管理者の資格情報
AD DS エンタープライズ管理者アカウントは、オンプレミスの Active Directory を構成する目的で使用します。 これらの資格情報を使用するのはインストール中のみであり、インストールの完了後には使用しません。 ドメイン管理者ではなく、エンタープライズ管理者が Active Directory のアクセス許可をすべてのドメインで設定できることを確認します。

DirSync からアップグレードする場合は、AD DS エンタープライズ管理者の資格情報を使用して、DirSync で使用されるアカウントのパスワードをリセットします。 Azure AD グローバル管理者の資格情報も必要になります。

### <a name="azure-ad-global-admin-credentials"></a>Azure AD 全体管理者の資格情報
これらの資格情報を使用するのはインストール中のみであり、インストールの完了後には使用しません。 この資格情報は、Azure AD への変更の同期に使用される [Azure AD コネクタ アカウント](#azure-ad-service-account)を作成するために使用します。 また、このアカウントにより、同期が Azure AD の機能として有効化されます。

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>AD DS コネクタ アカウントの必須のアクセス許可 (簡単設定の場合)
[AD DS コネクタ アカウント](#active-directory-account)は、Windows Server AD の読み取りと書き込みを目的に作成され、簡単設定によって作成された場合、以下のアクセス許可を持ちます。

| アクセス許可 | 使用対象 |
| --- | --- |
| <li>ディレクトリの変更のレプリケート</li><li>ディレクトリの変更をすべてにレプリケート |パスワード ハッシュの同期 |
| すべてのプロパティの読み取り/書き込み (ユーザー) |インポートおよび Exchange ハイブリッド |
| すべてのプロパティの読み取り/書き込み (iNetOrgPerson) |インポートおよび Exchange ハイブリッド |
| すべてのプロパティの読み取り/書き込み (グループ) |インポートおよび Exchange ハイブリッド |
| すべてのプロパティの読み取り/書き込み (連絡先) |インポートおよび Exchange ハイブリッド |
| [パスワードのリセット] |パスワード ライトバックを有効にするための準備 |

### <a name="express-installation-wizard-summary"></a>高速インストール ウィザードの概要

![高速インストール](media/active-directory-aadconnect-get-started-express/express.png)

次の表は、高速インストール ウィザードの各ページと収集される資格情報、その使用目的をまとめたものです。

| ウィザード ページ | 収集される資格情報 | 必要なアクセス許可 | 用途 |
| --- | --- | --- | --- |
| 該当なし |インストール ウィザードを実行するユーザー |ローカル サーバーの管理者 |<li>同期サービスの実行に使用する [ADSync サービス アカウント](#azure-ad-connect-sync-service-account)を作成します。 |
| Azure への接続 |Azure AD ディレクトリの資格情報 |Azure AD でのグローバル管理者ロール |<li>Azure AD ディレクトリの同期を有効にします。</li>  <li>Azure AD で進行中の同期操作に使用する [Azure AD コネクタ アカウント](#azure-ad-service-account)を作成します。</li> |
| AD DS に接続 |オンプレミスの Active Directory の資格情報 |Active Directory 内の Enterprise Admins (EA) グループのメンバー |<li>Active Directory で [AD DS コネクタ アカウント](#active-directory-account)を作成し、それに対するアクセス許可を付与します。 この作成されたアカウントは、同期中にディレクトリ情報を読み取るまたは書き込むために使用されます。</li> |


## <a name="custom-installation-settings"></a>カスタム インストールの設定

カスタム設定のインストール ウィザードでは、より多くの選択肢とオプションが提供されます。 

### <a name="custom-installation-wizard-summary"></a>カスタム インストール ウィザードの概要

次の表は、カスタム インストール ウィザードの各ページと収集される資格情報、その使用目的をまとめたものです。

![高速インストール](media/active-directory-aadconnect-accounts-permissions/customize.png)

| ウィザード ページ | 収集される資格情報 | 必要なアクセス許可 | 用途 |
| --- | --- | --- | --- |
| 該当なし |インストール ウィザードを実行するユーザー |<li>ローカル サーバーの管理者</li><li>完全な SQL Server を使用する場合、ユーザーは SQL のシステム管理者 (SA) である必要があります</li> |既定では、 [同期エンジン サービス アカウント](#azure-ad-connect-sync-service-account)として使用するローカル アカウントを作成します。 このアカウントは、管理者が特定のアカウントを指定しなかった場合のみ作成します。 |
| 同期サービスのインストール、サービス アカウントのオプション |AD またはローカル ユーザー アカウントの資格情報 |ユーザー。アクセス許可は、インストール ウィザードにより付与されます。 |管理者がアカウントを指定している場合は、このアカウントは、同期サービスのサービス アカウントとして使用します。 |
| Azure への接続 |Azure AD ディレクトリの資格情報 |Azure AD でのグローバル管理者ロール |<li>Azure AD ディレクトリの同期を有効にします。</li>  <li>Azure AD で進行中の同期操作に使用する [Azure AD コネクタ アカウント](#azure-ad-service-account)を作成します。</li> |
| ディレクトリの接続 |Azure AD に接続する各フォレストのオンプレミス Active Directory の資格情報 |アクセス許可はどの機能を有効にするかによって異なります。詳細については、「[AD DS コネクタ アカウントの作成](#create-the-ad-dso-connector-account)」を参照してください。 |このアカウントは、同期中にディレクトリ情報を読み取るまたは書き込むために使用されます。 |
| AD FS サーバー |ウィザードを実行しているユーザーのログオン資格情報では接続できない場合に、リスト内の各サーバーについてウィザードが資格情報を収集します。 |ドメイン管理者 |AD FS サーバー ロールをインストールして構成します。 |
| Web アプリケーション プロキシ サーバー |ウィザードを実行しているユーザーのログオン資格情報では接続できない場合に、リスト内の各サーバーについてウィザードが資格情報を収集します。 |ターゲット コンピューターのローカル管理者 |WAP サーバー ロールをインストールして構成します。 |
| プロキシ信頼資格情報 |フェデレーション サービスの信頼資格情報 (FS からの信頼証明書の登録にプロキシが使用する資格情報) |ドメイン アカウント (AD FS サーバーのローカル管理者) |FS-WAP 信頼証明書の初回登録 |
| AD FS サービス アカウントのページ、[ドメイン ユーザー アカウントの使用オプション] |AD ユーザー アカウントの資格情報 |ドメイン ユーザー |資格情報を指定した AD ユーザー アカウントは、AD FS サービスのログオン アカウントとして使用します。 |

### <a name="create-the-ad-ds-connector-account"></a>AD DS コネクタ アカウントの作成
**[ディレクトリの接続]** ページで指定するアカウントは、インストールの前に Active Directory に存在している必要があります。  Azure AD Connect バージョン 1.1.524.0 以降には、Azure AD Connect ウィザードが Active Directory への接続に使う **AD DS コネクタ アカウント**を作成できるオプションがあります。  

また、必要なアクセス許可を付与されている必要があります。 インストール ウィザードではアクセス許可は検証されません。問題があれば、同期中に検出されます。

必要なアクセス許可は、有効にしたオプションの機能によって異なります。 複数のドメインがある場合は、フォレスト内のすべてのドメインにアクセス許可を付与する必要があります。 これらのいずれの機能も有効にしなかった場合、既定の **ドメイン ユーザー** アクセス許可で十分対応できます。

| Feature | アクセス許可 |
| --- | --- |
| msDS-ConsistencyGuid 機能 |「[設計概念 - sourceAnchor としての msDS-ConsistencyGuid の使用](active-directory-aadconnect-design-concepts.md#using-msds-consistencyguid-as-sourceanchor)」に記載された msDS-ConsistencyGuid 属性への書き込みアクセス許可。 | 
| パスワード ハッシュの同期 |<li>ディレクトリの変更のレプリケート</li>  <li>ディレクトリの変更をすべてにレプリケート |
| Exchange ハイブリッドのデプロイメント |ユーザー、グループ、連絡先用の「[Exchange ハイブリッドの書き戻し](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback)」に記載された属性への書き込みアクセス許可。 |
| Exchange メールのパブリック フォルダー |パブリック フォルダーに関して、「[Exchange メールのパブリック フォルダー](active-directory-aadconnectsync-attributes-synchronized.md#exchange-mail-public-folder)」に記載された属性への読み取りアクセス許可。 | 
| パスワードの書き戻し |ユーザー向けの「[パスワード管理の概要](../authentication/howto-sspr-writeback.md)」に記載された属性への書き込みアクセス許可。 |
| デバイスの書き戻し |「[デバイスの書き戻し](active-directory-aadconnect-feature-device-writeback.md)」に説明されているように、PowerShell スクリプトを使用して付与されたアクセス許可。 |
| グループの書き戻し |同期された **Office 365 グループ**のグループ オブジェクトの読み取り、作成、更新、削除。  詳しくは、「[グループの書き戻し](active-directory-aadconnect-feature-preview.md#group-writeback)」をご覧ください。|

## <a name="upgrade"></a>アップグレード
Azure AD Connect のいずれかのバージョンから新しいリリースにアップグレードする場合、次のアクセス許可が必要です。

>[!IMPORTANT]
>ビルド 1.1.484 以降の Azure AD Connect には回帰バグがあり、SQL データベースのアップグレードに sysadmin アクセス許可が必要です。  このバグはビルド 1.1.647 で修正されています。  このビルドにアップグレードする場合は、sysadmin アクセス許可が必要です。  dbo アクセス許可では不十分です。  sysadmin アクセス許可がないユーザーが Azure AD Connect をアップグレードしようとすると、アップグレードは失敗し、Azure AD Connect が正しく機能しなくなります。  Microsoft はこの問題を認識しており、解決に取り組んでいます。


| プリンシパル | 必要なアクセス許可 | 使用対象 |
| --- | --- | --- |
| インストール ウィザードを実行するユーザー |ローカル サーバーの管理者 |バイナリを更新します。 |
| インストール ウィザードを実行するユーザー |ADSyncAdmins のメンバー |同期規則などの構成を変更します。 |
| インストール ウィザードを実行するユーザー |完全バージョンの SQL Server を使用している場合: 同期エンジン データベースの DBO (または類似のもの) |新しい列でテーブルを更新するなど、データベース レベルを変更します。 |

## <a name="more-about-the-created-accounts"></a>作成されたアカウントの詳細情報
### <a name="ad-ds-connector-account"></a>AD DS コネクタ アカウント
簡単設定を使用した場合、アカウントは、同期に使用される Active Directory に作成されます。 作成されたアカウントはユーザー コンテナーのフォレスト ルート ドメインに配置され、名前の先頭に **MSOL_** が付けられます。 アカウントの作成時には、有効期限のない長く複雑なパスワードが設定されます。 ドメインにパスワード ポリシーがある場合は、このアカウントに対して長い複雑なパスワードが許可されることを確認してください。

![AD アカウント](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

カスタム設定を使用した場合、インストールを開始する前に、アカウントを作成する必要があります。  「[AD DS コネクタ アカウントの作成](#create-the-ad-dso-connector-account)」を参照してください。

### <a name="adsync-service-account"></a>ADSync サービス アカウント
同期サービスは、複数のアカウントで実行できます。 これは、**仮想サービス アカウント** (VSA)、**グループの管理されたサービス アカウント** (gMSA/sMSA)、または通常のユーザー アカウントで実行できます。 サポートされるオプションは、新規インストールを実行した場合、Connect の 2017 年 4 月のリリースで変更されます。 Azure AD Connect の以前のリリースからアップグレードする場合は、これらの追加のオプションは利用できません。

| アカウントの種類 | インストール オプション | 説明 |
| --- | --- | --- |
| [仮想サービス アカウント](#virtual-service-account) | 高速およびカスタム、2017 年 4 月以降 | これは、ドメイン コントローラーでのインストールを除くすべての高速インストールに使用されるオプションです。 カスタムでは、別のオプションが使われていない限り、これが既定のオプションです。 |
| 
  [グループの管理されたサービス アカウント](#group-managed-service-account) | カスタム、2017 年 4 月以降 | リモートの SQL サーバーを使用した場合、グループの管理されたサービス アカウントを使用することをお勧めします。 |
| [ユーザー アカウント](#user-account) | 高速およびカスタム、2017 年 4 月以降 | AAD_ というプレフィックスが付いたユーザー アカウントが、Windows Server 2008 およびドメイン コントローラーにインストールした場合のインストール時にのみ作成されます。 |
| [ユーザー アカウント](#user-account) | 高速およびカスタム、2017 年 3 月以前 | AAD_ というプレフィックスが付いたローカル アカウントがインストール時に作成されます。 カスタム インストールを使用した場合は、別のアカウントを指定できます。 |

2017 年 3 月以前のバージョンのビルドで Connect を使用した場合、Windows は、セキュリティ上の理由から暗号化キーを破棄するため、サービス アカウントのパスワードをリセットする必要はありません。 アカウントを他のアカウントに変更するには、Azure AD Connect を再インストールする必要があります。 2017 年 4 月以降のビルドにアップグレードする場合、サービス アカウントのパスワードを変更することはできますが、使用されるアカウントを変更することはできません。

> [!Important]
> サービス アカウントを設定できるのは、初回のインストール時のみです。 インストールが完了した後にサービス アカウントを変更することはできません。

次の表に、同期サービス アカウントの既定のオプション、推奨されるオプション、サポートされているオプションを示します。

凡例:

- **太字**は既定のオプションを示し、ほとんどの場合、推奨されるオプションです。
- *斜体*は既定のオプションではないが、推奨されるオプションを示します。
- 2008 - Windows Server 2008 にインストールした場合の既定のオプション
- 太字でない部分 - サポートされているオプション
- ローカル アカウント - サーバー上のローカル ユーザー アカウント
- ドメイン アカウント - ドメイン ユーザー アカウント
- sMSA - [スタンドアロンの管理されたサービス アカウント](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [グループの管理されたサービス アカウント](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>カスタム | リモート SQL</br>カスタム |
| --- | --- | --- | --- |
| **スタンドアロン/ワークグループ コンピューター** | サポートされていません | **VSA**</br>ローカル アカウント (2008)</br>ローカル アカウント |  サポートされていません |
| **ドメインに参加しているコンピューター** | **VSA**</br>ローカル アカウント (2008) | **VSA**</br>ローカル アカウント (2008)</br>ローカル アカウント</br>ドメイン アカウント</br>sMSA、gMSA | **gMSA**</br>ドメイン アカウント |
| **ドメイン コントローラー** | **ドメイン アカウント** | *gMSA*</br>**ドメイン アカウント**</br>sMSA| *gMSA*</br>**ドメイン アカウント**|

#### <a name="virtual-service-account"></a>仮想サービス アカウント
仮想サービス アカウントは、パスワードのない特殊な種類のアカウントで、Windows によって管理されます。

![VSA](./media/active-directory-aadconnect-accounts-permissions/aadsyncvsa.png)

VSA は、同期エンジンと SQL が同じサーバー上にあるシナリオで使用するためのものです。 リモートの SQL を使用する場合は、代わりに[グループの管理されたサービス アカウント](#managed-service-account)を使用することをお勧めします。

この機能では、Windows Server 2008 R2 以降が必要です。 Windows Server 2008 に Azure AD Connect をインストールする場合、インストールは代わりに[ユーザー アカウント](#user-account)を使用してフォールバックします。

#### <a name="group-managed-service-account"></a>グループの管理されたサービス アカウント
リモートの SQL サーバーを使う場合は、**グループの管理されたサービス アカウント**を使うことをお勧めします。 グループの管理されたサービス アカウント用の Active Directory を準備する方法について詳しくは、「[グループの管理されたサービス アカウントの概要](https://technet.microsoft.com/library/hh831782.aspx)」をご覧ください。

このオプションを使用するには、[[必須コンポーネントのインストール]](active-directory-aadconnect-get-started-custom.md#install-required-components) ページで **[既存のサービス アカウントを使用する]** を選択し、**[管理されたサービス アカウント]** を選択します。  
![VSA](./media/active-directory-aadconnect-accounts-permissions/serviceaccount.png)  

  [スタンドアロンの管理されたサービス アカウント](https://technet.microsoft.com/library/dd548356.aspx)を使用することもできます。 ただし、これらはローカル コンピューターでのみ使うことができ、既定の仮想サービス アカウントでこれらを使うことにメリットはありません。

この機能では、Windows Server 2012 以降が必要です。 以前のオペレーティング システムを使用し、リモート SQL を使用する必要がある場合は、[ユーザー アカウント](#user-account)を使用する必要があります。

#### <a name="user-account"></a>ユーザー アカウント
ローカル サービス アカウントはインストール ウィザードで作成します (カスタム設定で使用するアカウントを指定しない限り)。 アカウントは、先頭に **AAD_** が付き、実際の同期サービスがそのアカウントで実行されます。 Azure AD Connect をドメイン コントローラーにインストールした場合、アカウントはドメインに作成されます。 次の場合、**AAD_** サービス アカウントがドメインに存在する必要があります。
   - SQL Server を実行しているリモート サーバーを使っている
   - 認証が必要なプロキシを使っている

![同期サービス アカウント](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

アカウントの作成時には、有効期限のない長く複雑なパスワードが設定されます。

このアカウントを使用して、安全な方法で他のアカウントのパスワードを保存します。 これらの他のアカウントのパスワードは暗号化されて、データベースに格納されます。 暗号化キーの秘密キーは、Windows データ保護 API (DPAPI) を使用する暗号化サービスの秘密キー暗号化によって保護されています。

完全バージョンの SQL Server を使用した場合、サービス アカウントは、同期エンジン用に作成されたデータベースの DBO になります。 サービスは、他のアクセス許可では意図したように機能しません。 SQL ログインも作成されます。

アカウントには、ファイル、レジストリ キー、また同期エンジンに関連するその他のオブジェクトへのアクセス許可も付与されます。

### <a name="azure-ad-connector-account"></a>Azure AD コネクタ アカウント
Azure AD のアカウントは、同期サービスで使用するために作成されます。 このアカウントは、その表示名で識別できます。

![AD アカウント](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount2.png)

アカウントが使用されるサーバーの名前は、ユーザー名の 2 番目の部分で識別できます。 図では、サーバー名は DC1 です。 ステージング サーバーがある場合、各サーバーに独自のアカウントが指定されます。

アカウントの作成時には、有効期限のない長く複雑なパスワードが設定されます。 このアカウントには、ディレクトリ同期タスクの実行権限のみを持つ特別なロール **ディレクトリ同期アカウント** が付与されます。 この特別な組み込みロールを Azure AD Connect ウィザードの外部で付与することはできません。 Azure Portal ではこのアカウントには**ユーザー** ロールが表示されます。

Azure AD での同期サービスのアカウント数の上限は 20 です。 Azure AD で既存の Azure AD サービス アカウントの一覧を取得するには、次の Azure AD PowerShell コマンドレットを実行します。`Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

使用されていない Azure AD サービス アカウントを削除するには、次の Azure AD PowerShell コマンドレットを実行します。`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

## <a name="related-documentation"></a>関連ドキュメント
「[オンプレミス ID と Azure Active Directory の統合](../active-directory-aadconnect.md)」をまだお読みでない方のために、次の表に関連トピックへのリンクを示します。

|トピック |リンク|  
| --- | --- |
|Azure AD Connect のダウンロード | [Azure AD Connect のダウンロード](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Express 設定を使用したインストール | [Azure AD Connect の高速インストール](./active-directory-aadconnect-get-started-express.md)|
|カスタマイズした設定を使用したインストール | [Azure AD Connect のカスタム インストール](./active-directory-aadconnect-get-started-custom.md)|
|DirSync からのアップグレード | [Azure AD 同期ツール (DirSync) からのアップグレード](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|インストール後に | [インストールの確認とライセンスの割り当て ](active-directory-aadconnect-whats-next.md)|

## <a name="next-steps"></a>次の手順
「[オンプレミス ID と Azure Active Directory の統合](../active-directory-aadconnect.md)」をご覧ください。
