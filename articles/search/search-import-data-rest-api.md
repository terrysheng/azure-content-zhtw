<properties
	pageTitle="使用 REST API 將資料匯入至 Azure 搜尋服務 | Microsoft Azure | 雲端託管搜尋服務"
	description="如何使用 REST API 將資料上傳至 Azure 搜尋服務中的索引"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# 使用 REST API 將資料匯入至 Azure 搜尋服務
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

本文將說明如何使用 [Azure 搜尋服務 .REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 填入索引。下列內容部分是來自 [新增、更新或刪除文件] (Azure 搜尋服務 REST API](https://msdn.microsoft.com/library/azure/dn798930.aspx)。請參閱上層文章中，以取得更多內容。

匯入的必要條件包括備妥用來接收資料的現有索引。

使用 REST API 時，資料擷取是以 Put 或 POST HTTP 要求為基礎。程式碼片段是來自[評分設定檔範例](search-get-started-scoring-profiles.md)。

        static bool PostDocuments(string fileName)
        {
            // Add some documents to the newly created index
            Uri requestUri = new Uri(serviceUrl + indexName + "/docs/index?" + ApiVersion);

            // Load the json containing the data from an external file
            string json = File.ReadAllText(fileName);

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PostAsync(requestUri,       // To add data use POST
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.OK)
                {
                    Console.WriteLine("Documents posted from file {0}. \r\n", fileName);
                    return true;
                }

                Console.WriteLine("Documents failed to upload: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->