---
title: 使用 Web 仪表板管理 Azure Kubernetes 服务群集
description: 了解如何使用内置的 Kubernetes Web UI 仪表板管理 Azure Kubernetes 服务 (AKS) 群集
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: mlearned
ms.openlocfilehash: 0de2f285b5eca88a098a2d7cfe1608ad2f0db71b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615239"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>访问 Azure Kubernetes 服务 (AKS) 中的 Kubernetes Web 仪表板

Kubernetes 包含一个可用于基本管理操作的 Web 仪表板。 使用此仪表板，可以查看应用程序的基本运行状况状态和指标，创建并部署服务，以及编辑现有应用程序。 本文介绍如何使用 Azure CLI 访问 Kubernetes 仪表板，然后引导你完成一些基本的仪表板操作。

有关 Kubernetes 仪表板的详细信息，请参阅[Kubernetes Web UI 仪表板][kubernetes-dashboard]。

## <a name="before-you-begin"></a>开始之前

本文档详述的步骤假设你已创建 AKS 群集并已通过该群集建立 `kubectl` 连接。 如果需要创建 AKS 群集，请参阅[AKS 快速入门][aks-quickstart]。

还需安装并配置 Azure CLI 2.0.46 或更高版本。 运行  `az --version`  即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="start-the-kubernetes-dashboard"></a>启动 Kubernetes 仪表板

若要启动 Kubernetes 仪表板，请使用[az aks 浏览][az-aks-browse]命令。 以下示例将为 *myResourceGroup* 资源组中的 *myAKSCluster* 群集打开仪表板：

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

此命令在开发系统与 Kubernetes API 之间创建一个代理，并在 Web 浏览器中打开 Kubernetes 仪表板。 如果 web 浏览器未打开到 Kubernetes 仪表板，请复制并粘贴通常在 Azure CLI 中，记下的 URL 地址`http://127.0.0.1:8001`。

![Kubernetes Web 仪表板的概述页](./media/kubernetes-dashboard/dashboard-overview.png)

### <a name="for-rbac-enabled-clusters"></a>对于启用了 RBAC 的群集

如果 AKS 群集使用 RBAC，则必须先创建 *ClusterRoleBinding*，然后才能正确访问仪表板。 默认情况下，Kubernetes 仪表板是使用最小读取访问权限部署的，并且显示 RBAC 访问错误。 Kubernetes 仪表板当前不支持使用用户提供的凭据来确定访问权限级别，而是使用授予给服务帐户的角色。 群集管理员可以选择向 *kubernetes-dashboard* 服务帐户授予更多访问权限，但这可能会导致需要进行权限提升。 还可以集成 Azure Active Directory 身份验证来提供更精细的访问权限级别。

若要创建一个绑定，请使用[kubectl 创建 clusterrolebinding][kubectl-create-clusterrolebinding]命令，在下面的示例所示。 

> [!WARNING]
> 此示例绑定不应用任何其他身份验证组件，因此可能会导致不安全的使用。 Kubernetes 仪表板将对有权访问该 URL 的任何人开放。 请勿公开 Kubernetes 仪表板。
>
> 使用不同的身份验证方法的详细信息，请参阅 Kubernetes 仪表板 wiki 上[的访问控制][dashboard-authentication]。

```console
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

现在可以访问启用了 RBAC 的群集中的 Kubernetes 仪表板。 若要启动 Kubernetes 仪表板，请使用[az aks 浏览][az-aks-browse]命令在上一步中所述。

## <a name="create-an-application"></a>创建应用程序

若要查看 Kubernetes 仪表板可以如何降低管理任务的复杂性，让我们创建一个应用程序。 可以从 Kubernetes 仪表板通过提供文本输入、YAML 文件或通过一个图形化向导来创建应用程序。

若要创建应用程序，请完成以下步骤：

1. 选择窗口右上角的“创建”按钮。 
1. 若要使用图形化向导，请选择“创建应用”  。
1. 为部署提供一个名称，例如 *nginx*
1. 输入要使用的容器映像的名称，例如 *nginx:1.15.5*
1. 若要为 Web 流量公开端口 80，请创建一个 Kubernetes 服务。 在“服务”下，选择“外部”，对于端口和目标端口，都输入 **80**。  
1. 准备就绪后，选择“部署”  来创建应用。

![在 Kubernetes Web 仪表板中部署应用](./media/kubernetes-dashboard/create-app.png)

为 Kubernetes 服务分配公共外部 IP 地址需要一到两分钟时间。 在左侧，在“发现和负载均衡”  下，选择“服务”。  此时将列出应用的服务，包括“外部终结点”  ，如以下示例中所示：

![查看服务和终结点的列表](./media/kubernetes-dashboard/view-services.png)

选择终结点地址以在 Web 浏览器窗口中打开默认的 NGINX 页面：

![查看部署的应用程序的默认 NGINX 页面](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>查看 Pod 信息

Kubernetes 仪表板可以提供基本的监视指标和故障排除信息，例如日志。

若要查看有关应用程序 Pod 的详细信息，请在左侧菜单中选择“Pod”  。 此时会显示可用 Pod 的列表。 选择你的 *nginx* Pod 来查看信息，例如资源消耗：

![查看 Pod 信息](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>编辑应用程序

除了创建和查看应用程序之外，Kubernetes 仪表板还可以用来编辑和更新应用程序部署。 若要为应用程序提供额外的冗余，让我们来增加 NGINX 副本数。

若要编辑部署，请执行以下操作：

1. 在左侧菜单中选择“部署”  ，然后选择你的 *nginx* 部署。
1. 在右上角的导航栏中选择“编辑”。 
1. 找到 `spec.replica` 值，大约在第 20 行。 若要增加应用程序的副本数，请将此值从 *1* 更改为 *3*。
1. 在完成后，选择“更新”。 

![编辑部署以更新副本数](./media/kubernetes-dashboard/edit-deployment.png)

在副本集内创建新 Pod 需要花费一些时间。 在左侧菜单上，选择“副本集”  ，然后选择你的 *nginx* 副本集。 Pod 列表现在反映了已更新的副本计数，如以下示例输出中所示：

![查看副本集的信息](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>后续步骤

有关 Kubernetes 仪表板的详细信息，请参阅[Kubernetes Web UI 仪表板][kubernetes-dashboard]。

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
