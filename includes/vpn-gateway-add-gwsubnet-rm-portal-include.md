---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 42e983ead6f7562c6a31cf9ef4ad2d97d0ff9707
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673248"
---
1. 在[Azure 门户](https://portal.azure.com)中，选择要为其创建虚拟网关的资源管理器虚拟网络。

2. 在虚拟网络页的“设置”部分中，选择“子网”展开“子网”页    。

3. 在“子网”页中，选择“网关子网”打开“添加子网”页    。

   ![添加网关子网](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "添加网关子网")

4. 子网的“名称”自动填充为值“GatewaySubnet”   。 Azure 需要此值才能识别作为网关子网的子网。 根据配置要求调整自动填充的“地址范围”值，然后选择“确定”以创建该子网   。

   ![添加子网](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "添加子网")
