<properties 
	pageTitle="Azure Data Factory 中的資料集 | Microsoft Azure" 
	description="了解 Azure Data Factory 資料集並學習如何建立它們。" 
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
	ms.date="01/26/2016" 
	ms.author="spelluru"/>

# Azure Data Factory 中的資料集

## 說明
資料集是資料的邏輯描述。所說明的資料可能有所不同，包括簡單位元組、半結構化資料 (例如 CSV 檔案)，乃至於關聯式資料表甚或模型。用來存取資料的機制 (位址、通訊協定、驗證結構描述) 已定義於連結服務中並可在資料集定義中參考。

## 語法

	{
	    "name": "<name of dataset>",
	    "properties":
	    {
	       "structure": [ ],
	       "type": "<type of dataset>",
			"external": <boolean flag to indicate external data>,
	        "typeProperties":
	        {
	        },
	        "availability":
	        {
	
	        },
	       "policy": 
	        {      
	
	        }
	    }
	}

語法詳細資料

| 屬性 | 說明 | 必要 | 預設值 |
| -------- | ----------- | -------- | ------- |
| 名稱 | 資料集的名稱 | 是 | NA |
| structure | 資料集的結構描述<br/><br/>請參閱[資料集結構](#Structure)一節，以取得詳細資訊 | 編號 | NA |
| 類型 | 資料集的類型 | 是 | NA |
| typeProperties | 對應至所選類型的屬性請參閱[資料集類型](#Type)一節，以取得支援的類型及其屬性的詳細資訊。 | 是 | NA |
| external | 用來指定資料集是否由 Data Factory 管線明確產生的布林值旗標 | 否 | false | 
| availability | 定義處理時間範圍或資料集生產的切割模型。<br/><br/>請參閱[資料集可用性](#Availability)主題，以取得詳細資訊<br/><br/>請參閱[排程和執行](data-factory-scheduling-and-execution.md)一文，以取得資料集切割模型的詳細資訊 | 是 | NA
| 原則 | 定義資料集配量必須符合的準則或條件。<br/><br/>請參閱[資料集原則](#Policy)主題，以取得詳細資訊 | 否 | NA |

### 範例

以下的資料集範例表示 Azure SQL Database 中名為 MyTable 的資料表。Azure SQL Database 連接字串已定義於此資料集中參照的 AzureSqlLinkedService。此資料集會每天切割。

	{
	    "name": "DatasetSample",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": 
	        {
	            "tableName": "MyTable"
	        },
	        "availability": 
	        {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

## <a name="Structure"></a>資料集結構

Structure 區段判斷提示資料集的結構描述。其中包含資料行的集合和這些資料行的類型。各資料行包含下列屬性：

屬性 | 說明 | 必要 | 預設值  
-------- | ----------- | -------- | -------
名稱 | 資料行的名稱 | 否 | NA 
類型 | 資料行的資料類型 | 否 | NA 

### 範例

在下列範例中，資料集有三個資料行 slicetimestamp、projectname 和 pageviews。

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Type"></a> 資料集類型

支援的資料來源和資料集類型會對應。如需資料集的類型和組態資訊，請參閱[資料移動活動](data-factory-data-movement-activities.md)一文中參照的連接器主題。

## <a name="Availability"></a> 資料集可用性

資料集中的 Availability 區段定義處理時間範圍或資料集生產的切割模型。如需資料集切割和相依性模型的詳細資訊，請參閱[排程和執行](data-factory-scheduling-and-execution.md)。

| 屬性 | 說明 | 必要 | 預設值 |
| -------- | ----------- | -------- | ------- |
| frequency | 指定資料集配量生產的時間單位。<br/><br/>**支援的頻率**：分鐘、小時、日、週、月 | 是 | NA |
| interval | 指定頻率的倍數<br/><br/>「頻率 x 間隔」可決定產生配量的頻率。<br/><br/>如果您需要每小時切割資料集，請將 [頻率] 設為 [小時]，將 [間隔] 設為 [1]。<br/><br/>**注意：** 如果您將 [頻率] 指定為 [分鐘]，建議您將間隔設為不小於 [15] | 是 | NA |
| style | 指定是否應該在間隔開始/結束時產生配量。<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>如果 [頻率] 設為 [月] 且 style 設為 EndOfInterval，則會在月份的最後一天產生配量。如果 style 設為 StartOfInterval，則會在月份的第一天產生配量。<br/><br/>如果 [頻率] 設為 [天] 且 style 設為 EndOfInterval，則會在一天的最後一個小時產生配量。<br/><br/>如果 [頻率] 設為 [小時] 且 style 設為 EndOfInterval，則會在一小時結束時產生配量。例如，若為下午 1 – 2 點期間的配量，此配量會在下午 2 點產生。 | 否 | EndOfInterval |
| anchorDateTime | 定義排程器用來計算資料集配量界限的時間絕對位置。<br/><br/>**注意：** 如果 AnchorDateTime 有比頻率更細微的日期部分，則會忽略更細微的部分。例如，如果 [間隔] 為 [每小時] \(頻率：小時，間隔：1) 而且 AnchorDateTime 包含分鐘和秒鐘，則會忽略 AnchorDateTime 的分鐘和秒鐘部分。 | 否 | 01/01/0001 |
| Offset | 所有資料集配量的開始和結束移位所依據的時間範圍。<br/><br/>**附註：** 如果已指定 anchorDateTime 和 offset，結果會是合併的移位。 | 否 | NA |

### anchorDateTime 範例

範例：於 2007-04-19T08:00:00 開始的 23 小時資料集配量

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": "23",	
		"anchorDateTime":"2007-04-19T08:00:00"	
	}


### 位移範例

於上午 6 點 (而非預設的午夜) 開始的每日配量。

	"availability":
	{
		"frequency": "Day",
		"interval": "1",
		"offset": "06:00:00"
	}

[頻率] 設定為 [月] 且 [間隔] 設定為 [1] \(一個月一次)：如果您希望在每個月第 9 天的早上 6 點產生配量，請將位移設定為 "09.06:00:00"。請記住，這是 UTC 時間。

在 12 個月 (頻率 = 月；間隔 = 12) 的排程中，offset: 60.00:00:00 表示每年的 3 月 1 日或 2 日 (如果樣式 = StartOfInterval，則為從年初算起的 60 天)，這取決於該年度是否為閏年。


## <a name="Policy"></a>資料集原則

資料集中的 Policy 區段定義資料集配量必須符合的準則或條件。

### 驗證原則

| 原則名稱 | 說明 | 適用於 | 必要 | 預設值 |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | 驗證 Azure Blob 中的資料是否符合最小的大小需求 (以 MB 為單位)。 | Azure Blob | 否 | NA |
|minimumRows | 驗證 Azure SQL Database 或 Azure 資料表中的資料是否包含最小的資料列數。 | <ul><li>Azure SQL Database</li><li>Azure 資料表</li></ul> | 否 | NA

#### 範例

minimumSizeMB：

	"policy":
	
	{
	    "validation":
	    {
	        "minimumSizeMB": 10.0
	    }
	}

minimumRows

	"policy":
	{
		"validation":
		{
			"minimumRows": 100
		}
	}

### ExternalData 原則

外部資料集是並非由 Data Factory 中的執行中管線所產生的資料集。如果資料集標示為「外部」，則可定義 ExternalData 原則來影響資料集配量可用性的行為。

| 名稱 | 說明 | 必要 | 預設值 |
| ---- | ----------- | -------- | -------------- |
| dataDelay | 延遲指定配量之外部資料可用性檢查的時間。例如，如果認為資料每小時可用，則可檢查外部資料是否確實可以使用，且對應的配量準備可能會因為 dataDelay 而延遲。<br/><br/>僅適用於當前的時間；例如，如果現在是下午 1:00 且這個值是 10 分鐘，則驗證將會在下午 1:10 開始。<br/><br/>此設定不會影響過去的配量，將會在沒有任何延遲的情況下處理 (配量結束時間 + dataDelay < 現在的配量)。<br/><br/>時間若大於 23:59 小時，則必須使用「日.小時:分:秒」格式指定。例如，若要指定 24 小時，請不要使用 24:00:00；請改用 1.00:00:00。如果您使用 24:00:00，這會視同 24 天 (24.00:00:00)。如為 1 天又 4 小時，請指定 1:04:00:00。 | 否 | 0 |
| retryInterval | 失敗與下一步重試嘗試之間的等待時間。適用於當前的時間；如果先前嘗試失敗，我們會在上次嘗試之後等待這麼久。<br/><br/>如果現在是下午 1:00，我們將開始第一次嘗試。如果完成第一次驗證檢查的持續時間是 1 分鐘且作業失敗，則下一次重試會在 1:00 + 1 分鐘 (持續時間) + 1 分鐘 (重試間隔) = 1:02pm。<br/><br/>若是過去的配量，則不會有任何延遲。重試會立即執行。 | 否 | 00:01:00 (1 分鐘) | 
| retryTimeout | 每次重試嘗試的逾時。<br/><br/>如果此值設為 10 分鐘，則必須在 10 分鐘內完成驗證。如果要花超過 10 分鐘來執行驗證，重試將會逾時。<br/><br/>如果所有驗證嘗試都逾時，則配量會標示為 TimedOut。 | 否 | 00:10:00 (10 分鐘) |
| maximumRetry | 檢查外部資料可用性的次數。允許的最大值為 10。 | 否 | 3 | 

#### 更多範例

如果您需要在特定日期和時間 (假設在每月三號上午 8:00) 執行以每月為基礎的管線，您可以使用 [位移] 標記，以設定其應該要執行的日期和時間。

	{
	  "name": "MyDataset",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Month",
	      "interval": 1,
	      "offset": "3.08:10:00",
	      "style": "StartOfInterval"
	    }
	  }
	}

<!---HONumber=AcomDC_0309_2016-->
