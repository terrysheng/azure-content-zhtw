<properties 
	pageTitle="Hive 活動" 
	description="了解如何使用 Azure 資料處理站中的 Hive 活動，以在隨選/您自己的 HDInsight 叢集上執行 Hive 查詢。" 
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
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# Hive 活動

Data Factory [管線](data-factory-create-pipelines.md)中的 HDInsight Hive 活動會在[您自己的](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或[隨選](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux 的 HDInsight 叢集上執行 Hive 查詢。這篇文章是根據〈[資料轉換活動](data-factory-data-transformation-activities.md)〉文章，它呈現資料轉換和支援的轉換活動的一般概觀。

## 語法

	{
		"name": "Hive Activity",
	    "description": "description",
	    "type": "HDInsightHive",
	    "inputs": [
	      {
	        "name": "input tables"
	      }
	    ],
	    "outputs": [
	      {
	        "name": "output tables"
	      }
	    ],
	    "linkedServiceName": "MyHDInsightLinkedService",
	    "typeProperties": {
	      "script": "Hive script",
	      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
	      "defines": {
	        "param1": "param1Value"
	      }
	    },
       "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
	}
	
## 語法詳細資料

屬性 | 說明 | 必要
-------- | ----------- | --------
名稱 | 活動的名稱 | 是
說明 | 說明活動用途的文字 | 否
類型 | HDinsightHive | 是
輸入 | Hive 活動所耗用的輸入 | 否
輸出 | Hive 活動所耗用的輸出 | 是 
linkedServiceName | 參考 HDInsight 叢集註冊為 Data Factory 中的連結服務 | 是 
script | 指定 Hive 指令碼內嵌 | 否
指令碼路徑 | 在 Azure Blob 儲存體中儲存 Hive 指令碼，並提供檔案的路徑。使用 'script' 或 'scriptPath' 屬性。兩者無法同時使用 | 否 
定義 | 在使用 'hiveconf' 的 Hive 指令碼內指定參數做為參考的金鑰/值組 | 否

## 範例

我們來看看遊戲記錄檔分析的範例，您想要識別使用者花多少時間在玩貴公司開發的遊戲。

以下是範例遊戲記錄檔，以逗號 (,) 分隔，並包含下列欄位 – ProfileID、SessionStart、Duration、SrcIPAddress 和 GameType。

	1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
	1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
	1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
	1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
	.....

要處理此資料的 **Hive 指令碼**看起來像這樣：

	DROP TABLE IF EXISTS HiveSampleIn; 
	CREATE EXTERNAL TABLE HiveSampleIn 
	(
		ProfileID		string, 
	    SessionStart 	string, 
	    Duration 		int, 
	    SrcIPAddress 	string, 
	    GameType 		string
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 
	
	DROP TABLE IF EXISTS HiveSampleOut; 
	CREATE EXTERNAL TABLE HiveSampleOut 
	(	
		ProfileID 	string, 
	    Duration 	int
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';
	
	INSERT OVERWRITE TABLE HiveSampleOut
	Select 
		ProfileID,
		SUM(Duration)
	FROM HiveSampleIn Group by ProfileID

若要在 Data Factory 管線中執行此 Hive 指令碼，您需要執行下列動作：

1. 建立連結服務以註冊[您自己的 HDInsight 運算叢集](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或設定[隨選 HDInsight 運算叢集](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)。讓我們將此連結服務命名為 "HDInsightLinkedService"。
2. 建立[連結服務](data-factory-azure-storage-connector.md)以設定裝載資料之 Azure Blob 儲存體的連接。讓我們來呼叫此連結服務 "StorageLinkedService"
3. 建立指向輸入和輸出資料的[資料集](data-factory-create-datasets.md)。讓我們來呼叫輸入資料集 "HiveSampleIn" 和輸出資料集 "HiveSampleOut"
4. 將 Hive 查詢作為檔案複製到上述步驟 #2 中設定的 Azure Blob 儲存體。如果裝載資料的連結服務和裝載此查詢檔案的服務不同，請建立個別的 Azure 儲存體連結服務並在活動組態中參考它。使用 **scriptPath** 指定 hive 查詢檔案的路徑並使用 **scriptLinkedService** 指定包含指令碼檔案的 Azure 儲存體。

	> [AZURE.NOTE]您也可以使用**指令碼**屬性提供活動定義中內嵌的 Hive 指令碼，但是不建議這麼做，因為 JSON 文件內指令碼中的所有特殊字元需要逸出，而且可能會造成偵錯問題。最佳作法是遵循步驟 #4。
5.	利用 HDInsightHive 活動建立下列管線來處理資料。

		{
		  "name": "HiveActivitySamplePipeline",
		  "properties": {
		    "activities": [
		      {
		        "name": "HiveActivitySample",
		        "type": "HDInsightHive",
		        "inputs": [
		          {
		            "name": "HiveSampleIn"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "HiveSampleOut"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "typeproperties": {
		          "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
		          "scriptLinkedService": "StorageLinkedService"
		        },
       			"scheduler": {
          			"frequency": "Hour",
          			"interval": 1
        		}
		      }
		    ]
		  }
		}

6.	部署管線。如需詳細資料，請參閱〈[建立管線](data-factory-create-pipelines.md)〉文章。
7.	使用資料處理站監視和管理檢視來監視管線。如需詳細資料，請參閱[監視及管理 Data Factory 管線](data-factory-monitor-manage-pipelines.md)一文。 


## 使用定義項目指定 Hive 指令碼的參數 

請考慮此範例，每天都會將遊戲記錄檔擷取到 Azure Blob 儲存體，並儲存在使用日期和時間分割的資料夾。您想要參數化 Hive 指令碼，在執行階段期間以動態方式傳遞輸入資料夾位置，並且產生使用日期和時間分割的輸出。

若要使用參數化 Hive 指令碼，請執行下列動作

- 定義 **defines** 中的參數。

		{
			"name": "HiveActivitySamplePipeline",
		  	"properties": {
		    "activities": [
		     	{
		        	"name": "HiveActivitySample",
		        	"type": "HDInsightHive",
			        "inputs": [
			          	{
				            "name": "HiveSampleIn"
				          }
		        	],
		        	"outputs": [
		          		{
				            "name": "HiveSampleOut"
				        }
		        	],
		        	"linkedServiceName": "HDInsightLinkedService",
		        	"typeproperties": {
		          		"scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
		          		"scriptLinkedService": "StorageLinkedService",
		          		"defines": {
		            		"Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            		"Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
		          		},
       					"scheduler": {
          					"frequency": "Hour",
          					"interval": 1
        				}
		        	}
		      	}
		    ]
		  }
		}

- 在 Hive 指令碼中，參考使用 **${hiveconf:parameterName}** 的參數。

		DROP TABLE IF EXISTS HiveSampleIn; 
		CREATE EXTERNAL TABLE HiveSampleIn 
		(
			ProfileID 	string, 
		    SessionStart 	string, 
		    Duration 	int, 
		    SrcIPAddress 	string, 
		    GameType 	string
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 
		
		DROP TABLE IF EXISTS HiveSampleOut; 
		CREATE EXTERNAL TABLE HiveSampleOut 
		(
		    ProfileID 	string, 
		    Duration 	int
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';
		
		INSERT OVERWRITE TABLE HiveSampleOut
		Select 
			ProfileID,
			SUM(Duration)
		FROM HiveSampleIn Group by ProfileID

<!---HONumber=Nov15_HO3-->