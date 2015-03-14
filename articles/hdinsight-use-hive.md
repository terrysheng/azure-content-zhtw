<properties 
	pageTitle="在 HDInsight 上使用 Hadoop Hive | Azure" 
	description="了解如何搭配 HDInsight 使用 Hive。您將使用記錄檔作為 HDInsight 資料表中的輸入，並使用 HiveQL 來查詢資料及報告基本統計資料。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="jgao"/>

# 在 HDInsight 上將 Hive 與 Hadoop 搭配使用

[Apache Hive][apache-hive] 提供了一種透過名為 *HiveQL* 的 SQL 式指令碼語言來執行 MapReduce 工作的機制。Hive 是適用於 Hadoop 的資料倉儲系統，可用來執行資料彙總、查詢和大量資料分析。在本文中，您會使用 HiveQL 查詢隨附於 HDInsight 叢集佈建中的範例資料檔案。


**必要條件：**

- 您必須已佈建 **HDInsight 叢集**。有關如何使用 Azure 入口網站來執行此項作業的逐步解說，請參閱[開始使用 HDInsight][hdinsight-get-started]。有關透過其他各種方法建立此類叢集的指示，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。

- 您的工作站必須已安裝 **Azure PowerShell**。如需執行此項作業之指示，請參閱[安裝和設定 Azure PowerShell][powershell-install-configure]。

## 本文內容

