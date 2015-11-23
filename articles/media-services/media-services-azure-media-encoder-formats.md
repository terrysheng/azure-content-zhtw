<properties 
	pageTitle="Azure Media Encoder 格式和轉碼器" 
	description="本主題提供 Azure 媒體編碼器格式和轉碼器的概觀。" 
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
	ms.date="10/15/2015"  
	ms.author="juliako"/>

#Azure Media Encoder 格式和轉碼器

本文件包含您可以和 Azure 媒體編碼器搭配使用之最常見輸入與輸出檔案格式和轉碼器的清單。


##輸入檔案格式 (容器)
 
檔案格式 (副檔名)|支援
---|---
3GPP、3GPP2 (.3gp、.3g2、.3gp2) |是
進階系統格式 (ASF) (.asf) |是
進階視訊編碼高畫質 (AVCHD) [ MPEG-2 傳輸資料流 ] (.mts、.m2ts) |是
音訊視訊交錯格式 (AVI) (.avi) |是
數位攝影機 MPEG-2 (MOD) (.mod) |是
DVD 傳輸串流 (TS) 檔案 (.ts) |是
DVD 視訊物件 (VOB) 檔案 (.vob) |是
Expression Encoder 螢幕擷取轉碼器檔案 (.xesc) |是
MP4 (.mp4、.m4a、.m4v)/ISMV (.isma、.ismv) |是
MPEG-1 系統資料流 (.mpeg、.mpg) |是
MPEG-2 視訊檔案 (.m2v) |是
Windows Media 視訊 (WMV) (.wmv) |是
AC-3 (Dolby Digital) 音訊 (.ac3)|是
音訊交換檔案格式 (AIFF) (.aiff)|是
廣播聲波格式 (.bwf)|是
MP3 (MPEG-1 音訊層 3) (.mp3)|是
MPEG-4 有聲書 (m4b)|是
WAVE 檔案 (.wav)|是
Windows Media 音訊 (.wma)|是
Adobe® Flash® F4V |否		
MXF/SMPTE 377M |限制 
GXF |否		 
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984)|否
Matroska/WebM |否


