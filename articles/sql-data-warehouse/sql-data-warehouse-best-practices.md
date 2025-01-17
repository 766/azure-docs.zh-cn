---
title: Azure SQL 数据仓库最佳实践 | Microsoft Docs
description: 开发 Azure SQL 数据仓库解决方案时应了解的建议和最佳实践。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/26/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9c9e293a6e9c8126f2b82f68d591aee56ec32aec
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "67672280"
---
# <a name="best-practices-for-azure-sql-data-warehouse"></a>Azure SQL 数据仓库最佳实践
本文包含一系列最佳实践，可帮助你从 Azure SQL 数据仓库获得最佳性能。  本文的有些概念很基本且很容易解释，而有些概念则相对高级，本文只对其进行大致介绍。  本文的目的是提供一些基本指导，让用户在生成数据仓库时更加关注那些重要的方面。  每部分都介绍一个概念，并提供哪里可以阅读深度介绍的详细文章。

如果刚开始使用 Azure SQL 数据仓库，千万别让本文吓到。  主题的顺序主要按照重要性排列。  如果一开始就关注头几个概念，则效果应该不错。  随着使用 SQL 数据仓库更加熟悉和熟悉, 请返回并查看几个其他概念。  融会贯通不需要很长时间。

有关加载指南，请参阅[加载数据的指南](guidance-for-loading-data.md)。

## <a name="reduce-cost-with-pause-and-scale"></a>使用暂停和缩放来降低成本
若要了解如何通过暂停和缩放来降低成本，请参阅[管理计算](sql-data-warehouse-manage-compute-overview.md)。 


## <a name="maintain-statistics"></a>维护统计信息
不同于 SQL Server（会自动检测列并创建或更新列的统计信息），SQL 数据仓库需要手动维护统计信息。  我们计划在将来改进这一点，但现在仍需要维护统计信息，以确保 SQL 数据仓库的计划优化。  优化工具创建的计划只能使用可用的统计信息。  **创建每个列的模板统计信息是开始使用统计信息的简单方式。**  更新统计信息和对数据做重大更改一样重要。  保守的做法是每天或每次加载之后更新统计信息。  创建和更新统计信息的性能与成本之间总有一些取舍。 如果发现维护所有统计信息所需时间太长，可能要更谨慎选择哪些列要进行统计信息、哪些列需要频繁更新。  例如，可能想要更新每天都要添加新值的日期列。 **对涉及联接的列、WHERE 子句中使用的列、在 GROUP BY 中找到的列进行信息统计，可以获得最大效益。**

