<properties title="Copy data with Azure Data Factory" pageTitle="使用 Azure Data Factory 複製資料" description="了解如何使用 Azure 資料處理站中的複製活動將資料從資料來源複製到另一個資料來源。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# 使用 Azure Data Factory 複製資料 (複製活動)
您可以使用管線中的「複製活動」****，以批次方式將資料從來源複製到接收器 (目的地)。「複製活動」可以用於下列案例：

- **輸入至 Azure**。在此案例中，會針對下列子案例，將資料從內部部署資料來源 (例如：SQL Server) 複製到 Azure 資料存放區 (例如：Azure Blob、Azure 資料表或 Azure SQL Database)：
	- 在 Azure 上的集中位置收集資料，以進一步處理。
	- 將資料從內部部署上或非 Azure 雲端平台移轉至 Azure。
	- 將資料封存或備份至 Azure，以進行符合成本效益的分層式儲存。
- **從 Azure 輸出**。在此案例中，會針對下列子案例，將資料從 Azure (例如：Azure Blob、Azure 資料表或 Azure SQL Database) 複製到內部部署資料超市及資料倉儲 (例如：SQL Server)：
	- 因缺乏雲端資料倉儲支援而將資料傳輸至內部部署。
	- 將資料傳輸至內部部署，以利用現有的內部部署解決方案或報告基礎結構。
	- 將資料封存或備份至內部部署，以進行分層式儲存
- **Azure 至 Azure 複製**。在此案例中，分散在 Azure 資料來源的資料會彙總到集中式的 Azure 資料存放區中。範例：Azure 資料表至 Azure Blob、Azure Blob 至 Azure 資料表、Azure 資料表至 Azure SQL、Azure Blob 至 Azure SQL。

請參閱[開始使用 Azure Data Factory][adfgetstarted] 中的教學課程，以了解如何使用「複製活動」將資料從 Azure Blob 儲存體複製到 Azure SQL Database。請參閱[讓您的管線使用內部部署資料][use-onpremises-datasources]中的逐步解說，以了解如何使用「複製活動」將資料從內部部署 SQL Server 資料庫複製到 Azure Blob 儲存體。


## 複製活動 - 元件
複製活動包含下列元件： 

- **輸入資料表**。資料表是具有結構描述的資料集，並且是矩形的。輸入資料表元件 
- 描述活動的輸入資料，包括下列各項：資料表的名稱、資料表的類型，以及參考含有輸入資料之資料來源的連結服務。
- **輸出資料表**。輸出資料表描述活動的輸入資料，包括下列各項：資料表的名稱、資料表的類型，以及參考含有輸出資料之資料來源的連結服務。
- **轉換規則**。轉換規則指定如何從來源擷取輸入資料，以及如何將輸出資料載入至接收器等等...
 
一個複製活動可以有一個「輸入資料表」**** 和一個「輸出資料表」****。

## 複製活動的 JSON
管線由一或多個活動所組成。管線中的活動是在 **activities[]** 區段內定義。管線的 JSON 如下所示：
         
	{
		"name": "PipelineName",
		"properties": 
    	{
        	"description" : "pipeline description",
        	"activities":
        	[
	
    		]
		}
	}

**activities** 區段內的每個活動都有下列最上層結構。**type** 屬性應設定為 **CopyActivity**。「複製活動」只可以有一個輸入資料表和一個輸出資料表。
         

	{
		"name": "ActivityName",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [],
		"outputs":  [],
		"transformation":
		{

		},
		"policy":
		{
		
		}
	}

下表說明用於活動區段的標記。 

<table border="1">	
	<tr>
		<th align="left">標記</th>
		<th align="left">描述</th>
		<th align="left">必要</th>
	</tr>	

	<tr>
		<td>name</td>
		<td>活動的名稱。</td>
		<td>是</td>
	</tr>	

	<tr>
		<td>description</td>
		<td>說明活動用途的文字。</td>
		<td>是</td>
	</tr>

	<tr>
		<td>type</td>
		<td>指定活動的類型。 <br/><br/>Auch die Eigenschaften <b>type</b> 應該設定為 <b>CopyActivity</b>。</td>
		<td>是</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>活動所使用的輸入資料表。只能為「複製活動」指定一個輸入資料表。</td>
		<td>是</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>活動所使用的輸出資料表。只能為「複製活動」指定一個輸出資料表。</td>
		<td>是</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>轉換中的屬性取決於類型。Auch die Eigenschaften <b>複製活動</b> 需要您指定一個 <b>source</b> 和一個 <b>sink</b> 區段 (皆在 <b>transformation</b> 區段內指定)。本文後面提供更多的詳細資料。 </td>
		<td>是</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>影響活動之執行階段行為的原則。如果未指定，則會使用預設值。</td>
		<td>否</td>
	</tr>


