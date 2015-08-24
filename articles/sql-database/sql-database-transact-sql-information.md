<properties
   pageTitle="Azure SQL Database Transact-SQL 資訊 | Microsoft Azure"
   description="Azure SQL Database 中的 Transact-SQL 陳述式"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/07/2015"
   ms.author="rick.byham@microsoft.com"/>

# Azure SQL Database Transact-SQL 資訊

Microsoft Azure SQL Database 中完全支援大多數的 SQL Server 2016 Transact-SQL 陳述式。這包括 SQL Server 資料類型、運算子和字串、算術、邏輯、指標函式和大部分應用程式相依的其他 Transact-SQL 元素。部分支援或不支援的函式通常與 SQL Database 如何管理資料庫的差異 (例如檔案、高可用性和安全性功能) 相關，或與 service broker 的特殊目的功能相關。因為 SQL Database 會隔離許多功能與 master 資料庫的相依性，許多伺服器層級活動是不當且不受支援的。SQL Server 中已被取代的功能在 SQL Database 中通常不受支援。

## 升級至 SQL Database V12

本主題討論當升級至免費的 SQL Database V12 時，SQL Database 可用的功能。如需 V12 的詳細資訊，請參閱 [SQL Database V12 新功能](sql-database-v12-whats-new.md)。SQL Database V12 新增效能和管理性增強功能，以及支援其他功能。新增的功能如下所示，分成完全支援的功能和部分支援的功能。

## 在 SQL Database V12 中部分支援的功能

SQL Database V12 支援部分而非全部的引數，這些引數存在於對應的 SQL Server 2016 Transact-SQL 陳述式中。例如，CREATE PROCEDURE 陳述式可以使用，但是 CREATE PROCEDURE 的 WITH ENCRYPTION 選項無法使用。請參閱連結的語法主題，以了解每個陳述式支援區域的詳細資訊。

- CLR 組件：[CREATE ASSEMBLY](https://msdn.microsoft.com/library/ms189524.aspx)
- 資料庫：[CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- DMV 通常可用於已公開推出的功能
- 函式：[CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)
- [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 
- 登入：[CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- 預存程序：[CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- 資料表：[CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- 類型 (自訂)：[CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)
- 使用者：[CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
- 檢視：[CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)

## 在 SQL Database 中不支援的功能

- 系統物件的定序
- 相關連接：端點陳述式、ORIGINAL\_DB\_NAME。Windows 驗證不適用於登入或自主資料庫使用者。
- 跨資料庫查詢、跨資料庫擁有權鏈結、可信任設定
- 資料收集器
- 資料庫圖表
- Database Mail
- DATABASEPROPERTY (改用 DATABASEPROPERTYEX)
- 分散式交易
- EXECUTE AS 登入
- 加密：可延伸金鑰管理
- 事件服務：事件、事件通知、查詢通知
- 與資料庫檔案位置、大小和資料庫檔案 (由 Microsoft Azure 自動管理) 相關的功能。
- 與高可用性相關的功能，高可用性是透過您的 Microsoft Azure 帳戶管理：備份、還原、AlwaysOn、資料庫鏡像、記錄傳送、復原模式。如需詳細資訊，請參閱「Azure SQL Database 備份和還原」。
- 依賴記錄讀取器的功能：複寫、異動資料擷取。
- 依賴 SQL Server Agent 或 MSDB 資料庫的功能：工作、警示、運算子、以原則為基礎的管理、Database Mail、中央管理伺服器。
- FILESTREAM
- 函式：fn\_get\_sql、fn\_virtualfilestats、fn\_virtualservernodes
- 全域暫存資料表
- 硬體相關的伺服器設定：記憶體、背景工作執行緒、CPU 親和性、追蹤旗標等。改用服務層級。
- HAS\_DBACCESS
- KILL STATS JOB
- 連結的伺服器、OPENQUERY、OPENROWSET、OPENDATASOURCE、BULK INSERT、3 和 4 個組件名稱
- 主要/目標伺服器
- 資源管理員
- 語意搜尋
- 伺服器認證
- 伺服器層級項目：伺服器角色、IS\_SRVROLEMEMBER、sys.login\_token。雖然某些伺服器層級權限已由資料庫層級權限取代，但是無法使用伺服器層級權限。雖然某些伺服器層級 DMV 已由資料庫層級 DMV 取代，但是無法使用某些伺服器層級 DMV。
- 無伺服器 Express：localdb、使用者執行個體
- Service broker
- SET REMOTE\_PROC\_TRANSACTIONS
- SHUTDOWN
- sp\_addmessage
- sp\_configure 選項和 RECONFIGURE
- SQL Server Audit (改用 SQL Database Auditing)
- SQL Server Profiler
- SQL Server 追蹤
- 追蹤旗標
- Transact-SQL 偵錯
- 觸發程序：伺服器範圍或登入觸發程序
- USE 陳述式

## 完整 Transact-SQL 參考

如需 Transact-SQL 文法、使用方式和範例的詳細資訊，請參閱《SQL Server 線上叢書》中的＜[Transact-SQL 參考 (資料庫引擎)](https://msdn.microsoft.com/library/bb510741.aspx)＞。

### 關於「適用於」標記

Transact-SQL 參考包含 SQL Server 2008、SQL Server 2008 R2、SQL Server 2012、SQL Server 2014 和 Microsoft Azure SQL Database 相關的主題。每個主題頂端附近是一個區段，指出哪些產品支援主題的主旨。如果略過產品，則主題所描述的功能就無法用於該產品中。例如，可用性群組是在 SQL Server 2012 中導入。「**建立可用性群組**」主題指出它會套用至 **SQL Server (SQL Server 2012 到最新版本)**，因為它不適用於 SQL Server 2008、SQL Server 2008 R2 或 Microsoft Azure SQL Database。

在某些情況下，主題的一般主旨可以用於產品，但並不支援所有引數。例如，自主資料庫使用者是在 SQL Server 2012 中導入。**CREATE USER** 陳述式可以用於任何 SQL Server 產品，不過 **WITH PASSWORD** 語法不能用於較舊版本。在此案例中，其他「**適用於**」區段會插入到主題的主體中適當的引數描述。

<!---HONumber=August15_HO7-->