另请参阅[管理表统计信息][Manage table statistics]、[CREATE STATISTICS][CREATE STATISTICS]、[UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="group-insert-statements-into-batches"></a>将 INSERT 语句分组为批
使用 INSERT 语句一次性载入小型表、甚至定期重新加载查找，可能与使用类似于 `INSERT INTO MyLookup VALUES (1, 'Type 1')` 的语句一样正常执行。  但是，如果一整天都要加载数千或数百万个行，可能发现单个 INSERT 跟不上要求。  请开发自己的可写入文件的进程，并开发另一个进程定期处理并加载此文件。

另请参阅 [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>使用 PolyBase 快速加载和导出数据
SQL 数据仓库支持通过多种工具（包括 Azure 数据工厂、PolyBase、BCP）来加载和导出数据。  对于少量的数据，性能不是那么重要，任何工具都可以满足需求。  但是，当要加载或导出大量数据，或者需要快速的性能时，PolyBase 是最佳选择。  PolyBase 使用 SQL 数据仓库的 MPP（大规模并行处理）体系结构，因此加载和导出巨量数据的速度比其他任何工具更快。  可使用 CTAS 或 INSERT INTO 来运行 PolyBase 加载。  **使用 CTAS 可以减少事务日志记录，是加载数据最快的方法。**  Azure 数据工厂还支持 PolyBase 加载，并且可以实现与 CTAS 类似的性能。  PolyBase 支持各种不同的文件格式，包括 Gzip 文件。  **要在使用 gzip 文本文件时获得最大的吞吐量，请将文件分成 60 个以上的文件让加载有最大化的并行度。**  若要更快的总吞吐量，请考虑并行加载数据。

另请参阅[加载数据][Load data]、[PolyBase 使用指南][Guide for using PolyBase]、[Azure SQL Data Warehouse loading patterns and strategies][Azure SQL Data Warehouse loading patterns and strategies]（Azure SQL 数据仓库加载模式和策略）、[使用 Azure 数据工厂加载数据][Load Data with Azure Data Factory]、[使用 Azure 数据工厂移动数据][Move data with Azure Data Factory]、[CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT]、[Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>加载并查询外部表
虽然 Polybase（也称外部表）可以最快速地加载数据，但并不特别适合查询。 SQL 数据仓库 Polybase 表目前只支持 Azure Blob 文件和 Azure Data Lake 存储。 这些文件并没有任何计算资源的支持。  因此，SQL 数据仓库无法卸载此工作，因此必须读取整个文件，方法是将其加载到 tempdb 来读取数据。  因此，如果有多个查询需要查询此数据，则最好是先加载一次此数据，然后让查询使用本地表。

另请参阅 [Guide for using PolyBase][Guide for using PolyBase]（PolyBase 使用指南）

## <a name="hash-distribute-large-tables"></a>哈希分布大型表
默认情况下，表是以轮循机制分布的。  这可让用户更容易开始创建表，而不必确定应该如何分布其表。  轮循机制表在某些工作负荷中执行良好，但大多数情况下，选择分布列的执行性能将更好。  按列分布表的性能远远高于轮循机制表的最常见例子是联接两个大型事实表。  例如，如果有一个依 order_id 分布的订单表，以及一个也是依 order_id 分布的事务表，如果将订单数据联接到事务表上的 order_id，此查询将变成传递查询，也就是数据移动操作会被消除。  减少步骤意味着加快查询速度。  更少的数据移动也将让查询更快。  这种解释较为粗略。 加载分布的表时，请确保传入数据的分布键没有排序，因为这会拖慢加载速度。  有关选择分布列如何能提升性能，以及如何在 CREATE TABLE 语句的 WITH 子句中定义分布表的详细信息，请参阅以下链接。

另请参阅[表概述][Table overview]、[表数据分布][Table distribution]、[Selecting table distribution][Selecting table distribution]（选择表数据分布）、[CREATE TABLE][CREATE TABLE]、[CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>不要过度分区
尽管数据分区可以让数据维护变得有效率（通过分区切换或优化扫描将分区消除），太多的分区将让查询变慢。  通常在 SQL Server 上运行良好的高数据粒度分区策略可能无法在 SQL 数据仓库上正常工作。  如果每个分区的行数少于 1 百万，太多分区还会降低聚集列存储索引的效率。  请记住，SQL 数据仓库数据在幕后将数据分区成 60 个数据库，因此如果创建包含 100 个分区的表，实际上将导致 6000 个分区。  每个工作负荷都不同，因此最佳建议是尝试不同的分区，找出最适合工作负荷的分区。  请考虑比 SQL Server 上运行良好的数据粒度更低的粒度。  例如，考虑使用每周或每月分区，而不是每日分区。

另请参阅[表分区][Table partitioning]

## <a name="minimize-transaction-sizes"></a>最小化事务大小
在事务中运行的 INSERT、UPDATE、DELETE 语句，失败时必须回滚。  为了将长时间回滚的可能性降到最低，请尽可能将事务大小最小化。  这可以通过将 INSERT、UPDATE、DELETE 语句分成小部分来达成。  例如，如果预期 INSERT 需要 1 小时，可能的话，将 INSERT 分成 4 个部分，每个运行 15 分钟。  使用特殊的最低限度日志记录方案，像是 CTAS、TRUNCATE、DROP TABLE 或 INSERT 空表，来降低回滚的风险。  另一个消除回滚的作法是使用“仅元数据”操作（像是分区切换）进行数据管理。  例如，不要运行 DELETE 语句来删除表中所有 order_date 为 2001 年 10 月的行，而是将数据每月分区后，再从另一个表将有空分区之数据的分区调动出来（请参阅 ALTER TABLE 示例）。  针对未分区的表，请考虑使用 CTAS 将想要保留的数据写入表中，而不是使用 DELETE。  如果 CTAS 需要的时间一样长，则较安全的操作，是在它具有极小事务记录的条件下运行它，且必要时可以快速地取消。

另请参阅[了解事务][Understanding transactions]、[优化事务][Optimizing transactions]、[表分区][Table partitioning]、[TRUNCATE TABLE][TRUNCATE TABLE]、[ALTER TABLE][ALTER TABLE]、[Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="use-the-smallest-possible-column-size"></a>使用最小可能的列大小
在定义 DDL 时，使用可支持数据的最小数据类型，能够改善查询性能。  这对 CHAR 和 VARCHAR 列尤其重要。  如果列中最长的值是 25 个字符，请将列定义为 VARCHAR(25)。  避免将所有字符列定义为较大的默认长度。  此外，将列定义为 VARCHAR（当它只需要这样的大小时）而非 NVARCHAR。

另请参阅[表概述][Table overview]、[表数据类型][Table data types]、[CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>对暂时性数据使用临时堆表
当在 SQL 数据仓库上暂时登录数据时，可能发现使用堆表可让整个过程更快速。  如果加载数据只是在做运行更多转换之前的预备，将表载入堆表会远快于将数据载入聚集列存储表。  此外，将数据载入临时表也比将表载入永久存储更快速。  临时表以“#”开头，且只有创建它的会话才能访问它，因此只能在受限情况下使用。   堆表在 CREATE TABLE 的 WITH 子句中定义。  如果使用临时表，请记得同时在该临时表上创建统计信息。

另请参阅[临时表][Temporary tables]、[CREATE TABLE][CREATE TABLE]、[CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>优化聚集列存储表
聚集列存储索引是将数据存储在 SQL 数据仓库中最有效率的方式之一。  默认情况下，SQL 数据仓库中的表将创建为聚集列存储。  为了让列存储表的查询获得最佳性能，良好的分段质量很重要。  当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  压缩行组中的行数可以测量分段质量。  有关检测和改善聚集列存储表分段质量的分步说明，请参阅[表索引][Table indexes]一文中的[列存储索引质量不佳的原因][Causes of poor columnstore index quality]。  由于高质量列存储段很重要，因此可以考虑使用用来加载数据的中型或大型资源类中的用户 ID。 使用较低的[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)值意味着需要向加载用户分配较大的资源类。

由于列存储表通常要等到每个表有超过 1 百万个行之后才会数据推送到压缩的列存储区段，并且每个 SQL 数据仓库表分区成 60 个表，根据经验法则，列存储表对于查询没有好处，除非表有超过 6 千万行。  小于 6 千万列的表使用列存储索引似乎不太合理，  但也无伤大雅。  此外，如果将分区，则要考虑的是每个分区必须有 1 百万个行，使用聚集列存储索引才有益。  如果表有 100 个分区，则它至少必须拥有 60 亿个行才将受益于聚集列存储（60 个分布区 * 100 个分区 * 1 百万行）。  如果表在本示例中并没有 60 亿个行，请减少分区数目，或考虑改用堆表。  使用辅助索引配合堆表而不是列存储表，也可能是值得进行的实验，看看是否可以获得较佳的性能。

查询列存储表时，如果只选择需要的列，查询运行将更快速。  

另请参阅[表索引][Table indexes]、[列存储索引指南][Columnstore indexes guide]、[重新生成列存储索引][Rebuilding columnstore indexes]

## <a name="use-larger-resource-class-to-improve-query-performance"></a>使用较大的资源类来改善查询性能
SQL 数据仓库使用资源组作为将内存分配给查询的一种方式。  默认情况下，所有用户都分配有小型资源类，此类授予每个分布区 100 MB 内存。  因为永远将有 60 个分布区，每个分布区有至少 100 MB，整个系统的总内存分配为 6000 MB 或者刚好接近6 GB。  有些查询，例如大型联接或载入聚集列存储表，将受益于较大的内存分配。  某些查询，例如纯扫描，则不会获得任何好处。  另一方面，使用较大的资源类会影响并行访问，因此将所有的用户移到大型资源类之前，要先将这一点纳入考虑。

另请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>使用较小的资源类来增加并发性
如果注意到用户查询似乎长时间延迟，可能是用户在较大资源类中运行，占用大量的并发性位置，而导致其他查询排入队列。  若要确认用户的查询是否被排入队列，请运行 `SELECT * FROM sys.dm_pdw_waits` 来看是否返回了任何行。

另请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md), [sys.databases _pdw_waits][sys.dm_pdw_waits]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>使用 DMV 来监视和优化查询
SQL 数据仓库有多个 DMV 可用于监视查询执行。  以下监视相关文章逐步说明了如何查看正在执行的查询的详细信息。  若要在这些 DMV 中快速找到查询，可在查询中使用 LABEL 选项。

另请参阅[使用 DMV 监视工作负荷][Monitor your workload using DMVs]、[LABEL][LABEL]、[OPTION][OPTION]、[sys.dm_exec_sessions][sys.dm_exec_sessions]、[sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests]、[sys.dm_pdw_request_steps][sys.dm_pdw_request_steps]、[sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests]、[sys.dm_pdw_dms_workers]、[DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN]、[sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>其他资源
另请参阅[故障诊断][Troubleshooting]一文，了解常见的问题和解决方案。

如果在本文中没有找到所需内容，可尝试使用本页面左侧的“搜索文档”来搜索所有 Azure SQL 数据仓库文档。  在[AZURE Sql 数据仓库论坛][Azure SQL Data Warehouse MSDN Forum]中, 您可以向其他用户和 SQL 数据仓库产品组提出问题。  我们会主动观察此论坛，确保用户的问题获得其他用户或我们的回答。  若要提问有关堆栈溢出的问题，请访问 [Azure SQL 数据仓库堆栈溢出论坛][Azure SQL Data Warehouse Stack Overflow Forum]。

最后，如需提出功能方面的请求，请使用 [Azure SQL 数据仓库反馈][Azure SQL Data Warehouse Feedback]页。  添加请求或对其他请求投赞成票对我们确定功能的优先级有很大的帮助。

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
