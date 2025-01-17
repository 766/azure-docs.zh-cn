---
title: 使用 PowerShell 创建 Windows 虚拟桌面预览版服务主体和角色分配 - Azure
description: 了解如何使用 PowerShell 在 Windows 虚拟桌面预览版中创建服务主体并分配角色。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 44c823653ecbad1c4dd1fd35b676c8a6d8bd1620
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206659"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>教程：通过使用 PowerShell 创建服务主体和角色分配

服务主体是可以在 Azure Active Directory 中创建的、用于根据特定目的分配角色和权限的标识。 在 Windows 虚拟桌面预览版中，可以创建服务主体来实现以下目的：

- 自动执行特定的 Windows 虚拟桌面管理任务。
- 运行任何 Windows 虚拟桌面的 Azure 资源管理器模板时，将服务主体用作凭据，而无需让用户执行 MFA。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure Active Directory 中创建服务主体。
> * 在 Windows 虚拟桌面中创建角色分配。
> * 使用服务主体登录到 Windows 虚拟桌面。

## <a name="prerequisites"></a>先决条件

在创建服务主体和角色分配之前，需要先完成三项操作：

1. 安装 AzureAD 模块。 若要安装该模块，请以管理员身份运行 PowerShell，然后运行以下 cmdlet：

    ```powershell
    Install-Module AzureAD
    ```

2. 结合引号中的值（请将这些值替换为与会话相关的值）运行以下 cmdlet。

    ```powershell
    $myTenantName = "<my-tenant-name>"
    ```

3. 在同一 PowerShell 会话中，遵照本文中的所有说明操作。 如果关闭窗口，然后重新打开该窗口，则操作可能无法正常进行。

## <a name="create-a-service-principal-in-azure-active-directory"></a>在 Azure Active Directory 中创建服务主体

在 PowerShell 会话中满足先决条件后，运行以下 PowerShell cmdlet 在 Azure 中创建多租户服务主体。

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>在 Windows 虚拟桌面预览版中创建角色分配

创建服务主体后，可以使用它来登录到 Windows 虚拟桌面。 请确保使用有权创建角色分配的帐户登录。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)（如果尚未这样做），以便在 PowerShell 会话中使用。

运行以下 PowerShell cmdlet 连接到 Windows 虚拟桌面，并为服务主体创建角色分配。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>使用服务主体登录

为服务主体创建角色分配后，请通过运行以下 cmdlet，确保该服务主体可以登录到 Windows 虚拟桌面：

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

登录后，请使用该服务主体测试几个 Windows 虚拟桌面 PowerShell cmdlet，以确保一切正常工作。

## <a name="view-your-credentials-in-powershell"></a>在 PowerShell 中查看凭据

在结束 PowerShell 会话之前，请查看凭据并将其记下，以备将来参考。 请务必记下密码，因为关闭此 PowerShell 会话后，便无法检索密码。

下面是应该记下的三个凭据，以及用于获取这些凭据的 cmdlet：

- 密码：

    ```powershell
    $svcPrincipalCreds.Value
    ```

- 租户 ID：

    ```powershell
    $aadContext.TenantId.Guid
    ```

- 应用程序 ID：

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="next-steps"></a>后续步骤

创建服务主体并在 Windows 虚拟桌面租户中为其分配角色后，可用它来创建主机池。 若要详细了解主机池，请继续学习有关在 Windows 虚拟桌面中创建主机池的教程。

 > [!div class="nextstepaction"]
 > [Windows 虚拟桌面主机池教程](./create-host-pools-azure-marketplace.md)
