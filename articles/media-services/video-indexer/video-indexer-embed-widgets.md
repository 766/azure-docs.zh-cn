---
title: 将视频索引器小组件嵌入应用程序
titlesuffix: Azure Media Services
description: 了解如何将视频索引器小组件嵌入应用程序。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: ec3c7379c8c7f28765fbc4396d3e9804a6c127f6
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663750"
---
# <a name="embed-video-indexer-widgets-into-your-applications"></a>将视频索引器小组件嵌入应用程序

本文介绍如何将视频索引器小组件嵌入应用程序。 视频索引器支持在应用程序中嵌入三种类型的小组件:**认知见解**、**播放器**和**编辑器**。 

从版本2开始, 小组件基 URL 包含指定帐户的区域。 例如，美国西部区域中的帐户将生成：`https://wus2.videoindexer.ai/embed/insights/...`。

## <a name="widget-types"></a>小组件类型

### <a name="cognitive-insights-widget"></a>认知见解小组件

**认知见解**小组件包括从视频索引过程中提取的所有视觉见解。 见解小组件支持以下可选的 URL 参数：

|名称|定义|描述|
|---|---|---|
|`widgets`|用逗号分隔的字符串|用于控制要呈现的见解。 <br/>示例：`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` 只呈现人物和品牌 UI 的见解<br/>可用选项：people、keywords、annotations、brands、sentiments、transcript、search。<br/>使用版本 2 时不支持在 URL 中提供<br/><br/>**注意：** 版本2中不支持小组件 URL 参数。 |
|`locale`|短语言代码|控制 insights 语言。 默认为 `en`。 例如：`language=de`。|
|`tab`|默认选定的选项卡|控制默认呈现的 "见解" 选项卡。 `tab=timeline`选择 "时间线" 选项卡以呈现见解。|

### <a name="player-widget"></a>播放器小组件

**播放器**小组件用于通过自适应比特率来流式传输视频。 播放器小组件支持以下可选的 URL 参数：

|名称|定义|描述|
|---|---|---|
|`t`|开始时间 (秒)|让播放器从给定时间点开始播放。<br/>示例：`t=60`。|
|`captions`|语言代码|在小组件加载过程中提取给定语言的字幕，使之在字幕菜单中可用。<br/>示例：`captions=en-US`。|
|`showCaptions`|布尔值|使播放器与已启用的字幕一起加载。<br/>示例：`showCaptions=true`。|
|`type`||激活音频播放器外观（视频部件已删除）。<br/>示例：`type=audio`。|
|`autoplay`|布尔值|确定播放器是否会在加载后开始播放视频（默认值为 true）。<br/>示例：`autoplay=false`。|
|`language`|语言代码|控制播放器语言（默认值为 en-US）<br/>示例：`language=de-DE`。|

### <a name="editor-widget"></a>编辑器小组件 

利用**编辑器**小组件, 您可以创建新项目并管理视频见解。

|名称|定义|描述|
|---|---|---|
|`accessToken`<sup>*</sup>|String|使用`accessToken`编辑器小组件时, 需要使用参数。<br/>访问令牌提供仅用于嵌入小组件的帐户的访问权限。 |
|`language`|语言代码|控制播放器语言（默认值为 en-US）<br/>示例：`language=de-DE`。|
|`locale`|短语言代码|控制 insights 语言。 默认为 `en`。 例如：`language=de`。|

<sup>*</sup>所有者应小心提供`accessToken` 。 

## <a name="embedding-public-content"></a>嵌入公共内容

