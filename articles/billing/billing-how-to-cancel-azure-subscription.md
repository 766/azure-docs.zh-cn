---
title: 取消 Azure 订阅 | Microsoft Docs
description: 介绍如何取消 Azure 订阅，例如免费试用版订阅
author: bandersmsft
manager: amberb
tags: billing
ms.assetid: 3051d6b0-179f-4e3a-bda4-3fee7135eac5
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: banders
ms.openlocfilehash: 7756174f01e3fede17bec3e2ac185e89caddc097
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666434"
---
# <a name="cancel-your-azure-subscription"></a>取消 Azure 订阅

如果不再需要该订阅, 可以在 Azure 门户中取消 Azure 订阅。 

在取消订阅之前，请执行以下操作：
* 备份数据。 例如，若要在 Azure 存储或 SQL 中存储数据，请下载一个副本。 如果有虚拟机，请在本地保存该虚拟机的映像。
* 关闭服务。 转到[管理门户中的资源页](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources)，**停止**任何正在运行的虚拟机、应用程序或其他服务。
* 考虑迁移数据。 请参阅[将资源移到新资源组或订阅中](../azure-resource-manager/resource-group-move-resources.md)。
* 删除所有资源和所有资源组。 必须先删除它们, 然后才能取消订阅。 每个资源组必须单独删除。 删除资源组时, 必须通过键入资源组名称来确认删除。
* 如果在中`AssignableScopes`有任何引用此订阅的自定义角色, 则应更新这些自定义角色以删除订阅。 如果在取消订阅后尝试更新自定义角色, 可能会收到错误。 有关详细信息, 请参阅 Azure 资源的[自定义角色](../role-based-access-control/troubleshooting.md#problems-with-custom-roles)和[自定义角色的](../role-based-access-control/custom-roles.md)问题疑难解答。

如果你取消付费的 Azure 支持计划, 则订阅期限的余下部分会向你收费。 有关详细信息，请参阅 [Azure 支持计划](https://azure.microsoft.com/support/plans/)。

## <a name="cancel-subscription-in-the-azure-portal"></a>在 Azure 门户中取消订阅

1. 从 "[订阅" 页的 Azure 门户中](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)选择订阅。
2. 选择要取消的订阅。
3. 选择“概述”，然后选择“取消订阅”。
    ![显示 "取消" 按钮的屏幕截图](./media/billing-how-to-cancel-azure-subscription/cancel_ibiza.png)
3. 按照提示进行操作并完成取消。


## <a name="who-can-cancel-a-subscription"></a>谁可以取消订阅？

下表描述了取消订阅所需的权限。

|订阅类型     |谁可以取消  |
|---------|---------|
|通过 Azure 网站注册 Azure 时创建的订阅。 例如, 当你注册[Azure 免费帐户](https://azure.microsoft.com/offers/ms-azr-0044p/)时,[使用即用即付费率](https://azure.microsoft.com/offers/ms-azr-0003p/)或[Visual studio 订阅者](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)帐户。 |  订阅的帐户管理员、所有者和参与者  |
|[Microsoft 企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/)和[企业开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  订阅的帐户所有者、所有者和参与者       |
|[适用于开发测试的](https://azure.microsoft.com/offers/ms-azr-0148g/) [Azure 计划](https://azure.microsoft.com/offers/ms-azr-0017g/)和 azure 计划     |  订阅的所有者和参与者      |


## <a name="what-happens-after-i-cancel-my-subscription"></a>取消订阅之后会发生什么情况？

取消后, 会立即停止计费。 但是，可能需要花费多达 10 分钟，取消才会显示在门户中。 如果在计费期间中途取消, 我们将在该时间段结束后, 按典型发票日期发送最终发票。

取消后, 服务将被禁用。 那意味着虚拟机已解除分配，临时 IP 地址已释放，并且存储是只读的。

我们会先等待 90 天，才会永久删除用户的数据，这样做是考虑到用户可能需要访问这些数据，或者改变了主意。 我们不会因为保留这些数据而向你收费。 若要了解详细信息，请参阅 [Microsoft Trust Center - How we manage your data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409)（Microsoft 信任中心 - 我们如何管理用户的数据）。

## <a name="reactivate-subscription"></a>重新激活订阅

如果你意外地取消了 "即用即付" 订阅, 可以[在帐户中心重新激活它](billing-subscription-become-disable.md)。

如果你的订阅不是使用即用即付费率的订阅, 请在90天内联系支持部门以重新激活你的订阅。

## <a name="need-help-contact-us"></a>需要帮助? 请联系我们。

如果你有疑问或需要帮助, 请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。
