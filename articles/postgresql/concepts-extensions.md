---
title: 使用 Azure Database for PostgreSQL（单一服务器）中的 PostgreSQL 扩展
description: 介绍有关使用 Azure Database for PostgreSQL（单一服务器）中的扩展来扩展数据库功能的功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/23/2019
ms.openlocfilehash: 93cc02fafcfa153c452f37c2bc69bb47e2629f1d
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998067"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL（单一服务器）中的 PostgreSQL 扩展
PostgreSQL 支持使用扩展来扩展数据的功能。 扩展将多个相关的 SQL 对象组合在一起, 可以使用单个命令在数据库中加载或删除单个包。 在数据库中加载后, 扩展功能类似于内置功能。

## <a name="how-to-use-postgresql-extensions"></a>如何使用 PostgreSQL 扩展
必须先在数据库中安装 PostgreSQL 扩展，然后才能使用它们。 若要安装特定扩展，请通过 psql 工具运行  [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html)  命令，将打包的对象加载到数据库中。

Azure Database for PostgreSQL 支持下面列出的一小部分关键扩展。 不支持超出列出的扩展名。 你无法在 Azure Database for PostgreSQL 中创建你自己的扩展。

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 支持的扩展
下表列出了用于 PostgreSQL 的 Azure 数据库目前支持的标准 PostgreSQL 扩展。 还可以通过运行 `SELECT * FROM pg_available_extensions;` 获取此信息。

### <a name="data-types-extensions"></a>数据类型扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | 提供用于自动加密密码的数据类型。 |
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | 提供不区分大小写的字符串类型。 |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | 提供用于多维数据集的数据类型。 |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | 提供用于存储键/值对集的数据类型。 |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | 提供用于国际产品编号标准的数据类型。 |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | 提供用于分层树形结构的数据类型。 |

