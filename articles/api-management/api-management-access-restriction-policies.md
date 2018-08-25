---
title: Azure API Management のアクセス制限ポリシー | Microsoft Docs
description: Azure API Management で使用できるアクセス制限ポリシーについて説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 54bb6056c41126aecada265eb0e079bc7c281be8
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865935"
---
# <a name="api-management-access-restriction-policies"></a>API Management のアクセス制限ポリシー
このトピックでは、次の API Management ポリシーについて説明します。 ポリシーを追加および構成する方法については、「 [Azure API Management のポリシー](http://go.microsoft.com/fwlink/?LinkID=398186)」をご覧ください。  
  
##  <a name="AccessRestrictionPolicies"></a> アクセス制限ポリシー  
  
-   [HTTP ヘッダーを確認する](api-management-access-restriction-policies.md#CheckHTTPHeader) - HTTP ヘッダーの存在と値の両方、またはそのどちらかを適用します。  
-   [呼び出しレートをサブスクリプション別に制限する](api-management-access-restriction-policies.md#LimitCallRate) - 呼び出しレートをサブスクリプションに基づいて制限することで、API の使用量の急増を防ぎます。  
-   [呼び出しレートをキー別に制限する](#LimitCallRateByKey) - 呼び出しレートをキーに基づいて制限することで、API の使用量の急増を防ぎます。  
-   [呼び出し元 IP を制限する](api-management-access-restriction-policies.md#RestrictCallerIPs) - 特定の IP アドレスとアドレス範囲の両方、またはそのどちらかからの呼び出しをフィルター処理 (許可/拒否) します。  
-   [使用量のクォータをサブスクリプション別に設定する](api-management-access-restriction-policies.md#SetUsageQuota) - 更新可能な呼び出しまたは有効期間中の呼び出しのボリュームと帯域幅クォータの両方またはそのどちらかをサブスクリプションに基づいて適用できます。  
-   [使用量のクォータをキー別に設定する](#SetUsageQuotaByKey) - 更新可能な呼び出しまたは有効期間中の呼び出しのボリュームと帯域幅クォータの両方またはそのどちらかをキーに基づいて適用できます。  
-   [JWT を検証する](api-management-access-restriction-policies.md#ValidateJWT) - 指定された HTTP ヘッダーまたは指定されたクエリ パラメーターから抽出した JWT の存在と有効性を適用します。  
  
##  <a name="CheckHTTPHeader"></a> HTTP ヘッダーを確認する  
 `check-header` ポリシーを使用して、HTTP ヘッダーの指定がもれなく要求に含まれるようにします。 必要に応じて、ヘッダーに特定の値が指定されているかどうか、または許可されている範囲内の値かどうかを確認できます。 チェックに失敗した場合、このポリシーに従って要求の処理は終了し、ポリシーで指定した HTTP 状態コードとエラー メッセージが返されます。  
  
### <a name="policy-statement"></a>ポリシー ステートメント  
  
```xml  
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">  
    <value>Value1</value>  
    <value>Value2</value>  
</check-header>  
```  
  
### <a name="example"></a>例  
  
```xml  
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">  
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>  
</check-header>  
```  
  
### <a name="elements"></a>要素  
  
|Name|説明|必須|  
|----------|-----------------|--------------|  
|check-header|ルート要素。|[はい]|  
|value|許可される HTTP ヘッダーの値。 複数の要素を指定した場合、いずれかの値に一致すればチェックは成功とみなされます。|いいえ |  
  
### <a name="attributes"></a>属性  
  
|Name|説明|必須|既定値|  
|----------|-----------------|--------------|-------------|  
|failed-check-error-message|ヘッダーが存在しないかヘッダーが無効な値である場合に HTTP 応答本文で返されるエラー メッセージ。 このメッセージ内では、特殊文字を適切にエスケープする必要があります。|[はい]|該当なし|  
|failed-check-httpcode|ヘッダーが存在しないかヘッダーが無効な値である場合に返される HTTP 状態コード。|[はい]|該当なし|  
|header-name|チェックする HTTP ヘッダーの名前。|[はい]|該当なし|  
|ignore-case|True または False に設定できます。 True に設定した場合、ヘッダー値と許容される値セットとの比較時に大文字と小文字は区別されません。|[はい]|該当なし|  
  
### <a name="usage"></a>使用法  
 このポリシーは、次のポリシー [セクション](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。  
  
-   **ポリシー セクション:** inbound、outbound  
  
-   **ポリシー スコープ:** グローバル、製品、API、操作  
  
##  <a name="LimitCallRate"></a> 呼び出しレートをサブスクリプション別に制限する  
 `rate-limit` ポリシーは、指定期間あたりの呼び出しレートを指定数に制限することで、サブスクリプションごとに API 使用量の急増を防ぎます。 このポリシーがトリガーされると、呼び出し元は `429 Too Many Requests` 応答状態コードを受け取ります。  
  
> [!IMPORTANT]
>  このポリシーは、ポリシー ドキュメントごとに 1 回のみ使用できます。  
>   
>  このポリシー内のポリシー属性では、[ポリシー式](api-management-policy-expressions.md)は使用できません。  
  
### <a name="policy-statement"></a>ポリシー ステートメント  
  
```xml  
<rate-limit calls="number" renewal-period="seconds">  
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />  
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />  
    </api>  
</rate-limit>  
```  
  
### <a name="example"></a>例  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit calls="20" renewal-period="90" />  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>要素  
  
|Name|説明|必須|  
|----------|-----------------|--------------|  
|set-limit|ルート要素。|[はい]|  
|api|製品内の API に対して呼び出しレート制限をかけるには、これらの要素を 1 つまたは複数追加します。 製品と API の呼び出しレート制限は別々に適用されます。 API は `name` または `id` のいずれかによって参照できます。 両方の属性が提供された場合、`id` が使用されて `name` は無視されます。|いいえ |  
|operation|API 内の操作に対して呼び出しレート制限をかけるには、これらの要素を 1 つまたは複数追加します。 製品、API、および操作の呼び出しレート制限は別々に適用されます。 操作は `name` または `id` のいずれかによって参照できます。 両方の属性が提供された場合、`id` が使用されて `name` は無視されます。|いいえ |  
  
### <a name="attributes"></a>属性  
  
|Name|説明|必須|既定値|  
|----------|-----------------|--------------|-------------|  
|name|レート制限の適用対象になる API の名前。|[はい]|該当なし|  
|calls|`renewal-period` で指定した期間中に許容する最大呼び出し総数。|[はい]|該当なし|  
|renewal-period|クォータのリセット間隔 (秒単位)。|[はい]|該当なし|  
  
### <a name="usage"></a>使用法  
 このポリシーは、次のポリシー [セクション](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。  
  
-   **ポリシー セクション:** inbound  
  
-   **ポリシー スコープ:** 製品  
  
##  <a name="LimitCallRateByKey"></a> 呼び出しレートをキー別に制限する  
 `rate-limit-by-key` ポリシーは、指定期間あたりの呼び出しレートを指定数に制限することで、キーごとに API 使用量の急増を防ぎます。 キーには任意の文字列値を設定でき、通常はポリシー式を使用して指定します。 必要に応じて増分条件を追加し、制限に対してカウントする要求を指定することもできます。 このポリシーがトリガーされると、呼び出し元は `429 Too Many Requests` 応答状態コードを受け取ります。  
  
 このポリシーの詳細と例については、「[Azure API Management を使用した高度な要求スロットル](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/)」を参照してください。  
  
> [!IMPORTANT]
>  このポリシーは、ポリシー ドキュメントごとに 1 回のみ使用できます。  
  
### <a name="policy-statement"></a>ポリシー ステートメント  
  
```xml  
<rate-limit-by-key calls="number"  
                   renewal-period="seconds"   
                   increment-condition="condition"   
                   counter-key="key value" />  
  
```  
  
### <a name="example"></a>例  
 次のサンプルでは、レート制限のキーに呼び出し元 IP を設定しています。  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit-by-key  calls="10"  
              renewal-period="60"  
              increment-condition="@(context.Response.StatusCode == 200)"  
              counter-key="@(context.Request.IpAddress)"/>  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>要素  
  
|Name|説明|必須|  
|----------|-----------------|--------------|  
|set-limit|ルート要素。|[はい]|  
  
### <a name="attributes"></a>属性  
  
|Name|説明|必須|既定値|  
|----------|-----------------|--------------|-------------|  
|calls|`renewal-period` で指定した期間中に許容する最大呼び出し総数。|[はい]|該当なし|  
|counter-key|レート制限ポリシーに使用するキー。|[はい]|該当なし|  
|increment-condition|クォータに対して要求の件数をカウントするかどうかを指定するブール式です (`true`)。|いいえ |該当なし|  
|renewal-period|クォータのリセット間隔 (秒単位)。|[はい]|該当なし|  
  
### <a name="usage"></a>使用法  
 このポリシーは、次のポリシー [セクション](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。  
  
-   **ポリシー セクション:** inbound  
  
-   **ポリシー スコープ:** グローバル、製品、API、操作  
  
##  <a name="RestrictCallerIPs"></a> 呼び出し元 IP を制限する  
 `ip-filter` ポリシーは、特定の IP アドレスやアドレス範囲からの呼び出しをフィルター処理 (許可または拒否) します。  
  
### <a name="policy-statement"></a>ポリシー ステートメント  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="example"></a>例  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="elements"></a>要素  
  
|Name|説明|必須|  
|----------|-----------------|--------------|  
|ip-filter|ルート要素。|[はい]|  
|address|フィルターを適用する単一の IP アドレスを指定します。|`address` 要素または `address-range` 要素は少なくとも 1 つ必要です。|  
|address-range from="address" to="address"|フィルターを適用する IP アドレスの範囲を指定します。|`address` 要素または `address-range` 要素は少なくとも 1 つ必要です。|  
  
### <a name="attributes"></a>属性  
  
|Name|説明|必須|既定値|  
|----------|-----------------|--------------|-------------|  
|address-range from="address" to="address"|アクセスを許可または拒否する IP アドレスの範囲。|`address-range` 要素を使用する場合は必須です。|該当なし|  
|ip-filter action="allow &#124; forbid"|指定した IP アドレスおよび IP アドレス範囲に対する呼び出しを許可するかどうかを指定します。|[はい]|該当なし|  
  
### <a name="usage"></a>使用法  
 このポリシーは、次のポリシー [セクション](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。  
  
-   **ポリシー セクション:** inbound  
-   **ポリシー スコープ:** グローバル、製品、API、操作  
  
##  <a name="SetUsageQuota"></a> 使用量のクォータをサブスクリプション別に設定する  
 `quota` ポリシーは、更新可能な呼び出しまたは有効期間中の呼び出しのボリュームと帯域幅クォータの両方またはそのどちらかをサブスクリプションに基づいて適用します。  
  
> [!IMPORTANT]
>  このポリシーは、ポリシー ドキュメントごとに 1 回のみ使用できます。  
>   
>  このポリシー内のポリシー属性では、[ポリシー式](api-management-policy-expressions.md)は使用できません。  
  
### <a name="policy-statement"></a>ポリシー ステートメント  
  
```xml  
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">  
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />  
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />  
    </api>  
</quota>  
```  
  
### <a name="example"></a>例  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>要素  
  
|Name|説明|必須|  
|----------|-----------------|--------------|  
|quota|ルート要素。|[はい]|  
|api|製品内の API に対して呼び出しクォータをかけるには、これらの要素を 1 つまたは複数追加します。 製品と API の呼び出しクォータは別々に適用されます。 API は `name` または `id` のいずれかによって参照できます。 両方の属性が提供された場合、`id` が使用されて `name` は無視されます。|いいえ |  
|operation|API 内の操作に対して呼び出しクォータをかけるには、これらの要素を 1 つまたは複数追加します。 製品、API、および操作の呼び出しクォータは別々に適用されます。 操作は `name` または `id` のいずれかによって参照できます。 両方の属性が提供された場合、`id` が使用されて `name` は無視されます。|いいえ |  
  
### <a name="attributes"></a>属性  
  
|Name|説明|必須|既定値|  
|----------|-----------------|--------------|-------------|  
|name|クォータを適用する API または操作の名前。|[はい]|該当なし|  
|bandwidth|`renewal-period` で指定した期間中に許可する最大合計キロバイト数。|`calls` と `bandwidth` のいずれかまたは両方と同時に指定する必要があります。|該当なし|  
|calls|`renewal-period` で指定した期間中に許容する最大呼び出し総数。|`calls` と `bandwidth` のいずれかまたは両方と同時に指定する必要があります。|該当なし|  
|renewal-period|クォータのリセット間隔 (秒単位)。|[はい]|該当なし|  
  
### <a name="usage"></a>使用法  
 このポリシーは、次のポリシー [セクション](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。  
  
-   **ポリシー セクション:** inbound  
-   **ポリシー スコープ:** 製品  
  
##  <a name="SetUsageQuotaByKey"></a> 使用量のクォータをキー別に設定する  
 `quota-by-key` ポリシーは、更新可能な呼び出しまたは有効期間中の呼び出しのボリュームと帯域幅クォータの両方またはそのどちらかをキーに基づいて適用します。 キーには任意の文字列値を設定でき、通常はポリシー式を使用して指定します。 必要に応じて増分条件を追加し、クォータに対してカウントする要求を指定することもできます。  
  
 このポリシーの詳細と例については、「[Azure API Management を使用した高度な要求スロットル](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/)」を参照してください。  
  
> [!IMPORTANT]
>  このポリシーは、ポリシー ドキュメントごとに 1 回のみ使用できます。  
>   
>  このポリシー内のポリシー属性では、[ポリシー式](api-management-policy-expressions.md)は使用できません。  
  
### <a name="policy-statement"></a>ポリシー ステートメント  
  
```xml  
<quota-by-key calls="number"   
              bandwidth="kilobytes"   
              renewal-period="seconds"  
              increment-condition="condition"   
              counter-key="key value" />  
  
```  
  
### <a name="example"></a>例  
 次のサンプルでは、クォータのキーに呼び出し元 IP を設定しています。  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"  
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"  
                      counter-key="@(context.Request.IpAddress)" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>要素  
  
|Name|説明|必須|  
|----------|-----------------|--------------|  
|quota|ルート要素。|[はい]|  
  
### <a name="attributes"></a>属性  
  
|Name|説明|必須|既定値|  
|----------|-----------------|--------------|-------------|  
|bandwidth|`renewal-period` で指定した期間中に許可する最大合計キロバイト数。|`calls` と `bandwidth` のいずれかまたは両方と同時に指定する必要があります。|該当なし|  
|calls|`renewal-period` で指定した期間中に許容する最大呼び出し総数。|`calls` と `bandwidth` のいずれかまたは両方と同時に指定する必要があります。|該当なし|  
|counter-key|クォータ ポリシーに使用するキー。|[はい]|該当なし|  
|increment-condition|クォータに対して要求の件数をカウントするかどうかを指定するブール式 (`true`)|いいえ |該当なし|  
|renewal-period|クォータのリセット間隔 (秒単位)。|[はい]|該当なし|  
  
### <a name="usage"></a>使用法  
 このポリシーは、次のポリシー [セクション](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。  
  
-   **ポリシー セクション:** inbound  
-   **ポリシー スコープ:** グローバル、製品、API、操作  
  
##  <a name="ValidateJWT"></a> JWT を検証する  
 `validate-jwt` ポリシーは、指定した HTTP ヘッダーまたは指定したクエリ パラメーターのどちらかから抽出した JWT が存在し、有効であることを必須にします。  
  
> [!IMPORTANT]
>  `validate-jwt` ポリシーは、`require-expiration-time` 属性を指定し `false` に設定した場合を除いて、`exp` 登録クレームが JWT トークンに含まれていることを必須にします。  
> `validate-jwt` ポリシーでは HS256 署名アルゴリズムと RS256 署名アルゴリズムがサポートされています。 HS256 の場合、キーをポリシー内に base64 エンコード形式でインライン指定する必要があります。 RS256 の場合、キーは Open ID 構成エンドポイントを介して指定する必要があります。  
  
### <a name="policy-statement"></a>ポリシー ステートメント  
  
```xml  
<validate-jwt   
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"   
    failed-validation-httpcode="http status code to return on failure"   
    failed-validation-error-message="error message to return on failure"   
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"   
    clock-skew="allowed clock skew in seconds">  
  <issuer-signing-keys>  
    <key>base64 encoded signing key</key>  
    <!-- if there are multiple keys, then add additional key elements -->  
  </issuer-signing-keys>  
  <audiences>  
    <audience>audience string</audience>  
    <!-- if there are multiple possible audiences, then add additional audience elements -->  
  </audiences>  
  <issuers>  
    <issuer>issuer string</issuer>  
    <!-- if there are multiple possible issuers, then add additional issuer elements -->  
  </issuers>  
  <required-claims>  
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>  
      <!-- if there is more than one allowed values, then add additional value elements -->  
    </claim>  
    <!-- if there are multiple possible allowed values, then add additional value elements -->  
  </required-claims>  
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />  
  <zumo-master-key id="key identifier">key value</zumo-master-key>  
</validate-jwt>  
  
```  
  
### <a name="examples"></a>例  
  
#### <a name="azure-mobile-services-token-validation"></a>Azure Mobile Services のトークン検証  
  
```xml  
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">  
    <issuers>  
        <issuer>urn:microsoft:windows-azure:zumo</issuer>  
    </issuers>  
    <audiences>  
        <audience>Facebook</audience>  
    </audiences>  
    <issuer-signing-keys>  
        <zumo-master-key id="0">insert key here</zumo-master-key>  
    </issuer-signing-keys>  
</validate-jwt>  
```  
  
#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory のトークン検証  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />  
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  

  
#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C のトークン検証  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  
  
#### <a name="authorize-access-to-operations-based-on-token-claims"></a>トークン クレームに基づいて操作へのアクセスを承認する  
 次の例では、[JWT を検証する](api-management-access-restriction-policies.md#ValidateJWT)ポリシーを使用して、トークン クレームに基づいて操作へのアクセスを事前に承認する方法を示します。 このポリシーの構成と使用についてのデモは、「[Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)」(クラウド カバー エピソード 177: Vlad Vinogradsky によるその他の API Management 機能の紹介) を 13:50 まで早送りしてご覧ください。 15:00 まで早送りし、ポリシー エディターで構成されるポリシーをご覧いただいた後、開発者ポータルから操作を呼び出す方法について、必要な認証トークンを使用した場合と使用しない場合の両方のデモを 18:50 からご覧ください。  
  
```xml  
<!-- Copy the following snippet into the inbound section at the api (or higher) level to pre-authorize access to operations based on token claims -->  
<set-variable name="signingKey" value="insert signing key here" />  
<choose>  
  <when condition="@(context.Request.Method.Equals("patch",StringComparison.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="edit">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <when condition="@(new [] {"post", "put"}.Contains(context.Request.Method,StringComparer.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="create">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <otherwise>  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
    </validate-jwt>  
  </otherwise>  
</choose>  
```  
  
### <a name="elements"></a>要素  
  
|要素|説明|必須|  
|-------------|-----------------|--------------|  
|validate-jwt|ルート要素。|[はい]|  
|audiences|トークン上に存在する可能性がある、許容される対象ユーザー クレームの一覧を記載します。 対象ユーザー値が複数存在する場合は、すべての値が消費される (この場合検証は失敗します) かいずれかの値の検証が成功するまで、各値について検証が行われます。 少なくとも 1 つの対象ユーザーを指定する必要があります。|いいえ |  
|issuer-signing-keys|署名付きトークンの検証に使用する base64 でエンコードされたセキュリティ キーの一覧。 セキュリティ キーが複数存在する場合は、すべてのキーが消費される (この場合検証は失敗します) かいずれかのキーの検証が成功する (トークンのロールオーバーに使用されます) まで、各キーについて検証が行われます。 キー要素には、`kid` クレームとの照合に使用される `id` 属性を必要に応じて設定できます。|いいえ |  
|issuers|トークンを発行した、許容されるプリンシパルの一覧。 発行者の値が複数存在する場合は、すべての値が消費される (この場合検証は失敗します) かいずれかの値の検証が成功するまで、各値について検証が行われます。|いいえ |  
|openid-config|署名キーと発行者を取得可能な準拠している Open ID 構成エンドポイントを指定するために使用する要素。|いいえ |  
|required-claims|トークン上に存在すると予測される、有効とみなすクレームの一覧を記載します。 `match` 属性を `all` に設定した場合、検証が成功するにはポリシー内のクレーム値がすべてトークン内に存在する必要があります。 `match` 属性を `any` に設定した場合、検証が成功するには少なくとも 1 つのクレームがトークン内に存在する必要があります。|いいえ |  
|zumo-master-key|Azure Mobile Services によって発行されたトークンのマスター キー|いいえ |  
  
### <a name="attributes"></a>属性  
  
|Name|説明|必須|既定値|  
|----------|-----------------|--------------|-------------|  
|clock-skew|期間。 トークンの発行者と API Management インスタンスのシステム クロックの間に予想される最大時間差を指定するために使用します。|いいえ |0 秒|  
|failed-validation-error-message|JWT が検証で不合格となった場合に HTTP 応答本文で返すエラー メッセージ。 このメッセージ内では、特殊文字を適切にエスケープする必要があります。|いいえ |既定のエラー メッセージは検証の問題によって異なります ("JWT not present" (JWT が存在しません) など)。|  
|failed-validation-httpcode|JWT が検証で不合格となった場合に返す HTTP 状態コード。|いいえ |401|  
|header-name|トークンを保持する HTTP ヘッダーの名前。|`header-name` と `query-parameter-name` はどちらかを指定する必要がありますが、両方を指定する必要はありません。|該当なし|  
|id|`key` 要素の `id` 属性を使用すると、署名検証用の適切なキーを確認するためにトークン内の `kid` クレーム (存在する場合) と照合する文字列を指定できます。|いいえ |該当なし|  
|match|`claim` 要素の `match` 属性では、検証が成功するためにポリシー内のクレーム値がすべてトークン内に存在する必要があるかどうかを指定します。 次のいずれかの値になります。<br /><br /> -                          `all` - 検証が成功するには、ポリシー内のクレーム値がすべてトークン内に存在する必要があります。<br /><br /> -                          `any` - 検証が成功するには、ポリシー内のクレーム値が少なくとも 1 つトークン内に存在する必要があります。|いいえ |すべて|  
|query-paremeter-name|トークンを保持するクエリ パラメーターの名前。|`header-name` と `query-paremeter-name` はどちらかを指定する必要がありますが、両方を指定する必要はありません。|該当なし|  
|require-expiration-time|ブール値。 トークン内に有効期限クレームが存在する必要があるかどうかを指定します。|いいえ |true|
|require-scheme|トークンの名前。例: "Bearer"。 この属性が設定されている場合、ポリシーは指定したスキームが承認ヘッダーの値に存在していることを確認します。|いいえ |該当なし|
|require-signed-tokens|ブール値。 トークンに署名が必要かどうかを指定します。|いいえ |true|  
|separator|文字列 をオンにします。 複数値を含む要求から一連の値を抽出するために使用する区切り記号を指定します (例: ",")。|いいえ |該当なし| 
|url|Open ID 構成メタデータを取得可能な Open ID 構成エンドポイントの URL。 応答は、URL で定義されている仕様に従っている必要があります:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`。  Azure Active Directory の場合は、`https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` という URL をご使用のディレクトリ テナント名 (`contoso.onmicrosoft.com` など) に置き換えて使用します。|[はい]|該当なし|  
  
### <a name="usage"></a>使用法  
 このポリシーは、次のポリシー [セクション](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)と[スコープ](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)で使用できます。  
  
-   **ポリシー セクション:** inbound  
-   **ポリシー スコープ:** グローバル、製品、API、操作  
  
## <a name="next-steps"></a>次の手順

ポリシーを使用する方法の詳細については、次のトピックを参照してください。

+ [API Management のポリシー](api-management-howto-policies.md)
+ [API を変換する](transform-api.md)
+ ポリシー ステートメントとその設定の一覧に関する[ポリシー リファレンス](api-management-policy-reference.md)
+ [ポリシーのサンプル](policy-samples.md)   
