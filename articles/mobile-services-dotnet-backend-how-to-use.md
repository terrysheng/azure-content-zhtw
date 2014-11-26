<properties linkid="mobile-services-dotnet-backend-how-to-use" urlDisplayName="Use the Mobile Services .NET Backend" pageTitle="Use the Mobile Services .NET Backend - Azure Mobile Services" metaKeywords="" description="Learn the details of the .NET Backend programming model for Azure Mobile Services, including how to work with table data, APIs, authentication, and scheduled jobs" metaCanonical="" services="" documentationCenter="Mobile" title="Use the Mobile Services .NET Backend" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg" />

# 使用行動服務 .NET 後端

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET 後端" class="current">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript 後端">JavaScript 後端</a></div>

本文提供如何在 Azure 行動服務中使用 .NET 後端的詳細資訊以及範例。本主題分成這些小節：

-   [簡介][簡介]
-   [資料表作業][資料表作業]

## <a name="intro"></a>簡介

行動服務 .NET 後端可讓您使用 [ASP.NET Web API][ASP.NET Web API] 和您慣用的 .NET 語言，撰寫豐富的後端商業邏輯。行動服務可將小型程式設計模型介面公開為 [NuGet 封裝][NuGet 封裝]，以便您從 Windows、Android、iOS 的跨平台行動服務用戶端 SDK 順暢地存取您的服務。這些 API 也可確保驗證和推播通知支援也可盡可能簡單地新增。但大部分的程式設計模型皆以 Web API 為基礎，開發人員十分熟悉，因此應能得心應手。

## <a name="table-scripts"></a>資料表作業

行動服務 .NET 後端提供通用的「資料表」抽象，以代表資料庫儲存體的 CRUD 型 HTTP API。此抽象可將資料儲存考量與商業邏輯區隔開來，讓您的行動服務以一致的 JSON 型連線格式公開不同的資料存放區，而跨平台行動服務用戶端 SDK 原本即可辨識此格式。

此程式設計模型的根本是 [**TableController<t>**][**TableController<t>**] 類別，此類別只是針對 CRUD 資料存取模式而自訂的一般 Web API [**ApiController**][**ApiController**]。**TableController** 可使用多種資料存放區，包括 SQL (透過 [Entity Framework][Entity Framework])、[Azure 資料表儲存體][Azure 資料表儲存體]、[MongoDB][MongoDB] 或您本身的自訂存放區。

  [簡介]: #intro
  [資料表作業]: #table-scripts
  [ASP.NET Web API]: http://www.asp.net/web-api
  [NuGet 封裝]: http://www.nuget.org/packages?q=%22mobile+services+.net+backend%22
  [**TableController<t>**]: http://msdn.microsoft.com/library/dn643359.aspx
  [**ApiController**]: http://msdn.microsoft.com/library/system.web.http.apicontroller.aspx
  [Entity Framework]: http://msdn.microsoft.com/data/ef.aspx
  [Azure 資料表儲存體]: http://azure.microsoft.com/documentation/services/storage/
  [MongoDB]: http://www.mongodb.org
