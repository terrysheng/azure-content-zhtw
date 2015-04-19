<properties 
	pageTitle="在 Azure 網站上建立全域網站空間" 
	description="本指南針對如何在 Azure 網站上託管您的組織的 (.COM) 網站提供技術概觀。這包括部署、自訂網域、SSL 和監視。" 
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


# 在 Azure 網站上建立全域網站空間

[Azure 網站]具有為 .COM 網站建立全域網站空間所需的所有功能。無論組織的規模大小，您都需要強固、 安全且可擴充的平台，以促進您的業務、 品牌認知以及客戶通訊。Azure 網站可透過 Microsoft 支援的業務持續性，協助維護公司的品牌與形象。

> [AZURE.NOTE] 如果您要在註冊帳戶前開始使用 Azure 網站，請移至 <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>，您可以在 Azure 網站中立即建立短期的免費 ASP.NET 簡易版網站。無需使用信用卡，也不用簽約。

以下是在 Azure 網站上執行的 .COM 網站範例。此範例示範只要撰寫 Azure 網站和其他服務、加上最少的技術投資便能完成的工作。**按一下拓樸中的項目以讀取詳細資訊。** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/corp-website-visio.svg" width="100%" height="100%"></object>

> [WACOM.NOTE]
> 本指南呈現一些與在 Azure 網站中執行的公用端 .COM 網站整合的最常見領域和工作。不過，還有其他您可在 Azure 網站中實作的常見解決方案。若要檢閱這些解決方案，請同時參閱<a href="http://azure.microsoft.com/manage/services/web-sites/digital-marketing-campaign-solution-overview">數位行銷活動</a> (英文) 與<a href="http://azure.microsoft.com/manage/services/web-sites/business-application-solution-overview">商務應用程式</a> (英文) 方面的其他指南。

### 從頭開始建立或沿用現有資產

從組件庫中受歡迎的 CMS 快速建立新網站，或在 Azure 網站沿用現有各種不同語言與架構的 Web 資產。

Azure 組件庫提供受歡迎網站內容管理系統 (CMS) 的範本，例如 [Orchard]、[Umbraco]、[Drupal] 以及 [WordPress]。您可以使用喜愛的 CMS 風格來建立網站。您可以根據自己的需求從各種不同的資料庫後端中選擇，包括 [Azure SQL Database] 與 [MySQL]。

不論是 .NET、PHP、Java、Node.js 或 Python，您現有的 Web 資產都能在 Azure 網站上執行。您可以使用熟悉的 [FTP] 工具或原始檔控制管理系統，將它們移至 Azure 網站。Azure 網站支援從 [Visual Studio]、[Visual Studio Online] 以及 [Git] (本機、GitHub、BitBucket、DropBox、Mercurial 等) 等受歡迎的原始檔控制選項直接發行。

### 可靠地發行

從現有的原始檔控制系統並即時測試內容，持續地直接發行，可靠地發行網站。 

在網站的計劃、原型設計及初期開發期間，您可以在網站上線之前，先[部署到預備位置]來查看網站的實際運作版本。透過整合原始檔控制與 Azure 網站，您可以[持續發行]到預備位置，並在您準備好時在沒有停機時間的情況下交換至生產環境。如果生產網站發生問題，您也可以立即換回舊版的網站。 

此外，針對作用中的網站規劃變更時，您很容易就能使用在生產環境測試的功能，針對提議的更新[執行 A/B 測試]，並分析實際的使用者行為，幫助您做出明智的網站設計決策。

### 品牌與安全

免費使用網站網域或對應至註冊的網域名稱，然後利用 CA 簽署的 SSL 憑證保護品牌安全。

當您在 Azure 網站執行網站時，可免費使用 **\*.azurewebsites.net** 網域。或者，您可以將網站對應至從任何 DNS 登錄取得 (例如 GoDaddy) 的[自訂網域] (例如 contoso.com)。

如果您收集任何使用者資訊、執行電子商務或管理任何其他機密資料，您可以使用 [HTTPS] 來保護品牌信譽與客戶。**\*.azurewebsites.net** 網域名稱已經隨附 SSL 憑證，而如果您使用自訂網域，可以在 Azure 網站使用該網域的 SSL 憑證。每個月都會收取與各個 SSL 憑證相關聯的費用 (每小時依比例)。如需詳細資訊，請參閱[網站定價詳細資料]。

### 邁向全球

使用 Azure 流量管理員來為遍佈全球的地區網站提供服務，以及使用 Azure CDN 快如閃電地提供內容。

若要向位於全球各地的客戶提供服務，請使用 [Azure 流量管理員]將網站訪客路由到可提供最佳效能的地區網站。或者，您可以將網站負載平均分配給多個地區的多個網站複本。

[整合網站與 Azure CDN]，就能快如閃電地將靜態內容提供給全球的使用者。Azure CDN 會將靜態內容快取在最接近使用者的 [CDN 節點]，讓延遲的情況和與網站連線的需求降至最低。

### 最佳化

