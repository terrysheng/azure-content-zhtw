<properties title="Use Pig and Hive with Azure Data Factory" pageTitle="使用 Pig 和 Hive 搭配 Azure Data Factory" description="了解如何從 Azure Data Factory 在 Azure HDInsight 叢集執行 Pig 和 Hive 指令碼來處理資料。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# 使用 Pig 和 Hive 搭配 Data Factory
Azure Data Factory 中的管線會使用連結的計算服務，處理連結的儲存體服務中的資料。它包含一系列活動，其中每個活動都會執行  特定的處理作業。例如，複製活動會將資料從來源儲存體複製到目的地儲存體，而進行 Hive/Pig 轉換的 HDInsight 活動則會使用 Azure HDInsight 叢集，利用 Hive/Pig 指令碼來處理資料。HDInsight 活動可以使用一或多個輸入，並產生一或多個輸出。 

## 本文內容

小節 | 描述
------- | -----------
[Pig JSON 範例](#PigJSON) | 本節提供 JSON 結構描述，用於定義一個使用 Pig 轉換的 HDInsight 活動。 
[Hive JSON 範例](#HiveJSON) | 本節提供 JSON 結構描述，用於定義一個使用 Hive 轉換的 HDInsight 活動。 
[使用儲存在 Azure Blob 儲存體的 Pig 和 Hive 指令碼](#ScriptInBlob) | 說明如何從 HDInsight 活動使用 Pig/Hive 轉換，來參考 Azure Blob 儲存體中儲存的 Pig/Hive 指令碼。
[參數化的 Pig 和 Hive 查詢](#ParameterizeQueries) | 說明如何為 Pig 和 Hive 指令碼中使用的參數指定值，方法為在 JSON 中使用 **extendedProperties** 屬性。
[逐步介紹：使用 Hive 搭配 Azure Data Factory](#Waltkthrough) | 提供逐步指示，建立一個使用 Hive 來處理資料的管線。  



在管線 JSON 中定義 Pig 或 Hive 活動時，**type** 屬性應該設定為：**HDInsightActivity**。

## <a name="PigJSON"></a> Pig JSON 範例

    {
		"name": "Pig Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Pig",
			"script": "pig script",
			"extendedProperties":
			{	
				"param1": "param1Value"
 			}
		}
	}

**請注意：**
	
- Activity **類型**設定為 **HDInsightActivity**。
- **linkedServiceName** 設定為 **MyHDInsightLinkedService**。 
- 轉換**的**類型**設定**為 **Pig**。
- 您可以為 **script** 屬性指定 Pig 內嵌指令碼，或將指令碼檔案儲存在 Azure Blob 儲存體，然後使用 **scriptPath** 屬性來參考該檔，本文後面會加以說明。 
- 您可以使用 **extendedProperties** 指定 Pig 指令碼的參數。本文後面提供更多的詳細資料。 


## <a name="HiveJSON"></a> ## Hive JSON 範例


    {
		"name": "Hive Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Hive",
			"script": "Hive script",
			"extendedProperties":
			{	
				"param1": "param1Value"
            }
		}
	}

**請注意：**
	
- Activity **類型**設定為 **HDInsightActivity**。
- **linkedServiceName** 設定為 **MyHDInsightLinkedService**。 
- 轉換**的**類型**設定**為 **Hive**。
- 您可以為 **script** 屬性指定 Hive 內嵌指令碼，或將指令碼檔案儲存在 Azure Blob 儲存體，然後使用 **scriptPath** 屬性來參考該檔，本文後面會加以說明。 
- 您可以使用 **extendedProperties** 指定 Hive 指令碼的參數。本文後面提供更多的詳細資料。 

> [WACOM.NOTE] 如需 cmdlet、JSON 結構描述和結構描述中的屬性的詳細資訊，請參閱[開發人員參考](http://go.microsoft.com/fwlink/?LinkId=516908)。 


## <a name="ScriptInBlob"></a>使用儲存在 Azure Blob 儲存體的 Pig 和 Hive 指令碼
您可以將 Pig/Hive 指令碼儲存在與 HDInsight 叢集相關聯的 Azure Blob 儲存體中，並在 JSON 中使用下列屬性，從 Pig/Hive 活動參考它們： 

* **scriptPath** - Pig 或 Hive 指令碼檔案的路徑
* **scriptLinkedService** - 包含指令碼檔案的 Azure 儲存體帳戶

範例管線的下列 JSON 範例會使用一個參考 **transformdata.hql** 檔案的 Hive 活動，而該檔案儲存於 **adfwalkthrough** 容器的 **scripts** 資料夾中，此資料夾位於 **StorageLinkedService** 所代表的 Azure Blob 儲存體中。

    {
    	"name": "AnalyzeMarketingCampaignPipeline",
    	"properties":
    	{
	        "description" : " Enriched Gamer Fact Data and push to SQL Azure",
    	    "activities":
    	    [
    	        {
					"name": "JoinData",
					"description": "Join Regional Campaign data with Enriched Gamer Fact Data",
					"type": "HDInsightActivity",
					"inputs": [ {"name": "EnrichedGameEventsTable"}, 
                            {"name": "RefMarketingCampaignTable"} ],
					"outputs": [ {"name": "MarketingCampaignEffectivenessBlobTable"} ],
					"linkedServiceName": "MyHDInsightLinkedService",
					"transformation":
					{
    					"type": "Hive",
    					"scriptpath": "adfwalkthrough\\scripts\\transformdata.hql",    		
						"scriptLinkedService": "StorageLinkedService", 
						"extendedProperties":
						{
						}		
					},
					"policy":
					{
						"concurrency": 1,
						"executionPriorityOrder": "NewestFirst",
						"retry": 1,
						"timeout": "01:00:00"
					}
            	}
        	]
      	}
	}

  

> [WACOM.NOTE] 如需 cmdlet、JSON 結構描述和結構描述中的屬性的詳細資訊，請參閱[開發人員參考](http://go.microsoft.com/fwlink/?LinkId=516908)。

## <a name="ParameterizeQueries"></a>參數化的 Pig 和 Hive 查詢
Data Factory Pig 和活動可讓您使用 **extendedProperties**，為 Pig 和 Hive 指令碼中使用的參數指定值。ExtendedProperties 區段包含參數的名稱和參數的值。

請參閱下列範例，了解如何使用 **extendedProperties** 為 Hive 指令碼指定參數。若要使用參數化的 Hive  指令碼，請執行下列動作：

1.	定義 **extendedProperties** 中的參數。
2.	在內嵌 Hive 指令碼 (或) Blog 儲存體中儲存的 Hive 指令碼檔案中，請使用 **${hiveconf:parameterName}** 參考參數。

   
    		
    	{
			"name": "ParameterizedHivePipeline",
			"properties": 
			{
	    		"description" : "Example - Parameterized Hive Pipeline",
		   	 "activities": 
				[
					{
						"name": "ProcessLog",
					  	"type": "HDInsightActivity",
					  	"inputs": [{"Name": "DA_Input"}],
						"outputs": [{"Name": "DA_Output1"}, {"Name": "DA_Output2"}],
				  		"linkedServiceName": "MyHDInsightLinkedService",
				  		"transformation":
				  		{
							"type": "Hive", 
							"extendedProperties":
							{
								"Param1": "$$Text.Format('{0:yyyy-MM-dd}', SliceStart)",
								"Param2": "value"
						  	},
    						"script": "ADD FILE ${hiveconf:Param1}://${hiveconf:Param2}/MyFile.DLL;"
    					}
					}
			   	]
			}
		}


-  

## <a name="Walkthrough"></a>逐步介紹：使用 Hive 搭配 Azure Data Factory
### 必要條件
1. 完成[開始使用 Azure Data Factory][adfgetstarted] 文章中的教學課程。
2. 將您在上述教學課程中建立的 **emp.txt** 檔案，以 **hiveinput\emp.txt** 上傳至 Blob 儲存體中的 adftutorial 容器。當您利用這個語法上傳 emp.txt 檔案時，**hiveinput** 資料夾會自動建立在 **adftutorial** 容器中。 
2. 利用下列內容，在 **C:\ADFGetStarted** 下名為 **Hive** 的子資料夾中檔案中建立 **hivequery.hql** 檔案。
    		
    	DROP TABLE IF EXISTS adftutorialhivetable; 
		CREATE EXTERNAL TABLE  adftutorialhivetable
		(                                  
 			country         string,                                   
 			state           string,   
 			sessioncount int                                 
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RESULTOUTPUT}/${hiveconf:Year}/${hiveconf:Month}/${hiveconf:Day}'; 

		INSERT OVERWRITE TABLE adftutorialhivetable 
		SELECT  country, state, count(*) 
		FROM hivesampletable 
		group by country, state;
		
3.  將 **hivequery.hql** 上傳至 Blob 儲存體中的 **adftutorial** 容器


### 逐步介紹

#### 建立輸入資料表
1. 利用下列內容，在 **C:\ADFGetStarted\Hive** 資料夾中建立名為 **HiveInputBlobTable.json** 的 JSON 檔案。
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1,
            		"waitonexternal": {}
        		}
    		}
		}

 
	**請注意：**
	
	- 位置**類型**設定為 **AzureBlobLocation**。
	- **linkedServiceName** 設定為定義 Azure 儲存體帳戶的 **MyBlobStore**。
	- **folderPath** 指定輸入資料的 Blob 容器\資料夾。 
	- **frequency=Day** 和 **interval=1** 表示配量每天可供使用
	- **waitOnExternal** 表示這項資料不是由另一個管線產生，而是在 Data Factory 外部產生。 
	

	如需 JSON 內容的描述，請參閱 [Data Factory 開發人員參考][developer-reference]。  

2. 啟動 **Azure PowerShell**，並視需要切換至 **AzureResourceManager** 模式。
    		
    	Switch-AzureMode AzureResourceManager

5. 切換至資料夾：**C:\ADFGetStarted\Hive**。
6. 執行下列命令，以在 **ADFTutorialDataFactory** 中建立輸入資料表。

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveInputBlobTable.json

	如需 Data Factory Cmdlet 的詳細概觀，請參閱 [Data Factory Cmdlet 參考][cmdlet-reference]。 
#### 建立輸出資料表
        
1. 利用下列內容，建立名為 **HiveInputBlobTable.json** 的 JSON 檔案，並儲存在 **C:\ADFGetStarted\Hive** 資料夾中。

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. 執行下列命令，以在 **ADFTutorialDataFactory** 中建立輸出資料表。
 
		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveOutputBlobTable.json

### 建立 HDInsight 叢集的連結服務
Azure Data Factory 服務支援建立隨選叢集，並使用它處理輸入來產生輸出資料。您也可以使用自己的叢集執行相同作業。當您使用隨選 HDInsight 叢集時，系統會為每個配量建立叢集。然而，當您使用自己的 HDInsight 叢集時，叢集就可以立即處理配量。因此，在使用隨選叢集時，可能無法像使用自己的叢集那麼快看到輸出資料。基於範例的目的，讓我們使用隨選叢集。 

#### 若要使用隨選 HDInsight 叢集
1. 利用下列內容，建立名為 **HDInsightOnDemandCluster.json** 的 JSON 檔案，並儲存至 **C:\ADFGetStarted\Hive** 資料夾。


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": "4",
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. 啟動 **Azure PowerShell**，並執行下列命令名，來切換 **AzureResourceManager** 模式。Azure Data Factory Cmdlets 可在 **AzureResourceManager** 模式中使用。

         switch-azuremode AzureResourceManager
		

3. 切換至 **C:\ADFGetstarted\Hive** 資料夾。
4. 執行下列命令，為隨選 HDInsight 叢集建立連結的服務。
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
3. 您應該在 **Azure 預覽入口網站** 的 **Data Factory** 分頁上看到資料表和連結的服務。    
   
#### 若要使用您自己的 HDInsight 叢集： 

1. 利用下列內容，建立名為 **MyHDInsightCluster.json** 的 JSON 檔案，並儲存至 **C:\ADFGetStarted\Hive** 資料夾。先以適當的值取代叢集名稱、使用者名稱和密碼，再儲存 JSON 檔案。  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. 啟動 **Azure PowerShell**，並執行下列命令名，來切換 **AzureResourceManager** 模式。The Azure Data Factory Cmdlets 可在 **AzureResourceManager** 模式中使用。

         switch-azuremode AzureResourceManager
		

3. 切換至 **C:\ADFGetstarted\Hive** 資料夾。
4. 執行下列命令，為您自己的 HDInsight 叢集建立連結的服務。
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json

### 建立和排程管線
   
1. 利用下列內容，建立名為 **ADFTutorialHivePipeline.json** 的 JSON 檔案，並儲存在 **C:\ADFGetStarted\Hive** 資料夾中。如果您想要使用自己的叢集，並已遵循步驟來建立 **MyHDInsightCluster** 連結的服務，請在下列 JSON 中，以 **MyHDInsightCluster** 取代 **HDInsightOnDemandCluster**。 


    	{
    		"name": "ADFTutorialHivePipeline",
    		"properties":
    		{
        		"description" : "It runs a HiveQL query and stores the result set in a blob",
        		"activities":
        		[
            		{
						"name": "RunHiveQuery",
						"description": "Runs a hive query",
						"type": "HDInsightActivity",
						"inputs": [{"name": "HiveInputBlobTable"}],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightOnDemandCluster",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@spestore.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\\hivequery.hql",
						    "scriptLinkedService": "MyBlobStore"
						},
						"policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"retry": 1,
							"timeout": "01:00:00"
						}
            		}
        		]
      		}
		}

