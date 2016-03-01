<properties 
   pageTitle="使用 Azure App Service 免費試用 Azure Search | Microsoft Azure"
   description="使用 Azure App Service 範本免費試用 Azure Search 長達一小時。"
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="search" 
   ms.date="02/21/2016"
   ms.author="heidist"/>

# 使用 Azure App Service 免費試用 Azure Search

[Try Azure App Service](https://tryappservice.azure.com/) 提供您一小時免費的瀏覽器式工作階段來試用 Azure 服務 (包括 **Azure 搜尋服務**)，且您不需註冊訂用帳戶。此網站可讓您從數個範本中選擇。當您選取包含 Azure 搜尋服務的 ASP.NET 範本時，您就能夠存取所選取服務支援的網站中所有的功能。

Azure 搜尋服務也是 **Try Azure App Service** 的一部分，且該服務已經建立並準備好接收搜尋查詢。您無法上傳或使用自己的索引或資料，但是您可以執行查詢，以及進行幾次程式碼變更來改造使用者經驗。

搜尋資料來自[美國地質調查局 (USGS)](http://geonames.usgs.gov/domestic/download_data.htm)，包含來自全美國大約 3 百萬個資料列的地標、歷史古蹟、建築和其他地標特色。

## 開始使用

如果您尚未開始您的 1 小時工作階段，請遵循下列步驟開始。

1. 以滑鼠右鍵按一下 [https://tryappservice.azure.com](https://tryappservice.azure.com/)，在新的瀏覽器視窗中開啟此網站。
2. 向下捲動以選取 [Web 應用程式]。 
2. 按 [下一步]。
3. 選擇 [ASP.NET + Azure Search 網站] 範本。

    ![][1]

4. 按一下 [建立]。
5. 選擇登入方法並提供使用者名稱和密碼。

    ![][2]

6. 請等候佈建網站。準備好時，您會看到類似以下的頁面。每個頁面會顯示正在執行的時鐘，好讓您隨時知道還剩下多少時間。

    ![][3]

7. 選擇 [使用 Visual Studio Team Services 進行編輯] 以檢視解決方案並瀏覽網站。
9. 在 Visual Studio Team Services 中，展開頁面頂端的工作階段選項，然後按一下 [瀏覽網站]。

    ![][4]

10. 您應該會看到 Azure 搜尋服務網站的開始使用頁面。按一下 [開始使用] 按鈕以開啟網站。

    ![][5]

11. ASP.NET 網站會在瀏覽器中開啟，提供搜尋方塊。輸入要搜尋的熟悉詞彙 (例如 *Yellowstone*) 或是知名的山 (例如 *Mount Rainier*)。從熟悉的地標開始以輕鬆評估結果。

    ![][6]


## 如何開始

由於搜尋索引可完全運作，良好的第一個步驟就是嘗試幾個查詢。Azure 搜尋服務支援所有標準搜尋運算子 (+、-、 |)、代表常值相符項目的引號、萬用字元 (*) 和優先順序運算子。您可以檢閱運算子完整清單的查詢語法參考。

- 加入星號 (`*`) 來開始萬用字元搜尋。這會告訴您在索引中找到多少文件：2,262,578。
- 接下來，輸入 "Yellowstone"，然後加入 "+ center"、"+building" 及 "-ND"，逐漸將搜尋結果範圍縮小成只有 Yellowstone 遊客中心，排除那些在 North Dakota 的結果：`Yellowstone +center +building -ND`。  
- 嘗試結合優先順序運算子與字串比對的搜尋片語：`statue+(park+MT)`。您應該會看到類似下列螢幕擷取畫面的結果。請注意，facet 類別會出現在功能類別之下，透過多面向導覽提供自我引導篩選，這是在大部分搜尋應用程式中經常發現的功能。

    ![][7]

準備好繼續進行了嗎？ 讓我們變更幾行程式碼，查看對全文檢索搜尋作業的影響。

## 變更 searchMode.All

Azure Search 具有可設定的 **searchMode** 屬性，可用來控制搜尋運算子行為。此屬性的有效值為 `Any` (預設值) 或 `All`。如需有關設定這些選項的詳細指引，請參閱[簡單的查詢語法](https://msdn.microsoft.com/library/dn798920.aspx)。

- **searchMode.Any** 規定與搜尋詞彙相符的任何項目都足以在搜尋結果中包含一個項目。如果您的搜尋片語是 `Yellowstone visitor center`，則任何包含這當中任何詞彙的文件都會包含在搜尋結果中。這種模式偏向「重新叫用」。
- 此範例中使用的 **searchModel.All** 會要求文件中必須要有所有指定的詞彙。這個模式比 **searchMode.Any** 更嚴苛，但是如果您偏好「精確度」勝過重新叫用，它可能會是您應用程式的理想選擇。 

> [AZURE.NOTE] **searchMode.Any** 在查詢建構大部分包含片語且使用最少量運算子時效果最佳。根據一般的經驗法則，搜尋消費者應用程式 (例如電子商務網站) 的人員傾向於只使用詞彙，而搜尋內容或資料的人員更有可能在搜尋片語中包含運算子。如果您認為搜尋可能會包括運算子 (尤其是 `NOT (-)` 運算子)，請以 **searchModel.All** 開始。對比之下，您的另一個選項 **searchMode.Any** 則會以 `OR` 將 `NOT` 運算子與其他搜尋詞彙連接，這會大幅擴充結果而不是削減結果。下列範例可協助您了解差異。

在這個工作中，您將變更 **searchMode**，並根據模式比較搜尋結果。

1. 開啟包含範例應用程式的瀏覽器視窗，選擇 [連接至 Visual Studio Team Services]。

    ![][8]

2. 開啟 **Search.cshtml**，尋找在第 39 行的 `searchMode.All`，並將它變更為 `searchMode.Any`。

    ![][9]

3. 在右邊的導向列中，按一下 [執行]。

    ![][10]
 
在重建的應用程式視窗中，輸入您先前使用過的搜尋詞彙 (例如 `Yellowstone +center +building -ND`)，並比較變更 **searchMode** 之前與之後的結果。

這是個很大的差異。您不會取得七個搜尋結果，而是取得超過兩百萬個。

   ![][11]
 
您目前觀察到的行為是由於包含 `NOT` 運算子 (在此案例中為 "-ND") 所造成，此運算子在 **searchMode** 設為 `Any` 時，是由 *OR* 而非 *AND* 連接。

在使用此組態的情況下，搜尋結果會包含搜尋詞彙 `Yellowstone`、`center` 和 `building` 的命中結果，而且每個 `NOT North Dakota` 文件也會包含在內。由於只有 13,081 個文件包含片語 `North Dakota`，因此幾乎傳回了所有的資料集。

無可否認地，這可能是罕見的案例，但是它也說明了 **searchMode** 對包含 `NOT` 運算子的搜尋片語的影響，因此有助於了解為什麼會發生此行為，以及如果這不是您想要的，要如何變更。

若要繼續進行本教學課程，請將 **searchMode** 還原回其原始值 (在第 39 行設為 `All`)、執行程式，然後使用重建的應用程式來進行剩餘的工作。
 
## 新增華盛頓州的全域篩選器

一般來說，如果您想要搜尋可用的資料子集，您會在匯入資料時，在資料來源設定篩選器。基於學習的目的，使用唯讀資料時，我們會在應用程式中設定篩選器，只傳回包含華盛頓州的文件。

1. 開啟 Search.cshtml，尋找 **SearchParameters** 程式碼區塊 (從第 36 行開始)，然後新增註解行再加上篩選。

        var sp = new SearchParameters
        {
            //Add a filter for _Washington State
            Filter = "STATE_ALPHA eq 'WA'",
            // Specify whether Any or All of the search terms 
            SearchMode = SearchMode.All,
            // Include a count of results in the query result
            IncludeTotalResultCount = true,
            // Return an aggregated count of feature classes based on the specified query
            Facets = new[] { "FEATURE_CLASS" },
            // Limit the results to 20 documents
            Top = 20
        };


篩選器可以使用 OData 語法來指定，並經常搭配多面向導覽使用或包含在限制查詢的查詢字串中。如需詳細資訊，請參閱 [OData 篩選語法](https://msdn.microsoft.com/library/azure/dn798921.aspx)。

2. 按一下 **[執行]**。

3. 開啟應用程式。

4. 輸入萬用字元 (*) 以傳回計數。請注意，結果現在會限制為 42,411 個項目，也就是華盛頓州中所有地理功能的所有文件。

   ![][12]

## 新增命中結果醒目提示

既然您進行了一連串的單行程式碼變更，您可能會想要嘗試更深層的修改，需要在多個位置中的程式碼變更。下列版本的 **Search.cshtml** 可以直接貼在您目前工作階段中的 Search.cshtml 檔案上。

下列程式碼會新增點閱數醒目提示。請注意 [SearchParameters](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchparameters_properties.aspx) 中宣告的新屬性。另外還有一個新的功能，可以逐一查看結果的集合，以取得需要醒目提示的文件，加上呈現結果的 HTML。

當您執行此程式碼範例時，在指定的欄位中有對應相符項目的搜尋詞彙輸入會以粗體字醒目提示。

   ![][14]

您可能會想要儲存一份原始 **Search.cshtml** 檔案的複本來查看這兩種版本的比較。

> [AZURE.NOTE] 已除去註解以減少檔案大小。
 
    @using System.Collections.Specialized
    @using System.Configuration
    @using Microsoft.Azure.Search
    @using Microsoft.Azure.Search.Models
    
    @{
    Layout = "~/_SiteLayout.cshtml";
    }
    
    @{
    // This modified search.cshtml file adds hit-highlighting
    
    string searchText = Request.Unvalidated["q"];
    string filterExpression = Request.Unvalidated["filter"];
    
    DocumentSearchResponse response = null;
    if (!string.IsNullOrEmpty(searchText))
    {
    searchText = searchText.Trim();
    
    // Retrieve search service name and an api key from configuration
    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
    
    var searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
    var indexClient = searchClient.Indexes.GetClient("geonames");
    
    // Set the Search parameters used when executing the search request
    var sp = new SearchParameters
    {
    // Specify whether Any or All of the search terms must be matched in order to count the document as a match.
    SearchMode = SearchMode.All,
    // Include a count of results in the query result
    IncludeTotalResultCount = true,
    // Return an aggregated count of feature classes based on the specified query
    Facets = new[] { "FEATURE_CLASS" },
    // Limit the results to 20 documents
    Top = 20,
    // Enable hit-highlighting
    HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
    HighlightPreTag = "<b>",
    HighlightPostTag = "</b>",
    };
    
    if (!string.IsNullOrEmpty(filterExpression))
    {
    // Add a search filter that will limit the search terms based on a specified expression.
    // This is often used with facets so that when a user selects a facet, the query is re-executed,
    // but limited based on the chosen facet value to further refine results
    sp.Filter = filterExpression;
    }
    
    // Execute the search query based on the specified search text and search parameters
    response = indexClient.Documents.Search(searchText, sp);
    }
    }
    
    @functions
    {
    private string GetFacetQueryString(string facetKey, string facetValue)
    {
    NameValueCollection queryString = HttpUtility.ParseQueryString(Request.Url.Query);
    queryString["filter"] = string.Format("{0} eq '{1}'", HttpUtility.UrlEncode(facetKey), HttpUtility.UrlEncode(facetValue));
    return queryString.ToString();
    }
    
    private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)
    {
    if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
    {
    string highlightedResult = string.Join("...", item.Highlights[fieldName]);
    return new HtmlString(highlightedResult);
    }
    return new HtmlString(item.Document[fieldName].ToString());
    }
    }
    
    <!-- Form to allow user to enter search terms -->
    <form class="form-inline" role="search">
    <div class="form-group">
    <input type="search" name="q" id="q" autocomplete="off" size="80" placeholder="Type something to search, i.e. 'park'." class="form-control" value="@searchText" />
    <button type="submit" id="search" class="btn btn-primary">Search</button>
    </div>
    </form>
    @if (response != null)
    {
    if (response.Count == 0)
    {
    <p style="padding-top:20px">No results found for "@searchText"</p>
    <h3>Suggestions:</h3>
    <ul>
    <li>Ensure words are spelled correctly.</li>
    <li>Try rephrasing keywords or using synonyms.</li>
    <li>Try less specific keywords.</li>
    </ul>
    }
    else
    {
    <div class="col-xs-3 col-md-2">
    <h3>Feature Class</h3>
    <ul class="list-unstyled">
    @if (!string.IsNullOrEmpty(filterExpression))
    {
    <li><a href="?q=@HttpUtility.UrlEncode(searchText)">All</a></li>
    }
    <!-- Cycle through the facet results and show the values and counts -->
    @foreach (var facet in response.Facets["FEATURE_CLASS"])
    {
    <li><a href="?@GetFacetQueryString("FEATURE_CLASS", (string)facet.Value)">@facet.Value (@facet.Count)</a></li>
    }
    </ul>
    </div>
    <div class="col-xs-12 col-sm-6 col-md-10">
    <p style="padding-top:20px">1 - @response.Results.Count of @response.Count results for "@searchText"</p>
    
    <ul class="list-unstyled">
    <!-- Cycle through the search results -->
    @foreach (var item in response.Results)
    {
    <li>
    <h3>@RenderHitHighlightedString(item, "FEATURE_NAME")</h3>
    <p>@RenderHitHighlightedString(item, "DESCRIPTION")</p>
    <p>@RenderHitHighlightedString(item, "FEATURE_CLASS"), elevation: @item.Document["ELEV_IN_M"] meters</p>
    <p>@RenderHitHighlightedString(item, "COUNTY_NAME") County, @RenderHitHighlightedString(item, "STATE_ALPHA")</p>
    <br />
    </li>
    }
    </ul>
    </div>
    }
    }

## 為什麼要選擇 Azure 搜尋服務？

雖然其他 Azure 服務，例如 SQL Database，提供全文檢索搜尋，但是類似 Azure 搜尋服務的服務可提供您調整控制項、分頁和計數；點閱數醒目提示、自動完成查詢建議、自然語言支援、多面向導覽、篩選以及更多功能。如同幾個[範例](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=search)所示範的，我們可以只使用 Azure Search 和 ASP.NET 來開發功能完整的搜尋型應用程式。

## 後續步驟

使用[試用 Azure App Service](https://tryappservice.azure.com/) 網站中提供的唯讀服務時，您已看到查詢語法和全文檢索搜尋如何運作、了解 searchMode 和篩選，並且將命中結果醒目提示新增至搜尋應用程式。在下一個步驟中，請考慮繼續建立並更新索引。這樣可以新增下列功能：

- [定義評分設定檔](https://msdn.microsoft.com/library/dn798928.aspx)：這些設定檔可用來微調搜尋分數，讓高值項目優先顯示。
- [定義建議工具](https://msdn.microsoft.com/library/mt131377.aspx)：這些建議工具可新增自動完成或預先輸入的查詢建議來回應使用者輸入。
- [定義索引子](https://msdn.microsoft.com/library/dn946891.aspx)：這些索引子可在資料來源是 Azure SQL Database 或 Azure DocumentDB 時，自動更新您的索引。

若要執行所有工作，您需要 Azure 訂用帳戶，讓您可以在服務中建立並填入索引。如需有關如何註冊免費試用的詳細資訊，請瀏覽 [https://azure.microsoft.com/pricing/free-trial](https://azure.microsoft.com/pricing/free-trial/)。

若要深入了解 Azure Search，請瀏覽 [http://azure.microsoft.com](https://azure.microsoft.com/) 上的[文件頁面](https://azure.microsoft.com/documentation/services/search/)，或查看任意數目的[範例和影片](search-video-demo-tutorial-list.md)以探索 Azure Search 的完整功能。

## 關於程式碼與 Azure 搜尋服務的事實

Azure Search 是一個完全受管理的「平台即服務」[PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) 服務，可讓開發人員將 Web 與行動應用程式中的絕佳搜尋體驗輕鬆整合。在搜尋作業中使用的資料會利用搜尋服務儲存在 Azure 中，其中資料和作業的鄰近性可確保低延遲性和一致的搜尋行為。深入探索這一點：

- 可搜尋的資料會儲存在由 Azure 搜尋服務管理的索引中。
- 定義索引的結構描述是由使用者定義，並會指定可搜尋的欄位，在篩選運算式中有用的非可搜尋欄位以及建構，例如微調結果的評分設定檔。
- 一個搜尋索引至少包含一個以上的文件 (類似資料表中的一個資料列)，而這些文件是可搜尋，也可擷取的。
- 大部分的索引都會從單一資料集載入，由您預先準備來包含僅在搜尋作業的內容中才有用的欄位。 
- 資料可以由索引子 (僅支援 Azure SQL Database 或 Azure DocumentDB) 自動載入或透過其中一個 Azure 搜尋服務 API 推送至搜尋索引。當您使用 API 時，您可以從任何資料來源推送資料，只要它是 JSON 格式即可。

在 [Azure App Service](https://tryappservice.azure.com/) 選項中，[ASP.NET + Azure Search 網站] 範本提供了 Web 應用程式的原始程式碼，可在 Visual Studio Team Services (在一小時的工作階段中可以使用) 中修改。檢視或變更程式碼不需要個別的開發工具。

程式碼是以 C# 撰寫，其中使用 [Azure Search .NET 用戶端程式庫](https://msdn.microsoft.com/library/dn951165.aspx)來針對索引執行查詢、提供多面向導覽，以及在網頁中顯示計數和搜尋結果。

其他程式碼 (不包含在範本中) 用來建置和載入 USGS 搜尋索引。因為服務是唯讀的，所以需要寫入存取權的所有作業都必須事先完成。您可以在本文結尾看到用來建置結構描述的[結構描述副本](#schema)。

<a name="Schema"></a>
## 關於結構描述

下列螢幕擷取畫面顯示用來建立此範本中所使用之索引的結構描述。
 
   ![][13]

### Schema.json 檔案

    {
      "@odata.context": "https://tryappservice.search.windows.net/$metadata#indexes/$entity",
      "name": "geonames",
      "fields": [
    {
      "name": "FEATURE_ID",
      "type": "Edm.String",
      "searchable": false,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": true,
      "analyzer": null
    },
    {
      "name": "FEATURE_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "FEATURE_CLASS",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_ALPHA",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_M",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_FT",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "MAP_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DESCRIPTION",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "HISTORY",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_CREATED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_EDITED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    }
      ],
      "scoringProfiles": [
    
      ],
      "defaultScoringProfile": null,
      "corsOptions": {
    "allowedOrigins": [
      "*"
    ],
    "maxAgeInSeconds": 300
      },
      "suggesters": [
    {
      "name": "AUTO_COMPLETE",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
    "FEATURE_NAME",
    "FEATURE_CLASS",
    "COUNTY_NAME",
    "DESCRIPTION"
      ]
    }
      ]
    }

<!--Image references-->
[1]: ./media/search-tryappservice/AzSearch-TryAppService-TemplateTile.png
[2]: ./media/search-tryappservice/AzSearch-TryAppService-LoginAccount.png
[3]: ./media/search-tryappservice/AzSearch-TryAppService-AppCreated.png
[4]: ./media/search-tryappservice/AzSearch-TryAppService-BrowseSite.png
[5]: ./media/search-tryappservice/AzSearch-TryAppService-GetStarted.png
[6]: ./media/search-tryappservice/AzSearch-TryAppService-QueryWA.png
[7]: ./media/search-tryappservice/AzSearch-TryAppService-QueryPrecedence.png
[8]: ./media/search-tryappservice/AzSearch-TryAppService-VSOConnect.png
[9]: ./media/search-tryappservice/AzSearch-TryAppService-cSharpSample.png
[10]: ./media/search-tryappservice/AzSearch-TryAppService-RunBtn.png
[11]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeany.png
[12]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeWAState.png
[13]: ./media/search-tryappservice/AzSearch-TryAppService-Schema.png
[14]: ./media/search-tryappservice/AzSearch-TryAppService-HitHighlight.png

<!---HONumber=AcomDC_0224_2016-->