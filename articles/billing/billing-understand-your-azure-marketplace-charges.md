---
title: 了解 Azure 外部服务收费 | Microsoft Docs
description: 了解 Azure 中外部服务（以前称为市场）的计费。
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 278e873d01eb3dd7d614d771e5b50b8fe624800a
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490353"
---
# <a name="understand-your-azure-external-services-charges"></a>了解 Azure 外部服务费用
外部服务由 Azure 应用商店中的第三方软件供应商发布。 例如，SendGrid 是可以购买在 Azure 中，但不是由 Microsoft 发布的外部服务。 某些 Microsoft 产品是通过 Azure marketplace 销售过。

## <a name="how-external-services-are-billed"></a>外部服务的计费方式

- 如果有[Microsoft 客户协议](#check-access)，第三方服务与你的 Azure 服务的其余部分进行计费。
- 如果你没有 Microsoft 客户协议，外部服务从你的 Azure 服务单独计费。
- 每个外部服务都有不同的计费模型。 某些服务收费以即用即付的方式，而其他人具有固定的每月费用。
- 购买外部服务时不能使用每月的免费信用额度。 如果使用包含的 Azure 订阅[免费信用额度](https://azure.microsoft.com/pricing/spending-limits/)，它们不能应用于费用从外部服务。 预配新的外部服务或资源时，会显示警告：

    ![市场购买警告](./media/billing-understand-your-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/billing-understand-your-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-invoices"></a>查看和下载发票

如果有[Microsoft 客户协议](#check-access)，同一个作为 Azure 费用发票将第三方计费。 了解如何[查看和下载 Azure 发票](billing-download-azure-invoice.md)从 Azure 门户以查看你的第三方费用。

如果你没有 Microsoft 客户协议，则必须单独为第三方费用发票。 可以查看和下载 Azure Marketplace 发票从 Azure 门户通过执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索**成本管理 + 计费**。
1. 在左侧菜单中，选择**发票**。
1. 单击**Azure Marketplace 和预订**选项卡。![Azure marketplace 和保留选项卡的图片](./media/billing-understand-your-azure-marketplace-charges/invoice-tabs.png)
1. 在订阅下拉列表中选择包含你想要查看有关发票的外部服务的订阅。

## <a name="external-spending-for-ea-customers"></a>对于 EA 客户支出的外部

EA 客户可以在 EA 门户中查看外部服务支出和下载报表。 若要开始使用，请参阅[适用于 EA 客户的 Azure 市场](https://ea.azure.com/helpdocs/azureMarketplace)。

## <a name="manage-payment-for-external-services"></a>管理对外部服务的付款

时购买外部服务，可以选择 Azure 订阅的资源。 所选 Azure 订阅的付款方式将成为外部服务的付款方法。 若要更改付款方式的外部服务，您必须[更改 Azure 订阅的付款方式](billing-how-to-change-credit-card.md)绑定到该外部服务。 您可以找出哪种订阅外部服务订单与通过执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 单击**的所有资源**左侧的导航菜单中。
     ![所有资源菜单项的屏幕截图](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. 搜索外部服务。
1. 查找名称中的订阅**订阅**列。
    ![资源的订阅名称的屏幕截图](./media/billing-understand-your-azure-marketplace-charges/sub-selected.png)
1. 单击订阅名称和[更新可用的付款方式](billing-how-to-change-credit-card.md)。

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/billing-understand-your-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>取消外部服务订单
若要取消外部服务订单，请在 [Azure 门户](https://portal.azure.com)中删除资源。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 单击**的所有资源**左侧的导航菜单中。
    ![所有资源菜单项的屏幕截图](./media/billing-understand-your-azure-marketplace-charges/all-resources.png)
1. 搜索外部服务。
1. 检查你想要删除的资源旁边的框。
1. 选择**删除**命令栏中。
    ![删除按钮的屏幕截图](./media/billing-understand-your-azure-marketplace-charges/delete-button.png)
1. 类型*是*确认边栏选项卡中。
    ![删除资源](./media/billing-understand-your-azure-marketplace-charges/delete-resource.PNG)
1. 单击“删除”  。

## <a name="check-access"></a>检查访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- [开始分析成本](../cost-management/quick-acm-cost-analysis.md)
