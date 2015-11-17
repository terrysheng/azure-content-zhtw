<properties
	pageTitle="使用 REST 呼叫在 Azure 搜尋服務中建立查詢 | Microsoft Azure | 雲端託管搜尋服務"
	description="使用 .NET 程式庫或 SDK 在 Azure 搜尋服務中建立搜尋查詢，並使用搜尋參數來篩選、排序和 facet 搜尋結果。"
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
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/10/2015"
	ms.author="heidist"/>

#使用 REST 呼叫在 Azure 搜尋服務中建立查詢 
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

本文將說明如何使用 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 建構對索引的查詢。下列內容部分是來自[搜尋文件 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx)。請參閱上層文章中，以取得更多內容。

匯入的必要條件包括備妥現有的索引 (隨著提供可搜尋資料的文件一起載入)。

使用 REST API 時，根據 GET HTTP 要求查詢。程式碼片段是來自[評分設定檔範例](search-get-started-scoring-profiles.md)。

        static JObject ExecuteRequest(string action, string query = "")
        {
            // original:  string url = serviceUrl + indexName + "/" + action + "?" + ApiVersion; 
            string url = serviceUrl + indexName + "/docs?" + action ;
            if (!String.IsNullOrEmpty(query))
            {
                url += query + "&" + ApiVersion;
            }

            string response = ExecuteGetRequest(url);
            return JObject.Parse(response);

        }

        static string ExecuteGetRequest(string requestUri)
        {
            //This will execute a get request and return the response
            using (HttpClient client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.GetAsync(requestUri).Result;        // Searches are done over port 80 using Get
                return response.Content.ReadAsStringAsync().Result;
            }

        }

<!---HONumber=Nov15_HO3-->