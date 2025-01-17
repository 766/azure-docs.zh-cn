---
title: 监视和跟踪免费的 Azure 服务使用情况
description: 了解如何检查 Azure 门户和使用情况 CSV 文件中的免费服务使用情况。
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3543bed7f699fd149ca7f2a6f61e9eb5aad5f1a3
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491427"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>检查 Azure 免费帐户包含的免费服务使用情况

不收费包含免费的 Azure 免费帐户，服务的除非超出这些服务的限制。 若要保持在限制，可以使用 Azure 门户或使用文件来监视和跟踪免费服务使用情况。

## <a name="check-usage-in-the-azure-portal"></a>检查 Azure 门户中的使用情况

1.  登录到 [Azure 门户](https://portal.azure.com)。

2.  在左侧的导航区域中，选择**所有服务**。

3.  选择 **订阅**。

4.  选择注册免费帐户时创建的订阅。

    ![显示所有订阅的屏幕截图](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  概述部分显示有关你的订阅的基本信息。 例如，订阅 ID、 产品/服务类型和订阅名称。 免费帐户信用额度到期时，还可以查找信息。

    ![显示订阅基本信息的屏幕截图](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  向下滚动到有关当前和预计成本查找信息。 成本包括未包含的免费帐户和使用量超出限制的免费服务，则服务使用情况。

    ![显示订阅成本信息的屏幕截图](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  概述部分的最后部分有一个表显示免费服务使用情况。

    ![显示免费服务使用情况的屏幕截图](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    该表具有以下列：

* **测定仪名称：** 确定要使用的测定仪的度量单位。 若要了解有关服务测定仪映射的信息，请参阅[了解免费服务测定仪映射](billing-understand-free-service-meter-mapping.md)。
* **使用情况/限值：** 当前月份的测定仪使用情况和限值。 还可在状态栏中找到此信息。
* **状态：** 测定仪的使用状态。 根据使用模式，可以具有以下法令之一：
  * **未使用：** 未使用测定仪或测定仪的使用情况尚未提交至计费系统。
  * **于 \<Date> 超过：** 已于 \<Date> 超过测定仪限值。
  * **不可能超过：** 不太可能超过测定仪的限值。
  * **于 \<Date> 超过：** 可能会于 \<Date> 超过测定仪限值。

## <a name="check-usage-with-the-usage-file"></a>与使用情况文件检查使用情况

使用文件提供你的 Azure 订阅的详细的信息。 可以从 Azure 帐户中心下载每月和每日使用情况文件。 若要了解如何下载使用文件并了解所需的访问权限，请参阅[获取发票和使用情况](billing-download-azure-invoice-daily-usage-date.md)。 若要了解有关使用文件中列的信息，请参阅[了解有关使用情况的术语](billing-understand-your-usage.md)。

使用文件具有免费服务和付费服务的使用情况信息。 免费服务测定仪在测定仪名称末尾附加 **Free**。 若要查找免费测定仪、 打开的文件中的 excel 和筛选器**测定仪类别列**具有文本的单元格 **-免费**(使用文本筛选器&rarr;Contains 筛选器)。


![显示免费服务使用情况的屏幕截图](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- [升级你的订阅](billing-upgrade-azure-subscription.md)
