<properties linkid="manage-services-hdinsight-sample-sqoop-import-export" urlDisplayName="HDInsight Samples" pageTitle="Sqoop Import-Export sample | Windows Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run Sqoop import and export on HDInsight." umbracoNaviHide="0" disqusComments="1" writer="bradsev" editor="cgronlun" manager="paulettm" title="Sqoop Import-Export sample" />

Sqoop 匯入-匯出範例
===================

本範例主題說明如何使用 Apache Sqoop，從 Windows Azure 上的 SQL 資料庫中，將資料匯入到 Azure HDFS 叢集上的 Hadoop。

在處理非結構化資料和半結構化資料時，例如記錄和檔案，雖然很自然會選擇 Hadoop，但有時也需要處理關聯式資料庫中儲存的結構化資料。

Sqoop 是一種專門在 Hadoop 叢集和關聯式資料庫之間傳送資料的工具。此工具可讓您從 SQL、MySQL 或 Oracle 等關聯式資料庫管理系統 (RDBMS) 中，將資料匯入至 Hadoop 分散式檔案系統 (HDFS)，在 Hadoop 中使用 MapReduce 或 Hive 來轉換資料，然後將資料匯回到 RDBMS。本教學課程中，您將使用 SQL 資料庫做為關聯式資料庫。

