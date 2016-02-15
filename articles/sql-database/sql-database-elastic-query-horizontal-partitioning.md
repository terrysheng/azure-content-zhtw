<properties
    pageTitle="分區化的彈性資料庫查詢 (水平資料分割) | Microsoft Azure"
    description="如何設定水平資料分割的彈性查詢"    
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/28/2016"
    ms.author="torsteng;sidneyh" />

# 分區化的彈性資料庫查詢 (水平資料分割)

本文件說明如何設定水平資料分割案例的彈性資料庫查詢，以及如何執行您的查詢。如需水平資料分割案例的定義，請參閱[彈性資料庫查詢概觀 (預覽)](sql-database-elastic-query-overview.md)。

![跨分區查詢][1]

此功能屬於 Azure SQL Database [彈性資料庫功能集](sql-database-elastic-scale-introduction.md)的一部分。
 
## 建立資料庫物件

彈性資料庫查詢會延伸 T-SQL 語法，以參考使用分區化 (或水平資料分割) 將資料分散於許多資料庫的資料層。本節提供與分區化資料表彈性查詢相關聯的 DDL 陳述式概觀。這些陳述式可在彈性查詢資料庫中建立分區化資料層的中繼資料表示法。執行這些陳述式的必要條件是使用彈性資料庫用戶端程式庫建立分區對應。如需詳細資訊，請參閱[分區對應管理](sql-database-elastic-scale-shard-map-management.md)；或使用下列主題中的範例來建立一個分區對應：[開始使用彈性資料庫工具](sql-database-elastic-scale-get-started.md)。

定義彈性資料庫查詢的資料庫物件依賴下列 T-SQL 陳述式，在以下的水平資料分割案例中會進一步說明：

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) 