支援部分未壓縮格式。如需詳細資訊，請參閱[支援的未壓縮視訊格式](#uncompressed)

##輸入視訊轉碼器

輸入視訊轉碼器|支援
---|--- 
H.264 (基準、主要和高設定檔) |是
AVC 8 位元/10 位元，高達 4:2:2，包括 AVCIntra |唯一的 8 位元 4:2:0
Avid DNxHD (使用 MXF) |否
DVCPro/DVCProHD (使用 MXF) |否
JPEG2000 |否
MPEG-2 (簡易和主要設定檔及 4:2:2 設定檔) |最高 4:2:2 設定檔
MPEG-1 (包括 MPEG-PS) |是
Windows Media 視訊/VC-1 |是
Canopus HQ/HQX |是
MPEG-4 v2 (簡易視覺設定檔和進階簡易設定檔) |是
[Theora](https://en.wikipedia.org/wiki/Theora) |否
VC-1 (簡易、主要和進階設定檔) |是
Windows Media 視訊 (簡易、主要和進階設定檔) |是
DV (DVC、DVHD、DVSD、DVSL) |是
Grass Valley HQ/HQX |是
 

##輸入音訊轉碼器

輸入音訊轉碼器|支援
---|---
AES (SMPTE 331M 和 302M，AES3-2003) |否
Dolby® E |否
Dolby® Digital (AC3) |是
Dolby® Digital Plus (E-AC3) |否
AAC (AAC-LC、搭配 AAC-LC 核心的 HE-AAC v1，和搭配 AAC-LC 核心的 HE-AAC v2；最高 5.1)|是
MPEG Layer 2|是|是|是
MP3 (MPEG-1 音訊層 3)|是
Windows Media 音訊 9 (Windows Media Audio Standard、Windows Media Audio Professional 和 Windows Media Audio Lossless) |是
WAV/PCM|是
[FLAC](https://en.wikipedia.org/wiki/FLAC)|否
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |否
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)|否


##輸入影像檔案格式

檔案格式 (副檔名) | 支援
---|---
Bitmap (.bmp) | 是
GIF、動畫 GIF (.gif)| 是
JPEG (.jpeg、.jpg)| 是
PNG (.png)| 是
TIFF (.tif)| 是
WPF 畫布 XAML (.xaml)| 是


##輸出格式和轉碼器

下表會列出支援匯出的轉碼器和檔案格式清單。

檔案格式|視訊轉碼器|音訊轉碼器
---|---|---
Windows Media (* .wmv、* .wma)|VC-1 (進階、主要和簡易設定檔)|Windows Media Audio Standard、Windows Media Audio Professional、Windows Media Audio Voice、Windows Media Audio Lossless
MP4 (* .mp4)|H.264 (高、主要和基準設定檔)|AAC-LC、HE-AAC v1、HE-AAC v2、Dolby Digital Plus
Smooth Streaming 檔案格式 (PIFF 1.1) (* .ismv、* .isma)|VC-1 (進階設定檔)<p>H.264 (高、主要和基準設定檔) |Windows Media Audio Standard、Windows Media Audio Professional<p><p>AAC-LC、HE-AAC v1、HE-AAC v2

如需媒體服務中其他支援的轉碼器和篩選器，請參閱 [Windows DirectShow 篩選](https://msdn.microsoft.com/library/windows/desktop/dd375464.aspx) (英文)。

##<a id="uncompressed"></a>支援的未壓縮視訊格式 

Azure 媒體服務提供匯入未壓縮視訊資料的支援。

以下是支援的未壓縮格式部分清單。

未壓縮的視訊格式|說明
---|---
標準 YVU9 格式未壓縮資料|平面 YUV 格式。每行的每個水平像素都是 Y 樣本，每第四個水平像素是 U 和 V 樣本。每條垂直線上的像素都是 Y 樣本，每第四條垂直線上的像素是 U 和 V 樣本。每像素 9 位元。
YUV 411 格式資料|每行的每個水平像素都是 Y 樣本，每第四個水平像素是 U 和 V 樣本。每條垂直線都會進行取樣。位元組順序 (從低到高) 會是 U0、Y0、V0、Y1、U4、Y2、V4、Y3、Y4、Y5、Y6、Y7，其中尾碼 0 是會最左邊的像素，而逐漸增加的數字是從左到右逐漸增加的像素。每個 12 位元組的區塊有 8 個影像像素。
Y41P 格式資料|每行的每個水平像素都是 Y 樣本，每第四個水平像素是 U 和 V 樣本。每條垂直線都會進行取樣。位元組順序 (從低到高) 會是 U0、Y0、V0、Y1、U4、Y2、V4、Y3、Y4、Y5、Y6、Y7，其中尾碼 0 是會最左邊的像素，而逐漸增加的數字是從左到右逐漸增加的像素。每個 12 位元組的區塊有 8 個影像像素。
YUY2 格式資料|與 UYVY 相同，但有不同的像素順序。位元組順序 (從低到高) 會是 Y0、U0、Y1、V0、Y2、U2、Y3、V2、Y4、U4、Y5、V4，其中尾碼 0 是會最左邊的像素，而逐漸增加的數字是從左到右逐漸增加的像素。每個 4 位元組的區塊有 2 個影像像素。
YVYU 格式資料|壓縮 YUV 格式。與 UYVY 相同，但有不同的像素順序。位元組順序 (從低到高) 會是 Y0、V0、Y1、U0、Y2、V2、Y3、U2、Y4、V4、Y5、U4，其中尾碼 0 是會最左邊的像素，而逐漸增加的數字是從左到右逐漸增加的像素。每個 4 位元組的區塊有 2 個影像像素。
UYVY 格式資料|壓縮 YUV 格式。每行的每個水平像素都是 Y 樣本，每第二個水平像素是 U 和 V 樣本。每條垂直線都會進行取樣。最常用的各種 YUV 4:2:2 格式。位元組順序 (從低到高) 會是 U0、Y0、V0、Y1、U2、Y2、V2、Y3、U4、Y4、V4、Y5，其中尾碼 0 是會最左邊的像素，而逐漸增加的數字是從左到右逐漸增加的像素。每個 4 位元組的區塊有 2 個影像像素。
YUV 211 格式資料|壓縮 YUV 格式。每行的每第二個水平像素都是 Y 樣本，每第四個水平像素是 U 和 V 樣本。每條垂直線都會進行取樣。位元組順序 (從低到高) 會是 U0、Y0、V0、Y1、U4、Y2、V4、Y3、Y4、Y5、Y6、Y7，其中尾碼 0 是會最左邊的像素，而逐漸增加的數字是從左到右逐漸增加的像素。每個 4 位元組的區塊有 4 個影像像素。
Cirrus Logic Jr YUV 411 格式|Cirrus Logic Jr YUV 411 格式， 每個 Y、U 和 V 樣本小於 8 位元。每行的每個水平像素都是 Y 樣本，每第四個水平像素是 U 和 V 樣本。每條垂直線都會進行取樣。
Indeo-produced YVU9 格式|Indeo-produced YVU9 格式，包含與最後一個影格的區別額外資訊。每個像素 9.5 位元，但回報為 9 位元。


##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=Nov15_HO3-->