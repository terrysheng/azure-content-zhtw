<properties
	pageTitle="NoSQL 與 SQL 的使用時機 | Microsoft Azure"
	description="比較使用非關聯式 NoSQL 解決方案和 SQL 解決方案的優點。了解 Microsoft Azure 的 NoSQL 服務或 SQL Server 解決方案哪一個最符合您的案例。"
	keywords="nosql 與 sql, NoSQL 的使用時機, sql 與 nosql"
	services="documentdb"
	documentationCenter=""
	authors="mimig1"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="03/28/2016"
	ms.author="mimig"/>

# NoSQL 與 SQL

SQL Server 和關聯式資料庫 (RDBMS) 成為最佳的資料庫已經超過 20 年。不過，處理較多磁碟區和各種資料的急劇上升增加需求，已改變應用程式開發人員對於資料儲存體的需求的本質。為了達成這個目的，可以大規模儲存非結構化和異質資料的 NoSQL 資料庫大受歡迎。

NoSQL 是與 SQL Database 完全不同的資料庫類別。NoSQL 通常用來表示「不是 SQL」的資料管理系統，或包含「不僅 SQL」的資料管理的方法。NoSQL 類別中有一些技術，包括文件資料庫、鍵值存放區、資料行系列存放區和圖形資料庫，在遊戲、社交和 IoT 應用程式中相當熱門。

![NoSQL 與 SQL 概觀圖表示範常見案例和資料模型](./media/documentdb-nosql-vs-sql/nosql-vs-sql-overview.png)

本文的目標是協助您了解 NoSQL 和 SQL 之間的差異，並且提供您 Microsoft 的 NoSQL 和 SQL 供應項目的簡介。

## 使用 NoSQL 的時機？

假設您正在建置新的社交網站。使用者可以建立文章，並且在其中新增圖片、影片和音樂。其他使用者可以評論文章，並且給予點數 (喜歡) 以對文章評分。登陸頁面上會有使用者可以共用和互動的文章的意見反應。

那麼您要如何儲存此資料？ 如果您熟悉 SQL，您可以如下所示開始繪製︰

![NoSQL 與 SQL 圖表顯示社交網站的關聯式資料模型](./media/documentdb-nosql-vs-sql/nosql-vs-sql-social.png)

目前一切良好，但是現在請思考單一文章的結構以及如何顯示它。如果您想要在網站或應用程式上顯示文章和相關聯的影像、音訊、視訊、註解、點數和使用者資訊，您必須執行具有八個資料表聯結的查詢以擷取內容。現在假設文章的串流以動態方式載入，並出現在畫面上，您可以輕易地預測它即將需要數千筆查詢和許多聯結來完成工作。

現在您可以使用類似 SQL Server 的關聯式解決方案來儲存資料，但是還有另一個選項，可簡化方法的 NoSQL 選項。藉由將文章轉換為 JSON 文件，如下所示，並且將其儲存在 DocumentDB (Azure NoSQL 文件資料庫服務) 中，您可以增加效能以及使用一個查詢且沒有任何聯結來擷取整個文章。這個方法更簡單、更直接了當，並且會產生更好的效能。

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

此外，這個資料可以依據文章識別碼分割，讓資料在本質上相應放大，並且善用 NoSQL 級別的特性。NoSQL 系統也可以讓開發人員放寬一致性，並提供高可用性的應用程式。最後，此解決方案不需要開發人員定義、管理和維護資料層的結構描述，即可快速反覆。

然後您就可以使用其他 Azure 服務建置此解決方案︰

