<properties
    pageTitle="使用 Transact-SQL 為 Azure SQL Database 設定異地複寫 | Microsoft Azure"
    description="使用 Transact-SQL 為 Azure SQL Database 設定異地複寫"
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
    ms.date="11/10/2015"
    ms.author="carlrab"/>

# 使用 Transact-SQL 為 Azure SQL Database 設定異地複寫



> [AZURE.SELECTOR]
- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


本文說明如何使用 Transact-SQL 為 Azure SQL Database 設定異地複寫。


異地複寫可讓您在不同的資料中心位置 (區域) 最多建立 4 個複本 (次要) 資料庫。在資料中心中斷或在無法連線至主要資料庫的情況下，便可使用次要資料庫。

異地複寫僅適用於 Standard 和 Premium 資料庫。

Standard 資料庫可以有一個不可讀取次要複本，並且必須使用建議的區域。Premium 資料庫最多可以有四個位於任何可用區域並且可讀取次要複本。


若要設定異地複寫，您需要下列項目：

- Azure 訂用帳戶 - 如果您沒有 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來這篇文章。
- 邏輯 Azure SQL Database 伺服器 <MyLocalServer> 和 SQL Database <MyDB> - 您想要複寫到不同地理區域的主要資料庫。
- 一或多個邏輯 Azure SQL Database 伺服器 <MySecondaryServer(n)> - 您將在其中建立異地複寫次要資料庫的夥伴伺服器的邏輯伺服器。
- 主要複本上 DBManager 的登入資訊，具備您將異地複寫的本機資料庫的 db\_ownership，以及在您設定異地複寫的夥伴伺服器上的 DBManager。
- 最新版本的 SQL Server Management Studio - 若要取得最新版本的 SQL Server Management Studio (SSMS)，請移至[下載 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。如需使用 SQL Server Management Studio 來管理 Azure SQL Database 邏輯伺服器和資料庫的詳細資訊，請參閱[使用 SQL Server Management Studio 管理 Azure SQL Database](sql-database-manage-azure-ssms.md)

## 加入次要資料庫

您可以使用 **ALTER DATABASE** 陳述式，在夥伴伺服器上建立異地複寫的次要資料庫。您在包含要複寫的資料庫伺服器的 master 資料庫上執行此陳述式。異地複寫資料庫 (「主要資料庫」) 會具備與要複寫的資料庫相同的名稱，並且預設與主要資料庫具有相同的服務層級。次要資料庫可以是可讀取或不可讀取，並且可以是單一資料庫或彈性資料庫。如需詳細資訊，請參閱 [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 和[服務層](https://azure.microsoft.com/documentation/articles/sql-database-service-tiers/)。建立次要資料庫並植入之後，資料就會開始以非同步方式從主要資料庫複寫。下列步驟說明如何使用 Management Studio 設定異地複寫。提供使用單一資料庫或彈性資料庫建立不可讀取和可讀取次要複本的步驟。

> [AZURE.NOTE]如果指定的夥伴伺服器上存在次要資料庫 (例如，因為目前存在或先前存在的異地複寫關係，命令將會失敗。


### 加入不可讀取次要複本 (單一資料庫)

您可以使用下列步驟，將不可讀取次要複本建立為單一資料庫。

1. 使用版本 13.0.600.65 或 SQL Server Management Studio 的更新版本。

 	 >[AZURE.IMPORTANT]下載[最新](https://msdn.microsoft.com/library/mt238290.aspx)版本的 SQL Server Management Studio。建議您一律使用最新版本的 Management Studio 保持與 Azure 入口網站更新同步。


2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列 **ALTER DATABASE** 陳述式，使本機資料庫成為具有 <MySecondaryServer1> 上不可讀取次要資料庫的異地複寫主要複本。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. 按一下 [執行] 來執行查詢。


### 加入可讀取次要複本 (單一資料庫)
您可以使用下列步驟，將可讀取次要複本建立為單一資料庫。

1. 在 Management Studio 中，連接到您的 Azure SQL Database 邏輯伺服器。

2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列 **ALTER DATABASE** 陳述式，使本機資料庫成為具有次要伺服器上可讀取次要資料庫的異地複寫主要複本。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. 按一下 [執行] 來執行查詢。



### 加入不可讀取次要複本 (彈性資料庫)
您可以使用下列步驟，將不可讀取次要複本建立為彈性資料庫。

1. 在 Management Studio 中，連接到您的 Azure SQL Database 邏輯伺服器。

2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列 **ALTER DATABASE** 陳述式，使本機資料庫成為具有彈性集區中次要伺服器上不可讀取次要資料庫的異地複寫主要複本。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO)
           , ELASTIC_POOL (name = MyElasticPool1);

4. 按一下 [執行] 來執行查詢。



### 加入可讀取次要複本 (彈性資料庫)
您可以使用下列步驟，將可讀取次要複本建立為彈性資料庫。

1. 在 Management Studio 中，連接到您的 Azure SQL Database 邏輯伺服器。

2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列 **ALTER DATABASE** 陳述式，使本機資料庫成為具有彈性集區中次要伺服器上可讀取次要資料庫的異地複寫主要複本。

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = NO)
           , ELASTIC_POOL (name = MyElasticPool2);

4. 按一下 [執行] 來執行查詢。



## 移除次要資料庫

您可以使用 **ALTER DATABASE** 陳述式以永久終止次要資料庫與其主要資料庫之間的複寫關係。此陳述式是在主要資料庫所在的 master 資料庫上執行。關聯性終止之後，次要資料庫會成為一般的讀寫資料庫。如果與次要資料庫的連線中斷，命令將會成功，但次要資料庫必須等到連線恢復後才會變成可讀寫。如需詳細資訊，請參閱 [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 和[服務層](https://azure.microsoft.com/documentation/articles/sql-database-service-tiers/)。

使用下列步驟從異地複寫關係移除異地複寫的次要複本。

1. 在 Management Studio 中，連接到您的 Azure SQL Database 邏輯伺服器。

2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列 **ALTER DATABASE** 陳述式來移除異地複寫的次要複本。

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. 按一下 [執行] 來執行查詢。


## 起始規劃的容錯移轉，將次要資料庫升級成為新主要複本

您可以使用 **ALTER DATABASE** 陳述式，來升級次要資料庫，使它成為規劃的方式中的新主要資料庫，將現有主要降級成為次要資料庫。此陳述式是在要升級的異地複寫次要資料庫所在的 Azure SQL Database 邏輯伺服器上的 master 資料庫上執行。這項功能是為了規劃的容錯移轉 (例如 DR 鑽研期間) 設計，並且需要主要資料庫可供使用。

此命令會執行下列工作流程：

1. 暫時切換複寫為同步模式，造成所有未完成的交易被排清至次要複本並封鎖所有新交易；

2. 切換異地複寫關係中兩個資料庫的角色。

這個順序可確保不會發生任何資料遺失。切換角色時，會有一小段時間無法使用這兩個資料庫 (大約為 0 到 25 秒)。在正常情況下，完成整個作業所需的時間應該少於一分鐘。如需詳細資訊，請參閱 [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) 和[服務層](https://azure.microsoft.com/documentation/articles/sql-database-service-tiers/)。


> [AZURE.NOTE]發出命令時如果主要資料庫無法使用，命令將會失敗並出現一則錯誤訊息指出沒有可用的主要伺服器。在少數情況下，作業會無法完成並且可能會出現停滯。在此情況下，使用者可以執行強制容錯移轉命令並接受資料遺失。

使用下列步驟來起始規劃的容錯移轉。

1. 在 Management Studio 中，連接到異地複寫次要資料庫所在的 Azure SQL Database 邏輯伺服器。

2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列 **ALTER DATABASE** 陳述式，使異地複寫資料庫成為具有 <ElasticPool2> 中 <MySecondaryServer4> 上可讀取次要資料庫。

        ALTER DATABASE <MyDB> FAILOVER;

4. 按一下 [執行] 來執行查詢。



## 起始從主要資料庫到次要資料庫的未規劃的容錯移轉

您可以使用 **ALTER DATABASE** 陳述式，來升級次要資料庫，使它成為未規劃的方式中的新主要資料庫，每當主要資料庫無法使用時，一次強制將現有主要降級成為次要資料庫。此陳述式是在要升級的異地複寫次要資料庫所在的 Azure SQL Database 邏輯伺服器上的 master 資料庫上執行。

這項功能是針對還原資料庫的可用性非常重要而且部分資料遺失是可接受時的災害復原所設計。叫用強制容錯移轉時，指定的次要資料庫立即成為主要資料庫，並開始接受寫入交易。原始主要資料庫能夠與這個新的主要資料庫重新連線時，會在原始主要資料庫執行增量備份，而舊的主要資料庫會變成新主要資料庫的次要資料庫；之後就只是新的主要資料庫的複本。

不過，因為次要資料庫上不支援還原時間點，發生強制容錯移轉之前，如果使用者想要復原已認可到舊主要資料庫但尚未複寫到新主要資料庫的資料，使用者應該接洽支援人員復源遺失的資料。

> [AZURE.NOTE]如果在主要資料庫和次要資料庫上線時發出此命令，舊的主要會變成新的次要資料庫，但不會嘗試資料同步處理。因此可能發生部分資料遺失。


如果主要資料庫中有多個次要資料庫，命令將只會在執行命令的次要伺服器上成功。不過，其他次要資料庫不會知道發生強制容錯移轉。使用者必須使用「移除次要複本」API 手動修復此組態，然後在這些額外的次要複本上重新設定異地複寫。

使用下列步驟從異地複寫關係強制移除異地複寫的次要複本。

1. 在 Management Studio 中，連接到異地複寫次要資料庫所在的 Azure SQL Database 邏輯伺服器。

2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列 **ALTER DATABASE** 陳述式，使 <MyLocalDB> 成為具有 <ElasticPool2> 中 <MySecondaryServer4> 上可讀取次要資料庫的異地複寫主要複本。

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. 按一下 [執行] 來執行查詢。

## 監視異地複寫組態和健康狀態

監視工作包括異地複寫組態的監視和監視資料複寫健康狀態。您可以使用 master 資料庫中的 **sys.dm\_geo\_replication\_links** 動態管理檢視，傳回 Azure SQL Database 邏輯伺服器上每個資料庫的所有現有複寫連結的相關資訊。這個檢視針對每個主要和次要資料庫之間的複寫連結包含一個資料列。您可以使用 **sys.dm\_replication\_status** 動態管理檢視，對目前參與複寫連結的每個 Azure SQL Database 傳回一個資料列。這包括主要和次要資料庫。如果給定主要資料庫有一個以上的連續複寫連結，此資料表會對每個關聯性包含一個資料列。會在所有資料庫 (包括邏輯主機) 中建立檢視。不過，在邏輯主機中查詢此檢視會傳回空集合。您可以使用 **sys.dm\_operation\_status** 動態管理檢視來顯示所有資料庫作業的狀態，包括複寫連結的狀態。如需詳細資訊，請參閱 [sys.geo\_replication\_links (Azure SQL Database)](https://msdn.microsoft.com/library/mt575501.aspx)、[sys.dm\_geo\_replication\_link\_status (Azure SQL Database)](https://msdn.microsoft.com/library/mt575504.aspx) 和 [sys.dm\_operation\_status (Azure SQL Database)](https://msdn.microsoft.com/library/dn270022.aspx)。

使用下列步驟可監視異地複寫關係。

1. 在 Management Studio 中，連接到您的 Azure SQL Database 邏輯伺服器。

2. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **master** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。

3. 使用下列陳述式可顯示具有異地複寫連結的所有資料庫。

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. 按一下 [執行] 來執行查詢。
5. 開啟 Databases 資料夾、展開 **System Databases** 資料夾、在 **MyDB** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。
6. 使用下列陳述式來顯示複寫落後和我的次要資料庫 MyDB 上次複寫的開始時間。

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. 按一下 [執行] 來執行查詢。
8. 使用下列陳述式可顯示與 MyDB 資料庫相關聯的最新異地複寫作業。

        SELECT * FROM sys.dm_operation_status where major_resource_is = 'MyDB'
        ORDER BY start_time DESC

9. 按一下 [執行] 來執行查詢。


## 後續步驟

- [災害復原詳細資訊](sql-database-disaster-recovery-drills.md)


## 其他資源

- [新異地複寫功能的要點](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication)
- [使用異地複寫設計業務持續性的雲端應用程式](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [業務續航力概觀](sql-database-business-continuity.md)
- [SQL Database 文件](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1203_2015-->