<properties
	pageTitle="開始使用資料表儲存體和 Visual Studio 已連接服務 (ASP.NET) | Microsoft Azure"
	description="在使用 Visual Studio 已連接服務連接到儲存體帳戶之後，如何在 Visual Studio ASP.NET 專案中開始使用 Azure 資料表儲存體"
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/21/2016"
	ms.author="tarcher"/>

# 開始使用資料表儲存體和 Visual Studio 已連接服務 (ASP.NET)

## 概觀
本文描述如何在您使用 Visual Studio 的 [**加入已連接服務**] 對話方塊，建立或參考 ASP.NET 專案中的 Azure 儲存體帳戶之後，開始在 Visual Studio 使用 Azure 資料表儲存體。本文說明如何在 Azure 資料表中執行常見工作，包括建立和刪除資料表，以及如何搭配使用資料表實體。這些範例均以 C# 程式碼撰寫，並使用 [Microsoft Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)。如需其他有關使用 Azure 資料表儲存體的一般資訊，請參閱[以 .NET 開始使用 Azure 資料表儲存體](storage-dotnet-how-to-use-tables.md)。

Azure 資料表儲存體可讓您儲存大量的結構化資料。此服務是一個 NoSQL 資料存放區，接受來自 Azure 雲端內外經過驗證的呼叫。Azure 資料表很適合儲存結構化、非關聯式資料。


## 在程式碼中存取資料表

1. 請確定 C# 檔案頂端的命名空間宣告包含這些 **using** 陳述式。

		 using Microsoft.Azure;
		 using Microsoft.WindowsAzure.Storage;
		 using Microsoft.WindowsAzure.Storage.Auth;
		 using Microsoft.WindowsAzure.Storage.Table;

2. 取得 **CloudStorageAccount** 物件，其代表您的儲存體帳戶資訊。使用下列程式碼，從 Azure 服務組態取得您的儲存體連接字串和儲存體帳戶資訊。

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **注意** - 請在下列範例中的程式碼前面使用上述所有程式碼。

3. 取得 **CloudTableClient** 物件，以參考儲存體帳戶中的資料表物件。

	    // Create the table client.
    	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. 取得 **CloudTable** 參考物件，以參考特定資料表與實體。

    	// Get a reference to a table named "peopleTable"
	    CloudTable table = tableClient.GetTableReference("peopleTable");

## 在程式碼中建立資料表

若要建立 Azure 資料表，請在上述程式碼中新增 **CreateIfNotExistsAsync()** 的呼叫。

	// Create the CloudTable if it does not exist
	await table.CreateIfNotExistsAsync();

## 將實體加入至資料表

若要將實體新增至資料表，請建立一個類別來定義實體的屬性。下列程式碼使用客戶名字作為資料列索引鍵、並使用姓氏作為資料分割索引鍵的，定義一個稱為 **CustomerEntity** 的實體類別。

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

實體相關的資料表作業會使用您稍早在＜存取程式碼中的資料表＞中所建立的 **CloudTable** 物件來執行。 **TableOperation** 物件要執行的操作。下列程式碼範例顯示如何建立 **CloudTable** 物件及 **CustomerEntity** 物件。為了準備這項操作，其建立了 **TableOperation**，以便在資料表中插入客戶實體。最後，呼叫 CloudTable.ExecuteAsync 來執行操作。

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";

	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);

	// Execute the insert operation.
	await peopleTable.ExecuteAsync(insertOperation);

## 插入實體批次

您可以在單一寫入操作中將多個項目插入至資料表。下列程式碼範例會建立兩個實體物件 ("Jeff Smith" 和 "Ben Smith")，並使用 Insert 方法將這兩個物件加入 **TableBatchOperation** 物件中，然後再呼叫 **CloudTable.ExecuteBatchAsync** 啟動作業。

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
	await peopleTable.ExecuteBatchAsync(batchOperation);

## 取得資料分割中的所有實體
若要向資料表查詢資料分割中的所有實體，請使用 **TableQuery** 物件。下列程式碼範例會指定篩選器來篩選出資料分割索引鍵為 'Smith' 的實體。此範例會將查詢結果中每個實體的欄位列印至主控台。

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity>
        resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
        }
        } while (token != null);

        return View();


## 取得單一實體
您可以撰寫查詢來取得單一特定實體。下列程式碼使用 **TableOperation** 物件來指定名為 'Ben Smith' 的客戶。這個方法只會傳回一個實體而不是集合，而且所傳回的 **TableResult.Result** 值是 **CustomerEntity** 物件。若要從表格服務中擷取單一實體，最快的方法是在查詢中同時指定資料分割索引鍵和資料列索引鍵。

	// Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

	// Execute the retrieve operation.
	TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);
	
	// Print the phone number of the result.
	if (retrievedResult.Result != null)
		Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

## 刪除實體
找到實體之後，您可以刪除它。下列程式碼會尋找名為 "Ben Smith" 的客戶實體，如果找到，就刪除它。

	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

	// Execute the operation.
	TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Execute the operation.
	   await peopleTable.ExecuteAsync(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Couldn't delete the entity.");

## 後續步驟

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

<!---HONumber=AcomDC_0224_2016-->