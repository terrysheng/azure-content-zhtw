<properties title="開始使用 Azure Search" pageTitle="開始使用 Azure Search" description="開始使用 Azure Search" metaKeywords="" services="Azure Search" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang="" ms.workload="search" ms.topic="article" ms.tgt_pltfrm="" ms.date="09/23/2014" ms.author="heidist" />

# 開始使用 Azure Search

[WACOM.INCLUDE [本文使用 Azure 預覽入口網站](../includes/preview-portal-note.md)]

Microsoft Azure Search (公用預覽) 是一項可讓您將搜尋功能內嵌到自訂用程式中的新服務。它可為您的資料提供搜尋引擎和儲存體，並讓您透過 REST API 加以存取和管理。若想深入了解為何要使用 Azure Search 的原因，請參閱 [Azure Search 案例和功能][Azure Search 案例和功能]。

身為管理員，當您選擇共用服務時，可以在現有訂用帳戶中免費新增搜尋服務，或是如果您選擇的是專用資源，則可以用較低的費率新增搜尋服務。本文包含下列章節：

<!--Table of contents -->

-   [開始使用免費服務][開始使用免費服務]
-   [升級至標準搜尋][升級至標準搜尋]
-   [測試服務作業][測試服務作業]
-   [瀏覽搜尋服務儀表板][瀏覽搜尋服務儀表板]
-   [立即試用][立即試用]

## 開始使用免費服務

訂戶可自動且免費地存取共用的多租用戶搜尋服務，此服務可用於學習、概念證明測試或是小型開發搜尋專案。請使用下列步驟註冊免費版本。

1.  使用您現有的訂用帳戶登入 [Azure 預覽入口網站][Azure 預覽入口網站]。請注意，此 URL 會引導您前往預覽入口網站。您必須使用預覽入口網站。

2.  按一下頁面底部的 [新增]。

    ![][0]

3.  按一下頁面頂端的 [全部]。

    ![][1]

4.  從 [組件庫] 中，按一下 [Data, storage, cache, + backup] 。

    ![][2]

5.  按一下 [See All] 來展開所有資料相關服務的清單。

    ![][3]

6.  從 [資料服務] 中，按一下 [搜尋]。

    ![][4]

7.  按一下 [搜尋] 頁面底部的 [建立]。

8.  輸入要在服務 URL 中使用的小寫服務名稱，請避免使用連字號和空格，並且不要超過 15 個字元的字串限制。

    ![][5]

9.  按一下 [Pricing Tier] 中的箭頭，來選擇收費選項。選擇 [免費] 然後在頁面底部按一下 [選擇]。免費版本所提供的容量，已足夠用來試驗教學課程以及撰寫概念證明程式碼，但並不適用於生產應用程式。

    ![][6]

10. 按一下 [資源群組] 中的箭頭，選取現有群組或建立新的群組。資源群組是用於一般用途之服務和資源的容器。例如，如果您以 Azure 搜尋、Azure 網站和 Azure BLOB 儲存體為基礎建立自訂搜尋應用程式，那麼您可以建立資源群組，來將這些服務一起放在入口網站的管理頁面中。

11. 如果您有多個訂用帳戶，而且您想針對此搜尋服務使用不同的訂用帳戶，請按一下 [訂用帳戶] 中的箭頭。

12. 按一下 [位置] 中的箭頭，選擇資料中心的區域。在此預覽中，您可以選擇美國西部、美國東部、北歐及東南亞。之後有其他區域上線時，請選擇您要建立服務的區域。公開預覽並不支援將資源分散在多個資料中心的設定。

13. 按一下 [建立] 來佈建服務。請注意，只有在填入所有必要值之後，才會啟用 [建立]。

幾分鐘後，服務即建立完成。您可以回到組態設定以取得 URL 或 API 金鑰。若想要連接至 [搜尋服務]，您必須同時擁有 URL 和 API 金鑰以驗證呼叫。下面會說明如何快速找到這些值：

1.  前往 [瀏覽] | [全部] | [Data, storage, cache, + backup] | [檢視全部] | [搜尋服務]，按一下您的搜尋服務以開啟服務儀表板。

    ![][7]

2.  在服務儀表板上，您會看到 [屬性] 和 [金鑰] 磚，以及可讓您立即清楚知道資源使用量的使用量資訊。

    ![][8]

[屬性] 包含服務 URL。

[金鑰] 包含用於驗證的 API 金鑰。

