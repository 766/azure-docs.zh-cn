---
title: 如何选择必应语音识别模式 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: 如何选择必应语音中的最佳识别模式。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 99e69691d9904285ecff356523813b426c98fca2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515018"
---
# <a name="bing-speech-recognition-modes"></a>必应语音识别模式

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

必应语音转文本 API 支持多种语音识别模式。 请选择在应用中识别效果最佳的模式。

| 模式 | 描述 |
|---|---|
| *交互* | 适用于交互用户应用方案的“命令和控制”识别。 用户说出较短的短语，作为向应用发出的命令。 |
| 听写  | 适用于听写方案的连续识别。 用户说出以文本形式显示的较长句子。 用户采用更正式的说话风格。 |
| 对话  | 适用于听录人员对话的连续识别。 用户采用不太正式的说话风格，并且可能会交替使用较长句子和较短短语。

> [!NOTE]
> 这些模式适用于使用 [REST API](../GetStarted/GetStartedREST.md) 的情况。 [客户端库](../GetStarted/GetStartedClientLibraries.md)使用不同的参数来指定识别模式。 有关详细信息，请参阅选定客户端库。

有关详细信息，请参阅[识别模式](../concepts.md#recognition-modes)页。
