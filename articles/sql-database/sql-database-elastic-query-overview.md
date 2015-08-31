<properties
    pageTitle="Azure SQL Database 彈性資料庫查詢概觀"
    description="彈性查詢功能的概觀"
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="sidneyh"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/09/2015"
    ms.author="sidneyh" />

# Azure SQL Database 彈性資料庫查詢 (預覽) 概觀

**彈性資料庫查詢功能** (預覽) 可讓您跨越 Azure SQL Database 中的多個資料庫執行 Transact-SQL 查詢。它可讓您將 Microsoft 和協力廠商工具 (Excel、PowerBI、Tableau 等) 連接到具有多個資料庫的資料層，尤其是當那些資料庫共用通用的結構描述時 (也稱為水平資料分割或分區化)。這項功能可讓您將查詢相應放大到 SQL Database 中的大型資料層，並將結果透過商務智慧 (BI) 報告視覺化。

若要開始建置彈性資料庫查詢應用程式，請參閱[開始使用彈性資料庫查詢](sql-database-elastic-query-getting-started.md)。

## 彈性資料庫查詢案例

彈性資料庫查詢的目標是協助報告案例便利進行，其中由多個資料庫提供資料列給單一整體結果。查詢可以由使用者或應用程式直接撰寫，或透過連接到資料庫的工具來間接撰寫。使用商業 BI 或資料整合工具 (或無法變更的任何軟體) 建立報告時，這特別有用。透過彈性資料庫查詢，您可以在 Excel、PowerBI、Tableau 或 Cognos 等工具中使用熟悉的 SQL Server 連線體驗，輕鬆地查詢多個資料庫。

彈性資料庫查詢也可讓您透過 SQL Server Management Studio 或 Visual Studio 所發出的查詢，輕鬆存取整個資料庫集合，並協助更方便從 Entity Framework 或其他 ORM 環境執行跨資料庫查詢。圖 1 顯示的案例中，現有的雲端應用程式 (使用彈性資料庫工具程式庫) 根據相應放大的資料層建置，而彈性資料庫查詢用於跨資料庫報告。

**圖 1**

![向相應放大資料層上使用的彈性資料庫查詢][1]

資料層相應放大到許多使用通用結構描述的資料庫。這個方法也稱為水平資料分割或分區化。執行和管理資料分割時可以使用 (1) [彈性資料庫用戶端程式庫](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) 或 (2) 使用應用程式特定模型將資料分散到多個資料庫。使用此拓撲時，報告通常必須跨越多個資料庫。現在，透過彈性資料庫查詢，您可以連線到單一 SQL 資料庫，而來自遠端資料庫的查詢結果就如同從單一虛擬資料庫產生一樣。

> [AZURE.NOTE]彈性資料庫查詢最適合可在資料層執行大部分處理的非經常性報告案例。對於繁重的報告工作負載或有更多複雜查詢的資料倉儲案例，也請考慮使用 [Azure SQL 資料倉儲](http://azure.microsoft.com/services/sql-data-warehouse/)。


## 彈性資料庫查詢案例

使用彈性資料庫查詢在水平分割的資料層執行報告工作時，需要彈性擴縮分區對應來代表資料層的資料庫。一般而言，這種案例中只會使用單一分區對應，並以具有彈性資料庫查詢功能的專用資料庫做為報告查詢的進入點。只有這個專用資料庫必須設定彈性資料庫查詢物件，如下所述。圖 2 說明此拓撲及其彈性資料庫查詢資料庫和分區對應的組態。

> [AZURE.NOTE]專用彈性資料庫查詢資料庫必須是 SQL DB v12 資料庫，且最初僅支援「高階」層。分區本身沒有任何限制。

**圖 2**

![使用彈性資料庫查詢產生分區化層的報告][2]

(**Shardlet** 是與分區上之分區化索引鍵的單一值相關聯的所有資料。**分區化索引鍵**是決定資料如何分佈至分區的資料行值。比方說，依區域分佈的資料可能以區域識別碼做為分區化索引鍵。如需詳細資訊，請參閱 [Elastic Scale 名詞解釋](sql-database-elastic-scale-glossary.md)。)


以後，彈性資料庫查詢功能將會支援其他拓樸。隨著新功能推出，這篇文章會更新來反映這些新功能。

## 設定分區對應來啟用彈性查詢

建立彈性資料庫查詢方案需要彈性資料庫工具[**分區對應**](sql-database-elastic-scale-shard-map-management.md)來代表彈性資料庫查詢的遠端資料庫。如果您已經在使用彈性資料庫用戶端程式庫，您可以使用現有的分區對應。否則，您必須使用彈性資料庫工具建立分區對應。

下列 C#程式碼範例示範如何新增單一遠端資料庫做為分區來建立分區對應。

    ShardMapManagerFactory.CreateSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerCreateMode.ReplaceExisting, RetryBehavior.DefaultRetryBehavior);
    smm = ShardMapManagerFactory.GetSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerLoadPolicy.Lazy,
      RetryBehavior.DefaultRetryBehavior);
    map = smm.CreateRangeShardMap<int>("yourshardmapname");
    shard = map.CreateShard(new ShardLocation("yoursqldbserver", "yoursqldbdatabasename"));

