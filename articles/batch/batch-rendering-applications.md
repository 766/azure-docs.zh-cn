---
title: 渲染应用程序 - Azure Batch
description: 预安装的 Batch 渲染应用程序
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 07/29/2019
ms.topic: conceptual
ms.openlocfilehash: 133742bf45bddf866d2dfcae97a331ee2a0f84fc
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639376"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>在渲染 VM 映像上预安装的应用程序

可以将任何渲染应用程序与 Azure Batch 配合使用。 不过，常见的预安装应用程序都提供了 Azure 市场 VM 映像。

在适用的情况下，可以为预安装的应用程序使用按使用付费许可。 创建 Batch 池时，可以指定必需的应用程序，将按分钟对 VM 和应用程序计费。 [Azure Batch 定价页面](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)上列出了应用程序价格。

某些应用程序仅支持 Windows，但大多数应用程序在 Windows 和 Linux 上都受支持。

## <a name="applications-on-centos-7-rendering-images"></a>CentOS 7 上的应用程序（渲染图像）

以下列表适用于 CentOS 7.6, 版本1.1.5 呈现图像。

* Autodesk Maya I/O 2017 更新 5 (cut 201708032230)
* Autodesk Maya I/O 2018 更新 2 (cut 201711281015)
* Autodesk Arnold for Maya 2017（Arnold 版本 5.0.1.1）MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 2018（Arnold 版本 5.0.1.4）MtoA-2.1.0.3-2018
* Chaos Group V-Ray for Maya 2017（版本 3.60.04）
* Chaos Group V-Ray for Maya 2018（版本 3.60.04）
* Blender (2.68)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>最新 Windows Server 2016 呈现图像上的应用程序

以下列表适用于 Windows Server 2016, 版本1.3.4 呈现图像。

* Autodesk Maya I/O 2017 更新 5（版本 17.4.5459）
* Autodesk Maya I/O 2018 更新 4（版本 18.4.0.7622）
* Autodesk 3ds Max I/O 2019 更新 1（版本 21.2.0.2219）
* Autodesk 3ds Max I/O 2018 更新 4（版本 20.4.0.4254）
* Autodesk Arnold for Maya 2017（Arnold 版本 5.2.0.1）MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018（Arnold 版本 5.2.0.1）MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max（Arnold 版本 5.0.2.4）（版本 1.2.926）
* Maya 2018 (版本 3.52.03) 的混乱组 V-Ray
* Chaos Group V-Ray for 3ds Max 2018（版本 3.60.02）
* Chaos Group V-Ray for Maya 2019（版本 3.52.03）
* 3ds Max 2019 (版本 4.10.01) 的混乱组 V-射线
* Blender (2.79)

> [!NOTE]
> 3ds Max 2019 (版本 4.10.01) 的混乱组 V-将重大更改引入了对双光线的重大更改。 若要使用以前的版本 (版本 3.60.02), 请使用 Windows Server 2016, 版本1.3.2 呈现节点。

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>以前的 Windows Server 2016 呈现图像上的应用程序

以下列表适用于 Windows Server 2016 版本 1.3.2（渲染图像）。

* Autodesk Maya I/O 2017 更新 5（版本 17.4.5459）
* Autodesk Maya I/O 2018 更新 4（版本 18.4.0.7622）  
* Autodesk 3ds Max I/O 2019 更新 1（版本 21.2.0.2219）
* Autodesk 3ds Max I/O 2018 更新 4（版本 20.4.0.4254）
* Autodesk Arnold for Maya 2017（Arnold 版本 5.2.0.1）MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018（Arnold 版本 5.2.0.1）MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max（Arnold 版本 5.0.2.4）（版本 1.2.926）
* Chaos Group V-Ray for Maya 2019（版本 3.52.03）
* Chaos Group V-Ray for 3ds Max 2018（版本 3.60.02）
* Blender (2.79)

## <a name="next-steps"></a>后续步骤

若要使用渲染 VM 映像，需要在创建池时在池配置中指定它们；请参阅 [Batch 池渲染功能](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools)。
