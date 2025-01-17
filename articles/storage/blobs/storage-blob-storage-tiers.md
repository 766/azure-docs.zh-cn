---
title: 适用于 Blob 的热、冷、存档访问层 - Azure 存储
description: Azure 存储帐户的热、冷、存档访问层。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 48c6d6ed60045d906fcb711bd07ab492b6bbf488
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543682"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob 存储：热、冷、存档访问层

Azure 存储提供了不同的访问层，允许以最具成本效益的方式存储 Blob 对象数据。 可用的访问层包括：

- **热**：适用于存储经常访问的数据。
- **冷**：适用于存储不常访问且存储时间至少为 30 天的数据。
- **存档**：适用于存储极少访问、存储时间至少为 180 天且延迟要求（以小时计）不严格的数据。

以下注意事项适用于不同的访问层：

- 在帐户级别只能设置热和冷访问层。 存档访问层在帐户级别不可用。
- 热层、冷层和存档层可以在 blob 级别设置。
- 冷访问层中的数据可容忍略微降低的可用性, 但仍需要高持续性、检索延迟以及与热数据类似的吞吐量特征。 对于冷数据，略低的可用性服务级别协议 (SLA) 和较高的访问成本（与热数据相比）对于更低的存储成本而言是可接受的折衷。
- 存档存储将数据脱机存储, 并提供最低的存储成本, 同时还提供最高的数据解除冻结和访问成本。

存储在云中的数据以指数速度增长。 若要针对不断增加的存储需求来管理成本，可以根据属性（如访问频率和计划保留期）整理数据以优化成本。 存储在云中的数据在其生成方式、处理方式以及在生存期内的访问方式等方面会不相同。 某些数据在其整个生存期中都会受到积极的访问和修改。 某些数据则在生存期早期会受到频繁的访问，随着数据变旧，访问会极大地减少。 某些数据在云中保持空闲状态，并且在存储后很少（如果有）被访问。

这些数据访问方案的每一个都受益于针对特定访问模式进行了优化的差异化访问层。 Azure Blob 存储采用热、冷、存档访问层，通过单独的定价模型来满足对差异化访问层的这种需要。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>支持分层的存储帐户

对象存储数据分层到 "热"、"冷" 或 "存档" 仅在 Blob 存储和常规用途 v2 (GPv2) 帐户中受支持。 常规用途 v1 (GPv1) 帐户不支持分层。 不过，客户可以轻松地将其现有的 GPv1 或 Blob 存储帐户转换为 GPv2 帐户，只需在 Azure 门户中单击一下即可。 GPv2 为 blob、文件和队列提供了新的定价结构, 以及对各种新存储功能的访问权限。 另外，以后只对 GPv2 帐户提供某些新功能和价格折扣。 因此, 客户应在全面查看新定价后评估使用 GPv2 帐户, 因为某些工作负荷比 GPv1 更昂贵。 有关详细信息，请参阅 [Azure 存储帐户概述](../common/storage-account-overview.md)。

Blob 存储和 GPv2 帐户在帐户级别公开“访问层”属性。 此属性可方便你将存储帐户中任何 Blob 的默认访问层指定为热层或冷层，前提是该 Blob 尚未在对象级别设置显式层。 对于已在对象级别设置该层的对象，不会应用帐户层。 存档层仅适用于对象级别。 可以随时在这些访问层之间进行切换。

## <a name="premium-performance-block-blob-storage"></a>高级性能块 blob 存储

高级性能块 blob 存储通过高性能硬件提供经常访问的数据。 此性能层中的数据存储在固态硬盘 (Ssd) 上, 针对低延迟和一致性延迟进行了优化。 与传统硬盘相比, Ssd 提供更高的事务速率和吞吐量。

高级性能块 blob 存储非常适合于需要快速而一致的响应时间的工作负荷。 它最适用于执行许多小型事务的工作负荷, 例如捕获遥测数据、消息传递和数据转换。 涉及最终用户的数据 (如交互视频编辑、静态 web 内容和联机事务) 也是良好的候选项。

高级性能块 blob 存储仅通过块 blob 存储帐户类型提供, 并且当前不支持对热、冷或存档访问层进行分层。

## <a name="hot-access-tier"></a>热访问层

热访问层的存储成本高于冷存储和存档层，但访问成本最低。 热访问层的示例使用方案包括：

