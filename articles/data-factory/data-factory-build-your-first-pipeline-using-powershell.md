<properties
	pageTitle="開始使用 Azure Data Factory (Azure PowerShell)"
	description="在本教學課程中，您將使用 Azure PowerShell，建立範例 Azure Data Factory 管線。"
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
	ms.topic="hero-article"
	ms.date="12/18/2015"
	ms.author="spelluru"/>

# 開始使用 Azure Data Factory (Azure PowerShell)
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


在本文中，您將了解如何使用 Azure PowerShell 來建立您的第一個 Azure Data Factory。


## 先決條件
除了「教學課程概觀」主題中所列的必要條件，您還需要安裝下列項目：

- 您**必須**詳讀[教學課程概觀](data-factory-build-your-first-pipeline.md)一文，並完成必要的步驟，再進一步繼續。
- **Azure PowerShell** (英文)。按照[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 一文中的指示，在您的電腦上安裝最新版的 Azure PowerShell。
- (選用) 這篇文章並未涵蓋所有的 Data Factory Cmdlet。如需 Data Factory Cmdlet 的完整文件，請參閱 [Data Factory Cmdlet 參考](https://msdn.microsoft.com/library/dn820234.aspx)。 

若您使用 **1.0 版本之前**的 Azure PowerShell，您必須使用[這裡][cmdlet-reference]所記載的 Cmdlet。您也必須在使用 Data Factory Cmdlet 之前，先執行下列命令：
 
1. 開啟 Azure PowerShell 並執行下列命令。將 Azure PowerShell 維持在開啟狀態，直到本教學課程結束為止。如果您關閉並重新開啟，則需要再次執行這些命令。
	1. 執行 **Add-AzureAccount**，並輸入您用來登入 Azure 入口網站的使用者名稱和密碼。
	2. 執行 **Get-AzureSubscription** 以檢視此帳戶的所有訂用帳戶。
	3. 執行 **Select-AzureSubscription** 以選取您想要使用的訂用帳戶。此訂用帳戶應該與您在 Azure 入口網站中使用的相同。
4. 切換至 AzureResourceManager 模式，因為 Azure Data Factory Cmdlet 可在此模式中使用：**Switch-AzureMode AzureResourceManager**。


## 步驟 1：建立 Data Factory

在此步驟中，您會使用 Azure PowerShell 建立名為 **FirstDataFactoryPSH** 的 Azure Data Factory。資料處理站可以有一或多個管線。其中的管線可以有一或多個活動。例如，「複製活動」會從來源複製資料到目的地資料存放區，HDInsight Hive 活動則是執行 Hive 指令碼轉換輸入資料以產生輸出資料。讓我們在這個步驟中開始建立 Data Factory。

1. 開啟 Azure PowerShell 並執行下列命令。將 Azure PowerShell 維持在開啟狀態，直到本教學課程結束為止。如果您關閉並重新開啟，則需要再次執行這些命令。
	- 執行 **Login-AzureRmAccount**，並輸入您用來登入 Azure 入口網站的使用者名稱和密碼。  
	- 執行 **Get-AzureSubscription** 以檢視此帳戶的所有訂用帳戶。
	- 執行 **Select-AzureSubscription <Name of the subscription>** 以選取您想要使用的訂用帳戶。此訂用帳戶應該與您在 Azure 入口網站中使用的相同。
3. 執行以下命令，建立名為 **ADFTutorialResourceGroup** 的 Azure 資源群組。

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	本教學課程的某些步驟假設您使用名為 ADFTutorialResourceGroup 的資源群組。如果使用不同的資源群組，您必須以該群組取代本教學課程中的 ADFTutorialResourceGroup。
4. 執行 **New-AzureRmDataFactory** Cmdlet，建立名為 **FirstDataFactoryPSH** 的 Data Factory。  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"

	> [AZURE.IMPORTANT]Azure Data Factory 的名稱在全域必須是唯一的。如果您收到錯誤：「Data Factory 名稱 “FirstDataFactoryPSH” 無法使用」，請變更名稱 (例如 yournameFirstDataFactoryPSH)。執行本教學課程中的步驟時，請使用此名稱來取代 ADFTutorialFactoryPSH。請參閱 [Data Factory - 命名規則](data-factory-naming-rules.md)主題，以了解 Data Factory 成品的命名規則。
	> 
	> Data Factory 的名稱未來可能會註冊為 DNS 名稱，因此會變成公開可見的名稱。

建立管線之前，您必須先建立一些 Data Factory 項目。首先，您要先建立連結的服務，以便將資料存放區/電腦連結到您的資料存放區；並定義輸入和輸出資料集，表示資料位於連結的資料存放區中，再建立具有使用這些資料集的活動之管線。

## 步驟 2：建立連結服務 
在此步驟中，您將您的 Azure 儲存體帳戶和隨選 Azure HDInsight 叢集連結到您的 Data Factory。Azure 儲存體帳戶會保留此範例中管線的輸入和輸出資料。HDInsight 連結服務會用來執行此範例中管線活動指定的 Hive 指令碼。您必須識別案例中使用的資料存放區/計算服務，並建立連結的服務將這些服務連結到 Data Factory。

### 建立 Azure 儲存體連結服務
在此步驟中，您將您的 Azure 儲存體帳戶連結到您的 Data Factory。在本教學課程中，您會使用相同的 Azure 儲存體帳戶來存放輸入/輸出資料及 HQL 指令碼檔案。

1. 在 C:\\ADFGetStarted 資料夾中，使用以下內容建立名為 StorageLinkedService.json 的 JSON 檔案。如果不存在，請建立 ADFGetStarted 資料夾。

		{
	    	"name": "StorageLinkedService",
	    	"properties": {
	        	"type": "AzureStorage",
	        	"description": "",
	        	"typeProperties": {
	            	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        	}
	    	}
		}

	使用您的 Azure 儲存體帳戶名稱取代帳戶名稱，並使用 Azure 儲存體帳戶的存取金鑰取代帳戶金鑰。若要了解如何取得儲存體存取金鑰，請參閱[檢視、複製和重新產生儲存體存取金鑰](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys)。

2. 在 Azure PowerShell 中，切換到 ADFGetStarted 資料夾。
3. 您可以使用 **New-AzureRmDataFactoryLinkedService** Cmdlet 建立連結服務。此 Cmdlet 和您在本教學課程中使用的其他 Data Factory Cmdlet，皆需要您將值傳給 *ResourceGroupName* 和 *DataFactoryName* 參數。或者，您可以使用 Get-AzureRmDataFactory 取得 DataFactory 物件，並傳遞此物件，就不需要在每次執行 Cmdlet 時輸入 ResourceGroupName 和 DataFactoryName。執行以下命令，將 **Get-AzureRmDataFactory** Cmdlet 的輸出指派給 **$df** 變數。

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

4. 現在，執行 **New-AzureRmDataFactoryLinkedService** Cmdlet 以建立連結的 **StorageLinkedService** 服務。

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	如果您還沒執行 **Get-AzureRmDataFactory** Cmdlet 和指派輸出給 **$df** 變數，您就必須指定 ResourceGroupName 和 DataFactoryName 參數的值，如下所示。

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json

	如果您在教學課程途中關閉 Azure PowerShell，則下次啟動 Azure PowerShell 時必須執行 **Get-AzureRmDataFactory** Cmdlet，才能完成教學課程。

### 建立 Azure HDInsight 連結服務
在此步驟中，您會將隨選 HDInsight 叢集與您的 Data Factory 連結。HDInsight 叢集會在執行階段自動建立，並在處理完成之後刪除，且會閒置一段時間。您可以使用自己的 HDInsight 叢集，不必使用隨選的 HDInsight 叢集。請參閱[計算連結服務](data-factory-compute-linked-services.md)以取得詳細資料。

1. 在 **C:\\ADFGetStarted** 資料夾中，使用以下內容建立名為 **HDInsightOnDemandLinkedService**.json 的 JSON 檔案。

		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}

	下表提供程式碼片段中所使用之 JSON 屬性的描述：

	| 屬性 | 說明 |
	| :------- | :---------- |
	| 版本 | 指定所建立的 HDInsight 版本為 3.2。 | 
	| ClusterSize | 建立一個單一節點的 HDInsight 叢集。 | 
	| TimeToLive | 指定 HDInsight 叢集在被刪除之前的閒置時間。 |
	| linkedServiceName | 指定將用來儲存 HDInsight 產生之記錄檔的儲存體帳戶 |

2. 執行 **New-AzureRmDataFactoryLinkedService** Cmdlet 建立名為 HDInsightOnDemandLinkedServiceHDInsightOnDemandLinkedService 的連結服務。

		New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


## 步驟 3：建立資料集
在此步驟中，您將建立資料集來代表 Hive 處理的輸入和輸出資料。這些資料集是您稍早在本教學課程中建立的 **StorageLinkedService**。連結的服務會指向 Azure 儲存體帳戶，而資料集則會指定保留輸入和輸出資料儲存體中的容器、資料夾和檔案名稱。

### 建立輸入資料集
1. 在 **C:\\ADFGetStarted** 資料夾中，使用下列內容建立名為 **InputTable.json** 的 JSON 檔案：

		{
			"name": "AzureBlobInput",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "input.log",
		            "folderPath": "adfgetstarted/inputdata",
		            "format": {
		                "type": "TextFormat",
		                "columnDelimiter": ","
		            }
		        },
		        "availability": {
		            "frequency": "Month",
		            "interval": 1
		        },
		        "external": true,
		        "policy": {}
		    }
		} 

	在上述範例中，您建立了名為 **AzureBlobOutput** 的資料集，並指定 JSON 程式碼片段；也建立了名為 **AzureBlobInput** 的資料集，表示管線中活動的輸入資料。此外，您指定將輸入資料放在名為 **adfgetstarted** 的 Blob 容器及名為 **inputdata** 的資料夾中。

	下表提供程式碼片段中所使用之 JSON 屬性的描述：

	| 屬性 | 說明 |
	| :------- | :---------- |
	| 類型 | 類型屬性設為 AzureBlob，因為資料位於 Azure Blob 儲存體。 |  
	| linkedServiceName | 表示您稍早建立的 StorageLinkedService。 |
	| fileName | 這是選用屬性。如果您省略此屬性，會挑選位於 folderPath 的所有檔案。在這種情況下，只會處理 input.log。 |
	| 類型 | 記錄檔為文字格式，因此我們會使用 TextFormat。 | 
	| columnDelimiter | 記錄檔案中的資料行會以 , (逗號) 分隔 |
	| frequency/interval | 頻率設為「每月」且間隔為 1，表示每個月都會可取得輸入配量。 | 
	| external | 如果輸入資料不是由 Data Factory 服務產生，此屬性會設為 true。 | 

