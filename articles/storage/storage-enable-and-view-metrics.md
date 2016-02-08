<properties 
	pageTitle="在 Azure 入口網站中啟用儲存體計量功能 | Microsoft Azure" 
	description="如何啟用 Blob、佇列、表格和檔案服務的儲存體度量" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="carmonm" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/01/2015" 
	ms.author="tamram"/>

# 啟用 Azure 儲存體計量和檢視計量資料

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## 概觀

根據預設，儲存體服務未啟用 [儲存體度量]。您可以透過 [Azure 入口網站](https://portal.azure.com)或 Windows PowerShell 啟用監視功能，或透過儲存體用戶端程式庫以程式設計方式啟用。

當您啟用儲存體度量時，必須選擇資料的保留期限：這段期間會決定儲存體服務用來保存度量的時間長度，以及該空間儲存它們所需的費用。一般而言，您應該針對每分鐘度量使用比每小時度量還短的保留期限，因為每分鐘度量明顯需要額外的空間。您應該選擇保留期限，讓您擁有足夠的時間來分析資料，並下載任何您想要保留以供離線分析或報告使用的度量。請記住，您還是必須支付從儲存體帳戶下載度量資料的費用。

## 如何使用 Azure 入口網站啟用計量功能

依照下列步驟在 [Azure 入口網站](https://portal.azure.com)中啟用計量功能：

1. 瀏覽至儲存體帳戶。 
1. 開啟 [**設定**] 刀鋒視窗，然後選取 [**診斷**]。
1. 確定 [**狀態**] 設為 [**開啟**]。
1. 選取您要監視之服務的計量。
2. 指定保留原則，以表示要保留計量與記錄資料的時間。

請注意，[Azure 入口網站](https://portal.azure.com)目前無法讓您在儲存體帳戶中設定每分鐘計量功能；您必須使用 PowerShell 或以程式設計方式啟用每分鐘計量功能。

## 如何使用 PowerShell 啟用計量功能

您可以在本機電腦上使用 PowerShell 來設定儲存體帳戶中的儲存體度量，做法是使用 Azure PowerShell Cmdlet Get-AzureStorageServiceMetricsProperty 來擷取目前的設定，並使用 Cmdlet Set-AzureStorageServiceMetricsProperty 來變更目前的設定。

控制儲存體度量的 Cmdlet 會使用下列參數：

- MetricsType：可能值為 Hour 和 Minute。

- ServiceType：可能值為 Blob、Queue 及 Table。

- MetricsLevel：可能值為 None、服務，以及 ServiceAndApi。

例如，下列命令會在您的預設儲存體帳戶中，為 Blob 服務開啟每分鐘計量功能，並將保留期間設為五天：

`Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`

下列命令會擷取您預設儲存體帳戶中 Blob 服務的目前每小時度量層級和保留天數：

`Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob`

如需如何設定 Azure PowerShell Cmdlet 以使用您的 Azure 訂用帳戶，以及如何選取要使用的預設儲存體帳戶的相關資訊，請參閱：[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)。

## 如何以程式設計方式啟用儲存體度量

下列 C# 程式碼片段示範如何使用 .NET 的儲存體用戶端程式庫，為 Blob 服務啟用計量和記錄功能：

	// Parse connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Enable Storage Analytics logging and set retention policy to 10 days. 
    ServiceProperties properties = new ServiceProperties();
    properties.Logging.LoggingOperations = LoggingOperations.All;
    properties.Logging.RetentionDays = 10;
    properties.Logging.Version = "1.0";

    // Configure service properties for metrics. Both metrics and logging must be set at the same time.
    properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.HourMetrics.RetentionDays = 10;
    properties.HourMetrics.Version = "1.0";

    properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.MinuteMetrics.RetentionDays = 10;
    properties.MinuteMetrics.Version = "1.0";

    // Set the default service version to be used for anonymous requests.
    properties.DefaultServiceVersion = "2015-04-05";

    // Set the service properties.
    blobClient.SetServiceProperties(properties);

    
## 檢視儲存體度量

在您設定儲存體分析計量監視您的儲存體帳戶後，儲存體分析會將計量記錄在您儲存體帳戶中一組已知資料表中。您可以在 [Azure 入口網站](https://portal.azure.com)中將圖表設定為檢視每小時計量：

1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的儲存體帳戶。
2. 在 [**監視**] 區段中，按一下 [**新增磚**] 以新增圖表。在 [**磚庫**] 中選取您要檢視的計量，並將它拖曳至 [**監視**] 區段。
3. 若要編輯要在圖表中顯示的計量，請按一下 [**編輯**] 連結。您可以透過選取或取消選取計量，來新增或移除個別的計量。
4. 編輯完計量後，按一下 [**儲存**]。

如果要下載長期儲存體的度量，或在本機加以分析，您必須使用工具或撰寫程式碼來讀取資料表。您必須下載每分鐘度量以進行分析。如果您在儲存體帳戶中列出所有資料表，則資料表就不會出現，但您可以直接依名稱存取它們。有許多協力廠商儲存體瀏覽工具可以感知這些資料表，讓您能夠直接檢視它們 (如需可用工具清單，請參閱部落格文章 [Microsoft Azure 儲存體總管](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx))。

### 每小時度量
- $MetricsHourPrimaryTransactionsBlob
- $MetricsHourPrimaryTransactionsTable
- $MetricsHourPrimaryTransactionsQueue

### 每分鐘度量
- $MetricsMinutePrimaryTransactionsBlob
- $MetricsMinutePrimaryTransactionsTable
- $MetricsMinutePrimaryTransactionsQueue

### 容量
- $MetricsCapacityBlob

您可以在[儲存體分析度量資料表結構描述](https://msdn.microsoft.com/library/azure/hh343264.aspx)上找到這些資料表之結構描述的完整詳細資料。下列資料列範例只會顯示可用的資料行子集，但可說明儲存體度量儲存這些度量資訊之方式的一些重要功能：

| PartitionKey | RowKey | Timestamp | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Availability | AverageE2ELatency | AverageServerLatency | PercentSuccess |
|---------------|:------------------:|-----------------------------:|---------------|-----------------------|--------------|-------------|--------------|-------------------|----------------------|----------------|
| 20140522T1100 | user;All | 2014-05-22T11:01:16.7650250Z | 7 | 7 | 4003 | 46801 | 100 | 104\.4286 | 6\.857143 | 100 |
| 20140522T1100 | user;QueryEntities | 2014-05-22T11:01:16.7640250Z | 5 | 5 | 2694 | 45951 | 100 | 143\.8 | 7\.8 | 100 |
| 20140522T1100 | user;QueryEntity | 2014-05-22T11:01:16.7650250Z | 1 | 1 | 538 | 633 | 100 | 3 | 3 | 100 |
| 20140522T1100 | user;UpdateEntity | 2014-05-22T11:01:16.7650250Z | 1 | 1 | 771 | 217 | 100 | 9 | 6 | 100 |

在這個每分鐘度量資料範例中，資料分割索引鍵會在每分鐘解析中使用時間。資料列索引鍵會識別資料列中儲存的資訊類型，而這是由兩部分的資訊 (存取類型及要求類型) 所組成：

- 存取類型是使用者或系統，其中使用者是指對儲存體服務所提出的所有使用者要求，而系統是指對儲存體分析所提出的要求。

- 要求類型就是全部 (在此情況下它是摘要行)，或者它會識別特定的 API，例如 QueryEntity 或 UpdateEntity。


上述範例資料會為單一分鐘 (從上午 11:00 開始) 顯示所有記錄，因此 QueryEntities 要求的數目加上 QueryEntity 要求的數目再加上 UpdateEntity 要求的數目最多七個，也就是 user:All 資料列上顯示的總數。同樣地，您可以藉由計算 ((143.8 * 5) + 3 + 9)/7，在 user:All 資料列上衍生平均的端對端延遲 104.4286。

您應該考慮在 [Azure 入口網站](https://portal.azure.com)的 [監視] 頁面上設定警示，讓儲存體計量可自動通知您儲存體服務行為的所有重要變更。如果您使用儲存體總管工具下載這個計量資料 (以分隔的格式)，就可以使用 Microsoft Excel 分析資料。如需可用儲存體總管工具的清單，請參閱部落格文章 [Microsoft Azure 儲存體總管](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)。



## 以程式設計方式存取度量資料

下列清單顯示 C# 程式碼範例，其會針對某個分鐘範圍存取每分鐘度量，並將結果顯示在主控台視窗中。它會使用 Azure 儲存體程式庫第 4 版，其中包含可簡化存取儲存體中之度量資料表的 CloudAnalyticsClient 類別。

    private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
    {
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    
    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
    Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
    var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
    var t = analyticsClient.GetMinuteMetricsTable(service);
    var opContext = new OperationContext();
    var query =
    from entity in metricsQuery
    // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
    // because they are calculated fields in the MetricsEntity class.
    // The PartitionKey identifies the DataTime of the metrics.
    where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0 
    select entity;
    
    // Filter on "user" transactions after fetching the metrics from Table Storage.
    // (StartsWith is not supported using LINQ with Azure table storage)
    var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
    var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
    Console.WriteLine(resultString);
    }
    }
    
    private static string MetricsString(MetricsEntity entity, OperationContext opContext)
    {
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
    string.Format("Time: {0}, ", entity.Time) +
    string.Format("AccessType: {0}, ", entity.AccessType) +
    string.Format("TransactionType: {0}, ", entity.TransactionType) +
    string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
    
    }




## 當您啟用儲存體度量時，會產生哪些費用？

寫入要求以建立度量的資料表實體，會以適用於所有 Azure 儲存體作業的標準費率來收費。

用戶端對於度量資料的讀取和刪除要求也會以標準費率來計費。如果您已設定資料保留原則，就不需要在 Azure 儲存體刪除舊的度量資料時付費。不過，如果您刪除分析資料，則您的帳戶必須支付刪除作業的費用。

度量資料表所使用的容量也會列入計費：您可以使用下列項目來估計儲存計量資料所使用的容量大小：

- 如果服務每小時會使用每個服務中的每一種 API，若您已啟用服務和 API 層級摘要，則每小時大約有 148 KB 的資料將儲存於度量交易資料表中。

- 如果服務每小時會使用每個服務中的每一種 API，若您只啟用服務層級摘要，則每小時大約有 12 KB 的資料將儲存於度量交易資料表中。

- 適用於 Blob 的容量資料表每天都會新增兩個資料列 (前提是使用者已選擇記錄檔)：也就是說，這個資料表的大小每天最多大約會增加 300 個位元組。

## 後續步驟：
[啟用儲存體記錄和存取記錄檔資料](https://msdn.microsoft.com/library/dn782840.aspx)
 

<!---HONumber=AcomDC_0128_2016-->