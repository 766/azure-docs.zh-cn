---
title: 使用 Azure 顾问提高 Azure 应用程序的性能 | Microsoft Docs
description: 使用顾问优化 Azure 部署的性能。
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 8fdae1e12e56dcbcb56941726b0c089ad59b8fc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66254653"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>使用 Azure 顾问提高 Azure 应用程序的性能

Azure 顾问性能建议有助于提高关键业务应用程序的速度和响应能力。 可通过顾问从顾问仪表板的“性能”  选项卡获取性能建议。

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>减少流量管理器配置文件上的 DNS 生存时间，可更快地故障转移到正常运行的终结点

使用流量管理器配置文件上的[生存时间 (TTL) 设置](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations)可以指定在给定终结点停止响应查询时切换终结点的速度。 减少 TTL 值意味着客户端将更快地路由到正常运行的终结点。

Azure 顾问将识别配置了较长 TTL 的流量管理器配置文件，并建议将 TTL 配置为 20 秒或 60 秒，具体取决于配置文件是否配置为使用[快速故障转移](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)。

## <a name="improve-database-performance-with-sql-db-advisor"></a>通过 SQL DB 顾问提高数据库性能

顾问针对所有 Azure 资源提供一个一致且统一的建议视图。 它与 SQL 数据库顾问集成，提供建议以改进 SQL Azure 数据库性能。 SQL 数据库顾问通过分析使用情况历史记录来评估 SQL Azure 数据库的性能。 提供的建议最适合运行数据库典型工作负荷。

> [!NOTE]
> 若要获取建议，数据库必须具有一周左右的使用量，且该周内必须有一些一致的活动。 SQL 数据库顾问优化一致的查询模式比优化随机的突发活动更加轻松。

有关 SQL 数据库顾问的详细信息，请参阅 [SQL 数据库顾问](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)。

## <a name="improve-app-service-performance-and-reliability"></a>提高应用服务性能和可靠性

Azure 顾问集成最佳做法建议，可改善应用服务体验，并发现相关平台的功能。 以下为应用服务建议示例：
* 检测其中应用运行时已耗尽内存或 CPU 资源的实例，并提出缓解措施。
* 检测在其中并置资源（如 Web 应用和数据库）的实例，以改善性能并降低成本。

有关应用服务建议的详细信息，请参阅 [Best Practices for Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)（Azure 应用服务的最佳实践）。

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>使用托管磁盘防止磁盘 I/O 限制

顾问将识别属于即将达到其可伸缩性目标的存储帐户的虚拟机。 这种情况使得这些 VM 容易遭受 I/O 限制。 顾问会建议它们使用托管磁盘以防止性能降低。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>可以通过使用高级磁盘来提高虚拟机磁盘的性能和可靠性

顾问将识别其中的标准磁盘在存储帐户上具有大量事务的虚拟机，并建议升级到高级磁盘。 

Azure 高级存储为运行 I/O 密集型工作负荷的虚拟机提供高性能、低延迟的磁盘支持。 在固态硬盘 (SSD) 上使用高级存储帐户存储数据的虚拟机磁盘。 为使应用程序实现最佳性能，建议将任何需要高 IOPS 的虚拟机磁盘迁移到高级存储。

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>删除 SQL 数据仓库表的数据倾斜以提高查询性能

运行工作负荷时，数据倾斜可能会造成不必要的数据移动或资源瓶颈。 顾问将检测大于 15% 的分布数据倾斜，并建议重新分布数据和重新访问表分布键选择。 若要了解有关识别和删除倾斜的详细信息，请参阅[倾斜疑难解答](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)。

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>创建或更新 SQL 数据仓库表的过期表统计信息以提高查询性能

顾问标识不包含最新[表统计信息](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)的表并建议创建或更新表统计信息。 SQL 数据仓库查询优化器使用最新统计信息来估计基数或查询结果中的行数，这样一来，查询优化器能够创建高质量的查询计划，以提高性能。

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>通过纵向扩展来优化 SQL 数据仓库上的缓存利用率，从而提升查询性能

Azure 顾问会检测 SQL 数据仓库是否具有高缓存使用百分比和低命中百分比。 这种情况表示存在高缓存逐出，可能会影响 SQL 数据仓库的性能。 顾问建议你对 SQL 数据仓库进行纵向扩展以确保为工作负荷分配足够的缓存容量。

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>将 SQL 数据仓库表转换为复制表以提高查询性能

顾问会识别非复制但是会受益于转换的表，并建议转换这些表。 建议是基于 SQL 数据仓库表的复制表大小、列数、表分布类型以及分区数提出的。 还可能会根据上下文在建议中提供额外的启发。 若要详细了解此建议是如何确定的，请参阅 [SQL 数据仓库建议](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables)。 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>将存储帐户迁移到 Azure 资源管理器来获取所有最新 Azure 功能。

将存储帐户部署模型迁移到 Azure 资源管理器（资源管理器）可利用模板部署和更多的安全选项，并可升级到 GPv2 帐户来利用 Azure 存储的最新功能。 顾问将识别使用经典部署模型的任何独立存储帐户，并建议迁移到资源管理器部署模型。