[使用量] 顯示文件計數、可用資源及儲存體限制。

接著前往[測試服務作業][測試服務作業]，可取得如何使用這些值連接至服務的指示。

## 升級至標準搜尋

標準搜尋會為您取得 Azure 資料中心內只有您可使用的專用資源。搜尋的工作負載需要用到儲存體和服務複本。當您註冊標準搜尋時，您可以將服務設定最佳化，以使用更多對您的狀況來說最重要的任何資源。

具有專用資源可以帶給您更大的規模和更好的效能，但不會帶來其他功能。共用和標準搜尋提供的功能皆相同。

若要使用標準搜尋，請建立新的搜尋服務，並選擇 [標準] 價格層。請注意，升級指的並不是將免費版本就地升級。切換至標準版 (透過其調整規模的潛能) 需要用到新的服務。您將需要重新載入搜尋應用程式所使用的索引和文件。

設定專用資源可能需要一些時間 (15分鐘或更久)。

**步驟 1 - 建立價格層設為標準的新服務**

1.  使用您現有的訂用帳戶登入 [Azure 預覽入口網站][Azure 預覽入口網站]。

2.  按一下頁面底部的 [新增]。

3.  按一下頁面頂端的 [全部]。

4.  從 [組件庫] 中，按一下 [Data, storage, cache, + backup] 。

5.  按一下 [See All] 來展開所有資料相關服務的清單。

6.  從 [資料服務] 中，按一下 [搜尋]。

7.  按一下 [搜尋] 頁面底部的 [建立]。

8.  輸入要在服務 URL 中使用的小寫服務名稱，請避免使用連字號和空格，並且不要超過 15 個字元的字串限制。

9.  按一下 [Pricing Tier] 中的箭頭，來選擇收費選項。選擇 [標準] 然後在頁面底部按一下 [選擇]。

![][9]

**步驟 2 - 依據規模需求調整搜尋單位**

每個標準搜尋一開始會有一個複本和資料分割，但可輕鬆地在更高的資源階層中重新調整。

1.  建立好服務之後，請回到服務儀表板，按一下 [調整] 磚。

2.  使用滑桿新增副本和 (或) 資料分割。

額外的複本和資料分割會以搜尋單位計費。當您新增資源時，頁面上會顯示要支援任何特定資源設定所需要的搜尋單位總數。您可以查看[定價詳細資料][定價詳細資料]以取得每單位的計費資訊。

![][10]

## 測試服務作業

設定搜尋服務的最後一個步驟是，確認您的服務可否從用戶端應用程式上操作和存取。此程序使用 Fiddler (您可以[透過 Telerik 免費下載][透過 Telerik 免費下載]) 來發出 HTTP 要求和檢視回應。藉由使用 Fiddler，您無須撰寫任何程式碼就可立即測試 API。

下列程序在共用及標準搜尋中皆適用。在以下步驟中，您將建立索引、上傳文件、查詢索引，然後查詢系統以取得服務資訊。

### 建立索引

1.  啟動 Fiddler。在 [File (檔案)] 功能表上，關閉 [Capture Traffic (擷取流量)] 以隱藏與目前工作無關的 HTTP 活動。在 [Composer (編寫器)] 索引標籤上，您可以制定如下所示的要求：

    ![][11]

2.  選取 [PUT]。

3.  輸入可指出服務 URL (可在 [Properties (屬性)] 頁面上找到)、要求屬性和 API 版本的完整 URL。請留意以下幾點：

-   使用 HTTPS 作為首碼
-   要求屬性為 "/indexes/hotels"。這可告知「搜尋」建立名為 'hotels' 的索引。
-   API 版本為小寫，並指定為 "?api-version=2014-07-31-preview"。API 版本十分重要，因為 Azure 搜尋會定期部署更新。在極少數情況下，更新服務可能會對 API 造成中斷變更。使用 API 版本時，您可以先繼續使用現有版本，方便時再升級到較新的版本。

    完整 URL 應該會類似下列範例：

         https://my-app.search.windows.net/indexes/hotels?api-version=2014-07-31-Preview

1.  指定要求標頭，使用服務的有效值取代主機和 API 金鑰。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

2.  在 [Request Body (要求本文)] 中，貼上構成索引定義的欄位。

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true},
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

3.  按一下 [Execute (執行)]。

幾秒鐘後，您應該就會在工作階段清單中看見 HTTP 204 的回應，其指出已成功建立索引。

