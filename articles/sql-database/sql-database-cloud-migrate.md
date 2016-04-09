<properties
   pageTitle="SQL Server 資料庫移轉至 SQL Database | Microsoft Azure"
   description="了解將內部部署 SQL Server 資料庫移轉至雲端 Azure SQL Database 的相關做法。請先使用資料庫移轉工具測試相容性再進行資料庫移轉。"
   keywords="database migration,sql server database migration,database migration tools,migrate database,migrate sql database,資料庫移轉,sql server 資料庫移轉,資料庫移轉工具,移轉資料庫,移轉 sql database"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="03/22/2016"
   ms.author="carlrab"/>

# SQL Server 資料庫移轉至雲端 SQL Database

在本文中，您將學習如何將內部部署 SQL Server 2005 或更新版的資料庫移轉到 Azure SQL Database。在此資料庫移轉過程中，您會將您的結構描述和資料從目前環境中的 SQL Server 資料庫移轉到 SQL Database，但前提是現有的資料庫通過相容性測試。使用 [SQL Database V12](sql-database-v12-whats-new.md)，除了伺服器層級和跨資料庫作業以外，剩餘的相容性問題很少。依賴[部分或未支援功能](sql-database-transact-sql-information.md)的資料庫和應用程式會需要一些再造來修正這些不相容情況，然後才能移轉 SQL Server 資料。

若要移轉，以下是您將採取的步驟︰

- **相容性測試**︰您必須先驗證資料庫與 [SQL Database V12](sql-database-v12-whats-new.md) 的相容性。 
- **如果有任何相容性問題，加以修正**︰如果驗證失敗，您必須先修正驗證錯誤。  
- **執行移轉**如果資料庫相容，您可以使用一或多個方法來執行移轉。 

SQL Server 提供數種方法來完成每個工作。這篇文章將提供可供每個工作使用的方法概觀。下圖說明步驟和方法。

  ![VSSSDT 移轉圖表](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)
  
 > [AZURE.NOTE] 若要將非 SQL Server 資料庫 (包括 Microsoft Access、Sybase、MySQL Oracle 和 DB2) 移轉到 Azure SQL Database，請參閱 [SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)。

## 資料庫移轉工具會測試 SQL Server 資料庫與 SQL Database 的相容性

若要在開始資料庫移轉程序之前，測試 SQL Database 相容性問題，請使用下列其中一種方法：

