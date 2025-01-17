---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173348"
---
| 限制标识符 | 限制 | 注释 |
| --- | --- | --- |
| 存储帐户凭据的最大数目 |64 | |
| 卷容器的最大数目 |64 | |
| 队列的最大数目 |255 | |
| 每个带宽模板的最大计划数 |168 |计划每隔一小时，每个日期是星期几。 |
| 物理设备上分层卷的大小上限 |StorSimple 8100 和 StorSimple 8600 的 64 TB |StorSimple 8100 和 StorSimple 8600 是物理设备。 |
| Azure 中虚拟设备上的分层卷的大小上限 |30 TB for StorSimple 8010 <br></br> 64 TB for StorSimple 8020 |StorSimple 8010 和 StorSimple 8020 是分别使用标准存储和高级存储的虚拟设备在 Azure 中。 |
| 物理设备上本地固定卷的大小上限 |9 TB for StorSimple 8100 <br></br> 24 TB for StorSimple 8600 |StorSimple 8100 和 StorSimple 8600 是物理设备。 |
| iSCSI 连接的最大数目 |512 | |
| 来自发起程序的 iSCSI 连接的最大数目 |512 | |
| 每个设备的访问控制记录的最大数目 |64 | |
| 每个备份策略的卷的最大数目 |24 | |
| 每个备份策略保留的备份的最大数目 |64 | |
| 每个备份策略的最大计划数 |10 | |
| 每个卷可保留的任何类型快照的最大数目 |256 |此数量包括本地快照和云快照。 |
| 任何设备中可以存在的快照的最大数目 |10,000 | |
| 可以并行处理以实现备份、还原或克隆的卷的最大数目 |16 |<ul><li>如果有超过 16 个卷，它们是依序处理处理槽可用。</li><li>在完成操作之前，不会发生新的克隆的备份或还原的分层的卷。 对于本地卷，卷处于联机状态后可以备份。</li></ul> |
| 分层卷的还原和克隆恢复时间 |< 2 分钟 |<ul><li>卷完成后还原或克隆操作，而不考虑卷大小的 2 分钟内可用。</li><li>卷性能一开始可能比正常的因为大部分数据和元数据仍驻留在云中慢。 为数据流从云到 StorSimple 设备可能会改善性能。</li><li>下载元数据的总时间取决于分配的卷大小。 在后台，元数据会自动以 5 分钟 1 TB 已分配卷数据的速率载入设备中。 此速率可能受到云的 Internet 带宽。</li><li>当所有元数据都位于设备上时，还原或克隆操作即告完成。</li><li>不能执行备份操作，直到还原或克隆操作完全完成。 |
| 本地固定卷的还原恢复时间 |< 2 分钟 |<ul><li>卷在完成还原操作后的 2 分钟内可用，无论卷大小如何。</li><li>卷性能一开始可能比正常的因为大部分数据和元数据仍驻留在云中慢。 为数据流从云到 StorSimple 设备可能会改善性能。</li><li>下载元数据的总时间取决于分配的卷大小。 在后台，元数据会自动以 5 分钟 1 TB 已分配卷数据的速率载入设备中。 此速率可能受到云的 Internet 带宽。</li><li>与分层卷不同如果有本地固定的卷，卷数据也会下载本地设备上。 将所有卷数据下载到设备后，还原操作即告完成。</li><li>还原操作可能很费时，完成还原的总时间取决于预配本地卷、 Internet 带宽和设备上的现有数据的大小。 在还原操作过程中，允许在本地固定卷上进行备份操作。 |
| 精简还原可用性 |上次故障转移 | |
| 最大客户端读取/写入吞吐量，从 SSD 层 * 提供服务时 |单一 10 吉比特以太网网络接口使用 920/720 MB/秒 |使用 MPIO 和两个网络接口的最多两次。 |
| 最大客户端读取/写入吞吐量，从 HDD 层 * 提供服务时 |120/250 MB/秒 | |
| 最大客户端读取/写入吞吐量，从云层 * 提供服务时 |11/41 MB/秒 |读取吞吐量取决于生成和维护足够的 I/O 队列深度的客户端。 |

&#42;每个 I/O 类型的最大吞吐量使用 100%读取方案和 100%写入方案进行测量。 实际吞吐量可能会较低，取决于 I/O 混用和网络条件。