2. 執行下列命令來建立管線。
    	
		New-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\ADFTutorialHivePipeline.json
    	
3. 排程管線。
    	
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-27 -EndDateTime 2014-09-30 -Name ADFTutorialHivePipeline 

	> [WACOM.NOTE] 以目前日期的前三天取代 **StartDateTime** 值，並以目前日期取代 **EndDateTime** 值。StartDateTime 和 EndDateTime 都是 UTC 時間，且必須採用 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。 
	> 如果您未指定 **EndDateTime**，系統會計算為 "**StartDateTime + 48 小時**"。若要無限期地執行管線， 請將 **EndDateTime** 指定為 **9/9/9999**。
  	
	將輸出資料表排定為每天產生，因此將產生三個配量。 

4. 請參閱[開始使用 Data Factory][adfgetstarted] 文章的[監視資料集和管線][adfgetstartedmonitoring]一節。   

## 另請參閱

文章 | 描述
------ | ---------------
[簡介 Azure Data Factory][data-factory-introduction] | 本文簡介 Azure Data Factory 服務、概念、其提供的值，以及其支援的案例。
[開始使用 Azure Data Factory][adf-getstarted] | 本文提供端對端教學課程，展示如何建立範例 Azure Data Factory，將資料從 Azure Blob 複製至 Azure SQL 資料庫。
[啟用您的管線以使用內部部署資料][use-onpremises-datasources] | 本文逐步解說如何將資料從內部部署 SQL Server Database複製到 Azure blob。
[教學課程：使用 Data Factory 來移動並處理記錄檔][adf-tutorial] | 本文提供端對端逐步解說，展示如何使用 Azure Data Factory 實作近乎真實世界的案例，將記錄檔中的資料轉換為見解。
[在 Data Factory 中使用自訂活動][use-custom-activities] | 本文章提供的逐步解說具有建立自訂活動並將它用於管線中的逐步指示。 
[對 Data Factory 問題進行疑難排解][troubleshoot] | 本文描述如何對 Azure Data Factory 問題進行疑難排解。  
[Azure Data Factory 開發人員參考][developer-reference] | 「開發人員參考」具有 Cmdlet、JSON 指令碼、函數等等的完整參考內容。 


[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfgetstartedmonitoring]:../data-factory-get-started#MonitorDataSetsAndPipeline 
[adftutorial]: ../data-factory-tutorial

[開發人員參考]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure 入口網站]: http://portal.azure.com
