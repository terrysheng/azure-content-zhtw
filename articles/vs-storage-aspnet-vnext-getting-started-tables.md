<properties title="開始使用 Azure 儲存體" pageTitle="開始使用 Azure 儲存體" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [開始使用][開始使用]
> -   [發生什麼情形][發生什麼情形]

## 開始使用 Azure 儲存體 (ASP.NET vNext 專案)

> [AZURE.SELECTOR]
>
> -   [Blob][Blob]
> -   [佇列][佇列]
> -   [資料表][開始使用]

Azure 資料表儲存體服務可讓您儲存大量的結構化資料。此服務是一個 NoSQL 資料存放區，接受來自 Azure 雲端內外經過驗證的呼叫。Azure 資料表很適合儲存結構化、非關聯式資料。如需詳細資訊，請參閱[如何從 .NET 使用資料表儲存體][如何從 .NET 使用資料表儲存體] (英文)。

若要在 ASP.NET vNext 專案中以程式設計方式存取資料表，您需要執行下列工作。

1.  將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端。

        using Microsoft.Framework.ConfigurationModel;

2.  使用下列程式碼來取得組態設定。

        var config = new Configuration();
        config.AddJsonFile("config.json");

##### 取得儲存體連接字串

您必須先取得將存放資料表的儲存體帳戶的連接字串，才能使用資料表。您可以使用 **CloudStorageAccount** 類型來代表儲存體帳戶資訊。如果您使用 ASP.NET vNext 專案，您可以呼叫 Configuration 物件的 get 方法，從 Azure 服務組態中取得儲存體連接字串和儲存體帳戶資訊，如下列程式碼所示。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

如需詳細資訊，請參閱 [ASP.NET vNext][ASP.NET vNext]。

  [開始使用]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-tables/
  [發生什麼情形]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [Blob]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [佇列]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-queues/
  [如何從 .NET 使用資料表儲存體]: http://azure.microsoft.com/zh-tw/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "如何從 .NET 使用資料表儲存體"
  [ASP.NET vNext]: http://www.asp.net/vnext
