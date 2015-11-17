<properties
	pageTitle="使用 REST API 建立 Azure 搜尋服務索引 | Microsoft Azure | 雲端託管搜尋服務"
	description="使用 Azure 搜尋服務和 HTTP REST API 在程式碼中建立索引。"
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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# 使用 REST API 建立 Azure 搜尋服務索引
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

本文將說明如何使用 [Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 建立索引。下列內容部分是來自[建立索引 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx)。請參閱上層文章中，以取得更多內容。

建立索引的必要條件包括先前已建立您的搜尋服務連線，通常是透過 'httpClient' 完成。

透過 REST API 建立索引的 3 部分法包含設定連線、提供索引結構描述，然後執行可建立索引的命令。程式碼片段是來自[評分設定檔範例](search-get-started-scoring-profiles.md)。

##定義索引結構描述

REST API 可接受 JSON 格式的要求。建立結構描述的方法之一是將獨立結構描述檔案納入為 JSON 文件。下列範例提供相關說明。

在衍生此程式碼片段的範例中，下列 JSON 會儲存在名為 schema.json 的檔案中。

	{
	    "name": "musicstoreindex",
	    "fields": [
	        { "name": "key", "type": "Edm.String", "key": true },
	        { "name": "albumTitle", "type": "Edm.String"},
	        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
	        { "name": "genre", "type": "Edm.String" },
	        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
	        { "name": "artistName", "type": "Edm.String"},
	        { "name": "orderableOnline", "type": "Edm.Boolean" },
	        { "name": "rating", "type": "Edm.Int32" },
	        { "name": "tags", "type": "Collection(Edm.String)" },
	        { "name": "price", "type": "Edm.Double", "filterable": false },
	        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
	        { "name": "inventory", "type": "Edm.Int32" },
	        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
	    ],
	    "scoringProfiles": [
	        {
	            "name": "boostGenre",
	            "text": {
	                "weights": {
	                    "albumTitle": 5,
	                    "genre": 50,
	                    "artistName": 5
	                }
	            }
	        },
	        {
	            "name": "newAndHighlyRated",
	            "functions": [
	                {
	                    "type": "freshness",
	                    "fieldName": "lastUpdated",
	                    "boost": 10,
	                    "interpolation": "quadratic",
	                    "freshness": {
	                        "boostingDuration": "P365D"
	                    }
	                },
	                {
	                    "type": "magnitude",
	                    "fieldName": "rating",
	                    "boost": 10,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 1,
	                        "boostingRangeEnd": 5,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        },
	        {
	            "name": "boostMargin",
	            "functions": [
	
	                {
	                    "type": "magnitude",
	                    "fieldName": "margin",
	                    "boost": 50,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 50,
	                        "boostingRangeEnd": 100,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        }
	    ]
	}

##設定 HTTP 要求來連接和建立索引

REST API 會在每個要求上制定 HTTP 連線。每個要求一律規定服務 URL、您使用哪個版本的 API，以及授與服務中讀取-寫入作業的管理金鑰 (以下稱為主索引鍵，因為這是索引鍵在入口網站中的命名方式)。

程式碼片段會使用靜態值當作輸入，包含索引的名稱 (如 app.config 檔案中指定 (未顯示))。以此方式建立範例，讓程式碼保持簡單。

        private const string ApiVersion = "api-version=2015-02-28";
        private static string serviceUrl = ConfigurationManager.AppSettings["serviceUrl"];
        private static string indexName = ConfigurationManager.AppSettings["indexName"];
        private static string primaryKey = ConfigurationManager.AppSettings["primaryKey"];

在下列範例中，您可看見對服務 URL 的 HTTP PUT 要求以及 `api-version` 和要建立的索引名稱。

        static bool CreateIndex()
        {
            // Create an index using an index name
            Uri requestUri = new Uri(serviceUrl + indexName + "?" + ApiVersion);

            // Load the json containing the schema from an external file
            string json = File.ReadAllText("schema.json");

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PutAsync(requestUri,        // To create index use PUT
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.Created)   // For Posts we want to know response had no content
                {
                    Console.WriteLine("Index created. \r\n");
                    return true;
                }

                Console.WriteLine("Index creation failed: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->