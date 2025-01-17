---
title: 配置 Azure Active Directory 身份验证 - Azure 应用服务
description: 了解如何为应用服务应用程序配置 Azure Active Directory 身份验证。
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/20/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: a77a41500a9c22aa25d3de396e73a5b2e4c0c419
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033887"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>将应用服务应用配置为使用 Azure Active Directory 登录

> [!NOTE]
> 目前, 不支持 AAD V2 (包括 MSAL) Azure 应用服务和 Azure Functions。 请查看是否有更新。
>

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文说明如何将 Azure 应用服务配置为使用 Azure Active Directory 作为身份验证提供程序。

## <a name="express"> </a>使用快速设置进行配置

1. 在 [Azure 门户]中，导航到应用服务应用。 在左侧导航栏中，选择“身份验证/授权”。
2. 如果尚未启用“身份验证/授权”，请选择“启用”。
3. 选择“Azure Active Directory”，然后选择“管理模式”下的“快速”。
4. 选择“确定”，在 Azure Active Directory 中注册应用服务应用。 这会创建一个新的应用注册。 如果想要选择现有应用注册，请单击“选择现有应用”，并在租户中搜索以前创建的应用注册的名称。
   单击应用注册将其选中，然后单击“确定”。 然后在 Azure Active Directory 设置页上单击“确定”。
   默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的已授权访问。 必须在应用代码中为用户授权。
5. （可选）若要限制只有通过 Azure Active Directory 身份验证的用户可以访问站点，请将“请求未经身份验证时需执行的操作”设置为“使用 Azure Active Directory 登录”。 这会要求对所有请求进行身份验证，所有未经身份验证的请求将重定向到 Azure Active Directory 以进行身份验证。

> [!CAUTION]
> 以这种方式限制访问权限适用于对应用的所有调用, 对于需要公开提供的主页的应用, 与在许多单页应用程序中一样。 对于此类应用程序, 可以首选 "**允许匿名请求 (无操作)** ", 应用手动启动登录, 如[此处](overview-authentication-authorization.md#authentication-flow)所述。

6. 单击“保存”。

## <a name="advanced"> </a>使用高级设置进行配置

也可以手动提供配置设置。 如果要使用的 Azure Active Directory 租户不同于登录 Azure 所用的租户，这是较好的解决方案。 要完成配置，必须先在 Azure Active Directory 中创建注册，然后向应用服务提供一些注册详细信息。

### <a name="register"> </a>向 Azure Active Directory 注册应用服务应用

1. 登录 [Azure 门户]，并导航到应用服务应用。 复制应用 URL。 稍后要使用此信息配置 Azure Active Directory 应用注册。
2. 导航到“Active Directory”，选择“应用注册”，然后单击顶部的“新应用程序注册”开始新应用注册。 
3. 在“创建”页中，输入应用注册的“名称”，选择“Web 应用/API”类型，在“登录 URL”框中粘贴应用程序 URL（来自于步骤 1）。 然后单击“创建”。
4. 几秒钟后即可看到刚刚创建的新应用注册。
5. 添加应用注册后，单击应用注册名称，单击顶部的“设置”，然后单击“属性” 
6. 在“应用 ID URI”框中粘贴应用程序 URL（来自于步骤 1），并在“主页 URL”中粘贴应用程序 URL（来自于步骤 1），然后单击“保存”
7. 现在, 单击**回复**url, 编辑**回复 Url**, 粘贴应用程序 url (从步骤 1), 然后将其追加到 URL 的末尾, */.auth/login/aad/callback* (例如`https://contoso.azurewebsites.net/.auth/login/aad/callback`)。 单击“保存”。

   > [!NOTE]
   > 可以通过添加其他**回复 url**, 为多个域使用相同的应用注册。 请确保对每个应用服务实例进行建模, 使其拥有自己的注册, 使其拥有自己的权限和许可。 还应考虑对单独的站点槽使用单独的应用注册。 这是为了避免在环境之间共享权限, 以便测试的新代码中的 bug 不会影响生产。
    
8. 此时，复制应用的“应用程序 ID”。 保留此 ID 供将来使用。 需要使用它来配置应用服务应用。
9. 关闭“已注册应用”页。 在“应用注册”页中，单击顶部的“终结点”按钮，然后复制“WS-FEDERATION SIGN-ON ENDPOINT”URL，但要从 URL 中删除 `/wsfed` 末尾。 最终结果应类似于 `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000`。 主权云的域名可能有所不同。 这将作为更高版本的颁发者 URL。

### <a name="secrets"> </a>将 Azure Active Directory 信息添加到应用服务应用

1. 返回 [Azure 门户]，导航到应用服务应用。 单击“身份验证/授权”。 如果“身份验证/授权”功能未启用，请切换为“打开”。 单击验证提供程序下的“Azure Active Directory”，配置应用。

    （可选）默认情况下，应用服务提供身份验证但不限制对站点内容和 API 的已授权访问。 必须在应用代码中为用户授权。 将“请求未经身份验证时需执行的操作”设置为“使用 Azure Active Directory 登录”。 此选项会要求对所有请求进行身份验证，所有未经身份验证的请求都重定向到 Azure Active Directory 进行身份验证。
2. 在 Active Directory 身份验证配置中，单击“管理模式”下的“高级”。 将应用程序 ID（来自于步骤 8）粘贴到“客户端 ID”框中，并将 URL（来自于步骤 9）粘贴到证书颁发者 URL 值。 然后单击“确定”。
3. 在 Active Directory 身份验证配置页上，单击“保存”。

现在，可以使用 Azure Active Directory 在应用服务应用中进行身份验证。

## <a name="configure-a-native-client-application"></a>配置本机客户端应用程序
可以注册权限映射控制度更高的本机客户端。 如果想要使用 Active Directory 身份验证库等客户端库进行登录，则需要这种注册。

1. 在 [Azure 门户]中，导航到“Active Directory”。
2. 在左侧导航栏中，选择“应用注册”。 在顶部单击“新建应用注册”。
4. 在“创建”页中，为应用注册输入“名称”。 在“应用程序类型”中选择“本机”。
5. 在“重定向 URI”框中，使用 HTTPS 方案输入站点的 */.auth/login/done* 终结点。 此值应类似于 *https://contoso.azurewebsites.net/.auth/login/done* 。 如果要创建 Windows 应用程序，请改用[包 SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) 作为 URI。
5. 单击“创建”。
6. 在成功添加应用注册后，选择该应用注册将其打开。 找到应用程序 ID 并记下此值。
7. 单击“所有设置” > “所需的权限” > “添加” > “选择 API”。
8. 键入先前注册的应用服务应用的名称进行搜索，然后选中该应用并单击“选择”。
9. 选择“访问 \<app_name>”。 然后单击“选择”。 然后单击“完成”。

现已配置可以访问应用服务应用的本机客户端应用程序。

## <a name="related-content"> </a>相关内容

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure 门户]: https://portal.azure.com/
[alternative method]:#advanced
