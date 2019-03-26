---
title: 使用 Azure 空间的定位点的有效定位体验的指导原则 |Microsoft Docs
description: 指导原则和注意事项，以创建和使用 Azure 空间的定位点来有效地定位的定位点。
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4714ea7aa4bf55f7cbd4500977b09505788233dd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895798"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>使用 Azure 空间的定位点创建有效的定位点体验

本文提供了指导原则和注意事项，以帮助您有效地创建和使用空间的定位点定位的定位点。

## <a name="good-anchors"></a>很好的定位点

空间的定位点可帮助您创建好的定位点。 请务必花费在培训或指导用户在你的用户体验 (UX) 创建好的定位点的时间。 通过在创建好的定位点预先投资，有助于最终用户能够可靠地找到的定位点：

- 跨不同设备。
- 在不同的时间。
- 在不同的光照条件。
- 从所需几个方面的空间中。

## <a name="static-and-dynamic-locations"></a>静态和动态的位置

设计的定位体验的一部分就选择位置。 位置将是静态的定义空间的管理员？ 或将它们是动态的和由用户定义？

零售商店经理可能希望诱使用户能够访问静态店内体验。 但是，混合现实棋盘类游戏开发人员可能想要让用户选择要播放的位置。

有关静态位置，可以给管理员花费时间来组织，从而具有很好的定位点的空间。

对于动态的位置，您应考虑如何教授或指导用户在你的用户体验创建好的定位点。

## <a name="stable-visual-features"></a>稳定的可视功能

直观跟踪系统使用混合现实，增强现实设备依赖于环境的可视化功能。 若要获取的最可靠的体验：  

- *执行*中具有稳定的视觉特征 （即，不经常更改的功能） 的位置创建锚点。

- *不*具有没有显著的特征的大型空白图面上创建锚点。

- *不*上高反射材料创建锚点。

- *不*其中重复该模式，如地毯或墙纸的图面上创建锚点。

![用于定位点的良好环境和一个错误的环境，用于定位点的示例](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>各种查看透视

在创建定位点，请考虑更高版本将尝试找到定位点的人员。

例如，考虑具有两个房间的房间的中间的定位点。 您可能想要允许用户从任一门进入聊天室。 在创建定位点，您将需要扫描这两个走廊其数据的位置。 更改角度，以捕获定位点周围的环境数据，以便用户可以找到从任一门的定位点。

一般情况下，在创建定位点，从扫描将尝试找到它的人员的角度来看。 因此如果要将虚拟内容放置在室外雕塑，最好四处雕塑，扫描它，如创建定位点时。 如果你定位点是在房间的角，则仅在一个方向对待它从。 在创建此定位点，你可以只需从这个角度看扫描。

## <a name="multiple-anchors"></a>多个定位点

照明可以使应用程序检测到的可视功能差异。 在强自然 light 中创建的定位点可能很难找到在人工光线，反之亦然。  

如果此问题，它可以帮助创建两个定位点。 在相同的作用点，创建一个定位点在夏时制，另一个在人工光线中。 然后，您的应用程序可以查询有关这两个定位点。 找到任一定位点时，应用将有定位点姿势。 

同样，在其中的可视化功能更改，因为大多数对象移动的环境中，可以帮助多个定位点。 定位点变得很难找到由于在环境中的重大更改，可以将定位点替换为一个新。 您可以这样做，例如，零售商店布局从中刷新每隔几个月中。

## <a name="targets-and-rooms"></a>目标和聊天室

在许多情况下，定位点是到你的应用体验的入口点。 你将想要快速完成此步骤并可靠地因此用户可以输入你的体验。 在用户将如何查找你的定位点上花费时间是一个重要的设计步骤。 最好考虑一下查找在两个广泛的方案方面的定位点：*目标*并*聊天室*。

### <a name="targets"></a>目标

在目标方案中，定位点的位置是已知的。 例如，在虚构的混合现实绘制应用中，一个用户将放入虚拟画布在墙上。 她指示其他室中的用户指向其设备上 wall，以找到定位点并开始体验相同的位置。  

目标方案的另一个示例可能是在读取时，"扫描的交易。"的咖啡店登录 咖啡厅此处放置一个定位点。 当用户扫描符号时，他们找到定位点，并输入增强现实体验咖啡上查找交易。

在目标方案中，可以帮助照片。 如果其设备上的预期目标照片显示用户，他们可以快速确定要扫描现实世界中的内容。 例如，可能会帮助你通过使用 GPS 到达预期目标常规区域内的用户。 当用户到达时，您的应用程序显示了目标的照片。 用户查找周围的空间，查找目标，然后将扫描的定位点。

![图中的定位点，显示用户的移动设备上的目标的照片](./media/start-here-edit.png)

### <a name="rooms"></a>聊天室

在空间方案中，用户输入一个空格，只需知道存在的定位点位置。 用户使用其设备扫描空间，并快速找到定位点。

这种体验通常要求您创建良好的组织有序的定位点，如中所述[各种查看透视](#consider-various-viewing-perspectives)。 如果你在创建定位点时扫描从多个视角空间，用户可以在尝试查找它时几乎任何地方扫描。

![图中的用户可以如何扫描空间来查找定位点](./media/scan-room.png)

实质上，您花费更多的时间来扫描空间时您创建定位点，以便更高版本的用户可以扫描，并快速找到定位点。 在您创建您的体验，您需要考虑此重要折中方案。

前面所述的混合现实绘制应用程序示例不起作用，以及空间方案。 在这里，将定位点放置的用户希望其他人快速加入体验。 用户不想等待，直到聊天室还扫描开始体验。 因为所有用户都知道从中点来查找与标记其设备，此示例作为目标方案的更好地工作。

## <a name="anchor-location"></a>定位点位置

直观跟踪系统依赖于在环境中的可视化功能。 扫描包括的可视化效果更佳功能，查找定位点的可能性就越高。

请按照本部分，若要生成的用户体验，鼓励有用扫描环境中的一般准则。

首先，如果用户不会在几秒钟内找到一个定位点，应用应鼓励用户可在移动设备以捕获更多透视。 应用程序还可以鼓励用户自己移动环境扫描的更多透视从定位点。 设备能够查看更多的功能透视越好。

对于目标方案，要求用户在目标中移动以查看从不同的角度。 换而言之，要求用户定位点直到找到捕获从新透视目标。

聊天室的情况下，要求用户进行慢扫描聊天室。 例如，要求用户打开，以捕获 180 度或甚至 360 度的空间。 或要求用户以查看从新的角度来看此聊天室。 

最有意义的方法是在同一房间扫描。 跨聊天室扫描捕获环境的更多个 visual 功能，比扫描附近的墙上，例如。 附近的墙上的扫描不会捕获环境的任意多个有用的可视功能。

不是反复从左到右移动设备，定位点查找时很有帮助。 这只是捕获来自同一个角度来看的相同点。

## <a name="experience-tests"></a>体验测试

在本文中，我们已经讨论了通用准则。 使用空间的定位点，您要编写与现实世界交互的应用。 正因为如此，则应为在真实环境中测试应用程序的定位点方案分配时间。 这是表示，希望你的用户使用此应用的环境的尤其如此。