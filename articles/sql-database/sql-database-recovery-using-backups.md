---
title: 从备份还原 Azure SQL 数据库 | Microsoft Docs
description: 了解有关时间点还原的信息，它让你能够将 Azure SQL 数据库回滚到之前的时间点（最多 35 天）。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 04/30/2019
ms.openlocfilehash: 55d60ec332515fcfa3deb565a4a770027681537a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566969"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>使用自动数据库备份恢复 Azure SQL 数据库

默认情况下，SQL 数据库备份存储在异地复制 blob 存储 (RA-GRS) 中。 以下选项适用于使用[自动数据库备份](sql-database-automated-backups.md)的数据库恢复：

- 在恢复到保留期内指定时间点的同一 SQL 数据库服务器上创建新数据库。
- 在恢复到已删除数据库的删除时间的同一 SQL 数据库服务器上创建数据库。
- 在恢复到最新备份点的相同区域中的任一 SQL 数据库服务器上创建新数据库。
- 在恢复到最近复制备份点的其他任何区域中的任一 SQL 数据库服务器上创建新数据库。

如果已配置[备份长期保留](sql-database-long-term-retention.md)，还可以从任一 SQL 数据库服务器上的任何 LTR 备份创建新数据库。

> [!IMPORTANT]
> 还原期间无法覆盖现有数据库。

使用“标准”或“高级”服务层级时，在遇到下列情况时，已还原数据库将产生额外的存储费用：

- 如果数据库最大大小超过 500 GB，将 P11–P15 还原为 S4-S12 或 P1–P6。
- 如果数据库最大大小超过 250 GB，将 P1–P6 还原为 S4-S12。

