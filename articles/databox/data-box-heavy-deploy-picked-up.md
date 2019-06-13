---
title: Azure Data Box Heavy 寄回教程 | Microsoft Docs
description: 了解如何将 Azure Data Box Heavy 寄送到 Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: 31185900ede0fae74a04f98eaecee7379fb1c4fe
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427809"
---
# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure-preview"></a>教程：退回 Azure Data Box Heavy 并验证上传到 Azure（预览版）的数据


本教程介绍如何退回 Azure Data Box Heavy 和验证上传到 Azure 的数据。

在本教程中，你将了解如下主题：

> [!div class="checklist"]
> * 先决条件
> * 准备交付
> * 将 Data Box Heavy 寄送到 Microsoft
> * 验证 Azure 中的数据上传
> * 从 Data Box Heavy 中擦除数据

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

- 已完成[教程：将数据复制到 Azure Data Box 并进行验证](data-box-heavy-deploy-copy-data.md)。
- 复制作业已完成。 如果复制作业正在进行，则无法运行“准备交付”。

## <a name="prepare-to-ship"></a>准备交付

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

## <a name="ship-data-box-heavy-back"></a>寄回 Data Box Heavy

1. 确保设备已关闭电源且已拔下所有电缆。 将 4 根电源线卷在线轴上安全地放入可从设备背面访问的托盘中。
2. 如果该设备是在美国或欧洲发运，则货运公司是联邦快递 (FedEx)。

    1. 拨打货运公司的本地号码以安排取件。
    2. 联系 [Data Box Operations](mailto:DataBoxOps@microsoft.com)，通知收件相关事宜并获取退回发货标签。
    3. 确保发货标签显示在设备前面的清晰屏幕上。
    4. 确保已从设备中删除上次发货的旧发货标签。
3. 承运人提取 Data Box Heavy 并进行扫描后，门户中的订单状态将更新为“已提货”。  此外还会显示一个跟踪 ID。

## <a name="verify-data-upload-to-azure"></a>验证 Azure 中的数据上传

当 Microsoft 收到并扫描该设备时，订单状态将更新为“已接收”。  然后，该设备将经受物理验证，以确定是否存在损坏或篡改迹象。

验证完成后，Data Box Heavy 将连接到 Azure 数据中心的网络。 数据复制将自动开始。 根据数据大小，复制操作可能需要几小时到几天的时间才能完成。 可以在门户中监视复制作业的进度。

复制完成后，订单状态将更新为“已完成”。 

从源中删除数据之前，请确认数据已上传到 Azure。 你的数据可位于：

- Azure 存储帐户。 将数据复制到 Data Box 时，会根据类型将数据将上传到 Azure 存储帐户中的以下路径之一。

  - 对于块 blob 和页 blob：`https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - 对于 Azure 文件：`https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    或者，可以转到 Azure 门户中的 Azure 存储帐户并从那里导航。

- 托管磁盘资源组。 创建托管磁盘时，VHD 作为页 blob 进行上传，然后转换为托管磁盘。 托管磁盘会附加到在创建排序时指定的资源组上。 

    - 如果在 Azure 中成功复制到托管磁盘，则可转到 Azure 门户中的“订单详细信息”，记下为托管磁盘指定的资源组  。

        ![标识托管磁盘资源组](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        转到所记录的资源组，并找到你的托管磁盘。

        ![附加到资源组的托管磁盘](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - 如果复制了 VHDX 或动态/差异 VHD，则 VHDX/VHD 会作为页 blob 上传到暂存存储帐户，但 VHD 转换到托管磁盘将失败。 请转到临时“存储帐户”>“Blob”，然后选择相应的容器 - 标准 SSD、标准 HDD 或高级 SSD  。 VHD 作为页 blob 上传到暂存存储帐户。

## <a name="erasure-of-data-from-data-box-heavy"></a>从 Data Box Heavy 中擦除数据
 
上传到 Azure 完成后，Data Box 将根据 [NIST SP 800-88 修订版 1 准则](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)擦除其磁盘上的数据。 擦除完成后，可以[下载订单历史记录](data-box-portal-admin.md#download-order-history)。

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
> * 先决条件
> * 准备交付
> * 将 Data Box Heavy 寄送到 Microsoft
> * 验证 Azure 中的数据上传
> * 从 Data Box Heavy 中擦除数据

请转到以下文章，了解如何通过本地 Web UI 管理 Data Box Heavy。

> [!div class="nextstepaction"]
> [使用本地 Web UI 管理 Azure Data Box](./data-box-local-web-ui-admin.md)

