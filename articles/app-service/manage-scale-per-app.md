---
title: 高密度托管使用每个应用缩放-Azure 应用服务 |Microsoft Docs
description: 在 Azure 应用服务上进行高密度托管
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 824abbdfd1b3980b419e6d6c46814bb0318adf13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602330"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>使用每个应用缩放 Azure 应用服务上进行高密度托管

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

使用应用服务时，可以通过缩放来缩放您的应用程序[应用服务计划](overview-hosting-plans.md)其上运行。 当多个应用在同一个应用服务计划中运行时，每个横向扩展实例会在计划中运行所有应用。

*每个应用缩放*可以在应用服务计划级别启用，以允许缩放应用时独立于承载它的应用服务计划。 这样，可以将一个应用服务计划扩展到 10 个实例，而将一个应用设置为仅用 5 个。

> [!NOTE]
> 按应用缩放仅适用于标准  、高级  、高级 V2  和独立  定价层。
>

应用分配到可用的应用服务计划使用均匀分布在实例之间的最有效的方法。 尽管不保证均匀分布，平台将确保同一应用程序的两个实例将不在同一个应用服务计划实例上托管。

平台不依赖于要在辅助角色分配决定的度量值。 仅当添加或删除从应用服务计划实例时，会重新平衡应用程序。

## <a name="per-app-scaling-using-powershell"></a>使用 PowerShell 的按应用缩放

通过将 ```-PerSiteScaling $true``` 参数传入 ```New-AzAppServicePlan``` cmdlet，创建按应用缩放的计划。

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

通过将 `-PerSiteScaling $true` 参数传入 ```Set-AzAppServicePlan``` cmdlet，使用现有应用服务计划启用按应用缩放。

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

在应用级别，配置应用可以在应用服务计划中使用的实例数。

在以下示例中，无论基础应用服务计划扩展到多少个实例，应用都被限制为两个实例。

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` 不同于 `$newapp.MaxNumberOfWorkers`。 按应用缩放使用 `$newapp.SiteConfig.NumberOfWorkers` 来确定应用的缩放特征。

## <a name="per-app-scaling-using-azure-resource-manager"></a>使用 Azure 资源管理器的按应用缩放

以下 Azure 资源管理器模板创建：

- 扩展到 10 个实例的应用服务计划
- 配置为最多扩展到 5 个实例的应用。

应用服务计划将 **PerSiteScaling** 属性设置为 true `"perSiteScaling": true`。 应用将要使用的**辅助角色数量** 设置为 5 `"properties": { "numberOfWorkers": "5" }`。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>高密度托管的建议配置

按应用缩放是一项功能，在全球 Azure 区域和[应用服务环境](environment/app-service-app-service-environment-intro.md)中均可启用。 但是，建议的策略是使用应用服务环境充分利用其高级的功能和更大的应用服务计划容量。  

按以下步骤为应用配置高密度托管：

1. 将应用服务计划指定为高密度计划并将它扩展到所需的容量。
1. 在应用服务计划中将 `PerSiteScaling` 标志设置为 true。
1. 将 **numberOfWorkers** 属性设置为 **1**，创建新应用并将其分配给该应用服务计划。
   - 使用此配置会产生可能的最高密度。
1. 可按应用独立配置辅助角色数，根据需要授予其他资源。 例如：
   - 使用率高的应用可通过将 **numberOfWorkers** 设置为  **3** 来提高该应用的处理能力。
   - 使用率低的应用可将 **numberOfWorkers** 属性设置为 **1**。

## <a name="next-steps"></a>后续步骤

- [Azure 应用服务计划深入概述](overview-hosting-plans.md)
- [应用服务环境简介](environment/app-service-app-service-environment-intro.md)
