---
title: 适用于 Azure Stack 中管理存储基础结构 |Microsoft Docs
description: 管理 Azure Stack 存储基础结构。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: ''
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 03/11/2019
ms.reviewer: jiahan
ms.openlocfilehash: 4a8287d7ca4da380ad7c2b1e039ab3058ca07a96
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760267"
---
# <a name="manage-storage-infrastructure-for-azure-stack"></a>适用于 Azure Stack 中管理存储基础结构

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本文介绍了运行状况以及 Azure Stack 存储基础结构资源的操作状态。 这些资源包括存储驱动器和卷。 当尝试解决各种问题，如驱动器不能添加到池时，本主题中的信息会很有价值。

## <a name="understand-drives-and-volumes"></a>了解驱动器和卷

### <a name="drives"></a>驱动器

Azure Stack 由 Windows Server 软件定义存储功能，使用存储空间直通 (S2D) 和 Windows Server 故障转移群集的组合来提供高性能、 可扩展、 有弹性存储服务。

Azure Stack 集成系统合作伙伴提供了许多解决方案变体，包括范围广泛的存储灵活性。 目前可以选择三种驱动器类型的组合：NVMe （非易失性内存速成版），SATA/SAS SSD （固态驱动器），HDD （硬盘驱动器）。

存储空间直通功能的缓存以提供最佳存储性能。 在 Azure Stack 设备，但一个或多个类型的驱动器，存储空间直通自动使用所有驱动器的"最快"(NVMe &gt; SSD &gt; HDD) 的缓存类型。 其余驱动器用于容量。 驱动器可以分组到部署中的"所有闪存"或"混合":

![Azure Stack 存储基础结构](media/azure-stack-storage-infrastructure-overview/image1.png)

所有闪存部署目的为获得最佳存储性能和不包括旋转硬盘驱动器 (HDD)。

![Azure Stack 存储基础结构](media/azure-stack-storage-infrastructure-overview/image2.png)

混合部署的目标是以平衡性能和容量，或若要最大化容量，但包括旋转硬盘驱动器 (HDD)。

根据为缓存的驱动器的类型自动确定缓存的行为。 当缓存固态驱动器 （例如 NVMe Ssd 的缓存），会缓存写操作。 这将减少穿戴设备上的容量驱动器，驱动器的容量减少累积的通信，并扩展其生存期。 在此期间，因为读取会严重影响 flash 的生命周期内，所以固态驱动器普遍提供读取的延迟不会缓存读取。 缓存的磁盘驱动器 （如 Ssd 缓存 Hdd 的)，这两个读取和写入缓存，以提供类似于 flash 的延迟时 (通常大约 10 倍更好) 两个。

![Azure Stack 存储基础结构](media/azure-stack-storage-infrastructure-overview/image3.png)

