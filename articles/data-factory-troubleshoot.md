<properties title="Troubleshoot Azure Data Factory issues" pageTitle="Azure 資料處理站的疑難排解" description="Learn how to troubleshoot issues with using Azure Data Factory." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# 資料處理站的疑難排解
您可以使用 Azure 入口網站 (或) Azure PowerShell Cmdlet 對 Azure 資料處理站進行疑難排解。本主題逐步解說如何使用 Azure 入口網站來對您使用資料處理站時遇到的錯誤進行快速疑難排解。 

## 本文內容

- [逐步介紹：利用複製資料對錯誤進行疑難排解](#copywalkthrough)
- [逐步介紹：利用 Hive/Pig 處理對錯誤進行疑難排解](#pighivewalkthrough)

## <a name="copywalkthrough"></a>逐步介紹：利用複製資料對錯誤進行疑難排解
在本逐步解說中，您將從＜開始使用資料處理站＞文章裡的教學課程中引用一個錯誤，然後了解如何使用 Azure 入口網站對錯誤進行疑難排解。

### 必要條件
1. 完成[＜開始使用 Azure 資料處理站＞][adfgetstarted]文章中的教學課程。
2. 確認 **ADFTutorialDataFactory** 可在 Azure SQL Database 中的 **emp** 資料表中產生資料。  
3. 現在，請從 Azure SQL Database 中刪除 **emp** 資料表 (**放棄 emp 資料表**)。這會引發錯誤。
4. 執行下列 **Azure PowerShell** 中的命令，以更新管線的活動期，如此一來，管線會嘗試將資料寫入已不存在的 **emp** 資料表。

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline
	
	> [WACOM.NOTE] 將 <b>StartDateTime</b> 的值用當天日期取代，然後 <b>EndDateTime</b> 的值用隔天日期取代。 


### 使用 Azure 預覽入口網站對錯誤進行疑難排解

1.	登入 [Azure 預覽入口網站][azure-preview-portal]。 
2.	按一下**開始面板**上的 [**ADFTutorialDataFactory**]。如果您沒有看見**開始面板**上的資料處理站連結，按一下 [**瀏覽**] 中心，然後按一下 [**全部**]。按一下 [**瀏覽**] 分頁中的 [**資料處理站**]，然後按一下 [**ADFTutorialDataFactory**]。
3.	請注意，您會看見 [**With errors**] 顯示在 [**資料集**] 的磚上。按一下 [**With errors**]。您應該會看見 [**Datasets with errors**] 的分頁。

	![Data Factory with Errors link][image-data-factory-troubleshoot-with-error-link]

4. 在出現錯誤的**資料集**分頁中，按一下 [**EmpSQLTable**] 可看見 [**資料表**] 分頁。	

	![Datasets with errors blade][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. 在 [**資料表**] 分頁中，您應該可以看見問題區塊，也就是底部 [**問題區塊**] 清單中有錯誤的區塊。您也可以在 [**最近的區塊**] 清單中看見任何最近出現錯誤的區塊。按一下 [**問題區塊**] 清單中的區塊。 

	![Table blade with problem slices][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	如果您按一下 [**問題區塊**] (不是在指定問題上)，您將會看見 [**資料區塊**] 分頁，然後按一下**特定的問題區塊**檢視所選資料區塊的 [**資料區塊**] 投影片。

6. 在 **EmpSQLTable** 的 [**資料區塊**] 分頁中，您將會看見底部清單中所有此區塊的**活動執行**。在失敗的清單中的**活動執行**上按一下。

	![Data Slice blade with active runs][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. 在您所選的活動執行的 [**活動執行詳細資料**] 分頁中，您應該可以看見關於此錯誤的詳細資料。在此案例中，您會看到：**無效的物件名稱 'emp'**。

	![Activity run details with an error][image-data-factory-troubleshoot-activity-run-with-error]

若要解決這個問題，請使用[＜開始使用資料處理站＞][adfgetstarted]文章中的 SQL 指令碼建立 **emp** 資料表。


### 使用 Azure PowerShell Cmdlet 對錯誤進行疑難排解
1.	啟動 **Azure PowerShell**。 
2.	切換至 **AzureResourceManager** 模式，因為資料處理站 Cmdlet 只可在此模式中使用。

         
		switch-azuremode AzureResourceManager

3. 執行 Get-AzureDataFactorySlice 命令來查看區塊和其狀態。您應該會看到有以下狀態的區塊：失敗。	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [WACOM.NOTE] 將 **StartDateTime** 取代成您針對 **Set-AzureDataFactoryPipelineActivePeriod** 而指定的 StartDateTime 的值。 

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	記下輸出內的問題區塊 (區塊**狀態**設定為**失敗**) 的**開始**時間。 
4. 現在，執行 **Get-AzureDataFactoryRun** Cmdlet 以取得關於此區塊之活動執行的詳細資料。
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	**StartDateTime** 的值是錯誤/問題區塊的開始時間，也就是您在先前步驟中記下的。date-time 應該用雙引號括住。
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

 

## <a name="pighavewalkthrough"></a>逐步介紹：利用 Hive/Pig 處理對錯誤進行疑難排解
本逐步解說提供搭配 Azure 預覽入口網站和 Azure PowerShell 使用 Hive/Pig 處理對錯誤進行疑難排解。 


### 逐步介紹：使用 Azure 入口網站並藉由 Pig/Hive 處理對錯誤進行疑難排解
在此案例中，因為 HDInsight 叢集中 Hive 處理失敗，資料集處於錯誤狀態。

1. 在 [**資料處理站**] 首頁按一下 [**資料集**] 磚上的 [**With errors**]。

	![With errors link on Datasets tile][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. 在 [**Datasets with errors**] 分頁上，按一下您感興趣的 [**資料表**]。

	![Datasets with errors blade][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. 在 [**資料表**] 分頁中，按一下其**狀態**設為**失敗**的 [**問題區塊**]。

	![Table with problem slices][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. 在 [**資料區塊**] 分頁中，按一下失敗的 [**活動執行**]。

	![Data slice with failed runs][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. 在 [**活動執行詳細資料**]分頁中，您可以下載與 HDInsight 處理相關聯的檔案。按一下 **Status/stderr** 中的 [**下載**] 以下載包含錯誤詳細資料的錯誤記錄檔。

	![Activity run details with download link][image-data-factory-troubleshoot-activity-run-details]

    
### 逐步介紹：搭配 Azure PowerShell 使用 Pig/Hive 處理對錯誤進行疑難排解
1.	啟動 **Azure PowerShell**。 
2.	切換至 **AzureResourceManager** 模式，因為資料處理站 Cmdlet 只可在此模式中使用。

         
		switch-azuremode AzureResourceManager

3. 執行 Get-AzureDataFactorySlice 命令來查看區塊和其狀態。您應該會看到有以下狀態的區塊：失敗。	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [WACOM.NOTE] 將 **StartDateTime** 取代成您針對 **Set-AzureDataFactoryPipelineActivePeriod** 而指定的 StartDateTime 的值。 

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	記下輸出內的問題區塊 (區塊**狀態**設定為**失敗**) 的**開始**時間。 
4. 現在，執行 **Get-AzureDataFactoryRun** Cmdlet 以取得關於此區塊之活動執行的詳細資料。
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	**StartDateTime** 的值是錯誤/問題區塊的開始時間，也就是您在先前步驟中記下的。date-time 應該用雙引號括住。
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

6. 您可以使用在上述輸出中看見的識別碼值，執行 **Save-AzureDataFactoryLog** Cmdlet，以及使用此 Cmdlet 的 **-DownloadLogs** 選項下載記錄檔。

## 疑難排解秘訣

### 無法連線到內部部署 SQL Server 
確認已安裝閘道的機器可連接到 SQL Server。


1. 偵測已安裝 SQL Server 的機器
2. 在安裝閘道的機器上，嘗試使用您在 Azure 入口網站中以 SQL Server Management Studio (SSMS) 指定的憑證連線到 SQL Server 執行個體。

### 複製作業失敗
1. 啟動已安裝閘道之機器上的「資料管理閘道設定管理員」。確認 **Azure 入口網站**上的**閘道名稱**設定為邏輯閘道名稱，**閘道主要狀態**為**已註冊**，而**服務狀態**為**已開始**。 
2. 啟動**事件檢視器**。展開 [**應用程式及服務記錄檔**]，然後按一下 [**資料管理閘道**]。查看是否有任何與資料管理閘道相關的錯誤。 



## 另請參閱

文章 | 描述
------ | ---------------
[啟用您的管線以使用內部部署資料][use-onpremises-datasources] | 本文逐步解說如何將資料從內部部署 SQL Server Database複製到 Azure blob。
[搭配 Data Factory 使用 Pig 和 Hive][use-pig-and-hive-with-data-factory] | 本文逐步解說如何使用 HDInsight 活動以執行 hive/pig 指令碼處理輸入資料並產生輸出資料。 
[教學課程：使用 Data Factory 來移動並處理記錄檔][adf-tutorial] | 本文提供端對端逐步解說，展示如何使用 Azure Data Factory 實作近乎真實世界的案例，將記錄檔中的資料轉換為見解。
[在 Data Factory 中使用自訂活動][use-custom-activities] | 本文章提供的逐步解說具有建立自訂活動並將它用於管線中的逐步指示。 
[使用 PowerShell 監視和管理 Azure 資料處理站][monitor-manage-using-powershell] | 本文說明如何使用 Azure PowerShell Cmdlet 監視 Azure 資料處理站。 
[Data Factory 的疑難排解][troubleshoot] | 本文說明如何對 Azure Data Factory 的問題進行疑難排解。您可以透過引用一個錯誤 (刪除 Azure SQL Database 中的資料表)，嘗試本文中針對 ADFTutorialDataFactory 所做的逐步解說。 
[Azure Data Factory 開發人員參考][developer-reference] | 「開發人員參考」具有 Cmdlet、JSON 指令碼、函數等等的完整參考內容。 
[Azure Data Factory Cmdlet 參考][cmdlet-reference] | 本參考內容詳述所有 **Data Factory Cmdlet**。

[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
