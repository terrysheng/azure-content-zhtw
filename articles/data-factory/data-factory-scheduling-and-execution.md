<properties 
	pageTitle="使用 Data Factory 進行排程和執行" 
	description="了解 Azure Data Factory 應用程式模型的排程和執行層面。" 
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
	ms.date="01/27/2016" 
	ms.author="spelluru"/>

# 使用 Data Factory 進行排程和執行
  
本文說明 Azure Data Factory 應用程式模型的排程和執行層面。本文是根據[建立管線](data-factory-create-pipelines.md)和[建立資料集](data-factory-create-datasets.md)文章建置，並且假設您已了解 Data Factory 應用程式模型的基本概念：活動、管線、連結的服務和資料集。

## 排程活動

使用活動 JSON 中的 [**排程器**] 區段，您可以指定活動的週期性排程。例如，您可以排程每小時進行一次的活動，如下所示：

	"scheduler": {
		"frequency": "Hour",
	    "interval": 1
	},  
    
![排程器範例](./media/data-factory-scheduling-and-execution/scheduler-example.png)

如上所示，為活動指定排程將會建立一系列輪轉視窗。輪轉視窗是一系列的固定大小、非重疊和連續的時間間隔。活動的這些邏輯輪轉視窗稱為**活動視窗**。
 
對於目前正在執行的活動視窗，其相關聯的時間間隔可以使用活動 JSON 中的 **WindowStart** 和 **WindowEnd** 系統變數來存取。您可以在您的活動 JSON 和與活動 (包括從輸入選取資料、輸出代表時間序列資料的資料集) 相關聯的指令碼中針對不同用途使用這些變數。

在資料集中，**scheduler** 屬性支援和 **availability** 屬性相同的子屬性。如需可用於排程器 (包括在特定時間位移排程、設定模式以針對活動視窗在間隔的開始或結束對齊處理) 之不同屬性的詳細資訊，請參閱[資料集可用性](data-factory-create-datasets.md#Availability)文章。

## 時間序列資料集和資料配量

時間序列資料是連續順序的資料點，通常包含一段時間間隔的後續度量。時間序列資料的常見範例包括感應器資料、應用程式遙測資料等。

使用 Azure Data Factory，您可以批次方式使用活動執行處理時間序列資料。通常有週期性節奏，在該節奏下，輸入資料抵達以及產生需要的輸出資料。這個節奏是由指定資料集的 [**可用性**] 區段而模式化，如下所示：

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

活動執行取用和產生的每個資料單位稱為資料**配量**。下圖顯示活動的範例，該活動具有輸入時間序列資料集與輸出時間序列資料集，每個都有設定為每小時頻率的可用性集合。

![可用性排程器](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

輸入和輸出資料集的每小時資料配量會顯示在上圖中。下圖顯示 3 個輸入配量，已就緒可以進行處理，且進行中的 10-11AM 活動執行會產生 10-11AM 輸出配量。

與產生之目前配量相關聯的時間間隔可以在資料集 JSON 中使用 **SliceStart** 和 **SliceEnd** 變數存取。

如需適用於可用性區段之不同屬性的詳細資訊，請參閱[建立資料集](data-factory-create-datasets.md)文章。

## 範例 – 「複製活動」會將資料從 Azure SQL 移至 Azure Blob

讓我們整理並且實際操作一下，方法是重新造訪[建立管線](data-factory-create-pipelines.md)文章中顯示的複製活動的範例，這個範例會每小時將資料從 Azure SQL 資料表複製到 Azure Blob。

**輸入：Azure SQL 資料集**

	{
	    "name": "AzureSqlInput",
	    "properties": {
	        "published": false,
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": true,
	        "policy": {}
	    }
	}


請注意，在 [**可用性**] 區段中，[**頻率**] 設為 [**小時**]，[**間隔**] 設為 [**1**]。

**輸出：Azure Blob 資料集**
	
	{
	    "name": "AzureBlobOutput",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
	            "format": {
	                "type": "TextFormat"
	            },
	            "partitionedBy": [
	                {
	                    "name": "Year",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "yyyy"
	                    }
	                },
	                {
	                    "name": "Month",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%M"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%d"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%H"
	                    }
	                }
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


請注意，在 [**可用性**] 區段中，[**頻率**] 設為 [**小時**]，[**間隔**] 設為 [**1**]。



**活動：複製活動**

	{
	    "name": "SamplePipeline",
	    "properties": {
	        "description": "copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "name": "AzureSQLtoBlob",
	                "description": "copy activity",	
	                "typeProperties": {
	                    "source": {
	                        "type": "SqlSource",
	                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 100000,
	                        "writeBatchTimeout": "00:05:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "AzureSQLInput"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOutput"
	                    }
	                ],
	       			"scheduler": {
	          			"frequency": "Hour",
	          			"interval": 1
	        		}
	            }
	        ],
	        "start": "2015-01-01T08:00:00Z",
	        "end": "2015-01-01T11:00:00Z"
	    }
	}


