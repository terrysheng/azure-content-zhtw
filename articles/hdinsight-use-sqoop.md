<properties urlDisplayName="Use Hadoo Sqoop in HDInsight" pageTitle="在 HDInsight 上使用 Hadoop Sqoop | Azure" metaKeywords="" description="了解如何從工作站使用 Azure PowerShell，在 HDInsight 叢集與 Azure SQL Database 之間執行 Sqoop 匯入和匯出。" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Use Hadoop Sqoop in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="jgao" />

# 在 HDInsight 上將 Sqoop 與 Hadoop 搭配使用
 
了解如何從工作站使用 Azure PowerShell 和 HDInsight .NET SDK，在 HDInsight 叢集與 Azure SQL Database 或 SQL Server Database 之間執行 Sqoop 匯入和匯出。

##本文內容

- [什麼是 Sqoop？](#whatissqoop)
- [必要條件](#prerequisites)
- [了解教學課程案例](#scenario)
- [教學課程前置工作](#prepare)
- [使用 PowerShell 執行 Sqoop 匯出](#export)
- [使用 HDInsight SDK 執行 Sqoop 匯出](#export-sdk)
- [使用 PowerShell 執行 Sqoop 匯入](#import)
- [後續步驟](#nextsteps)


## <a id="whatissqoop"></a> 什麼是 Sqoop？

在處理非結構化資料和半結構化資料時，例如記錄和檔案，雖然很自然會選擇 Hadoop，但有時也需要處理關聯式資料庫中儲存的結構化資料。

[Sqoop][sqoop-user-guide-1.4.4] 是一種可在 Hadoop 叢集和關聯式資料庫之間傳送資料的工具。此工具可讓您從 SQL、MySQL 或 Oracle 等關聯式資料庫管理系統 (RDBMS) 中，將資料匯入至 Hadoop 分散式檔案系統 (HDFS)，在 Hadoop 中使用 MapReduce 或 Hive 來轉換資料，然後將資料匯回到 RDBMS。本教學課程中，您將使用 SQL 資料庫做為關聯式資料庫。

如需 HDInsight 叢集上支援的 Sqoop 版本，請參閱 [HDInsight 所提供叢集版本的新功能][hdinsight-versions]。




##<a id="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **工作站** - 已安裝並設定 Azure PowerShell 的電腦。如需指示，請參閱[安裝並設定 Azure PowerShell][powershell-install]。若要執行 PowerShell 指令碼，您必須以系統管理員的身分執行 Azure PowerShell，並將執行原則設為 *RemoteSigned*。請參閱[執行 Windows PowerShell 指令碼][powershell-script]。

- **Azure HDInsight 叢集**。如需叢集佈建的指示，請參閱[開始使用 HDInsight][hdinsight-get-started] 或[佈建 HDInsight 叢集][hdinsight-provision]。進行教學課程時，您將需要下列資料：

	<table border="1">
	<tr><th>叢集屬性</th><th>PowerShell 變數名稱</th><th>值</th><th>說明</th></tr>
	<tr><td>HDInsight 叢集名稱</td><td>$clusterName</td><td></td><td>這是您的 HDInsight 叢集名稱。</td></tr>
	<tr><td>Azure 儲存體帳戶名稱</td><td>$storageAccountName</td><td></td><td>可供 HDInsight 叢集使用的 Azure 儲存體帳戶。在本教學課程中，請使用在叢集佈建程序中指定的預設儲存體帳戶。</td></tr>
	<tr><td>Azure Blob 容器名稱</td><td>$containerName</td><td></td><td>在此範例中，請使用預設 HDInsight 叢集檔案系統所使用的 Azure Blob 儲存體容器。根據預設，此容器的名稱會與 HDInsight 叢集名稱相同。</td></tr>
	</table>

- **Azure SQL Database**.您必須設定 SQL Database Server 的防火牆規則，以允許從您的工作站加以存取。如需建立 SQL 資料庫和設定防火牆的相關指示，請參閱[開始使用 Azure SQL Database][sqldatabase-get-started]。本文將提供建立本教學課程所需之 SQL Database 資料表的 PowerShell 指令碼。 

	<table border="1">
	<tr><th>SQL Database 屬性</th><th>PowerShell 變數名稱</th><th>值</th><th>說明</th></tr>
	<tr><td>SQL Database 伺服器名稱</td><td>$sqlDatabaseServer</td><td></td><td>Sqoop 會將資料匯出或匯入的 SQL Database 伺服器。 </td></tr>
	<tr><td>SQL Database 登入名稱</td><td>$sqlDatabaseLogin</td><td></td><td>SQL Database 登入名稱。</td></tr>
	<tr><td>SQL Database 登入密碼</td><td>$sqlDatabasePassword</td><td></td><td>SQL Database 登入密碼。</td></tr>
	<tr><td>SQL Database 名稱</td><td>$sqlDatabaseName</td><td></td><td>Sqoop 會將資料匯出或匯入的 Azure SQL Database。 </td></tr>
	</table>

	> [WACOM.NOTE] 依預設，Azure SQL 資料庫接受來自 Azure 服務 (例如 Azure HDInsight) 的連線。如果此防火牆設定停用，您必須從 Azure 管理入口網站中啟用它。如需建立 SQL 資料庫和設定防火牆規則的相關指示，請參閱[建立和設定 SQL 資料庫][sqldatabase-create-configue]。 

* **SQL Server**。如果您的 HDInsight 叢集與 SQL Server 位於相同的 Azure 虛擬網路上，您可以使用本文中的步驟將資料匯入和匯出至 SQL Server Database。如需詳細資訊，請參閱下列文章。

	> [WACOM.NOTE] > Azure HDInsight 僅支援以位置為基礎的虛擬網路，目前無法使用以同質群組為基礎的虛擬網路。

	* 若要**建立及設定虛擬網路**，請參閱[虛擬網路組態工作](http://msdn.microsoft.com/zh-tw/library/azure/jj156206.aspx)。

		* 在您的資料中心裡**使用 SQL Server 時，**您必須將虛擬網路設定為 [*站對站*] 或 [*點對站*]。

			> [WACOM.NOTE] 使用**點對站**虛擬網路時，SQL Server 必須執行 VPN 用戶端組態應用程式；此應用程式可從 Azure 虛擬網路組態的 [**儀表板**] 存取。

		* 在 **Azure 虛擬機器**中使用 SQL Server 時，可以使用任何虛擬網路組態，只要主控 SQL Server 的虛擬機器屬於與 HDInsight 相同的虛擬網路即可。

	* 若要**在虛擬網路上佈建 HDInsight 叢集**，請參閱[使用自訂選項在 HDInsight 上佈建 Hadoop 叢集](/zh-tw/documentation/articles/hdinsight-provision-clusters/)

	> [WACOM.NOTE] SQL Server 也必須允許 SQL 驗證。您必須使用 SQL 登入來執行本文中的步驟。

	<table border="1">
	<tr><th>SQL Database 屬性</th><th>PowerShell 變數名稱</th><th>值</th><th>說明</th></tr>
	<tr><td>SQL Server 名稱</td><td>$sqlDatabaseServer</td><td></td><td>Sqoop 會將資料匯出或匯入的 SQL Server。 </td></tr>
	<tr><td>SQL Server 登入名稱</td><td>$sqlDatabaseLogin</td><td></td><td>SQL 登入名稱。</td></tr>
	<tr><td>SQL 登入密碼</td><td>$sqlDatabasePassword</td><td></td><td>SQL 登入密碼。</td></tr>
	<tr><td>SQL Server Database 名稱</td><td>$sqlDatabaseName</td><td></td><td>Sqoop 會將資料匯出或匯入的資料庫。 </td></tr>
	</table>


> [WACOM.NOTE] 將值填入上方的資料表中。這將有助於本教學課程的執行。

## <a id="scenario"></a>了解案例
HDInsight 叢集附有某些範例資料。您將用到以下兩項：

- 位於 */example/data/sample.log* 的 log4j 記錄檔。下列記錄擷取自此檔案：

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

- 參考位於 */hive/warehouse/hivesampletable* 之資料檔案的 Hive 資料表 *hivesampletable*。此資料表包含某些行動裝置資料。此 Hive 資料表的結構描述為：

	<table border="1">
	<tr><th>欄位</th><th>資料類型</th></tr>
	<tr><td>clientid</td><td>字串</td></tr>
	<tr><td>querytime</td><td>字串</td></tr>
	<tr><td>market</td><td>字串</td></tr>
	<tr><td>deviceplatform</td><td>字串</td></tr>
	<tr><td>devicemake</td><td>字串</td></tr>
	<tr><td>devicemodel</td><td>字串</td></tr>
	<tr><td>state</td><td>字串</td></tr>
	<tr><td>country</td><td>字串</td></tr>
	<tr><td>querydwelltime</td><td>double</td></tr>
	<tr><td>sessionid</td><td>bigint</td></tr>
	<tr><td>sessionpagevieworder</td><td>bigint</td></tr>
	</table>

您必須先將 sample.log 和 hivesampletable 匯出至 SQL Database 或 SQL Server，再使用下列路徑，將包含行動裝置資料的資料表匯回 HDInsight：

	/tutorials/usesqoop/importeddata

###了解 HDInsight 儲存體

HDInsight 會使用 Azure Blob 儲存體來儲存資料。我們稱之為 *WASB* 或 *Azure 儲存體 - Blob*。WASB 是 Microsoft 在 Azure Blob 儲存體上的 HDFS 實作。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。 

當您佈建 HDInsight 叢集時，會將一個 Azure 儲存體帳戶及其下的特定 Blob 儲存體容器指定為預設檔案系統，如同在 HDFS 中一般。除了此儲存體帳戶，您也可以在佈建過程中從相同或不同的 Azure 訂用帳戶中新增其他儲存體帳戶。如需有關新增其他儲存體帳戶的詳細資訊，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。為簡化本教學課程中使用的 PowerShell 指令碼，所有檔案都會儲存在位於 */tutorials/usesqoop* 的預設檔案系統容器中。根據預設，此容器的名稱會與 HDInsight 叢集名稱相同。 
WASB 語法如下：

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight 叢集 3.0 版僅支援 *wasb://* 語法。HDInsight 2.1 和 1.6 叢集支援舊的 *asv://* 語法，但在 HDInsight 3.0 叢集中已不支援，未來的版本中也不再支援。

> [WACOM.NOTE] WASB 路徑為虛擬路徑。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。 

儲存在預設檔案系統容器中的檔案，可使用下列任一 URI (範例中使用 sample.log) 從 HDInsight 存取：

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

如果您要直接從儲存體帳戶存取檔案，檔案的Blob 名稱為：

	example/data/sample.log


##<a id="prepare"></a>教學課程前置工作

您將在 SQL Database 或 SQL Server 中建立兩個資料表。本教學課程稍後的 Sqoop 匯出會使用這些資料表。您也須執行 sample.log 檔案的前置處理，Sqoop 才能處理此檔案。

###建立 SQL 資料表

**適用於 Azure SQL Database**

1. 開啟 Windows PowerShell ISE (在 Windows 8 的 [開始] 畫面上輸入 **PowerShell_ISE**，然後按一下 [**Windows PowerShell ISE**]。請參閱[在 Windows 8 和 Windows 上啟動 Windows PowerShell][powershell-start])。

2. 將下列指令碼複製到指令碼窗格中，然後設定前四個變數：
		
		#SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>" 
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseName = "<SQLDatabaseName>" 

		$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

	如需變數的詳細說明，請參閱本教學課程的＜[必要條件]＞(#prerequisites) 一節。 

3. 將下列指令碼附加至指令碼窗格中。這些指令碼是會定義兩個資料表及其叢集索引的 SQL 陳述式。SQL Database 需要叢集索引。

		# SQL query strings for creating tables and clustered indexes
		$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
		    [t1] [nvarchar](50), 
		    [t2] [nvarchar](50), 
		    [t3] [nvarchar](50), 
		    [t4] [nvarchar](50), 
		    [t5] [nvarchar](50), 
		    [t6] [nvarchar](50), 
		    [t7] [nvarchar](50))"
		
		$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"
		
		$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
		[clientid] [nvarchar](50), 
		[querytime] [nvarchar](50), 
		[market] [nvarchar](50), 
		[deviceplatform] [nvarchar](50), 
		[devicemake] [nvarchar](50), 
		[devicemodel] [nvarchar](50), 
		[state] [nvarchar](50), 
		[country] [nvarchar](50), 
		[querydwelltime] [float],
		[sessionid] [bigint],
		[sessionpagevieworder][bigint])"
		
		$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

4. 將下列指令碼附加至用以執行 SQL 命令的指令碼窗格中：

		Write-Host "Connect to the SQL Database ..." -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = $sqlDatabaseConnectionString
		$conn.Open()
		
		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.Connection = $conn
		$cmd.CommandText = $cmdCreateLog4jTable
		$ret = $cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateLog4jClusteredIndex
		$cmd.ExecuteNonQuery()
		
		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd.CommandText = $cmdCreateMobileTable
		$cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
		$cmd.ExecuteNonQuery()
		
		Write-Host "Close connection ..." -ForegroundColor Green		
		$conn.close()
		
		Write-Host "Done" -ForegroundColor Green
	
5. 按一下 [**執行指令碼**]，或按 **F5** 以執行指令碼。 
6. 使用 [Azure 管理入口網站][azure-management-portal]，檢查資料表和叢集索引。

**適用於 SQL Server**

1. 開啟 [**SQL Server Management Studio**]，然後連接到 SQL Server。

2. 建立名為 **sqoopdb** 的新資料庫。

3. 選取 **sqoopdb** 資料庫，然後從 SQL Server Management Studio 頂端的功能區中選取 [**新增查詢**]。

4. 將下列內容輸入查詢視窗中。

		CREATE TABLE [dbo].[log4jlogs](
		 [t1] [nvarchar](50), 
		 [t2] [nvarchar](50), 
		 [t3] [nvarchar](50), 
		 [t4] [nvarchar](50), 
		 [t5] [nvarchar](50), 
		 [t6] [nvarchar](50), 
		 [t7] [nvarchar](50))

		CREATE TABLE [dbo].[mobiledata](
		 [clientid] [nvarchar](50), 
		 [querytime] [nvarchar](50), 
		 [market] [nvarchar](50), 
		 [deviceplatform] [nvarchar](50), 
		 [devicemake] [nvarchar](50), 
		 [devicemodel] [nvarchar](50), 
		 [state] [nvarchar](50), 
		 [country] [nvarchar](50), 
		 [querydwelltime] [float],
		 [sessionid] [bigint],
		 [sessionpagevieworder][bigint])

5. 使用 **F5** 功能鍵，或選取 **!在功能區上執行**以執行查詢。查詢下方應出現下列訊息。

		Command(s) completed successfully.

6. 關閉 [SQL Server Management Studio]。

###產生資料

在本教學課程中，您會將 log4j 記錄檔 (使用分隔符號的檔案) 和 Hive 資料表匯出至 SQL Database。使用分隔符號的檔案為 */example/data/sample.log*。在本教學課程先前的內容中，您已看過 log4j 記錄檔的幾個範例。記錄檔中有某些空白行，且有幾行顯示如下：

	java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
		at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

這在使用此資料的其他範例中並沒有問題，但我們必須移除這些例外狀況，才能將其匯入 SQL Database 或 SQL Server 中。如果其中有空白字串，或某一行的元素數少於 SQL Database 資料表中定義的欄位數，Sqoop 匯出將會失敗。log4jlogs 資料表有 7 個字串類型欄位。

**進行 sample.log 檔案的前置處理**

1. 開啟 Windows PowerShell ISE。
2. 在底部窗格中執行下列命令，以連接到您的 Azure 訂用帳戶：

		Add-AzureAccount

	系統會提示您輸入 Azure 帳號認證。這種新增訂用帳戶連線的方法會逾時，且在 12 小時後，您將必須重新登入。 

	> [WACOM.NOTE] 如果您有多個 Azure 訂用帳戶，且預設訂用帳戶並非您要使用的訂用帳戶，請使用 <strong>Select-AzureSubscription</strong> Cmdlet 選取目前的訂用帳戶。

3. 將下列指令碼複製到指令碼窗格中，然後設定前兩個變數：
		
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"
		
		$sourceBlobName = "example/data/sample.log"
		$destBlobName = "tutorials/usesqoop/data/sample.log"

	如需變數的詳細說明，請參閱本教學課程的[必要條件](#prerequisites) 一節。 
 
4. 將下列指令碼附加至指令碼窗格中：

		# Define the connection string
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		
		# Create block blob objects referencing the source and destination blob.
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		
		# Define a MemoryStream and a StreamReader for reading from the source file
		$stream = New-Object System.IO.MemoryStream
		$stream = $sourceBlob.OpenRead()
		$sReader = New-Object System.IO.StreamReader($stream)
		
		# Define a MemoryStream and a StreamWriter for writing into the destination file
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		
		# process the source blob
		$exString = "java.lang.Exception:"
		while(-Not $sReader.EndOfStream){
		    $line = $sReader.ReadLine()
		    $split = $line.Split(" ")
		
		    # remove the "java.lang.Exception" from the first element of the array
		    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
		    if ($split[0] -eq $exString){
		        #create a new ArrayList to remove $split[0]
		        $newArray = [System.Collections.ArrayList] $split
		        $newArray.Remove($exString)
		
		        # update $split and $line
		        $split = $newArray
		        $line = $newArray -join(" ")
		    }
		
		    # remove the lines that has less than 7 elements
		    if ($split.count -ge 7){
		        write-host $line
		        $writeStream.WriteLine($line)
		    }
		}
		
		# Write to the destination blob
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream)

5. 按一下 [**執行指令碼**]，或按 [**F5**] 以執行指令碼。  
6. 若要檢查已修改的資料檔案，您可以使用 Azure 管理入口網站、Azure 儲存體瀏覽工具或 Azure PowerShell。[開始使用 HDInsight][hdinsight-get-started] 提供了使用 PowerShell 來下載檔案及顯示檔案內容的程式碼範例。


##<a id="export"></a>使用 PowerShell 執行 Sqoop 匯出

在本節中，您將使用 Azure PowerShell 執行Sqoop 匯出命令，將 Hive 資料表和資料檔案匯出至 Azure SQL Database 或 SQL Server。下一節將提供 HDInsight .NET 範例。

> [WACOM.NOTE] 除了連接字串資訊以外，本節中的步驟應可運用在 Azure SQL Database 或 SQL Server 上。這些步驟已使用下列組態進行測試：
> 
> * **Azure 虛擬網路點對站組態** - 在私人資料中心裡將 HDInsight 叢集連接到 SQL Server 的虛擬網路。如需詳細資訊，請參閱[使用管理入口網站設定點對站 VPN](http://msdn.microsoft.com/zh-tw/library/azure/dn133792.aspx) 。
> * **Azure HDInsight 3.1** - 請參閱[使用自訂選項在 HDInsight 上佈建 Hadoop 叢集]，(/zh-tw/documentation/articles/hdinsight-provision-clusters/) 取得在虛擬網路上建立叢集的資訊
> * **SQL Server 2014** - 設定成允許 SQL 驗證，以及執行 VPN 用戶端組態套件安全地連接到虛擬網路

**匯出 log4j 記錄檔**

1. 開啟 Windows PowerShell ISE。
2. 在底部窗格中執行下列命令，以連接到您的 Azure 訂用帳戶：

		Add-AzureAccount

	系統會提示您輸入 Azure 帳號認證。

3. 將下列指令碼複製到指令碼窗格中，然後設定前七個變數：

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$databaseName = "<SQLDatabaseName>"

		$tableName_log4j = "log4jlogs"
		
		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server.
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"
		
		$exportDir_log4j = "/tutorials/usesqoop/data"
	
	如需變數的詳細說明，請參閱本教學課程的＜[必要條件]＞(#prerequisites) 一節。 

	請留意到，$exportDir_log4j 並未指定 sample.log 的檔案名稱。Sqoop 會從該資料夾下的所有檔案中匯出資料。

4. 將下列指令碼附加至指令碼窗格中：

		# Submit a Sqoop job
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

	請注意，欄位分隔符號為 **\0x20**，即空格。此分隔符號定義於 sample.log 檔案前置處理 PowerShell 指令碼中。如需 **-m 1** 的相關資訊，請參閱 [Sqoop 使用者指南][sqoop-user-guide-1.4.4]。

5. 按一下 [**執行指令碼**]，或按 **F5** 以執行指令碼。  
6. 使用 [Azure 管理入口網站][azure-management-portal]，檢查匯出的資料。

**匯出 hivesampletable Hive 資料表**

1. 開啟 Windows PowerShell ISE。
2. 在底部窗格中執行下列命令，以連接到您的 Azure 訂用帳戶：

		Add-AzureAccount

	系統會提示您輸入 Azure 帳號認證。

3. 將下列指令碼複製到指令碼窗格中，然後設定前七個變數：

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_mobile = "mobiledata"
		
		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"
		
		$exportDir_mobile = "/hive/warehouse/hivesampletable"
	
	如需變數的詳細說明，請參閱本教學課程的[必要條件](#prerequisites) 一節。 

4. 將下列指令碼附加至指令碼窗格中：
		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
		
		
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. 按一下 [**執行指令碼**]，或按 [**F5**] 以執行指令碼。   
6. 使用 [Azure 管理入口網站][azure-management-portal]，檢查匯出的資料。



##<a id="export-sdk"></a>使用 HDInsight .NET SDK 執行 Sqoop 匯出

以下是使用 HDInsight .NET SDK 執行 Sqoop 匯出的 C# 範例。如需使用 HDInsight .NET SDK 的一般資訊，請參閱[以程式設計方式提交 Hadoop 工作][hdinsight-submit-jobs]。


	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.IO;
	using System.Threading;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.Hadoop.Client;
	
	namespace sqoopSDKSample
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Set the variables
	            string subscriptionID = "<AzureSubscriptionID>";
	            string clusterName = "<HDInsightClusterName>";
	            string certFriendlyName = "<AzureCertificateFriendlyName>";
	            string sqlDatabaseServerName = "<SQLDatabaseServerName>";
	            string sqlDatabaseLogin = "<SQLDatabaseLogin>";
	            string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
	            string sqlDatabaseDatabaseName = "hdisqoop";
	            string sqlDatabaseTableName = "log4jlogs";
	
	            cmdExport = @"export";
				// Connection string for using Azure SQL Database.
				// Comment if using SQL Server
	            cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
				// Connection string for using SQL Server.
				// Uncomment if using SQL Server
				//cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
	            cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
	            cmdExport = cmdExport + @" --export-dir /tutorials/usesqoop/data";
	            cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";
	
	            SqoopJobCreateParameters sqoopJobDefinition = new SqoopJobCreateParameters()
	            {
	                Command = cmdExport,
	                StatusFolder = "/tutorials/usesqoop/jobStatus"
	            };
	
	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
	            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
	
	            // Submit the Hive job
	            var jobClient = JobSubmissionClientFactory.Connect(creds);
	            JobCreationResults jobResults = jobClient.CreateSqoopJob(sqoopJobDefinition);
	
	            // Wait for the job to complete
	            WaitForJobCompletion(jobResults, jobClient);
	
	            // Print the Hive job output
	            System.IO.Stream stream = jobClient.GetJobErrorLogs(jobResults.JobId);
	
	            StreamReader reader = new StreamReader(stream);
	            Console.WriteLine(reader.ReadToEnd());
	
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadLine();
	        }
	
	        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
	        {
	            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
	            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
	            {
	                jobInProgress = client.GetJob(jobInProgress.JobId);
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	            }
	        }
	    }
	}

若要執行指令碼檔案，您可以將

	Command = cmdExport,

 取代為

	File = "/tutorials/usesqoop/sqoopexport.txt",

指令碼檔案必須位於 WASB 上。




##<a id="import"></a>使用 PowerShell 執行 Sqoop 匯入

在本節中，您會將 log4j 記錄檔 (您先前匯出至 SQL Database) 匯回 HDInsight。

1. 開啟 Windows PowerShell ISE。
2. 在底部窗格中執行下列命令，以連接到您的 Azure 訂用帳戶：

		Add-AzureAccount

	系統會提示您輸入 Azure 帳號認證。

3. 將下列指令碼複製到指令碼窗格中，然後設定前七個變數：

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_log4j = "log4jlogs"
		
		# Connection string for Azure SQL Database
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"
		
		$tableName_mobile = "mobiledata"
		$targetDir_mobile = "/tutorials/usesqoop/importeddata/"
	
	如需變數的詳細說明，請參閱本教學課程的＜[必要條件]＞(#prerequisites) 一節。 

4. 將下列指令碼附加至指令碼窗格中：
	
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
		
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. 按一下 [**執行指令碼**]，或按 **F5** 以執行指令碼。 
6. 若要檢查已修改的資料檔案，您可以使用 Azure 管理入口網站、Azure 儲存體瀏覽工具或 Azure PowerShell。[開始使用 HDInsight][hdinsight-get-started] 提供了使用 PowerShell 來下載檔案及顯示檔案內容的程式碼範例。

##<a id="nextsteps"></a>後續步驟

現在，您已了解如何使用 Sqoop。若要深入了解，請參閱：

- [在 HDInsight 上使用 Oozie][hdinsight-use-oozie]：在 Oozie 工作流程中使用 Sqoop 動作。
- [使用 HDInsight 分析航班延誤資料][hdinsight-analyze-flight-data]：使用 Hive 分析航班誤點資料，然後使用 Sqoop 將資料匯出至 SQL Database。
- [將資料上傳至 HDInsight][hdinsight-upload-data]：尋找其他將資料上傳至 HDInsight/Azure Blob 儲存體的方法。


 

[azure-management-portal]: https://manage.windowsazure.com/

[hdinsight-versions]:  ../hdinsight-component-versioning/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[sqldatabase-get-started]: ../sql-database-get-started/
[sqldatabase-create-configue]: ../sql-database-create-configure/

[powershell-start]: http://technet.microsoft.com/zh-tw/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/zh-tw/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!--HONumber=35.1-->
