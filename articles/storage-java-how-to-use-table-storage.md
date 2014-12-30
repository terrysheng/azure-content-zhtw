<properties urlDisplayName="Table Service" pageTitle="如何使用資料表儲存體 (Java) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Java, table storage Java" description="Learn how to use the table storage service in Azure. Code samples are written in Java code." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Table storage service from Java" authors="robmcm" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# 如何使用 Java 的資料表儲存體

本指南將示範如何使用 Azure 資料表儲存體服務執行一般案例。相關範例是以 Java 撰寫並使用 [Azure Storage SDK for Java][]。涵蓋的案例包括**建立**、**列出**和**刪除**資料表，以及**插入**、**查詢**、**修改**和**刪除**資料表中的項目。如需資料表的詳細資訊，請參閱[後續步驟](#NextSteps) 一節。

注意：有一套 SDK 可供在 Android 裝置上使用 Azure 儲存體的開發人員使用。如需詳細資訊，請參閱 [Azure Storage SDK for Android][]。 

## <a name="Contents"> </a>目錄

* [什麼是資料表儲存體](#what-is)
* [概念](#Concepts)
* [建立 Azure 儲存體帳戶](#CreateAccount)
* [建立 Java 應用程式](#CreateApplication)
* [設定您的應用程式來存取資料表儲存體](#ConfigureStorage)
* [設定 Azure 儲存體連接字串](#ConnectionString)
* [作法：建立資料表](#CreateTable)
* [作法：列出資料表](#ListTables)
* [作法：將實體加入至資料表](#AddEntity)
* [作法：插入實體批次](#InsertBatch)
* [作法：擷取資料分割中的所有實體](#RetrieveEntities)
* [作法：擷取資料分割中某個範圍的實體](#RetrieveRange)
* [作法：擷取單一實體](#RetriveSingle)
* [作法：修改實體](#ModifyEntity)
* [作法：查詢實體屬性的子集](#QueryProperties)
* [作法：插入或取代實體](#InsertOrReplace)
* [作法：刪除實體](#DeleteEntity)
* [作法：刪除資料表](#DeleteTable)
* [後續步驟](#NextSteps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

##<a name="CreateAccount"></a>建立 Azure 儲存體帳戶

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"></a>建立 Java 應用程式

在本指南中，您將使用儲存體功能，這些功能可執行於本機的 Java 應用程式內，也可執行於在 Azure 中之 Web 角色或背景工作角色內執行的程式碼中。

若要這樣做，您需要安裝 Java Development Kit (JDK)，並在 Azure 訂用帳戶中建立 Azure 儲存體帳戶。完成此動作之後，您需要驗證開發系統符合 GitHub 上的 [Azure Storage SDK for Java][] 儲存機制中所列出的最低需求和相依性。如果系統符合這些需求，則您可以依照指示，從該儲存機制中下載 Azure Storage Libraries for Java 並安裝在系統上。完成這些工作之後，您就能夠利用本文中的範例來建立 Java 應用程式。

## <a name="ConfigureStorage"> </a>設定您的應用程式來存取資料表儲存體

在您要使用 Windows Azure 儲存體 API 來存取資料表的 Java 檔案頂端，新增下列 import 陳述式：

    // Include the following imports to use table APIs
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.table.*;
    import com.microsoft.azure.storage.table.TableQuery.*;

## <a name="ConnectionString"> </a>設定 Azure 儲存體連接字串

Azure 儲存體用戶端會使用儲存體連接字串來儲存存取資料管理服務時所用的端點與認證。在用戶端應用程式中執行時，您必須以下列格式提供儲存體連接字串 (其中的 *AccountName* 和 *AccountKey* 值要使用您儲存體帳戶的名稱，以及在管理入口網站中針對該儲存體帳戶而列出的主要存取金鑰)。本範例將示範如何宣告靜態欄位來存放連接字串：

    // Define the connection-string with your values.
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

在 Microsoft Azure 的角色內執行的應用程式中，此字串可以儲存在服務組態檔 ServiceConfiguration.cscfg 裡，且可以藉由呼叫 **RoleEnvironment.getConfigurationSettings** 方法來存取。以下是從服務組態檔中名為 StorageConnectionString 的 **Setting** 元素取得連接字串的範例：

    // Retrieve storage account from connection-string.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

下列範例假設您已經使用這兩個方法之一來取得儲存體連接字串。

## <a name="CreateTable"> </a>作法：建立資料表

**CloudTableClient** 物件可讓您取得資料表和實體的參照物件。下列程式碼會建立 **CloudTableClient** 物件，並使用此物件建立新的 **CloudTable** 物件，以代表一個名為 "people" 的資料表。(注意：還有其他方法可建立 **CloudStorageAccount** 物件。如需詳細資訊，請參閱 [Azure 儲存體用戶端 SDK 參考] (英文) 中的 **CloudStorageAccount**)。

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

	   // Create the table client.
	   CloudTableClient tableClient = storageAccount.createCloudTableClient();

	   // Create the table if it doesn't exist.
	   String tableName = "people";
	   CloudTable cloudTable = new CloudTable(tableName,tableClient);
	   cloudTable.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="ListTables"></a>作法：列出資料表

若要取得資料表清單，請呼叫 **CloudTableClient.listTables()** 方法來擷取可逐一查看的資料表名稱清單。

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Loop through the collection of table names.
    	for (String table : tableClient.listTables())
    	{
		  // Output each table name.
		  System.out.println(table);
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="AddEntity"> </a>作法：將實體加入至資料表

將實體對應至 Java 物件，是透過一個實作 **TableEntity** 的自訂類別進行。為了方便起見，**TableServiceEntity** 類別會實作 **TableEntity**，並使用反映將屬性對應至為屬性指定的 Getter 和 Setter 方法。若要將實體新增至資料表，請先建立一個類別來定義實體的屬性。下列程式碼會定義一個使用客戶名字做為資料列索引鍵、並使用姓氏做為資料分割索引鍵的實體類別。實體的資料分割索引鍵和資料列索引鍵共同唯一識別資料表中的實體。相較於查詢具有不同資料分割索引鍵的實體，查詢具有相同資料分割索引鍵的實體速度會較快。

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;
        
        public String getEmail() {
            return this.email;
        }
        
        public void setEmail(String email) {
            this.email = email;
        }
        
        public String getPhoneNumber() {
            return this.phoneNumber;
        }
        
        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

涉及實體的資料表操作需要 **TableOperation** 物件。此物件定義了要在實體上執行的操作，而該操作可由 **CloudTable** 物件執行。下列程式碼會建立 **CustomerEntity** 類別的新執行個體，其中含有一些要儲存的客戶資料。程式碼接著會呼叫 **TableOperation.insertOrReplace** 來建立將實體插入資料表中的 **TableOperation** 物件，然後將新的 **CustomerEntity** 與該物件建立關聯。最後，程式碼會呼叫 **CloudTable** 物件上的 **execute** 方法，其中指定 "people" 資料表和新的 **TableOperation**，而後者就會傳送要求給儲存體服務，以將新的客戶實體插入 "people" 資料表中。

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();
			
    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");
			
    	// Create a new customer entity.
    	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    	customer1.setEmail("Walter@contoso.com");
    	customer1.setPhoneNumber("425-555-0101");
			
    	// Create an operation to add the new customer to the people table.
    	TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    	// Submit the operation to the table service.
    	cloudTable.execute(insertCustomer1);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="InsertBatch"> </a>作法：插入實體批次

您可以在單一寫入操作中，插入實體批次至資料表服務。下列程式碼會建立一個 **TableBatchOperation** 物件，然後在其中新增三個插入操作。加入每個插入操作的方式都是建立新的實體物件、設定其值，然後呼叫 **TableBatchOperation** 物件上的 **insert** 方法以將實體與新的插入操作建立關聯。然後，程式碼會呼叫 **

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Define a batch operation.
    	TableBatchOperation batchOperation = new TableBatchOperation();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Create a customer entity to add to the table.
    	CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    	customer.setEmail("Jeff@contoso.com");
    	customer.setPhoneNumber("425-555-0104");
    	batchOperation.insertOrReplace(customer);

	   // Create another customer entity to add to the table.
	   CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	   customer2.setEmail("Ben@contoso.com");
	   customer2.setPhoneNumber("425-555-0102");
	   batchOperation.insertOrReplace(customer2);

	   // Create a third customer entity to add to the table.
	   CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
	   customer3.setEmail("Denise@contoso.com");
	   customer3.setPhoneNumber("425-555-0103");
	   batchOperation.insertOrReplace(customer3);

	   // Execute the batch of operations on the "people" table.
	   cloudTable.execute(batchOperation);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Some things to note on batch operations:

- You can perform up to 100 insert, delete, merge, replace, insert or merge, and insert or replace operations in any combination in a single batch.
- A batch operation can have a retrieve operation, if it is the only operation in the batch.
- All entities in a single batch operation must have the same partition key.
- A batch operation is limited to a 4MB data payload.

## <a name="RetrieveEntities"> </a>How to: Retrieve all entities in a partition

To query a table for entities in a partition, you can use a **TableQuery**. Call **TableQuery.from** to create a query on a particular table that returns a specified result type. The following code specifies a filter for entities where 'Smith' is the partition key. **TableQuery.generateFilterCondition** is a helper method to create filters for queries. Call **where** on the reference returned by the **TableQuery.from** method to apply the filter to the query. When the query is executed with a call to **execute** on the **CloudTable** object, it returns an **Iterator** with the **CustomerEntity** result type specified. You can then use the **Iterator** returned in a for each loop to consume the results. This code prints the fields of each entity in the query results to the console.

    try
    {
    	// Define constants for filters.
    	final String PARTITION_KEY = "PartitionKey";
    	final String ROW_KEY = "RowKey";
    	final String TIMESTAMP = "Timestamp";

    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();
			
	   // Create a cloud table object for the table.
	   CloudTable cloudTable = tableClient.getTableReference("people");

    	// Create a filter condition where the partition key is "Smith".
    	String partitionFilter = TableQuery.generateFilterCondition(
	       PARTITION_KEY, 
	       QueryComparisons.EQUAL,
	       "Smith");

	   // Specify a partition query, using "Smith" as the partition key filter.
	   TableQuery<CustomerEntity> partitionQuery =
	       TableQuery.from(CustomerEntity.class)
	       .where(partitionFilter);

        // Loop through the results, displaying information about the entity.
        for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() + 
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="RetrieveRange"> </a>How to: Retrieve a range of entities in a partition

If you don't want to query all the entities in a partition, you can specify a range by using comparison operators in a filter. The following code combines two filters to get all entities in partition "Smith" where the row key (first name) starts with a letter up to 'E' in the alphabet. Then it prints the query results. If you use the entities added to the table in the batch insert section of this guide, only two entities are returned this time (Ben and Denise Smith); Jeff Smith is not included.

    try
    {
    	// Define constants for filters.
    	final String PARTITION_KEY = "PartitionKey";
    	final String ROW_KEY = "RowKey";
    	final String TIMESTAMP = "Timestamp";
			
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

	   // Create the table client.
	   CloudTableClient tableClient = storageAccount.createCloudTableClient();

	   // Create a cloud table object for the table.
	   CloudTable cloudTable = tableClient.getTableReference("people");

    	// Create a filter condition where the partition key is "Smith".
    	String partitionFilter = TableQuery.generateFilterCondition(
	       PARTITION_KEY, 
	       QueryComparisons.EQUAL,
	       "Smith");

    	// Create a filter condition where the row key is less than the letter "E".
    	String rowFilter = TableQuery.generateFilterCondition(
	       ROW_KEY, 
	       QueryComparisons.LESS_THAN,
	       "E");

    	// Combine the two conditions into a filter expression.
    	String combinedFilter = TableQuery.combineFilters(partitionFilter, 
	        Operators.AND, rowFilter);

    	// Specify a range query, using "Smith" as the partition key,
    	// with the row key being up to the letter "E".
    	TableQuery<CustomerEntity> rangeQuery =
	       TableQuery.from(CustomerEntity.class)
	       .where(combinedFilter);

    	// Loop through the results, displaying information about the entity
        for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
            System.out.println(entity.getPartitionKey() +
                " " + entity.getRowKey() +
                "\t" + entity.getEmail() +
                "\t" + entity.getPhoneNumber());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="RetriveSingle"> </a>How to: Retrieve a single entity

You can write a query to retrieve a single, specific entity. The following code calls **TableOperation.retrieve** with partition key and row key parameters to specify the customer "Jeff Smith", instead of creating a **TableQuery** and using filters to do the same thing. When executed, the retrieve operation returns just one entity, rather than a collection. The **getResultAsType** method casts the result to the type of the assignment target, a **CustomerEntity** object. If this type is not compatible with the type specified for the query, an exception will be thrown. A null value is returned if no entity has an exact partition and row key match. Specifying both partition and row keys in a query is the fastest way to retrieve a single entity from the Table service.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    	TableOperation retrieveSmithJeff = 
	       TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

	   // Submit the operation to the table service and get the specific entity.
	   CustomerEntity specificEntity =
    		cloudTable.execute(retrieveSmithJeff).getResultAsType();
			
    	// Output the entity.
    	if (specificEntity != null)
    	{
            System.out.println(specificEntity.getPartitionKey() +
                " " + specificEntity.getRowKey() +
                "\t" + specificEntity.getEmail() +
                "\t" + specificEntity.getPhoneNumber());
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="ModifyEntity"> </a>How to: Modify an entity

To modify an entity, retrieve it from the table service, make changes to the entity object, and save the changes back to the table service with a replace or merge operation. The following code changes an existing customer's phone number. Instead of calling **TableOperation.insert** like we did to insert, this code calls **TableOperation.replace**. The **CloudTable.execute** method calls the table service, and the entity is replaced, unless another application changed it in the time since this application retrieved it. When that happens, an exception is thrown, and the entity must be retrieved, modified, and saved again. This optimistic concurrency retry pattern is common in a distributed storage system.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount =
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    	TableOperation retrieveSmithJeff = 
	       TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    	// Submit the operation to the table service and get the specific entity.
    	CustomerEntity specificEntity =
		  cloudTable.execute(retrieveSmithJeff).getResultAsType();

    	// Specify a new phone number.
    	specificEntity.setPhoneNumber("425-555-0105");

    	// Create an operation to replace the entity.
    	TableOperation replaceEntity = TableOperation.replace(specificEntity);

    	// Submit the operation to the table service.
    	cloudTable.execute(replaceEntity);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="QueryProperties"> </a>How to: Query a subset of entity properties

A query to a table can retrieve just a few properties from an entity. This technique, called projection, reduces bandwidth and can improve query performance, especially for large entities. The query in the following code uses the **select** method to return only the email addresses of entities in the table. The results are projected into a collection of **String** with the help of an **EntityResolver**, which does the type conversion on the entities returned from the server. You can learn more about projection in this [blog post][]. Note that projection is not supported on the local storage emulator, so this code runs only when using an account on the table service.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

    	// Create the table client.
    	CloudTableClient tableClient = storageAccount.createCloudTableClient();

    	// Create a cloud table object for the table.
    	CloudTable cloudTable = tableClient.getTableReference("people");

    	// Define a projection query that retrieves only the Email property
    	TableQuery<CustomerEntity> projectionQuery = 
	       TableQuery.from(CustomerEntity.class)
	       .select(new String[] {"Email"});

    	// Define a Entity resolver to project the entity to the Email value.
    	EntityResolver<String> emailResolver = new EntityResolver<String>() {
            @Override
            public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
                return properties.get("Email").getValueAsString();
            }
        };

        // Loop through the results, displaying the Email values.
        for (String projectedString : 
            cloudTable.execute(projectionQuery, emailResolver)) {
                System.out.println(projectedString);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="InsertOrReplace"> </a>How to: Insert or Replace an entity

Often you want to add an entity to a table without knowing if it already exists in the table. An insert-or-replace operation allows you to make a single request which will insert the entity if it does not exist or replace the existing one if it does. Building on prior examples, the following code inserts or replaces the entity for "Walter Harp". After creating a new entity, this code calls the **TableOperation.insertOrReplace** method. This code then calls **execute** on the **CloudTable** object with the table and the insert or replace table operation as the parameters. To update only part of an entity, the **TableOperation.insertOrMerge** method can be used instead. Note that insert-or-replace is not supported on the local storage emulator, so this code runs only when using an account on the table service. You can learn more about insert-or-replace and insert-or-merge in this [blog post][].

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create a new customer entity.
        CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
        customer5.setEmail("Walter@contoso.com");
        customer5.setPhoneNumber("425-555-0106");

        // Create an operation to add the new customer to the people table.
        TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

        // Submit the operation to the table service.
        cloudTable.execute(insertCustomer5);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteEntity"> </a>How to: Delete an entity

You can easily delete an entity after you have retrieved it. Once the entity is retrieved, call **TableOperation.delete** with the entity to delete. Then call **execute** on the **CloudTable** object. The following code retrieves and deletes a customer entity.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Create a cloud table object for the table.
        CloudTable cloudTable = tableClient.getTableReference("people");

        // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
        TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

        // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
        CustomerEntity entitySmithJeff =
            cloudTable.execute(retrieveSmithJeff).getResultAsType();

        // Create an operation to delete the entity.
        TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

        // Submit the delete operation to the table service.
        cloudTable.execute(deleteSmithJeff);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="DeleteTable"> </a>How to: Delete a table

Finally, the following code deletes a table from a storage account. A table which has been deleted will be unavailable to be recreated for a period of time following the deletion, usually less than forty seconds.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the table client.
        CloudTableClient tableClient = storageAccount.createCloudTableClient();

        // Delete the table and all its data if it exists.
        CloudTable cloudTable = new CloudTable("people",tableClient);
        cloudTable.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## <a name="NextSteps"> </a>Next steps

Now that you've learned the basics of table storage, follow these links to learn how to do more complex storage tasks.

- [Azure Storage SDK for Java]
- [Azure Storage Client SDK Reference]
- [Azure Storage REST API]
- [Azure Storage Team Blog]

[Azure SDK for Java]: http://www.windowsazure.com/zh-tw/develop/java/
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage Client SDK Reference]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: http://msdn.microsoft.com/zh-tw/library/azure/gg433040.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[blog post]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

<!--HONumber=35_1-->
