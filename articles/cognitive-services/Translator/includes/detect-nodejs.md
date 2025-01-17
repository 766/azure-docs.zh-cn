---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 43f9d82ca2b6db55fe5c125b63da6c199e7cf24c
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968205"
---
## <a name="prerequisites"></a>先决条件

本快速入门需要：

* [Node 8.12.x 或更高版本](https://nodejs.org/en/)
* 适用于文本翻译的 Azure 订阅密钥

## <a name="create-a-project-and-import-required-modules"></a>创建一个项目并导入必需的模块

使用最喜欢的 IDE 或编辑器创建新的项目。 然后，将此代码片段复制到项目的名为 `detect.js` 的文件中。

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> 如果尚未使用这些模块，则需在运行程序之前安装它们。 若要安装这些包，请运行 `npm install request uuidv4`。

若要构造 HTTP 请求以及为 `'X-ClientTraceId'` 标头创建唯一标识符，必须使用这些模块。

## <a name="set-the-subscription-key"></a>设置订阅密钥

此代码会尝试从环境变量 `TRANSLATOR_TEXT_KEY` 读取文本翻译订阅密钥。 如果不熟悉环境变量，则可将 `subscriptionKey` 设置为字符串并注释掉条件语句。

将以下代码复制到项目中：

```javascript
/* Checks to see if the subscription key is available
as an environment variable. If you are setting your subscription key as a
string, then comment these lines out.

If you want to set your subscription key as a string, replace the value for
the Ocp-Apim-Subscription-Key header as a string. */
const subscriptionKey = process.env.TRANSLATOR_TEXT_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};
```

## <a name="configure-the-request"></a>配置请求

使用通过请求模块提供的 `request()` 方法，可以以 `options` 对象的形式传递 HTTP 方法、URL、请求参数、标头和 JSON 正文。 在此代码片段中，我们将配置请求：

>[!NOTE]
> 有关终结点、路由和请求参数的详细信息，请参阅[文本翻译 API 3.0：检测](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect)。

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'detect',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Salve, mondo!'
    }],
    json: true,
};
```
若要对请求进行身份验证，最容易的方法是将订阅密钥作为 `Ocp-Apim-Subscription-Key` 标头传入，这是我们在此示例中使用的方法。 替代方法是交换订阅密钥来获取访问令牌，将访问令牌作为 `Authorization` 标头传入，以便对请求进行验证。

如果使用的是认知服务多服务订阅，则还必须在请求标头中包括 `Ocp-Apim-Subscription-Region`。

有关详细信息，请参阅[身份验证](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="make-the-request-and-print-the-response"></a>发出请求并输出响应

接下来，请使用 `request()` 方法来创建请求。 它采用我们在上一部分创建的 `options` 对象作为第一个参数，然后输出美化的 JSON 响应。

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> 在此示例中，我们将在 `options` 对象中定义 HTTP 请求。 不过，请求模块也支持便捷方法，例如 `.post` 和 `.get`。 有关详细信息，请参阅[便捷方法](https://github.com/request/request#convenience-methods)。

## <a name="put-it-all-together"></a>将其放在一起

就是这样，你已构建了一个简单的程序。该程序可以调用文本翻译 API 并返回 JSON 响应。 现在，可以运行该程序了：

```console
node detect.js
```

## <a name="sample-response"></a>示例响应

请在此[语言列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)中查找国家/地区缩写。

```json
[
    {
        "alternatives": [
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "pt",
                "score": 1.0
            },
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "en",
                "score": 1.0
            }
        ],
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "it",
        "score": 1.0
    }
]
```

## <a name="clean-up-resources"></a>清理资源

如果已将订阅密钥硬编码到程序中，请确保在完成本快速入门后删除该订阅密钥。

## <a name="next-steps"></a>后续步骤

查看 API 参考，了解使用文本翻译 API 可以执行的所有操作。

> [!div class="nextstepaction"]
> [API 参考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
