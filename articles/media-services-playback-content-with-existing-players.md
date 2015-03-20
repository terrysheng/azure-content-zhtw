<properties 
	pageTitle="播放您的內容" 
	description="本主題描述如何播放您的內容。" 
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
	ms.date="02/16/2015" 
	ms.author="juliako"/>


#播放您的內容

Azure 媒體服務支援許多熱門的串流格式，例如 Smooth Streaming、HTTP 即時串流和 MPEG-Dash。本主題會指引您可用來測試串流的現有播放程式。  

##使用現有播放器來播放您的內容

本主題說明您可用來播放內容的現有播放程式。

>[AZURE.NOTE]若要播放動態封裝或動態加密的內容，請務必從您打算傳遞內容的串流端點取得至少一個串流單元。如需調整串流單元的相關資訊，請參閱：[如何調整串流單位](../media-services-manage-origins#scale_streaming_endpoints)。


###Azure 管理入口網站媒體服務內容播放程式

**Azure 管理入口網站**提供內容播放程式，您可用來測試您的視訊。

按一下所需的視訊 (請確定它已[發佈](../media-services-manage-content#publish))，然後按一下入口網站底部的 [**播放**] 按鈕。 
 
適用以下考量事項：

- **媒體服務內容播放程式**會從預設串流端點播放。如果您想要從非預設串流端點播放，請使用其他播放程式。例如 [Azure 媒體服務播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。
 

![AMSPlayer][AMSPlayer]

###Azure 媒體服務播放器

使用 [Azure 媒體服務播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)來播放您的內容。


##開發視訊播放器

如需如何自行開發播放器的相關資訊，請參閱[開發視訊播放器](../media-services-develop-video-players)
 
[AMSPlayer]: ./media/media-services-players/media-services-portal-player.png
<!--HONumber=47-->
