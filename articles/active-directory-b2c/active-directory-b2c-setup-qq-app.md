---
title: 使用 Azure Active Directory B2C 设置通过 QQ 帐户注册与登录
description: 使用 Azure Active Directory B2C，为应用程序中的客户提供通过 QQ 帐户注册与登录的功能。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 11fc41374089c0ab7258dd191a255387713836eb
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622308"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 设置通过 QQ 帐户注册与登录

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>创建 QQ 应用程序

要将 QQ 帐户用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要在表示它的租户中创建一个应用程序。 如果还没有 QQ 帐户, 可以在[https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033)注册。

### <a name="register-for-the-qq-developer-program"></a>注册 QQ 开发人员计划

1. 使用 QQ 帐户凭据登录 [QQ 开发人员门户](http://open.qq.com)。
1. 登录后，请转到 [http://open.qq.com/reg](http://open.qq.com/reg) 将自己注册为开发者。
1. 选择“个人”(individual developer)。
1. 输入所需的信息，并选择“下一步” (next step)。
1. 完成电子邮件验证过程。 注册为开发人员后，需要等待几天，以获得批准。

### <a name="register-a-qq-application"></a>注册 QQ 应用程序

1. 转到[https://connect.qq.com/index.html](https://connect.qq.com/index.html)。
1. 单击“应用管理”(app management)。
1. 选择“创建应用” (create app)，然后输入所需的信息。
1. 在“授权回调域”(callback URL) 中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp`。 例如，如果 `tenant_name` 是 contoso，请将 URL 设置为 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`。
1. 选择“创建应用”(create app)。
1. 在确认页上选择“应用管理”(app management) 返回应用管理页。
1. 选择刚刚创建的应用旁边的“查看”(view)。
1. 选择“修改”(edit)。
1. 复制“应用 ID”和“应用密钥”。 将标识提供者添加到租户时需要这两个值。

## <a name="configure-qq-as-an-identity-provider"></a>将 QQ 配置为标识提供者

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
1. 通过在顶部菜单中选择 "**目录 + 订阅**" 筛选器并选择包含租户的目录, 确保使用的是包含 Azure AD B2C 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
1. 选择 "**标识提供者**", 然后选择 " **QQ (预览版)** "。
1. 输入“名称”。 例如, *QQ*。
1. 对于 "**客户端 ID**", 请输入之前创建的 QQ 应用程序的应用 ID。
1. 对于**客户端密码**, 请输入你记录的应用密钥。
1. 选择**保存**。
