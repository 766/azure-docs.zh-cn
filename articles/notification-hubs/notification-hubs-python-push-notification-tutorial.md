---
title: 如何结合使用通知中心与 Python
description: 了解如何从 Python 后端使用 Azure 通知中心。
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.author: jowargo
ms.date: 01/04/2019
ms.openlocfilehash: e2d0c7089ea070d82c75337c07fcc4a9df1c7c28
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359823"
---
# <a name="how-to-use-notification-hubs-from-python"></a>如何通过 Python 使用通知中心

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

如 MSDN 文章[通知中心 REST API](https://msdn.microsoft.com/library/dn223264.aspx) 中所述，可以使用通知中心 REST 接口从 Java/PHP/Python/Ruby 后端访问所有通知中心功能。

> [!NOTE]
> 这是在 Python 中实现通知发送的示例引用实现，不是官方支持的通知中心 Python SDK。 该示例是使用 Python 3.4 创建的。

本文介绍以下操作：

- 以 Python 构建 REST 客户端以获取通知中心功能。
- 使用 Python 接口发送通知到通知中心 REST API。
- 获取 HTTP REST 请求/响应的转储以进行调试/培训。

可按照所选定移动平台的[入门教程](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)，以 Python 实现后端部分。

> [!NOTE]
> 该示例仅限于发送通知，并不执行任何注册管理操作。

## <a name="client-interface"></a>客户端接口

主要的客户端接口可提供 [.NET 通知中心 SDK](https://msdn.microsoft.com/library/jj933431.aspx) 中可用的相同方法。 此接口允许直接翻译该网站目前提供的所有教程和示例，这些内容均由社区通过 Internet 提供。

可以在 [Python REST 包装器示例]中找到提供的所有代码。

例如，创建客户端：

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

发送 Windows toast 通知：

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>实现

如果尚未实现，按照[入门教程]学至最后一节，必须在此过程中实现后端。

有关实现完整 REST 包装器的所有详细信息，请访问 [MSDN](https://msdn.microsoft.com/library/dn530746.aspx)。 本节介绍访问通知中心 REST 终结点和发送通知所需的主要步骤的 Python 实现

1. 解析连接字符串
2. 生成授权令牌
3. 使用 HTTP REST API 发送通知

### <a name="parse-the-connection-string"></a>解析连接字符串

下面是实现客户端的主类，其构造函数解析连接字符串：

```python
class NotificationHub:
    API_VERSION = "?api-version=2013-10"
    DEBUG_SEND = "&test"

    def __init__(self, connection_string=None, hub_name=None, debug=0):
        self.HubName = hub_name
        self.Debug = debug

        # Parse connection string
        parts = connection_string.split(';')
        if len(parts) != 3:
            raise Exception("Invalid ConnectionString.")

        for part in parts:
            if part.startswith('Endpoint'):
                self.Endpoint = 'https' + part[11:]
            if part.startswith('SharedAccessKeyName'):
                self.SasKeyName = part[20:]
            if part.startswith('SharedAccessKey'):
                self.SasKeyValue = part[16:]
```

### <a name="create-security-token"></a>创建安全令牌

有关创建安全令牌的详细信息，请访问[此处](https://msdn.microsoft.com/library/dn495627.aspx)。
将以下方法添加到 `NotificationHub` 类，以便根据当前请求的 URI 和提取自连接字符串的凭据创建令牌。

```python
@staticmethod
def get_expiry():
    # By default returns an expiration of 5 minutes (=300 seconds) from now
    return int(round(time.time() + 300))


@staticmethod
def encode_base64(data):
    return base64.b64encode(data)


def sign_string(self, to_sign):
    key = self.SasKeyValue.encode('utf-8')
    to_sign = to_sign.encode('utf-8')
    signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
    digest = signed_hmac_sha256.digest()
    encoded_digest = self.encode_base64(digest)
    return encoded_digest


def generate_sas_token(self):
    target_uri = self.Endpoint + self.HubName
    my_uri = urllib.parse.quote(target_uri, '').lower()
    expiry = str(self.get_expiry())
    to_sign = my_uri + '\n' + expiry
    signature = urllib.parse.quote(self.sign_string(to_sign))
    auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
    sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
    return sas_token
```

### <a name="send-a-notification-using-http-rest-api"></a>使用 HTTP REST API 发送通知

首先，定义表示通知的类。

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm',
                         'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

此类是一个容器，其中包含本机通知正文或一组模板通知的属性，以及一组包含格式（本机平台或模板）和平台特定属性（如 Apple 过期属性和 WNS 标头）的标头。

请参阅[通知中心 REST API 文档](https://msdn.microsoft.com/library/dn495827.aspx)和具体的通知平台格式，了解所有可用选项。

现在有了此类后，便可在 `NotificationHub` 类中编写发送通知方法了。

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(
        parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True,
                                       indent=4, separators=(' ', ': ')))
        print("--- END REQUEST ---\n")

    connection.request('POST', url, payload, headers)
    response = connection.getresponse()

    if self.Debug > 0:
        # print out detailed response information for debugging purpose
        print("\n\n--- RESPONSE ---")
        print(str(response.status) + " " + response.reason)
        print(response.msg)
        print(response.read())
        print("--- END RESPONSE ---")

    elif response.status != 201:
        # Successful outcome of send message is HTTP 201 - Created
        raise Exception(
            "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

    connection.close()


def send_notification(self, notification, tag_or_tag_expression=None):
    url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

    if any(x in notification.format for x in json_platforms):
        content_type = "application/json"
        payload_to_send = json.dumps(notification.payload)
    else:
        content_type = "application/xml"
        payload_to_send = notification.payload

    headers = {
        'Content-type': content_type,
        'Authorization': self.generate_sas_token(),
        'ServiceBusNotification-Format': notification.format
    }

    if isinstance(tag_or_tag_expression, set):
        tag_list = ' || '.join(tag_or_tag_expression)
    else:
        tag_list = tag_or_tag_expression

    # add the tags/tag expressions to the headers collection
    if tag_list != "":
        headers.update({'ServiceBusNotification-Tags': tag_list})

    # add any custom headers to the headers collection that the user may have added
    if notification.headers is not None:
        headers.update(notification.headers)

    self.make_http_request(url, payload_to_send, headers)


def send_apple_notification(self, payload, tags=""):
    nh = Notification("apple", payload)
    self.send_notification(nh, tags)


def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
    self.send_notification(nh, tags)


def send_adm_notification(self, payload, tags=""):
    nh = Notification("adm", payload)
    self.send_notification(nh, tags)


def send_baidu_notification(self, payload, tags=""):
    nh = Notification("baidu", payload)
    self.send_notification(nh, tags)


def send_mpns_notification(self, payload, tags=""):
    nh = Notification("windowsphone", payload)

    if "<wp:Toast>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'toast',
                      'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile',
                      'X-NotificationClass': '1'}

    self.send_notification(nh, tags)


def send_windows_notification(self, payload, tags=""):
    nh = Notification("windows", payload)

    if "<toast>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/toast'}
    elif "<tile>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/tile'}
    elif "<badge>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/badge'}

    self.send_notification(nh, tags)


def send_template_notification(self, properties, tags=""):
    nh = Notification("template", properties)
    self.send_notification(nh, tags)
```

这些方法将 HTTP POST 请求发送到通知中心的 /messages 终结点，该请求具有发送通知的正确正文和标头。

### <a name="using-debug-property-to-enable-detailed-logging"></a>使用调试属性启用详细的日志记录

在初始化通知中心时启用调试属性会写出关于 HTTP 请求和响应转储的详细日志记录信息，以及详细的通知消息发送结果。
[通知中心 TestSend 属性](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100))会返回有关通知发送结果的详细信息。
若要使用它 - 请使用以下代码进行初始化：

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

通知中心发送请求 HTTP URL 会追加​​一个“test”查询字符串作为结果。

## <a name="complete-tutorial"></a>完成教程

现在，可以通过从 Python 后端发送通知来完成该入门教程。

初始化通知中心客户端（按[入门教程]中所述替换连接字符串和中心名称）：

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

然后，根据目标移动平台添加发送代码。 此示例还添加了更高级别的方法以支持基于平台发送通知，例如 send_windows_notification for windows; send_apple_notification (for apple) 等。

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows 应用商店和 Windows Phone 8.1（非 Silverlight）

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 和 8.1 Silverlight

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle Fire

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>百度

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

运行 Python 代码，现在应该生成显示在目标设备上的通知。

## <a name="examples"></a>示例

### <a name="enabling-the-debug-property"></a>启用 `debug` 属性

如果在初始化 NotificationHub 时启用调试标志，则会显示详细的 HTTP 请求和响应转储以及 NotificationOutcome，如下所示，可从中了解哪些 HTTP 标头传入请求以及从通知中心收到哪些 HTTP 响应：

![][1]

例如会出现详细的通知中心结果。

- 当消息成功发送到推送通知服务时。
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- 如果没有找到任何推送通知的目标，则可能出现以下输出作为响应（这表明可能没有找到传递通知的注册，因为这些注册具有一些不匹配的标记）
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>将 toast 通知广播到 Windows

向 Windows 客户端发送 toast 广播通知时请注意发送出去的标头。

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>发送通知指定标记（或标记表达式）

请注意添加到 HTTP 请求的 Tags HTTP 标头（在以下示例中，仅将通知发送给具有 'sports' 负载的注册）

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>发送通知指定多个标记

请注意发送多个标记时 Tags HTTP 标头的变化方式。

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>模板通知

请注意 Format HTTP 标头的变化和负载正文作为 HTTP 请求正文的一部分发送：

**客户端 - 已注册的模板：**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**服务器端 - 发送有效负载：**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>后续步骤

本文介绍了如何为通知中心创建 Python REST 客户端。 从这里可以：

- 下载完整的 [Python REST 包装器示例]，其中包含本文中的所有代码。
- 在[突发新闻教程]中继续学习通知中心标记功能
- 在[本地化新闻教程]中继续学习通知中心模板功能

<!-- URLs -->
[Python REST 包装器示例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[入门教程]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[突发新闻教程]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[本地化新闻教程]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