* [建立資料庫範圍認證](https://msdn.microsoft.com/library/mt270260.aspx)

* [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)

* [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)

### 1\.1 資料庫範圍的主要金鑰和認證 

認證代表使用者識別碼和密碼，供彈性查詢用來連接到您在 Azure SQL DB 中的遠端資料庫。若要使用下列語法建立所需的主要金鑰和認證：

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = ’password’;
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = ‘<username>’,  
    SECRET = ‘<password>’
    [;]

或要卸除認證和金鑰：

    DROP DATABASE SCOPED CREDENTIAL <credential_name>;  
    DROP MASTER KEY;   

 
**注意** 請確定 *< username>* 不含任何 *“@servername”* 後置詞。

### 1\.2 外部資料來源

定義外部資料來源，以提供分區對應和資料層的相關資訊。外部資料來源會參考分區對應。彈性查詢會接著使用外部資料來源和基礎分區對應來列舉參與資料層的資料庫。用於建立外部資料來源的語法定義如下：

	<External_Data_Source> ::=    
	CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                               	           
			(TYPE = SHARD_MAP_MANAGER,
                   	LOCATION = '<fully_qualified_server_name>',
			DATABASE_NAME = ‘<shardmap_database_name>',
			CREDENTIAL = <credential_name>, 
			SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 
 
或要建立外部資料來源：

	DROP EXTERNAL DATA SOURCE <data_source_name>[;] 

#### CREATE/DROP EXTERNAL DATA SOURCE 的權限 

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

請注意，在彈性查詢處理期間，使用相同的認證來讀取分區對應和存取分區上的資料。

### 1\.3 外部資料表 
 
彈性查詢會擴充外部資料表 DDL，以參考水平分割於數個資料庫的外部資料表。外部資料表定義包含下列方面：

* **結構描述**：外部資料表 DDL 會定義您的查詢可以使用的結構描述。外部資料表定義中提供的結構描述必須符合實際資料儲存所在之分區上資料表的結構描述。 

* **資料散發**：外部資料表 DDL 會定義您用來將資料分散於資料層的資料散發。請注意，Azure SQL Database 不會驗證您在外部資料表上針對分區上的實際散發所定義的散發。您須負責確保分區上的實際資料散發符合外部資料表定義。

* **資料層參考**：外部資料表 DDL 指的是外部資料來源。外部資料來源會指定分區對應，以將找出資料層中的所有資料庫所需的資訊提供給外部資料表。

如上一節所述使用外部資料來源，建立和卸除外部資料表的語法如下：

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
	    { WITH ( <sharded_external_table_options> ) }
	) [;]  
	
	<sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
	  [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

DATA\_SOURCE 子句會定義用於外部資料表的外部資料來源 (水平資料分割情形中的分區對應)。

SCHEMA\_NAME 和 OBJECT\_NAME 子句提供的功能可將外部資料表定義分別對應至分區上不同結構描述中的資料表，或對應至不同名稱的資料表。如果省略，即會假設遠端物件的結構描述為 "dbo" 並假設其名稱與所定義的外部資料表名稱相同。

如果您的遠端資料表名稱已存在於您要建立外部資料表的資料庫中，SCHEMA\_NAME 和 OBJECT\_NAME 子句特別有用。當您想要定義外部資料表以取得相應放大的資料層上目錄檢視或 DMV 的彙總檢視時，可能會出現此問題。由於目錄檢視和 DMV 已經存在於本機，所以您無法將其名稱使用於外部資料表定義。而是，在 SCHEMA\_NAME 和/或 OBJECT\_NAME 子句中使用不同的名稱和使用目錄檢視或 DMV 的名稱。(請參閱下面的範例)。

DISTRIBUTION 子句會指定用於此資料表的資料散發：

* SHARDED 表示此資料表的資料會水平分割於分區對應中的資料庫。在 <sharding_column_name> 參數中可擷取資料散發的資料分割索引鍵。  

* REPLICATED 表示資料表的相同複本會存在於分區對應中的每個資料庫上。Azure SQL DB 不會維護資料表的複本。您必須負責確保複本在所有資料庫上都相同。

* ROUND\_ROBIN 表示使用水平資料分割散發資料表。不過，已使用應用程式相依散發。

查詢處理器會利用 DISTRIBUTION 子句中提供的資訊來建置最有效率的查詢計劃。

使用下列陳述式來卸除外部資料表：

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]  

**CREATE/DROP EXTERNAL TABLE 的權限：**需要 ALTER ANY EXTERNAL DATA SOURCE權限，在參考基礎資料來源時也有需要。

**安全性考量**：可存取外部資料表的使用者可以在外部資料來源定義中所提供的認證下，自動取得基礎遠端資料表的存取權。您應該仔細管理外部資料表的存取權，以避免透過外部資料來源的認證所造成的意外權限提升。一般的 SQL 權限可用來授與或撤銷外部資料表的存取權，如同它是一般資料表那樣。

**範例**：下列範例說明如何建立外部資料表：

	CREATE EXTERNAL TABLE [dbo].[order_line]( 
		 [ol_o_id] int NOT NULL, 
		 [ol_d_id] tinyint NOT NULL,
		 [ol_w_id] int NOT NULL, 
		 [ol_number] tinyint NOT NULL, 
		 [ol_i_id] int NOT NULL, 
		 [ol_delivery_d] datetime NOT NULL, 
		 [ol_amount] smallmoney NOT NULL, 
		 [ol_supply_w_id] int NOT NULL, 
		 [ol_quantity] smallint NOT NULL, 
		 [ol_dist_info] char(24) NOT NULL 
	) 
	
	WITH 
	( 
		DATA_SOURCE = MyExtSrc, 
	 	SCHEMA_NAME = 'orders', 
	 	OBJECT_NAME = 'order_details', 
		DISTRIBUTION=SHARDED(ol_w_id)
	); 

下列範例顯示如何從目前資料庫擷取外部資料表清單：

	select * from sys.external_tables; 

## 查詢 

### 2\.1 完整的 T-SQL 查詢 

一旦您已定義外部資料來源和外部資料表，現在您可以對外部資料表使用完整的 T-SQL。

**水平資料分割範例**：下列查詢在倉儲、訂單及訂單明細之間執行三方聯結，並使用數個彙總和選擇性篩選。其假設 (1) 水平資料分割 (分區化) 以及 (2) 倉儲、訂單及訂單明細依倉儲識別碼資料行分區，而彈性查詢可以將聯結共置於分區上以及平行處理分區上成本較高的查詢部分。

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
 
### 2\.2 預存程序 SP\_ EXECUTE\_FANOUT 

彈性查詢也會介紹可供直接存取分區的預存程序。此預存程序稱為 sp\_execute\_fanout 並採用下列參數：

* 伺服器名稱 (nvarchar)：裝載分區對應的邏輯伺服器的完整名稱。 
* 分區對應資料庫名稱 (nvarchar)：分區對應資料庫的名稱。 
* 使用者名稱 (nvarchar)：用來登入分區對應資料庫的使用者名稱。 
* 密碼 (nvarchar)：使用者密碼。 
* 分區對應名稱 (nvarchar)：用於查詢之分區對應的名稱。該名稱位於 \_ShardManagement.ShardMapsGlobal 資料表中，而它是在利用[開始使用彈性資料庫工具](sql-database-elastic-scale-get-started.md)一文中的範例應用程式來建立資料庫時，所使用的預設名稱。應用程式中的預設名稱是 「CustomerIDShardMap」。
*  查詢：對每個分區執行的查詢。 
*  參數宣告 (nvarchar) - 選用：含有查詢參數 (如 sp\_executesql) 中所用參數的資料類型定義的字串。 
*  參數值清單 - 選用：以逗號分隔的參數值清單 (如 sp\_executesql)  

sp\_execute\_fanout 會使用叫用參數中提供的分區對應資訊，在所有向分區對應註冊的分區上執行指定的 T-SQL 陳述式。任何結果都透過 UNION ALL 語意進行合併。結果也包含額外的「虛擬」資料行，內有分區名稱。

請注意，相同的認證用來連接到分區對應資料庫和分區。

範例：

	sp_execute_fanout 
		N'myserver.database.windows.net', 
		N'ShardMapDb', 
		N'myuser', 
		N'MyPwd', 
		N'ShardMap', 
		N'select count(w_id) as foo from warehouse' 

## 工具的連線能力  

使用一般 SQL Server 連接字串，將您的應用程式、BI 和資料整合工具連接到包含外部資料表定義的資料庫。請確定 SQL Server 可支援做為您的工具的資料來源。然後像任何其他連接到工具的 SQL Server 資料庫一樣，參考彈性查詢資料庫，並如同本機資料表一樣，從您的工具或應用程式使用外部資料表。

## 最佳作法 

* 請確定分區對應資料庫和所有分區都已透過 SQL DB 防火牆取得彈性查詢端點資料庫的存取權。  

* 彈性查詢不會驗證或強制執行外部資料表所定義的資料分佈。如果您的實際資料分佈與資料表定義中指定的散發不同，您的查詢可能會產生非預期的結果。

* 彈性查詢目前不會執行分區刪除，因為分區化索引鍵的述詞允許安全地排除處理某些分區。

* 彈性查詢最適合可在分區完成大部分運算的查詢。使用可在分區上評估的選擇性篩選述詞，或在所有分區上以資料分割對齊方式來聯結資料分割索引鍵，通常可以獲得最佳查詢效能。其他查詢模式可能需要從分區載入大量資料至前端節點，但效能可能不佳。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->

<!---HONumber=AcomDC_0204_2016-->