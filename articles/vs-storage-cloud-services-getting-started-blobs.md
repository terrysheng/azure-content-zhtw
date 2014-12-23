<properties title="Getting Started with Azure Storage" pageTitle="開始使用 Azure 儲存體" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [開始使用](/documentation/articles/vs-storage-cloud-services-getting-started-blobs/)
> - [發生什麼情形](/documentation/articles/vs-storage-cloud-services-what-happened/)

## 開始使用 Azure 儲存體 (雲端服務專案)

> [AZURE.SELECTOR]
> - [Blob](/documentation/articles/vs-storage-cloud-services-getting-started-blobs/)
> - [佇列](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [資料表](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)

Azure 二進位大型物件 (Windows Azure Blob) 儲存是一項儲存大量非結構化資料的服務，全球任何地方都可透過 HTTP 或 HTTPS 來存取這些資料。單一 Blob 可以是任何大小。Blob 可以是影像、音訊和視訊檔、原始資料及文件檔案。

若要開始，您需要建立 Azure 儲存體帳戶，然後在儲存體中建立一或多個容器。例如，您可以建立稱為 "Scrapbook" 的儲存體，然後在儲存體中建立稱為 "images" 的容器來儲存圖片，再建立另一個稱為 "audio" 的容器來儲存音訊檔。建立容器之後，就可以將個別的 Blob 檔案上傳至這些容器。如需以程式設計方式操作 Blob 的詳細資訊，請參閱[如何使用 .NET 的 Blob 儲存體](http://azure.microsoft.com/zh-tw/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET")。

將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端。

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

#####取得儲存體連接字串
您必須先取得將存放 Blob 的儲存體帳戶的連接字串，才能使用 Blob。您可以使用 **CloudStorageAccount** 類型來代表儲存體帳戶資訊。在雲端服務專案中，您可以使用 **CloudConfigurationManager** 類型，從 Azure 服務組態中擷取儲存體連接字串和儲存體帳戶資訊，如下列程式碼所示。

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]