> [!NOTE]
> Azure Monitor 的经典警报计划于 2019 年 6 月停用。 建议升级经典存储帐户以使用资源管理器在新平台中保留警报功能。 有关详细信息，请参阅[经典警报停用](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/)。

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>设计你的存储帐户，以免达到最大订阅限制

Azure 区域可支持最多 250 每个订阅的存储帐户。 达到限制后，将不能在该地区/订阅组合中创建任何更多的存储帐户。 顾问将检查订阅和图面上，以设计为较少的存储帐户有关的任何建议即将达到最大限制。

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>优化 Azure MySQL、 Azure PostgreSQL 和 Azure MariaDB 服务器的性能 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>CPU 瓶颈修复你的 Azure MySQL、 Azure PostgreSQL 和 Azure MariaDB 服务器的 CPU 压力
很长一段使用率很高的 cpu 可以导致工作负荷的慢查询性能。 增加 CPU 大小将帮助优化的数据库查询运行时并改进整体性能。 Azure 顾问将高的 CPU 使用率可能正在运行受约束的 CPU 的工作负荷的建议缩放计算在标识服务器。

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>减少了在 Azure MySQL、 Azure PostgreSQL 和 Azure MariaDB 服务器上的内存限制或迁移到内存优化的 SKU
低缓存命中率可能会导致速度较慢的查询性能和更高的 IOPS。 这可能是由于错误的查询计划或运行内存密集型工作负荷。 修复的查询计划或 [将内存增加](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers)的 Azure Database for PostgreSQL 数据库服务器、 Azure MySQL 数据库服务器或 Azure MariaDB 服务器，将有助于优化数据库工作负荷的执行。 Azure 顾问标识由于此高缓冲区池改动受影响的服务器，并建议修复查询计划中，转到更高的 SKU 具有更多内存或增加存储大小，以获取更多 IOPS。

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>使用 Azure MySQL 或 Azure PostgreSQL 读取副本来横向扩展读取密集型工作负荷的读取
Azure 顾问利用基于工作负荷的试探法如读取与写入服务器上标识读取密集型工作负荷在过去七天内的比率。 PostgreSQL 资源的 Azure 数据库或 MySQL 资源非常高的读/写比率的 Azure 数据库会导致查询性能变慢的 CPU 和/或内存争用。 添加 [副本](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal)中扩大到副本服务器，防止在主服务器上的 CPU 和/或内存约束的读取将有所帮助。 顾问将识别的服务器使用此类高读取密集型工作负荷并建议将添加 [读取副本](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) 来分担一些的读取工作负荷。


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>缩放到更高的 SKU，以防止连接约束 Azure MySQL、 Azure PostgreSQL 或 Azure MariaDB 服务器
每个新连接到数据库服务器占用一些内存。 在数据库服务器的性能下降，如果连接到服务器失败由于 [上限](https://docs.microsoft.com/azure/postgresql/concepts-limits)在内存中。 Azure 顾问将识别运行具有许多连接故障的服务器，并建议升级你的服务器的连接限制，以通过纵向扩展计算或使用内存优化的 Sku，其具有更多计算每个核心提供到您的服务器的更多内存。

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>你为不同大小的缓存或 SKU，以提高缓存缩放和应用程序性能

缓存实例未运行高内存压力、 高服务器负载，或高网络带宽，这可能会使其变得无响应，出现数据丢失或变得不可用时效果最佳。 顾问将确定在这种情况的缓存实例，并应用最佳实践来降低内存压力、 服务器负载或网络带宽或缩放到不同的大小或 SKU 具有更多容量的建议。

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>将区域的流量添加到你的 Azure Cosmos DB 帐户

顾问将检测到了流量，可以从当前未配置的区域的 Azure Cosmos DB 帐户，并且建议将添加到该区域。 这将改善来自该区域的请求的延迟，并确保发生区域故障时的可用性。 [了解有关使用 Azure Cosmos DB 的全局数据分布的详细信息](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>配置 Azure Cosmos DB 与客户进行索引策略包括或排除的路径

Azure 顾问将标识正在使用的默认索引策略，但无法受益于基于工作负荷模式的自定义索引策略的 Cosmos DB 容器。 所有属性编制都索引的默认索引策略，但与查询筛选器中使用的显式包含或排除路径中使用自定义索引策略可以降低 Ru，然后在为索引使用的存储。 [了解有关修改索引策略的详细信息](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>将 Azure Cosmos DB 查询页大小 (MaxItemCount) 配置为 -1 

Azure 顾问会标识 Azure Cosmos DB 容器使用的查询页大小 100，建议使用更快地扫描的页大小为-1。 [了解有关最大项计数的详细信息](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何访问顾问中的性能建议

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

2.  在顾问仪表板中，单击“性能”  选项卡。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：

* [顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-performance-recommendations.md)
* [顾问高可用性建议](advisor-high-availability-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
