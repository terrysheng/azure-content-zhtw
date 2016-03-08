<properties
    pageTitle="使用 REST API 建立 Azure 搜尋服務索引 | Microsoft Azure | 雲端託管搜尋服務"
    description="使用 Azure 搜尋服務 HTTP REST API 在程式碼中建立索引。"
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="02/29/2016"
    ms.author="ashmaka"/>

# 使用 REST API 建立 Azure 搜尋服務索引
> [AZURE.SELECTOR]
- [概觀](search-what-is-an-index.md)
- [入口網站](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)


本文將逐步引導您完成使用 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 建立 Azure 搜尋服務[索引](https://msdn.microsoft.com/library/azure/dn798941.aspx)的程序。若要使用 REST API 建立 Azure 搜尋服務索引，您將會發出單一 HTTP POST 要求到 Azure 搜尋服務的 URL 端點。索引定義會以正確格式之 JSON 內容的形式包含在要求主體中。

在按照本指南進行並建立索引錢，請先[建立好 Azure 搜尋服務](search-create-service-portal.md)。

## I.識別 Azure 搜尋服務的系統管理 API 金鑰
既然您已佈建好 Azure 搜尋服務，您接著可以針對使用 REST API 的服務 URL 端點發出 HTTP 要求。不過，*所有* API 要求都必須包含針對您佈建的搜尋服務所產生的 API 金鑰。擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

1. 若要尋找服務的 API 金鑰，您必須登入 [Azure 入口網站](https://portal.azure.com/)。
2. 前往 Azure 搜尋服務的刀鋒視窗。
3. 按一下 [金鑰] 圖示。

服務會有*系統管理金鑰*和*查詢金鑰*。
  * 主要和次要*系統管理金鑰*會授與所有作業的完整權限，包括管理服務以及建立和刪除索引、索引子與資料來源的能力。由於有兩個金鑰，因此如果您決定重新產生主要金鑰，您可以繼續使用次要金鑰，反之亦然。
  * *查詢金鑰*會授與索引和文件的唯讀存取權，且通常會分派給發出搜尋要求的用戶端應用程式。

主要或次要系統管理金鑰都可用於建立索引。

## II.使用正確格式的 JSON 定義 Azure 搜尋服務索引
針對服務提出單一 HTTP POST 要求就會建立索引。HTTP POST 要求主體會包含單一 JSON 物件以定義 Azure 搜尋服務索引。
1. 此 JSON 物件的第一個屬性是索引名稱。
2. 此 JSON 物件的第二個屬性是名為 `fields` 的 JSON 陣列，其針對索引中的每個欄位各包含一個 JSON 物件。每個 JSON 物件都針對每個欄位屬性 (包括 [名稱]、[類型] 等) 包含多個名稱/值組。

由於必須為每個欄位指派[適當屬性](https://msdn.microsoft.com/library/azure/dn798941.aspx)，因此在設計索引時，請務必牢記搜尋服務使用者體驗和商務需求。這些屬性可控制要對哪些欄位套用哪些搜尋功能 (篩選、面向設定、排序全文檢索搜尋等)。對於未指定的屬性，除非您明確停用，否則其預設值是啟用對應的搜尋功能。

在我們的範例中，我們已將索引命名為 "hotels"，並將欄位定義如下：

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

我們已根據欄位在應用程式中的可能使用方式仔細選擇其各自的索引屬性。例如，`hotelId` 是搜尋飯店的使用者可能不會知道的唯一索引鍵，因此我們將 `searchable` 設定為 `false` 以停用該欄位的全文檢索搜尋，以節省索引中的空間。

請注意，`Edm.String` 類型的索引中必須有一個欄位 (且只有一個) 指定為 'key' 欄位。

`description_fr` 欄位會用來儲存法文文字，因此上述索引定義會對此欄位使用自訂語言分析器。如需語言分析器的詳細資訊，請參閱 [MSDN 上的語言支援主題](https://msdn.microsoft.com/library/azure/dn879793.aspx)以及對應的[部落格文章](https://azure.microsoft.com/blog/language-support-in-azure-search/)。

## III.發出 HTTP 要求
1. 請使用索引定義做為要求主體來對 Azure 搜尋服務端點 URL 發出 HTTP POST 要求。在 URL 中，請務必使用服務名稱做為主機名稱，然後放置適當的 `api-version` 做為查詢字串參數 (本文件發行時的最新 API 版本是 `2015-02-28`)。
2. 在要求標頭中，指定 `Content-Type` 做為 `application/json`。您也必須提供您在步驟 I 中於 `api-key` 標頭所識別的服務系統管理金鑰。


    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28 Content-Type: application/json api-key: [api-key]

若要求成功，您應該會看到狀態碼 201 (已建立)。如需透過 REST API 建立索引的詳細資訊，請瀏覽 [MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx) 上的 API 參考資料。如需失敗時可能傳回的其他 HTTP 狀態碼的詳細資訊，請參閱[這篇文章](https://msdn.microsoft.com/library/azure/dn798925.aspx)。

索引已使用完畢而想要將其刪除時，請直接發出 HTTP DELETE 要求。例如，以下是我們刪除 "hotels" 索引的方式：

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2015-02-28
    api-key: [api-key]

## 下一步
建立好 Azure 搜尋服務索引後，您就可以上傳內容到索引以便開始搜尋資料。

<!---HONumber=AcomDC_0302_2016-->