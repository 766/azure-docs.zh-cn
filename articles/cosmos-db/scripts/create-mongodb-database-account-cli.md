---
title: Azure CLI 脚本 - 使用 Azure Cosmos DB 的用于 MongoDB 的 API 创建 Cosmos 帐户
description: Azure CLI 脚本示例 - 使用 Azure Cosmos DB 的用于 MongoDB 的 API 创建 Cosmos 帐户
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 7/2/2019
ms.reviewer: sngun
ms.openlocfilehash: a256a0ff5164ec9b25aea3849f20643ee3719fac
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541427"
---
# <a name="create-an-azure-cosmos-db-account-with-azure-cosmos-dbs-api-for-mongodb-using-azure-cli"></a>使用 Azure CLI 通过 Azure Cosmos DB 的用于 MongoDB 的 API 创建 Azure Cosmos DB 帐户

该示例 CLI 脚本使用 Azure Cosmos DB 的用于 MongoDB 的 API 创建 Cosmos 帐户。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

[!NOTE] 若要了解有关数据库和容器命名约定的详细信息，请参阅[使用 Azure Cosmos DB 中的数据库、容器和项目](../databases-containers-items.md)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-mongodb-account/create-cosmosdb-mongodb-account.sh "Create a Cosmos account with Azure Cosmos DB's API for MongoDB - account, database, and collection.")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | 创建 Azure Cosmos DB 帐户。 |
| [az cosmosdb database create](/cli/azure/cosmosdb/database#az-cosmosdb-database-create) | 创建 Azure Cosmos DB 数据库。 |
| [az cosmosdb collection create](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-create) | 创建用于 MongoDB 的 Azure Cosmos DB 集合。 |
| [az group delete](/cli/azure/resource#az-resource-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Cosmos DB CLI 文档](../cli-samples.md)中找到其他 Azure Cosmos DB CLI 脚本示例。
