<properties 
	pageTitle="使用 Pig 和 Hive 搭配 Azure Data Factory" 
	description="了解如何從 Azure Data Factory 在 Azure HDInsight 叢集執行 Pig 和 Hive 指令碼來處理資料。" 
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

# 使用 Pig 和 Hive 搭配 Data Factory
Azure Data Factory 中的「管線」會使用連結的計算服務，來處理連結的儲存體服務中的資料。它包含一系列活動，其中每個活動都會執行特定的處理作業。本文說明在 Azure 資料 Factory 管線中的 Pig/Hive 轉換中使用 HDInsight 活動。請參閱 [叫用資料 Factory 的 MapReduce 程式][data-factory-map-reduce] 如需詳細資訊執行 MapReduce 程式在 HDInsight 上的叢集從一個 Azure 資料 factory 管線。

## 逐步解說： 使用 Hive 與 Azure 資料處理站
本逐步解說提供使用 HDInsight 活動使用 Hive 轉換資料 Factory 管線中的逐步指示。

### 必要條件
1. 完成本教學課程，從 [開始使用 Azure 資料 Factory][adfgetstarted] 發行項。
2. 上傳 **emp.txt** 上述的教學課程為您建立的檔案 **hiveinput\emp.txt** adftutorial 容器中 blob 儲存體。 **Hiveinput** 資料夾會自動建立在 **adftutorial** 時使用這個語法的 emp.txt 檔案上傳的容器。

	> [AZURE.NOTE]Emp.txt 檔案是空檔案對於此逐步解說。實際的輸入的資料來自 **hivesampletable** HDInsight 叢集上已經存在。管線並不使用 emp.txt 檔案。
	
2. 建立 **hivequery.hql** 檔案中一個名為子 **Hive** 下 **C:\ADFGetStarted** 含有下列內容。
    		
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

	> [AZURE.NOTE]若要使用 **Tez** 引擎來執行 Hive 查詢 HQL 檔案中，加入"* * 設定 hive.execution.engine=tez** ； 「 在檔案頂端。
		
3.  上傳 **hivequery.hql** 至 **adftutorial** 在 blob 儲存體容器


### 逐步介紹

#### 建立輸入資料表
1. 在 **資料 FACTORY** 刀鋒伺服器 **ADFTutorialDataFactory**, ，按一下 **作者和部署** 來啟動資料 Factory 編輯器。
	
	![資料處理站分頁][data-factory-blade]

2. 在 **資料 Factory 編輯器**, ，按一下 **新的資料集**, ，然後按一下 **Azure Blob 儲存體** 從命令列。
3. 在右窗格中的 JSON 指令碼取代成下列 JSON 指令碼：    
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1,
            		"waitonexternal": {}
        		}
    		}
		}

 
	**請注意下列事項：**
	
	- 位置 **類型** 設為 **AzureBlobLocation**。
	- **linkedServiceName** 設為 **StorageLinkedService** 定義 Azure 儲存體帳戶。
	- **folderPath** 指定輸入資料的 blob container\folder。 
	- **頻率 = 日** 和 **間隔 = 1** 表示配量每天可供使用
	- **waitOnExternal** 表示這項資料就不會產生另一個管線，它，而是從外部產生之資料處理站。 
	

	請參閱 [資料工廠開發人員參考][developer-reference] 如需 JSON 內容的描述。

2. 按一下 **部署** 命令列部署資料表上。
  
#### 建立輸出資料表
        
1. 在 **資料 Factory 編輯器**, ，按一下 **新的資料集**, ，然後按一下 **Azure Blob 儲存體** 從命令列。
2. 在右窗格中的 JSON 指令碼取代成下列 JSON 指令碼：

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. 按一下 **部署** 命令列部署資料表上。


### 建立 HDInsight 叢集的連結服務
Azure Data Factory 服務支援建立隨選叢集，並使用它處理輸入來產生輸出資料。您也可以使用自己的叢集執行相同作業。當您使用隨選 HDInsight 叢集時，系統會為每個配量建立叢集。然而，當您使用自己的 HDInsight 叢集時，叢集就可以立即處理配量。因此，在使用隨選叢集時，可能無法像使用自己的叢集那麼快看到輸出資料。基於範例的目的，讓我們使用隨選叢集。

