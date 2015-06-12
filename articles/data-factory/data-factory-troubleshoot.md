<properties 
	pageTitle="Azure 資料處理站的疑難排解" 
	description="了解如何使用 Azure Data Factory 進行問題的疑難排解。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# 資料處理站的疑難排解
您可以使用 Azure 入口網站 (或) Azure PowerShell Cmdlet 對 Azure 資料處理站進行疑難排解。本主題逐步解說如何使用 Azure 入口網站來對您使用資料處理站時遇到的錯誤進行快速疑難排解。

## 問題： 無法執行資料處理站 cmdlet
若要解決這個問題，切換至 Azure 模式 **AzureResourceManager**：

啟動 **Azure PowerShell** 並執行下列命令以切換到 **AzureResourceManager** 模式。中可用的 Azure 資料 Factory cmdlet **AzureResourceManager** 模式。

         switch-azuremode AzureResourceManager

## 執行資料處理站 cmdlet 時的問題： 未授權的錯誤
您可能不使用 Azure PowerShell 使用右 Azure 帳戶或訂用帳戶。使用下列 cmdlet 來選取權限的 Azure 帳戶和訂閱來使用 Azure PowerShell 使用。

1. 加入-AzureAccount-使用適當的使用者識別碼和密碼
2. Get-azuresubscription-檢視帳戶的所有訂閱。 
3. Select-azuresubscription <subscription name> -選取正確的訂用帳戶。使用您在 Azure 預覽入口網站上建立的資料處理站所使用的相同。

## 問題： 無法啟動資料閘道器 Express 安裝從 Azure 入口網站
Express 安裝程式以進行資料閘道器需要 Internet Explorer 或 Microsoft ClickOnce 相容的網頁瀏覽器。如果您無法啟動 Express 安裝程式，您可以

1. 如果您與其他瀏覽器就會失敗，請切換至 Internet Explorer。或
2. 使用顯示在入口網站中相同的分頁上的 [手動 Setup] 連結以進行安裝，然後複製在畫面上，提供的索引鍵，並貼上資料管理閘道器組態就緒時。如果它不會啟動，請檢查 「 Microsoft 資料管理閘道器 」 開始] 功能表，並啟動時，索引鍵中貼上。 


## 問題： 無法啟動認證管理員從 Azure 入口網站
何時設定或更新透過 Azure 入口網站，將會啟動應用程式，以確保安全性認證管理員的 SQL Server 連結服務。它需要 Internet Explorer 或 Microsoft ClickOnce 相容的網頁瀏覽器。如果您無法與其他瀏覽器，您可以切換至 Internet Explorer。

## 問題： 無法連線到內部部署 SQL Server 
確認已安裝閘道的機器可連接到 SQL Server。您可以在電腦上安裝閘道器，

1. Ping 電腦安裝 SQL Server。或
2. 嘗試連接到 SQL Server 執行個體，使用您在 Azure 入口網站使用 SQL Server Management Studio (SSMS) 指定的認證。


## 問題： 輸入配量處於 PendingExecution 或 PendingValidation 狀態為曾

配量可能會處於 **PendingExecution** 或 **PendingValidation** 狀態，因為許多原因，以及其中一個常見的原因在於 **waitOnExternal** 中未指定屬性 **可用性** ＞ 一節的第一個資料表/資料集在管線中。Azure 資料 Factory 的範圍之外產生任何資料集應該用來標記 **waitOnExternal** 下的屬性 **可用性** ＞ 一節。這表示資料是外部，並不支援任何管線內的資料處理站。資料配量會標示為 **準備** 資料一旦就可以使用個別的存放區中。

請參閱下列範例使用 **waitOnExternal** 屬性。您可以指定 **waitOnExternal {}** 而不需要設定一節中的屬性值，如此會使用預設值。