- 处于活跃使用状态或预期会频繁访问（读取和写入）的数据。
- 分阶段进行处理，并最终迁移至冷访问层的数据。

## <a name="cool-access-tier"></a>冷访问层

与热存储相比，冷访问层的存储成本较低，访问成本较高。 此层适用于将要保留在冷层中至少 30 天的数据。 冷访问层的示例使用方案包括：

- 短期备份和灾难恢复数据集。
- 不再经常查看、但访问时应立即可用的较旧的媒体内容。
- 在收集更多数据以便将来处理时需要经济高效地存储的大型数据集。 （例如，长期存储的科学数据、来自制造设施的原始遥测数据）

## <a name="archive-access-tier"></a>存档访问层

与热层和冷层相比，存档访问层的存储成本最低，但数据检索成本较高。 此层适用于可以容忍数小时的检索延迟且会保留在存档层至少 180 天的数据。

如果 Blob 位于存档存储中，则 Blob 数据处于脱机状态，不能读取、复制、覆盖或修改。 不能创建存档存储中 Blob 的快照。 但是，Blob 元数据会保持联机和可用状态，因而可列出 Blob 及其属性。 对于存档中的 Blob，仅以下操作有效：GetBlobProperties、GetBlobMetadata、ListBlobs、SetBlobTier 和 DeleteBlob。

存档访问层的示例使用方案包括：

- 长期备份、辅助备份和存档数据集
- 必须保留的原始数据，即使它已被处理为最终可用的形式。 （例如，转码为其他格式后的原始媒体文件）
- 需要长时间存储并且几乎不访问的合规性和存档数据。 （例如，安全监控录像、医保组织的旧 X 光/MRI 资料、音频记录、金融服务机构的客户呼叫记录）

### <a name="blob-rehydration"></a>Blob 解除冻结

[!INCLUDE [storage-blob-rehydrate-include](../../../includes/storage-blob-rehydrate-include.md)]
若要了解详细信息, 请参阅[存档层中的解除冻结 blob 数据](storage-blob-rehydration.md)。  

## <a name="account-level-tiering"></a>帐户级别分层

所有三个访问层中的 Blob 可以在同一帐户中共存。 如果 Blob 没有显式分配的层，则会从帐户访问层设置推断相应的层。 如果访问层是从帐户推断出来的，则可看到Blob 属性“推断的访问层”已设置为“true”，且 Blob 属性“访问层”与帐户层匹配。 在 Azure 门户中,_访问层推断_属性将显示为 "**热" (推理)** 或 "**冷" (推断)** 的 "blob 访问" 层。

更改帐户访问层适用于存储在帐户中但未设置显式层的所有_访问层推断_对象。 如果将帐户层从热切换为冷，则只按 GPv2 帐户中没有设置层的所有 Blob 的写入操作次数（以 10,000 次为单位）收费。 不会在 Blob 存储帐户中对此更改收费。 如果在 Blob 存储或 GPv2 帐户中从 "冷" 切换到 "热", 则会对读取操作 (按 10000) 和数据检索 (每 GB) 进行收费。

## <a name="blob-level-tiering"></a>Blob 级别分层

使用 Blob 级分层功能即可通过名为[设置 Blob 层](/rest/api/storageservices/set-blob-tier)的单一操作在对象级别更改数据的层。 可以在使用模式更改时轻松地在热、冷或存档层之间更改 Blob 的访问层，不需在帐户之间移动数据。 所有层级更改都会立即执行。 但是，从存档层中解除冻结 blob 可能需要几个小时。

上次 Blob 层更改的时间通过 Blob 属性“访问层更改时间”公开。 如果 Blob 位于存档层中，则无法被覆盖，因此在这种情况下，不允许上传相同的 Blob。 可以覆盖热层和冷层中的 Blob，在这种情况下，新的 Blob 会继承被覆盖的 Blob 的层。

> [!NOTE]
> 存档存储和 Blob 级别分层仅支持块 Blob。 当前还不能更改包含快照的块 blob 的层。

### <a name="blob-lifecycle-management"></a>Blob 生命周期管理

Blob 存储生命周期管理提供了一种基于规则的丰富策略, 可用于将数据转换为最佳访问层并使数据在其生命周期结束时过期。 请参阅[管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)来了解详细信息。  

