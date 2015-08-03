<properties
	pageTitle="如何使用 SSMS 連接到 Azure SQL Database" 
	description="了解如何使用 SSMS 連接到 Azure SQL Database。"
	services="sql-database"
	documentationCenter=""
	authors="sidneyh" 
	manager="jhubbard" 
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="07/15/2015"
	ms.author="sidneyh" />

# 連接 SQL Server Management Studio

以下是安裝 SQL Server Management Studio (SSMS) 和使用 SSMS 來連接並查詢 SQL Database 的步驟。

## 必要條件
* 如[開始使用 Microsoft Azure SQL Database](sql-database-get-started.md) 中所述的 SQL Database AdventureWorks 範例資料庫。

## 安裝並啟動 SQL Server Management Studio (SSMS)
1. 移至 [SQL Server 2014 Express](http://www.microsoft.com/download/details.aspx?id=42299) 的下載頁面，按一下 [**下載**]，並選擇 32 位元版本 (x86) 或 64 位元版本 (x64) 的 MgmtStudio 下載。

	![MgtmtStudio32BIT 或 MgmtStudio64BIT][1]
2.	當您使用預設設定安裝 SSMS 時，請遵循相關提示。
3.	下載之後，請在您的電腦上搜尋 SQL Server 2014 Management Studio，然後啟動 SSMS。


## 連接到您的 SQL Database
1. 開啟 SSMS。
2. 在 [**連至到伺服器**] 視窗的 [**伺服器名稱**] 方塊中，輸入格式為 *&lt;servername>*.**database.windows.net** 的伺服器名稱。
3. 在 [**驗證**] 下拉式方塊中，選取 [**SQL Server 驗證**]。
4. 輸入您建立 SQL Database 伺服器時指定的 [**登入**] 和 [**密碼**]。

	![[連接至伺服器] 對話方塊][2]
5. 按一下 [**選項**] 按鈕。
6. 在 [**連接至資料庫**] 方塊中輸入**AdventureWorks**，然後按一下 [**連接**]。

	![連接至資料庫][3]

### 如果連接失敗
確定您建立之邏輯伺服器的防火牆允許來自本機電腦的連線。請參閱[作法：進行防火牆設定 (Azure SQL Database)](https://msdn.microsoft.com/library/azure/jj553530.aspx)

## 執行範例查詢

1. 在 [**物件總管**] 中，瀏覽至 **AdventureWorks** 資料庫。
2. 在資料庫上按一下滑鼠右鍵，然後按一下 [**新增查詢**]。

	![新增查詢][4]
3. 在新開啟的查詢視窗中，複製並貼上下列程式碼：

		SELECT 
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. 然後按一下 [**執行**] 按鈕。成功時如下所示：![成功][5]


## 後續步驟
您可以使用 Transact-SQL 陳述式來建立或管理資料庫。請參閱 [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) 和[使用 SQL Server Management Studio 管理 Azure SQL Database](sql-database-manage-azure-ssms.md)。您也可以將事件記錄到 Azure 儲存體。請參閱[開始使用 SQL Database 稽核](sql-database-auditing-get-started.md)。

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png
 

<!---HONumber=July15_HO4-->