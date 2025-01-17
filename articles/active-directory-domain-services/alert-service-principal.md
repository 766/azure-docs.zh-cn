---
title: Azure Active Directory 域服务：服务主体疑难解答 |Microsoft Docs
description: Azure AD 域服务的服务主体配置疑难解答
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: 9e5fa8c84f5e7ca58117666846b603a118826150
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234141"
---
# <a name="troubleshoot-invalid-service-principal-configurations-for-azure-active-directory-domain-services"></a>排查 Azure Active Directory 域服务的服务主体配置无效

本文帮助你排查和解决与导致以下警报消息的配置错误相关的服务主体问题：

## <a name="alert-aadds102-service-principal-not-found"></a>警报 AADDS102：找不到服务主体

**警报消息：** 已从 Azure AD 目录中删除 Azure AD 域服务正常工作所需的服务主体。*此配置影响 Microsoft 监视、管理、修补和同步托管域的功能*。

[服务主体](../active-directory/develop/app-objects-and-service-principals.md)是指 Microsoft 用于管理、更新和维护托管域的应用程序。 如果删除服务主体，将中断 Microsoft 为域提供服务的功能。


## <a name="check-for-missing-service-principals"></a>检查是否缺少服务主体
请使用以下步骤确定是否需要重新创建服务主体：

1. 导航到 Azure 门户中的[企业应用程序 - 所有应用程序](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)页。
2. 在“显示”下拉列表中，选择“所有应用程序”，然后单击“应用”    。
3. 使用下表，通过将 ID 粘贴到搜索框中并按 Enter 来搜索每个应用程序 ID。 如果搜索结果为空，则必须按照“解决方法”列中的步骤重新创建服务主体。

| 应用程序 ID | 解决方法 |
| :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [使用 PowerShell 重新创建缺少的服务主体](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [重新注册到 Microsoft.AAD 命名空间](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [重新注册到 Microsoft.AAD 命名空间](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [重新注册到 Microsoft.AAD 命名空间](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>使用 PowerShell 重新创建缺失的服务主体
如果 Azure AD 目录中缺少 ID 为 ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` 的服务主体，请按照以下步骤操作。

**解决方法：** 需要 Azure AD PowerShell 来完成这些步骤。 有关安装 Azure AD PowerShell 的信息，请参阅[本文](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.)。

若要解决此问题，在 PowerShell 窗口中键入以下命令：
1. 安装 Azure AD PowerShell 模块并将其导入。

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. 通过执行以下 PowerShell 命令检查目录中是否缺失 Azure AD 域服务所需的服务主体：

    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```

3. 通过键入以下 PowerShell 命令创建服务主体：

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

4. 创建好缺少的服务主体后，请稍等两小时，然后再检查托管域的运行状况。


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>使用 Azure 门户重新注册到 Microsoft.AAD 命名空间
如果 Azure AD 目录中缺少 ID 为 ```443155a6-77f3-45e3-882b-22b3a8d431fb```、```abba844e-bc0e-44b0-947a-dc74e5d09022``` 或 ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` 的服务主体，请按照以下步骤操作。

**解决方法：** 使用以下步骤还原目录上的域服务：

1. 导航到 Azure 门户中的[订阅](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)页。
2. 从与托管域相关联的表中选择订阅
3. 使用左侧的导航，选择“资源提供程序” 
4. 在表中搜索“Microsoft.AAD”并单击“重新注册” 
5. 请在两小时后查看托管域的运行状况页面，确保已解除警报。


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>警报 AADDS105：密码同步应用程序已过期

**警报消息：** 应用程序 ID 为“d87dcbc6-a371-462e-88e3-28ad15ec4e64”的服务主体已删除并重新创建。 重新创建会导致在为托管域提供服务所需的 Azure AD 域服务资源上出现不一致的权限。 托管域上的密码同步可能会受影响。


**解决方法：** 需要 Azure AD PowerShell 来完成这些步骤。 有关安装 Azure AD PowerShell 的信息，请参阅[本文](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.)。

若要解决此问题，在 PowerShell 窗口中键入以下命令：
1. 安装 Azure AD PowerShell 模块并将其导入。

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. 使用以下 PowerShell 命令删除旧的应用程序和对象

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. 删除这两者后，系统将修正自身并重新创建密码同步所需的应用程序。 若要确保警报已修正，请等待两个小时，然后检查域的运行状况。


## <a name="contact-us"></a>联系我们
欢迎联系 Azure Active Directory 域服务产品团队[分享看法或请求支持](contact-us.md)。
