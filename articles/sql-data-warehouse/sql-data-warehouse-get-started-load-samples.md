<properties
   pageTitle="將範例資料載入 SQL 資料倉儲 | Microsoft Azure"
	description="將範例資料載入 SQL 資料倉儲"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""/>

<tags
   ms.service="sql-data-warehouse"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-services"
	ms.date="08/05/2015"
	ms.author="lodipalm;barbkess"/>

#將範例資料載入 SQL 資料倉儲

既然您已經安裝 SQL 資料倉儲執行個體，就可以輕鬆地將某些範例資料載入其中。以下資訊將協助您在資料庫中建立一個稱為 AdventureWorksPDW2012 的資料集。此資料集以虛構公司 (稱為 AdventureWorks) 的範例資料倉儲結構為其模型。請注意，您將必須已安裝 BCP，才能進行下列步驟。如果目前沒有安裝 BCP，請安裝[適用於 SQL Server 的 Microsoft 命令列公用程式][]。

1. 若要開始，請按一下以下載我們的[範例資料指令碼][]。

2. 下載檔案之後，請解壓縮 AdventureWorksPDW2012.zip 檔案的內容，並開啟新的 AdventureWorksPDW2012 資料夾。

3. 編輯 aw\_create.bat 檔案，並在檔案頂端設定下列值：

   a.**伺服器**：SQL 資料倉儲所在之伺服器的完整名稱

   b.**使用者**：上述伺服器的使用者
   
   c.**密碼**：用於登入所提供之伺服器的密碼
   
   d.**資料庫**：您想要載入資料至其中之 SQL 資料倉儲執行個體的名稱
   
   確保 '=' 和這些參數之間沒有空格。
   

4. 從其所在目錄執行 aw\_create.bat。這會建立結構描述，並使用 BCP 將資料載入至所有資料表。


## 連接並查詢您的範例

如[連接及查詢][]文件中所述，您可以使用 Visual Studio 和 SSDT 連接到這個資料庫。既然您已經將某些範例資料載入您的 SQL 資料倉儲中，就可以快速地執行幾個查詢以開始。

我們可以執行簡單的 select 陳述式，來取得員工的所有資訊：

	SELECT * FROM DimEmployee;

我們也可以使用建構 (例如 GROUP BY) 執行更複雜的查詢， 來查看每一天所有銷售的總金額：

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

我們甚至可以使用 WHERE 子句，來篩選出特定日期之前的訂單：

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

事實上，SQL 資料倉儲幾乎支援 SQL Server 支援的所有 T-SQL 建構，而且您可以在我們的[移轉程式碼][]文件中找到一些差異。

## 後續步驟
既然我們已經提供您一些時間，讓您利用範例資料進入狀況，接著請查看如何[開發][]、[載入][]或[移轉][]。

<!--Image references-->

<!--Article references-->
[移轉]: https://azure.microsoft.com/zh-TW/documentation/articles/sql-data-warehouse-overview-migrate/
[開發]: https://azure.microsoft.com/zh-TW/documentation/articles/sql-data-warehouse-overview-develop/
[載入]: https://azure.microsoft.com/zh-TW/documentation/articles/sql-data-warehouse-overview-load/
[連接及查詢]: https://azure.microsoft.com/zh-TW/documentation/articles/sql-data-warehouse-get-started-connect-query/
[移轉程式碼]: https://azure.microsoft.com/zh-TW/documentation/articles/sql-data-warehouse-migrate-code/

<!--MSDN references-->
[適用於 SQL Server 的 Microsoft 命令列公用程式]: http://www.microsoft.com/zh-TW/download/details.aspx?id=36433

<!--Other Web references-->
[範例資料指令碼]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip

<!---HONumber=August15_HO9-->