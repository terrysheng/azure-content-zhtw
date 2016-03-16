<properties
   pageTitle="SQL Server 資料庫移轉至 SQL Database | Microsoft Azure"
   description="了解將內部部署 SQL Server 資料庫移轉至雲端 Azure SQL Database 的相關做法。請先使用資料庫移轉工具測試相容性再進行資料庫移轉。"
   keywords="database migration,sql server database migration,database migration tools,migrate database,migrate sql database,資料庫移轉,sql server 資料庫移轉,資料庫移轉工具,移轉資料庫,移轉 sql database"
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
   ms.date="01/05/2016"
   ms.author="carlrab"/>

# SQL Server 資料庫移轉至雲端 SQL Database

在本文中，您將學習如何將內部部署 SQL Server 2005 或更新版的資料庫移轉到 Azure SQL Database。在此資料庫移轉過程中，您會將您的結構描述和資料從目前環境中的 SQL Server 資料庫移轉到 SQL Database，但前提是現有的資料庫通過相容性測試。使用 [SQL Database V12](sql-database-v12-whats-new.md)，除了伺服器層級和跨資料庫作業以外，剩餘的相容性問題很少。依賴[部分或未支援功能](sql-database-transact-sql-information.md)的資料庫和應用程式會需要一些再造來[修正這些不相容情況](sql-database-cloud-migrate-fix-compatibility-issues.md)，然後才能移轉 SQL Server 資料。

> [AZURE.NOTE] 若要將非 SQL Server 資料庫 (包括 Microsoft Access、Sybase、MySQL Oracle 和 DB2) 移轉到 Azure SQL Database，請參閱 [SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)。

## 資料庫移轉工具會測試 SQL Server 資料庫與 SQL Database 的相容性

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

若要在開始資料庫移轉程序之前，測試 SQL Database 相容性問題，請使用下列其中一種方法：

- [使用 SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)：SqlPackage 是一個命令提示字元公用程式，將會測試相容性問題並在發現時產生包含偵測到的相容性問題的報表。
- [使用 SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)：SQL Server Management Studio 中的「匯出資料層應用程式精靈」將會在螢幕上顯示偵測到的錯誤。

## 修正資料庫移轉相容性問題

如果偵測到相容性問題，您必須先修正這些相容性問題，再繼續進行 SQL Server 資料庫移轉。使用下列資料庫移轉工具：

- 使用 [SQL Azure 移轉精靈](sql-database-cloud-migrate-fix-compatibility-issues.md)
- 使用 [Visual Studio 適用的 SQL Server Data Tools ](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- 使用 [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## 將相容 SQL Server 資料庫移轉到 SQL Database

若要移轉相容的 SQL Server 資料庫，Microsoft 會為各種案例提供數種移轉方法。您選擇的方法取決於您允許的停機時間、SQL Server 資料庫的大小和複雜度，以及您連線到 Microsoft Azure 雲端的能力。

> [AZURE.SELECTOR]
- [SSMS Migration Wizard](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Export to BACPAC File](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Import from BACPAC File](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transactional Replication](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

若要選擇移轉方法，要提問的第一個問題是您是否經得起在移轉期間將資料庫移出生產環境。在發生作用中交易時移轉資料庫，可能會導致資料庫不一致且資料庫可能損毀。有許多方法可以停止資料庫，從停用用戶端連接性到建立[資料庫快照集](https://msdn.microsoft.com/library/ms175876.aspx)。

若要在停機時間最短的情況下移轉，如果您的資料庫符合交易複寫的需求，請使用 [SQL Server 交易複寫](sql-database-cloud-migrate-compatible-using-transactional-replication.md)。如果您可以經得起一些停機時間，或者您在執行生產資料庫的測試移轉，以便稍後進行移轉，請考慮下列三種方法之一：

- [SSMS 移轉精靈](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)：針對小型到中型資料庫，移轉相容的 SQL Server 2005 或更新版本資料庫的步驟跟在 SQL Server Management Studio 中執行[將資料庫部署到 Microsoft Azure Database 精靈](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)一樣簡單。
- [匯出到 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)，然後 [從 BACPAC 檔案匯入](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)：如果您有連線挑戰 (沒有連線能力、低頻寬或逾時問題)，對於中大型資料庫，請使用 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案。使用此方法，將 SQL Server 結構描述和資料匯出至 BACPAC 檔案，然後使用 SQL Server Management Studio 中的 [匯出資料層應用程式精靈] 或 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令提示字元公用程式，將 BACPAC 檔案匯入 SQL 資料庫中。
- 同時使用 BACPAC 和 BCP：對更大型的資料庫使用 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案和 [BCP](https://msdn.microsoft.com/library/ms162802.aspx)，以達到規模更大的平行處理來增加效能 (儘管複雜性更高)。使用此方法，分開移轉結構描述和資料。
 - [將結構描述只匯出至 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)。
 - [只從 BACPAC 檔案匯入結構描述](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)到 SQL Database。
 - 使用 [BCP](https://msdn.microsoft.com/library/ms162802.aspx) 將資料擷取至一般檔案，然後將這些檔案[平行載入](https://technet.microsoft.com/library/dd425070.aspx)至 Azure SQL Database。

	 ![SQL Server 資料庫移轉 - 將 SQL Database 移轉至雲端。](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

<!---HONumber=AcomDC_0128_2016-->