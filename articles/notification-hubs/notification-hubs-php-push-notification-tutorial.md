---
title: 如何结合使用通知中心与 PHP
description: 了解如何从 PHP 后端使用 Azure 通知中心。
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 054edaf321d90015840fd84e1697fca742fd7e1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60872171"
---
# <a name="how-to-use-notification-hubs-from-php"></a>如何通过 PHP 使用通知中心

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

如 MSDN 主题[通知中心 REST API](https://msdn.microsoft.com/library/dn223264.aspx) 中所述，可以使用通知中心 REST 接口从 Java/PHP/Ruby 后端访问所有通知中心功能。

本主题中，我们将向你介绍如何：

* 以 PHP 构建 REST 客户端以获取通知中心功能；
* 请按照选定移动平台的[入门教程](notification-hubs-ios-apple-push-notification-apns-get-started.md)操作，用 PHP 实现后端部分。

## <a name="client-interface"></a>客户端接口

主要的客户端接口可提供 [.NET 通知中心 SDK](https://msdn.microsoft.com/library/jj933431.aspx) 中提供的相同方法，这允许直接翻译当前此站点上提供的所有教程和示例，这些内容均来自 Internet 上的社区。

可以在 [PHP REST 包装器示例]中找到提供的所有代码。

例如，创建客户端：

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

发送 iOS 本机通知：

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>实现

如果尚未实现，按照[入门教程]学至最后一节，必须在此过程中实现后端。
此外，如果希望可以使用 [PHP REST 包装器示例]中的代码并直接转到[完成本教程](#complete-tutorial)部分。

有关实现完整 REST 包装器的所有详细信息，请访问 [MSDN](https://msdn.microsoft.com/library/dn530746.aspx)。 本部分介绍了访问通知中心 REST 终结点所需的主要步骤的 PHP 实现：

1. 解析连接字符串
2. 生成授权令牌
3. 执行 HTTP 调用

### <a name="parse-the-connection-string"></a>解析连接字符串

下面是实现客户端的主类，其构造函数将解析连接字符串：

    ```php
    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";

        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;

        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;

            $this->parseConnectionString($connectionString);
        }

        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }

            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }
    ```

### <a name="create-a-security-token"></a>创建安全令牌

请参阅 Azure 文档来了解有关如何[创建 SAS 安全令牌](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token)的信息。

将 `generateSasToken` 方法添加到 `NotificationHub` 类，以便根据当前请求的 URI 和提取自连接字符串的凭据创建令牌。

    ```php
    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }
    ```

### <a name="send-a-notification"></a>发送通知

首先，让我们定义表示通知的类。

    ```php
    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }
    ```

此类是一个容器，其中包含本机通知正文或一组模板通知上的属性，以及一组包含格式（本机平台或模板）和平台特定属性（如 Apple 过期属性和 WNS 标头）的标头。

请参阅[通知中心 REST API 文档](https://msdn.microsoft.com/library/dn495827.aspx)和具体的通知平台格式，了解所有可用选项。

具备了此类后，我们现在可以在 `NotificationHub` 类中编写发送通知方法了：

    ```php
    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "fcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }

        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 
    ```

以上方法将 HTTP POST 请求发送到通知中心的 `/messages` 终结点，该请求具有发送通知的正确正文和标头。

## <a name="complete-tutorial"></a>完成教程

现在，可以通过从 PHP 后端发送通知来完成该入门教程。

初始化通知中心客户端（按[入门教程]中所述替换连接字符串和中心名称）：

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

然后，根据目标移动平台添加发送代码。

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows 应用商店和 Windows Phone 8.1（非 Silverlight）

    ```php
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);
    ```

### <a name="ios"></a>iOS

    ```php
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);
    ```

### <a name="android"></a>Android

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("fcm", $message);
    $hub->sendNotification($notification, null);
    ```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 和 8.1 Silverlight

    ```php
    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);
    ```

### <a name="kindle-fire"></a>Kindle Fire

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);
    ```

运行 PHP 代码，现在应该生成显示在目标设备上的通知。

## <a name="next-steps"></a>后续步骤

在本主题中，我们介绍了如何为通知中心创建简单的 Java REST 客户端。 从这里可以：

* 下载完整的 [PHP REST 包装器示例]，其中包含上述所有代码。
* 在 [突发新闻教程] 中继续学习通知中心标记功能
* 在 [通知用户教程] 中了解如何将通知推送到单个用户

有关详细信息，另请参阅 [PHP 开发人员中心](https://azure.microsoft.com/develop/php/)。

[PHP REST 包装器示例]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[入门教程]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
