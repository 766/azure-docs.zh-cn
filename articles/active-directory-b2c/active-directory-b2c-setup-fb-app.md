---
title: 设置使用 Facebook 帐户的注册和登录-Azure Active Directory B2C
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 Facebook 帐户注册与登录的功能。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 524e1e5f877fcb03d4252d79635ef855b9811f09
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622095"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 Facebook 帐户注册与登录

## <a name="create-a-facebook-application"></a>创建 Facebook 应用程序

若要将 Facebook 帐户用作 Azure Active Directory (Azure AD) B2C 中的[标识提供者](active-directory-b2c-reference-oauth-code.md)，需要在表示它的租户中创建一个应用程序。 如果还没有 Facebook 帐户, 可以在[https://www.facebook.com/](https://www.facebook.com/)注册。

1. 使用 Facebook 帐户凭据登录 [Facebook 开发人员](https://developers.facebook.com/)。
1. 如果以前没有登录过，需要注册为 Facebook 开发人员。 为此, 请选择页面右上角的 "**入门**", 接受 Facebook 的策略, 并完成注册步骤。
1. 选择 **"我的应用"** , 并**添加新应用**。
1. 输入“显示名称”和有效的“联系人电子邮件”。
1. 单击“创建应用程序 ID”。 这会要求接受 Facebook 平台策略并完成在线安全检查。
1. 选择“设置” > “基本”。
1. 选择“类别”，例如 `Business and Pages`。 此值是 Facebook 必需的，但不用于 Azure AD B2C。
1. 在页面底部，选择“添加平台”，然后选择“网站”。
1. 在“站点 URL”中，输入 `https://your-tenant-name.b2clogin.com/`，将 `your-tenant-name` 替换为你的租户名称。 为“隐私策略 URL”输入一个 URL，例如 `http://www.contoso.com`。 策略 URL 是继续提供应用程序的隐私信息的页面。
1. 选择“保存更改”。
1. 在页面的顶部，复制“应用 ID”的值。
1. 单击“显示”，并复制“应用程序密码”的值。 使用这两个值将 Facebook 配置为租户中的标识提供者。 “应用程序密码”是一个非常重要的安全凭据。
1. 选择 "**产品**" 旁边的加号, 然后选择 " **Facebook 登录**" 下的 "**设置**"。
1. 在 " **Facebook 登录**" 下, 选择 "**设置**"。
1. 在“有效的 OAuth 重定向 URL”中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称。 单击页面底部的“保存更改”。
1. 若要使 Facebook 应用程序可在 Azure AD B2C 中使用，请单击页面右上角的状态选择器，将其设置为“打开”以公开该应用程序，然后单击“确认”。  此时，状态应从“开发”变为“实时”。

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>将 Facebook 帐户配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 通过在顶部菜单中选择 "**目录 + 订阅**" 筛选器并选择包含租户的目录, 确保使用的是包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择 "**标识提供者**", 然后选择 " **Facebook**"。
1. 输入“名称”。 例如*Facebook*。
1. 对于 "**客户端 ID**", 请输入之前创建的 Facebook 应用程序的应用 ID。
1. 对于**客户端密码**, 请输入你记录的应用密码。
1. 选择**保存**。
