---
title: 监视公共基本负载均衡器的操作、事件和计数器
titlesuffix: Azure Load Balancer
description: 了解如何为公共基本负载均衡器启用警报事件以及探测运行状况日志记录
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: allensu
ms.openlocfilehash: 1995ad5e8179fdee11e960c2ad0e7c03602ebd31
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274803"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>用于公共基本负载均衡器的 Azure Monitor 日志

>[!IMPORTANT] 
>Azure 负载均衡器支持两种不同的类型：“基本”和“标准”。 本文介绍基本负载均衡器。 有关标准负载均衡器的详细信息，请参阅[标准负载均衡器概述](load-balancer-standard-overview.md)，该概述通过 Azure Monitor 中的多维指标公开遥测数据。

可以在 Azure 中使用不同类型的日志对基本负载均衡器进行管理和故障排除。 可通过门户访问其中某些日志。 可从 Azure Blob 存储提取所有日志并在 Excel 和 PowerBI 等各种工具中查看。 可从下表了解有关各种类型日志的详细信息。

* **审核日志：** 可以使用 [Azure 审核日志](../monitoring-and-diagnostics/insights-debugging-with-events.md)（以前称为操作日志）查看提交到 Azure 订阅的所有操作及其状态。 审核日志默认情况下启用，并且可以在 Azure 门户中查看。
* **警报事件日志：** 可以使用此日志查看负载均衡器引发的警报。 每隔五分钟收集一次负载均衡器的状态。 仅在引发了负载均衡器警报事件的情况下，才会向此日志写入相关内容。
* **运行状况探测日志：** 可以使用此日志查看运行状况探测器检测到的问题，例如后端池中由于运行状况探测失败未从负载均衡器接收请求的实例数。 当运行状况探测状态发生更改时，将写入此日志。

> [!IMPORTANT]
> Azure Monitor 日志当前仅适用于公共基本负载均衡器。 日志仅适用于在资源管理器部署模型中部署的资源。 不能将日志用于经典部署模型中的资源。 有关部署模型的详细信息，请参阅[了解 Resource Manager 部署和经典部署](../azure-resource-manager/resource-manager-deployment-model.md)。

## <a name="enable-logging"></a>启用日志记录

每个 Resource Manager 资源都会自动启用审核日志记录。 需启用事件和运行状况探测日志记录才能开始收集通过这些日志提供的数据。 使用以下步骤启用日志记录。

登录到 [Azure 门户](https://portal.azure.com)。 如果还没有负载均衡器，请先[创建负载均衡器](load-balancer-get-started-internet-arm-ps.md)，再继续。

1. 在门户中，单击“浏览”。 
2. 选择“负载均衡器”。 

    ![门户 - 负载均衡器](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. 选择现有的负载均衡器，并单击“所有设置”。 
4. 在负载均衡器名称下的对话框右侧，滚动到“监视”，并单击“诊断”。  

    ![门户 - 负载均衡器 - 设置](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. 在“诊断”窗格中，在“状态”下选择“开”。   
6. 单击“存储帐户”。 
7. 在“日志”下，选择现有存储帐户或创建新存储帐户。  使用滑块来确定事件数据值得在事件日志中存储的天数。 
8. 单击“保存”  。

诊断将保存在指定存储帐户的表存储中。 如果日志未保存，这是因为没有生成相关日志。

![门户 - 诊断日志](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> 审核日志不需要单独的存储帐户。 使用存储来记录事件和运行状况探测需支付服务费用。

## <a name="audit-log"></a>审核日志

默认情况下会生成审核日志。 日志在 Azure 的事件日志存储区中保留 90 天。 通过阅读[查看事件和审核日志](../monitoring-and-diagnostics/insights-debugging-with-events.md)一文可了解有关这些日志的详细信息。

## <a name="alert-event-log"></a>警报事件日志

只有基于每个负载均衡器启用了此日志，才会生成此日志。 事件以 JSON 格式记录，并存储在启用日志记录时指定的存储帐户中。 下面是事件的示例。

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

JSON 输出显示的 *eventname* 属性说明负载均衡器创建警报的原因。 在本示例中，生成警报是因为源 IP NAT 限制 (SNAT) 导致 TCP 端口耗竭。

## <a name="health-probe-log"></a>运行状况探测日志

仅当按如上所述基于每个负载均衡器启用此日志后，才会生成此日志。 数据存储在启用日志记录时指定的存储帐户中。 创建了名为“insights-logs-loadbalancerprobehealthstatus”的容器并记录了以下数据：

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

JSON 输出在属性字段显示了探测运行状况的基本信息。 *dipDownCount* 属性显示了在后端因探测响应失败而收不到网络流量的实例的总数。

## <a name="view-and-analyze-the-audit-log"></a>查看和分析审核日志

可以使用任何以下方法查看和分析审核日志数据：

* **Azure 工具：** 通过 Azure PowerShell、Azure 命令行界面 (CLI)、Azure REST API 或 Azure 预览门户检索审计日志中的信息。 [使用 Resource Manager 审核操作](../azure-resource-manager/resource-group-audit.md)一文中详细介绍了每种方法的分步说明。
* **Power BI：** 如果尚无 [Power BI](https://powerbi.microsoft.com/pricing) 帐户，可免费试用。 使用[适用于 Power BI 的 Azure 审核日志内容包](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs)，可以借助预配置的仪表板分析数据，也可以自定义视图来满足自己的要求。

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>查看和分析运行状况探测和事件日志

需要连接到存储帐户并检索事件和运行状况探测日志的 JSON 日志项。 下载 JSON 文件后，可以将它们转换为 CSV 并在 Excel、PowerBI 或任何其他数据可视化工具中查看。

> [!TIP]
> 如果熟悉 Visual Studio 和更改 C# 中的常量和变量值的基本概念，则可以使用 GitHub 提供的[日志转换器工具](https://github.com/Azure-Samples/networking-dotnet-log-converter)。

## <a name="additional-resources"></a>其他资源

* [使用 Power BI 直观显示 Azure 审核日志](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)博客文章。
* [查看和分析 Power BI 中的 Azure 审核日志及更多内容](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)博客文章。

## <a name="next-steps"></a>后续步骤

[了解负载均衡器探测](load-balancer-custom-probe-overview.md)
