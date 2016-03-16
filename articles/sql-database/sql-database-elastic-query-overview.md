<properties
    pageTitle="Azure SQL Database 彈性資料庫查詢概觀 | Microsoft Azure"
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
    ms.date="01/22/2016"
    ms.author="torsteng" />

# Azure SQL Database 彈性資料庫查詢概觀 (預覽)

彈性資料庫查詢功能 (預覽) 可讓您跨越 Azure SQL Database 中的多個資料庫執行 Transact-SQL 查詢 (SQLDB)。它可讓您執行跨資料庫查詢以存取遠端資料表，以及將 Microsoft 和協力廠商工具 (Excel、PowerBI、Tableau 等) 連接到具有多個資料庫的資料層。這項功能可讓您將查詢相應放大到 SQL Database 中的大型資料層，並將結果透過商務智慧 (BI) 報告視覺化。若要開始建置彈性資料庫查詢應用程式，請參閱[開始使用彈性資料庫查詢](sql-database-elastic-query-getting-started.md)。

## 彈性資料庫查詢的新功能

* 現在可以用 T-SQL 完全定義具有單一遠端資料庫的跨資料庫查詢案例。如此即可進行遠端資料庫的唯讀查詢。目前內部部署 SQL Server 客戶可選擇使用三和四部分的名稱或 SQL DB 的連結伺服器來移轉應用程式。
* 除了進階效能層以外，標準效能層現在也支援彈性查詢。請參閱下面「預覽限制」一節中較低效能層級的效能限制。
* 彈性查詢現在可以將 SQL 參數發送至遠端資料庫以供執行。
* 使用 sp\_execute\_fanout 的遠端預存程序呼叫或遠端函式引動現在可以使用類似於 [sp\_executesql](https://msdn.microsoft.com/library/ms188001.aspx) 的參數。
* 從遠端資料庫擷取龐大結果集的效能已獲得改善。
* 具有彈性查詢的外部資料表現在可以參考具有不同結構描述或資料表名稱的遠端資料表。

## 彈性資料庫查詢案例

目標是協助查詢案例便利進行，其中由多個資料庫提供資料列給單一整體結果。查詢可以由使用者或應用程式直接撰寫，或透過連接到資料庫的工具來間接撰寫。使用商業 BI 或資料整合工具 (或無法變更的任何應用程式) 建立報告時，這特別有用。透過彈性查詢，您可以在 Excel、PowerBI、Tableau 或 Cognos 等工具中使用熟悉的 SQL Server 連線體驗，進而查詢多個資料庫。彈性查詢可讓您透過 SQL Server Management Studio 或 Visual Studio 所發出的查詢，輕鬆存取整個資料庫集合，並協助更方便從 Entity Framework 或其他 ORM 環境執行跨資料庫查詢。圖 1 顯示的案例中，現有的雲端應用程式 (使用[彈性資料庫用戶端程式庫](sql-database-elastic-database-client-library.md)) 根據相應放大的資料層建置，而彈性查詢用於跨資料庫報告。

**圖 1** 相應放大的資料層上使用的彈性資料庫查詢

![向相應放大資料層上使用的彈性資料庫查詢][1]

彈性查詢的客戶案例可依下列拓撲區分特性：

* **垂直資料分割 - 跨資料庫查詢** (拓撲 1)：資料會垂直分割於資料層中的數個資料庫。一般而言，不同的資料表集位於不同的資料庫。這表示不同資料庫的結構描述不同。比方說，庫存的所有資料表都位於一個資料庫上，而所有會計相關資料表則位於另一個資料庫上。此拓撲的常見使用案例會要求使用者跨多個資料庫中的資料表進行查詢或編譯報表。
* **水平資料分割 - 分區化** (拓撲 2)：資料會進行水平分割，以將資料列分散於相應放大的資料層。使用此方法時，所有參與資料庫的結構描述都相同。這個方法也稱為「分區化」。使用 (1) 彈性資料庫工具程式庫或 (2) 自行分區化可以執行和管理分區化。彈性查詢用於查詢或編譯跨多個分區的報表。

> [AZURE.NOTE] 彈性資料庫查詢最適合可在資料層執行大部分處理的非經常性報告案例。對於繁重的報告工作負載或有更多複雜查詢的資料倉儲案例，也請考慮使用 [Azure SQL 資料倉儲](https://azure.microsoft.com/services/sql-data-warehouse/)。


## 彈性資料庫查詢案例

### 拓撲 1：垂直資料分割 - 跨資料庫查詢

若要開始撰寫程式碼，請參閱[開始使用跨資料庫查詢 (垂直資料分割)](sql-database-elastic-query-getting-started-vertical.md)。

彈性查詢可讓位於 SQLDB 資料庫中的資料可供其他 SQLDB 資料庫使用。如此一來，來自一個資料庫的查詢即可參考任何其他遠端 SQLDB 資料庫中的資料表。第一個步驟是定義每個遠端資料庫的外部資料來源。外部資料來源已定義於本機資料庫中，您想要從中取得遠端資料庫上資料表的存取權。遠端資料庫不需要進行任何變更。在不同資料庫有不同結構描述的典型垂直資料分割案例中，彈性查詢可用來實作常見使用案例，例如存取參考資料和跨資料庫查詢。

**參考資料**：拓撲 1 用於參考資料管理。在下圖中，包含參考資料的兩個資料表 (T1 和 T2) 會保留在專用的資料庫上。利用彈性查詢，您現在可以在遠端從其他資料庫存取資料表 T1 和 T2，如圖所示。如果參考資料表很小或參考資料表的遠端查詢有選擇性述詞，則使用拓撲 1。

**圖 2** 垂直資料分割 - 使用彈性查詢來查詢參考資料

![垂直資料分割 - 使用彈性查詢來查詢參考資料][3]

**跨資料庫查詢**：彈性查詢能夠達成需要進行跨多個 SQLDB 資料庫查詢的使用案例。圖 3 顯示四個不同的資料庫：CRM、庫存、HR 和產品。在其中一個資料庫中執行的查詢也需要存取另一個或其他所有資料庫。利用彈性查詢，您可以在上述每個資料庫上執行一些簡單的 DDL 陳述式，針對此案例設定您的資料庫。進行此一次性設定之後，存取遠端資料表就像從 T-SQL 查詢或從 BI 工具參考本機資料表一樣簡單。如果遠端查詢未傳回大量結果，則建議使用這個方法。

**圖 3** 垂直資料分割 - 使用彈性查詢來查詢各種資料庫

![垂直資料分割 - 使用彈性查詢來查詢各種資料庫][4]

### 拓撲 2：水平資料分割 - 分區化

若要開始撰寫程式碼，請參閱[開始使用彈性資料庫查詢進行水平資料分割 (分區化)](sql-database-elastic-query-getting-started.md)

使用彈性查詢在分區化 (即水平分割) 的資料層執行報告工作時，需要[彈性資料庫分區對應](sql-database-elastic-scale-shard-map-management.md)來代表資料層的資料庫。一般而言，這種案例中只會使用單一分區對應，並以具有彈性查詢功能的專用資料庫做為報告查詢的進入點。只有這個專用的資料庫需要存取分區對應。圖 2 說明此拓撲及其彈性查詢資料庫和分區對應的組態。請注意，只有彈性查詢資料庫必須是 Azure SQL Database v12 資料庫。資料層中的資料庫可以是任何 Azure SQL Database 版本。如需有關彈性資料庫用戶端程式庫和建立分區對應的詳細資訊，請參閱[分區對應管理](sql-database-elastic-scale-shard-map-management.md)。

**圖 4** 水平資料分割 - 使用彈性查詢來報告分區化資料層

![水平資料分割 - 使用彈性查詢來報告分區化資料層][5]

> [AZURE.NOTE] 專用彈性資料庫查詢資料庫必須是 SQL DB v12 資料庫。分區本身沒有任何限制。


## 實作彈性資料庫查詢

下列各節討論對垂直和水平資料分割案例實作彈性查詢的步驟。這些章節也會參考更詳細的文件，以取得不同的資料分割案例。

### 垂直資料分割 - 跨資料庫查詢

下列步驟可針對需要存取位於某個遠端 SQLDB 資料庫上的某個資料表的垂直資料分割案例，設定彈性資料庫查詢：

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource (類型為 **RDBMS**)
*    [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) mytable

執行 DDL 陳述式之後，您可以存取遠端資料表 "mytable"，就像存取本機資料表一樣。Azure SQL Database 會自動開啟遠端資料庫的連線、處理您對遠端資料庫的要求，以及傳回結果。如需垂直資料分割案例所需步驟的詳細資訊，請參閱[垂直資料分割的彈性查詢](sql-database-elastic-query-vertical-partitioning.md)。

### 水平資料分割 - 分區化

下列步驟可針對需要存取 (通常) 位於數個遠端 SQLDB 資料庫上的一組資料表的水平資料分割案例，設定彈性資料庫查詢：

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    使用彈性資料庫用戶端程式庫，建立代表您的資料層的[分區對應](sql-database-elastic-scale-shard-map-management.md)。   
*    [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource (類型為 **SHARD\_MAP\_MANAGER**)
*    [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) mytable

執行這些步驟後，您即可存取水平分割的資料表 "mytable"，就像存取本機資料表一樣。Azure SQL Database 會自動開啟遠端資料庫 (實際儲存資料表的位置) 的多個平行連線、處理對於遠端資料庫的要求，以及傳回結果。如需水平資料分割案例所需步驟的詳細資訊，請參閱[水平資料分割的彈性查詢](sql-database-elastic-query-horizontal-partitioning.md)。

## T-SQL 查詢
一旦您已定義外部資料來源和外部資料表，您可以使用一般 SQL Server 連接字串來連接到您定義外部資料表的資料庫。您可以接著對該連線上的外部資料表執行 T-SQL 陳述式，其限制如下所述。您可以在[水平資料分割](sql-database-elastic-query-horizontal-partitioning.md)和[垂直資料分割](sql-database-elastic-query-vertical-partitioning.md)的文件主題中找到 T-SQL 查詢的詳細資訊和範例。

## 工具的連線能力
您可以使用一般 SQL Server 連接字串，將您的應用程式、BI 或資料整合工具連接到具有外部資料表的資料庫。請確定 SQL Server 可支援做為您的工具的資料來源。連線之後，請參考彈性查詢資料庫和該資料庫中的外部資料表，就如同您會使用您的工具連接的任何其他 SQL Server 資料庫一樣。

## 成本

彈性查詢算在 Azure SQL Database 資料庫的成本內。請注意，支援遠端資料庫與彈性查詢端點位於不同資料中心的拓撲，但從遠端資料庫輸出的資料以一般 [Azure 費率](https://azure.microsoft.com/pricing/details/data-transfers/)收費。

## 預覽限制
* 在標準效能層上執行第一個彈性查詢最多可能需要幾分鐘的時間。需要這些時間才能載入彈性查詢功能；較高效能層級改善了載入效能。
* 尚未支援來自 SSMS 或 SSDT 的外部資料來源或外部資料表的指令碼。
* SQL DB 匯入/匯出還不支援外部資料來源和外部資料表。如果您需要使用匯入/匯出，請在匯出前卸除這些物件，然後在匯入後予以重新建立。
* 彈性資料庫查詢目前僅支援外部資料表的唯讀存取。不過，您可以在定義外部資料表的資料庫上使用完整的 T-SQL 功能。這很有用，例如，使用 SELECT <column_list> INTO <local_table> 保存暫存結果，或在彈性查詢資料庫上定義預存程序來參考外部資料表。
* 除了 nvarchar (max) 以外，外部資料表定義不支援 LOB 類型。若要解決此問題，您可以在將 LOB 類型轉型成 nvarchar (max) 的遠端資料庫上建立檢視表、透過此檢視表而非基底資料表定義外部資料表，然後在查詢中將它轉換回原始的 LOB 類型。
* 目前不支援外部資料表的資料行統計資料。支援資料表統計資料，但必須以手動方式建立。

## 意見反應
請在下面 Disqus、MSDN 論壇或 Stackoverflow 上，與我們分享您的彈性查詢體驗意見。我們很樂意接受關於服務的各種意見 (缺失、不完善、功能落差)。

## 詳細資訊

您可以在下列文件中找到有關跨資料庫查詢和垂直資料分割案例的詳細資訊：

* [跨資料庫查詢和垂直資料分割概觀](sql-database-elastic-query-vertical-partitioning.md)
* 請嘗試我們的逐步教學課程，立即體驗完整的實用範例：[開始使用跨資料庫查詢 (垂直資料分割)](sql-database-elastic-query-getting-started-vertical.md)。


以下可取得更多有關水平資料分割和分區化案例的資訊：

* [水平資料分割和分區化概觀](sql-database-elastic-query-horizontal-partitioning.md)
* 請嘗試我們的逐步教學課程，立即體驗完整的實用範例：[開始使用彈性資料庫查詢進行垂直資料分割 (分區化)](sql-database-elastic-query-getting-started.md)。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->

<!---HONumber=AcomDC_0128_2016-->