<properties 
	pageTitle="在 Azure 網站建立數位行銷活動" 
	description="本指南提供如何使用 Azure 網站來建立數位行銷活動的技術概觀。這包括部署、社交媒體整合、調整策略和監視。" 
	editor="jimbe" 
	manager="wpickett" 
	authors="cephalin" 
	services="web-sites" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2014" 
	ms.author="cephalin"/>

# Azure 網站上的數位行銷活動
[Azure 網站]是數位行銷活動的絕佳選擇。數位行銷活動通常只有短暫週期，目的是推廣短期的行銷目標。有兩個需要考量的主要案例。在第一個案例中，協力廠商行銷公司在促銷期間為其客戶建立及管理行銷活動。第二個案例則涉及行銷公司建立數位行銷活動資源擁有權，然後將該擁有權轉移給其客戶。客戶再接著自行執行和管理數位行銷活動。這是這兩種案例的絕佳解決方案。 

> [AZURE.NOTE] 如果您要在註冊帳戶前開始使用 Azure 網站，請移至 <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>，您可以在 Azure 網站中立即建立短期的免費 ASP.NET 簡易版網站。無需使用信用卡，也不用簽約。

以下是使用 Azure 網站進行全域、多管道之數位行銷活動的範例。此範例示範只要撰寫 Azure 網站和其他服務、加上最少的技術投資便能完成的工作。**按一下拓樸中的項目以讀取詳細資訊。** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/digital-marketing-notitle.svg" width="100%" height="100%"></object>

> [WACOM.NOTE]
> 本指南提供一些與在 Azure 網站中執行數位行銷活動整合的最常見領域和工作。不過，還有其他您可在 Azure 網站中實作的常見解決方案。若要檢閱這些解決方案，請參閱關於<a href="http://azure.microsoft.com/manage/services/web-sites/global-web-presence-solution-overview/">全域網站空間</a>和<a href="http://azure.microsoft.com/manage/services/web-sites/business-application-solution-overview">商務應用程式</a>上的其他指南。

### 從頭開始建立或利用現有的資產

快速地從資源庫中受歡迎的 CMS 建立新的網站，或將各種不同語言和架構的現有 Web 資產用於 Azure 網站。 

Azure 資源庫提供來自受歡迎的網站內容管理系統 (CMS) 的範本，例如 [Orchard]、[Umbraco]、[Drupal] 及 [WordPress]。您可以使用您最愛的 CMS 類別建立網站。您可以從各種資料庫後端 (包括 [Azure SQL 資料庫]和 [MySQL]) 中選擇以符合您的需求。