对于存储的可用配置，可以检查 Azure Stack OEM 合作伙伴 (https://azure.microsoft.com/overview/azure-stack/partners/)详细规范。

> [!Note]  
> Azure Stack 设备的信息，以提供具有同时 HDD 和 SSD （或 NVMe） 驱动器的混合部署。 但速度更快的类型的驱动器将用作缓存驱动器和所有剩余的驱动器将用作作为池容量驱动器。 容量的驱动器上放置租户数据 （blob、 表、 队列和磁盘）。 因此预配高级磁盘，或选择高级存储帐户类型并不意味着对象都保证在 SSD 上分配或 NVMe 驱动器并获得更高的性能。

### <a name="volumes"></a>卷

*存储服务*分区到单独分配用于保存系统和租户数据的卷的可用存储空间。 卷将组合中引入的容错能力、 可伸缩性和性能优势的存储空间直通存储池的驱动器。

![Azure Stack 存储基础结构](media/azure-stack-storage-infrastructure-overview/image4.png)

有三种类型的 Azure Stack 存储池上创建卷：

-   使用 Azure Stack 基础结构 Vm 和核心服务的基础结构： 主机文件。

-   VM Temp： 主机临时磁盘附加到租户虚拟机，该数据存储在这些磁盘。

-   维护 blob、 表、 队列和 VM 磁盘的对象存储： 主机租户数据。

在多节点部署中，您将看到三个基础结构卷，而 VM Temp 卷和对象存储卷的数量等于 Azure Stack 部署中的节点数：

-   在四节点部署中，有四个相等的 VM Temp 卷和四个相等的对象存储卷。

-   如果将新节点添加到群集，将有新的卷创建这两种类型。

-   卷数保持相同，即使节点出现故障或被删除。

-   如果使用 Azure Stack 开发工具包，则具有多个共享的单个卷。

存储空间直通中的卷提供复原能力以防止出现硬件问题，如驱动器或服务器故障，并启用整个服务器维护，例如软件更新的连续可用性。 Azure Stack 部署使用三向镜像以确保数据恢复能力。 租户数据的三个副本写入到不同的服务器，它们在缓存中保存到的位置：

![Azure Stack 存储基础结构](media/azure-stack-storage-infrastructure-overview/image5.png)

镜像的所有数据的多个副本保留，从而提供容错能力。 条带化和放置该数据的方式很重要 （请参阅此博客以了解详细信息），但它绝对为 true，则假设其无法全部展示，使用镜像存储任何数据写入多次。 每个副本写入到不同的物理硬件 （在不同的服务器不同的驱动器），假定独立失败。 三向镜像可以安全地容忍至少两个硬件问题 （驱动器或服务器） 一次。 例如，如果要重新启动一台服务器时突然另一个驱动器或服务器失败，所有数据保持安全且可连续访问。

## <a name="volume-states"></a>卷状态

若要找出什么状态 volumess 中，请使用以下 PowerShell 命令：

\$scaleunit\_name = (Get-AzsScaleUnit)\[0\].name

\$subsystem\_name = (Get-AzsStorageSubSystem -ScaleUnit \$scaleunit\_name)\[0\].name

Get AzsVolume ScaleUnit \$scaleunit\_名称的 StorageSubSystem\$子系统\_名称 |选择对象 VolumeLabel、 HealthStatus、 OperationalStatus、 RepairStatus、 说明、 操作、 TotalCapacityGB、 RemainingCapacityGB

下面是示例输出显示了一个已分离的卷和降级或不完整的卷：

| VolumeLabel | HealthStatus | OperationalStatus |
|-------------|--------------|------------------------|
| ObjStore_1 | Unknown | 分离 |
| ObjStore_2 | 警告 | {降级、 不完整} |

以下部分列出的运行状况和操作状态。

### <a name="volume-health-state-healthy"></a>卷运行状况状态：Healthy

| 运行状态 | 描述 |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OK | 卷处于正常状态。 |
| 非最优 | 不是均匀地在驱动器写入数据。<br> <br>**操作：** 请联系支持部门以优化存储池中的驱动器使用情况。 在执行操作，开始使用的指导，日志文件收集过程之前 https://aka.ms/azurestacklogfiles。 您可能需要从备份中还原失败的连接后还原。 |


### <a name="volume-health-state-warning"></a>卷运行状况状态：警告

当卷处于警告运行状况状态时，这意味着，数据的一个或多个副本都不可用，但 Azure Stack 仍可以读取的数据的至少一个副本。

| 运行状态 | 描述 |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 在服务中 | 添加或删除驱动器后，azure Stack 如修复卷。 修复完成后，该卷将返回到正常的运行状况状态。<br> <br>**操作：** 等待 Azure Stack，以完成修复卷，并稍后检查状态。 |
| 不完整 | 卷的恢复能力被减少，因为一个或多个驱动器失败或缺少。 但是，缺少驱动器包含你的数据的最新副本。<br> <br>**操作：** 重新连接任何缺少的驱动器，替换任何失败的驱动器，并使联机处于脱机状态的任何服务器。 |
| 已降级 | 因为一个或多个驱动器失败或缺失，并且有过时的这些驱动器上的数据副本，减少卷的恢复能力。<br> <br>**操作：** 重新连接任何缺少的驱动器，替换任何失败的驱动器，并使联机处于脱机状态的任何服务器。 |

 

### <a name="volume-health-state-unhealthy"></a>卷运行状况状态：不正常

当卷处于不正常的运行状况状态时，是当前无法访问部分或全部卷上的数据。

| 运行状态 | 描述 |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 未冗余 | 该卷已丢失数据，因为过多的驱动器失败。<br> <br>**操作：** 请联系支持部门。 在执行操作，开始使用的指导，日志文件收集过程之前 https://aka.ms/azurestacklogfiles。 |


### <a name="volume-health-state-unknown"></a>卷运行状况状态：Unknown

如果已成为分离虚拟磁盘时，卷也可以是中的未知运行状况状态。

| 运行状态 | 描述 |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 分离 | 存储设备失败，出现了可能会导致无法访问的卷。 某些数据可能会丢失。<br> <br>**操作：** <br>1.检查物理和网络的所有存储设备的连接。<br>2.如果所有设备已正确都连接，请联系支持部门。 在执行操作，开始使用的指导，日志文件收集过程之前 https://aka.ms/azurestacklogfiles。 您可能需要从备份中还原失败的连接后还原。 |

## <a name="drive-states"></a>驱动器状态

使用以下 PowerShell 命令来监视驱动器的状态：

 

\$scaleunit\_name = (Get-AzsScaleUnit)\[0\].name

\$subsystem\_name = (Get-AzsStorageSubSystem -ScaleUnit \$scaleunit\_name)\[0\].name

SerialNumber

 

Get AzsDrive ScaleUnit \$scaleunit\_名称的 StorageSubSystem\$子系统\_名称 |选择对象 StorageNode、 PhysicalLocation、 HealthStatus、 OperationalStatus、 说明、 操作、 使用情况、 CanPool、 CannotPoolReason、 SerialNumber、 模型、 媒体类型、 CapacityGB

以下部分介绍可以是驱动器的运行状况状态。

### <a name="drive-health-state-healthy"></a>驱动器运行状况状态：Healthy

| 运行状态 | 描述 |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| OK | 卷处于正常状态。 |
| 在服务中 | 驱动器正在执行某些内部管理操作。 操作完成后，驱动器应返回到正常的运行状况状态。 |

### <a name="drive-health-state-healthy"></a>驱动器运行状况状态：Healthy

警告状态可以中的驱动器读取和写入数据已成功但出现问题。

| 运行状态 | 描述 |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 通信中断 | 连接已丢失的驱动器。<br> <br>**操作：** 将所有服务器重新联机。 如果仍未解决它，重新连接该驱动器。 如果发生这使情况，将为要确保完全复原能力的驱动器。 |
| 预计故障 | 驱动器的故障预测很快就会发生。<br> <br>**操作：** 将尽快以确保完全复原能力的驱动器。 |
| IO 错误 | 出现临时错误访问驱动器。<br> <br>**操作：** 如果发生这使情况，将为要确保完全复原能力的驱动器。 |
| 暂时性错误 | 出现临时驱动器错误。 这通常意味着在驱动器没有响应，但它还可能表示的存储空间直通不恰当地从驱动器中删除保护性的分区。 <br> <br>**操作：** 如果发生这使情况，将为要确保完全复原能力的驱动器。 |
| 异常等待时间 | 驱动器是有时无响应，显示失败的迹象。<br> <br>**操作：** 如果发生这使情况，将为要确保完全复原能力的驱动器。 |
| 从池中删除 | Azure Stack 是从存储池删除驱动器的过程中。<br> <br>**操作：** 等待 Azure Stack，若要删除该驱动器，并稍后检查状态。<br>如果状态保持，请联系支持部门。 在执行操作，开始使用的指导，日志文件收集过程之前 https://aka.ms/azurestacklogfiles。 |
|  |  |
| 启动维护模式 | Azure Stack 是过程中将该驱动器放入维护模式。 这是临时状态-驱动器应该很快就是在维护模式状态中。<br> <br>**操作：** 等待 Azure Stack，以完成该过程中，并稍后检查状态。 |
| 处于维护模式 | 驱动器处于维护模式，正在停止读取和写入从驱动器。 这通常意味着之类 PNU 或 FRU 操作系统驱动器的 Azure Stack 管理任务。 但是，管理员还可以将驱动器置于维护模式。<br> <br>**操作：** 等待 Azure Stack，以完成管理任务，并稍后检查状态。<br>如果状态保持，请联系支持部门。 在执行操作，开始使用的指导，日志文件收集过程之前 https://aka.ms/azurestacklogfiles。 |
|  |  |
| 停止维护模式 | Azure Stack 是过程中将恢复联机状态的驱动器。 这是临时状态-驱动器应该很快就可以在另一种状态的理想情况下正常运行。<br> <br>**操作：** 等待 Azure Stack，以完成该过程中，并稍后检查状态。 |

 

### <a name="drive-health-state-unhealthy"></a>驱动器运行状况状态：不正常

当前处于不正常状态的驱动器不能写入或通过访问。

| 运行状态 | 描述 |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 拆分 | 已从池中与驱动器分离。<br> <br>**操作：** 替换新的磁盘驱动器。 如果必须使用此磁盘，从系统中删除磁盘，确保磁盘上没有任何有用数据、 擦除该磁盘，并重新安装磁盘。 |
| 不能使用 | 物理磁盘被隔离，因为它不支持你解决方案的供应商。 支持的解决方案已批准状态和有正确的磁盘固件的磁盘。<br> <br>**操作：** 替换具有已批准的制造商和型号的解决方案的磁盘驱动器。 |
| 元数据已过时 | 更换磁盘之前使用，并且包含未知的存储系统中的数据。 该磁盘将被隔离。        <br> <br>**操作：** 替换新的磁盘驱动器。 如果必须使用此磁盘，从系统中删除磁盘，确保磁盘上没有任何有用数据、 擦除该磁盘，并重新安装磁盘。 |
| 无法识别的元数据 | 这通常意味着的驱动器上它的不同池从具有元数据的驱动器上找到无法识别的元数据。<br> <br>**操作：** 替换新的磁盘驱动器。 如果必须使用此磁盘，从系统中删除磁盘，确保磁盘上没有任何有用数据、 擦除该磁盘，并重新安装磁盘。 |
| 介质故障 | 驱动器失败，并且不会再使用存储空间。<br> <br>**操作：** 将尽快以确保完全复原能力的驱动器。 |
| 设备硬件故障 | 在此驱动器上出现硬件故障。 <br> <br>**操作：** 将尽快以确保完全复原能力的驱动器。 |
| 更新固件 | Azure Stack 更新的驱动器上的固件。 这只是临时状态的持续时间通常少于一分钟，并在此期间时间在池中其他驱动器处理所有读取和写入。<br> <br>**操作：** 等待 Azure Stack，以完成更新，并稍后检查状态。 |
| 正在启动 | 驱动器前的准备操作。 这应该是临时状态-完成后，驱动器应转换为不同的操作状态。<br> <br>**操作：** 等待 Azure Stack，以完成此操作，并稍后检查状态。 |
 

## <a name="reasons-a-drive-cant-be-pooled"></a>不能共用一个驱动器的原因

某些驱动器只是未准备好要在 Azure Stack 存储池中。 您可以找出为什么驱动器不是有资格参与池通过查看驱动器的 CannotPoolReason 属性。 下表提供有关每个原因一些更多详细信息。

| 原因 | 描述 |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 硬件不符合 | 驱动器不在列表中的指定通过使用运行状况服务的已批准的存储模型。<br> <br>**操作：** 替换新的磁盘驱动器。 |
| 固件不符合 | 物理驱动器上的固件不在已批准的固件程序版本的列表中使用运行状况服务。<br> <br>**操作：** 替换新的磁盘驱动器。 |
| 在使用群集 | 故障转移群集当前使用的驱动器。<br> <br>**操作：** 替换新的磁盘驱动器。 |
| 可移动媒体 | 驱动器归类为可移动驱动器。 <br> <br>**操作：** 替换新的磁盘驱动器。 |
| 不正常 | 驱动器未处于正常状态，并且可能需要更换。<br> <br>**操作：** 替换新的磁盘驱动器。 |
| 产能不足 | 有分区占用的驱动器上的可用空间。<br> <br>**操作：** 替换新的磁盘驱动器。 如果必须使用此磁盘，从系统中删除磁盘，确保磁盘上没有任何有用数据、 擦除该磁盘，并重新安装磁盘。 |
| 正在进行验证 | 运行状况服务正在检查以查看使用已批准的驱动器上的固件。<br> <br>**操作：** 等待 Azure Stack，以完成该过程中，并稍后检查状态。 |
| 验证失败 | 运行状况服务无法查看使用已批准的驱动器上的固件。<br> <br>**操作：** 请联系支持部门。 在执行操作，开始使用的指导，日志文件收集过程之前 https://aka.ms/azurestacklogfiles。 |
| 脱机 | 驱动器处于脱机状态。 <br> <br>**操作：** 请联系支持部门。 在执行操作，开始使用的指导，日志文件收集过程之前 https://aka.ms/azurestacklogfiles。 |

## <a name="next-step"></a>后续步骤

[管理存储容量](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-storage-shares) 