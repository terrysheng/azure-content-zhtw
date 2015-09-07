<properties
   pageTitle="Azure SQL Database 安全性方針和限制 | Microsoft Azure"
	description="了解與安全性相關的 Microsoft Azure SQL Database 方針和限制。"
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
	ms.date="08/20/2015"
	ms.author="rickbyh"/>

# Azure SQL Database 安全性方針和限制

本主題描述與安全性相關的 Microsoft Azure SQL Database 方針和限制。管理您的 Azure SQL Database 安全性時，請考慮下列幾點。

## 防火牆

Azure SQL Database 服務唯有透過 TCP 通訊埠 1433 才能使用。若要從您的電腦存取 SQL Database，請務必確認您的防火牆允許 TCP 連接埠 1433 上的傳出 TCP 通訊。連接程序當中，從 Azure 虛擬機器的連接會被重新導向到不同的 IP 位址和連接埠，針對每個背景工作角色都是唯一。連接埠號碼的範圍從 11000 到 11999。

在您第一次連接到 Azure SQL Database 伺服器之前，必須先使用 [Azure 入口網站](https://portal.azure.com) 或 [Azure 平台管理入口網站](https://manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/All/dashboard)設定 Azure SQL Database 防火牆。您必須建立伺服器層級的防火牆設定，讓您的電腦或 Azure 可以嘗試連接到 Azure SQL Database 伺服器。此外，如果您想要控制 Azure SQL Database 伺服器中特定資料庫的存取，請針對個別資料庫建立資料庫層級的防火牆規則。如需詳細資訊，請參閱 [Azure SQL Database 防火牆](sql-database-firewall-configure.md)。

## 連接加密和憑證驗證

SQL 資料庫和您的應用程式之間的所有通訊隨時都需要加密 (SSL)。如果用戶端應用程式沒有在連接時驗證憑證，則連接至 SQL Database 時容易受到「攔截式」攻擊。

若要使用應用程式程式碼或工具驗證憑證，請明確地要求加密的連接，並且不要信任伺服器憑證。即使您的應用程式程式碼或工具未要求加密連接，它們仍然會接收加密的連接。不過，它們可能不會驗證伺服器憑證，這樣一來很容易受到「攔截式」攻擊。

若要使用 ADO.NET 應用程式程式碼驗證憑證，請在資料庫連接字串中設定 ``Encrypt=True`` 和 ``TrustServerCertificate=False``。如需詳細資訊，請參閱[程式碼範例：使用 ADO.NET 連接到 Azure SQL Database 的重試邏輯](https://msdn.microsoft.com/library/azure/ee336243.aspx)。

SQL Server Management Studio 也支援憑證驗證。在 [連接到伺服器] 對話方塊中，按一下 [連接屬性] 索引標籤上的 [加密連接]。

> [AZURE.NOTE]SQL Server Management Studio 不支援連接到 SQL Server 2008 R2 之前版本中的 SQL Database。

雖然 SQLCMD 支援從 SQL Server 2008 開始的 SQL Database，但是它不支援在 SQL Server 2008 R2 之前的版本中進行憑證驗證。若要使用 SQLCMD (從 SQL Server 2008 R2 開始 ) 驗證憑證，請使用 ``-N`` 命令列選項，不要使用 ``-C`` 選項。若使用 -N 選項，SQLCMD 會要求加密的連接。如果不使用 ``-C`` 選項，SQLCMD 就不會以隱含方式信任伺服器憑證，並且會強制驗證憑證。

如需技術方面的補充資訊，請參閱 TechNet Wiki 網站的 [Azure SQL Database 連接安全性](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#comment-4847)文章。

## 驗證

SQL Database 僅支援 SQL Server 驗證。不支援 Windows 驗證 (整合式安全性)。使用者必須在每次連線到 SQL Database 時提供認證 (登入和密碼)。如需 SQL Server 驗證的詳細資訊，請參閱《SQL Server 線上叢書》的[選擇驗證模式](https://msdn.microsoft.com/library/ms144284.aspx)。

[SQL Database V12](sql-database-v12-whats-new.md) 可讓使用者使用自主資料庫使用者在資料庫進行驗證。如需詳細資訊，請參閱[自主資料庫使用者 - 讓資料庫具有可攜性](https://msdn.microsoft.com/library/ff929188.aspx)、[CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx) 和[自主資料庫](https://technet.microsoft.com/library/ff929071.aspx)。

> [AZURE.NOTE]Microsoft 建議使用自主資料庫使用者以加強延展性。

資料庫引擎會關閉保持閒置超過 30 分鐘的連接。必須重新登入才能使用連接。

持續作用中的 SQL Database 連接，需要至少每 10 個小時重新授權一次 (由資料庫引擎執行)。資料庫引擎嘗試重新授權時，會使用原本送出的密碼，但不需要使用者輸入。基於效能考量，當在 SQL Database 中重設密碼時，此連接將不會重新驗證，即使連接因為連接共用而重設。這點與內部部署 SQL Server 的行為不同。如果連接經過初始授權後，密碼已經變更，則必須終止連接並使用新密碼建立新的連接。具有 KILL DATABASE CONNECTION 權限的使用者可以使用 [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 命令，明確地終止 SQL Database 的連接。

## 登入和使用者

管理 SQL Database 的登入和使用者時，會有一些限制。

下列限制適用於伺服器層級主體登入：

- 對應至伺服器層級主體登入的 master 資料庫中的資料庫使用者，無法予以更改或卸除。 
- 雖然伺服器層級主體登入並不是 **master** 資料庫中這兩個資料庫角色 **dbmanager** 和 **loginmanager** 的成員，但是它擁有可授與這兩個角色的所有權限。

> [AZURE.NOTE]此登入是在伺服器佈建期間所建立，而且類似於 SQL Server 執行個體中的 **sa** 登入。

下列限制適用於所有的登入：

- 英文 (美國) 是預設的語言。
- 若要存取 **master** 資料庫，每一個登入必須對應到 **master** 資料庫中的使用者帳戶。**master** 資料庫不支援自主資料庫使用者。
- 如果您未在連接字串中指定資料庫，根據預設您將會連接到 **master** 資料庫。
- 執行 ``CREATE/ALTER/DROP LOGIN`` 和 ``CREATE/ALTER/DROP DATABASE`` 陳述式時，您必須連接到 **master** 資料庫。 
- 在 ADO.NET 應用程式中執行 ``CREATE/ALTER/DROP LOGIN`` 和 ``CREATE/ALTER/DROP DATABASE`` 陳述式時，不允許使用參數化的命令。如需詳細資訊，請參閱[命令和參數](https://msdn.microsoft.com/library/ms254953.aspx)。
- 執行 ``CREATE/ALTER/DROP DATABASE`` 和 ``CREATE/ALTER/DROP LOGIN`` 陳述式時，各陳述式在 Transact-SQL 批次中必須是唯一的陳述式。否則便會發生錯誤。例如，下列 Transact-SQL 會檢查資料庫是否存在。如果資料庫存在，則會呼叫 ``DROP DATABASE`` 陳述式以移除資料庫。因為 ``DROP DATABASE`` 陳述式不是批次中的唯一陳述式，執行下列 Transact-SQL 陳述式時就會產生錯誤。

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- 搭配執行 ``CREATE USER`` 陳述式和 ``FOR/FROM LOGIN`` 選項時，它必須是 Transact-SQL 批次中唯一的陳述式。
- 搭配執行 ``ALTER USER`` 陳述式和 ``WITH LOGIN`` 選項時，它必須是 Transact-SQL 批次中唯一的陳述式。
- 只有伺服器層級主體登入以及 **master** 資料庫中的 **dbmanager** 資料庫角色成員，才有權限執行 ``CREATE DATABASE`` 和 ``DROP DATABASE`` 陳述式。
- 只有伺服器層級主體登入以及 **master** 資料庫中的 **loginmanager** 資料庫角色成員，才有權限執行 ``CREATE LOGIN``、``ALTER LOGIN`` 和 ``DROP LOGIN`` 陳述式。
- 若要 ``CREATE/ALTER/DROP`` 使用者，必須擁有資料庫的 ``ALTER ANY USER`` 權限。
- 當資料庫角色的擁有者嘗試針對該資料庫角色加入或移除另一個資料庫使用者時，可能會發生下列錯誤：**此資料庫中沒有使用者或角色 'Name'**。 因為擁有者看不到使用者，所以會發生此錯誤。若要解決這個問題請，授與角色擁有者 ``VIEW DEFINITION`` 使用者的權限。 

如需登入和使用者的詳細資訊，請參閱[管理 Azure SQL Database 的資料庫和登入](sql-database-manage-logins.md)。

## 安全性最佳作法

請考慮下列幾點，讓 Azure SQL Database 應用程式比較不容易受到安全性威脅：

- 一律使用最新的更新：連接到 SQL Database 時，一律使用最新版的工具和程式庫，以防止安全性弱點。如需可支援何種工具和程式庫的詳細資訊，請參閱 [Azure SQL Database 一般方針和限制](https://msdn.microsoft.com/library/azure/ee336245.aspx)。
- 封鎖 TCP 通訊埠 1433 的傳入連接：應用程式與 SQL Database 進行通訊時，只需要 TCP 通訊埠 1433 的傳出連接。如果該電腦上的任何其他應用程式不需要傳入的通訊，請確定您的防火牆會繼續封鎖 TCP 通訊埠 1433 的傳入連接。
- 防止插入式攻擊弱點：若要確定您的應用程式沒有 SQL 插入式攻擊弱點，盡可能使用參數化的查詢。同時，請務必在部署應用程式之前，徹底檢閱程式碼並執行滲透測試。


## 另請參閱

[Azure SQL Database 防火牆](sql-database-firewall-configure.md)

[作法：進行防火牆設定 (Azure SQL Database)](sql-database-configure-firewall-settings.md)

[Azure SQL Database 一般方針和限制](https://msdn.microsoft.com/library/azure/ee336245.aspx)

[管理 Azure SQL Database 的資料庫和登入](sql-database-manage-logins.md)

<!---HONumber=August15_HO9-->