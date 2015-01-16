<properties title="Copy data with Azure Data Factory" pageTitle="使用 Azure Data Factory 複製資料" description="了解如何使用 Azure Data Factory 中的複製活動將資料來源的資料複製到其他資料來源。" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# 使用 Azure Data Factory 複製資料 (複製活動)
您可以在管線中使用 [**複製活動**]，以批次方式將資料從來源複製到接收器 (目的地)。複製活動可以用於下列案例：

- **輸入至 Azure**。在此案例中，資料會針對下列子案例，從內部部署資料來源 (例如 SQL Server) 複製到 Azure 資料存放區 (例如 Azure Blob、Azure 資料表或 Azure SQL Database)：
	- 在 Azure 上的集中位置收集資料，以進一步處理。
	- 將資料從內部部署上或非 Azure 雲端平台移轉至 Azure。
	- 將資料封存或備份至 Azure，以進行符合成本效益的分層式儲存。
- **從 Azure 輸出**。在此案例中，資料會針對下列子案例，從 Azure (例如 Azure Blob、Azure 資料表或 Azure SQL Database) 複製到內部部署資料超市和資料倉儲 (例如 SQL Server)：
	- 因缺乏雲端資料倉儲支援而將資料傳輸至內部部署。
	- 將資料傳輸至內部部署，以利用現有的內部部署解決方案或報告基礎結構。
	- 將資料封存或備份至內部部署，以進行分層式儲存
- **Azure 至 Azure 複製**。在此案例中，分散在 Azure 資料來源的資料會彙總到集中式的 Azure 資料存放區中。範例：Azure 資料表至 Azure Blob、Azure Blob 至 Azure 資料表，Azure 資料表至 Azure SQL、Azure Blob 至 SQL Azure。

請參閱[開始使用 Azure Data Factory][adfgetstarted] 中的教學課程，以了解如何使用「複製活動」將資料從 Azure Blob 儲存體複製到 Azure SQL Database。請參閱[讓您的管線使用內部部署資料][use-onpremises-datasources] 中的逐步解說，以了解如何使用「複製活動」將資料從內部部署 SQL Server 資料庫複製到 Azure Blob 儲存體。


## 複製活動 - 元件
複製活動包含下列元件： 

- **輸入資料表**。資料表是具有結構描述的資料集，並且是矩形的。輸入資料表的元件 
- 說明活動的輸入資料，包括：資料表名稱、資料表類型，以及參照資料來源的連結服務，其中包含輸入資料。
- **輸出資料表**。輸出資料表說明活動的輸出資料，包括：資料表名稱、資料表類型，以及參照資料來源的連結服務，其中包含輸出資料。
- **轉換規則**。轉換規則指定如何從來源擷取輸入資料以及輸出資料如何載入至接收器等等...
 
一個複製活動可以有一個**輸入資料表**和一個**輸出資料表**。

## 複製活動的 JSON
管線由一或多個活動所組成。管線中的活動定義於**活動[]**區段中。管線的 JSON 如下所示：
         
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

**活動**區段內的每個活動都有下列最上層結構。**類型**屬性應設為 **CopyActivity**。複製活動只可以有一個輸入資料表和一個輸出資料表。
         

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
		<td>名稱</td>
		<td>活動的名稱。</td>
		<td>Y</td>
	</tr>	

	<tr>
		<td>說明</td>
		<td>說明活動用途的文字。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>類型</td>
		<td>指定活動的類型。<br/><br/><b>類型</b>應設為 <b>CopyActivity</b>。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>輸入</td>
		<td>活動使用的輸入資料表。  只能為複製活動指定一個輸入資料表。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>輸出</td>
		<td>活動使用的輸出資料表。  只能為複製活動指定一個輸出資料表。</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>轉換</td>
		<td>轉換中的屬性視類型而定。  使用<b>複製活動</b>時，您必須在<b>轉換</b>區段中指定<b>來源</b>和<b>接收器</b>區段。本文稍後將提供更多詳細資料。 </td>
		<td>Y</td>
	</tr>

	<tr>
		<td>原則</td>
		<td>會影響活動之執行階段行為的原則。如果未指定，則會使用預設值。</td>
		<td>N</td>
	</tr>


