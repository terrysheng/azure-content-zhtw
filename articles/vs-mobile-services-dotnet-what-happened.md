       <properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [開始使用](/documentation/articles/vs-mobile-services-dotnet-getting-started/)
> - [發生什麼情形](/documentation/articles/vs-mobile-services-dotnet-what-happened/)

###<span id="whathappened">我的專案發生什麼情形？</span>

#####加入參考

Azure 行動服務 NuGet 封裝已加入至專案。因此，下列 .NET 參考已加入至專案：

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

#####行動服務的連接字串值

在 App.xaml.cs 檔案中，已使用所選行動服務的應用程式 URL 和應用程式金鑰建立 **MobileServiceClient** 物件。 

[深入了解行動服務](http://azure.microsoft.com/documentation/services/mobile-services/)