2. 在 Azure PowerShell 中執行以下命令來建立 Data Factory 資料集。

		New-AzureRmDataFactoryDataset $df -File .\InputTable.json

### 建立輸出資料集
現在，您將建立輸出資料集來代表 Azure Blob 儲存體中儲存的輸出資料。

1. 在 **C:\\ADFGetStarted** 資料夾中，使用下列內容建立名為 **OutputTable.json** 的 JSON 檔案：

		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adfgetstarted/partitioneddata",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Month",
		      "interval": 1
		    }
		  }
		}

	在 JSON 程式碼片段中，建立名為 **AzureBlobOutput** 的資料集，並指定將由 Hive 指令碼產生的資料結構。此外，指定將結果儲存在名為 **adfgetstarted** 的 Blob 容器及名為 **partitioneddata** 的資料夾中。**availability** 區段指定每個月產生一次輸出資料集。

2. 在 Azure PowerShell 中執行以下命令來建立 Data Factory 資料集。

		New-AzureRmDataFactoryDataset $df -File .\OutputTable.json

## 步驟 3：建立您的第一個管線
在此步驟中，您將建立第一個具有 **HDInsightHive** 活動的管線。請注意，您每個月都可取得輸入配量資訊 (頻率：每月，間隔：1)，輸出配量則是每用產生，而活動的排程器屬性也設為每用 (如下所示)。輸出資料集設定及活動排程器必須相符。這時，輸出資料集會影響排程，因此即使活動並未產生任何輸出，您都必須建立輸出資料集。如果活動沒有任何輸入，您可以略過建立輸入資料集。本節結尾會說明下列 JSON 中使用的屬性。


