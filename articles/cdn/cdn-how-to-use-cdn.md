<properties 
	pageTitle="如何使用 CDN |Microsoft Azure" 
	description="了解如何使用 Azure 內容傳遞網路 (CDN) 來快取 Blob 和靜態內容，以傳遞高頻寬內容。" 
	services="cdn" 
	documentationCenter=".net" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>


# 使用 Azure 的 CDN

Azure 內容傳遞網路 (CDN) 是在 Azure 中調整任何 HTTP 應用程式的基礎建置組塊。它透過快取和傳遞接近使用者的內容，將全域方案提供給 Azure 客戶。因此，使用者要求會聰明地路由至最適合執行的 CDN 邊緣 POP，而不是每次都按原點。這會大幅增加效能和使用者經驗。如需 CDN 節點位置的最新清單，請參閱「[Azure 內容傳遞網路 (CDN) POP 位置](cdn-pop-locations.md)」。

使用 CDN 來快取 Azure 資料的優點包括：

- 讓離內容來源遙遠，且所使用的應用程式需要許多 HTTP 要求才能載入內容的使用者，能享有更好的效能和使用者經驗。
- 大型的分散式規模，可更妥善地處理瞬間大量負載，例如產品上市等活動的開頭。
- 透過分散使用者要求，以及從全域邊緣 POP 放送內容，傳送至原始來源的流量將會減少。

>[AZURE.TIP]Azure CDN 可以從各種不同的原始來源散布內容。Azure 中的整合式原始來源包含 App Service、雲端服務、Blob 儲存體，以及媒體服務。您也可以使用任何可公開存取的網址，來定義自訂的原始來源。

##如何啟用 CDN

1. 建立其端點指向您原始來源的 CDN 設定檔

	CDN 設定檔就是 CDN 端點的集合。每個設定檔都有至少一個 CDN 端點。當您建立 CDN 設定檔之後，就能建立使用您所選原始來源的新 CDN 端點。
	
	>[AZURE.NOTE]一個 Azure 訂用帳戶只能擁有四個 CDN 設定檔，而每個 CDN 設定檔則只能擁有四個 CDN 端點。
	>
	> CDN 定價是根據 CDN 設定檔層級來套用的。如果您想要混合使用標準和進階的 CDN 功能，就需要擁有多個 CDN 設定檔。
	
	如需建立 CDN 設定檔和端點的詳細教學課程，請參閱「[如何啟用 Azure 內容傳遞網路](cdn-create-new-endpoint.md)」。
	
2. 設定您的 CDN 組態

	您可以為 CDN 端點啟用一些功能，例如[快取原則](cdn-caching-policy.md)、[查詢字串快取](cdn-query-string.md)、[規則引擎](cdn-rules-engine.md)等。如需詳細資訊，請參閱左邊的 [管理] 功能表。

3. 存取 CDN 內容

	若要存取 CDN 上快取的內容，請使用入口網站中提供的 CDN URL。例如，快取 Blob 的位址會類似：`http://<identifier>.azureedge.net/<myPublicContainer>/<BlobName>`

## 從 Azure 儲存體快取內容

在 Azure 儲存體帳戶上啟用 CDN 之後，將會透過 CDN 快取 Blob，而這些 Blob 位於公用容器中且可供匿名存取。使用 Azure CDN 只能快取公開使用的 Blob。若要讓 Blob 設為公開可用以供匿名存取，則必須將其容器表示為公用。這樣做之後，該容器內的所有 Blob 都可供進行匿名讀取存取。您也可以選擇將容器資料設為公開，或只限存取其內的 Blob。如需管理容器和 Blob 之存取控制的相關資訊，請參閱「[管理 Azure 儲存體資源的存取](../storage/storage-manage-access-to-resources.md)」。

為了達到最佳效能，請使用 CDN 邊緣快取來傳遞大小小於 10 GB 的 Blob。

當您啟用儲存體帳戶的 CDN 存取時，管理入口網站會以下列格式提供 CDN 網域名稱：`http://<identifier>.azureedge.net/`。此網域名稱可用來從網站擷取物件。例如，如果提供名稱為 cdn 的公用容器和稱為 music.png 的影像檔，則使用者可以使用下列兩個 URL 中的任何一個來存取物件：

- **Azure Blob 服務 URL**：`http://myAccount.blob.core.windows.net/music/` 
- **Azure CDN URL**：`http://<identifier>.azureedge.net/music/` 

## 從 Azure 網站快取內容

您可以從網站啟用 CDN 來快取您的 Web 內容 (例如影像、指令碼和樣式表)。請參閱「[整合 Azure 網站與 Azure CDN](../app-service-web/cdn-websites-with-cdn.md)」。

當您啟用網站的 CDN 存取時，管理入口網站會以下列格式提供 CDN 網域名稱：`http://<identifier>.azureedge.net/`。此網域名稱可用來從網站擷取物件。例如，如果提供名稱為 cdn 的公用容器和稱為 music.png 的影像檔，則使用者可以使用下列兩個 URL 中的任何一個來存取物件：

