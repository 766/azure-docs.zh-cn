---
title: 创建面向 Internet 的负载均衡器 - Azure PowerShell（经典）
titlesuffix: Azure Load Balancer
description: 了解如何使用 PowerShell 在经典模式下创建面向 Internet 的负载均衡器
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
ms.openlocfilehash: 54ef8782620a387d60454023d0e446279e467a99
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "64730331"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>开始在 PowerShell 中创建面向 Internet 的负载均衡器（经典）

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure 云服务](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> 在使用 Azure 资源之前，请务必了解 Azure 当前具有的两种部署模型：Azure 资源管理器和经典。 在使用任何 Azure 资源之前，请确保了解 [部署模型和工具](../azure-classic-rm.md) 。 可以通过单击本文顶部的选项卡来查看不同工具的文档。 本文介绍经典部署模型。 还可以[了解如何使用 Azure 资源管理器创建面向 Internet 的负载均衡器](load-balancer-get-started-internet-arm-ps.md)。

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>使用 PowerShell 设置负载均衡器

若要使用 powershell 设置负载均衡器，请完成以下步骤：

1. 如果从未使用过 Azure PowerShell，请参阅 [How to Install and Configure Azure PowerShell](/powershell/azure/overview)（如何安装和配置 Azure PowerShell），并始终按照说明进行操作，以登录到 Azure 并选择订阅。
2. 创建虚拟机后，可以使用 PowerShell cmdlet 将负载均衡器添加到同一云服务中的虚拟机。

在以下示例中，会将名为“webfarm”的负载均衡器集添加到云服务“mytestcloud”（或 myctestcloud.cloudapp.net），将负载均衡器的终结点添加到名为“web1”和“web2”的虚拟机。 负载均衡器在端口 80 上接收网络流量，并在由本地终结点（在此示例中为端口 80）定义的虚拟机之间使用 TCP 进行负载均衡。

### <a name="step-1"></a>步骤 1

为第一个 VM“web1”创建负载均衡终结点

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

### <a name="step-2"></a>步骤 2

使用相同的负载均衡器集名称为第二个 VM“web2”创建另一个终结点

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>从负载均衡器中删除虚拟机

可以使用 Remove-AzureEndpoint 从负载均衡器中删除虚拟机终结点

```powershell
Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin | Update-AzureVM
```

## <a name="next-steps"></a>后续步骤

还可以[开始创建内部负载均衡器](load-balancer-get-started-ilb-classic-ps.md)，并配置适合特定负载均衡器网络流量行为的[分发模式](load-balancer-distribution-mode.md)类型。

如果应用程序需要始终保持对负载均衡器后面的服务器的连接，可详细了解[负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)。 可借助该文章了解使用 Azure 负载均衡器时的空闲连接行为。