透過使用自動調整功能自動地進行調整、使用 Azure Redis 快取功能進行快取、使用 WebJobs 執行背景工作，還有使用 Azure 流量管理員維持高可用性，最佳化 .COM 網站。

無論您的工作負載多寡，Azure 網站都能夠依 .COM 網站的需求[相應增加和放大]。透過 [Azure 管理入口網站]以手動方式、透過[服務管理 API] 或 [PowerShell 指令碼]以程式設計方式，或透過自動調整功能自動相應放大網站。採用「標準」****虛擬主機方案，自動調整功能可讓您根據 CPU 使用率自動相應放大網站。如需最佳做法，請參閱 [Troy Hunt] 的[我所學到關於使用 Azure 快速調整網站的 10 件事]。

使用 [Azure Redis 快取]加速您網站的回應速度。使用此功能，可從後端資料庫以及 [ASP.NET 工作階段狀態]與[輸出快取]等其他項目快取資料。

使用 [Azure 流量管理員]維持網站的高可用性。使用「容錯移轉」****方法，如果主要網站發生問題，流量管理員會自動將流量路由至次要網站。

### 監視和分析

使用 Azure 或協力廠商工具掌握最新的網站效能。收到重大網站事件的警示。使用 Application Insight 或使用 HDInsight 中的 Web 記錄分析，輕鬆取得使用者觀點。 

在 Azure 網站儀表板中，[快速概覽]網站目前的效能度量與資源配額。如需全面檢視應用程式的可用性、效能及使用率，請使用 [Azure Application Insights] 快速地提供精闢的疑難排解、診斷以及使用率觀點。或者，使用協力廠商工具 (例如 [New Relic]) 提供進階的網站監視資料。

採用「標準」**** 虛擬主機方案，可監視網站回應能力，還可在網站毫無回應時，收到電子郵件通知。如需詳細資訊，請參閱 [如何：在 Windows Azure 中接收警示通知及管理警示規則]。

### 使用多媒體並觸達所有裝置

透過多媒體提升 .COM 網站吸睛度，例如：

-  使用 [Azure 媒體服務]在全球上傳和串流處理視訊
-  使用 [Azure 市集中的 SendGrid 服務]將電子郵件傳送給使用者

## 其他資源

- [Azure 網站文件](/zh-tw/documentation/services/websites/)
- [了解 Azure 網站的對應](websites-learning-map.md)
- [Azure Web 部落格](/blog/topics/web/)



[Azure 網站]：/zh-tw/services/websites/

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
[持續發行]:http://rickrainey.com/2014/01/21/continuous-deployment-github-with-azure-web-sites-and-staged-publishing/
[執行 A/B 測試]:http://blogs.msdn.com/b/tomholl/archive/2014/11/10/a-b-testing-with-azure-websites.aspx

[自訂網域]:/zh-tw/documentation/articles/web-sites-custom-domain-name/
[HTTPS]:/zh-tw/documentation/articles/web-sites-configure-ssl-certificate/
[網站定價詳細資料]:/zh-tw/pricing/details/web-sites/#service-ssl

[Azure 流量管理員]:http://www.hanselman.com/blog/CloudPowerHowToScaleAzureWebsitesGloballyWithTrafficManager.aspx
[整合網站與 Azure CDN]:/zh-tw/documentation/articles/cdn-websites-with-cdn/ 
[CDN 節點]:https://msdn.microsoft.com/library/azure/gg680302.aspx

[相應增加和放大]:/zh-tw/manage/services/web-sites/how-to-scale-websites/
[Azure 管理入口網站]:http://manage.windowsazure.com/
[服務管理 API]:http://msdn.microsoft.com/library/windowsazure/ee460799.aspx
[PowerShell 指令碼]:http://msdn.microsoft.com/library/windowsazure/jj152841.aspx
[Troy 搜尋]:https://twitter.com/troyhunt
[我了解使用 Azure 網站快速調整網站的十大事項]:http://www.troyhunt.com/2014/09/10-things-i-learned-about-rapidly.html
[Azure Redis 快取]:/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
[ASP.NET 工作階段狀態]:https://msdn.microsoft.com/zh-tw/library/azure/dn690522.aspx
[輸出快取]:https://msdn.microsoft.com/zh-tw/library/azure/dn798898.aspx

[快速概覽]:/zh-tw/manage/services/web-sites/how-to-monitor-websites/
[Azure Application Insights]:http://blogs.msdn.com/b/visualstudioalm/archive/2015/01/07/application-insights-and-azure-websites.aspx
[New Relic]:/zh-tw/develop/net/how-to-guides/new-relic/
[作法：在 Azure 中接收警示通知及管理警示規則]:http://msdn.microsoft.com/library/windowsazure/dn306638.aspx

[Azure 媒體服務]:http://blogs.technet.com/b/cbernier/archive/2013/09/03/windows-azure-media-services-and-web-sites.aspx
[Azure 市集中的 SendGrid 服務]:/zh-tw/documentation/articles/sendgrid-dotnet-how-to-send-email/



<!--HONumber=42-->
