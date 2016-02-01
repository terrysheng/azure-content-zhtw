<properties 
   pageTitle="Azure SQL Database 一般限制與方針"
   description="此頁面描述 Azure SQL Database 的部分一般限制，以及互通性與支援的範圍。"
   services="sql-database"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar" />
<tags 
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="01/15/2016"
   ms.author="jroth" />

# Azure SQL Database 一般限制與方針

本主題提供 Azure SQL Database 的一般限制與方針。若要完整了解配額、資源管理與支援，請參閱本主題結尾處的[其他資源](#additional-guidelines)。

## 連線能力

 - 不支援 Windows 驗證。請參閱[在 Azure SQL Database 中管理資料庫與登入](sql-database-manage-logins.md)。 

 - Microsoft Azure SQL Database 支援表格式資料流 (TDS) 通訊協定用戶端 7.3 版或更新版本。

 - 僅允許 TCP/IP 連線。

 - 不支援 SQL Server 2008 SQL Server 瀏覽器，因為 Microsoft Azure SQL Database 沒有動態連接埠，只有連接埠 1433。

## SQL Server Agent/工作

Microsoft Azure SQL Database 不支援 SQL Server Agent 或工作。然而，您可以執行內部部署 SQL Server 上的 SQL Server Agent，並連線至 Microsoft Azure SQL Database。

## SQL Server 定序支援

Microsoft Azure SQL Database 使用的預設資料庫定序是 **SQL\_LATIN1\_GENERAL\_CP1\_CI\_AS**，其中 **LATIN1\_GENERAL** 是英文 (美國)，**CP1** 是代碼頁 1252，**CI** 不區分大小寫，**AS** 區分重音。可以使用 Transact-SQL 改變 V12 資料庫的定序。如需如何設定定序的詳細資訊，請參閱＜[COLLATE (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx)＞。

## 命名需求

基於安全理由，不允許某些使用者名稱。您無法使用下列名稱：

 - **admin** 
 - **administrator** 
 - **guest** 
 - **root** 
 - **sa** 

所有新物件的名稱都必須遵循識別碼的 SQL Server 規則。如需詳細資訊，請參閱[識別碼](https://msdn.microsoft.com/library/ms175874.aspx)。

此外，登入與使用者名稱不能包含 \\ 字元 (不支援 Windows 驗證)。

## 其他指導方針

- 除了本文所述的一般限制外，SQL Database 會有根據您的**服務層級**而有特定的配額與限制。如需服務層級的概觀，請參閱 [SQL Database 服務層級](sql-database-service-tiers.md)。

- 如需其他 SQL Database 限制，請參閱＜[Azure SQL Database 資源限制](sql-database-resource-limits.md)＞。

- 如需與安全性相關的方針，請參閱＜[Azure SQL Database 安全性方針與限制](sql-database-security-guidelines.md)＞。

- 另一個相關範圍與相容性有關，就是 Azure SQL Database 有內部部署版的 SQL Server，例如 SQL Server 2014。Azure SQL Database 的最新 V12 版已在此方面進行許多改善。如需詳細資訊，請參閱＜[SQL Database V12 的新功能](sql-database-v12-whats-new.md)＞。

- 如需驅動程式的可用性和 SQL Database 支援的相關資訊，請參閱＜[SQL Database 與 SQL Server 的連線庫](sql-database-libraries.md)＞。

<!---HONumber=AcomDC_0121_2016-->