- [Azure 搜尋服務](https://azure.microsoft.com/services/search/)可以透過 Web 應用程式用來讓使用者搜尋文章。
- [Azure App Service](https://azure.microsoft.com/services/app-service/) 可以用來裝載應用程式和背景處理程序。
- [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/)可以用來儲存完整的使用者設定檔，包括影像。
- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 可以用來儲存大量的資料，例如登入資訊和使用情況分析資料。
- [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) 可以用來建置知識和智慧，可以提供意見反應給處理程序，並協助將正確的內容傳遞給正確的使用者。

此社交網站只是 NoSQL 資料庫是作業的正確資料模型的其中一個案例。如果您有興趣深入了解這個案例，以及如何在社交媒體應用程式中針對 DocumentDB 模型化資料，請參閱[使用 DocumentDB 進行社交活動](documentdb-social-media-apps.md)。

## NoSQL 與 SQL 的比較

下表比較 NoSQL 與 SQL 之間的主要差異。

![NoSQL 與 SQL 圖表顯示 NoSQL 和 SQL 的使用時機。SQL 與 NoSQL 的比較](./media/documentdb-nosql-vs-sql/nosql-vs-sql-comparison.png)

如果 NoSQL 資料庫最適合您的需求，繼續進行下一節以深入了解可從 Azure 取得的 NoSQL 服務。如果 SQL Database 最適合您的需求，則跳到 [Microsoft SQL 供應項目有哪些？](#what-are-the-microsoft-sql-offerings)

## Microsoft Azure NoSQL 供應項目有哪些？

Azure 有四個完全受管理的 NoSQL 服務︰

- [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)
- [Azure 資料表儲存體](https://azure.microsoft.com/services/storage/)
- [HDInsight 一部分的 Azure HBase](https://azure.microsoft.com/services/hdinsight/)
- [Azure Redis 快取](https://azure.microsoft.com/services/cache/)

以下比較圖表對應出每個服務的重要差異。哪一個最能精確描述應用程式的需求？

![NoSQL 與 SQL 圖表顯示 Microsoft Azure 的 NoSQL 供應項目的使用時機，包括 DocumentDB、表格儲存體、HDInsight 一部分的 HBase 和 Redis 快取](./media/documentdb-nosql-vs-sql/nosql-vs-sql-documentdb-storage-hbase-hdinsight-redis-cache.png)

如果其中一或多個服務符合您的應用程式的需求，使用下列資源進一步了解︰

- [DocumentDB 學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)和 [DocumentDB 使用案例](documentdb-use-cases.md)
- [開始使用 Azure 表格儲存體](../storage/storage-dotnet-how-to-use-tables.md)
- [HDInsight 中的 HBase 是什麼](../hdinsight/hdinsight-hbase-overview.md)
- [Redis 快取學習路徑](https://azure.microsoft.com/documentation/learning-paths/redis-cache/)

然後移至[後續步驟](#next-steps)以取得免費試用的資訊。

## Microsoft SQL 供應項目有哪些？

Microsoft 有五個 SQL 供應項目︰

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)
- [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
- [Azure SQL 資料倉儲 (預覽)](https://azure.microsoft.com/services/sql-data-warehouse/)
- [分析平台系統 (內部部署應用裝置)](https://www.microsoft.com/zh-TW/server-cloud/products/analytics-platform-system/)

如果您對於虛擬機器或 SQL Database 上的 SQL Server 有興趣，請參閱[選擇雲端 SQL Server 選項：Azure SQL (PaaS) Database 或 Azure VM 上的 SQL Server (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)，以深入了解兩者之間的差異。

如果 SQL 聽起來像是最佳的選項，則移至 [SQL Server](https://www.microsoft.com/server-cloud/products/) 以深入了解我們的 Microsoft SQL 產品和服務的供應項目。

然後移至[後續步驟](#next-steps)以取得免費試用和評估連結。

## 後續步驟

我們邀請您免費嘗試，進一步了解我們的 SQL 和 NoSQL 產品。

- 對於所有 Azure 服務，您可以註冊[一個月免費試用](https://azure.microsoft.com/pricing/free-trial/)，並且獲得可以在任何 Azure 服務上使用的美金 200 元獎金。
    - [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)
    - [HDInsight 一部分的 Azure HBase](https://azure.microsoft.com/services/hdinsight/)
    - [Azure Redis 快取](https://azure.microsoft.com/services/cache/)
    - [Azure SQL 資料倉儲 (預覽)](https://azure.microsoft.com/services/sql-data-warehouse/)
    - [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
    - [Azure 資料表儲存體](https://azure.microsoft.com/services/storage/)

- 您可以啟動[虛擬機器上的評估版 SQL Server 2016](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016ctp33evaluationwindowsserver2012r2/) 或下載[評估版 SQL Server](https://www.microsoft.com/zh-TW/evalcenter/evaluate-sql-server-2016)。
    - [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
    - [Azure 虛擬機器上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)

<!---HONumber=AcomDC_0330_2016-->