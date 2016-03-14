<properties
	pageTitle="SQL Database 和 SQL Server 的連線庫"
	description="列出用戶端程式可用來連接到 Azure SQL Database 或 Microsoft SQL Server 之每個驅動程式的最低版本號碼。連結所提供的驅動程式版本資訊，是由社群 (而不是由 Microsoft) 所發行。"
	services="sql-database"
	documentationCenter=""
	authors="pehteh"
	manager="jeffreyg"
	editor="genemi"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="pehteh"/>

# SQL Database 和 SQL Server 的連線庫

本主題列出當連接到 Azure SQL Database 或 Microsoft SQL Server 時，用戶端程式可使用之每個程式庫/驅動程式的最低版本號碼。

## Microsoft 所發行的驅動程式庫資料表

下表顯示 Microsoft 發行的程式庫。「程式庫」一欄提供可讓您下載每個程式庫的連結。「版本」一欄則列出與 Azure SQL Database 和 Microsoft SQL Server 互動的建議最低版本。

| 平台 | 作業系統 | 下載的<br/>程式庫 | 驅動程式<br/>版本 | 驅動程式<br/>描述 | 更多<br/>資訊 |
| :--- | :--- | :--- | :--- | :--- | :-- |
| .NET | 跨平台 (.NET) | [ADO.NET、System.Data.SqlClient](http://www.microsoft.com/download/details.aspx?id=30653) | 4\.5 以上 | .NET Framework 的 SQL Server 提供者 | 。 |
| PHP | Windows | [PHP for SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) | 2\.0 以上 | SQL Server 適用的 PHP 驅動程式 | [連結](http://msdn.microsoft.com/library/dn865013.aspx) |
| Java | Windows | [JDBC for SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) | 2\.0 以上 | Type 4 JDBC 驅動程式，提供透過標準 JDBC API 的資料庫連接 | [連結](https://msdn.microsoft.com/library/mt654048.aspx) |
| ODBC | Windows | [ODBC for SQL Server](http://www.microsoft.com/download/details.aspx?id=36434) | 11\.0 以上 | SQL Server 適用的 Microsoft ODBC 驅動程式 | [連結](http://msdn.microsoft.com/library/jj730308.aspx) |
| ODBC | Suse Linux | [ODBC for SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11\.0 以上 | SQL Server 適用的 Microsoft ODBC 驅動程式 | [連結](https://msdn.microsoft.com/zh-TW/library/hh568451.aspx) |
| ODBC | Redhat Linux | [ODBC for SQL Server](http://www.microsoft.com/download/details.aspx?id=34687) | 11\.0 以上 | SQL Server 適用的 Microsoft ODBC 驅動程式 | [連結](https://msdn.microsoft.com/zh-TW/library/hh568451.aspx) |

### ODBC 支援

使用資料來源名稱 (DSN) 精靈來定義 Azure SQL Database 的資料來源，按一下 [以使用者輸入的登入識別碼和密碼進行 SQL Server 驗證] 選項，並選取 [連線到 SQL Server 以獲得其他設定選項的預設設定]。輸入您的使用者名稱和密碼做為 [登入識別碼] 和 [密碼] 以連線到 Azure SQL Database 伺服器。清除 [連線到 SQL Server 以獲得其他設定...] 核取方塊。按一下 [變更預設資料庫為:] 然後輸入您的 Azure SQL Database 名稱，即使它沒有顯示在清單中。請注意，精靈會在 [變更 SQL Server 系統訊息的語言為:] 清單中列出數種語言。

在此版本中，Microsoft Azure SQL Database 僅支援英文，因此選取英文做為語言。Microsoft Azure SQL Database 不支援 **鏡像伺服器**或**附加資料庫**，所以這些項目保留空白。按一下 [測試連接]。

使用 SQL Server 2008 Native Client ODBC 驅動程式時，[測試連接] 按鈕可能會造成不支援 **master.dbo.syscharsets** 錯誤。忽略此錯誤，儲存 DSN 並使用它。

### 適用於 DB2 和 SQL Server 及 DRDA 設計的 OLEDB

Microsoft OLE DB Provider for DB2 Version 5.0 (資料提供者) 可讓您建立以 IBM DB2 資料庫為目標的分散式應用程式。此資料提供者運用 Microsoft SQL Server 資料存取架構，還有可當做分散式關聯資料庫架構 (DRDA) 應用程式要求者的 Microsoft DB2 網路用戶端。此資料提供者會將 Microsoft 元件物件模型 (COM) OLE DB 命令和資料類型轉換成 DRDA 通訊協定字碼指標和資料格式。

如需詳細資訊，請參閱：

- [Microsoft OLE DB Provider for DB2 Version 5.0](http://msdn.microsoft.com/library/dn745875.aspx)
- [Microsoft OLEDB Provider for DB2 v4.0 for Microsoft SQL Server 2012](http://www.microsoft.com/download/details.aspx?id=29100)

## 協力廠商程式庫

> [AZURE.IMPORTANT] 下表列出由協力廠商依協力廠商授權條款所發行的程式庫。您必須負責確認並遵守相關的協力廠商授權，才能使用這些程式庫。您必須自行承擔使用這些程式庫的風險。Microsoft 不會為此處提供的資訊做任何明示或默示保證，提供此資訊只是為了方便使用者了解。此處所提供的一切皆無隱含 Microsoft 的任何背書。<br/><br/>＜協力廠商程式庫＞一節中的資訊會由開發人員公用社群更新和維護 (藉由使用 GitHub.com 上 **Azure** 所擁有的 [azure-content](http://github.com/Azure/azure-content/) 儲存機制)。Microsoft 鼓勵開發人員更新本節。Microsoft 人員「並不」打算維護本節中的資訊，有一部分是因為其他人比我們更精通每個特定的協力廠商程式庫。感謝您。

下表列出由協力廠商 (例如其他公司或社群) 所發行的程式庫。Microsoft 所發行的程式庫僅限於本主題中先前的章節。

| 平台 | 程式庫 |
| :-- | :-- |
| Ruby | [tinytds *(org, stable)*](https://rubygems.org/gems/tiny_tds/versions/0.7.0) |
| GO | [go-mssqldb *(org, stable)*](https://github.com/denisenkom/go-mssqldb) |
| Python | [pymssql *(org, stable)*](http://pymssql.org/en/stable/) |
| Node.js | [Node-MSSQL *(npmjs)*](https://www.npmjs.com/package/node-mssql)<br/><br/>[Node-MSSQL-Connector *(npmjs)*](https://www.npmjs.com/package/node-mssql-connector) |
| C++ | [FreeTDS *(org)*](http://www.freetds.org/) |



<!--
https://en.wikipedia.org/wiki/Draft:Microsoft_SQL_Server_Libraries/Drivers
-->

<!---HONumber=AcomDC_0302_2016-->