如果您收到 HTTP 504，請確認 URL 所指定的是 HTTPS。如果您看見 HTTP 400 或 404，請查看要求本文以確認並沒有「複製-貼上」錯誤。HTTP 403 通常表示 API 金鑰有問題 (可能是金鑰無效或是用來指定 API 金鑰的語法有問題)。

### 載入文件

在 [Composer (編寫器)] 索引標籤上，張貼文件的要求看起來會像下面這樣。要求本文包含 4 間飯店的搜尋資料。

![][12]

1.  選取 [POST]。

2.  輸入以 HTTPS 開頭，並於後面依序加上服務 URL 和 "/indexes/\<'indexname'\>/docs/index?api-version=2014-07-31-preview" 的完整 URL。完整 URL 應該會類似下列範例：

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2014-07-31-Preview

3.  要求標頭應與之前的相同。請記住，您已使用您服務的有效值取代主機和 API 金鑰。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  [Request Body (要求本文)] 包含 4 個要新增到飯店索引的文件。

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

5.  按一下 [Execute (執行)]。

幾秒鐘後，您應該就會在工作階段清單中看見 HTTP 200 的回應。這表示已成功建立文件。如果您收到 207，表示至少有一個文件上傳失敗。如果您收到 404，則表示要求的標頭或本文有語法錯誤。

### 查詢索引

現在已載入索引和文件，您可以對其發出查詢。在 [Composer (編寫器)] 索引標籤上，查詢服務的 GET 命令看起來會像下面這樣：

![][13]

1.  選取 [GET]。

2.  輸入以 HTTPS 開頭，並於後面依序加上服務 URL、"/indexes/\<'indexname'\>/docs?" 和查詢參數的完整 URL。舉例來說，使用下列 URL，並以服務的有效值取代範例主機名稱。

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2014-07-31-Preview

    此查詢會搜尋「motel」一字，並擷取評等的 Facet 類別。

3.  要求標頭應與之前的相同。請記住，您已使用您服務的有效值取代主機和 API 金鑰。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

回應碼應為 200，而回應的輸出看起來應該會類似下圖。

![][14]

下列範例查詢來自 MSDN 上的[搜尋索引作業 (Azure 搜尋 API)][搜尋索引作業 (Azure 搜尋 API)] (英文)。此主題中有許多範例查詢包含空格，這在 Fiddler 中是不允許的。因此，請先使用 + 字元取代空格，再貼到查詢字串中，然後再於 Fiddler 中嘗試該查詢：

**取代空格之前：**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-07-31-Preview

**以 + 取代空格之後：**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2014-07-31-Preview

### 查詢系統

您也可以查詢系統以取得文件計數和儲存體用量。在 [Composer (編寫器)] 索引標籤上，您的要求看起來會像下面這樣，而回應會傳回文件計數和空間的使用量。

![][15]

1.  選取 [GET]。

2.  輸入由服務 URL 和 "/indexes/hotels/stats?api-version=2014-07-31-Preview" 組成的完整 URL：

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2014-07-31-Preview 

3.  指定要求標頭，使用服務的有效值取代主機和 API 金鑰。

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  讓要求本文保持空白。

5.  按一下 [Execute (執行)]。您應該會在工作階段清單中看到 HTTP 200 的狀態碼。選取為命令張貼的項目。

6.  按一下 [Inspectors (檢測器)] 索引標籤 | [標頭]，然後選取 [JSON] 格式。您應該會看到文件計數和儲存體大小 (以 KB計算)。

    ![][16]

## 瀏覽搜尋服務儀表板

如果您需要複習如何尋找設定頁面，請依循以下步驟尋找服務儀表板。

1.  使用您現有的訂用帳戶登入 [Azure 預覽入口網站][Azure 預覽入口網站]。
2.  按一下 [瀏覽] | [全部]。

    ![][17]

3.  從清單中選擇 [搜尋服務] 。您應該會看到建立在您訂用帳戶下的所有搜尋服務清單。

4.  按一下其中一個服務開啟其儀表板。請注意，[啟動]、[停止] 及 [刪除] 命令皆在頂端。服務儀表板包含用來檢視 [屬性]、[金鑰] 及 [快速啟動] 的磚，並且有連結可查看相關資訊和指示。向下捲動以檢視 [使用量]。

5.  按一下 [屬性]。請注意，[屬性] 頁面會在右方開啟。服務 URL 位於頁面頂端。若要取得用來向服務進行驗證的 API 金鑰，請按一下 [金鑰]。

    ![][8]

