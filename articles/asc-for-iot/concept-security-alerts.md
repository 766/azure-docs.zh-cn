---
title: 有关 IoT 预览版 ASC 安全警报指南 |Microsoft Docs
description: 了解有关安全警报和建议的补救措施使用 ASC 适用于 IoT 功能和服务。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 23af0d9e2d7c4033a2457676b29b5e4b2f8c15ea
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579438"
---
# <a name="asc-for-iot-security-alerts"></a>ASC 为 IoT 安全警报的

> [!IMPORTANT]
> Iot ASC 目前处于公共预览状态。
> 此预览版本没有附带服务级别协议提供，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

适用于 IoT 的 ASC 会持续分析 IoT 解决方案是使用高级的分析和威胁智能用以提醒恶意活动。
此外，您可以创建自定义警报根据预期的设备行为的了解。
警报充当一个指示器 comprise，并应调查和修正。

在本文中，您将发现内置警报可以触发 IoT 中心和/或 IoT 设备上的列表。
内置警报旁边 iot ASC 可以定义基于预期的中心和/或设备行为的自定义警报。
有关更多详细信息，请参阅[创建自定义警报](quickstart-create-custom-alerts.md)。

## <a name="built-in-alerts-for-iot-devices"></a>IoT 设备的的内置警报

