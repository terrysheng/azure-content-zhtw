<properties 
	pageTitle="Media Encoder Standard 格式和轉碼器"
	description="本主題提供 Azure Media Encoder Standard 格式和轉碼器的概觀。"
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

#Media Encoder Standard 格式和轉碼器


本文件包含您可以在 Media Encoder Standard 中使用的常見匯入和匯出檔案格式清單。


##輸入容器/檔案格式

檔案格式 (副檔名)|支援
---|---|---|---
FLV (使用 H.264 和 AAC 轉碼器) (.flv) |是 
MXF (.mxf) |是 
GXF (.gxf) |是 
MPEG2-PS、MPEG2-TS、3GP (.ts、.ps、.3gp) |是 
Windows Media 視訊 (WMV)/ASF (.wmv、.asf) |是 
AVI (未壓縮 8 位元/10 位元) (.avi)|是 
MP4/ISMV (.ismv)|是 
[Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |是 
Matroska/WebM (.mkv) |是 
WAVE/WAV (.wav) |是 
 

##輸入視訊轉碼器

輸入視訊轉碼器|支援
---|---|---|---
AVC 8 位元/10 位元，高達 4:2:2，包括 AVCIntra |8 位元 4:2:0 和 4:2:2 
Avid DNxHD (使用 MXF) |是 
DVCPro/DVCProHD (使用 MXF) |是 
JPEG 2000 |是 
MPEG-2 (高達 422 Profile 和 High Level，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs ® 和 D10 等變種)|最高 422 設定檔 
MPEG-1 |是 
VC-1/WMV9 |是 
Canopus HQ/HQX |否 
Mpeg-4 第 2 部分 |是 
[Theora](https://en.wikipedia.org/wiki/Theora) |是 
YUV420 未壓縮或夾層 |是


##輸入音訊轉碼器

輸入音訊轉碼器|支援
---|---|---|---
AAC (AAC-LC、AAC-HE 和 AAC-HEv2；高達 5.1)|是 
MPEG Layer 2|是 
MP3 (MPEG-1 音訊層 3)|是 
Windows Media 音訊|是 
WAV/PCM|是 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|是 
[Opus](https://en.wikipedia.org/wiki/Opus_(audio_format) |是 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|是 
AMR (可變多速率)|是
AES (SMPTE 331M 和 302M，AES3-2003) |否 
Dolby® E |否 
Dolby® Digital (AC3) |否 
Dolby® Digital Plus (E-AC3) |否 


##輸出格式和轉碼器

下表會列出支援匯出的轉碼器和檔案格式清單。


檔案格式|視訊轉碼器|音訊轉碼器
---|---|---
MP4 (* .mp4)<br/><br/>(包括多位元速率 MP4 容器) |H.264 (高、主要和基準設定檔)|AAC-LC、HE-AAC v1、HE-AAC v2 
MPEG2-TS |H.264 (高、主要和基準設定檔)|AAC-LC、HE-AAC v1、HE-AAC v2 

##另請參閱

[透過 Azure Media Services 編碼的隨選內容](media-services-encode-asset.md)

[如何使用 Media Encoder Standard 進行編碼](media-services-dotnet-encode-with-media-encoder-standard.md)

<!---HONumber=September15_HO1-->