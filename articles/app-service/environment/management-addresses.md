---
title: 应用服务环境管理地址 - Azure
description: 列出用于监管应用服务环境的管理地址
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: afc43005765e3ae91c829cfc6b25a3f372241e0b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561520"
---
# <a name="app-service-environment-management-addresses"></a>应用服务环境管理地址

应用服务环境 (ASE) 是在 Azure 虚拟网络 (VNet) 中运行的 Azure App Service 的单租户部署。  虽然 ASE 在 VNet 中运行, 但仍必须可通过 Azure App Service 用于管理服务的多个专用 IP 地址进行访问。  对于 ASE, 管理流量会遍历用户控制的网络。 如果此流量被阻塞或被错误路由，则 ASE 将会挂起。 有关 ASE 网络依赖项的详细信息, 请参阅[网络注意事项和应用服务环境][networking]。 有关 ASE 的一般信息, 可以从[应用服务环境简介][intro]着手。

所有 ASE 都有一个公用 VIP，管理流量将从其中进入。 来自这些地址的传入管理流量将传入到 ASE 的公共 VIP 上的端口 454 和 455。 本文档列出了发往 ASE 的管理流量的应用服务源地址。 这些地址也位于名为 AppServiceManagement 的 IP 服务标记中。

可以在路由表中配置下面所述的地址, 以避免管理流量存在非对称路由问题。 路由作用于 IP 级别的流量, 但不知道流量方向, 或者流量是 TCP 回复消息的一部分。 如果 TCP 请求的答复地址不同于发送到的地址, 则表示存在非对称路由问题。 若要避免 ASE 管理流量的非对称路由问题, 需要确保将答复发回发送到的地址。 若要详细了解如何将 ASE 配置为在本地发送出站流量的环境中操作, 请参阅[使用强制隧道配置 ase][forcedtunnel]

## <a name="list-of-management-addresses"></a>管理地址的列表 ##

| 地区 | 地址 |
|--------|-----------|
| 所有公共区域 | 13.64.115.203, 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.127.117, 13.77.50.128, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64,23.100.226.236, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.90.240.166, 40.91.126.196, 40.112.242.192, 40.119.4.111,40.124.47.188, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.80.89, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197,52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.43.242.137, 104.44.129.141, 104.44.129.243,104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 104.214.49.0, 157.55.176.93, 157.55.208.185, 191.233.203.64, 191.236.154.88 |
| Microsoft Azure 政府 | 23.97.29.209、13.72.53.37、13.72.180.105、23.97.0.17、23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>配置网络安全组

利用网络安全组, 无需担心各个地址或维护自己的配置。 有一个名为 AppServiceManagement 的 IP 服务标记, 它与所有地址保持同步。 若要在 NSG 中使用此 IP 服务标记, 请在门户中, 打开网络安全组 UI, 然后选择 "入站安全规则"。 如果预先存在用于入站管理流量的规则, 请对其进行编辑。 如果未对 ASE 创建此 NSG，或者此 NSG 是全新的，请选择“添加”。 在“源”下拉列表中，选择“服务标记”。  在 "源服务" 标记下, 选择 " **AppServiceManagement**"。 将源端口范围设置为 \*，将“目标”设置为“任何”，将目标端口范围设置为 **454-455**，将“协议”设置为“TCP”，将“操作”设置为“允许”。 如果要进行规则, 则需要设置优先级。 

![使用服务标记创建 NSG][1]

## <a name="configuring-a-route-table"></a>配置路由表

可将管理地址放在包含 Internet 下一跃点的路由表中，以确保所有入站管理流量能够通过同一路径返回。 配置强制隧道时需要这些路由。 若要创建路由表，可以使用门户、PowerShell 或 Azure CLI。  下面是在 PowerShell 提示符下使用 Azure CLI 创建路由表的命令。 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    $managementAddresses = "13.64.115.203", "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.127.117", "13.77.50.128", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "23.100.226.236", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.90.240.166", "40.91.126.196", "40.112.242.192", "40.119.4.111", "40.124.47.188", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.80.89", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.14.230", "65.52.172.237", "65.52.193.203", "70.37.57.58", "70.37.89.222", "104.43.242.137", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "104.214.49.0", "157.55.176.93", "157.55.208.185", "191.233.203.64", "191.236.154.88"

    az network route-table create --name $rt --resource-group $rg --location $location
    foreach ($ip in $managementAddresses) {
        az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
    }

创建路由表后，需在 ASE 子网中设置该路由表。  

## <a name="get-your-management-addresses-from-api"></a>通过 API 获取管理地址 ##

可以使用以下 API 调用列出与 ASE 匹配的管理地址。

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

API 返回一个 JSON 文档，其中包含 ASE 的所有入站地址。 地址列表包括管理地址、ASE 使用的 VIP 和 ASE 子网地址范围本身。  

若要使用 [armclient](https://github.com/projectkudu/ARMClient) 调用此 API，请使用以下命令，但请替换为你的订阅 ID、资源组和 ASE 名称。  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
