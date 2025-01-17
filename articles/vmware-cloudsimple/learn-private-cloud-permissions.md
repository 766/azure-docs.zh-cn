---
title: Azure VMware 解决方案 (按 CloudSimple)-私有云权限模型
description: 介绍 CloudSimple 私有云权限模型、组和类别
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab74b37fa8adc58f8641893b4168f71c453b0fdd
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972706"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>VMware vCenter 的 CloudSimple 私有云权限模型

CloudSimple 保留对私有云环境的完全管理访问权限。 为每个 CloudSimple 客户授予了足够的管理权限, 以便在其环境中部署和管理虚拟机。  如果需要, 您可以暂时提升您的权限以执行管理功能。

## <a name="cloud-owner"></a>云所有者

在创建私有云时, 将在 vCenter 单一登录域中创建**CloudOwner**用户, 并使用**云所有者角色**访问权限来管理私有云中的对象。 此用户还可以设置其他[VCenter 标识源](set-vcenter-identity.md), 并将其他用户设置为私有云 vCenter。

> [!NOTE]
> CloudSimple 私有云 vCenter 的默认用户是`cloudowner@cloudsimple.local`在创建私有云时, 无需将其链接到本地 vCenter。

## <a name="user-groups"></a>用户组

在部署私有云的过程中, 会创建一个名为 "**云所有者组**" 的组。 此组中的用户可以管理私有云上的 vSphere 环境的各个部分。 此组会自动授予**Cloud 所有者角色**的权限, 并且**CloudOwner**用户将添加为此组的成员。  CloudSimple 创建具有受限管理权限的其他组。  你可以将任何用户添加到这些预先创建的组, 并且会自动将以下定义的权限分配给这些组中的用户。

### <a name="pre-created-groups"></a>预先创建的组

