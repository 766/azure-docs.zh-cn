---
title: Azure SignalR 服务无服务器快速入门 - Python
description: 使用 Azure SignalR 服务和 Azure Functions 创建聊天室的快速入门。
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: antchu
ms.openlocfilehash: fbae9d15ba8572a8ae2c493ec85eb0010c2664eb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886419"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>快速入门：使用 Python 通过 Azure Functions 和 SignalR 服务创建聊天室

使用 Azure SignalR 服务可以轻松地将实时功能添加到应用程序。 Azure Functions 是一个无服务器平台，可让你在不管理任何基础结构的情况下运行代码。 本快速入门介绍了如何使用 SignalR 服务和 Functions 构建无服务器的实时聊天应用程序。

## <a name="prerequisites"></a>先决条件

本快速入门可以在 macOS、Windows 或 Linux 上运行。

确保已安装了代码编辑器，例如 [Visual Studio Code](https://code.visualstudio.com/)。

安装 [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing)（版本 2.7.1505 或更高版本），以便在本地运行 Python Azure Functions 应用。

Azure Functions 需要 [Python 3.6](https://www.python.org/downloads/)。

为了安装扩展，Azure Functions Core Tools 目前需要安装 [.NET Core SDK](https://www.microsoft.com/net/download)。 但是，构建 Python Azure Function 应用不需要了解 .NET。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 <https://portal.azure.com/> 的 Azure 门户。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>配置和运行 Azure 函数应用

1. 在打开 Azure门户的浏览器中，通过在门户顶部的搜索框中搜索先前部署的 SignalR 服务实例的名称，确认该实例已成功创建。 选择该实例以将其打开。

    ![搜索 SignalR 服务实例](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. 选择“密钥”  以查看 SignalR 服务实例的连接字符串。

1. 选择并复制主连接字符串。

    ![创建 SignalR 服务](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. 在代码编辑器中，打开克隆存储库中的 src/chat/python  文件夹。

1. 要本地开发和测试 Python 函数，必须在 Python 3.6 环境中工作。 运行以下命令来创建并激活一个名为 `.venv` 的虚拟环境。

    **Linux 或 macOS：**

    ```bash
    python3.6 -m venv .venv
    source .venv/bin/activate
    ```

    **Windows**：

    ```powershell
    py -3.6 -m venv .venv
    .venv\scripts\activate
    ```

1. 将 *local.settings.sample.json* 重命名为 *local.settings.json*。

1. 在 **local.settings.json** 中，将连接字符串粘贴到 **AzureSignalRConnectionString** 设置的值中。 保存文件。

1. Python 函数将组织到文件夹中。 每个文件夹中有两个文件：*function.json* 定义函数中使用的绑定，\_\_init\_\_.py  是函数的主体。 此函数应用中有两个 HTTP 触发的函数：

    - **negotiate** - 使用 *SignalRConnectionInfo* 输入绑定生成并返回有效的连接信息。
    - **messages** - 在请求正文中接收聊天消息，并使用 *SignalR* 输出绑定将消息广播到所有连接的客户端应用程序。

1. 在终端中，确保你位于 src/chat/python  文件夹中。 使用 Azure Functions Core Tools 安装运行应用所需的扩展。

    ```bash
    func extensions install
    ```

1. 运行函数应用。

    ```bash
    func start
    ```

    ![运行函数应用](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你在 VS Code 中生成并运行了一个实时无服务器应用程序。 接下来，详细了解如何从 VS Code 中部署 Azure Functions。

> [!div class="nextstepaction"]
> [使用 VS Code 部署 Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started)