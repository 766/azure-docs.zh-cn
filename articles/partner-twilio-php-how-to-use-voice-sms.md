---
title: 如何使用 Twilio 实现语音和短信功能 (PHP) | Microsoft Docs
description: 了解如何在 Azure 中使用 Twilio API 服务发起电话呼叫和发送短信。 采用 PHP 编写的代码示例。
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 34057f1962338927a252011dccc56ed6a77bec47
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636029"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>如何通过 PHP 使用 Twilio 实现语音和 SMS 功能
本指南演示如何在 Azure 中使用 Twilio API 服务执行常见编程任务。 所涉及的任务包括发起电话呼叫和发送短信服务 (SMS) 消息。 有关 Twilio 以及在应用程序中使用语音和短信的详细信息，请参阅[后续步骤](#NextSteps)部分。

## <a id="WhatIs"></a>什么是 Twilio？
Twilio 为将来的商业沟通提供强大支持，并使开发人员能够将语音、VoIP 和消息传送嵌入到应用程序中。 它们对基于云的全球环境中所需的所有基础结构进行虚拟化，并通过 Twilio 通信 API 平台将其公开。 可轻松构建和扩展应用程序。 享受现用现付定价所带来的灵活性，并从云可靠性中受益。

利用 **Twilio 语音**，应用程序可以发起和接收电话呼叫。 **Twilio SMS** 使应用程序能够发送和接收文本消息。 利用 **Twilio 客户端**，可以从任何手机、平板电脑或浏览器发起 VoIP 呼叫并支持 WebRTC。

## <a id="Pricing"></a>Twilio 定价和特别优惠
Azure 客户在升级 Twilio 帐户后即可获得[特别优惠](https://www.twilio.com/azure)：10 美元的 Twilio 信用额度。 此 Twilio 信用可应用于任何 Twilio 使用（10 美元信用等价于发送多达 1,000 条 SMS 消息或接收长达 1000 分钟的入站语音，具体取决电话号码和消息或呼叫目标的位置）。 兑换此 Twilio 信用并从以下网址开始：[https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure)。

Twilio 是一种现用现付服务。 没有设置费用，并且可以随时关闭帐户。 可以在 [Twilio 定价][twilio_pricing]中找到更多详细信息。

## <a id="Concepts"></a>概念
Twilio API 是一个为应用程序提供语音和 SMS 功能的 RESTful API。 提供多种语言的客户端库;有关列表, 请参阅[TWILIO API 库][twilio_libraries]。

Twilio API 的关键方面是 Twilio 谓词和 Twilio 标记语言 (TwiML)。

### <a id="Verbs"></a>Twilio 谓词
API 利用了 Twilio 谓词；例如， **&lt;Say&gt;** 谓词指示 Twilio 在呼叫时传递语音消息。

下面是 Twilio 谓词的列表。 通过 [Twilio 标记语言文档](https://www.twilio.com/docs/api/twiml)了解其他谓词和功能。

* **&lt;Dial&gt;** ：将呼叫方连接到其他电话。
* **&lt;Gather&gt;** ：收集通过电话按键输入的数字。
* **&lt;Hangup&gt;** ：结束呼叫。
* **&lt;Play&gt;** ：播放音频文件。
* **&lt;Pause&gt;** ：安静地等待指定的秒数。
* **&lt;Record&gt;** ：录制呼叫方的声音并返回包含该录音的文件的 URL。
* **&lt;Redirect&gt;** ：将对呼叫或短信的控制转移到其他 URL 上的 TwiML。
* **&lt;Reject&gt;** ：拒绝对 Twilio 号码的传入呼叫且无需付费
* **&lt;Say&gt;** ：将文本转换为通话语音。
* **&lt;Sms&gt;** ：发送短信。

### <a id="TwiML"></a>TwiML
TwiML 是一组基于 XML 的指令，这些指令以用于指示 Twilio 如何处理呼叫或 SMS 的 Twilio 谓词为基础。

例如，以下 TwiML 将文本 **Hello World** 转换为语音。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

当应用程序调用 Twilio API 时，某个 API 参数将为返回 TwiML 响应的 URL。 在开发过程中，可以使用 Twilio 提供的 URL 来提供应用程序所使用的 TwiML 响应。 还可以托管自己的 URL 来生成 TwiML 响应，也可以选择使用 **TwiMLResponse** 对象。

有关 Twilio 谓词、其属性和 TwiML 的详细信息, 请参阅[TwiML][twiml]。 有关 Twilio API 的其他信息, 请参阅[TWILIO api][twilio_api]。

## <a id="CreateAccount"></a>创建 Twilio 帐户
准备好获取 Twilio 帐户后, 请在[试用 Twilio][try_twilio]上注册。 可以先使用免费帐户，以后再升级帐户。

注册 Twilio 帐户时，将收到帐户 ID 和身份验证令牌。 需要二者才能发起 Twilio API 呼叫。 为了防止对帐户进行未经授权的访问，请保护身份验证令牌。 你的帐户 ID 和身份验证令牌可在[Twilio 帐户页][twilio_account]上分别在标记为 "**帐户 SID** " 和 "**身份验证令牌**" 的字段中查看。

## <a id="create_app"></a>创建 PHP 应用程序
使用 Twilio 服务且在 Azure 中运行的 PHP 应用程序与任何其他使用 Twilio 服务的 PHP 应用程序之间没有任何差别。 尽管 Twilio 服务是基于 REST 的, 并且可以通过多种方式从 PHP 中调用, 本文将重点介绍如何将 Twilio 服务与 GitHub 中的用于[PHP 的 Twilio 库][twilio_php]一起使用。 有关使用用于 PHP 的 Twilio 库的详细信息, 请[https://www.twilio.com/docs/libraries/php][twilio_lib_docs]参阅。

有关生成 Twilio/PHP 应用程序并将其部署到 Azure 的详细说明,[请参阅如何在 azure 上的 PHP 应用程序中使用 Twilio 发起电话呼叫][howto_phonecall_php]。

## <a id="configure_app"></a>将应用程序配置为使用 Twilio 库
可以通过两种方式将应用程序配置为使用用于 PHP 的 Twilio 库：

1. 从 GitHub 下载用于 PHP 的 Twilio 库 ([https://github.com/twilio/twilio-php][twilio_php]), 并将 "**服务**" 目录添加到应用程序。
   
    \- 或 -
2. 将用于 PHP 的 Twilio 库作为 PEAR 包安装。 可使用以下命令安装它：
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

安装用于 PHP 的 Twilio 库后，可以在 PHP 文件的顶部添加 **require_once** 语句来引用该库：

        require_once 'Services/Twilio.php';

有关详细信息，请参阅 [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme]。

## <a id="howto_make_call"></a>如何：发起传出呼叫
下面演示了如何使用 **Services_Twilio** 类发起传出呼叫。 此代码还使用 Twilio 提供的网站返回 Twilio 标记语言 (TwiML) 响应。 用自己的值替换“呼叫方”和“被呼叫方”电话号码，并确保在运行代码之前验证 Twilio 帐户的“呼叫方”电话号码。

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the call.
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "https://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

如前所述，此代码使用 Twilio 提供的网站返回 TwiML 响应。 可以改用自己的网站来提供 TwiML 响应；有关详细信息，请参阅[如何从自己的网站提供 TwiML 响应](#howto_provide_twiml_responses)。

* **注意**：若要解决 SSL 证书验证错误, 请参阅[http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>如何：发送短信
下面演示如何使用 **Services_Twilio** 类发送 SMS 消息。 “呼叫方”号码由 Twilio 提供，供试用帐户用来发送 SMS 消息。 在运行代码前，必须为 Twilio 帐户验证“被呼叫方”号码。

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>如何：从自己的网站提供 TwiML 响应
当应用程序发起对 Twilio API 的调用时，Twilio 会将请求发送到应返回 TwiML 响应的 URL。 上面的示例使用 Twilio 提供的 URL [https://twimlets.com/message][twimlet_message_url]。 （虽然 TwiML 专供 Twilio 使用，但可以在浏览器中查看它。 [https://twimlets.com/message][twimlet_message_url]例如, 单击以查看空[https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] `<Response>` 元素; 作为另一个示例`<Response>` , 单击以查看包含`<Say>` 元素的元素。)

可以创建自己的返回 HTTP 响应的网站，而不用依赖 Twilio 提供的 URL。 可以使用任何语言创建返回 XML 响应的站点；本主题假设要使用 PHP 创建 TwiML。

以下 PHP 页面将生成在呼叫时念出 **Hello World** 的 TwiML 响应。

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

如上面的示例中所示，TwiML 响应只是一个 XML 文档。 用于 PHP 的 Twilio 库包含将生成 TwiML 的类。 下面的示例将生成与上面所示相同的响应，但该响应会使用用于 PHP 的 Twilio 库中的 **Services\_Twilio\_Twiml** 类：

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

有关 TwiML 的详细信息，请参阅 [https://www.twilio.com/docs/api/twiml][twiml_reference]。 

将 PHP 页面设置为提供 TwiML 响应后，请使用 PHP 页面的 URL 作为传入到 `Services_Twilio->account->calls->create` 方法中的 URL。 例如，如果已将名为 **MyTwiML** 的 Web 应用程序部署到 Azure 托管服务，且 PHP 页面的名称将为 **mytwiml.php**，则可将 URL 传递到 **Services_Twilio->account->calls->create**，如以下示例所示：

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

有关将 Azure 中的 Twilio 与 PHP 配合使用的其他信息, 请参阅[如何在 azure 上的 PHP 应用程序中使用 Twilio 发起电话呼叫][howto_phonecall_php]。

## <a id="AdditionalServices"></a>如何：使用其他 Twilio 服务
除了此处所示的示例之外，Twilio 还提供了基于 Web 的 API，可通过这些 API 从 Azure 应用程序中使用其他 Twilio 功能。 有关完整详细信息, 请参阅[TWILIO API 文档][twilio_api_documentation]。

## <a id="NextSteps"></a>后续步骤
现在，已了解 Twilio 服务的基础知识，单击下面的链接可以了解详细信息：

* [Twilio 安全准则][twilio_security_guidelines]
* [Twilio 如何和示例代码][twilio_howtos]
* [Twilio 快速入门教程][twilio_quickstarts] 
* [GitHub 上的 Twilio][twilio_on_github]
* [与 Twilio 支持人员交流][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
