---
title: Azure SQL 数据库基于 vCore 的资源限制 - 弹性池 | Microsoft Docs
description: 本页介绍 Azure SQL 数据库中弹性池的一些常见基于 vCore 的资源限制。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 06/26/2019
ms.openlocfilehash: aa35cb1ae0ad035bdef6ff8e19cf115188ba7f3a
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640856"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-based-purchasing-model-limits"></a>使用基于 vCore 的购买模型限制的弹性池的资源限制

本文提供针对使用基于 vCore 的购买模型的 Azure SQL 数据库弹性池和共用数据库的详细资源限制。

有关基于 DTU 的购买模型限制，请参阅 [SQL 数据库基于 DTU 的资源限制 - 弹性池](sql-database-dtu-resource-limits-elastic-pools.md)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

可通过 [Azure 门户](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)、[PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases)、[Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases) 或 [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases) 设置服务层级、计算大小和存储量。

> [!IMPORTANT]
> 有关缩放指南和注意事项，请参阅[缩放弹性池](sql-database-elastic-pool-scale.md)
> [!NOTE]
> 弹性池中单个数据库的资源限制通常与池外部具有相同计算大小的单一数据库相同。 例如，GP_Gen4_1 数据库的最大并发工作进程数为 200 个。 因此，GP_Gen4_1 池中数据库的最大并发工作进程数也是 200 个。 请注意，GP_Gen4_1 池中的并发工作进程总数为 210 个。

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>“常规用途”服务层级：存储大小和计算大小

> [!IMPORTANT]
> 澳大利亚东部或巴西南部区域不再支持新的 Gen4 数据库。

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>“常规用途”服务层级：第 4 代计算平台（第 1 部分）

