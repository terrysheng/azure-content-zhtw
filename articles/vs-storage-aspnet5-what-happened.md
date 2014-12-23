<properties title="Getting Started with Azure Storage" pageTitle="開始使用 Azure 儲存體" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [開始使用](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [發生什麼情形](/documentation/articles/vs-storage-aspnet5-what-happened/)

###<span id="whathappened">我的專案發生什麼情形？</span>

##### 加入參考

Azure 儲存體 NuGet 封裝已加入至 Visual Studio 專案。  
這個封裝會加入下列 .NET 參考：

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

另外也已加入 NuGet 封裝 **Microsoft.Framework.ConfigurationModel.Json**。

#####加入 Azure 儲存體的連接字串 
在專案的 config.json 檔案中，已使用所選儲存體帳戶的連接字串和金鑰建立一個元素。

如需詳細資訊，請參閱 [ASP.NET 5](http://www.asp.net/vnext)。
