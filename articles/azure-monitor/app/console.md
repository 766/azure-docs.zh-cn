---
title: 适用于控制台应用程序的 Azure Application Insights | Microsoft Docs
description: 监视 Web 应用程序的可用性、性能和使用情况。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/30/2019
ms.reviewer: lmolkova
ms.author: mbullwin
ms.openlocfilehash: 0c2a28462633d47ad1d3f247793e3fcf6f4d40c0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795445"
---
# <a name="application-insights-for-net-console-applications"></a>适用于 .NET 控制台应用程序的 Application Insights
使用 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 可以监视 Web 应用程序的可用性、性能和使用情况。

需要 [Microsoft Azure](https://azure.com) 订阅。 使用 Microsoft 帐户登录，该帐户可能适用于 Windows、XBox Live 或其他 Microsoft 云服务。 团队可能拥有 Azure 组织订阅：要求所有者使用 Microsoft 帐户你将加入其中。

## <a name="getting-started"></a>入门

* 在 [Azure 门户](https://portal.azure.com)中，[创建 Application Insights 资源](../../azure-monitor/app/create-new-resource.md)。 对于应用程序类型，选择“常规”。 
* 获取检测密钥的副本。 在创建的新资源的“概要”下拉列表中找到该密钥。  
* 安装最新的 [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) 包。
* 在跟踪任何遥测之前，请先在代码中设置检测密钥（或设置 APPINSIGHTS_INSTRUMENTATIONKEY 环境变量）。 设置后，应能手动跟踪遥测并在 Azure 门户中查看

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

* 安装最新版本的 [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) 包 - 它可自动跟踪 HTTP、SQL 或一些其他外部依赖项调用。

可通过代码或 `ApplicationInsights.config` 文件初始化和配置 Application Insights。 请确保尽早进行初始化。 

> [!NOTE]
> 提到了 **ApplicationInsights.config** 的说明仅适用于以 .NET Framework 为目标的应用，不适用于 .NET Core 应用程序。

### <a name="using-config-file"></a>使用配置文件
默认情况下，创建 `TelemetryConfiguration` 时，Application Insights SDK 在工作目录中查找 `ApplicationInsights.config` 文件

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

还可以指定该配置文件的路径。

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

有关详细信息，请参阅[配置文件参考](configuration-with-applicationinsights-config.md)。

通过安装最新版本的 [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) 包，可以获取配置文件的完整示例。 此处是依赖项集合的“最小”配置，等效于代码示例  。

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>通过代码配置遥测集合
> [!NOTE]
> 在.NET Core 上不支持读取配置文件。 您可以考虑使用[适用于 ASP.NET Core 的 Application Insights SDK](../../azure-monitor/app/asp-net-core.md)

* 在应用程序启动期间创建并配置 `DependencyTrackingTelemetryModule` 实例 - 该实例必须是单一实例，并在应用程序生存期中保留。

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* 添加常用遥测初始值设定项

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

如果使用纯创建配置`TelemetryConfiguration()`构造函数中，您需要另外启用相关支持。 **不需要**如果从文件读取配置，使用`TelemetryConfiguration.CreateDefault()`或`TelemetryConfiguration.Active`。

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* 您可能还想要安装和初始化性能计数器收集器模块，如所述[此处](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)


#### <a name="full-example"></a>完整示例

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient();
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>后续步骤
* [监视依赖项](../../azure-monitor/app/asp-net-dependencies.md)，查看 REST、SQL 或其他外部资源是否会降低性能。
* [使用 API](../../azure-monitor/app/api-custom-events-metrics.md)，发送自己的事件和指标以获取应用的性能和使用情况的更详细视图。