> [!NOTE]
> 存储在块 blob 存储帐户 (高级性能) 中的数据当前无法通过[设置 Blob 层](/rest/api/storageservices/set-blob-tier)或使用 Azure blob 存储生命周期管理分层到热、冷或存档。
> 若要移动数据, 必须使用[URL API 中的 Put 块](/rest/api/storageservices/put-block-from-url)或支持此 API 的 AzCopy 版本将 blob 从块 blob 存储帐户同步复制到其他帐户中的热访问层。
> *通过 URL 放置块* API 同步复制服务器上的数据，这意味着只有在所有数据都从原服务器位置移动到目标位置后，调用才会完成。

### <a name="blob-level-tiering-billing"></a>Blob 级别分层计费

将 blob 移到更冷的层（热->冷、热->存档或冷->存档）时，操作按目标层写入操作计费，具体说来就是按目标层的写入操作次数（以 10,000 次为单位）和数据写入量（以 GB 为单位）收费。

将 Blob 移到更暖的层（存档->冷、存档->热或冷->热）时，操作按从源层读取计费，具体说来就是按源层的读取操作次数（以 10,000 次为单位）和数据检索量（以 GB 为单位）收费。 也可能还会收取从池或存档层移出的任何 Blob 的早期删除费用。 下表总结了如何对层更改进行计费。

| | **写入费用（操作 + 访问）** | **读取费用（操作 + 访问）**
| ---- | ----- | ----- |
| **SetBlobTier 方向** | 热 > 超酷,<br> 热 > 存档,<br> 冷 > 存档 | 存档-> 超酷,<br> 存档-> 热、<br> 冷 > 热

### <a name="cool-and-archive-early-deletion"></a>“冷”层和“存档”层提前删除

除了按 GB 和按月收费，移到冷层（仅限 GPv2 帐户）中的 Blob 会有一个 30 天的冷层早期删除期限，移到存档层中的 Blob 会有一个 180 天的存档层早期删除期限。 此项费用按比例计算。 例如，如果将某个 Blob 移到存档层，然后在 45 天后将其删除或移到热层，则需支付相当于将该 Blob 存储在存档层中 135（180 减 45）天的早期删除费用。

如果没有任何访问层发生更改, 则可以使用 blob 属性 "**上次修改时间**" 来计算早期删除。 否则，可以通过查看 Blob 属性（即“access-tier-change-time”）来使用最后一次将访问层修改为“冷”或“存档”的时间。 有关 Blob 属性的详细信息，请参阅[获取 Blob 属性](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties)。

## <a name="comparing-block-blob-storage-options"></a>比较块 Blob 存储选项

下表显示了高级性能块 blob 存储以及 "热"、"冷" 和 "存档" 访问层的比较。

|                                           | **高级性能**   | **热存储层** | **冷存储层**       | **存档存储层**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **可用性**                          | 99.9%                     | 99.9%        | 99%                 | 脱机           |
| **可用性** <br> （RA-GRS 读取）  | 不可用                       | 99.99%       | 99.9%               | 脱机           |
| 使用费                         | 存储成本更高, 访问和事务成本较低 | 存储费用较高，访问和事务费用较低 | 存储费用较低，访问和事务费用较高 | 存储费用最低，访问和事务费用最高 |
| 最小对象大小                   | 不可用                       | 不可用          | 不可用                 | 不可用               |
| 最短存储持续时间              | 不可用                       | 不可用          | 30天<sup>1</sup> | 180 天
| **延迟** <br> （距第一字节时间） | 一位数的毫秒 | 毫秒 | 毫秒        | 小时<sup>2</sup> |

<sup>1</sup> GPv2 帐户的冷层中的对象的保持期最短为30天。 Blob 存储帐户没有冷层的最小保持期。

