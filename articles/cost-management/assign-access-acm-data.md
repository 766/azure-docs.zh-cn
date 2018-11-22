---
title: 分配对 Azure 成本管理数据的访问权限 | Microsoft Docs
description: 本文介绍如何分配不同访问范围的 Azure 成本管理数据访问权限。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: e32e281509da32d4816c9e137a462553891c82f1
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686137"
---
# <a name="assign-access-to-cost-management-data"></a>分配对成本管理数据的访问权限

大多数用户都在 Azure 门户和企业 (EA) 门户中被授予了一些权限的组合，这些权限定义了用户对 Azure 成本管理数据的访问级别。 本文介绍如何分配对成本管理数据的访问权限。 用户分配到权限组合后，会基于他们有权访问的范围和他们在 Azure 门户中所选的范围查看成本管理中的数据。

用户所选的范围将用于整个成本管理，以提供数据整合并控制对成本信息的访问。 使用范围时，用户不要多选它们。 而应选择一个子范围要汇总到的较大范围，然后筛选到要查看的范围。 理解数据整合很重要，因为有些人不应该访问子范围汇总到的父范围。

## <a name="cost-management-scopes"></a>成本管理范围

若要查看成本数据，用户必须至少具有以下一个或多个范围的读取权限。

| **范围** | **定义位置** | **查看数据所需的访问权限** | **先决条件 EA 设置** | **将数据合并到** |
| --- | --- | --- | --- | --- |
| 计费帐户<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | 企业管理员 | 无 | 企业协议中的所有订阅 |
| 部门 | [https://ea.azure.com](https://ea.azure.com/) | 部门管理员 | **DA 视图费用**已启用 | 属于一个合约帐户的所有订阅，该帐户已关联到部门 |
| 合约帐户<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | 帐户所有者 | **AO 视图费用**已启用 | 注册帐户的所有订阅 |
| 管理组 | [https://portal.azure.com](https://portal.azure.com/) | 成本管理读者（或读者） | **AO 视图费用**已启用 | 管理组下的所有订阅 |
| 订阅 | [https://portal.azure.com](https://portal.azure.com/) | 成本管理读者（或读者） | **AO 视图费用**已启用 | 订阅中的所有资源/资源组 |
| 资源组 | [https://portal.azure.com](https://portal.azure.com/) | 成本管理读者（或读者） | **AO 视图费用**已启用 | 资源组中的所有资源 |

<sup>1</sup> 计费帐户还称为“企业协议”或“合约”。

<sup>2</sup> 合约帐户还称为“帐户所有者”。

## <a name="enable-access-to-costs-in-the-ea-portal"></a>启动对 EA 门户中的成本的访问

计费帐户范围需要在 EA 门户中启用“DA 视图费用”选项。 所有其他范围需要在 EA 门户中启用“AO 视图费用”选项。

若要启用此选项，请执行以下操作：

1. 使用企业管理员帐户在 [https://ea.azure.com](https://ea.azure.com) 登录到 EA 门户。
2. 在左窗格中选择“管理”。
3. 为想提供访问权限的成本管理范围启用“DA 视图费用”和“AO 视图费用”的费用选项。  
    ![显示 DA 和 AO 视图费用选项的“合约”选项卡](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

启用视图费用选项后，大多数范围还需要 Azure 门户中的基于角色的访问控制 (RBAC) 权限配置。

## <a name="enterprise-administrator-role"></a>企业管理员角色

默认情况下，企业管理员有权访问计费帐户（企业协议/合约）和所有其他范围（子范围）。 企业管理员为其他用户分配对这些范围的访问权限。 考虑到业务连续性，最好始终保持有两位用户具有企业管理员访问权限。 以下部分提供示例来演示企业管理员如何为其他用户分配对这些范围的访问权限。

## <a name="assign-billing-account-scope-access"></a>分配计费帐户范围的访问权限

要访问计费帐户范围，需要在 EA 门户中具备企业管理员权限。 企业管理员有权查看整个 EA 合约或多个合约的成本。 对于计费帐户范围，在 Azure 门户中不需要任何操作。

1. 使用企业管理员帐户在 [https://ea.azure.com](https://ea.azure.com) 登录到 EA 门户。
2. 在左窗格中选择“管理”。
3. 在“合约”选项卡中选择要管理的合约。  
    ![EA 门户](./media/assign-access-acm-data/ea-portal.png)
4. 单击“+ 添加管理员”。
5. 在“添加管理员”框中选择身份验证类型并键入用户的电子邮件地址。
6. 如果要向用户授予对成本和使用情况数据的只读访问权限，请在“只读”下选择“是”。  否则请选择“否”。
7. 单击“添加”以创建帐户。  
    ![“添加管理员”框](./media/assign-access-acm-data/add-admin.png)

可能在 30 分钟以后，该新用户才能访问成本管理中的数据。

### <a name="assign-department-scope-access"></a>分配部门范围访问权限

若要访问部门范围，需要在 EA 门户中具备部门管理员（DA 视图费用）访问权限。 部门管理员有权查看与一个或多个部门关联的成本和使用情况数据。  部门数据包含属于关联至该部门的合约帐户的所有订阅。 在 Azure 门户中不需要任何操作。

1. 使用企业管理员帐户在 [https://ea.azure.com](https://ea.azure.com) 登录到 EA 门户。
2. 在左窗格中选择“管理”。
3. 在“合约”选项卡中选择要管理的合约。
4. 单击“部门”选项卡，然后单击“添加管理员”。
5. 在“添加部门管理员”框中选择身份验证类型并键入用户的电子邮件地址。
6. 如果要向用户授予对成本和使用情况数据的只读访问权限，请在“只读”下选择“是”。  否则请选择“否”。
7. 选择要授予其部门管理权限的部门。
8. 单击“添加”以创建帐户。  
    ![“添加部门管理员”框](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>分配合约帐户范围的访问权限

若要访问合约范围，需要在 EA 门户中具备帐户所有者（AO 视图费用）访问权限。 帐户所有者可以查看与合约帐户关联的成本和使用情况数据。 合约帐户中的数据包括与该合约关联的所有 Azure 订阅。 在 Azure 门户中不需要任何操作。

1. 使用企业管理员帐户在 [https://ea.azure.com](https://ea.azure.com) 登录到 EA 门户。
2. 在左窗格中选择“管理”。
3. 在“合约”选项卡中选择要管理的合约。
4. 单击“帐户”选项卡，然后单击“添加帐户”。
5. 在“添加帐户”框中，选择帐户要关联到的“部门”，或将其保留为未分配状态。
6. 选择身份验证类型并键入帐户名。
7. 键入用户的电子邮件地址，然后根据需要键入成本中心。
8. 单击“添加”以创建帐户。  
    ![“添加帐户”框](./media/assign-access-acm-data/add-account.png)

## <a name="assign-management-group-scope-access"></a>分配管理组范围的访问权限

若要访问管理组范围，至少需要成本管理读取者（或读取者）权限。 在 Azure 门户中配置对管理组的访问权限。 要启用其他用户的访问权限，至少需要具备该管理组的参与者权限。 此外，还必须在 EA 门户中启用“AO 视图费用”设置。

1. 在 [http://portal.azure.com](http://portal.azure.com) 中登录 Azure 门户。
2. 在侧栏中选择“所有服务”，搜索“管理组”，然后选择“管理组”。
3. 在层次结构中选择管理组。
4. 单击管理组名称旁边的“详细信息”。
5. 从左窗格中选择“访问控制(标识和访问管理)”。
6. 单击“添加”。
7. 在“角色”下选择“成本管理读取者”。
8. 在“分配访问权限至”下选择“Azure AD 用户、组或应用程序”。
9. 若要分配访问权限，请搜索用户并选择用户。
10. 单击“ **保存**”。  
    ![“添加权限”框](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>分配订阅范围的访问权限

若要访问订阅，至少需要成本管理读取者（或读取者）权限。 在 Azure 门户中配置对订阅的访问权限。 要启用其他用户的访问权限，至少需要该订阅的参与者权限。 此外，还必须在 EA 门户中启用“AO 视图费用”设置。

1. 在 [http://portal.azure.com](http://portal.azure.com) 中登录 Azure 门户。
2. 在侧栏中选择“所有服务”，搜索“订阅”，然后选择“订阅”。
3. 选择订阅。
4. 从左窗格中选择“访问控制(标识和访问管理)”。
5. 单击“添加”。
6. 在“角色”下选择“成本管理读取者”。
7. 在“分配访问权限至”下选择“Azure AD 用户、组或应用程序”。
8. 若要分配访问权限，请搜索用户并选择用户。
9. 单击“ **保存**”。

## <a name="assign-resource-group-scope-access"></a>分配资源组范围的访问权限

若要访问资源组，至少需要成本管理读取者（或读取者）权限。 在 Azure 门户中配置对资源组的访问权限。 要启用其他用户的访问权限，至少需要具备该资源组的参与者权限。 此外，还必须在 EA 门户中启用“AO 视图费用”设置。

1. 在 [http://portal.azure.com](http://portal.azure.com) 中登录 Azure 门户。
2. 在侧栏中选择“所有服务”，搜索“资源组”，然后选择“资源组”。
3. 选择你的资源组。
4. 从左窗格中选择“访问控制(标识和访问管理)”。
5. 单击“添加”。
6. 在“角色”下选择“成本管理读取者”。
7. 在“分配访问权限至”下选择“Azure AD 用户、组或应用程序”。
8. 若要分配访问权限，请搜索用户并选择用户。
9. 单击“ **保存**”。

## <a name="next-steps"></a>后续步骤

- 如果尚未完成有关成本管理的第一个快速入门，请阅读[开始分析成本](quick-acm-cost-analysis.md)。