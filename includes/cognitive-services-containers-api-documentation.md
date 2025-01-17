---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 00cc63f53388ab7bea05a0b55784247f63477684
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704319"
---
## <a name="validate-that-a-container-is-running"></a>验证容器是否正在运行 

有几种方法可用于验证容器是否正在运行。 

|请求|目的|
|--|--|
|`http://localhost:5000/`|容器提供主页。|
|`http://localhost:5000/status`|使用 GET 进行了请求，从而在不会导致终结点查询的情况下验证容器是否正在运行。 此请求可用于 Kubernetes [运行情况和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。|
|`http://localhost:5000/swagger`|容器提供终结点以及 `Try it now` 功能的整套文档。 使用此功能可以将设置输入到基于 Web 的 HTML 表单并进行查询，而无需编写任何代码。 查询返回后，将提供示例 CURL 命令，用于演示所需的 HTTP 标头和正文格式。 |

![容器的主页](./media/cognitive-services-containers-api-documentation/container-webpage.png)
