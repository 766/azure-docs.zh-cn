---
title: include 文件
description: include 文件
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 7/16/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a42284765a46f4a000dc5b7fcf2867ef17d69570
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229327"
---
| Resource | 默认限制 |
| --- | --- |
| 数据吞吐量 |30 Gbps<sup>1</sup> |
|规则|10000, 所有规则类型都是组合的。|
|最小 AzureFirewallSubnet 大小 |/26|
|网络和应用程序规则的端口范围|0-64,000。 目前正在努力放宽此限制。|
|公共 IP 地址|最大为 100|
|路由表|默认情况下, AzureFirewallSubnet 具有 0.0.0.0/0 路由, 并将 NextHopType 值设置为**Internet**。<br><br>Azure 防火墙必须具有直接的 Internet 连接。 如果你的 AzureFirewallSubnet 通过 BGP 了解到本地网络的默认路由, 则必须使用 0.0.0.0/0 UDR 替代该路由, 并将**NextHopType**值设置为**internet**以维护直接 Internet 连接。 默认情况下，Azure 防火墙不支持强制的安全加密链路连接到本地网络。<br><br>但是，如果你的配置要求强制的安全加密链路连接到本地网络，Microsoft 将基于具体的情况提供支持。 请联系支持人员，以便我们可以查看你的情况。 如果接受, 我们将允许你的订阅, 并确保维护所需的防火墙 Internet 连接。|

<sup>1</sup>如果需要增加这些限制, 请与 Azure 支持部门联系。