#### 若要使用隨選 HDInsight 叢集
1. 按一下 **新計算** 從命令列，然後選取 **隨選 HDInsight 叢集** 從功能表。
2. 依下列方式在 JSON 指令碼中： 
	1. 針對 **clusterSize** 屬性，指定 HDInsight 叢集的大小。
	2. 針對 **jobsContainer** 屬性，指定將儲存的叢集記錄檔的預設容器的名稱。基於本教學課程的目的指定 **adfjobscontainer**。
	3. 針對 **timeToLive** 屬性，指定多久之前將會刪除該叢集可以處於閒置狀態。 
	4. 針對 **版本** 屬性，指定您想要使用的 HDInsight 版本。如果您排除此屬性時，會使用最新版本。  
	5. 針對 **linkedServiceName**, ，指定 **StorageLinkedService** 您必須建立在 Get 入門教學課程。 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. 按一下 **部署** 命令列上的部署連結的服務。
   
   
#### 若要使用您自己的 HDInsight 叢集： 

1. 按一下 **新計算** 從命令列，然後選取 **HDInsight 叢集** 從功能表。
2. 依下列方式在 JSON 指令碼中： 
	1. 針對 **clusterUri** 屬性中，輸入您的 HDInsight 的 URL。例如： https://<clustername>.azurehdinsight.net/     
	2. 針對 **UserName** 屬性中，輸入能夠存取 HDInsight 叢集的使用者名稱。
	3. 針對 **密碼** 屬性中，輸入使用者的密碼。 
	4. 針對 **LinkedServiceName** 屬性中，輸入 **StorageLinkedService**。這是您必須在入門教學課程中建立連結的服務。 

2. 按一下 **部署** 命令列上的部署連結的服務。

### 建立和排程管線
   
