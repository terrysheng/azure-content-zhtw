<properties 
	pageTitle="Hadoop 串流活動" 
	description="了解如何使用 Azure Data Factory 中的 Hadoop 串流活動，以在隨選/您自己的 HDInsight 叢集上執行 Hadoop 串流程式。" 
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
	ms.date="10/25/2015" 
	ms.author="spelluru"/>

# Hadoop 串流活動
您可以使用 HDInsightStreamingActivity 活動從 Azure Data Factory 管線叫用 Hadoop 串流工作。下列 JSON 片段會示範在管線 JSON 檔案中使用 HDInsightStreamingActivity 的語法。

Data Factory [管線](data-factory-create-pipelines.md)中的 HDInsight 串流活動會在[您自己的](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或[隨選](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 叢集上執行 Hadoop 串流程式。這篇文章是根據[資料轉換活動](data-factory-data-transformation-activities.md)文章，它呈現資料轉換和支援的轉換活動的一般概觀。

## 範例

	{
	    "name": "HadoopStreamingPipeline",
	    "properties":
	    {
	        "description" : "Hadoop Streaming Demo",
	        "activities":
	        [
	           {
	               "name": "RunHadoopStreamingJob",
	               "description": "Run a Hadoop streaming job",
	               "type": "HDInsightStreaming",
	               "getDebugInfo": "Failure",
	               "inputs": [ ],
	               "outputs": [ {"name": "OutputTable"} ],
	               "linkedServiceName": "HDInsightLinkedService",
	               "typeProperties":
	               {
	                   "mapper": "cat.exe",
	                   "reducer": "wc.exe",
	                   "input":  "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                   "output": " wasb://adfsample@<account name>.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                   "filePaths": [ 
	                       "adfsample/example/apps/wc.exe" , 
	                       "adfsample/example/apps/cat.exe" 
	                   ],
	                   "fileLinkedService" : "StorageLinkedService",
	                   "arguments":[
	                   ]
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

請注意：

1. 對連結的服務的名稱設定 **linkedServiceName**，該服務指向您的 HDInsight 叢集，串流 mapreduce 工作會在該叢集上執行。
2. 將活動的類型設為 **HDInsightStreaming**。
3. 針對 **mapper** 屬性，指定對應程式可執行檔的名稱。在上述範例中，cat.exe 是對應程式可執行檔。
4. 針對 **reducer** 屬性，指定減壓器可執行檔的名稱。在上述範例中，cat.exe 是減壓器可執行檔。
5. 針對 **input** 屬性，指定對應程式的輸入檔 (包括位置)。在 "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt" 範例中：adfsample 是 blob 容器，example/data/Gutenberg 是資料夾，而 davinci.txt 是 blob。
6. 針對 **output** 屬性，指定減壓器的輸出檔 (包括位置)。Hadoop 串流工作的輸出會寫入針對這個屬性指定的位置。
7. 在 **filePaths** 區段中，指定對應程式和減壓器可執行檔的路徑。在 "adfsample/example/apps/wc.exe" 範例中，adfsample 是 blob 容器，example/apps 是資料夾，而 wc.exe 是可執行檔。
8. 針對 **fileLinkedService** 屬性，指定代表 Azure 儲存體 (包含 filePaths 區段中指定的檔案) 的 Azure 儲存體連結服務。
9. 針對 **arguments** 屬性，指定串流工作的引數。
10. **getDebugInfo** 屬性是選擇性元素。該屬性設定為 [失敗] 時，只能在執行失敗時下載記錄檔。當其設定為 [所有] 時，無論執行狀態為何，一律下載記錄檔。 

	

<!---HONumber=Nov15_HO1-->