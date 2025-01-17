---
title: 了解 Azure 虚拟机预留实例折扣 | Microsoft Docs
description: 了解如何将 Azure 虚拟机预留实例折扣应用于正在运行的虚拟机。
author: yashesvi
manager: yashar
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2019
ms.author: banders
ms.openlocfilehash: 191160035f516d818d5537c5c47f9604998c46f7
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849989"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>如何将 Azure 预订折扣应用到虚拟机

购买 Azure 虚拟机预留实例后，预留折扣将自动应用于与预留的属性和数量匹配的虚拟机。 预留涵盖虚拟机的计算成本。

预订折扣适用于从 Azure Marketplace 购买的基本 Vm。

有关 SQL 数据库保留容量，请参阅[了解 Azure 预留实例折扣](billing-understand-reservation-charges.md)。

下表说明了购买虚拟机预留实例后的虚拟机成本。 在任何情况下，均按标准费率收取存储和网络费用。

| 虚拟机类型  | 通过虚拟机预留实例收费 |
|-----------------------|--------------------------------------------|
|未安装其他软件的 Linux VM | 预留涵盖 VM 基础结构成本。|
|具有软件费用的 Linux VM（例如，Red Hat） | 预留涵盖基础结构成本。 将收取其他软件费用。|
|未安装其他软件的 Windows VM |预留涵盖基础结构成本。 将收取 Windows 软件费用。|
|安装了其他软件（例如，SQL server）的 Windows VM | 预留涵盖基础结构成本。 将收取 Windows 软件和其他软件费用。|
|具有 [Azure 混合权益](../virtual-machines/windows/hybrid-use-benefit-licensing.md)的 Windows VM | 预留涵盖基础结构成本。 Azure 混合权益涵盖 Windows 软件成本。 其他任何软件都是单独收费的。|

## <a name="how-reservation-discount-is-applied"></a>如何应用预订折扣

预订折扣是 "*it 使用-或丢失*"。 因此, 如果你没有任何小时的匹配资源, 则会丢失该小时的预订量。 不能结转未使用的保留小时数。

关闭资源时, 预订折扣将自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到任何匹配资源, 则保留的时间将*丢失*。

## <a name="reservation-discount-for-non-windows-vms"></a>非 Windows Vm 的预订折扣

 Azure 预留折扣按小时应用于正在运行的 VM 实例。 已购买的预订将与正在运行的 VM 所发送的使用情况信息进行匹配以应用预订折扣。 对于可能无法运行整个小时的 VM，将从其他未使用预订的 VM（包括同时运行的 VM）填充预订。 在一个小时结束时，将锁定该小时内的 VM 预留应用程序。 如果 VM 未运行一小时或该小时内同时运行的 VM 未填充该小时的预留，则该小时的预留未充分利用。 下图说明了如何将预订应用于应计费的 VM 使用量。 该说明基于一次预订购买和两个匹配的 VM 实例。

![一个已应用预留和两个匹配的 VM 实例的屏幕截图](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. 预订行以上的任何使用量按常规的现用现付费率进行收费。 对于预留范围内的任何使用量不收取任何费用，因为这些使用量已在购买预留时付费。
2. 在第 1 个小时内，实例 1 运行了 0.75 小时，实例 2 运行了 0.5 小时。 第 1 个小时的总体使用情况为 1.25 小时。 将按即用即付费率收取剩余 0.25 小时的费用。
3. 在第 2 个小时和第 3 个小时内，这两个实例都各运行了 1 小时。 一个实例的费用由预订费用涵盖，按即用即付费率对另一个实例收费。
4. 在第 4 个小时内，实例 1 运行了 0.5 小时，实例 2 运行了 1 小时。 预订费用完全涵盖了实例 1 的费用，并涵盖了实例 2 的 0.5 小时费用。 将按即用即付费率收取剩余 0.5 小时的费用。

要了解 Azure 预留应用情况并在计费使用情况报告中查看该信息，请参阅[了解预留使用情况](billing-understand-reserved-instance-usage-ea.md)。

## <a name="reservation-discount-for-windows-vms"></a>Windows Vm 的预订折扣

正在运行 Windows VM 实例时，将应用预留以涵盖基础结构成本。 对 Windows VM 的 VM 基础结构成本应用预订与对非 Windows VM 的 VM 基础结构成本应用预订相同。 将按 vCPU 对 Windows 软件单独收费。 请参阅 [Windows 软件的预留成本](billing-reserved-Instance-windows-software-costs.md)。 可以利用 [Windows Server 的 Azure 混合权益](../virtual-machines/windows/hybrid-use-benefit-licensing.md)来涵盖 Windows 许可费用。

## <a name="discount-can-apply-to-different-sizes"></a>折扣适用于不同大小

在购买预留 VM 实例时，如果选择“优化对象：实例大小灵活性”，则折扣覆盖范围将取决于所选的 VM 大小   。 预订可以应用于同一大小系列组中的虚拟机 (VM) 大小。 有关详细信息，请参阅[预订 VM 实例的虚拟机大小灵活性](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。

## <a name="discount-applies-to-matching-servicetype-only"></a>折扣仅适用于匹配的 ServiceType

预订折扣仅适用于 VM 使用率, 其中的`ServiceType` `AdditionalInfo`值与购买的预订匹配。 预订折扣应用程序忽略用于 Vm 的计量, 只计算`ServiceType`。 知道您为 VM 购买的服务类型。 你可以为高级存储预留交换非高级存储 VM 保留, 也可以采用相反的方式。

## <a name="services-that-get-vm-reservation-discounts"></a>获取 VM 保留折扣的服务

VM 预留适用于从多个服务发出的 VM 使用量, 而不仅仅适用于你的 VM 部署。 获取预订折扣的资源根据实例大小的灵活性设置而变化。

### <a name="instance-size-flexibility-setting"></a>实例大小灵活性设置

实例大小灵活性设置确定哪些服务获取预订实例折扣。

无论该设置是打开还是关闭, 预订折扣在*ConsumedService*为`Microsoft.Compute`时自动应用到任何匹配的 VM。 因此, 请检查使用情况数据中的*ConsumedService*值。 示例包括：

- 虚拟机
- 虚拟机规模集
- 容器服务
- Azure Batch 部署 (在 "用户订阅" 模式下)
- Azure Kubernetes 服务 (AKS)
- Service Fabric

当设置为 on 时, 预订折扣将自动应用于匹配的 VM 使用情况 (当*ConsumedService*为以下任意项时):

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

检查使用情况数据中的*ConsumedService*值, 以确定其使用情况是否适用于预订折扣。

有关实例大小灵活性的详细信息, 请参阅[具有保留 VM 实例的虚拟机大小灵活性](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)。


## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如果你有疑问或需要帮助, 请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [Azure 的保留内容是什么？](billing-save-compute-costs-reservations.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [通过 Azure SQL 数据库保留容量预付 SQL 数据库计算资源费用](../sql-database/sql-database-reserved-capacity.md)
- [管理 Azure 的预订](billing-manage-reserved-vm-instance.md)
- [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
- [了解 CSP 订阅的预留使用情况](/partner-center/azure-reservations)
- [预留未包含的 Windows 软件成本](billing-reserved-instance-windows-software-costs.md)
