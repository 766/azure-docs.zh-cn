---
title: Azure 通知中心安全推送
description: 了解如何从 Azure 将安全推送通知发送到 iOS 应用。 代码示例是使用 .Objective-C 和 C# 编写的。
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: d88bdb1eaeb95413df84bf69ed4fc763b6d4901f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61458480"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure 通知中心安全推送

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>概述

利用 Microsoft Azure 中的推送通知支持，可以访问易于使用且横向扩展的多平台推送基础结构，这大大简化了为移动平台的使用者应用程序和企业应用程序实现推送通知的过程。

由于法规或安全约束，有时应用程序可能想要在通知中包含某些无法通过标准推送通知基础结构传输的内容。 本教程介绍如何通过客户端设备和应用后端之间安全且经过验证的连接发送敏感信息，以便获得相同的体验。

在高级别中，此流程如下所示：

1. 应用后端：
   * 在后端数据库中存储安全有效负载。
   * 将此通知的 ID 发送到此设备（不发送任何安全信息）。
2. 此设备上的应用在接收通知时：
   * 此设备将联系请求安全有效负载的后端。
   * 此应用可以将有效负载显示为设备上的通知。

请务必注意，在之前的流程（以及本教程中）中，我们假设此设备会在用户登录后在本地存储中存储身份验证令牌。 这可以保证无缝的体验，因为该设备可以使用此令牌检索通知的安全有效负载。 如果应用程序未在设备上存储身份验证令牌，或者如果这些令牌可能已过期，此设备应用在收到通知时应显示提示用户启动应用的通用通知。 然后应用会对用户进行身份验证，并显示通知有效负载。

本安全推送教程演示如何安全地发送推送通知。 本教程以[通知用户](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)教程为基础，因此应先完成该教程中的步骤。

> [!NOTE]
> 本教程假设已根据[通知中心入门 (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) 中所述创建并配置了通知中心。

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>修改 iOS 项目

现在，将应用后端修改为只发送通知的 ID，必须更改 iOS 应用来处理该通知并回调后端以检索要显示的安全消息  。

若要实现此目标，我们必须编写逻辑来从应用后端检索安全内容。

1. 在 `AppDelegate.m` 中，请确保该应用将注册无提示通知，以便它可以处理从后端发送的通知 ID。 在 didFinishLaunchingWithOptions 中添加 `UIRemoteNotificationTypeNewsstandContentAvailability` 选项：

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. 在 `AppDelegate.m` 中，通过以下声明在顶部添加实现部分：

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. 然后将以下代码添加到实现部分，将占位符 `{back-end endpoint}` 替换为先前获取的后端终结点：

    ```objc
    NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    {
        // check if authenticated
        ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
        NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                    sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                    delegate:nil
                                    delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                completion([json objectForKey:@"Payload"], nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }
    ```

    此方法使用存储在共享首选项中的凭据调用应用后端来检索通知内容。

4. 现在我们必须处理传入通知，并使用上面的方法来检索要显示的内容。 首先，我们必须使 iOS 应用能在接收推送通知时在后台运行。 在 **XCode** 中，在左侧面板上选择应用项目，并单击中央窗格的“目标”  部分中的主应用目标。
5. 然后，单击中央窗格顶部的“功能”  选项卡，并选中“远程通知”  复选框。

    ![][IOS1]

6. 在 `AppDelegate.m` 中，添加以下方法来处理推送通知：

    ```objc
    -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
        NSLog(@"%@", userInfo);

        [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
            if (!error) {
                // show local notification
                UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                localNotification.alertBody = payload;
                localNotification.timeZone = [NSTimeZone defaultTimeZone];
                [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                completionHandler(UIBackgroundFetchResultNewData);
            } else {
                completionHandler(UIBackgroundFetchResultFailed);
            }
        }];

    }
    ```

    请注意，最好由后端处理缺失身份验证标头属性或拒绝的情况。 这些情况下的特定处理主要取决于目标用户的体验。 一种选择是显示包含用户用来进行身份验证的通用提示的通知，从而检索实际通知。

## <a name="run-the-application"></a>运行应用程序

若要运行应用程序，请执行以下操作：

1. 在 XCode 中，在物理 iOS 设备上运行此应用（推送通知将无法在模拟器中正常工作）。
2. 在 iOS 应用 UI 中，输入用户名和密码。 这些信息可以是任意字符串，但必须是相同的值。
3. 在 iOS 应用 UI 中，单击“登录”  。 然后单击“发送推送”  。 应该能看到通知中心中所显示的安全通知。

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
