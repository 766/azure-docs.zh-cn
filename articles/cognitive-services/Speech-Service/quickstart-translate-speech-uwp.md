---
title: 快速入门：翻译语音，C# (UWP) - 语音服务
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将创建一个简单的通用 Windows 平台 (UWP) 应用程序来捕获用户语音，将其翻译为另一种语言，并将文本输出到命令行。 本指南适用于 Windows 用户。
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 07/23/2019
ms.author: erhopf
ms.topic: quickstart
ms.openlocfilehash: 813edbea0548a5cac9532750a450de08bd238028
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640032"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>快速入门：使用适用于 C# 的语音 SDK 翻译语音 (UWP)

针对[语音转文本](quickstart-csharp-uwp.md)、[文本转语音](quickstart-text-to-speech-csharp-uwp.md)和[语音优先虚拟助手](quickstart-virtual-assistant-csharp-uwp.md)也提供了快速入门。

在本快速入门中，你将创建一个简单的通用 Windows 平台 (UWP) 应用程序，该应用程序从计算机的麦克风中捕获用户语音，翻译语音，并将翻译后的文本实时转录到命令行。 此应用程序设计为在 64 位 Windows 上运行，它是使用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2017 或更高版本生成的。

有关可用于语音翻译的语言的完整列表，请参阅[语言支持](language-support.md)。

> [!NOTE]
> 使用 UWP 可以开发在支持 Windows 10 的任何设备上运行的应用，包括电脑、Xbox、Surface Hub 和其他设备。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) 或更高版本
* 语音服务的 Azure 订阅密钥。 [免费获得一个](get-started.md)。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>添加示例代码

1. 应用程序的用户界面使用 XAML 进行定义。 在解决方案资源管理器中打开 `MainPage.xaml`。 在设计器的 XAML 视图中，将以下 XAML 代码片段插入到 `<Grid>` 和 `</Grid>` 之间。

    [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. 打开代码隐藏源文件 `MainPage.xaml.cs`（可以发现它归在 `MainPage.xaml` 下）。 将其中的所有代码替换为以下内容。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. 在此文件的 `SpeechTranslationFromMicrophone_ButtonClicked` 处理程序中，将字符串 `YourSubscriptionKey` 替换为订阅密钥。

1. 在 `SpeechTranslationFromMicrophone_ButtonClicked` 处理程序中，将字符串 `YourServiceRegion` 替换为与订阅（例如，`westus` 免费试用版订阅）相关联的[区域](regions.md)。

1. 保存对项目的所有更改。

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 构建应用程序。 从菜单栏中，选择“构建” > “构建解决方案”   。 现在，编译代码时应不会提示错误。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“生成解决方案”选项](media/sdk/qs-csharp-uwp-08-build.png "成功生成")

1. 启动应用程序。 在菜单栏中，选择“调试” > “开始调试”，或按 F5    。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“启动调试”选项](media/sdk/qs-csharp-uwp-09-start-debugging.png "启动应用进入调试")

1. 弹出一个窗口。 选择“启用麦克风”，然后确认弹出的权限请求  。

    ![权限请求的屏幕截图](media/sdk/qs-csharp-uwp-10-access-prompt.png "启动应用进入调试")

1. 选择“使用麦克风输入进行语音识别”，然后对着设备的麦克风说一个英文短语或句子  。 你的语音将传输到语音服务并转录为文本，该文本将显示在窗口中。

    ![语音识别用户界面的屏幕截图](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 C# 示例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另请参阅

- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
