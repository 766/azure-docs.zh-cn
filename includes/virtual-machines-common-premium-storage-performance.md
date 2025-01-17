---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8aeb32ecddc0ef368b615a201179f17178ececad
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68817200"
---
## <a name="application-performance-indicators"></a>应用程序性能指标

我们评估应用程序的性能好坏时，会使用下面这样的性能指标：应用程序处理用户请求的速度如何、应用程序每个请求处理多少数据、应用程序在特定时间内处理多少请求、用户在提交其请求后必须等待多长时间才能获得响应。 与这些性能指标相对应的技术术语是：IOPS、吞吐量或带宽、延迟。

我们会在本部分讨论使用高级存储情况下的常见性能指标。 在随后的“收集应用程序要求”部分，介绍如何为应用程序度量这些性能指标。 在随后的“优化应用程序性能”部分，介绍影响这些性能指标的各种因素，并提供优化建议。

## <a name="iops"></a>IOPS

IOPS，或每秒输入/输出操作，是指应用程序在一秒内发送到存储磁盘的请求数。 可以按顺序或随机读取或写入输入/输出操作。 联机事务处理 (OLTP) 应用程序（例如在线零售网站）需要即时处理多个并发用户请求。 用户请求是插入和更新操作密集型数据库事务，必须通过应用程序进行快速处理。 因此，OLTP 应用程序需要很高的 IOPS。 此类应用程序处理数百万个小型和随机的 IO 请求。 如果应用程序是这样的，则必须在设计应用程序基础结构时针对 IOPS 进行优化。 在后面的优化应用程序性能部分，我们会详细讨论获取高 IOPS 必须考虑的所有因素。

将高级存储磁盘连接到大型 VM 时，Azure 会根据磁盘规格预配保障数目的 IOPS。 例如，P50 磁盘预配 7500 IOPS。 每个大型 VM 还存在一个其所能承受的特定 IOPS 限制。 例如，标准 GS5 VM 的限制为 80,000 IOPS。

## <a name="throughput"></a>吞吐量

吞吐量或带宽是指应用程序在指定时间间隔内发送到存储磁盘的数据量。 如果应用程序执行的输入/输出操作使用的 IO 单位很大，则需要高吞吐量。 数据仓库应用程序往往会发出扫描密集型操作（这些操作一次就会访问大量的数据），并且通常会执行批处理操作。 换而言之，此类应用程序需要更高的吞吐量。 如果应用程序是这样的，则必须在设计其基础结构时针对吞吐量进行优化。 在下一部分，我们会详细讨论那些为了实现此目标而必须进行调整的因素。

将高级存储磁盘连接到大型 VM 时，Azure 会根据磁盘规格预配吞吐量。 例如，P50 磁盘预配 250 MB/秒的磁盘吞吐量。 每个高规格 VM 还存在一个其所能承受的特定吞吐量限制。 例如，标准 GS5 VM 的最大吞吐量为 2,000 MB/秒。

吞吐量和 IOPS 之间存在一个关系，如以下公式所示。

