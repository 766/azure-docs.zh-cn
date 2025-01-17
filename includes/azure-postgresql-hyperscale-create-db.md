---
title: include 文件
description: include 文件
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 05/14/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: c07e352288d7dc1d0bf198fd74c8baaded3a2d23
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173193"
---
如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>创建 Azure Database for PostgreSQL - 超大规模 (Citus)

可以按照以下步骤创建用于 PostgreSQL 的 Azure 数据库：
1. 在 Azure 门户的左上角单击“创建资源”。 
2. 从“新建”页中选择“数据库”，并从“数据库”页中选择“用于 PostgreSQL 的 Azure 数据库”。    
3. 对于部署选项，请单击“Hyperscale (Citus)服务器组 - 预览版”下的“创建”按钮   。
4. 使用以下信息填写“新服务器详细信息”窗体：
   - 资源组：单击此字段的文本框下的“新建”链接  。 输入一个名称，例如 **myresourcegroup**。
   - 服务器组名称：输入新服务器组的唯一名称，该名称也将用于服务器子域。
   - 管理员用户名：当前必须是值 **citus**，并且不能更改。
   - 密码：长度必须至少为八个字符，且必须包含以下类别中三种类别的字符 - 英文大写字母、英文小写字母、数字 (0-9) 和非字母数字字符（!、$、#、%，等等。）
   - 位置：使用距离你的用户最近的位置，使用户可以最快速度访问数据。

   > [!IMPORTANT]
   > 登录到服务器及其数据库时需要使用在此处指定的服务器管理员密码。 请牢记或记录此信息，以后会使用到它。

5. 单击“配置服务器组”  。 保留该部分的设置不变，单击“保存”  。
6. 单击“查看 + 创建”，然后单击“创建”，预配服务器   。 预配需要数分钟。
7. 页面会重定向，以监视部署。 当实时状态从“部署正在进行”变为“部署已完成”时，单击页面左侧的“输出”菜单项    。
8. 输出页将包含协调器主机名，主机名旁边有一个按钮，用于将值复制到剪贴板。 记录此信息以供将来使用。

## <a name="configure-a-server-level-firewall-rule"></a>配置服务器级防火墙规则

Azure Database for PostgreSQL – Hyperscale (Citus)（预览版）服务在服务器级别使用防火墙。 默认情况下，防火墙会阻止所有外部应用程序和工具连接到内部的协调器节点和任何数据库。 我们必须添加一个规则，针对特定的 IP 地址范围打开防火墙。

1. 从你之前复制协调器节点主机名的“输出”部分，单击“概述”菜单项   。

2. 找到你的部署的服务器组的名称并单击它。 （服务器组名称将没有  后缀。 例如，名称以“-c”、“-w0”或“-w1”结尾的项不是服务器组。）

3. 在左侧菜单中的“安全性”下方单击“防火墙”   。

4. 单击“+ 针对当前客户端 IP 地址添加防火墙规则”  链接。

5. 最后，单击“保存”按钮  。

   > [!NOTE]
   > Azure PostgreSQL 服务器通过端口 5432 进行通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 5432 的出站流量。 若是如此，则无法连接到 Azure SQL 数据库服务器，除非 IT 部门启用了端口 5432。
   >

## <a name="connect-to-the-database-using-psql"></a>使用 psql 连接到数据库

创建 Azure Database for PostgreSQL 服务器时，会创建名为 **citus** 的默认数据库。 若要连接到你的数据库服务器，需要具有连接字符串和管理员密码。

1. 获取连接字符串值。 在服务器组页面中，单击“连接字符串”  菜单项。 （它位于“设置”  下。）查找标为 **C++ (libpq)** 的字符串。 它的形式如下：

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   复制此字符串。 你需要将“{your\_password}”替换为你之前选择的管理员密码。 系统不存储你的纯文本密码，因此无法在连接字符串中为你显示密码。

2. 在本地计算机上打开一个终端窗口。

3. 在提示符下，使用 [psql](https://www.postgresql.org/docs/current/app-psql.html) 实用工具连接到你的 Azure Database for PostgreSQL 服务器。 将连接字符串放在引号中传递，请确保其中包含密码：
   ```bash
   psql "{connection_string}"
   ```

   例如，以下命令连接到服务器组 **mydemoserver** 的协调器节点：

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
