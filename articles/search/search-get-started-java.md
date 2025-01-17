---
title: 快速入门：使用 Java 创建 Azure 搜索索引
description: 介绍如何使用 Java 和 Azure 搜索 REST Api 创建索引、加载数据以及运行查询。
services: search
author: jj09
manager: jlembicz
ms.service: search
ms.topic: conceptual
ms.date: 08/26/2018
ms.author: jjed
ms.custom: seodec2018, seo-java-july2019
ms.openlocfilehash: 7deb9d2cf16aa82de7ce4ea163652c2936819063
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533237"
---
# <a name="quickstart-create-an-azure-search-index-in-java"></a>快速入门：使用 Java 创建 Azure 搜索索引
> [!div class="op_single_selector"]
> * [门户](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

了解如何生成使用 Azure 搜索进行搜索体验的自定义 Java 搜索应用程序。 本教程使用 [Azure 搜索服务 REST API](https://msdn.microsoft.com/library/dn798935.aspx) 构造用于此练习的对象和操作。

若要运行此示例，必须具有 Azure 搜索服务，可在 [Azure 门户](https://portal.azure.com)注册此服务。 有关分步说明，请参阅 [在门户中创建 Azure 搜索服务](search-create-service-portal.md) 。

使用以下软件构建和测试此示例：

* [Eclipse IDE for Java EE Developers](https://www.eclipse.org/downloads/packages/release/photon/r/eclipse-ide-java-ee-developers)。 请务必下载 EE 版本。 一个验证步骤需要的功能只能在此版本中找到。
* [JDK 8u181](https://aka.ms/azure-jdks)
* [Apache Tomcat 8.5.33](https://tomcat.apache.org/download-80.cgi#8.5.33)

## <a name="about-the-data"></a>关于数据
此示例应用程序使用的数据来自 [美国地质调查局 (USGS)](https://geonames.usgs.gov/domestic/download_data.htm)，对罗得岛州进行了筛选以减小数据集大小。 使用此数据生成搜索应用程序，该应用程序返回医院、学校等地标性建筑，以及河流、湖泊和山峰等地质特征。

在此应用程序中, **searchservlet.java**程序使用[索引器](https://msdn.microsoft.com/library/azure/dn798918.aspx)构造, 从 Azure SQL 数据库检索筛选的 USGS 数据集, 生成并加载索引。 程序代码中提供了联机数据源的预定义凭据和连接信息。 在数据访问方面，无需进一步配置。

> [!NOTE]
> 此数据集应用了筛选器，使其保持在免费定价层限定的 10,000 个文档以下。 如果使用标准层，此限制不适用，可以修改此代码以使用更大的数据集。 有关每个定价层容量的详细信息，请参阅 [限制和约束](search-limits-quotas-capacity.md)。
> 
> 

## <a name="about-the-program-files"></a>关于程序文件
以下列表说明了与此示例相关的文件。

* Search.jsp：提供用户界面
* SearchServlet.java：提供方法（类似于 MVC 中的控制器）
* SearchServiceClient.java：处理 HTTP 请求
* SearchServiceHelper.java：一个提供静态方法的帮助器类
* Document.java：提供数据模型
* config.properties：设置搜索服务 URL 和`api-key`
* pom.xml：Maven 依赖项

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>查找服务名称和`api-key` Azure 搜索服务
对 Azure 搜索的所有 REST API 调用都需要提供服务 URL 和`api-key`。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在跳转栏中, 选择 "**搜索服务**" 以列出为订阅预配的所有 Azure 搜索服务。
3. 选择要使用的服务。
4. 在服务仪表板中，可看到基本信息的磁贴，以及用于访问管理密钥的密钥图标。
   
      ![显示如何从服务仪表板访问管理密钥的屏幕截图][3]
5. 复制服务 URL 和管理密钥。 稍后将它们添加到 **config.properties** 文件时会需要它们。

## <a name="download-the-sample-files"></a>下载示例文件
1. 转到 GitHub 上的[搜索 java 索引器演示](https://github.com/Azure-Samples/search-java-indexer-demo)。
2. 选择 "**下载 zip**", 将 .zip 文件保存到磁盘, 然后提取其包含的所有文件。 可考虑将文件解压缩到 Java 工作区中，以便于以后查找项目。
3. 示例文件是只读文件。 右键单击文件夹属性，并清除只读属性。

将对此文件夹中的文件进行所有后续文件修改并运行语句。  

## <a name="import-project"></a>导入项目
1. 在 Eclipse 中, 选择 "**文件** > " "将**常规** > 现有项目**导入** > **到工作区**"。
   
    ![显示如何导入现有项目的屏幕截图][4]
2. 在“选择根目录”中，浏览到包含示例文件的文件夹。 选择包含 .project 文件夹的文件夹。 该项目应在“项目” 列表中显示为所选项目。
   
    ![显示 "导入项目" 窗口中的 "项目" 列表的屏幕截图][12]
3. 选择“完成”。
4. 使用 **项目资源管理器** 查看和编辑文件。 如果尚未打开, 请选择 "**窗口** > " "**显示视图** > **项目资源管理器**" 或使用快捷方式打开它。

## <a name="configure-the-service-url-and-api-key"></a>配置服务 URL 和`api-key`
1. 在 "**项目资源管理器**" 中, 双击 " **config. 属性**" 以编辑包含服务器名称`api-key`和的配置设置。
2. 请参阅本文前面部分中的步骤, 其中你找到了服务 URL `api-key` , 在[Azure 门户](https://portal.azure.com)中, 若要获取你现在将输入到**config. 属性**中的值。
3. 在 "**配置**" 中, 将 "API 密钥" 替换`api-key`为服务的。 接下来，使用服务名称（URL https://servicename.search.windows.net) 的第一部分）替换同一文件中的“service name”。
   
    ![显示如何替换 API 密钥的屏幕截图][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>配置项目，构建运行时环境
1. 在 Eclipse 的“项目资源管理器”中，右击该项目，并单击“属性” > “项目方面”。
2. 选择“动态 Web 模块”、“Java”和“JavaScript”。
   
    ![显示如何为项目选择项目方面的屏幕截图][6]
3. 选择“应用”。
4. 选择“窗口” > “首选项” > “服务器” > “运行时环境” > “添加..”。
5. 展开 Apache 并选择以前安装的 Apache Tomcat 服务器的版本。 在我们的系统上安装的是版本 8。
   
    ![显示在运行时环境窗口中可选择你的 Apache Tomcat 版本的屏幕截图][7]
6. 在下一页上，指定 Tomcat 安装目录。 在 Windows 计算机上，这很可能是 C:\Program Files\Apache Software Foundation\Tomcat 版本。
7. 选择“完成”。
8. 选择“窗口” > “首选项” > “Java” > “已安装 JRE” > “添加”。
9. 在“添加 JRE”中，选择“标准 VM”。
10. 选择“**下一步**”。
11. 在 "JRE" 中, 选择 "**目录**"。
12. 导航到“程序文件” > “Java”，并选择以前安装的 JDK。 请务必将 JDK 选为 JRE。
13. 在 "已安装的 Jre" 中, 选择**JDK**。 设置应类似于以下屏幕截图。
    
    ![显示如何选择 JDK 作为已安装 JRE 的屏幕截图][9]
14. 根据需要可以选择“窗口” > “Web 浏览器” > “Internet Explorer”，在外部浏览器窗口中打开应用程序。 使用外部浏览器可以获得更好的 Web 应用程序体验。
    
    ![显示如何将 Internet Explorer 选为外部浏览窗口的屏幕截图][8]

现在已完成配置任务。 接下来，生成并运行项目。

## <a name="build-the-project"></a>生成项目
1. 在 "项目资源管理器" 中, 右键单击项目名称, 然后选择 "**作为** > **Maven 生成**运行" 以配置项目。
   
    ![显示如何在 "项目资源管理器" 窗口中选择 Maven 生成的屏幕截图][10]
2. 在 "编辑配置" 的 "目标" 中, 输入 "全新安装", 然后选择 "**运行**"。

状态消息将输出到控制台窗口。 应看到“BUILD SUCCESS”，该项指示项目生成无错误。

## <a name="run-the-app"></a>运行应用程序
在此最后一个步骤中，会在本地服务器运行时环境中运行应用程序。

如果尚未在 Eclipse 中指定服务器运行时环境，则需要先执行该操作。

1. 在项目资源管理器中，展开“WebContent”。
2. 右键单击“Search.jsp” > “运行方式” > “在服务器上运行”。 选择 "Apache Tomcat 服务器", 然后选择 "**运行**"。

> [!TIP]
> 如果已使用非默认工作区来存储项目，则需要修改“运行配置”使其指向项目位置，以避免服务器启动错误。 在“项目资源管理器”中，右键单击“Search.jsp” > “运行方式” > “运行配置”。 选择 Apache Tomcat 服务器。 选择 "**参数**"。 选择 "**工作区**" 或 "**文件系统**" 来设置包含项目的文件夹。
> 
> 

运行应用程序时，应看到浏览器窗口，其中提供用于输入搜索词的搜索框。

请等待大约一分钟, 然后再选择 "**搜索**", 为创建和加载索引指定服务时间。 如果收到 HTTP 404 错误，只需再等待长点时间，并重试。

## <a name="search-on-usgs-data"></a>搜索 USGS 数据
USGS 数据集包含罗得岛州的相关记录。 如果在空搜索框中选择 "**搜索**", 将获得最大的50项, 这是默认值。

输入搜索词，为搜索引擎提供可搜索的内容。 尝试输入区域名称。 “Roger Williams”是罗得岛的第一任州长。 许多公园、建筑和学校都以他的名字命名。

![显示如何搜索 USGS 数据的屏幕截图][11]

还可以尝试以下任一词语：

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>后续步骤
这是基于 Java 和 USGS 数据集的第一个 Azure 搜索教程。 日后，本教程会进一步扩展，演示自定义解决方案中可能要使用的其他搜索功能。

如果已了解 Azure 搜索的一些背景知识，可以使用此示例作为进一步试验的起点，也许扩充[搜索页](search-pagination-page-layout.md)或实现[分面导航](search-faceted-navigation.md)。 还可以通过添加计数以及批处理文档，改进搜索结果页，以便用户可以翻阅结果。

不熟悉 Azure 搜索？ 建议尝试其他教程，深入了解可以创建哪些内容。 请访问 [文档页](https://azure.microsoft.com/documentation/services/search/) 查找更多资源。 

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
