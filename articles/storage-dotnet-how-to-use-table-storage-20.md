<properties  linkid="dev-net-2-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage  from .NET | Microsoft Azure" metaKeywords="Get started Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure table .NET   Azure table storage .NET   Azure table C#   Azure table storage C#" description="Learn how to use table storage to create and delete tables and insert and query entities in a table." services="storage" documentationCenter=".NET" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="How to use the Table Storage Service" authors="tamram" />

# 如何使用 .NET 的資料表儲存體服務

本指南將示範如何使用 Azure 資料表儲存體服務執行一般案例。這些範例均以 C# 程式碼撰寫，並使用 Azure Storage Client Library for .NET。所涵蓋的案例包括「建立和刪除資料表」****，以及「使用資料表實體」****。如需資料表的詳細資訊，請參閱[後續步驟](#next-steps)一節。

## 目錄

* [什麼是資料表服務](#what-is)
* [概念](#concepts)
* [建立 Azure 儲存體帳戶](#create-account)
* [設定儲存體連接字串](#setup-connection-string)
* [作法：以程式設計方式存取資料表儲存體](#configure-access)
* [作法：建立資料表](#create-table)
* [作法：將實體加入至資料表](#add-entity)
* [作法：插入實體批次](#insert-batch)
* [作法：擷取資料分割中的所有實體](#retrieve-all-entities)
* [作法：擷取資料分割中某個範圍的實體](#retrieve-range-entities)
* [作法：擷取單一實體](#retrieve-single-entity)
* [作法：取代實體](#replace-entity)
* [作法：插入或取代實體](#insert-or-replace-entity)
* [作法：查詢實體屬性的子集](#query-entity-properties)
* [作法：刪除實體](#delete-entity)
* [作法：刪除資料表](#delete-table)
* [後續步驟](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">建立帳戶</span>建立 Azure 儲存體帳戶</h2>


[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">設定連接字串</span>設定儲存體連接字串</h2>


Azure Storage Client Library for .NET 可支援使用儲存體連接字串，來設定存取儲存服務時所需的端點與認證。您可以將儲存體連接字串置於設定檔中，而非硬式編碼至程式碼中：

* 使用 Azure 雲端服務時，建議您使用 Azure 服務設定系統 (`*.csdef` 和 `*.cscfg` 檔案) 來儲存連接字串。
* 使用 Azure 網站、Azure 虛擬機器，或建置要在 Azure 外執行的 .NET 應用程式時，建議您使用 .NET 設定系統 (例如
  `web.config` 或 `app.config` 檔案) 來儲存連接字串。

在這兩種情況下，您都可以使用 `CloudConfigurationManager.GetSetting`
方法擷取連接字串，如本指南稍後所示範。

### 在使用雲端服務時設定連接字串

服務設定機制為 Azure 雲端服務專案所獨有，可讓您從 Azure 管理入口網站動態變更組態設定，而無需重新部署應用程式。

若要在 Azure 服務設定中設定連接字串：

1.  在 Visual Studio 的 **方案總管]，於 Azure 部署專案之 [角色** 資料夾中的 Web 角色或背景工作角色上按一下滑鼠右鍵，再按一下 **屬性**。  
    ![Blob5](./media/storage-dotnet-how-to-use-table-storage-20/blob5.png)

2.  按一下 **設定** 索引標籤，再按 **加入設定** 按鈕。  
    ![Blob6](./media/storage-dotnet-how-to-use-table-storage-20/blob6.png)
    
    設定清單中隨即顯示新的 **Setting1** 項目。

3.  在新 **Setting1** 項目的 **類型** 下拉式清單中，選擇 **連接字串**。  
    ![Blob7](./media/storage-dotnet-how-to-use-table-storage-20/blob7.png)

4.  按一下 **Setting1** 項目右側的 **...** 按鈕。**儲存體帳戶連接字串** 對話方塊隨即開啟。

5.  選擇要以儲存體模擬器 (本機電腦上模擬的 Azure 儲存體)，還是雲端中的實際儲存體帳戶為目標。不論您選擇哪一種，本指南的程式碼都適用。如您想要在稍早於 Azure 上建立的儲存體帳戶中儲存 Blob 資料，請輸入在本教學課程的稍早步驟複製的 **主要存取金鑰** 值。  
     ![Blob8](./media/storage-dotnet-how-to-use-table-storage-20/blob8.png)

6.  將 **名稱** 項目從 **Setting1** 變為更易記的名稱，如 **StorageConnectionString**。您稍後將在本指南的程式碼中參照此連接字串。  
    ![Blob9](./media/storage-dotnet-how-to-use-table-storage-20/blob9.png)

### 使用 .NET 設定來設定連接字串

如果您要撰寫的應用程式並非 Azure 雲端服務 (請參閱上節)，建議您使用 .NET 設定系統 (例如 `web.config` 或
`app.config`)。這類應用程式包括 Azure 網站或 Azure 虛擬機器，以及設計成在 Azure
外執行的應用程式。您可以使用如下所示的 `<a ppSettings>` 元素儲存連接字串：

	<configuration>
  		<a ppSettings>
    		<a dd key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
  		</appSettings>
	</configuration>

 如需儲存體連接字串的詳細資訊，請閱讀[設定連接字串][1]。

 現在您已準備就緒，可以開始執行本指南的「作法」工作。

 <h2> <a name="configure-access"> </a><span  class="short-header">以程式設計方式存取</span>作法：以程式設計方式存取資料表儲存體</h2>


 <h3>取得組件</h3>


 您可以使用 NuGet 來取得 `Microsoft.WindowsAzure.Storage.dll` 組件。在 **方案總管** 中以滑鼠右鍵按一下專案，然後選擇 **管理 NuGet 封裝**。在線上搜尋 "WindowsAzure.Storage"，再按一下 **安裝** 以安裝 Azure 儲存體封裝與相依性。

 `Microsoft.WindowsAzure.Storage.dll` 也隨附於 Azure SDK for .NET (可自 [.NET Developer Center][2] 下載)。此組件會安裝至 `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk>\ref\` 目錄中。

 <h3>命名空間宣告</h3>


 將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端：

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

 確定已參照 `Microsoft.WindowsAzure.Storage.dll` 組件。

 <h3>擷取連接字串</h3>


 您可以使用 **CloudStorageAccount** 類型來代表儲存體帳戶資訊。如果您使用 Azure 專案範本且 (或) 具有 Microsoft.WindowsAzure.CloudConfigurationManager 命名空間參照，可以使用 **CloudConfigurationManager** 類型，擷取 Azure 服務設定中的儲存體連接字串與儲存體帳戶資訊：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

 如果您是建立不含 Microsoft.WindowsAzure.CloudConfigurationManager 參照的應用程式，且連接字串位於 `web.config` 或 `app.config` (如上所示)，則可使用 **ConfigurationManager** 來擷取連接字串。您需要將 System.Configuration.dll 參照新增至專案，並為其新增其他命名空間宣告：

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

 <h3>ODataLib 相依性</h3>


 Storage Client Library for .NET 中的 ODataLib 相依性現已透過 ODataLib (5.0.2 版) 封裝解決，該封裝是由 NuGet 而非 WCF Data Services 提供。您可以直接下載 ODataLib 程式庫，或是由您的程式碼專案透過 NuGet 參照這些程式庫。這些 ODataLib 封裝具體來說是 [OData][3]、[Edm][4] 和
[Spatial][5]。

 <h2><a name="create-table"></a><span  class="short-header">建立資料表</span>作法：建立資料表</h2>


 **CloudTableClient** 物件可讓您取得資料表和實體的參照物件。下列程式碼會建立 **CloudTableClient** 物件，並使用該物件建立新資料表。本指南的所有程式碼都假設正在建置的應用程式為 Azure 雲端服務專案，並使用 Azure 應用程式的服務設定中所儲存的儲存體連接字串。

    // 從連接字串擷取儲存體帳戶。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 建立資料表用戶端。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // 於資料表不存在時建立資料表。
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

 <h2><a name="add-entity"></a><span  class="short-header">將實體加入至資料表</span>作法：將實體加入至資料表</h2>


 實體會使用衍生自 **TableEntity** 的自訂類別來對應至 C# 物件。若要將實體新增至資料表，請建立一個類別來定義實體的屬性。下列程式碼會定義一個使用客戶名字作為資料列索引鍵、並使用姓氏作為資料分割索引鍵的實體類別。實體的資料分割索引鍵和資料列索引鍵共同唯一識別資料表中的實體。相較於查詢具有不同資料分割索引鍵的實體，查詢具有相同資料分割索引鍵的實體速度會較快，但使用不同的資料分割索引鍵可獲得更大的平行操作延展性。任何應該儲存於資料表服務中的屬性，都必須是屬於所支援類型 (其同時公開 `get` 和 `set`) 的公用屬性。此外，您的實體類型「必須」**公開無參數建構函式。

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }
		
        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

 涉及實體的資料表操作是以您在「作法：建立資料表」中建立的 **CloudTable** 物件執行。要執行的操作是以 **TableOperation** 物件代表。下列程式碼範例會依序建立 **CloudTable** 物件及 **CustomerEntity** 物件。為了準備這項操作，其建立了 **TableOperation**，以便在資料表中插入客戶實體。最後，其呼叫了 **CloudTable.Execute** 來執行操作。

    // 從連接字串擷取儲存體帳戶。
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // 建立資料表用戶端。
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // 建立代表 "people" 資料表的 CloudTable 物件。
    CloudTable table = tableClient.GetTableReference("people");

    // 建立新的客戶實體。
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // 建立會插入客戶實體的 TableOperation。
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // 執行插入操作。
    table.Execute(insertOperation);

 <h2><a name="insert-batch"></a><span  class="short-header">插入實體批次</span>作法：插入實體批次</h2>


 您可以在單一寫入操作中，插入實體批次至資料表。以下是批次操作的其他一些注意事項：

1.  您可以在同一批次操作中執行更新、刪除和插入。
2.  單一批次操作最多可包含 100 個實體。
3.  單一批次操作中的所有實體必須具有相同的資料分割索引鍵。
4.  雖然可以單一批次操作的形式來執行查詢，但該查詢必須是批次中的唯一操作。


 下列程式碼範例會建立兩個實體物件，並使用 **Insert** 方法將每個實體物件新增至 **TableBatchOperation**。接著會呼叫 **CloudTable.Execute** 執行操作。

	    // 從連接字串擷取儲存體帳戶。
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	    	CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	    // 建立資料表用戶端。
	    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	    // 建立代表 "people" 資料表的 CloudTable 物件。
	    CloudTable table = tableClient.GetTableReference("people");
	
	    // 建立批次操作。
	    TableBatchOperation batchOperation = new TableBatchOperation();
	
	    // 建立客戶實體並將它新增至資料表。
	    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	    customer1.Email = "Jeff@contoso.com";
	    customer1.PhoneNumber = "425-555-0104";
	            
	    // 建立其他客戶實體並將它新增至資料表。
	    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	    customer2.Email = "Ben@contoso.com";
	    customer2.PhoneNumber = "425-555-0102";
	            
	    // 將兩個客戶實體都新增至批次插入操作。
	    batchOperation.Insert(customer1);
	    batchOperation.Insert(customer2);
	
	    // 執行批次操作。
	    table.ExecuteBatch(batchOperation);

 <h2><a name="retrieve-all-entities"></a><span  class="short-header">擷取所有實體</span>作法：擷取資料分割中的所有實體</h2>


 若要向資料表查詢資料分割中的所有實體，請使用 **TableQuery** 物件。下列程式碼範例會指定篩選器來篩選出資料分割索引鍵為 \'Smith\' 的實體。此範例會將查詢結果中每個實體的欄位列印至主控台。

	    // 從連接字串擷取儲存體帳戶。
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	    	CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	    // 建立資料表用戶端。
	    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	    // 建立代表 "people" 資料表的 CloudTable 物件。
	    CloudTable table = tableClient.GetTableReference("people");
	
	    // 建構查詢操作來查詢 PartitionKey="Smith" 的所有客戶實體。
	    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
	
	    // 列印每位客戶的欄位。
	    foreach (CustomerEntity entity in table.ExecuteQuery(query))
	    {
	    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
	    entity.Email, entity.PhoneNumber);
	    }

 <h2><a name="retrieve-range-entities"></a><span  class="short-header">擷取某個範圍的實體</span>作法：擷取資料分割中某個範圍的實體</h2>


 如果您不想要查詢資料分割中的所有實體，可結合資料分割索引鍵篩選器與資料列索引鍵篩選器來指定範圍。下列程式碼範例會使用兩個篩選器來取得資料分割 \'Smith\' 中，資料列索引鍵 (名字) 是以字母 \'E\' 前之字母為開頭的所有實體，然後會列印查詢結果。

	    // 從連接字串擷取儲存體帳戶。
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	    	CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	    // 建立資料表用戶端。
	    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	    // 建立代表 "people" 資料表的 CloudTable 物件。
	    CloudTable table = tableClient.GetTableReference("people");
	
	    // 建立資料表查詢。
	    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
	    TableQuery.CombineFilters(
	    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
	    TableOperators.And,
	    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));
	
	    // 對結果進行迴圈，顯示實體的相關資訊。
	    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
	    {
	    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
	    entity.Email, entity.PhoneNumber);
	    }

 <h2><a name="retrieve-single-entity"></a><span  class="short-header">擷取單一實體</span>作法：擷取單一實體</h2>


 您可以撰寫查詢來擷取單一特定實體。下列程式碼範例使用 **TableOperation** 來指定客戶 \'Ben Smith\'。此方法只會傳回一個實體而非一個集合，且 **TableResult.Result** 中的傳回值會是 **CustomerEntity**。若要從資料表服務中擷取單一實體，最快的方法是在查詢中同時指定資料分割索引鍵和資料列索引鍵。

	    // 從連接字串擷取儲存體帳戶。
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	    	CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	    // 建立資料表用戶端。
	    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	    // 建立代表 "people" 資料表的 CloudTable 物件。
	    CloudTable table = tableClient.GetTableReference("people");
	
	    // 建立用來取得客戶實體的擷取操作。
	    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	    // 執行擷取操作。
	    TableResult retrievedResult = table.Execute(retrieveOperation);
	
	    // 列印結果的電話號碼。
	    if (retrievedResult.Result != null)
	    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	    else
	    Console.WriteLine("The phone number could not be retrieved.");

 <h2><a name="replace-entity"></a><span  class="short-header">擷取實體</span>作法：取代實體</h2>


 若要更新實體，請從資料表服務擷取該實體、修改實體物件，然後將變更回存至資料表服務。下列程式碼會變更現有客戶的電話號碼。此程式碼不會呼叫 **Insert**，而是使用 **Replace**。如此會完全取代伺服器上的實體，但如果伺服器上的實體自擷取後產生變化，操作就會失敗。如此會造成失敗，是為了防止應用程式意外覆寫該應用程式的其他元件在擷取後到更新前的這段期間所做的變更。正確處理此失敗的方式為重新擷取實體、進行變更 (如果仍然有效)，然後再執行一次 **Replace** 操作。下一節將示範如何覆寫此行為。

	    // 從連接字串擷取儲存體帳戶。
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	    	CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	    // 建立資料表用戶端
	    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	    // 建立代表 "people" 資料表的 CloudTable 物件。
	    CloudTable table = tableClient.GetTableReference("people");
	
	    // 建立用來取得客戶實體的擷取操作。
	    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	    // 執行操作。
	    TableResult retrievedResult = table.Execute(retrieveOperation);
	
	    // 將結果指派給 CustomerEntity 物件。
	    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;
	    if (updateEntity != null)
	    {
	    // 變更電話號碼。
	    updateEntity.PhoneNumber = "425-555-0105";
	
	    // 建立 InsertOrReplace TableOperation
	    TableOperation updateOperation = TableOperation.Replace(updateEntity);
	
	    // 執行操作。
	    table.Execute(updateOperation);
	    Console.WriteLine("Entity updated.");
	    }
	
	    else
	    Console.WriteLine("Entity could not be retrieved.");

 <h2><a name="insert-or-replace-entity"></a><span  class="short-header">插入或取代實體</span>作法：插入或取代實體</h2>


 如果從伺服器擷取的實體自擷取後發生變化，**Replace** 操作將失敗。此外，您必須先從伺服器擷取實體，**Replace** 才會成功。但有時候，您可能不知道實體是否存在伺服器上，而實體中目前儲存的值並不重要，此時您的更新就應該加以完全覆寫。若要達到此目標，您可以使用
**InsertOrReplace** 操作。此操作會插入實體 (如果其目前並不存在) 或取代實體 (如果其已存在)，不論上次是何時更新。在下列程式碼範例中，仍會擷取 Ben Smith 的客戶實體，但接著會使用 **InsertOrReplace** 將它回存到伺服器。在擷取後到更新前的這段期間對實體所做的任何更新，都會遭到覆寫。

	    // 從連接字串擷取儲存體帳戶。
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	    	CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	    // 建立資料表用戶端。
	    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	    // 建立代表 "people" 資料表的 CloudTable 物件。
	    CloudTable table = tableClient.GetTableReference("people");
	
	    // 建立用來取得客戶實體的擷取操作。
	    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	    // 執行操作。
	    TableResult retrievedResult = table.Execute(retrieveOperation);
	
	    // 將結果指派給 CustomerEntity 物件。
	    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;
	    if (updateEntity != null)
	    {
	    // 變更電話號碼。
	    updateEntity.PhoneNumber = "425-555-1234";
	
	    // 建立 InsertOrReplace TableOperation
	    TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);
	
	    // 執行操作。
	    table.Execute(insertOrReplaceOperation);
	    Console.WriteLine("Entity was updated.");
	    }
	
	    else
	    Console.WriteLine("Entity could not be retrieved.");

 <h2><a name="query-entity-properties"></a><span  class="short-header">查詢屬性的子集</span>作法：查詢實體屬性的子集</h2>


 一項資料表查詢可以只擷取實體的少數屬性而非所有屬性。這項稱為「投射」的技術可減少頻寬並提高查詢效能 (尤其是對大型實體而言)。下列程式碼中的查詢只會傳回資料表中各實體的電子郵件地址。這是透過使用 **DynamicTableEntity** 與 **EntityResolver** 的查詢來完成。您可以閱讀這篇[部落格文章][6]深入了解投射。請注意，投射並不支援在本機儲存體模擬器上進行，因此此程式碼唯有在使用資料表服務上的帳戶時才會執行。

	    // 從連接字串擷取儲存體帳戶
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	    	CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	    // 建立資料表用戶端
	    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	    // 建立代表 "people" 資料表的 CloudTable。
	    CloudTable table = tableClient.GetTableReference("people");
	
	    // 定義查詢，並且僅選取 Email 屬性
	    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });
	
	    // 定義實體解析程式以使用所擷取的實體。
	    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;
	    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
	    {
	    Console.WriteLine(projectedEmail);
	    }

 <h2><a name="delete-entity"></a><span  class="short-header">刪除實體</span>作法：刪除實體</h2>


 您可以使用與進行實體更新時所示範的相同模式，輕易刪除已擷取的實體。下列程式碼會擷取並刪除客戶實體。

	    // 從連接字串擷取儲存體帳戶
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	    	CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	    // 建立資料表用戶端
	    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	    // 建立代表 "people" 資料表的 CloudTable。
	    CloudTable table = tableClient.GetTableReference("people");
	
	    // 建立會預期收到客戶實體的擷取操作。
	    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	    // 執行操作。
	    TableResult retrievedResult = table.Execute(retrieveOperation);
	
	    // 將結果指派給 CustomerEntity。
	    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;
	
	    // 建立 Delete TableOperation。
	    if (deleteEntity != null)
	    {
	    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
	    // 執行操作。
	    table.Execute(deleteOperation);
	    Console.WriteLine("Entity deleted.");
	    }
	
	    else
	    Console.WriteLine("Could not retrieve the entity.");

 <h2><a name="delete-table"></a><span  class="short-header">刪除資料表</span>作法：刪除資料表</h2>


 最後，下列程式碼範例會從儲存體帳戶刪除資料表。刪除資料表後，將有一段時間無法重新建立該資料表。

	    // 從連接字串擷取儲存體帳戶。
	    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
	    	CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	    // 建立資料表用戶端。
	    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	    // 建立代表 "people" 資料表的 CloudTable。
	    CloudTable table = tableClient.GetTableReference("people");
	
	    // 於資料表已存在時將其刪除。
	    table.DeleteIfExists();

 <h2><a name="next-steps"></a><span  class="short-header">後續步驟</span>後續步驟</h2>


 了解資料表儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

 *  如需可用 API 的完整詳細資訊，請檢視 Blob 服務參考文件：
  *  [Storage Client Library for .NET 參考資料][7]
  *  [REST API 參考資料][8]
  

*  請參閱[在 Azure 中儲存和存取資料][9]，深入了解可使用 Azure 儲存體執行的更多進階工作。
*  如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。
  *  使用 [Blog 儲存體](/en-us/develop/net/how-to-guides/blob-storage/)儲存非結構化資料。
  *  使用 [SQL Database](/en-us/develop/net/how-to-guides/sql-database/) 儲存關聯式資料。
  


 </appsettings>



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
[2]: http://www.windowsazure.com/en-us/develop/net/#
[3]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[4]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[5]: http://nuget.org/packages/System.Spatial/5.0.2
[6]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/dn495001(v=azure.10).aspx
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
[9]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx