---
title: Azure Active Directory での Office 365 グループのグループ名前付けポリシーの設定 (プレビュー) | Microsoft Docs
description: Azure Active Directory (プレビュー) で Office 365 グループの有効期限を設定する方法
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 05/21/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 111be7d3ee00f2b40ace3bfe4efdacc5029ccf77
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239136"
---
# <a name="enforce-a-naming-policy-for-office-365-groups-in-azure-active-directory-preview"></a>Azure Active Directory での Office 365 グループの名前付けポリシーの強制 (プレビュー)

ユーザーが作成または編集した Office 365 グループに一貫した名前付け規則を強制するには、Azure Active Directory (Azure AD) でテナントのグループ名前付けポリシーを設定します。 たとえば、名前付けポリシーを使用して、グループ、メンバーシップ、地理的地域の機能やグループ作成者の職務を伝えることができます。 また、名前付けポリシーを使用して、アドレス帳でグループを分類することもできます。 このポリシーを使用して、グループ名やグループ エイリアスで特定の単語の使用を禁止できます。

> [!IMPORTANT]
> Office 365 グループ名前付けポリシー プレビューを使用するには、1 つ以上の Office 365 グループのメンバーである一意のユーザーごとに Azure Active Directory Premium P1 ライセンスまたは Azure AD Basic EDU ライセンスが必要です。

名前付けポリシーは、ワークロード (Outlook、Microsoft Teams、SharePoint、Exchange、Planner など) でのグループの作成または編集に適用されます。 ポリシーは、グループ名とグループ エイリアスの両方に適用されます。 Azure AD で名前付けポリシーを設定し、既存の Exchange グループ名前付けポリシーもある場合は、Azure AD の名前付けポリシーが適用されます。

## <a name="naming-policy-features"></a>名前付けポリシーの機能
Office 365 グループの名前付けポリシーは、次の 2 通りの方法で強制できます。

-   **プレフィックス/サフィックス名前付けポリシー**: グループに名前付け規則を強制するために自動的に追加される、プレフィックスまたはサフィックスを定義できます (たとえば、グループ名 "GRP\_JAPAN\_My Group\_Engineering" では、GRP\_JAPAN\_ がプレフィックス、\_Engineering がサフィックスです)。 

-   **カスタム禁止単語**: ユーザーが作成したグループで禁止される、組織固有の一連の禁止単語 (例: "CEO, Payroll, HR") をアップロードできます。

### <a name="prefix-suffix-naming-policy"></a>プレフィックス/サフィックス名前付けポリシー

名前付け規則の一般的な構造は、"Prefix[GroupName]Suffix" です。 複数のプレフィックスとサフィックスを定義できますが、設定で使用できる [GroupName] のインスタンスは 1 つに限られます。 プレフィックスまたはサフィックスには、固定文字列またはユーザー属性 (\[Department\] など) のいずれかを指定できます。ユーザー属性は、グループを作成しているユーザーに基づいて置き換えられます。 プレフィックス文字列とサフィックス文字列の組み合わせに使用できる合計文字数は 53 文字です。 

プレフィックスとサフィックスには、グループ名とグループ エイリアスでサポートされている特殊文字を含めることができます。 グループ エイリアスでサポートされていないプレフィックスまたはサフィックスの文字はグループ名には適用されますが、グループ エイリアスからは削除されます。 この制限により、グループ名に適用されるプレフィックスとサフィックスは、グループ エイリアスに適用されるものとは異なる場合があります。 

#### <a name="fixed-strings"></a>固定文字列

文字列を使用すると、グローバル アドレス一覧やグループ ワークロードの左側のナビゲーション リンクでグループのスキャンと区別が容易になります。 一般的なプレフィックスとして、"Grp\_Name"、"\#Name"、"\_Name" などのキーワードがあります。

#### <a name="user-attributes"></a>ユーザー属性

グループの作成対象となった部門、オフィス、または地理的地域の特定に役立つ属性をご利用いただけます。 たとえば、`PrefixSuffixNamingRequirement = “GRP [GroupName] [Department]”` という名前付けポリシーを定義し、`User’s department = Engineering` の場合、強制されるグループ名は "GRP My Group Engineering" になります。 サポートされている Azure AD 属性は、\[Department\]、\[Company\]、\[Office\]、\[StateOrProvince\]、\[CountryOrRegion\]、\[Title\] です。 サポートされていないユーザー属性は固定文字列として扱われます (例: \[postalCode\])。 拡張属性とカスタム属性はサポートされていません。