- [SQL Server Data Tools for Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)：SSDT 使用最新的相容性規則來偵測 SQL Database V12 不相容。如果偵測到不相容，您可以直接在此工具中修正偵測到的問題。這是目前用來測試和修正 SQL Database V12 相容性問題的建議方法。 
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)：SqlPackage 是一個命令提示字元公用程式，將會測試相容性問題並在發現時產生包含偵測到的相容性問題的報表。如果您使用此工具，請確定您使用的是最新版本，才能使用最新的相容性規則。如果偵測到錯誤，您必須使用其他工具來修正任何偵測到的相容性問題 - 建議使用 SSDT。  
- [SQL Server Management Studio 的匯出資料層應用程式精靈](sql-database-cloud-migrate-determine-compatibility-ssms.md)：此精靈可以在螢幕上偵測與回報錯誤。如果沒有偵測到錯誤，您可以繼續並完成移轉至 SQL Database。如果偵測到錯誤，您必須使用其他工具來修正任何偵測到的相容性問題 - 建議使用 SSDT。
- [Microsoft SQL Server 2016 Upgrade Advisor Preview](http://www.microsoft.com/download/details.aspx?id=48119)︰這項獨立工具目前正在預覽中，將會偵測並產生 SQL Database V12 不相容的報告。此工具還沒有最新的相容性規則。如果沒有偵測到錯誤，您可以繼續並完成移轉至 SQL Database。如果偵測到錯誤，您必須使用其他工具來修正任何偵測到的相容性問題 - 建議使用 SSDT。 
- [SQL Azure 移轉精靈 ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md)：SAMW 是 codeplex 工具，使用 Azure SQL Database V11 相容性規則偵測 Azure SQL Database V12 不相容。如果偵測到不相容，有些問題可以直接在此工具中修正。這項工具可能會發現不需要修正的不相容，但它是第一個可使用的 Azure SQL Database 移轉協助工具，而且有很多來自 SQL Server 社群的支援。此外，此工具可以在工具本身內部完成移轉。 

## 修正資料庫移轉相容性問題

如果偵測到相容性問題，您必須先修正這些相容性問題，再繼續進行 SQL Server 資料庫移轉。您可能會發現各種不同的不相容問題，取決於來源資料庫中的 SQL Server 版本，以及您正在移轉的資料庫複雜度。來源資料庫的 SQL Server 版本越舊，您就越可能發現不相容。除了使用您選擇之搜尋引擎的目標網際網路搜尋之外，請使用下列資源︰

- [Azure SQL Database 中不支援的 SQL Server 資料庫功能](sql-database-transact-sql-information.md)
- [SQL Server 2016 中已終止的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [SQL Server 2014 中已終止的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [SQL Server 2012 中已終止的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [SQL Server 2008 R2 中已終止的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [SQL Server 2005 中已終止的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

除了搜尋網際網路和使用這些資源之外，如需識別如何以最佳方式修正不相容問題的另一項優良資源，可使用 [MSDN SQL Server 社群論壇](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver)或 [StackOverflow](http://stackoverflow.com/)。

使用下列其中一個資料庫移轉工具來修正偵測到的問題︰

- 使用 [SQL Server Data Tools for Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)︰ 若要使用 SSDT，您要將資料庫結構描述匯入 SQL Server Data Tools for Visual Studion ("SSDT")、建置 SQL Database V12 部署的專案、在 SSDT 中修正所有偵測到的相容性問題，再將變更同步處理回來源資料庫 (或來源資料庫的複本)。這是目前用來測試和修正 SQL Database V12 相容性問題的建議方法。遵循[逐步解說使用 SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) 的連結。
- 使用 [SQL Server Management Studio ("SSMS")](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)︰若要使用 SSMS，您可以使用其他工具來修正偵測到的錯誤，以執行 Transact-SQL 命令來修正偵測到的錯誤。這個方法主要是供進階使用者直接在來源資料庫中修改資料庫結構描述。 
- 使用 [SQL Azure 移轉精靈 ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md)︰若要使用 SAMW，您必須從來源資料庫產生 Transact-SQL 指令碼，稍後會由精靈在適當時機將其轉換，讓結構描述和 SQL Database V12 相容。完成時，SAMW 可以連接到 SQL Database V12 以執行指令碼。這項工具也會分析追蹤檔案以判斷相容性問題。只能使用結構描述產生指令碼，或者指令碼可以 BCP 格式包含資料。

## 將相容 SQL Server 資料庫移轉到 SQL Database

若要移轉相容的 SQL Server 資料庫，Microsoft 會為各種案例提供數種移轉方法。您選擇的方法取決於您允許的停機時間、SQL Server 資料庫的大小和複雜度，以及您連線到 Microsoft Azure 雲端的能力。

> [AZURE.SELECTOR]
- [SSMS 移轉精靈](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [匯出至 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [從 BACPAC 檔案匯入](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [異動複寫](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

若要選擇移轉方法，要提問的第一個問題是您是否經得起在移轉期間將資料庫移出生產環境。在發生作用中交易時移轉資料庫，可能會導致資料庫不一致且資料庫可能損毀。有許多方法可以停止資料庫，從停用用戶端連接性到建立[資料庫快照集](https://msdn.microsoft.com/library/ms175876.aspx)。

若要在停機時間最短的情況下移轉，如果您的資料庫符合交易複寫的需求，請使用 [SQL Server 交易複寫](sql-database-cloud-migrate-compatible-using-transactional-replication.md)。如果您可以經得起一些停機時間，或者您在執行生產資料庫的測試移轉，以便稍後進行移轉，請考慮下列三種方法之一：

- [SSMS 移轉精靈](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)：針對小型到中型資料庫，移轉相容的 SQL Server 2005 或更新版本資料庫的步驟跟在 SQL Server Management Studio 中執行[將資料庫部署到 Microsoft Azure Database 精靈](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)一樣簡單。
- [匯出到 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)，然後 [從 BACPAC 檔案匯入](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)：如果您有連線挑戰 (沒有連線能力、低頻寬或逾時問題)，對於中大型資料庫，請使用 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案。使用此方法，將 SQL Server 結構描述和資料匯出至 BACPAC 檔案，然後使用 SQL Server Management Studio 中的 [匯出資料層應用程式精靈] 或 [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) 命令提示字元公用程式，將 BACPAC 檔案匯入 SQL 資料庫中。
- 同時使用 BACPAC 和 BCP：對更大型的資料庫使用 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案和 [BCP](https://msdn.microsoft.com/library/ms162802.aspx)，以達到規模更大的平行處理來增加效能 (儘管複雜性更高)。使用此方法，分開移轉結構描述和資料。
 - [將結構描述只匯出至 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)。
 - [只從 BACPAC 檔案匯入結構描述](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)到 SQL Database。
 - 使用 [BCP](https://msdn.microsoft.com/library/ms162802.aspx) 將資料擷取至一般檔案，然後將這些檔案[平行載入](https://technet.microsoft.com/library/dd425070.aspx)至 Azure SQL Database。

	 ![SQL Server 資料庫移轉 - 將 SQL Database 移轉至雲端。](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

<!---HONumber=AcomDC_0323_2016-->