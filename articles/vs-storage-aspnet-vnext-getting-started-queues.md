<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-vnext-getting-started-intro][vs-storage-aspnet-vnext-getting-started-intro]]

# 開始使用 Azure 儲存體

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs" title="Blob" class="current">Blob</a><a href="/zh-tw/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues" title="佇列">佇列</a><a href="/zh-tw/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables" title="資料表">資料表</a></div>

Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。單一佇列訊息的大小上限為 64 KB，而一個佇列可以包含數百萬個訊息，以儲存體帳戶的總容量為限。如需詳細資訊，請參閱[如何從 .NET 使用佇列儲存體][如何從 .NET 使用佇列儲存體] (英文)。

若要在 ASP.NET vNext 專案中以程式設計方式存取佇列，您需要執行下列工作。

1.  取得 Microsoft.WindowsAzure.Storage.dll 組件。您可以使用 NuGet 這樣做。在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [管理 NuGet 封裝]。在線上搜尋 "WindowsAzure.Storage"，再按一下 [安裝] 以安裝 Azure 儲存體封裝與相依性。將此組件的參考加入至專案。
2.  安裝 Microsoft.Framework.ConfigurationModel.Json NuGet 封裝。
3.  將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端。

        using Microsoft.Framework.ConfigurationModel;

4.  使用下列程式碼來取得組態設定。

        var config = new Configuration();
        config.AddJsonFile("config.json");

### 取得儲存體連接字串

您必須先取得將存放佇列的儲存體帳戶的連接字串，才能使用佇列。您可以使用 **CloudStorageAccount** 類型來代表儲存體帳戶資訊。如果您使用 ASP.NET vNext 專案，您可以呼叫 Configuration 物件的 get 方法，從 Azure 服務組態中取得儲存體連接字串和儲存體帳戶資訊，如下列程式碼所示。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include][vs-storage-getting-started-queues-include]]

  [vs-storage-aspnet-vnext-getting-started-intro]: ../includes/vs-storage-aspnet-vnext-getting-started-intro.md
  [Blob]: /zh-tw/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs "Blob"
  [佇列]: /zh-tw/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues "佇列"
  [資料表]: /zh-tw/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables "資料表"
  [如何從 .NET 使用佇列儲存體]: http://azure.microsoft.com/zh-tw/documentation/articles/storage-dotnet-how-to-use-queues/ "如何從 .NET 使用佇列儲存體"
  [vs-storage-getting-started-queues-include]: ../includes/vs-storage-getting-started-queues-include.md
