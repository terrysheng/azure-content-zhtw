<properties linkid="dev-net-commons-tasks-profiling" urlDisplayName="Performance Profiling" pageTitle="Use Performance Counters in Azure (.NET)" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="Learn how to enable and collect data from performance counters in Azure applications. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Using performance counters in Azure" authors="ryanwi" solutions="" manager="" editor="" />

在 Azure 中使用效能計數器
=========================

您可以在 Azure 應用程式中使用效能計數器來收集資料，以利判斷系統瓶頸及微調系統和應用程式效能。適用於 Windows Server 2008、Windows Server 2012、IIS 和 ASP.NET 的效能計數器可用來收集資料，以判斷 Azure 應用程式的健康情況。

本主題將說明如何使用 diagnostics.wadcfg 組態檔在您的應用程式中啟用效能計數器。如需在 [Azure 管理入口網站](http://manage.windowsazure.com)中監視應用程式效能的相關資訊，請參閱[如何監視雲端服務](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-monitor-a-cloud-service/)。如需建立記錄及追蹤策略、使用診斷和其他技術進行疑難排解，以及將 Azure 應用程式最佳化的其他深入指引，請參閱[開發 Azure 應用程式的疑難排解最佳作法](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh771389.aspx) (英文)。

此工作包含下列步驟：

-   [必要條件](#prereqs)
-   [步驟 1：收集和儲存來自效能計數器的資料](#step1)
-   [步驟 2：(選用) 建立自訂效能計數器](#step2)
-   [步驟 3：查詢效能計數器資料](#step3)
-   [後續步驟](#nextsteps)
-   [其他資源](#additional)

必要條件
--------

本文假設您已將診斷監視器匯入應用程式中，並且已在 Visual Studio 方案中新增 diagnostics.wadcfg 組態檔。如需步驟 1 和 2 的詳細資訊，請參閱[啟用 Azure 中的診斷](https://www.windowsazure.com/en-us/develop/net/common-tasks/diagnostics/) (英文)。

步驟 1：收集和儲存來自效能計數器的資料
--------------------------------------

將 diagnostics.wadcfg 檔案新增至 Visual Studio 方案後，您即可在 Azure 應用程式中進行效能計數器資料的收集和儲存設定。將效能計數器新增至 diagnostics.wadcfg 檔案，即可完成此動作。首先會在執行個體上收集診斷資料，包括效能計數器在內。這項資料後續會持續存留在 Azure 資料表服務的 **WADPerformanceCountersTable** 資料表中，因此您也須在應用程式中指定儲存帳號。如果您要使用計算模擬器在本機中測試應用程式，您也可以在儲存模擬器中本機儲存診斷資料。在儲存診斷資料之前，您必須先移至 [Azure 管理入口網站](http://manage.windowsazure.com)，並建立儲存帳號。最佳作法是，將儲存帳號設定在與 Azure 應用程式相同的地理位置，以免支付外部頻寬成本，同時降低延遲。

### 將效能計數器新增至 diagnostics.wadcfg 檔案

有許多效能計數器可供您收集。下列範例說明幾個建議用於 Web 和背景工作角色監視的效能計數器。

開啟 diagnostics.wadcfg 檔案，並將下列內容新增至 **DiagnosticMonitorConfiguration** 元素：

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

     <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
        </PerformanceCounters>    

**bufferQuotaInMB** 屬性會指定可用於資料收集類型 (Azure 記錄、IIS 記錄等) 的檔案系統儲存體數量上限。預設值為 0。到達此配額時，即會在新增新資料時刪除最舊的資料。所有 **bufferQuotaInMB** 屬性 (property) 的總和必須大於 **OverallQuotaInMB** 屬性 (attribute) 的值。如需判斷收集診斷資料將需要多少儲存體的詳細討論，請參閱[開發 Azure 應用程式的疑難排解最佳作法](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh771389.aspx) (英文) 中的「設定 WAD」一節。

**scheduledTransferPeriod** 屬性會指定排程的資料傳輸所採用的間隔 (四捨五入至最接近的分鐘)。下列範例將此值設為 PT30M (30 分鐘)。將傳輸期間設為較小的值 (例如 1 分鐘)，將對生產環境中的應用程式效能造成不良影響，但在執行測試時可能有助於診斷的快速運作。排程的傳輸期間應大小適中，以確保執行個體上的診斷資料不會被覆寫，同時不會對應用程式的效能造成影響。

**counterSpecifier** 屬性會指定要收集的效能計數器。

**sampleRate** 屬性會指定對效能計數器取樣的頻率，在此案例中為 30 秒。

新增您要收集的效能計數器後，請將變更儲存至 diagnostics.wadcfg 檔案。接著，您必須指定將持續保存診斷資料的儲存帳號。

### 指定儲存帳號

若要讓您的診斷資訊持續存留於 Azure 儲存帳號中，您必須在服務組態檔 (ServiceConfiguration.cscfg) 中指定連接字串。適用於 Visual Studio 1.4 版 (2011 年 8 月) 和更新版本的 Azure 工具，允許您對本機與雲端使用不同的組態檔 (ServiceConfiguration.cscfg)。有多項服務組態可運用在診斷上，因為您可以使用儲存模擬器免費執行本機測試，同時為生產環境使用個別的組態檔。

若要設定連接字串：

1.  使用您慣用的文字編輯器開啟 ServiceConfiguration.Cloud.cscfg 檔案，然後設定儲存帳號的連接字串：

        <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
        </ConfigurationSettings>

    **AccountName** 和 **AccountKey** 值可在管理入口網站的儲存帳號儀表板中找到 (位於**[管理金鑰]** 下)。

2.  儲存 ServiceConfiguration.Cloud.cscfg 檔案。
3.  開啟 ServiceConfiguration.Local.cscfg 檔案，驗證 **UseDevelopmentStorage** 設為 true (預設值)。

        <ConfigurationSettings>

        </ConfigurationSettings>

    現在，連接字串已設定，您的應用程式將可在部署時，將診斷資料持續存留至您的儲存帳號中。

4.  儲存並建置您的專案，然後部署應用程式。

步驟 2：(選用) 建立自訂效能計數器
---------------------------------

除了預先定義的效能計數器以外，您也可以新增自訂效能計數器，以監視 Web 或背景工作角色。自訂效能計數器可用來追蹤及監視應用程式特定行為，並可藉由提高的權限在啟動工作、Web 角色或背景工作角色中建立或刪除。

執行下列步驟，可建立名為 "\\MyCustomCounterCategory\\MyButton1Counter" 的簡易自訂效能計數器：

1.  開啟應用程式的服務定義檔 (CSDEF)。
2.  將 **Runtime** 元素新增至 **WebRole** 或 **WorkerRole** 元素，使其可在提升的權限下執行：

        <Runtime executionContext="elevated" />

3.  儲存檔案。
4.  開啟 diagnostics.wadcfg 檔案，並將下列內容新增至 **DiagnosticMonitorConfiguration** 元素：

        <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
        <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
        </PerformanceCounters>        

5.  儲存檔案。
6.  在您角色的 **OnStart** 方法中建立自訂效能計數器類別，然後叫用 **base.OnStart**。下列 C\# 範例會建立自訂類別 (如果尚不存在)：

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

7.  更新應用程式內的計數器。下列範例會更新 **Button1\_Click** 事件的自訂效能計數器：

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

8.  儲存檔案。

前述步驟完成後，Azure 診斷監視器即會收集自訂效能計數器資料。

步驟 3：查詢效能計數器資料
--------------------------

當應用程式完成部署並開始執行後，診斷監視器即會開始收集效能計數器，並將資料存留至 Azure 儲存體。您可以使用 Visual Studio 中的 **[伺服器總管]**、[Azure 儲存體總管](http://azurestorageexplorer.codeplex.com/)或 Cerebrata 的 [Azure 診斷管理員](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx)等工具，檢視 **WADPerformanceCountersTable** 資料表中的效能計數器資料。您也可以透過程式設計，使用 [C\#][]、[Java](http://www.windowsazure.com/en-us/develop/java/how-to-guides/table-service/)、[Node.js][]、[Python](http://www.windowsazure.com/en-us/develop/python/how-to-guides/table-service/) 或 [PHP](http://www.windowsazure.com/en-us/develop/php/how-to-guides/table-service/) 來查詢資料表服務。

下列 C\# 範例將說明對 **WADPerformanceCountersTable** 資料表的簡易查詢，並將診斷資料儲存至 CSV 檔案。效能計數器儲存至 CSV 檔案後，您可以使用 Microsoft Excel 或其他工具的圖表功能，將資料視覺化。請務必為 Azure SDK for .NET (2012 年 10 月或更新版本) 隨附的 Microsoft.WindowsAzure.Storage.dll 新增參考。此組件會安裝在 %Program Files%\\Microsoft SDKs\\Windows Azure.NET SDK\\version-num\\ref\\ 目錄中。

     using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Table;

        ...

        // Get the connection string.When using Azure Cloud Services, it is recommended 
        // you store your connection string using the Azure service configuration
        // system (*.csdef and *.cscfg files).You can you use the CloudConfigurationManager type 
        // to retrieve your storage connection string.If you're not using Cloud Services, it's
        // recommended that you store the connection string in your web.config or app.config file.
        // Use the ConfigurationManager type to retrieve your storage connection string.
        
        string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
        //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;
            
        // Get a reference to the storage account using the connection string.You can also use the development storage account (Storage Emulator)
        // for local debugging.              
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

實體會使用衍生自 **TableEntity** 的自訂類別來對應至 C\# 物件。下列程式碼會定義一個實體類別，用以代表 **WADPerformanceCountersTable** 資料表中的效能計數器。

     public class PerformanceCountersEntity :TableEntity
        {
            public long EventTickCount { get; set; }
            public string DeploymentId { get; set; }
            public string Role { get; set; }
            public string RoleInstance { get; set; }
            public string CounterName { get; set; }
            public double CounterValue { get; set; }                
        }

後續步驟
--------

了解收集效能計數器的基礎概念之後，請參考下列連結以了解如何實作更複雜的疑難排解案例。

-   [開發 Azure 應用程式的疑難排解最佳作法](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh771389.aspx)
-   [如何監視雲端服務](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-monitor-a-cloud-service/)
-   [如何使用自動調整應用程式區塊](http://www.windowsazure.com/en-us/develop/net/how-to-guides/autoscaling/)
-   [建置彈性、恢復力強的雲端應用程式](http://msdn.microsoft.com/zh-tw/library/hh680949(PandP.50).aspx)

其他資源
--------

-   [啟用 Azure 中的診斷](https://www.windowsazure.com/en-us/develop/net/common-tasks/diagnostics/)
-   [使用 Azure 診斷收集記錄資料](http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433048.aspx)
-   [偵錯 Azure 應用程式](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee405479.aspx)