</table>

請參閱 [JSON 指令碼參考][json-script-reference]，以取得有關 JSON 屬性/標記的詳細資訊。

## 複製活動 - 範例
在此範例中會定義輸入資料表和輸出資料表，並且在管線內的「複製活動」中使用這些資料表，將資料從內部部署 SQL Server 資料庫複製到 Azure Blob。

**假設**
後續的範例 JSON 指令碼將參考下列 Azure Data Factory 成品：

* 名為 **ADF** 的資源群組。
* 名為 **CopyFactory** 的 Azure Data Factory。
* 名為 **MyOnPremisesSQLDB** 並指向內部部署 SQL Server 資料庫的連結服務。
* 名為 **MyAzureStorage** 並指向 Azure Blob 儲存體的連結服務。

### 輸入資料表 JSON
下列 JSON 指令碼定義了參照下列 SQL 資料表的輸入資料表：**MyTable** (位於 **MyOnPremisesSQLDB** 連結服務所定義的內部部署 SQL Server 資料庫中)。請注意，**name** 是 Azure Data Factory 資料表的名稱，而 **tableName** 是 SQL Server 資料庫中 SQL 資料表的名稱。

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

下列範例 Azure PowerShell 命令使用 **New-AzureDataFactoryTable**，此 Cmdlet 會使用包含上述指令碼的 JSON 檔案，在下列 Azure Data Factory 中建立資料表 (**MyOnPremTable**)：**CopyFactory**。
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -Name MyOnPremTable -DataFactoryName CopyFactory -File <Filepath>\MyOnPremTable.json.

請參閱 [Cmdlet 參考][cmdlet-reference]，以取得 Data Factory Cmdlet 的相關詳細資料。 

### 輸出資料表 JSON
下列 JSON 指令碼會定義輸出資料表：**MyDemoBlob**，此資料表參照 Azure Blob：**MyBlob** (位於 Blob 資料夾：**MySubFolder** 中，而此資料夾位於 Blob 容器：**MyContainer** 中)。
         
	{
   		"name": "MyDemoBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

下列範例 Azure PowerShell 命令使用 **New-AzureDataFactoryTable**，此 Cmdlet 會使用包含上述指令碼的 JSON 檔案，在下列 Azure Data Factory 中建立資料表 (**MyDemoBlob**)：**CopyFactory**。
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>


### 管線 (具有複製活動) JSON
在此範例中，管線：**CopyActivityPipeline** 被定義了下列屬性： 

- **type** 屬性是設定為 **CopyActivity**。
- **MyOnPremTable** 被指定為輸入 (**inputs** 標記)。
- **MyAzureBlob** 被指定為輸出 (**outputs** 標記)。
- **Transformation** 區段包含兩個子區段：**source** 和 **sink**。source 的 type 是設定為 **SqlSource**，而 sink 的 type 是設定為 **BlobSink**。**sqlReaderQuery** 定義了要在來源上執行的轉換 (投射)。如需有關所有屬性的詳細資料，請參閱 [JSON 指令碼參考][json-script-reference]。

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}


 下列範例 Azure PowerShell 命令使用 **New-AzureDataFactoryPipeline**，此 Cmdlet 會使用包含上述指令碼的 JSON 檔案，在下列 Azure Data Factory 中建立管線 (**CopyActivityPipeline**)：**CopyFactory**。
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>

## 支援的輸入和輸出
前述範例以 SqlSource 作為轉換區段中的來源，以 BlobSink 作為接收器。下表列出「複製活動」所支援的來源和接收器。 

<table border="1">	
	<tr>
		<th><i>接收器/來源<i></th>
		<th>Azure Blob</th>
		<th>Azure 資料表</th>
		<th>Azure SQL Database</th>
		<th>內部部署 SQL Server</th>
		<th>IaaS 上的 SQL Server</th>
	</tr>	

	<tr>
		<td><b>Azure Blob</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>

	<tr>
		<td><b>Azure 資料表</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>	

	<tr>
		<td><b>Azure SQL Database</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>


	<tr>
		<td><b>內部部署 SQL Server</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>IaaS 上的 SQL Server</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

</table>


下表列出可供 JSON 檔案用於包含複製活動之管線的來源類型和接收器類型。


