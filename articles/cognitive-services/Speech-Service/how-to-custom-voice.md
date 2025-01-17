---
title: 什么是自定义语音？ -语音服务
titleSuffix: Azure Cognitive Services
description: 自定义语音是一组联机工具, 可用于为品牌创建可识别的一种类型的声音。 开始使用的只是一些音频文件和关联的转录。 请按照以下链接开始创建自定义语音到文本体验。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 10d76bc1dd52f04cceb9f0952a755c55d90c6896
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562802"
---
# <a name="get-started-with-custom-voice"></a>自定义语音入门

自定义语音是一组联机工具, 可用于为品牌创建可识别的一种类型的声音。 开始使用的只是一些音频文件和关联的转录。 请按照以下链接开始创建自定义的文本到语音体验。

## <a name="whats-in-custom-voice"></a>自定义语音有哪些功能？

在开始使用自定义语音之前, 你将需要一个 Azure 帐户和一个语音服务订阅。 创建帐户后, 可以准备数据、训练和测试模型、评估语音质量并最终部署自定义语音模型。

下图突出显示了使用自定义语音门户创建自定义语音模型的步骤。 使用链接了解更多信息。

![自定义语音体系结构图](media/custom-voice/custom-voice-diagram.png)

1.  [订阅和创建项目](#set-up-your-azure-account)-创建 Azure 帐户并创建语音服务订阅。 此统一订阅使你可以访问语音到文本、文本到语音、语音翻译和自定义语音门户。 然后, 使用您的语音服务订阅创建您的第一个自定义语音项目。

2.  使用自定义语音门户或自定义语音 API[上传数据](how-to-custom-voice-create-voice.md#upload-your-datasets)(音频和文本)。 在门户中, 可以调查和评估发音评分和信噪比。 有关详细信息, 请参阅[如何为自定义语音准备数据](how-to-custom-voice-prepare-data.md)。

3.  [训练模型](how-to-custom-voice-create-voice.md#build-your-custom-voice-model)–使用您的数据创建自定义的文本到语音转换语音模型。 您可以用不同的语言来训练模型。 在定型后测试模型, 如果对结果满意, 则可以部署模型。

4.  [部署模型](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint)-为文本语音模型创建自定义终结点, 并在产品、工具和应用程序中将其用于语音合成。

## <a name="set-up-your-azure-account"></a>设置你的 Azure 帐户

需要语音服务订阅, 然后才能使用自定义语音门户创建自定义模型。 按照这些说明在 Azure 中创建语音服务订阅。 如果没有 Azure 帐户, 可以注册一个新帐户。  

创建 Azure 帐户和语音服务订阅后, 需要登录到自定义语音门户并连接订阅。

1. 从 Azure 门户获取语音服务订阅密钥。
2. 登录到[自定义语音门户](https://aka.ms/custom-voice)。
3. 选择订阅并创建语音项目。
4. 若要切换到另一个语音订阅, 请使用位于顶部导航栏中的齿轮图标。

> [!NOTE]
> 自定义语音服务不支持30天免费试用版。 必须先在 Azure 中创建 F0 或 S0 密钥, 然后才能使用该服务。

## <a name="how-to-create-a-project"></a>如何创建项目

数据、模型、测试和终结点等内容在自定义语音门户中组织为**项目**。 每个项目都是特定于国家/语言的, 以及要创建的语音的性别。 例如, 你可以创建一个项目, 用于在美国 (en-us) 中使用英语的呼叫中心的聊天机器人。

若要创建您的第一个项目, 请选择 "**文本到语音/自定义语音**" 选项卡, 然后单击 "**新建项目**"。 按照向导提供的说明创建项目。 创建项目后, 你将看到四个选项卡:**数据**、**培训**、**测试**和**部署**。 使用[后续步骤](#next-steps)中提供的链接来了解如何使用各个选项卡。

## <a name="next-steps"></a>后续步骤

- [准备自定义语音数据](how-to-custom-voice.md)
- [创建自定义语音](how-to-custom-voice-create-voice.md)
- [向导记录语音示例](record-custom-voice-samples.md)