上述範例顯示設為每小時頻率的活動排程和資料集可用性區段。此範例會示範如何能夠運用 **WindowStart** 和 **WindowEnd** 變數，針對指定活動執行選取相關資料，並且將其傳送至 Blob (具有已參數化以每小時具有資料夾的適當動態 **folderPath**)。

當 8 – 11 AM 的其中 3 個配量執行時，這就是範例 Azure 資料表和 Blob 的外觀。

假設 Azure SQL 中的資料如下所示：

![範例輸入](./media/data-factory-scheduling-and-execution/sample-input-data.png)

在部署上述管線時 Azure Blob 將會填入，如下所示：

1.	使用資料歸檔 mypath/2015/1/1/8/Data.<Guid>.txt 

		10002345,334,2,2015-01-01 08:24:00.3130000
		10002345,347,15,2015-01-01 08:24:00.6570000
		10991568,2,7,2015-01-01 08:56:34.5300000

	**附註:**<Guid> 會取代實際的 GUID。範例檔案名稱：Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
2.	使用資料歸檔 mypath/2015/1/1/9/Data.<Guid>.txt：

		10002345,334,1,2015-01-01 09:13:00.3900000
		24379245,569,23,2015-01-01 09:25:00.3130000
		16777799,21,115,2015-01-01 09:47:34.3130000
3.	不使用資料歸檔 mypath/2015/1/1/10/Data.<Guid>.txt。


## 資料配量、管線和並行配量執行的使用中週期

[建立管線](data-factory-create-pipelines.md)文章導入藉由設定管線的 **start** 和 **end** 屬性來指定管線的使用中週期的概念。
 
您可以設定過去日期的管線使用中週期的開始日期，Data Factory 會自動計算 (回補) 過去的所有資料配量，並且開始處理。

回補資料配量之後，就可以設定它們以平行執行。您可以在活動 JSON 的 [**原則**] 區段中設定並行屬性來執行此作業，如[建立管線](data-factory-create-pipelines.md)文章所示。

## 重新執行失敗的資料配量和自動資料相依性追蹤

您可以使用豐富的視覺化方式監視配量的執行。如需詳細資訊，請參閱[監視及管理管線](data-factory-monitor-manage-pipelines.md)文章。

請思考一下會顯示兩個活動的下列範例。Activity1 會產生時間序列資料集，具有由 Activity2 做為輸入取用的輸出配量，以產生最終輸出時間序列資料集。

![失敗的配量](./media/data-factory-scheduling-and-execution/failed-slice.png)

<br/>

上圖顯示 3 個最近的配量當中有失敗，針對 **Dataset2** 產生 9-10 AM 配量。Data Factory 會自動追蹤時間序列資料集的相依性，並且因而暫緩開始 9-10 AM 下游配量的活動執行。