</table>

請參閱 [JSON 指令碼參考][json-script-reference]，以取得關於 JSON 屬性/標記的詳細資訊。

## 複製活動 - 範例
在此範例中會定義輸入資料表和輸出資料表，並且在管線內的「複製活動」中使用這些資料表，將資料從內部部署 SQL Server 資料庫複製到 Azure Blob。

**假設**
後續的範例 JSON 指令碼將參考下列 Azure Data Factory 成品：

* 名為 **ADF** 的資源群組。
* 名為 **CopyFactory** 的 Azure Data Factory。
* 指向內部部署 SQL Server 資料庫的連結服務 **MyOnPremisesSQLDB**。
* 指向 Azure Blob 儲存體的連結服務 **MyAzureStorage**。

### 輸入資料表 JSON
下列 JSON 指令碼定義參照 SQL 資料表的輸入資料表：**MyTable** 是由 **MyOnPremisesSQLDB** 連結服務定義的內部部署 SQL Server 資料庫。請注意，**name** 是 Azure Data Factory 資料表的名稱，而 **tableName** 是 SQL Server 資料庫中的 SQL 資料表名稱。

         
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

下列範例 Azure PowerShell 命令會使用 **New-AzureDataFactoryTable**，其中使用的 JSON 檔案會包含前述指令碼在 Azure Data Factory 中建立資料表 (**MyOnPremTable**)：**CopyFactory**。
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -Name MyOnPremTable -DataFactoryName CopyFactory -File <Filepath>\MyOnPremTable.json.

請參閱 [Cmdlet 參考][cmdlet-reference]，以取得 Data Factory Cmdlet 的詳細資訊。 

### 輸出資料表 JSON
下列 JSON 指令碼會定義輸出資料表：**MyDemoBlob**，會參照 Azure Blob：Blob 資料夾中的 **MyBlob**：Blob 容器中的 **MySubFolder**：**MyContainer**。
         
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

下列範例 Azure PowerShell 命令會使用 **New-AzureDataFactoryTable**，其中使用的 JSON 檔案會包含前述指令碼在 Azure Data Factory 中建立資料表 (**MyDemoBlob**)：**CopyFactory**。
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>


### 管線 (具有複製活動) JSON
在此範例中，管線：**CopyActivityPipeline** 使用下列屬性定義： 

- **類型**屬性設為 **CopyActivity**。
- **MyOnPremTable** 指定為輸入 (**輸入**標記)。
- **MyAzureBlob** 指定為輸出 (**輸出**標記)。
- **轉換**區段包含兩個子區段：**來源**和**接收器**。來源的類型設為 **SqlSource**，接收器的類型設為 **BlobSink**。**sqlReaderQuery** 定義要在來源上執行的轉換 (投射)。如需有關所有屬性的詳細資訊，請參閱 [JSON 指令碼參考][json-script-reference]。

         
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


下列範例 Azure PowerShell 命令會使用 **New-AzureDataFactoryPipeline**，其中使用的 JSON 檔案會包含前述指令碼在 Azure Data Factory 中建立管線 (**CopyActivityPipeline**)：**CopyFactory**。
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>