1. 按一下 **新管線** 命令列上。如果看不到命令，按一下 [ **...(省略符號)** 若要查看它。 
2. 在右窗格中的 JSON 取代成下列 JSON 指令碼。如果您想要使用您自己的叢集，並遵循步驟來建立 **HDInsightLinkedService** 連結服務，取代 **HDInsightOnDemandLinkedService** 與 **HDInsightLinkedService** 下列 JSON 中。 


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
						"linkedServiceName": "HDInsightLinkedService",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@<your storage account>.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\hivequery.hql",
						    "scriptLinkedService": "StorageLinkedService"
						},
						"policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"retry": 1,
							"timeout": "01:00:00"
						}
            		}
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false

      		}
		}

	> [AZURE.NOTE]取代 **StartDateTime** 值與目前日期之前三天和 **EndDateTime** 值與目前的日期。StartDateTime 和 EndDateTime 必須在 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如： 2014年-10-14T16:32:41Z。將輸出資料表排定為每天產生，因此將產生三個配量。
	
	> [AZURE.NOTE]取代 **儲存體帳戶** JSON 的儲存體帳戶名稱中。
	
	請參閱 [JSON 指令碼參考](http://go.microsoft.com/fwlink/?LinkId=516971) 如需詳細資訊 JSON 內容。
2. 按一下 **部署** 命令列來部署管線。
4. 請參閱 [監視資料集和管線][adfgetstartedmonitoring] 區段 [開始使用資料 Factory][adfgetstarted] 發行項。 

	> [AZURE.NOTE]在 **活動執行詳細資料** 輸出資料表的配量的分頁 (選取輸出資料表]-> [選取配量]-> [的選取活動入口網站中執行)，您會看到 HDInsight 叢集所建立的記錄檔的連結。您可以在入口網站本身中檢閱它們，或下載到電腦。
  

## Pig JSON 範例
在 JSON 中，管線中定義 Pig 或 Hive 活動時 **類型** 屬性必須設為： **HDInsightActivity**。

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

**請注意下列事項：**
	
- 活動 **類型** 設為 **HDInsightActivity**。
- **linkedServiceName** 設為 **MyHDInsightLinkedService**。建立連結的 HDInsight 服務，請參閱下面連結的 HDInsight 服務區段，如需詳細資訊。
-  **類型** 的 **轉換** 設為 **Pig**。
- 您可以指定為 Pig 指令碼內嵌 **指令碼** 屬性或儲存在 Azure 中的指令碼檔案的 blob 儲存體和檔案使用 **scriptPath** 屬性，在本文稍後說明。 
- 使用指定的 Pig 指令碼參數 **extendedProperties**。本文後面提供更多的詳細資料。 


## Hive JSON 範例


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

**請注意下列事項：**
	
- 活動 **類型** 設為 **HDInsightActivity**。
- **linkedServiceName** 設為 **MyHDInsightLinkedService**。 
-  **類型** 的 **轉換** 設為 **Hive**。
- 您可以指定為 Hive 指令碼內嵌 **指令碼** 屬性或儲存在 Azure 中的指令碼檔案的 blob 儲存體和檔案使用 **scriptPath** 屬性，在本文稍後說明。 
- 使用指定的 Hive 指令碼參數 **extendedProperties**。本文後面提供更多的詳細資料。 

> [AZURE.NOTE]請參閱 [開發人員參考](http://go.microsoft.com/fwlink/?LinkId=516908) 如需詳細資訊 cmdlet、 JSON 結構描述和結構描述中的屬性。


## 在 HDInsight 活動中使用 Pig 及 Hive 指令碼
您可以將 Pig/Hive 指令碼儲存在與 HDInsight 叢集相關聯的 Azure Blob 儲存體中，並在 JSON 中使用下列屬性，從 Pig/Hive 活動參考它們：

* **scriptPath** – Pig 或 Hive 指令碼檔案的路徑
* **scriptLinkedService** – 包含指令碼檔案的 Azure 儲存體帳戶

JSON 範例如下列範例管線使用指的是 Hive 活動 **transformdata.hql** 檔案儲存在 **指令碼** 資料夾中的 **adfwalkthrough** 中由 Azure blob 儲存體容器 **StorageLinkedService**。

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
    					"scriptpath": "adfwalkthrough\scripts\transformdata.hql",    		
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


> [AZURE.NOTE]若要使用 **Tez** 引擎來執行 Hive 查詢，請執行"* * 設定 hive.execution.engine=tez**;"之前執行 Hive 查詢。
> 
> 請參閱 [開發人員參考](http://go.microsoft.com/fwlink/?LinkId=516908) 如需詳細資訊 cmdlet、 JSON 結構描述和結構描述中的屬性。

## 參數化的 Pig 和 Hive 查詢
資料處理站 Pig 和 Hive 活動可讓您指定所使用的 Pig 和 Hive 指令碼中使用的參數值 **extendedProperties**。ExtendedProperties 區段包含參數的名稱和參數的值。

請參閱下列範例指定使用 Hive 指令碼參數 **extendedProperties**。若要使用參數化的 Hive 指令碼，請執行下列動作：

1.	定義中的參數 **extendedProperties**。
2.	在內嵌 Hive 指令碼 (或) 部落格儲存體中儲存的 Hive 指令碼檔案，請參閱參數使用 **${hiveconf:parameterName}**。

   
    		
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


## 另請參閱

文章 | 說明
------ | ---------------
[教學課程： 移動及處理序使用資料處理站的記錄檔][adf-tutorial] | 這篇文章提供端對端逐步解說，示範如何實作幾近的真實世界的案例中使用 Azure 資料 Factory 從記錄檔的資料轉換成見解。
[Azure 資料工廠開發人員參考資料][developer-reference] | 開發人員參考資料具有完整的參考內容 cmdlet、 JSON 指令碼、 函式等等... 

[data-factory-copy-activity]: ..//data-factory-copy-activity
[data-factory-map-reduce]: ..//data-factory-map-reduce

[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[data-factory-blade]: ./media/data-factory-pig-hive-activities/DataFactoryBlade.png


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!---HONumber=GIT-SubDir--> 