Data Factory 監視和管理工具可讓您深入診斷記錄以了解失敗的配量，輕鬆地找出問題的根本原因並加以修正。一旦您修正問題之後，也可以輕易地開始活動執行以產生失敗的配量。如需如何開始重新執行的詳細資訊、了解資料配量的狀態轉換，請參閱[監視和管理](data-factory-monitor-manage-pipelines.md)文章。

一旦開始重新執行且 dataset2 的 9-10AM 配量已就緒之後，Data Factory 就會開始最終資料集的 9-10 AM 相依配量的執行，如下圖所示。

![重新執行失敗的配量](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

若要深入剖析針對複雜的活動和資料集鏈結指定相依性和追蹤相依性，請參閱下列各節。

## 鏈結活動
您可以將一個活動的輸出資料集設為另一個活動的輸入資料集，藉此鏈結兩個活動。活動可以在相同的管線中或在不同的管線中。只有當第一個活動執行成功完成時，第二個活動才會執行。

例如，請考慮下列情況：
 
1.	管線 P1 具有需要外部輸入資料集 D1 的活動 A1，並產生**輸出**資料集 **D2**。
2.	管線 P2 具有需要資料集 **D2** 的 **輸入**的活動 A2，並產生輸出資料集 D3。
 
在此案例中，活動 A1 會在外部資料提供使用時執行，且達到排程的可用性頻率。活動 A2 會在 D2 的排定的分割可供使用時執行，且達到排程的可用性頻率。如果資料集 D2 中的其中一個分割發生錯誤，則不會針對該分割執行 A2，直到該分割可供使用為止。

[圖表檢視] 看起來如下：

![兩個管線中的鏈結活動](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

兩個活動同時在相同管線中的 [圖表檢視] 看起來如下：

![相同管線中的鏈結活動](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### 已排序的複本
能夠以循序/排序的方式，逐一執行多個複製作業。假設您在管線中有兩個複製活動︰CopyActivity1 和 CopyActivity，並具備下列輸入資料輸出資料集。

CopyActivity1：輸入：Dataset1 輸出 Dataset2

CopyActivity2：輸入：Dataset2 輸出：Dataset4

唯有當 CopyActivity1 成功執行且 Dataset2 可供使用時，CopyActivity2 才會執行。

在上述範例中，CopyActivity2 可以有不同的輸入 (假設 Dataset3)，但是您也必須指定 Dataset2 做為 CopyActivity2 的輸入，因此在 CopyActivity1 完成之前，活動將不會執行。例如：

CopyActivity1：輸入：Dataset1 輸出 Dataset2

CopyActivity2：輸入：Dataset3、Dataset2 輸出：Dataset4

指定多個輸入時，只有第一個輸入資料集會用來複製資料，但是其他資料集會用來做為相依性。CopyActivity2 只會符合下列條件時開始執行︰

- CopyActivity2 已順利完成且 Dataset2 可供使用。將資料複製到 Dataset4 時，將不會使用此資料集。它只會用來做為 CopyActivity2 的排程相依性。   
- Dataset3 可供使用。此資料集代表已複製到目的地的資料。  



## 使用不同的頻率模型化資料集

在如上所示的範例中，輸入和輸出資料集和活動排程時段的頻率是相同的。某些案例需要能夠以不同於一或多個輸入的頻率產生輸出。Data Factory 支援模型化這些案例。

### 範例 1：對每小時可用的輸入資料產生每日輸出報告

假設我們在 Azure Blob 中有每小時可用之感應器的輸入度量資料，且我們想要產生每日彙總報告，具有平均值、最大值、最小值等統計資料...針對 Data Factory 每天的 [Hive 活動](data-factory-hive-activity.md)。

以下是使用 Data Factory 模型化這個項目的方式：

**輸入 Azure Blob 資料集：**

每小時輸入檔案會針對指定日期在資料夾中卸除。輸入的可用性設定為每小時 (頻率：小時、間隔：1)。

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**輸出 Azure Blob 資料集**

一個輸出檔會在資料夾中每日卸除。輸出的可用性設定為每日 (頻率：日、間隔：1)。


	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**活動：管線中的 Hive 活動**

Hive 指令碼會收到適當的日期時間資訊，做為運用 **WindowStart** 變數的參數，如下所示。Hive 指令碼會使用此變數將資料從右邊資料夾載入，並執行彙總來產生輸出。

		{  
		    "name":"SamplePipeline",
		    "properties":{  
		    "start":"2015-01-01T08:00:00",
		    "end":"2015-01-01T11:00:00",
		    "description":"hive activity",
		    "activities": [
		        {
		            "name": "SampleHiveActivity",
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
		            "linkedServiceName": "HDInsightLinkedService",
		            "type": "HDInsightHive",
		            "typeProperties": {
		                "scriptPath": "adftutorial\\hivequery.hql",
		                "scriptLinkedService": "StorageLinkedService",
		                "defines": {
		                    "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
		                    "Month": "$$Text.Format('{0:%M}',WindowStart)",
		                    "Day": "$$Text.Format('{0:%d}',WindowStart)"
		                }
		            },
		            "scheduler": {
		                "frequency": "Day",
		                "interval": 1
		            },			
		            "policy": {
		                "concurrency": 1,
		                "executionPriorityOrder": "OldestFirst",
		                "retry": 2,
		                "timeout": "01:00:00"
		            }
	             }
		     ]
		   }
		}

以下是它從資料相依性的觀點看起來的外觀。

![資料相依性](./media/data-factory-scheduling-and-execution/data-dependency.png)

每一天的輸出配量取決於輸入資料集之 24 小時每小時的配量。Data Factory 會自動運算這些相依性，方法是釐清落在與要產生之輸出配量相同時間期間的輸入資料配量。如果 24 個輸入配量中有任何一項無法使用 (例如，因為處理發生在產生該配量的活動上游)，Data Factory 會等待輸入配量就緒，才開始每日活動執行。


### 範例 2：使用運算式和 Data Factory 函式指定相依性

我們來看一下另一種案例。假設您有處理兩個輸入資料集的 Hive 活動，其中一個每日有新資料，但是另一個會每週取得新資料。假設您想要跨兩個輸入進行聯結作業，並且每日產生輸出。
 
目前為止簡單的方法是 Data Factory 會自動找出要處理的正確輸入配量，方法是包括與不再運作之輸出資料配量時間期間對齊的輸入資料配量。

您必須指定每個活動執行，Data Factory 應該針對每週輸入資料集使用上一週的資料配量。您可以透過 Azure Data Factory 函式的協助執行此作業，如下所示。

**Input1：Azure Blob**

第一個輸入是 Azure Blob **每日**更新。
	
	{
	  "name": "AzureBlobInputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Input2：Azure Blob**

Input2 是 Azure Blob **每週**更新。

	{
	  "name": "AzureBlobInputWeekly",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 7
	    }
	  }
	}

**輸出：Azure Blob**

一個輸出檔會在資料夾中每日卸除。輸出的可用性設定為每日 (頻率：日、間隔：1)。
	
	{
	  "name": "AzureBlobOutputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**活動：管線中的 Hive 活動**

Hive 活動接受 2 個輸入，並且每日產生輸出配量。您可以針對每週輸入指定每日的輸出配量是根據上一週的輸入配量，如下所示。
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"hive activity",
	    "activities": [
	      {
	        "name": "SampleHiveActivity",
	        "inputs": [
	          {
	            "name": "AzureBlobInputDaily"
	          },
	          {
	            "name": "AzureBlobInputWeekly",
	            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
	            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutputDaily"
	          }
	        ],
	        "linkedServiceName": "HDInsightLinkedService",
	        "type": "HDInsightHive",
	        "typeProperties": {
	          "scriptPath": "adftutorial\\hivequery.hql",
	          "scriptLinkedService": "StorageLinkedService",
	          "defines": {
	            "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
	            "Month": "$$Text.Format('{0:%M}',WindowStart)",
	            "Day": "$$Text.Format('{0:%d}',WindowStart)"
	          }
	        },
	        "scheduler": {
	          "frequency": "Day",
	          "interval": 1
	        },			
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 2,  
	          "timeout": "01:00:00"
	        }
		   } 
	     ]
	   }
	}