請參閱資料表中的主題 [JSON 指令碼參考][json-scripting-reference] 如需有關此屬性的詳細資訊。
	
	{
	    "name": "CustomerTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureBlobLocation",
	            "folderPath": "MyContainer/MySubFolder/",
	            "linkedServiceName": "MyLinkedService",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ",",
	                "rowDelimiter": ";"
	            }
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1,
	            "waitOnExternal":
	            {
	                "dataDelay": "00:10:00",
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}

 若要解決這個錯誤，新增 **waitOnExternal** JSON 定義輸入資料表的區段，並重新建立資料表。

## 問題： 混合式複製作業失敗
若要了解更多詳細資料：

1. 閘道已安裝所在之電腦上啟動資料管理閘道組態管理員。確認 **閘道器名稱** 上設定為邏輯的閘道器名稱 **Azure 入口網站**, ，**閘道器金鑰狀態** 是 **註冊** 和 **服務狀態** 是 **Started**。 
2. 啟動 **事件檢視器**。展開 **應用程式及服務記錄檔** 按一下 **資料管理閘道器**。查看是否有任何與資料管理閘道相關的錯誤。 

## 問題： 上選 HDInsight 佈建失敗，錯誤

當使用型別的 HDInsightOnDemandLinkedService 連結的服務時，您應該指定指向 Azure Blob 儲存體 linkedServiceName。此儲存體帳戶將用來複製記錄檔和隨選 HDInsight 叢集的支援檔案。有時會隨選佈建在 HDInsight 上的活動可能會失敗並出現下列錯誤：

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

此錯誤通常表示 linkedServiceName 中指定的儲存體帳戶的位置不在其中發生 HDInsight 佈建的相同資料中心位置。例如，如果您的 Azure 資料 Factory 位置是美國西部，和隨選 HDInsight 佈建動作會在美國西部，但是 Azure blob 儲存體帳戶位置設定為美國東部、 隨選佈建將會失敗。

另外，還有第二個的 JSON 屬性 additionalLinkedServiceNames 其中可指定其他儲存體帳戶中隨選 HDInsight。這些其他連結的儲存體帳戶應該是在相同的位置與 HDInsight 叢集，或它將會失敗，並同樣的錯誤。



## 問題： 自訂活動會失敗
當 Azure 資料 Factory (管線活動類型 CustomActivity) 中使用自訂活動，自訂應用程式會在指定連結的服務到 HDInsight 以對應只串流 MapReduce 工作。

自訂活動執行時，都能夠擷取該輸出從 HDInsight 叢集，並且儲存在 Azure 資料 Factory *adfjobs* Azure Blob 儲存體帳戶中的儲存體容器。發生錯誤，您可以閱讀的文字 **stderr** 輸出文字檔案之後所造成的失敗。這些檔案包括存取和讀取從 Azure 入口網站本身在 web 瀏覽器中，或使用儲存體總管工具來存取直接保留在 Azure Blob 儲存體中的儲存體容器中的檔案。

列舉並讀取特定的自訂活動的記錄，您可以依照下列其中一個詳述的逐步解說稍後在此頁面上。摘要：

1.  在 Azure 入口網站中 **瀏覽** 找出您的資料處理站。
2.  使用 **圖表** 按鈕，即可檢視資料 factory 圖表，然後按一下 **Dataset** 遵循特定的資料表 **管線** 具有自訂活動。 
3.  在 **資料表** 分頁中感興趣的配量上，按一下 **問題配量** 調查的時間範圍。
4.  詳細 **資料配量** 分頁會出現，它可以列出多個 **活動會執行** 配量。按一下 **活動** 從清單。 
5.   **活動執行詳細資料** 分頁會出現。它會列出 **錯誤訊息** 中途分頁，以及數個 **記錄檔** 附屬於執行該活動分頁底部所列。
	- 記錄檔/系統-0.log
	- 狀態
	- 狀態/結束
	- 狀態/stderr
	- 狀態/stdout

6. 按一下第一天 **記錄檔** 清單，以及記錄檔中的項目會在新的分頁中開啟顯示讓您讀取的完整文字。每一項，即可檢閱每個記錄檔的文字。會開啟 [文字] 檢視器分頁。您可以按一下 **下載** ] 按鈕，下載的選擇性離線的文字檔。

