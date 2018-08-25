---
title: ハイブリッド Azure Active Directory 参加済みデバイスを手動で構成する | Microsoft Docs
description: ハイブリッド Azure Active Directory 参加済みデバイスを手動で構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: ba47223f86005809189214f26a63b75b21449e3a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630621"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>チュートリアル: ハイブリッド Azure Active Directory 参加済みデバイスを手動で構成する 

Azure Active Directory (Azure AD) のデバイス管理を使用して、ユーザーがセキュリティとコンプライアンスの基準と一致するデバイスからリソースにアクセスしていることを保証できます。 詳細については、「[Azure Active Directory のデバイス管理の概要](overview.md)」を参照してください。

オンプレミスの Active Directory 環境があるときに、ドメイン参加済みデバイスを Azure AD に参加させたい場合は、ハイブリッド Azure AD 参加済みデバイスを構成することによってこれを実現できます。 この記事では、その関連手順を示します。 



> [!TIP]
> Azure AD Connect を使用する選択肢がある場合は、「[Select your scenario](hybrid-azuread-join-plan.md#select-your-scenario)」 (シナリオを選択する) を参照してください。 Azure AD Connect を使用すると、ハイブリッド Azure AD 参加の構成を大幅に簡略化できます。




## <a name="prerequisites"></a>前提条件

このチュートリアルは、次の事項を熟知していることを前提としています。
    
-  [Azure Active Directory のデバイス管理の概要](../device-management-introduction.md)
    
-  [ハイブリッド Azure Active Directory Join の実装を計画する方法](hybrid-azuread-join-plan.md)

-  [デバイスのハイブリッド Azure AD Join を制御する方法](hybrid-azuread-join-control.md)


組織内でハイブリッド Azure AD 参加済みデバイスの有効化を開始する前に、以下の点を確認する必要があります。

- Azure AD connect の最新バージョンを実行しています。

- Azure AD Connect は、Azure AD に参加するハイブリッド Azure AD にするデバイスのコンピューター オブジェクトを同期済みです。 コンピューター オブジェクトが特定の組織単位 (OU) に属している場合、これらの OU を Azure AD Connect についても構成する必要があります。

  

Azure AD Connect:

- オンプレミス Active Directory (AD) のコンピューター アカウントと Azure AD のデバイス オブジェクトの間の関連付けを維持します。 
- Windows Hello for Business のような、その他のデバイス関連機能を利用できるようにします。

Azure AD にコンピューターを登録するため、組織ネットワーク内のコンピューターから次の URL にアクセスできることを確認してください。

- https://enterpriseregistration.windows.net

- https://login.microsoftonline.com
- https://device.login.microsoftonline.com

- 組織の STS (フェデレーション ドメイン)

ユーザーのローカル イントラネット設定に組織の STS (フェデレーション ドメイン) が含まれていない場合、追加してください。

組織がシームレス SSO の使用を計画している場合、組織内のコンピューターから次の URL に到達できる必要があります。また、ユーザーのローカル イントラネット ゾーンに追加する必要があります。

- https://autologon.microsoftazuread-sso.com

- また、ユーザーのイントラネット ゾーンで [スクリプトを介したステータス バーの更新を許可する] という設定を有効にしてください。

組織がオンプレミス AI に (フェデレーションではない) 管理セットアップを使用しており、Azure AD とのフェデレーションに ADFS を使用しない場合、Windows 10 のハイブリッド Azure AD 参加は、AD のコンピューター オブジェクトを利用して Azure AD と同期します。 Azure AD Connect 同期構成の同期に対して、ハイブリッド Azure AD に参加する必要があるコンピューター オブジェクトが含まれる組織単位 (OU) が有効になっていることを確認します。

バージョン 1703 以前の Windows 10 デバイスに関して、送信プロキシ経由でインターネットにアクセスする必要がある組織の場合は、Windows 10 コンピューターを Azure AD に登録できるように、Web プロキシ自動発見 (WPAD) を実装する必要があります。 

## <a name="configuration-steps"></a>構成の手順

ハイブリッド Azure AD 参加済みデバイスは、さまざまな種類の Windows デバイス プラットフォームで構成できます。 このトピックには、すべての一般的な構成シナリオで必要となる手順が含まれています。  

次の表を使用して、自分のシナリオに必要な手順の概要を把握してください。  



| 手順                                      | 最新の Windows デバイスとパスワード ハッシュ同期 | 最新の Windows デバイスとフェデレーション | ダウンレベルの Windows |
| :--                                        | :-:                                    | :-:                            | :-:                |
| サービス接続ポイントの構成 | ![○][1]                            | ![○][1]                    | ![○][1]        |
| 要求の発行のセットアップ           |                                        | ![○][1]                    | ![○][1]        |
| 非 Windows 10 デバイスの有効化      |                                        |                                | ![○][1]        |
| 参加済みデバイスの確認          | ![○][1]                            | ![○][1]                    | ![○][1]        |



## <a name="configure-service-connection-point"></a>サービス接続ポイントの構成

サービス接続ポイント (SCP) オブジェクトは、登録中に Azure AD テナント情報を検出するために、デバイスによって使用されます。 オンプレミスの Active Directory (AD) で、ハイブリッド Azure AD 参加デバイスの SCP オブジェクトが、コンピューターのフォレストの構成名前付けコンテキストのパーティションに存在する必要があります。 各フォレストには、構成名前付けコンテキストが 1 つだけあります。 複数フォレストの Active Directory 構成では、ドメイン参加済みコンピューターが含まれているすべてのフォレストにサービス接続ポイントが存在している必要があります。

フォレストの構成名前付けコンテキストを取得するには、[**Get-ADRootDSE**](https://technet.microsoft.com/library/ee617246.aspx) コマンドレットを使用することができます。  

Active Directory ドメイン名が *fabrikam.com* であるフォレストの場合、構成名前付けコンテキストは次のようになります。

`CN=Configuration,DC=fabrikam,DC=com`

フォレストで、ドメイン参加済みデバイスの自動登録の SCP オブジェクトは、次の場所にあります。  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Azure AD Connect のデプロイ方法によっては、SCP オブジェクトが既に構成されている場合があります。
次の Windows PowerShell スクリプトを使用して、オブジェクトの存在を確認し、検出値を取得することができます。 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

**$scp.Keywords** の出力は次のような Azure AD テナント情報を示します。

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

サービス接続ポイントが存在しない場合は、Azure AD Connect サーバーで `Initialize-ADSyncDomainJoinedComputerSync` コマンドレットを実行して作成できます。 このコマンドレットを実行するには、エンタープライズ管理者の資格情報が必要です。  
このコマンドレットは、以下の処理を行います。

- Azure AD Connect の接続先 Active Directory フォレストにサービス接続ポイントを作成します。 
- ユーザーに `AdConnectorAccount` パラメーターを指定するように求めます。 これは、Azure AD Connect の Active Directory コネクタ アカウントとして構成されているアカウントです。 


次のスクリプトは、このコマンドレットの使用例を示しています。 このスクリプトで、`$aadAdminCred = Get-Credential` はユーザーにユーザー名の入力を求めます。 ユーザー プリンシパル名 (UPN) の形式 (`user@example.com`) でユーザー名を指定する必要があります。 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

`Initialize-ADSyncDomainJoinedComputerSync` コマンドレットは、

- Active Directory PowerShell モジュールと AD DS ツールを使用しますが、そのモジュールとツールは、ドメイン コントローラーで実行されている Active Directory Web サービスに依存しています。 Active Directory Web サービスは、Windows Server 2008 R2 以降が実行されているドメイン コントローラーでサポートされています。
- **MSOnline PowerShell module version 1.1.166.0** でのみサポートされます。 このモジュールをダウンロードするには、この[リンク](https://msconfiggallery.cloudapp.net/packages/MSOnline/1.1.166.0/)を使用します。   
- AD DS ツールがインストールされていない場合、`Initialize-ADSyncDomainJoinedComputerSync` は失敗します。  AD DS ツールをインストールするには、[機能] - [リモート サーバー管理ツール] - [役割管理ツール] の下のサーバー マネージャーを使用します。

Windows Server 2008 またはそれ以前のバージョンが実行されているドメイン コントローラーでは、次のスクリプトを使用してサービス接続ポイントを作成します。

複数フォレスト構成では、次のスクリプトを使用して、コンピューターが存在する各フォレストにサービス接続ポイントを作成する必要があります。
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your AD configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC
    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()

上記のスクリプトで、

- `$verifiedDomain = "contoso.com"` は、Azure AD で検証済みドメイン名のいずれかに置き換える必要があるプレースホルダーです。 これを使用するには、先にドメインを所有する必要があります。

確認済みのドメイン名の詳細については、「[Azure Active Directory へのカスタム ドメイン名の追加](../active-directory-domains-add-azure-portal.md)」を参照してください。  
確認済みの会社のドメインの一覧を取得するには、[Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain?view=azureadps-2.0) コマンドレットを使用できます。 

![Get-AzureADDomain](./media/hybrid-azuread-join-manual-steps/01.png)

## <a name="setup-issuance-of-claims"></a>要求の発行のセットアップ

フェデレーション Azure AD 構成では、デバイスは Azure AD に対する認証を Active Directory Federation Services (AD FS) またはサード パーティのオンプレミス フェデレーション サービスに任せます。 デバイスは、Azure Active Directory Device Registration Service (Azure DRS) に登録するためのアクセス トークンを取得するために、認証を行います。

最新の Windows デバイスは、統合 Windows 認証を使用して、オンプレミス フェデレーション サービスによってホストされているアクティブな WS-Trust エンドポイント (1.3 または 2005 バージョン) に対する認証を行います。

> [!NOTE]
> AD FS を使用する場合は、**adfs/services/trust/13/windowstransport** または **adfs/services/trust/2005/windowstransport** を有効にする必要があります。 Web 認証プロキシを使用している場合は、このエンドポイントがプロキシ経由で公開されていることも確認します。 どのエンドポイントが有効になっているかは、**[サービス] の [エンドポイント]** にある AD FS 管理コンソールで確認できます。
>
>オンプレミス フェデレーション サービスとして AD FS を使用していない場合は、ベンダーの指示に従って、WS-Trust 1.3 または 2005 のエンドポイントがサポートされていることと、それらが Metadata Exchange ファイル (MEX) を通じて公開されていることを確認してください。

デバイス登録を完了するには、Azure DRS によって受信されるトークンに以下の要求が存在する必要があります。 Azure DRS は、この情報の一部を持つデバイス オブジェクトを Azure AD に作成します。その後、この情報は、新たに作成されたデバイス オブジェクトをオンプレミスのコンピューター アカウントに関連付けるために、Azure AD Connect によって使用されます。

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

複数の検証済みドメイン名がある場合は、コンピューターに次の要求を提供する必要があります。

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

既に ImmutableID 要求 (たとえば、代替ログイン ID) を発行した場合は、コンピューターに 1 つの対応する要求を提供する必要があります。

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

以降のセクションでは、以下のものについて説明します。
 
- 各要求に含まれている必要がある値
- AD FS で定義がどのように表示されるか

定義によって、値が存在しているかどうかや、値を作成する必要があるかどうかを確認することができます。

> [!NOTE]
> オンプレミスのフェデレーション サーバーに AD FS を使用しない場合は、ベンダーの指示に従って、これらの要求を発行するための適切な構成を作成してください。

### <a name="issue-account-type-claim"></a>アカウントの種類の要求を発行する

**`http://schemas.microsoft.com/ws/2012/01/accounttype`** - この要求には、**DJ** の値が含まれている必要があります。この値により、デバイスがドメイン参加済みコンピューターと識別されます。 AD FS では、次のような発行変換規則を追加できます。

    @RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>オンプレミスのコンピューター アカウントの objectGUID を発行する

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`** - この要求には、オンプレミスのコンピューター アカウントの **objectGUID** 値が含まれている必要があります。 AD FS では、次のような発行変換規則を追加できます。

    @RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>オンプレミスのコンピューター アカウントの objectSID を発行する

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`** - この要求には、オンプレミスのコンピューター アカウントの **objectSid** 値が含まれている必要があります。 AD FS では、次のような発行変換規則を追加できます。

    @RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Azure AD に複数の検証済みドメイン名があるときにコンピューターの issuerID を発行する

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`** - この要求には、トークンを発行しているオンプレミスのフェデレーション サービス (AD FS またはサード パーティ) と接続するいずれかの検証済みドメイン名の Uniform Resource Identifier (URI) が含まれている必要があります。 AD FS では、次のような発行変換規則を、上の規則の後に特定の順序で追加できます。 ユーザーに対する規則を明示的に発行する 1 つの規則が必要であることに注意してください。 以下の規則では、ユーザーの認証かコンピューターの認証かを特定する最初の規則が追加されています。

    @RuleName = "Issue account type with the value User when its not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://<verified-domain-name>/adfs/services/trust/"
    );


上記の要求では、

- `<verified-domain-name>` は、Azure AD で検証済みドメイン名のいずれかに置き換える必要があるプレースホルダーです。 たとえば、Value = "http://contoso.com/adfs/services/trust/" です。



確認済みのドメイン名の詳細については、「[Azure Active Directory へのカスタム ドメイン名の追加](../active-directory-domains-add-azure-portal.md)」を参照してください。  

確認済みの会社のドメインの一覧を取得するには、[Get-msoldomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) コマンドレットを使用できます。 

![Get-MsolDomain](./media/hybrid-azuread-join-manual-steps/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>ユーザーに対する ImmutableID が存在する (たとえば、代替ログイン ID が設定されている) 場合は、コンピューターの ImmutableID を発行する

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`** - この要求には、コンピューターの有効な値が含まれている必要があります。 AD FS では、次のような発行変換規則を作成することができます。

    @RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>AD FS 発行変換規則を作成するヘルパー スクリプト

次のスクリプトは、上で説明した発行変換規則の作成に役立ちます。

    $multipleVerifiedDomainNames = $false
    $immutableIDAlreadyIssuedforUsers = $false
    $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains
    
    $rule1 = '@RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );'

    $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'

    $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);'

    $rule4 = ''
    if ($multipleVerifiedDomainNames -eq $true) {
    $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
    );'
    }

    $rule5 = ''
    if ($immutableIDAlreadyIssuedforUsers -eq $true) {
    $rule5 = '@RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'
    }

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

### <a name="remarks"></a>解説 

- このスクリプトは、既存の規則に規則を追加します。 このスクリプトは 2 回実行しないでください。規則のセットが 2 回追加されてしまいます。 スクリプトを再実行する前に、これらの要求に対応する規則が (対応する条件の下に) ないことを確認してください。

- (Azure AD ポータルまたは Get-MsolDomains コマンドレットで示されるとおり) 複数の検証済みドメイン名がある場合は、スクリプトで **$multipleVerifiedDomainNames** の値を **$true** に設定します。 また、Azure AD Connect やその他の方法で作成された可能性がある既存の issuerid 要求を必ず削除します。 この規則の例を次に示します。


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- ユーザー アカウントの **ImmutableID** 要求を既に発行している場合は、スクリプトの **$immutableIDAlreadyIssuedforUsers** の値を **$true** に設定します。

## <a name="enable-windows-down-level-devices"></a>ダウンレベルの Windows デバイスの有効化

ドメイン参加済みデバイスの一部がダウンレベルの Windows デバイスである場合は、以下の操作が必要です。

- ユーザーがデバイスを登録できるように Azure AD のポリシーを設定する。
 
- デバイスの登録で**統合 Windows 認証 (IWA)** をサポートする要求を発行するように、オンプレミス フェデレーション サービスを構成する。
 
- デバイスの認証時に証明書の指定を求めるメッセージが表示されないように、ローカルのイントラネット ゾーンに Azure AD デバイス認証のエンドポイントを追加する。

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>ユーザーがデバイスを登録できるように Azure AD のポリシーを設定する

ダウンレベルの Windows デバイスを登録するには、Azure AD へのデバイスの登録をユーザーに許可する設定になっている必要があります。 この設定は、Azure Portal の次の場所で確認できます。

`Azure Active Directory > Users and groups > Device settings`
    
**[ユーザーはデバイスを Azure AD に登録できます]** というポリシーは、**[すべて]** に設定する必要があります。

![デバイスを登録する](./media/hybrid-azuread-join-manual-steps/23.png)


### <a name="configure-on-premises-federation-service"></a>オンプレミス フェデレーション サービスを構成する 

オンプレミス フェデレーション サービスは、Azure AD 証明書利用者への認証要求を受信したときに、resouce_params パラメーターに以下のエンコード値が保持されている場合、**authenticationmehod** と **wiaormultiauthn** 要求の発行をサポートしている必要があります。

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

このような要求を受けたら、オンプレミス フェデレーション サービスは、統合 Windows 認証を使用してユーザーを認証する必要があります。認証に成功した場合は、以下の 2 つの要求を発行する必要があります。

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

AD FS では、この認証方法をパスする発行変換規則を追加する必要があります。  

**この規則を追加するには:**

1. AD FS 管理コンソールで、`AD FS > Trust Relationships > Relying Party Trusts` に移動します。
2. [Microsoft Office 365 ID プラットフォーム] 証明書利用者信頼オブジェクトを右クリックし、 **[要求規則の編集]** を選択します。
3. **[発行変換規則]** タブで、**[規則の追加]** を選択します。
4. **[要求規則]** テンプレート一覧から **[カスタム規則を使用して要求を送信]** を選択します。
5. **[次へ]** を選択します。
6. **[要求規則名]** ボックスに「**Auth Method Claim Rule**」と入力します。
7. **[要求規則]** ボックスに、次の規則を入力します。

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. フェデレーション サーバーで、以下の PowerShell コマンドを入力します。その際に、**\<RPObjectName\>** を、Azure AD 証明書利用者信頼オブジェクトの証明書利用者オブジェクト名に置き換えます。 通常、このオブジェクトは "**Microsoft Office 365 ID プラットフォーム**" という名前です。
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Azure AD デバイス認証エンドポイントをローカル イントラネット ゾーンに追加する

デバイスを登録するユーザーが Azure AD で認証を受けるときに証明書の指定を求めるメッセージが表示されないように、ドメイン参加済みデバイスにポリシーをプッシュして、以下の URL を Internet Explorer のローカル イントラネット ゾーンに追加することができます。

`https://device.login.microsoftonline.com`


## <a name="verify-joined-devices"></a>参加済みデバイスの確認

[Azure Active Directory PowerShell モジュール](/powershell/azure/install-msonlinev1?view=azureadps-2.0)の [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) コマンドレットを使用して、組織内の正常に参加しているデバイスを確認できます。

このコマンドレットの出力は、Azure AD への登録と参加が行われているデバイスを表示します。 すべてのデバイスを取得するには、**-All** パラメーターを使用し、その後で **deviceTrustType** プロパティを使用してフィルター処理します。 ドメイン参加済みデバイスの値は、**Domain Joined** です。



## <a name="troubleshoot-your-implementation"></a>実装のトラブルシューティング

ドメイン参加済み Windows デバイスのハイブリッド Azure AD 参加を行うときに問題が発生した場合は、次のトピックを参照してください。

- [最新の Windows デバイスのハイブリッド Azure AD 参加のトラブルシューティング](troubleshoot-hybrid-join-windows-current.md)
- [ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加のトラブルシューティング](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>次の手順

* [Azure Active Directory のデバイス管理の概要](overview.md)



<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual-steps/12.png