<table border="1">	
	<tr>
		<th></th>
		<th align="left">來源類型</th>
		<th align="left">接收器類型</th>
	</tr>	

	<tr>
		<td><b>Azure Blob</b></td>
		<td>BlobSource</td>
		<td>BlobSink</td>
	</tr>

	<tr>
		<td><b>Azure 資料表</b></td>
		<td>AzureTableSource</td>
		<td>AzureTableSink</td>
	</tr>

	<tr>
		<td><b>Azure SQL Database</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>內部部署 SQL Server</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>IaaS 上的 SQL</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>
</table>

下表列出這些來源和接收器所支援的屬性。

<table border="1">

	<tr>
	<th align="left">來源/接收器</th>
	<th align="left">支援的屬性</th>
	<th align="left">說明</th>
	<th align="left">允許的值</th>
	<th align="left">必要</th>
	</tr>

	<tr>
		<tr>
		<td><b>BlobSource</b></td>
		<td>BlobSourceTreatEmptyAsNull</td>
		<td>指定是否將 null 或空字串視為 null 值。</td>
		<td>TRUE<br/>FALSE</td>
		<td>否</td>
		</tr>
	</tr>

	<tr>
		<td></td>
		<td>BlobSourceSkipHeaderLineCount</td>
		<td>指出需要略過多少行。</td>
		<td>0 與最大值之間的整數。</td>
		<td>否</td>
	</tr>

	<tr>
		<td><b>AzureTableSource</b></td>
		<td>AzureTableSourceQuery</td>
		<td>使用自訂查詢來讀取資料。</td>
		<td>Azure 資料表查詢字串。<br/>範例："ColumnA eq ValueA"</td>
		<td>否</td>
	</tr>

	<tr>
		<td></td>
		<td>AzureTableSourceIgnoreTableNotFound</td>
		<td>指出是否要忽略例外狀況：「資料表不存在」。</td>
		<td>TRUE<br/>FALSE</td>
		<td>否</td>
	</tr>


	<tr>
		<td><b>SqlSource</b></td>
		<td>sqlReaderQuery</td>
		<td>使用自訂查詢來讀取資料。</td>
		<td>SQL 查詢字串。<br/><br/> 例如："Select * from MyTable"。<br/><br/> 如果未指定，則為 "select <結構中定義的資料行> from MyTable" 查詢。</td>
		<td>否</td>
	</tr>


	<tr>
		<td><b>AzureTableSink</b></td>
		<td>azureTableDefaultPartitionKeyValue</td>
		<td>可供接收器使用的預設資料分割索引鍵值。</td>
		<td>字串值。</td>
		<td>否</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTablePartitionKeyName</td>
		<td>使用者指定的資料行名稱，其資料行值會做為資料分割索引鍵。<br/><br/> 如果未指定，則會使用 AzureTableDefaultPartitionKeyValue 做為資料分割索引鍵。</td>
		<td>資料行名稱。</td>
		<td>否</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableRowKeyName</td>
		<td>要用來做為資料列索引鍵的指定之資料行名稱的資料行值。<br/><br/>如果未指定，則會針對每個資料列使用 GUID。</td>
		<td>資料行名稱。</td>
		<td>否</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableInsertType</td>
		<td>將資料插入 Azure 資料表的模式。</td>
		<td>"merge"<br/><br/>"replace"</td>
		<td>否</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchSize</td>
		<td>在達到 WriteBatchSize 或 writeBatchTimeout 時將資料插入 Azure 資料表中。</td>
		<td>1 與 100 之間的整數 (單位 = 資料列計數)</td>
		<td>否<br/><br/>(預設值 = 100)</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchTimeout</td>
		<td>在達到 WriteBatchSize 或 writeBatchTimeout 時將資料插入 Azure 資料表中</td>
		<td>(單位 = 時間範圍)<br/><br/>範例："00:20:00" (20 分鐘)</td>
		<td>否<br/><br/>(預設為儲存體用戶端的預設逾時值 90 秒)</td>
	</tr>

	<tr>
		<td><b>SqlSink</b></td>
		<td>SqlWriterStoredProcedureName</td>
		<td>指定要將資料更新插入 (更新/插入) 目標資料表中的預存程序名稱。</td>
		<td>預存程序名稱。</td>
		<td>否</td>
	</tr>

	<tr>
		<td></td>
		<td>SqlWriterTableType</td>
		<td>指定要用於前述預存程序中的資料表類型。</td>
		<td>資料表類型名稱。</td>
		<td>否</td>
	</tr>
