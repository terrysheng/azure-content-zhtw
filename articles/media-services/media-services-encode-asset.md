<properties 
	pageTitle="透過 Azure Media Services 編碼的隨選內容" 
	description="本主題簡介如何使用 Media Services 編碼隨選內容。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015"  
	ms.author="juliako"/>

#透過 Azure Media Services 編碼的隨選內容

本主題是[媒體服務隨選視訊工作流程](media-services-video-on-demand-workflow.md)的一部分。

##概觀

媒體服務支援下列編碼器：

- [Media Encoder Standard](#media_encoder_standard)
- [Azure Media Encoder](#azure_media_encoder)
- [Media Encoder Premium Workflow](#media_encoder_premium_workflow)

[下列章節](#compare_encoders)會比較受支援編碼器的編碼功能。

依預設，每個媒體服務帳戶一次可以有一個進行中的編碼工作。您可以保留編碼單位，這樣就可以同時執行多個編碼工作，其中一個用於您購買的每一個編碼保留單位。如需瞭解如何調整編碼單位，請參閱下列**入口網站**和 **.NET** 主題。

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

##<a id="media_encoder_standard"></a>Media Encoder Standard

[Media Encoder Standard 支援的格式](media-services-media-encoder-standard-formats.md) – 討論 **Media Encoder Standard** 支援的檔案和串流格式。

**Media Encoder Standard** 使用[這裡](http://go.microsoft.com/fwlink/?LinkId=618336)所述的其中一個編碼器預設或以[這些](http://go.microsoft.com/fwlink/?LinkId=618336)預設為基礎的自訂預設進行設定。

如需詳細資訊，請參閱[此部落格](http://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)。

##<a id="azure_media_encoder"></a>Azure Media Encoder

[媒體服務編碼器支援的格式](media-services-azure-media-encoder-formats.md) – 討論 **Azure Media Encoder** 支援的檔案和串流格式。

**Azure Media Encoder** 使用[這裡](https://msdn.microsoft.com/library/azure/dn619392.aspx)所述的其中一種編碼器預設字串進行設定。您也可以從[這裡](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/Azure%20Media%20Encoder)取得實際的 Azure Media Encoder 預設檔案。

###範例

使用 **Azure 管理入口網站**、**.NET** 或 **REST API**，以 **Azure Media Encoder** 進行編碼。
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

####其他相關主題

[動態封裝](https://msdn.microsoft.com/library/azure/jj889436.aspx) – 描述如何編碼為自適性位元速率 MP4，並動態提供 Smooth Streaming、Apple HLS 或 MPEG-DASH。

[控制媒體服務編碼器輸出檔案名稱](https://msdn.microsoft.com/library/azure/dn303341.aspx) – 描述 Azure Media Encoder 使用的檔案命名慣例以及如何修改輸出檔名稱。

[使用 Dolby Digital Plus 將您的媒體編碼](media-services-encode-with-dolby-digital-plus.md) – 描述如何使用 Dolby Digital Plus 編碼技術進行曲目的編碼。


##<a id="media_encoder_premium_wokrflow"></a>Media Encoder Premium Workflow 

**注意** 本主題中討論的 Media Encoder Premium Workflow 媒體編碼器不適用於中國。

[Media Encoder Premium Workflow 支援的格式](media-services-premium-workflow-encoder-formats.md) – 討論 **Media Encoder Premium Workflow** 支援的檔案格式和轉碼器。

### 工作流程設計工具

**Media Encoder Premium Workflow** 使用複雜的工作流程設定。您可以使用[工作流程設計工具](media-services-workflow-designer.md)建立工作流程檔案。

您可在[這裡](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)取得預設的工作流程檔案。資料夾也包含這些檔案的說明。

###範例
利用 **Media Encoder Premium Workflow** 並搭配 **.NET** 進行編碼。如需詳細資訊，請參閱[使用 Media Encoder Premium Workflow 進行進階編碼](media-services-encode-with-premium-workflow.md)。
 

##<a id="compare_encoders"></a>比較編碼器

###<a id="billing"></a>每個編碼器所使用的帳單計量表

媒體處理器名稱|適用的價格|注意事項
---|---|---
**Windows Azure Media Encoder** |LEGACY ENCODER|在 LEGACY ENCODER 資料行之下，編碼工作會以[這裡][1]指定的費率，根據輸入資產和輸出資產的總大小 (以 GB 為單位) 收費。
**Azure Media Encoder** |ENCODER|在 ENCODER 資料行之下，編碼工作會以[這裡][1]指定的費率，根據輸出資產的大小 (以 GB 為單位) 收費。
**Media Encoder Standard** |ENCODER|在 ENCODER 資料行之下，編碼工作會以[這裡][1]指定的費率，根據輸出資產的大小 (以 GB 為單位) 收費。
**Media Encoder Premium Workflow** |PREMIUM ENCODER|在 PREMIUM ENCODER 資料行之下，編碼工作會以[這裡][1]指定的費率，根據輸出資產的大小 (以 GB 為單位) 收費。



本節比較 **Azure 媒體編碼器**、**Media Encoder Premium Workflow** 和 **Media Encoder Standard** 的編碼功能。


###輸入格式

輸入容器/檔案格式

輸入容器/檔案格式|Media Encoder Premium Workflow|Azure Media Encoder|Media Encoder Standard
---|---|---|---
Adobe® Flash® F4V|是|否|是
MXF/SMPTE 377M|是|限制|是
GXF|是|否|是
MPEG-2 傳輸資料流|是|是|是
MPEG-2 程式資料流|是|是|是
MPEG-4/MP4|是|是|是
Windows Media/ASF|是|是|是
AVI (未壓縮 8 位元/10 位元)|是|是|是
3GPP/3GPP2|否|是|是
Smooth Streaming 檔案格式 (PIFF 1.3)|否|是|是
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|否|否|是
Matroska/WebM|否|否|是

輸入視訊轉碼器

輸入視訊轉碼器|Media Encoder Premium Workflow|Azure Media Encoder|Media Encoder Standard
---|---|---|---
AVC 8 位元/10 位元，高達 4:2:2，包括 AVCIntra|是|唯一的 8 位元 4:2:0|8 位元 4:2:0 和 4:2:2
Avid DNxHD (使用 MXF)|是|否|是
DVCPro/DVCProHD (使用 MXF)|是|否|是
JPEG2000|是|否|是
MPEG-2 (高達 422 Profile 和 High Level，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs ® 和 D10 等變種)|是|最高 422 設定檔|最高 422 設定檔
MPEG-1|是|是|是
Windows Media 視訊/VC-1|是|是|是
Canopus HQ/HQX|否|是|否
Mpeg-4 第 2 部分|否|否|是
[Theora](https://en.wikipedia.org/wiki/Theora)|否|否|是

輸入音訊轉碼器

輸入音訊轉碼器|Media Encoder Premium Workflow|Azure Media Encoder|Media Encoder Standard
---|---|---|---
AES (SMPTE 331M 和 302M，AES3-2003)|是|否|否
Dolby® E|是|否|否
Dolby® Digital (AC3)|是|是|否
Dolby® Digital Plus (E-AC3)|是|否|否
AAC (AAC-LC、AAC-HE 和 AAC-HEv2；高達 5.1)|是|是|是
MPEG Layer 2|是|是|是
MP3 (MPEG-1 音訊層 3)|是|是|是
Windows Media 音訊|是|是|是
WAV/PCM|是|是|是
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|否|否|是
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |否|否|是
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|否|否|是

###輸出格式

輸出容器/檔案格式

輸出容器/檔案格式|Media Encoder Premium Workflow|Azure Media Encoder|Media Encoder Standard
---|---|---|---
Adobe® Flash® F4V|是|否|否
MXF (OP1a、XDCAM 和 AS02)|是|否|否
DPP (包括 AS11)|是|否|否
GXF|是|否|否
MPEG-4/MP4|是|是|是
MPEG-TS|是|否|是
Windows Media/ASF|是|是|否
AVI (未壓縮 8 位元/10 位元)|是|否|否
Smooth Streaming 檔案格式 (PIFF 1.3)|是|是|否

輸出視訊轉碼器

輸出視訊轉碼器|Media Encoder Premium Workflow|Azure Media Encoder|Media Encoder Standard
---|---|---|---
AVC (H.264；8 位元；高達 High Profile、Level 5.2；4K Ultra HD；AVC Intra)|是|僅限高達 1080p 的 8 位元 4:2:0|僅限 8 位元 4:2:0
Avid DNxHD (使用 MXF)|是|否|否
DVCPro/DVCProHD (使用 MXF)|是|否|否
MPEG-2 (高達 422 Profile 和 High Level，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs ® 和 D10 等變種)|是|否|否
MPEG-1|是|否|否
Windows Media 視訊/VC-1|是|是|否
JPEG 縮圖建立|是|是|否

輸出音訊轉碼器

輸出音訊轉碼器|Media Encoder Premium Workflow|Azure Media Encoder|Media Encoder Standard
---|---|---|---
AES (SMPTE 331M 和 302M，AES3-2003)|是|否|否
Dolby® Digital (AC3)|是|是|否
Dolby® Digital Plus (E-AC3) 高達 7.1|是|最高 5.1|否
AAC (AAC-LC、AAC-HE 和 AAC-HEv2；高達 5.1)|是|是|是
MPEG Layer 2|是|否|否
MP3 (MPEG-1 音訊層 3)|是|否|否
Windows Media 音訊|是|是|否

##相關文章

- [介紹 Azure 媒體服務中的 Premium 編碼](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
- [如何使用 Azure 媒體服務中的 Premium 編碼](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
- [配額和限制](media-services-quotas-and-limitations.md)

 
<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

<!---HONumber=August15_HO7-->