您現有的 Web 資產 (無論是 .NET、PHP、Java、Node.js 或 Python) 都可以在 Azure 網站上執行。您可以使用熟悉的 [FTP] 工具將它們移至 Azure 網站。如果您經常建立數位行銷活動，您可能在原始檔控制管理系統已經有 Web 資產。您可以直接從受歡迎的原始檔控制選項 (例如 [Visual Studio]、[Visual Studio Online] 、及 [Git] (本機、GitHub、BitBucket、DropBox 及 Mercurial 等) 部署至 Azure 網站。

### 維持敏捷性

透過直接從您現有的原始檔控制持續發佈，以及在 Azure 網站中執行 A/B 測試，以維持敏捷性。  

在網站的計劃、原型設計及初期開發期間，您和您的客戶可以透過[部署到預備位置] (在 Azure 網站中)，以在行銷活動網站上線之前，先查看網站的實際運作版本。透過整合原始控制檔與 Azure 網站，您可以[持續發佈]到預備位置，而且不需停機，在準備好時即可切換到實際執行環境。 

此外，在規劃時即時網站的變更時，您可以使用 [在實際執行環境中測試] 功能輕鬆地[執行 A/B 測試]和分析實際的使用者行為，以協助您在設計網站做出明智的決策。


### 社交網路化

透過向受歡迎的提供者 (例如 Facebook 與 Twitter) 驗證，Azure 網站中的數位行銷活動可以和社交媒體整合。如需在 ASP.NET 應用程式使用這個方法的範例，請參閱[將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 應用程式部署至 Azure 網站]。 

此外，每個社交媒體網站通常會提供透過 .NET 和許多其他架構來與它整合的資訊。

### 使用豐富的媒體並連線到所有裝置

使用其他 Azure 服務豐富您的數位行銷活動，例如：

-  使用 [Azure 媒體服務]以全域方式上傳和串流處理視訊
-  使用 [Azure Marketplace 中的 SendGrid 服務]傳送電子郵件給使用者
-  使用[行動服務]在 Windows、iOS 及 Android 裝置上建立空間
-  使用[通知中樞]傳送推播通知到數百萬個裝置

### 全球化

使用 Azure 流量管理員為地區網站提供服務，並使用 Azure CDN 快速提供內容，以達到全球化

若要為不同地區的全球客戶提供服務，請使用 [Azure 流量管理員]，將網站訪客路由至提供最佳效能的地區網站。或者，您可以將網站負載平均分散到裝載於多個地區的多個網站複本。

透過[將您的網站與 Azure CDN] 整合，以將您的靜態內容快速提供給全球的使用者Azure CDN 會快取 [CDN 節點]中最靠近使用者的靜態內容，儘可能減少延遲和與您網站的連線。

### 最佳化

透過使用自動調整規模自動調整、使用 Azure Redis Cache 執行快取、使用 WebJobs 執行背景工作，以及使用 Azure 流量管理員維持高可用性，以最佳化您的網站。

Azure 網站[向上延展和向外延展]的能力非常適合無法預期的工作負載，這正是數位行銷活動的情況。透過 [Azure 管理入口網站]以手動方式、透過[服務管理 API] 或 [PowerShell 指令碼處理]以程式設計方式，或是使用「自動調整規模」功能自動地將您的網站向外延展。在**「標準」**主控方案中，「自動調整規模」可讓您依據 CPU 使用量自動向外延展網站。這項功能只會在必要時依據使用者活動向外延展網站，因此可協助您達到最大敏捷性，同時將成本降至最低。如需最佳做法，請參閱 [Troy Hunt] 的[我所學到關於使用 Azure 快速調整網站的 10 件事]。

使用 [Azure Redis 快取] 加速您網站的回應速度使用它來快取後端資料庫和其他項目的資料，例如 [ASP.NET 工作階段狀態]與[輸出快取]。

使用 [Azure 流量管理員]來維持網站的高可用性。使用**「容錯移轉」**方法時，若主要網站發生問題，流量管理員會自動將流量路由至次要網站。

### 監視及分析

使用 Azure 或協力廠商工具，掌握關於您網站效能的最新資訊。接收重要網站事件的警示。使用 Application Insight 或使用 HDInsight 的 Web 記錄分析，取得使用者深入觀點。 

在 Azure 網站儀表板中取得網站目前之效能度量和資源配額的[快速瀏覽]。如需您的應用程式的可用性、效能和使用方式的 360 度檢視，請使用 [Azure Application Insights] 提供快速和強大的疑難排解、診斷和使用方式的深入觀點。或者，使用像是 [New Relic] 的協力廠商工具，提供您網站的進階監視資料。

在**「標準」**主控方案中，每當您的網站沒有回應時，監視網站回應便會收到電子郵件通知。如需詳細資訊，請參閱 [如何：在 Windows Azure 中接收警示通知及管理警示規則]。

## 其他資源

- [Azure 網站文件](/zh-tw/documentation/services/websites/)
- [Azure 網站的學習地圖](websites-learning-map.md)
- [Azure Web 部落格](/blog/topics/web/)


[Azure 網站]:/zh-tw/services/websites/

[Orchard]:/zh-tw/documentation/articles/web-sites-dotnet-orchard-cms-gallery/
[Umbraco]:/zh-tw/documentation/articles/web-sites-gallery-umbraco/
[Drupal]:/zh-tw/documentation/articles/web-sites-php-migrate-drupal/
[WordPress]:/zh-tw/documentation/articles/web-sites-php-web-site-gallery/
  
[MySQL]:/zh-tw/documentation/articles/web-sites-php-mysql-deploy-use-git/
[Azure SQL Database]:/zh-tw/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[FTP]:/zh-tw/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/zh-tw/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/zh-tw/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/zh-tw/documentation/articles/web-sites-publish-source-control/

[部署至預備位置]:/zh-tw/documentation/articles/web-sites-staged-publishing/ 
[持續發佈]:http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[執行 A/B 測試]:http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 應用程式部署至 Azure 網站]:/zh-tw/develop/net/tutorials/web-site-with-sql-database/

[Azure 媒體服務]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Azure Marketplace 中的 SendGrid 服務]:/zh-tw/documentation/articles/sendgrid-dotnet-how-to-send-email/
[行動服務]:/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
[通知中樞]:/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[Azure 流量管理員]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[使用 Azure CDN 整合您的網站]:/zh-tw/documentation/articles/cdn-websites-with-cdn/ 
[CDN 節點]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[向上延展和向外延展]:/zh-tw/manage/services/web-sites/how-to-scale-websites/
[Azure 管理入口網站]:http://manage.windowsazure.com/
[服務管理 API]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell 指令碼處理]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy Hunt]:https://twitter.com/troyhunt
[我所學到關於使用 Azure 快速調整網站的 10 件事]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis 快取]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET 工作階段狀態]:https://msdn.microsoft.com/zh-tw/library/azure/dn690522.aspx
[輸出快取]:https://msdn.microsoft.com/zh-tw/library/azure/dn798898.aspx

[快速瀏覽]:/zh-tw/manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:/zh-tw/develop/net/how-to-guides/new-relic/
[作法：在 Azure 中接收警示通知及管理警示規則]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

  
  [gitstaging]:http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites  


<!--HONumber=42-->
