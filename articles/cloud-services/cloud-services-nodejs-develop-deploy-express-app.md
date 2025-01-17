---
title: 生成 Node.js Express 应用并将其部署到 Azure 云服务
description: 在 Node.js 中生成 Express.js 应用程序并将其部署到 Azure 云服务
services: cloud-services
documentationcenter: nodejs
author: georgewallace
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: gwallace
ms.openlocfilehash: b3d8e364a982f85ad9df6e48dc4d28da1e8efb40
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359066"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>使用 Express 在 Azure 云服务中生成并部署 Node.js Web 应用程序

Node.js 包含核心运行时中最小的一个功能集。
开发人员在开发 Node.js 应用程序时经常会使用第三方模块来提供额外功能。 本教程将使用 [Express](https://github.com/expressjs/express) 模块新建一个应用程序，该模块可提供用于创建 Node.js Web 应用程序的 MVC 框架。

以下是已完成应用程序的屏幕快照：

![显示“Welcome to Express in Azure”的 Web 浏览器](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>创建云服务项目
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

执行下列步骤可新建一个名为“expressapp”的云服务项目：

1. 在“开始”菜单或“开始”屏幕中，搜索 **Windows PowerShell**。 最后，右键单击“Windows PowerShell”并选择“以管理员身份运行”。
   
    ![Azure PowerShell 图标](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. 将目录切换到 c:\\node 目录，然后输入下列命令，新建一个名为 expressapp 的解决方案和名为 WebRole1 的 Web 角色：
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > 默认情况下，**Add-azurenodewebrole** 使用旧版 Node.js。 上述 **Set-azureserviceprojectrole** 语句将指示 Azure 使用 v0.10.21 版本的节点。  请注意，这些参数区分大小写。  通过检查 **WebRole1\package.json** 中的 **engines** 属性可以验证是否已选择正确的 Node.js 版本。
    > 
    > 

## <a name="install-express"></a>安装 Express
1. 通过发出以下命令安装 Express 生成器：
   
        PS C:\node\expressapp> npm install express-generator -g
   
    npm 命令的输出应与以下结果类似。 
   
    ![Windows PowerShell 显示 npm install express 命令的输出。](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. 将目录切换到 **WebRole1** 目录，并使用 express 命令生成一个新的应用程序：
   
        PS C:\node\expressapp\WebRole1> express
   
    系统会提示是否覆盖之前创建的应用程序。 输入 **y** 或 **yes** 以继续操作。 Express 将生成用于生成应用程序的 app.js 文件和一个文件夹结构。
   
    ![express 命令的输出](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. 若要安装在 package.json 文件中定义的其他依赖项，请输入以下命令：
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![npm install 命令的输出](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. 使用以下命令将 bin/www 文件复制到 server.js。 这样，云服务便可以找到此应用程序的入口点。
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   完成此命令后，WebRole1 目录中应会生成一个 **server.js** 文件。
5. 修改 **server.js** 以删除以下行中的“.”字符之一。
   
       var app = require('../app');
   
   完成此项修改后，该行应如下所示。
   
       var app = require('./app');
   
   之所以需要进行此更改，是因为我们已将文件（以前的 bin/www）移到了所需应用文件所在的同一个目录。 完成此更改后，请保存 **server.js** 文件。
6. 使用以下命令以在 Azure 模拟器中运行应用程序：
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![包含 Welcome to Express 的网页。](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>修改视图
现在，修改视图以显示消息“Welcome to Express in Azure”。

1. 请输入以下命令来打开 index.jade 文件：
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![index.jade 文件的内容。](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade 是 Express 应用程序使用的默认视图引擎。 有关 Index.jade 视图引擎的详细信息, 请参阅[http://jade-lang.com][http://jade-lang.com]。
2. 通过追加 **in Azure** 来修改最后一行文本。
   
   ![index.jade 文件，最后一行的内容为：p Welcome to \#{title} in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. 保存文件并退出记事本。
4. 刷新浏览器即可看到所做的更改。
   
   ![浏览器窗口，其中的页面包含 Welcome to Express in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

测试应用程序后，请使用 **Stop-AzureEmulator** cmdlet 停止模拟器。

## <a name="publishing-the-application-to-azure"></a>将应用程序发布到 Azure
在 Azure PowerShell 窗口中，可使用 **Publish-AzureServiceProject** cmdlet 将应用程序部署到云服务

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

在部署操作完成后，浏览器将打开并显示该网页。

![Web 浏览器中显示 Express 页。 URL 指示它现在托管在 Azure 上。](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 [Node.js 开发人员中心](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest)。

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com


