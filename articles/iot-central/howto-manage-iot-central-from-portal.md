---
title: 通过 Azure 门户管理 IoT Central | Microsoft Docs
description: 通过 Azure 门户管理 IoT Central。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 7893c8e8b8d67b4b63bd9d6bb5a71552e95c9125
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850250"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>通过 Azure 门户管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

如果不从 IoT Central [应用程序管理器](https://aka.ms/iotcentral)页面创建和管理 IoT Central 应用程序，可以使用 [Azure 门户](https://portal.azure.com) 来管理应用程序。

## <a name="create-iot-central-applications"></a>创建 IoT Central 应用程序

若要创建应用程序, 请导航到[Azure 门户](https://ms.portal.azure.com), 然后选择左侧导航菜单中的 "**创建资源**"。

![管理门户：导航菜单](media/howto-manage-iot-central-from-portal/image0.png)

在搜索栏中，键入“IoT Central”  。

![管理门户：搜索](media/howto-manage-iot-central-from-portal/image0a1.png)

在搜索结果中选择 " **IoT Central 应用程序**" 行项。

![管理门户：搜索结果](media/howto-manage-iot-central-from-portal/image0b1.png)

现在选择“创建”  。

![管理门户：IoT Central 资源](media/howto-manage-iot-central-from-portal/image0c1.png)

填写窗体中的所有字段。 此窗体与在 IoT Central [应用程序管理器](https://aka.ms/iotcentral)页创建应用程序时需要填写的窗体类似。 有关详细信息，请参阅[创建 IoT Central 应用程序](quick-deploy-iot-central.md)快速入门。

![管理门户：创建 IoT Central 资源](media/howto-manage-iot-central-from-portal/image1a.png)  

填写所有字段后, 选择 "**创建**"。

## <a name="manage-existing-iot-central-applications"></a>管理现有 IoT Central 应用程序

如果已有 Azure IoT Central 应用程序，可将其删除或移动到 Azure 门户中的其他订阅或资源组。

> [!NOTE]
> Azure 门户中不会显示试用版应用程序，因为它们与订阅无关。

若要开始, 请选择左侧导航菜单中的 "**所有资源**"。 在搜索框中键入应用程序的名称以在资源列表中将其找到。 然后选择要管理的 IoT Central 应用程序。

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image2a.png)

若要导航到应用程序, 请选择 "IoT Central 应用程序 URL"。

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image3.png)

若要将应用程序移动到其他资源组, 请选择资源组旁边的 "**更改**"。 在“移动资源”页上，选取要将此应用程序迁移到其中的资源组  。

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image4a.png)

若要将应用程序移动到其他订阅, 请选择订阅旁边的 "**更改**" 链接。 在显示的对话框中，选取要将此应用程序迁移到其中的订阅。

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 门户管理 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)