<properties 
	pageTitle="開始使用 Azure 搜尋服務 | Microsoft Azure | 雲端託管搜尋服務" 
	description="開始使用 Azure 搜尋服務 (Microsoft Azure 上的雲端託管搜尋服務)。" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/11/2016" 
	ms.author="heidist"/>

# 開始使用 Azure Search

Microsoft Azure 搜尋服務是託管的雲端搜尋服務，可讓您將搜尋功能內嵌到自訂用程式。它可為您的資料提供搜尋引擎和儲存體，並讓您透過 .NET 檔案庫或 REST API 加以存取和管理。

本文章可讓您開始使用 Azure 搜尋服務 REST API。

.NET 開發人員的替代方式就是使用 Azure 搜尋服務 .NET SDK。如需詳細資訊，請參閱 [開始使用 .NET 中的 Azure 搜尋服務](search-get-started-dotnet.md)或[如何使用 Azure 搜尋服務 .NET SDK](search-howto-dotnet-sdk.md)。


> [AZURE.NOTE]若要完成本教學課程，需有 [Azure 訂用帳戶](../includes/free-trial-note.md)。如果尚未準備好註冊試用版訂用帳戶，則您可以略過本教學課程，並改為選擇 [試用 Azure App Service](https://tryappservice.azure.com/)。這個替代選項免費提供您 Azure 搜尋服務與 ASP.NET Web 應用程式 - 每個工作階段一小時 - 不需有訂用帳戶。
 
<a id="sub-1"></a>
## 建立 Azure 搜尋服務

身為管理員，當您選擇共用服務時，可以在現有訂用帳戶中免費新增搜尋服務，或是如果您選擇的是專用資源，則可以用較低的費率新增搜尋服務。

訂戶可自動且免費地存取共用的多租用戶搜尋服務，此服務可用於學習、概念證明測試或是小型開發搜尋專案。

使用您現有的訂用帳戶登入 [Azure 入口網站](https://portal.azure.com)。若要依照逐步指示進行，請參閱[在入口網站中建立 Azure 搜尋服務](search-create-service-portal.md)。

## 取得服務 URL 和 API 金鑰

建立服務之後，可以返回設定取得 URL 和 API 金鑰。若想要連接至 [搜尋服務]，您必須同時擁有 URL 和 API 金鑰以驗證呼叫。下面會說明如何快速找到這些值：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在導向列中，按一下 [搜尋服務] 列出為您的訂用帳戶佈建的所有 Azure 搜尋服務。
3. 選取您要使用的服務。
4.	在服務儀表板上，您會看到 [屬性] 和 [金鑰] 磚，以及可讓您立即清楚知道資源使用量的使用量資訊。 


<a id="sub-3"></a>
## 測試服務作業

測試您的服務是否可從用戶端應用程式運作並存取，做為驗證步驟。此程序使用 Fiddler (您可以[透過 Telerik 免費下載](http://www.telerik.com/fiddler)) 來發出 HTTP 要求和檢視回應。藉由使用 Fiddler，您無須撰寫任何程式碼就可立即測試 API。

在以下步驟中，您將建立索引、上傳文件、查詢索引，然後查詢系統以取得服務資訊。

### 建立索引

1. 啟動 Fiddler。在 [File (檔案)] 功能表上，關閉 [Capture Traffic (擷取流量)] 以隱藏與目前工作無關的 HTTP 活動。在 [Composer (編寫器)] 索引標籤上，您可以制定如下所示的要求： 

  	![][16]

2. 選取 [PUT]。

3. 輸入可指出服務 URL (可在 [Properties (屬性)] 頁面上找到)、要求屬性和 API 版本的完整 URL。請留意以下幾點：
   + 使用 HTTPS 作為首碼
   + 要求屬性為 "/indexes/hotels"。這可告知「搜尋」建立名為 'hotels' 的索引。
   + API 版本為小寫，並指定為 "?api-version=2015-02-28"。API 版本十分重要，因為 Azure 搜尋服務會定期部署更新。在極少數情況下，更新服務可能會對 API 造成中斷變更。使用 API 版本時，您可以先繼續使用現有版本，方便時再升級到較新的版本。

    完整 URL 應該會類似下列範例：

         https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.	指定要求標頭，使用服務的有效值取代主機和 API 金鑰。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

5.	在 [Request Body (要求本文)] 中，貼上構成索引定義的欄位。

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
          {"name": "hotelName", "type": "Edm.String", "suggestions": true},
          {"name": "category", "type": "Edm.String"},
          {"name": "tags", "type": "Collection(Edm.String)"},
          {"name": "parkingIncluded", "type": "Edm.Boolean"},
          {"name": "smokingAllowed", "type": "Edm.Boolean"},
          {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
          {"name": "rating", "type": "Edm.Int32"},
          {"name": "location", "type": "Edm.GeographyPoint"}
         ] 
        }

6.	按一下 [Execute (執行)]。

幾秒鐘後，您應該就會在工作階段清單中看見 HTTP 201 的回應，指出已成功建立索引。

如果您收到 HTTP 504，請確認 URL 所指定的是 HTTPS。如果您看見 HTTP 400 或 404，請查看要求本文以確認並沒有「複製-貼上」錯誤。HTTP 403 通常表示 API 金鑰有問題 (可能是金鑰無效或是用來指定 API 金鑰的語法有問題)。

### 載入文件

在 [Composer (編寫器)] 索引標籤上，張貼文件的要求看起來會像下面這樣。要求本文包含 4 間飯店的搜尋資料。

   ![][17]

1. 選取 [POST]。

2.	輸入以 HTTPS 開頭的 URL，並於後面依序加上服務 URL 和 "/indexes/<'indexname'>/docs/index?api-version=2015-02-28"。完整 URL 應該會類似下列範例：

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.	要求標頭應與之前的相同。請記住，您已使用您服務的有效值取代主機和 API 金鑰。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	[Request Body (要求本文)] 包含 4 個要新增到飯店索引的文件。

        {
        "value": [
        {
        	"@search.action": "upload",
        	"hotelId": "1",
        	"baseRate": 199.0,
        	"description": "Best hotel in town",
        	"hotelName": "Fancy Stay",
        	"category": "Luxury",
        	"tags": ["pool", "view", "wifi", "concierge"],
        	"parkingIncluded": false,
        	"smokingAllowed": false,
        	"lastRenovationDate": "2010-06-27T00:00:00Z",
        	"rating": 5,
        	"location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "2",
        	"baseRate": 79.99,
        	"description": "Cheapest hotel in town",
        	"hotelName": "Roach Motel",
        	"category": "Budget",
        	"tags": ["motel", "budget"],
        	"parkingIncluded": true,
        	"smokingAllowed": true,
        	"lastRenovationDate": "1982-04-28T00:00:00Z",
        	"rating": 1,
        	"location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "3",
        	"baseRate": 279.99,
        	"description": "Surprisingly expensive",
        	"hotelName": "Dew Drop Inn",
        	"category": "Bed and Breakfast",
        	"tags": ["charming", "quaint"],
        	"parkingIncluded": true,
        	"smokingAllowed": false,
        	"lastRenovationDate": null,
        	"rating": 4,
        	"location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "4",
        	"baseRate": 220.00,
        	"description": "This could be the one",
        	"hotelName": "A Hotel for Everyone",
        	"category": "Basic hotel",
        	"tags": ["pool", "wifi"],
        	"parkingIncluded": true,
        	"smokingAllowed": false,
        	"lastRenovationDate": null,
        	"rating": 4,
        	"location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
          }
         ]
        }

8.	按一下 [Execute (執行)]。

幾秒鐘後，您應該就會在工作階段清單中看見 HTTP 200 的回應。這表示已成功建立文件。如果您收到 207，表示至少有一個文件上傳失敗。如果您收到 404，則表示要求的標頭或本文有語法錯誤。

### 查詢索引

現在已載入索引和文件，您可以對其發出查詢。在 [Composer (編寫器)] 索引標籤上，查詢服務的 GET 命令看起來會像下面這樣：

   ![][18]

1.	選取 [GET]。

2.	輸入以 HTTPS 開頭，並於後面依序加上服務 URL、"/indexes/<'indexname'>/docs?" 和查詢參數的完整 URL。舉例來說，使用下列 URL，並以服務的有效值取代範例主機名稱。

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    此查詢會搜尋「motel」一字，並擷取評等的 Facet 類別。

3.	要求標頭應與之前的相同。請記住，您已使用您服務的有效值取代主機和 API 金鑰。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

回應碼應為 200，而回應的輸出看起來應該會類似下圖。
 
   ![][19]

下列範例查詢來自 MSDN 上的[搜尋索引作業 (Azure 搜尋服務 API)](http://msdn.microsoft.com/library/dn798927.aspx) (英文)。此主題中有許多範例查詢包含空格，這在 Fiddler 中是不允許的。因此，請先使用 + 字元取代空格，再貼到查詢字串中，然後再於 Fiddler 中嘗試該查詢：

**取代空格之前：**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**以 + 取代空格之後：**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28
### 查詢系統

您也可以查詢系統以取得文件計數和儲存體用量。在 [Composer (編寫器)] 索引標籤上，您的要求看起來會像下面這樣，而回應會傳回文件計數和空間的使用量。

   ![][20]

1.	選取 [GET]。

2.	輸入含有服務 URL 的 URL，並於後面加上 "/indexes/hotels/stats?api-version=2015-02-28"：

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28 

3.	指定要求標頭，使用服務的有效值取代主機和 API 金鑰。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	讓要求本文保持空白。

5.	按一下 [Execute (執行)]。您應該會在工作階段清單中看到 HTTP 200 的狀態碼。選取為命令張貼的項目。

6.	按一下 [Inspectors (檢測器)] 索引標籤 | [標頭]，然後選取 [JSON] 格式。您應該會看到文件計數和儲存體大小 (以 KB計算)。

 	![][21]

<a id="sub-4"></a>
## 瀏覽搜尋服務儀表板

如果您需要複習如何尋找設定頁面，請依循以下步驟尋找服務儀表板。

1.	使用您現有的訂用帳戶登入 [Azure 入口網站](https://portal.azure.com)。 
2.	按一下 [**首頁**]，然後按一下搜尋服務的磚。

 	![][22]

4.	按一下磚會開啟服務儀表板。請注意，[啟動]、[停止] 及 [刪除] 命令皆在頂端。

5.	請注意，服務 URL 是在頁面頂端附近。您需要此 URL 來連接到 Azure 搜尋服務。
	
7.	按一下 [**金鑰**] 圖示以檢視 API 金鑰。您需要系統管理金鑰來驗證服務。您可以使用主要或次要金鑰。(選擇性) 您可以建立查詢金鑰以進行服務的唯讀存取。


<!--Next steps and links -->
<a id="next-steps"></a>
## 立即試用

準備好前往下一個步驟了嗎？ 下列連結會引導您前往其他資料頁面，這些頁面會說明如何建置和管理使用 Azure 搜尋服務的搜尋應用程式。

- [建立 Azure 搜尋服務 GeoSearch 範例](search-create-geospatial.md)

- [在 Microsoft Azure 中管理搜尋方案](search-manage.md)

- [何謂 Azure 搜尋服務？](search-what-is-azure-search.md)

- [Azure 搜尋服務 REST API](http://msdn.microsoft.com/library/dn798935.aspx)

- [Azure 搜尋服務 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)

- [第 9 頻道影片：Azure 搜尋服務簡介](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)

- [第 9 頻道影片：Azure 搜尋服務和地理空間資料](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)

- [雲端報導第 152 集：在 Azure 搜尋服務中產生索引](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh)

<!--Anchors-->
[Start with the free service]: #sub-1
[Upgrade to standard search]: #sub-2
[Test service operations]: #sub-3
[Explore Search service dashboard]: #sub-4
[Try it out]: #next-steps

<!--Image references-->
[6]: ./media/search-get-started/AzureSearch_Configure1_1_New.PNG
[7]: ./media/search-get-started/AzureSearch_Configure1_2_Everything.PNG
[8]: ./media/search-get-started/Azuresearch_Configure1_3a_Gallery.PNG
[9]: ./media/search-get-started/AzureSearch_Configure1_4_GallerySeeAll.PNG
[10]: ./media/search-get-started/AzureSearch_Configure1_5_SearchTile.PNG
[11]: ./media/search-get-started/AzureSearch_Configure1_6_URL.PNG
[12]: ./media/search-get-started/AzureSearch_Configure1_7a_Free.PNG
[13]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[14]: ./media/search-get-started/AzureSearch_Configure1_9_Standard.PNG
[15]: ./media/search-get-started/AzureSearch_Configure1_10_ScaleUp.PNG
[16]: ./media/search-get-started/AzureSearch_Configure1_11_PUTIndex.PNG
[17]: ./media/search-get-started/AzureSearch_Configure1_12_POSTDocs.PNG
[18]: ./media/search-get-started/AzureSearch_Configure1_13_GETQuery.PNG
[19]: ./media/search-get-started/AzureSearch_Configure1_14_GETQueryResponse.PNG
[20]: ./media/search-get-started/AzureSearch_Configure1_15_Stats.PNG
[21]: ./media/search-get-started/AzureSearch_Configure1_16_StatsResponse.PNG
[22]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[23]: ./media/search-get-started/AzureSearch_Configure1_18_Explore.PNG


<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md

<!---HONumber=AcomDC_0114_2016-->