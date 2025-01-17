---
title: 对未能启动的角色进行故障排除 | Microsoft Docs
description: 以下是云服务角色无法启动的一些常见原因。 此外还提供了这些问题的解决方案。
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 9e1ae9ca6106a6a55e0cfc933ad47a5944ed5abb
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945398"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>对无法启动的云服务角色进行故障排除
以下是一些与无法启动的 Azure 云服务角色相关的常见问题和解决方案。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>缺少 DLL 或依赖项
DLL 或程序集缺失可能导致出现不响应的角色以及在“**正在初始化**”、“**忙**”和“**正在停止**”状态之间循环的角色。

DLL 或程序集缺失的症状可能为：

* 角色实例的状态在“**正在初始化**”、“**忙**”、“**正在停止**”之间循环。
* 角色实例已转为“**就绪**”状态，但在导航到 Web 应用程序时未显示相应页面。

若要调查这些问题，可采用几种推荐的方法。

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>在 Web 角色中诊断缺失 DLL 的问题
如果导航到在 Web 角色中部署的网站，且浏览器显示类似于下面的服务器错误，可能指示 DLL 缺失。

!['/' 应用程序中出现服务器错误。](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>通过关闭自定义错误来诊断问题
可通过配置 Web 角色的 web.config，将自定义错误模式设置为“关闭”并重新部署服务，来查看更完整的错误信息。

若要在不使用远程桌面的情况下查看更完整的错误，请执行以下操作：

1. 在 Microsoft Visual Studio 中打开解决方案。
2. 在“**解决方案资源管理器**”中，找到 web.config 文件并打开。
3. 在 web.config 文件中，找到 system.web 部分并添加以下行：

    ```xml
    <customErrors mode="Off" />
    ```
4. 保存该文件。
5. 重新打包并重新部署服务。

重新部署服务后，会看到错误消息，其中包含缺失的程序集或 DLL 的名称。

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>通过远程查看错误来诊断问题
可使用远程桌面来访问角色并远程查看更完整的错误信息。 通过以下步骤使用远程桌面来查看错误：

1. 确保安装了 Azure SDK 1.3 或更高版本。
2. 在使用 Visual Studio 部署解决方案的过程中，启用远程桌面。 有关详细信息，请参阅[使用 Visual Studio 为 Azure 云服务中的角色启用远程桌面连接](cloud-services-role-enable-remote-desktop-visual-studio.md)。
3. 在 Microsoft Azure 门户中，一旦实例显示“就绪”状态，则远程连接到该实例。 有关通过云服务使用远程桌面的详细信息，请参阅[远程连接到角色实例](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances)。
5. 使用在远程桌面配置期间指定的凭据登录到虚拟机。
6. 打开命令窗口。
7. 键入 `IPconfig`。
8. 记录 IPV4 地址值。
9. 打开 Internet Explorer。
10. 键入 Web 应用程序的地址和名称。 例如，`http://<IPV4 Address>/default.aspx`。

现在，导航到网站将返回更明确的错误消息：

* '/' 应用程序中出现服务器错误。
* 描述:执行当前 web 请求期间发生未处理的异常。 请检查堆栈跟踪信息，以了解有关该错误以及代码中导致错误的出处的详细信息。
* 异常详细信息：System.IO.FIleNotFoundException:无法加载文件或程序集 "Windowsazure.storage. StorageClient, Version = 1.1.0.0, Culture = 中立, PublicKeyToken = 31bf856ad364e35" 或其依赖项之一。 系统找不到指定文件。

例如：

!['/' 应用程序中出现显式服务器错误](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>使用计算模拟器诊断问题
可以使用 Microsoft Azure 计算模拟器来诊断并解决缺失依赖项和出现 web.config 错误的问题。

为了在使用此诊断方法时获得最佳结果，应使用包含 Windows 的干净安装的计算机或虚拟机。 若要以最佳效果模拟 Azure 环境，请使用 Windows Server 2008 R2 x64。

1. 安装独立版本的 [Azure SDK](https://azure.microsoft.com/downloads/)。
2. 在开发计算机上生成云服务项目。
3. 在 Windows 资源管理器中，导航到云服务项目的 bin\debug 文件夹。
4. 将 .csx 文件夹和 .cscfg 文件复制到用来调试问题的计算机。
5. 在干净的计算机上打开 Azure SDK 命令提示符窗口并键入 `csrun.exe /devstore:start`。
6. 在命令提示符处，键入：`run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`。
7. 角色启动后，会在 Internet Explorer 中看到详细的错误信息。 还可使用标准的 Windows 故障排除工具来进一步诊断问题。

## <a name="diagnose-issues-by-using-intellitrace"></a>使用 IntelliTrace 诊断问题
对于使用 .NET Framework 4 的辅助角色和 Web 角色，可以使用 Microsoft Visual Studio Enterprise 中提供的 [IntelliTrace](/visualstudio/debugger/intellitrace)。

请按照以下步骤操作来部署启用了 IntelliTrace 的服务：

1. 确认已安装 Azure SDK 1.3 或更高版本。
2. 使用 Visual Studio 部署解决方案。 在部署期间，请选中“**为 .NET 4 角色启用 IntelliTrace**”复选框。
3. 实例启动后，打开“**服务器资源管理器**”。
4. 展开 **Azure\\Cloud Services** 节点并查找部署。
5. 展开部署，直至看到角色实例。 右键单击其中一个实例。
6. 选择“**查看 IntelliTrace 日志**”。 此时会打开“**IntelliTrace 摘要**”。
7. 查找摘要的异常部分。 如果存在异常，则会将该部分标记为“**异常数据**”。
8. 展开“**异常数据**”并查找类似如下内容的 **System.IO.FileNotFoundException** 错误：

![异常数据、缺少文件或程序集](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>解决缺失 DLL 和程序集的问题
若要纠正丢失 DLL 和程序集错误，请按照以下步骤进行操作：

1. 在 Visual Studio 中打开解决方案。
2. 在“**解决方案资源管理器**”中，打开 **References** 文件夹。
3. 单击错误中标识的程序集。
4. 在“属性”窗格中，找到“复制本地属性”并将值设置为“True”。
5. 重新部署云服务。

确认所有错误均已更正后，可以在不选中“**为 .NET 4 角色启用 IntelliTrace**”复选框的情况下部署服务。

## <a name="next-steps"></a>后续步骤
查看更多针对云服务的[故障排除文章](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services)。

若要了解如何使用 Azure PaaS 计算机诊断数据对云服务角色问题进行故障排除，请参阅 [Kevin Williamson 博客系列](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)。
