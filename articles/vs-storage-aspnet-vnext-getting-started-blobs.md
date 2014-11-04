<properties title="開始使用 Azure 儲存體" pageTitle="開始使用 Azure 儲存體" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [開始使用][開始使用]
> -   [發生什麼情形][發生什麼情形]

## 開始使用 Azure 儲存體 (ASP.NET vNext 專案)

> [AZURE.SELECTOR]
>
> -   [Blob][開始使用]
> -   [佇列][佇列]
> -   [資料表][資料表]

Azure 二進位大型物件 (Windows Azure Blob) 儲存是一項儲存大量非結構化資料的服務，全球任何地方都可透過 HTTP 或 HTTPS 來存取這些資料。單一 Blob 可以是任何大小。Blob 可以是影像、音訊和視訊檔、原始資料及文件檔案。

若要開始，您需要建立 Azure 儲存體帳戶，然後在儲存體中建立一或多個容器。例如，您可以建立稱為 “Scrapbook” 的儲存體，然後在儲存體中建立稱為 “images” 的容器來儲存圖片，再建立另一個稱為 “audio” 的容器來儲存音訊檔。建立容器之後，就可以將個別的 Blob 檔案上傳至這些容器。如需有關如何以程式設計方法操作 Blob 的詳細資訊，請參閱[如何從 .NET 使用 Blob 儲存體][如何從 .NET 使用 Blob 儲存體] (英文)。

若要在 ASP.NET vNext 專案中以程式設計方式存取 Blob，您需要執行下列工作。

1.  將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端。

        using Microsoft.Framework.ConfigurationModel;

2.  使用下列程式碼來取得組態設定。

        var config = new Configuration();
        config.AddJsonFile("config.json");

##### 取得儲存體連接字串

您必須先取得將存放 Blob 的儲存體帳戶的連接字串，才能使用 Blob。您可以使用 **CloudStorageAccount** 類型來代表儲存體帳戶資訊。如果您使用 ASP.NET vNext 專案，您可以呼叫 Configuration 物件的 get 方法，從 Azure 服務組態中取得儲存體連接字串和儲存體帳戶資訊，如下列程式碼所示。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

如需詳細資訊，請參閱 [ASP.NET vNext][ASP.NET vNext]。

  [開始使用]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [發生什麼情形]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [佇列]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-queues/
  [資料表]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-tables/
  [如何從 .NET 使用 Blob 儲存體]: http://azure.microsoft.com/zh-tw/documentation/articles/storage-dotnet-how-to-use-blobs/ "如何從 .NET 使用 Blob 儲存體"
  [vs-storage-getting-started-blobs-include]: ../includes/vs-storage-getting-started-blobs-include.md
  [ASP.NET vNext]: http://www.asp.net/vnext
