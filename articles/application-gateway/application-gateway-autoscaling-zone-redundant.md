---
title: 自动缩放和区域冗余的应用程序网关 v2
description: 本文介绍了 Azure 应用程序 Standard_v2 和 WAF_v2 SKU，其中包括自动缩放和区域冗余的功能。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/13/2019
ms.author: victorh
ms.openlocfilehash: 8e79fd1a839113cad5a3a36c01855d98793d7032
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655312"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>自动缩放和区域冗余的应用程序网关 v2 

应用程序网关和 Web 应用程序防火墙 (WAF) 也是下 Standard_v2 和 WAF_v2 SKU 提供的。 V2 SKU 提供性能增强功能，并添加了对关键新功能，如自动缩放、 区域冗余和支持为静态 Vip 支持。 标准和 WAF SKU 下的现有功能继续支持在新的 v2 SKU，但有少数例外中列出[比较](#differences-with-v1-sku)部分。

新的 v2 SKU 包括以下增强功能：

- 自动缩放  ：凭借自动缩放 SKU，应用程序网关或 WAF 部署可根据变化中的流量负载模式增加或减少。 自动缩放还无需在预配期间要求选择部署大小或实例计数。 此 SKU 提供，则返回 true 的弹性。 在 Standard_v2 和 WAF_v2 SKU 中，应用程序网关可以同时在固定容量 （已禁用自动缩放） 和启用自动缩放模式下进行操作。 固定容量模式对具有一致性和可预测工作负荷的方案非常有用。 自动缩放模式为有益于应用程序，请参阅中的应用程序流量的变体。
- **区域冗余**：应用程序网关或 WAF 部署可以跨多个可用性区域，无需预配每个区域使用流量管理器中的单独应用程序网关实例。 您可以选择一个区域或多个区域部署应用程序网关实例位置，这将发生区域故障更具弹性。 应用程序的后端池可以通过类似方式分布在多个可用性区域中。

  区域冗余是可用仅 Azure 区域提供的。 在其他区域，支持所有其他功能。 有关详细信息，请参阅 [Azure 中的可用性区域是什么？](../availability-zones/az-overview.md#services-support-by-region)
- **静态 VIP**：应用程序网关 v2 SKU 仅支持静态 VIP 类型。 这可确保与应用程序网关相关联的 VIP 不会更改的部署，即使在重启后的生命周期。  不存在静态 VIP 在 v1 中，因此你必须使用应用程序网关 URL 而不是 IP 地址的域名称路由到应用服务通过应用程序网关。
- **标头重写**:应用程序网关，可添加、 删除或更新与 v2 SKU 的 HTTP 请求和响应标头。 有关详细信息，请参阅[重写 HTTP 标头与应用程序网关](rewrite-http-headers.md)
- **密钥保管库集成 （预览版）** :应用程序网关 v2 支持的附加到启用 HTTPS 侦听器的服务器证书 （在公共预览版） 与密钥保管库集成。 有关详细信息，请参阅[使用密钥保管库证书的 SSL 终止](key-vault-certs.md)。
- **Azure Kubernetes 服务入口控制器 （预览版）** :应用程序网关 v2 入口控制器允许 Azure 应用程序网关要用作入口的 Azure Kubernetes 服务 (AKS) 名为 AKS 群集。 有关详细信息，请参阅[文档页](https://azure.github.io/application-gateway-kubernetes-ingress/)。
- **性能增强**：V2 SKU 提供最多 5 X 更好 SSL 卸载相比标准/WAF SKU 的性能。
- **更快的部署和更新时间**v2 SKU 提供标准/WAF SKU 相比更快地部署和更新的时间。 这还包括 WAF 配置更改。

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>支持的区域

在以下区域均提供 Standard_v2 和 WAF_v2 SKU:美国中北部、 美国中南部、 美国西部、 美国西部 2、 美国东部、 美国东部 2、 美国中部、 欧洲北部、 欧洲西部、 亚洲东南部、 法国中部、 英国西部、 日本东部、 日本西部、 澳大利亚东部、 澳大利亚东南部、 加拿大中部、 加拿大东部、 亚洲东部、 韩国中部、 韩国南部、 印度南部、 英国南部、 印度中部、 印度西部、 印度南部。

## <a name="pricing"></a>定价

具有 v2 SKU 的定价模型由消耗驱动和不再附加到实例计数或大小。 V2 SKU 的定价有两个组件：

- **固定的价格**-这是每小时 （或不足 1 小时） 来预配 Standard_v2 或 WAF_v2 网关的价格。
- **容量单位价格**-这是基于使用量的费用，收取固定费用。 容量单位费用也按小时或部分按小时计算。 容量单位有 3 个维度：计算单位、持久连接和吞吐量。 计算单位测量的是所使用的处理器容量。 因素会影响计算单位是 TLS 连接/sec、 URL 重写计算和 WAF 规则处理。 持续性连接是在给定计费间隔中的应用程序网关建立 TCP 连接的度量值。 吞吐量是由系统处理给定的计费时间间隔中的平均兆位/秒。

每个容量单位最多个组成：1 个计算单元，或 2500年持久连接或 2.22 Mbps 吞吐量。

计算单元的指南：

- **Standard_v2** -每个计算单元是能够每秒含 RSA 2048 位密钥的 TLS 证书的大约 50 个连接。
- **WAF_v2** -每个计算单元可以支持大约 10 个并发请求每秒的 70%的流量的 70-30%组合请求不超过 2 KB 获取/发布和剩余更高版本。 WAF 性能当前不受响应大小。

> [!NOTE]
> 目前，每个实例都能支持大约 10 个容量单位。
> 计算单元可以处理的请求数取决于各种条件如 TLS 证书密钥大小、 密钥交换算法、 标头重写，如果 WAF 传入的请求大小。 我们建议你执行应用程序测试，以确定每个计算单元的请求速率。 容量单位和计算单元将可作为一项度量计费开始之前。

下表显示了示例价格，并将仅供说明之。

**在美国东部定价**:

|              SKU 名称                             | 固定的价格 （$/ 小时）  | 容量单位价格 （$/ CU-小时）   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

有关定价的详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/application-gateway/)。 计费计划在 2019 年 7 月 1 日开始。

**示例 1**

应用程序网关 Standard_v2 固定容量的五个实例而无需手动缩放模式中的自动缩放设置。

固定的价格 = 744(hours) * 0.20 美元 = $148.8 <br>
容量单位 = 744 （小时数） * 每个实例的 10 个容量单位 * 5 个实例 * 每个容量单位小时 0.008 美元 = $297.6

总价格 = $148.8 + $297.6 = $446.4

**示例 2**

一个月内预配应用程序网关 standard_v2 并在此期间接收 25 新 SSL 连接数/秒，平均 8.88 Mbps 数据传输。 假设连接生存期很短，您的价格将为：

固定的价格 = 744(hours) * 0.20 美元 = $148.8

容量单位价格 = 744(hours) * 最大值 （25/50 计算单位的连接数/秒，吞吐量 8.88/2.22 容量单位） * 0.008 = 744 * 4 * 0.008 美元 = $23.81

总价格 = $148。 23.81 8 + = $172.61

> [!NOTE]
> Max 函数返回的值对中的最大值。

**示例 3**

一个月内预配应用程序网关 WAF_v2。 在此期间，它接收 25 新 SSL 连接数/秒，平均 8.88 Mbps 数据传输，并每秒 80 请求。 假设连接较短，而且，应用程序的计算单元计算每个计算单位支持 10 个 RPS，您的价格将为：

固定的价格 = 744(hours) * 为 0.36 美元 = $267.84

容量单位价格 = 744(hours) * 最大值 (计算单元 Max(25/50 for connections/sec, 80/10 WAF RPS)，8.88/2.22 容量单位的吞吐量) * $0.0144 = 744 * 8 * 0.0144 = $85.71

总价格 = $267.84 + $85.71 = $353.55

> [!NOTE]
> Max 函数返回的值对中的最大值。

## <a name="scaling-application-gateway-and-waf-v2"></a>缩放应用程序网关和 WAF v2

可以将应用程序网关和 WAF 配置的两种模式中的扩展：

- **自动缩放**-可以通过启用自动缩放，Sku 增加或减少根据应用程序流量需求的应用程序网关和 WAF v2。 此模式提供了更好地为你的应用程序的弹性，无需猜测应用程序网关大小或实例计数。 此模式还可以通过不要求若要运行网关在预计的最大流量负载的预配的峰值容量来节省成本。 客户必须指定的最小值和 （可选） 最大实例计数。 最小容量可确保应用程序网关和 WAF v2 不低于指定即使在流量不存在的最小实例计数。 您将收取此最小容量甚至在没有任何流量。 您还可以选择指定最大实例计数，这可确保应用程序网关不会扩展到指定数量的实例。 将继续付费网关提供服务的流量。 实例计数的范围可以介于 0 到 125。 如果未指定，则最大实例计数的默认值为 20。
- **手动**-或者，可以选择手动模式下，网关不会自动缩放。 在此模式下，如果没有更多流量比什么应用程序网关或 WAF 可以处理，它可能导致流量丢失。 手动模式下，指定实例计数是必需的。 实例计数可能会不同于 1 到 125 实例。

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>SKU v1 和 v2 SKU 之间的功能比较

下表比较了每个 SKU 提供的功能。

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| 自动缩放                                       |          | &#x2713; |
| 区域冗余                                   |          | &#x2713; |
| 静态 VIP                                        |          | &#x2713; |
| Azure Kubernetes 服务 (AKS) 入口控制器 |          | &#x2713; |
| Azure 密钥保管库集成                       |          | &#x2713; |
| 重写 HTTP (S) 标头                           |          | &#x2713; |
| 基于 URL 的路由                                 | &#x2713; | &#x2713; |
| 多站点托管                             | &#x2713; | &#x2713; |
| 流量重定向                               | &#x2713; | &#x2713; |
| Web 应用程序防火墙 (WAF)                    | &#x2713; | &#x2713; |
| 安全套接字层 (SSL) 终止            | &#x2713; | &#x2713; |
| 端到端 SSL 加密                         | &#x2713; | &#x2713; |
| 会话相关性                                  | &#x2713; | &#x2713; |
| 自定义错误页                                | &#x2713; | &#x2713; |
| WebSocket 支持                                 | &#x2713; | &#x2713; |
| HTTP/2 支持                                    | &#x2713; | &#x2713; |
| 连接清空                               | &#x2713; | &#x2713; |

> [!NOTE]
> SKU 现在支持自动缩放 v2[默认运行状况探测](application-gateway-probe-overview.md#default-health-probe)自动监视后端池中的所有资源的运行状况并突出显示那些被视为不正常的后端成员。 为没有任何自定义探测配置的后端自动配置默认运行状况探测。 若要了解详细信息，请参阅[运行状况探测应用程序网关中](application-gateway-probe-overview.md)。

## <a name="differences-with-v1-sku"></a>与 v1 SKU 之间的差异

|差异|详细信息|
|--|--|
|身份验证证书|不支持。<br>有关详细信息，请参阅[应用程序网关的端到端 SSL 概述](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)。|
|在同一子网上混合使用 Standard_v2 和标准应用程序网关|不支持|
|应用程序网关子网上的用户定义路由 (UDR)|不支持|
|入站端口范围的 NSG| 对于 Standard_v2 SKU，为 - 65200 到 65535<br>对于标准 SKU，为 - 65503 到 65534<br>有关详细信息，请参阅[常见问题](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)。|
|Azure 诊断中的性能日志|不支持。<br>应当使用 Azure 指标。|
|计费|计划在 2019 年 7 月 1 日开始计费。|
|FIPS 模式|目前不支持。|
|“仅 ILB”模式|目前不支持。 同时支持公共和 ILB 模式。|
|Netwatcher 集成|不支持。|
|Azure 安全中心集成|尚不可用。

## <a name="migrate-from-v1-to-v2"></a>从 v1 迁移到 v2

Azure PowerShell 脚本是帮助您迁移到 v2 自动缩放 SKU 从 v1 应用程序网关/WAF PowerShell 库中。 此脚本可帮助你将配置复制从 v1 网关。 流量迁移仍由你负责。 有关详细信息，请参阅[迁移 Azure 应用程序网关从 v1 到 v2](migrate-v1-v2.md)。

## <a name="next-steps"></a>后续步骤

- [快速入门：使用 Azure 应用程序网关定向 Web 流量 - Azure 门户](quick-create-portal.md)
- [使用 Azure PowerShell 创建具有预留虚拟 IP 地址的自动缩放区域冗余应用程序网关](tutorial-autoscale-ps.md)
- 了解有关[应用程序网关](overview.md)的详细信息。
- 了解有关 [Azure 防火墙](../firewall/overview.md)的详细信息。
