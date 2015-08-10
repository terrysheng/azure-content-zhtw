<properties
	pageTitle="使用 Excel 連接到 Azure SQL Database"
	description="Azure SQL Database 的 Excel 試算表，可報告和資料探索。"
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="joseidz"
	editor="joseidz"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="joseidz"/>


# 使用 Excel 連接到 Azure SQL Database
將 Excel 連接到 Azure SQL Database，並在資料庫中建立資料報告。

## 必要條件
- 已佈建且在執行中的 Azure SQL Database。若要建立新的 SQL Database，請參閱[開始使用 Microsoft Azure SQL Database](sql-database-get-started.md)。
- [Microsoft Excel 2013](https://products.office.com/zh-tw/) (或 Microsoft Excel 2010)

## 連接至 SQL Database 並建立報告
1.	開啟 Excel。
2.	在頁面頂端的功能表列中，按一下 [**資料**]。
3.	按一下 [**從其他來源**]，然後按一下 [**從 SQL Server**]。**資料連線精靈**隨即開啟。

	![資料連線精靈][1]
4.	在 [**伺服器名稱**] 方塊中，輸入 Azure SQL Database 伺服器名稱。範例：

	 	adventureserver.database.windows.net
5.	在 [**登入認證**] 區段中，選取 [**使用下列使用者名稱和密碼**] 然後輸入適當的 SQL Database 伺服器認證。然後按 [下一步]。

	注意：Excel 的[PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) 和 [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) 增益集都有非常相似的體驗。

6. 在 [**選取資料庫及資料表**] 對話方塊中，從下拉式功能表選取 [**AdventureWorks**] 資料庫，然後從資料表和檢視清單中選取 [**vGetAllCategories**]，然後按 [**下一步**]。

	![選取資料庫和資料表][5]
7. 在 [**儲存資料連線檔案和完成**] 對話方塊中，按一下 [**完成**]。
8. 在 [**匯入資料**] 對話方塊中，選取 [**樞紐分析圖**] 並按一下 [**確定**]。

	![選取匯入資料][2]
9. 在 [**樞紐分析圖欄位**] 對話方塊中，選取下列組態來建立每個類別的產品計數報告。

	![組態][3]
10.	成功案例看起來像這樣：

	![成功][4]

## 後續步驟

如果您是軟體即服務 (SaaS) 開發人員，請了解[彈性資料庫集區](sql-database-elastic-pool.md)。您可以輕鬆地使用[彈性資料庫工作](sql-database-elastic-jobs-overview.md)管理大型資料庫集合。

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=July15_HO5-->