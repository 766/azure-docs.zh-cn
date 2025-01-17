---
title: 跟踪的 Microsoft 客户协议的 Azure 信用额度余额
description: 了解如何检查 Microsoft 客户协议的 Azure 信用额度余额。
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 352737b3ea61a51a39e066d4211c8f4ceae74184
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490969"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>跟踪 Microsoft 客户协议的 Azure 信用额度余额

您可以在 Azure 门户来检查 Microsoft 客户协议的 Azure 信用额度余额。 使用信用额度支付涵盖的信用额度的费用。

使用未涵盖的信用额度的产品或使用量超出你的信用余额时，则要收费。 有关详细信息，请参阅 [未涵盖的 Azure 信用额度的产品。 (#products-that-aren't-covered-by-azure-credits)。

本文适用于 Microsoft 客户协议向计费帐户。 [检查是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

## <a name="check-your-credit-balance"></a>检查你的信用余额

1. 登录到 [Azure 门户]( https://portal.azure.com)。

2. 搜索**成本管理 + 计费**。

    ![有关成本管理 + 计费门户中显示搜索的屏幕截图](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  选择**Azure 额度**从左上方。 根据您的访问权限，可能需要选择计费帐户或帐单的配置文件，然后选择**Azure 额度**。

4. Azure 额度页显示以下信息：

   ![信用额度余额和计费配置文件的事务的屏幕截图](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | 术语               | 定义                           |
   |--------------------|--------------------------------------------------------|
   | 估计的余额  | 信用额度的估计的量必须考虑所有计费后和挂起的事务 |
   | 当前余额    | 自上张发票起的信用额度量。 它不包含任何挂起的事务 |
   | 事务       | 所有受影响你的 Azure 信用额度余额的计费事务 |

   当您估计的余额降为 0 时，会收取你所有使用情况，包括涵盖的信用额度的产品。

6. 选择**信用额度列表**的信用额度账单资料的查看列表。 信用额度列表提供了以下信息：

   ![信用额度的屏幕截图列出了为计费配置文件](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | 术语 | 定义 |
   |---|---|
   | 估计的余额 | 未出单信用额度符合条件的费用从当前余额中减去之后具有的 Azure 信用额度量|
   | 当前余额 | 在考虑未出单信用额度符合条件的费用之前具有 Azure 信用额度量。 通过在最后一张发票时将收到新的 Azure 额度添加到的信用额度余额计算|
   | source | 获取源的信用额度 |
   | 开始日期 | 获取信用额度时的日期 |
   | 到期日期 | 信用额度的到期日期 |
   | Balance | 自上张发票起余额 |
   | 原始金额 | 原始信用额度量 |
   | 状态 | 信用额度的当前状态。 状态可以是处于活动状态，使用、 已过期，或即将到期 |

## <a name="how-credits-are-used"></a>如何使用信用额度

中的 Microsoft 客户协议的计费帐户，你可以使用计费的配置文件来管理你的发票和付款方法。 为每个计费的配置文件生成一个每月的发票和付款方法用于向发票付款。

Azure 额度是一种付款方式。 促销信用额度和服务级别的信用额度等，可以从 Microsoft 获取信用额度。 这些信用额度分配给计费的配置文件。 计费的配置文件生成发票后，信用额度会自动应用到计费总金额来计算需要支付的金额。 你使用另一个付款方法，如检查支付剩余的金额或电汇方式。

## <a name="products-that-arent-covered-by-azure-credits"></a>未涵盖的 Azure 信用额度的产品

 你的 Azure 额度不涵盖以下产品。 可使用这些产品而不考虑你的信用余额支付：

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- 已注册的用户
- Openlogic
- 远程访问权限 XenApp Essentials 已注册的用户
- Ubuntu Advantage
- Visual Studio Enterprise（每月）
- Visual Studio Enterprise（每年）
- Visual Studio Professional（每月）
- Visual Studio Professional（每年）
- Azure Marketplace 产品
- Azure 支持计划

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查与 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- [了解 Microsoft 客户协议的计费帐户](billing-mca-overview.md)
- [了解有关 Microsoft 客户协议发票的术语](billing-mca-understand-your-invoice.md)
