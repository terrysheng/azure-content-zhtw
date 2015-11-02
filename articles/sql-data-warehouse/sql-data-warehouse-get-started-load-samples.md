<properties
   pageTitle="將範例資料載入 SQL 資料倉儲 | Microsoft Azure"
   description="了解如何將範例資料載入 SQL 資料倉儲"
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
   ms.date="10/21/2015"
   ms.author="lodipalm;barbkess"/>

# 將範例資料載入 SQL 資料倉儲

當您建立 SQL 資料倉儲執行個體時，可以輕鬆地將某些範例資料載入其中。如果您在佈建期間錯過這個步驟，也可以[手動載入範例資料][]。

以下將提供如何在資料庫中載入 AdventureWorksDW 的簡單檢視。此資料集是以虛構公司 (稱為 AdventureWorks) 的範例資料倉儲結構為其模型，其中包含代表該公司之銷售與客戶的資料。

## 在建立期間加入範例資料
您可以使用下列步驟，確保可在部署期間，將範例資料載入 SQL 資料倉儲：

1. 在建立程序一開始，請先在 [Azure 入口網站][]中依序按一下 [+ 新增] 和 [資料和儲存體]，或者在 Marketplace 中搜尋「SQL 資料倉儲」，來尋找 SQL 資料倉儲。 
 
2. 一旦啟動程序之後，請務必按一下 [選取來源] 選項並將它設定為「範例」。如果您正在建立的並非新的伺服器，系統也會要求您針對正用來建立的伺服器提供登入。


> [AZURE.NOTE]若要將範例資料載入您的執行個體，您必須啟用 Azure 服務來存取伺服器 (在建立新的伺服器時，應該預設會啟用此項)。若未執行此動作，則載入將會失敗，但您仍能[手動載入範例資料][]。


## 使用 Power BI 分析 Adventureworks

使用範例資料集是開始使用 Power BI 的好方法。載入範例資料之後，您可以開啟與 SQL 資料倉儲的連線，方法是按一下 Azure 入口網站中的 [在 Power BI 中開啟] 按鈕，或者移至 [Power BI][] 並[連線到 SQL 資料倉儲][]。連接之後，應該會使用與您的資料倉儲相同的名稱來建立新的資料集。為了讓分析更容易，我們建立了名為 'AggregateSales' 的檢視，以及數個可用來分析公司銷售的關鍵衡量標準。您可以按一下此檢視的名稱來展開檢視，並查看其中包含的資料行，而且可遵循下列步驟來建立一些快速的視覺效果：

1. 一開始，我們可以按一下 [PostalCode] 和 [SalesAmount] 資料行，輕鬆地建立所有銷售的地圖。Power BI 甚至會自動將此辨識為地理資料，並將它放入地圖中。 

2. 現在，如果您想要建立銷售橫條圖，只要按一下 [SalesAmount] 資料行，Power BI 將會為您自動建立。您可藉由將 [CustomerIncome] 圖表拖曳到 [AggregateSales] 左邊的 [軸] 欄位來增加額外的深度，以便依客戶營收等級顯示銷售業績。

3. 最後，如果您想要建立銷售時間軸，只需按一下 [SalesAmount]、[OrderDate] 和 [折線圖] ([視覺效果] 下第二行中的第一個圖示) 即可。

您隨時都可以按一下左下角的 [儲存] 按鈕並將您的視覺效果儲存為報告，藉以儲存您的進度。

## 連接並查詢您的範例

您也可以利用傳統的方式來分析範例資料。如[連接及查詢][]文件中所述，您可以使用 Visual Studio 中的 SQL Server Data Tools 連接到這個資料庫。既然您已經將某些範例資料載入您的 SQL 資料倉儲中，就可以快速地執行幾個查詢以開始。

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
[移轉]: ./sql-data-warehouse-overview-migrate.md
[開發]: ./sql-data-warehouse-overview-develop.md
[載入]: ./sql-data-warehouse-overview-load.md
[連接及查詢]: ./sql-data-warehouse-get-started-connect.md
[移轉程式碼]: ./sql-data-warehouse-migrate-code.md
[手動載入範例資料]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Azure 入口網站]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[連線到 SQL 資料倉儲]: ./sql-data-warehouse-integrate-power-bi.md

<!--MSDN references-->
[Microsoft Command Line Utilities for SQL Server]: http://www.microsoft.com/download/details.aspx?id=36433/

<!--Other Web references-->
[Sample Data Scripts]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip/

<!---HONumber=Oct15_HO4-->