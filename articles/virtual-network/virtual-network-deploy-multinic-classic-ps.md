---
title: 创建具有多个 NIC 的 VM（经典） - Azure PowerShell | Microsoft 文档
description: 了解如何使用 PowerShell 创建具有多个 NIC 的 VM（经典）。
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 087b52bd603e8aed6078ab340e84c1f6bd0e8082
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60748484"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>使用 PowerShell 创建具有多个 NIC 的 VM（经典）

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

可以在 Azure 中创建虚拟机 (VM)，并将多个网络接口 (NIC) 附加到每个 VM。 通过多个 NIC 可分离跨 NIC 的流量类型。 例如，一个 NIC 可与 Internet 通信，而另一个 NIC 仅与未连接到 Internet 的内部资源通信。 许多网络虚拟设备（例如应用程序交付和 WAN 优化解决方案）都需要具备跨多个 NIC 分离网络流量的能力。

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同的部署模型：[资源管理器部署模型和经典部署模型](../resource-manager-deployment-model.md)。 本文介绍使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。 了解如何使用 [Resource Manager 部署模型](../virtual-machines/windows/multiple-nics.md)执行这些步骤。

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

以下步骤使用名为 *IaaSStory* 的资源组作为主资源组，并在名为 *IaaSStory-BackEnd* 的资源组中实现后端服务器。

## <a name="prerequisites"></a>必备组件

创建数据库服务器之前，需要先使用此方案的所有必需资源创建 *IaaSStory* 资源组。 若要创建这些资源，请完成如下步骤。 若要创建虚拟网络，请完成[创建虚拟网络](virtual-networks-create-vnet-classic-netcfg-ps.md)一文中的步骤。

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>创建后端 VM
后端 VM 取决于以下资源的创建：

* **后端子网**。 各数据库服务器将分别属于各个子网，以便隔离流量。 以下脚本期望此子网存在于名为 *WTestVnet* 的虚拟网络中。
* **数据磁盘的存储帐户**。 为了提高性能，数据库服务器上的数据磁盘使用固态驱动器 (SSD) 技术，这需要高级存储帐户。 请确保部署到的 Azure 位置支持高级存储。
* **可用性集**。 所有数据库服务器都将添加到单个可用性集，以确保在维护期间至少有一个 VM 已启动且正在运行。

### <a name="step-1---start-your-script"></a>步骤 1 - 启动脚本
可以在[此处](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1)下载所用的完整 PowerShell 脚本。 请按以下步骤更改要在环境中使用的脚本。

1. 基于在上面[先决条件](#prerequisites)中部署的现有资源组更改以下变量的值。

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. 基于要用于后端部署的值更改以下变量的值。

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>步骤 2 - 为 VM 创建必要的资源
需要为所有 VM 的数据磁盘创建新的云服务和存储帐户。 还需要为 VM 指定映像和本地管理员帐户。 若要创建这些资源，请完成以下步骤：

1. 创建新的云服务。

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. 创建新的高级存储帐户。

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. 将上面创建的存储帐户设置为订阅的当前存储帐户。

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. 为 VM 选择映像。

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. 设置本地管理员帐户凭据。

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>步骤 3 - 创建 VM
需要使用循环创建所需数量的 VM，并在循环中创建所需的 NIC 和 VM。 若要创建 NIC 和 VM，请执行以下步骤。

1. 启动 `for` 循环以基于 `$numberOfVMs` 变量的值重复执行命令以根据需要创建任意多次一个 VM 和两个 NIC。

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. 创建 `VMConfig` 对象，它为 VM 指定映像、大小和可用性集。

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. 将 VM 预配为 Windows VM。

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. 设置默认 NIC，并为其分配静态 IP 地址。

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. 为每个 VM 添加第二个 NIC。

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. 为每个 VM 创建两个数据磁盘。

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. 创建每个 VM，并结束循环。

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>步骤 4 - 运行脚本
既已根据需要下载并更改脚本，请运行该脚本以创建具有多个 NIC 的后端数据库 VM。

1. 保存脚本并从 **PowerShell** 命令提示符或 **PowerShell ISE** 运行它。 将看到最初的输出，如下所示。

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. 在凭据提示中填写所需信息，并单击“**确定**”。 返回以下输出。

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>步骤 5 - 在 VM 的操作系统中配置路由

Azure DHCP 会将默认网关分配给附加到虚拟机的第一个（主）网络接口。 Azure 不会将默认网关分配给附加到虚拟机的其他（辅助）网络接口。 因此，默认情况下无法与辅助网络接口所在子网的外部资源进行通信。 但是，辅助网络接口可以与子网之外的资源进行通信。 要配置二级网络接口的路由，请参阅以下文章：

- [为多个 NIC 配置 Windows VM](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [为多个 NIC 配置 Linux VM](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)