| 严重性 | 名称                                                   | 数据源 | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|----------|--------------------------------------------------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 高     | 成功的本地登录名                                 | 代理       | 检测到成功的本地设备登录                                                                                                                                                                                                                                                                                                                                                                                         |
| 高     | 成功的暴力破解                                  | 代理       | 检测到多个失败登录尝试次数后, 跟一个成功的登录名。 设备遇到了成功的暴力破解攻击。                                                                                                                                                                                                                                                                                                              |
| 高     | 二进制的命令行                                    | 代理       | Linux 的二进制文件调用，或从命令行检测到执行。 此过程可能是合法活动，或可能表示你的设备遭到破坏。                                                                                                                                                                                                                                                                  |
| 高     | 反向 shell                                         | 代理       | 分析主机数据，检测到潜在的反向 shell 使用。 反向 shell 攻击通常用于获取新的受攻击的计算机，将回调到由攻击者拥有的计算机。                                                                                                                                                                                                                                                                                          |
| 高     | 禁用对主机防火墙                                       | 代理       | 在主机防火墙检测到的可能操作。 攻击者通常会禁用上主机防火墙泄露数据。                                                                                                                                                                                                                                                                                                                                    |
| 高     | Web shell                                              | 代理       | 检测到的 web shell 的可能用途。 通常，攻击者将 web shell 上载到用于获取持久性在环境中或以进一步利用漏洞攻击计算机。                                                                                                                                                                                                                                                                              |
| 高     | 端口转发                                        | 代理       | 检测到的启动的端口转发到外部 IP 地址。                                                                                                                                                                                                                                                                                                                                                                       |
| 高     | 可能尝试禁用检测到的审核日志记录    | 代理       | Linux 审核系统提供了一种方法来跟踪安全相关的系统信息。 审核记录尽可能多可能的有关您的系统发生的事件的信息。  审核信息的关键环境，以便能够确定安全策略违规者并了解它们所执行的操作至关重要。 禁用审核日志记录可以暂停，发现的系统上使用的安全策略冲突。 |
| 中型   | 无效的 SAS 令牌签名                            | IoT 中心     | 设备使用 SAS 令牌具有无效签名。 SAS 令牌与主密钥或辅助密钥不匹配。                                                                                                                                                                                                                                                                                                                               |
| 中型   | x.509 设备证书指纹不匹配           | IoT 中心     | 将 X.509 设备证书指纹与配置不匹配                                                                                                                                                                                                                                                                                                                                                                             |
| 中型   | X.509 证书已过期                              | IoT 中心     | X.509 设备证书已过期                                                                                                                                                                                                                                                                                                                                                                                                        |
| 中型   | 失败的本地登录                                     | 代理       | 检测到对设备的本地登录失败的尝试。                                                                                                                                                                                                                                                                                                                                                                                     |
| 中型   | 加密硬币 miner                                      | 代理       | 检测到通常与数字货币挖掘关联的进程的执行。                                                                                                                                                                                                                                                                                                                                                        |
| 中型   | 已知的凭据访问工具                          | 代理       | 检测到使用情况的通常与尝试访问凭据的恶意执行组件关联的工具。                                                                                                                                                                                                                                                                                                                                                             |
| 中型   | 加密硬币大亨容器映像                      | 代理       | 检测到运行已知数字货币挖掘程序映像的容器。                                                                                                                                                                                                                                                                                                                                                                     |
| 中型   | 已知的攻击工具                                      | 代理       | 检测到通常与恶意用户攻击其他计算机以某种方式相关的工具。                                                                                                                                                                                                                                                                                                                                              |
| 中型   | 检测到的 htaccess 文件的访问权限                       | 代理       | 分析主机数据，检测到 htaccess 文件的可能操作。 Htaccess 是功能强大的配置文件，以允许您为运行 Apache Web 软件包括基本重定向功能的 web 服务器或更高级的功能，如基本密码保护的多个更改。 攻击者通常会修改 htaccess 它们已破坏获得持久性的计算机上的文件。                    |
| 中型   | 行为类似于检测到的常见 Linux 机器人         | 代理       | 通常与检测到的常见 Linux 僵尸网络关联的进程的执行。                                                                                                                                                                                                                                                                                                                                                              |
| 中型   | 可疑的文件然后下载运行活动             | 代理       | 分析主机数据，检测到正在下载并运行在同一命令中的文件。 这是一种常用技术，攻击者使用以获取到受害者计算机上的恶意文件。                                                                                                                                                                                                                                                              |
| 中型   | 检测到的数据丢失                         | 代理       | 主机数据分析检测到可能的数据出口 （数据丢失） 条件。 通常，攻击者通常出口已破坏的计算机中的数据。                                                                                                                                                                                                                                                                                                      |
| 中型   | 文件名后空格| 代理 | 主机数据的分析表明执行的进程带有可疑的扩展名。 可疑的扩展插件可以欺骗用户在考虑文件可以放心地打开，并可能表示系统上的恶意软件是否存在。                                                                                                                                                                                                                   |
| 中型   | 通过 TCP 套接字的公开的 Docker 守护程序                    | 代理       | 计算机日志指示你的 Docker 守护程序 (dockerd) 公开 TCP 套接字。 默认情况下，Docker 配置，不使用加密或身份验证时启用 TCP 套接字。 公开的 TCP 套接字启用完全访问权限的任何人有权访问到 Docker 守护程序的相关端口。                                                                                                                                                          |
| 中型   | 类似于 Fairware 勒索软件检测到的行为       | 代理       | 主机数据分析检测到 rm-rf 命令应用于可疑位置的执行。 Rm-rf 以递归方式删除文件，并在离散的文件夹通常情况下使用它。 目前，它使用已很多可能被会删除你的数据的位置。 已知 Fairware 勒索软件是在此文件夹中执行 rm-rf 命令。                                                                                                    |
| 中型   | 检测到的可能后门                             | 代理       | 分析主机数据，检测到可疑的文件已被下载并随后运行的可疑文件源自在订阅中下载。 此活动之前已与安装的后门程序相关联。                                                                                                                                                                                                                                                |
| 中型   | 检测到的本地主机侦测                     | 代理       | 主机数据分析检测到执行的命令通常与常见 Linux 机器人侦测相关联。                                                                                                                                                                                                                                                                                                                         |
| 中型   | 常见的文件可能重写                   | 代理       | 在设备上覆盖常见可执行文件。 攻击者通常作为一种方法来隐藏其操作，或获取在环境中的持久性覆盖公共文件。                                                                                                                                                                                                                                                                                              |
| 中型   | 从已知恶意源检测到的文件下载   | 代理       | 设备数据的分析检测到已知的恶意软件的来源的文件的下载。                                                                                                                                                                                                                                                                                                                                                    |
| 中型   | 检测到特权的容器                          | 代理       | 计算机日志指示特权的 Docker 容器正在运行。 特权的容器具有完全访问权限的主机资源。 如果受到破坏，攻击者可以使用特权的容器获得主机计算机的访问权限。                                                                                                                                                                                                     |
| 中型   | 行为类似于检测到的勒索软件                | 代理       | 设备数据的分析检测到的文件具有相似的已知的勒索软件的可执行阻止用户访问他们的系统或个人文件和需求 ransom 付款为了重新获得访问权限。                                                                                                                                                                                                           |
| 中型   | 删除的系统日志检测到的文件                  | 代理       | 分析主机数据，检测到可疑删除主机上的日志文件。                                                                                                                                                                                                                                                                                                                                                             |
| 中型   | 脚本解释器和文件扩展名之间不匹配 | 代理       | 分析主机数据，检测到脚本解释器和脚本文件作为输入提供的扩展名之间不匹配。 这通常是与攻击者脚本执行相关联。                                                                                                                                                                                                                                       |
| 中型   | 检测到可疑的编译                        | 代理       | 分析主机数据，检测到可疑的编译。 攻击者通常会编译已破坏的计算机上的攻击以提升的权限。                                                                                                                                                                                                                                                                                     |
| 中型   | 检测到可疑 nohup 命令使用           | 代理       | 分析主机数据，检测到可疑 nohup 命令在主机上使用。 攻击者已看过从临时目录运行命令 nohup 以便其可执行文件在后台运行。 不正常的位于临时目录中文件上运行此命令。                                                                                                                                      |
| 中型   | 检测到可疑的 useradd 命令使用         | 代理       | 分析主机数据，检测到可疑的 useradd 命令在设备上使用                                                                                                                                                                                                                                                                                                                                                      |
| 中型   | 可疑的 IP 地址通信                    | 代理       | 检测到可疑 IP 地址的通信，基于对设备数据的分析。                                                                                                                                                                                                                                                                                                                                                         |
| 低      | SAS 令牌已过期                                      | IoT 中心     | 设备已使用过期的 SAS 令牌                                                                                                                                                                                                                                                                                                                                                                                                   |
| 低      | 设备无提示                                          | 代理       | 设备过去 72 小时内不发送任何遥测数据                                                                                                                                                                                                                                                                                                                                                                                 |
| 低      | 清除 bash 历史记录                                   | 代理       | 清除 bash 历史记录日志。 攻击者可以隐藏自己的命令从日志中显示                                                                                                                                                                                                                                                                                                                                                   |
| 低      | 失败的暴力破解                                      | 代理       | 标识多个失败登录尝试次数。 已尝试并且失败的暴力攻击强制攻击在设备上进行。                                                                                                                                                                                                                                                                                                                                                  |

