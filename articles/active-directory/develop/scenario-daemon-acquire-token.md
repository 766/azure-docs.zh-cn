---
title: 调用 Web API 的守护程序应用（获取应用的令牌）- Microsoft 标识平台
description: 了解如何构建调用 Web API 的守护程序应用（获取令牌）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a5f15aa5264c0abf87cb15f0468e8a3a924e0b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562358"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>用于调用 Web API 的守护程序应用 - 获取令牌

构建机密客户端应用程序以后，可以获取应用的令牌，方法是调用 ``AcquireTokenForClient``，传递作用域，然后强制刷新或不刷新令牌。

## <a name="scopes-to-request"></a>请求的作用域

请求客户端凭据流时，其作用域是资源的名称后跟 `/.default`。 这种表示法告知 Azure AD 使用在应用程序注册过程中静态声明的**应用程序级权限**。 另外，如前所示，这些 API 权限必须由租户管理员授予

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

在 MSAL.Python 中，该配置文件应该如以下代码片段所示：

```Python
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "secret": "This is a sample only. You better NOT persist your password."
    "scope": ["https://graph.microsoft.com/.default"]
}
```

### <a name="java"></a>Java

```Java
public final static String KEYVAULT_DEFAULT_SCOPE = "https://vault.azure.net/.default";
```

### <a name="all"></a>全部

用于客户端凭据的作用域应该始终为 resourceId+"/.default"

### <a name="case-of-azure-ad-v10-resources"></a>Azure AD (1.0) 资源的情况

> [!IMPORTANT]
> 对于 MSAL (Microsoft 标识平台终结点), 请求获取用于接受 v1.0 访问令牌的资源的访问令牌, Azure AD 通过使用最后一个斜杠之前的所有内容并将其用作资源标识符, 来分析所请求范围内的所需受众。
> 因此，如果像 Azure SQL ( **https://database.windows.net** ) 一样，资源预期受众以斜杠结尾（例如 Azure SQL：`https://database.windows.net/` ），则需请求作用域 `https://database.windows.net//.default` （注意其中的双斜杠）。 另请参阅 MSAL.NET 问题 [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)：将省略资源 URL 的尾部斜杠，因为该斜杠会导致 SQL 身份验证失败。

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

### <a name="net"></a>.NET

```CSharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

#### <a name="application-token-cache"></a>应用程序令牌缓存

在 MSAL.NET 中，`AcquireTokenForClient` 使用**应用程序令牌缓存**（所有其他 AcquireTokenXX 方法使用用户令牌缓存）。请勿在调用 `AcquireTokenForClient` 之前调用 `AcquireTokenSilent`，因为 `AcquireTokenSilent` 使用**用户**令牌缓存。 `AcquireTokenForClient` 会检查**应用程序**令牌缓存本身并对其进行更新。

### <a name="python"></a>Python

```Python
# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

### <a name="java"></a>Java

```Java
ClientCredentialParameters parameters = ClientCredentialParameters
        .builder(Collections.singleton(KEYVAULT_DEFAULT_SCOPE))
        .build();

CompletableFuture<AuthenticationResult> future = cca.acquireToken(parameters);

// You can complete the future in many different ways. Here we use .get() for simplicity
AuthenticationResult result = future.get();
```

### <a name="protocol"></a>Protocol

如果还没有所选语言的库，则可能需要直接使用协议：

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>第一种情况：使用共享机密访问令牌请求

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>第二种情况：使用证书访问令牌请求

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

### <a name="learn-more-about-the-protocol"></a>详细了解协议

有关详细信息，请参阅协议文档：[Microsoft 标识平台和 OAuth 2.0 客户端凭据流](v2-oauth2-client-creds-grant-flow.md)。

## <a name="troubleshooting"></a>疑难解答

### <a name="did-you-use-the-resourcedefault-scope"></a>你是否使用过 resource/.default 作用域？

如果出现一条错误消息，指出所使用的作用域无效，则表明你并未使用 `resource/.default` 作用域。

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>你是否忘记提供管理员许可？ 守护程序应用需要它！

如果在调用 API 时出现错误“权限不足，无法完成该操作”，则租户管理员需要授予对应用程序的权限。 请查看上面的步骤 6：注册客户端应用。
通常会看到类似以下错误描述的错误：

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [守护程序应用 - 调用 Web API](scenario-daemon-call-api.md)