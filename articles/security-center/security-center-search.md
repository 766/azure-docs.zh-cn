---
title: Azure 安全中心搜索 | Microsoft Docs
description: 了解 Azure 安全中心如何使用 Azure Monitor 日志搜索来检索和分析你的安全数据。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin
ms.openlocfilehash: c88e2025444aa2fd83e05fdff97ea640ceefaa04
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662913"
---
# <a name="azure-security-center-search-retired"></a>Azure 安全中心搜索 (已停用)

> [!NOTE]
> 安全中心的搜索仪表板已于2019年7月31日停用。 有关详细信息和备用服务，请参阅用[安全中心功能的停用（2019 年 7 月）](security-center-features-retirement-july2019.md#menu_search)。

Azure 安全中心使用[Azure Monitor 日志搜索](../log-analytics/log-analytics-log-searches.md)来检索和分析你的安全数据。 Azure Monitor 日志包含一种查询语言, 可用于快速检索和合并数据。 从安全中心, 你可以利用 Azure Monitor 日志搜索来构造查询并分析收集的数据。

安全中心的免费层和标准层均提供了此搜索功能。  日志搜索提供的数据取决于应用到工作区的层级别。  请参阅安全中心[定价页](../security-center/security-center-pricing.md)了解详细信息。


> [!NOTE]
> 安全中心不会为处于免费层下的工作区保存安全数据。 可以将各种日志发送到处于免费层下的工作区，然后搜索该数据，但搜索结果不会包含来自安全中心的数据。 安全中心仅将数据保存到处于标准层下的工作区。
>
>

## <a name="access-search"></a>访问“搜索”
1. 在“安全中心”主菜单下，选择“搜索”。

   ![选择“日志搜索”][1]

2. 安全中心将列出 Azure 订阅下的所有工作区。 选择工作区。 （如果只有一个工作区，将不会出现此工作区选择器。）

   ![选择工作区][2]

3. “日志搜索”将打开。 若要在所选工作区下查询更多数据，请输入以下查询示例：

   SecurityEvent | where EventID == 4625 | summarize count() by TargetAccount

   结果显示所有无法登录的帐户 (事件 4625)。

   ![搜索结果][3]

有关如何在所选工作区下查询数据的详细信息, 请参阅[Kusto 查询语言](../log-analytics/log-analytics-search-reference.md)。

## <a name="next-steps"></a>后续步骤
在本文中，你已了解如何在安全中心中访问搜索功能。 安全中心使用 Azure Monitor 日志搜索。 若要详细了解 Azure Monitor 日志搜索, 请参阅:

- [什么是 Azure Monitor 日志？](../log-analytics/log-analytics-overview.md) – Azure Monitor 日志概述
- [了解 Azure Monitor 日志中的日志搜索](../log-analytics/log-analytics-log-search-new.md)-介绍如何在 Azure Monitor 日志中使用日志搜索, 并提供在创建日志搜索之前应了解的概念。
- [在 Azure Monitor 日志中使用日志搜索查找数据](../log-analytics/log-analytics-log-searches.md)–有关使用日志搜索的教程
- [Kusto 搜索参考](../log-analytics/log-analytics-search-reference.md)–介绍 Azure Monitor 日志中的查询语言

若要了解有关安全中心的详细信息，请参阅：

- [安全中心概述](security-center-intro.md) – 介绍安全中心的主要功能

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