<sup>2</sup>存档存储当前支持2个解除冻结优先级, 高和标准提供不同的检索延迟。 有关详细信息, 请参阅[Blob 解除冻结](#blob-rehydration)。

> [!NOTE]
> Blob 存储帐户支持与常规用途 v2 存储帐户相同的性能和可伸缩性目标。 有关详细信息，请参阅 [Azure 存储可伸缩性和性能目标](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="quickstart-scenarios"></a>快速入门方案

本部分使用 Azure 门户演示以下方案：

- 如何更改 GPv2 或 Blob 存储帐户的默认帐户访问层。
- 如何更改 GPv2 或 Blob 存储帐户中 Blob 的层。

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>更改 GPv2 或 Blob 存储帐户的默认帐户访问层

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 要导航到存储帐户，请先选择“所有资源”，然后选择存储帐户。

1. 在“设置”边栏选项卡中，单击“ **配置** ”以查看和/或更改帐户配置。

1. 根据需求选择合适的访问层：将“访问层”设置为“冷”或“热”。

1. 单击边栏选项卡顶部的“保存”。

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>更改 GPv2 或 Blob 存储帐户中 Blob 的层

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 若要导航到存储帐户中的 Blob，请依次选择“所有资源”、存储帐户、容器、Blob。

1. 在 " **Blob 属性**" 边栏选项卡中, 选择 "**更改层**" 按钮打开层边栏选项卡。

1. 选择 "**热**"、"**冷**" 或 "**存档**" 访问层。 如果 blob 目前处于存档状态, 并且想要解除冻结到联机层, 则还可以选择 "**标准**" 或 "**高**" 的 "解除冻结" 优先级。

1. 单击边栏选项卡底部的“确定”。

## <a name="pricing-and-billing"></a>定价和计费

所有存储帐户都根据每个 blob 的层使用块 blob 存储的定价模型。 请紧记以下计费注意事项：

- **存储成本**：除了存储的数据量，存储数据的成本将因访问层而异。 层越冷，单 GB 成本越低。
- **数据访问成本**：层越冷，数据访问费用越高。 对于冷访问层和存档访问层中的数据，需要按 GB 支付读取方面的数据访问费用。
- **事务成本**：层越冷，每个层的按事务收费越高。
- **异地复制数据传输成本**：此费用仅适用于配置了异地复制的帐户，包括 GRS 和 RA-GRS。 异地复制数据传输会产生每 GB 费用。
- **出站数据传输成本**：出站数据传输（传出 Azure 区域的数据）会按每 GB 产生带宽使用费，与通用存储帐户一致。
- **更改访问层**：更改帐户访问层将导致_访问层推导出_存储在帐户中但未设置显式层的 blob 的层变动。 有关更改单个 blob 的访问层的信息, 请参阅[blob 级别分层计费](#blob-level-tiering-billing)。

> [!NOTE]
> 有关块 blob 定价的详细信息, 请参阅[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)页。 有关出站数据传输收费的详细信息，请参阅[数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/)页。

## <a name="faq"></a>常见问题

**如果要对数据分层，是应该使用 Blob 存储帐户还是 GPv2 帐户？**

建议使用 GPv2 帐户而非 Blob 存储帐户进行分层。 GPv2 支持 Blob 存储帐户支持的所有功能，以及许多其他功能。 Blob 存储和 GPv2 的定价几乎相同，但某些新功能和价格折扣只提供给 GPv2 帐户。 GPv1 帐户不支持分层。

GPv1 和 GPv2 帐户的定价结构不同，客户在决定使用 GPv2 帐户之前，应仔细评估这二者。 只需单击一下，即可轻松地将现有的 Blob 存储或 GPv1 帐户转换为 GPv2 帐户。 有关详细信息，请参阅 [Azure 存储帐户概述](../common/storage-account-overview.md)。

**是否可以将对象存储在同一帐户的所有三个访问层（热、冷和存档）中？**

是的。 在帐户级别设置的 "**访问层**" 属性是默认帐户层, 适用于该帐户中没有显式设置层的所有对象。 但是，Blob 级别分层允许在对象级别设置访问层，不管该帐户上的访问层设置如何。 这三个访问层（热、冷或存档）中任何一层的 Blob 都可以存在于同一帐户中。

**是否可以更改 Blob 或 GPv2 存储帐户的默认访问层？**

是的, 可以通过设置存储帐户上的 "**访问层**" 属性来更改默认帐户层。 更改帐户层适用于存储在帐户中的所有不具有显式层集的对象 (例如, "热" **(推理)** 或 "**冷**")。 将帐户层从 "热" 切换到 "冷" 会对不包含 GPv2 帐户中的设置层的所有 blob 进行写操作 (每 10000), 并从 "冷" 切换到 "热" 会产生对 Blob 存储中所有 blob 的读取操作 (每 10000) 和数据检索 (以 GB 为单位) 费用和 GPv2 帐户。

**能否将默认帐户访问层设置为存档层？**

否。 只能将默认帐户访问层设置为热访问层或冷访问层。 只能在对象级别设置存档层。

**哪些区域提供了热、冷、存档访问层？**

所有区域均提供热访问层和冷访问层以及 Blob 级别的分层。 存档存储一开始只会在选定区域提供。 如需完整列表，请参阅 [Azure 产品（按区域）](https://azure.microsoft.com/regions/services/)。

**冷访问层中 Blob 的行为方式是否与热访问层中的不同？**

热访问层中 Blob 的延迟与 GPv1、GPv2 和 Blob 存储帐户中 Blob 的延迟相同。 冷访问层中 Blob 的延迟（以毫秒为单位）与 GPv1、GPv2 和 Blob 存储帐户中 Blob 的延迟类似。 存档访问层中的 Blob 在 GPv1、GPv2 和 Blob 存储帐户中有数小时的延迟。

冷访问层中的 Blob 具有的可用性服务级别 (SLA) 比存储在热访问层中的 Blob 略低。 有关详细信息，请参阅[存储的 SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)。

**热层、冷层和存档层中的操作是否相同？**

热层和冷层中的所有操作 100% 一致。 所有有效的存档操作 (包括 GetBlobProperties、GetBlobMetadata、ListBlobs、SetBlobTier 和 DeleteBlob) 均为 100%, 并且具有热和冷。 在解除冻结之前, 无法读取或修改在存档层中的 Blob 数据存档时只支持 blob 元数据读取操作。

**通过解冻方式将 Blob 从存档层移到热层或冷层时，如何确定解除冻结操作何时完成？**

在解除冻结期间，可以使用“获取 Blob 属性”操作来轮询“存档状态”属性，确认层更改的完成时间。 状态显示为“rehydrate-pending-to-hot”或“rehydrate-pending-to-cool”，具体取决于目标层。 完成后，“存档状态”属性会被删除，“访问层”Blob 属性会反映出新层是热层还是冷层。  

**设置 Blob 的层以后，何时开始按相应费率收费？**

每个 blob 始终按其“访问层”属性指示的层收费。 为 blob 设置新层后，“访问层”属性会立即为所有转换显示该新层。 但是，将 blob 从存档层解除冻结到热层或冷层可能需要几个小时。 在这种情况下，仍按存档层费率计费，直至解除冻结操作完成。解冻后“访问层”属性会反映该新层。 那时便按热层或冷层费率对该 blob 收费。

**如何确定在删除或移出冷层或存档层的 Blob 时是否会产生早期删除费用？**

在删除或移出冷层（仅限 GPv2 帐户）或存档层的任何 Blob 时，如果相应的存储时间不足 30 天（冷层）和 180 天（存档层），则会产生按比例计费的早期删除费用。 若要确定 blob 已在冷层或存档层中存储了多长时间，可以查看“访问层更改时间”blob 属性，该属性提供上次进行层更改的戳记。 如果 blob 的层从未更改, 则可以选中 "**上次修改**的 blob" 属性。 有关详细信息，请参阅[冷层和存档层的早期删除](#cool-and-archive-early-deletion)。

**哪些 Azure 工具和 SDK 支持 Blob 级别的分层和存档存储？**

Azure 门户、PowerShell 和 CLI 工具以及 .NET、Java、Python 和 Node.js 客户端库都支持 Blob 级别的分层和存档存储。  

**可以在热层、冷层和存档层中存储多少数据？**

数据存储和其他限制在帐户级别设置，不是按访问层设置。 因此，可以选择在一个层中用完所有存储配额，也可以分散用于三个层。 有关详细信息，请参阅 [Azure 存储可伸缩性和性能目标](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="next-steps"></a>后续步骤

### <a name="evaluate-hot-cool-and-archive-in-gpv2-and-blob-storage-accounts"></a>评估 GPv2 和 Blob 存储帐户的热、冷和存档

[按区域查看热层、冷层和存档层](https://azure.microsoft.com/regions/#services)

[管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)

[了解存档层中的解除冻结 blob 数据](storage-blob-rehydration.md)

[通过启用 Azure 存储度量值来评估当前存储帐户的使用情况](../common/storage-enable-and-view-metrics.md)

[按区域查看 Blob 存储帐户和 GPv2 帐户中的热层、冷层和存档层定价](https://azure.microsoft.com/pricing/details/storage/)

[检查数据传输定价](https://azure.microsoft.com/pricing/details/data-transfers/)
