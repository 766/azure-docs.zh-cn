---
title: 在 Azure API 管理中使用外部缓存 | Microsoft Docs
description: 了解如何在 Azure API 管理中配置和使用外部缓存。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: apimpm
ms.openlocfilehash: e2362d06fa0ef795122a2d47a7a621b66fdd9470
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780357"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>在 Azure API 管理中使用用于 Redis 的外部 Azure 缓存

除了利用内置缓存之外，Azure API 管理还允许将响应缓存在用于 Redis 的外部 Azure 缓存中。

使用外部缓存可以克服内置缓存的一些限制。 如果希望达到以下目的，则它非常有用：

* 避免在执行 API 管理更新期间定期清除缓存
* 更好地控制你的缓存配置
* 缓存比 API 管理层允许的数据更多的数据
* 将缓存与 API 管理的消耗层配合使用

若要更详细地了解缓存，请参阅 [API 管理缓存策略](api-management-caching-policies.md)和 [Azure API 管理中的自定义缓存](api-management-sample-cache-by-key.md)。

![将自己的缓存带到 APIM](media/api-management-howto-cache-external/overview.png)

学习内容：

> [!div class="checklist"]
> * 在 API 管理中添加外部缓存

## <a name="prerequisites"></a>必备组件

要完成本教程，需要：

+ [创建一个 Azure API 管理实例](get-started-create-service-instance.md)
+ 了解 [Azure API 管理中的缓存](api-management-howto-cache.md)

## <a name="create-cache"> </a> 创建用于 Redis 的 Azure 缓存

本部分介绍了如何在 Azure 中创建用于 Redis 的 Azure 缓存。 如果你已有用于 Redis 的 Azure 缓存（无论是在 Azure 内部还是外部），则可以<a href="#add-external-cache">跳到</a>下一部分。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-external-cache"> </a>添加外部缓存

按照以下步骤在 Azure API 管理中添加用于 Redis 的外部 Azure 缓存。

![将自己的缓存带到 APIM](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> **使用从**设置指定区域的部署将进行通信的 API 管理与 API 管理的多区域配置时配置的缓存。 指定为**默认**的缓存将替代为具有区域值的缓存。
>
> 例如，如果 API 管理承载在“美国东部”、“东南亚”和“西欧”，并且配置了两个缓存，一个用于**默认**，另一个用于**东南亚**，则**东南亚**中的 API 管理将使用其自己的缓存，而其他两个区域将使用**默认**缓存项。

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>从同一订阅中添加用于 Redis 的 Azure 缓存

1. 在 Azure 门户中浏览到你的 API 管理实例。
2. 从左侧的菜单中选择“外部缓存”选项卡。 
3. 单击“ **+ 添加**”按钮。
4. 从“缓存实例”下拉字段中选择你的缓存。 
5. 选择**默认**，或指定所需的区域中**从使用**下拉列表中的字段。
6. 单击“ **保存**”。

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>添加位于当前 Azure 订阅外部或 Azure 外部（后者为一般情况）的用于 Redis 的 Azure 缓存

1. 在 Azure 门户中浏览到你的 API 管理实例。
2. 从左侧的菜单中选择“外部缓存”选项卡。 
3. 单击“ **+ 添加**”按钮。
4. 从“缓存实例”下拉字段中选择“自定义”。  
5. 选择**默认**，或指定所需的区域中**从使用**下拉列表中的字段。
6. 在“连接字符串”字段中提供用于 Redis 的 Azure 缓存连接字符串。 
7. 单击“ **保存**”。

## <a name="use-the-external-cache"></a>使用外部缓存

在 Azure API 管理中配置外部缓存后，可以通过缓存策略使用该缓存。 有关详细步骤，请参阅[添加缓存以提高 Azure API 管理中的性能](api-management-howto-cache.md)。

## <a name="next-steps"></a>后续步骤

* 有关缓存策略的详细信息，请参阅 [API 管理策略参考][API Management policy reference]中的[缓存策略][Caching policies]。
* 有关使用策略表达式按密钥缓存项目的信息，请参阅 [Azure API 管理中的自定义缓存](api-management-sample-cache-by-key.md)。

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
