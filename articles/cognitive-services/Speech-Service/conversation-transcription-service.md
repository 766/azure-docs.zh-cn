---
title: 对话听录服务-语音服务
titleSuffix: Azure Cognitive Services
description: 对话听录服务是一项高级的功能的结合了实时的语音识别、 说话人识别和 diarization 语音服务。 对话听录服务非常适用于能够区分扬声器转录内的面对面会议，它允许您知道谁说的内容和时间，允许参与者可以将精力集中在会议上并快速跟进后续步骤。 此功能还可以提高可访问性。 使用脚本，您可以主动进行有听力障碍的参与者。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 2c3269a9e57c0392e152fedfb640b9fae91a4989
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025931"
---
# <a name="what-is-the-conversation-transcription-service"></a>什么是会话听录服务？

对话听录服务是一项高级的功能的结合了实时的语音识别、 说话人识别和 diarization 语音服务。 对话听录服务非常适用于能够区分扬声器转录内的面对面会议，它允许您知道谁说的内容和时间，允许参与者可以将精力集中在会议上并快速跟进后续步骤。 此功能还可以提高可访问性。 使用脚本，您可以主动进行有听力障碍的参与者。   

会话脚本服务为您提供使用，可以调整以了解行业和特定于公司的词汇的可自定义语音模型准确识别。 此外，您可以搭配使用对话听录服务使用语音设备 SDK 来优化多麦克风设备的体验。

>[!NOTE]
> 目前，对于小型会议建议会话脚本服务。 如果你想要在规模较大的大型会议会话听录服务扩展，请与我们联系。

此图描述了硬件、 软件和服务，可与对话听录服务一起工作。

![导入会话听录服务关系图](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> 循环的七个麦克风数组具有特定几何图形配置是必需的。 规范和设计的详细信息，请参阅[Microsoft 语音设备 SDK 麦克风](https://aka.ms/cts/microphone)。 若要了解详细信息，或购买开发工具包，请参阅[获取 Microsoft 语音设备 SDK](https://aka.ms/cts/getsdk)。

## <a name="get-started-with-conversation-transcription"></a>开始使用会话的脚本

有三个步骤需要采取若要开始使用对话脚本服务。

1. 从用户收集语音样本。
2. 生成使用用户语音示例的用户配置文件
3. 使用语音 SDK 标识的用户 （扬声器） 和转录语音

## <a name="collect-user-voice-samples"></a>收集用户语音样本

第一步是从每个用户收集音频录制。 用户语音应记录在一个安静的环境，而无需背景噪音。 每个音频采样的推荐的长度为 30 秒到两分钟。 标识演讲者时，较长的音频示例都将导致更加准确。 音频必须是具有 16 KHz 采样率的 mono 通道。

超出上述指南，如何音频记录并将它们存储为取决于您-被建议的安全的数据库中。 在下一步部分中，我们将回顾此音频如何用于生成语音 SDK 中用于识别说话人的用户配置文件。

## <a name="generate-user-profiles"></a>生成用户配置文件

接下来，你将需要发送的音频录制已收集到签名生成服务以验证音频并生成用户配置文件。 [签名生成服务](https://aka.ms/cts/signaturegenservice)是一组 REST Api，允许您生成并检索用户配置文件。

若要创建用户配置文件，你将需要使用`GenerateVoiceSignature`API。 规范详细信息和示例代码有：

> [!NOTE]
> 对话听录服务目前已在以下区域：`centralus`和`eastasia`。

* [REST 规范](https://aka.ms/cts/signaturegenservice)
* [了解如何使用会话脚本服务](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>转录并识别说话人

对话听录服务需要多声道音频流和用户配置文件作为输入，使生成转录和识别说话人。 音频和用户配置文件数据发送到对话听录服务使用语音设备 SDK。 如前文所述，需要循环的七个麦克风阵列和语音设备 SDK 使用对话脚本服务。

>[!NOTE]
> 规范和设计的详细信息，请参阅[Microsoft 语音设备 SDK 麦克风](https://aka.ms/cts/microphone)。 若要了解详细信息，或购买开发工具包，请参阅[获取 Microsoft 语音设备 SDK](https://aka.ms/cts/getsdk)。

若要了解如何使用语音设备 SDK 的对话听录服务，请参阅[了解如何使用会话听录服务](https://aka.ms/cts/howto)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解有关语音设备 SDK 的详细信息](speech-devices-sdk.md)