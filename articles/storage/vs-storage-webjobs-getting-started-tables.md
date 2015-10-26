<properties
	pageTitle="開始使用 Azure 儲存體和 Visual Studio 已連接服務 (WebJob 專案)"
	description="在使用 Visual Studio 已連接服務連接到儲存體帳戶之後，如何於 Visual Studio Azure WebJobs 專案中開始使用 Azure 資料表儲存體"
	services="storage"
	documentationCenter=""
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="patshea"/>

# 開始使用 Azure 儲存體 (Azure WebJob 專案)

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-webjobs-getting-started-tables.md)
> - [What happened](vs-storage-webjobs-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-webjobs-getting-started-blobs.md)
> - [Queues](vs-storage-webjobs-getting-started-queues.md)
> - [Tables](vs-storage-webjobs-getting-started-tables.md)



## 概觀

本文提供了 C# 程式碼範例，示範如何透過 Azure 資料表儲存體服務使用 Azure WebJobs SDK 1.x 版。此程式碼範例會使用 [WebJobs SDK](websites-dotnet-webjobs-sdk.md) 1.x 版。

Azure 資料表儲存體服務可讓您儲存大量的結構化資料。此服務是一個 NoSQL 資料存放區，接受來自 Azure 雲端內外經過驗證的呼叫。Azure 資料表很適合儲存結構化、非關聯式資料。如需詳細資訊，請參閱[如何使用 .NET 的資料表儲存體](storage-dotnet-how-to-use-tables.md/#create-a-table "如何使用 .NET 的資料表儲存體")。


有一些程式碼片段顯示用於以[手動方式呼叫](vs-storage-webjobs-getting-started-blobs.md#manual)函式的 **Table** 屬性，亦即不是使用其中一個觸發程序屬性。

## 如何將實體新增至資料表

若要將實體新增至資料表，請將 **Table** 屬性搭配 **ICollector<T>** 或 **IAsyncCollector<T>** 參數使用，其中 **T** 會指定您想要加入之實體的結構描述。屬性建構函式採用字串參數，來指定資料表的名稱。

以下程式碼範例會將 **Person** 實體加入名為 *Ingress* 的資料表。

		[NoAutomaticTrigger]
		public static void IngressDemo(
		    [Table("Ingress")] ICollector<Person> tableBinding)
		{
		    for (int i = 0; i < 100000; i++)
		    {
		        tableBinding.Add(
		            new Person() {
		                PartitionKey = "Test",
		                RowKey = i.ToString(),
		                Name = "Name" }
		            );
		    }
		}

通常您搭配 **ICollector** 使用的類型是衍生自 **TableEntity** 或實作 **ITableEntity** 而得，但其實並不需要這麼做。下列任一 **Person** 類別都能搭配前面 **Ingress** 方法中所示的程式碼使用。

		public class Person : TableEntity
		{
		    public string Name { get; set; }
		}

		public class Person
		{
		    public string PartitionKey { get; set; }
		    public string RowKey { get; set; }
		    public string Name { get; set; }
		}

如果您想要直接使用 Azure 儲存體 API，可將 **CloudStorageAccount** 參數新增至方法簽章。

## 即時監視

因為資料外送流量函式經常處理大量資料，所以 WebJobs SDK 儀表板提供即時監視資料。[引動過程記錄] 區段可告訴您是否仍有執行中的函式。

![輸入函式執行中](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

[引動過程詳細資料] 頁面會報告執行中函式的進度 (寫入的實體數目)，並讓您有機會將它中止。

![輸入函式執行中](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

當函式完成時，[引動過程詳細資料] 頁面會報告寫入的資料列數目。

![輸入函式已完成](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## 如何讀取資料表中的多個實體

若要讀取資料表，請使用 **Table** 屬性與 **IQueryable<T>** 參數，其中類型 **T** 是衍生自 **TableEntity** 或實作 **ITableEntity** 而得。

下列程式碼範例會讀取並記錄 **Ingress** 資料表中的所有資料列：

		public static void ReadTable(
		    [Table("Ingress")] IQueryable<Person> tableBinding,
		    TextWriter logger)
		{
		    var query = from p in tableBinding select p;
		    foreach (Person person in query)
		    {
		        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
		            person.PartitionKey, person.RowKey, person.Name);
		    }
		}

### 如何讀取資料表中的單一實體

**Table** 屬性建構函式搭配兩個額外參數，可在想要繫結到單一資料表實體時，讓您指定資料分割索引鍵與資料列索引鍵。

下列程式碼範例會根據在佇列訊息中收到的資料分割索引鍵與資料列索引鍵值，來讀取 **Person** 實體的資料表列：

		public static void ReadTableEntity(
		    [QueueTrigger("inputqueue")] Person personInQueue,
		    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
		    TextWriter logger)
		{
		    if (personInTable == null)
		    {
		        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
		                personInQueue.PartitionKey, personInQueue.RowKey);
		    }
		    else
		    {
		        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
		                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
		    }
		}


本範例中的 **Person** 類別不必實作 **ITableEntity**。

## 如何直接利用 .NET 儲存體 API 來使用資料表

您也可以使用 **Table** 屬性搭配 **CloudTable** 物件，以更有彈性的方式使用資料表。

下列程式碼範例使用 **CloudTable** 物件將單一實體新增至 *Ingress* 資料表。

		public static void UseStorageAPI(
		    [Table("Ingress")] CloudTable tableBinding,
		    TextWriter logger)
		{
		    var person = new Person()
		        {
		            PartitionKey = "Test",
		            RowKey = "100",
		            Name = "Name"
		        };
		    TableOperation insertOperation = TableOperation.Insert(person);
		    tableBinding.Execute(insertOperation);
		}

如需如何使用 **CloudTable** 物件的詳細資訊，請參閱[如何使用 .NET 的資料表儲存體](../storage-dotnet-how-to-use-tables.md)。

## 佇列操作說明文章所涵蓋的相關主題

如需如何處理佇列訊息所觸發的資料表處理的相關資訊，或是非資料表處理特有的 WebJobs SDK 案例，請參閱[如何透過 WebJobs SDK 使用 Azure 佇列儲存體](vs-storage-webjobs-getting-started-queues.md)。



## 後續步驟

本文提供的程式碼範例示範如何處理使用 Azure 資料表的常見案例。如需 Azure WebJobs 和 WebJobs SDK 的詳細資訊，請參閱[Azure WebJobs 建議使用的資源](http://go.microsoft.com/fwlink/?linkid=390226)。

<!---HONumber=Oct15_HO3-->