一個 **常見的錯誤** 從自訂活動會封裝執行失敗，結束代碼 '1'。請參閱 'wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/狀態/stderr' 如需詳細資訊。

若要查看這種錯誤的其他詳細資料，請開啟 **stderr** 檔案。看過有的一個常見的錯誤是這類逾時狀況： 資訊 mapreduce。作業: 工作識別碼： attempt_1424212573646_0168_m_000000_0，狀態: 失敗 AttemptID:attempt_1424212573646_0168_m_000000_0 後逾時 600 秒

如果工作已重試 3 次，例如範圍 30 分鐘內，可能出現多次，這個相同錯誤。

此逾時錯誤指出 600 秒 (10 分鐘) 發生逾時。通常這表示自訂.Net 應用程式並未授與任何狀態更新，時間為 10 分鐘。如果應用程式擱置或停止了某件東西上等候逾時是安全機制可防止它們無止盡等待和延遲 Azure 資料 Factory 管線太長，10 分鐘。

此逾時產生的自訂活動中連結的 HDInsight 叢集組態中。設定是 **mapred.task.timeout**, ，預設為 600000 毫秒，此處 Apache 預設設定中所述： http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

您可以覆寫這個預設值的佈建 HDInsight 佈建叢集時變更預設值。當使用 Azure 資料 Factory 和 **HDInsight 隨選** 連結服務，JSON 屬性可以加入附近 HDInsightOnDemandLinkedService JSON 內容。例如，您可以增加到 20 分鐘內使用此 JSON 屬性的值。
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

更多的內容和編輯這些對應 JSON 的完整範例減少組態屬性，請參閱範例 #3 中的 MSDN 文件 https://msdn.microsoft.com/library/azure/dn893526.aspx

## 問題： PowerShell 要求失敗，發生錯誤錯誤 400 要求錯誤"...找不到已註冊的資源提供者"

於 2015 年 3 月 10 日，從 Azure 資料 Factory PowerShell 早期私人預覽版本 2014年-05-01-預覽、 2014年-07-01-預覽、 和 2014年-08-01-預覽將會停用。我們建議您改用 ADF 指令程式，也就是現在的 Azure PowerShell 下載，例如從此 URL 下載一部分的最新版本 http://go.microsoft.com/?linkid=9811175&clcid=0x409

如果您使用 Azure PowerShell sdk 已停止的版本可能會收到下列錯誤：

		HTTP/1.1 400 Bad Request
		Cache-Control: no-cache
		Pragma: no-cache
		Content-Type: application/json; charset=utf-8
		Expires: -1
		x-ms-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-correlation-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-routing-request-id: WESTUS:20150306T234829Z:e07181e4-e421-46be-8a08-1f71d5e90494
		Strict-Transport-Security: max-age=31536000; includeSubDomains
		Date: Fri, 06 Mar 2015 23:48:29 GMT
		Content-Length: 157
		{"error":{"code":"NoRegisteredProviderFound","message":"No registered resource provider found for location 'west US' and API version '2014-05-01-preview'."}}


## <a name="copywalkthrough"></a> 逐步解說： 疑難排解與將資料複製錯誤
在本逐步解說中，您將從＜開始使用資料處理站＞文章裡的教學課程中引用一個錯誤，然後了解如何使用 Azure 入口網站對錯誤進行疑難排解。

### 必要條件
1. 完成本教學課程中的 [開始使用 Azure 資料 Factory][adfgetstarted] 發行項。
2. 確認已選取 **ADFTutorialDataFactory** 後產生的資料在 **emp** Azure SQL Database 中的資料表。  
3. 現在，請刪除 **emp** 資料表 (* * 卸除資料表 emp * *) 從 Azure SQL 資料庫。這會引發錯誤。
4. 執行下列命令 **Azure PowerShell** 更新管線使用中的週期，以便讓它會嘗試將資料寫入 **emp** 不存在的資料表。

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	> [AZURE.NOTE]取代 <b>StartDateTime</b> 值與目前的日期和 <b>EndDateTime</b> 隔天的值。


