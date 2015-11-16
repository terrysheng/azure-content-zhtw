<properties 
	pageTitle="Azure 隨選媒體編碼器的概觀和比較" 
	description="本主題概要說明並提供 Azire 隨選媒體編碼器的比較。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/29/2015"  
	ms.author="juliako"/>

#Azure 隨選媒體編碼器的概觀和比較

##編碼概觀

Azure 媒體服務提供多個用於將雲端中之媒體編碼的選項。

開始使用媒體服務時，請務必了解轉碼器和檔案格式之間的差異。轉碼器是實作壓縮/解壓縮演算法的軟體，而檔案格式是保存已壓縮視訊的容器。

媒體服務提供動態封裝，這讓您以媒體服務支援的串流格式 (MPEG DASH, 、HLS、Smooth Streaming、HDS) 提供調適性位元速率 MP4 或 Smooth Streaming 編碼內容，而不必重新封裝成這些串流格式。

若要利用[動態封裝](media-services-dynamic-packaging-overview.md)，您需要執行下列動作：

- 將您的夾層 (來源) 檔編碼為一組調適性位元速率 MP4 檔案或調適性位元速率 Smooth Streaming 檔案 (編碼步驟稍後示範於本教學課程中)。
- 為您計畫從該處傳遞內容的串流端點取得至少一個隨選串流單元。如需詳細資訊，請參閱[如何調整隨選串流保留單元](media-services-manage-origins.md#scale_streaming_endpoints/)。

媒體服務支援本文中所描述的下列隨選編碼器：

- **Media Encoder Standard**
- **Azure 媒體編碼器** 
- **Media Encoder Premium 工作流程**

本文概略敘述隨選媒體編碼器，並提供文章連結以提供更詳細資訊。本主題也提供各種編碼器的比較。

請注意，每個媒體服務帳戶預設一次可以有一個進行中的編碼工作。您可以保留編碼單位，這樣就可以同時執行多個編碼工作，其中一個用於您購買的每一個編碼保留單位。如需相關資訊，請參閱[調整編碼單位](media-services-portal-encoding-units.md)。

##Media Encoder Standard

###概觀

建議使用 Media Encoder Standard 編碼器。不過，此編碼器目前沒有透過 Azure 入口網站公開。

相較於 Azure 媒體編碼器，此編碼器支援更多的輸入和輸出格式和轉碼器。其他優點包括：

- 容許更多建立輸入檔案的方式
- 具有比 Azure 媒體編碼器更好的 H.264 轉碼器品質
- 建置在較新且更具彈性的管線
- 更穩固/具彈性

###使用方式

[如何使用 Media Encoder Standard 進行編碼](media-services-dotnet-encode-with-media-encoder-standard.md)

###格式

[格式和轉碼器](media-services-media-encoder-standard-formats.md)

###預設值

Media Encoder Standard 使用[這裡](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)描述的其中一個編碼器預設值進行設定。

###輸入和輸出中繼資料

[這裡](http://msdn.microsoft.com/library/azure/dn783120.aspx)說明編碼器輸入中繼資料。

[這裡](http://msdn.microsoft.com/library/azure/dn783217.aspx)說明編碼器輸出中繼資料。

###縮圖

如需如何產生縮圖的相關資訊，請參閱[如何使用媒體編碼器標準產生縮圖](media-services-dotnet-generate-thumbnail-with-mes.md)。

###音訊和/或視訊重疊

目前不支援。

###另請參閱

[媒體服務部落格](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)
 
##Azure 媒體編碼器

###概觀

Azure 媒體編碼器是媒體服務支援的其中一個編碼器。從 2015 年 7 月開始，建議使用 [Media Encoder Standard](media-services-encode-asset.md#media_encoder_standard)。

###使用方式

[如何使用 Azure 媒體編碼器進行編碼](media-services-dotnet-encode-asset.md)

###格式

[格式和轉碼器](media-services-azure-media-encoder-formats.md)

###預設值

Azure 媒體編碼器使用[這裡](https://msdn.microsoft.com/library/azure/dn619392.aspx)描述的其中一種編碼器預設值進行設定。您也可以從[這裡](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder)取得實際的 Azure 媒體編碼器預設檔案。

###輸入和輸出中繼資料

[這裡](http://msdn.microsoft.com/library/azure/dn783120.aspx)說明編碼器輸入中繼資料。

[這裡](http://msdn.microsoft.com/library/azure/dn783217.aspx)說明編碼器輸出中繼資料。

###縮圖

[建立縮圖](https://msdn.microsoft.com/library/hh973624.aspx)

###音訊和/或視訊重疊

[建立重疊](media-services-azure-media-customize-ame-presets.md#creating-overlays)。

###命名慣例

[如何修改輸出檔名稱](media-services-azure-media-customize-ame-presets.md#controlling-azure-media-encoder-output-file-names)

###另請參閱

[使用 Dolby Digital Plus 將您的媒體編碼](media-services-encode-with-dolby-digital-plus.md)

##Media Encoder Premium Workflow

###概觀

[介紹 Azure 媒體服務中的 Premium 編碼](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

###使用方式

Media Encoder Premium Workflow 使用複雜的工作流程設定。您可以使用[工作流程設計工具](media-services-workflow-designer.md)建立和更新工作流程檔案。

[如何使用 Azure 媒體服務中的 Premium 編碼](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

##<a id="compare_encoders"></a>比較編碼器

###<a id="billing"></a>每個編碼器所使用的計費計量表

媒體處理器名稱|適用的價格|注意事項
---|---|---
**Media Encoder Standard** |ENCODER|在 ENCODER 資料行之下，編碼工作會以[這裡][1]指定的費率，根據輸出資產的大小 (以 GB 為單位) 收費。
**Azure 媒體編碼器** |ENCODER|在 ENCODER 資料行之下，編碼工作會以[這裡][1]指定的費率，根據輸出資產的大小 (以 GB 為單位) 收費。
**Media Encoder Premium 工作流程** |PREMIUM ENCODER|在 PREMIUM ENCODER 資料行之下，編碼工作會以[這裡][1]指定的費率，根據輸出資產的大小 (以 GB 為單位) 收費。


本節比較 **媒體編碼器標準**、**Azure 媒體編碼器**以及**媒體編碼器高階工作流程**的編碼功能。


###輸入容器/檔案格式

輸入容器/檔案格式|Media Encoder Standard|Azure 媒體編碼器|Media Encoder Premium Workflow
---|---|---|---
Adobe® Flash® F4V |是|否 |是
MXF/SMPTE 377M |是|限制|是
GXF |是|否 |是
MPEG-2 傳輸資料流 |是|是 |是
MPEG-2 程式資料流 |是|是 |是
MPEG-4/MP4 |是|是 |是
Windows Media/ASF |是|是 |是
AVI (未壓縮 8 位元/10 位元)|是|是 |是
3GPP/3GPP2 |是|是 |否
Smooth Streaming 檔案格式 (PIFF 1.3)|是|是|否
[Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|是|否|否
Matroska/WebM |是|否|否
QuickTime (.mov) |是|否|否

###輸入視訊轉碼器

輸入視訊轉碼器|Media Encoder Standard|Azure 媒體編碼器|Media Encoder Premium Workflow
---|---|---|---
AVC 8 位元/10 位元，高達 4:2:2，包括 AVCIntra |8 位元 4:2:0 和 4:2:2|唯一的 8 位元 4:2:0|是
Avid DNxHD (使用 MXF) |是|否|是
DVCPro/DVCProHD (使用 MXF) |是|否|是
JPEG2000 |是|否|是
MPEG-2 (高達 422 Profile 和 High Level，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs ® 和 D10 等變種)|最高 422 設定檔|最高 422 設定檔|是
MPEG-1 |是|是|是
Windows Media 視訊/VC-1 |是|是|是
Canopus HQ/HQX |否|是|否
Mpeg-4 第 2 部分 |是|否|否
[Theora](https://en.wikipedia.org/wiki/Theora) |是|否|否
Apple ProRes 422 |是|否|否
Apple ProRes 422 LT |是|否|否
Apple ProRes 422 HQ |是|否|否
Apple ProRes Proxy|是|否|否
Apple ProRes 4444 |是|否|否
Apple ProRes 4444 XQ |是|否|否

###輸入音訊轉碼器

輸入音訊轉碼器|Media Encoder Standard|Azure 媒體編碼器|Media Encoder Premium Workflow
---|---|---|---
AES (SMPTE 331M 和 302M，AES3-2003) |否|否|是
Dolby® E |否|否|是
Dolby® Digital (AC3) |否|是|是
Dolby® Digital Plus (E-AC3) |否|否|是
AAC (AAC-LC、AAC-HE 和 AAC-HEv2；高達 5.1)|是|是|是
MPEG Layer 2|是|是|是
MP3 (MPEG-1 音訊層 3)|是|是|是
Windows Media 音訊|是|是|是
WAV/PCM|是|是|是
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|是|否|否
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |是|否|否
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|是|否|否


###輸出容器/檔案格式

輸出容器/檔案格式|Media Encoder Standard|Azure 媒體編碼器|Media Encoder Premium Workflow
---|---|---|---
Adobe® Flash® F4V|否|否|是
MXF (OP1a、XDCAM 和 AS02)|否|否|是
DPP (包括 AS11)|否|否|是
GXF|否|否|是
MPEG-4/MP4|是|是|是
MPEG-TS|是|否|是
Windows Media/ASF|否|是|是
AVI (未壓縮 8 位元/10 位元)|否|否|是
Smooth Streaming 檔案格式 (PIFF 1.3)|否|是|是

###輸出視訊轉碼器

輸出視訊轉碼器|Media Encoder Standard|Azure 媒體編碼器|Media Encoder Premium Workflow
---|---|---|---
AVC (H.264；8 位元；高達 High Profile、Level 5.2；4K Ultra HD；AVC Intra)|僅限 8 位元 4:2:0|僅限高達 1080p 的 8 位元 4:2:0|是
Avid DNxHD (使用 MXF)|否|否|是
DVCPro/DVCProHD (使用 MXF)|否|否|是
MPEG-2 (高達 422 Profile 和 High Level，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs ® 和 D10 等變種)|否|否|是
MPEG-1|否|否|是
Windows Media 視訊/VC-1|否|是|是
JPEG 縮圖建立|否|是|是

###輸出音訊轉碼器

輸出音訊轉碼器|Media Encoder Standard|Azure 媒體編碼器|Media Encoder Premium Workflow
---|---|---|---
AES (SMPTE 331M 和 302M，AES3-2003)|否|否|是
Dolby® Digital (AC3)|否|是|是
Dolby® Digital Plus (E-AC3) 高達 7.1|否|最高 5.1|是
AAC (AAC-LC、AAC-HE 和 AAC-HEv2；高達 5.1)|是|是|是
MPEG Layer 2|否|否|是
MP3 (MPEG-1 音訊層 3)|否|否|是
Windows Media 音訊|否|是|是


##媒體服務學習路徑

您可以在此檢視 AMS 學習路徑：

- [AMS 即時資料流工作流程](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS 隨選資料流工作流程](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

##相關文章

- [配額和限制](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=Nov15_HO2-->