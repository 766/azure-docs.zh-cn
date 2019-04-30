---
title: 部署 Azure 虚拟网络-CLI 中的 IPv6 双堆栈应用程序
titlesuffix: Azure Virtual Network
description: 本文说明如何部署使用 Azure CLI 的 Azure 虚拟网络中的 IPv6 双堆栈应用程序。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 12fbf2ae5387ac0a9350cc203f4a6f2587c8dafe
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130843"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>部署 Azure 虚拟网络-CLI （预览版） 中的 IPv6 双堆栈应用程序

本文介绍如何部署双堆栈 （IPv4 + IPv6） 应用程序中包括一个双堆栈子网，使用双 （IPv4 + IPv6） 前端配置，具有双重 IP 配置的 Nic 的 Vm 的负载均衡器使用的双堆栈虚拟网络的 Azure双网络安全组规则和双公共 Ip。

> [!Important]
> 为 Azure 虚拟网络的 IPv6 双堆栈当前处于公共预览状态。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果还没有 Azure 订阅，请现在就创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您决定安装并改为本地使用 Azure CLI，本快速入门需要你使用 Azure CLI 版本 2.0.49 或更高版本。 若要查找已安装的版本，请运行 `az --version`。 有关安装或升级信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>必备组件
若要为 Azure 虚拟网络功能使用 IPv6，必须配置你的订阅使用 Azure PowerShell，如下所示：

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
功能注册最多需要 30 分钟才能完成。 可以通过运行以下 Azure CLI 命令检查注册状态：

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
```
注册完成后，运行以下命令：

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>创建资源组

可以创建双堆栈虚拟网络之前，必须创建一个包含资源组[az 组创建](/cli/azure/group)。 下面的示例创建名为的资源组*myRGDualStack*中*eastus*位置：

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>创建 IPv4 和 IPv6 公共 IP 地址的负载均衡器
若要访问 Internet 上的将 IPv4 和 IPv6 终结点，需要负载均衡器 IPv4 和 IPv6 公共 IP 地址。 使用 [az network public-ip create](/cli/azure/network/public-ip) 创建公共 IP 地址。 下面的示例创建名为 IPv4 和 IPv6 IP 公共地址*dsPublicIP_v4*并*dsPublicIP_v6*中*myRGDualStack*资源组：

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>为 Vm 创建公共 IP 地址

若要远程访问 internet 上的 Vm，请为虚拟机都需要 IPv4 公共 IP 地址。 使用 [az network public-ip create](/cli/azure/network/public-ip) 创建公共 IP 地址。

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>创建基本负载均衡器

在本部分中，可以配置双前端 IP （IPv4 和 IPv6） 和负载均衡器的后端地址池，然后创建基本负载均衡器。

### <a name="create-load-balancer"></a>创建负载均衡器

创建与基本负载均衡器[创建 az 网络 lb](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest)名为**dsLB** ，其中包含名为的前端池**dsLbFrontEnd_v4**，名为后端池**dsLbBackEndPool_v4** IPv4 公共 IP 地址与该键相关联**dsPublicIP_v4**你在上一步中创建。 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>创建 IPv6 前端

创建具有 IPV6 前端 IP [az 网络 lb frontend-ip 创建](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create)。 下面的示例创建一个名为的前端 IP 配置*dsLbFrontEnd_v6*并将其附加*dsPublicIP_v6*地址：

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>配置 IPv6 后端地址池

创建 IPv6 后端地址池[az network lb address-pool 创建](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create)。 下面的示例创建名为后端地址池*dsLbBackEndPool_v6*以与 IPv6 NIC 配置中包括虚拟机：

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则用于定义将流量分配给 VM 的方式。 定义传入流量的前端 IP 配置和后端 IP 池以接收流量，同时定义所需的源端口和目标端口。 

使用 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) 创建负载均衡器规则。 下面的示例创建名为负载均衡器规则*dsLBrule_v4*并*dsLBrule_v6*并在平衡流量*TCP*端口*80*到IPv4 和 IPv6 前端 IP 配置：

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \ 
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>创建网络资源
在部署一些虚拟机之前，必须创建支持的网络资源的可用性集、 网络安全组、 虚拟网络和虚拟 Nic。 
### <a name="create-an-availability-set"></a>创建可用性集
若要提高应用的可用性，放置在可用性集中的 Vm。

使用 [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest) 创建可用性集。 下面的示例创建一个可用性集命名*dsAVset*:

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>创建网络安全组

创建用于将控制 VNET 中的入站和出站通信的规则的网络安全组。

#### <a name="create-a-network-security-group"></a>创建网络安全组

创建网络安全组与[az 网络 nsg 创建](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>创建入站和出站连接的网络安全组规则

创建网络安全组规则，以允许通过端口 3389，internet 连接的 RDP 连接，通过端口 80，并为使用出站连接[az 网络 nsg 规则创建](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)。

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) 创建虚拟网络。 下面的示例创建名为的虚拟网络*dsVNET*与子网*dsSubNET_v4*并*dsSubNET_v6*:

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>创建 NIC

为每个 VM 创建虚拟 Nic [az 网络 nic 创建](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)。 下面的示例为每个 VM 创建虚拟 NIC。 每个 NIC 具有两个 IP 配置 （1 IPv4 配置，1 IPv6 配置）。 创建具有 IPV6 配置[az 网络 nic ip 配置创建](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create)。
 
```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>创建虚拟机

使用 [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 创建 VM。 以下示例创建两台 VM 和所需的虚拟网络组件（如果它们尚不存在）。 

创建虚拟机*dsVM0* ，如下所示：

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest  
```
创建虚拟机*dsVM1* ，如下所示：

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 门户中查看 IPv6 双堆栈虚拟网络
您可以按如下所示在 Azure 门户中查看 IPv6 双堆栈的虚拟网络：
1. 在门户的搜索栏中，输入*dsVnet*。
2. 当“myVirtualNetwork”出现在搜索结果中时，将其选中。 这将启动**概述**的名为的双堆栈虚拟网络页*dsVnet*。 双堆栈虚拟网络使用 IPv4 和 IPv6 配置位于名为的双堆栈子网中显示两个 Nic *dsSubnet*。

  ![在 Azure 中的 IPv6 双堆栈虚拟网络](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> Azure 虚拟网络的 IPv6 现已推出的 Azure 门户中的此预览版本以只读的。


## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令将其删除。

```azurecli
 az group delete --name DsRG1
```

## <a name="next-steps"></a>后续步骤

在本文中，创建基本负载均衡器与双前端 IP 配置 （IPv4 和 IPv6）。 此外创建包含 Nic 具有到负载均衡器的后端池添加双重 IP 配置 （IPV4 + IPv6） 的两个虚拟机。 若要了解有关 Azure 虚拟网络中的 IPv6 支持的详细信息，请参阅[什么是 Azure 虚拟网络的 IPv6？](ipv6-overview.md)