## Data Factory 函式與系統變數   

請參閱 [Data Factory 函式與系統變數](data-factory-functions-variables.md)文章，以取得 Azure Data Factory 支援的函式與系統變數清單。

## 資料相依性的深入探討

若要依據活動執行來產生資料集配量，Data Factory 會使用下列**相依性模型**來判斷活動取用的資料集與活動產生的資料集之間的關聯性。

需要輸入資料集的時間範圍以產生稱為**相依性期間**的輸出資料集配量。

活動執行只會在相依性期間內輸入資料集的資料配量可用時產生資料集配量。這表示相依性期間所包含的所有輸入配量，對於活動執行產生的輸出資料集配量必須在**就緒**狀態。

若要產生資料集配量 [開始、結束]，需要函式以將資料集配量對應至其相依性期間。此函式基本上是將資料集配量的開始和結束轉換為相依性期間的開始和結束的公式。更正式的說法，
	
	DatasetSlice = [start, end]
	DependecyPeriod = [f(start, end), g(start, end)]

其中 f 和 g 是對應函式，計算每個活動輸入的相依性期間的開始和結束。

如上述範例所示，大部分的情況下，相依性期間與產生資料配量的期間是相同的。在這些情況下，Data Factory 會自動運算落在相依性期間內的輸入配量。

