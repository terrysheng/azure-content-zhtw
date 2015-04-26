<properties title="Invoke MapReduce Program from Azure Data Factory" pageTitle="從 Azure Data Factory 叫用 MapReduce 程式" description="了解如何在 Azure HDInsight 叢集上從 Azure 資料處理站執行 MapReduce 程式來處理資料。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# 從 Data Factory 叫用 MapReduce 程式
本文說明如何使用「HDInsight 活動」****搭配「MapReduce 轉換」****，從 Azure Data Factory 管線叫用 **MapReduce** 程式。 

## 簡介 
Azure Data Factory 中的管線會使用連結的計算服務，處理連結的儲存體服務中的資料。它包含一系列活動，其中每個活動都會執行特定的處理作業。 

- 「複製活動」****會將資料從來源儲存體複製到目的地儲存體。若要深入了解「複製活動」，請參閱[使用 Data Factory 複製資料][data-factory-copy-activity]。 
- 「HDInsight 活動」****會在 HDInsight 叢集上執行 Hive/Pig 指令碼或 MapReduce 程式來處理資料。「HDInsight 活動」支援三種轉換：**Hive**、**Pig** 及 **MapReduce**。「HDInsight 活動」可以使用一或多個輸入，並產生一或多個輸出。
 
若要了解如何使用「HDInsight 活動」的 Pig/Hive 轉換，在 HDInsight 叢集上從 Azure Data Factory 管線執行 Pig/Hive 指令碼，請參閱[使用 Pig 和 Hive 搭配 Data Factory][data-factory-pig-hive-activities]。本文說明如何使用「HDInsight 活動」的 MapReduce 轉換。

## 管線 JSON
在管線的 JSON 檔案中：
 
1. 將 **activity** 的 **type** 設定為 **HDInsightActivity**。
2. 將 **transformation** 的 **type** 設定為 **HDInsightActivity**。
3. 指定 **className** 屬性的類別名稱。
4. 為 **jarFilePath** 屬性指定包含檔案名稱的 JAR 檔案路徑。
5. 為 **jarLinkedService** 屬性指定參考包含 JAR 檔案之「Azure Blob 儲存體」的連結服務。   
6. 在 **arguments** 區段中指定 MapReduce 城市的任何引數。 

您可以使用 MapReduce 轉換，在 HDInsight 叢集上執行任何 MapReduce JAR 檔案。在下列管線的範例 JSON 定義中，已設定讓「HDInsight 活動」執行 Mahout JAR 檔案。   
 

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

## 範例
您可以從下列位置下載使用「HDInsight 活動」搭配「MapReduce 轉換」的範例：[GitHub 上的 Data Factory 範例][data-factory-samples]。  

## 另請參閱

文章 | 描述
------ | ---------------
[Azure Data Factory 簡介][data-factory-introduction] | 本文介紹 Azure Data Factory 服務、概念、其提供的值，以及其支援的案例。
[開始使用 Azure Data Factory][adf-getstarted] | 本文提供端對端教學課程，示範如何建立會將資料從 Azure Blob 複製到 Azure SQL Database 的範例 Azure Data Factory。
[讓您的管線能夠與內部部署資料搭配使用][use-onpremises-datasources] | 本文提供逐步解說，示範如何將資料從內部部署 SQL Server 資料庫複製到 Azure Blob。
[教學課程：使用 Data Factory 移動及處理記錄檔][adf-tutorial] | 本文提供端對端逐步解說，展示如何使用 Azure Data Factory 實作近乎真實世界的案例，將記錄檔中的資料轉換為見解。
[在 Data Factory 中使用自訂活動][use-custom-activities] | 本文提供逐步解說，內含建立自訂活動並在管線中使用此活動的逐步指示。
[Data Factory 問題疑難排解][troubleshoot] | 本文說明如何對 Azure Data Factory 問題進行疑難排解。
[Azure Data Factory 開發人員參考][developer-reference] |＜開發人員參考＞提供 Cmdlet、JSON 指令碼、函式等等的完整參考內容。 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: ../data-factory-pig-hive-activities
[data-factory-copy-activity]: ..//data-factory-copy-activity
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

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!--HONumber=35.2-->