<!--Next steps and links -->

## 立即試用

準備好前往下一個步驟了嗎？下列連結會引導您前往其他資料頁面，這些頁面會說明如何建置和管理使用 Azure 搜尋的搜尋應用程式。

-   [建立第一個 Azure 搜尋方案][建立第一個 Azure 搜尋方案]

-   [建立 Azure Search GeoSearch 範例][建立 Azure Search GeoSearch 範例]

-   [在 Microsoft Azure 中管理搜尋方案][在 Microsoft Azure 中管理搜尋方案]

-   [Azure 搜尋的技術概觀][Azure 搜尋的技術概觀]

-   [Azure 搜尋 REST API][Azure 搜尋 REST API]

-   [第 9 頻道視訊：Azure Search 簡介][第 9 頻道視訊：Azure Search 簡介]

-   [第 9 頻道視訊：Azure Search 和地理空間資料][第 9 頻道視訊：Azure Search 和地理空間資料]

-   [雲端報導第 152 集：在 Azure Search 中產生索引][雲端報導第 152 集：在 Azure Search 中產生索引]

<!--Anchors-->\r\n<!--Image references-->\r\n<!--Link references-->

  [Azure Search 案例和功能]: http://azure.microsoft.com/blog/2014/08/28/azure-search-scenarios-and-capabilities/
  [開始使用免費服務]: #sub-1
  [升級至標準搜尋]: #sub-2
  [測試服務作業]: #sub-3
  [瀏覽搜尋服務儀表板]: #sub-4
  [立即試用]: #next-steps
  [Azure 預覽入口網站]: https://portal.azure.com
  [0]: ./media/search-get-started/AzureSearch_Configure1_1_New.PNG
  [1]: ./media/search-get-started/AzureSearch_Configure1_2_Everything.PNG
  [2]: ./media/search-get-started/Azuresearch_Configure1_3_Gallery.PNG
  [3]: ./media/search-get-started/AzureSearch_Configure1_4_GallerySeeAll.PNG
  [4]: ./media/search-get-started/AzureSearch_Configure1_5_DataServicesList.PNG
  [5]: ./media/search-get-started/AzureSearch_Configure1_6_URL.PNG
  [6]: ./media/search-get-started/AzureSearch_Configure1_7_Free.PNG
  [7]: ./media/search-get-started/AzureSearch_Configure1_8_SearchServiceList.PNG
  [8]: ./media/search-get-started/AzureSearch_Configure1_18_Explore.PNG
  [9]: ./media/search-get-started/AzureSearch_Configure1_9_Standard.PNG
  [定價詳細資料]: http://go.microsoft.com/fwlink/p/?LinkID=509792
  [10]: ./media/search-get-started/AzureSearch_Configure1_10_ScaleUp.PNG
  [透過 Telerik 免費下載]: http://www.telerik.com/fiddler
  [11]: ./media/search-get-started/AzureSearch_Configure1_11_PUTIndex.PNG
  [12]: ./media/search-get-started/AzureSearch_Configure1_12_POSTDocs.PNG
  [13]: ./media/search-get-started/AzureSearch_Configure1_13_GETQuery.PNG
  [14]: ./media/search-get-started/AzureSearch_Configure1_14_GETQueryResponse.PNG
  [搜尋索引作業 (Azure 搜尋 API)]: http://msdn.microsoft.com/zh-tw/library/dn798927.aspx
  [15]: ./media/search-get-started/AzureSearch_Configure1_15_Stats.PNG
  [16]: ./media/search-get-started/AzureSearch_Configure1_16_StatsResponse.PNG
  [17]: ./media/search-get-started/AzureSearch_Configure1_17_BrowseEverything.PNG
  [建立第一個 Azure 搜尋方案]: ../search-create-first-solution/
  [建立 Azure Search GeoSearch 範例]: ../search-create-geospatial/
  [在 Microsoft Azure 中管理搜尋方案]: ../search-manage/
  [Azure 搜尋的技術概觀]: http://msdn.microsoft.com/zh-tw/library/dn798933.aspx
  [Azure 搜尋 REST API]: http://msdn.microsoft.com/zh-tw/library/dn798935.aspx
  [第 9 頻道視訊：Azure Search 簡介]: http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search
  [第 9 頻道視訊：Azure Search 和地理空間資料]: http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data
  [雲端報導第 152 集：在 Azure Search 中產生索引]: http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh
