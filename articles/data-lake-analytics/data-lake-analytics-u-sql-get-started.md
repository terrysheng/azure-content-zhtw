<properties 
   pageTitle="使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼 | Azure" 
   description="了解如何安裝適用於 Visual Studio 的資料湖工具，如何開發和測試 U-SQL 指令碼。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/26/2015"
   ms.author="jgao"/>

# 教學課程：開始使用 Azure 資料湖分析 U-SQL 語言

了解如何開發 U-SQL 指令碼。

U-SQL 語言結合了 SQL 的所有優點，可運用您自有程式碼的運算式能力來處理任何規模的資料。U-SQL 的可調整分散式查詢功能可讓您有效率地分析存放區中的資料，以及跨 Azure SQL Database 等關聯式存放區分析資料。它可讓您藉由套用「讀取時的結構描述」(Schema-on-Read) 處理非結構化資料、插入自訂邏輯和 UDF，並可經由擴充功能來對如何大規模執行資料分析進行更細緻的掌控。若要深入了解 U-SQL 背後的設計原理，請參閱此 [Visual Studio 部落格文章](http://blogs.msdn.com/b/visualstudio/archive/2015/09/28/introducing-u-sql.aspx)。

U-SQL 與 ANSI SQL 或 T-SQL 有一些差異。例如，其關鍵字 (像是 SELECT) 一定要全是大寫字。

select 子句內部是型別系統和運算式語言，在這裡面述詞等項目是以 C# 來撰寫。這表示資料型別是屬於 C# 型別且資料型別使用 C# NULL 語意，而述詞內的比較作業會遵循 C# 語法 (例如 == "foo")。這也表示值全都是 .NET 物件，可讓您輕鬆地使用任何方法來操作物件 (例如 "f o o o".Split(' ') )。

如需詳細資訊，請參閱 [U-SQL 參考](http://go.microsoft.com/fwlink/p/?LinkId=691348)。

**必要條件**

- **已安裝 Visual C++ 的 Visual Studio 2015、Visual Studio 2013 更新 4 或 Visual Studio 2012** 
- **Microsoft Azure SDK for .NET 2.5 版或更新版本**。使用 [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) 來進行安裝。
- **[適用於 Visual Studio 的資料湖工具](http://aka.ms/adltoolsvs)**。 
	
	安裝適用於 Visual Studio 的資料湖工具之後，您會在 Visual Studio 中看到 [資料湖]功能表：
	
	![U-SQL Visual Studio 功能表](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **對於資料湖分析和適用於 Visual Studio 的資料湖工具有基本的認識**。若要開始使用，請參閱：
 
	- [使用 Azure Preview 入口網站開始使用 Azure 資料湖分析](data-lake-analytics-get-started-portal.md)。
	- [使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。

- **資料湖分析帳戶**。請參閱[建立 Azure 資料湖 (ADL) 分析帳戶](data-lake-analytics-get-started-portal.md#create_adl_analytics_account)。
- **將範例資料上傳到資料湖分析帳戶**。請參閱[將 SearchLog.tsv 上傳到預設資料湖儲存體帳戶](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account)。

	資料湖工具不支援建立資料湖分析帳戶。因此您必須使用 Azure Preview 入口網站、Azure PowerShell、.NET SDK 或 Azure CLI 建立帳戶。若要執行資料湖分析工作，您需要一些資料。即使資料湖工具支援上傳資料，您將使用入口網站來上傳範例資料，以方便遵循本教學課程。

## 從 Visual Studio 連接至 Azure

您必須先連接至 Azure，然後才能建置及測試任何 U-SQL 指令碼。

**連接到資料湖分析**

1. 開啟 Visual Studio。
2. 從 [資料湖] 功能表，按一下 [選項和設定]。
4. 按一下 [登入]，或者如果已有其他人登入則按一下 [變更使用者]，並遵循指示進行登入。
5. 按一下 [確定] 關閉 [選項和設定] 對話方塊。

**瀏覽您的資料湖分析帳戶**

1. 從 Visual Studio 中，按 **CTRL+ALT+S**，開啟 [伺服器總管]。
2. 從 [伺服器總管] 中，展開 [Azure]，然後展開 [資料湖分析]。如果有資料湖分析帳戶，您就會看到其清單。您無法從 Visual Studio 建立資料湖分析帳戶。若要建立帳戶，請參閱[使用 Azure Preview 入口網站開始使用 Azure 資料湖分析](data-lake-analytics-get-started-portal.md)或[使用 Azure PowerShell 開始使用 Azure 資料湖分析](data-lake-get-started-powershell.md)。


## 開發您的第一個 U-SQL 指令碼 

本節的主要目的是要了解使用適用於 Visual Studio 的資料湖工具來撰寫和測試 U-SQL 應用程式的程序。其他資料湖分析教學課程也會使用 U-SQL 陳述式。它會直接讀取定位點分隔檔 (tsv) 檔案並將該檔案輸出為逗號分隔檔 (csv) 檔案。
 
**建立並提交資料湖分析工作**

1. 從 [檔案] 功能表中，按一下 [新增]，再按 [專案]。
2. 選取 [U-SQL 專案] 類型。

	![新的 U-SQL Visual Studio 專案](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
	
3. 按一下 [確定]。Visual Studio 會建立具有 Script.usql 檔案的方案。
4. 在 Script.usql 檔案中輸入下列指令碼：

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/output/SearchLog-first-u-sql.csv"
        USING Outputters.Csv();

		The next section in this article will explain the script in details.  You just need to focus on understanding the development and testing process in this section.
5. 在 [提交] 按鈕旁邊，指定分析帳戶。
5. 從 [方案總管] 中，在 [Script.usql] 上按一下滑鼠右鍵，然後按一下 [建置指令碼]。確認 [輸出] 窗格中的結果。如果您的指令碼有錯誤，您會在此看到錯誤輸出。 
6. 從 [方案總管] 中，在 [Script.usql] 上按一下滑鼠右鍵，然後按一下 [提交指令碼]。
7. 確認 [分析帳戶]，然後按一下 [提交]。提交作業完成時，[適用於 Visual Studio 的資料湖工具結果] 視窗中便會出現提交結果和工作連結。
8. 您可以按一下 [重新整理] 按鈕，查看最新的工作狀態並重新整理畫面。請等待工作成功完成。如果工作失敗，很可能是因為遺漏了原始檔。您可以使用工具中的 [錯誤] 索引標籤來查看服務所傳回的錯誤。請參閱本教學課程的＜必要條件＞一節。如需其他疑難排解資訊，請參閱[監視和疑難排解 Azure 資料湖分析工作](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)。

	
**查看工作輸出**

1. 從 [伺服器總管] 依序展開 [Azure]、[資料湖分析]、您的資料湖分析帳戶和 [儲存體帳戶]，以滑鼠右鍵按一下預設的 [資料湖儲存體帳戶]，然後按一下 [總管]。 
2.  按兩下 [輸出] 以開啟資料夾
3.  按兩下 **SearchLog-frist-u-sql.csv**。
4.  您也可以按兩下工作圖形檢視中的輸出檔，直接瀏覽至輸出檔。

## 了解第一個 U-SQL 指令碼

讓我們仔細看看您在上一節撰寫的指令碼：

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/output/SearchLog-first-usql.csv"
        USING Outputters.Csv();

此指令碼沒有任何轉換步驟。它會從原始程式檔 **SearchLog.tsv** 讀取資料，為其建立結構描述，並將資料列集輸出回 **SearchLog-from-adltools.csv** 檔案。

請注意 [持續時間] 欄位的資料類型旁邊的問號。該符號表示 [持續時間] 欄位可能是 null。

以下是指令碼中的一些概念和關鍵字：

- **資料列集變數**：每個會產生資料列集的查詢運算式都可以指派給變數。U-SQL 遵循 T-SQL 的變數命名模式，例如指令碼中的 **@searchlog**。請注意，U-SQL 不會強制執行指派工作。它只是命名運算式，並讓您能夠建置更複雜的運算式。
- **EXTRACT** 讓您能夠定義「讀取時的結構描述」。結構描述是透過每個資料行的資料行名稱和 C# 型別名稱配對來加以指定。它會使用所謂的**擷取器** (例如 **Extractors.Tsv()**) 來擷取 tsv 檔案。您可以開發自訂擷取器。
- **OUTPUT** 會抓取資料列集並將其序列化。Outputters.Csv() 會將逗號分隔檔輸出到指定的位置。您也可以開發自訂輸出器。
- 請注意，指令碼中的兩個路徑是相對路徑。您也可以使用絕對路徑。例如 
    
        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    您必須使用絕對路徑，才能存取所連結儲存體帳戶中的檔案。儲存在連結 Azure 儲存體帳戶中的檔案語法為：
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]目前不支援具有公用 Blob 或公用容器存取權的 Azure Blob 容器。

## 使用純量變數

您也可以使用純量變數以方便維護指令碼。您的第一個 U-SQL 指令碼也可以撰寫成下面這樣：

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";
    
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();
    
    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();
      
##轉換資料列集

使用 **SELECT** 來轉換資料列集：

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";
    
    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

WHERE 子句使用 [C# 布林運算式](https://msdn.microsoft.com/library/6a71f45d.aspx)。您可以使用 C# 運算式語言來建置自己的運算式和函式。您甚至可以將它們與邏輯結合 (AND) 和邏輯分離 (OR) 做結合，以執行更複雜的篩選。

下列指令碼使用 DateTime.Parse() 方法和邏輯結合。

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");
    
    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();
        
請注意，第二個查詢會對第一個資料列集的結果起作用，因此最終結果是兩個篩選條件的組合。您也可以重複使用變數名稱，因為它們是語彙範圍型名稱。

##彙總資料列集

U-SQL 提供您已熟悉使用的 **ORDER BY**、**GROUP BY** 和各種彙總語法。

下列查詢會尋找每個區域的總持續時間，然後按順序輸出前 5 大持續時間。

U-SQL 資料列集不會保留它們的順序以供下一次查詢使用。因此，若要對輸出排序，您需要將 ORDER BY 加入 OUTPUT 陳述式，如下所示：

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";
    
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;
    
    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;
    
    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2 
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
        
U-SQL 的 ORDER BY 子句必須搭配 FETCH 子句一起用在 SELECT 運算式中。

U-SQL 的 HAVING 子句可以用來將輸出限制為符合 HAVING 條件的群組：

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;
    
    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();


## 聯結資料

U-SQL 提供常見的聯結運算子，例如 INNER JOIN、LEFT/RIGHT/FULL OUTER JOIN、SEMI JOIN，不僅可聯結資料表，還可以連結任何資料列集 (即使是由檔案所產生的資料列集)。

下列指令碼聯結了搜尋日誌與廣告印象日誌，並提供我們特定日期的查詢字串的廣告。

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();
    
    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s 
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;
    
    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U-SQL 只支援 ANSI 相容聯結語法：Rowset1 JOIN Rowset2 ON 述詞。不支援舊有的 FROM Rowset1, Rowset2 WHERE 述詞語法。JOIN 中的述詞必須是等號比較聯結且沒有運算式。如果您想要使用運算式，請將它加入前一個資料列集的 select 子句。如果您想要進行不同的比較，則可將它移至 WHERE 子句。

        
## 建立資料庫、資料表值函式、檢視和資料表

U-SQL 可讓您在資料庫和結構描述的內容中使用資料。因此您不必一直對檔案進行讀取或寫入。

每個 U-SQL 指令碼在執行時，都會有預設資料庫 (主要) 與預設結構描述 (dbo) 做為其預設內容。您可以建立自己的資料庫和 (或) 結構描述。若要變更內容，請使用 **USE** 陳述式來變更內容。


### 建立資料表值函式 (TVF)

在先前的 U-SQL 指令碼中，您重複使用會從相同原始程式檔進行讀取的 EXTRACT。U-SQL 資料表值函式可讓您封裝資料以供日後重複使用。

下列指令碼會在預設資料庫和結構描述中建立名為 *Searchlog()* 的 TVF。

    DROP FUNCTION IF EXISTS Searchlog;
    
    CREATE FUNCTION Searchlog() 
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN 
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;
    
下列指令碼會示範如何使用先前的指令碼中定義的 TVF：

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;
    
    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
        
### 建立檢視

如果您只有一個想要抽取但不要予以參數化的查詢運算式，您可以建立檢視而不是資料表值函式。

下列指令碼會在預設資料庫和結構描述中建立名為 *SearchlogView* 的檢視：

    DROP VIEW IF EXISTS SearchlogView;
    
    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    
下列指令碼示範使用定義的檢視：

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;
    
    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### 建立資料表 

U-SQL 與關聯式資料庫資料表類似，可讓您使用預先定義的結構描述建立資料表，或建立資料表並從填入資料表的查詢推斷結構描述 (也就是 CREATE TABLE AS SELECT 或 CTAS)。

下列指令碼會建立一個資料庫和兩個資料表：

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
    USE DATABASE SearchLogDb;
    
    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;
    
    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,
    
                INDEX sl_idx CLUSTERED (UserId ASC) 
                    PARTITIONED BY HASH (UserId)
    );
    
    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;
    
    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC) 
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### 查詢資料表

您可以運用和查詢資料檔案一樣的方式來查詢資料表 (上一個指令碼所建立的)。您現在可以直接參考資料表名稱，而不必使用 EXTRACT 建立資料列集。

您先前使用的轉換指令碼會修改成從資料表進行讀取：

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;
    
    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;
    
    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

請注意，您目前無法在用來建立資料表的相同指令碼中，對該資料表執行 SELECT。


##結論

本教學課程所涵蓋的內容僅是 U-SQL 的一小部分。本教學課程的範圍有限，不可能囊括所有內容，例如：

- 使用 CROSS APPLY 將局部字串、陣列和對應解除封裝到資料列。
- 操作經過分割的各組資料 (檔案集和經過分割的資料表)。
- 以 C# 開發使用者定義的運算子，例如擷取器、輸出器、處理器、使用者定義的彙總器。
- 使用 U-SQL 視窗函式。
- 使用檢視、資料表值函式和預存程序管理 U-SQL 程式碼。
- 在您的處理節點上執行任意的自訂程式碼。 
- 連接到 Azure SQL Database 並同盟這些資料庫和 U-SQL 與 Azure 資料湖資料的查詢。

## 另請參閱 

- [Microsoft Azure 資料湖分析概觀](data-lake-analytics-overview.md)
- [使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)
- [針對 Azure 資料湖分析工作使用 U-SQL 視窗函式](data-lake-analytics-use-window-functions.md)
- [使用 Azure Preview 入口網站監視和疑難排解 Azure 資料湖分析工作](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## 讓我們知道您的想法

- [建議新的文件集待處理項目](data-lake-analytics-documentation-backlog.md)
- [提交功能要求](http://aka.ms/adlafeedback)
- [在論壇上取得協助](http://aka.ms/adlaforums)
- [提供關於 U-SQL 的意見](http://aka.ms/usqldiscuss)

<!---HONumber=Nov15_HO1-->