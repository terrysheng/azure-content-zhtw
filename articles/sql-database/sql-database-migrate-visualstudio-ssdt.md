<properties 
   pageTitle="使用 Visual Studio 和 SSDT 移轉"
	description="Microsoft Azure SQL Database, 資料庫移轉, 匯入資料庫, 匯出資料庫, 移轉精靈"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""/>

<tags
   ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-management"
	ms.date="08/24/2015"
	ms.author="carlrab"/>

#就地更新資料庫，接著部署至 Azure SQL Database

![替代文字](./media/sql-database-migrate-visualstudio-ssdt/01VSSSDTDiagram.png)

將內部部署資料庫移轉至 Azure SQL Database V12 時，因為 Azure SQL Database 中不支援資料庫使用的 SQL Server 功能而必須變更結構描述時，或是為了測試內部部署資料庫中目前是否有不受支援的功能時，請使用此選項。

搭配使用 Visual Studio 2013 Update 4 或更新版本及 [ Visual Studio 適用的最新 SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)。

使用此選項：

 - 會先使用 Visual Studio 的 SQL Server Data Tools ("SSDT") 從來源資料庫建立資料庫專案。 
 - 專案的目標平台會設為 Azure SQL Database V12，且會建置專案以找出所有相容性問題。 
 - 一旦專案成功建置後，結構描述會發佈回來源資料庫的複本 (不覆寫來源資料庫)。
 - 接著會使用 SSDT 中的資料比較功能，將來源資料庫與新建立的 Azure SQL 相容資料庫進行比較，然後再以來源資料庫的資料更新新的資料庫。 
 - 更新過的資料庫接著會使用 SSMS，直接或透過先匯出後匯入 BACPAC 檔案來部署至 Azure。
 
>[AZURE.NOTE]注意：如果您需要的是僅限結構描述的部署，則可以將更新過的結構描述直接從 Visual Studio 發佈至 Azure SQL Database。

## 移轉步驟

1.	開啟 Visual Studio 中的 [SQL Server 物件總管]。使用 [加入 SQL Server] 連接到包含移轉之資料庫的 SQL Server 執行個體。在總管中找出資料庫，以滑鼠右鍵按一下該資料庫，然後選取 [建立新專案...]。 

	![替代文字](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)

2.	將匯入設定設為 [僅匯入應用程式範圍的物件]。取消核取該選項，以匯入參考登入、權限和資料庫設定。

	![替代文字](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	按一下 [開始] 匯入資料庫並建立專案，其中會包含資料庫中每個物件的 T-SQL 指令碼檔案。專案內資料夾中的指令碼檔案為巢狀。

	![替代文字](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	在 Visual Studio [方案總管] 中，以滑鼠右鍵按一下資料庫專案，然後選取 [屬性]。這會開啟 [專案設定] 頁面，您應該在其中將 [目標平台] 設為 [Microsoft Azure SQL Database V12]。

	![替代文字](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)

5.	以滑鼠右鍵按一下專案，然後選取 [建置] 來建置專案。

	![替代文字](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)

6.	[錯誤清單] 會顯示每一個不相容。在此案例中，使用者名稱 NT AUTHORITY\\NETWORK SERVICE 不相容。由於它不相容，您可以加上註解，或將它移除 (和解決將此登入和角色從資料庫解決方案移除的連帶影響)。

	![替代文字](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)
7.	在第一個指令碼上按兩下，在查詢視窗中開啟指令碼，為指令碼加上註解，然後執行指令碼。![替代文字](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

8.	對每一個含有不相容的指令碼重複執行此程序，直到沒有錯誤為止。![替代文字](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
9.	當資料庫沒有任何錯誤時，以滑鼠右鍵按一下專案，然後選取 [發佈]，以建置和發佈該資料庫至來源資料庫的複本 (強烈建議至少在一開始使用複本)。 
 - 在您發佈之前，根據來源 SQL Server 版本 (早於 SQL Server 2014)，您可能需要重設專案的目標平台以啟用部署。 
 - 如果您正在移轉舊版 SQL Server 資料庫，除非您先將資料庫移轉至新版 SQL Server，否則請勿將任何功能導入來源 SQL Server 不支援的專案。 

	![替代文字](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)

	![替代文字](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)

10.	在 [SQL Server 物件總管] 中，以滑鼠右鍵按一下來源資料庫並按一下 [資料比較] 將專案與原始資料庫進行比較，了解精靈做過哪些變更。選取您的 Azure SQL V12 版本的資料庫，然後按一下 [完成]。

	![替代文字](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)

	![替代文字](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)

12.	檢閱偵測到的差異，然後按一下 [更新目標] 將資料從來源資料庫移轉至 Azure SQL V12 資料庫。

	![替代文字](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)

14.	使用 SSMS 將 Azure SQL V12 相容資料庫的結構描述和資料部署到 Azure SQL Database。請參閱[使用 SSMS 移轉相容資料庫](sql-database-migrate-ssms.md)。

<!---HONumber=August15_HO9-->