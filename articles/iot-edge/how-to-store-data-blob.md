---
title: 将块 Blob 存储在设备上 - Azure IoT Edge |Microsoft Docs
description: 了解分层和生存时间功能、查看支持的 Blob 存储操作，以及连接到 Blob 存储帐户。
author: kgremban
manager: mchad
ms.author: kgremban
ms.reviewer: kgremban
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 861b5c3ee6d5661339788e7a27ba70557d0ea267
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947033"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>在 IoT Edge 上通过 Azure Blob 存储将数据存储在边缘

IoT Edge 上的 Azure Blob 存储在边缘提供了[块 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 存储解决方案。 IoT Edge 设备上的 blob 存储模块的行为类似于 Azure 块 blob 服务, 不同之处在于块 blob 存储在本地 IoT Edge 设备上。 你可以使用相同的 Azure 存储 SDK 方法或已经习惯的块 blob API 调用来访问 blob。 本文说明与 IoT Edge 容器中的 Azure Blob 存储相关的概念，该容器在 IoT Edge 设备上运行 Blob 服务。

此模块适用于方案:
* 需要将数据存储在本地, 直到其可以处理或传输到云。 此数据可以是视频、图像、财务数据、医院数据或任何其他非结构化数据。
* 当设备位于具有有限连接性的位置时。
* 如果要在本地处理数据以获取对数据的低延迟访问, 以便能够尽快响应突发。
* 当你想要降低带宽成本并避免将 tb 的数据传输到云时。 你可以在本地处理数据, 并仅将已处理的数据发送到云。

观看视频以了解快速简介
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

此模块附带 **deviceToCloudUpload** 和 **deviceAutoDelete** 功能。

**deviceToCloudUpload**是一种可配置的功能。 此函数自动将本地 blob 存储中的数据上载到 Azure, 并且支持间歇性 internet 连接。 该功能允许：

- 启用/禁用 deviceToCloudUpload 功能。
- 选择将数据复制到 Azure 的顺序，例如，NewestFirst 或 OldestFirst。
- 指定要将数据上传到的 Azure 存储帐户。
- 指定要上传到 Azure 的容器。 此模块允许指定源和目标容器名称。
- 选择将内容上传到云存储后立即删除 Blob 的功能
- 执行完整 Blob 上传（使用 `Put Blob` 操作）和块级上传（使用 `Put Block` 和 `Put Block List` 操作）。

如果 Blob 由块构成，则此模块使用块级上传。 下面是一些常见场景：

- 应用程序需要更新以前上传的 Blob 的某些块；此模块只上传更新的块，而不上传整个 Blob。
- 当模块正在上传 Blob 时，Internet 连接断开；连接恢复后，该模块只上传剩余的块，而不上传整个 Blob。

如果在 blob 上传过程中发生意外的进程终止 (如电源故障), 则在模块重新联机后, 将再次上载所有正在等待上载的块。

**deviceAutoDelete**是一种可配置的功能。 此函数在指定的持续时间 (以分钟为单位) 到期时, 自动从本地存储中删除 blob。 该功能允许：

- 启用/禁用 deviceAutoDelete 功能。
- 指定以分钟为单位的时间 (deleteAfterMinutes)，该时间过后会自动删除这些 Blob。
- 选择在 deleteAfterMinutes 值过期的情况下上载 blob 的功能。


## <a name="prerequisites"></a>先决条件

Azure IoT Edge 设备：

- 可以按照适用于 [Linux](quickstart-linux.md) 或 [Windows 设备](quickstart.md)的快速入门中的步骤，将开发计算机或虚拟机用作 IoT Edge 设备。

- 有关支持的操作系统和体系结构的列表, 请参阅[Azure IoT Edge 支持的系统](support.md#operating-systems)。 IoT Edge 模块上的 Azure Blob 存储支持以下体系结构:
    - Windows AMD64
    - Linux AMD64
    - Linux ARM32
    - Linux ARM64 (预览版)

云资源：

Azure 中的标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload 和 deviceAutoDelete 属性

使用模块所需的属性设置**deviceToCloudUploadProperties**和**deviceAutoDeleteProperties**。 可以在部署期间设置所需的属性, 也可以在以后通过编辑模块内的进行更改, 无需重新部署。 我们建议检查“模块孪生”中的 `reported configuration` 和 `configurationValidation`，以确保正确传播值。

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

此设置的名称为 `deviceToCloudUploadProperties`

| 字段 | 可能的值 | 说明 | 环境变量 |
| ----- | ----- | ---- | ---- |
| uploadOn | true、false | 默认情况`false`下, 设置为。 如果要启用此功能, 请将此字段设置为`true`。 | `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst、OldestFirst | 用于选择将数据复制到 Azure 的顺序。 默认情况`OldestFirst`下, 设置为。 顺序由 Blob 的上次修改时间确定 | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`是一个连接字符串, 用于指定要将数据上传到的存储帐户。 指定 `Azure Storage Account Name`、`Azure Storage Account Key` 或 `End point suffix`。 添加用于上传数据的适当 Azure EndpointSuffix，它在全局 Azure、政府 Azure 和 Microsoft Azure Stack 中是不同的。 <br><br> 可在此处选择指定 Azure 存储 SAS 连接字符串。 但必须在此属性过期时进行更新。  | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | 允许你指定要上传到 Azure 的容器名称。 此模块允许指定源和目标容器名称。 如果未指定目标容器名称，系统会自动分配 `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>` 作为容器名称。 可以创建目标容器名称的模板字符串，具体请查看“可能的值”列。 <br>* %h -> IoT 中心名称（3 到 50 个字符）。 <br>* %d -> IoT Edge 设备 ID（1 到 129 个字符）。 <br>* %m -> 模块名称（1 到 64 个字符）。 <br>* %c -> 源容器名称（3 到 63 个字符）。 <br><br>容器名称的最大大小为 63 个字符。尽管系统会自动分配目标容器名称，但如果容器大小超过 63 个字符，系统会将每个部分（IoTHubName、IotEdgeDeviceID、ModuleName、SourceContainerName）修剪为 15 个字符。 | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true、false | 默认情况`false`下, 设置为。 将它设置为 `true` 时，它会在数据上传到云存储以后自动删除数据。 | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

此设置的名称为 `deviceAutoDeleteProperties`

| 字段 | 可能的值 | 说明 | 环境变量 |
| ----- | ----- | ---- | ---- |
| deleteOn | true、false | 默认情况`false`下, 设置为。 如果要启用此功能, 请将此字段设置为`true`。 | `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | 以分钟为单位指定时间。 达到此值时，模块会自动删除本地存储中的 Blob | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true、false | 默认情况下，它设置为 `true`。在 deleteAfterMinutes 分钟过后，它会保留上传到云存储的 Blob。 可以将它设置为 `false`。在 deleteAfterMinutes 分钟过后，它会删除数据。 注意:要使此属性生效，应将 uploadOn 设置为 true| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="using-smb-share-as-your-local-storage"></a>使用 SMB 共享作为本地存储
在 Windows 主机上部署此模块的 Windows 容器时, 可以提供 SMB 共享作为本地存储路径。

请确保 SMB 共享和 IoT 设备位于相互信任的域中。

可以在运行`New-SmbGlobalMapping` Windows 的 IoT 设备上运行 PowerShell 命令以映射到本地的 SMB 共享。

下面是配置步骤:
```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```
例如： <br>
`$creds = Get-Credential` <br>
`New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G: `

此命令将使用凭据向远程 SMB 服务器进行身份验证。 然后, 将远程共享路径映射到 G: 驱动器号 (可以是任何其他可用的驱动器号)。 IoT 设备现在已将数据卷映射到 G: 驱动器上的路径。 

请确保 IoT 设备中的用户可以读取/写入远程 SMB 共享。

对于部署, 的值`<storage mount>`可以是**G:/ContainerData: C:/BlobRoot**。 

## <a name="granting-directory-access-to-container-user-on-linux"></a>授予对 Linux 上的容器用户的目录访问权限
如果在 Linux 容器的 "创建" 选项中使用了 "用于存储的[卷装入](https://docs.docker.com/storage/volumes/)", 则无需执行任何额外的步骤, 但如果使用了[bind 装载](https://docs.docker.com/storage/bind-mounts/), 则需要执行这些步骤才能正确运行该服务。

按照最低权限原则将用户的访问权限限制为用户执行其工作所需的最低权限, 此模块包括用户 (name: absie, ID:11000) 和用户组 (name: absie, ID:11000)。 如果容器以**root**身份启动 (默认用户为**root**用户), 则我们的服务将作为低特权**absie**用户启动。 

此行为会使主机路径的权限配置对服务的访问权限进行正确的绑定, 否则服务将崩溃, 并出现拒绝访问错误。 目录绑定中使用的路径需要容器用户可访问 (示例: absie 11000)。 可以通过在主机上执行以下命令来授予容器用户对目录的访问权限:

```terminal
sudo chown -R 11000:11000 <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

例如：<br>
`sudo chown -R 11000:11000 /srv/containerdata` <br>
`sudo chmod -R 700 /srv/containerdata `


如果需要以非**absie**的用户身份运行服务, 则可以在部署清单中的 "user" 属性下的 createOptions 中指定自定义用户 ID。 在这种情况下, 需要使用默认或根组`0`ID。

```json
“createOptions”: { 
  “User”: “<custom user ID>:0” 
} 
```
现在, 授予容器用户对目录的访问权限
```terminal
sudo chown -R <user ID>:<group ID> <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

## <a name="configure-log-files"></a>配置日志文件

若要了解如何为模块配置日志文件，请参阅这些[生产最佳做法](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)。

## <a name="connect-to-your-blob-storage-module"></a>连接到 blob 存储模块

可以使用为模块配置的帐户名和帐户密钥来访问 IoT Edge 设备上的 blob 存储。

将你的 IoT Edge 设备指定为对其进行的任何存储请求的 blob 终结点。 你可以使用 IoT Edge 设备信息和配置的帐户名[为显式存储终结点创建连接字符串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。

- 对于部署在设备上的模块，如果该设备上运行 IoT Edge 上的 Azure Blob 存储模块，则 Blob 终结点为 `http://<module name>:11002/<account name>`。
- 对于在其他设备上运行的模块或应用程序, 您必须为您的网络选择正确的终结点。 根据您的网络设置, 选择一个终结点格式, 以便来自外部模块或应用程序的数据流量可以访问运行 IoT Edge 模块上的 Azure Blob 存储的设备。 此方案的 blob 终结点是以下其中之一:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob 存储快速入门示例

Azure Blob 存储文档包括多种语言的快速入门示例代码。 可以通过将 Blob 终结点更改为连接到本地 Blob 存储模块来运行这些示例，以测试 IoT Edge 上的 Azure Blob 存储。

以下快速入门示例使用 IoT Edge 也同样支持的语言，因此，你可以将它们作为 IoT Edge 模块与 Blob 存储模块一起部署：

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java-v10.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-v10.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>通过 Azure 存储资源管理器连接到本地存储

可以使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)连接到本地存储帐户。

1. 下载并安装 Azure 存储资源管理器

1. 使用连接字符串连接到 Azure 存储

1. 提供连接字符串：`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. 完成用于连接的步骤。

1. 在本地存储帐户中创建容器

1. 开始将文件作为块 Blob 上传。
   > [!NOTE]
   > 此模块不支持页 Blob。

1. 也可以选择将 Azure 存储帐户连接存储资源管理器。 此配置为你提供本地存储帐户和 Azure 存储帐户的单一视图

## <a name="supported-storage-operations"></a>受支持的存储操作

IoT Edge 上的 Blob 存储模块使用 Azure 存储 Sdk, 并且与适用于块 blob 终结点的2017-04-17 版本的 Azure 存储 API 一致。 

由于并非所有 Azure Blob 存储操作都受 IoT Edge 上的 Azure Blob 存储支持，因此此部分列出了每项操作的状态。

### <a name="account"></a>帐户

受支持：

- 列出容器

不受支持：

- 获取和设置 blob 服务属性
- 预检 blob 请求
- 获取 blob 服务统计信息
- 获取帐户信息

### <a name="containers"></a>容器

受支持：

- 创建和删除容器
- 获取容器属性和元数据
- 列出 blob
- 获取和设置容器 ACL
- 设置容器元数据

不受支持：

- 租用容器

### <a name="blobs"></a>Blob

受支持：

- 放置、获取和删除 blob
- 获取和设置 blob 属性
- 获取和设置 blob 元数据

不受支持：

- 租用 blob
- 获取 blob 快照
- 复制和中止复制 blob
- 撤销删除 blob
- 设置 blob 层

### <a name="block-blobs"></a>块 Blob

受支持：

- 放置块
- 放置和获取块列表

不受支持：

- 放置来自 URL 的块

## <a name="release-notes"></a>发行说明

这是此模块[在 Docker 中心的发行说明](https://hub.docker.com/_/microsoft-azure-blob-storage)

## <a name="feedback"></a>反馈

你的反馈对我们很重要，我们的目标是使此模块及其功能有用且易用。 请分享你的反馈并告诉我们该如何改进。

你可以通过 absiotfeedback@microsoft.com 联系我们

## <a name="next-steps"></a>后续步骤

了解如何[在 IoT Edge 上部署 Azure Blob 存储](how-to-deploy-blob.md)

在[IoT Edge 博客上的 Azure Blob 存储](https://aka.ms/abs-iot-blogpost)中随时了解最新的更新和公告
