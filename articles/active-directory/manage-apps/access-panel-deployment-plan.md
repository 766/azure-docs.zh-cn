---
title: 规划 Azure Active Directory 访问面板部署
description: 有关部署 Azure AD 访问面板功能的指南
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 448af7075297c2b18df1eceaeaac34d5da762489
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576519"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>规划 Azure Active Directory 访问面板部署

Azure Active Directory 访问面板是一个基于 web 的门户, 可让你降低支持成本, 提高工作效率和安全性, 并减少用户不满。 系统包括详细的报告, 用于跟踪用户访问系统的时间, 并通知管理员误用或滥用。

使用 Azure Active Directory 访问面板, 用户可以:

* 发现并访问其公司的所有 Azure Active Directory 连接资源, 如应用程序。
* 请求访问新的应用和组, 或管理其他人对这些资源的访问权限。
* 管理自助服务密码重置和多重身份验证设置。
* 管理其设备。

它还允许管理员管理:

* 服务条款
* 组织
* 访问审阅


## <a name="benefits-of-azure-active-directory-access-panel-integration"></a>Azure Active Directory 访问面板集成的优点

Microsoft Azure Active Directory (Azure AD) 访问面板通过以下方式提高企业的优势:

**提供直观的用户体验**:通过启用访问面板, 用户可以在已连接的应用程序上为其所有 Azure 单一登录提供单一启动板。 添加了组管理和自助密码重置等功能后, 用户可以继续使用统一门户来查找这些设置。 直观的体验使用户能够更快地工作并提高工作效率, 同时降低其不满。

**提高工作效率**:访问面板中的所有用户应用程序均已启用单一登录 (SSO)。 跨企业应用程序和 Office 365 启用单一登录可为现有用户提供出色的登录体验, 从而减少或消除额外的登录提示。 访问面板支持自助服务和动态成员身份, 并通过确保适当的人员管理对应用程序的访问, 提高标识系统的整体安全性。 访问面板充当一个连贯的登陆页面, 用户可在其中快速查找资源和继续工作任务。

**管理成本**:启用带有 Azure Active Directory 的访问面板可能会允许 divestment 本地基础结构。 它使用户能够拥有一致的门户来查找其所有应用、请求访问资源和管理其帐户, 从而降低了支持成本。

**提高灵活性和安全性**:使用访问面板可以访问云平台提供的安全性和灵活性。 管理员可以轻松地更改应用程序和资源的设置, 并在不影响用户的情况下适应新的安全要求。

**启用可靠的审核和使用情况跟踪**:对于所有最终用户功能的审核和使用情况跟踪, 可让用户了解用户使用其资源的时间, 并确保评估安全性。

### <a name="licensing-considerations"></a>许可注意事项

访问面板是免费的, 不需要任何许可证即可在基本的 "所有用户" 中使用。 但是, 目录中的对象数以及要部署的功能可能需要额外的许可证。 常见的 Azure AD 方案包括以下具有许可要求的安全功能。