1. 在 C:\\ADFGetStarted 資料夾中，使用下列內容建立名為 MyFirstPipelinePSH.json 的 JSON 檔案：

	> [AZURE.IMPORTANT]在 JSON 中使用您的儲存體帳戶名稱取代 **storageaccountname**。
		
		{
		    "name": "MyFirstPipeline",
		    "properties": {
		        "description": "My first Azure Data Factory pipeline",
		        "activities": [
		            {
		                "type": "HDInsightHive",
		                "typeProperties": {
		                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
		                    "scriptLinkedService": "StorageLinkedService",
		                    "defines": {
		                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
		                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
		                    }
		                },
		                "inputs": [
		                    {
		                        "name": "AzureBlobInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "AzureBlobOutput"
		                    }
		                ],
		                "policy": {
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Month",
		                    "interval": 1
		                },
		                "name": "RunSampleHiveActivity",
		                "linkedServiceName": "HDInsightOnDemandLinkedService"
		            }
		        ],
		        "start": "2014-02-01T00:00:00Z",
		        "end": "2014-02-02T00:00:00Z",
		        "isPaused": false
		    }
		}

	您會在 JSON 程式碼片段中建立一個管線，其中包括在 HDInsight 叢集上使用 Hive 處理「資料」的單一活動。
	
	Hive 指令碼檔案 **partitionweblogs.hql** 儲存於 Azure 儲存體帳戶 (透過 scriptLinkedService 指定，名為 **StorageLinkedService**)，且位於 **adfgetstarted**容器的 **script** 資料夾中。

	**defines** 區段可用來指定執行階段設定，該設定將傳遞到 Hive 指令碼做為 Hive 組態值 (例如 ${hiveconf:inputtable}、${hiveconf:partitionedtable})。

	管線的 **start** 和 **end** 屬性會指定管線的作用中期間。

	在活動 JSON 中，您會指定 Hive 指令碼要在透過 **linkedServiceName** – **HDInsightOnDemandLinkedService** 指定的計算上執行。
