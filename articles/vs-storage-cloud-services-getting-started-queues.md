<properties title="開始使用 Azure 儲存體" pageTitle="開始使用 Azure 儲存體" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [開始使用][開始使用]
> -   [發生什麼情形][發生什麼情形]

## 開始使用 Azure 儲存體 (雲端服務專案)

> [AZURE.SELECTOR]
>
> -   [Blob][Blob]
> -   [佇列][開始使用]
> -   [資料表][資料表]

Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。單一佇列訊息的大小上限為 64 KB，而一個佇列可以包含數百萬個訊息，以儲存體帳戶的總容量為限。如需詳細資訊，請參閱[如何從 .NET 使用佇列儲存體][如何從 .NET 使用佇列儲存體] (英文)。

將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

##### 取得儲存體連接字串

您必須先取得將存放佇列的儲存體帳戶的連接字串，才能使用佇列。您可以使用 **CloudStorageAccount** 類型來代表儲存體帳戶資訊。在雲端服務專案中，您可以使用 **CloudConfigurationManager** 類型，從 Azure 服務組態中擷取儲存體連接字串和儲存體帳戶資訊，如下列程式碼所示。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

  [開始使用]: /documentation/articles/vs-storage-cloud-services-getting-started-queues/
  [發生什麼情形]: /documentation/articles/vs-storage-cloud-services-what-happened/
  [Blob]: /documentation/articles/vs-storage-cloud-services-getting-started-blobs
  [資料表]: /documentation/articles/vs-storage-cloud-services-getting-started-tables/
  [如何從 .NET 使用佇列儲存體]: http://azure.microsoft.com/zh-tw/documentation/articles/storage-dotnet-how-to-use-queues/ "如何從 .NET 使用佇列儲存體"
  [vs-storage-getting-started-queues-include]: ../includes/vs-storage-getting-started-queues-include.md