Sqoop 是 Cloudera, Inc. 的開放原始碼軟體產品。在 2011 年，Sqoop 的軟體開發從 gitHub 移至 [Apache Sqoop](http://sqoop.apache.org/) 網站。

在 Windows Azure HDInsight 中，Sqoop 是從 Hadoop 命令 Shell 部署到 HDFS 叢集的前端節點上。您將在 Azure 入口網站上，使用 Hadoop 中可用的遠端桌面功能來存取此部署的叢集前端節點。

**您將了解：**

-   如何使用 Windows Azure PowerShell 在 Windows Azure HDInsight 上執行 MapReduce 程式，以分析檔案中包含的資料。

**必要條件**：

您具有 Windows Azure 帳戶，且您的訂閱已啟用 HDInsight 服務。您已安裝 Windows Azure PowerShell 和適用於 Windows Azure HDInsight 的 Powershell 工具，且已設定它們供您的帳戶使用。關於此作法的指示，請參閱＜[開始使用 Windows Azure HDInsight](/zh-tw/manage/services/hdinsight/get-started-hdinsight/)＞

在 SQL 資料庫上設定防火牆時，您也需要目前位置的對外 IP 位址。若要取得此位址，請移至 [WhatIsMyIP](http://www.whatismyip.com/) 網站並記下此位址。稍後在程序中，您也需要 Haddop 叢集前端的對外 IP 位址。您可以用相同的方式取得此 IP 位址。

**大綱**

本主題說明如何執行範例、呈現 MapReduce 程式的 Java 程式碼、總結所學知識及概述一些後續步驟。其中包含下列幾節。

1.  [設定 SQL 資料庫](#set-up-sql)
2.  [使用 Sqoop 將資料從 HDInsigh 匯入到叢集](#java-code)
3.  [摘要](#summary)
4.  [後續步驟](#next-steps)

設定 SQL 資料庫
---------------

TBD:本節的螢幕擷取畫面 - 這仍然是 RDP 方法。

**建立 SQL 資料庫**

1.  登入 Windows Azure 管理入口網站。
2.  依序一下左下角的 [新增]、[資料服務]、[SQL 資料庫]。登入 Windows Azure 帳戶。若要建立資料庫伺服器，按一下頁面左下角的 **[資料庫]** 圖示。

3.  在 **[開始使用]** 頁面，按一下 **[Create a new SQL Database Server]** 選項。

4.  在 **[建立伺服器]** 視窗中，選取與您的帳戶相關聯的訂閱類型 (例如 **[隨收隨付]**)，然後按 **[下一步]**。

5.  在 **[建立伺服器]** 視窗中選取適當的 **[地區]**，然後按 **[下一步]**。

6.  指定 SQL Database 伺服器的伺服器層級主體的登入和密碼，然後按 **[下一步]**。

7.  按 **[加入]** 來指定防火牆規則，以允許目前的位置存取 SQL Database 來上傳 AdventureWorks 資料庫。防火牆會根據每一個要求的來源 IP 位址來授與存取權。請利用本教學課程在預備組態中的 IP 位址來決定要加入的值。指定規則名稱，如畫面所示，但務必使用您的 IP 位址，而不是以下做為說明用途的 IP 位址(您也必須加入 Hadoop 叢集前端節點的對外 IP 位址。若已知道此位址，請現在就加入)。然後按一下 **[完成]** 按鈕。

**匯入 AdventureWorks2012**

1.  從 Adventure Works for SQL Database 網站的 [建議下載] 連結，將 AdventureWorks2012 資料庫下載到本機電腦。

2.  將檔案解壓縮，開啟系統管理員命令提示字元，然後瀏覽至 AdventureWorks2012ForSQLAzure 資料夾內的 AdventureWorks 目錄。

3.  輸入下列命令來執行 CreateAdventureWorksForSQLAzure.cmd：

	CreateAdventureWorksForSQLAzure.cmd servername username password

    例如，如果指派的 SQL Database 伺服器是 b1gl33p，系統管理員使用者名稱是 "Fred"，密碼是 "Secret"，請輸入：

    CreateAdventureWorksForSQLAzure.cmd b1gl33p.database.windows.net Fred@b1gl33p Secret

    此指令碼會建立資料庫、安裝結構描述，在資料庫中填入範例資料。

1.  回到 **WindowsAzurePlatform** 入口網站頁面，在左邊按一下訂閱 (下列範例中是 **[隨收隨付]**)，然後選取資料庫 (這裡的名稱是 wq6xlbyoq0)。**[資料庫名稱]** 資料行中應該會列出 AventureWorks2012。選取它並按下頁面頂端的 **[管理]** 圖示。

2.  出現提示時，輸入 SQL 資料庫的認證，然後按 **[登入]**。

3.  這會在 SQL Database 上開啟 Adventure Works 資料庫的 Web 介面。按下頂端的 [新增查詢]**New Query** 圖示來開啟查詢編輯器。

4.  因為 Sqoop 目前會將資料表名稱加上方括弧，我們必須加上同義字來支援 SQL Server 資料表的兩部分命名。若要這麼做，請執行下列查詢：

	`CREATE SYNONYM [Sales.SalesOrderDetail] FOR Sales.SalesOrderDetail`

1.  執行下列查詢並檢閱結果。

	`select top 200 * from [Sales.SalesOrderDetail] `

使用 Sqoop 將資料從 HDInsigh 匯入到叢集
---------------------------------------

1.  從 [帳戶] 頁面，向下捲動到 [Your cluster] 區段中的 [開啟連接埠] 圖示，按一下圖示來開啟叢集前端節點的 ODBC 伺服器連接埠。

2.  回到 [帳戶] 頁面，向下捲動到 [Your cluster] 區段，這次按一下 **[遠端桌面]** 圖示來開啟叢集的前端節點。TBD:更新為與 PS 一起使用。

3.  出現提示時，選取 **[開啟]** 來開啟 .rdp 檔案。

4.  在 [遠端桌面連線] 視窗中選取 [連線]。

5.  在 **[Windows 安全性]** 視窗中輸入 Hadoop 叢集的認證 (不是您在 Azure 帳戶的 Hadoop)，然後選取 **[確定]**。

6.  開啟 Internet Explorer 並移至 WhatIsMyIP 網站，取得叢集前端節點的對外 IP 位址。返回 SQL 資料庫管理頁面，加入防火牆規則以允許 Hadoop 叢集存取 SQL 資料庫。防火牆會根據每一個要求的來源 IP 位址來授與存取權。

7.  按兩下桌面左上方的 Hadoop 命令 Shell 圖示來開啟 Shell。瀏覽至 "c:\\Apps\\dist\\sqoop\\bin" 目錄並執行下列命令：

    `sqoop import --connect "jdbc:sqlserver://[serverName].database.windows.net;username=[userName]@[serverName];password=[password];database=AdventureWorks2012" --table Sales.SalesOrderDetail --target-dir /data/lineitemData -m 1`

    例如，若使用下列值：
	* 伺服器名稱：wq6xlbyoq0 
	* 使用者名稱：HadoopOnAzureSqoopAdmin 
	* 密碼：Pa$$w0rd

		sqoop 命令為：

	    `sqoop import --connect "jdbc:sqlserver://wq6xlbyoq0.database.windows.net;username=HadoopOnAzureSqoopAdmin@wq6xlbyoq0;password=Pa$$w0rd;;database=AdventureWorks2012" --table Sales.SalesOrderDetail --target-dir /data/lineitemData -m 1`

8.  回到 Azure 入口網站上的 Hadoop 的 **[帳戶]** 頁面，這次開啟互動式主控台。從 JavaScript 主控台執行 \#lsr 命令，列出 HDFS 叢集上的檔案和目錄。GA 之前的 TBD 更新。

9.  執行 \#tail 命令，從 part-m-0000 檔案中檢視選取的結果。
	`tail /user/RAdmin/data/SalesOrderDetail/part-m-00000`

摘要
----

本教學課程中，您看到如何使用 Apache Sqoop 在 Windows Azure HDInsight 管理的 Hadoop 叢集與關聯性資料庫之間傳送資料。

後續步驟
--------

關於執行其他範例的教學課程，以及如何以 Windows Azure PowerShell 在 Windows Azure HDInsight 上使用 Pig、Hive 和 MapReduce 工作的指示，請參閱下列主題：

-   [範例：Pi 估算器](/zh-tw/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [範例：字數統計](/zh-tw/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [範例：C\# 串流](/zh-tw/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [範例：10GB GraySort](/zh-tw/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [教學課程：使用 Pig](/zh-tw/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [教學課程：使用 Hive](/zh-tw/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [教學課程：使用 MapReduce](/zh-tw/manage/services/hdinsight/using-mapreduce-with-hdinsight/)
-   [Windows Azure HDInsight SDK 文件](http://msdnstage.redmond.corp.microsoft.com/zh-tw/library/dn479185.aspx)