2.  確認您在 Azure Blob 儲存體的 **adfgetstarted/inputdata** 資料夾中看到了**input.log** 檔案，並執行下列命令以部署管線。由於 **start** 和 **end** 時間設定在過去，且 **isPaused** 設為 false，管線 (管線中的活動) 會在部署之後立即執行。 

		New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. 恭喜，您已經成功使用 Azure PowerShell 建立您的第一個管線！

### <a name="MonitorDataSetsAndPipeline"></a>監視資料集和管線
在此步驟中，您將使用 Azure PowerShell 來監視 Azure Data Factory 的運作情形。

1. 執行 **Get-AzureRmDataFactory** 並指派輸出給 **$df** 變數。

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

2. 執行 **Get-AzureRmDataFactorySlice**，以取得 **EmpSQLTable** (管線的輸出資料表) 所有配量的詳細資料。

		Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	請注意，您在此處指定的 StartDateTime 與在管線 JSON 中指定的開始時間是相同的。您應該會看到如下所示的輸出。

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : FirstDataFactoryPSH
		DatasetName       : AzureBlobOutput
		Start             : 2/1/2014 12:00:00 AM
		End               : 3/1/2014 12:00:00 AM
		RetryCount        : 0
		State             : InProgress
		SubState          :
		LatencyStatus     :
		LongRetryCount    : 0


3. 執行 **Get-AzureRmDataFactoryRun**，以取得特定配量的活動執行詳細資料。

		Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

	您應該會看到如下所示的輸出。
		
		Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : FirstDataFactoryPSH
		DatasetName         : AzureBlobOutput
		ProcessingStartTime : 12/18/2015 4:50:33 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 2/1/2014 12:00:00 AM
		DataSliceEnd        : 3/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 12/18/2015 4:50:33 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	您可以繼續執行此 Cmdlet 直到您看到配量處於**就緒**狀態或**失敗**狀態。當配量處於就緒狀態時，檢查您 Blob 儲存體中 **adfgetstarted** 容器內 **partitioneddata** 資料夾的輸出資料。請注意，建立隨選 HDInsight 叢集通常需要一些時間。

	![輸出資料](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)
## 後續步驟
在本文中，您已經建立可在隨選 Azure HDInsight 叢集上執行 Hive 指令碼，含有轉換活動 (HDInsight 活動) 的管線。若要了解如何使用「複製活動」從 Azure Blob 將資料複製到 Azure SQL，請參閱[教學課程：從 Azure Blob 將資料複製到 Azure SQL](./data-factory-get-started.md)。

### 參考
| 主題 | 說明 |
| :---- | :---- |
| [Data Factory Cmdlet 參考](https://msdn.microsoft.com/library/azure/dn820234.aspx) | 請參閱 Data Factory Cmdlet 中的完整文件 |
| [管線](data-factory-create-pipelines.md) | 本文將協助您了解 Azure Data Factory 中的管線和活動，以及如何運用這些來為您的案例或業務建構端對端的資料導向工作流程。 |
| [資料集](data-factory-create-datasets.md) | 他的文章會協助您了解 Azure Data Factory 中的資料集。
| [排程和執行](data-factory-scheduling-and-execution.md) | 本文說明 Azure Data Factory 應用程式模型的排程和執行層面。 |
| [監視與管理管線](data-factory-monitor-manage-pipelines.md) | 本文描述如何監視、管理和偵錯您的管線。同時也會提供如何建立警示和取得失敗通知的詳細資訊。 |

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx

<!---HONumber=AcomDC_1223_2015-->