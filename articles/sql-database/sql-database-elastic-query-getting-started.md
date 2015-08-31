<properties
	pageTitle="開始使用彈性資料庫查詢"
	description="如何使用彈性資料庫查詢"
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
	ms.date="06/23/2015"
	ms.author="sidneyh" />

# 開始使用彈性資料庫查詢

Azure SQL Database 彈性資料庫查詢 (預覽) 可讓您執行使用單一連接點跨越多個資料庫的 T-SQL 查詢。如需有關彈性資料庫查詢功能的詳細資訊，請參閱 [功能概觀頁](sql-database-elastic-query-overview.md)。

本主題會延伸[彈性資料庫工具入門](sql-database-elastic-scale-get-started.md)中可找到的範例。完成時，您將：了解如何設定和使用 Azure SQL Database 以執行跨越多個相關資料庫的查詢。
## 必要條件

下載並執行[彈性資料庫工具範例入門](sql-database-elastic-scale-get-started.md)。

## 使用範例應用程式建立分區對應管理員

在這裡，您將建立分區對應管理員以及數個分區，接著插入資料至分區。若您的分區設定中已有分區資料，則可以略過下列步驟並移至下一節。

1. 建置並執行**彈性資料庫工具入門**範例應用程式。遵循步驟，直到[下載及執行範例應用程式](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools)小節的步驟 7。在步驟 7 結束時，您會看到下列的命令提示字元：

	![命令提示字元][1]

2.  在命令視窗中，輸入 "1"，然後按 **Enter**。這會建立分區對應管理員，並加入兩個分區到伺服器。然後輸入 "3"，然後按 **Enter**；重複此動作四次。這會在您的分區中插入範例資料列。
3.  [Azure Preview 入口網站](https://portal.azure.com)應該在 v12 伺服器中顯示三個新的資料庫：

	![Visual Studio 確認][2]

	目前，跨資料庫查詢是透過彈性資料庫用戶端程式庫支援。例如，在命令視窗中使用第 4 個選項。來自多個分區查詢的結果永遠是所有分區的結果的**全部聯集**。

	在下一節中，我們會建立支援更豐富的跨分區資料查詢的範例資料庫端點。

## 建立彈性的查詢資料庫

1. 開啟 [Azure Preview 入口網站](https://portal.azure.com)並登入。
2. 在與分區安裝程式相同的伺服器中建立新的 Azure SQL Database。將資料庫命名為 "ElasticDBQuery"。 針對定價層，您必須選取其中一個 Premium 提供項目。彈性資料庫查詢目前僅適用於 Premium 層。

	![Azure 入口網站和定價層][3]

	附註：您可以使用現有的 Premium 資料庫。如果您可以這樣做，它不得是您想要對其執行查詢的其中一個分區。此資料庫將用於為彈性資料庫查詢建立中繼資料物件。


## 建立資料庫物件

### 資料庫範圍的主要金鑰和認證

這些是用來連接到分區對應管理員和分區：

1. 在 Visual Studio 中開啟 SQL Server Management Studio 或 SQL Server Data Tools
2. 連接至 ElasticDBQuery 資料庫，並執行下列 T-SQL 命令：

		CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

		CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
		WITH IDENTITY = '<username>',
		SECRET = '<password>';

	"username" 和 "password" 應該與[彈性資料庫工具入門](sql-database-elastic-scale-get-started.md)中[下載及執行範例應用程式](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools)的步驟 6 中所使用的登入資訊相同。

### 外部資料來源

若要建立外部資料來源，請在 ElasticDBQuery 資料庫上執行下列命令：

	CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
	  CREDENTIAL = ElasticDBQueryCred,
 	  SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 如果您使用彈性資料庫工具範例來建立分區對應和分區對應管理員，"CustomerIDShardMap" 會是分區對應的名稱。不過，如果您為此範例使用您的自訂安裝程式，它應該是您在應用程式中選擇的分區對應名稱。

### 外部資料表

在 ElasticDBQuery 資料庫上執行下列命令，建立符合分區上的客戶資料表外部資料表：

	CREATE EXTERNAL TABLE [dbo].[Customers]
	( [CustomerId] [int] NOT NULL,
	  [Name] [nvarchar](256) NOT NULL,
	  [RegionId] [int] NOT NULL)
	WITH
	( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
	) ;

## 執行範例彈性資料庫 T-SQL 查詢

一旦您已定義外部資料來源和外部資料表，現在您可以對外部資料表使用完整的 T-SQL。

在 ElasticDBQuery 資料庫上執行此查詢：

	select count(CustomerId) from [dbo].[Customers]

您會注意到查詢會從所有分區彙總結果，並提供下列輸出：

![輸出詳細資料][4]

## 匯入彈性資料庫查詢結果到 Excel

 您可以從查詢的結果匯入到 Excel 檔案。

1. 啟動 Excel 2013。
2. 	瀏覽至 [**資料**] 功能區。
3. 	按一下 [**從其他來源**]，然後按一下 [**從 SQL Server**]。

	![從其他來源的 Excel 匯入][5]
4. 	在 [**資料連線精靈**] 中，輸入伺服器名稱和登入認證。然後按 [下一步]。
5. 	在對話方塊 [**選取包含您想要的資料的資料庫**] 中，選取 **ElasticDBQuery** 資料庫。
6. 	在清單檢視中選取 [**客戶**] 資料表，然後按 [**下一步**]。然後按一下 [**完成**]。
7. 	在 [**匯入資料**] 表單中，於 [**選取您要在活頁簿中檢視此資料的方式**] 下，選取 [**資料表**]，然後按一下 [**確定**]。

儲存在不同分區中、來自 [**客戶**] 資料表的所有資料列會填入 Excel 工作表。

## 後續步驟
您現在可以使用 Excel 強大資料功能。您可以使用具備您的伺服器名稱、資料庫名稱和認證的連接字串來連接您的 BI 和資料整合工具至彈性查詢資料庫。請確定 SQL Server 可支援做為您的工具的資料來源。您可以參考彈性查詢資料庫和外部資料表，就如同您會使用您的工具連接的任何其他 SQL Server 資料庫和 SQL Server 資料表。

### 成本
使用彈性資料庫查詢功能不另行收費。不過，目前這項功能僅適用於 Premium 資料庫做為端點，但分區可以是任何服務層。

如需定價資訊，請參閱 [SQL Database 定價詳細資料](http://azure.microsoft.com/pricing/details/sql-database/)。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->

<!---HONumber=August15_HO8-->