---
title: 使用 Azure CLI 管理 Azure Cosmos DB 资源
description: 使用 Azure CLI 管理 Azure Cosmos DB 帐户、数据库和容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: f9d8bf9161343e4b36a3c16209873962b69d8af5
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615210"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>使用 Azure CLI 管理 Azure Cosmos 资源

以下指南介绍了使用 Azure CLI 自动管理 Azure Cosmos DB 帐户、数据库和容器的常见命令。 [Azure CLI 参考](https://docs.microsoft.com/cli/azure/cosmosdb)中收录了所有 Azure Cosmos DB CLI 命令的参考页。 还可以在[针对 Azure Cosmos DB 的 Azure CLI 示例](cli-samples.md)中找到更多示例，包括如何为 MongoDB、Gremlin、Cassandra 和表 API 创建和管理 Cosmos DB 帐户、数据库和容器。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

若要使用 SQL API 创建 Azure Cosmos DB 帐户, 请在美国东部和美国西部区域中进行会话一致性, 并运行以下命令:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount # must be lowercase and < 31 characters \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=EastUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=WestUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Azure Cosmos 帐户名称必须小写。

## <a name="create-a-database"></a>创建数据库

若要创建 Cosmos 数据库, 请运行以下命令:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>创建容器

若要创建 Cosmos 400 的 RU/s 和分区键, 请运行以下命令:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --partition-key-path /myPartitionKey \
   --throughput 400
```

## <a name="change-the-throughput-of-a-container"></a>更改容器的吞吐量

若要将 Cosmos 容器的吞吐量更改为 1000 RU/s, 请运行以下命令:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>列出帐户密钥

若要获取 Cosmos 帐户的密钥，请运行以下命令：

```azurecli-interactive
# List account keys
az cosmosdb keys list \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>列出连接字符串

若要获取 Cosmos 帐户的连接字符串，请运行以下命令：

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>重新生成帐户密钥

若要重新生成 Cosmos 帐户的新主密钥，请运行以下命令：

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅：

- [安装 Azure CLI](/cli/azure/install-azure-cli)
- [Azure CLI 参考](https://docs.microsoft.com/cli/azure/cosmosdb)
- [针对 Azure Cosmos DB 的其他 Azure CLI 示例](cli-samples.md)
