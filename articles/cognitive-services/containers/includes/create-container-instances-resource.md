---
title: 容器支持
titleSuffix: Azure Cognitive Services
description: 了解如何创建 Azure 容器实例资源。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 05284d434e6bd22fd50957f7cc5ec966f88a4fd4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229181"
---
## <a name="create-an-azure-container-instance-resource"></a>创建 Azure 容器实例资源

1. 转到[创建](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances)容器实例的页面。

2. 上**基础知识**选项卡上，输入以下详细信息：

    |设置|值|
    |--|--|
    |订阅|选择订阅。|
    |资源组|选择可用的资源组或创建一个新如`cognitive-services`。|
    |容器名称|输入一个名称，例如`cognitive-container-instance`。 名称必须在较低的上限。|
    |Location|选择用于部署的区域。|
    |映像类型|`Public`|
    |映像名称|输入的认知服务容器位置。 该位置可以是在中使用相同`docker pull`命令，请参阅[容器存储库和映像](../../cognitive-services-container-support.md#container-repositories-and-images)可用映像名称和其相应的存储库。|
    |OS 类型|`Linux`|
    |Size|将大小更改为特定的认知服务容器的建议建议：<br>2 个 CPU 内核<br>4 GB

3. 上**网络**选项卡上，输入以下详细信息：

    |设置|ReplTest1|
    |--|--|
    |端口|将 TCP 端口设置为`5000`。 公开端口 5000 上的容器。|

4. 上**高级**选项卡上，输入所需**环境变量**计费 ACI 资源设置的容器：

    | Key | ReplTest1 |
    |--|--|
    |`apikey`|从复制**密钥**资源页面。 它是不包含空格或短划线的 32 字母数字字符字符串`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`。|
    |`billing`|从复制**概述**资源页面。|
    |`eula`|`accept`|

1. 单击**查看和创建**
1. 验证通过后，单击**创建**完成创建过程
1. 已成功部署资源时，就可以