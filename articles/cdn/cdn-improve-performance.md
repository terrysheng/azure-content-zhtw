<properties 
	pageTitle="CDN - 藉由壓縮檔案來改善效能"
	description="您可以藉由壓縮檔案來改善檔案傳輸速度並增加頁面載入效能。"
	services="cdn"
	documentationCenter=".NET"
	authors="juliako"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="juliako"/>

#藉由壓縮檔案來改善效能

本主題討論如何藉由壓縮檔案來改善檔案傳輸速度並增加頁面載入效能。

CDN 有兩種方式可以支援壓縮：

- 您可以在原始伺服器上啟用壓縮，此時 CDN 會依預設支援壓縮並將壓縮的檔案傳送到用戶端。 
- 您可以直接在 CDN 邊緣伺服器上啟用壓縮，此時 CDN 會壓縮檔案並將其提供給使用者。

##定義

- **Accept-Encoding** 要求標頭 - 此標頭指出使用者代理程式支援的壓縮方法。它必須包含在要求標頭中。如需詳細資訊，請參閱[標頭欄位定義](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)。
- **內容類型** - 必須加入內容類型的清單以進行壓縮。只有文字檔案適用於壓縮。例如 text/plain、text/html。
- **壓縮方法** - 受支援的壓縮方法有 gzip/deflate/bzip2，必須在 Accept-Encoding 要求標頭中設定受支援的方法。 
- **快取狀態** - 快取狀態會識別是否已在最接近要求者的 POP 上快取要求的內容。  
- **檔案大小** - 根據預設，壓縮僅支援大小超過 1 個位元組和小於 1 MB 的檔案。  

##工作流程

1. 要求者會傳送內容的要求。
2. 邊緣伺服器會檢查是否有 **Accept-Encoding** 標頭。
	1. 若包含此標頭，它會識別要求的壓縮方法。
	1. 若遺漏此標頭，這種類型的要求會以未壓縮的格式提供。
3.	最接近的邊緣 POP 會檢查快取狀態、壓縮方法以及它是否依然具有有效的 TTL。
	1.	快取遺漏：如果未快取要求的版本，要求會轉送到原始來源。
	2.	使用相同壓縮方法的快取點擊：Edge Server 會立即將壓縮的內容傳送至用戶端。
	3.	使用不同壓縮方法的快取點擊：Edge Server 會將資產轉碼為要求的壓縮方法。 
	4.	快取點擊和未壓縮：如果初始要求造成資產以未壓縮格式快取，則會執行檢查，查看要求是否適用於 Edge Server 壓縮 (根據上述定義/需求一節中的準則)。
		1.	如果適用，Edge Server 將會壓縮檔案並將其提供給用戶端。
		2.	如果不適用，Edge Server 會立即將未壓縮的內容傳送給用戶端。 

![檔案壓縮](./media/cdn-file-compression/cdn-compress-files.png)

##注意事項 

1. 媒體服務 CDN 會啟用串流端點，預設會為下列內容類型啟用壓縮：application/vnd.ms-sstr+xml,application/dash+xml,application/vnd.apple.mpegurl,application/f4m+xml。您不能使用 Azure 入口網站啟用/停用上述類型的壓縮。  
2. Edge Server 上只能快取一個檔案版本 (壓縮或未壓縮)。不同版本的要求將會導致 Edge Server 轉碼內容。  

<!---HONumber=September15_HO1-->