### 使用 Azure 預覽入口網站對錯誤進行疑難排解

1.	登入 [Azure 預覽入口網站][azure-preview-portal]。 
2.	按一下 **ADFTutorialDataFactory** 從 **儀表板**。如果您沒有看到連結上的資料處理站 **儀表板**, ，按一下 **瀏覽** 中樞和按一下 **一切**。按一下 **...資料 factory** 中 **瀏覽** blade 中，然後按一下 [ **ADFTutorialDataFactory**。
3.	請注意，您會看到 **錯誤** 上 **資料集** 並排顯示。按一下 **錯誤**。您應該會看到 **但發生錯誤的資料集** 分頁。

	![資料處理站與錯誤連結][image-data-factory-troubleshoot-with-error-link]

4. 在 **資料集** 錯誤 blade 中，按一下 [ **EmpSQLTable** 查看 **資料表** 分頁。

	![錯誤分頁的資料集][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. 在 **資料表** blade 中，您應該會看到問題配量，亦即，發生錯誤的配量 **問題配量** 清單底部。您也可以查看任何最新的配量，但發生錯誤 **最近的配量** 清單。按一下中扇區 **問題配量** 清單。

	![問題配量與資料表分頁][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	如果您按一下 **問題配量** (不是在特定問題)，您會看到 **資料配量** 分頁，然後按一下 [ **特定問題配量** 查看 **資料配量** 選取的資料配量的投影片。

6. 在 **資料配量** 刀鋒伺服器 **EmpSQLTable**, ，您會看到所有 **活動執行** 針對下方清單中的配量。按一下 **活動執行** 從失敗的清單。

	![具有作用中執行的資料配量分頁][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. 在 **活動執行詳細資料** 分頁活動執行您所選，您應該會看到錯誤的詳細資訊。在此案例中，您會看到： **無效的物件名稱 'emp'**。

	![活動執行詳細資料，並傳回錯誤][image-data-factory-troubleshoot-activity-run-with-error]

若要解決這個問題，請建立 **emp** 資料表中使用 SQL 指令碼從 [開始使用資料 Factory][adfgetstarted] 發行項。


### 使用 Azure PowerShell Cmdlet 對錯誤進行疑難排解
1.	啟動 **Azure PowerShell**。 
2.	切換至 **AzureResourceManager** 模式資料 Factory cmdlet 都只在這種模式。

         
		switch-azuremode AzureResourceManager

3. 執行 Get-AzureDataFactorySlice 命令來查看區塊和其狀態。您應該會看到配量，狀態: 失敗。

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [AZURE.NOTE]取代 **StartDateTime** StartDateTime 指定值的 **組 AzureDataFactoryPipelineActivePeriod**。

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	請注意 **開始** 時間問題配量 (與配量 **狀態** 設 **失敗**) 在輸出中。 
4. 現在，執行 **Get AzureDataFactoryRun** 指令程式可取得有關執行配量的活動詳細資料。
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	值 **StartDateTime** 是您先前步驟中記下錯誤/問題配量的開始時間。date-time 應該用雙引號括住。
5. 您應該會看到關於此錯誤的詳細資料 (如下所示) 輸出：

		Id                  	: 2b19475a-c546-473f-8da1-95a9df2357bc
		ResourceGroupName   	: ADFTutorialResourceGroup
		DataFactoryName     	: ADFTutorialDataFactory
		TableName           	: EmpSQLTable
		ResumptionToken    		:
		ContinuationToken   	:
		ProcessingStartTime 	: 10/15/2014 11:13:39 PM
		ProcessingEndTime  	 	: 10/15/2014 11:16:59 PM
		PercentComplete     	: 0
		DataSliceStart     		: 10/15/2014 4:00:00 PM
		DataSliceEnd       		: 10/15/2014 5:00:00 PM
		Status              	: FailedExecution
		Timestamp           	: 10/15/2014 11:13:39 PM
		RetryAttempt       		: 0
		Properties          	: {}
		ErrorMessage        	: Unknown error in CopyActivity: System.Data.SqlClient.SqlException (0x80131904): **Invalid object name 'emp'.**
                         at System.Data.SqlClient.SqlConnection.OnError(SqlException exception, Boolean
                      breakConnection, Action`1 wrapCloseInAction)
                         at System.Data.SqlClient.TdsParser.ThrowExceptionAndWarning(TdsParserStateObject stateObj,

 

## <a name="pighivewalkthrough"></a> 逐步解說： 疑難排解與 Hive/Pig 處理錯誤
本逐步解說提供搭配 Azure 預覽入口網站和 Azure PowerShell 使用 Hive/Pig 處理對錯誤進行疑難排解。


### 逐步解說： 使用 Azure 入口網站，以疑難排解 Pig/Hive 處理錯誤
在此案例中，因為 HDInsight 叢集中 Hive 處理失敗，資料集處於錯誤狀態。

1. 按一下 **錯誤** 上 **資料集** 磚上 **資料 FACTORY** 首頁。

	![在 [資料集並排顯示的錯誤連結][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. 在 **但發生錯誤的資料集** blade 中，按一下 **資料表** 您感興趣。

	![錯誤分頁的資料集][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. 在 **資料表** 分頁上，按一下 **問題配量** 與 **狀態** 設 **失敗**。

	![問題配量與資料表][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. 在 **資料配量** blade 中，按一下 **活動執行** 失敗。

	![資料配量與失敗的執行][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. 在 **活動執行詳細資料** blade 中，您可以下載與 HDInsight 處理相關聯的檔案。按一下 **下載** 的 **狀態/stderr** 下載錯誤記錄檔，其中包含錯誤的詳細資訊。

	![活動執行詳細資料的下載連結][image-data-factory-troubleshoot-activity-run-details]

    
### 逐步解說： 使用 Azure PowerShell 使用 Pig/Hive 處理錯誤的疑難排解
1.	啟動 **Azure PowerShell**。 
2.	切換至 **AzureResourceManager** 模式資料 Factory cmdlet 都只在這種模式。

         
		switch-azuremode AzureResourceManager

3. 執行 Get-AzureDataFactorySlice 命令來查看區塊和其狀態。您應該會看到配量，狀態: 失敗。

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [AZURE.NOTE]取代 **StartDateTime** StartDateTime 指定值的 **組 AzureDataFactoryPipelineActivePeriod**。

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	請注意 **開始** 時間問題配量 (與配量 **狀態** 設 **失敗**) 在輸出中。 
4. 現在，執行 **Get AzureDataFactoryRun** 指令程式可取得有關執行配量的活動詳細資料。
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	值 **StartDateTime** 是您先前步驟中記下錯誤/問題配量的開始時間。date-time 應該用雙引號括住。
5. 您應該會看到關於此錯誤的詳細資料 (如下所示) 輸出：

		Id                  : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   : ADF
		DataFactoryName     : LogProcessingFactory3
		TableName           : EnrichedGameEventsTable
		ProcessingStartTime : 10/10/2014 3:04:52 AM
		ProcessingEndTime   : 10/10/2014 3:06:49 AM
		PercentComplete     : 0
		DataSliceStart      : 5/5/2014 12:00:00 AM
		DataSliceEnd        : 5/6/2014 12:00:00 AM
		Status              : FailedExecution
		Timestamp           : 10/10/2014 3:04:52 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        : Pig script failed with exit code '5'. See 'wasb://adfjobs@spestore.blob.core.windows.net/PigQuery
								Jobs/841b77c9-d56c-48d1-99a3-8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for more details.
		ActivityName        : PigEnrichLogs
		PipelineName        : EnrichGameLogsPipeline
		Type                :

6. 您可以執行 **儲存 AzureDataFactoryLog** 指令程式，請參閱上述輸出中，並下載記錄的識別碼值與檔案所使用的 **-DownloadLogs** cmdlet 的選項。



[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png

<!---HONumber=GIT-SubDir--> 