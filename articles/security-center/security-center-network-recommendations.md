---
title: 保护 Azure 安全中心的网络资源 | Microsoft Docs
description: 本文档介绍 Azure 安全中心中的建议，以帮助你保护 Azure 网络资源并保持符合安全策略。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: v-mohabe
ms.openlocfilehash: 6dfd3bd86e55d19bf1c15486a502a4cbab3c96a8
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905303"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>保护 Azure 安全中心的网络资源
Azure 安全中心不断分析 Azure 资源的安全状态，以实现网络安全最佳做法。 在安全中心识别出潜在的安全漏洞时，它会创建一些建议，指导完成配置所需控件以强化和保护资源的过程。

本文从网络安全角度介绍适用于 Azure 资源的建议。 网络建议以下一代防火墙、网络安全组、JIT VM 访问过度宽容的入站流量规则等为中心。 有关网络建议和修复操作的列表，请参阅[管理 Azure 安全中心的安全建议](security-center-recommendations.md)。

> [!NOTE]
> 借助“网络”页，可从网络角度深入了解 Azure 资源运行状况。 网络映射和自适应网络控制仅适用于 Azure 安全中心标准层。 [如果使用免费层，则可以单击按钮查看旧网络并接收网络资源建议](#legacy-networking)。
>

“网络”页概述了可以深入了解的部分，以获取有关网络资源运行状况的详细信息：

- 网络映射（仅限 Azure 安全中心标准层）
- 自适应网络强化
- 网络安全建议。
- 旧版“网络”边栏选项卡（以前的网络边栏选项卡） 
 
![网络窗格](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>网络映射
交互式网络映射提供了带有安全覆盖的图形视图，提供了强化网络资源的建议和见解。 你可以通过映射查看 Azure 工作负载的拓扑、虚拟机和子网之间的连接、从映射向下钻取特定资源的功能以及这些资源的建议。

打开网络映射：

1. 在“安全中心”的“资源安全机制”下，选择“网络”。
2. 在“网络映射”下，单击“查看拓扑”。
 
拓扑图的默认视图显示：

- 在 Azure 中选择的订阅。 该图支持多个订阅。
- 资源管理器资源类型的 Vm、子网和 Vnet (不支持经典 Azure 资源)
- 对等互连 Vnet
- 仅限具有高或中等严重性的[网络建议](security-center-recommendations.md)的资源  
- 面向 Internet 的资源
- 该映射针对在 Azure 中选择的订阅进行了优化。 如果修改了选择，则会根据新设置重新计算并重新优化映射。  

![拓扑图](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>了解网络映射

网络映射可以在“拓扑”视图和“流量”视图中显示 Azure 资源。

### <a name="the-topology-view"></a>拓扑视图

在网络映射的“拓扑”视图中，可以查看有关网络资源的以下见解：

- 在内圈中，可以看到所选订阅中的所有 Vnet，下一个圈是所有子网，外圈是所有虚拟机。
- 可以在连接映射中资源的行中了解到哪些资源相互关联，以及 Azure 网络的结构。 
- 使用严重性指示器可以快速了解安全中心建议打开哪些资源。
- 可以单击任何资源深入查看这些资源，并在网络映射的上下文中直接查看该资源及其建议的详细信息。  
- 如果映射上显示的资源太多，Azure 安全中心将使用其专有算法对资源进行智能群集，突出显示处于最关键状态和具有最高严重性的建议的资源。 

由于映射是交互式和动态的，因此每个节点都是可点击的，并且视图可以根据筛选器进行更改：

1. 可以使用顶部的筛选器修改可在网络映射上看到的内容。 可以根据以下内容聚焦映射：

   -  **安全运行状况**：可以根据 Azure 资源的严重性（高、中、低）筛选映射。
   - **建议**：可以根据这些资源上处于活动状态的建议来选择显示的资源。 例如，只能查看安全中心建议启用网络安全组的资源。
   - **网络区域**：默认情况下，映射仅显示面向 Internet 的资源，也可以选择内部 VM。
 
2. 可以随时单击左上角的“重置”以将映射恢复为默认状态。

向下钻取资源：

1. 在映射上选择特定资源时，右侧窗格将打开，并提供有关资源、连接的安全解决方案（如果有）以及与资源相关的建议的常规信息。 对于选择的每种资源，它都是相同类型的行为。 
2. 将鼠标悬停在映射中的节点上时，可以查看有关资源的常规信息，包括订阅、资源类型和资源组。
3. 使用该链接可放大工具提示并将映射重新聚焦在该特定节点上。 
4. 要将映射的焦点从特定节点移开，请缩小。

### <a name="the-traffic-view"></a>流量视图

“流量”视图提供了资源之间所有可能流量的映射。 这提供了配置的所有规则的可视化映射，这些规则定义了哪些资源可以与谁通信。 由此可查看网络安全组的现有配置，以及快速识别工作负载中可能存在的有风险的配置。

### <a name="uncover-unwanted-connections"></a>发现不需要的连接

此视图的优势在于能够显示这些允许的连接以及存在的漏洞，因此可以使用此截面的数据对资源执行必要的强化。 

例如，可能会检测到两台你不知道可以通信的计算机，从而能够更好地隔离工作负载和子网。

### <a name="investigate-resources"></a>调查资源

向下钻取资源：

1. 在映射上选择特定资源时，右侧窗格将打开，并提供有关资源、连接的安全解决方案（如果有）以及与资源相关的建议的常规信息。 对于选择的每种资源，它都是相同类型的行为。 
2. 单击“流量”以查看资源上可能的出站和入站流量列表 - 这是一个全面的列表，列出谁可以与资源进行通信、可以与谁通信以及通过哪些协议和端口进行通信。 例如, 当你选择某个 VM 时, 它可以与之通信的所有 Vm 都将显示出来, 并且当你选择一个子网时, 将显示它可以与之通信的所有子网。

此数据基于对网络安全组的分析以及分析多个规则以了解其交叉和交互的高级机器学习算法。 

![网络流量映射](./media/security-center-network-recommendations/network-map-traffic.png)

## 旧网络 <a name ="legacy-networking"></a>

如果没有安全中心标准层，则本节介绍如何查看免费的网络建议。

要访问此信息，请在“网络”边栏选项卡中单击“查看旧网络”。 

![旧网络](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>面向 Internet 的终结点部分
在“面向 Internet 的终结点”部分中，可以查看当前配置了面向 Internet 的终结点的虚拟机及其状态。

此表提供终结点名称、面向 Internet 的 IP 地址，以及网络安全组和 NGFW 建议当前的严重性状态。 此表按严重性排序。

### <a name="networking-topology-section"></a>拓扑部分
“拓扑”部分提供了资源的分层视图。

此表按严重性排序（虚拟机和子网）。

在此拓扑视图中，第一层级显示 Vnets。 第二层级显示子网，第三层级显示属于这些子网的虚拟机。 右侧一栏显示网络安全组对这些资源的建议的当前状态。

第三层级显示虚拟机，与前面的描述类似。 可以单击任何资源了解详细信息，或应用所需的安全控制或配置。

## <a name="network-recommendations"></a>网络建议

|建议名称|描述|Severity|安全功能分数|资源类型|
|----|----|----|----|----|----|
|应启用子网级别的网络安全组|启用网络安全组来控制子网中部署的资源的网络访问。|高/中|30|Subnet|
|虚拟机应与网络安全组相关联|启用网络安全组以控制虚拟机的网络访问。|高/中|30|虚拟机|
|对于具有面向 Internet 的 Vm 的许可网络安全组, 应限制访问权限|通过限制现有允许规则的访问权限, 强化面向 Internet 的 Vm 的网络安全组。|高|20|虚拟机|
|应强化 IaaS Nsg 上的 web 应用程序的规则|强化运行 web 应用程序的虚拟机的网络安全组 (NSG), 其中包含对 web 应用程序端口而言过于宽松的 NSG 规则。|高|20|虚拟机|
|应限制对应用服务的访问|通过更改网络配置来限制对应用服务的访问, 拒绝范围太大的入站流量。|高|10|应用服务|
|应在虚拟机上关闭管理端口|强化虚拟机的网络安全组, 以限制对管理端口的访问。|高|10|虚拟机|
应启用 DDoS 防护标准版|启用 DDoS 保护服务标准, 保护包含具有公共 Ip 的应用程序的虚拟网络。 DDoS 保护支持网络容量耗尽和协议攻击的缓解。|高|10|虚拟网络|
|应禁用虚拟机上的 IP 转发|禁用 IP 转发。 在虚拟机的 NIC 上启用 IP 转发后, 计算机可以接收发送到其他目标的流量。 很少需要 IP 转发 (例如, 使用 VM 作为网络虚拟设备时), 因此应由网络安全团队进行审核。|中型|10|虚拟机|
|只能通过 HTTPS 访问 Web 应用程序|为 web 应用程序启用 "仅 HTTPS" 访问权限。 使用 HTTPS 可确保服务器/服务进行身份验证, 并保护传输中的数据免受网络层窃听攻击。|中型|20|Web 应用程序|
|应在虚拟机上应用实时网络访问控制|应用实时 (JIT) 虚拟机 (VM) 访问控制以永久锁定对所选端口的访问权限, 并使授权用户仅在有限的时间内通过 JIT 打开这些端口。|高|20|虚拟机|
|只能通过 HTTPS 访问函数应用|为 function app 启用 "仅 HTTPS" 访问权限。 使用 HTTPS 可确保服务器/服务进行身份验证, 并保护传输中的数据免受网络层窃听攻击。|中型|20|Function App|
|应该启用安全传输到存储帐户|启用到存储帐户的安全传输。 安全传输选项会强制存储帐户仅接受来自安全连接 (HTTPS) 的请求。 使用 HTTPS 可确保服务器与服务之间的身份验证, 并保护数据免遭网络层攻击 (如中间人、窃听和会话劫持) 的传输。|高|20|存储帐户|

## <a name="see-also"></a>请参阅
若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下内容：

* [在 Azure 安全中心保护计算机和应用程序](security-center-virtual-machine-protection.md)
* [保护 Azure 安全中心中的应用程序](security-center-application-recommendations.md)
* [保护 Azure 安全中心中的 Azure SQL 服务](security-center-sql-service-recommendations.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](tutorial-security-policy.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题。
