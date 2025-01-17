---
title: 列出实体类型-LUIS
titleSuffix: Azure Cognitive Services
description: 列表实体表示一组固定、封闭的相关单词及其同义词。 LUIS 不会为列表实体发现更多值。 使用 "建议" 功能可查看基于当前列表的新词建议。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: ca9f8b570ee28b1913c8ec81c66a5b70827c04d6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559958"
---
# <a name="list-entity"></a>列表实体 

列表实体表示一组固定、封闭的相关单词及其同义词。 LUIS 不会为列表实体发现更多值。 使用“建议”功能根据当前列表查看有关新词的建议。 如果存在多个具有相同值的列表实体，则终结点查询中会返回其中每个实体。 

列表实体不是计算机学习的实体。 它是确切的文本匹配。 LUIS 将任何列表中某个项的任何匹配项标记为响应中的实体。 

**当文本数据为时, 实体非常适合:**

* 是已知的集。
* 不经常更改。 如果需要经常更改列表或希望列表自行扩展，则使用短语列表提升的简单实体是更好的选择。 
* 此集不超出此实体类型的最大 LUIS [边界](luis-boundaries.md)。
* 话语中的文本是同义项或规范名称的完全匹配。 LUIS 不会使用除文本完全匹配项之外的列表。 使用列表实体无法解析模糊匹配、不区分大小写、词干、复数形式和其他变体。 若要管理变体，请考虑使用带有可选文本语法的[模式](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance)。

![列表实体](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>示例 JSON

假设应用有一个名为 `Cities` 的列表，允许城市名称的变体，包括机场城市 (Sea-tac)、机场代码 (SEA)、邮政编码 (98101) 和电话区号 (206)。

|列表项|项同义词|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`、`roissy`、`ory`、`75001`、`1`、`+33`|

`book 2 tickets to paris`

在之前的陈述中，单词 `paris` 映射至属于 `Cities` 列表实体一部分的“巴黎”项。 列表实体同时匹配项的规范化名称及其同义词。

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 22,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

另一个使用巴黎的同义词的示例陈述：

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

|数据对象|实体名称|值|
|--|--|--|
|简单实体|`Customer`|`bob jones`|

## <a name="next-steps"></a>后续步骤

在本[教程](luis-quickstart-intent-and-list-entity.md)中, 了解如何使用**列表实体**从已知项列表中提取精确的文本匹配项。 