* [Hive 使用案例](#usage)
* [上傳 Hive 資料表的資料](#uploaddata)
* [使用 PowerShell 執行 Hive 查詢](#runhivequeries)
* [使用 HDInsight Tools for Visual Studio 執行 HIve 查詢](#runhivefromvs)
* [使用 Tez 以提升效能](#usetez)
* [後續步驟](#nextsteps)

## <a id="usage"></a>Hive 使用案例

![HDI.HIVE.Architecture][image-hdi-hive-architecture]

Hive 專案在大量的未結構化資料上建構，然後讓您查詢該資料。Hive 會在 Java 型 MapReduce 架構上提供抽象層，讓使用者無需具備 Java 或 MapReduce 知識即可查詢資料。HiveQL (Hive 查詢語言) 可讓您使用類似於 T-SQL 的陳述式撰寫查詢。HDInsight 會將 HiveQL 查詢編譯為 MapReduce，並在叢集上執行。Hive 的其他優點包括：

- Hive 可讓熟悉 MapReduce 架構的程式設計人員插入自訂的對應程式和節流程式，以執行 HiveQL 語言的內建功能無法處理的複雜分析。
- Hive 最適合批次處理大量的固定資料 (例如網頁記錄)。它不適用於回應時間必須非常快的交易應用程式，例如資料庫管理系統。
- Hive 經過最佳化處理，能徹底發揮可擴充性 (可動態地將更多電腦加入至 Hadoop 叢集)、延展性 (在 MapReduce 架構內，搭配其他程式設計介面) 和容錯性。延遲性並非主要的設計考量。

## <a id="uploaddata"></a>上傳 Hive 資料表的資料

HDInsight 使用 Azure Blob 儲存容器做為 Hadoop 叢集的預設檔案系統。有些範例資料檔案會新增至 Blob 儲存體，做為叢集佈建的一部分。本文使用隨 HDInsight 叢集配送的 *log4j* 範例檔，此檔案儲存在您 Blob 儲存體容器下的 **/example/data/sample.log** 中。檔案中的每一筆記錄由一列欄位組成，包括以 `[LOG LEVEL]` 欄位來顯示類型和嚴重性。例如：

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

在前述範例中，記錄層級是「錯誤」。

> [AZURE.NOTE] 您也可以使用 [Apache Log4j][apache-log4j] 記錄公用程式產生自己的 log4j 檔案，然後將該檔案上傳至 Blob 容器。如需指示，請參閱[將資料上傳至 HDInsight][hdinsight-upload-data]。如需有關如何使用 Azure Blob 儲存體搭配 HDInsight 的詳細資訊，請參閱[使用 Azure Blob 儲存體搭配 HDInsight][hdinsight-storage]。

HDInsight 可使用 **wasb** 字首存取 Blob 儲存體中儲存的檔案。例如，若要存取 sample.log 檔案，您應使用下列語法：

	wasb:///example/data/sample.log

由於 WASB 是 HDInsight 的預設儲存體，因此您也可以使用 **/example/data/sample.log** 存取檔案。

> [AZURE.NOTE] 上述語法 **wasb:///** 可用來存取 HDInsight 叢集的預設儲存體容器所儲存的檔案。如果您在佈建叢集時指定其他儲存體帳戶，並想要存取這些帳戶上儲存的檔案，您可以指定容器名稱和儲存體帳戶地址來存取資料。例如 **wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**。

## <a id="runhivequeries"></a> 使用 PowerShell 執行 Hive 查詢

在 PowerShell 中，可使用 **Start-AzureHDInsightJob** Cmdlet 或 **Invoke-Hive** Cmdlet 來執行 Hive 查詢

* **Start-AzureHDInsightJob** 是一般的工作執行程式，可用來啟動 HDInsight 叢集上的 Hive、Pig 和 MapReduce 工作。**Start-AzureHDInsightJob** 是非同步的，並會在完成工作前傳回。會傳回工作的相關資訊，並可使用 Cmdlet，例如 **Wait-AzureHDInsightJob**、**Stop-AzureHDInsightJob** 和 **Get-AzureHDInsightJobOutput**。  **Get-AzureHDInsightJobOutput** 必須用來擷取工作寫入至 **STDOUT** 或 **STDERR** 的資訊。

* **Invoke-Hive** 會執行 Hive 查詢，等待查詢完成，並擷取查詢的輸出做為一個動作。

1. 開啟 Azure PowerShell 主控台視窗。相關指示位於[安裝和設定 Azure PowerShell][powershell-install-configure]。
2. 執行下列命令，以連接到您的 Azure 訂用帳戶：

		Add-AzureAccount

	系統會提示您輸入 Azure 帳號認證。

2. 在下列指令碼中設定變數並執行。

		# Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide HDInsight cluster name Where you want to run the Hive job
		$clusterName = "<HDInsightClusterName>"

3. 使用範例資料，執行下列指令碼來建立名為 **log4jLogs** 的新資料表。

		# HiveQL
		# Create an EXTERNAL table
		$queryString = "DROP TABLE log4jLogs;" +
		               "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
		               "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

	HiveQL 陳述式將執行下列動作

	* **DROP TABLE** - 當資料表已存在時，刪除資料表和資料檔
	* **CREATE EXTERNAL TABLE** - 在 Hive 中建立新的  'external' 資料表。外部資料表只會在 Hive 中儲存資料表定義 - 資料會保留在原始的位置
	* **ROW FORMAT** - 告訴 Hive 如何格式化資料。在此情況下，會以一個空格區隔每個記錄中的欄位
	* **STORED AS TEXTFILE LOCATION** - 將資料的儲存位置告訴 Hive (範例/資料目錄)，且資料是儲存為文字
	* **SELECT** - 選取資料欄 **t4** 中包含值 **[ERROR]** 的所有資料列計數。應會傳回值 **3**，因為有 3 個資料列包含此值

	> [AZURE.NOTE] 當您預期以外部來源更新基礎資料 (例如自動化資料上傳程序)，或以其他 MapReduce 作業更新基礎資料，但希望 Hive 查詢一律使用最新資料時，必須使用外部資料表。
	>
	> 捨棄外部資料表**不會**刪除資料，只會刪除資料表定義。


4. 執行下列指令碼，從先前的查詢建立 Hive 工作定義。

		# Create a Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	您也可以使用 -File 參數來指定 HDFS 上的 HiveQL 指令碼檔案。

5. 執行下列指令碼來提交 Hive 工作：

		# Submit the job to the cluster
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

6. 執行下列指令碼來等待 Hive 工作完成：

		# Wait for the Hive job to complete
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

7. 執行下列指令碼來列印標準輸出：

		# Print the standard error and the standard output of the Hive job.
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput


 	![HDI.HIVE.PowerShell][image-hdi-hive-powershell]

	結果如下：

		[ERROR] 3

	這表示 *sample.log* 檔案中有三個錯誤記錄執行個體。

4. 若要使用 **Invoke-Hive**，您必須先設定要使用的叢集。

		# Connect to the cluster
		Use-AzureHDInsightCluster $clusterName

4. 使用 **Invoke-Hive** Cmdlet，以下列指令碼新建名為 **errorLogs** 的  'internal' 資料表。

		# Run a query to create an 'internal' Hive table
		$response = Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		"@
		# print the output on the console
		Write-Host $response

	這些陳述式將執行下列動作。

	* **CREATE TABLE IF NOT EXISTS** - 建立還不存在的資料表。由於未使用 **EXTERNAL** 關鍵字，因此這是  'internal' 資料表，會儲存在 Hive 資料倉儲中，並完全由 Hive 完全
	* **STORED AS ORC** - 以最佳化資料列單欄式 (Optimized Row Columnar, ORC) 格式儲存資料。若是儲存 Hive 資料，這是高度最佳化和有效的格式
	* **INSERT OVERWRITE ...SELECT** - 從包含 **[ERROR]** 的 **log4jLogs** 資料表中選取資料列，然後將資料插入 **errorLogs** 資料表中

	> [AZURE.NOTE] 與 **EXTERNAL** 資料表不同，捨棄內部資料表也會刪除基礎資料。

	輸出顯示如下。

	![PowerShell Invoke-Hive output][img-hdi-hive-powershell-output]

	> [AZURE.NOTE] 如果 HiveQL 查詢的時間很長，您可以使用 PowerShell Here-Strings 或 HiveQL 指令碼檔案。下列程式碼片段說明如何使用 *Invoke-Hive* Cmdlet 來執行 HiveQL 指令碼檔案。必須將 HiveQL 指令碼檔案上傳至 WASB。
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> 如需 Here-Strings 的詳細資訊，請參閱 [Using Windows PowerShell Here-Strings][powershell-here-strings]。

5. 若要確認只將資料欄 t4 中包含 **[ERROR]** 的資料列儲存至 **errorLogs** 資料表，請使用下列陳述式，從 **errorLogs** 傳回所有資料列。

		#Select all rows
		$response = Invoke-Hive -Query "SELECT * from errorLogs;"
		Write-Host $response

	必須傳回三個資料列，且在資料欄 t4 中全部包含 **[ERROR]**。


> [AZURE.NOTE] 如有必要，您也可以將查詢的輸出匯入 Microsoft Excel 中，以進行深入分析。如需指示，請參閱[使用 Power Query 將 Excel 連接到 Hadoop][import-to-excel]。

## <a id="runhivefromvs"></a>使用 Visual Studio 執行 Hive 查詢
HDInsight Tools for Visual Studio 隨附 Azure SDK for .NET 2.5 版或更新版本。  您可以使用 Visual Studio 的工具，連線至 HDInsight 叢集、建立 Hive 資料表及執行 Hive 查詢。  如需詳細資訊，請參閱[開始使用 HDInsight Hadoop Tools for Visual Studio][1]。



## <a id="usetez"></a>使用 Tez 以提升效能

[Apache Tez][apache-tez] 是可讓您使用資料高用量應用程式 (例如 Hive) 大規模而高效能地執行作業的架構。在最新版的 HDInsight 中，Hive 現已支援在 Tez 上執行。  此功能目前預設為關閉，而必須啟用。  在未來的叢集版本中，此功能將預設為開啟。若要充分發揮 Tez 的效益，您必須設定 Hive 查詢的下列值：

		set hive.execution.engine=tez;

此值可就個別查詢逐一提交，只要將值放在查詢開頭處即可。  您也可以在建立叢集時設定組態值，而在叢集上將此值預設為開啟。  您可以在  [佈建 HDInsight 叢集中][hdinsight-provision]找到更多詳細資料。

[Tez 上的 Hive 設計文件][hive-on-tez-wiki]包含實作選擇和調整組態的詳細資料。


## <a id="nextsteps"></a>後續步驟

Hive 可讓您使用 SQL 型的查詢語言來輕鬆查詢資料，但 HDInsight 還有其他元件也提供補充的功能，例如資料移動和轉換。若要深入了解，請參閱下列文章：

* [開始使用 HDInsight Hadoop Tools for Visual Studio][1]
* [在 HDInsight 上使用 Oozie][hdinsight-use-oozie]
* [以程式設計方式提交 Hadoop 工作][hdinsight-submit-jobs]
* [在 HDInsight 上使用 Pig](../hdinsight-use-pig/)
* [使用 HDInsight 分析班機延誤資料][hdinsight-analyze-flight-data]
* [Azure HDInsight SDK 文件][hdinsight-sdk-documentation]
* [將資料上傳至 HDInsight][hdinsight-upload-data]
* [開始使用 Azure HDInsight](../hdinsight-get-started/)


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/zh-tw/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
<!--HONumber=42-->
