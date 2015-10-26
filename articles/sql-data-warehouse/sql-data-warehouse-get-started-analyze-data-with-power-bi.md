<properties
    pageTitle="使用 Power BI 分析 SQL 資料倉儲資料 | Microsoft Azure"
    description="使用 Power BI 分析 SQL 資料倉儲資料"
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
    ms.date="10/07/2015"
    ms.author="lodipalm"/>

# 使用 Power BI 分析資料
本教學課程會示範如何使用 Power BI 來連接到 SQL 資料倉儲，並建立一些基本的視覺效果。

> [AZURE.NOTE]若要完成本教學課程，您需要隨著 AdventureWorksDW 範例資料庫預先載入的 SQL 資料倉儲資料庫。[建立 SQL 資料倉儲](sql-data-warehouse-get-started-provision.md)示範如何建立一個資料倉儲。
> 
> 如果您已經有 SQL 資料倉儲資料庫但沒有範例資料，您可以[手動載入範例資料][]。


## 連接到 SQL 資料倉儲資料庫

若要開啟 Power BI 並連接到您的 AdventureWorksDW 資料庫：

1. 登入 [Azure Preview 入口網站][]。
2. 按一下 [SQL 資料庫]，並選擇您的 AdventureWorks SQL 資料倉儲資料庫。 

    ![尋找您的資料庫][1]

3. 按一下 [在 Power BI 中開啟] 按鈕。

    ![Power BI 按鈕][2]

4. 您現在應該會看到顯示資料庫資訊的 SQL 資料倉儲連接頁面。輸入您的密碼，並且會將您完全連接到 SQL 資料倉儲資料庫。


## 使用 Power BI 分析資料

您現在已準備好使用 Power BI 來分析 AdventureWorksDW 範例資料。為了執行分析，AdventureWorksDW 有一個稱為 AggregateSales 的檢視。這個檢視包含用來分析公司銷售的一些重要指標。

1. 若要建立根據郵遞區號產生銷售金額的對應圖，請按一下 AggregateSales 檢視以展開它，然後按一下 PostalCode 和 SalesAmount 資料行。Power BI 會自動將此辨識為地理資料，並將它放入地圖中。

    ![Power BI 對應圖][3]

2. 若要建立銷售長條圖，只要按一下 SalesAmount 資料行。若要加入詳細資料，請將 CustomerIncome 圖表拖曳到 AggregateSale 左邊的 Axis 欄位，來依客戶收入顯示銷售業績。

    ![Power BI 長條圖][4]

3. 若要建立銷售時間軸，請按一下 [SalesAmount]、[OrderDate] 和 [折線圖]，這是在 [視覺效果] 下第二行中的第一個圖示。

    ![Power BI 折線圖][5]

您隨時都可以按一下左下角的 [儲存] 按鈕並將您的視覺效果儲存為報告，藉以儲存您的進度。

## 後續步驟
既然我們已經提供您一些時間，讓您利用範例資料進入狀況，接著請查看如何進行[開發][]、[載入][]或[移轉][]。

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-find-database.png
[2]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-button.png
[3]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-map.png
[4]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-bar.png
[5]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-line.png

<!--Article references-->
[移轉]: sql-data-warehouse-overview-migrate.md
[開發]: sql-data-warehouse-overview-develop.md
[載入]: sql-data-warehouse-overview-load.md
[手動載入範例資料]: sql-data-warehouse-get-started-manually-load-samples.md
[Azure Preview 入口網站]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO3-->