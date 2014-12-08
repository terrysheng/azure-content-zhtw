<properties title="Monitor and manage Azure Data Factory using Azure PowerShell" pageTitle="使用 Azure PowerShell 監視和管理 Azure Data Factory" description="Learn how to use Azure PowerShell to monitor and manage Azure data factories you have created." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# 使用 Azure PowerShell 監視和管理 Azure Data Factory
下表列出您可以用來使用 Azure PowerShell 監視和管理 Azure Data Factory 的 Cmdlet。 

> [WACOM.NOTE] 如需 Data Factory Cmdlet 的完整描述，請參閱 [Data Factory Cmdlet 參考][cmdlet-reference]。 


- [Get-AzureDataFactory](#get-azuredatafactory)
- [Get-AzureDataFactoryLinkedService](#get-azuredatafactorylinkedservice)
- [Get-AzureDataFactoryTable](#get-azuredatafactorytable)
- [Get-AzureDataFactoryPipeline](#get-azuredatafactorypipeline)
- [Get-AzureDataFactorySlice](#get-azuredatafactoryslice)
- [Get-AzureDataFactoryRun](#get-azuredatafactoryrun)
- [Save-AzureDataFactoryLog](#save-azuredatafactorylog)
- [Get-AzureDataFactoryGateway](#get-azuredatafactorygateway)
- [Set-AzureDataFactoryPipelineActivePeriod](#set-azuredatafactorypipelineactiveperiod)
- [Set-AzureDataFactorySliceStatus](#set-azuredatafactoryslicestatus)
- [Suspend-AzureDataFactoryPipeline](#suspend-azuredatafactorypipeline)
- [Resume-AzureDataFactoryPipeline](#resume-azuredatafactorypipeline)


##<a name="get-azuredatafactory"></a>Get-AzureDataFactory
在指定的資源群組內取得 Azure 訂閱中特定 Data Factory 或所有 Data Factories 的相關資訊。
 
###範例 1

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup

此命令會傳回 ADFTutorialResourceGroup 資源群組中的所有 Data Factory。
 
###範例 2

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactory

此命令會傳回 ADFTutorialResourceGroup 資源群組中 ADFTutorialDataFactory Data Factory 的詳細資料。 

## <a name="get-azuredatafactorylinkedservice"></a> Get-AzureDataFactoryLinkedService ##
Get-AzureDataFactoryLinkedService Cmdlet 會取得 Azure Data Factory 中特定連結服務或所有連結服務的相關資訊。

### 範例 1 ###

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
 
這個命令會傳回 Azure Data Factory ADFTutorialDataFactory 中所有連結服務的相關資訊。


您可以使用-DataFactory 參數，而不使用 DataFactoryName 和 ResourceGroupName 參數。這可協助您只需輸入一次資源群組和 Factory 名稱，並使用 Data Factory 物件做為同時採用 ResourceGroupName 和 DataFactoryName 做為參數之所有 Cmdlet 的參數。

    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df 

### 範例 2

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name MyBlobStore

這個命令會傳回 Azure Data Factory ADFTutorialDataFactory 中連結服務 MyBlobStore 的相關資訊。 

您可以使用-DataFactory 參數，而不使用 -ResourceGroup 和 -DataFactoryName 參數，如下所示： 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df -Name MyBlobStore


## <a name="get-azuredatafactorytable"></a> Get-AzureDataFactoryTable
Get-AzureDataFactoryTable Cmdlet 會取得 Azure Data Factory 中特定資料表或所有資料表的相關資訊。 

### 範例 1

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

這個命令會傳回 Azure Data Factory ADFTutorialDataFactory 中所有資料表的相關資訊。

您可以使用-DataFactory 參數，而不使用 -ResourceGroup 和 -DataFactoryName 參數，如下所示： 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryTable -DataFactory $df

### 範例 2

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name EmpTableFromBlob

這個命令會傳回 Azure Data Factory ADFTutorialDataFactory 中資料表 EmpTableFromBlob 的相關資訊。



## <a name="get-azuredatafactorypipeline"></a>Get-AzureDataFactoryPipeline
Get-AzureDataFactoryPipeline Cmdlet 會取得 Azure Data Factory 中特定管線或所有管線的相關資訊。

### 範例 1

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

這個命令會傳回 Azure Data Factory ADFTutorialDataFactory 中所有管線的相關資訊。

### 範例 2

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialPipeline

取得 Azure Data Factory ADFTutorialDataFactory 中管線 ADFTutorialPipeline 的相關資訊。

## <a name="get-azuredatafactoryslice"> </a> Get-AzureDataFactorySlice
Get-AzureDataFactorySlice Cmdlet 會取得 Azure Data Factory 中所有在 StartDateTime 之後以及在 EndDateTime 之前產生的資料表配量。狀態為 Ready 的資料配量可供相依配量使用。

下表列出配量的所有狀態及其描述。

<table border="1">	
	<tr>
		<th align="left">Status</th>
		<th align="left">描述</th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>資料處理尚未啟動。</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>資料處理進行中。</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>資料處理已完成，而且資料配量就緒。</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>產生配量的執行無法執行。</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>略過配量處理。</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>重試產生配量的執行。</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>配量的資料處理逾時。</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>在開始處理之前，資料配量正在等候依照驗證原則進行驗證。</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>重試配量的驗證。</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>配量的驗證失敗。</td>
	</tr>

	<tr>
		<td>LongRetry</td>
		<td>如果已在資料表 JSON 中指定 LongRetry，而且配量的一般重試失敗，則配量將會處於這個狀態。</td>
	</tr>

	<tr>
		<td>ValidationInProgress</td>
		<td>正在執行配量的驗證 (根據 JSON 資料表中定義的原則) 。</td>
	</tr>

</table>

針對每個配量，您可以向下深入鑽研，並使用 Get-AzureDataFactoryRun 和 Save-AzureDataFactoryLog Cmdlet 查看正要產生配量之執行的相關資訊。

### 範例

    Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-05-20T10:00:00

這個命令會取得 Azure Data Factory ADFTutorialDataFactory 中於 2014-05-20T10:00:00 (GMT) 之後產生的資料表 EmpSQLTable 的所有配量。以您執行 Set-AzureDataFactoryPipelineActivePeriod 時所指定的開始日期時間取代日期時間。

## <a name="get-azuredatafactoryrun"></a> Get-AzureDataFactoryRun

Get-AzureDataFactoryRun Cmdlet 會取得 Azure Data Factory 中資料表的資料配量的所有執行。  Azure Data Factory 中的資料表是由時間軸上方的配量組成。配量的寬度取決於排程 - 每小時/每日。執行是配量的處理單位。如果您重新嘗試，或是在失敗時重新執行配量，可能執行一次或多次配量。配量是由其開始時間識別。因此，若為 Get-AzureDataFactoryRun Cmdlet，您需要從 Get-AzureDataFactorySlice Cmdlet 的結果傳入配量的開始時間。

例如，若要執行下列配量，請使用 2015-04-02T20:00:00。 

    ResourceGroupName  	: ADFTutorialResourceGroup
    DataFactoryName 	: ADFTutorialDataFactory
    TableName 			: EmpSQLTable
    Start 				: 5/2/2014 8:00:00 PM
    End 				: 5/3/2014 8:00:00 PM
    RetryCount 			: 0
    Status 				: Ready
    LatencyStatus 		:



### 範例

    Get-AzureDataFactoryRun -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -ResourceGroupName ADFTutorialResourceGroup -StartDateTime 2014-05-21T16:00:00

這個命令會取得 Azure Data Factory ADFTutorialDataFactory 中資料表 EmpSQLTable 的所有配量執行，從 05/21/2014 的 4 PM GMT 開始。

## <a name="save-azuredatafactorylog"></a> Save-AzureDataFactoryLog
Save-AzureDataFactoryLog Cmdlet 會將與處理 Pig 或 Hive 專案的 Azure HDInsight 相關聯的記錄檔或自訂活動的記錄檔下載至本機硬碟。首先執行 Get-AzureDataFactoryRun Cmdlet，以取得資料配量的活動執行識別碼，然後使用該識別碼從與 HDInsight 叢集相關聯的二進位大型物件 (BLOB) 儲存體擷取記錄檔。 

如果您未指定 **-DownloadLogs** 參數，則 Cmdlet 只會傳回記錄檔的位置。 

如果您指定 **-DownloadLogs** 參數，而未指定輸出目錄 (**-Output **參數)，則記錄檔會下載至預設 **Documents** 資料夾。 

如果您指定 **-DownloadLogs** 參數以及輸出目錄 (**-Output **參數)，則記錄檔會下載至指定的資料夾。 


### 範例 1
這個命令會儲存識別碼為 841b77c9-d56c-48d1-99a3-8c16c3e77d39 之活動執行的記錄檔，其中活動屬於資源群組 (名為 ADF) 中 Data Factory (名為 LogProcessingFactory) 內的管線。記錄檔會儲存至 C:\Test 資料夾。 

	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
 

### 範例 2
這個命令會將記錄檔儲存至 Documents 資料夾 (預設值)。


	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs
 

### 範例 3
這個命令會傳回記錄檔的位置。請注意，並未指定 - DownloadLogs 參數。 
  
	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39"
 



## <a name="get-azuredatafactorygateway"></a> Get-AzureDataFactoryGateway
The Get-AzureDataFactoryGateway Cmdlet 會取得 Azure Data Factory 中特定閘道或所有閘道的相關資訊。您必須在內部部署電腦上安裝閘道，才能將內部部署 SQL Server 新增至 Data Factory 做為連結服務。

### 範例 1
    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
這個命令會傳回 Azure Data Factory ADFTutorialDataFactory 中所有閘道的相關資訊。

### 範例 2

    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialGateway

這個命令會傳回 Azure Data Factory ADFTutorialDataFactory 中閘道 ADFTutorialGateway 的相關資訊。

## <a name="set-azuredatafactorypipelineactiveperiod"></a> Set-AzureDataFactoryPipelineActivePeriod
這個 cmdlet 會設定由管線處理之資料配量的作用中期間。如果您使用 Set-AzureDataFactorySliceStatus，請確定配量開始日期和結束日期是在管線的作用中期間內。

一旦建立線後，您就可以指定將發生資料處理的持續時間。藉由指定管線的作用期間，依據對每個 ADF 資料表所定義之可用性屬性，您會定義將處理資料配量的持續時間。

### 範例

    Set-AzureDataFactoryPipelineActivePeriod  -Name ADFTutorialPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-22T16:00:00

這個命令會對管線 ADFTutoiralPipeline 所處理的資料配量設定作用中期間，從 5/21/2014 4 PM GMT 到 5/22/2014 4 PM GMT。

## <a name="set-azuredatafactoryslicestatus"></a> Set-AzureDataFactorySliceStatus
設資料表配量的狀態。配量開始日期和結束日期必須在管線的作用中期間內。

### 支援的狀態值
資料表的每個資料配量會經過不同階段。根據是否指定驗證原則，這些階段會稍有不同。


- 如果未指定驗證原則：  PendingExecution -> InProgress -> Ready
- 如果已指定驗證原則：PendingExecution -> Pending Validation -> InProgress -> Ready

下表提供配量可能狀態的描述，並告訴您是否可以使用 Set-AzureDataFactorySliceStatus 設定狀態。

<table border="1">	
	<tr>
		<th>Status</th>
		<th>描述</th>
		<th>可以使用 cmdlet 這樣設定嗎 ></th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>資料處理尚未啟動。</td>
		<td>Y</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>資料處理進行中。</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>資料處理已完成，而且資料配量就緒。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>產生配量的執行無法執行。</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>略過配量處理。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>重試產生配量的執行。</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>資料處理逾時。</td>
		<td>N</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>在開始處理之前，資料配量正在等候依照驗證原則進行驗證。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>重試配量的驗證。</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>配量的驗證失敗。</td>
		<td>N</td>
	</tr>
	</table>


### 支援的值 - 更新類型
對於 Azure Data Factory 中每個資料表，當設定配量的狀態時，您需要指定狀態更新是否只套用至資料表，或狀態更新是否傳播到所有受影響的配量。

<table border="1">	
	<tr>
		<th>更新類型</th>
		<th>描述</th>
		<th>可以使用 cmdlet 這樣設定嗎</th>
	</tr>

	<tr>
		<td>Individual</td>
		<td>在指定的時間範圍內指定資料表的每個配量的狀態</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>UpstreamInPipeline</td>
		<td>設定資料表和所有相依 (上游) 資料表的每個配量的狀態，而這些相依資料表會做為管線中活動的輸入資料表。</td>
		<td>Y</td>
	</tr>

</table>
## <a name="suspend-azuredatafactorypipeline"></a> Suspend-AzureDataFactoryPipeline
Suspend-AzureDataFactoryPipeline Cmdlet 會暫停 Azure Data Factory 中指定的管線。稍後，您可以使用 Resume-AzureDataFactoryPipeline Cmdlet 來繼續使用管線。

### 範例

    Suspend-AzureDataFactoryPipeline -Name ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

這個命令會暫停 Azure Data Factory ADFTutorialDataFactory 中的管線 ADFTutorialPipeline。

## <a name="resume-azuredatafactorypipeline"></a> Resume-AzureDataFactoryPipeline
Resume-AzureDataFactoryPipeline Cmdlet 會繼續使用 Azure Data Factory 中目前處於暫停狀態的指定管線。 

### 範例

    Resume-AzureDataFactoryPipeline ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

這個命令會繼續使用 Azure Data Factory ADFTutorialDataFactory 中之前使用 Suspend-AzureDataFactoryPipeline 命令暫停的管線 ADFTutorialPipeline。

## 另請參閱

文章 | 描述
------ | ---------------
[使用 Azure 預覽入口網站監視和管理 Azure Data Factory][monitor-manage-using-portal] | 本文描述如何使用 Azure 預覽入口網站監視和管理 Azure Data Factory。
[啟用您的管線以使用內部部署資料][use-onpremises-datasources] | 本文逐步解說如何將資料從內部部署 SQL Server Database複製到 Azure blob。
[搭配 Data Factory 使用 Pig 和 Hive][use-pig-and-hive-with-data-factory] | 本文逐步解說如何使用 HDInsight 活動以執行 hive/pig 指令碼處理輸入資料並產生輸出資料。 
[教學課程：使用 Data Factory 來移動並處理記錄檔][adf-tutorial] | 本文提供端對端逐步解說，展示如何使用 Azure Data Factory 實作近乎真實世界的案例，將記錄檔中的資料轉換為見解。
[在 Data Factory 中使用自訂活動][use-custom-activities] | 本文章提供的逐步解說具有建立自訂活動並將它用於管線中的逐步指示。 
[對 Data Factory 問題進行疑難排解][troubleshoot] | 本文描述如何對 Azure Data Factory 問題進行疑難排解。
[Azure Data Factory 開發人員參考][developer-reference] | 「開發人員參考」具有 Cmdlet、JSON 指令碼、函數等等的完整參考內容。 
[Azure Data Factory Cmdlet 參考][cmdlet-reference] | 本參考內容詳述所有 **Data Factory Cmdlet**。

[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-portal]: ../data-factory-monitor-manage-using-management-portal

[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
