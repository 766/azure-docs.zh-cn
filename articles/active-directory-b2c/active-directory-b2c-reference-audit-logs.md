---
title: Azure Active Directory B2C 中的审核日志示例和定义 | Microsoft Docs
description: 访问 Azure AD B2C 审核日志的指南和示例。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d8cc67b8e243fb2b97cd1522a850adc63c84428e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69969630"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>访问 Azure AD B2C 审核日志

Azure Active Directory B2C (Azure AD B2C) 发出审核日志，其中包含有关 B2C 资源、颁发的令牌和管理员访问权限的活动信息。 本文简要概述了通过审核日志可获取的信息并介绍了如何为 Azure AD B2C 租户访问此数据。

> [!IMPORTANT]
> 审核日志仅保留七天。 如果需要保留更长时间，请使用下面所示的方法计划下载并存储日志。

> [!NOTE]
> 不能在“Azure Active Directory”或“Azure AD B2C”边栏选项卡的“用户”部分查看各个 Azure AD B2C 应用程序的用户登录。 该处的登录会显示用户活动，但不能回过头来将其与用户登录到的 B2C 应用程序相关联。 必须使用其审核日志，这一点会在本文中进一步阐述。

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>审核日志的 B2C 类别中提供的活动的概述
审核日志中的“B2C”类别包含以下类型的活动：

|活动类型 |描述  |
|---------|---------|
|Authorization |涉及授权用户访问 B2C 资源（例如，管理员访问 B2C 策略列表）的活动         |
|目录 |与管理员使用 Azure 门户登录时检索到的目录属性相关的活动 |
|应用程序 | 与 B2C 应用程序相关的 CRUD 操作 |
|Key |与 B2C 密钥容器中存储的密钥相关的 CRUD 操作 |
|Resource |与 B2C 资源（如策略和标识提供者）相关的 CRUD 操作
|身份验证 |用户凭据和令牌颁发的验证|

> [!NOTE]
> 有关用户对象 CRUD 活动，请参阅“核心目录”类别。

## <a name="example-activity"></a>示例活动
下面的示例显示了用户使用外部标识提供者登录时捕获的数据：  
    ![Azure 门户中的审核日志活动详细信息页的示例](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

活动详细信息面板包含以下相关信息：

|节|字段|描述|
|-------|-----|-----------|
| activities | 姓名 | 发生了哪项活动。 例如，“向应用程序颁发 id_token”（这将结束实际的用户登录）。 |
| 发起者（参与者） | ObjectId | 用户登录的 B2C 应用程序的**对象 ID**（此标识符在 Azure 门户中不可见，但可以通过其他方式（例如图形 API）进行访问）。 |
| 发起者（参与者） | SPN | 用户登录的 B2C 应用程序的**应用程序 ID**。 |
| 目标 | ObjectId | 正在登录的用户的**对象 ID**。 |
| 更多详细信息 | TenantId | Azure AD B2C 租户的**租户 ID**。 |
| 更多详细信息 | PolicyId | 用于登录用户的用户流（策略）的**策略 ID**。 |
| 更多详细信息 | ApplicationId | 用户登录的 B2C 应用程序的**应用程序 ID**。 |

## <a name="accessing-audit-logs-through-the-azure-portal"></a>通过 Azure 门户访问审核日志
1. 转到 [Azure 门户](https://portal.azure.com)。 请确保位于 B2C 目录。
2. 单击左侧收藏夹栏中的“Azure Active Directory”

    ![左侧门户菜单中突出显示的 Azure Active Directory 按钮](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. 在“活动”下，单击“审核日志”

    ![菜单的“活动”部分突出显示的“审核日志”按钮](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. 在“类别”下拉列表中，选择“B2C”
3. 单击“应用”

    ![“审核日志”筛选器中突出显示的“类别和应用”按钮](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

可看到最近七天记录的活动列表。
- 使用“活动资源类型”下拉列表，按上述活动类型进行筛选
- 使用“日期范围”下拉列表，筛选所示活动的日期范围
- 如果单击列表中的特定行，右侧的上下文框会显示与该活动关联的其他属性
- 单击“下载”可将活动下载为 csv 文件

> [!NOTE]
> 也可导航到 **Azure AD B2C** 而不是左侧收藏夹栏中的 **Azure Active Directory**，以这种方式查看审核日志。 在“活动”下单击“审核日志”，在其中会找到具有类似筛选功能的相同日志。

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>通过 Azure AD 报告 API 访问审核日志
审核日志将发布到与 Azure Active Directory 其他活动相同的管道，因此可通过 [Azure Active Directory 报告 API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)进行访问。

### <a name="prerequisites"></a>先决条件
若要向 Azure AD 报告 API 进行身份验证，首先需要注册应用程序。 请确保遵循[访问 Azure AD 报告 API 的先决条件](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)中的步骤。

### <a name="accessing-the-api"></a>访问 API
若要通过 API 下载 Azure AD B2C 审核日志，需筛选“B2C”类别的日志。 若要按类别筛选，请在调用 Azure AD 报告 API 终结点时使用查询字符串参数，如下所示：

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>PowerShell 脚本
以下脚本提供了使用 PowerShell 查询 Azure AD 报告 API 并将结果存储为 JSON 文件的示例：

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```
