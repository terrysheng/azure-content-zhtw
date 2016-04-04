<properties 
	pageTitle="DocumentDB 的 NoSQL Python 範例 | Microsoft Azure" 
	description="在 Github 上尋找 DocumentDB 中的一般工作所適用的 NoSQL Python 範例，包括 NoSQL 資料庫中的 JSON 文件的 CRUD 作業。" 
	keywords="Python 範例"
	services="documentdb" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter="python"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/22/2016" 
	ms.author="ryancraw"/>


# DocumentDB Python 範例

> [AZURE.SELECTOR]
- [.NET 範例](documentdb-dotnet-samples.md)
- [Node.js 範例](documentdb-nodejs-samples.md)
- [Python 範例](documentdb-python-samples.md)
- [Azure 程式碼範例庫](https://azure.microsoft.com/documentation/samples/?service=documentdb)

[azure-documentdb-python](https://github.com/Azure/azure-documentdb-python/tree/master/samples) GitHub 儲存機制中包含可對 DocumentDB 資源執行 CRUD 作業和其他常見作業的範例解決方案。本文提供：

- 每個 Python 範例專案檔中各項工作的連結。 
- 相關 API 參考內容的連結。

**必要條件**

1. 您必須要有 Azure 帳戶才能使用這些 Python 範例：
    - 您可以[免費申請 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/) - 您將取得可試用付費 Azure 服務的額度，且即使在額度用完後，您仍可保留帳戶，並使用免費的 Azure 服務，例如「網站」。除非您明確變更您的設定且同意付費，否則我們將不會從您的信用卡收取任何費用。
   - 您可以[啟用 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)：您的 Visual Studio 訂用帳戶每個月都會提供額度，供您用在 Azure 付費服務。
2. 您也需要 [Python SDK](documentdb-sdk-python.md)。 

    > [AZURE.NOTE] 每個範例都各自獨立，自己設定，並自行清理。據此，這些範例對 [document\_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) 發出多個呼叫。每當執行此動作時，即會根據所建立之集合的效能層，對您的訂用帳戶計入一小時的使用量費用。

## 資料庫範例

[DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) 專案的 [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) 檔案說明如何執行下列工作。

工作 | API 參考資料
--- | ---
[建立資料庫](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) | [document\_client.CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[查詢帳戶中的資料庫](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) | [document\_client.QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[依識別碼讀取資料庫](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) | [document\_client.ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[列出帳戶的資料庫](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) | [document\_client.ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)
[刪除資料庫](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) | [document\_client.DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html)

## 集合範例 

[CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) 專案的 [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) 檔案說明如何執行下列工作。

工作 | API 參考資料
--- | ---
[建立集合](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) | [document\_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[讀取資料庫中所有集合的清單](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) | [document\_client.ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[依識別碼取得集合](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) | [document\_client.ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[取得集合的效能層](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) | [DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[變更集合的效能層](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) | [document\_client.ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)
[刪除集合](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) | [document\_client.DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection)

<!---HONumber=AcomDC_0323_2016-->