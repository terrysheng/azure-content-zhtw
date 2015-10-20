<properties
	pageTitle="使用 Excel 連接到 Azure SQL Database"
	description="Azure SQL Database 的 Excel 試算表，可報告和資料探索。"
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="jeffreyg"
	editor="jeffreyg"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/09/2015"
	ms.author="joseidz"/>


# 使用 Excel 連接到 Azure SQL Database

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

本文將說明如何將 Excel 連接到 Azure SQL 資料庫，以及建立資料庫資料報告的方式。您需要先有 SQL Database。如果您沒有，請參閱[建立您的第一個 SQL Database](sql-database-get-started.md) 以取得包含範例資料的資料庫，並執行幾分鐘的時間。本文是以該文章中的範例資料為基礎，但是您可以依照類似的步驟並使用您自己的資料來執行。

您也會需要 Excel。本文使用 [Microsoft Excel 2016](https://products.office.com/zh-TW/)。

## 連接與建立報告

1.	開啟 Excel，然後建立新的活頁簿或開啟您想要連接的活頁簿。

2.	在頁面頂端的功能表列中，依序按一下 [資料]、[從其他來源]，然後按一下 [從 SQL Server]。
	
	![選取資料來源](./media/sql-database-connect-excel/excel_data_source.png)

	資料連線精靈隨即開啟。

3.	在 [連接到資料庫伺服器] 對話方塊中，以 **<*servername*>.database.windows.net** 格式，輸入裝載您希望連接之邏輯伺服器的**伺服器名稱**。例如，**adventureserver.database.windows.net**。

4.	在 [登入認證] 區段中，按一下 [使用下列的使用者名稱和密碼]，輸入您在建立 SQL Database 時針對它所建立的**使用者名稱**和**密碼**，然後按 [下一步]。

	> [AZURE.TIP]Excel 的 [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) 和 [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) 增益集都有非常相似的體驗。

5. 在 [選取資料庫及資料表]對話方塊中，從下拉式功能表選取 [AdventureWorks] 資料庫，然後從資料表和檢視清單中選取 [vGetAllCategories]，然後按 [下一步]。

	![選取資料庫和資料表][5]

6. 在 [儲存資料連線檔案和完成] 對話方塊中，按一下 [完成]。

7. 在 [匯入資料] 對話方塊中，選取 [樞紐分析圖] 並按一下 [確定]。

	![選取匯入資料][2]

8. 在 [樞紐分析圖欄位] 對話方塊中，選取下列組態來建立每個類別的產品計數報告。

	![組態][3]

	成功案例看起來像這樣：

	![成功][4]

## 後續步驟

如果您是軟體即服務 (SaaS) 開發人員，請了解[彈性資料庫集區](sql-database-elastic-pool.md)。您可以輕鬆地使用[彈性資料庫工作](sql-database-elastic-jobs-overview.md)管理大型資料庫集合。

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=Oct15_HO3-->