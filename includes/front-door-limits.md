---
title: include 文件
description: include 文件
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: deca0034996f6c8ddcac71cd4f191c1a0659b655
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333366"
---
| Resource | 默认值/最大限制 |
| --- | --- |
| 每个订阅的 azure 第一道防线服务资源 | 100 |
| 前端主机，其中包括每个资源的自定义域 | 100 |
| 每个资源的路由规则数 | 100 |
| 每个资源的后端池 | 50 |
| 每个后端池的后端 | 100 |
| 要匹配路由规则的路径模式数 | 25 |
| 每个策略的自定义 Web 应用程序防火墙规则数 | 10 |
| 每个资源的 Web 应用程序防火墙策略数 | 100 |
| 每个自定义规则的 web 应用程序防火墙匹配条件 | 10 |
| 每个 web 应用程序防火墙 IP 地址范围匹配条件 | 600 |
| 每个匹配条件的 web 应用程序防火墙字符串匹配值 | 10 |
| Web 应用程序防火墙字符串匹配值长度 | 256 |
| Web 应用程序防火墙 POST 正文参数名称长度 | 256 |
| Web 应用程序防火墙 HTTP 标头名称长度 | 256 |
| Web 应用程序防火墙 cookie 名称长度 | 256 |
| 检查 web 应用程序防火墙 HTTP 请求正文大小 | 128 KB |
| Web 应用程序防火墙自定义响应正文长度 | 2 KB |

### <a name="timeout-values"></a>超时值
#### <a name="client-to-front-door"></a>客户端到 Front Door
- Front Door 的空闲 TCP 连接超时为 61 秒。

#### <a name="front-door-to-application-back-end"></a>到应用程序后端的第一道防线
- 如果分块的响应进行响应，或如果收到第一个区块后返回 200。
- HTTP 请求转发到后端后，第一道防线等待 30 秒钟，从后端的第一个数据包。 然后它将 503 错误返回到客户端。
- 从后端收到的第一个数据包后，等待第一道防线的空闲超时 30 秒。 然后它将 503 错误返回到客户端。
- 第一道防线到后端 TCP 会话超时为 30 分钟。

### <a name="upload-and-download-data-limit"></a>上传和下载数据限制

|  | 使用 chunked 传输编码 (CTE) | 不 HTTP 分块 |
| ---- | ------- | ------- |
| **下载** | 对下载大小没有任何限制。 | 对下载大小没有任何限制。 |
| **上载** |  没有任何限制，只要每个 CTE 上传为不超过 2 GB。 | 大小不能大于 2 GB。 |

### <a name="other-limits"></a>其他限制
- 最大 URL 大小-8192 字节的指定最大长度的原始 URL (方案 + 主机名 + 端口 + 路径 + 查询 URL 的字符串)-最大查询字符串大小-4,096 个字节的以字节为单位指定查询字符串的最大长度。