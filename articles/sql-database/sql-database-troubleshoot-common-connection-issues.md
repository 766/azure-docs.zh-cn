---
title: 排查 Azure SQL 数据库的常见连接问题
description: 识别和解决 Azure SQL 数据库常见连接错误的步骤。
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: daleche
ms.reviewer: jrasnik
ms.date: 01/25/2019
ms.openlocfilehash: 6dccfca256239f922c2243e2ebfab5c26835ee98
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566304"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>排查 Azure SQL 数据库的连接问题

与 Azure SQL 数据库连接失败时，会收到[错误消息](sql-database-develop-error-messages.md)。 本文是一个集中介绍对 Azure SQL 数据库连接问题进行故障排除的主题。 本文介绍连接问题的[常见原因](#cause)，推荐可帮助确定问题的[故障排除工具](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues)，还提供解决[暂时性错误](#troubleshoot-transient-errors)和[持久或非暂时性错误](#troubleshoot-persistent-errors)的故障排除步骤。 

如果遇到连接问题，请尝试本文中介绍的故障排除步骤。
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>原因

连接问题可能由以下任何原因引起：

* 在应用程序设计过程中无法应用最佳实践和设计准则。  请参阅 [SQL 数据库开发概述](sql-database-develop-overview.md)了解入门信息。
* Azure SQL 数据库重新配置
* 防火墙设置
* 连接超时
* 不正确的登录信息
* 一些 Azure SQL 数据库资源达到最大限制

通常，Azure SQL 数据库的连接问题可按如下方式分类：

* [暂时性错误（短期或间歇性）](#troubleshoot-transient-errors)
* [持久或非暂时性错误（定期重复发生的错误）](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>尝试使用 Azure SQL 数据库连接问题的故障排除工具

如果遇到特定的连接错误，请尝试使用[此工具](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database)，以帮助快速识别并解决问题。

## <a name="troubleshoot-transient-errors"></a>对暂时性错误进行故障排除

当应用程序连接到 Azure SQL 数据库时，你会收到以下错误消息：

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> 此错误消息通常是暂时的（生存期较短）。
> 
> 

当数据库正在移动（或重新配置），且应用程序与数据库断开连接时，会出现此错误。 之所以会发生数据库重新配置事件是因为，有计划内事件（例如，软件升级）或计划外事件（例如，进程故障或负载均衡）。 大多数重新配置事件的生存期通常较短，并且应在最多 60 秒内完成。 但是，偶尔这些事件可能需要更长时间才能完成，例如当一个大型事务导致长时间运行的恢复时。

### <a name="steps-to-resolve-transient-connectivity-issues"></a>解决暂时性连接问题的步骤

1. 查看 [Microsoft Azure 服务仪表板](https://azure.microsoft.com/status)以了解应用程序报告错误时出现的任何已知中断。
2. 连接到云服务的应用程序（如 Azure SQL 数据库）应期望定期重新配置事件并实施重试逻辑来处理这些错误，而不是将它们作为应用程序错误展现给用户。 查看[暂时性错误](sql-database-connectivity-issues.md)部分和 [SQL 数据库开发概述](sql-database-develop-overview.md)中的最佳实践和设计准则以了解更多信息和常规重试策略。 有关详细信息，请参阅[用于 SQL 数据库和 SQL Server 的连接库](sql-database-libraries.md)中的代码示例。
3. 由于数据库即将达到其资源限制，因此错误看起来像是暂时性连接问题。 请参阅[资源限制](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached)。
4. 如果连接问题继续存在，或者应用程序发生错误的持续时间超过 60 秒或在特定的一天中看到错误多次发生，请通过在 [Azure 支持](https://azure.microsoft.com/support/options)网站上选择“**获取支持**”提出 Azure 支持请求。

## <a name="troubleshoot-persistent-errors"></a>排查一再出现的错误
如果应用程序一直无法连接到 Azure SQL 数据库，通常表示下列其中一项出现了问题：

* 防火墙配置。 Azure SQL 数据库或客户端防火墙阻止了与 Azure SQL 数据库的连接。
* 客户端上重新配置了网络：例如，使用了新的 IP 地址或代理服务器。
* 用户失误：例如，连接参数（例如连接字符串中的服务器名称）键入错误。

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>解决永久性连接问题的步骤
1. 设置[防火墙规则](sql-database-configure-firewall-settings.md)以允许客户端 IP 地址。 为便于临时测试，可使用 0.0.0.0 作为 IP 地址范围的开头，并使用 255.255.255.255 作为 IP 地址范围的结尾设置一个防火墙规则。 这会在所有 IP 地址上打开服务器。 如果这样可以解决连接性问题，请删除此规则，再针对适当限制的 IP 地址或地址范围创建防火墙规则。 
2. 在客户端与 Internet 之间的所有防火墙上，确保为出站连接打开端口 1433。 有关需要为 Azure Active Directory 身份验证打开的其他端口的其他信息，请查看[配置 Windows 防火墙以允许 SQL Server 访问](https://msdn.microsoft.com/library/cc646023.aspx)和[混合标识所需的端口和协议](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports)。
3. 验证连接字符串和其他连接设置。 请参阅[连接问题主题](sql-database-connectivity-issues.md#connections-to-sql-database)中的“连接字符串”部分。
4. 在仪表板中检查服务运行状况。 如果认为存在区域性的中断，请参阅[从中断恢复](sql-database-disaster-recovery.md)，以了解恢复到新区域的步骤。

## <a name="next-steps"></a>后续步骤
* [在 Microsoft Azure 上搜索文档](https://azure.microsoft.com/search/documentation/)
* [查看 Azure SQL 数据库服务的最新更新](https://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>其他资源
* [SQL 数据库开发概述](sql-database-develop-overview.md)
* [暂时性错误处理的一般指南](../best-practices-retry-general.md)
* [SQL 数据库和 SQL Server 的连接库](sql-database-libraries.md)