### <a name="functions-extensions"></a>函数扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | 提供一种计算地球表面上的大圆距离的方法。 |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 提供多个函数，用于确定字符串间的相似性和差异。 |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | 提供用于操作无 null 整数数组的函数和运算符。 |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 提供加密函数。 |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 按时间或 ID 管理已分区表。 |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | 提供函数和运算符，用于基于三元匹配确定字母数字文本的相似性。 |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | 提供可操作整个表（包括交叉表）的函数。 |
> | [uuid ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | 生成全局唯一标识符 (UUID)。 （请参阅下文了解此扩展的说明）。 |
> | [orafce](https://github.com/orafce/orafce) | 提供部分通过商业数据库模拟的函数和包。 |

### <a name="full-text-search-extensions"></a>全文搜索扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | 提供用于整数的文本搜索字典模板。 |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | 删除了词素中重音（附加符号）的文本搜索字典。 |

### <a name="index-types-extensions"></a>索引类型扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | 提供示例 GIN 运算符类，该类对特定数据类型实现类似 B-tree 的行为。 |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | 提供实施 B-tree 的 GiST 索引运算符类。 |

### <a name="language-extensions"></a>语言扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL 可加载过程语言。 |
> | [plv8](https://plv8.github.io/) | 可用于存储过程、触发器等的 PostgreSQL 的 Javascript 语言扩展。 |

### <a name="miscellaneous-extensions"></a>其他扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | 提供一种方法用于实时检查共享缓冲区缓存的当前状况。 |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | 提供一种方法用于将相关数据加载到缓冲区缓存中。 |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | 提供一种方法用于跟踪服务器执行的所有 SQL 语句的执行统计信息。 （请参阅下文了解此扩展的说明）。 |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | 提供一种显示行级锁定信息的方法。 |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | 提供一种显示元组级别统计信息的方法。 |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 外部数据包装器，用于访问外部 PostgreSQL 服务器中存储的数据。 （请参阅下文了解此扩展的说明）。|
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | 提供了一种创建不耗费 CPU 或磁盘的假设索引的方法。 |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | 一个支持从数据库会话中连接到其他 PostgreSQL 数据库的模块。 （请参阅下文了解此扩展的说明）。 |


### <a name="postgis-extensions"></a>PostGIS 扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | PostgreSQL 的空间和地理对象。 |
> | address\_standardizer, address\_standardizer\_data\_us | 用于将地址分析成构成元素。 用于支持地理编码地址规范化步骤。 |
> | [pgrouting](https://pgrouting.org/) | 扩展 PostGIS / PostgreSQL 地理空间数据库，以提供地理空间路由功能。 |


### <a name="time-series-extensions"></a>时序扩展

> [!div class="mx-tableFixed"]
> | **扩展** | **说明** |
> |---|---|
> | [TimescaleDB](https://docs.timescale.com/latest) | 一个时序 SQL 数据库，支持通过自动分区来加快引入和查询速度。 提供以时间为导向的分析功能、优化，并根据时序工作负荷来缩放 PostgreSQL。 TimescaleDB 是由 [Timescale, Inc.](https://www.timescale.com/) 开发的的，是其注册商标。（请参阅下文了解此扩展的说明）。 |

## <a name="postgres-11-extensions"></a>Postgres 11 扩展

以下扩展在 Postgres 版本为11的 Azure Database for PostgreSQL 服务器中可用。

> [!div class="mx-tableFixed"]
> | **扩展**| **扩展版本** | **说明** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 用于将地址分析成构成元素。 |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US dataset 示例|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | 对 GIN 中常见数据类型的索引的支持|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | 对 GiST 中常见数据类型的索引的支持|
> |[citext](https://www.postgresql.org/docs/11/static/citext.html)                       | 1.5             | 不区分大小写的字符串的数据类型|
> |[cube](https://www.postgresql.org/docs/11/static/cube.html)                         | 1.4             | 多维数据集的数据类型|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | 从数据库内连接到其他 PostgreSQL 数据库|
> |[dict_int](https://www.postgresql.org/docs/11/static/dict-int.html)                     | 1.0             | 整数的文本搜索字典模板|
> |[earthdistance](https://www.postgresql.org/docs/11/static/earthdistance.html)                | 1.1             | 计算地球表面上的大圆距离|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/static/fuzzystrmatch.html)                | 1.1             | 确定两个字符串之间的相似性和距离|
> |[hstore](https://www.postgresql.org/docs/11/static/hstore.html)                       | 1.5             | 用于存储 (key, value) 对集的数据类型|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | PostgreSQL 的假设索引|
> |[intarray](https://www.postgresql.org/docs/11/static/intarray.html)                     | 1.2             | 对一个二维整数数组的函数、运算符和索引的支持|
> |[isn](https://www.postgresql.org/docs/11/static/isn.html)                          | 1.2             | 国际产品编号标准的数据类型|
> |[ltree](https://www.postgresql.org/docs/11/static/ltree.html)                        | 1.1             | 分层树形结构的数据类型|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 用于从商业 RDBMS 模拟函数和包子集的函数和运算符|
> |[pgcrypto](https://www.postgresql.org/docs/11/static/pgcrypto.html)                     | 1.3             | 加密函数|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting 扩展|
> |[pgrowlocks](https://www.postgresql.org/docs/11/static/pgrowlocks.html)                   | 1.2             | 显示行级锁定信息|
> |[pgstattuple](https://www.postgresql.org/docs/11/static/pgstattuple.html)                  | 1.5             | 显示元组级别统计信息|
> |[pg_buffercache](https://www.postgresql.org/docs/11/static/pgbuffercache.html)               | 1.3             | 检查共享缓冲区缓存|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | 用于按时间或 ID 管理已分区表的扩展|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | prewarm 关系数据|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/static/pgstatstatements.html)           | 1.6             | 跟踪执行的所有 SQL 语句的执行统计信息|
> |[pg_trgm](https://www.postgresql.org/docs/11/static/pgtrgm.html)                      | 1.4             | 基于 trigrams 的文本相似性度量和索引搜索|
> |[plpgsql](https://www.postgresql.org/docs/11/static/plpgsql.html)                      | 1.0             | PL/pgSQL 过程语言|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (v8) 受信任的过程语言|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometry、geography 和光栅空间类型和函数|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL 函数|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tiger geocoder and reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS 拓扑空间类型和函数|
> |[postgres_fdw](https://www.postgresql.org/docs/11/static/postgres-fdw.html)                 | 1.0             | 远程 PostgreSQL 服务器的外部数据包装器|
> |[tablefunc](https://www.postgresql.org/docs/11/static/tablefunc.html)                    | 1.0             | 操作整个表 (包括交叉表) 的函数|
> |[unaccent](https://www.postgresql.org/docs/11/static/unaccent.html)                     | 1.1             | 删除重音的文本搜索字典|
> |[uuid ossp](https://www.postgresql.org/docs/11/static/uuid-ossp.html)                    | 1.1             | 生成全局唯一标识符 (Uuid)|


## <a name="pg_stat_statements"></a>pg_stat_statements
Pg_stat_statements 扩展在每个 Azure Database for PostgreSQL 服务器上预加载, 为你提供一种跟踪 SQL 语句执行统计信息的方法。
设置 `pg_stat_statements.track`，它可以控制哪些语句由扩展计数，默认为 `top`，这意味着跟踪所有由客户端直接发布的语句。 另外两个跟踪级别为 `none` 和 `all`。 此设置可通过 [Azure 门户](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)或 [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli) 作为服务器参数进行配置。

查询执行信息 pg_stat_statements 提供的权限与记录每个 SQL 语句时对服务器性能的影响之间存在权衡。 如果不经常使用 pg_stat_statements 扩展，则建议将 `pg_stat_statements.track` 设置为 `none`。 请注意，某些第三方监视服务可能依赖 pg_stat_statements 来提供查询性能见解，因此，请确认这是否适合你。

## <a name="dblink-and-postgres_fdw"></a>dblink 和 postgres_fdw
dblink 和 postgres_fdw 允许从一台 PostgreSQL 服务器连接到另一台服务器，或者连接到同一服务器中的另一个数据库。 接收服务器需要允许来自发送服务器的连接通过其防火墙。 当使用这些扩展在 Azure Database for PostgreSQL 服务器之间进行连接时，可以通过将“允许访问 Azure 服务”设置为“开启”来实现此目的。 如果希望使用扩展来环回到同一服务器，也需要进行此设置。 可以在 Postgres 服务器的 Azure 门户页面中的“连接安全性”下找到“允许访问 Azure 服务”设置。 启用 "允许访问 Azure 服务" 时, 将所有 Azure Ip 置于允许列表中。

目前不支持从 Azure Database for PostgreSQL 进行出站连接，连接到其他 Azure Database for PostgreSQL 服务器的情况除外。

## <a name="uuid"></a>uuid
如果计划使用 uuid-ossp 扩展中的 `uuid_generate_v4()`，请考虑将其与 pgcrypto 扩展中的 `gen_random_uuid()` 进行比较，以了解性能优势。


## <a name="timescaledb"></a>TimescaleDB
TimescaleDB 是一个时序数据库，已作为 PostgreSQL 的扩展打包。 TimescaleDB 提供以时间为导向的分析功能、优化，并根据时序工作负荷来缩放 Postgres。

[详细了解 TimescaleDB](https://docs.timescale.com/latest)，[Timescale, Inc.](https://www.timescale.com/) 的注册商标。

### <a name="installing-timescaledb"></a>安装 TimescaleDB
若要安装 TimescaleDB，需将其包括在服务器的共享预加载库中。 更改 Postgres 的 `shared_preload_libraries` 参数需要**重启服务器**才能生效。 可以使用 [Azure 门户](howto-configure-server-parameters-using-portal.md)或 [Azure CLI](howto-configure-server-parameters-using-cli.md) 更改参数。

> [!NOTE]
> 可以在 Azure Database for PostgreSQL 版本 9.6 和 10 上启用 TimescaleDB

使用 [Azure 门户](https://portal.azure.com/)：

1. 选择你的 Azure Database for PostgreSQL 服务器。

2. 在侧栏中选择“服务器参数”。

3. 搜索 `shared_preload_libraries` 参数。

4. 选择“TimescaleDB”。

5. 选择“保存”，保留所做的更改。 保存更改后会获得通知。 

6. 获得通知后，请**重启**服务器以应用这些更改。 若要了解如何重启服务器，请参阅[重启 Azure Database for PostgreSQL 服务器](howto-restart-server-portal.md)。


现在可以在 Postgres 数据库中启用 TimescaleDB。 连接到数据库并发出以下命令：
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> 如果看到错误，请确认是否已在保存 shared_preload_libraries 后[重启服务器](howto-restart-server-portal.md)。 

现在可以[从头开始](https://docs.timescale.com/getting-started/creating-hypertables)创建 TimescaleDB hypertable，也可以迁移 [PostgreSQL 中的现有时序数据](https://docs.timescale.com/getting-started/migrating-data)。


## <a name="next-steps"></a>后续步骤
如果未看到要使用的扩展，请告诉我们。 在我们的[反馈论坛](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)中投票现有请求或创建新的反馈请求。
