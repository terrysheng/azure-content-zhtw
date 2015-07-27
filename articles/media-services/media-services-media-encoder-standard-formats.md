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
	ms.date="07/08/2015" 
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

<table border="1">
<tr><th>檔案格式</th><th>檔案副檔名</th></tr>
<tr><td>FLV (使用 H.264 和 AAC 轉碼器) </td><td>.flv</td></tr>
<tr><td>MP4/ISMV</td><td>*.ismv</td></tr>
<tr><td>MPEG2-PS、MPEG2-TS、3GP</td><td>.ts、.ps、.3gp</td></tr>
<tr><td>MXF</td><td>.mxf</td></tr>
<tr><td>WMV/ASF</td><td>.mwv、.asf</td></tr>
<tr><td>DVR-MS</td><td>.dvr-ms </td></tr>
<tr><td>AVI</td><td>.avi</td></tr>
<tr><td>Matroska</td><td>.mkv</td></tr>
<tr><td>GXF</td><td>.gxf</td></tr>
<tr><td>WAVE/WAV </td><td>.wav</td></tr>
</table>

##<a id="export_formats"></a>Media Encoder 匯出格式

下表會列出支援匯出的轉碼器和檔案格式清單。


<table border="1">
<tr><th>檔案格式</th><th>視訊轉碼器</th><th>音訊轉碼器</th></tr>
<tr><td>MP4 (*.mp4)<br/><br/>(包括多位元速率 MP4 容器) </td><td>H.264 (高、主要和基準設定檔)</td><td>AAC-LC、HE-AAC v1、HE-AAC v2 </td></tr>
<tr><td>MPEG2-TS </td><td>H.264 (高、主要和基準設定檔)</td><td>AAC-LC、HE-AAC v1、HE-AAC v2 </td></tr>
</table>

##另請參閱

[透過 Azure Media Services 編碼的隨選內容](media-services-encode-asset.md)

<!---HONumber=July15_HO3-->