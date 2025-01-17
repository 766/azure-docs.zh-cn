---
title: 创建和上传 Oracle Linux VHD | Microsoft Docs
description: 了解如何创建和上传包含 Oracle Linux 操作系统的 Azure 虚拟硬盘 (VHD)。
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 0d83a6f9b42a56799371c5cdf82422ab73b8859a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671091"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>为 Azure 准备 Oracle Linux 虚拟机
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>先决条件
本文假定你已在虚拟硬盘中安装了 Oracle Linux 操作系统。 存在多个用于创建 .vhd 文件的工具，例如 Hyper-V 等虚拟化解决方案。 有关说明，请参阅[安装 Hyper-V 角色和配置虚拟机](https://technet.microsoft.com/library/hh846766.aspx)。

### <a name="oracle-linux-installation-notes"></a>Oracle Linux 安装说明
* 另请参阅[常规 Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)，获取更多有关如何为 Azure 准备 Linux 的提示。
* Hyper-V 和 Azure 同时支持 Oracle 的 Red Hat 兼容内核及其 UEK3（坚不可摧企业内核）。 为了获得最佳结果，请务必在准备 Oracle Linux VHD 时更新到最新内核。
* Hyper-V 和 Azure 不支持 Oracle 的 UEK2，因为它不包括所需的驱动程序。
* Azure 不支持 VHDX 格式，仅支持**固定大小的 VHD**。  可使用 Hyper-V 管理器或 convert-vhd cmdlet 将磁盘转换为 VHD 格式。
* 在安装 Linux 系统时，建议使用标准分区而不是 LVM（通常是许多安装的默认值）。 这会避免 LVM 与克隆 VM 发生名称冲突，特别是在 OS 磁盘需要连接到另一台 VM 以进行故障排除的情况下。 如果需要，可以在数据磁盘上使用 [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 由于低于 2.6.37 的 Linux 内核版本中的 bug，更大的 VM 不支持 NUMA。 此问题主要影响使用上游 Red Hat 2.6.32 内核的分发。 手动安装的 Azure Linux 代理 (waagent) 将自动在 Linux 内核的 GRUB 配置中禁用 NUMA。 可以在下面的步骤中找到有关此内容的详细信息。
* 不要在操作系统磁盘上配置交换分区。 可以配置 Linux 代理，以在临时资源磁盘上创建交换文件。  可以在下面的步骤中找到有关此内容的详细信息。
* Azure 上的所有 VHD 必须已将虚拟大小调整为 1MB。 从原始磁盘转换为 VHD 时，必须确保在转换前原始磁盘大小是 1MB 的倍数。 有关详细信息，请参阅 [Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)。
* 请确保已启用 `Addons` 存储库。 编辑文件 `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) 或 `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7)，并在此文件中 **[ol6_addons]** 或 **[ol7_addons]** 下将行 `enabled=0` 更改为 `enabled=1`。

## <a name="oracle-linux-64"></a>Oracle Linux 6.4+
你必须在操作系统中完成特定的配置步骤才能使虚拟机在 Azure 中运行。

1. 在 Hyper-V 管理器的中间窗格中，选择虚拟机。
2. 单击“连接”打开虚拟机窗口。 
3. 通过运行以下命令卸载 NetworkManager：
   
        # sudo rpm -e --nodeps NetworkManager
   
    **注意：** 如果未安装此包，则该命令会失败，并显示一条错误消息。 这是正常情况。
4. 在包含以下文本的 `/etc/sysconfig/` 目录中创建一个名为 **network** 的文件：
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. 在包含以下文本的 `/etc/sysconfig/network-scripts/` 目录中创建一个名为 **ifcfg-eth0** 的文件：
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. 修改 udev 规则，以避免产生以太网接口的静态规则。 在 Microsoft Azure 或 Hyper-V 中克隆虚拟机时，这些规则可能会引发问题：
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. 通过运行以下命令，确保网络服务会在引导时启动：
   
        # chkconfig network on
8. 通过运行以下命令安装 python-pyasn1：
   
        # sudo yum install python-pyasn1
9. 在 grub 配置中修改内核引导行，以使其包含 Azure 的其他内核参数。 为此，请在文本编辑器中打开“/boot/grub/menu.lst”，并确保默认内核包含以下参数：
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   这还将确保所有控制台消息都发送到第一个串行端口，从而可以协助 Azure 支持人员调试问题。 由于 Oracle 的 Red Hat 兼容内核中的一个 bug，这将禁用 NUMA。
   
   除此之外，建议*删除*以下参数：
   
        rhgb quiet crashkernel=auto
   
   图形界面式引导和安静引导在云环境中不适用，在云环境中，我们希望所有日志都发送到串行端口。
   
   根据需要可以配置 `crashkernel` 选项，但请注意此参数会使虚拟机中的可用内存量减少 128MB 或更多，这在较小的虚拟机上可能会出现问题。
10. 请确保已安装 SSH 服务器且已将其配置为在引导时启动。  这通常是默认设置。
11. 通过运行以下命令来安装 Azure Linux 代理。 最新版本为 2.0.15。
    
        # sudo yum install WALinuxAgent
    
    请注意，如果没有如步骤 2 中所述删除 NetworkManager 包和 NetworkManager-gnome 包，则安装 WALinuxAgent 包将删除它们。
12. 不要在 OS 磁盘上创建交换空间。
    
    Azure Linux 代理可使用在 Azure 上设置后附加到虚拟机的本地资源磁盘自动配置交换空间。 请注意，本地资源磁盘是*临时*磁盘，并可能在取消预配 VM 时被清空。 在安装 Azure Linux 代理（请参见前一步骤）后，相应地在 /etc/waagent.conf 中修改以下参数：
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. 运行以下命令可取消对虚拟机的设置并且对其进行准备以便在 Azure 上进行设置：
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. 在 Hyper-V 管理器中单击“操作”->“关闭”  。 Linux VHD 现已准备好上传到 Azure。

---
## <a name="oracle-linux-70"></a>Oracle Linux 7.0+
**Oracle Linux 7 中的更改**

为 Azure 准备 Oracle Linux 7 虚拟机非常类似于 Oracle Linux 6，但有几个值得注意的重要区别：

* 在 Azure 中同时支持 Red Hat 兼容内核和 Oracle 的 UEK3。  建议使用 UEK3 内核。
* NetworkManager 包不再与 Azure Linux 代理冲突。 默认情况下将安装此包，建议不要删除它。
* GRUB2 现在用作默认引导加载程序，因此编辑内核参数的过程已更改（见下文）。
* XFS 现在是默认文件系统。 如果需要，仍可以使用 ext4 文件系统。

**配置步骤**

1. 在 Hyper-V 管理器中，选择虚拟机。
2. 单击“连接”打开该虚拟机的控制台窗口。 
3. 在包含以下文本的 `/etc/sysconfig/` 目录中创建一个名为 **network** 的文件：
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. 在包含以下文本的 `/etc/sysconfig/network-scripts/` 目录中创建一个名为 **ifcfg-eth0** 的文件：
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. 修改 udev 规则，以避免产生以太网接口的静态规则。 在 Microsoft Azure 或 Hyper-V 中克隆虚拟机时，这些规则可能会引发问题：
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. 通过运行以下命令，确保网络服务会在引导时启动：
   
        # sudo chkconfig network on
7. 通过运行以下命令安装 python-pyasn1 包：
   
        # sudo yum install python-pyasn1
8. 运行以下命令以清除当前 yum 元数据并安装所有更新：
   
        # sudo yum clean all
        # sudo yum -y update
9. 在 grub 配置中修改内核引导行，以使其包含 Azure 的其他内核参数。 为此，请在文本编辑器中打开“/etc/default/grub”并编辑 `GRUB_CMDLINE_LINUX` 参数，例如：
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   这还将确保所有控制台消息都发送到第一个串行端口，从而可以协助 Azure 支持人员调试问题。 此外，还会关闭 NIC 的新 OEL 7 命名约定。 除此之外，建议*删除*以下参数：
   
       rhgb quiet crashkernel=auto
   
   图形界面式引导和安静引导在云环境中不适用，在云环境中，我们希望所有日志都发送到串行端口。
   
   根据需要可以配置 `crashkernel` 选项，但请注意此参数会使虚拟机中的可用内存量减少 128MB 或更多，这在较小的虚拟机上可能会出现问题。
10. 完成后，请按照上面所示编辑“/etc/default/grub”，运行以下命令以重新生成 grub 配置：
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. 请确保已安装 SSH 服务器且已将其配置为在引导时启动。  这通常是默认设置。
12. 通过运行以下命令来安装 Azure Linux 代理：
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. 不要在 OS 磁盘上创建交换空间。
    
    Azure Linux 代理可使用在 Azure 上设置后附加到虚拟机的本地资源磁盘自动配置交换空间。 请注意，本地资源磁盘是*临时*磁盘，并可能在取消预配 VM 时被清空。 在安装 Azure Linux 代理（请参见前一步骤）后，相应地在 /etc/waagent.conf 中修改以下参数：
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. 运行以下命令可取消对虚拟机的设置并且对其进行准备以便在 Azure 上进行设置：
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. 在 Hyper-V 管理器中单击“操作”->“关闭”  。 Linux VHD 现已准备好上传到 Azure。

## <a name="next-steps"></a>后续步骤
现在，已准备就绪，可以使用 Oracle Linux .vhd 在 Azure 中创建新的虚拟机了。 如果是首次将 .vhd 文件上传到 Azure，请参阅[从自定义磁盘创建 Linux VM](upload-vhd.md#option-1-upload-a-vhd)。

