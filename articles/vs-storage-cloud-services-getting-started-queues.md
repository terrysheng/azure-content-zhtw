<properties 
	pageTitle="開始使用 Azure 儲存體" 
	description="如何開始在 Visual Studio 的雲端服務專案中使用 Azure 佇列儲存體"
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [開始使用](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [發生什麼情形](/documentation/articles/vs-storage-cloud-services-what-happened/)

## 開始使用 Azure 儲存體 (雲端服務專案)

> [AZURE.SELECTOR]
> - [Blob](/documentation/articles/vs-storage-cloud-services-getting-started-blobs)
> - [Queues](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [資料表](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)

Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。單一佇列訊息的大小上限為 64 KB，而一個佇列可以包含數百萬個訊息，以儲存體帳戶的總容量為限。如需詳細資訊，請參閱[如何使用 .NET 的佇列儲存體](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/ "How to use Queue Storage from .NET")。

將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端。

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

#####取得儲存體連接字串
您必須先取得將存放佇列的儲存體帳戶的連接字串，才能使用佇列。您可以使用 **CloudStorageAccount** 類型來代表儲存體帳戶資訊。在雲端服務專案中，您可以使用 **CloudConfigurationManager** 類型，從 Azure 服務組態中擷取儲存體連接字串和儲存體帳戶資訊，如下列程式碼所示。

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]


<!--HONumber=46--> 