![IOPS 和吞吐量的关系](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

因此，必须确定应用程序所需的最佳吞吐量和 IOPS 值。 尝试优化其中一个值时，另一个值也会受影响。 在后面的优化应用程序性能部分，我们会更详细地讨论如何优化 IOPS 和吞吐量。

## <a name="latency"></a>延迟

延迟是指应用程序接收单个请求，将其发送到存储磁盘，然后又将响应发送到客户端所花的时间。 这是除 IOPS 和吞吐量之外的针对应用程序性能的关键度量。 高级存储磁盘的延迟是指该磁盘检索请求的信息并将其发送回应用程序所花的时间。 高级存储提供持续一致的低延迟服务。 高级磁盘旨在为大多数 IO 操作提供个位数的毫秒级延迟。 如果在高级存储磁盘上启用 ReadOnly 主机缓存，则可获得相当低的读取延迟。 在后面的优化应用程序性能部分，我们将更详细地讨论磁盘缓存。

对应用程序进行优化以获取更高的 IOPS 和吞吐量时，应用程序的延迟就会受到影响。 在优化应用程序性能以后，应始终评估应用程序的延迟，以免出现意外的高延迟行为。

在托管磁盘上进行的下述控制平面操作可能涉及将磁盘从一个存储位置移动到另一个存储位置。 这是通过在后台复制数据来安排的，可能需要花费数小时才能完成，通常少于 24 小时，具体取决于磁盘中的数据量。 在此期间，由于一些读取可能被重定向到原始位置，所以应用程序可能会经历比平常更高的读取延迟，并且可能需要花费更长时间才能完成。 在此期间，对写入延迟没有影响。

- 更新存储类型
- 分离磁盘以及将磁盘从一个 VM 附加到另一个 VM
- 从 VHD 创建托管磁盘
- 从快照创建托管磁盘
- 将非托管磁盘转换为托管磁盘

# <a name="performance-application-checklist-for-disks"></a>磁盘性能应用程序清单

设计在 Azure 高级存储上运行的高性能应用程序时，第一步是了解应用程序的性能要求。 收集性能要求后，即可优化应用程序，使性能得到最大优化。

在上一节中，我们介绍了常见的性能指标：IOPS、吞吐量和延迟。 必须确定这其中的哪些性能指标对于应用程序最重要，以便为用户提供理想的体验。 例如，对于需要在一秒内处理数百万事务的 OLTP 应用程序来说，提高 IOPS 最重要。 而对于需要在一秒内处理大量数据的数据仓库应用程序来说，提高吞吐量最重要。 对于实时应用程序（例如视频直播网站）来说，最重要的是确保极低的延迟。

接下来，请衡量一下应用程序在其整个生存期的最大性能要求。 一开始请使用下面的示例清单。 记录在正常工作负荷期间、高峰工作负荷期间、非工作时间工作负荷期间的最大性能要求。 确定所有工作负荷级别的要求以后，就能够确定应用程序的总体性能要求。 例如，电子商务网站的正常工作负荷是指该网站在一年中的多数日子需要处理的事务数。 网站的高峰工作负荷是指该网站在假日或进行特殊促销活动时候需要处理的事务数。 高峰工作负荷通常会在有限的时段内出现，但可能需要应用程序处理正常运行期间两倍或两倍以上的事务。 找出第 50 百分位数、第 90 百分位和第 99 百分位的要求。 这有助于筛选出性能要求中的任何离群值，让你能够专门针对正确的值进行优化。

## <a name="application-performance-requirements-checklist"></a>应用程序性能要求清单

| **性能要求** | **第 50 百分位数** | **第 90 百分位数** | **第 99 百分位数** |
| --- | --- | --- | --- |
| 最大 每秒事务数 | | | |
| 读取操作百分数 | | | |
| 写入操作百分数 | | | |
| 随机操作百分数 | | | |
| 顺序操作百分数 | | | |
| IO 请求大小 | | | |
| 平均吞吐量 | | | |
| 最大 吞吐量 | | | |
| 最小值 延迟 | | | |
| 平均延迟 | | | |
| 最大 CPU | | | |
| 平均 CPU | | | |
| 最大 内存 | | | |
| 平均内存 | | | |
| 队列深度 | | | |

> [!NOTE]
> 应该根据应用程序未来的预期增长情况，考虑对这些数字进行缩放。 最好是预先对增长情况进行计划，因为以后可能更难通过更改基础结构来提高性能。

如果在已经有一个应用程序的情况下想要转到高级存储，请首先构建一个针对现有应用程序的上述清单。 然后，在高级存储上生成应用程序的原型，根据本文档后面部分的“优化应用程序性能”中描述的准则设计应用程序。 下一篇文章说明那些可以用来收集性能度量的工具。

### <a name="counters-to-measure-application-performance-requirements"></a>用于衡量应用程序性能要求的计数器

若要衡量应用程序的性能要求，最好的方式是使用服务器的操作系统提供的性能监视工具。 可将 PerfMon 用于 Windows，将 iostat 用于 Linux。 这些工具会根据以上部分所述的每个度量来捕获计数器。 必须在应用程序运行其正常工作负荷、高峰工作负荷和非工作时间工作负荷时捕获这些计数器的值。

PerfMon 计数器适用于处理器、内存以及服务器的每个逻辑磁盘和物理磁盘。 将高级存储磁盘用于 VM 时，物理磁盘计数器适用于每个高级存储磁盘，逻辑磁盘计数器适用于在高级存储磁盘上创建的每个卷。 必须捕获承载应用程序工作负荷的磁盘的值。 如果在逻辑磁盘和物理磁盘之间存在一一映射，则可以引用物理磁盘计数器，否则请引用逻辑磁盘计数器。 在 Linux 中，iostat 命令会生成 CPU 和磁盘使用率报告。 磁盘使用率报告会按物理设备或分区提供统计信息。 如果数据库服务器的数据和日志位于不同的磁盘上，则请针对两种磁盘收集此类数据。 下表描述了磁盘、处理器和内存的计数器：

| 计数器 | 描述 | PerfMon | Iostat |
| --- | --- | --- | --- |
| **IOPS 或每秒事务数** |每秒发送到存储磁盘的 I/O 请求数。 |磁盘读取数/秒 <br> 磁盘写入数/秒 |tps <br> r/s <br> w/s |
| **磁盘读取数和写入数** |在磁盘上执行的读取和写入操作的百分比。 |磁盘读取时间百分比 <br> 磁盘写入时间百分比 |r/s <br> w/s |
| **吞吐量** |每秒从磁盘读取或向磁盘写入的数据量。 |磁盘读取的字节数/秒 <br> 磁盘写入字节数/秒 |kB_read/s <br> kB_wrtn/s |
| **延迟** |完成磁盘 IO 请求的总时间。 |磁盘平均秒数/读取 <br> 磁盘平均秒数/写入 |await <br> svctm |
| **IO 大小** |向存储磁盘发出的 I/O 请求的大小。 |磁盘平均字节数/读取 <br> 磁盘平均字节数/写入 |avgrq-sz |
| **队列深度** |等待从存储磁盘读取或等待向存储磁盘写入的待处理 I/O 请求的数目。 |当前的磁盘队列长度 |avgqu-sz |
| **最大内存** |顺利运行应用程序所需的内存量 |提交的在用字节数百分比 |使用 vmstat |
| **最大CPU** |顺利运行应用程序所需的 CPU 速度 |处理器时间百分比 |%util |

详细了解 [iostat](https://linux.die.net/man/1/iostat) 和 [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx)。



## <a name="optimize-application-performance"></a>优化应用程序性能

影响运行在高级存储上的应用程序的性能的主要因素包括：IO 请求的性质、VM 大小、磁盘大小、磁盘数目、磁盘缓存、多线程处理和队列深度。 可以使用系统提供的设置来控制其中部分因素。 大多数应用程序可能不提供直接更改 IO 大小和队列深度的选项。 例如，如果使用 SQL Server，则不能选择 IO 大小和队列深度。 SQL Server 会选择最佳 IO 大小和队列深度值以获取最大性能。 必须了解两类因素对应用程序性能的影响，以便根据性能需要预配相应的资源。

此部分从始至终都需要参考所创建的应用程序要求清单，以便确定需要将应用程序性能优化到何种程度。 据此，可确定此部分中需要调整的因素。 若要了解每个因素对应用程序性能的影响，可在应用程序安装以后运行基准测试工具。 请参阅本文末尾的“基准测试”部分，了解需要执行哪些步骤才能在 Windows 和 Linux VM 上运行常见的基准测试工具。

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>迅速优化 IOPS、吞吐量和延迟

下表汇总了性能因素以及进行 IOPS、吞吐量和延迟优化所需的步骤。 此汇总以后的部分将更深入地介绍每个因素。

有关 VM 大小以及每种类型的 VM 可用的 IOPS、吞吐量和延迟的详细信息，请参阅 [Linux VM 大小](../articles/virtual-machines/linux/sizes.md) 或 [Windows VM 大小](../articles/virtual-machines/windows/sizes.md)。

| &nbsp; | **IOPS** | **吞吐量** | **延迟** |
| --- | --- | --- | --- |
| **示例方案** |企业 OLTP 应用程序，需要很高的每秒事务数比率。 |企业数据仓库应用程序，处理大量数据。 |近实时应用程序，需要对用户请求进行即时响应，例如在线游戏。 |
| 性能因素 | &nbsp; | &nbsp; | &nbsp; |
| **IO 大小** |IO 大小越小，产生的 IOPS 越高。 |IO 大小越大，产生的吞吐量越大。 | &nbsp;|
| **VM 大小** |使用所提供的 IOPS 超出应用程序要求的 VM 大小。 |使用 VM 大小时，应确保吞吐量限制超出应用程序要求。 |使用所提供的规模限制超出应用程序要求的 VM 大小。 |
| **磁盘大小** |使用所提供的 IOPS 超出应用程序要求的磁盘大小。 |使用磁盘大小时，应确保吞吐量限制超出应用程序要求。 |使用所提供的规模限制超出应用程序要求的磁盘大小。 |
| **VM 和磁盘规模限制** |所选 VM 大小的 IOPS 限制应大于已连接的高级存储磁盘所要求的总 IOPS。 |所选 VM 大小的吞吐量限制应大于已连接的高级存储磁盘所要求的总吞吐量。 |所选 VM 大小的规模限制必须大于已连接高级存储磁盘的总规模限制。 |
| **磁盘缓存** |在需要进行大量读取操作的高级存储磁盘上启用 ReadOnly 缓存，以便提高读取 IOPS。 | &nbsp; |在需要进行大量读取操作的高级存储磁盘上启用 ReadOnly 缓存，以便尽量降低读取延迟。 |
| **磁盘条带化** |使用多个磁盘并将其条带化，在组合后获得更高的 IOPS 和吞吐量限制。 单个 VM 的组合限制应高于所连接的高级磁盘的组合限制。 | &nbsp; | &nbsp; |
| **条带大小** |较小的条带大小适用于随机小型 IO 模式，见于 OLTP 应用程序。 例如，SQL Server OLTP 应用程序使用 64 KB 的条带大小。 |较大的条带大小适用于顺序大型 IO 模式，见于数据仓库应用程序。 例如，SQL Server 数据仓库应用程序使用 256 KB 的条带大小。 | &nbsp; |
| **多线程处理** |使用多线程处理将更高数目的请求推送到高级存储，导致 IOPS 和吞吐量更高。 例如，在 SQL Server 上设置较高的 MAXDOP 值，将更多 CPU 分配到 SQL Server。 | &nbsp; | &nbsp; |
| **队列深度** |队列深度越大，产生的 IOPS 越高。 |队列深度越大，产生的吞吐量越高。 |队列深度越小，产生的延迟越低。 |

## <a name="nature-of-io-requests"></a>IO 请求的性质

IO 请求是应用程序将要执行的输入/输出操作单元。 识别 IO 请求的性质（是随机还是顺序，是读取还是写入，是小还是大）将有助于确定应用程序的性能要求。 了解 IO 请求的性质很重要，这有助于在设计应用程序基础结构时作出正确决策。

IO 大小是较为重要的因素之一。 IO 大小是由应用程序生成的输入/输出操作请求的大小。 IO 大小对性能（尤其是应用程序能够实现的 IOPS 和带宽）有很大的影响。 下面的公式说明了 IOPS、IO 大小和带宽/吞吐量之间的关系。  
    ![](media/premium-storage-performance/image1.png)

某些应用程序允许更改其 IO 大小，而某些应用程序则不允许。 例如，SQL Server 会自行确定最佳 IO 大小，不允许用户对其进行更改。 另一方面，Oracle 提供了名为 [DB\_BLOCK\_SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) 的参数，可用于配置数据库的 I/O 请求大小。

如果使用的应用程序不允许更改 IO 大小，可根据本文中的准则来优化与应用程序最相关的性能 KPI。 例如，

* OLTP 应用程序生成数以百万计的小型和随机 IO 请求。 若要处理这些类型的 IO 请求，必须对应用程序基础结构进行设计，以便提高 IOPS。  
* 数据仓库应用程序生成大型和顺序 IO 请求。 若要处理这些类型的 IO 请求，必须对应用程序基础结构进行设计，以便提高带宽或吞吐量。

如果使用的应用程序允许更改 IO 大小，则可使用这条针对 IO 大小的经验法则以及其他性能准则。

* 通过降低 IO 大小来提高 IOPS。 例如，对 OLTP 应用程序使用 8 KB 的 IO 大小。  
* 通过提高 IO 大小来提高带宽/吞吐量。 例如，对数据仓库应用程序使用 1024 KB 的 IO 大小。

下面是一个如何计算应用程序的 IOPS 和吞吐量/带宽的示例。 以使用 P30 磁盘的应用程序为考虑对象。 P30 磁盘能够实现的最大 IOPS 和吞吐量/带宽分别是 5000 IOPS 和 200 MB/秒。 现在，如果的应用程序要求实现 P30 磁盘能够达到的最大 IOPS 而使用了较小 IO（例如 8 KB），则能够获得的带宽为 40 MB/秒。 但是，如果应用程序要求实现 P30 磁盘能够达到的最大吞吐量/带宽，而你使用较大的 IO 大小（例如 1024 KB），则最终 IOPS 将较小 (200 IOPS)。 因此，可以调整 IO 大小，使之满足应用程序的 IOPS 和吞吐量/带宽要求。 下表总结了 P30 磁盘的不同 IO 大小以及相应的 IOPS 和吞吐量。

| 应用程序要求 | I/O 大小 | IOPS | 吞吐量/带宽 |
| --- | --- | --- | --- |
| 最大 IOPS |8 KB |5,000 |40 MB/秒 |
| 最大吞吐量 |1024 KB |200 |每秒 200 MB |
| 最大吞吐量 + 高 IOPS |64 KB |3,200 |每秒 200 MB |
| 最大 IOPS + 高吞吐量 |32 KB |5,000 |160 MB/秒 |

要获取比单个高级存储磁盘的最大值还要高的 IOPS 和带宽，可将多个高级磁盘一起条带化。 例如，将两个 P30 磁盘条带化得到的组合 IOPS 为 10,000 IOPS，得到的组合吞吐量为 400 MB/秒。 如下一部分所述，必须使用支持组合式磁盘 IOPS 和吞吐量的 VM 大小。

> [!NOTE]
> 增加 IOPS 或吞吐量这两个指标中的其中一个时，另一指标也将增加，因此增加任一指标时都请勿超过磁盘或 VM 的吞吐量或 IOPS 限制。

若要了解 IO 大小对应用程序性能的影响，可在 VM 和磁盘上运行基准测试工具。 创建多个测试运行并对每个运行使用不同的 IO 大小，即可观察相应的影响。 如需更多详细信息，请参阅本文末尾的“基准测试”部分。

## <a name="high-scale-vm-sizes"></a>大型 VM 大小

开始设计应用程序时，首先要做的一件事就是选择一个 VM 来托管应用程序。 高级存储提供高规格 VM 大小，可以运行需要更高计算能力和高的本地磁盘 I/O 性能的应用程序。 这些 VM 为本地磁盘提供更快的处理器、更高的内存内核比和固态驱动器 (SSD)。 支持高级存储的高规模 Vm 的示例包括 DS、DSv2 和 GS 系列 Vm。

高规格 VM 提供不同的大小、不同数目的 CPU 内核、内存、OS 和临时磁盘大小。 每种 VM 大小还设置了可以连接到 VM 的最大数目的数据磁盘。 因此，所选 VM 大小会影响提供给应用程序的处理能力、内存大小和存储容量。 它还会影响计算和存储成本。 例如，以下是 DS 系列、DSv2 系列和 GS 系列中最大 VM 大小的规格：

| VM 大小 | CPU 核心数 | 内存 | VM 磁盘大小 | 最大 数据磁盘 | 缓存大小 | IOPS | 带宽缓存 IO 限制 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OS = 1023 GB <br> 本地 SSD = 224 GB |32 |576 GB |50,000 IOPS <br> 每秒 512 MB |4,000 IOPS，33 MB/秒 |
| Standard_GS5 |32 |448 GB |OS = 1023 GB <br> 本地 SSD = 896 GB |64 |4224 GB |80,000 IOPS <br> 每秒 2,000 MB |5,000 IOPS，50 MB/秒 |

若要查看所有可用 Azure VM 大小的完整列表，请参阅 [Windows VM 大小](../articles/virtual-machines/windows/sizes.md)或 [Linux VM 大小](../articles/virtual-machines/linux/sizes.md)。 选择能够满足或者在扩展后能够满足所需应用程序性能要求的 VM 大小。 除此之外，在选择 VM 大小时，还需考虑以下重要事项。

规模限制  
每个 VM 和每个磁盘的最大 IOPS 限制是不同的，且互不影响。 请确保应用程序所要实现的 IOPS 处于 VM 以及连接到 VM 的高级磁盘的限制内。 否则，应用程序性能就会受到限制。

举例来说，假设应用程序要求的 IOPS 最大值为 4,000。 为此，请在 DS1 VM 上预配 P30 磁盘。 P30 磁盘可以提供的 IOPS 最多为 5,000。 但是，DS1 VM 的 IOPS 限制为 3,200。 因此，应用程序性能将受限于 3,200 IOPS 的 VM 限制，性能会下降。 若要防止这种情况的发生，在选择 VM 和磁盘大小时，应确保二者都能满足应用程序要求。

操作成本  
在许多情况下，与使用标准存储相比，使用高级存储的总体运行成本可能更低。

例如，以需要 16,000 IOPS 的应用程序为考虑对象。 若要实现此性能, 需要一个标准\_的 D14 Azure IaaS VM, 该 VM 可以使用32标准存储空间 1 TB 磁盘, 最大 IOPS 为16000。 每个 1 TB 标准存储磁盘最多可以达到 500 IOPS。 此 VM 每月的估计成本将是 $1,570。 32 个标准存储磁盘每月的成本将是 $1,638。 每月估计的总成本将是 $3,208。

但是，如果将相同的应用程序托管在高级存储上，需要的 VM 大小会降低，需要的高级存储磁盘数会减少，从而降低总体成本。 Standard\_DS13 VM 可以使用 4 个 P30 磁盘来满足 16,000 IOPS 的要求。 DS13 VM 的最大 IOPS 为 25,600，每个 P30 磁盘的最大 IOPS 为 5,000。 总起来说，此配置可以达到 5,000 x 4 = 20,000 的 IOPS。 此 VM 每月的估计成本将是 $1,003。 4 个 P30 高级存储磁盘每月的成本是 $544.34。 每月估计的总成本将是 $1,544。

下表总结了这种情况下标准存储和高级存储的成本明细。

| &nbsp; | **标准** | **高级** |
| --- | --- | --- |
| **VM 每月的成本** |$1,570.58 (Standard\_D14) |$1,003.66 (Standard\_DS13) |
| **每月磁盘成本** |$1638.40 (32 x 1 TB 磁盘) |$544.34（4 x P30 磁盘） |
| **每月成本总计** |$3,208.98 |$1,544.34 |

Linux 发行版  

使用 Azure 高级存储，可以让运行 Windows 和 Linux 的 VM 获得相同级别的性能。 支持多种 Linux 发行版，可在[此处](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)查看完整列表。 请务必注意，不同的发行版适用于不同类型的工作负荷。 根据运行工作负荷的发行版，所见性能级别会有所不同。 使用应用程序测试各种 Linux 发行版，选择最适合的。

使用高级存储运行 Linux 时，请查看与所需驱动程序相关的最新更新，确保实现高性能。

## <a name="premium-storage-disk-sizes"></a>高级存储磁盘大小

Azure 高级存储提供8个 GA 磁盘大小和三个目前处于预览阶段的磁盘大小。 每种磁盘大小对 IOPS、带宽和存储空间设置了不同规格的限制。 选择正确的高级存储磁盘大小，具体取决于应用程序要求和高规格 VM 大小。 下表显示了11个磁盘大小及其功能。 目前，仅托管磁盘支持 P4、P6、P15、P60、P70 和 P80 大小。

| 高级磁盘类型  | P4    | P6    | P10   | P15 | P20   | P30   | P40   | P50   | P60   | P70   | P80   |
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
| 磁盘大小           | 32 GiB | 64 GiB | 128 GiB| 256 GiB| 512 GB            | 1,024 GiB (1 TiB)    | 2,048 GiB (2 TiB)    | 4,095 GiB (4 TiB)    | 8,192 GiB (8 TiB)    | 16,384 GiB (16 TiB)    | 32,767 GiB (32 TiB)    |
| 每个磁盘的 IOPS       | 120   | 240   | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12,500              | 15,000              | 20,000              |
| 每个磁盘的吞吐量 | 25 MiB/秒  | 50 MiB/秒  | 100 MiB/秒 |125 MiB/秒 | 150 MiB/秒 | 200 MiB/秒 | 250 MiB/秒 | 250 MiB/秒 | 480 MiB/秒 | 750 MiB/秒 | 750 MiB/秒 |

选择多少磁盘取决于所选磁盘大小。 可以使用一个 P50 磁盘或多个 P10 磁盘来满足应用程序需求。 进行选择时，可考虑下面列出的注意事项。

规模限制（IOPS 和吞吐量）  
每种高级磁盘大小的 IOPS 和吞吐量限制都是不同的，与 VM 规模限制无关。 请确保磁盘的总 IOPS 和吞吐量处于所选 VM 大小的规模限制范围内。

例如，如果应用程序需要最大 250 MB/秒的吞吐量，而使用的是有单个 P30 磁盘的 DS4 VM。 该 DS4 VM 可以提供高达 256 MB/秒的吞吐量。 但是，单个 P30 磁盘的吞吐量限制为 200 MB/秒。因此，应用程序将因磁盘限制而限制为 200 MB/秒。 为了克服这一局限，请为 VM 预配多个数据磁盘，或将磁盘大小重设为 P40 或 P50。

> [!NOTE]
> 缓存提供的读取不包括在磁盘 IOPS 和吞吐量之中，因此不受磁盘限制。 缓存具有单独的 IOPS 和吞吐量限制，具体取决于每个 VM。
>
> 例如，初始读取和写入分别为 60MB/秒和 40MB/秒。 随着时间的延长，缓存得到预热，可以通过缓存提供越来越多的读取。 然后，就可以从磁盘获取更高的写入吞吐量。

*磁盘数*  
通过评估应用程序要求，确定所需磁盘数。 每种 VM 大小还设置了可以连接到 VM 的磁盘的数目限制。 通常情况下，这是内核数的两倍。 请确保所选 VM 大小能够支持所需磁盘数。

请记住，与标准存储磁盘相比，高级存储磁盘具有更高的性能。 因此，如果要将应用程序从使用标准存储的 Azure IaaS VM 迁移到高级存储，可能只需较少的高级磁盘即可让应用程序实现相同的性能或更高的性能。

## <a name="disk-caching"></a>磁盘缓存

利用 Azure 高级存储的高规格 VM 使用名为 BlobCache 的多层缓存技术。 BlobCache 使用虚拟机 RAM 和本地 SSD 的组合进行缓存。 此缓存适用于高级存储的永久性磁盘和 VM 本地磁盘。 默认情况下，此缓存设置已设置为允许对 OS 磁盘进行读/写操作，允许对托管在高级存储中的数据磁盘进行只读操作。 在高级存储磁盘上启用磁盘缓存后，高规格 VM 可以达到相当高的性能级别，超出基础磁盘性能。

> [!WARNING]
> 磁盘 4 TiB 和更大的磁盘缓存不受支持。 如果将多个磁盘附加到 VM, 则每个小于 4 TiB 的磁盘将支持缓存。
>
> 更改 Azure 磁盘的缓存设置可分离和重新附加目标磁盘。 如果它是操作系统磁盘，将重启 VM。 更改磁盘缓存设置前，停止所有可能受此中断影响的应用程序/服务。

若要详细了解 BlobCache 的工作方式，请参阅内部的 [Azure 高级存储](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)博客文章。

必须在正确的磁盘集上启用缓存。 是否应在高级磁盘上启用磁盘缓存将取决于该磁盘需要处理的工作负荷模式。 下表显示 OS 和数据磁盘的默认缓存设置。

| **磁盘类型** | **默认缓存设置** |
| --- | --- |
| 操作系统磁盘 |ReadWrite |
| 数据磁盘 |只读 |

以下是针对数据磁盘建议的磁盘缓存设置：

| **磁盘缓存设置** | **有关何时使用此设置的建议** |
| --- | --- |
| 无 |对于只写磁盘和频繁写入磁盘，可将 host-cache 配置为“无”。 |
| ReadOnly |对于只读磁盘和读写磁盘，可将 host-cache 配置为“ReadOnly”。 |
| ReadWrite |如果应用程序可以在需要时将缓存数据正确写入永久性磁盘，则可将 host-cache 配置为 ReadWrite。 |

*ReadOnly*  
在高级存储数据磁盘上配置 ReadOnly 缓存，可以为应用程序实现较低的读取延迟，并获得极高的读取 IOPS 和吞吐量。 这有两个原因。

1. 通过缓存执行的读取操作发生在 VM 内存和本地 SSD 上，其速度要大大快于从数据磁盘进行的读取操作，后者发生在 Azure Blob 存储上。  
1. 高级存储不将从缓存提供的读取操作计入磁盘 IOPS 和吞吐量。 因此，应用程序能够实现更高的总 IOPS 和吞吐量。

*ReadWrite*  
默认情况下，OS 磁盘已启用 ReadWrite 缓存。 我们最近还增加了对在数据磁盘上进行 ReadWrite 缓存的支持。 如果使用的是 ReadWrite 缓存，则必须使用正确的方法将数据从缓存写入到永久性磁盘中。 例如，SQL Server 会自行将缓存数据写入永久性存储磁盘。 对不负责保留所需数据的应用程序使用 ReadWrite 缓存可能会在 VM 崩溃时导致数据丢失。

*无*  
目前只能在数据磁盘上进行**任何**支持。 OS 磁盘不支持此方法。 如果在 OS 磁盘上设置 "**无**", 则将在内部覆盖此设置, 并将其设置为**ReadOnly**。

举例来说，可以通过执行以下操作将这些准则应用到在高级存储上运行的 SQL Server：

1. 在托管数据文件的高级存储磁盘上配置“ReadOnly”缓存。  
   a.  从缓存快速读取可以缩短 SQL Server 查询时间，因为从缓存检索数据页的速度要大大快于直接从数据磁盘进行检索的速度。  
   b.  从缓存进行读取意味着可以从高级数据磁盘获得更多的吞吐量。 SQL Server 可以利用这额外的吞吐量来检索更多数据页和执行其他操作，例如备份/还原、批量加载以及索引重建。  
1. 在托管日志文件的高级存储磁盘上将缓存配置为“无”。  
   a.  日志文件主要是进行频繁的写入操作。 因此，将缓存设置为 ReadOnly 对其无用。

## <a name="optimize-performance-on-linux-vms"></a>优化 Linux Vm 的性能

对于缓存设置为 ReadOnly 或 None 的所有高级存储 SSD 或极致磁盘，必须在装入文件系统时禁用“屏障”。 在此方案中不需要屏障，因为写入高级存储磁盘对于这些缓存设置是持久性的。 成功完成写入请求时，数据已写入持久存储。 若要禁用“屏障”，请使用以下方法之一： 选择适用于文件系统的方法：
  
* 对于 reiserFS，请使用 `barrier=none` 装入选项来禁用屏障。 （若要启用屏障，请使用 `barrier=flush`。）
* 对于 ext3/ext4，请使用 `barrier=0` 装入选项来禁用屏障。 （若要启用屏障，请使用 `barrier=1`。）
* 对于 XFS，请使用 `nobarrier` 装入选项来禁用屏障。 （若要启用屏障，请使用 `barrier`。）
* 对于缓存设置为 ReadWrite 的高级存储磁盘，请启用屏障来实现写入持久性。
* 若要在重新启动 VM 后保留卷标，必须在 /etc/fstab 中更新对磁盘的全局唯一标识符 (UUID) 引用。 有关详细信息，请参阅[将托管磁盘添加到 Linux VM](../articles/virtual-machines/linux/add-disk.md)。

下面是我们使用高级 SSD 验证过的 Linux 发行版。 为了高级 SSD 更好的性能和稳定性，建议将 VM 升级到其中至少一个版本（或更新版本）。 

某些版本需要最新的适用于 Azure 的 Linux Integration Services (LIS) v4.0。 若要下载并安装某个发行版，请单击下表中所列的链接。 完成验证后，我们将陆续在该列表中添加映像。 我们的验证表明，性能根据每个映像的不同而异。 性能取决于工作负荷特征和映像设置。 不同的映像已针对不同种类的工作负荷进行优化。

| 分发 | 版本 | 支持的内核 | 详细信息 |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x、8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5、6.6、6.7、7.0 | &nbsp; | [需要 LIS4](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> 请参阅下一部分中的注释 |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [建议使用 LIS4](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> 请参阅下一部分中的注释 |
| Red Hat Enterprise Linux (RHEL) | 6.8+、7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+、7.2+ | &nbsp; | UEK4 或 RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 或 RHCK w/[LIS 4.1+](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 或 RHCK w/[LIS 4.1+](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |

### <a name="lis-drivers-for-openlogic-centos"></a>OpenLogic CentOS 的 LIS 驱动程序

运行 OpenLogic CentOS VM 时，请运行以下命令来安装最新的驱动程序：

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

若要激活新驱动程序，请重启 VM。

## <a name="disk-striping"></a>磁盘条带化

当高规格 VM 与多个高级存储永久性磁盘连接时，可以将这些磁盘一起条带化，以便聚合其 IOPS、带宽和存储容量。

在 Windows 上，可以使用存储空间将磁盘条带化。 必须为池中每个磁盘配置一列。 否则，条带化卷的整体性能可能会低于预期，因为磁盘之间的通信分配不平均。

重要:使用服务器管理器 UI，可以将列的总数设置为每个条带化卷最多 8 个。 连接 8 个以上的磁盘时，可使用 PowerShell 来创建卷。 使用 PowerShell，可以将列数设置为与磁盘数相等。 例如，如果一个条带集中有 16 个磁盘，可在 *New-VirtualDisk* PowerShell cmdlet 的 *NumberOfColumns* 参数中指定 16 个列。

在 Linux 中，可使用 MDADM 实用工具将磁盘条带化。 有关在 Linux 中对磁盘进行条带化操作的详细步骤，请参阅[在 Linux 上配置软件 RAID](../articles/virtual-machines/linux/configure-raid.md)。

*条带大小*  
进行磁盘条带化操作时，一项重要配置是条带大小。 条带大小或块大小是应用程序可以在条带化卷上处理的最小数据块区。 配置的条带大小取决于应用程序类型及其请求模式。 如果选择了错误的条带大小，可能导致 IO 不一致，从而导致应用程序性能下降。

例如，如果应用程序生成的 IO 请求大于磁盘条带大小，存储系统会将数据写在不止一个磁盘上，跨越条带单元的边界。 在需要访问该数据时，则必须跨多个条带单元进行搜索才能完成请求。 这种行为的累积效应就是性能大幅下降。 另一方面，如果 IO 请求大小小于条带大小，并且其性质是随机的，则 IO 请求可能会在同一磁盘上累积起来，导致瓶颈的出现，最终导致 IO 性能下降。

请根据应用程序正在运行的工作负荷的类型，选择合适的条带大小。 对于随机的较小的 IO 请求，请使用较小的条带大小。 而对于大型的顺序性的 IO 请求，则请使用较大的条带大小。 对于要在高级存储上运行的应用程序，请找出相应的条带大小建议。 对于 SQL Server，如果工作负荷为 OLTP 工作负荷，请将条带大小配置为 64 KB；如果工作负荷为数据仓库型工作负荷，则请将条带大小配置为 256 KB。 请参阅 [Azure VM 上的 SQL Server 性能最佳实践](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance)以了解更多信息。

> [!NOTE]
> 在 DS 系列 VM 上可将最多 32 个高级存储磁盘条带化，在 GS 系列 VM 上可将最多 64 个高级存储磁盘条带化。

## <a name="multi-threading"></a>多线程处理

Azure 将高级存储平台设计为可以进行大规模并行处理。 因此，相对于单线程应用程序，多线程应用程序可以实现更高的性能，而且要高得多。 多线程应用程序将其任务拆分成多个线程，并将 VM 和磁盘资源利用到极致，从而提高其执行效率。

例如，如果应用程序是在使用两个线程的单核 VM 上运行，则 CPU 可以在这两个线程之间进行切换以实现高效率。 当一个线程在等待磁盘 IO 完成时，CPU 可以切换至另一个线程。 通过这种方式，两个线程可以完成比单个线程更多的任务。 如果 VM 有多个内核，则可进一步缩短运行时间，因为每个内核都可以并行执行任务。

可能无法更改现成应用程序实施单线程处理或多线程处理的方式。 例如，SQL Server 能够处理多 CPU 和多核的情况。 但是，SQL Server 可以决定在什么样的情况下利用一个或多个线程来处理查询。 它可以运行查询，并使用多线程处理来生成索引。 如果一个查询需要先联接多个大型表并对数据进行排序才能返回给用户，则 SQL Server 可能会使用多个线程。 但是，用户不能控制 SQL Server 是使用单线程还是多线程来执行查询。

可以通过更改配置设置来影响应用程序的这种多线程处理或并行处理。 例如，在使用 SQL Server 的情况下，可以更改最大并行度配置。 此设置称为 MAXDOP，可用于配置 SQL Server 在并行处理时能够使用的最大处理器数。 可为单个查询或索引操作配置 MAXDOP。 对于性能关键型应用程序，这在需要均衡系统资源时大有裨益。

例如，假设应用程序使用 SQL Server，且同时执行大型查询和索引操作。 假设想让索引操作性能优于大型查询。 在这种情况下，可以将索引操作的 MAXDOP 值设为高于查询的 MAXDOP 值。 这样一来，SQL Server 在进行索引操作时，就可以利用比进行大型查询所需的处理器更多的处理器。 请记住，无法人为控制 SQL Server 要用于每个操作的线程数。 可以控制多线程处理专用的最大处理器数。

详细了解 SQL Server 中的[并行度](https://technet.microsoft.com/library/ms188611.aspx)。 找出应用程序中影响多线程处理的此类设置及其配置，以便优化性能。

## <a name="queue-depth"></a>队列深度

队列深度、队列长度或队列大小是指系统中等待处理的 IO 请求的数目。 队列深度的值决定了应用程序可以让多少个 IO 操作排队供存储磁盘处理。 它会影响我们在本文中讨论过的所有三个应用程序性能指标，即 IOPS、吞吐量和延迟。

队列深度和多线程处理密切相关。 队列深度值表示应用程序可以实现的多线程处理的程度。 如果队列深度很大，则应用程序可以并行执行更多的操作，换言之，可以进行更多的多线程处理。 如果队列深度小，则即使应用程序具有多个线程，它也无法让足够多的请求排队来完成并发执行。

通常情况下，现成应用程序不允许更改队列深度，因为设置不正确反而有害。 应用程序会将队列深度设置成合适的值以获取最佳性能。 但是，理解这一概念对于解决应用程序性能问题很重要。 也可通过在系统中运行基准测试工具来观察队列深度的影响。

某些应用程序提供可以影响队列深度的设置。 例如，前一部分介绍的 SQL Server 中的 MAXDOP（最大并行度）设置。 MAXDOP 是一种影响队列深度和多线程处理的方法，虽然它不直接更改 SQL Server 的队列深度值。

*高队列深度*  
高队列深度可以让更多操作在磁盘上排队。 磁盘可以提前知道其队列中的下一个请求。 因此，磁盘可以提前计划操作，按最佳顺序对其进行处理。 由于应用程序向磁盘发送了更多的请求，因此磁盘可以处理更多的并行 IO。 最终，应用程序可以实现更高的 IOPS。 由于应用程序处理了更多的请求，因此应用程序的总吞吐量也增加。

通常，在每个连接的磁盘存在 8-16 个以上待处理 IO 的情况下，应用程序可以实现最大吞吐量。 如果队列深度为 1，则应用程序不会将足够的 IO 推送到系统，在给定时间内所能处理的 IO 数目会较少。 换而言之，吞吐量降低。

例如，在 SQL Server 中，将查询的 MAXDOP 值设置为“4”就是告知 SQL Server：最多可以使用 4 个内核来执行查询。 将由 SQL Server 来确定最佳队列深度值以及执行查询所需的内核数目。

*最佳队列深度*  
队列深度值过高也有其缺点。 如果队列深度值过高，则应用程序会尝试实现非常高的 IOPS。 除非应用程序的永久性磁盘具有足够高的预配 IOPS，否则会对应用程序延迟造成负面影响。 以下公式显示了 IOPS、延迟和队列深度之间的关系。  
    ![](media/premium-storage-performance/image6.png)

不应随意地将队列深度配置为某个很高的值，而应将其配置为最佳值，该值可以确保应用程序实现足够高的 IOPS，但又不会影响延迟。 例如，如果应用程序延迟需要设置为 1 毫秒，则要实现 5,000 IOPS，所需队列深度为：QD = 5000 x 0.001 = 5。

条带化卷的队列深度  
条带化卷应保持足够高的队列深度，使得每个磁盘都有各自的高峰队列深度。 例如，以某个应用程序为考虑对象，该应用程序所推送的队列深度为 2，条带中有四个磁盘。 两个 IO 请求会发送到两个磁盘中，剩下两个磁盘将处于空闲状态。 因此，请将队列深度配置为让所有磁盘都能够处于繁忙状态。 下面的公式说明了如何确定条带化卷的队列深度。  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>限制

Azure 高级存储根据所选 VM 大小和磁盘大小，预配指定数目的 IOPS 和吞吐量。 任何时候，只要应用程序尝试实现的 IOPS 或吞吐量超出了这些限制（VM 或磁盘能够处理的量），高级存储就会对其进行限制。 这会以应用程序性能下降的方式体现出来。 具体表现为延迟增高、吞吐量下降或 IOPS 降低。 如果高级存储不对此进行限制，应用程序可能会超过其资源的处理能力，从而彻底崩溃。 因此，为了避免因限制而造成的性能问题，请始终为应用程序预配足够的资源。 请考虑一下我们在上面的 VM 大小和磁盘大小部分讨论过的内容。 要了解需要哪些资源来托管应用程序，最好的方式是进行基准测试。

## <a name="next-steps"></a>后续步骤

了解有关可用磁盘类型的详细信息：

* [选择磁盘类型](../articles/virtual-machines/windows/disks-types.md)  

SQL Server 用户请阅读有关 SQL Server 性能最佳实践的文章：

* [Azure 虚拟机中 SQL Server 的性能最佳实践](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage provides highest performance for SQL Server in Azure VM](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)（Azure 高级存储为 Azure VM 中的 SQL Server 提供最高性能）