|计算大小|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|硬件代次|4|4|4|4|4|4|
|vCore|1|2|3|4|5|6|
|内存 (GB)|7|14|21|28|35|42|
|每个池的最大数据库数|100|200|500|500|500|500|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|不可用|不可用|不可用|不可用|不可用|不可用|
|最大数据大小 (GB)|512|756|756|1536|1536|1536|
|最大日志大小|154|227|227|461|461|461|
|TempDB 大小 (GB)|32|64|96|128|160|192|
|存储类型|高级（远程）存储|高级（远程）存储|高级（远程）存储|高级（远程）存储|高级（远程）存储|高级（远程）存储|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|目标 IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|日志速率限制 (MBps)|4.6875|9.375|14.0625|18.75|23.4375|28.125|
|每个池的最大并发工作线程数（请求数）* |210|420|630|840|1050|1260|
|每个池的最大并发登录数 * |210|420|630|840|1050|1260|
|允许的最大会话数|30000|30000|30000|30000|30000|30000|
|每个数据库的最小/最大弹性池 vCore 选项|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|副本数|1|1|1|1|1|1|
|Multi-AZ|不可用|不可用|不可用|不可用|不可用|不可用|
|读取横向扩展|不可用|不可用|不可用|不可用|不可用|不可用|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\*如需了解任何单个数据库的最大并发辅助角色数（请求数），请参阅[单一数据库资源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果弹性池使用 Gen5 且其每个数据库的最大 vCore 数为 2，则最大并发辅助角色数为 200。  如果每个数据库的最大 vCore 数为 0.5，则最大并发辅助角色数为 50，因为 Gen5 上每个 vCore 的最大并发辅助角色数为 100。  对于每个数据库的最大 vCore 设置小于 1 个 vCore 或更少的其他情况，最大并发辅助角色数会相应重新缩放。

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>“常规用途”服务层级：第 4 代计算平台（第 2 部分）

|计算大小|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|硬件代次|4|4|4|4|4|4|
|vCore|7|8|9|10|16|24|
|内存 (GB)|49|56|63|70|112|168|
|每个池的最大数据库数|500|500|500|500|500|500|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|不可用|不可用|不可用|不可用|不可用|不可用|
|最大数据大小 (GB)|1536|2048|2048|2048|3584|4096|
|最大日志大小 (GB)|461|614|614|614|1075|1229|
|TempDB 大小 (GB)|224|256|288|320|384|384|
|存储类型|高级（远程）存储|高级（远程）存储|高级（远程）存储|高级（远程）存储|高级（远程）存储|高级（远程）存储|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|目标 IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|日志速率限制 (MBps)|32.8125|37.5|37.5|37.5|37.5|37.5|
|每个池的最大并发工作线程数（请求数）*|1470|1680|1890|2100|3360|5040|
|每个池的最大并发登录数（请求数）*|1470|1680|1890|2100|3360|5040|
|允许的最大会话数|30000|30000|30000|30000|30000|30000|
|每个数据库的最小/最大弹性池 vCore 选项|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|副本数|1|1|1|1|1|1|
|Multi-AZ|不可用|不可用|不可用|不可用|不可用|不可用|
|读取横向扩展|不可用|不可用|不可用|不可用|不可用|不可用|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\*如需了解任何单个数据库的最大并发辅助角色数（请求数），请参阅[单一数据库资源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果弹性池使用 Gen5 且其每个数据库的最大 vCore 数为 2，则最大并发辅助角色数为 200。  如果每个数据库的最大 vCore 数为 0.5，则最大并发辅助角色数为 50，因为 Gen5 上每个 vCore 的最大并发辅助角色数为 100。  对于每个数据库的最大 vCore 设置小于 1 个 vCore 或更少的其他情况，最大并发辅助角色数会相应重新缩放。

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>“常规用途”服务层级：第 5 代计算平台（第 1 部分）

|计算大小|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|硬件代次|5|5|5|5|5|5|5|
|vCore|2|4|6|8|10|12|14|
|内存 (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|每个池的最大数据库数|100|200|500|500|500|500|500|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|不可用|不可用|不可用|不可用|不可用|不可用|不可用|
|最大数据大小 (GB)|512|756|756|1536|1536|1536|
|最大日志大小 (GB)|154|227|227|461|461|461|461|
|TempDB 大小 (GB)|64|128|192|256|320|384|384|
|存储类型|高级（远程）存储|高级（远程）存储|高级（远程）存储|高级（远程）存储|高级（远程）存储|高级（远程）存储|高级（远程）存储|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|目标 IOPS (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|日志速率限制 (MBps)|4.6875|9.375|14.0625|18.75|23.4375|28.125|32.8125|
|每个池的最大并发工作线程数（请求数）*|210|420|630|840|1050|1260|1470|
|每个池的最大并发登录数（请求数）*|210|420|630|840|1050|1260|1470|
|允许的最大会话数|30000|30000|30000|30000|30000|30000|30000|
|每个数据库的最小/最大弹性池 vCore 选项|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|副本数|1|1|1|1|1|1|1|
|Multi-AZ|不可用|不可用|不可用|不可用|不可用|不可用|不可用|
|读取横向扩展|不可用|不可用|不可用|不可用|不可用|不可用|不可用|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\*如需了解任何单个数据库的最大并发辅助角色数（请求数），请参阅[单一数据库资源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果弹性池使用 Gen5 且其每个数据库的最大 vCore 数为 2，则最大并发辅助角色数为 200。  如果每个数据库的最大 vCore 数为 0.5，则最大并发辅助角色数为 50，因为 Gen5 上每个 vCore 的最大并发辅助角色数为 100。  对于每个数据库的最大 vCore 设置小于 1 个 vCore 或更少的其他情况，最大并发辅助角色数会相应重新缩放。

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>“常规用途”服务层级：第 5 代计算平台（第 2 部分）

|计算大小|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|硬件代次|5|5|5|5|5|5|5|
|vCore|16|18|20|24|32|40|80|
|内存 (GB)|81.6|91.8|102|122.4|163.2|204|408|
|每个池的最大数据库数|500|500|500|500|500|500|500|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|不可用|不可用|不可用|不可用|不可用|不可用|不可用|
|最大数据大小 (GB)|2048|2048|3072|3072|4096|4096|4096|
|最大日志大小 (GB)|614|614|922|922|1229|1229|1229|
|TempDB 大小 (GB)|384|384|384|384|384|384|384|
|存储类型|高级（远程）存储|高级（远程）存储|高级（远程）存储|高级（远程）存储|高级（远程）存储|高级（远程）存储|高级（远程）存储|
|IO 延迟（近似）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|5-7 毫秒（写入）<br>5-10 毫秒（读取）|
|目标 IOPS (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|日志速率限制 (MBps)|37.5|37.5|37.5|37.5|37.5|37.5|37.5|
|每个池的最大并发工作线程数（请求数）*|1680|1890|2100|2520|33600|4200|8400|
|每个池的最大并发登录数（请求数）*|1680|1890|2100|2520|33600|4200|8400|
|每个数据库的最小/最大弹性池 vCore 选项|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|副本数|1|1|1|1|1|1|1|
|Multi-AZ|不可用|不可用|不可用|不可用|不可用|不可用|不可用|
|读取横向扩展|不可用|不可用|不可用|不可用|不可用|不可用|不可用|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\*如需了解任何单个数据库的最大并发辅助角色数（请求数），请参阅[单一数据库资源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果弹性池使用 Gen5 且其每个数据库的最大 vCore 数为 2，则最大并发辅助角色数为 200。  如果每个数据库的最大 vCore 数为 0.5，则最大并发辅助角色数为 50，因为 Gen5 上每个 vCore 的最大并发辅助角色数为 100。  对于每个数据库的最大 vCore 设置小于 1 个 vCore 或更少的其他情况，最大并发辅助角色数会相应重新缩放。

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>“业务关键”服务层级：存储大小和计算大小

> [!IMPORTANT]
> 澳大利亚东部或巴西南部区域不再支持新的 Gen4 数据库。

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>“业务关键”服务层级：第 4 代计算平台（第 1 部分）

|计算大小|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|硬件代次|4|4|4|4|4|4|
|vCore|1|2|3|4|5|6|
|内存 (GB)|7|14|21|28|35|42|
|每个池的最大数据库数|此计算大小仅支持单个 DB|50|100|100|100|100|
|列存储支持|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|1|2|3|4|5|6|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|最大数据大小 (GB)|650|650|650|650|650|650|
|最大日志大小 (GB)|195|195|195|195|195|195|
|TempDB 大小 (GB)|32|64|96|128|160|192|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|目标 IOPS (64 KB)|5000|10000|15000|20000|25000|30000|
|日志速率限制 (MBps)|10|20|30|40|50|60|
|每个池的最大并发工作线程数（请求数）*|210|420|630|840|1050|1260|
|每个池的最大并发登录数（请求数）*|210|420|630|840|1050|1260|
|允许的最大会话数|30000|30000|30000|30000|30000|30000|
|每个数据库的最小/最大弹性池 vCore 选项|不可用|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|副本数|4|4|4|4|4|4|
|Multi-AZ|是|是|是|是|是|是|
|读取横向扩展|是|是|是|是|是|是|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\*如需了解任何单个数据库的最大并发辅助角色数（请求数），请参阅[单一数据库资源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果弹性池使用 Gen5 且其每个数据库的最大 vCore 数为 2，则最大并发辅助角色数为 200。  如果每个数据库的最大 vCore 数为 0.5，则最大并发辅助角色数为 50，因为 Gen5 上每个 vCore 的最大并发辅助角色数为 100。  对于每个数据库的最大 vCore 设置小于 1 个 vCore 或更少的其他情况，最大并发辅助角色数会相应重新缩放。

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>“业务关键”服务层级：第 4 代计算平台（第 2 部分）

|计算大小|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|硬件代次|4|4|4|4|4|4|
|vCore|7|8|9|10|16|24|
|内存 (GB)|81.6|91.8|102|122.4|163.2|204|
|每个池的最大数据库数|100|100|100|100|100|100|
|列存储支持|不可用|不可用|不可用|不可用|不可用|不可用|
|内存中 OLTP 存储 (GB)|7|8|9.5|11|20|36|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|最大数据大小 (GB)|650|650|650|650|1024|1024|
|最大日志大小 (GB)|195|195|195|195|307|307|
|TempDB 大小 (GB)|224|256|288|320|384|384|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|目标 IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|日志速率限制 (MBps)|70|80|80|80|80|80|
|每个池的最大并发工作线程数（请求数）*|1470|1680|1890|2100|3360|5040|
|每个池的最大并发登录数（请求数）*|1470|1680|1890|2100|3360|5040|
|允许的最大会话数|30000|30000|30000|30000|30000|30000|
|每个数据库的最小/最大弹性池 vCore 选项|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|副本数|4|4|4|4|4|4|
|Multi-AZ|是|是|是|是|是|是|
|读取横向扩展|是|是|是|是|是|是|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\*如需了解任何单个数据库的最大并发辅助角色数（请求数），请参阅[单一数据库资源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果弹性池使用 Gen5 且其每个数据库的最大 vCore 数为 2，则最大并发辅助角色数为 200。  如果每个数据库的最大 vCore 数为 0.5，则最大并发辅助角色数为 50，因为 Gen5 上每个 vCore 的最大并发辅助角色数为 100。  对于每个数据库的最大 vCore 设置小于 1 个 vCore 或更少的其他情况，最大并发辅助角色数会相应重新缩放。

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>“业务关键”服务层级：第 5 代计算平台（第 1 部分）

|计算大小|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|硬件代次|5|5|5|5|5|5|5|
|vCore|2|4|6|8|10|12|14|
|内存 (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|每个池的最大数据库数|此计算大小仅支持单个 DB|50|100|100|100|100|100|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|1.571|3.142|4.713|6.284|8.655|11.026|13.397|
|最大数据大小 (GB)|1024|1024|1536|1536|1536|3072|3072|
|最大日志大小 (GB)|307|307|307|461|461|922|922|
|TempDB 大小 (GB)|64|128|192|256|320|384|384|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|目标 IOPS (64 KB)|5000|10000|15000|20000|25000|30000|35000|
|日志速率限制 (MBps)|15|30|45|60|75|90|105|
|每个池的最大并发工作线程数（请求数）*|210|420|630|840|1050|1260|1470|
|每个池的最大并发登录数（请求数）*|210|420|630|840|1050|1260|1470|
|允许的最大会话数|30000|30000|30000|30000|30000|30000|30000|
|每个数据库的最小/最大弹性池 vCore 选项|不可用|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|副本数|4|4|4|4|4|4|4|
|Multi-AZ|是|是|是|是|是|是|
|读取横向扩展|是|是|是|是|是|是|是|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\*如需了解任何单个数据库的最大并发辅助角色数（请求数），请参阅[单一数据库资源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果弹性池使用 Gen5 且其每个数据库的最大 vCore 数为 2，则最大并发辅助角色数为 200。  如果每个数据库的最大 vCore 数为 0.5，则最大并发辅助角色数为 50，因为 Gen5 上每个 vCore 的最大并发辅助角色数为 100。  对于每个数据库的最大 vCore 设置小于 1 个 vCore 或更少的其他情况，最大并发辅助角色数会相应重新缩放。

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>“业务关键”服务层级：第 5 代计算平台（第 2 部分）

|计算大小|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|硬件代次|5|5|5|5|5|5|5|
|vCore|16|18|20|24|32|40|80|
|内存 (GB)|81.6|91.8|102|122.4|163.2|204|408|
|每个池的最大数据库数|100|100|100|100|100|100|100|
|列存储支持|是|是|是|是|是|是|是|
|内存中 OLTP 存储 (GB)|15.768|18.139|20.51|25.252|37.936|52.22|131.64|
|最大数据大小 (GB)|3072|3072|3072|4096|4096|4096|4096|
|最大日志大小 (GB)|922|922|922|1229|1229|1229|1229|
|TempDB 大小 (GB)|384|384|384|384|384|384|384|
|存储类型|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|本地 SSD|
|IO 延迟（近似）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|1-2 毫秒（写入）<br>1-2 毫秒（读取）|
|目标 IOPS (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|日志速率限制 (MBps)|120|120|120|120|120|120|120|
|每个池的最大并发工作线程数（请求数）*|1680|1890|2100|2520|3360|4200|8400|
|每个池的最大并发登录数（请求数）*|1680|1890|2100|2520|3360|4200|8400|
|允许的最大会话数|30000|30000|30000|30000|30000|30000|30000|
|每个数据库的最小/最大弹性池 vCore 选项|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|副本数|4|4|4|4|4|4|4|
|Multi-AZ|是|是|是|是|是|是|是|
|读取横向扩展|是|是|是|是|是|是|是|
|随附的备份存储|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|1 倍数据库大小|

\*如需了解任何单个数据库的最大并发辅助角色数（请求数），请参阅[单一数据库资源限制](sql-database-vcore-resource-limits-single-databases.md)。 例如，如果弹性池使用 Gen5 且其每个数据库的最大 vCore 数为 2，则最大并发辅助角色数为 200。  如果每个数据库的最大 vCore 数为 0.5，则最大并发辅助角色数为 50，因为 Gen5 上每个 vCore 的最大并发辅助角色数为 100。  对于每个数据库的最大 vCore 设置小于 1 个 vCore 或更少的其他情况，最大并发辅助角色数会相应重新缩放。

如果弹性池的所有 vCore 繁忙，则池中的每个数据库将接收相同数量的计算资源来处理查询。 SQL 数据库服务通过确保相等的计算时间片，在数据库之间提供资源共享的公平性。 弹性池资源共享公平性是在将每个数据库的 vCore 最小值设为非零值时，对另外为每个数据库保证的任意资源量的补充。

## <a name="database-properties-for-pooled-databases"></a>共用数据库的数据库属性

下表介绍了共用数据库的属性。

| 属性 | 描述 |
|:--- |:--- |
| 每个数据库的最大 vCore 数 |根据池中其他数据库的 vCore 使用率，池中任何数据库可以使用的 vCore 的最大数目。 每个数据库的 vCore 上限并不是数据库的资源保障。 此设置是应用于池中所有数据库的全局设置。 将每个数据库的最大 vCore 数设置得足够高，以处理数据库使用高峰情况。 因为池通常会假定数据库存在热使用模式和冷使用模式，在这些模式中并非所有数据库同时处于高峰使用状态，所以预期会存在某种程度的过量使用情况。|
| 每个数据库的最小 vCore 数 |池中任何数据库可以保证的 vCore 最小数目。 此设置是应用于池中所有数据库的全局设置。 每个数据库的最小 vCore 可能设为 0，这也是默认值。 该属性值可以设置为介于 0 和每个数据库的平均 vCore 使用量之间的任意值。 池中数据库数目和每个数据库的 vCore 下限的积不能超过每个池的 vCore 数。|
| 每个数据库的最大存储 |用户为池中的数据库设置的最大数据库大小。 共用数据库共享分配的池存储，因此数据库可以访问的大小限制为剩余的池存储与数据库大小中的较小者。 最大数据库大小是指数据文件的最大大小，不包括日志文件使用的空间。 |
|||

## <a name="next-steps"></a>后续步骤

- 有关单一数据库的 vCore 资源限制，请参阅[使用基于 vCore 的购买模型的单一数据库的资源限制](sql-database-vcore-resource-limits-single-databases.md)
- 有关单一数据库的 DTU 资源限制，请参阅[使用基于 DTU 的购买模型的单一数据库的资源限制](sql-database-dtu-resource-limits-single-databases.md)
- 有关弹性池的 DTU 资源限制，请参阅[使用基于 DTU 的购买模型的弹性池的资源限制](sql-database-dtu-resource-limits-elastic-pools.md)
- 有关托管实例的资源限制，请参阅[托管实例资源限制](sql-database-managed-instance-resource-limits.md)。
- 有关常规 Azure 限制的相关信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-subscription-service-limits.md)。
- 有关数据库服务器上的资源限制的信息，请参阅 [SQL 数据库服务器资源限制概述](sql-database-resource-limits-database-server.md)了解有关服务器级别和订阅级别限制的信息。
