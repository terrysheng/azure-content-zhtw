<properties 
	pageTitle="Azure 搜尋服務的搜尋流量分析 |Microsoft Azure" 
	description="啟用 Azure 搜尋服務 (Microsoft Azure 上裝載的雲端搜尋服務) 的搜尋流量分析，來深入分析您的使用者與資料。" 
	services="search" 
	documentationCenter="" 
	authors="bernitorres" 
	manager="pablocas" 
	editor=""
/>

<tags 
	ms.service="search" 
	ms.devlang="multiple" 
	ms.workload="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="12/11/2015" 
	ms.author="betorres"
/>


# 啟用和使用搜尋流量分析 #

搜尋流量分析是一項 Azure 搜尋服務功能，可讓您掌握您的搜尋服務，並深入分析您的使用者及其行為。當您啟用此功能時，您的搜尋服務資料會複製到您選擇的儲存體帳戶。此資料包括您的搜尋服務記錄檔和彙總的作業度量。之後，您就能利用任何方式來處理和管理使用方式資料。


## 如何啟用搜尋流量分析 ##

### 1\.使用入口網站 ###
在 [Azure 入口網站](http://portal.azure.com)中開啟 Azure 搜尋服務。您可以在 [設定] 下方找到 [搜尋流量分析] 選項。

![][1]

選取此選項，即會開啟新的刀鋒視窗。將 [狀態] 變更為 [開啟]、選取您的資料將複製到其中的 Azure 儲存體帳戶，然後選擇要複製的資料：記錄檔、度量或兩者。建議您複製記錄檔和度量。

![][2]


> [AZURE.IMPORTANT]儲存體帳戶必須與您的搜尋服務位於相同的區域和訂用帳戶中。
> 
> 標準收費適用此儲存體帳戶

### 2\.使用 PowerShell ###

您也可以執行下列 PowerShell Cmdlet 來啟用此功能。

```PowerShell
Login-AzureRmAccount
Set-AzureRmDiagnosticSetting -ResourceId <SearchService ResourceId> StorageAccountId <StorageAccount ResourceId> -Enabled $true
```

-   **SearchService ResourceId**：```
/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Search/searchServices/<searchServiceName>
```

 
-  **StorageAccount ResourceId**：您可以在入口網站的 [設定]-> [屬性]-> [ResourceId] 中找到它 ```
New: /subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>
OR
Classic: /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ClassicStorage/storageAccounts/<storageAccountName>
```

----------

啟用之後，資料將會在 5-10 分鐘內開始傳送至您的儲存體帳戶。您將可在 Blob 儲存體中找到 2 個新容器：

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


## 了解資料 ##

資料會以 JSON 格式儲存於 Azure 儲存體 Blob 中。

每個容器每小時會有一個 Blob。
  
範例路徑：`resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### 記錄檔 ###

記錄檔 Blob 包含您的搜尋服務流量記錄檔。

每個 Blob 會一個名為**記錄**的根物件，其中包含記錄檔物件的陣列

####記錄檔結構描述####

名稱 |類型 |範例 |注意事項 
------|-----|----|-----
分析 |datetime |"2015-12-07T00:00:43.6872559Z" |作業的時間戳記
resourceId |字串 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |您的 ResourceId
operationName |字串 |"Query.Search" |作業的名稱
operationVersion |字串 |"2015-02-28"|使用的 api-version
category |字串 |"OperationLogs" |常數 
resultType |字串 |"Success" |可能的值：Success 或 Failure 
resultSignature |int |200 |HTTP 結果碼 
durationMS |int |50 |作業的持續時間 (以毫秒為單位) 
properties |物件 |請參閱下方 |包含作業特定資料的物件

####屬性結構描述####

|名稱 |類型 |範例 |注意事項|
|------|-----|----|-----|
|說明|字串 |"GET /indexes('content')/docs" |作業的端點 |
|查詢 |字串 |"?search=AzureSearch&$count=true&api-version=2015-02-28" |查詢參數 |
|文件 |int |42 |處理的文件數目|
|IndexName |字串 |"testindex"|與作業相關聯的索引名稱 |

### 度量 ###

度量 Blob 包含您搜尋服務的彙總值。每個檔案會一個名為**記錄**的根物件，其中包含度量物件的陣列

可用的度量：

- 延遲

####度量結構描述####

|名稱 |類型 |範例 |注意事項|
|------|-----|----|-----|
|resourceId |字串 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |您的資源識別碼 |
|metricName |字串 |"Latency" |度量的名稱 |
|分析|datetime |"2015-12-07T00:00:43.6872559Z" |作業的時間戳記 |
|average |int |64|度量時間間隔中原始範例的平均值 |
|minimum |int |37 |度量時間間隔中原始範例的最小值 |
|maximum |int |78 |度量時間間隔中原始範例的最大值 |
|total |int |258 |度量時間間隔中原始範例的總和值 |
|計數 |int |4 |用來產生度量的原始樣本數 |
|timegrain |字串 |"PT1M" |採用 ISO 8601 的度量時間粒紋|

## 分析您的資料 ##

資料會在您的儲存體帳戶中，而我們鼓勵您以最適合您狀況的方式來探索此資料。

我們建議您一開始使用 [Power BI Desktop](https://powerbi.microsoft.com/zh-TW/desktop) 來探索和視覺化資料。您可以輕鬆地連線到您的 Azure 儲存體帳戶，並快速開始分析您的資料。

請查看下列範例查詢，讓您在 Power BI Desktop 中建立自己的報表。

### 範例的指示 ###

1. 開啟新的 PowerBI Desktop 報表
2. 選取 [取得資料] -> [更多...]

	![][3]

3. 依序選取 [Microsoft Azure Blob 儲存體] 和 [連接]

	![][4]

4. 輸入您儲存體帳戶的名稱和帳戶金鑰
5. 使用滑鼠右鍵按一下 insight-logs-operationlogs，然後選取 [載入]
6. 隨即會開啟 [查詢編輯器]。現在依序選取 [檢視]-> [進階編輯器] 來開啟 [進階編輯器]

	![][5]

7. 保留前 2 行，並使用下列查詢來取代其餘部分：

	>     #"insights-logs-operationlogs" = Source{[Name="insights-logs-operationlogs"]}[Data],
	>     #"Sorted Rows" = Table.Sort(#"insights-logs-operationlogs",{{"Date modified", Order.Descending}}),
	>     #"Kept First Rows" = Table.FirstN(#"Sorted Rows",288),
	>     #"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
	>     #"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
	>     #"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
	>     #"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
	>     #"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}, {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}),
	>     #"Expanded properties" = Table.ExpandRecordColumn(#"Expanded records1", "properties", {"Description", "Query", "IndexName", "Documents"}, {"Description", "Query", "IndexName", "Documents"}),
	>     #"Renamed Columns" = Table.RenameColumns(#"Expanded properties",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"operationName", "OperationName"}, {"operationVersion", "OperationVersion"}, {"category", "Category"}, {"resultType", "ResultType"}, {"resultSignature", "ResultSignature"}, {"durationMS", "Duration"}}),
	>     #"Added Custom2" = Table.AddColumn(#"Renamed Columns", "QueryParameters", each Uri.Parts("http://tmp" & [Query])),
	>     #"Expanded QueryParameters" = Table.ExpandRecordColumn(#"Added Custom2", "QueryParameters", {"Query"}, {"Query.1"}),
	>     #"Expanded Query.1" = Table.ExpandRecordColumn(#"Expanded QueryParameters", "Query.1", {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}, {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}),
	>     #"Removed Columns1" = Table.RemoveColumns(#"Expanded Query.1",{"OperationVersion"}),
	>     #"Changed Type" = Table.TransformColumnTypes(#"Removed Columns1",{{"Datetime", type datetimezone}, {"ResourceId", type text}, {"OperationName", type text}, {"Category", type text}, {"ResultType", type text}, {"ResultSignature", type text}, {"Duration", Int64.Type}, {"Description", type text}, {"Query", type text}, {"IndexName", type text}, {"Documents", Int64.Type}, {"search", type text}, {"$skip", Int64.Type}, {"$top", Int64.Type}, {"$count", type logical}, {"api-version", type text}, {"searchMode", type text}, {"$filter", type text}}),
	>     #"Inserted Date" = Table.AddColumn(#"Changed Type", "Date", each DateTime.Date([Datetime]), type date),
	>     #"Duplicated Column" = Table.DuplicateColumn(#"Inserted Date", "ResourceId", "Copy of ResourceId"),
	>     #"Split Column by Delimiter" = Table.SplitColumn(#"Duplicated Column","Copy of ResourceId",Splitter.SplitTextByEachDelimiter({"/"}, null, true),{"Copy of ResourceId.1", "Copy of ResourceId.2"}),
	>     #"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Copy of ResourceId.1", type text}, {"Copy of ResourceId.2", type text}}),
	>     #"Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Copy of ResourceId.1"}),
	>     #"Renamed Columns1" = Table.RenameColumns(#"Removed Columns2",{{"Copy of ResourceId.2", "ServiceName"}}),
	>     #"Lowercased Text" = Table.TransformColumns(#"Renamed Columns1",{{"ServiceName", Text.Lower}}),
	>     #"Added Custom" = Table.AddColumn(#"Lowercased Text", "DaysFromToday", each Duration.Days(DateTimeZone.UtcNow() - [Datetime])),
	>     #"Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"DaysFromToday", Int64.Type}})
	>     in
	>     #"Changed Type2"

8. 按一下 [完成]，然後選取 [常用] 索引標籤中的 [關閉並套用]。

9. 現在準備好取用您的資料。請繼續進行，並建立一些[視覺效果](https://powerbi.microsoft.com/zh-TW/documentation/powerbi-desktop-report-view/)。

## 後續步驟 ##

深入了解搜尋語法和查詢參數。如需詳細資訊，請參閱[搜尋文件 (Azure 搜尋服務 REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx)。

深入了解如何建立令人讚嘆的報告。如需詳細資訊，請參閱[開始使用 Power BI Desktop](https://powerbi.microsoft.com/zh-TW/documentation/powerbi-desktop-getting-started/)

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/GetData.png
[4]: ./media/search-traffic-analytics/BlobStorage.png
[5]: ./media/search-traffic-analytics/QueryEditor.png

<!---HONumber=AcomDC_1217_2015-->