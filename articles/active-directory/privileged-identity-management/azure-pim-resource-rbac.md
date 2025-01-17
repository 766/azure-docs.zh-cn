---
title: 查看活动和审核历史记录的 PIM-Azure Active Directory 中的 Azure 资源角色 |Microsoft Docs
description: 在 Azure AD Privileged Identity Management (PIM) 中查看 Azure 资源角色的活动和审核历史记录。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84bd491d992ed15df288d9226b58bfe832e0692a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476497"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>在 PIM 中查看 Azure 资源角色的活动和审核历史记录

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，可以查看组织中的 Azure 资源角色的活动、激活和审核历史记录。 这些资源包括订阅、资源组甚至虚拟机。 Azure 门户中利用 Azure 基于角色的访问控制 (RBAC) 功能的任何资源都可以利用 PIM 中的安全和生命周期管理功能。

## <a name="view-activity-and-activations"></a>查看活动和激活

若要查看特定用户在各种资源中执行的操作，可以查看与给定激活时段关联的 Azure 资源活动。

1. 打开“Azure AD Privileged Identity Management”。 

1. 单击“Azure 资源”  。

1. 单击你要查看其活动和激活的资源。

1. 单击“角色”  或“成员”  。

1. 单击某个用户。

    这将按日期显示用户在 Azure 资源中的操作的图形视图。 其中还显示了同一时间段内的最近角色激活。

    ![资源活动摘要和角色激活的详细信息用户](media/azure-pim-resource-rbac/rbac-user-details.png)

1. 单击某个特定的角色可查看详细信息，以及在该用户处于活动状态期间发生的相应 Azure 资源活动。

    ![所选的角色激活和按日期显示的活动详细信息](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>导出具有子级的角色分配

你可能具有合规性要求，必须向审核者提供角色分配的完整列表。 可以使用 PIM 查询特定资源上的角色分配，这包括针对所有子资源的角色分配。 以前，管理员很难获取某个订阅的角色分配完整列表，他们必须导出每个特定资源的角色分配。 使用 PIM，可以查询某个订阅中所有处于活动状态和符合条件的角色分配，包括针对所有资源组和资源的角色分配。

1. 打开“Azure AD Privileged Identity Management”。 

1. 单击“Azure 资源”  。

1. 单击要为其导出角色分配的资源，例如订阅。

1. 单击“成员”  。

1. 单击“导出”  以打开“导出成员身份”窗格。

    ![导出的成员身份窗格，以导出所有成员](media/azure-pim-resource-rbac/export-membership.png)

1. 单击“导出所有成员”  以在 CSV 文件中导出所有角色分配。

    ![导出为在 Excel 中显示的 CSV 文件中的角色分配](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>查看资源审核历史记录

资源审核提供资源的所有角色活动的视图。

1. 打开“Azure AD Privileged Identity Management”。 

1. 单击“Azure 资源”  。

1. 单击要查看其审核历史记录的资源。

1. 单击“资源审核”  。

1. 可以使用预定义的日期或自定义范围筛选历史记录。

    ![资源审核筛选器列表](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. 对于“审核类型”  ，选择“激活(已分配 + 已激活)”  。

    ![资源审核列表按激活审核类型筛选](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. 在“操作”  下，单击某个用户的 **（活动）** 可查看该用户在 Azure 资源中的活动详细信息。

    ![特定的操作的的用户活动详细信息](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>查看我的审核

使用“我的审核”，可以查看你的个人角色活动。

1. 打开“Azure AD Privileged Identity Management”。 

1. 单击“Azure 资源”  。

1. 单击要查看其审核历史记录的资源。

1. 单击“我的审核”  。

1. 可以使用预定义的日期或自定义范围筛选历史记录。

    ![审核为当前用户的列表](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>后续步骤

- [在 PIM 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)
- [在 PIM 中批准或拒绝 Azure 资源角色的请求](pim-resource-roles-approval-workflow.md)
- [在 PIM 中的 Azure AD 角色查看审核历史记录](pim-how-to-use-audit-log.md)
