<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-cloud-services-getting-started-intro][vs-storage-cloud-services-getting-started-intro]]

### 開始使用 Azure 儲存體

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/vs-storage-cloud-services-getting-started-blobs" title="Blob" class="current">Blob</a><a href="/zh-tw/documentation/articles/vs-storage-cloud-services-getting-started-queues" title="佇列">佇列</a><a href="/zh-tw/documentation/articles/vs-storage-cloud-services-getting-started-tables" title="資料表">資料表</a></div>

Azure 資料表儲存體服務可讓您儲存大量的結構化資料。此服務是一個 NoSQL 資料存放區，接受來自 Azure 雲端內外經過驗證的呼叫。Azure 資料表很適合儲存結構化、非關聯式資料。如需詳細資訊，請參閱[如何從 .NET 使用資料表儲存體][如何從 .NET 使用資料表儲存體] (英文)。

若要在雲端服務專案中以程式設計方式存取資料表，您需要執行下列工作。

1.  取得 Microsoft.WindowsAzure.Storage.dll 組件。您可以使用 NuGet 這樣做。在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [管理 NuGet 封裝]。在線上搜尋 "WindowsAzure.Storage"，再按一下 [安裝] 以安裝 Azure 儲存體封裝與相依性。將此組件的參考加入至專案。
2.  將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端。

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

###### 取得儲存體連接字串

您必須先取得將存放資料表的儲存體帳戶的連接字串，才能使用資料表。您可以使用 **CloudStorageAccount** 類型來代表儲存體帳戶資訊。在雲端服務專案中，您可以使用 **CloudConfigurationManager** 類型，從 Azure 服務組態中擷取儲存體連接字串和儲存體帳戶資訊，如下列程式碼所示。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include][vs-storage-getting-started-tables-include]]

  [vs-storage-cloud-services-getting-started-intro]: ../includes/vs-storage-cloud-services-getting-started-intro.md
  [Blob]: /zh-tw/documentation/articles/vs-storage-cloud-services-getting-started-blobs "Blob"
  [佇列]: /zh-tw/documentation/articles/vs-storage-cloud-services-getting-started-queues "佇列"
  [資料表]: /zh-tw/documentation/articles/vs-storage-cloud-services-getting-started-tables "資料表"
  [如何從 .NET 使用資料表儲存體]: http://azure.microsoft.com/zh-tw/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "如何從 .NET 使用資料表儲存體"
  [vs-storage-getting-started-tables-include]: ../includes/vs-storage-getting-started-tables-include.md