</table>

### 基礎架構即服務 (IaaS) 上的 SQL
IaaS 上的 SQL 支援以 Azure 做為 laaS 提供者。下列網路和 VPN 拓撲可受支援。請注意，案例 #2 和 #3 需要「資料管理閘道」，但案例 #1 則不需要。如需有關「資料管理閘道」的詳細資料，請參閱[讓您的管線使用內部部署資料][use-onpremises-datasources]。

1.	具有公用 DNS 名稱以及靜態公用連接埠：私用連接埠對應的 VM
2.	具有公用 DNS 名稱、但未公開 SQL 端點的 VM
3.	虛擬網路
	<ol type='a'>
	<li>Azure Cloud VPN with following topology at the end of the list. </li>	
	<li>VM with onpremises-to-cloud site-to-site VPN using Azure Virtual Network.</li>	
	</ol>  
	![Data Factory with Copy Activity][image-data-factory-copy-actvity]

## Column filtering using structure definition
Depending on the type of Table, it is possible to specify a subset of the columns from the source by specifying fewer columns in the **Structure** definition of the table definition than the ones that exist in the underlying data source. The following table provides information about column filtering logic for different types of table. 

<table>

	<tr>
		<th align="left">資料表類型</th>
		<th align="left">資料行篩選邏輯</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>資料表 JSON 中的 <b>Structure</b> 定義必須與 Blob 的結構相符。若要選取資料行的子集，請使用下一節中所述的資料行對應功能：轉換規則 - 資料行對應。</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation 和 OnPremisesSqlServerTableLocation</td>
		<td align="left">
			<p>如果屬性 <b>SqlReaderQuery</b> 被指定為「複製活動」定義的一部分，則資料表的 <b>Structure</b> 定義應該與查詢中所選取的資料欄一致。</p>
			<p>如果未指定屬性 <b>SqlReaderQuery</b> ，則「複製活動」將會自動根據資料表之 <b>Structure</b> 定義中指定的資料行，建構 SELECT 查詢。</p>
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			資料表定義中的 <b>Structure</b> 區段可包含基礎「Azure 資料表」中完整的一組資料行或資料行子集。
		</td>
	<tr>

</table> 

## 轉換規則 - 資料行對應
資料行對應可以用來指定來源資料表中的資料行如何對應至接收器資料表中的資料行。它支援下列案例：

- 將來源資料表「結構」中的所有資料行對應至目的地資料表「結構」。
- 來源資料表「結構」中的資料行子集會對應至所有目的地資料表「結構」。

它不支援下列項目，而會擲回例外狀況： 

- 目的地的資料行會較多或較少。
- 重複的對應。
- SQL 查詢結果沒有資料行名稱

#### 範例 1 - 從 SQL Server 到 Azure Blob 的資料行對應
在此範例中，「輸入資料表」****的定義如下。此輸入資料表具有一個結構，而它指向 SQL Server 資料庫中的 SQL 資料表。
         
		{
		    "name": "MyOnPremTable",
    		"properties":
    		{
				"structure": 
				[
            		{ "name": "userid", "type": "String"},
            		{ "name": "name", "type": "String"},
            		{ "name": "group", "type": "Decimal"}
				],
				"location":
				{
					"type": "OnPremisesSqlServerTableLocation",
					"tableName": "MyTable",
					"linkedServiceName": "MyOnPremisesSQLDB"
				},
				"availability":	
				{
    				"frequency": "Hour",
    				"interval": 1
				}
     		}
		}

在此範例中，「輸出資料表」****的定義如下。此輸出資料表具有一個結構，而它指向 Azure Blob 儲存體中的 Blob。
         
		
	{
		"name": "MyDemoBlob",
		"properties":
		{
    		"structure":
			[
        	    { "name": "myuserid", "type": "String"},
        	    { "name": "mygroup", "type": "String"},
        	    { "name": "myname", "type": "Decimal"}
			],
			"location":
    		{
    	    	"type": "AzureBlobLocation",
		        "folderPath": "MyContainer/MySubFolder",
				"fileName": "MyBlobName",
    	    	"linkedServiceName": "MyLinkedService",
    	    	"format":
    	    	{
    	        	"type": "TextFormat",
		            "columnDelimiter": ",",
    		        "rowDelimiter": ";",
    		        "EscapeChar": "$",
    		        "NullValue": "NaN"
    		    }
			},
			"availability":
			{
    			"frequency": "Hour",
    			"interval": 1
			}
		}
	}	

