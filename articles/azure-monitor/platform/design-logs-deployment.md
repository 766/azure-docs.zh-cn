---
title: 设计 Azure Monitor 日志部署 |Microsoft Docs
description: 本文介绍了在 Azure Monitor 中准备部署工作区的客户的注意事项和建议。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: magoedte
ms.openlocfilehash: 1c2416d9fb1d45116bb6594b29863c1fe8f524a3
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883200"
---
# <a name="designing-your-azure-monitor-logs-deployment"></a>设计 Azure Monitor 日志部署

Azure Monitor 将[日志](data-platform-logs.md)数据存储在 Log Analytics 工作区中, 这是一个 Azure 资源和一个容器, 在其中收集、聚合数据并用作管理边界。 虽然你可以在 Azure 订阅中部署一个或多个工作区, 但你应了解以下几个事项, 以确保你的初始部署遵循我们的指导原则, 以便为你提供经济高效的可管理性和可缩放性满足组织需求的部署。

工作区中的数据组织到表中, 每个表都存储不同种类的数据, 并基于生成数据的资源具有自己的唯一属性集。 大多数数据源都将写入到 Log Analytics 工作区中各自的表中。

![工作区数据模型示例](./media/design-logs-deployment/logs-data-model-01.png)

Log Analytics 工作区可提供：

* 数据存储的地理位置。
* 数据隔离, 方法是根据我们推荐的设计策略之一授予不同的用户访问权限。
* 配置设置的范围, 如[定价层](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier)、[保留](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)和[数据上限](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap)。

本文详细介绍了设计和迁移注意事项、访问控制概述以及我们建议为 IT 组织提供的设计实现。

## <a name="important-considerations-for-an-access-control-strategy"></a>访问控制策略的重要注意事项

确定所需的工作区数量受以下一项或多项要求的影响:

* 贵公司是全球性公司，因数据所有权和合规性需要将日志数据存储于特定区域。
* 正在使用 Azure，并希望通过让工作区与它所管理的 Azure 资源位于同一区域，避免产生出站数据传输费用。
* 你管理多个部门或业务组, 你希望每个部门或业务组查看其自己的数据, 而不是来自其他部门的数据。 此外, 对于合并的跨部门或业务组视图, 不存在业务要求。

如今, IT 组织采用集中式、分散式或两种结构之间的混合模型。 因此, 以下工作区部署模型通常用于映射到其中一个组织结构:

* **集中式**:所有日志都存储在中央工作区中并由单个团队进行管理, Azure Monitor 提供按团队区分的访问。 在此方案中, 可以轻松管理、跨资源搜索和交叉关联日志。 工作区可能会显著增加, 具体取决于从订阅中的多个资源收集的数据量, 以及维护对不同用户的访问控制的额外管理开销。
* **分散**:每个团队都在其自己的资源组中创建了自己的工作区, 并管理了日志数据, 并按资源隔离了日志数据。 在这种情况下, 工作区可以保持安全, 而访问控制与资源访问保持一致, 但难以交叉关联日志。 需要广泛查看多个资源的用户无法以有意义的方式分析数据。
* **混合**:安全审核遵从性要求会进一步使此方案复杂化, 因为许多组织都同时实施这两种部署模型。 这通常会导致复杂、昂贵且难以维护的配置, 并在日志覆盖范围内出现缺口。

当使用 Log Analytics 代理收集数据时, 需要了解以下各项, 以便规划代理部署:

* 若要从 Windows 代理收集数据, 你可以[将每个代理配置为向一个或多个工作区报告](../../azure-monitor/platform/agent-windows.md), 即使它向 System Center Operations Manager 管理组报告。 Windows 代理最多可报告四个工作区。
* Linux 代理不支持多宿主, 只能向单个工作区进行报告。

如果使用 System Center Operations Manager 2012 R2 或更高版本:

* 每个 Operations Manager 管理组只能[连接到一个工作区](../platform/om-agents.md)。 
* 向管理组报告的 Linux 计算机必须配置为直接报告到 Log Analytics 工作区。 如果 Linux 计算机已直接向工作区报告并想要使用 Operations Manager 进行监视, 请按照以下步骤向[Operations Manager 管理组报告](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)。 
* 你可以在 Windows 计算机上安装 Log Analytics Windows 代理, 并将其报告为与工作区集成的 Operations Manager 和其他工作区。

## <a name="access-control-overview"></a>访问控制概述

