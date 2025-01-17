---
title: 预生成的域引用-LUIS
titleSuffix: Azure Cognitive Services
description: 预构建的域参考，这些参考是语言理解智能服务 (LUIS) 中意向和实体的预构建集合。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 6e701396191ec8bf66ece0e2858b9f32857e7b17
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933589"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>LUIS 应用的预构建的域参考
此参考提供有关[预构建的域](luis-how-to-use-prebuilt-domains.md)的信息，这些参考是 LUIS 提供的意向和实体的预构建集合。

相比之下，[自定义域](luis-how-to-start-new-app.md)从无意向和模型开始。 可将任何预构建的域意向和实体添加到自定义模型中。

## <a name="supported-domains-across-cultures"></a>各个区域支持的域

唯一支持的区域性为英语。 

<!--

The table below summarizes the currently supported domains. Support for English is usually more complete than others.


| Entity Type       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendar    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -      | -    | -    | -     | -  |
| Communication   | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Email           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| HomeAutomation           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Notes      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Places    | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| RestaurantReservation   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo_IPA        | ✓    | ✓       | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Utilities          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Web    | ✓    | -        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
||||||||||||| 

-->

<br><br>

|实体类型|description|
|--|--|
|Calendar|日历是关于个人会议和约会的任何内容, 而 _是公共活动 (如世界杯计划、西雅图活动日历) 或常规日历 (如今天今天的那天, 开始的时间, 何时是劳动日)。|
|通信|请求通话、发送短信或即时消息、查找并添加联系人，以及各种其他通信相关的请求（通常为传出请求）。 仅限联系人姓名的查询不属于通信域。|
|Email|电子邮件是通信域的子域。 它主要包含通过电子邮件发送和接收消息的请求。|
|家庭自动化|家庭自动化域提供与控制智能家居设备相关的意向和实体。 它主要支持与灯光和空调相关的控制命令，但对于其他电器提供某些通用化功能。|
|说明|请注意，域提供意向和实体用于创建笔记以及记下用户的项。|
|场所|场所包括企业、机构、餐馆、公共场所和地址。 该域支持位置查找以及询问有关公共场所的信息，例如其位置、营业时间和距离。|
|餐位预订|餐位预订域支持用于处理餐位预订的意向。|
|待办事项|待办事项域提供任务列表的类型，让用户添加、标记和删除其待办事项。|
|公用事业|公用设施域是所有 LUIS 预生成模型的总域，其中包含不同方案中的通用意向和言语。|
|天气|天气域侧重于检查天气状况和提供有关位置与时间的建议，或按天气状况检查时间。|
|Web|Web 域提供用于搜索网站的意向和实体。|
