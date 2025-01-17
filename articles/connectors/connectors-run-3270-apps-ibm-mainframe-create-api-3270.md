---
title: 连接至 Azure-Azure 逻辑应用与 IBM 大型机上 3270 应用程序
description: 集成并使用 Azure 逻辑应用和 IBM 3270 连接器自动执行使用 Azure 3270 屏幕驱动应用程序
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: 7388dc0c61dad9c31da0c178febcee4c8481bc50
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60538795"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>使用 Azure 逻辑应用和 IBM 3270 连接器与 Azure 集成 IBM 大型机上的 3270 屏幕驱动应用

> [!NOTE]
> 此连接器处于[*公共预览版*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

使用 Azure 逻辑应用和 IBM 3270 连接器，可以访问并运行你通常为驱动器 3270 仿真程序屏幕之间导航的 IBM 大型机应用程序。 这样一来，您可以将集成 IBM 大型机应用程序与 Azure、 Microsoft、 其他应用程序、 服务和系统通过与 Azure 逻辑应用创建自动化工作流。 连接器使用 TN3270 协议与 IBM 大型机进行通信，并在 Azure 政府和 Azure 中国的 21Vianet 除外的所有 Azure 逻辑应用区域均可用。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

本文介绍了使用 3270 连接器的这些方面： 

* 为何使用 Azure 逻辑应用和连接器如何运行 3270 屏幕驱动的应用程序中的 IBM 3270 连接器

* 先决条件和安装程序使用 3270 连接器

* 3270 连接器操作添加到逻辑应用的步骤

## <a name="why-use-this-connector"></a>为什么要使用此连接器？

若要访问 IBM 大型机上的应用程序，通常使用 3270 终端模拟器，通常称为"绿色屏幕"。 此方法是经过时间考验的方式，但具有限制。 尽管 Host Integration Server (HIS) 可帮助您直接使用这些应用程序，有时，将分离的屏幕和业务逻辑可能不可能。 或者，也许您不再拥有主机应用程序工作原理的信息。

若要扩展这些方案，Azure 逻辑应用中的 IBM 3270 连接器适用于 3270 设计工具，它使用到记录，或"捕获"，用于特定任务的主机屏幕，定义通过大型机应用程序，该任务的导航流以及定义使用输入和输出参数，该任务的方法。 设计工具将该信息转换为 3270 连接器时调用一个操作从逻辑应用表示该任务使用的元数据。

从设计工具生成的元数据文件后，您将该文件添加到 Azure 中的集成帐户。 这样一来，当添加 3270 连接器操作时，逻辑应用可以访问应用程序的元数据。 连接器读取元数据文件在集成帐户中，处理通过 3270 屏幕中，导航并动态显示 3270 连接器操作的参数。 然后，可以提供给主机应用程序，数据和连接器将结果返回到逻辑应用。 这样一来，你可与 Azure、 Microsoft 和其他应用程序、 服务和 Azure 逻辑应用支持的系统集成传统应用程序。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 建议：[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  为创建和运行逻辑应用的位置，可以选择此环境。 ISE 提供从逻辑应用到 Azure 虚拟网络中保护的资源的访问。

* 逻辑应用要用于自动执行和运行 3270 屏幕驱动应用

  IBM 3270 连接器没有触发器，因此使用另一个触发器启动逻辑应用，如**重复周期**触发器。 然后，你可以添加 3270 连接器操作。 若要开始，[创建空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 
  如果你使用 ISE，选择该 ISE 作为逻辑应用的位置。

* [下载并安装 3270 设计工具](https://aka.ms/3270-design-tool-download)。
唯一的先决条件是[Microsoft.NET Framework 4.6.1](https://aka.ms/net-framework-download)。

  此记录的添加和 3270 连接器操作以运行应用程序中的屏幕、 导航路径、 方法和参数的任务的工具可帮助。 该工具生成一个主机集成设计器 XML (HIDX) 文件，提供要用于驱动大型机应用程序使用的连接器的必需元数据。
  
  后下载和安装此工具，请按照以下步骤连接到你的主机：

  1. 打开 3270 设计工具。 从**会话**菜单中，选择**主机会话**。
  
  1. 提供 TN3270 主机服务器信息。

* [集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，这是以地图形式存储 HIDX 文件以便在逻辑应用可以访问该文件中的元数据和方法定义的位置。 

  请确保集成帐户链接到逻辑应用使用。 此外，如果你使用 ISE，请确保集成帐户的位置是逻辑应用将使用相同 ISE。

* 对 TN3270 服务器承载您的大型机应用程序的访问

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>创建元数据概述

在 3270 屏幕驱动应用中，屏幕和数据字段是唯一的对您的方案，因此 3270 连接器需要有关您的应用程序，您可以根据元数据提供此信息。 此元数据描述信息，可帮助确定和识别屏幕在逻辑应用，介绍如何在屏幕之间导航，输入数据的位置和预期结果的位置。 若要指定和生成此元数据，使用 3270 设计工具，它将指导你完成这些特定*模式*，或更高版本中更多详细信息所述的阶段：

* **捕获**:在此模式下，你记录完成特定任务与大型机应用，例如，所需的屏幕获取银行余额。

* **导航**:在此模式下，指定的计划或如何通过为特定任务的大型机应用程序的屏幕导航路径。

* **方法**:在此模式下，将方法定义，例如， `GetBalance`，用于描述屏幕导航路径。 你还可以选择每个屏幕上，成为方法的输入和输出参数的字段。

### <a name="unsupported-elements"></a>不支持的元素

设计工具不支持这些元素：

* 部分 IBM 基本映射支持 (BMS) 映射：如果导入 BMS 映射，设计工具将忽略部分屏幕定义。
* In/Out 参数：不能定义 In/Out 参数。
* 菜单处理：预览期间不支持
* 数组处理：预览期间不支持

<a name="capture-screens"></a>

## <a name="capture-screens"></a>捕获屏幕

在此模式下，您将标记每个唯一标识该屏幕的 3270 屏幕上的项。 例如，你可能指定文本的一行或一组更复杂的条件，例如特定的文本和非空字段。 可以通过实时连接到主机服务器中，记录这些屏幕，也可以从 IBM 基本映射支持 (BMS) 映射导入此信息。 实时连接使用 TN3270 仿真程序用于连接到主机。 每个连接器的操作必须映射到的单个任务，与连接到你的会话开始和结束的与你的会话断开连接。

1. 如果尚未安装，请打开 3270 设计工具。 在工具栏中，选择**捕获**，以便输入捕获模式。

1. 若要开始录制，请按 F5 键，或从**录制**菜单中，选择**开始录制**。 

1. 从**会话**菜单中，选择**Connect**。

1. 在中**捕获**窗格中，从应用中，单步执行你在记录的特定任务的应用程序中的第一个屏幕。

1. 在完成任务后，注销您的应用程序通常一样。

1. 从**会话**菜单中，选择**断开连接**。

1. 若要停止录制，请按 Shift + F5 键，或从**录制**菜单中，选择**停止录制**。

   捕获的任务的屏幕后，该设计器工具会显示表示这些屏幕的缩略图。 有关这些缩略图的一些注意事项：

   * 包含使用捕获屏幕，具有一个名为"空"的屏幕。

     当您首次连接到[CICS](https://www.ibm.com/it-infrastructure/z/cics)，必须将"清除"密钥发送之前可以输入你想要运行的事务的名称。 你将"清除"密钥发送的屏幕上没有任何*识别属性*，如屏幕标题，可以使用屏幕识别编辑器添加。 若要表示此屏幕，缩略图包括名为"空"的屏幕。 您可以更高版本使用此屏幕用于表示屏幕你在其中输入事务名称。

   * 默认情况下，捕获的屏幕的名称在屏幕上使用的第一个单词。 如果该名称已存在，设计工具将追加一个下划线和数字，例如，"WBGB"和"WBGB_1"的名称。

1. 若要捕获的屏幕允许更有意义的名称，请执行以下步骤：

   1. 在中**主机屏幕**窗格中，选择你想要重命名的屏幕。

   1. 在同一窗格中，在同一窗格中，底部附近查找**屏幕名称**属性。

   1. 将当前屏幕名称更改为更具描述性的名称。

1. 现在，指定用于标识每个屏幕的字段。

   与 3270 数据流，屏幕不具有默认的标识符，因此您需要选择每个屏幕上的唯一文本。 对于复杂的方案，可以指定多个条件，例如，唯一的文本并具有特定条件的字段。

选择识别字段后，将移动到下一步的模式。

### <a name="conditions-for-identifying-repeated-screens"></a>用于标识重复的屏幕的条件

有关导航和区分屏幕之间的连接器，通常可用作标识符中捕获屏幕的屏幕上找到唯一的文本。 对于重复的屏幕，您可能需要更多的标识方法。 例如，假设您有两个屏幕看起来相同，除了一个屏幕返回有效的值，而另一个屏幕将返回一条错误消息。

在设计工具中，可以添加*识别属性*，例如，"获取帐户余额"，例如通过使用屏幕识别编辑器的屏幕标题。 如果具有分叉的路径，并且这两个分支返回同一个屏幕，但使用不同的结果，您需要其他识别的属性。 在运行时，连接器将使用这些属性来确定当前分支和分支。 下面是可以使用的条件：

* 特定值：此值与指定的字符串中指定的位置。
* 不是特定的值：此值不匹配的指定的字符串中指定的位置。
* 为空：此字段为空。
* 不为空：此字段不为空。

若要了解详细信息，请参阅[的示例导航计划](#example-plan)本主题中更高版本。

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>定义导航计划

在此模式下，您定义的流或特定任务的大型机应用程序的屏幕中导航的步骤。 例如，有时，你可能具有您的应用程序可能需要在其中一个路径生成正确的结果，而另一条路径生成了错误的多个路径。 对于每个屏幕上，指定将移至下一个屏幕，如有必要击键`CICSPROD <enter>`。

> [!TIP]
> 如果要自动执行许多任务，请使用相同的连接和断开连接的屏幕，设计工具提供了特殊的连接和断开连接计划类型。 在定义这些计划时，可以将它们添加到导航计划的开始和结束。

### <a name="guidelines-for-plan-definitions"></a>计划定义的准则

* 包括所有屏幕，开始使用连接和断开连接结束。

* 可以创建独立计划，也可以使用的连接和断开连接的计划，可让你重复使用一系列屏幕普遍适用于所有事务。

  * 连接计划中的最后一个屏幕导航计划中必须作为第一个屏幕在同一个屏幕。

  * 在断开连接计划中的第一个屏幕导航计划中必须作为最后一个屏幕的同一个屏幕。

* 捕获的屏幕可能包含许多重复的屏幕，因此选择，在计划中使用任何重复的屏幕的一个实例。 下面是一些示例重复屏幕：

  * 在屏幕中，例如，符号**MSG 10**屏幕
  * CICS 欢迎屏幕
  * "清除"或**空**屏幕

<a name="create-plans"></a>

### <a name="create-plans"></a>创建计划

1. 3270 设计工具的工具栏上，选择**导航**，以便输入导航模式。

1. 要启动你的计划，请在**导航**窗格中，选择**新计划**。

1. 下**选择新计划名称**，输入你的计划的名称。 从**类型**列表中，选择计划类型：

   | 计划类型 | 描述 |
   |-----------|-------------|
   | **过程** | 独立安装还是计划相结合 |
   | **“连接”** | 有关连接计划 |
   | **断开连接** | 为断开连接计划 |
   |||

1. 从**主机屏幕**窗格中，拖动到导航计划捕获缩略图中呈现**导航**窗格。

   若要表示您在其中输入事务名称的空白屏幕，请使用"空"屏幕。

1. 排列中描述的任务，你定义的顺序的屏幕。

1. 若要定义的数据流路径之间的屏幕，其中包括分叉和联接，在设计工具的工具栏上，选择**流**。

1. 在流中选择第一个屏幕。 拖放在流中下一个屏幕上绘制连接。

1. 对于每个屏幕上，提供的值**辅助设备密钥**属性 （注意标识符） 和有关**固定文本**属性，用于将流量移到下一个屏幕。

   您可能只是辅助密钥，或同时具有辅助密钥和固定的文本。

完成导航计划后，你可以[中的下一步模式定义方法](#define-method)。

<a name="example-plan"></a>

### <a name="example"></a>示例

在此示例中，假设您要运行 CICS 事务名为"WBGB"具有以下步骤： 

* 在第一个屏幕上，输入一个名称和帐户。
* 在第二个屏幕上，你可以获取帐户余额。
* 你退出"空"屏幕。
* 您从注销 CICS"MSG-10"屏幕上。

此外假设，重复这些步骤，但你输入不正确的数据，以便可以捕获将会显示错误屏幕。 下面是在捕获的屏幕：

* MSG-10
* CICS 欢迎
* 空
* WBGB_1 （输入）
* WBGB_2 （错误）
* Empty_1
* MSG-10_1

尽管许多屏幕此处获得唯一的名称，一些屏幕位于同一屏幕上，例如，"MSG-10"和"空"。 对于重复屏幕中，使用只有一个实例为该屏幕在计划中。 下面是显示独立计划、 Connect 计划、 断开连接计划和组合的计划可能的显示方式的示例：

* 独立计划

  ![独立导航计划](./media/connectors-create-api-3270/standalone-plan.png)

* 连接计划

  ![连接计划](./media/connectors-create-api-3270/connect-plan.png)

* 断开连接计划

  ![断开连接计划](./media/connectors-create-api-3270/disconnect-plan.png)

* 组合的计划

  ![组合的计划](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>示例：标识重复的屏幕

为了导航和区分屏幕连接器，通常可以用作标识符跨捕获屏幕的屏幕上找到唯一的文本。 对于重复的屏幕，您可能需要更多的标识方法。 示例计划具有一个分叉哪里可以获得如下所示的屏幕。 一个屏幕返回帐户余额，而另一个屏幕将返回一条错误消息。

设计工具，可添加识别的属性，例如，屏幕标题命名为"获取帐户余额"，通过使用屏幕识别编辑器。 在使用类似的屏幕的情况下，您需要其他属性。 在运行时，连接器将使用这些属性用于确定的分支和分支。

* 返回有效的输入，这是与帐户余额屏幕，在分支中可以添加具有"不为空的"条件的字段。

* 在返回的错误消息的分支，可以添加具有一个"空"条件的字段。

<a name="define-method"></a>

## <a name="define-methods"></a>定义方法

在此模式下，定义与导航计划相关联的方法。 对于每个方法参数，指定的数据类型，如字符串、 整数、 日期或时间，依次类推。 完成后，可以测试你实时主机上的方法，并确认该方法按预期方式正常工作。 生成主机集成设计器 XML (HIDX) 文件，现在有要用于创建和运行 IBM 3270 连接器的操作的方法定义的元数据文件。

1. 3270 设计工具的工具栏上，选择**方法**以便进入方法模式。 

1. 在中**导航**窗格中，选择具有所需的输入的字段的屏幕。

1. 若要添加你的方法的第一个输入的参数，请按照下列步骤：

   1. 在中**捕获**窗格中的，在 3270 仿真程序屏幕上，选择整个字段，而不仅仅是文本字段中，在你希望作为第一个输入。

      > [!TIP]
      > 若要显示所有字段，并确保在选择完成字段时，**视图**菜单中，选择**的所有字段**。

   1. 在设计工具的工具栏上选择**输入字段**。 

   若要添加更多的输入的参数，请为每个参数重复前面的步骤。

1. 若要添加你的方法的第一个输出参数，请按照下列步骤：

   1. 在中**捕获**窗格中的，在 3270 仿真程序屏幕上，选择整个字段，而不仅仅是文本字段中，在你希望作为第一个输出。

      > [!TIP]
      > 若要显示所有字段，并确保在选择完成字段时，**视图**菜单中，选择**的所有字段**。

   1. 在设计工具的工具栏上选择**输出字段**。

   若要添加更多输出参数，请为每个参数重复前面的步骤。

1. 添加方法的所有参数后，定义这些属性的每个参数：

   | 属性名称 | 可能的值 | 
   |---------------|-----------------|
   | 数据类型  | 字节，日期时间、 Decimal、 Int、 Long、 简单地说，字符串 |
   | **字段填充方法** | 参数支持以下填充类型，用空格填充，如有必要： <p><p>- **类型**:按顺序的字段中输入字符。 <p>- **填充**:替换字符，用空格根据需要填充字段的内容。 <p>- **EraseEofType**:清除此字段，然后在字段按顺序输入字符。 |
   | **格式字符串** | 某些参数数据类型使用一个格式字符串，指示如何从屏幕的文本转换的.NET 数据类型的 3270 连接器： <p><p>- **DateTime**:日期时间格式字符串之后[.NET 自定义日期和时间格式字符串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 例如，日期`06/30/2019`使用的格式字符串`MM/dd/yyyy`。 <p>- **十进制**:十进制格式字符串使用[COBOL 图片子句](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html)。 例如，数字`100.35`使用的格式字符串`999V99`。 |
   |||

## <a name="save-and-view-metadata"></a>保存并查看元数据

定义你的方法之后但在测试方法之前，将保存到目前为止到 RAP (.rap) 文件定义的所有信息。
你可以随时在任何模式期间保存对此 RAP 文件。 设计工具还包括一个示例 RAP 文件，您可以打开并查看通过浏览到此位置的设计工具的安装文件夹并打开"WoodgroveBank.rap"文件：

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

但是，如果您尝试保存更改到示例 RAP 文件或从示例 RAP 文件生成 HIDX 文件，而该文件将保留在设计工具的安装文件夹中，您可能会收到"拒绝访问"错误。 默认情况下，设计工具安装在 Program Files 文件夹中，而无需提升的权限。 如果遇到错误，请尝试以下解决方法之一：

* 将示例文件复制到另一个位置。
* 以管理员身份运行的设计工具。
* 使自己的 SDK 文件夹的所有者。

## <a name="test-your-method"></a>测试方法

1. 若要针对实时主机时仍处于方法模式中运行你的方法按 F5 键，或从设计工具的工具栏中，选择**运行**。

   > [!TIP]
   > 可以在任何时候更改模式。 上**文件**菜单中，选择**模式**，然后选择所需的模式。

1. 输入参数的值，然后选择**确定**。

1. 若要继续到下一个屏幕中，选择**下一步**。

1. 完成后，选择**完成**，它显示了你的输出参数值。

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>生成并上传 HIDX 文件

在准备就绪时，生成 HIDX 文件，因此可以上传到集成帐户。 3270 设计工具在您保存 RAP 文件的新子文件夹中创建 HIDX 文件。

1. 3270 设计工具的工具栏上，选择**生成代码**。

1. 转到包含你 RAP 的文件，该文件夹并打开该工具生成 HIDX 文件后创建的子文件夹。 确认该工具创建 HIDX 文件。

1. 登录到[Azure 门户](https://portal.azure.com)，并查找集成帐户。

1. 将 HIDX 文件作为映射添加到集成帐户通过[遵循这些类似的步骤以添加映射](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)，但当你选择地图类型，选择**HIDX**。

本主题后面时第一次，将 IBM 3270 操作添加到逻辑应用则会提示你通过提供连接信息，如在集成帐户和主机服务器的名称创建逻辑应用和主机服务器之间的连接. 创建连接后，可以选择你以前添加的 HIDX 文件、 要运行的方法和要使用的参数。

完成所有这些步骤后，可以使用用于连接到在 IBM 大型机，驱动器屏幕为你的应用在逻辑应用中创建的操作、 输入数据、 返回的结果，依次类推。 你可以继续将其他操作添加到逻辑应用与其他应用程序、 服务和系统集成。

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>运行 IBM 3270 操作

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 在你想要添加操作的最后一步，选择**新步骤**，然后选择**添加操作**。 

1. 在搜索框中，选择**企业**。 在搜索框中，输入作为筛选器"3270"。 在操作列表中选择此操作：**TN3270 连接上运行的大型机程序**

   ![选择 3270 操作](./media/connectors-create-api-3270/select-3270-action.png)

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 
   选择出现的加号 ( **+** )，然后选择“添加操作”。 

1. 如果尚不存在任何连接，提供所需的信息为你的连接，并选择**创建**。

   | 属性 | 需要 | Value | 描述 |
   |----------|----------|-------|-------------|
   | **连接名称** | 是 | <connection-name  > | 连接名称 |
   | **集成帐户 ID** | 是 | <integration-account-name>  | 集成帐户的名称 |
   | **集成帐户 SAS URL** | 是 | <*integration-account-SAS-URL*> | 集成帐户的共享访问签名 (SAS) URL，则可以从 Azure 门户中的集成帐户的设置生成。 <p>1.在你的集成帐户菜单中下,**设置**，选择**回调 URL**。 <br>2.在右侧窗格中，复制**生成回调 URL**值。 |
   | **服务器** | 是 | <*TN3270-server-name*> | TN3270 服务的服务器名称 |
   | 端口  | 否 | <*TN3270-server-port*> | TN3270 服务器使用的端口。 如果为空，则连接器将使用`23`作为默认值。 |
   | **设备类型** | 否 | <*IBM-terminal-model*> | 模型名称或编号，以便在 IBM 终端模拟。 如果为空，则连接器将使用默认值。 |
   | **代码页** | 否 | <*code-page-number*> | 主机代码页编号。 如果为空，则连接器将使用`37`作为默认值。 |
   | **逻辑单元名称** | 否 | <*logical-unit-name*> | 要向宿主请求的特定的逻辑单元名称 |
   | **启用 SSL?** | 否 | 打开或关闭 | 启用或禁用 SSL 加密。 |
   | **验证主机 ssl 证书？** | 否 | 打开或关闭 | 打开或关闭对服务器的证书验证。 |
   ||||

   例如：

   ![连接属性](./media/connectors-create-api-3270/connection-properties.png)

1. 提供的操作所需的信息：

   | 属性 | 需要 | Value | 描述 |
   |----------|----------|-------|-------------|
   | **Hidx 名称** | 是 | <*HIDX-file-name*> | 选择你想要使用的 3270 HIDX 文件。 |
   | **方法名称** | 是 | <*method-name*> | 选择你想要使用 HIDX 文件中的方法。 选择一种方法之后,**添加新参数**列表会显示您可以选择要用于该方法的参数。 |
   ||||

   例如：

   **选择 HIDX 文件**

   ![选择 HIDX 文件](./media/connectors-create-api-3270/select-hidx-file.png)

   **选择的方法**

   ![选择方法](./media/connectors-create-api-3270/select-method.png)

   **选择参数**

   ![选择参数](./media/connectors-create-api-3270/add-parameters.png)

1. 完成后，保存并运行逻辑应用。

   你的逻辑应用完成后运行，显示该运行中的步骤。 
   成功的步骤显示复选标记，而不成功的步骤显示的字母"X"。

1. 若要检查的输入和输出的每个步骤，请展开该步骤。

1. 若要查看输出，请选择**查看原始输出**。

## <a name="connector-reference"></a>连接器参考

有关触发器、 操作和限制的技术详细信息，其中描述了连接器的 OpenAPI (以前称为 Swagger) 说明，请查看[连接器的参考页](/connectors/si3270/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
