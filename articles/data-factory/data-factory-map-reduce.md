<properties 
	pageTitle="從 Azure Data Factory 叫用 MapReduce 程式" 
	description="了解如何從 Azure Data Factory，在 Azure HDInsight 叢集上執行 MapReduce 程式以處理資料。" 
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
	ms.date="09/22/2015" 
	ms.author="spelluru"/>

# 從 Data Factory 叫用 MapReduce 程式
本文說明如何使用 **HDInsight MapReduce 活動**，從 Azure Data Factory 管線叫用 **MapReduce** 程式。

## 簡介 
Azure Data Factory 中的「管線」會使用連結的計算服務，來處理連結的儲存體服務中的資料。它包含一系列活動，其中每個活動都會執行特定的處理作業。本文說明如何使用「HDInsight 活動」的 MapReduce 轉換。
 
若要了解如何使用 HDInsight 活動的 Pig/Hive 轉換，在 HDInsight 叢集上從 Azure Data Factory 管線執行 Pig/Hive 指令碼，請參閱〈[Pig](data-factory-pig-activity) 和 [Hive](data-factory-hive-activity.md)〉文章。

## 使用 MapReduce 轉換之 HDInsight 活動的 JSON 

在 HDInsight 活動的 JSON 定義中：
 
1. 將 **activity** 的 **type** 設為 **HDInsight**。
3. 指定 **className** 屬性的類別名稱。
4. 為 **jarFilePath** 屬性指定包含檔案名稱的 JAR 檔案路徑。
5. 為 **jarLinkedService** 屬性指定連結服務，此連結服務參考包含 JAR 檔案的 Azure Blob 儲存體。   
6. 在 **arguments** 區段中，為 MapReduce 程式指定所有引數。在執行階段，您會看到 MapReduce 架構的幾個額外的引數 (例如: mapreduce.job.tags)。若要區分在 MapReduce 引數中所使用您的引數，請考慮同時使用選項和值做為引數，如下列範例所示 (-s、--input、--output 等等...這些選項後面緊跟著其值)。

 

		{
		  "name": "MahoutMapReduceSamplePipeline",
		  "properties": {
		    "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		    "activities": [
		      {
		        "name": "MyMahoutActivity",
		        "description": "Custom Map Reduce to generate Mahout result",
		        "inputs": [
		          {
		            "Name": "MahoutInput"
		          }
		        ],
		        "outputs": [
		          {
		            "Name": "MahoutOutput"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "type": "HDInsightMapReduce",
		        "typeProperties": {
		          "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		          "jarFilePath": "<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		          "jarLinkedService": "StorageLinkedService",
		          "arguments": [
		            "-s",
		            "SIMILARITY_LOGLIKELIHOOD",
		            "--input",
		            "$$Text.Format('wasb://<container>@<accountname>.blob.core.windows.net/Mahout/Input/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            "--output",
		            "$$Text.Format('wasb://<container>@<accountname>.blob.core.windows.net/Mahout/Output/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            "--maxSimilaritiesPerItem",
		            "500",
		            "--tempDir",
		            "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		          ]
		        },
		        "policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "OldestFirst",
		          "retry": 3,
		          "timeout": "01:00:00"
		        }
		      }
		    ]
		  }
		}

您可以使用 MapReduce 轉換，在 HDInsight 叢集上執行任何 MapReduce JAR 檔案。在下列管線的範例 JSON 定義中，已設定讓「HDInsight 活動」執行 Mahout JAR 檔案。

## 範例
您可以從 [GitHub 上的 Data Factory 範例](data-factory-samples.md)下載搭配 MapReduce 轉換使用 HDInsight 活動的範例。


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=Oct15_HO3-->