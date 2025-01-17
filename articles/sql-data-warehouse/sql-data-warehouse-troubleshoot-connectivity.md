---
title: 排查 Azure SQL 数据仓库问题 | Microsoft Docs
description: 排查 Azure SQL 数据仓库问题。
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 290753b866f15e09a52572fdd7a43a60fc2812d6
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575539"
---
# <a name="troubleshooting-connectivity-issues"></a>排查连接问题

本文列出了有关连接到 SQL 数据仓库的常见故障排除方法。
- [检查服务可用性](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [检查是否已暂停或缩放操作](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [检查防火墙设置](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [检查 VNet/服务终结点设置](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [检查最新的驱动程序](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [检查连接字符串](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [间歇性连接问题](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [常见错误消息](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>检查服务可用性

检查该服务是否可用。 在 Azure 门户中, 请前往要尝试连接的 SQL 数据仓库。 在左侧的 TOC 面板中, 单击 "**诊断并解决问题**"。

![选择资源运行状况](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

你的 SQL 数据仓库的状态将显示在此处。 如果服务未显示为 "**可用**", 请查看其他步骤。

![可用服务](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

如果资源运行状况显示数据仓库已暂停或正在缩放, 请按照指南操作以恢复数据仓库。

![服务暂停](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)有关资源运行状况的其他信息可在此处找到。

## <a name="check-for-paused-or-scaling-operation"></a>检查是否已暂停或缩放操作

检查门户, 查看是否已暂停或缩放 SQL 数据仓库。

![已暂停服务](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

如果你发现你的服务已暂停或正在缩放, 请检查其在维护计划期间是否不会出现。 在 SQL 数据仓库*概述*门户上, 你将看到选择的维护计划。

![概述维护计划](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

否则, 请与 IT 管理员联系以验证此维护不是计划事件。 若要恢复 SQL 数据仓库, 请执行[此处](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute)概述的步骤。

## <a name="check-your-firewall-settings"></a>检查防火墙设置

SQL 数据仓库通过端口 1433 进行通信。   如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 1433 的出站流量。 在这种情况下, 你无法连接到 Azure SQL 数据库服务器, 除非 IT 部门打开了端口1433。 有关防火墙配置的其他信息, 请参阅[此处](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#manage-server-level-ip-firewall-rules-using-the-azure-portal)。

## <a name="check-your-vnetservice-endpoint-settings"></a>检查 VNet/服务终结点设置

如果收到错误40914和 40615, 请参阅[此处的错误描述和解决方法](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)。

## <a name="check-for-the-latest-drivers"></a>检查最新的驱动程序

### <a name="software"></a>软件

请检查以确保使用最新的工具连接到 SQL 数据仓库:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>驱动程序

请检查以确保使用的是最新的驱动程序版本。  使用较旧版本的驱动程序可能会导致意外的行为, 因为旧驱动程序可能不支持新功能。

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>检查连接字符串

检查以确保正确设置了连接字符串。  下面是一些示例。  可在此处找到有关[连接字符串](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings)的其他信息。

ADO.NET 连接字符串

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC 连接字符串

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP 连接字符串

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC 连接字符串

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>间歇性连接问题

检查是否在服务器上遇到大量具有大量排队请求的负载。 可能需要为数据仓库扩展其他资源。

## <a name="common-error-messages"></a>常见错误消息

错误40914和 40615, 请在[此处查看错误说明和解决方法](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)。

## <a name="still-having-connectivity-issues"></a>仍有连接问题？
创建[支持票证](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket), 使工程团队能够为你提供支持。