1. 浏览到[视频索引器](https://www.videoindexer.ai/)网站并登录。
2. 单击要使用的视频。
3. 单击显示在视频下面的“嵌入”按钮。

    ![小组件](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    单击按钮以后，一个嵌入模式会显示在屏幕上，你可以在其中选择要嵌入到应用程序中的具体小组件。
    选择小组件 (**认知见解**、**播放器**或**编辑器**) 会生成嵌入的代码, 以便在应用程序中进行粘贴。
 
4. 选择所需的小组件类型 (**认知见解**、**播放器**或**编辑**)。
5. 复制嵌入代码并将其添加到应用程序。 

    ![小组件](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> 如果在共享视频 Url 时遇到问题, 请尝试将 "location" 参数添加到该链接。 应将参数设置为[视频索引器所在的 Azure 区域](regions.md)。 例如， `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial` 。

## <a name="embedding-private-content"></a>嵌入专用内容

可以从嵌入弹出窗口（如上一部分所示）获取嵌入代码，但仅限**公用**视频。 

若要嵌入**专用**视频，需在 **iframe** 的 **src** 属性中传入访问令牌：

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
使用[**获取见解小组件**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) API 获取认知见解小组件内容，或者使用[**获取视频访问令牌**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?)并将其作为查询参数添加到 URL，如上所示。 将此 URL 指定为 **iframe** 的 **src** 值。

若要在嵌入式小组件中提供编辑见解功能（就像我们在 Web 应用程序中拥有的功能一样），需传递具有编辑权限的访问令牌。 请使用[**获取见解小组件**](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)或[**获取视频访问令牌**](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?)并设置 **&allowEdit=true**。 

## <a name="widgets-interaction"></a>小组件交互

**认知见解**小组件可以与应用程序的视频交互。 本部分说明如何实现此交互。

![小组件](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>跨域通信

为了让视频索引器小组件与其他组件通信，视频索引器服务会执行以下操作：

- 使用跨域通信 HTML5 方法 **postMessage**； 
- 跨 VideoIndexer.ai 域验证消息。 

如果选择实施你自己的播放器代码并完成与**认知见解**小组件的集成，则你有责任验证来自 VideoIndexer.ai 的消息的域。

### <a name="embed-widgets-in-your-application--blog-recommended"></a>在应用程序中嵌入小组件/博客 (推荐) 

本部分介绍如何在两个视频索引器小组件之间实现交互，这样当某个用户单击应用程序中的见解控件时，播放器就会跳到相关的时刻。

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

1. 复制**播放器**小组件嵌入代码。
2. 复制**认知见解**嵌入代码。
3. 添加[**转存进程文件**](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js)，以便处理两个小组件之间的通信：

`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

现在，当某个用户单击应用程序中的见解控件时，播放器就会跳到相关的时刻。

有关详细信息，请参阅[此演示](https://codepen.io/videoindexer/pen/NzJeOb)。

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>嵌入认知见解小组件并使用 Azure Media Player 来播放内容

本部分介绍如何使用 [AMP 插件](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)来实现**认知见解**小组件与 Azure Media Player 实例之间的交互。
 
1. 为 AMP 播放器添加视频索引器插件。<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. 使用视频索引器插件实例化 Azure Media Player。

        // Init Source
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // Here is how to load vtt from VI, you can replace it with your vtt url.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plugin
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically
            initSource.call(this);
        });

3. 复制**认知见解**嵌入代码。

现在应该可以与 Azure Media Player 通信了。

有关详细信息，请参阅[此演示](https://codepen.io/videoindexer/pen/rYONrO)。

### <a name="embed-video-indexer-cognitive-insights-widget-and-use-your-own-player-could-be-any-player"></a>嵌入视频索引器认知见解小组件并使用你自己的播放器（可以是任何播放器）

如果使用自己的播放器，则需自行负责播放器的操作以实现通信。 

1. 插入视频播放器。

    例如，标准 HTML5 播放器

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. 嵌入认知见解小组件。
3. 通过侦听“消息”事件实现播放器的通信。 例如：

        <script>
    
            (function(){
            // Reference your player instance
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Here you need to call your player "jumpTo" implementation
                playerInstance.currentTime = evt.data.time;
               
                // Confirm arrival to us
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to message event
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

有关详细信息，请参阅[此演示](https://codepen.io/videoindexer/pen/YEyPLd)。

## <a name="adding-subtitles"></a>添加字幕

如果使用自己的[Azure Media Player](https://aka.ms/azuremediaplayer)嵌入视频索引器见解, 则可以使用**GetVttUrl**方法来获取隐藏式字幕 (副标题)。 也可从视频索引器 AMP 插件 **getSubtitlesUrl**（如前所示）调用 javascript 方法。 

## <a name="customizing-embeddable-widgets"></a>自定义可嵌入式小组件

### <a name="cognitive-insights-widget"></a>认知见解小组件

可以选择所需见解的类型，方法是将其指定为以下 URL 参数的值，此参数已添加到从 API 或 Web 应用程序获取的嵌入代码：`&widgets=<list of wanted widgets>`。

可用值：people、keywords、sentiments、transcript、search。

例如，如果需要嵌入的小组件仅包含人物和搜索见解，则 iframe 嵌入 URL 将如下所示：

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

iframe 窗口的标题也可自定义，只需为 iframe URL 提供 `&title=<YourTitle>` 即可。 （它会自定义 html \<title> 值）。
    
例如，如果需要为 iframe 窗口提供标题“MyInsights”，则 URL 将如下所示：

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

请注意，仅当你需要在新窗口中打开见解时，此选项才适用。

### <a name="player-widget"></a>播放器小组件

如果嵌入视频索引器播放器，则可通过指定 iframe 的大小来选择播放器的大小。

例如：

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

默认情况下，视频索引器播放器会根据从视频提取的视频脚本以及在视频上传时选定的源语言来自动生成隐藏式字幕。

若要在嵌入时使用其他语言，则可向嵌入播放器 URL 添加 `&captions=< Language | ”all” | “false” >`；或者，若要使用所有可用的语言字幕，则可使用“all”作为值。
如果需要默认显示字幕，则可传递 `&showCaptions=true`。

然后，嵌入 URL 将如下所示： 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

若要禁用字幕，可传递“false”作为 captions 参数的值。

自动播放 - 播放器会按照默认设置开始播放视频。 可以选择不自动播放，只需向上述嵌入 URL 传递 &autoplay=false 即可。

## <a name="next-steps"></a>后续步骤

若要了解如何查看和编辑视频索引器见解，请参阅[此](video-indexer-view-edit.md)文。

此外, 请查看[视频索引器 CodePen](https://codepen.io/videoindexer/pen/eGxebZ)。