| 组名称 | 用途 | Role |
| -------- | ------- | ------ |
| 云所有者-组 | 此组的成员具有对私有云 vCenter 的管理权限 | [云所有者-角色](#cloud-owner-role) |
| 云-全局-群集-管理组 | 此组的成员具有对私有云 vCenter 群集的管理权限 | [云群集-管理角色](#cloud-cluster-admin-role) |
| 云-全局存储管理组 | 此组的成员可以管理私有云 vCenter 上的存储 | [云存储-管理员角色](#cloud-storage-admin-role) |
| 云-全局-网络管理组 | 此组的成员可以管理私有云 vCenter 上的网络和分布式端口组 | [云网络-管理员角色](#cloud-network-admin-role) |
| 云-全局-VM-管理组 | 此组的成员可以管理私有云 vCenter 上的虚拟机 | [云-VM-管理员角色](#cloud-vm-admin-role) |

若要授予各个用户管理私有云的权限, 请创建用户帐户并将其添加到相应的组。

## <a name="list-of-vcenter-privileges-for-default-roles"></a>默认角色的 vCenter 权限列表

### <a name="cloud-owner-role"></a>云所有者-角色

| **类别** | **特权** |
|----------|-----------|
| **警报** | 确认警报 <br> 创建警报 <br> 禁用警报操作 <br> 修改警报 <br> 删除警报 <br> 设置警报状态 |
| **权限** | 修改权限 |
| **内容库** | 添加库项 <br> 创建本地库 <br> 创建订阅的库 <br> 删除库项 <br> 删除本地库 <br> 删除订阅的库 <br> 下载文件 <br> 逐出库项 <br> 逐出订阅的库 <br> 导入存储 <br> 探测订阅信息 <br> 读取存储 <br> 同步库项 <br> 同步订阅的库 <br> 键入自检 <br> 更新配置设置 <br> 更新文件 <br> 更新库 <br> 更新库项 <br> 更新本地库 <br> 更新订阅的库 <br> 查看配置设置 |
| **加密操作** | 添加磁盘 <br> 克隆 <br> 解密 <br> 直接访问 <br> 加密 <br> 加密新 <br> 管理 KMS <br> 管理加密策略 <br> 管理键 <br> 迁移 <br> Recrypt <br> 注册 VM <br> 注册主机 |
| **dvPort 组** | 创建 <br> DELETE <br> 修改 <br> 策略操作 <br> 作用域操作 |
| **Ipam** | 分配空间 <br> 浏览数据存储 <br> 配置数据存储 <br> 低级别文件操作 <br> 移动数据存储 <br> 删除数据存储 <br> 删除文件 <br> 重命名数据存储 <br> 更新虚拟机文件 <br> 更新虚拟机元数据 |
| **ESX 代理程序管理器** | 配置 <br> 修改 <br> 视图 |
| **扩展** | 注册扩展 <br> 注销扩展 <br> 更新扩展 |
| **外部统计信息提供程序**| 注册 <br> 注销 <br> Update |
| **文件夹** | 创建文件夹 <br> 删除文件夹 <br> 移动文件夹 <br> 重命名文件夹 |
| **全局** | 取消任务 <br> 容量计划 <br> 诊断 <br> 禁用方法 <br> 启用方法 <br> 全局标记 <br> 健康 <br> 许可证 <br> 日志事件 <br> 管理自定义属性 <br> 代理 <br> 脚本操作 <br> 服务管理器 <br> 设置自定义属性 <br> 系统标记 |
| **运行状况更新提供程序** | 注册 <br> 注销 <br> Update |
| **主机 > 配置** | 存储分区配置 |
| **主机 > 清单** | 修改群集 |
| **vSphere 标记** | 分配或取消分配 vSphere 标记 <br> 创建 vSphere 标记 <br> 创建 vSphere 标记类别 <br> 删除 vSphere 标记 <br> 删除 vSphere 标记类别 <br> 编辑 vSphere 标记 <br> 编辑 vSphere 标记类别 <br> 为类别修改 UsedBy 字段 <br> 修改标记的 UsedBy 字段 |
| **网络** | 分配网络 <br> 配置 <br> 移动网络 <br> Remove |
| **性能** | 修改间隔 |
| **主机配置文件** | 视图 |
| **资源** | 应用建议 <br> 将 vApp 分配到资源池 <br> 将虚拟机分配到资源池 <br> 创建资源池 <br> 迁移关闭的虚拟机 <br> 迁移已启动的虚拟机 <br> 修改资源池 <br> 移动资源池 <br> 查询 vMotion <br> 删除资源池 <br> 重命名资源池 |
| **计划任务** | 创建任务 <br> 修改任务 <br> 删除任务 <br> 运行任务 |
| **会话** | 模拟用户 <br> 消息 <br> 验证会话 <br> 查看和停止会话 |
| **数据存储群集** | 配置数据存储群集 |
| **配置文件驱动的存储** | 配置文件驱动的存储更新 <br> 配置文件驱动的存储视图 |
| **存储视图** | 配置服务 <br> 视图 |
| **任务** | 创建任务 <br> 更新任务 |
| **传输服务**| 管理 <br> 监视器 |
| **vApp** | 添加虚拟机 <br> 分配资源池 <br> 分配 vApp <br> 克隆 <br> 创建 <br> DELETE <br> 导出 <br> 导入 <br> 移动 <br> 关机 <br> 开机 <br> 重命名 <br> 挂起 <br> 注销 <br> 查看 OVF 环境 <br> vApp 应用程序配置 <br> vApp 实例配置 <br> vApp managedBy 配置 <br> vApp 资源配置 |
| **VRMPolicy** | 查询 VRMPolicy <br> 更新 VRMPolicy |
| **虚拟机 > 配置** | 添加现有磁盘 <br> 添加新磁盘 <br> 添加或删除设备 <br> 高级 <br> 更改 CPU 计数 <br> 更改资源 <br> 配置 managedBy <br> 磁盘更改跟踪 <br> 磁盘租约 <br> 显示连接设置 <br> 扩展虚拟磁盘 <br> 主机 USB 设备 <br> 内存 <br> 修改设备设置 <br> 查询容错兼容性 <br> 查询无主文件 <br> 原始设备 <br> 从路径重载 <br> 删除磁盘 <br> 重命名 <br> 重置来宾信息 <br> 设置批注 <br> 设置 <br> 交换文件位置 <br> 切换分叉父级 <br> 解锁虚拟机 <br> 升级虚拟机的兼容性 |
| **虚拟机 > 来宾操作** | 修改来宾操作别名 <br> 来宾操作别名查询 <br> 来宾操作修改 <br> 来宾操作程序执行 <br> 来宾操作查询 |
| **虚拟机 > 交互** | 回答问题 <br> 虚拟机上的备份操作 <br> 配置 CD 媒体 <br> 配置软盘媒体 <br> 控制台交互 <br> 创建屏幕截图 <br> 磁盘碎片整理 <br> 设备连接 <br> 拖放 <br> 通过 VIX API 进行来宾操作系统管理 <br> 插入 USB HID 扫描代码 <br> 暂停或取消暂停 <br> 执行擦除或收缩操作 <br> 关机 <br> 开机 <br> 在虚拟机上记录会话 <br> 在虚拟机上重播会话 <br> 重置 <br> 恢复容错 <br> 挂起 <br> 挂起容错 <br> 测试故障转移 <br> 测试重新启动辅助 VM <br> 关闭容错 <br> 启用容错 <br> VMware 工具安装 |
| **虚拟机 > 清单** | 从现有创建 <br> 新建 <br> 移动 <br> 注册 <br> Remove <br> 注销 |
| **虚拟机 > 预配** | 允许磁盘访问 <br> 允许文件访问 <br> 允许只读磁盘访问 <br> 允许下载虚拟机 <br> 允许上传虚拟机文件 <br> 克隆模板 <br> 克隆虚拟机 <br> 从虚拟机创建模板 <br> 自定义 <br> 部署模板 <br> 标记为模板 <br> 标记为虚拟机 <br> 修改自定义规范 <br> 提升磁盘 <br> 读取自定义规范 |
| **虚拟机 > 服务配置** | 允许通知 <br> 允许轮询全局事件通知 <br> 管理服务配置 <br> 修改服务配置 <br> 查询服务配置 <br> 读取服务配置 |
| **虚拟机 > 快照管理** | 创建快照 <br> 删除快照 <br> 重命名快照 <br> 还原到快照 |
| **虚拟机 > vSphere 复制** | 配置复制 <br> 管理复制 <br> 监视复制 |
| **vService** | 创建依赖关系 <br> 销毁依赖项 <br> 重新配置依赖项配置 <br> 更新依赖项 |

### <a name="cloud-cluster-admin-role"></a>云群集-管理角色

| **类别** | **特权** |
|----------|-----------|
| **Ipam** | 分配空间 <br> 浏览数据存储 <br> 配置数据存储 <br> 低级别文件操作 <br> 删除数据存储 <br> 重命名数据存储 <br> 更新虚拟机文件 <br> 更新虚拟机元数据 |
| **文件夹** | 创建文件夹 <br> 删除文件夹 <br> 移动文件夹 <br> 重命名文件夹 |
| **主机 > 配置**  | 存储分区配置 |
| **vSphere 标记** | 分配或取消分配 vSphere 标记 <br> 创建 vSphere 标记 <br> 创建 vSphere 标记类别 <br> 删除 vSphere 标记 <br> 删除 vSphere 标记类别 <br> 编辑 vSphere 标记 <br> 编辑 vSphere 标记类别 <br> 为类别修改 UsedBy 字段 <br> 修改标记的 UsedBy 字段 |
| **网络** | 分配网络 |
| **资源** | 应用建议 <br> 将 vApp 分配到资源池 <br> 将虚拟机分配到资源池 <br> 创建资源池 <br> 迁移关闭的虚拟机 <br> 迁移已启动的虚拟机 <br> 修改资源池 <br> 移动资源池 <br> 查询 vMotion <br> 删除资源池 <br> 重命名资源池 |
| **vApp** | 添加虚拟机 <br> 分配资源池 <br> 分配 vApp <br> 克隆 <br> 创建 <br> DELETE <br> 导出 <br> 导入 <br> 移动 <br> 关机 <br> 开机 <br> 重命名 <br> 挂起 <br> 注销 <br> 查看 OVF 环境 <br> vApp 应用程序配置 <br> vApp 实例配置 <br> vApp managedBy 配置 <br> vApp 资源配置 |
| **VRMPolicy** | 查询 VRMPolicy <br> 更新 VRMPolicy |
| **虚拟机 > 配置** | 添加现有磁盘 <br> 添加新磁盘 <br> 添加或删除设备 <br> 高级 <br> 更改 CPU 计数 <br> 更改资源 <br> 配置 managedBy <br> 磁盘更改跟踪 <br> 磁盘租约 <br> 显示连接设置 <br> 扩展虚拟磁盘 <br> 主机 USB 设备 <br> 内存 <br> 修改设备设置 <br> 查询容错兼容性 <br> 查询无主文件 <br> 原始设备 <br> 从路径重载 <br> 删除磁盘 <br> 重命名 <br> 重置来宾信息 <br> 设置批注 <br> 设置 <br> 交换文件位置 <br> 切换分叉父级 <br> 解锁虚拟机 <br> 升级虚拟机的兼容性 |
| **虚拟机 > 来宾操作** | 修改来宾操作别名 <br> 来宾操作别名查询 <br> 来宾操作修改 <br> 来宾操作程序执行 <br> 来宾操作查询 |
| **虚拟机 > 交互** | 回答问题 <br> 虚拟机上的备份操作 <br> 配置 CD 媒体 <br> 配置软盘媒体 <br> 控制台交互 <br> 创建屏幕截图 <br> 磁盘碎片整理 <br> 设备连接 <br> 拖放 <br> 通过 VIX API 进行来宾操作系统管理 <br> 插入 USB HID 扫描代码 <br> 暂停或取消暂停 <br> 执行擦除或收缩操作 <br> 关机 <br> 开机 <br> 在虚拟机上记录会话 <br> 在虚拟机上重播会话 <br> 重置 <br> 恢复容错 <br> 挂起 <br> 挂起容错 <br> 测试故障转移 <br> 测试重新启动辅助 VM <br> 关闭容错 <br> 启用容错 <br> VMware 工具安装
| **虚拟机 > 清单** | 从现有创建 <br> 新建 <br> 移动 <br> 注册 <br> Remove <br> 注销 |
| **虚拟机 > 预配** | 允许磁盘访问 <br> 允许文件访问 <br> 允许只读磁盘访问 <br> 允许下载虚拟机 <br> 允许上传虚拟机文件 <br> 克隆模板 <br> 克隆虚拟机 <br> 从虚拟机创建模板 <br> 自定义 <br> 部署模板 <br> 标记为模板 <br> 标记为虚拟机 <br> 修改自定义规范 <br> 提升磁盘  <br> 读取自定义规范 |
| **虚拟机 > 服务配置** | 允许通知 <br> 允许轮询全局事件通知 <br> 管理服务配置 <br> 修改服务配置 <br> 查询服务配置 <br> 读取服务配置
| **虚拟机 > 快照管理** | 创建快照 <br> 删除快照 <br> 重命名快照 <br> 还原到快照 |
| **虚拟机 > vSphere 复制** | 配置复制 <br> 管理复制 <br> 监视复制 |
| **vService** | 创建依赖关系 <br> 销毁依赖项 <br> 重新配置依赖项配置 <br> 更新依赖项 |

### <a name="cloud-storage-admin-role"></a>云存储-管理员角色

| **类别** | **特权** |
|----------|-----------|
| **Ipam** | 分配空间 <br> 浏览数据存储 <br> 配置数据存储 <br> 低级别文件操作 <br> 删除数据存储 <br> 重命名数据存储 <br> 更新虚拟机文件 <br> 更新虚拟机元数据 |
| **主机 > 配置** | 存储分区配置 |
| **数据存储群集** | 配置数据存储群集 |
| **配置文件驱动的存储** | 配置文件驱动的存储更新 <br> 配置文件驱动的存储视图 |
| **存储视图** | 配置服务 <br> 视图 |

### <a name="cloud-network-admin-role"></a>云网络-管理员角色

| **类别** | **特权** |
|----------|-----------|
| **dvPort 组** | 创建 <br> DELETE <br> 修改 <br> 策略操作 <br> 作用域操作 |
| **网络** | 分配网络 <br> 配置 <br> 移动网络 <br> Remove |
| **虚拟机 > 配置** | 修改设备设置 |

### <a name="cloud-vm-admin-role"></a>云-VM-管理员角色

| **类别** | **特权** |
|----------|-----------|
| **Ipam** | 分配空间 <br> 浏览数据存储 |
| **网络** | 分配网络 |
| **资源** | 将虚拟机分配到资源池 <br> 迁移关闭的虚拟机 <br> 迁移已启动的虚拟机
| **vApp** | 导出 <br> 导入 |
| **虚拟机 > 配置** | 添加现有磁盘 <br> 添加新磁盘 <br> 添加或删除设备 <br> 高级 <br> 更改 CPU 计数 <br> 更改资源 <br> 配置 managedBy <br> 磁盘更改跟踪 <br> 磁盘租约 <br> 显示连接设置 <br> 扩展虚拟磁盘 <br> 主机 USB 设备 <br> 内存 <br> 修改设备设置 <br> 查询容错兼容性 <br> 查询无主文件 <br> 原始设备 <br> 从路径重载 <br> 删除磁盘 <br> 重命名 <br> 重置来宾信息 <br> 设置批注 <br> 设置 <br> 交换文件位置 <br> 切换分叉父级 <br> 解锁虚拟机 <br> 升级虚拟机的兼容性 |
| **虚拟机 > 来宾操作** | 修改来宾操作别名 <br> 来宾操作别名查询 <br> 来宾操作修改 <br> 来宾操作程序执行 <br> 来宾操作查询    |
| **虚拟机 > 交互** | 回答问题 <br> 虚拟机上的备份操作 <br> 配置 CD 媒体 <br> 配置软盘媒体 <br> 控制台交互 <br> 创建屏幕截图 <br> 磁盘碎片整理 <br> 设备连接 <br> 拖放 <br> 通过 VIX API 进行来宾操作系统管理 <br> 插入 USB HID 扫描代码 <br> 暂停或取消暂停 <br> 执行擦除或收缩操作 <br> 关机 <br> 开机 <br> 在虚拟机上记录会话 <br> 在虚拟机上重播会话 <br> 重置 <br> 恢复容错 <br> 挂起 <br> 挂起容错 <br> 测试故障转移 <br> 测试重新启动辅助 VM <br> 关闭容错 <br> 启用容错 <br> VMware 工具安装 |
| **虚拟机 > 清单** | 从现有创建 <br> 新建 <br> 移动 <br> 注册 <br> Remove <br> 注销 |
| **虚拟机 > 预配** | 允许磁盘访问 <br> 允许文件访问 <br> 允许只读磁盘访问 <br> 允许下载虚拟机 <br> 允许上传虚拟机文件 <br> 克隆模板 <br> 克隆虚拟机 <br> 从虚拟机创建模板 <br> 自定义 <br> 部署模板 <br> 标记为模板 <br> 标记为虚拟机 <br> 修改自定义规范 <br> 提升磁盘 <br> 读取自定义规范 |
| **虚拟机 > 服务配置** | 允许通知 <br> 允许轮询全局事件通知 <br> 管理服务配置 <br> 修改服务配置 <br> 查询服务配置 <br> 读取服务配置
| **虚拟机 > 快照管理** | 创建快照 <br> 删除快照 <br> 重命名快照 <br> 还原到快照 |
| **虚拟机 > vSphere 复制** | 配置复制 <br> 管理复制 <br> 监视复制 |
| **vService** | 创建依赖关系 <br> 销毁依赖项 <br> 重新配置依赖项配置 <br> 更新依赖项 |
