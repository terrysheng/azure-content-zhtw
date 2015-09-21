<properties 
   pageTitle="具備 Azure 搜尋服務的 Try Azure App Service" 
   description="使用 TryAzureAppService 範本，免費試用 Azure 搜尋服務一小時。" 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search" 
   ms.date="07/13/2015"
   ms.author="heidist"/>

# 具備 Azure 搜尋服務的 Try Azure App Service

[Try Azure App Service](https://tryappservice.azure.com/) 是試用選取之 Azure 服務 (包括 Azure 搜尋服務) 的完全免費新方式，最多一個小時，且不需要 Azure 訂用帳戶註冊。

此網站可讓您從數個範本中選擇。當您選取包含 Azure 搜尋服務的 ASP.NET 範本時，您就能獲得一個小時存取功能完整的網站，由您所選取的服務所支援。您將無法更新或刪除任何由 Azure 搜尋服務管理的資料 – 但是您可以執行查詢，並進行任意數目的程式碼變更，以重塑使用者體驗。如果工作階段在您完成探索之前到期，您一律可以重新開始另一個工作階段，或者如果您的目標是要直接建立或載入索引，請移至試用版或完整的訂用帳戶。

在 [Try Azure App Service](https://tryappservice.azure.com/) 網站上，Azure 搜尋服務是 Web 應用程式範本的一部分 - 在 Azure 平台上提供豐富的全文檢索搜尋體驗，以及許多僅適用於此服務的搜尋中心功能。

雖然其他 Azure 服務，例如 SQL Database，提供全文檢索搜尋，但是類似 Azure 搜尋服務的服務可提供您調整控制項、分頁和計數；點閱數醒目提示、自動完成查詢建議、自然語言支援、多面向導覽、篩選以及更多功能。如同幾個[範例](https://github.com/AzureSearch)所示範的，我們可以只使用 Azure 搜尋服務和 ASP.NET 開發功能完整的搜尋型應用程式。

做為 [Try Azure App Service](https://tryappservice.azure.com/) 提供項目的一部分，您將使用的 Azure 搜尋服務是唯讀的 – 代表您將需要使用工作階段中提供的搜尋主體。您無法上傳或使用您自己的索引或資料。您將使用的資料是來自[美國地質調查局 (USG)]()，包含來自全美國大約 3 百萬個資料列的地標、歷史古蹟、建築和其他地標特色。

為了協助您充分運用一個小時的工作階段，下列指示將逐步引導您了解查詢和程式碼。

繼續進行之前，您可能要花幾分鐘的時間檢閱一些關於程式碼、服務和可搜尋資料的重點。如果您還不熟悉 Azure 搜尋服務，了解一些背景相當實用。

##關於程式碼與 Azure 搜尋服務的事實

Azure 搜尋服務是 service-plus-data [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) 供應項目，其中包含完全受管理的搜尋服務，再加上您使用未受限制之 Azure 搜尋服務的執行個體時上傳的可搜尋資料 (亦即，當您不使用 Try Azure App Service 選項)。

在搜尋作業中使用的資料會利用搜尋服務儲存在 Azure 中，其中資料和作業的鄰近性可確保低延遲性和一致的搜尋行為。目前不支援可搜尋資料的離線或遠端儲存體。深入探索這一點：

- 搜尋資料會儲存在由 Azure 搜尋服務管理、由文件填入的索引中，每個可搜尋向目都有一份文件。 
- 大部分的索引都會從單一資料集載入，由您預先準備來包含僅在搜尋作業的內容中才有用的欄位。 
- 定義索引的結構描述是由使用者定義，並會指定可搜尋的欄位，在篩選運算式中有用的非可搜尋欄位以及建構，例如微調結果的評分設定檔。
- 資料可以由索引子 (僅支援 Azure SQL Database 或 Azure DocumentDB) 自動載入或透過其中一個 Azure 搜尋服務 API 推送至搜尋索引。當您使用 API 時，您可以從任何資料來源推送資料，只要它是 JSON 格式即可。

在 [Try Azure App Service](https://tryappservice.azure.com/) 選項中，ASP.NET + Azure 搜尋服務網站範本提供 Web 應用程式的原始程式碼，可在 Visual Studio Online 中修改 (可做為一小時工作階段的一部分)。檢視或變更程式碼不需要個別的開發工具。

在 C# 中使用 [Azure 搜尋服務 .NET 用戶端程式庫](https://msdn.microsoft.com/library/dn951165.aspx)撰寫程式碼，以針對索引執行查詢，提供多面向導覽及顯示網頁中的計數和搜尋結果。

其他程式碼 (不包含在範本中) 用來建置和載入 USGS 搜尋索引。因為服務是唯讀的，所以需要寫入存取權的所有作業都必須事先完成。您可以在本文結尾看到用來建置結構描述的[結構描述副本](#schema)。

##開始使用

如果您尚未開始您的 1 小時工作階段，請遵循下列步驟開始。

1. 移至 [https://tryappservice.azure.com](https://tryappservice.azure.com/) 並向下捲動以選取 [**Web 應用程式**]。 
2. 按 [下一步]。
3. 選擇 **ASP.NET + Azure 搜尋服務網站**範本。

    ![][1]

4. 按一下 [建立]。
5. 選擇登入方法並提供使用者名稱和密碼。

    ![][2]

6. 請等候佈建網站。準備好時，您會看到類似以下的頁面。每個頁面會顯示正在執行的時鐘，好讓您隨時知道還剩下多少時間。

    ![][3]

7. 選擇**利用 Visual Studio Online 編輯**檢視解決方案並瀏覽網站。
9. 在 Visual Studio Online 中，展開在頁面頂端的工作階段選項，然後按一下 [**瀏覽網站**]。

    ![][4]

10. 您應該會看到 Azure 搜尋服務網站的開始使用頁面。按一下 [**開始使用**] 按鈕以開啟網站。

    ![][5]

11. ASP.NET 網站會在瀏覽器中開啟，提供搜尋方塊。輸入要搜尋的熟悉詞彙，例如 *Yellowstone* 或是知名的山 *Mount Rainier*。從熟悉的地標開始以輕鬆評估結果。

    ![][6]


##如何開始
由於搜尋索引可完全運作，良好的第一個步驟就是嘗試幾個查詢。Azure 搜尋服務支援所有標準搜尋運算子 (+、-、 |)、代表常值相符項目的引號、萬用字元 (*) 和優先順序運算子。您可以檢閱運算子完整清單的查詢語法參考。

- 藉由加入星號 (`*`) 開始萬用字元搜尋。這會告訴您在索引中找到多少文件：2,262,578。
- 接下來，輸入 "Yellowstone"，然後加入 "+ center" 和 "+building" 和 "-ND"，漸進式將搜尋結果縮小成只有 Yellowstone 訪客中心，排除那些在北達科他的結果：`Yellowstone +center +building -ND`。  
- 請嘗試搜尋片語，結合優先順序運算子和字串比對：`statue+(park+MT)`。您應該會看到類似下列螢幕擷取畫面的結果。請注意，facet 類別會出現在功能類別之下，透過多面向導覽提供自我引導篩選，這是在大部分搜尋應用程式中經常發現的功能。

    ![][7]

準備好繼續進行了嗎？ 讓我們變更幾行程式碼，查看對全文檢索搜尋作業的影響。

##變更 searchMode.All

Azure 搜尋服務具有可設定的 **searchMode** 屬性，可用來控制搜尋運算子行為。此屬性的有效值為 `Any` (預設值) 或 `All`。請參閱[簡單的查詢語法](https://msdn.microsoft.com/library/dn798920.aspx)以了解設定這些選項的詳細指引。

- **searchMode.Any**規定搜尋詞彙的任何相符項目都要足以在搜尋結果中包含一個項目。如果您的搜尋片語是 `Yellowstone visitor center`，則包含任何詞彙的任何文件都會包含在搜尋結果中。這種模式偏向*重新叫用*。
- 此範例中使用的 **searchModel.All** 需要所有指定之詞彙出現在文件中。這個模式會比 **searchMode.Any** 更嚴苛，但是如果您偏好*精確度*勝過重新叫用，它可能是您的應用程式理想選項。 

> [AZURE.NOTE]**searchMode.Any** 在查詢建構包含大部分片語且使用最少量運算子的時候效果最佳。根據一般的經驗法則，搜尋消費者應用程式 (例如電子商務網站) 的人員傾向於只使用詞彙，而搜尋內容或資料的人員更有可能在搜尋片語中包含運算子。如果您認為搜尋可能會包括運算子，尤其是 `NOT (-)` 運算子，請以 **searchModel.All** 開頭。相較之下，您的另一個選項 **searchMode.Any** 將會 `OR` `NOT` 運算子與其他搜尋詞彙，可大幅擴充結果而不是去除結果。下列範例可協助您了解差異。

在這個工作中，您將變更 **searchMode** 並根據模式比較搜尋結果。

1. 開啟包含範例應用程式的瀏覽器視窗，選擇 [**連接到 Visual Studio Online**]。

    ![][8]

2. 開啟 **Search.cshtml**，尋找 第 39 行上的 `searchMode.All`，並將它變更為 `searchMode.Any`。

    ![][9]

3. 在右邊的導向列中，按一下 [**執行**]。

    ![][10]
 
在重建應用程式視窗中，輸入您先前使用過的搜尋詞彙，例如 `Yellowstone +center +building -ND`，並比較變更 **searchMode** 之前與之後的結果。

這是個很大的差異。您不會取得七個搜尋結果，而是取得超過兩百萬個。

   ![][11]
 
您正在觀察的行為是因為包含 `NOT` 運算子 (在此案例中為 "-ND")，也就是在 **searchMode** 設為 `Any` 時為 *OR'd* 而非 *AND'd*。

若為此組態，搜尋結果會包含搜尋詞彙 `Yellowstone`、`center` 和 `building` 的叫用，而且也包含每個做為 `NOT North Dakota` 的文件。由於只含 13,081 個文件包含片語 `North Dakota`，所以幾乎傳回所有的資料集。

無可否認地，這可能是罕見的案例，但是它也說明了 **searchMode** 對包含 `NOT` 運算子的搜尋片語的影響，因此有助於了解為什麼會發生此行為，以及如果這不是您想要的，要如何變更。

若要繼續本教學課程，請將 **searchMode** 還原回其原始值 (設為第 39 行的 `All`)、執行程式，並且將重建應用程式用於其餘的工作。
 
##新增華盛頓州的全域篩選器

一般來說，如果您想要搜尋可用的資料子集，您會在匯入資料時，在資料來源設定篩選器。基於學習的目的，使用唯讀資料時，我們會在應用程式中設定篩選器，只傳回包含華盛頓州的文件。

1. 開啟 Search.cshtml，尋找**SearchParameters** 程式碼區塊 (從第 36 行開始)，並加入註解行以及篩選器。

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


篩選器可以使用 OData 語法來指定，並經常搭配多面向導覽使用或包含在限制查詢的查詢字串中。請參閱 [OData 篩選語法](https://msdn.microsoft.com/library/azure/dn798921.aspx)以取得需詳細資訊。

2. 按一下 **[執行]**。

3. 開啟應用程式。

4. 輸入萬用字元 (*) 以傳回計數。請注意，結果現在會限制為 42,411 個項目，也就是華盛頓州中所有地理功能的所有文件。

   ![][12]

##加入點閱數醒目提示

既然您進行了一連串的單行程式碼變更，您可能會想要嘗試更深層的修改，需要在多個位置中的程式碼變更。下列版本的 **Search.cshtml** 可以直接貼在目前工作階段中的 Search.cshtml 檔案上。

下列程式碼會新增點閱數醒目提示。請注意在 [SearchParameters](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchparameters_properties.aspx) 中宣告的新屬性。另外還有一個新的功能，可以逐一查看結果的集合，以取得需要醒目提示的文件，加上呈現結果的 HTML。

當您執行此程式碼範例時，在指定的欄位中有對應相符項目的搜尋詞彙輸入會以粗體字醒目提示。

   ![][14]

您可能會想要儲存一份原始 **Search.cshtml** 檔案的複本來查看這兩種版本的比較。

> [AZURE.NOTE]已除去註解以減少檔案大小。
 
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


##後續步驟

使用 [Try Azure App Service](https://tryappservice.azure.com/) 網站中提供的唯讀服務時，您已看過作用中的查詢語法和全文檢索搜尋，了解 searchMode 和篩選器，並且將點閱數醒目提示加入至搜尋應用程式。在下一個步驟中，請考慮繼續建立並更新索引。這樣可以新增下列功能：

- [定義評分設定檔](https://msdn.microsoft.com/library/dn798928.aspx)用來進行微調搜尋分數，這樣會先顯示高值項目。
- [定義建議工具](https://msdn.microsoft.com/library/mt131377.aspx)，加入自動完成或預先輸入查詢的建議以回應使用者輸入。
- [定義索引子](https://msdn.microsoft.com/library/dn946891.aspx)，在資料來源是 Azure SQL Database 或 Azure DocumentDB 時自動更新您的索引。

若要執行所有工作，您需要 Azure 訂用帳戶，讓您可以在服務中建立並填入索引。如需有關如何註冊免費試用版的詳細資訊，請造訪 [https://azure.microsoft.com/pricing/free-trial](https://azure.microsoft.com/pricing/free-trial/)。

若要深入了解 Azure 搜尋，請瀏覽 [http://azure.microsoft.com](http://azure.microsoft.com) 上的[文件頁面](http://azure.microsoft.com/documentation/services/search/)或查看任何[範例和影片](search-video-demo-tutorial-list.md)以探索 Azure 搜尋的完整功能。

<a name="Schema"></a>
##關於結構描述

下列螢幕擷取畫面顯示用來建立此範本中所使用之索引的結構描述。
 
   ![][13]

###Schema.json 檔案

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

<!---HONumber=Sept15_HO2-->