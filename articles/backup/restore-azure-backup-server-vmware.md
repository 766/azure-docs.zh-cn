---
title: 通过 Azure 备份服务器还原 VMware Vm
description: 使用 Azure 备份服务器 (MABS) 还原 VMware vCenter/ESXi 服务器上运行的 VMware Vm。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: dacurwin
ms.openlocfilehash: fd851822a7068928a2f332c240bc33b70b1e16f6
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642300"
---
# <a name="restore-vmware-virtual-machines"></a>还原 VMware 虚拟机

本文介绍如何使用 Microsoft Azure 备份 Server (MABS) 还原 VMware VM 恢复点。 有关使用 MABS 恢复数据的概述, 请参阅[恢复受保护的数据](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server)。 在 MABS 管理员控制台中, 有两种方法可用于查找可恢复的数据-搜索或浏览。 在恢复数据时, 你可能会或不希望将数据或 VM 还原到相同的位置。 出于此原因, MABS 支持用于 VMware VM 备份的三个恢复选项:

•**原始位置恢复 (OLR)** -使用 OLR 将受保护的 VM 还原到其原始位置。 仅当已添加或删除了备份后, 才可以将 VM 还原到其原始位置。 如果已添加或删除磁盘, 则必须使用备用位置恢复。

•**备用位置恢复 (ALR)** -缺少原始 vm 或不想要扰乱原始 vm 时, 请将 VM 恢复到备用位置。 若要将 VM 恢复到备用位置, 必须提供 ESXi 主机、资源池、文件夹以及存储数据存储和路径的位置。 为了帮助将还原的 VM 与原始 VM 区分开来, MABS 将 "-已恢复" 追加到 VM 的名称。

•**单个文件位置恢复 (ILR)** -如果受保护的 Vm 是 WINDOWS Server VM, 则可以使用 MABS 的 ILR 功能恢复 VM 内的各个文件/文件夹。 若要恢复单个文件, 请参阅本文后面的过程。


## <a name="restore-a-recovery-point"></a>还原恢复点

1.  在 MABS 管理员控制台中, 单击 "恢复视图"。

2.  使用 "浏览" 窗格, 浏览或筛选以查找要恢复的 VM。 选择 VM 或文件夹后, 窗格的 "恢复点" 将显示可用的恢复点。 

    ![可用恢复点](./media/restore-azure-backup-server-vmware/recovery-points.png)

3.  在 "**恢复点**" 字段中, 使用 "日历" 和下拉菜单来选择执行恢复点的日期。 以粗体显示的日历日期具有可用的恢复点。

