<properties title="開始使用 Azure 儲存體" pageTitle="開始使用 Azure 儲存體" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [開始使用][開始使用]
> -   [發生什麼情形][發生什麼情形]

### <span id="whathappened">我的專案發生什麼情形？</span>

###### 加入參考

Azure 儲存體 NuGet 封裝加入至 Visual Studio 專案。
此封裝加入下列 .NET 參考：

-   `Microsoft.Data.Edm`
-   `Microsoft.Data.OData`
-   `Microsoft.Data.Services.Client`
-   `Microsoft.WindowsAzure.Configuration`
-   `Microsoft.WindowsAzure.Storage`
-   `Newtonsoft.Json`
-   `System.Data`
-   `System.Spatial`

###### 加入 Azure 儲存體的連接字串

已使用所選儲存體帳戶的連接字串和金鑰建立元素。已修改下列檔案：

-   `ServiceDefinition.csdef`
-   `ServiceConfiguration.Cloud.cscfg`
-   `ServiceConfiguration.Local.cscfg`

  [開始使用]: /documentation/articles/vs-storage-cloud-services-getting-started-blobs/
  [發生什麼情形]: /documentation/articles/vs-storage-cloud-services-what-happened/
