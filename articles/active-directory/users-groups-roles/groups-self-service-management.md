---
title: 设置自助服务组管理 - Azure Active Directory | Microsoft Docs
description: 在 Azure Active Directory 中创建和管理安全组或 Office 365 组，并可以请求安全组或 Office 365 组成员身份
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b860257fd1b3f0897152dc3d48bff0c7e1d3d994
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60469855"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>设置 Azure Active Directory 中的自助服务组管理 

可以使用户能够创建和管理自己的安全组或 Azure Active Directory (Azure AD) 中的 Office 365 组。 组所有者可以批准或拒绝成员资格请求，并且可以委派对组成员身份的控制。 自助服务组管理功能不可用于已启用邮件的安全组或通讯组列表。

## <a name="self-service-group-membership-defaults"></a>自助服务组的成员身份的默认值

当在 Azure 门户中创建安全组或使用 Azure AD PowerShell，只有该组的所有者可以更新成员身份。 在中创建的安全组[访问面板](https://account.activedirectory.windowsazure.com/r#/joinGroups)所有 Office 365 组，且支持所有者批准或自动批准加入的所有用户。 在访问面板中，您可以更改成员身份选项，在创建组时。

中创建的组 | 安全组默认行为 | Office 365 组默认行为
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | 只有所有者可以添加成员<br>但不可用，将在访问面板中可见 | 打开要加入的所有用户
[Azure 门户](https://portal.azure.com) | 只有所有者可以添加成员<br>但不可用，将在访问面板中可见<br>在创建组不会自动分配所有者 | 打开要加入的所有用户
[访问面板](https://account.activedirectory.windowsazure.com/r#/joinGroups) | 打开要加入的所有用户<br>创建组时，可以更改成员资格选项 | 打开要加入的所有用户<br>创建组时，可以更改成员资格选项

## <a name="self-service-group-management-scenarios"></a>自助服务组管理方案

* **委托组管理** — 以管理对公司所用 SaaS 应用程序的访问权限的管理员为例。 由于有许多加入者和离开者，管理这些访问权限变得越来越繁琐，因此该管理员要求业务所有者创建一个新组。 管理员将该应用程序的访问权限分配给新组，并添加到所有人员已访问该应用程序的组。 然后，业务所有者可以添加更多用户，而这些用户会自动预配到该应用程序中。 业务所有者无需等待管理员管理用户的访问权限。 如果管理员将相同的权限授予不同业务组中的管理器，此人可也其自己的组成员的管理访问。 既不为业务所有者和管理员可以查看或管理彼此的组成员身份。 该管理员仍然可以看到有权访问该应用程序的所有用户，并可根据需要阻止访问权限。
* **自助组管理** — 以下是该方案的一个示例：两个用户都拥有独立设置的 SharePoint Online 站点。 他们都想为对方的团队提供对其站点的访问权限。 要实现此目的，他们可以在 Azure AD 中创建一个组，各自在 SharePoint Online 中选择该组并为该组提供对其站点的访问权限。 当有人想要访问时，他们从访问面板发出请求，获得批准后便可自动访问这两个 SharePoint Online 站点。 后来，他们中的一人决定，允许访问其站点的所有人也访问特定的 SaaS 应用程序。 SaaS 应用程序的管理员可以将此应用程序的访问权限添加到 SharePoint Online 站点。 从那以后，他批准的任何请求都将提供对这两个 SharePoint Online 站点以及该 SaaS 应用程序的访问权限。

## <a name="make-a-group-available-for-user-self-service"></a>使组可用于用户自助服务

1. 使用目录的全局管理员帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 选择“用户和组”  ，然后选择“组设置”  。
3. 将“启用自助服务组管理”  设置为“是”  。
4. 将“用户可以创建安全组”  或“用户可以创建 Office 365 组”  设置为“是”  。
   * 启用这些设置时，将允许目录中的所有用户创建新的安全组并在这些组中添加成员。 这些新组也会显示在其他所有用户的“访问面板”中。 如果组的策略设置允许，其他用户可以创建加入这些组的请求。 
   * 禁用这些设置时，用户将无法创建组，也无法更改其拥有的现有组。 不过，他们仍然可以管理这些组的成员身份，并审批其他用户加入其组的请求。

还可以使用“可以管理安全组的用户”和“可以管理 Office 365 组的用户”对用户的自助服务组管理功能进行更精细的访问控制   。 启用“用户可以创建组”后，将允许租户中的所有用户创建新组并在这些组中添加成员  。 不能指定可以创建自己的组的人员。 可以仅为使组所有者的另一个组成员指定的个人。

通过设置**可以对安全组使用自助服务用户**并**可以管理 Office 365 组的用户**到**是**，允许你创建新的租户中的所有用户组。

还可以使用“可以管理安全组的组”  或“可以管理 Office 365 组的组”  来指定其成员可以使用自助服务的单个组。

## <a name="next-steps"></a>后续步骤

这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问权限](../fundamentals/active-directory-manage-groups.md)
* [用于配置组设置的 Azure Active Directory cmdlet](groups-settings-cmdlets.md)
* [Azure Active Directory 中的应用程序管理](../manage-apps/what-is-application-management.md)
* [什么是 Azure Active Directory？](../fundamentals/active-directory-whatis.md)
* [将本地标识与 Azure Active Directory 集成](../hybrid/whatis-hybrid-identity.md)