如果已还原数据库的最大大小超出了目标数据库服务层级和性能级别附送的存储量，将产生额外的费用。 超出附送量的额外预配存储将产生额外的费用。 有关额外存储定价的详细信息，请参阅 [SQL 数据库定价页面](https://azure.microsoft.com/pricing/details/sql-database/)。 如果实际使用的空间量小于附送的存储量，可以通过将数据库最大大小设置为附送的量，来避免产生额外的费用。

> [!NOTE]
> 在创建[数据库副本](sql-database-copy.md)时，将用到[自动数据库备份](sql-database-automated-backups.md)。

## <a name="recovery-time"></a>恢复时间

使用自动数据库备份还原数据库的恢复时间受几个因素的影响：

- 数据库的大小
- 数据库的计算大小
- 所涉及的事务日志数
- 需要重新播放以恢复到还原点的活动数量
- 还原到不同区域时的网络带宽
- 目标区域中正在处理的并行还原请求数

对于较大和/或非常活跃的数据库，还原可能要花费几个小时。 如果一个区域出现长时间的服务中断，则可能是因为存在大量正在由其他区域处理的异地还原请求。 当存在很多请求时，则可能会延长该区域中数据库的恢复时间。 大部分数据库还原操作可在 12 小时内完成。

对于单个订阅，并发还原请求的数目存在限制。  这些限制适用于时间点还原、异地还原和从长期保留备份中还原的任意组合：

| | **处理的并发请求数最多为 #** | **提交的并发请求数最多为 #** |
| :--- | --: | --: |
|单个数据库（每个订阅）|10|60|
|弹性池（每个池）|4|200|
||||

目前没有任何内置的方法可以还原整个服务器。 [Azure SQL 数据库：完全恢复服务器](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)脚本举例演示了如何完成此任务。

> [!IMPORTANT]
> 若要使用自动备份进行恢复，用户必须是订阅中的 SQL Server 参与者角色的成员或是订阅所有者 - 请参阅 [RBAC：内置角色](../role-based-access-control/built-in-roles.md)。 可以使用 Azure 门户、PowerShell 或 REST API 进行恢复。 但不能使用 Transact-SQL。

## <a name="point-in-time-restore"></a>时间点还原

可以使用 Azure 门户、[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) 或 [REST API](https://docs.microsoft.com/rest/api/sql/databases)，将独立数据库、共用数据库或实例数据库还原到早期的时间点。 请求可以指定要还原的数据库的任何服务层级或计算大小。 确保要将数据库还原到其中的服务器上有足够的资源。 完成后，将在原始数据库所在的同一台服务器上创建一个新数据库。 还原的数据库将根据其服务层级和计算大小按标准费率计费。 在数据库还原完成之前，不会产生费用。

为了恢复目的，通常会将数据库还原到一个较早的点。 可将还原的数据库视为原始数据库的替代数据库，或使用它作为源数据来更新原始数据库。

- **数据库替换**

  如果还原的数据库旨在替换原始数据库, 则应指定原始数据库的计算大小和服务层。 然后，可以使用 T-SQL 中的 [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) 命令来重命名原始数据库，并为还原的数据库指定原有的名称。

- **数据恢复**

  如果你打算从还原的数据库检索数据以从用户或应用程序错误中恢复，则需要编写并执行一个数据恢复脚本，用于从还原的数据库提取数据并将其应用到原始数据库。 尽管还原操作可能需要很长时间才能完成，但整个还原过程中，都可在数据库列表中看到还原数据库。 如果在还原期间删除数据库，将取消还原操作，则不会针对未完成还原的数据库向你收费。

要使用 Azure 门户将单一数据库、共用数据库或实例数据库恢复到某个时间点，请打开该数据库的页面，并在工具栏上单击“还原”。

![时间点还原](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> 若要以编程方式从备份还原数据库，请参阅[使用自动备份以编程方式执行恢复](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>已删除的数据库还原

使用 Azure 门户、[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) 或 [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)，可将已删除的数据库还原到同一 SQL 数据库服务器上的删除时间或早期的时间点。 可以[使用 PowerShell 在托管实例上还原已删除的数据库](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance)。 

> [!TIP]
> 有关展示了如何还原已删除数据库的示例 PowerShell 脚本，请参阅[使用 PowerShell 还原 SQL 数据库](scripts/sql-database-restore-database-powershell.md)。
> [!IMPORTANT]
> 如果删除 Azure SQL 数据库服务器实例，其所有数据库也会一并删除，并且无法恢复。 目前不支持还原已删除的服务器。

### <a name="deleted-database-restore-using-the-azure-portal"></a>使用 Azure 门户还原已删除的数据库

若要使用 Azure 门户恢复已删除的数据库，请打开服务器的页面，然后在“操作”区中单击“已删除的数据库”。

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> 若要以编程方式还原已删除的数据库，请参阅[使用自动备份以编程方式执行恢复](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>异地还原

可在任何 Azure 区域的任何服务器上从最新异地复制的备份中还原 SQL 数据库。 异地还原使用异地复制的备份作为源。 即使由于停电而无法访问数据库或数据中心，也依然能够请求此功能。

当数据库因其所在的区域发生事故而不可用时，异地还原是默认的恢复选项。 可将数据库还原到任何其他区域中的服务器。 创建备份后，将其异地复制到其他区域中的 Azure Blob 时会出现延迟。 因此，已还原的数据库最长可能滞后于原始数据库 1 小时。 下图显示的是从其他区域中的最后一个可用备份中还原数据库。

![异地还原](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> 有关展示了如何执行异地还原的示例 PowerShell 脚本，请参阅[使用 PowerShell 还原 SQL 数据库](scripts/sql-database-restore-database-powershell.md)。

当前，异地辅助数据库上不支持时间点还原。 仅主数据库支持时间点还原。 有关使用异地还原在中断后恢复的详细信息，请参阅[在中断后恢复](sql-database-disaster-recovery.md)。

> [!IMPORTANT]
> 异地还原是 SQL 数据库中提供的最基本的灾难恢复解决方案。 它依赖于自动创建的异地复制备份，其 RPO 为 1 小时，估计恢复时间最长为 12 小时。 它不保证在发生区域性的服务中断后，目标区域可提供足够的容量来还原数据库，因为此时的需求可能会急剧上升。 对于使用相对较小数据库的非业务关键型应用程序，异地还原是适当的灾难恢复解决方案。 对于使用大型数据库并且必须确保业务连续性的业务关键应用程序, 应使用[自动故障转移组](sql-database-auto-failover-group.md)。 此功能提供的 RPO 和 RTO 要低得多，并且始终可以保证容量。 有关业务连续性选项的详细信息，请参阅[业务连续性概述](sql-database-business-continuity.md)。

### <a name="geo-restore-using-the-azure-portal"></a>使用 Azure 门户进行异地还原

要在其[基于 DTU 的模型保留期](sql-database-service-tiers-dtu.md)或[基于 vCore 的模型保留期](sql-database-service-tiers-vcore.md)期间使用 Azure 门户对数据库进行异地还原，请打开 SQL 数据库页，并单击“添加”。 在“选择源”文本框中，选择“备份”。 指定在选择的区域和服务器上执行还原的备份。

> [!Note]
> 使用 Azure 门户的异地还原在托管实例中不可用。 请改用 PowerShell。

## <a name="programmatically-performing-recovery-using-automated-backups"></a>使用自动备份以编程方式执行恢复

如前所述，除了使用 Azure 门户外，还可以使用 Azure PowerShell 或 REST API 以编程方式执行数据库恢复。 下表描述了可用的命令集。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

- 若要还原独立或共用数据库, 请参阅[AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)。

  | Cmdlet | 描述 |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |获取一个或多个数据库。 |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | 获取可以还原的已删除数据库。 |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |获取数据库的异地冗余备份。 |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |还原 SQL 数据库。 |

  > [!TIP]
  > 有关展示了如何执行数据库的时间点还原的示例 PowerShell 脚本，请参阅[使用 PowerShell 还原 SQL 数据库](scripts/sql-database-restore-database-powershell.md)。

- 若要还原托管实例数据库, 请参阅[AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)。

  | Cmdlet | 描述 |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |获取一个或多个托管实例。 |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | 获取实例数据库。 |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |还原实例数据库。 |

### <a name="rest-api"></a>REST API

使用 REST API 还原单一数据库或共用数据库：

| API | 描述 |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |还原数据库 |
| [获取创建或更新数据库状态](https://docs.microsoft.com/rest/api/sql/operations) |在还原操作过程中返回状态 |

### <a name="azure-cli"></a>Azure CLI

- 若要使用 Azure CLI 还原单一数据库或共用数据库，请参阅 [az sql db restore](/cli/azure/sql/db#az-sql-db-restore)。
- 若要使用 Azure CLI 恢复托管实例, 请参阅[az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>总结

自动备份可保护数据库，使其免受用户和应用程序错误、意外的数据库删除和长时间中断的影响。 此内置的功能适用于所有服务层级和计算大小。

## <a name="next-steps"></a>后续步骤

- 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)。
- 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)。
- 若要了解长期保留，请参阅[长期保留](sql-database-long-term-retention.md)。
- 若要了解更快的恢复选项，请参阅[活动异地复制](sql-database-active-geo-replication.md)或[自动故障转移组](sql-database-auto-failover-group.md)。
