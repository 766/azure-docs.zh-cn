---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: a6c12a2fdc8616dd6f7107d11e8f6c77401811fb
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968005"
---
## <a name="prerequisites"></a>先决条件

* [JDK 7 或更高版本](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* 适用于文本翻译的 Azure 订阅密钥

## <a name="initialize-a-project-with-gradle"></a>使用 Gradle 初始化项目

首先，创建此项目的工作目录。 从命令行（或终端）中，运行以下命令：

```console
mkdir length-sentence-sample
cd length-sentence-sample
```

接下来，初始化一个 Gradle 项目。 此命令将创建 Gradle 的基本生成文件，最重要的是 `build.gradle.kts`，它在运行时用来创建并配置应用程序。 从工作目录运行以下命令：

```console
gradle init --type basic
```

当提示你选择一个 **DSL** 时，选择 **Kotlin**。

## <a name="configure-the-build-file"></a>配置生成文件

找到 `build.gradle.kts` 并使用你喜欢使用的 IDE 或文本编辑器将其打开。 然后将以下生成配置复制到其中：

```
plugins {
    java
    application
}
application {
    mainClassName = "LengthSentence"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

请注意，此示例依赖于 HTTP 请求的 OkHttp 以及 Gson 来处理和分析 JSON。 如果要详细了解生成配置，请参阅[创建新的 Gradle 生成](https://guides.gradle.org/creating-new-gradle-builds/)。

## <a name="create-a-java-file"></a>创建 Java 文件

为示例应用创建一个文件夹。 从工作目录中，运行：

```console
mkdir -p src/main/java
```

接下来，在此文件夹中，创建一个名为 `LengthSentence.java` 的文件。

## <a name="import-required-libraries"></a>导入所需的库

打开 `LengthSentence.java` 并添加以下 import 语句：

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>定义变量

首先，为你的项目创建一个公共类：

```java
public class LengthSentence {
  // All project code goes here...
}
```

将以下行添加到 `LengthSentence` 类。 你会注意到，除了 `api-version`，还可以定义输入语言。 在此示例中，语言为英语。

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0&language=en";
```
如果使用的是认知服务多服务订阅，则还必须在请求参数中包括 `Ocp-Apim-Subscription-Region`。 [详细了解如何使用多服务订阅进行身份验证](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="create-a-client-and-build-a-request"></a>创建客户端并生成请求

将以下行添加到 `LengthSentence` 类来实例化 `OkHttpClient`：

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

接下来，我们将生成 POST 请求。 可随意更改文本。 文本必须进行转义。

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"How are you? I am fine. What did you do today?\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>创建一个函数来分析响应

这个简单的函数分析来自文本翻译服务的 JSON 响应并对其进行美化。

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>将其放在一起

最后一步是发出请求并获得响应。 将以下行添加你的项目：

```java
public static void main(String[] args) {
    try {
        LengthSentence lengthSentenceRequest = new LengthSentence();
        String response = lengthSentenceRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>运行示例应用

上述操作完成后，就可以运行示例应用了。 从命令行（或终端会话）导航到工作目录的根，然后运行以下命令：

```console
gradle build
```

当生成完成后，请运行：

```console
gradle run
```

## <a name="sample-response"></a>示例响应

成功的响应以 JSON 格式返回，如以下示例所示：

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>后续步骤

查看 API 参考，了解使用文本翻译 API 可以执行的所有操作。

> [!div class="nextstepaction"]
> [API 参考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