## 支援的輸入和輸出
前述範例以 SqlSource 作為轉換區段中的來源，以 BlobSink 作為接收器。下表列出複製活動所支援的來源與接收器。 

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
		<td>N</td>
		</tr>
	</tr>

	<tr>
		<td></td>
		<td>BlobSourceSkipHeaderLineCount</td>
		<td>指出需要略過多少行。</td>
		<td>0 與最大值之間的整數。</td>
		<td>N</td>
	</tr>

	<tr>
		<td><b>AzureTableSource</b></td>
		<td>AzureTableSourceQuery</td>
		<td>使用自訂查詢來讀取資料。</td>
		<td>Azure 資料表查詢字串。<br/>範例："ColumnA eq ValueA"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>AzureTableSourceIgnoreTableNotFound</td>
		<td>指出是否要忽略例外狀況：「資料表不存在」。</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>SqlSource</b></td>
		<td>sqlReaderQuery</td>
		<td>使用自訂查詢來讀取資料。</td>
		<td>SQL 查詢字串。<br/><br/>例如："Select * from MyTable"。<br/><br/>如果未指定，則會選取 <columns defined in structure> from MyTable" 查詢。</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>AzureTableSink</b></td>
		<td>azureTableDefaultPartitionKeyValue</td>
		<td>可供接收器使用的預設資料分割索引鍵值。</td>
		<td>字串值。</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTablePartitionKeyName</td>
		<td>使用者指定的資料行名稱，其資料行值會做為資料分割索引鍵。<br/><br/> 如果未指定，則會使用 AzureTableDefaultPartitionKeyValue 做為資料分割索引鍵。</td>
		<td>資料行名稱。</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableRowKeyName</td>
		<td>指定資料行名稱要做為資料列索引鍵的資料行值。<br/><br/>如果未指定，GUID 會使用每個資料列。</td>
		<td>資料行名稱。</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableInsertType</td>
		<td>將資料插入 Azure 資料表的模式。</td>
		<td>"merge"<br/><br/>"replace"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchSize</td>
		<td>在達到 WriteBatchSize 或 writeBatchTimeout 時將資料插入 Azure 資料表中。</td>
		<td>1 與 100 之間的整數 (單位 = 資料列計數)</td>
		<td>N<br/><br/>(預設值 = 100)</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchTimeout</td>
		<td>在達到 WriteBatchSize 或 writeBatchTimeout 時將資料插入 Azure 資料表中</td>
		<td>(單位 = 時間範圍)<br/><br/>範例："00:20:00" (20 分鐘)</td>
		<td>N<br/><br/>(預設為儲存體用戶端預設逾時值 90 秒)</td>
	</tr>

	<tr>
		<td><b>SqlSink</b></td>
		<td>SqlWriterStoredProcedureName</td>
		<td>指定要將資料更新插入 (更新/插入) 目標資料表中的預存程序名稱。</td>
		<td>預存程序名稱。</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>SqlWriterTableType</td>
		<td>指定要用於前述預存程序中的資料表類型。</td>
		<td>資料表類型名稱。</td>
		<td>N</td>
	</tr>
</table>

### 基礎架構即服務 (IaaS) 上的 SQL
IaaS 上的 SQL 支援以 Azure 做為 laaS 提供者。下列網路和 VPN 拓撲可受支援。請注意，案例 #2 和 #3 需要資料管理閘道，案例 #1 則不需要。如需資料管理閘道的詳細資訊，請參閱[讓您的管線可存取內部部署資料][use-onpremises-datasources]。

1.	具有公用 DNS 名稱和靜態公用連接埠的 VM：私用連接埠對應
2.	具有公用 DNS 名稱、但未公開 SQL 端點的 VM
3.	虛擬網路
	<ol type='a'>
	<li>在清單結尾處具有下列拓撲的 Azure 雲端 VPN。 </li>	
	<li>具有內部部署至雲端站對站 VPN (使用 Azure 虛擬網路) 的 VM。</li>	
	</ol>  
	![Data Factory with Copy Activity][image-data-factory-copy-actvity]

## 使用結構定義的資料行篩選
根據資料表類型，您可以藉由在資料表定義的**結構**定義中指定比存在於基礎資料來源中的資料行還少的資料行，來指定資料行的子集。下表提供不同資料表類型之資料行篩選邏輯的相關資訊。 

<table>

	<tr>
		<th align="left">資料表類型</th>
		<th align="left">資料行篩選邏輯</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>資料表 JSON 中的<b>結構</b>定義必須符合 Blob 的結構。  若要選取資料行的子集，請使用下一節中所說明的資料行對應功能：轉換規則 - 資料行對應。</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation 和 OnPremisesSqlServerTableLocation</td>
		<td align="left">
			<p>如果屬性 <b>SqlReaderQuery</b> 指定為複製活動定義的一部分，資料表的<b>結構</b>定義即應與查詢中選取的資料行對應。</p>
			<p>如果未指定屬性 <b>SqlReaderQuery</b>，複製活動將會自動根據資料表定義的<b>結構</b>定義中指定的資料行建構 SELECT 查詢。</p>
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			資料表定義中的<b>結構</b>區段可包含基礎 Azure 資料表中完整的一組資料行或資料行子集。
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
在此範例中，**輸入資料表**的定義如下。輸入資料表都有一個結構，會指向 SQL Server 資料庫中的 SQL 資料表。
         
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