組織のすべてのユーザーに値が入力された属性を使用し、長い値の属性は使用しないことをお勧めします。

### <a name="custom-blocked-words"></a>カスタム禁止単語

禁止単語リストは、グループ名とグループ エイリアスで禁止されているフレーズのコンマ区切りリストです。 サブ文字列の検索は実行されません。 エラーをトリガーするには、グループ名と 1 つ以上のカスタム禁止単語が完全に一致する必要があります。 サブ文字列の検索は実行されないため、"lass" が禁止単語であっても、ユーザーは "Class" のような一般的な単語を使用できます。

禁止単語リストのルールは次のとおりです。
- 禁止単語では大文字と小文字は区別されません。
- ユーザーがグループ名の一部として禁止単語を入力すると、その禁止単語と共にエラー メッセージが表示されます。
- 禁止単語に文字の制限はありません。
- 禁止単語リストで構成できるフレーズの上限は 5000 フレーズです。 

### <a name="administrator-override"></a>管理者のオーバーライド

すべてのグループ ワークロードおよびエンドポイントで、選択した管理者をこれらのポリシーから除外できます。これにより、管理者は禁止単語や独自の名前付け規則を使用してグループを作成できます。 グループ名前付けポリシーから除外される管理者ロールを次に示します。

- 全体管理者
- Partner Tier 1 サポート
- Partner Tier 2 サポート
- ユーザー アカウント管理者
- ディレクトリ ライター

## <a name="install-powershell-cmdlets-to-configure-a-naming-policy"></a>PowerShell コマンドレットをインストールして名前付けポリシーを構成する

PowerShell コマンドを実行する前に、古いバージョンの Windows PowerShell 用 Azure Active Directory PowerShell for Graph モジュールをアンインストールし、[Azure Active Directory PowerShell for Graph - パブリック プレビュー リリース 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) をインストールする必要があります。 

1. 管理者として Windows PowerShell アプリを開きます。
2. 以前のバージョンの AzureADPreview をアンインストールします。
  
  ````
  Uninstall-Module AzureADPreview
  ````
3. 最新バージョンの AzureADPreview をインストールします。
  
  ````
  Install-Module AzureADPreview
  ````
信頼されていないリポジトリへのアクセスに関するメッセージが表示されたら、「**Y**」と入力します。新しいモジュールのインストールには数分かかる場合があります。

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-ad-powershell"></a>Azure AD PowerShell を使用してテナントのグループ名前付けポリシーを構成する

1. コンピューターで Windows PowerShell ウィンドウを開きます。 これは昇格された特権がなくても開くことができます。

2. 次のコマンドを実行して、コマンドレットを実行する準備をします。
  
  ````
  Import-Module AzureADPreview
  Connect-AzureAD
  ````
  表示された **[アカウントにサインイン]** 画面で、管理者アカウントとパスワードを入力してサービスに接続し、**[サインイン]** を選択します。

3. 「[グループの設定を構成するための Azure Active Directory コマンドレット](groups-settings-cmdlets.md)」の手順に従って、このテナントのグループ設定を作成します。

### <a name="view-the-current-settings"></a>現在の設定を表示する

1. 現在の設定を表示する現在の名前付けポリシーを取り込みます。
  
  ````
  $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
  ````
  
2. 現在のグループ設定を表示します。
  
  ````
  $Setting.Values
  ````
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>名前付けポリシーとカスタム禁止単語を設定する

1. Azure AD PowerShell でグループ名のプレフィックスとサフィックスを設定します。
  
  ````
  $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
  ````
  
2. 制限するカスタム禁止単語を設定します。 次の例は、独自のカスタム単語を追加する方法を示しています。
  
  ````
  $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
  ````
  
3. 次の例のように、新しいポリシーの設定を保存して有効にします。
  
  ````
  Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
  ````
  
これで終了です。 名前付けポリシーが設定され、禁止単語が追加されました。

## <a name="export-or-import-the-list-of-custom-blocked-words"></a>カスタム禁止単語のリストをエクスポートまたはインポートする

詳細については、「[グループの設定を構成するための Azure Active Directory コマンドレット](groups-settings-cmdlets.md)」をご覧ください。

