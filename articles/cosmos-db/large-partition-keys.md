---
title: 使用 Azure 门户和各种 SDK 通过大分区键创建 Azure Cosmos 容器。
description: 了解如何使用 Azure 门户和不同的 SDK 通过大分区键在 Azure Cosmos DB 中创建容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: bd1697378e5db0432d181f9f688ccc2468b306e7
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566028"
---
# <a name="create-containers-with-large-partition-key"></a>使用大分区键创建容器

Azure Cosmos DB 使用基于哈希的分区方案实现数据的水平缩放。 在 2019 年 5 月 3 日之前创建的所有 Azure Cosmos 容器都使用哈希函数根据分区键的前 100 个字节计算哈希。 如果多个分区键的前 100 个字节相同，则服务会将这些逻辑分区视为同一逻辑分区。 这可能会导致问题，例如分区大小配额不正确、对不同的分区键应用了唯一索引。 为了解决此问题，我们引入了大分区键。 Azure Cosmos DB 现在支持其值最高为 2 KB 的大分区键。

可以使用增强版哈希函数来支持大分区键，该增强版可以使用最高为 2 KB 的分区键生成唯一哈希。 也可将该哈希版本建议用于分区键基数高的情况，不管分区键的大小如何。 根据定义，分区键基数是指唯一逻辑分区的数目，例如，某个容器中有大约 30000 个逻辑分区。 本文介绍如何使用 Azure 门户和不同的 SDK 通过大分区键创建容器。 

## <a name="create-a-large-partition-key-net-sdk-v2"></a>创建大分区键 (.Net SDK V2)

若要使用较大的分区键，使用.NET SDK 创建容器，指定`PartitionKeyDefinitionVersion.V2`属性。 下面的示例演示如何指定 PartitionKeyDefinition 对象中的版本属性并将其设置为 PartitionKeyDefinitionVersion.V2。

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="create-a-large-partition-key-azure-portal"></a>创建大分区键（Azure 门户） 

若要在使用 Azure 门户创建新容器时创建大分区键，请勾选“我的分区键大于 100 字节”选项。  默认情况下，所有新容器都可以使用大分区键。 如果你不需要大分区键，或早 1.18 于 Sdk 版本上运行的应用程序，请取消选中该复选框。

![使用 Azure 门户创建大分区键](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>创建一个大分区键 (PowerShell)

若要使用较大的分区键，使用 PowerShell 创建容器，包括`"version" = 2`为`partitionKey`对象。

```azurepowershell-interactive
# Create a Cosmos SQL API container with large partition key support (version 2)
$resourceGroupName = "myResourceGroup"
$containerName = "mycosmosaccount" + "/sql/" + "myDatabase" + "/" + "myContainer"

# Container with large partition key support (version = 2)
$containerProperties = @{
  "resource"=@{
    "id"=$containerName;
    "partitionKey"=@{
        "paths"=@("/myPartitionKey");
        "kind"="Hash";
        "version" = 2
    };
    "indexingPolicy"=@{
        "indexingMode"="Consistent";
        "includedPaths"= @(@{
            "path"="/*"
        });
        "excludedPaths"= @(@{
            "path"="/myPathToNotIndex/*"
        })
    }
  }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerName -PropertyObject $containerProperties
```

## <a name="supported-sdk-versions"></a>支持的 SDK 版本

以下 SDK 最低版本支持大分区键：

|SDK 类型  | 最低版本   |
|---------|---------|
|.Net     |    1.18     |
|Java 同步     |   2.4.0      |
|Java 异步   |  2.5.0        |
| REST API | 使用 `x-ms-version` 请求标头时版本高于 `2017-05-03`。|

目前不能在 Power BI 和 Azure Logic Apps 中将容器与大分区键配合使用。 在这些应用程序中，可以在没有大分区键的情况下使用容器。

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的分区](partitioning-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)
* [在容器和数据库上预配吞吐量](set-throughput.md)
* [使用 Azure Cosmos 帐户](account-overview.md)


