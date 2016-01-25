<properties
	pageTitle="什麼是 Azure 搜尋服務 | Microsoft Azure | 雲端託管搜尋服務"
	description="Azure 搜尋服務是受完整管理的託管雲端搜尋服務。深入了解此功能概觀。"
	services="search"
	authors="ashmaka"
	documentationCenter=""/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="01/12/2016"
	ms.author="ashmaka"/>

# 何謂 Azure 搜尋服務？
Azure 搜尋服務是一項雲端搜尋即服務解決方案，可將伺服器和基礎結構管理委託給 Microsoft，讓您利用立即可用的服務來填入搜尋資料，然後用來在 Web 或行動應用程式中加入搜尋。Azure 搜尋服務可讓您使用簡單的 [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 或 [.NET SDK](search-howto-dotnet-sdk.md) 輕鬆地將強大的搜尋經驗加入至應用程式，而不需要管理搜尋基礎結構或成為搜尋專家。

## 讓使用者擁有強大的搜尋經驗

**強大的查詢**可以使用[簡單的查詢語法](https://msdn.microsoft.com/library/azure/dn798920.aspx)制訂，以提供邏輯運算子、片語搜尋運算子、後置運算子、優先順序運算子。此外，[Lucene 查詢語法](https://msdn.microsoft.com/library/azure/mt589323.aspx) (目前處於預覽階段) 可以啟用模糊搜尋、拼字錯誤、鄰近搜尋、詞彙提升及規則運算式。Azure 搜尋服務也支援自訂語彙分析器，可讓應用程式能使用語音比對和規則運算式來處理複雜的搜尋查詢。

**語言支援**[包含 56 種不同的語言](https://msdn.microsoft.com/library/azure/dn879793.aspx)。Azure 搜尋服務可以同時使用 Lucene 分析器和 Microsoft 分析器 (經由 Office 和 Bing 中數年的自然語言處理修改) 分析應用程式搜尋方塊中的文字，以智慧的方式處理語言專屬的語言學，包括動詞時態、性別、不規則複數名詞 (例如 ' mouse' 與 'mice')、取消複合字、斷字 (針對不含空格的語言) 等等。

可以對自動完成搜尋列和隨打即找查詢啟用**搜尋建議**。在使用者輸入部分搜尋輸入時[建議您索引中的實際文件](https://msdn.microsoft.com/library/azure/dn798936.aspx)。

**醒目提示**[可讓](https://msdn.microsoft.com/library/azure/dn798927.aspx)使用者在每個結果中看到包含其查詢相符項目的文字片段。您可以挑選哪些欄位傳回醒目提示的文字片段。

**多面向導覽**已輕鬆地加入至 Azure 搜尋服務的搜尋結果頁面。只要使用[單一查詢參數](https://msdn.microsoft.com/library/azure/dn798927.aspx)，Azure 搜尋服務就會傳回所有必要的資訊，以在您的應用程式 UI 中創造多面向搜尋體驗，讓使用者能向下鑽研及篩選搜尋結果 (例如依價格範圍或品牌搜尋目錄項目)。

**地理空間**[支援](search-create-geospatial.md)可讓您以智慧方式處理、篩選和顯示地理位置。Azure 搜尋服務可讓使用者根據指定位置的搜尋結果鄰近程度，或根據特定的地理區域來瀏覽資料。

**篩選**可用來輕鬆地將多面向導覽納入應用程式的 UI、增強查詢編寫，以及根據使用者或開發人員指定的準則進行篩選。使用 [OData 語法](https://msdn.microsoft.com/library/azure/dn798921.aspx)建立強大的篩選條件。

## 利用簡單易用的服務來賦予開發人員能力

**高可用性**可確保相當可靠的搜尋服務體驗。經過適當的調整，[Azure 搜尋服務可提供 99.9% SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

Azure 搜尋服務會當作端對端解決方案**受到完整管理**，所以完全不需要基礎結構管理。以兩種方式調整您的服務，即可輕鬆地針對您的需求量身訂做，以處理更多的文件儲存體、更高的查詢負載，或兩者。

使用[索引子](https://msdn.microsoft.com/library/azure/dn946891.aspx)的**資料整合**可讓 Azure 搜尋服務自動耙梳 Azure SQL Database、Azure DocumentDB 或 [Azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)，以同步處理搜尋索引的內容與主要資料存放區。

**文件破解**可用於 (目前處於預覽階段) [讀取和檢索主要檔案格式](search-howto-indexing-azure-blob-storage.md)，包括 Microsoft Office 以及 PDF 和 HTML 文件。

[輕鬆地收集和分析](search-traffic-analytics.md)**搜尋流量分析**，以便深入分析使用者在搜尋方塊中輸入的內容。

**簡單評分**是 Azure 搜尋服務的主要優點。[評分設定檔](https://msdn.microsoft.com/library/azure/dn798928.aspx)可讓組織將相關性模型化為文件本身的值函數。例如，您可能想要新推出的產品或折扣的產品，出現在搜尋結果中較高的位置。您也可以根據您所追蹤並個別儲存的客戶搜尋喜好設定，使用標記進行個人化計分來建置計分設定檔。

**排序**是透過索引結構描述來對多個欄位提供，然後使用單一搜尋參數在查詢階段進行切換。

利用 Azure 搜尋服務對您的搜尋結果提供的[微調控制項](search-pagination-page-layout.md)，直接對您的搜尋結果進行**分頁**和節流。

**搜尋總管**可讓您直接從您的帳戶的 Azure 入口網站，對您所有的索引發出查詢，以便輕鬆地測試查詢並修改評分設定檔。

## 運作方式

### 1\.佈建服務
您可以使用 [Azure 入口網站](https://portal.azure.com/)或 [Azure 資源管理 API](https://msdn.microsoft.com/library/azure/dn832684.aspx)來加速 Azure 搜尋服務。

根據設定服務的方式，您將使用與其他 Azure 搜尋服務訂閱者共用的免費層，或使用可提供您的服務專用資源的標準[定價層](https://azure.microsoft.com/pricing/details/search/)。佈建您的服務時，您也可以選擇裝載服務的資料中心區域。

在標準層中使用 Azure 搜尋服務時，您可以用兩個方式來調整您的服務：1) 新增複本以提升您處理繁重查詢負載的能力，以及 2) 新增資料分割以增加可容納更多文件的儲存體。您可以分開處理文件儲存體和查詢輸送量，進而自訂您的搜尋服務以滿足特定需求。

### 2\.建立索引
您必須先定義 Azure 搜尋服務索引，才可以將您的內容上傳至 Azure 搜尋服務。索引就像是資料庫資料表，其中保存您的資料並可接受搜尋查詢。您可定義索引結構描述，以對應至您要搜尋的文件結構 (類似於資料庫中的欄位)。

您可以在 Azure 入口網站中，或[使用 .NET SDK](search-howto-dotnet-sdk.md) 或 [REST API](https://msdn.microsoft.com/library/azure/dn798941.aspx) 以程式設計方式建立這些索引的結構描述。一旦定義索引之後，您就可以將資料上傳至 Azure 搜尋服務，接著編製索引。

### 3\.索引資料
定義索引的欄位和屬性之後，即可將您的內容上傳至索引。您可以使用發送或提取模型，將資料上傳至索引。

提取模型是透過可以針對需要或排定的更新而設定的索引子提供的 (請參閱[索引子作業 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn946891.aspx))，可讓您輕鬆地從裝載於 Azure VM 的 Azure DocumentDB、Azure SQL Database、Azure Blob 儲存體或 SQL Server 擷取資料和資料變更。

發送模式是透過用於將更新的文件傳送到索引的 SDK 或 REST API 提供的。使用 JSON 格式，您幾乎可以從任何資料集發送資料。如需載入資料的指引，請參閱[新增、更新或刪除文件](https://msdn.microsoft.com/library/azure/dn798930.aspx)或[如何使用 .NET SDK](search-howto-dotnet-sdk.md)。

### 4\.搜尋
填入您的 Azure Search 索引後，您現在可以透過 REST API 或 .NET SDK 使用簡單的 HTTP 要求對服務端點[發出搜尋查詢](https://msdn.microsoft.com/library/azure/dn798927.aspx)。

## 立即試用 (免費！)
您可以立即試用 Azure 搜尋服務！ 如果您已經有 Azure 帳戶，您可以[在免費層中佈建服務](search-create-service-portal.md)。

如果您沒有 Azure 帳戶，您可以免費試用 60 分鐘的工作階段 (不需要註冊)。移至[試用 Azure App Service](http://go.microsoft.com/fwlink/p/?LinkId=618214)並選取 [Web 應用程式]。 然後選取「ASP.NET + Azure 搜尋」範本開始進行。

<!---HONumber=AcomDC_0114_2016-->