複数の禁止単語をエクスポートする PowerShell スクリプトの例を次に示します。

````
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
````

複数の禁止単語をインポートする PowerShell スクリプトの例を次に示します。

````
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
$Settings["EnableMSStandardBlockedWords"] = $True
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
````

## <a name="naming-policy-experiences-across-office-365-apps"></a>Office 365 アプリの名前付けポリシー エクスペリエンス

Azure AD でグループ名前付けポリシーを設定した後、ユーザーが Office 365 アプリでグループを作成すると、次のようになります。 

* ユーザーがグループ名を入力するとすぐに、名前付けポリシー (プレフィックス/サフィックス) に従って名前のプレビューが表示されます。
* ユーザーが禁止単語を入力すると、禁止単語を削除できるようにエラー メッセージが表示されます。

ワークロード | コンプライアンス
----------- | -------------------------------
Azure Active Directory ポータル | グループの作成または編集時にユーザーがグループ名を入力すると、Azure AD ポータルとアクセス パネル ポータルに、名前付けポリシーが強制された名前が表示されます。 ユーザーがカスタム禁止単語を入力すると、その禁止単語と共にエラー メッセージが表示されるので、ユーザーはその単語を削除できます。
Outlook Web Access (OWA) | ユーザーがグループ名またはグループ エイリアスを入力すると、Outlook Web Access に、名前付けポリシーが強制された名前が表示されます。 ユーザーがカスタム禁止単語を入力すると、UI にその禁止単語と共にエラー メッセージが表示されるので、ユーザーはその単語を削除できます。
Outlook デスクトップ | Outlook デスクトップで作成されたグループは、名前付けポリシー設定に準拠しています。 Outlook デスクトップ アプリでは、ポリシーが強制されたグループ名のプレビューはまだ表示されず、ユーザーがグループ名を入力したときにカスタム禁止単語エラーは返されません。 ただし、グループの作成または編集時に名前付けポリシーが自動的に適用されます。グループ名またはグループ エイリアスにカスタム禁止単語が含まれていると、ユーザーにエラー メッセージが表示されます。
Microsoft Teams | ユーザーがチーム名を入力すると、Microsoft Teams に、グループ名前付けポリシーが強制された名前が表示されます。 ユーザーがカスタム禁止単語を入力すると、その禁止単語と共にエラー メッセージが表示されるので、ユーザーはその単語を削除できます。
SharePoint  |  ユーザーがサイト名またはグループの電子メール アドレスを入力すると、SharePoint に、名前付けポリシーが強制された名前が表示されます。 ユーザーがカスタム禁止単語を入力すると、その禁止単語と共にエラー メッセージが表示されるので、ユーザーはその単語を削除できます。
Microsoft Stream | ユーザーがグループ名またはグループの電子メール エイリアスを入力すると、Microsoft Stream に、グループ名前付けポリシーが強制された名前が表示されます。 ユーザーがカスタム禁止単語を入力すると、その禁止単語と共にエラー メッセージが表示されるので、ユーザーはその単語を削除できます。
Outlook iOS および Android アプリ | Outlook アプリで作成されたグループは、構成済みの名前付けポリシー設定に準拠しています。 Outlook モバイル アプリでは、名前付けポリシーが強制された名前のプレビューはまだ表示されず、ユーザーがグループ名を入力したときにカスタム禁止単語エラーは返されません。 ただし、作成/編集をクリックすると、名前付けポリシーが自動的に適用されます。グループ名またはグループ エイリアスにカスタム禁止単語が含まれていると、ユーザーにエラー メッセージが表示されます。
Groups モバイル アプリ | Groups モバイル アプリで作成されたグループは、名前付けポリシーに準拠しています。 Groups モバイル アプリでは、名前付けポリシーのプレビューは表示されず、ユーザーがグループ名を入力したときにカスタム禁止単語エラーは返されません。 ただし、グループの作成または編集時に名前付けポリシーが自動的に適用されます。グループ名またはグループ エイリアスにカスタム禁止単語が含まれていると、ユーザーに該当するエラーが表示されます。
Planner | Planner は名前付けポリシーに準拠しています。 プラン名を入力すると、Planner に名前付けポリシーのプレビューが表示されます。 ユーザーがカスタム禁止単語を入力すると、プランの作成時にエラー メッセージが表示されます。
Dynamics 365 for Customer Engagement | Dynamics 365 for Customer Engagemen は名前付けポリシーに準拠しています。 ユーザーがグループ名またはグループの電子メール エイリアスを入力すると、Dynamics 365 に、名前付けポリシーが強制された名前が表示されます。 ユーザーがカスタム禁止単語を入力すると、その禁止単語と共にエラー メッセージが表示されるので、ユーザーはその単語を削除できます。
School Data Sync (SDS) | SDS を使用して作成されたグループは名前付けポリシーに準拠していますが、名前付けポリシーが自動的に適用されるわけではありません。 SDS 管理者は、グループを作成して SDS にアップロードする必要があるクラス名にプレフィックスとサフィックスを追加する必要があります。 そうしないと、グループの作成または編集は失敗します。
Outlook Customer Manager (OCM) | Outlook Customer Manager は名前付けポリシーに準拠しています。Outlook Customer Manager で作成されたグループには名前付けポリシーが自動的に適用されます。 カスタム禁止単語が検出された場合、OCM でのグループの作成がブロックされ、ユーザーは OCM アプリを使用できなくなります。
Classroom アプリ | Classroom アプリで作成されたグループは名前付けポリシーに準拠していますが、名前付けポリシーが自動的に適用されるわけではありません。教室グループ名を入力したときに、名前付けポリシーのプレビューはユーザーに表示されません。 ユーザーは、プレフィックスとサフィックスを付けたポリシー強制後の教室グループ名を入力する必要があります。 そうしないと、教室グループの作成または編集操作はエラーで失敗します。
Power BI | Power BI ワークスペースは、名前付けポリシーに準拠しています。    
Yammer | Azure Active Directory アカウントで Yammer にサインインしたユーザーが グループを作成する、またはグループ名を編集する場合、グループ名は名前付けポリシーに準拠します。 これは、 Office 365 接続 グループ及び他のすべての Yammer グループどちらにも適用されます。<br>名前付けポリシーが実施される前に Office 365 接続グループを作成した場合、グループ名は名前付けポリシーに自動的には従いません。 ユーザーは、グループ名を編集するときに、プレフィックスとサフィックスを追加するよう求められます。
StaffHub  | StaffHub チームは名前付けポリシーに従いませんが、基になる Office 365 グループは従います。 StaffHub チーム名にはプレフィックスとサフィックスは適用されず、カスタム禁止単語もチェックされません。 ただし、StaffHub ではプレフィックスとサフィックスが適用され、基になる Office 365 グループから禁止単語が削除されます。
Exchange PowerShell | Exchange PowerShell コマンドレットは名前付けポリシーに準拠しています。 ユーザーがグループ名やグループ エイリアス (mailNickname) で名前付けポリシーに従っていない場合、推奨されるプレフィックスとサフィックスやカスタム禁止単語を示す適切なエラー メッセージが表示されます。
Azure Active Directory PowerShell コマンドレット | Azure Active Directory PowerShell コマンドレットは名前付けポリシーに準拠しています。 ユーザーがグループ名やグループ エイリアスで名前付け規則に従っていない場合、推奨されるプレフィックスとサフィックスやカスタム禁止単語を示す適切なエラー メッセージが表示されます。
Exchange 管理センター | Exchange 管理センターは名前付けポリシーに準拠しています。 ユーザーがグループ名やグループ エイリアスで名前付け規則に従っていない場合、推奨されるプレフィックスとサフィックスやカスタム禁止単語を示す適切なエラー メッセージが表示されます。
Office 365 管理センター | Office 365 管理センターは名前付けポリシーに準拠しています。 ユーザーがグループ名を作成または編集すると、名前付けポリシーが自動的に適用されます。ユーザーがカスタム禁止単語を入力すると、該当するエラーが返されます。 Office 365 管理センターでは、名前付けポリシーのプレビューはまだ表示されず、ユーザーがグループ名を入力したときにカスタム禁止単語エラーは返されません。

## <a name="next-steps"></a>次の手順
次の記事では、Azure AD グループに関する追加情報が提供されています。

* [既存のグループの表示](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Office 365 グループの有効期限ポリシー](groups-lifecycle.md)
* [グループの設定の管理](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [グループのメンバーの管理](../fundamentals/active-directory-groups-members-azure-portal.md)
* [グループのメンバーシップの管理](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [グループ内のユーザーの動的ルールの管理](groups-dynamic-membership.md)
