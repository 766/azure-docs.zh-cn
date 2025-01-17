---
title: 为 Azure 云服务创建面向 Internet 的负载均衡器
titlesuffix: Azure Load Balancer
description: 了解如何在经典部署模型中为云服务创建面向 Internet 的负载均衡器
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
tags: azure-service-management
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 66c978a7eb151ce9df939a11e2e3c0016c8e7c9e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60532511"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>开始为云服务创建面向 Internet 的负载均衡器

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure 云服务](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> 在使用 Azure 资源之前，请务必了解 Azure 当前具有的两种部署模型：Azure 资源管理器和经典。 在使用任何 Azure 资源之前，请确保了解 [部署模型和工具](../azure-classic-rm.md) 。 可以通过单击本文顶部的选项卡来查看不同工具的文档。 本文介绍经典部署模型。 还可以[了解如何使用 Azure Resource Manager 创建面向 Internet 的负载均衡器](load-balancer-get-started-internet-arm-ps.md)。

自动为云服务配置负载均衡器，并可以通过服务模型自定义云服务。

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>使用服务定义文件创建负载均衡器

可以利用用于 .NET 2.5 的 Azure SDK 来更新云服务。 云服务的终结点设置在[服务定义](https://msdn.microsoft.com/library/azure/gg557553.aspx).csdef 文件中进行。

下面的示例演示如何配置云部署的 servicedefinition.csdef 文件：

检查云部署生成的 .csdef 文件的代码段，可以看到配置的外部终结点，以便在端口 10000、10001 和 10002 上使用端口 HTTP。

```xml
<ServiceDefinition name="Tenant">
    <WorkerRole name="FERole" vmsize="Small">
        <Endpoints>
            <InputEndpoint name="FE_External_Http" protocol="http" port="10000" />
            <InputEndpoint name="FE_External_Tcp"  protocol="tcp"  port="10001" />
            <InputEndpoint name="FE_External_Udp"  protocol="udp"  port="10002" />

            <InputEndpoint name="HTTP_Probe" protocol="http" port="80" loadBalancerProbe="MyProbe" />

            <InstanceInputEndpoint name="InstanceEP" protocol="tcp" localPort="80">
                <AllocatePublicPortFrom>
                    <FixedPortRange min="10110" max="10120"  />
                </AllocatePublicPortFrom>
            </InstanceInputEndpoint>
            <InternalEndpoint name="FE_InternalEP_Tcp" protocol="tcp" />
        </Endpoints>
    </WorkerRole>
</ServiceDefinition>
```

## <a name="check-load-balancer-health-status-for-cloud-services"></a>检查云服务的负载均衡器运行状况状态

下面是运行状况探测器的示例：

```xml
<LoadBalancerProbes>
    <LoadBalancerProbe name="MyProbe" protocol="http" path="Probe.aspx" intervalInSeconds="5" timeoutInSeconds="100" />
</LoadBalancerProbes>
```

负载均衡器组合了终结点信息和探测器信息，以便以 `http://{DIP of VM}:80/Probe.aspx` 格式创建可用于查询服务的运行状况的 URL。

该服务通过同一 IP 地址检测定期探测。 这是来自正在运行虚拟机的节点主机的运行状况探测请求。 该服务必须对负载均衡器以 HTTP 200 状态代码进行响应，以假设该服务处于正常状态。 任何其他 HTTP 状态代码（例如 503）会直接将虚拟机从轮转列表中删除。

探测器定义还控制探测的频率。 在上面的示例中，负载均衡器每隔 5 秒探测一次终结点。 如果在 10 秒（两个探测时间间隔）内未收到肯定应答，则认为探测器关闭，并且虚拟机将从轮转列表中删除。 同样，如果该服务已退出轮转列表并且收到肯定应答，则该服务将立即回到轮转列表。 如果该服务在正常和不正常之间波动，负载均衡器可以决定推迟将该服务重新引入到轮转列表，直到多次探测的结果为正常。

有关详细信息，请查看[运行状况探测的服务定义架构](https://msdn.microsoft.com/library/azure/jj151530.aspx)。

## <a name="next-steps"></a>后续步骤

[开始配置内部负载均衡器](load-balancer-get-started-ilb-arm-ps.md)

[配置负载均衡器分发模式](load-balancer-distribution-mode.md)

[配置负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)

