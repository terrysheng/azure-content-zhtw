<properties 
	pageTitle="Azure CDN 概觀" 
	description="了解何謂 Azure 內容傳遞網路 (CDN)，以及如何使用它透過快取 Blob 和靜態內容來傳遞高頻寬內容。" 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2016" 
	ms.author="casoper"/>

# Azure 內容傳遞網路 (CDN) 概觀

Azure 內容傳遞網路 (CDN) 會在策略性放置的位置上快取雲端服務所使用的 Azure Blob 和靜態內容，以提供將內容傳遞給使用者的最大頻寬。

現有的 CDN 客戶現在可以透過 [Microsoft Azure 入口網站](https://portal.azure.com)管理您的 CDN 端點。


CDN 為開發人員提供一套全球解決方案，以在全球實體節點上快取內容來傳遞高頻寬內容。如需 CDN 節點位置的最新清單，請參閱「[Azure 內容傳遞網路 (CDN) POP 位置](cdn-pop-locations.md)」。

使用 CDN 來快取 Azure 資料的優點包括：

- 讓離內容來源很遙遠、且所使用的應用程式需要在網際網路上歷經長途跋涉才能載入內容的使用者，享有更好的效能和使用者經驗
- 大型的分散式規模可更妥善處理瞬間大量負載 (例如產品上市事件的開頭)。 


>[AZURE.IMPORTANT]建立或啟用 CDN 端點時，可能需要 90 分鐘的時間才能傳播到全世界。
 
第一次對 CDN 提出物件要求時，會直接從該物件的來源原始位置擷取物件。這個原始來源可以是 Azure 儲存體帳戶、Web 應用程式、雲端服務或接受公用 Web 要求的任何自訂來源。使用 CDN 語法提出要求時，會將要求重新導向至 CDN 端點，而此 CDN 端點最接近從中提出要求以存取物件的位置。如果在該端點上找不到物件，則會從服務中擷取該物件，並在端點上進行快取，而且會維護所快取物件的存留時間 (TTL) 設定。

## 標準功能

標準 CDN 層包含下列功能:

- 很容易與 [儲存體](cdn-create-a-storage-account-with-cdn.md)、Web Apps，以及媒體服務等 Azure 服務整合
- [查詢字串快取](cdn-query-string.md)
- [自訂網域名稱支援](cdn-map-content-to-custom-domain.md)
- [國家 (地區) 篩選](cdn-restrict-access-by-country.md)
- [核心分析](cdn-analyze-usage-patterns.md)
- [自訂內容來源](cdn-map-content-to-custom-domain.md)
- [HTTPS 支援](cdn-how-to-use-cdn.md#accessing-cached-content-over-https)
- 負載平衡
- DDoS 保護
- [快速清除](cdn-purge-endpoint.md)
- [資產預先載入](cdn-preload-endpoint.md)
- [透過 REST API 管理](https://msdn.microsoft.com/library/mt634456.aspx)


## 進階功能

高階 CDN 層包含所有標準層的功能，以及以下這些額外功能：

- [可自訂的、規則式內容傳遞引擎](cdn-rules-engine.md)
- [進階 HTTP 報告](cdn-advanced-http-reports.md)
- [即時統計資料](cdn-real-time-stats.md)

<!---HONumber=AcomDC_0121_2016-->