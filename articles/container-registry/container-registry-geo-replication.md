---
title: 异地复制的 Azure 容器注册表
description: 开始创建和管理异地复制的 Azure 容器注册表。
services: container-registry
author: stevelas
manager: gwallace
ms.service: container-registry
ms.topic: overview
ms.date: 05/24/2019
ms.author: stevelas
ms.openlocfilehash: 2fffa3b063969cbe68fb9a405f4198f15b3f9809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845204"
---
# <a name="geo-replication-in-azure-container-registry"></a>Azure 容器注册表中的异地复制

需要本地状态或热备份的公司可选择从多个 Azure 区域运行服务。 最佳实践是在映像运行于的每个区域放置一个容器注册表，允许近网络操作，以实现快速可靠的映像层传输。 异地复制允许 Azure 容器注册表充当单个注册表，向多个区域提供多主区域注册表。 

异地复制注册表有以下优点：

* 单个注册表/映像/标记的名称可跨多个区域使用
* 由区域部署实现近网络注册表访问
* 由于是从与容器主机处于相同区域的本地复制注册表中拉取映像，因此无额外传输费用
* 跨多个区域对注册表进行单一管理

> [!NOTE]
> 如果需要在多个 Azure 容器注册表中维护容器映像的副本，则 Azure 容器注册表还支持[映像导入](container-registry-import-images.md)。 例如，在 DevOps 工作流中，可以将映像从开发注册表导入到生产注册表中，不需要使用 Docker 命令。
>

## <a name="example-use-case"></a>示例用例
Contoso 在美国、加拿大和欧洲各地运行着一个公开展示网站。 为了向这些市场提供本地近网络内容，Contoso 在美国西部、美国东部、加拿大和西欧都运行着 [Azure Kubernetes 服务](/azure/aks/) (AKS) 群集。 部署为 Docker 映像的网站应用程序在所有区域中均使用相同的代码和映像。 从在每个区域独特部署的数据库检索该区域的本地内容。 对于本地数据库这样的资源，每个区域部署均有其唯一配置。

开发团队位于华盛顿州西雅图市，使用美国西部数据中心。

![推送到多个注册表](media/container-registry-geo-replication/before-geo-replicate.png)<br />*推送到多个注册表*

使用异地复制功能之前，Contoso 已在美国西部拥有基于美国的注册表，在西欧拥有其他注册表。 为了向这些不同的区域提供服务，开发团队将映像推送到了两个不同的注册表。

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![从多个注册表拉取](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*从多个注册表拉取*

多个注册表的典型挑战包括：

* 美国东部、美国西部和加拿大中部的群集均拉取自美国西部的注册表，当每个远程容器主机从美国西部的数据中心拉取映像时，将产生传输费用。
* 开发团队必须将映像推送到美国西部和西欧的注册表。
* 开发团队必须使用引用本地注册表的映像名称配置和维护每个区域的部署。
* 必须为每个区域配置注册表访问。

## <a name="benefits-of-geo-replication"></a>异地复制的优点

![从异地复制注册表拉取](media/container-registry-geo-replication/after-geo-replicate-pull.png)

使用 Azure 容器注册表的异地复制功能，将实现以下优点：

* 跨所有区域管理单个注册表：`contoso.azurecr.io`
* 管理多个映像部署的单个配置，因为所有区域使用同一个映像 URL：`contoso.azurecr.io/public/products/web:1.2`
* 推送到单个注册表，而 ACR 管理异地复制。 可以配置区域性 [Webhook](container-registry-webhook.md) 来通知你特定副本中的事件。

## <a name="configure-geo-replication"></a>配置异地复制

配置异地复制就如在地图上单击区域一样简单。 你还可以使用包括 Azure CLI 中的 [az acr replication](/cli/azure/acr/replication) 命令在内的工具来管理异地复制。

异地复制是[高级注册表](container-registry-skus.md)特有的功能。 如果尚未使用高级注册表，可在 [Azure 门户](https://portal.azure.com)中将基本和标准更改为高级：

![在 Azure 门户中切换 SKU](media/container-registry-skus/update-registry-sku.png)

若要为高级注册表配置异地复制，可通过 https://portal.azure.com 登录到 Azure 门户。

导航到 Azure 容器注册表，然后选择“复制”  ：

![Azure 门户容器注册表 UI 中的副本](media/container-registry-geo-replication/registry-services.png)

地图中显示了所有当前的 Azure 区域：

 ![Azure 门户中的区域地图](media/container-registry-geo-replication/registry-geo-map.png)

* 蓝色六边形表示当前的副本
* 绿色六边形表示可能的复制区域
* 灰色六边形表示尚不可复制的 Azure 区域

若要配置副本，请选择一个绿色六边形，然后选择“创建”  ：

 ![Azure 门户中的“创建副本”UI](media/container-registry-geo-replication/create-replication.png)

若要创建其他副本，请选择表示其他区域的绿色六边形，然后单击“创建”  。

ACR 将开始在配置的副本间同步映像。 完成后，门户将显示“就绪”  。 门户中的副本状态不会自动更新。 使用刷新按钮查看更新状态。

## <a name="considerations-for-using-a-geo-replicated-registry"></a>使用异地复制注册表的注意事项

* 异地复制注册表中的每个区域在设置后都是独立的。 Azure 容器注册表 SLA 适用于每个异地复制区域。
* 当你从异地复制注册表中推送或拉取映像时，后台的 Azure 流量管理器会将请求发送到位于离你最近的区域中的注册表。
* 将映像或标记更新推送到最近的区域后，Azure 容器注册表需要一些时间将清单和层复制到你选择加入的其余区域。 较大的映像比较小的映像复制所需的时间更长。 映像和标记通过最终一致性模型在复制区域之间进行同步。
* 若要管理依赖于异地复制注册表的推送更新的工作流，建议你配置 [Webhook](container-registry-webhook.md) 以响应推送事件。 你可以在异地复制注册表中设置区域性 Webhook，以跟踪在异地复制区域内完成的推送事件。


## <a name="geo-replication-pricing"></a>异地复制定价

异地复制是 Azure 容器注册表[高级 SKU](container-registry-skus.md) 的一项功能。 将注册表复制到所需区域时，每个区域都会产生高级注册表费用。

在前面的示例中，Contoso 将两个注册表合并到一起，并向美国东部、加拿大中部和西欧添加副本。 Contoso 每月将支付四次高级费用，且无额外配置或管理。 现在每个区域就从本地拉取映像，既提升了性能和可靠性，又节省了从美国西部到加拿大和美国东部的网络传输费用。

## <a name="next-steps"></a>后续步骤

签出三部分的教程系列，[Azure 容器注册表中的异地复制](container-registry-tutorial-prepare-registry.md)。 演示创建异地复制注册表、构建容器，然后使用单个 `docker push` 命令将其部署到多个区域的用于容器的 Web 应用。

> [!div class="nextstepaction"]
> [Azure 容器注册表中的异地复制](container-registry-tutorial-prepare-registry.md)