* [Azure 多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [基于组的成员资格](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [自助服务密码要求](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [标识保护](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

请参阅[Azure Active Directory 的完整许可指南](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>部署 Azure AD 访问面板的先决条件

在此项目开始之前, 必须先完成以下先决条件。

* [应用程序 SSO 集成](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [Azure AD 用户和组基础结构](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>规划 Azure AD 访问面板部署

下表概述了访问面板部署的主要用例:

| 区域| 描述 |
| - | - |
| 访问权限| 可以从企业网络中的公司和个人设备访问访问面板门户。 |
|访问权限 | 企业网络外部的公司设备可以访问访问面板门户。 |
| 审核| 使用情况数据至少每29天下载到企业系统。 |
| 调控| 定义和监视 Azure AD 连接的应用程序和组的用户分配生命周期。 |
| 安全性| 可以通过用户和组分配来控制对资源的访问权限。 只有经过授权的用户可以管理资源访问权限。 |
| 性能| 访问分配传播时间线已记录并被监视。 |
| 用户体验| 用户了解访问面板功能以及如何使用它们。|
| 用户体验| 用户可以自行管理对应用程序和组的访问权限。|
| 用户体验| 用户可以管理其帐户。 |
| 用户体验| 用户了解浏览器的兼容性。 |
| 支持| 用户可以找到对访问面板问题的支持。 |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>部署 Azure AD 访问面板的最佳实践

访问面板的功能可以逐渐启用。 建议遵循以下部署顺序:

1. 我的应用
   * 应用启动器
   * 自助服务应用管理
   * Microsoft Office 365 集成

1. 自助服务应用程序发现
   * 自助密码重置
   * 多重身份验证设置
   * 设备管理
   * 使用条款
   * 管理组织

1. 我的组
   * 自助组管理
1. 访问审阅
   * 访问评审管理

从 "我的应用" 开始, 将用户作为访问资源的公用位置引入门户。 自助服务应用程序发现的添加是在 "我的应用" 体验的基础上进行的。 我的组和访问权限审阅了自助服务功能上的版本。

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>Azure AD 访问面板的规划配置

下表列出了几个重要的访问面板配置和您可能使用的典型值:

| 配置| 典型值 |
| - | - |
| 确定试验组| 确定要使用的 Azure AD 安全组, 并确保所有试点成员都是组的一部分。 |
| 确定要为生产启用的组。| 确定要使用的 Azure AD 安全组或同步到 Azure AD 的 AD 组。 确保所有试点成员都是组的一部分。 |
| 允许用户对哪些类型的应用程序使用单一登录| 联合 SSO, OAuth, 密码 SSO, 应用代理 |
| 允许用户使用自助服务密码重置| 是 |
| 允许用户使用多重身份验证| 是 |
| 允许用户对哪些类型的组使用自助服务组管理| 安全组, O365 组 |
| 允许用户使用自助服务应用管理| 是 |
| 允许用户使用访问评审| 是 |

### <a name="plan-consent-strategy"></a>计划同意策略

用户或管理员必须同意任何应用程序的使用条款和隐私策略。 如果可能, 我们建议使用管理员许可, 这为用户提供了更好的体验。

若要使用管理员许可, 你必须是租户的全局管理员, 并且应用程序必须:

* 已在租户中注册，或

* 在另一个 Azure AD 租户中注册, 并且之前至少由一个最终用户同意。

有关详细信息, 请参阅[在 Azure Active Directory 中配置最终用户同意应用程序的方式](configure-user-consent.md)。

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

当技术项目失败时, 他们通常会这样做, 因为对影响、结果和责任的预期不匹配。 若要避免这些问题, 请[确保你参与了正确的利益干系人](../fundamentals/active-directory-deployment-plans.md), 并且项目中的利益干系人角色非常了解。

### <a name="plan-communications"></a>规划沟通

通信对于任何新服务的成功至关重要。 主动向用户通知其体验的发生时间和时间, 以及如何在需要时获得支持。

尽管访问面板通常不会创建用户问题, 但必须做好准备。 开始之前, 请为支持人员创建指南和所有资源的列表。

#### <a name="communications-templates"></a>通信模板

Microsoft 为访问面板的[电子邮件和其他通信提供可自定义的模板](https://aka.ms/APTemplates)。 你可以根据你的企业文化调整这些资产, 以便在其他通信通道中使用。

## <a name="plan-your-sso-configuration"></a>规划 SSO 配置

当用户登录到某个应用程序时, 他们将经历身份验证过程, 并需要证明他们是谁。 如果没有单一登录, 则会在应用程序中存储密码, 用户需要知道此密码。 通过单一登录 (SSO) 用户的凭据传递到应用程序, 因此他们无需为每个应用程序重新输入密码。

若要在 "我的应用" 中启动应用程序, 必须为应用程序启用单一登录 (SSO)。

Azure AD 支持三种不同的方式来启用[对应用程序的单一登录](what-is-single-sign-on.md):

* **联合单一登录** 
    * 允许应用程序重定向到用于用户身份验证的 Azure AD, 而不是提示输入密码。 
    * 使用 SAML 2.0、WS 联合身份验证或 OpenID Connect 等协议的应用程序支持, 它是单一登录的最丰富的模式。

* **基于密码的单一登录** 
    * 使用 web 浏览器扩展或移动应用启用安全的应用程序密码存储和重播。 
    * 利用应用程序提供的现有登录过程, 但允许管理员管理密码。 用户不需要知道密码。

* **现有单一登录** 
    * 使 Azure AD 能够利用已为应用程序配置的任何现有单一登录。
    * 允许将这些应用程序链接到 Office 365 或 Azure AD 访问面板门户。 
    * 在应用程序启动时, 在 Azure AD 中启用其他报表。 
    * 包括使用 Azure 应用程序代理和链接的单一登录模式。

在此处了解如何配置应用程序的 SSO 模式:[单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

为了获得最佳的 "我的应用" 页面, 我们建议从可用于联合 SSO 的云应用程序集成。 联合 SSO 使用户能够在应用程序启动表面上保持一致的一键式体验, 并在配置控制中更可靠。

当应用程序支持联合 SSO 与 Azure AD (OpenID Connect/SAML) 时, 请使用联合 SSO, 而不是基于密码的 SSO 和 ADFS。

有关如何部署和配置 SaaS 应用程序的详细信息, 请参阅[SAAS SSO 部署计划](https://aka.ms/deploymentplans/sso)。

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>规划部署 "我的应用" 浏览器扩展

如果最终用户可以使用基于密码的 SSO 应用程序, 则需要安装 "我的应用" 安全登录扩展才能登录。 扩展执行脚本, 将密码传输到应用程序的登录窗体。 首次启动基于密码的 SSO 应用程序时, 系统将提示用户安装扩展。 有关扩展的详细信息, 请参阅有关[安装访问面板浏览器扩展](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)的文档。

如果必须集成基于密码的 SSO 应用程序, 则应定义一种机制, 以使用[受支持的浏览器](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)大规模部署扩展。 选项包括：

* [Internet Explorer 组策略](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [Internet Explorer System Center Configuration Manager (SCCM)](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)

* [用户驱动的 Chrome、Firefox、Microsoft Edge 或 IE 的下载和配置](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

了解更多：[如何配置密码单一登录](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)。

不使用基于密码的 SSO 应用程序的用户也受益于扩展。 这些优势包括: 从其搜索栏启动任何应用、查找对最近使用的应用程序的访问权限, 以及链接到 "我的应用程序" 页。

下面是首次启动基于密码的 SSO 应用程序时用户将看到的内容:

![我的应用浏览器扩展安装屏幕屏幕截图 ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>规划移动访问

使用 Intune 策略 (Microsoft Edge 或 Intune Managed Browser) 保护的浏览器对于启动基于密码的 SSO 应用程序的移动用户是必需的。 受策略保护的浏览器允许传输为应用程序保存的密码。 Microsoft Edge 或托管浏览器提供了一组 web 数据保护功能。 你还可以使用 Microsoft Edge 在 iOS 和 Android 设备上使用企业方案。 Microsoft Edge 支持与 Intune Managed Browser 相同的管理方案, 并改进了最终用户体验。 了解更多：[使用 Microsoft Intune 策略保护的浏览器管理 web 访问](https://docs.microsoft.com/intune/app-configuration-managed-browser)

## <a name="plan-your-my-apps-deployment"></a>规划我的应用部署

访问面板的基础是应用程序启动器 "我的应用程序", 用户可以[https://myapps.microsoft.com](https://myapps.microsoft.com/)在这些应用程序中访问。 "我的应用" 页面为用户准备了一个开始其工作的位置, 并可访问所需的应用程序。 在这里, 用户可以找到他们有权访问单一登录的所有应用程序的列表。 

!["应用" 面板的屏幕截图](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

当用户使用 Office 365 门户时, 将在 Office 365 应用启动器中显示相同的应用程序。

计划将应用程序添加到 "我的应用程序启动器" 的顺序, 以及是将这些应用程序逐步地展开还是同时滚动。 为此, 请创建一个应用程序清单, 其中列出了每个应用程序的身份验证类型和任何现有的单一登录 (SSO) 集成。

#### <a name="add-applications-to-the-my-apps-panel"></a>将应用程序添加到 "我的应用" 面板

任何启用了 SSO 的 Azure AD 应用程序均可添加到 "我的应用" 启动程序。 使用链接的 SSO 选项添加其他应用程序。 你可以配置应用程序磁贴, 该磁贴链接到现有 web 应用程序的 URL。 链接 SSO 使你可以开始将用户定向到 "我的应用" 门户, 而无需将所有应用程序迁移到 Azure Active Directory SSO。 你可以逐步迁移到 Azure AD SSO 配置的应用程序, 而不会中断用户的体验。

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>规划是使用我的应用还是现有门户

你的用户可能已具有应用程序或门户以外的应用程序。 如果是这样, 请决定是否同时支持这两个门户, 或者是否仅使用一个。

如果现有门户已用作用户的起始点, 则可以使用 "用户访问 Url" 来集成我的应用功能。 用户访问 Url 作为指向 "我的应用" 门户中可用的应用程序的直接链接。 这些 Url 可以嵌入到任何现有网站中。 当用户单击该链接时, 它将从 "我的应用" 门户启动该应用程序。

可以在 Azure 门户的应用程序的 "属性" 区域中找到 "用户访问 URL" 属性。

!["应用" 面板的屏幕截图](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>规划自助应用程序发现和访问

将一组核心应用程序部署到用户的 "我的应用" 页后, 建议启用自助服务应用管理功能。 自助服务应用程序发现支持:
* 用户可以在其 "我的应用" 中添加新的应用。 
* 用户在安装过程中添加您可能不知道其所需的可选应用。

审批工作流可用于明确批准访问应用程序。 如果有挂起的应用程序访问请求, 作为审批者的用户将在 "我的应用" 门户中收到通知。

## <a name="plan-self-service-group-membership"></a>规划自助服务组成员身份 

可以让用户在 Azure Active Directory (Azure AD) 中创建和管理其自己的安全组或 Office 365 组。 组的所有者可以批准或拒绝成员身份请求并委派对组成员身份的控制。 自助服务组管理功能不可用于启用邮件的安全组或通讯组列表。

若要规划自助服务组成员身份, 请确定你是否允许组织中的所有用户创建和管理组, 或者仅允许一部分用户。 如果用户的子集, 则需要设置添加到用户的组。 有关启用这些方案的详细信息, 请参阅[在 Azure Active Directory 中设置自助服务组管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)。

## <a name="plan-reporting-and-auditing"></a>规划报告和审核

Azure AD 提供了[提供技术和业务见解的报表](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)。 与您的业务和技术应用程序所有者合作, 以定期获得这些报告并使用这些报告。 下表提供了一些典型报表方案的示例。

|   | 管理风险| 提高工作效率| 管理和符合性 |
|  - |- | - | - |
| 报表类型|  应用程序权限和使用情况。| 帐户预配活动| 查看谁在访问应用程序 |
| 可能的操作| 审核访问;废除权限| 修正任何预配错误| 撤销访问权限 |

Azure AD 将大多数审核数据保持30天。 可以通过 Azure 管理门户或 API 使用这些数据下载到分析系统。

#### <a name="auditing"></a>审核

用于应用程序访问的审核日志在30天内可用。 如果企业中的安全审核需要更长的保留期, 则需要将日志导出到安全信息事件和管理 (SIEM) 工具 (例如 Splunk 或 ArcSight) 中。

对于审核、报告和灾难恢复备份, 记录所需的下载频率, 目标系统是, 负责管理每个备份的人员。 您可能不需要单独的审核和报告备份。 灾难恢复备份应为单独的实体。

## <a name="deploy-applications-to-users-my-apps-panel"></a>将应用程序部署到用户的 "我的应用" 面板

为 SSO 配置应用程序后, 将为组分配访问权限。 已分配组中的用户将具有访问权限, 并会在 "我的应用" 和 Office 365 应用启动器中看到该应用程序

请参阅[将用户和组分配到 Active Directory 中的应用程序](methods-for-assigning-users-and-groups.md)。

如果在测试或部署过程中要添加组, 但仍不允许应用程序显示在 "我的应用" 中, 请参阅[在 Azure Active Directory 中的用户体验中隐藏应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)。

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>将 Microsoft Office 365 应用程序部署到我的应用程序

对于 Office 365 应用程序, 用户可以基于分配给他们的许可证接收 Office 副本。 访问 Office 应用程序的先决条件是将用户分配到与 Office 应用程序关联的正确许可证。 向用户分配许可证时, 他们将自动在 "我的应用" 页和 O365 应用启动器中看到与该许可证关联的应用程序。

如果要从用户中隐藏一组 Office 应用程序, 可以选择在 "我的应用" 门户中隐藏应用程序, 同时仍允许从 O365 门户进行访问。 在应用程序的 "用户设置" 部分找到这些设置。 了解更多：[在 Azure Active Directory 的用户体验中隐藏应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)。

![配置如何隐藏应用程序的屏幕截图](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>部署应用程序自助服务功能

自助服务应用程序访问权限允许用户自行发现并请求访问任何应用程序。 用户可以自由地访问所需的应用程序, 而无需在每次请求访问权限时通过 IT 组进行访问。 当用户请求访问并由应用所有者自动或手动审批时, 它们将添加到后端上的组中。 在请求、批准或删除了访问权限的用户上启用了报告, 并使你能够控制分配的角色。

你可以将对应用程序访问请求的审批委托给业务审批者。 业务审批者可以直接从业务审批者的 "我的应用" 页设置应用程序访问密码。

了解更多：[如何使用自助服务应用程序访问](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)

![配置自助服务应用程序管理的屏幕截图](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>验证你的部署

确保访问面板部署经过全面测试, 并且已准备好回滚计划。

以下测试适用于公司拥有的设备和个人设备。 这些测试用例还应反映你的业务用例。 下面是基于本文档中的示例业务要求和典型技术方案的一些案例。 添加特定于您需求的其他内容。

#### <a name="application-sso-access-test-case-examples"></a>应用程序 SSO 访问测试用例示例:


| 商业案例| 预期结果 |
| - | -|
| 用户登录到 "我的应用" 门户| 用户可以登录并查看其应用程序 |
| 用户启动联合 SSO 应用程序| 用户已自动登录到应用程序 |
| 用户首次启动密码 SSO 应用程序| 用户需要安装 "我的应用" 扩展 |
| 用户在后续时间启动密码 SSO 应用程序| 用户已自动登录到应用程序 |
| 用户从 O365 门户启动应用| 用户已自动登录到应用程序 |
| 用户从 Managed Browser 启动应用| 用户已自动登录到应用程序 |


#### <a name="application-self-service-capabilities-test-case-examples"></a>应用程序自助服务功能测试用例示例


| 商业案例| 预期结果 |
| - | - |
| 用户可以管理应用程序的成员身份| 用户可以添加/删除有权访问该应用程序的成员 |
| 用户可以编辑应用程序| 用户可以为密码 SSO 应用程序编辑应用程序的描述和凭据 |

### <a name="rollback-steps"></a>回滚步骤

如果你的部署不按计划进行, 则必须规划要执行的操作。 如果在部署过程中 SSO 配置失败, 你必须了解如何[排查 sso 问题](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso)并降低对用户的影响。 在极端情况下, 可能需要[回滚 SSO](../manage-apps/plan-sso-deployment.md)。


## <a name="manage-your-implementation"></a>管理实现

Microsoft 建议使用最小特权角色来完成 Azure Active Directory 中所需的任务。 Microsoft 建议[查看可用的不同角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal), 并选择正确的角色以满足此应用程序的每个角色的需求。 在部署完成后, 可能需要暂时应用某些角色并将其删除。

| 角色| 角色| Azure AD 角色  |
| - | -| -|
| 咨询台管理员| 第1层支持| None |
| 标识管理员| 在问题影响时进行配置和调试 Azure AD| 全局管理员 |
| 应用程序管理员| 应用程序中的用户证明, 具有权限的用户配置| None |
| 基础结构管理员| 证书滚动更新所有者| 全局管理员 |
| 业务所有者/利益干系人| 应用程序中的用户证明, 具有权限的用户配置| 无 |

建议使用[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)来管理角色, 以便为具有目录权限的用户提供其他审核、控制和访问权限。

### <a name="troubleshoot-access-panel-issues"></a>访问面板问题的疑难解答

通过常见方案为你的支持组织创建故障排除指南, 并指向 Microsoft 文档来了解其解决方法。 你可能想要创建一些指导, 以将支持分解为你的组织所使用的层。

有关参考, 请参阅下面的故障排除指南:

[不显示应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[出现意外应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[用户无法登录到访问面板](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[使用自助应用程序访问时的问题](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[浏览器扩展问题](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>后续步骤

[规划 Azure Active Directory 多重身份验证的部署](https://aka.ms/deploymentplans/mfa)
