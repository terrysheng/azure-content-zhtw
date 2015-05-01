<properties 
	pageTitle="如何使用 .NET 的資料表儲存體 | Microsoft Azure「" 
	description="了解如何使用 Microsoft Azure 資料表儲存體來建立和刪除資料表，以及插入和查詢資料表中的實體。」" 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tamram"/>


# 如何使用 .NET 的資料表儲存體

[AZURE.INCLUDE [storage-selector-table-include](../includes/storage-selector-table-include.md)]

## 概觀

本指南將示範如何使用 Azure Blob 服務執行一般案例 
Azure 佇列服務執行一般案例。這些範例均以 C\# 程式碼撰寫，並使用 Azure Storage Client Library for .NET。所涵蓋的案例包括「**建立和刪除資料表**」，以及「**使用資料表實體**」。

> [AZURE.NOTE] 本指南以 Azure .NET Storage Client Library 2.x 和更新版本為對象。建議的版本是儲存體用戶端程式庫 4.x，可透過 [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) 或從 [Azure SDK for .NET](/downloads/) 中取得。請參閱 [以程式設計方式存取資料表儲存體](#programmatically-access-table-storage) ，以詳細了解如何取得儲存體用戶端程式庫。

[AZURE.INCLUDE [storage-table-concepts-include](../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../includes/storage-configure-connection-string-include.md)]

## 以程式設計方式存取資料表儲存體

### 取得組件
您可以使用 NuGet 來取得 `Microsoft.WindowsAzure.Storage.dll` 組件。在 [**方案總管**] 中以滑鼠右鍵按一下專案，然後選擇 [**管理 NuGet 封裝**]。在線上搜尋 "WindowsAzure.Storage"，再按一下 [**安裝**] 以安裝 Azure 儲存體封裝與相依性。

`Microsoft.WindowsAzure.Storage.dll` 也隨附於 Azure SDK for .NET (可自 <a href="http://azure.microsoft.com/develop/net/#">.NET Developer Center</a> 下載)。此組件會安裝在 `%Program Files%\Microsoft SDKs\Azure\.NET SDK\<sdk-version>\ref\` 目錄。

### 命名空間宣告
將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C\# 檔案內的頂端：

    using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

確定已參照 `Microsoft.WindowsAzure.Storage.dll` 組件。

### 擷取連接字串
您可以使用 **CloudStorageAccount** 類型來代表儲存體帳戶資訊。如果使用 
Azure 專案範本且 (或) 具有
Microsoft.WindowsAzure.CloudConfigurationManager 命名空間參照，您可以使用 **CloudConfigurationManager** 類型，從 Azure 服務組態中擷取儲存體連接字串和儲存體帳戶資訊：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

如果您是建立不含 Microsoft.WindowsAzure.CloudConfigurationManager 參照的應用程式，且連接字串位於 `web.config` 或 `app.config` (如上所示)，則可使用 **ConfigurationManager** 來擷取連接字串。您需要將 System.Configuration.dll 參照新增至專案，並為其新增其他命名空間宣告：

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"]);

### ODataLib 相依性
Storage Client Library for .NET 中的 ODataLib 相依性現已透過 ODataLib (5.0.2 版) 封裝解決，該封裝是由 NuGet 而非 WCF Data Services 提供。您可以直接下載 ODataLib 程式庫，或是由您的程式碼專案透過 NuGet 參照這些程式庫。這些 ODataLib 封裝具體來說是 [OData]、[Edm] 和 [Spatial]。

## 建立資料表

**CloudTableClient** 物件可讓您取得資料表和實體的參照物件。下列程式碼會建立 **CloudTableClient** 物件，並使用該物件建立新資料表。本指南的所有程式碼都假設正在建置的應用程式為 Azure 雲端服務專案，並使用 Azure 應用程式的服務設定中所儲存的儲存體連接字串。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

## 將實體加入至資料表

實體會使用衍生自 
**TableEntity** 的自訂類別來對應至 C\# 物件。若要將實體新增至資料表，請建立一個類別來定義實體的屬性。下列程式碼會定義一個使用客戶名字做為資料列索引鍵、並使用姓氏做為資料分割索引鍵的實體類別。實體的資料分割索引鍵和資料列索引鍵共同唯一識別資料表中的實體。相較於查詢具有不同資料分割索引鍵的實體，查詢具有相同資料分割索引鍵的實體速度會較快，但使用不同的資料分割索引鍵可獲得更大的平行操作延展性。任何應該儲存於資料表服務中的屬性，都必須是屬於所支援類型 (其同時公開 `get` 和 `set`) 的公用屬性。
此外，您的實體類型 *must*公開無參數建構函式。

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

涉及實體的資料表操作是以您在「作法：建立資料表」中建立的 **CloudTable** 物件執行。要執行的操作是以 **TableOperation** 物件代表。下列程式碼範例會依序建立 **CloudTable** 物件及 **CustomerEntity** 物件。為了準備這項作業，請建立 **TableOperation** 以便在資料表中插入客戶實體。最後，呼叫 **CloudTable.Execute** 來執行操作。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

## 插入實體批次

您可以在單一寫入操作中，插入實體批次至資料表。以下是批次操作的其他一些注意事項：

1.  您可以在同一批次操作中執行更新、刪除和插入。
2.  單一批次操作最多可包含 100 個實體。
3.  單一批次操作中的所有實體必須具有相同的資料
    分割索引鍵。
4.  雖然可以單一批次操作的形式來執行查詢，但該查詢必須是批次中的唯一操作。

<!-- -->
下列程式碼範例會建立兩個實體物件，並使用 **Insert** 方法將每個實體物件新增至 **TableBatchOperation**。接著會呼叫 **CloudTable.Execute** 執行操作。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
	// Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";
            
	// Create another customer entity and add it to the table.
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";
            
	// Add both customer entities to the batch insert operation.
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);

	// Execute the batch operation.
	table.ExecuteBatch(batchOperation);

## 擷取資料分割中的所有實體

若要向資料表查詢資料分割中的所有實體，請使用 **TableQuery** 物件。
下列程式碼範例會指定篩選器來篩選出資料分割索引鍵為 'Smith' 的實體。此範例會將查詢結果中每個實體的欄位列印至主控台。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## 擷取資料分割中某個範圍的實體

如果您不想要查詢資料分割中的所有實體，可結合資料分割索引鍵篩選器與資料列索引鍵篩選器來指定範圍。下列程式碼範例會使用兩個篩選器來取得資料分割 'Smith' 中，資料列索引鍵 (名字) 是以字母 'E' 前之字母為開頭的所有實體，然後會列印查詢結果。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

	// Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

## 擷取單一實體

您可以撰寫查詢來擷取單一特定實體。下列程式碼範例使用 **TableOperation** 來指定客戶 'Ben Smith'。
此方法只會傳回一個實體而非一個集合，且 **TableResult.Result** 中的傳回值會是 **CustomerEntity**。
若要從資料表服務中擷取單一實體，最快的方法是在查詢中同時指定資料分割索引鍵和資料列索引鍵。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

## 取代實體

若要更新實體，請從資料表服務擷取該實體、修改實體物件，然後將變更回存至資料表服務。下列程式碼會變更現有客戶的電話號碼。此程式碼不會呼叫 **Insert**，而是使用  
**Replace**。如此會完全取代伺服器上的實體，但如果伺服器上的實體自擷取後產生變化，操作就會失敗。如此會造成失敗，是為了防止應用程式意外覆寫該應用程式的其他元件在擷取後到更新前的這段期間所做的變更。正確處理此失敗的方式為重新擷取實體、進行變更 (如果仍然有效)，然後再執行一次 **Replace** 操作。下一節將示範如何覆寫此行為。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-0105";

	   // Create the InsertOrReplace TableOperation
	   TableOperation updateOperation = TableOperation.Replace(updateEntity);

	   // Execute the operation.
	   table.Execute(updateOperation);

	   Console.WriteLine("Entity updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

## 插入或取代實體

如果從伺服器擷取的實體自擷取後發生變化，**Replace** 操作將失敗。此外，您必須先從伺服器擷取實體，**Replace**才會成功。
但有時候，您可能不知道實體是否存在伺服器上，而實體中目前儲存的值並不重要，此時您的更新就應該加以完全覆寫。若要達到此目標，您可以使用 **InsertOrReplace** 操作。此操作會插入實體 (如果其目前並不存在) 或取代實體 (如果其已存在)，不論上次是何時更新。在下列程式碼範例中，仍會擷取 Ben Smith 的客戶實體，但接著會使用 **InsertOrReplace** 將它回存到伺服器。在擷取後到更新前的這段期間對實體所做的任何更新，都會遭到覆寫。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-1234";

	   // Create the InsertOrReplace TableOperation
	   TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

	   // Execute the operation.
	   table.Execute(insertOrReplaceOperation);

	   Console.WriteLine("Entity was updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

## 查詢實體屬性的子集

一項資料表查詢可以只擷取實體的少數屬性而非所有屬性。這項稱為「投射」的技術可減少頻寬並提高查詢效能 (尤其是對大型實體而言)。下列程式碼中的查詢只會傳回資料表中各實體的電子郵件地址。這是透過使用 **DynamicTableEntity** 與 **EntityResolver** 的查詢來完成。您可以閱讀這篇 [部落格文章][] (英文) 深入了解投射。請注意，投射並不支援在本機儲存體模擬器上進行，因此此程式碼唯有在使用資料表服務上的帳戶時才會執行。

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and only select the Email property
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

## 刪除實體

您可以使用與進行實體更新時所示範的相同模式，輕易刪除已擷取的實體。下列程式碼會擷取並刪除客戶實體。

    // 從連接字串擷取儲存體帳戶
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Execute the operation.
	   table.Execute(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Could not retrieve the entity.");

## 刪除資料表

最後，下列程式碼範例會從儲存體帳戶刪除資料表。刪除資料表後，將有一段時間無法重新建立該資料表。

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

## 後續步驟

了解資料表儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

<ul>
<li>如需可用 API 的完整詳細資訊，請檢視資料表服務參考文件：
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Storage Client Library for .NET 參考</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dd179355">REST API 參考</a></li>
  </ul>
</li>
<li>請參閱 [<a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">在 Azure 中儲存和存取資料</a>]，深入了解可使用 Azure 儲存體執行的更多進階工作。</li>
<li>了解如何使用 <a href="../websites-dotnet-webjobs-sdk/">Azure WebJobs SDK，來簡化撰寫使用 Azure 儲存體的程式碼。</li>
<li>如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。
  <ul>
    <li>使用 <a href="/documentation/articles/storage-dotnet-how-to-use-blobs/">Blog 儲存體</a>儲存非結構化資料。</li>
    <li>使用<a href="/documentation/articles/storage-dotnet-how-to-use-queues/">佇列儲存體</a>儲存結構化資料。</li>
    <li>使用 <a href="/documentation/articles/sql-database-dotnet-how-to-use/">SQL Database</a> 儲存關聯式資料。</li>
  </ul>
</li>
</ul>

  [下載並安裝 Azure SDK for .NET]: /develop/net/
  [在 Visual Studio 中建立 Azure 專案]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  
  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png
  
  [部落格文章]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [.NET 用戶端程式庫參考]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [在 Azure 中儲存和存取資料]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
  [設定連接字串]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [作法：以程式設計方式存取資料表儲存體]: #tablestorage

<!--HONumber=49-->