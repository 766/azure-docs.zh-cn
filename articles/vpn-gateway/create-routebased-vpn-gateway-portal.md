---
title: 创建基于路由的 VPN 网关：Azure 门户 | Microsoft Docs
description: 使用 Azure 门户创建基于路由的 VPN 网关
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: cherylmc
ms.openlocfilehash: 2a04c0fa2d92514103377c2aef420290d1bdd057
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781173"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>使用 Azure 门户创建基于路由的 VPN 网关

本文可帮助你使用 Azure 门户快速创建基于路由的 Azure VPN 网关。  创建与本地网络的 VPN 连接时使用 VPN 网关。 还可以使用 VPN 网关连接 VNet。 

本文中的步骤将创建 VNet、子网、网关子网和基于路由的 VPN 网关（虚拟网络网关）。 完成网关创建后，可以创建连接。 执行这些步骤需要 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="vnet"></a>创建虚拟网络

[!INCLUDE [create-gateway](../../includes/vpn-gateway-create-virtual-network-portal-include.md)]

## <a name="gwsubnet"></a>添加网关子网

[!INCLUDE [gateway subnet](../../includes/vpn-gateway-add-gateway-subnet-portal-include.md)]

## <a name="gwvalues"></a>配置和创建网关

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>基本网关 SKU 不支持 IKEv2 或 RADIUS 身份验证。 如果计划将 Mac 客户端连接到虚拟网络, 请不要使用基本 SKU。

## <a name="viewgw"></a>查看 VPN 网关

1. 创建网关后，请在门户中导航到 VNet1。 VPN 网关将作为已连接的设备显示在概述页上。

   ![已连接的设备](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "已连接的设备")

2. 在设备列表中，单击 **VNet1GW** 可查看详细信息。

   ![查看 VPN 网关](./media/create-routebased-vpn-gateway-portal/view-gateway.png "查看 VPN 网关")

## <a name="next-steps"></a>后续步骤

完成创建网关后，可以创建虚拟网络与另一个 VNet 之间的连接。 或者，创建虚拟网络与本地位置之间的连接。

> [!div class="nextstepaction"]
> [创建站点到站点连接](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [创建点到站点连接](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [创建与另一个 VNet 的连接](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
