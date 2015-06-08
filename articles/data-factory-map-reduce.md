<properties 
	pageTitle="從 Azure Data Factory 叫用 MapReduce 程式" 
	description="了解如何在 Azure HDInsight 叢集上執行 MapReduce 程式，從 Azure 資料處理站所處理的資料。" 
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

# 從 Data Factory 叫用 MapReduce 程式
本文說明如何叫用 **MapReduce** 程式使用 Azure 資料 Factory 管線 **HDInsight 活動** 與 **MapReduce 轉換**。

## 簡介 
Azure Data Factory 中的「管線」會使用連結的計算服務，來處理連結的儲存體服務中的資料。它包含一系列活動，其中每個活動都會執行特定的處理作業。本文說明如何使用「HDInsight 活動」的 MapReduce 轉換。
 
請參閱 [使用 Pig 和 Hive 與資料處理站][data-factory-pig-hive-activities] 如需詳細資訊執行 Pig/Hive 指令碼在 HDInsight 上的從一個 Azure 資料 factory 管線使用叢集的 HDInsight 活動的 Pig/Hive 轉換。

## JSON HDInsight 使用 MapReduce 轉換的活動 

在 HDInsight 活動的 JSON 定義：
 
1. 設定 **類型** 的 **活動** 至 **HDInsightActivity**。
2. 設定 **類型** 的 **轉換** 至 **MapReduce**。
3. 指定的類別名稱 **className** 屬性。
4. 指定包含的檔案名稱的 JAR 檔案的路徑 **jarFilePath** 屬性。
5. 指定包含 JAR 檔案，以 Azure Blob 儲存體是指連結的服務 **jarLinkedService** 屬性。   
6. 指定任何引數中的 MapReduce 程式 **引數** ＞ 一節。 

   
 

		{  
		   "name":"MahoutMapReduceSamplePipeline",
		   "properties":{  
		      "description":"Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		      "activities":[  
		         {  
		            "name":"MyMahoutActivity",
		            "description":"Custom Map Reduce to generate Mahout result",
		            "type":"HDInsightActivity",
		            "inputs":[  
		               {  
		                  "Name":"MahoutInput"
		               }
		            ],
		            "outputs":[  
		               {  
		                  "Name":"MahoutOutput"
		               }
		            ],
		            "linkedServiceName":"HDInsightLinkedService",
		            "transformation":{  
		               "type":"MapReduce",
		               "className":"org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		               "jarFilePath":"<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		               "jarLinkedService":"StorageLinkedService",
		               "arguments":[  
		                  "-s",
		                  "SIMILARITY_LOGLIKELIHOOD",
		                  "--input",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/input",
		                  "--output",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/output/",
		                  "--maxSimilaritiesPerItem",
		                  "500",
		                  "--tempDir",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		               ]
		            },
		            "policy":{  
		               "concurrency":1,
		               "executionPriorityOrder":"OldestFirst",
		               "retry":3,
		               "timeout":"01:00:00"
		            }
		         }
		      ]
		   }
		}

您可以使用 MapReduce 轉換，在 HDInsight 叢集上執行任何 MapReduce JAR 檔案。在下列管線的範例 JSON 定義中，已設定讓「HDInsight 活動」執行 Mahout JAR 檔案。

## 範例
您可以下載 MapReduce 轉換中使用 HDInsight 活動的範例： [GitHub 上的資料處理站範例][data-factory-samples]。

## 另請參閱

文章 | 說明
------ | ---------------
[教學課程： 移動及處理序使用資料處理站的記錄檔][adf-tutorial] | 這篇文章提供端對端逐步解說，示範如何實作幾近的真實世界的案例中使用 Azure 資料 Factory 從記錄檔的資料轉換成見解。在本教學課程中，您將使用 Pig 和 Hive 處理資料的轉換。 
[Azure 資料工廠開發人員參考資料][developer-reference] | 開發人員參考資料具有完整的參考內容 cmdlet、 JSON 指令碼、 函式等等... 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: data-factory-pig-hive-activities.md
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!---HONumber=GIT-SubDir-->