例如：在上述彙總範例中，其中輸出是每日產生，而輸入資料每小時可用，資料配量期間為 24 小時。Data Factory 會針對此時間期間尋找相關的每小時輸入配量，讓輸出配量相依於輸入配量。

您也可以針對相依性期間提供自己的對應，如上述範例所示，當中的其中一個輸入是每週，而輸出配量是每日產生。
   
## 資料相依性和驗證

資料集可以選擇性地具有定義的驗證原則，指定配量執行所產生的資料在供取用之前如何驗證。如需詳細資訊，請參閱[建立資料集](data-factory-create-datasets.md)文章。

在這種情況下，一旦配量完成執行，輸出配量狀態就會變更為**等候**，子狀態為**驗證**。一旦配量經過驗證之後，配量狀態會變更為**就緒**。
   
如果已產生資料配量但是未通過驗證，將不會處理相依於驗證失敗的配量的下游配量活動執行。

Data Factory 中資料配量的各種狀態涵蓋於[監視和管理管線](data-factory-monitor-manage-pipelines.md)文章中。

## 外部資料

資料集可以標示為外部 (如以下的 JSON 所示)，意味著它尚未使用 Azure Data Factory 產生。在這種情況下，資料集原則可以包含一組額外的參數，描述資料集的驗證和重試原則。如需所有屬性的描述，請參閱[建立管線](data-factory-create-pipelines.md)。

類似於 Data Factory 產生的資料集，在可以處理相依配量之前，外部資料的資料配量必須是就緒狀態。

	{
		"name": "AzureSqlInput",
		"properties": 
		{
			"type": "AzureSqlTable",
			"linkedServiceName": "AzureSqlLinkedService",
			"typeProperties": 
			{
				"tableName": "MyTable"	
			},
			"availability": 
			{
				"frequency": "Hour",
				"interval": 1     
			},
			"external": true,
			"policy": 
			{
				"externalData": 
				{
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}  
		} 
	} 






  









 
 












      

  

<!---HONumber=AcomDC_0316_2016-->