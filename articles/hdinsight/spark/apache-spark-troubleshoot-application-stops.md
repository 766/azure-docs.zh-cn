---
title: Apache Spark 流式处理应用程序在24天内停止处理数据, Azure HDInsight 的日志中没有已知错误
description: 在执行24天后, Apache Spark 流式处理应用程序将停止, 并且日志文件中没有错误。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 002c45c514b5d8207a1aa70ec8e1c749677a239a
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620878"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>方案:在 Azure HDInsight 中执行24天后, Apache Spark 流式处理应用程序停止

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

在执行24天后, Apache Spark 流式处理应用程序将停止, 并且日志文件中没有错误。

## <a name="cause"></a>原因

`livy.server.session.timeout`值控制 Apache Livy 应等待会话完成的时间。 会话长度达到`session.timeout`值后, 会自动终止 Livy 会话和应用程序。

## <a name="resolution"></a>解决

对于长时间运行的作业, 请`livy.server.session.timeout`使用 Ambari UI 增加的值。 可使用 URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`从 Ambari UI 访问 Livy 配置。

将`<yourclustername>`替换为 HDInsight 群集的名称, 如门户中所示。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系, 通过将 Azure 社区连接到适当的资源来改进客户体验: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
