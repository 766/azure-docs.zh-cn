---
title: 用于调用 web Api 的移动应用程序 (代码配置)-Microsoft 标识平台 |Microsoft
description: 了解如何构建调用 Web API 的移动应用（应用的代码配置）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bdf9210eb88b2057cf861b208f19d3e6f562e9a
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414845"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>用于调用 web Api-代码配置的移动应用

创建应用程序后, 你将了解如何从应用程序注册时获取的应用程序参数配置代码。 移动应用程序还具有一些复杂的细节, 这与在构建这些应用时使用的框架有关

## <a name="msal-libraries-supporting-mobile-apps"></a>支持移动应用的 MSAL 库

支持移动应用的 Microsoft 库包括:

  MSAL 库 | 描述
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 开发可移植应用程序。 用于构建移动应用程序的 MSAL.NET 支持平台为 UWP、Xamarin 和 Xamarin。
  ![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | 开发具有目标 C 或 Swift 的本机 iOS 应用程序
  ![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | 在 Java 中开发适用于 Android 的本机 Android 应用程序

## <a name="configuring-the-application"></a>配置应用程序

移动应用程序使用 MSAL 的`PublicClientApplication`类。 下面介绍如何对其进行实例化:

### <a name="android"></a>Android

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

```swift
// Initialize the app.
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
}
```

### <a name="xamarin-or-uwp"></a>Xamarin 或 UWP

下一段说明如何为 Xamarin、Xamarin 和 UWP 应用配置应用程序代码。 第一步是实例化应用程序。 可选步骤是配置代理。

#### <a name="instantiating-the-application"></a>实例化应用程序

在 Xamarin 或 UWP 中, 实例化应用程序的最简单方法如下, 其中是你`ClientId`的应用程序的 Guid (注册为)。

```CSharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

还有其他一些*参数*方法用于设置 UI 父代, 替代默认的颁发机构, 指定客户端名称和版本 (对于遥测), 指定 "重定向 URI", 指定要使用的 Http 工厂 (例如, 要处理代理, 请指定遥测和日志记录)。 这是以下段落的主题。

##### <a name="specifying-the-parent-uiwindowactivity"></a>指定父 UI/窗口/活动

在 Android 上, 你需要在进行交互式身份验证之前传递父活动。 在 iOS 上, 使用 broker 时, 需要传递 ViewController 中的。 对于 UWP, 你可能想要传入父窗口。 这在获取令牌时是可能的, 但也可以在应用创建时指定返回 UIParent 的委托。

```CSharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

在 Android 上, 我们建议你`CurrentActivityPlugin`这样[做。](https://github.com/jamesmontemagno/CurrentActivityPlugin)  然后, `PublicClientApplication`生成器代码将如下所示:

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>更多应用生成参数

- 如需 `PublicClientApplicationBuilder` 上提供的所有修饰符的列表，请参阅参考文档 [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- 如需 `PublicClientApplicationOptions` 中公开的所有选项的说明，请参阅参考文档中的 [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)

#### <a name="xamarin-ios-specific-considerations"></a>Xamarin 特定于 iOS 的注意事项

在 Xamarin iOS 上, 使用 MSAL.NET 时, 必须考虑几个注意事项:

1. [重写并实现 `AppDelegate` 中的 `OpenUrl` 函数](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [启用密钥链组](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [启用令牌缓存共享](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [启用密钥链访问](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

[Xamarin iOS 注意事项](msal-net-xamarin-ios-considerations.md)中提供了详细信息

#### <a name="other-xamarin-android-specific-considerations"></a>其他 Xamarin Android 特有注意事项

下面是 Xamarin Android 具体内容:

- [当身份验证流的交互式部分结束后, 确保控件返回到 MSAL](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [更新 Android 清单](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [使用嵌入的 web 视图 (可选)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [故障排除](msal-net-xamarin-android-considerations.md#troubleshooting)

[Xamarin Android 注意事项](msal-net-xamarin-android-considerations.md)中提供了详细信息

最后, 在 Android 上了解有关浏览器的一些 specificities。 MSAL.NET 中的[Xamarin 特定于 Android 的注意事项](msal-net-system-browser-android-considerations.md)对它们进行了介绍。

#### <a name="uwp-specific-considerations"></a>UWP 特定注意事项

在 UWP 上, 你可以使用公司网络。 有关 UWP 详细信息的信息, 请参阅[MSAL.NET 的通用 Windows 平台相关注意事项](msal-net-uwp-considerations.md)。

## <a name="configuring-the-application-to-use-the-broker"></a>将应用程序配置为使用 broker

### <a name="why-use-brokers-on-xamarinios-and-xamarinandroid-applications"></a>为什么使用适用于 Xamarin 和 Xamarin Android 应用程序的代理？

在 Android 和 iOS 上, 代理启用:

- 单一登录 (SSO)。 用户无需登录到每个应用程序。
- 设备标识。 通过访问设备上已加入工作区的设备证书, 启用与 Azure AD 设备相关的条件性访问策略。
- 应用程序标识验证。 当应用程序调用代理时, 它会传递其重定向 url, 并且 broker 将对其进行验证。

### <a name="enable-the-brokers-on-xamarin"></a>在 Xamarin 上启用代理

若要启用这些功能之一, 请在`WithBroker()` `PublicClientApplicationBuilder.CreateApplication`调用方法时使用参数。 `.WithBroker()`默认情况下, 设置为 true。 对于[iOS](#brokered-authentication-for-xamarinios), 请遵循以下步骤。

### <a name="brokered-authentication-for-xamarinios"></a>针对 Xamarin 的中转身份验证

请按照以下步骤操作, 使你的 Xamarin iOS 应用与[Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)应用进行交流。

#### <a name="step-1-enable-broker-support"></a>步骤 1：启用代理支持

代理支持以每个`PublicClientApplication`原则启用。 此项默认禁用。 通过`WithBroker()` 创建时`PublicClientApplication` , 必须使用参数 (默认设置为 "true")。 `PublicClientApplicationBuilder`

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>步骤 2：更新 AppDelegate 以处理回调

当 MSAL.NET 调用代理时, 代理将转而通过`AppDelegate.OpenUrl`方法回调到你的应用程序。 由于 MSAL 将等待来自代理的响应, 因此应用程序需要合作才能调用 MSAL.NET。 为此, 请更新`AppDelegate.cs`文件以重写以下方法。

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

每次启动应用程序时都将调用此方法, 并将其用作处理来自代理的响应并完成 MSAL.NET 启动的身份验证过程的机会。

#### <a name="step-3-set-a-uiviewcontroller"></a>步骤 3：设置 UIViewController ()

使用 Xamarin iOS 时, 通常不需要设置对象窗口, 但在这种情况下, 需要从代理发送和接收响应。 仍在`AppDelegate.cs`中, 设置 ViewController。

执行以下操作来设置对象窗口:

1) 在`AppDelegate.cs`中, `App.RootViewController`将设置为新`UIViewController()`的。 这将确保有`UIViewController`与代理的调用的。 如果未正确设置, 则可能会收到此错误:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) 在 AcquireTokenInteractive 调用中, 使用`.WithParentActivityOrWindow(App.RootViewController)`并将引用传递给你将使用的对象窗口。

例如：

在 `App.cs` 中：
```CSharp
   public static object RootViewController { get; set; }
```
在 `AppDelegate.cs` 中：
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
在获取令牌调用中:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>步骤 4：注册 URL 方案

MSAL.NET 使用 Url 调用 broker, 然后将 broker 响应返回到应用。 若要完成往返过程, 需要在`Info.plist`文件中注册应用程序的 URL 方案。

`CFBundleURLSchemes` 用`msauth`作为前缀。 然后, `CFBundleURLName`将添加到末尾。

`$"msauth.(BundleId)"`

**例如:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> 此 URL 方案将成为 RedirectUri 的一部分, 用于在接收来自代理的响应时唯一标识应用。

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

#### <a name="step-5-lsapplicationqueriesschemes"></a>步骤 5：LSApplicationQueriesSchemes

MSAL 使用`–canOpenURL:`检查是否在设备上安装了 broker。 在 iOS 9 中, Apple 锁定了应用程序可以查询的方案。

**添加**到文件的`LSApplicationQueriesSchemes`部分。 `Info.plist` **`msauthv2`**

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>适用于 Xamarin 的中转身份验证

MSAL.NET 尚不支持适用于 Android 的代理。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获取令牌](scenario-mobile-acquire-token.md)