## <a name="built-in-alerts-for-iot-hub"></a>IoT 中心的内置警报

| 严重性 | 名称                                                                         | 描述                                                                                                                                                                                                                                                                                                                            |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 中型   | 已添加到 IoT 中心的新证书                                    |已添加到 IoT 中心的新证书。 如果此添加不由经过授权的参与方，则可能表示恶意活动。                                                                                                                                                                                                    |
| 中型   | 从 IoT 中心已删除证书                                    | 从 IoT 中心已删除的证书。 如果此操作不由经过授权的参与方，则可能表示恶意活动。                                                                                                                                                                                                |
| 中型   | 若要将证书添加到 IoT 中心检测到的尝试失败     | 若要添加到 IoT 中心的证书检测到的不成功尝试。 如果此操作不由经过授权的参与方，则可能表示恶意活动。                                                                                                                                                                     |
| 中型   | 检测到从 IoT 中心中删除证书的尝试失败 | 检测到不成功尝试从 IoT 中心中删除证书。 如果此操作不由经过授权的参与方，则可能表示恶意活动。                                                                                                                                                                |
| 低      | 尝试添加或编辑检测到的 IoT 中心的诊断设置    | 尝试添加或编辑已检测到 IoT 中心的诊断设置。 诊断设置，可发生安全事件或泄露您的网络时重新创建的调查的活动跟踪信息。 如果此操作不由经过授权的参与方，则可能表示恶意活动。  |
| 低      | 尝试从 IoT 中心检测到删除诊断设置       | 尝试删除检测到的 IoT 中心的诊断设置。 诊断设置，可发生安全事件或泄露您的网络时重新创建的调查的活动跟踪信息。 如果此操作不由经过授权的参与方，则可能表示恶意活动。       |
|

## <a name="see-also"></a>另请参阅

- [概述](overview.md)
- [访问安全数据](how-to-security-data-access.md)
- [调查设备](how-to-investigate-device.md)