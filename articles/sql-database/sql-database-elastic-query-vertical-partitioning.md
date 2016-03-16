<properties
    pageTitle="跨資料庫查詢 (垂直資料分割) 的彈性資料庫查詢 | Microsoft Azure"
    description="如何透過垂直資料分割設定跨資料庫查詢"    
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
    ms.date="01/06/2016"
    ms.author="torsteng;sidneyh" />

# 跨資料庫查詢 (垂直資料分割) 的彈性資料庫查詢

本文件說明如何設定跨資料庫查詢案例 (垂直資料分割) 的彈性查詢以及如何執行您的查詢。如需垂直資料分割案例的定義，請參閱 [Azure SQL Database 彈性資料庫查詢概觀 (預覽)](sql-database-elastic-query-overview.md)。

![在不同資料庫中跨資料表查詢][1]

## 建立資料庫物件

垂直資料分割的案例下，彈性查詢會延伸目前的 T-SQL DDL 參考儲存在遠端資料庫的資料表。本節提供設定彈性查詢以透明方式存取遠端資料表的 DDL 陳述式概觀。這些 DDL 陳述式可讓您在本機資料庫中建立遠端資料表的中繼資料表示法。

**請注意**：與水平資料分割不同的是，這些 DDL 陳述式不會依靠透過彈性資料庫用戶端程式庫定義資料層與分區對應。

定義彈性資料庫查詢的資料庫物件依賴下列 T-SQL 陳述式，在以下的垂直資料分割案例中會進一步說明：

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
    
刪除憑證：
    
    DROP DATABASE SCOPED CREDENTIAL <credential_name>;  
    DROP MASTER KEY;   

 
請確定 *< username>* 不含任何 *"@servername"* 後置詞。

### 1\.2 外部資料來源

您可藉由定義外部資料來源以提供遠端資料庫的資訊給彈性查詢。建立和卸除外部資料來源的語法定義如下：

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

請注意將此資料來源定義為 RDBMS 的 TYPE 參數。

您可以使用下列陳述式卸除外部資料來源：

    DROP EXTERNAL DATA SOURCE <data_source_name>[;]

#### CREATE/DROP EXTERNAL DATA SOURCE 的權限 

使用者必須擁有 ALTER ANY EXTERNAL DATA SOURCE 權限。此權限包含在 ALTER DATABASE 權限中。

**範例**

下列範例說明對外部資料來源使用 CREATE 陳述式。

	CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
	WITH 
	( 
		TYPE=RDBMS, 
		LOCATION='myserver.database.windows.net', 
		DATABASE_NAME='ReferenceData', 
		CREDENTIAL= SqlUser 
	); 
 
從下列目錄檢視擷取目前的外部資料來源清單：

    select * from sys.external_data_sources; 

### 1\.3 外部資料表 

彈性的查詢會延伸現有的外部資料表語法來定義使用 RDBMS 類型外部資料來源的外部資料表。垂直資料分割的外部資料表定義包含下列各方面：

* **結構描述**：外部資料表 DDL 會定義您的查詢可以使用的結構描述。外部資料表定義中提供的結構描述必須符合實際資料儲存所在之遠端資料庫中資料表的結構描述。 

* **遠端資料庫參考**：外部資料表 DDL 指的是外部資料來源。外部資料來源可指定邏輯伺服器名稱和實際資料表資料儲存所在之遠端資料庫的資料庫名稱。

如上一節所述使用外部資料來源，建立外部資料表的語法如下：

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
	{ WITH ( <rdbms_external_table_options> ) } 
	)[;] 
	
	<rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

DATA\_SOURCE 子句會定義用於外部資料表的外部資料來源 (亦即垂直資料分割情形中的遠端資料庫)。

SCHEMA\_NAME 和 OBJECT\_NAME 子句提供的功能可將外部資料表定義分別對應至遠端資料庫上不同結構描述中的資料表，或對應至不同名稱的資料表。如果您想要為目錄檢視或是在遠端資料庫上的 DMV 定義外部資料表 – 或在遠端資料表名稱已在本機被使用的任何情況中，這個方法都很實用。

下列 DDL 陳述式會從本機目錄卸除現有的外部資料表定義。它不會影響遠端資料庫。

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]  

**CREATE/DROP EXTERNAL TABLE 的權限**：ALTER ANY EXTERNAL DATA SOURCE 權限對外部資料表 DDL 而言是必要的，而後者在參考基礎資料來源時也是必要的。

**安全性考量**：可存取外部資料表的使用者可以在外部資料來源定義中所提供的認證下，自動取得基礎遠端資料表的存取權。您應該仔細管理外部資料表的存取權，以避免透過外部資料來源的認證所造成的意外權限提升。一般的 SQL 權限可用來授與或撤銷外部資料表的存取權，如同它是一般資料表那樣。


 **範例**：下列範例說明如何建立外部資料表：

	CREATE EXTERNAL TABLE [dbo].[customer]( 
		[c_id] int NOT NULL, 
		[c_firstname] nvarchar(256) NULL, 
		[c_lastname] nvarchar(256) NOT NULL, 
		[street] nvarchar(256) NOT NULL, 
		[city] nvarchar(256) NOT NULL, 
		[state] nvarchar(20) NULL, 
		[country] nvarchar(50) NOT NULL, 
	) 
	WITH 
	( 
	       DATA_SOURCE = RemoteReferenceData 
	); 

下列範例顯示如何從目前資料庫擷取外部資料表清單：

	select * from sys.external_tables; 

## 查詢

### 2\.1 完整的 T-SQL 查詢 

一旦您已定義外部資料來源和外部資料表，現在您可以對外部資料表使用完整的 T-SQL。

**垂直資料分割的範例**：下列查詢會執行訂單和訂單明細的兩個本機資料表與客戶遠端資料表之間的三方聯結。這是彈性查詢的參考資料使用案例的範例：

	SELECT  	
	 c_id as customer,
	 c_lastname as customer_name,
	 count(*) as cnt_orderline, 
	 max(ol_quantity) as max_quantity,
	 avg(ol_amount) as avg_amount,
	 min(ol_delivery_d) as min_deliv_date
	FROM customer 
	JOIN orders 
	ON c_id = o_c_id
	JOIN  order_line 
	ON o_id = ol_o_id and o_c_id = ol_c_id
	WHERE c_id = 100

  
## 工具的連線能力

您可以使用一般 SQL Server 連接字串，在啟用彈性查詢及定義外部資料表的 SQL DB 伺服器上，將 BI 和資料整合工具連接到資料庫。請確定 SQL Server 可支援做為您的工具的資料來源。然後參考彈性查詢資料庫和其外部資料表，就如同您會使用您的工具連接的任何其他 SQL Server 資料庫。

## 最佳作法 
 
* 請確定遠端資料庫已藉由在 Azure 服務的 SQL DB 防火牆組態中啟用其存取權，獲得彈性查詢端點資料庫的存取權。也請確定外部資料來源定義中提供的認證可以成功登入遠端資料庫，而且具有存取遠端資料表的權限。  

* 彈性查詢最適合可在遠端資料庫上完成大部分運算的查詢。使用可在遠端資料庫上評估的選擇性篩選述詞，或可在遠端資料庫上完全執行的聯結，通常可以獲得最佳查詢效能。其他查詢模式可能需要從遠端資料庫載入大量的資料，而且執行效能可能會很差。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->

<!---HONumber=AcomDC_0107_2016-->