使用基于角色的访问控制 (RBAC), 可以仅向用户和组授予在工作区中使用监视数据所需的访问量。 这样, 你就可以使用单个工作区来存储所有资源上启用的已收集数据, 从而使你的 IT 组织操作模型保持一致。 例如, 你向你的团队授予对 Azure 虚拟机 (Vm) 上托管的基础结构服务的访问权限, 因此, 他们将只能访问 Vm 生成的日志。 这就是我们新的资源上下文日志模型的关注。 此模型的基础是由 Azure 资源发出的每个日志记录自动与此资源关联。 日志被转发到基于资源的作用域和 RBAC 的中央工作区。

用户有权访问的数据由下表中列出的一系列因素决定。 后续部分会描述每种因素。

| 因素 | 描述 |
|:---|:---|
| [访问模式](#access-mode) | 用户用于访问工作区的方法。  定义可用数据的范围，以及应用的访问控制模式。 |
| [访问控制模式](#access-control-mode) | 工作区中的设置，用于定义是要在工作区级别还是资源级别应用权限。 |
| [权限](manage-access.md#manage-accounts-and-users) | 应用到工作区或资源的个人用户或用户组的权限。 定义用户有权访问哪些数据。 |
| [表级 RBAC](manage-access.md#table-level-rbac) | 适用于所有用户的可选粒度权限, 不考虑其访问模式或访问控制模式。 定义用户可以访问哪些数据类型。 |

## <a name="access-mode"></a>访问模式

访问模式是指用户如何访问 Log Analytics 工作区，并定义他们有权访问的数据范围。 

用户有两个选项可用于访问数据:

* **工作区-上下文**:你可以查看你有权使用的工作区中的所有日志。 在此模式下，只能查询该工作区中所有表内的所有数据。 使用工作区作为范围来访问日志时（例如，在 Azure 门户上的“Azure Monitor”菜单中选择“日志”时），将使用此访问模式。

    ![从工作区 Log Analytics 上下文](./media/design-logs-deployment/query-from-workspace.png)

* **资源上下文**:当你访问特定资源、资源组或订阅的工作区时, 例如, 当你从 Azure 门户中的资源菜单中选择**日志**时, 只能查看你有权访问的所有表中的资源的日志。 在此模式下，只能查询与该资源关联的数据。 此模式还启用粒度 RBAC。

    ![从资源 Log Analytics 上下文](./media/design-logs-deployment/query-from-resource.png)

    > [!NOTE]
    > 仅当日志与相关资源正确关联时, 才可用于资源上下文查询。 目前，以下资源存在限制：
    > - Azure 外部的计算机
    > - Service Fabric
    > - Application Insights
    >
    > 可以通过运行一个查询并检查所需的记录，来测试日志是否已适当关联到其资源。 如果 [_ResourceId](log-standard-properties.md#_resourceid) 属性中包含正确的资源 ID，则可以对数据进行以资源为中心的查询。

Azure Monitor 根据你执行日志搜索的上下文, 自动确定正确的模式。 范围始终显示在 Log Analytics 的左上方。

### <a name="comparing-access-modes"></a>比较访问模式

下表汇总了访问模式：

| | 工作区-上下文 | 资源上下文 |
|:---|:---|:---|
| 每种模式适合哪类用户？ | 集中管理。 需要配置数据收集的管理员，以及需要访问各种资源的用户。 目前还需要访问 Azure 外部资源的日志。 | 应用程序团队。 受监视 Azure 资源的管理员。 |
| 用户需要哪些权限才能查看日志？ | 对工作区的权限。 请参阅[管理帐户和用户](manage-access.md#manage-accounts-and-users)中的“工作区权限”。 | 对资源的读取访问权限。 请参阅[管理帐户和用户](manage-access.md#manage-accounts-and-users)中的“资源权限”。 权限可以继承（例如，从包含资源组继承），也可以直接分配给资源。 系统会自动分配对资源日志的权限。 |
| 权限范围是什么？ | 工作区。 具有工作区访问权限的用户可以从他们有权访问的表中查询工作区中的所有日志。 请参阅[表访问控制](manage-access.md#table-level-rbac) | Azure 资源。 用户可以从任何工作区查询日志以获取他们有权访问的特定资源、资源组或订阅, 但不能查询其他资源的日志。 |
| 用户如何访问日志？ | <ul><li>从**Azure Monitor**菜单启动**日志**。</li></ul> <ul><li>从**Log Analytics 工作区**启动**日志**。</li></ul> <ul><li>Azure Monitor[工作簿](../visualizations.md#workbooks)中。</li></ul> | <ul><li>从 Azure 资源的菜单启动**日志**</li></ul> <ul><li>从**Azure Monitor**菜单启动**日志**。</li></ul> <ul><li>从**Log Analytics 工作区**启动**日志**。</li></ul> <ul><li>Azure Monitor[工作簿](../visualizations.md#workbooks)中。</li></ul> |

## <a name="access-control-mode"></a>访问控制模式

"*访问控制" 模式*是每个工作区的一项设置, 用于定义如何确定工作区的权限。

* **需要工作区权限**：此控制模式不允许精细的 RBAC。 用户若要访问工作区，必须获得对该工作区或特定表的权限。

    如果用户访问工作区上下文模式下的工作区, 则他们可以访问他们已被授予访问权限的任何表中的所有数据。 如果用户按照资源上下文模式访问工作区, 则他们只能访问已被授予访问权限的任何表中该资源的数据。

    这是在 2019 年 3 月之前创建的所有工作区的默认设置。

* **使用资源或工作区权限**：此控制模式允许精细的 RBAC。 用户可以通过分配 Azure `read`权限向用户授予对其可以查看的资源的访问权限。 

    当用户访问工作区上下文模式下的工作区时, 将应用工作区权限。 当用户在资源上下文模式下访问工作区时, 只会验证资源权限, 并且忽略工作区权限。 要为用户启用 RBAC，可将其从工作区权限中删除，并允许识别其资源权限。

    这是在 2019 年 3 月之后创建的所有工作区的默认设置。

    > [!NOTE]
    > 如果用户对工作区只有资源权限, 则只有在将工作区访问模式设置为**使用资源或工作区权限**的情况下, 用户才能使用资源上下文模式访问工作区。

若要了解如何在门户中使用 PowerShell 或使用资源管理器模板更改访问控制模式, 请参阅[定义访问控制模式](manage-access.md#define-access-control-mode)。

## <a name="recommendations"></a>推荐

![资源上下文设计示例](./media/design-logs-deployment/workspace-design-resource-context-01.png)

此方案涉及 IT 组织中的单个工作区设计, 此类设计不受数据主权或法规遵从性的约束, 或者需要映射到你的资源部署到的区域。 它可让你的组织的安全和 IT 管理员团队利用改进的与 Azure 访问管理的集成以及更安全的访问控制。

所有资源、监视解决方案和见解 (如 Application Insights 和用于 VM 的 Azure Monitor)、支持由不同团队维护的基础结构和应用程序配置为将收集的日志数据转发给 IT 组织集中式共享工作区。 为每个团队的用户授予对其访问权限的资源的访问权限。

部署工作区体系结构后, 可以使用[Azure 策略](../../governance/policy/overview.md)在 azure 资源上强制实施此策略。 它提供一种方法来定义策略并确保符合 Azure 资源, 以便将所有诊断日志发送到特定的工作区。 例如, 使用 Azure 虚拟机或虚拟机规模集, 可以使用评估工作区相容性和报告结果的现有策略, 或自定义以在不符合条件下进行修正。  

## <a name="workspace-consolidation-migration-strategy"></a>工作区合并迁移策略

对于已经部署了多个工作区并对资源上下文访问模型进行合并感兴趣的客户, 我们建议你采用增量方法迁移到建议的访问模型, 而不尝试实现此目的快速或主动。 按照在合理的时间线上计划、迁移、验证和停用的分阶段方法, 将有助于避免任何计划外事件或对云操作产生意外影响。 如果出于符合性或业务原因没有数据保留策略, 则需要评估在过程中要从中进行迁移的工作区中保留数据的适当时间长度。 重新配置要向共享工作区报告的资源时, 仍可以根据需要在原始工作区中分析数据。 迁移完成后, 如果要在保持期结束之前将数据保留在原始工作区中, 请不要将其删除。

在规划迁移到此模型时, 请考虑以下事项:

* 了解你必须遵守的有关数据保留的行业法规和内部政策。
* 请确保应用程序团队可以在现有的资源上下文功能中工作。
* 确定授予应用程序团队资源的访问权限, 并在开发环境中进行测试, 然后再在生产环境中实施。
* 将工作区配置为启用 "**使用资源" 或 "工作区" 权限**。
* 删除 "应用程序团队" 权限以读取和查询工作区。
* 启用和配置任何监视解决方案, 如 Azure Monitor 容器和/或用于 VM 的 Azure Monitor、你的自动化帐户和管理解决方案 (例如, 更新管理、启动/停止 Vm 等) 部署在原始空间.

## <a name="next-steps"></a>后续步骤

若要实现本指南中建议的安全权限和控制, 请参阅[管理对日志的访问权限](manage-access.md)。