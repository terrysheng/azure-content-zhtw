<properties urlDisplayName="Use time-based Hadoop Oozie Coordinator in HDInsight" pageTitle="在 HDInsight 上使用以時間為基礎的 Hadoop Oozie 協調器 | Azure" metaKeywords="" description="Use time-based Hadoop Oozie Coordinator in HDInsight, a big data solution. Learn how to define Oozie workflows and coordinators, and submit coordinator jobs." metaCanonical="" services="hdinsight" documentationCenter="" title="Use time-based Hadoop Oozie Coordinator in HDInsight" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />


# 在 HDInsight 上將以時間為基礎的 Oozie 協調器與 Hadoop 搭配使用

了解如何定義工作流程和協調器，以及如何觸發以時間為基礎的協調器工作。進行本文之前先完成 [在 HDInsight 上使用 Oozie][hdinsight-use-oozie]，將有所幫助。 



**預估完成時間：**  40 Minuten

##本文內容

0. [什麼是 Oozie](#whatisoozie)
1. [必要條件](#prerequisites)
2. [定義 Oozie 工作流程檔案](#defineworkflow)
2. [部署 Oozie 專案及進行教學課程前置工作](#deploy)
3. [執行工作流程](#run)
4. [後續步驟](#nextsteps)

##<a id="whatisoozie"></a>什麼是 Oozie

Apache Oozie 是可管理 Hadoop 工作的工作流程/協調系統。它可與 Hadoop 堆疊相整合，並支援 Apache MapReduce、Apache Pig、Apache Hive 和 Apache Sqoop 的 Hadoop 工作。它也可用來排程系統的特定工作，例如 Java 程式或 Shell 指令碼。

您將實作的工作流程包含兩個動作： 

![Workflow diagram][img-workflow-diagram]

1. Hive 動作會執行一個 HiveQL 指令碼，以計算每個記錄層級類型在 log4j 記錄檔中的出現次數。每一個 log4j 記錄各由一列欄位組成，其中包含 [LOG LEVEL] 欄位以顯示類型和嚴重性。例如：

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

	Hive 指令碼輸出如下：
	
		[DEBUG] 434
		[ERROR] 3
		[FATAL] 1
		[INFO]  96
		[TRACE] 816
		[WARN]  4

	如需 Hive 的詳細資訊，請參閱[在 HDInsight 上使用 Hive][hdinsight-use-hive]。
	
2.  Sqoop 動作會將 HiveQL 動作的輸出匯出至 Azure SQL Database 上的資料表。如需 Sqoop 的詳細資訊，請參閱[在 HDInsight 上使用 Sqoop][hdinsight-use-sqoop]。

> [WACOM.NOTE] 如需 HDInsight 叢集上支援的 Oozie 版本，請參閱 [HDInsight 所提供叢集版本的新功能][hdinsight-versions]。

> [WACOM.NOTE] 本教學課程將執行於 HDInsight 叢集 2.1 和 3.0 版。本文並未使用 HDInsight 模擬器進行測試。


##<a id="prerequisites"></a>必要條件

開始進行本教學課程之前，您必須具備下列條件：

已安裝並設定 Azure PowerShell 的- **工作站**。如需指示，請參閱[安裝並設定 Azure PowerShell][powershell-install-configure]。若要執行 PowerShell 指令碼，您必須以系統管理員的身分執行 Azure PowerShell，並將執行原則設為 *RemoteSigned*。請參閱[執行 Windows PowerShell 指令碼][powershell-script]。
- **HDInsight 叢集**。如需建立 HDInsight 叢集的相關資訊，請參閱[佈建 HDInsight 叢集][hdinsight-provision] 或 [開始使用 HDInsight][hdinsight-get-started]。進行教學課程時，您將需要下列資料：

	<table border = "1">
	<tr><th>叢集屬性</th><th>PowerShell 變數名稱</th><th>值</th><th>說明</th></tr>
	<tr><td>HDInsight 叢集名稱</td><td>$clusterName</td><td></td><td>您將用來執行此教學課程的 HDInsight 叢集。</td></tr>
	<tr><td>HDInsight 叢集使用者名稱</td><td>$clusterUsername</td><td></td><td>HDInsight 叢集使用者的使用者名稱。 </td></tr>
	<tr><td>HDInsight 叢集使用者密碼 </td><td>$clusterPassword</td><td></td><td>HDInsight 叢集使用者密碼。</td></tr>
	<tr><td>Azure 儲存體帳戶名稱</td><td>$storageAccountName</td><td></td><td>可供 HDInsight 叢集使用的 Azure 儲存體帳戶。在本教學課程中，請使用在叢集佈建程序中指定的預設儲存體帳戶。</td></tr>
	<tr><td>Azure Blob 容器名稱</td><td>$containerName</td><td></td><td>在此範例中，請使用預設 HDInsight 叢集檔案系統所使用的 Azure Blob 儲存體容器。根據預設，此容器的名稱會與 HDInsight 叢集名稱相同。</td></tr>
	</table>

- **Azure SQL Database**。您必須設定 SQL Database Server 的防火牆規則，以允許從您的工作站加以存取。如需建立 SQL 資料庫和設定防火牆的相關指示，請參閱[開始使用 Azure SQL Database][sqldatabase-get-started]。本文將提供建立本教學課程所需之 SQL Database 資料表的 PowerShell 指令碼。 

	<table border = "1">
	<tr><th>SQL Database 屬性</th><th>PowerShell 變數名稱</th><th>值</th><th>說明</th></tr>
	<tr><td>SQL Database 伺服器名稱</td><td>$sqlDatabaseServer</td><td></td><td>Sqoop 會將資料匯出到的 SQL Database 伺服器。 </td></tr>
	<tr><td>SQL Database 登入名稱</td><td>$sqlDatabaseLogin</td><td></td><td>SQL Database 登入名稱。</td></tr>
	<tr><td>SQL Database 登入密碼</td><td>$sqlDatabaseLoginPassword</td><td></td><td>SQL Database 登入密碼。</td></tr>
	<tr><td>SQL Database 名稱</td><td>$sqlDatabaseName</td><td></td><td>Sqoop 會將資料匯出到的 Azure SQL Database。 </td></tr>
	</table>

	> [WACOM.NOTE]依預設，Azure SQL Database 接受來自 Azure 服務 (例如 Azure HDinsight) 的連線。如果此防火牆設定停用，您必須從 Azure 管理入口網站中啟用它。如需建立 SQL Database 和設定防火牆規則的相關指示，請參閱[建立和設定 SQL 資料庫][sqldatabase-create-configue]。 


> [WACOM.NOTE] 將值填入資料表中。  這將有助於本教學課程的執行。


##<a id="defineworkflow"></a>定義 Oozie 工作流程和相關的 HiveQL 指令碼

Oozie 工作流程定義會以 hPDL 撰寫 (一種 XML 程序定義語言)。預設工作流程檔案名稱為 *workflow.xml*。  您將在本機儲存工作流程檔案，然後在本教學課程中使用 Azure PowerShell 將其部署至 HDInsight 叢集。

此工作流程中的 Hive 動作會呼叫 HiveQL 指令碼檔案。此指令碼檔案包含三個 HiveQL 陳述式：

1. **DROP TABLE 陳述式** 會刪除 log4j Hive 資料表 (如果存在)。
2. **CREATE TABLE 陳述式** 會建立 log4j Hive 外部資料表，指向 
3.  log4j 記錄檔的位置。欄位分隔符號為 ","。預設行分隔符號為 "\n"。  Hive 外部資料表可讓您在需要執行 Oozie 工作流程多次時，避免資料檔案從原始位置遭到移除。
3. **INSERT OVERWRITE 陳述式** 可從 log4j Hive 資料表中計算每個記錄層級類型的出現次數，並將輸出儲存至 Azure 儲存體 - Blob (WASB) 位置。 

Hive 路徑有已知問題。此問題會在您提交 Oozie 工作時發生。如需修正此問題的指示，請參閱 [TechNet Wiki][technetwiki-hive-error]。

**若要定義要由工作流程呼叫的 HiveQL 指令碼檔案：**

1. 建立含有下列內容的文字檔：

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

	指令碼中使用三種變數：

	- ${hiveTableName}
	- ${hiveDataFolder}
	- ${hiveOutputFolder}
			
	工作流程定義檔 (在本教學課程中為 workflow.xml) 會在執行階段將這些值傳至此 HiveQL 指令碼。
		
2. 使用 **ANSI(ASCII)** 編碼 (如果您的文字編輯器未提供此選項，請使用 [記事本])，將檔案儲存為 **C:\Tutorials\UseOozie\useooziewf.hql**。此指令碼檔案將在本教學課程中部署至 HDInsight 叢集。



**定義工作流程**

1. 建立含有下列內容的文字檔：

		<workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
		    <start to = "RunHiveScript"/> 
			
		    <action name="RunHiveScript">
		        <hive xmlns="uri:oozie:hive-action:0.2">
		            <job-tracker>${jobTracker}</job-tracker>
		            <name-node>${nameNode}</name-node>
		            <configuration>
		                <property>
		                    <name>mapred.job.queue.name</name>
		                    <value>${queueName}</value>
		                </property>
		            </configuration>
		            <script>${hiveScript}</script>
			    	<param>hiveTableName=${hiveTableName}</param>
		            <param>hiveDataFolder=${hiveDataFolder}</param>
		            <param>hiveOutputFolder=${hiveOutputFolder}</param>
		        </hive>
		        <ok to="RunSqoopExport"/>
		        <error to="fail"/>
		    </action>
		
		    <action name="RunSqoopExport">
		        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
		            <job-tracker>${jobTracker}</job-tracker>
		            <name-node>${nameNode}</name-node>
		            <configuration>
		                <property>
		                    <name>mapred.compress.map.output</name>
		                    <value>true</value>
		                </property>
		            </configuration>
			    <arg>export</arg>
			    <arg>--connect</arg> 
			    <arg>${sqlDatabaseConnectionString}</arg> 
			    <arg>--table</arg>
			    <arg>${sqlDatabaseTableName}</arg> 
			    <arg>--export-dir</arg> 
			    <arg>${hiveOutputFolder}</arg> 
			    <arg>-m</arg> 
			    <arg>1</arg>
			    <arg>--input-fields-terminated-by</arg>
			    <arg>"\001"</arg>
		        </sqoop>
		        <ok to="end"/>
		        <error to="fail"/>
		    </action>
		
		    <kill name="fail">
		        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
		    </kill>
		
		   <end name="end"/>
		</workflow-app>

	此工作流程中定義了兩個動作。起始動作為 *RunHiveScript*。如果此動作 *順利執行*，則下一個動作為 *RunSqoopExport*。

	RunHiveScript 有數個變數。您將在使用 Azure PowerShell 從工作站提交 Oozie 工作時傳入這些值。

	<table border = "1">
	<tr><th>工作流程變數</th><th>說明</th></tr>
	<tr><td>${jobTracker}</td><td>指定 hadoop 工作追蹤器的 URL。在 HDInsight 叢集 2.0 和 3.0 版上請使用 <strong>jobtrackerhost:9010</strong>。</td></tr>
	<tr><td>${nameNode}</td><td>指定 hadoop 節點名稱的 URL。請使用預設檔案系統 WASB 位址。例如 <i>wasb://&lt;containerName&gt;@&lt;storageAccountName&gt;.blob.core.windows.net</i>。</td></tr>
	<tr><td>${queueName}</td><td>指定工作所將提交到的佇列名稱。請使用 <strong>預設值</strong>。</td></tr>
	</table>

	<table border = "1">
	<tr><th>Hive 動作變數</th><th>說明</th></tr>
	<tr><td>${hiveDataFolder}</td><td>Hive Create Table 命令的來源目錄。</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>INSERT OVERWRITE 陳述式的輸出資料夾。</td></tr>
	<tr><td>${hiveTableName}</td><td>參考 log4j 資料檔案之 Hive 資料表的名稱。</td></tr>
	</table>

	<table border = "1">
	<tr><th>Sqoop 動作變數</th><th>說明</th></tr>
	<tr><td>${sqlDatabaseConnectionString}</td><td>SQL DDatabase 連接字串。</td></tr>
	<tr><td>${sqlDatabaseTableName}</td><td>資料所將匯出到的 SQL Database 資料表。</td></tr>
	<tr><td>${hiveOutputFolder}</td><td>INSERT OVERWRITE 陳述式的輸出資料夾。這是 Sqoop Export export-dir 的相同資料夾。</td></tr>
	</table>

	如需 Oozie 工作流程和使用工作流程動作的詳細資訊，請參閱 [Apache Oozie 4.0 文件][apache-oozie-400] (適用於 HDInsight 叢集 3.0 版) 或 [Apache Oozie 3.3.2 文件][apache-oozie-332] (適用於 HDInsight 叢集 2.1 版)。

2. 使用 ANSI (ASCII) 編碼 (如果您的文字編輯器未提供此選項，請使用 [記事本])，將檔案儲存為 **C:\Tutorials\UseOozie\workflow.xml**。

**定義協調器**

1. 建立含有下列內容的文字檔：

		<coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
		   <action>
		      <workflow>
		         <app-path>${wfPath}</app-path>
		      </workflow>
		   </action>
		</coordinator-app>

	定義檔中使用五種變數：

	| 變數          | 說明|
	| ------------------|------------ |
	| ${coordFrequency} | 工作暫停時間。頻率一律以分鐘表示。 |
	| ${coordStart}     | 工作開始時間。 |
	| ${coordEnd}       | 工作結束時間。 |
    | ${coordTimezone}  | Oozie 會以未採用 DST 的固定時區 (通常為 UTC ) 處理協調器工作，此時區稱為「Oozie 處理時區」。 |
	| ${wfPath}         | workflow.xml 的路徑。  如果工作流程檔案名稱不是預設檔案名稱 (workflow.xml)，您必須加以指定。 |
	
2. 使用 ANSI (ASCII) 編碼 (如果您的文字編輯器未提供此選項，請使用 [記事本])，將檔案儲存為 **C:\Tutorials\UseOozie\coordinator.xml**。
	
##<a id="deploy"></a>部署 Oozie 專案及進行教學課程前置工作

您將執行 Azure PowerShell 指令碼，以執行下列作業：

- 將 HiveQL 指令碼 (useoozie.hql) 複製到 Azure Blob 儲存體 wasb:///tutorials/useoozie/useoozie.hql。
- 將 workflow.xml 複製到 wasb:///tutorials/useoozie/workflow.xml。
- 將 coordinator.xml 複製到 wasb:///tutorials/useoozie/coordinator.xml。
- 將資料檔案 (/example/data/sample.log) 複製到 wasb:///tutorials/useoozie/data/sample.log。 
- 建立用以儲存 Sqoop 匯出資料的 SQL Database 資料表。  資料表名稱為 *log4jLogCount*。

**了解 HDInsight 儲存體**

HDInsight 使用 Azure Blob 儲存體作為資料儲存區。  我們稱之為 *WASB* 或 *Windows Azure 儲存體 - Blob*。WASB 是 Microsoft 在 Azure Blob 儲存體上的 HDFS 實作。如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。 

當您佈建 HDInsight 叢集時，會將一個 Azure 儲存體帳戶及其下的特定 Blob 儲存體容器指定為預設檔案系統，如同在 HDFS 中一般。除了此儲存體帳戶，您也可以在佈建過程中從相同或不同的 Azure 訂用帳戶中新增其他儲存體帳戶。如需有關新增其他儲存體帳戶的詳細資訊，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。為簡化本教學課程中使用的 PowerShell 指令碼，所有檔案都會儲存在位於 */tutorials/useoozie* 的預設檔案系統容器中。根據預設，此容器的名稱會與 HDInsight 叢集名稱相同。 
WASB 語法如下：

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight 叢集 3.0 版僅支援 *wasb://* 語法。HDInsight 2.1 和 1.6 支援舊的 *asv://* 語法，但在 HDInsight 3.0 叢集中已不受支援，未來的版本也不加以支援。

> [WACOM.NOTE] WASB 路徑為虛擬路徑。  如需詳細資訊，請參閱[搭配 HDInsight 使用 Azure Blob 儲存體][hdinsight-storage]。 

儲存在預設檔案系統容器中的檔案，可使用下列任一 URI (範例中使用 workflow.xml) 從 HDInsight 存取：

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
	wasb:///tutorials/useoozie/workflow.xml
	/tutorials/useoozie/workflow.xml

如果您要直接從儲存體帳戶存取檔案，檔案的Blob 名稱為：

	tutorials/useoozie/workflow.xml

**了解 Hive 內部資料表和外部資料表**

關於 Hive 內部資料表和外部資料表，有若干事項您必須了解：

- CREATE TABLE 命令會建立內部資料表，也稱為受管理的資料表。資料檔案必須位於預設容器中。
- CREATE TABLE 命令會將資料檔案移至預設容器上的 /hive/warehouse/<TableName> 資料夾。
- CREATE EXTERNAL TABLE 命令會建立外部資料表。資料檔案可位於預設容器外。
- CREATE EXTERNAL TABLE 命令不會移動資料檔案。
- CREATE EXTERNAL TABLE 命令不允許在 LOCATION 子句中指定的資料夾下放置任何子資料夾。因此，此教學課程複製了 sample.log 檔案。

如需詳細資訊，請參閱 [HDInsight：Hive 內部和外部資料表簡介][cindygross-hive-tables]。

**教學課程前置工作**

1. 開啟 Windows PowerShell ISE (在 Windows 8 的 [開始] 畫面上輸入 **PowerShell_ISE**，然後按一下 [**Windows PowerShell ISE**]。請參閱[在 Windows 8 和 Windows 上啟動 Windows PowerShell][powershell-start])。
2. 在底部窗格中執行下列命令，以連接到您的 Azure 訂用帳戶：

		Add-AzureAccount

	系統會提示您輸入 Azure 帳戶認證。這種新增訂用帳戶連線的方法會逾時，且在 12 小時後，您將必須重新執行 Cmdlet。 

	> [WACOM.NOTE] 如果您有多個 Azure 訂用帳戶，且預設訂用帳戶並非您要使用的訂用帳戶，請使用 <strong>Select-AzureSubscription</strong> Cmdlet 選取目前的訂用帳戶。

3. 將下列指令碼複製到指令碼窗格中，然後設定前六個變數。
			
		# WASB variables
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<BlobStorageContainerName>"
		
		# SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"  
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  
		$sqlDatabaseTableName = "log4jLogsCount"
		
		# Oozie files for the tutorial	
		$hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
		$workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
		$coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"
		
		# WASB folder for storing the Oozie tutorial files.
		$destFolder = "tutorials/useoozie"  # Do NOT use the long path here


	如需變數的詳細說明，請參閱本教學課程中的[必要條件](#prerequisites)一節。 

3. 將下列程式碼附加到指令碼窗格中的指令碼：
		
		# Create a storage context object
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		function uploadOozieFiles()
		{		
		    Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..."-ForegroundColor Green
			Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
			Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
			Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
		}
				
		function prepareHiveDataFile()
		{
			Write-Host "Make a copy of the sample.log file ..." -ForegroundColor Green
			Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
		}
				
		function prepareSQLDatabase()
		{
			# SQL query string for creating log4jLogsCount table
			$cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
				    [Level] [nvarchar](10) NOT NULL,
				    [Total] float,
				CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
				(
				[Level] ASC
				)
				)"
				
			#Create the log4jLogsCount table
		    Write-Host "Create Log4jLogsCount table ..."-ForegroundColor Green
			$conn = New-Object System.Data.SqlClient.SqlConnection
			$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
			$conn.open()
			$cmd = New-Object System.Data.SqlClient.SqlCommand
			$cmd.connection = $conn
			$cmd.commandtext = $cmdCreateLog4jCountTable
			$cmd.executenonquery()
				
			$conn.close()
		}
				
		# upload workflow.xml, coordinator.xml, and ooziewf.hql
		uploadOozieFiles;
				
		# make a copy of example/data/sample.log to example/data/log4j/sample.log
		prepareHiveDataFile;
		
		# create log4jlogsCount table on SQL database
		prepareSQLDatabase;

4. 按一下 [**執行指令碼**] 或按 [**F5**] 以執行指令碼。輸出應該類似：

	![Tutorial preparation output][img-preparation-output]

##<a id="run"></a>執行 Oozie 專案

Azure PowerShell 目前並未提供任何用以定義 Oozie 工作的 Cmdlet。您可以使用 
Invoke-RestMethod PowerShell Cmdlet 來叫用 Oozie Web 服務。Oozie Web 服務 API 是 HTTP REST JSON API。如需 Oozie Web 服務 API 的詳細資訊，請參閱 [Apache Oozie 4.0 文件][apache-oozie-400] (適用於 HDInsight 叢集 3.0 版) 或 [Apache Oozie 3.3.2 文件][apache-oozie-332] (適用於 HDInsight 叢集 2.1 版)。

**提交 Oozie 工作**

1. 開啟 Windows PowerShell ISE (在 Windows 8 的 [開始] 畫面上輸入 **PowerShell_ISE**，然後按一下 [**Windows PowerShell ISE**]。請參閱[在 Windows 8 和 Windows 上啟動 Windows PowerShell][powershell-start])。

3. 將下列指令碼複製到指令碼窗格中，然後設定前十四個變數 (請略過第 6 個變數 $storageUri)。 

		#HDInsight cluster variables
		$clusterName = "<HDInsightClusterName>"
		$clusterUsername = "<HDInsightClusterUsername>"
		$clusterPassword = "<HDInsightClusterUserPassword>"
		
		#Azure Blob storage (WASB) variables
		$storageAccountName = "<StorageAccountName>"
		$storageContainerName = "<BlobContainerName>"
		$storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"
		
		#Azure SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  
		
		#Oozie WF/coordinator variables
		$coordStart = "2014-03-21T13:45Z"
		$coordEnd = "2014-03-21T13:45Z"
		$coordFrequency = "1440"	# in minutes, 24h x 60m = 1440m
		$coordTimezone = "UTC"	#UTC/GMT

		$oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
		$waitTimeBetweenOozieJobStatusCheck=10

		#Hive action variables
		$hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
		$hiveTableName = "log4jlogs"
		$hiveDataFolder = "$storageUri/tutorials/useoozie/data"
		$hiveOutputFolder = "$storageUri/tutorials/useoozie/output"
		
		#Sqoop action variables
		$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
		$sqlDatabaseTableName = "log4jLogsCount"

		$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

	如需變數的詳細說明，請參閱本教學課程中的[必要條件](#prerequisites)一節。

	$coordstart 和 $coordend 是工作流程的開始和結束時間。 To find out the UTC/GMT time, search "utc time" on bing.com.$coordFrequency 是您要執行工作流程的頻率 (以分鐘為單位)。 

3. 將下列程式碼附加至指令碼：這部分會定義 Oozie 裝載：
		
		#OoziePayload used for Oozie web service submission
		$OoziePayload =  @"
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		
		   <property>
		       <name>nameNode</name>
		       <value>$storageUrI</value>
		   </property>
		
		   <property>
		       <name>jobTracker</name>
		       <value>jobtrackerhost:9010</value>
		   </property>
		
		   <property>
		       <name>queueName</name>
		       <value>default</value>
		   </property>
		
		   <property>
		       <name>oozie.use.system.libpath</name>
		       <value>true</value>
		   </property>

		   <property>
		       <name>oozie.coord.application.path</name>
		       <value>$oozieWFPath</value>
		   </property>

		   <property>
		       <name>wfPath</name>
		       <value>$oozieWFPath</value>
		   </property>

		   <property>
		       <name>coordStart</name>
		       <value>$coordStart</value>
		   </property>

		   <property>
		       <name>coordEnd</name>
		       <value>$coordEnd</value>
		   </property>

		   <property>
		       <name>coordFrequency</name>
		       <value>$coordFrequency</value>
		   </property>

		   <property>
		       <name>coordTimezone</name>
		       <value>$coordTimezone</value>
		   </property>

		   <property>
		       <name>hiveScript</name>
		       <value>$hiveScript</value>
		   </property>
		
		   <property>
		       <name>hiveTableName</name>
		       <value>$hiveTableName</value>
		   </property>
		
		   <property>
		       <name>hiveDataFolder</name>
		       <value>$hiveDataFolder</value>
		   </property>
		
		   <property>
		       <name>hiveOutputFolder</name>
		       <value>$hiveOutputFolder</value>
		   </property>
		
		   <property>
		       <name>sqlDatabaseConnectionString</name>
		       <value>&quot;$sqlDatabaseConnectionString&quot;</value>
		   </property>
		
		   <property>
		       <name>sqlDatabaseTableName</name>
		       <value>$SQLDatabaseTableName</value>
		   </property>
		
		   <property>
		       <name>user.name</name>
		       <value>admin</value>
		   </property>
		
		</configuration>
		"@

	>[WACOM.NOTE] 不同於工作流程提交裝載檔案的主要差異在於變數 **oozie.coord.application.path**。提交工作流程工作時，您必須改用 **oozie.wf.application.path**。

4. 將下列程式碼附加至指令碼：這部分會檢查 Oozie Web 服務狀態：	
			
		function checkOozieServerStatus()
		{
		     Write-Host "Checking Oozie server status..."-ForegroundColor Green
		    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
		    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 
		    
		    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
		    $oozieServerSatus = $jsonResponse[0].("systemMode")
		    Write-Host "Oozie server status is $oozieServerSatus..."
		
		    if($oozieServerSatus -notmatch "NORMAL")
		    {
		        Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs.Check the server status and re-run the job."
		        exit 1
		    }
		}
	
5. 將下列程式碼附加至指令碼：這部分會建立 Oozie 工作：	

		function createOozieJob()
		{
		    # create Oozie job
		    Write-Host "Sending the following Payload to the cluster:"-ForegroundColor Green
		    Write-Host "`n--------`n$OoziePayload`n--------"
		    $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
		    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose
		
		    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
		    $oozieJobId = $jsonResponse[0].("id")
		    Write-Host "Oozie job id is $oozieJobId..."
		
		    return $oozieJobId
		}

	> [WACOM.NOTE] 提交工作流程工作時，您必須在工作建立後發出另一個 Web 服務呼叫，以啟動工作。在此情況下，協調器工作會依時間觸發。  工作將會自動啟動。

6. 將下列程式碼附加至指令碼：這部分會檢查 Oozie 工作狀態：		

		function checkOozieJobStatus($oozieJobId)
		{
		    # get job status
		    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..."-ForegroundColor Green
		    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
		
		    Write-Host "Getting job status and waiting for the job to complete..."-ForegroundColor Green
		    $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
		    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
		    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
		    $JobStatus = $jsonResponse[0].("status")
		
		    while($JobStatus -notmatch "SUCCEEDED|KILLED")
		    {
		        Write-Host "$(Get-Date -format 'G'):$oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
		        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
		        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
		        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
		        $JobStatus = $jsonResponse[0].("status")
		    }
		
		    Write-Host "$(Get-Date -format 'G'):$oozieJobId is in $JobStatus state!"
		    if($JobStatus -notmatch "SUCCEEDED")
		    {
		        Write-Host "Check logs at http://headnode0:9014/cluster for detais."
		        exit -1
		    }
		}

7. (選用) 將下列程式碼附加至指令碼。 

		function listOozieJobs()
		{
		    Write-Host "Listing Oozie jobs..."-ForegroundColor Green
		    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
		    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds 
		    
		    write-host "Job ID                                   App Name        Status      Started                         Ended"
		    write-host "----------------------------------------------------------------------------------------------------------------------------------"
		    foreach($job in $response.workflows)
		    {
		        Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
		    }
		}

		function ShowOozieJobLog($oozieJobId)
		{
		    Write-Host "Showing Oozie job info..."-ForegroundColor Green
		    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
		    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds 
		    write-host $response
		}

		function killOozieJob($oozieJobId)
		{
		    Write-Host "Killing the Oozie job $oozieJobId..."-ForegroundColor Green
		    $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
		    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
		}
  
7. 將下列程式碼附加至指令碼：

		checkOozieServerStatus
		# listOozieJobs
		$oozieJobId = createOozieJob($oozieJobId)
		checkOozieJobStatus($oozieJobId)
		# ShowOozieJobLog($oozieJobId)
		# killOozieJob($oozieJobId)

	Remove the # signs if you want to run the additional functions.

7. 如果您的 HDinsight 叢集是 2.1 版，請將 "https://$clusterName.azurehdinsight.net:443/oozie/v2/" 取代為 "https://$clusterName.azurehdinsight.net:443/oozie/v1/"。HDInsight 叢集 2.1 版不支援 Web 服務的第 2 版。

7. 按一下 [**執行指令碼**] 或按 [**F5**] 以執行指令碼。輸出將類似於：

	![Tutorial run workflow output][img-runworkflow-output]

8. 連接到您的 SQL Database，以檢視匯出的資料。

**檢查工作錯誤記錄**

若要進行工作流程的疑難排解，您可以從叢集前端節點，找出位於 C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log 的 Oozie 記錄檔。如需 RDP 的相關資訊，請參閱[使用管理入口網站管理 HDInsight 叢集][hdinsight-admin-portal]。

**重新執行教學課程**

若要重新執行工作流程，您必須執行下列作業：

- 刪除 Hive 指令碼輸出檔案
- 刪除 log4jLogsCount 資料表中的資料

以下提供可供使用的範例 PowerShell 指令碼：

	$storageAccountName = "<AzureStorageAccountName>"
	$containerName = "<ContainerName>"
	
	# SQL database variables
	$sqlDatabaseServer = "<SQLDatabaseServerName>"
	$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
	$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
	$sqlDatabaseName = "<SQLDatabaseName>"
	$sqlDatabaseTableName = "log4jLogsCount"
	
	Write-host "Delete the Hive script output file ..."-ForegroundColor Green
	$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
	Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName
	
	Write-host "Delete all the records from the log4jLogsCount table ..."-ForegroundColor Green
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
	$conn.open()
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.connection = $conn
	$cmd.commandtext = "delete from $sqlDatabaseTableName"
	$cmd.executenonquery()
	
	$conn.close()


##<a id="nextsteps"></a>後續步驟
在本教學課程中，您已了解如何定義 Oozie 工作流程和 Oozie 協調器，以及如何使用 Azure PowerShell 來執行 Oozie 協調器工作。若要深入了解，請參閱下列文章：

- [開始使用 HDInsight][hdinsight-get-started]
- [開始使用 HDInsight Emulator][hdinsight-get-started-emulator]
- [在 HDInsight 上使用 Azure Blob 儲存體][hdinsight-storage]
- [使用 PowerShell 管理 HDInsight][hdinsight-admin-powershell]
- [將資料上傳至 HDInsight][hdinsight-upload-data]
- [搭配 HDInsight 使用 Sqoop][hdinsight-use-sqoop]
- [搭配 HDInsight 使用 Hive][hdinsight-use-hive]
- [搭配 HDInsight 使用 Pig ][hdinsight-use-pig]
- [開發 HDInsight 的 C# Hadoop 串流工作][hdinsight-develop-streaming-jobs]
- [開發 HDInsight 的 Java MapReduce 程式][hdinsight-develop-java-mapreduce]



[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563


[hdinsight-versions]:  ../hdinsight-component-versioning/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/


[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-develop-streaming-jobs]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-java-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hdinsight-use-oozie]: ../hdinsight-use-oozie/

[sqldatabase-create-configue]: ../sql-database-create-configure/
[sqldatabase-get-started]: ../sql-database-get-started/

[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/zh-tw/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../install-and-configure-powershell/
[powershell-start]: http://technet.microsoft.com/zh-tw/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/zh-tw/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png  

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