如需有關分區對應的詳細資訊，請參閱 [Shardmap 管理](sql-database-elastic-scale-shard-map-management.md)。

若要使用彈性資料庫查詢功能，您必須先建立分區對應並將遠端資料庫註冊為分區。此作業只需要執行一次。只有在新增或移除遠端資料庫時才需要變更分區對應 -- 這些是現有分區對應上的累加作業。


## 建立彈性資料庫查詢資料庫物件

為了描述可從彈性資料庫查詢端點存取的遠端資料表，我們引進功能來定義外部資料表，對您的應用程式和協力廠商工具而言，這些外部資料表如同本機資料表一樣。針對這些資料庫物件，可透過工具隱含地提交查詢，或從 SQL Server Management Studio、Visual Studio 資料工具或應用程式明確提交查詢。彈性資料庫查詢執行起來就像任何其他 Transact-SQL 陳述式一樣 -- 明顯的差異是查詢會分散至外部物件底下可能的許多遠端資料庫。

彈性資料庫查詢功能依賴這四個 DDL 陳述式。一般而言，這些 DDL 陳述式只在應用程式的結構描述變更時使用一次或很少使用。

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
*    [CREATE CREDENTIAL](https://msdn.microsoft.com/library/ms189522.aspx)
*    [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
*    [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)

### 資料庫範圍的主要金鑰和認證

認證代表使用者識別碼和密碼，供彈性資料庫查詢用來連接到彈性擴縮分區對應和您在 Azure SQL DB 中的遠端資料庫。您可以使用下列語法建立所需的主要金鑰和認證：

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';  
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>
    WITH IDENTITY = '<shard_map_username>',
    SECRET = '<shard_map_password>'
     [;]
請確定 &lt;shard\_map\_username> 不含任何 "@servername" 後置詞。

認證的相關資訊會顯示在 sys.database\_scoped.credentials 目錄檢視中。

您可以使用下列語法卸除主要金鑰和認證：

    DROP CREDENTIAL <credential_name> ON DATABASE;
    DROP MASTER KEY;  

### 外部資料來源

在下一個步驟中，您需要將分區對應註冊為外部資料來源。建立和卸除外部資料來源的語法定義如下：

      CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
                     (TYPE = SHARD_MAP_MANAGER,
                     LOCATION = '<fully_qualified_server_name>',
                     DATABASE_NAME = '<shardmap_database_name>',
                     CREDENTIAL = <credential_name>,
                     SHARD_MAP_NAME = '<shardmapname>'
    ) [;]

您可以使用下列陳述式卸除外部資料來源：

    DROP EXTERNAL DATA SOURCE <data_source_name>[;]

使用者必須擁有 ALTER ANY EXTERNAL DATA SOURCE 權限。此權限包含在 ALTER DATABASE 權限中。

**範例**

下列範例說明對外部資料來源使用 CREATE 陳述式。

    CREATE EXTERNAL DATA SOURCE MyExtSrc
    WITH
    (
    TYPE=SHARD_MAP_MANAGER,
    LOCATION='myserver.database.windows.net',
    DATABASE_NAME='ShardMapDatabase',
    CREDENTIAL= SMMUser,
    SHARD_MAP_NAME='ShardMap'
    );

您可以從下列目錄檢視擷取目前的外部資料來源清單：

    select * from sys.external_data_sources;

請注意，在查詢處理期間，使用相同的認證來讀取分區對應和存取遠端資料庫上的資料。


### 外部資料表

在彈性資料庫查詢中，我們擴充現有的外部資料表語法，參考已分割到 Azure SQL DB 中 (數個) 遠端資料庫的資料表。根據上述的外部資料來源概念，建立和卸除外部資料表的語法定義如下：

    CREATE EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    )[;]

    <sharded_external_table_options> ::=
          DATA_SOURCE = <External_Data_Source>,
          DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED | ROUND_ROBIN

分區化原則控制將資料表視為分區化資料表或複寫資料表。在分區化資料表中，來自不同分區的資料不重疊。複寫資料表在每個分區上反而都有相同的資料。查詢處理器依賴這項資訊，能夠正確和更有效率處理查詢。循環配置資源散發表示使用應用程式特定方法，散發該資料表的資料。

    DROP EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name[;]

**CREATE/DROP EXTERNAL TABLE** 的權限：需要 ALTER ANY EXTERNAL DATA SOURCE權限，在參考基礎資料來源時也有需要。

**範例**：下列範例說明如何建立外部資料表：

    CREATE EXTERNAL TABLE [dbo].[order_line](
        [ol_o_id] [int] NOT NULL,
        [ol_d_id] [tinyint] NOT NULL,
        [ol_w_id] [int] NOT NULL,
        [ol_number] [tinyint] NOT NULL,
        [ol_i_id] [int] NOT NULL,
        [ol_delivery_d] [datetime] NOT NULL,
        [ol_amount] [smallmoney] NOT NULL,
        [ol_supply_w_id] [int] NOT NULL,
        [ol_quantity] [smallint] NOT NULL,
        [ol_dist_info] [char](24) NOT NULL
    )
    WITH
    (
        DATA_SOURCE = MyExtSrc,
        DISTRIBUTION=SHARDED(ol_w_id)
    );

下列範例顯示如何從目前資料庫擷取外部資料表清單：

    select * from sys.external_tables;


## 報告和查詢

### 查詢
一旦您已定義外部資料來源和外部資料表，您可以使用熟悉的 SQL 資料庫連接字串，連接到已啟用彈性資料庫查詢功能的資料庫。您現在可以對外部資料表使用執行完整唯讀查詢 -- 受到的一些限制如以下[限制](#preview-limitations)一節所述。

**範例**：下列查詢在倉儲、訂單及訂單明細之間執行三方聯結，並使用數個彙總和選擇性篩選。假設倉儲、訂單及訂單明細依倉儲識別碼資料行分割，則彈性資料庫查詢可以將聯結共置在遠端資料庫上，且可以相應放大處理成本較高的查詢部分。

    select
        w_id as warehouse,
        o_c_id as customer,
        count(*) as cnt_orderline,
        max(ol_quantity) as max_quantity,
        avg(ol_amount) as avg_amount,
        min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id

### 預存程序 SP\_ EXECUTE\_FANOUT

SP\_EXECUTE\_FANOUT 預存程序可存取分區對應所代表的資料庫。此預存程序接受下列參數：

-    **伺服器名稱** (nvarchar)：裝載分區對應的邏輯伺服器的完整名稱。
-    **分區對應資料庫名稱** (nvarchar)：分區對應資料庫的名稱。
-    **使用者名稱** (nvarchar)：登入分區對應資料庫和遠端資料庫的使用者名稱。
-    **密碼** (nvarchar)：使用者密碼。
-    **分區對應名稱** (nvarchar)：用於查詢之分區對應的名稱。
-    **查詢**：對每個分區執行的查詢。

它會使用叫用參數中提供的分區對應資訊，在所有向分區對應註冊的分區上執行指定的陳述式。任何結果都透過類似於多分區查詢的 UNION ALL 語意合併。結果也包含額外的「虛擬」資料行，內有遠端資料庫名稱。

請注意，相同的認證用來連接到分區對應資料庫和分區。

**範例**：

    sp_execute_fanout 'myserver.database.windows.net', N'ShardMapDb', N'myuser', N'MyPwd', N'ShardMap', N'select count(w_id) as foo from warehouse'

## 工具的連線能力
您可以使用彈性資料庫查詢資料庫方面熟悉的 SQL 資料庫連接字串，連接您的 BI 和資料整合工具。請確定 SQL Server 可支援做為您的工具的資料來源。然後，在彈性資料庫查詢資料庫中使用外部物件，就像您要使用工具來連接的任何其他 SQL Server 資料庫一樣。

## 最佳作法
*    請確定防火牆規則中允許從 Microsoft Azure 存取分區對應管理員資料庫和資料庫分區對應中定義的資料庫。必須如此，彈性資料庫查詢資料庫才能連接它們。如需詳細資訊，請參閱 [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx)。
*    彈性資料庫查詢不會驗證或強制執行外部資料表所定義的資料分佈。如果您的實際資料分佈與資料表定義中指定的散發不同，您的查詢可能會產生非預期的結果。
*    彈性資料庫查詢最適合可在分區完成大部分運算的查詢。使用可在分區上評估的選擇性篩選述詞，或在所有分區上以資料分割對齊方式來聯結資料分割索引鍵，通常可以獲得最佳查詢效能。其他查詢模式可能需要從分區載入大量資料至前端節點，效能可能不佳。

## 成本

彈性資料庫查詢算在 Azure SQL Database 資料庫的成本內。請注意，支援的拓撲允許遠端資料庫和彈性資料庫查詢端點位於不同的資料中心，但從遠端資料庫輸出的資料以一般 Azure 輸出費率收費。

## 預覽限制

關於預覽版，有幾點需要注意：

*    最初只有 SQL DB v12 高階效能層提供彈性資料庫查詢功能，而彈性資料庫查詢存取的遠端資料庫可能在任何一層。
* 外部資料來源所參考的外部資料表僅支援對遠端資料庫執行讀取作業。不過，您可以在外部資料表定義本身所在的彈性資料庫查詢資料庫上使用完整 Transact-SQL 功能。這很有用，比方說，使用 SELECT column\_list INTO local\_table 保存暫存結果，或在彈性資料庫查詢資料庫上定義預存程序來參考外部資料表。
*    目前，查詢中的參數不能推送到遠端資料庫。參數化查詢需要將所有的資料帶給前端節點，根據資料大小而定，可能發生效能不佳。暫時的解決辦法是避免在查詢中使用參數，或使用 RECOMPILE 選項將參數自動取代為目前的值。
* 目前不支援外部資料表的資料行層級統計資料。
* 彈性資料庫查詢目前不會執行分區刪除，因為分區化索引鍵的述詞允許安全地排除處理某些遠端資料庫。因此，查詢一定會觸及查詢的外部資料來源所代表的所有遠端資料庫。
* 涉及不同資料庫的資料表之間聯結的任何查詢，可能會將大量資料列帶回給彈性資料庫查詢資料庫進行處理，造成效能成本。最好開發可在每個遠端資料庫就地處理的查詢，或在執行聯結之前，使用 WHERE 子句限制每個資料庫傳回的資料列。
*    在預覽期間，將會變更用於彈性資料庫查詢中繼資料定義的語法。
*    SSMS 或 SSDT 中的 Transact-SQL 指令碼功能目前無法搭配彈性資料庫查詢物件使用。

## 意見反應
請在 Disqus 或 Stackoverflow 上和我們分享您的體驗之後的意見。我們很樂意接受關於服務的各種意見 (缺失、不完善、功能落差)。

## 後續步驟
若要開始探索彈性資料庫查詢，請嘗試我們的逐步教學課程，立即體驗完整的實用範例：[開始使用彈性資料庫查詢](sql-database-elastic-query-getting-started.md)。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png

<!--anchors-->

<!---HONumber=August15_HO8-->