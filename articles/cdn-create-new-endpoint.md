<properties 
	 pageTitle="如何啟用 Azure 內容傳遞網路 (CDN)" 
	 description="本主題說明如何啟用 Azure 內容傳遞網路 (CDN)。" 
	 services="cdn" 
	 documentationCenter="" 
	 authors="zhangmanling" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="03/05/2015" 
	 ms.author="mazha"/>



#如何啟用 Azure 內容傳遞網路 (CDN)  

啟用儲存體帳戶、雲端服務之後這類來源的 CDN 端點之後，所有公開可用的物件皆適用於 CDN 邊緣快取。  


##建立新的 CDN 端點  

1.	登入 [Azure 管理入口網站](http://manage.windowsazure.com/)。
2.	在導覽窗格中，按一下 [**CDN**]。
3.	在功能區上，按一下 [**新增**]。在 [**新增**] 對話方塊中，依序選取 [**應用程式服務**]、[**CDN**] 和 [**快速建立**]。
4.	在 [**原始網域**] 下拉式清單中，從可用的原始類型清單中選取您想要的目的地。
5.	按一下 [**建立**] 按鈕，建立新的端點。




> 注意：為端點建立的組態無法立即使用，最多需要 60 分鐘進行註冊，以透過 CDN 網路傳播。嘗試立即使用 CDN 網域名稱的使用者可能會收到狀態碼 400 (不正確的要求)，直到可透過 CDN 使用內容為止。

#另請參閱
[如何將內容傳遞網路 (CDN) 內容對應至自訂網域](./cdn-map-content-to-custom-domain.md)

<!--HONumber=49-->