如果您未指定「輸入資料表」****的 **fileName**，則來自輸入資料夾 (**folderPath**) 的所有檔案/Blob 都會被視為輸入。如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。如需範例，請參閱[教學課程][adf-tutorial]中的範例檔案。

如果您未指定「輸出資料表」**** 的 **fileName**，**folderPath** 中產生的檔案會以下列格式命名：Data.<Guid>.txt (例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

若要根據 **SliceStart** 時間動態設定 **folderPath** 和 **fileName**，請使用 **partitionedBy** 屬性。在下列範例中，**folderPath** 使用 SliceStart (處理配量的開始時間) 中的 Year、Month 和 Day，而 fileName 使用 SliceStart 中的 Hour。例如，若已針對 2014-10-20T08:00:00 產生配量，folderName 會設定為 wikidatagateway/wikisampledataout/2014/10/20 而 fileName 會設定為 08.csv。 

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

在此範例中，管線中的活動定義如下。來源中的資料行使用 **Translator** 屬性對應至接收器中的資料行 (**columnMappings**)(columnMappings)。

##### 範例 - 定義資料行對應

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "MyOnPremTable"  } ],
		"outputs":  [ { "name": "MyDemoBlob" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource"
    		},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
    		}
		}
	}

![Column Mapping][image-data-factory-column-mapping-1]

##### 範例 2 - 透過 SQL 查詢從 SQL Server 至 Azure Blob 的資料行對應
在此範例中，SQL 查詢 (相對於前一個範例中的資料表) 會用來從內部部署 SQL Server 擷取資料，且查詢結果中的資料行會對應至來源成品，然後對應至目的地成品。針對此範例的用途，查詢會傳回 5 個資料行。

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource",
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', Time.AddHours(SliceStart, 0))"
			},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
    		}
		}
	}

![Column Mapping 2][image-data-factory-column-mapping-2]

#### 複製活動的資料類型處理

資料表定義之 Structure 區段中指定的資料類型僅適用於 **BlobSource**。下表說明針對其他類型的來源和接收器，將如何處理資料類型。

<table>	
	<tr>
		<th align="left">來源/接收器</th>
		<th align="left">資料類型處理邏輯</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>資料表定義之 <b>Structure</b> 區段中的資料類型都會被忽略。基礎 SQL 資料庫上定義的資料類型將在複製活動期間，用來擷取資料。</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>資料表定義之 <b>Structure</b> 區段中的資料類型都會被忽略。基礎來源和目的地上的資料類型將被比較，如果有類型不符的情況，就會以隱含方式進行類型轉換。</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td><p>從 <b>BlobSource</b> 傳輸至 <b>BlobSink</b>時，沒有類型轉換。資料表定義之 <b>Structure</b> 區段中的類型都會被忽略。針對 <b>BlobSink</b>以外的目的地，資料表定義之 <b>Structure</b> 區段中的資料類型將被採用。</p>
		<p>
		如果資料表定義中未指定 <b>Structure</b> ，類型處理就會取決於 <b>format</b> 屬性 (隸屬於 <b>BlobSink</b>)：
		</p>
		<ul>
			<li> <b>TextFormat：</b> 所有資料行類型都會被視為字串，且所有資料行名稱會設為 "Prop_<0-N>"</li> 
			<li><b>AvroFormat：</b> 使用 Avro 檔案中的內建資料行類型和名稱。</li> 
			<li><b>JsonFormat：</b> 所有資料行類型都會被視為字串，並使用 Json 檔案中的內建資料行名稱。</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>輸入資料表定義之 <b>Structure</b> 區段中的資料類型都會被忽略。會使用定義於基礎輸入資料存放區的資料類型。資料行將針對 Avro 序列化指定為可為 Null。</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>資料表定義之 <b>Structure</b> 區段中的類型都會被忽略。將使用基礎 Azure 資料表上定義的資料類型。</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>資料表定義之 <b>Structure</b> 區段中的類型都會被忽略。會使用定義於基礎輸入資料存放區的資料類型。</td>
	</tr>

</table>


## 逐步解說
請參閱[開始使用 Azure Data Factory][adfgetstarted] 中的教學課程，以了解如何使用「複製活動」將資料從 Azure Blob 儲存體複製到 Azure SQL Database。
 
請參閱[讓您的管線使用內部部署資料][use-onpremises-datasources]中的逐步解說，以了解如何使用「複製活動」將資料從內部部署 SQL Server 資料庫複製到 Azure Blob 儲存體。



[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png

<!--HONumber=35.2-->
