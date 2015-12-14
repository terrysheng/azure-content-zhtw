<properties
   pageTitle="在 Azure 診斷中使用效能計數器 |Microsoft Azure"
   description="在 Azure 雲端服務或虛擬機器中使用效能計數器來找出瓶頸和調整效能。"
   services="cloud-services"
   documentationCenter=".net"
   authors="rboucher"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/25/2015"
   ms.author="robb" />

# 在 Azure 應用程式中建立及使用效能計數器

本文說明效能計數器的優點，以及如何將效能計數器放入 Azure 應用程式中。您可以使用它們來收集資料、找出瓶頸，以及調整系統和應用程式效能。

適用於 Windows Server、IIS 和 ASP.NET 的效能計數器也可用來收集資料，以判斷 Azure Web 角色、背景工作角色和虛擬機器的健康情況。您也可以建立和使用自訂效能計數器。

您可以檢查效能計數器資料：1.直接在應用程式主機上，使用遠端桌面存取效能監視器工具。2.透過使用 Azure Management Pack 3 的 System Center Operations Manager。透過其他監視工具，存取已傳輸至 Azure 儲存體的診斷資料。如需詳細資訊，請參閱[在 Azure 儲存體中儲存和檢視診斷資料](https://msdn.microsoft.com/library/azure/hh411534.aspx)。

如需在 [Azure 傳統入口網站](http://manage.azure.com/)中監視應用程式效能的詳細資訊，請參閱[如何監視雲端服務](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/)。

如需建立記錄及追蹤策略、使用診斷和其他技術進行疑難排解，以及將 Azure 應用程式最佳化的其他深入指引，請參閱[開發 Azure 應用程式的疑難排解最佳作法](https://msdn.microsoft.com/library/azure/hh771389.aspx) (英文)。


## 啟用效能計數器監視

預設不會啟用效能計數器。您的應用程式或啟動工作必須修改預設診斷代理程式組態，以納入您想要針對每個角色執行個體監視的效能計數器。

### Microsoft Azure 可用的效能計數器

Azure 為 Windows Server、IIS 和 ASP.NET 堆疊提供了一小組可用的效能計數器。下表列出一些對 Azure 應用程式特別實用的效能計數器。

|計數器類別：物件 (執行個體)|計數器名稱 |參考|
|---|---|---|
|.NET CLR 例外狀況 (_全域_)|擲回的例外狀況數目 / 秒 |例外狀況效能計數器|
|.NET CLR 記憶體 (_全域_) |記憶體回收中的時間 % |記憶體效能計數器|
|ASP.NET |應用程式重新啟動 |ASP.NET 的效能計數器|
|ASP.NET |要求執行時間 |ASP.NET 的效能計數器|
|ASP.NET |中斷連接的要求 |ASP.NET 的效能計數器|
|ASP.NET |背景工作角色處理序重新啟動 |ASP.NET 的效能計數器|
|ASP.NET 應用程式 (__總計__)|要求總數 |ASP.NET 的效能計數器|
|ASP.NET 應用程式 (__總計__)|要求/秒 |ASP.NET 的效能計數器|
|ASP.NET v4.0.30319 |要求執行時間 |ASP.NET 的效能計數器|
|ASP.NET v4.0.30319 |要求等候時間 |ASP.NET 的效能計數器|
|ASP.NET v4.0.30319 |目前的要求 |ASP.NET 的效能計數器|
|ASP.NET v4.0.30319 |已排入佇列的要求 |ASP.NET 的效能計數器|
|ASP.NET v4.0.30319 |遭拒絕的要求 |ASP.NET 的效能計數器|
|記憶體 |可用的 MB |記憶體效能計數器|
|記憶體 |認可的位元組 |記憶體效能計數器|
|Processor(\_Total) |處理器時間 % |ASP.NET 的效能計數器| |TCPv4 |連線失敗 |TCP 物件| |TCPv4 |建立的連線 |TCP 物件| |TCPv4 |重設的連線 |TCP 物件| |TCPv4 |傳送的區段/秒 |TCP 物件| |網路介面(*) |接收的位元組/秒 |網路介面物件| |網路介面(*) |傳送的位元組/秒 |網路介面物件| |網路介面 (Microsoft 虛擬機器匯流排網路介面卡 \_2)|接收的位元組/秒|網路介面物件| |網路介面 (Microsoft 虛擬機器匯流排網路介面卡 \_2)|傳送的位元組/秒|網路介面物件| |網路介面 (Microsoft 虛擬機器匯流排網路介面卡 \_2)|位元組總數/秒|網路介面物件|

## 建立自訂效能計數器並加入您的應用程式中

Azure 支援建立和修改 Web 角色和背景工作角色的自訂效能計數器。計數器可用來追蹤和監視應用程式特有的行為。您可以用更高權限，建立和刪除啟動工作、Web 角色或背景工作角色的自訂效能計數器類別和規範。

>[AZURE.NOTE]必須擁有更高權限，才能執行對自訂效能計數器進行變更的程式碼。如果程式碼屬於 Web 角色或背景工作角色，角色必須在 ServiceDefinition.csdef 檔案中包含標記 <Runtime executionContext="elevated" />，才能正確地初始化角色。

您可以使用診斷代理程式，將自訂效能計數器資料傳送到 Azure 儲存體。

標準效能計數器資料是由 Azure 處理序產生。自訂效能計數器資料必須由 Web 角色或背景工作角色應用程式建立。如需自訂效能計數器可儲存的資料類型的相關資訊，請參閱[效能計數器類型](https://msdn.microsoft.com/library/z573042h.aspx)。如需在 Web 角色中建立及設定自訂效能計數器資料的範例，請參閱 [PerformanceCounters 範例](http://code.msdn.microsoft.com/azure/)。

## 儲存和檢視效能計數器資料

Azure 會快取效能計數器資料與其他診斷資訊。當角色執行個體正在執行時，使用遠端桌面存取來檢視效能監視器等工具，此資料即可供遠端監視。若要在角色執行個體外部保存資料，則診斷代理程式必須將資料傳輸到 Azure 儲存體。在診斷代理程式中可以設定快取的效能計數器資料的大小限制，或也可以將它設定為所有診斷資料之共用限制的一部分。如需有關設定緩衝區大小的詳細資訊，請參閱 [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) 和 [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx)。如需設定診斷代理程式以將資料傳輸到儲存體帳戶的概觀，請參閱[在 Azure 儲存體中儲存和檢視診斷資料](https://msdn.microsoft.com/library/azure/hh411534.aspx)。

每個設定的效能計數器執行個體都會以指定的取樣速率進行記錄，而取樣的資料會經由排程的傳輸要求或隨選傳輸要求傳輸到儲存體帳戶。可將自動傳輸的頻率排程為每分鐘一次。診斷代理程式所傳輸的效能計數器資料會儲存在儲存體帳戶的 WADPerformanceCountersTable 資料表中。使用標準 Azure 儲存體 API 方法即可存取和查詢此資料表。如需查詢及顯示 WADPerformanceCountersTable 資料表中效能計數器資料的範例，請參閱 [Microsoft Azure PerformanceCounters 範例](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9)。

>[AZURE.NOTE]視診斷代理程式的傳輸頻率和佇列延遲研定，儲存體帳戶中的最新效能計數器資料可能會過期幾分鐘。

## 使用診斷組態檔來啟用效能計數器

使用下列程序在 Azure 應用程式中啟用效能計數器。

## 必要條件

本節假設您已將診斷監視器匯入應用程式中，並已將診斷組態檔加入 Visual Studio 方案中 (SDK 2.4 和以下版本為 diagnostics.wadcfg，而 SDK 2.5 和以上版本為 diagnostics.wadcfgx)。如需詳細資訊，請參閱[在 Azure 雲端服務和虛擬機器中啟用診斷](./cloud-services-dotnet-diagnostics.md)中的步驟 1 和 2。

## 步驟 1：收集和儲存來自效能計數器的資料

將診斷檔案加入 Visual Studio 方案中後，您即可在 Azure 應用程式中進行效能計數器資料的收集和儲存設定。將效能計數器加入診斷檔案中，即可完成此動作。首先會在執行個體上收集診斷資料，包括效能計數器在內。這項資料後續會持續存留在 Azure 資料表服務的 WADPerformanceCountersTable 資料表中，因此您也須在應用程式中指定儲存帳號。如果您要使用計算模擬器在本機中測試應用程式，您也可以在儲存模擬器中本機儲存診斷資料。在儲存診斷資料之前，您必須先移至 [Azure 傳統入口網站](http://manage.windowsazure.com/)，並建立儲存體帳戶。最佳作法是，將儲存帳號設定在與 Azure 應用程式相同的地理位置，以免支付外部頻寬成本，同時降低延遲。

### 將效能計數器加入診斷檔案中

有許多計數器可供您使用。下列範例說明幾個建議用於 Web 和背景工作角色監視的效能計數器。

開啟診斷檔案 (SDK 2.4 和以下版本為 diagnostics.wadcfg，而 SDK 2.5 和以上版本為 diagnostics.wadcfgx)，並將下列內容加入 DiagnosticMonitorConfiguration 元素中：

```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
       <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)# of marshalling" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)# Bytes in all Heaps" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
    </PerformanceCounters>
```

bufferQuotaInMB 屬性會指定可用於資料收集類型 (Azure 記錄、IIS 記錄等) 的檔案系統儲存體數量上限。預設值為 0。到達此配額時，即會在新增新資料時刪除最舊的資料。所有 bufferQuotaInMB 屬性 (property) 的總和必須大於 OverallQuotaInMB 屬性 (attribute) 的值。如需判斷收集診斷資料將需要多少儲存體的詳細討論，請參閱[開發 Azure 應用程式的疑難排解最佳作法](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx) (英文) 中的「設定 WAD」一節。

scheduledTransferPeriod 屬性會指定排程的資料傳輸所採用的間隔 (四捨五入至最接近的分鐘)。下列範例將此值設為 PT30M (30 分鐘)。將傳輸期間設為較小的值 (例如 1 分鐘)，將對生產環境中的應用程式效能造成不良影響，但在執行測試時可能有助於診斷的快速運作。排程的傳輸期間應大小適中，以確保執行個體上的診斷資料不會被覆寫，同時不會對應用程式的效能造成影響。

counterSpecifier 屬性會指定要收集的效能計數器。sampleRate 屬性會指定對效能計數器取樣的頻率，在本例中為 30 秒。

加入您要收集的效能計數器後，請將變更儲存至診斷檔案。接著，您必須指定將持續保存診斷資料的儲存帳號。

### 指定儲存帳號

若要讓您的診斷資訊持續存留於 Azure 儲存帳號中，您必須在服務組態檔 (ServiceConfiguration.cscfg) 中指定連接字串。

在 Azure SDK 2.5 中，儲存體帳戶可在 diagnostics.wadcfgx 檔案中指定。

>[AZURE.NOTE]這些指示只會套用至 Azure SDK 2.4 和以下版本。在 Azure SDK 2.5 中，儲存體帳戶可在 diagnostics.wadcfgx 檔案中指定。

若要設定連接字串：

1. 使用您慣用的文字編輯器開啟 ServiceConfiguration.Cloud.cscfg 檔案，然後設定儲存體的連接字串。*AccountName* 和 *AccountKey* 值可在 Azure 傳統入口網站的儲存體帳戶儀表板中找到 (位於[管理金鑰] 下)。

    ```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. 儲存 ServiceConfiguration.Cloud.cscfg 檔案。

3. 開啟 ServiceConfiguration.Local.cscfg 檔案，驗證 UseDevelopmentStorage 是否設為 true。

    ```
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
現在，連接字串已設定，您的應用程式將可在部署時，將診斷資料持續存留至您的儲存帳號中。
4. 儲存並建置您的專案，然後部署應用程式。

## 步驟 2：(選擇性) 建立自訂效能計數器

除了預先定義的效能計數器以外，您也可以新增自訂效能計數器，以監視 Web 或背景工作角色。自訂效能計數器可用來追蹤及監視應用程式特定行為，並可藉由提高的權限在啟動工作、Web 角色或背景工作角色中建立或刪除。

Azure 診斷代理程式會在啟動一分鐘後從 .wadcfg 檔案重新整理效能計數器組態。如果您在 OnStart 方法中建立自訂效能計數器，而且您的啟動工作在一分鐘以後執行，則您的自訂效能計數器在 Azure 診斷代理程式嘗試載入時尚未建立。在此情況下，您會看到 Azure 診斷正確地擷取自訂效能計數器以外的所有診斷資料。若要解決此問題，請在啟動工作中建立效能計數器，或在建立效能計數器之後，將部分啟動工作移到 OnStart 方法。

執行下列步驟，可建立名為 "\\MyCustomCounterCategory\\MyButton1Counter" 的簡易自訂效能計數器：

1. 開啟應用程式的服務定義檔 (CSDEF)。
2. 將 Runtime 元素新增至 WebRole 或 WorkerRole 元素，使其可在提升的權限下執行：

    ```
    <RuntimeexecutionContext="elevated"/>
    ```
3. 儲存檔案。
4. 開啟診斷檔案 (SDK 2.4 和以下版本為 diagnostics.wadcfg，而 SDK 2.5 和以上版本為 diagnostics.wadcfgx)，並將下列內容加入 DiagnosticMonitorConfiguration 中 

    ```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
     <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. 儲存檔案。
6. 在您角色的 OnStart 方法中建立自訂效能計數器類別，然後叫用 base.OnStart。下列 C# 範例會建立自訂類別 (如果尚不存在)：

    ```
    public override bool OnStart()
    {
    if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
    {
       CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

       // add a counter tracking user button1 clicks
       CounterCreationData operationTotal1 = new CounterCreationData();
       operationTotal1.CounterName = "MyButton1Counter";
       operationTotal1.CounterHelp = "My Custom Counter for Button1";
       operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
       counterCollection.Add(operationTotal1);

       PerformanceCounterCategory.Create(
         "MyCustomCounterCategory",
         "My Custom Counter Category",
         PerformanceCounterCategoryType.SingleInstance, counterCollection);

       Trace.WriteLine("Custom counter category created.");
    }
    else{
       Trace.WriteLine("Custom counter category already exists.");
    }

    return base.OnStart();
    }
    ```
7. 更新應用程式內的計數器。下列範例會更新 Button1\_Click 事件的自訂效能計數器：

    ```
    protected void Button1_Click(object sender, EventArgs e)
        {
         PerformanceCounter button1Counter = new PerformanceCounter(
           "MyCustomCounterCategory",
           "MyButton1Counter",
           string.Empty,
           false);
         button1Counter.Increment();
        this.Button1.Text = "Button 1 count: " +
           button1Counter.RawValue.ToString();
        }
    ```
8. 儲存檔案。  

Azure 診斷監視器現在即會收集自訂效能計數器資料。

## 步驟 3：查詢效能計數器資料

當應用程式完成部署並開始執行後，診斷監視器即會開始收集效能計數器，並將資料存留至 Azure 儲存體。您可以使用 Visual Studio 中的伺服器總管、[Azure 儲存體總管](http://azurestorageexplorer.codeplex.com/)或 Cerebrata 提供的 [Azure 診斷管理員](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx)等工具，檢視 WADPerformanceCountersTable 資料表中的效能計數器資料。您也可以透過程式設計，使用 [C#](../storage/storage-dotnet-how-to-use-tables.d)、[Java](../storage/storage-java-how-to-use-table-storage.md)、[Node.js](../storage/storage-nodejs-how-to-use-table-storage.md)、[Python](../storage/storage-python-how-to-use-table-storage.md)、[Ruby](../storage/storage-ruby-how-to-use-table-storage.md) 或 [PHP](../storage/storage-php-how-to-use-table-storage.md) 來查詢表格服務。

下列 C# 範例將說明對 WADPerformanceCountersTable 資料表的簡易查詢，並將診斷資料儲存至 CSV 檔案。效能計數器儲存至 CSV 檔案後，您可以使用 Microsoft Excel 或其他工具的圖表功能，將資料視覺化。請務必為 Azure SDK for .NET (2012 年 10 月或更新版本) 隨附的 Microsoft.WindowsAzure.Storage.dll 新增參考。此組件會安裝在 %Program Files%\\Microsoft SDKs\\Microsoft Azure.NET SDK\\version-num\\ref\\ 目錄中。

```
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ...

    // Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
    // you store your connection string using the Microsoft Azure service configuration
    // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
    // to retrieve your storage connection string.  If you're not using Cloud Services, it's
    // recommended that you store the connection string in your web.config or app.config file.
    // Use the ConfigurationManager type to retrieve your storage connection string.

    string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

    // Get a reference to the storage account using the connection string.  You can also use the development
    // storage account (Storage Emulator) for local debugging.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
    CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

    // Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
    TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
       .Where(
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
          TableOperators.And,
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
          TableOperators.And,
          TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
          )
       )
    );

    // Execute the table query.
    IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

    // Process the query results and build a CSV file.
    StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

    foreach (PerformanceCountersEntity entity in result)
    {
       sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
          + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
    }

    StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
    sw.Write(sb.ToString());
    sw.Close();
```

實體會使用衍生自 **TableEntity** 的自訂類別來對應至 C# 物件。下列程式碼會定義一個實體類別，用以代表 **WADPerformanceCountersTable** 資料表中的效能計數器。


    public class PerformanceCountersEntity : TableEntity
    {
       public long EventTickCount { get; set; }
       public string DeploymentId { get; set; }
       public string Role { get; set; }
       public string RoleInstance { get; set; }
       public string CounterName { get; set; }
       public double CounterValue { get; set; }
    }


## 後續步驟

了解收集效能計數器的基礎概念之後，請參考下列連結以了解如何實作更複雜的疑難排解案例。

[開發 Azure 應用程式的疑難排解最佳作法](https://msdn.microsoft.com/library/azure/hh771389.aspx)

[如何監視雲端服務](./how-to-monitor-a-cloud-service.md)

<!---HONumber=AcomDC_1203_2015-->