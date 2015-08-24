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


[Media Encoder 匯入格式](#import_formats)

[Media Encoder 匯出格式](#export_formats)


##<a id="import_formats"></a>Media Encoder 匯入格式 

下節會列出支援匯入的轉碼器和檔案格式清單。


##視訊轉碼器：

- MPEG-2
- H.264
- YUV420 未壓縮或夾層
- DNxHD
- VC-1/WMV9
- MPEG-4 Part 2
- JPEG 2000
- Theora

###音訊轉碼器

- PCM
- AAC (AAC-LC、AAC-HE 及 AAC-HEv2)
- WMA9/Pro
- MP3 (MPEG-1 音訊層 3)
- FLAC
- Opus
- Vorbis
 
###格式

檔案格式|檔案副檔名
---|---
FLV (使用 H.264 和 AAC 轉碼器) |.flv
MP4/ISMV|* .ismv
MPEG2-PS、MPEG2-TS、3GP|.ts、.ps、.3gp
MXF|.mxf
WMV/ASF|.mwv、.asf
DVR-MS|.dvr-ms 
AVI|.avi
Matroska|.mkv
GXF|.gxf
WAVE/WAV |.wav


##<a id="export_formats"></a>Media Encoder 匯出格式

下表會列出支援匯出的轉碼器和檔案格式清單。


檔案格式|視訊轉碼器|音訊轉碼器
---|---|---
MP4 (* .mp4)<br/><br/>(包括多位元速率 MP4 容器) |H.264 (高、主要和基準設定檔)|AAC-LC、HE-AAC v1、HE-AAC v2 
MPEG2-TS |H.264 (高、主要和基準設定檔)|AAC-LC、HE-AAC v1、HE-AAC v2 

##另請參閱

[透過 Azure Media Services 編碼的隨選內容](media-services-encode-asset.md)

<!---HONumber=August15_HO7-->