- **Azure 網站 URL**：`http://mySiteName.azurewebsites.net/cdn/music.png` 
- **Azure CDN URL**：`http://<identifier>.azureedge.net/cdn/music.png`
 
## 從 Azure 雲端服務快取內容

您可以將 Azure 雲端服務所提供的物件快取至 CDN。

雲端服務的快取具有下列限制：


- CDN 只應該用來快取靜態內容。

	>[AZURE.WARNING]快取高易變性或真正動態內容可能會對效能造成不良影響，或導致內容問題，而這些都會增加成本。
- 您的雲端服務必須部署至生產部署中。
- 您的雲端服務必須使用 HTTP 在連接埠 80 上提供物件。
- 雲端服務必須在雲端服務的 /cdn 資料夾中放置要快取的內容，或從中傳遞內容。

當您啟用雲端服務的 CDN 存取時，管理入口網站會以下列格式提供 CDN 網域名稱：`http://<identifier>.azureedge.net/`。此網域名稱可用來從雲端服務擷取物件。例如，如果提供名稱為 myHostedService 的雲端服務以及稱為 music.aspx 並傳遞內容的 ASP.NET 網頁，則使用者可以使用下列兩個 URL 中的任何一個來存取物件：


- **Azure 雲端服務 URL**：`http://myHostedService.cloudapp.net/music.aspx` 
- **Azure CDN URL**：`http://<identifier>.azureedge.net/music.aspx` 

## 快取自訂原始來源的內容

您可以將物件快取到由任何可公開存取的 Web 應用程式所提供的 CDN。

自訂原始來源的快取有下列限制：

- CDN 只應該用來快取靜態內容。

	>[AZURE.WARNING]快取高易變性或真正動態內容可能會對效能造成不良影響，或導致內容問題，而這些都會增加成本。
- 自訂原始來源的內容必須裝載在擁有公用 IP 位址的伺服器上。CDN 邊緣節點無法從防火牆後方的內部網路伺服器擷取資產。

當您啟用自訂原始來源的 CDN 存取時，Azure 入口網站會以下列格式提供 CDN 網域名稱：`http://<identifier>.azureedge.net/`。此網域名稱可用來從自訂原始來源擷取物件。例如，假設有個位於 www.contoso.com 的網站，其中有個稱為 music.aspx 的 ASP.NET 網頁會傳遞內容，則使用者可使用下列其中一個 URL 來存取物件：


- **自訂原始內容 URL**：`http://www.contoso.com/music.aspx` 
- **Azure CDN URL**：`http://<identifier>.azureedge.net/music.aspx` 

## 使用查詢字串快取特定內容

您可以使用查詢字串來區分擷取自原始來源的物件。例如，如果原始來源所顯示的圖表可能會有變化，您可以傳遞查詢字串來擷取所需的特定圖表。例如：

`http://<identifier>.azureedge.net/chart.aspx?item=1`

查詢字串會傳遞為字串常值。如果您有個使用兩個參數 (例如 `?area=2&item=1`) 的服務，且您使用 `?item=1&area=2` 進行對原始來源的後續呼叫，則您會對相同的物件快取兩次。

如需有關查詢字串快取的詳細資訊，請參閱「[使用查詢字串控制 CDN 要求的快取行為](cdn-query-string.md)」。

## 透過 HTTPS 存取所快取的內容

Azure 可讓您使用 HTTPS 呼叫從 CDN 擷取內容。這可讓您將 CDN 中所快取的內容納入安全的網頁，而不會收到混合安全性內容類型的警告。

使用 HTTPS 存取 CDN 內容具有下列限制：


- 您必須使用 CDN 所提供的憑證。不支援協力廠商憑證。
- 您必須使用 CDN 網域來存取內容。因為 CDN 目前不支援自訂憑證，所以自訂網域名稱 (CNAME) 不提供 HTTPS 支援。

如需有關為 CDN 內容啟用 HTTPS 的詳細資訊，請參閱「[如何啟用 Azure 內容傳遞網路 (CDN)](cdn-create-new-endpoint.md)」。


## 使用自訂網域存取所快取的內容

您可以將 CDN HTTP 端點對應至自訂網域名稱，並使用該名稱從 CDN 要求物件。

如需有關對應自訂網域的詳細資訊，請參閱「[如何將內容傳遞網路 (CDN) 內容對應至自訂網域](cdn-map-content-to-custom-domain.md)」。

## 以程式設計方式管理 CDN

您可以使用 [CDN 資源提供者 REST API](https://msdn.microsoft.com/library/mt634456.aspx) 以程式設計方式管理 Microsoft Azure CDN。


## 另請參閱

- [如何啟用 Azure 內容傳遞網路](cdn-create-new-endpoint.md)
- [Azure 內容傳遞網路 (CDN) 概觀](cdn-overview.md)
- [清除 Azure CDN 端點](cdn-purge-endpoint.md)
- [CDN 資源提供者 REST API](https://msdn.microsoft.com/library/mt634456.aspx)

<!---HONumber=AcomDC_1217_2015-->