4.  在工具功能区上, 单击 "**恢复**" 以打开 "**恢复向导**"。 

    ![恢复向导, 复查恢复选择](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5.  单击 "**下一步**" 转到 "**指定恢复选项**" 屏幕。

6.  如果要启用网络带宽限制, 请在 "**指定恢复选项**" 屏幕上, 单击 "**修改**"。 若要禁用网络限制, 请单击 "**下一步**"。 对于 VMware Vm, 此向导屏幕上没有其他选项可用。 如果选择修改网络带宽限制, 请在 "限制" 对话框中, 选择 "**启用网络带宽使用限制**" 以将其打开。 启用后, 配置 "**设置**" 和 "**工作计划**"。

7.  在 "**选择恢复类型**" 屏幕上, 选择是要恢复到原始实例还是新位置, 然后单击 "**下一步**"。

     * 如果选择 "**恢复到原始实例**", 则不需要在向导中进行任何其他选择。 使用原始实例的数据。

    * 如果选择 "**在任何主机上作为虚拟机恢复**", 则在 "**指定目标**" 屏幕上, 提供**ESXi 主机、资源池、文件夹**和**路径**的信息。 

      ![选择恢复类型](./media/restore-azure-backup-server-vmware/recovery-type.png)

8.    在 "**摘要**" 屏幕上, 查看设置, 然后单击 "**恢复**" 以启动恢复过程。 "**恢复状态**" 屏幕显示恢复操作的进度。

## <a name="restore-an-individual-file-from-a-vm"></a>从 VM 还原单个文件

你可以从受保护的 VM 恢复点还原单个文件。 此功能仅适用于 Windows Server Vm。 还原单个文件与还原整个 VM 类似, 只是在启动恢复过程之前, 浏览到 VMDK 并找到所需的文件。 若要从 Windows Server VM 恢复单个文件或选择文件:

1.  在 MABS 管理员控制台中, 单击 "**恢复**视图"。

2.  使用 "**浏览**" 窗格, 浏览或筛选以查找要恢复的 VM。 选择 VM 或文件夹后, 窗格的 "恢复点" 将显示可用的恢复点。

    ![可用恢复点](./media/restore-azure-backup-server-vmware/recovery-points.png)
 
3.  在 "**恢复点:** " 窗格中, 使用日历选择包含所需恢复点的日期。 根据备份策略的配置方式, 日期可以有多个恢复点。 选择恢复点的拍摄日期后, 请确保选择了正确的**恢复时间**。 如果所选日期有多个恢复点, 请在 "恢复时间" 下拉菜单中选择您的恢复点。 选择恢复点后, 可恢复项目的列表将显示在 "**路径:** " 窗格中。

4.  若要查找要恢复的文件, 请在 "**路径**" 窗格中, 双击 "**可恢复项目**" 列中的项以将其打开。 选择要恢复的文件或文件夹。 若要选择多个项, 请在选择每个项时按**Ctrl**键。 使用 "**路径**" 窗格搜索出现在 "**可恢复项目**" 列中的文件或文件夹的列表。 **下面的搜索列表**不搜索子文件夹。 若要在子文件夹中进行搜索, 请双击该文件夹。 使用 "**上**移" 按钮可从子文件夹移动到父文件夹。 您可以选择多个项目 (文件和文件夹), 但是它们必须位于同一个父文件夹中。 不能从同一恢复作业的多个文件夹中恢复项。

5.  选择要恢复的项目后, 请在管理员控制台工具功能区中单击 "**恢复**" 以打开 "**恢复向导**"。 在恢复向导中, "**复查恢复选择**" 屏幕会显示要恢复的选定项目。 
    ![复查恢复选择](./media/restore-azure-backup-server-vmware/review-recovery.png)

6.  如果要启用网络带宽限制, 请在 "**指定恢复选项**" 屏幕上, 单击 "**修改**"。 若要禁用网络限制, 请单击 "**下一步**"。 对于 VMware Vm, 此向导屏幕上没有其他选项可用。 如果选择修改网络带宽限制, 请在 "限制" 对话框中, 选择 "**启用网络带宽使用限制**" 以将其打开。 启用后, 配置 "**设置**" 和 "**工作计划**"。
7.  在 "**选择恢复类型**" 屏幕上, 单击 "**下一步**"。 只能将文件或文件夹恢复到网络文件夹中。
8.  在 "**指定目标**" 屏幕上, 单击 "**浏览**" 查找文件或文件夹的网络位置。 MABS 创建一个文件夹, 将在其中复制所有恢复的项。 文件夹名称的前缀为 MABS_day。 选择恢复的文件或文件夹的位置时, 将提供该位置的详细信息 (目标、目标路径和可用空间)。 

       ![指定恢复文件的位置](./media/restore-azure-backup-server-vmware/specify-destination.png)

9.  在 "**指定恢复选项**" 屏幕上, 选择要应用的安全设置。 你可以选择修改网络带宽使用限制, 但默认情况下禁用限制。 此外, 不会启用**SAN 恢复**和**通知**。
10. 在 "**摘要**" 屏幕上, 查看设置, 然后单击 "**恢复**" 以启动恢复过程。 "**恢复状态**" 屏幕显示恢复操作的进度。


## <a name="next-steps"></a>后续步骤
有关使用 Azure 备份服务器时的疑难解答问题, 请查看[Azure 备份服务器故障排除指南](./backup-azure-mabs-troubleshoot.md)。

