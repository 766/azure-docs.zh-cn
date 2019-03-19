---
title: Azure 媒体服务动态打包概述 | Microsoft 文档
description: 本主题提供媒体服务中的动态打包概述。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: juliako
ms.openlocfilehash: d9c59bdb2e8a7b115761554f70ebedeecaf5d04e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901710"
---
# <a name="dynamic-packaging"></a>动态打包

Microsoft Azure 媒体服务可用于向多种客户端技术（例如，iOS 和 XBOX）传送多种媒体源文件格式、媒体流格式和内容保护格式。 这些客户端可识别不同的协议，例如，iOS 需要 HTTP Live Streaming (HLS) 格式，Xbox 需要平滑流式处理。 如果有一组自适应比特率 （多码率） MP4 （ISO 基媒体 14496-12） 文件或一组自适应比特率平滑流式处理文件要提供给了解 HLS、 MPEG DASH 或平滑流式处理的客户端，您可以充分利用动态打包。 支持 SD/HD/UHD-4k、 打包是不可知的视频分辨率。

[流式处理终结点](streaming-endpoint-concept.md)是用于向客户端播放器提供媒体内容的媒体服务中的动态打包服务。 动态打包是一项功能提供对所有的标准**流式处理终结点**（标准或高级）。 没有无需额外成本与媒体服务 v3 中的此功能相关联。 

若要充分利用**动态打包**，你需要准备**资产**具有一组自适应比特率 MP4 文件和所需的 Media Services 动态打包的流式处理配置文件。 若要获取的文件的一种方法是使用媒体服务将夹层 （源） 文件编码。 若要使视频编码资产中播放的客户端，您必须创建**流式处理定位符**和生成流 Url。 然后，根据流式处理客户端清单 （HLS、 DASH 或平滑流） 中指定的格式，具有选定的协议接收流。

因此，只需以单一存储格式存储文件并为其付费，然后媒体服务服务就会基于客户端的请求构建并提供相应响应。 

在媒体服务动态打包使用是否进行实时或按需流式处理。 下图显示了按需流式处理与动态打包工作流。

![动态打包](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

## <a name="common-video-on-demand-workflow"></a>常见视频按需工作流

以下是常见的 Media Services 流式处理工作流使用动态打包的位置。

1. 上传一个输入文件（称为夹层文件）。 例如，H.264、MP4 或 WMV（有关受支持格式的列表，请参阅[Media Encoder Standard 支持的格式](media-encoder-standard-formats.md)）。
2. 将夹层文件编码为 H.264 MP4 自适应比特率集。
3. 发布包含自适应比特率 MP4 集的资产。 通过创建发布**流式处理定位符**。
4. 创建面向不同的格式 （HLS、 Dash 和平滑流式处理） 的 Url。 **流式处理终结点**将负责为正确的清单并对所有这些不同格式的请求提供服务。

## <a name="encode-to-adaptive-bitrate-mp4s"></a>编码为自适应比特率 mp4

璝惠[如何使用媒体服务的视频进行编码](encoding-concept.md)，请参阅下面的示例：

* [从 HTTPS URL 使用内置的预设编码](job-input-from-http-how-to.md)
* [对本地文件使用内置的预设进行编码](job-input-from-local-file-how-to.md)
* [生成自定义预设，以满足特定的方案或设备要求](customize-encoder-presets-how-to.md)

Media Encoder Standard 格式和编解码器的列表，请参阅[格式和编解码器](media-encoder-standard-formats.md)

## <a name="delivery-protocols"></a>传递协议

|协议|示例|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|平滑流| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="video-codecs-supported-by-dynamic-packaging"></a>支持的动态打包的视频编解码器

动态打包支持 MP4 文件，其中包含使用编码的视频[H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) （mpeg-4 AVC 或 AVC1） [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC，hev1 或 hvc1)。

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>动态打包支持的音频编解码器

动态打包支持 MP4 文件，其中包含使用编码的音频[AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC、 HE-AAC v1、 HE-AAC v2)， [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) （高级 ac-3 或 e-ac3） 或[DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS速成版，DTS LBR、 DTS HD、 DTS HD 无损）。

> [!NOTE]
> 动态打包不支持包含 [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) 音频（它是旧编解码器）的文件。

## <a name="manifests"></a>清单 
 
媒体服务支持 HLS、 MPEG DASH、 平滑流式处理协议。 作为的一部分**动态打包**，流式处理客户端清单 （HLS 主播放列表、 短划线媒体演示描述 (MPD)，和平滑流式处理） 动态生成基于在 URL 中的格式选择器。 请参阅中的交付协议[本节](#delivery-protocols)。 

清单文件包含流元数据，例如： 轨迹类型 （音频、 视频或文本）、 轨迹名称、 开始和结束时间、 比特率 （质量）、 轨迹语言、 演播窗口 （持续时间固定的滑动窗口） 和视频编解码器 (FourCC)。 此文件还会通过提供有关下一个可播放视频片段及其位置的信息，来指示播放器检索下一个片段。 片段（或段）实际上是视频内容的“区块”。

### <a name="hls-master-playlist"></a>HLS 主播放列表

下面是 HLS 清单文件的示例： 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="dash-media-presentation-description-mpd"></a>短划线媒体演示描述 (MPD)

下面是短划线清单的一个示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
### <a name="smooth-streaming"></a>平滑流

下面是平滑流式处理清单的一个示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```
## <a name="next-steps"></a>后续步骤

[上传、编码、流式处理视频](stream-files-tutorial-with-api.md)
