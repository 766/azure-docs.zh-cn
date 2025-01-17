---
title: 使用端到端密钥轮替和审核设置 Azure 密钥保管库 | Microsoft Docs
description: 借助本操作指南设置密钥轮换和监视 Key Vault 日志。
services: key-vault
author: barclayn
manager: barbkess
tags: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 20a170963ff4a8ff9cb69d3397e66e12c1047d16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65561188"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>使用密钥轮替和审核设置 Azure Key Vault

## <a name="introduction"></a>简介

有了密钥保管库以后，即可使用它来存储密钥和机密。 应用程序不再需要保存密钥或机密，可以根据需要从保管库请求密钥或机密。 使用 Key Vault 可以更新密钥和机密，而不会影响应用程序，同时可以各种可能的方法管理密钥和机密。

>[!IMPORTANT]
> 本文中的示例仅用于说明目的， 不应在生产环境中使用。 

本文介绍：

- 一个使用 Azure Key Vault 存储机密的示例。 在本文中，存储的机密是应用程序访问的 Azure 存储帐户密钥。 
- 如何实现该存储帐户机密的计划轮换。
- 如何监视 Key Vault 审核日志，并在收到意外的请求时发出警报。

> [!NOTE]
> 本文不详细说明 Key Vault 的初始设置。 有关信息，请参阅[什么是 Azure 密钥保管库？](key-vault-overview.md)。 有关跨平台命令行接口的说明，请参阅[使用 Azure CLI 管理 Key Vault](key-vault-manage-with-cli2.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>设置密钥保管库

要使应用程序能够从密钥保管库检索机密，必须先创建机密并将其上传到保管库。

启动 Azure PowerShell 会话，并使用以下命令登录 Azure 帐户：

```powershell
Connect-AzAccount
```

在弹出的浏览器窗口中，输入 Azure 帐户的用户名和密码。 PowerShell 将获取与此帐户关联的所有订阅。 PowerShell 默认使用第一个订阅。

如果有多个订阅，可能需要指定用来创建密钥保管库的订阅。 输入以下命令查看帐户的订阅：

```powershell
Get-AzSubscription
```

若要指定与要记录的 Key Vault 关联的订阅，请输入：

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

因为本文介绍了如何将存储帐户密钥存储为机密，因此，必须获取该存储帐户密钥。

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

检索用户的机密（在本例中，为存储帐户密钥）后，必须将该密钥转换为安全字符串，并在 Key Vault 中使用该值创建机密。

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

接下来，获取所创建的机密的 URI。 在稍后的步骤中调用 Key Vault 和检索机密时，需要用到此 URI。 运行以下 PowerShell 命令，并记下 ID 值（即机密 URI）：

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>设置应用程序

存储机密后，可以在执行几个附加步骤后，使用代码检索并使用该机密。

首先必须将应用程序注册到 Azure Active Directory。 然后向 Key Vault 告知应用程序信息，使其允许来自应用程序的请求。

> [!NOTE]
> 必须在与密钥保管库相同的 Azure Active Directory 租户上创建应用程序。

1. 打开“Azure Active Directory”。 
2. 选择“应用注册”。  
3. 选择“新建应用程序注册”，以将一个应用程序添加到 Azure Active Directory。 

    ![在 Azure Active Directory 中打开应用程序](./media/keyvault-keyrotation/azure-ad-application.png)

4. 在“创建”下，将应用程序类型保留为“Web 应用/API”，并为应用程序命名。   为应用程序指定“登录 URL”  。 此 URL 可以是任意 URL，适合本演示即可。

    ![创建应用程序注册](./media/keyvault-keyrotation/create-app.png)

5. 将应用程序添加到 Azure Active Directory 后，应用程序页将会打开。 依次选择“设置”、“属性”。   复制“应用程序 ID”值。  后面的步骤需要用到。

接下来，为应用程序生成密钥，使其可与 Azure Active Directory 交互。 若要创建密钥，请在“设置”下选择“密钥”。   记下为 Azure Active Directory 应用程序生成的新密钥。 后面的步骤需要用到。 从此部分导航出来以后，该密钥将不可用。 

![Azure Active Directory 应用密钥](./media/keyvault-keyrotation/create-key.png)

在建立从应用程序到 Key Vault 的任何调用之前，必须让 Key Vault 知道应用程序及其权限。 以下命令使用 Azure Active Directory 应用中的保管库名称和应用程序 ID 为应用程序授予对 Key Vault 的 **Get** 访问权限。

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

现在可以开始生成应用程序调用。 在应用程序中，必须安装所需的 NuGet 包，以便与 Azure Key Vault 和 Azure Active Directory 交互。 从 Visual Studio 包管理器控制台中，输入以下命令。 在编写本文时，Azure Active Directory 包的最新版本为 3.10.305231913，请确认最新版本并视需要进行更新。

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

在应用程序代码中，创建一个类来保存 Azure Active Directory 身份验证的方法。 在本示例中，该类名为“Utils”  。 添加以下 `using` 语句：

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

接下来，添加以下方法，从 Azure Active Directory 检索 JWT 令牌。 为了方便维护，请将硬编码的字符串值移到 Web 或应用程序配置。

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

添加所需的代码，调用密钥保管库并检索机密值。 首先，必须添加以下 `using` 语句：

```csharp
using Microsoft.Azure.KeyVault;
```

添加方法调用，调用密钥保管库并检索机密。 在此方法中，提供在前面步骤中保存的机密 URI。 请注意如何使用前面创建的 **Utils** 类中的 **GetToken** 方法。

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

现在，当运行应用程序时，应该会向 Azure Active Directory 进行身份验证，并从 Azure 密钥保管库中检索机密值。

## <a name="key-rotation-using-azure-automation"></a>使用 Azure 自动化进行密钥轮替

> [!IMPORTANT]
> Azure 自动化 Runbook 仍需使用 `AzureRM` 模块。

现在，对于存储为 Key Vault 机密的值，可以设置轮换策略。 可通过多种方式轮换机密：

- 手动轮换
- 使用 API 调用以编程方式轮换
- 通过 Azure 自动化脚本轮换

本文结合使用 Azure PowerShell 和 Azure 自动化来更改 Azure 存储帐户的访问密钥。 然后使用新密钥更新 Key Vault 机密。

若要允许 Azure 自动化在 Key Vault 中设置机密值，必须获取名为 **AzureRunAsConnection** 的连接的客户端 ID。 此连接是建立 Azure 自动化实例时创建的。 若要查找此 ID，请在 Azure 自动化实例中选择“资产”。  在此处选择“连接”，然后选择“AzureRunAsConnection”服务主体。   记下“ApplicationId”值。 

![Azure 自动化客户端 ID](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

在“资产”中选择“模块”。   选择“库”，然后搜索并导入以下每个模块的更新版本： 

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> 在撰写本文时，只需要针对以下脚本更新上面记下的模块。 如果自动化作业失败，请确认已导入所有必要的模块及其依赖项。

检索 Azure 自动化连接的应用程序 ID 之后，必须让 Key Vault 知道此应用程序有权更新保管库中的机密。 使用以下 PowerShell 命令：

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

接下来，选择 Azure 自动化实例下的“Runbook”，然后选择“添加 Runbook”。   选择“快速创建”。  为 Runbook 命名，然后选择“PowerShell”作为 Runbook 类型。  可以添加说明。 最后，选择“创建”  。

![创建 Runbook](./media/keyvault-keyrotation/Create_Runbook.png)

将以下 PowerShell 脚本粘贴在新 Runbook 的编辑器窗格中：

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

在编辑器窗格中，选择“测试”窗格以测试脚本。  正常运行脚本后，可以选择“发布”，并在 Runbook 配置窗格中应用 Runbook 的计划。 

## <a name="key-vault-auditing-pipeline"></a>密钥保管库审核管道

设置密钥保管库时，可以打开审核功能，收集有关对密钥保管库发出的访问请求的日志。 这些日志存储在指定的 Azure 存储帐户，可以提取、 监视和分析。 以下方案使用 Azure functions、 Azure 逻辑应用和密钥保管库审核日志来创建与 web 应用的应用程序 ID 不匹配的应用从保管库检索机密时发送一封电子邮件的管道。

首先，必须对密钥保管库启用日志记录。 使用以下 PowerShell 命令。 (您所见中的完整详细信息[本文，了解密钥保管库日志记录](key-vault-logging.md)。)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

启用日志记录后，审核日志将开始存储在指定的存储帐户中。 这些日志包含有关访问密钥保管库的方式、时间和用户的事件。

> [!NOTE]
> 在密钥保管库运行后，有 10 分钟时间访问日志记录信息。 它通常是可比的更快。

下一步是[创建 Azure 服务总线队列](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)。 此队列是密钥保管库审核日志的推送位置。 在队列中的审核日志消息时，逻辑应用将选取这些内容，并对它们进行操作。 使用以下步骤创建服务总线实例：

1. 创建服务总线命名空间 （如果已有一个你想要使用，则跳到步骤 2）。
2. 浏览到 Azure 门户中的服务总线实例，并选择你想要在其中创建队列的命名空间。
3. 选择**创建资源** > **企业集成** > **服务总线**，然后输入所需的详细信息。
4. 通过选择命名空间，然后选择查找的服务总线连接信息**连接信息**。 对于下一步部分，将需要此信息。

下一步，[创建一个 Azure 函数](../azure-functions/functions-create-first-azure-function.md)以轮询存储帐户中的密钥保管库日志并选取新的事件。 将按计划触发此函数。

若要创建 Azure function app，选择**创建资源**，在 marketplace 中搜索**Function App**，然后选择**创建**。 在创建过程中，可以使用现有的托管计划，或创建新的计划。 您还可以选择动态托管。 有关 Azure Functions 的宿主选项的详细信息，请参阅[如何缩放 Azure Functions](../azure-functions/functions-scale.md)。

创建 Azure function app 后，请转到它，然后选择**计时器**方案并**C\#** 的语言。 然后选择**创建此函数**。

![Azure Functions“开始”屏幕边栏选项卡](./media/keyvault-keyrotation/Azure_Functions_Start.png)

在“开发”  选项卡上，将 run.csx 代码替换为以下内容：

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> 更改在前面的代码中指向存储帐户写入密钥保管库日志的位置，更早版本，创建的服务总线实例然后在密钥保管库存储日志的特定路径中的变量。

该函数在写入密钥保管库日志的存储帐户中选取最新日志文件、从该文件中获取最新事件，并将这些事件推送到服务总线队列。 

由于单个文件可以有多个事件，应创建该函数也会检查以确定已选取的最后一个事件的时间戳的 sync.txt 文件。 使用此文件可确保多个时间，您都不推送相同的事件。 

Sync.txt 文件包含上次遇到的事件的时间戳。 加载日志，它们必须按它根据其时间戳，以确保它们正确排序。

对于此函数中，我们引用不是在 Azure Functions 中现成可用的几个其他库。 若要包含这些库，需要 Azure Functions 来使用 NuGet 提取它们。 下**代码**框中，选择**查看文件**。

!["查看文件"选项](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

添加名为 project.json 包含以下内容的文件：

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

选择后**保存**，Azure Functions 将下载必需的二进制文件。

切换到“**集成**”选项卡，为计时器参数指定一个要在函数中使用的有意义名称。 在前面的代码，该函数需要调用计时器*myTimer*。 指定[CRON 表达式](../app-service/webjobs-create.md#CreateScheduledCRON)计时器，如下所示： `0 * * * * *`。 此表达式将导致函数一次一分钟运行。

在同一**集成**选项卡上，添加类型的输入**Azure Blob 存储**。 此输入会指向 sync.txt 文件，其中包含在查找函数的最后一个事件的时间戳。 通过使用参数名称，将在函数内访问此输入。 在前面的代码中，Azure Blob 存储输入要求参数名称为*inputBlob*。 选择 sync.txt 文件将位于其中的存储帐户 （它可以是相同或不同的存储帐户）。 在路径字段中，提供了格式的文件的路径`{container-name}/path/to/sync.txt`。

添加类型的输出**Azure Blob 存储**。 此输出将指向输入中定义的 sync.txt 文件。 该函数使用此输出将查找在最后一个事件的时间戳。 在上面的代码中，要求此参数名为 *outputBlob*。

函数现在已准备就绪。 确保切换回“开发”  选项卡并保存代码。 检查输出窗口了解任何编译错误，并根据需要更正它们。 如果代码可以编译，然后该代码应现在是每隔一分钟检查密钥保管库日志并将任何新事件推送到定义的服务总线队列。 每次触发该函数时，都应该看到向日志窗口写入日志记录信息。

### <a name="azure-logic-app"></a>Azure 逻辑应用

接下来，必须创建 Azure 逻辑应用，选取函数推送到服务总线队列、 分析内容，并将发送一封电子邮件，基于匹配的条件的事件。

[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)通过选择**创建资源** > **集成** > **逻辑应用**。

创建逻辑应用后，请转到它并选择**编辑**。 在逻辑应用编辑器中，选择**服务总线队列**并输入你的服务总线凭据以连接到的队列。

![Azure 逻辑应用服务总线](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

选择“添加条件”。  在条件中，切换到高级编辑器并输入下面的代码。 替换*APP_ID*与你的 web 应用的实际的应用程序 ID:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

此表达式实质上是返回**false**如果*appid*传入事件 （这是服务总线消息的正文） 中不是*appid*的应用程序。

现在下, 创建一个操作**如果否，则不执行任何操作**。

![Azure 逻辑应用选择操作](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

对于操作，选择**Office 365-发送电子邮件**。 填写字段，创建当定义的条件返回 **false** 时要发送的电子邮件。 如果你没有 Office 365，查找替代方案，以获得相同的结果。

您现在拥有一个端到端管道，寻找新的密钥保管库审核日志一分钟一次。 它将发现的新日志推送到服务总线队列。 当新消息进入队列中时，将触发逻辑应用。 如果*appid*内事件与调用应用程序的应用 ID 不匹配，它将发送一封电子邮件。
