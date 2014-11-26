<properties linkid="dev-net-2-how-to-table-services" urlDisplayName="Table Service (2.0)" pageTitle="How to use table storage | Microsoft Azure" metaKeywords="Get started Azure table, Azure nosql, Azure large structured data store, Azure table, Azure table storage, Azure table .NET, Azure table storage .NET, Azure table C#, Azure table storage C#" description="Learn how to use table storage to create and delete tables and insert and query entities in a table." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Table Storage Service" authors="" solutions="" manager="paulettm" editor="cgronlun" />

如何使用資料表儲存體服務
========================

[1.7 版](/zh-tw/develop/net/how-to-guides/table-services-v17/ "1.7 版") [2.0 版](/zh-tw/develop/net/how-to-guides/table-services/ "2.0 版")

本指南將示範如何使用 Azure 資料表儲存體服務執行一般案例。這些範例均以 C\# 程式碼撰寫，並使用 .NET API。所涵蓋的案例包括「建立和刪除資料表」、「在資料表中插入及查詢實體」****。如需資料表的詳細資訊，請參閱[後續步驟](#next-steps)一節。

目錄
----

-   [什麼是資料表服務](#what-is)
-   [概念](#concepts)
-   [建立 Azure 儲存體帳戶](#create-account)
-   [設定儲存體連接字串](#setup-connection-string)
-   [作法：以程式設計方式存取資料表儲存體](#configure-access)
-   [作法：建立資料表](#create-table)
-   [作法：將實體加入至資料表](#add-entity)
-   [作法：插入實體批次](#insert-batch)
-   [作法：擷取資料分割中的所有實體](#retrieve-all-entities)
-   [作法：擷取資料分割中某個範圍的實體](#retrieve-range-entities)
-   [作法：擷取單一實體](#retrieve-single-entity)
-   [作法：更新實體](#update-entity)
-   [作法：查詢實體屬性的子集](#query-entity-properties)
-   [作法：插入或取代實體](#insert-entity)
-   [作法：刪除實體](#delete-entity)
-   [作法：刪除資料表](#delete-table)
-   [後續步驟](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

建立帳戶建立 Azure 儲存體帳戶
-----------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

設定連接字串設定儲存體連接字串
------------------------------

Azure .NET 儲存體 API 可支援使用儲存體連接字串，來設定存取儲存服務時所需的端點與認證。您可以將儲存體連接字串置於設定檔中，而非硬式編碼至程式碼中：

-   使用 Azure 雲端服務時，建議您使用 Azure 服務設定系統 (`*.csdef` 和 `*.cscfg` 檔案) 來儲存連接字串。
-   使用 Azure 網站或 Azure 虛擬機器時，建議您使用 .NET 設定系統 (例如 `web.config` 檔案) 來儲存連接字串。

在這兩種情況下，您都可以使用 `CloudConfigurationManager.GetSetting` 方法擷取連接字串，如本指南稍後所示範。

### 在使用雲端服務時設定連接字串

服務設定機制為 Azure 雲端服務專案所獨有，可讓您從 Azure 管理入口網站動態變更組態設定，而無需重新部署應用程式。

若要在 Azure 服務設定中設定連接字串：

1.  在 Visual Studio 的 [方案總管]，於 Azure 部署專案之 **[角色]** 資料夾中的 Web 角色或背景工作角色上按一下滑鼠右鍵，再按一下 **[屬性]**。
    ![Blob5](./media/storage-dotnet-how-to-use-table-storage-17/blob5.png)

2.  按一下 **[設定]** 索引標籤，再按 **[加入設定]** 按鈕。
    ![Blob6](./media/storage-dotnet-how-to-use-table-storage-17/blob6.png)

    設定清單中隨即顯示新的 **[Setting1]** 項目。

3.  在新 **[Setting1]** 項目的 **[類型]** 下拉式清單中，選擇 **[連接字串]**。
    ![Blob7](./media/storage-dotnet-how-to-use-table-storage-17/blob7.png)

4.  按一下 **[Setting1]** 項目右側的 **[...]** 按鈕。**[儲存體帳戶連接字串]** 對話方塊隨即開啟。

5.  選擇要以儲存體模擬器 (本機電腦上模擬的 Azure 儲存體)，還是雲端中的實際儲存體帳戶為目標。不論您選擇哪一種，本指南的程式碼都適用。如您想要在稍早於 Azure 上建立的儲存體帳戶中儲存 Blob 資料，請輸入在本教學課程的稍早步驟複製的 **[主要存取金鑰]** 值。
     ![Blob8](./media/storage-dotnet-how-to-use-table-storage-17/blob8.png)

6.  將 **[名稱]** 項目從 **[Setting1]** 變為更易記的名稱，如 **StorageConnectionString**。您稍後將在本指南的程式碼中參照此連接字串。
    ![Blob9](./media/storage-dotnet-how-to-use-table-storage-17/blob9.png)

### 使用網站或虛擬機器時設定連接字串

使用網站或虛擬機器時，建議您使用 .NET 設定系統 (例如 `web.config`)。您可以使用 `<appSettings>` 元素來儲存連接字串：

    <configuration>
    <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

如需儲存體連接字串的詳細資訊，請閱讀[設定連接字串](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee758697.aspx)。

現在您已準備就緒，可以開始執行本指南的「作法」工作。

以程式設計方式存取作法：以程式設計方式存取資料表儲存體
------------------------------------------------------

將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C\# 檔案內的頂端：

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

您可以使用 **CloudStorageAccount** 類型及 **CloudConfigurationManager** 類型，擷取 Azure 服務設定中的儲存體連接字串與儲存體帳戶資訊：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

建立資料表作法：建立資料表
--------------------------

**CloudTableClient** 物件可讓您取得資料表和實體的參照物件。下列程式碼會建立 **CloudTableClient** 物件，並使用該物件建立新資料表。本指南的所有程式碼都使用 Azure 應用程式服務設定中所儲存的儲存體連接字串。也有其他方式可建立 **CloudStorageAccount** 物件。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist
    string tableName = "people";
    tableClient.CreateTableIfNotExist(tableName);

將實體加入至資料表作法：將實體加入至資料表
------------------------------------------

實體會使用衍生自 **TableServiceEntity** 的自訂類別來對應至 C\# 物件。若要將實體新增至資料表，請先建立一個類別來定義實體的屬性。下列程式碼會定義一個使用客戶名字作為資料列索引鍵、並使用姓氏作為資料分割索引鍵的實體類別。實體的資料分割索引鍵和資料列索引鍵共同唯一識別資料表中的實體。相較於查詢具有不同資料分割索引鍵的實體，查詢具有相同資料分割索引鍵的實體速度會較快。

    public class CustomerEntity : TableServiceEntity
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

涉及實體的資料表操作需要 **TableServiceContext** 物件。此物件會追蹤在用戶端程式碼中建立及存取的所有資料表實體的用戶端狀態。維護用戶端物件呈現每個實體，會讓寫入操作更有效率，因為在執行儲存操作時，只有已變更的物件會更新到資料表服務。下列程式碼會呼叫 **GetDataServiceContext** 方法來建立 **TableServiceContext** 物件。然後程式碼會建立 **CustomerEntity** 類別的執行個體。程式碼會呼叫 **serviceContext.AddObject**，將新實體插入到資料表中。這會將實體物件新增至 **serviceContext**，但不會發生任何服務操作。最後，程式碼在呼叫 **SaveChangesWithRetries** 方法時，將新實體傳送給資料表服務。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a new customer entity
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Add the new customer to the people table
    serviceContext.AddObject("people", customer1);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

插入實體批次作法：插入實體批次
------------------------------

您可以在單一寫入操作中，插入實體批次至資料表服務。下列程式碼會建立三個實體物件，並使用 **AddObject** 方法將各實體物件新增至服務內容。接著程式碼會呼叫 **SaveChangesWithRetries** 並使用 **SaveChangesOptions.Batch** 參數。如果省略 **SaveChangesOptions.Batch**，將會發生三次對資料表服務的個別呼叫。以下是批次操作的其他一些注意事項：

1.  您可以執行批次更新、刪除或插入。
2.  單一批次操作最多可包含 100 個實體。
3.  單一批次操作中的所有實體必須具有相同的資料分割索引鍵。

<!-- -->

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    string tableName = "people";

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a customer entity and add to the table
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.Email = "Jeff@contoso.com";
    customer.PhoneNumber = "425-555-0104";
    serviceContext.AddObject(tableName, customer);

    // Create another customer entity and add to the table
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";
    serviceContext.AddObject(tableName, customer2);

    // Create a customer entity and add to the table
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.Email = "Denise@contoso.com";
    customer3.PhoneNumber = "425-555-0103";
    serviceContext.AddObject(tableName, customer3);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.Batch);

擷取所有實體作法：擷取資料分割中的所有實體
------------------------------------------

若要向資料表查詢資料分割中的實體，可以使用 LINQ 查詢。呼叫 **serviceContext.CreateQuery** 以從資料來源建立查詢。下列程式碼會指定篩選器來篩選出資料分割索引鍵為 'Smith' 的實體。在 LINQ 查詢的結果呼叫 **AsTableServiceQuery&lt;CustomerEntity\>** 以完成建立 **CloudTableQuery** 物件。接著可以使用在 **foreach** 迴圈中建立的 **partitionQuery** 物件取用結果。此程式碼會將查詢結果中每個實體的欄位列印至主控台。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Specify a partition query, using "Smith" as the partition key
    CloudTableQuery<CustomerEntity> partitionQuery =
    (from e in serviceContext.CreateQuery<CustomerEntity>("people")
    where e.PartitionKey == "Smith"
    select e).AsTableServiceQuery<CustomerEntity>();

    // Loop through the results, displaying information about the entity
    foreach (CustomerEntity entity in partitionQuery)
    {
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
    entity.Email, entity.PhoneNumber);
    }

擷取某個範圍的實體作法：擷取資料分割中某個範圍的實體
----------------------------------------------------

如果您不想要查詢資料分割中的所有實體，可使用 **CompareTo** 方法，而不要使用一般的大於 (\>) 和小於 (&lt;) 運算子來指定範圍。這是因為後者將導致不適當的查詢建構。下列程式碼會使用兩個篩選器來取得資料分割 'Smith' 中，資料列索引鍵 (名字) 是以字母 'E' 前之字母為開頭的所有實體。然後列印查詢結果。如果您使用在本指南批次插入小節中新增至資料表的實體，則這時只會傳回兩個實例 (Ben Smith 及 Denise Smith)；Jeff Smith 不在其中。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Specify a partition query, using "Smith" as the partition key,
    // with the row key being up to the letter "E"
    CloudTableQuery<CustomerEntity> entityRangeQuery =
    (from e in serviceContext.CreateQuery<CustomerEntity>("people")
    where e.PartitionKey == "Smith" && e.RowKey.CompareTo("E") < 0
    select e).AsTableServiceQuery<CustomerEntity>();

    // Loop through the results, displaying information about the entity
    foreach (CustomerEntity entity in entityRangeQuery)
    {
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
    entity.Email, entity.PhoneNumber);
    }

擷取單一實體作法：擷取單一實體
------------------------------

您可以撰寫查詢來擷取單一特定實體。下列程式碼範例使用兩個篩選器來來指定客戶 'Jeff Smith'。此程式碼不會呼叫 **AsTableServiceQuery**，而是呼叫 **FirstOrDefault**。此方法只會傳回一個實體而非一個集合，因此程式碼會將傳回值直接指派給 **CustomerEntity** 物件。如果沒有實體具有完全相符的資料分割和資料列索引鍵，則會傳回 Null 值。若要從資料表服務中擷取單一實體，最快的方法是在查詢中同時指定資料分割索引鍵和資料列索引鍵。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Return the entity with partition key of "Smith" and row key of "Jeff"
    CustomerEntity specificEntity =
    (from e in serviceContext.CreateQuery<CustomerEntity>("people")
    where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
    select e).FirstOrDefault();

更新實體作法：更新實體
----------------------

若要更新實體，請從資料表服務擷取該實體、修改實體物件，然後將變更回存至資料表服務。下列程式碼會變更現有客戶的電話號碼。此程式碼不會像插入時一樣呼叫 **AddObject**，而是呼叫 **UpdateObject**。**SaveChangesWithRetries** 方法會呼叫資料表服務，且系統會更新實體，除非是在此應用程式擷取它之後，有另一個應用程式變更它。發生此情況時，系統會擲回例外狀況，且必須重新擷取、修改及儲存實體。這種重試模式常見於分散式儲存體系統。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Return the entity with partition key of "Smith" and row key of "Jeff"
    CustomerEntity specificEntity =
    (from e in serviceContext.CreateQuery<CustomerEntity>("people")
    where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
    select e).FirstOrDefault();

    // Specify a new phone number
    specificEntity.PhoneNumber = "425-555-0105";

    // Update the entity
    serviceContext.UpdateObject(specificEntity);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

查詢屬性的子集作法：查詢實體屬性的子集
--------------------------------------

一項資料表查詢可以只擷取實體的少數屬性。這項稱為「投射」的技術可減少頻寬並提高查詢效能 (尤其是對大型實體而言)。下列程式碼中的查詢只會傳回資料表中各實體的電子郵件地址。您可以閱讀這篇[部落格文章](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx) (英文) 深入了解投射。請注意，投射並不支援在本機儲存體模擬器上進行，因此此程式碼唯有在使用資料表服務上的帳戶時才會執行。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Define a projection query that retrieves only the Email property
    var projectionQuery = 
    from e in serviceContext.CreateQuery<CustomerEntity>("people")
    select new
        {
    Email = e.Email
    // You can specify additional fields here
        };

    // Loop through the results, displaying the Email value
    foreach (var person in projectionQuery)
    {
    Console.WriteLine(person.Email);
    }

插入或取代實體作法：插入或取代實體
----------------------------------

您經常會想要新增實體至資料表，但不知道它是否已在資料表中。插入或取代實體允許您透過單一要求，如果實體不存在便插入它，若是存在則取代現有實體。以先前的範例為基礎，下列程式碼會插入或取代 'Walter Harp' 的實體。建立新實體之後，此程式碼會呼叫 **serviceContext.AttachTo** 方法。接著，此程式碼會呼叫 **UpdateObject**，最後則是呼叫 **SaveChangesWithRetries** 並搭配 **SaveChangesOptions.ReplaceOnUpdate** 參數。省略 **SaveChangesOptions.ReplaceOnUpdate** 參數會導致插入或合併操作。請注意，插入或取代並不支援在本機儲存體模擬器上進行，因此此程式碼唯有在使用資料表服務上的帳戶時才會執行。您可以閱讀這篇[部落格文章](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx) (英文) 深入了解插入或取代，以及插入或合併。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a new customer entity
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.Email = "Walter@contoso.com";
    customer5.PhoneNumber = "425-555-0106";

    // Attach this customer to the people table
    serviceContext.AttachTo("people", customer5);

    // Insert this customer if new, or replace if exists
    serviceContext.UpdateObject(customer5);

    // Submit the operation the table service, using the ReplaceOnUpdate option
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.ReplaceOnUpdate);

刪除實體作法：刪除實體
----------------------

擷取實體之後，可以輕鬆地將它刪除。您也可以使用 **AttachTo** 方法以開始追蹤它，而不從伺服器擷取 (請參閱上述的插入或取代)。使用 **serviceContext** 追蹤實體之後，針對要刪除的實體呼叫 **DeleteObject**。然後呼叫 **SaveChangesWithRetries**。下列程式碼會擷取並刪除客戶實體。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    CustomerEntity specificEntity =
    (from e in serviceContext.CreateQuery<CustomerEntity>("people")
    where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
    select e).FirstOrDefault();

    // Delete the entity
    serviceContext.DeleteObject(specificEntity);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

刪除資料表作法：刪除資料表
--------------------------

最後，下列程式碼會從儲存體帳戶刪除資料表。刪除資料表後，將有一段時間無法重新建立該資料表。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Delete the table it if exists
    tableClient.DeleteTableIfExist("people");

後續步驟後續步驟
----------------

了解資料表儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   如需可用 API 的完整詳細資訊，請檢視 Blob 服務參考文件：
    -   [.NET 用戶端程式庫參考](http://msdn.microsoft.com/zh-tw/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [REST API 參考](http://msdn.microsoft.com/zh-tw/library/windowsazure/dd179355)
-   請參閱[在 Azure 中儲存和存取資料](http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx)，深入了解可使用 Azure 儲存體執行的更多進階工作。
-   如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。
    -   使用 [Blog 儲存體](/zh-tw/develop/net/how-to-guides/blob-storage/)儲存非結構化資料。
    -   使用 [SQL Database](/zh-tw/develop/net/how-to-guides/sql-database/) 儲存關聯式資料。

