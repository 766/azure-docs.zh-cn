---
title: 设置 Azure Service Fabric 网格 CLI | Microsoft Docs
description: 了解如何设置 Azure Service Fabric 网格 CLI。
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: c716ae0a2bb30e7e8eb249a1d230097efc0d3795
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097787"
---
# <a name="set-up-service-fabric-mesh-cli"></a>设置 Service Fabric 网格 CLI
Service Fabric 网格命令行界面 (CLI) 是在本地以及 Azure Service Fabric 网格中部署和管理资源所必需的。 

有三种可以使用的 CLI，下表对它们进行了汇总。 

| CLI 模块 | 目标环境 |  说明 | 
|---|---|---|
| az mesh | Azure Service Fabric 网格 | 主要 CLI，允许针对 Azure Service Fabric 网格环境部署应用程序并管理资源。 
| sfctl | 本地群集 | Service Fabric CLI 允许针对本地群集部署和测试 Service Fabric 资源。  
| Maven CLI | 本地群集与 Azure Service Fabric 网格 | 周围的包装器`az mesh`和`sfctl`允许 Java 开发人员能够使用熟悉的命令行体验，以本地和 Azure 开发体验。  

对于预览版，Azure Service Fabric 网格 CLI 编写为 Azure CLI 的一个扩展。 可以在 Azure Cloud Shell 中安装它，也可以在 Azure CLI 的本地安装中进行安装。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>安装 Azure Service Fabric 网格 CLI
1. 必须安装 Azure CLI 版本 2.0.43 或更高版本。 运行 `az --version` 即可查找版本。 若要安装或升级到最新版本的 CLI，请参阅[安装 Azure CLI][azure-cli-install]。

2. 使用以下命令安装 Azure Service Fabric 网格 CLI 扩展模块。 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. 使用以下命令更新现有的 Azure Service Fabric 网格 CLI 模块。

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>安装 Service Fabric CLI (sfctl) 

根据[设置 Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) 的说明进行操作。 **sfctl** 模块可用于针对本地计算机上的 Service Fabric 群集根据资源模型部署应用程序。 

## <a name="install-the-maven-cli"></a>安装 Maven CLI 

要想使用 Maven CLI，需要在计算机上安装以下项： 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure Mesh CLI (az mesh) - 以 Azure Service Fabric 网格作为目标 
* SFCTL (sfctl) - 以本地群集作为目标 

适用于 Service Fabric 的 Maven CLI 目前仍为预览版。 

若要在 Maven Java 应用中使用 Maven 插件，请将以下代码片段添加到 pom.xml 文件：

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

请阅读 [Maven CLI 参考](service-fabric-mesh-reference-maven.md)部分来了解详细用法。

## <a name="next-steps"></a>后续步骤

还可以设置你的 [Windows 开发环境](service-fabric-mesh-howto-setup-developer-environment-sdk.md)。

找到[常见问题](service-fabric-mesh-faq.md)的答案。

[azure-cli-install]: /cli/azure/install-azure-cli
