---
title: 没有为任何用户预配 Azure AD 库应用程序 | Microsoft Docs
description: 当在针对用户预配使用 Azure AD 配置的 Azure AD 库应用程序中没有看到用户时，如何解决面对的常见问题
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b80539181e6614283b6170b9cd9d4db85f812a5f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879891"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>没有为任何用户预配 Azure AD 库应用程序
为应用程序配置自动化预配后（包括验证提供给 Azure AD 以连接至该应用的应用凭据是否有效），用户和/或组将预配到应用。 预配取决于以下事情：

-   哪些用户和组已**分配**到应用程序。 请注意, 不支持预配嵌套组或 Office 365 组。 有关分配的详细信息，请参阅[在 Azure Active Directory 中向企业应用分配用户或组](assign-user-or-group-access-portal.md)。
-   **属性映射**是否已启用，并且是否经过配置，可将有效属性从 Azure AD 同步至应用。 有关属性映射的详细信息，请参阅[为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射](customize-application-attributes.md)。
-   是否存在根据特定属性值筛选用户的**作用域筛选器**。 有关作用域筛选器的详细信息，请参阅[通过作用域筛选器基于属性预配应用程序](define-conditional-rules-for-provisioning-user-accounts.md)。
  
如果发现用户没有预配，请在 Azure AD 中查阅审核日志。 在日志条目中搜索特定用户。

可在 Azure 门户中访问预配审核日志，具体位置在 **“Azure Active Directory”&gt;“企业应用”&gt;“应用程序名称”\[\]“审核日志”&gt;** 选项卡。在“帐户预配”类别上筛选日志，以仅查看该应用的预配事件。 可根据在属性映射中为用户配置的“匹配 ID”搜索用户。 例如，如果在 Azure AD 端将“用户主体名称”或“电子邮件地址”配置为匹配属性，并且尚未预配的用户的值为“audrey@contoso.com”，然后在审核日志中搜索“audrey@contoso.com”，并查看返回的条目。

预配审核日志记录了预配服务执行的全部操作，包括查询 Azure AD 以找到预配作用域中分配的用户、查询目标应用以验证是否存在这些用户以及比较系统之间的用户对象。 然后根据比较结果在目标系统中添加、更新或禁用用户帐户。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>要考虑的有关预配的常规问题范围
以下为常规问题范围列表，如果知道从何处着手，可做一番深入了解。

- [预配服务似乎未启动](#provisioning-service-does-not-appear-to-start)
- [即使用户已分配，审核日志还是显示他们已被跳过且未预配](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>预配服务似乎未启动
如果在 Azure 门户的“Azure Active Directory”&gt;“企业应用”&gt;“应用程序名称”&gt;“预配”部分中将“预配状态”设置为“打开” **\[\]** 。 但是在后续重新加载后该页面未显示其他状态详细信息，则有可能是因为该服务正在运行，但尚未完成初始同步。 检查上述**审核日志**以确定服务正在执行哪些操作，以及是否出现任何错误。

>[!NOTE]
>初始同步可能会持续 20 分钟至几小时，具体取决于 Azure AD 目录的大小与作用域中的预配用户数量。 在初始同步之后同步速度会变快，因为在初始同步后，预配服务将存储代表两个系统状态的水印。此初始同步会提升后续同步的性能。
>


## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>即使用户已分配，审核日志还是显示他们已被跳过且未预配

当用户在审核日志中显示为“已跳过”时，请务必阅读日志消息中已扩展的详细信息，以确定原因。 以下为常见的原因和解决方法：

- **已配置作用域筛选器**，**它根据属性值筛选用户**。 有关范围筛选器的详细信息，请参阅[范围筛选器](define-conditional-rules-for-provisioning-user-accounts.md)。
- **该用户“未得到有效授权”。** 如果看到此特定错误消息，是因为存储在 Azure AD 中的用户分配记录出现问题。 要解决此问题，请从应用中取消分配用户（或组），并重新分配。 有关分配的详细信息，请参阅[分配用户或组访问权限](assign-user-or-group-access-portal.md)。
- **所需的属性丢失或未替用户填写。** 在设置预配时需考虑的重要一点是查看并配置属性映射和工作流，它们可以确定哪些用户（或组）属性将从 Azure AD 流向应用程序。 此配置包括设置用于在两个系统之间唯一地标识和匹配用户/组的“匹配属性”。 有关此重要过程的详细信息，请参阅[为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射](customize-application-attributes.md)。
- **组的属性映射：** 如果某些应用程序支持，除了成员以外，还可以对组名和详细信息进行预配。 可通过对“预配”选项卡中显示的组对象启用或禁用**映射**，从而启用或禁用此功能。如果启用预配组，请务必查看属性映射以确保相应字段用于“匹配 ID”。 此匹配 ID 可以是显示名称或电子邮件别名。 如果匹配属性为空，或没有为 Azure AD 中的某个组填写时，该组及组成员将不会进行预配。

## <a name="next-steps"></a>后续步骤

[Azure AD Connect 同步：了解声明性预配](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
