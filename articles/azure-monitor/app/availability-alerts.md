---
title: 设置可用性警报，使用 Azure Application Insights |Microsoft Docs
description: 在 Application Insights 中设置 Web 测试。 当网站不可用或响应速度缓慢时接收警报。
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: cc022f91d4b4fec42929769df8c979320548a1f9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305185"
---
# <a name="availability-alerts"></a>可用性警报

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 将来自全球各地的 Web 请求定期发送到应用程序。 如果你的应用程序没有响应，或它的响应速度太慢，它可以通知你。

## <a name="enable-alerts"></a>启用警报

默认情况下，现在会自动启用警报，但为了完全配置警报，首先需要最初创建可用性测试。

![创建体验](./media/availability-alerts/create-test.png)

> [!NOTE]
>  使用[新的统一警报](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)时，必须在警报体验中配置预警规则严重性和[操作组](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)的通知首选项  。 如果不执行以下步骤，则只会收到门户内通知。

1. 保存可用性测试后，在详细信息选项卡上单击你刚才所做的测试旁边的省略号。 单击“编辑警报”。

   ![编辑保存后](./media/availability-alerts/edit-alert.png)

2. 设置所需的严重性级别、规则说明，最重要的是设置具有要用于此警报规则的通知首选项的操作组。

   ![编辑保存后](./media/availability-alerts/set-action-group.png)

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>当 Y 个位置中有 X 个报告失败时发出警报

创建新的可用性测试时，会在[新的统一警报体验](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)中默认启用“Y 个位置中的 X 个”警报规则。 可通过选择“经典”选项或选择禁用该警报规则来选择退出。

> [!NOTE]
> 通过执行上述步骤，将操作组配置为在警报触发时接收通知。 如果不执行此步骤，则在规则触发时只会收到门户内通知。
>

### <a name="alert-on-availability-metrics"></a>根据可用性指标发出警报

使用[新的统一警报](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)时，可以根据分段聚合可用性发出警报以及测试持续时间指标：

1. 在指标体验中选择 Application Insights 资源，然后选择可用性指标：

    ![可用性指标选择](./media/availability-alerts/select-metric.png)

2. 从菜单中配置警报选项将转到新体验，可在其中选择特定测试或位置以将警报规则设置为打开。 还可以在此处配置此警报规则的操作组。

### <a name="alert-on-custom-analytics-queries"></a>根据自定义分析查询发出警报

使用[新的统一警报](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)时，可以根据[自定义日志查询](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log)发出警报。 借助自定义查询，可以在有助于获得最可靠的可用性问题信号的任意条件下发出警报。 如果使用 TrackAvailability SDK 发送自定义可用性结果，这也特别适用。 

> [!Tip]
> 可用性数据的指标包括可能通过调用我们的 TrackAvailability SDK 提交的任何自定义可用性结果。 可以使用“根据指标发出警报”支持根据自定义可用性结果发出警报。
>

## <a name="troubleshooting"></a>故障排除

专用[疑难解答文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>后续步骤

* [多步骤 web 测试](availability-multistep.md)
* [Url ping web 测试](monitor-web-app-availability.md)

