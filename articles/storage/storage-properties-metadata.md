
<properties 
  pageTitle="在 Azure 儲存體中設定和擷取物件的屬性與中繼資料 | Microsoft Azure" 
  description="將物件的自訂中繼資料儲存在Azure 儲存體中，並設定和擷取系統屬性。" 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="adinah" 
  editor=""/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="09/03/2015" 
  ms.author="tamram"/>


# 設定與擷取屬性和中繼資料 #

## 概觀

除了所包含之資料外，在 Azure 儲存體支援系統屬性和使用者定義中繼資料中的物件：

*   **系統屬性。** 系統屬性存在於每個儲存體資源上。其中有些系統屬性可以讀取或設定，有些則是唯讀的。實際上，有些系統屬性會對應至特定的標準 HTTP 標頭。Azure 儲存體用戶端程式庫會為您維護這些 HTTP 標頭。  

*   **使用者定義的中繼資料。** 使用者定義的中繼資料是您針對給定的資源，以名稱/值組的形式指定的中繼資料。您可以使用中繼資料將額外的值與儲存體資源一同儲存。這些值僅供自己的用途使用，不會影響資源的運作方式。

## 設定與擷取屬性

擷取儲存體資源的屬性和中繼資料值是一個兩步驟程序。您必須先呼叫 **FetchAttributes** 或 **FetchAttributesAsync** 方法明確地擷取這些值，才能開始讀取。

> [AZURE.IMPORTANT]除非您呼叫其中一個 **FetchAttributes** 方法，否則無法填入儲存體資源的屬性和中繼資料值。

若要設定 Blob 的屬性，指定屬性值，然後呼叫 **SetProperties** 或 **SetPropertiesAsync** 方法。

下列程式碼範例會建立一個容器，並將屬性值寫入主控台視窗。

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	//Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## 設定與擷取中繼資料

您可以針對 Blob 或容器資源，將中繼資料指定為一個或多個名稱/值組。若要設定中繼資料，將名稱/值組加入至資源的**中繼資料**集合，然後呼叫 **SetMetadata** 方法，將這些值儲存至服務。

> [AZURE.NOTE]：您的中繼資料名稱必須符合 C# 識別碼的命名慣例。
 
若要擷取 Blob 或容器的中繼資料，請呼叫 **FetchAttributes** 方法以填入**中繼資料**集合，然後讀取這些值。

下列程式碼範例會建立一個新的容器，並為其設定中繼資料，然後讀回中繼資料值：

         
	// Account name and key.  Modify for your account.
	<span style="color:Blue;">string accountName = <span style="color:#A31515;">"myaccount";
	<span style="color:Blue;">string accountKey = <span style="color:#A31515;">"SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";

	// Get a reference to the storage account and client with authentication credentials.
	StorageCredentials credentials = <span style="color:Blue;">new StorageCredentials(accountName, accountKey);
	CloudStorageAccount storageAccount = <span style="color:Blue;">new CloudStorageAccount(credentials, <span style="color:Blue;">true);
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Set metadata for the container.
	container.Metadata[<span style="color:#A31515;">"category"] = <span style="color:#A31515;">"images";
	container.Metadata[<span style="color:#A31515;">"owner"] = <span style="color:#A31515;">"azure";
	container.SetMetadata();

	// Get container metadata.
	container.FetchAttributes();
	<span style="color:Blue;">foreach (<span style="color:Blue;">string key <span style="color:Blue;">in container.Metadata.Keys)
	{
	   Console.WriteLine(<span style="color:#A31515;">"Metadata key: " + key);
	   Console.WriteLine(<span style="color:#A31515;">"Metadata value: " + container.Metadata[key]);
	}

	//Clean up.
	container.Delete();

## 另請參閱  

- [Azure Storage Client Library 參考](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [開始使用適用於 .NET 的 Blob 儲存體](storage-dotnet-how-to-use-blobs.md)  
 

<!---HONumber=Sept15_HO2-->