---
title: Azure Active Directory の接続済みサービスでエラーを診断する方法
description: Active Directory の接続済みサービスで、サポートされていない認証の種類が検出された
services: active-directory
author: ghogen
manager: douge
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 82449c3a8154142a64aa264f72d2dec75fe2f23c
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42143462"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Azure Active Directory の接続済みサービスでエラーを診断する

Azure Active Director 接続済みサービスが以前の認証コードを検出するときに、互換性のない認証の種類が検出されました。

プロジェクトで以前の認証コードを正しく検出するには、プロジェクトを構築する必要があります。  このエラーが発生したときに、以前の認証コードがない場合は、再構築して、もう一度やり直してください。

## <a name="project-types"></a>プロジェクトの種類

接続済みサービスは、プロジェクトに適切な認証ロジックを挿入できるように、開発しているプロジェクトの種類を確認します。 プロジェクトに `ApiController` から派生したコントローラーが含まれている場合は、WebAPI プロジェクトと見なされます。 プロジェクトに `MVC.Controller` から派生したコントローラーしか含まれていない場合は、MVC プロジェクトと見なされます。 接続済みサービスでは他の種類のプロジェクトはサポートされません。

## <a name="compatible-authentication-code"></a>互換性のある認証コード

接続済みサービスは、以前にサービスで構成されたか、サービスと互換性のある認証設定が存在するかどうかも確認します。 すべての設定が存在する場合は、再入可能なケースと見なされ、接続済みサービスが開き、設定が表示されます。  一部の設定のみが存在する場合は、エラー ケースと見なされます。

MVC プロジェクトの場合、接続済みサービスは、以前にサービスを使用して構成された次の設定のいずれかが存在するかどうかを確認します。

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

さらに、以前に接続済みサービスを使用して構成された、Web API プロジェクトの次の設定のいずれかが存在するかどうかを確認します。

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>互換性のない認証コード

最後に、接続済みサービスは、Visual Studio の以前のバージョンで構成された認証コードのバージョンを検出しようと試みます。 このエラーが発生した場合、プロジェクトで互換性のない認証の種類が検出されたことを表します。 接続済みサービスは、Visual Studio の以前のバージョンに対して次の種類の認証を検出します。

* Windows 認証
* 個々のユーザー アカウント
* 組織アカウント

MVC プロジェクトで Windows 認証を検出するために、接続済みサービスは `web.config` ファイルで `authentication` 要素を探します。

```xml
<configuration>
    <system.web>
        <span style="background-color: yellow"><authentication mode="Windows" /></span>
    </system.web>
</configuration>
```

Web API プロジェクトで Windows 認証を検出するために、接続済みサービスはプロジェクトの `.csproj` ファイルで `IISExpressWindowsAuthentication` 要素を探します。

```xml
<Project>
    <PropertyGroup>
        <span style="background-color: yellow"><IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication></span>
    </PropertyGroup>
</Project>
```

個々のユーザー アカウント認証を検出するために、接続済みサービスは `packages.config` ファイルで package 要素を探します。

```xml
<packages>
    <span style="background-color: yellow"><package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
</packages>
```

組織アカウント認証の古い形式を検出するために、接続済みサービスは `web.config`ファイルで次の要素を探します:

```xml
<configuration>
    <appSettings>
        <span style="background-color: yellow"><add key="ida:Realm" value="***" /></span>
    </appSettings>
</configuration>
```

認証の種類を変更するには、互換性のない認証の種類を削除して、再度接続済みサービスの追加を試行します。

詳細については、「 [Azure AD の認証シナリオ](authentication-scenarios.md)」を参照してください。