在此範例中，**輸出資料表**的定義如下。輸出資料表有一個結構，會指向 Azure Blob 儲存體中的 Blob。
         
		
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

如果未針對**輸入資料表**指定 **fileName**，則輸入資料夾 (**folderPath**) 中的所有檔案都會被視為輸入。如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。如需範例，請參閱[教學課程][adf-tutorial] 中的範例檔案。

如果您未針對**輸出資料表**指定 **fileName**，則在 **folderPath** 中產生的檔案將會以下列格式命名：Data.<Guid>.txt (例如：: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

若要根據 **SliceStart** 時間動態設定 **folderPath** 和 **fileName**，請使用 **partitionedBy** 屬性。在下列範例中，**folderPath** 使用 SliceStart (處理配量的開始時間) 中的年、月和日，fileName 使用 SliceStart 中的小時。例如，如果配量產生於 2014-10-20T08:00:00，folderName 設定為 wikidatagateway/wikisampledataout/2014/10/20，而 fileName 設定為 08.csv。 

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

在此範例中，管線中的活動定義如下。來源中的資料行使用**轉譯器**屬性對應至接收器中的資料行 (**columnMappings**)。

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
在此範例中，SQL 查詢 (相對於前一個範例中的資料表) 會用來從內部部署 SQL Server 擷取資料，且查詢結果中的資料行會對應至來源成品，然後對應至目的地成品。根據此範例的用途，查詢會傳回 5 個資料行。

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

資料表定義的結構區段中指定的資料類型，僅適用於 **BlobSource**。下表說明為其他類型的來源和接收器處理資料類型的方式。

<table>	
	<tr>
		<th align="left">來源/接收器</th>
		<th align="left">資料類型處理邏輯</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>資料表定義的<b>結構</b>區段中定義的資料類型會被忽略。  定義於基礎 SQL 資料庫上的資料類型將會在複製活動期間用於資料擷取。</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>資料表定義的<b>結構</b>區段中定義的資料類型會被忽略。  基礎來源和目的地上的資料類型將會進行比較，若類型不符，則會執行隱含的類型轉換。</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td><p>從 <b>BlobSource</b> 轉換至 <b>BlobSink</b> 類型時，並不會轉換類型。資料表定義的<b>結構</b>區段中定義的資料類型會被忽略。  針對 <b>BlobSink</b> 以外的目的地，將可接受在資料表定義的<b>結構</b>區段中定義的資料類型。</p>
		<p>
		如果未在資料表定義中指定<b>結構</b>，類型處理將會取決於 <b>BlobSink</b> 的<b>格式</b>屬性：
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
		<td>輸入資料表定義的<b>結構</b>區段中定義的資料類型會被忽略。  將會使用在基礎輸入資料存放區上定義的資料類型。  針對 Avro 序列化，資料行將會指定為可為 null。</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>資料表定義的<b>結構</b>區段中定義的資料類型會被忽略。  將會使用定義於基礎 Azure 資料表上的資料類型。</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>資料表定義的<b>結構</b>區段中定義的資料類型會被忽略。  將會使用定義於基礎輸入資料存放區上的資料類型。</td>
	</tr>

</table>

## 限制

> [WACOM.NOTE] 當管線暫停或刪除，或 Data Factory 刪除時，進行中的複製作業將不會擱置。它會繼續執行到完成為止。不過，牽涉到的內部部署資料來源的複製作業，可以藉由使用「資料管理閘道」組態管理員或「服務」小程式來重新啟動資料管理閘道服務，而加以停止。 



## 逐步解說
請參閱[開始使用 Azure Data Factory][adfgetstarted] 中的教學課程，以了解如何使用「複製活動」將資料從 Azure Blob 儲存體複製到 Azure SQL Database。
 
請參閱[讓您的管線使用內部部署資料][use-onpremises-datasources] 中的逐步解說，以了解如何使用「複製活動」將資料從內部部署 SQL Server 資料庫複製到 Azure Blob 儲存體。



[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png
