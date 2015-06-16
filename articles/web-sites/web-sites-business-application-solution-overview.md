<properties 
	pageTitle="在 Azure 網站建立企業營運系統應用程式" 
	description="本指南提供如何使用 Azure 網站來建立內部網路、企業營運系統應用程式的技術概觀。這包括驗證策略、Service Bus Relay 和監視。" 
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



# 在 Azure 網站建立企業營運系統應用程式

[Azure 網站]是企業營運應用程式的絕佳選擇。這些應用程式是供內部業務使用的內部網路應用程式，應受到保護。它們通常需要驗證，一般是針對公司目錄，有一部分則是內部部署資料和服務的存取或整合。 

將企業營運應用程式移至 Azure 網站有幾項主要優點，例如：

-  隨動態工作負載向上和向下調整，例如處理年度績效審查的應用程式。在審查期間，大公司的流量就會激增到高峰層級。Azure 提供調整選項，可讓公司一方面在高流量審查期間相應擴充規模以處理負載，另一方面又可在一年中其餘的時候調整回來以節省開支。 
-  投注較少的心力在基礎結構的取得和管理上，更專注於應用程式開發上
-  提供員工和合作夥伴更多的支援，讓他們可以從任何位置使用應用程式。使用者不須連線到公司網路，即可使用應用程式，而 IT 群組也可避免使用複雜的反向 Proxy 方案。有數個驗證選項可以確保公司應用程式的存取受到保護。

以下是在 Azure 網站上執行之企業營運應用程式的範例。此範例示範只要撰寫 Azure 網站和其他服務、加上最少的技術投資便能完成的工作。**按一下拓樸中的項目以讀取詳細資訊。** 

<object type="image/svg+xml" data="https://sidneyhcontent.blob.core.windows.net/documentation/web-app-notitle.svg" width="100%" height="100%"></object>

<div class="dev-callout">
<strong>附註</strong>
<p>本指南呈現一些與企業營運應用程式整合的最常見領域和工作。不過，還有其他 Azure 網站功能可供您在特定的實作中使用。若要檢閱這些功能，請一併參閱<a href="http://azure.microsoft.com/manage/services/web-sites/global-web-presence-solution-overview/">全域網站空間</a>和<a href="http://azure.microsoft.com/manage/services/web-sites/digital-marketing-campaign-solution-overview">數位行銷活動</a>上的其他指南。</p>
</div>

### 使用現有的資產

將各種語言和架構的現有 Web 資產應用在 Azure 網站。

您現有的 Web 資產 (無論是 .NET、PHP、Java、Node.js 或 Python) 都可以在 Azure 網站上執行。您可以使用熟悉的 [FTP] 工具或您的原始檔控制管理系統將它們移至 Azure 網站。Azure 網站支援從普遍的原始檔控制選項 (例如 [Visual Studio]、[Visual Studio Online] 及 [Git] (本機、GitHub、BitBucket、DropBox 及 Mercurial 等) 直接發佈。

### 保護您的資產

以加密、驗證公司使用者是現場或遠端使用者的方式保護資產，並授權他們使用資產。 

使用 [HTTPS] 保護內部資產，以防止監聽者竊取資訊。***.azurewebsites.net** 網域名稱已經有 SSL 憑證，若您使用自訂網域，您可以將您的 SSL 憑證應用在 Azure 網站中。每個 SSL 憑證每月會有一筆相關費用 (以小時計)。如需詳細資訊，請參閱[網站定價詳細資料]。

針對公司目錄[驗證使用者]。Azure 網站可以向內部部署的身分識別提供者 (例如，Active Directory Federation Services (AD FS)) 驗證使用者，或向已和您公司的 Active Directory 部署同步的 Azure Active Directory 租用戶驗證使用者。當使用者在辦公室內或外出時，均可透過單一登入存取您在 Azure 網站的 Web 內容。現有的服務 (例如 Office 365 或 Windows Intune) 已在使用 Azure Active Directory。透過[簡單驗證]，以相同的 Azure Active Directory 租用戶開啟網站的驗證功能非常容易。 

[授權使用者]使用 Web 內容。使用最少的額外程式碼，即可將相同的內部部署 ASP.NET 程式碼撰寫模式運用在 Azure 網站，例如使用 `[Authorize]` 裝飾。與內部部署的應用程式一樣，您可以保留精密存取控制的彈性。

### 連線到內部部署資源 ###

連線到雲端 (效能考量) 或內部部署 (法規遵循考量) 的網站資料或資源。如需在 Azure 中保存資料的詳細資訊，請參閱 [Azure 信任中心]。 

您可以在 Azure 中的各種資料庫後端 (包括 [Azure SQL Database] 和 [MySQL]) 中選擇，以符合您的網站需求。將資料安全地保存在 Azure 中，可以使資料在地理位置上接近您的網站，進而最佳化其效能。

不過，您的企業可能需將資料保存在公司內部。Azure 網站可讓您輕鬆地設定與內部部署資源 (例如資料庫後端) 的[混合式連線]。若您希望統一管理您的內部部署連線，可以將許多 Azure 網站與具有站台對站台 VPN 的 [Azure 虛擬網路]整合。接著，就像 Azure 網站是內部部署的一樣，您就能存取內部部署資源。[Enterprise Pizza - 使用服務匯流排將網站連線至內部部署][enterprisepizza]

### 最佳化

透過使用自動調整規模自動調整、使用 Azure Redis Cache 執行快取、使用 WebJobs 執行背景工作，以及使用 Azure 流量管理員維持高可用性，以最佳化您的企業營運應用程式。

無論您的工作負載有多大，Azure 網站[向上延展和向外延展]的能力都能符合您企業營運應用程式的需求。透過 [Azure 管理入口網站]以手動方式、透過[服務管理 API] 或 [PowerShell 指令碼處理]以程式設計方式，或是使用「自動調整規模」功能自動地將您的網站向外延展。在**「標準」**主控方案中，「自動調整規模」可讓您依據 CPU 使用量自動向外延展網站。如需最佳做法，請參閱 [Troy Hunt] 的[我所學到關於使用 Azure 快速調整網站的 10 件事]。

使用 [Azure Redis Cache] 加速您網站的回應速度使用它來快取後端資料庫和其他項目的資料，例如 [ASP.NET 工作階段狀態]與[輸出快取]。

使用 [Azure 流量管理員]來維持網站的高可用性。使用**「容錯移轉」**方法時，若主要網站發生問題，流量管理員會自動將流量路由至次要網站。

### 監視及分析

使用 Azure 或協力廠商工具，掌握關於您網站效能的最新資訊。接收重要網站事件的警示。使用 Application Insight 或使用 HDInsight 的 Web 記錄分析，取得使用者深入觀點。 

在 Azure 網站儀表板中取得網站目前之效能度量和資源配額的[快速瀏覽]。如需您的應用程式的可用性、效能和使用方式的 360 度檢視，請使用 [Azure Application Insights] 提供快速和強大的疑難排解、診斷和使用方式的深入觀點。或者，使用像是 [New Relic] 的協力廠商工具，提供您網站的進階監視資料。

在**「標準」**主控方案中，每當您的網站沒有回應時，監視網站回應便會收到電子郵件通知。如需詳細資訊，請參閱 [如何：在 Windows Azure 中接收警示通知及管理警示規則]。

## 其他資源

- [Azure 網站文件](/zh-tw/documentation/services/websites/)
- [Azure 網站的學習地圖](app-service-web/websites-learning-map.md)
- [Azure Web 部落格](/blog/topics/web/)



[Azure 網站]:/zh-tw/services/websites/

[FTP]:/zh-tw/documentation/articles/web-sites-deploy/#ftp
[Visual Studio]:/zh-tw/documentation/articles/web-sites-dotnet-get-started/
[Visual Studio Online]:/zh-tw/documentation/articles/cloud-services-continuous-delivery-use-vso/
[Git]:/zh-tw/documentation/articles/web-sites-publish-source-control/

[HTTPS]:/zh-tw/documentation/articles/web-sites-configure-ssl-certificate/
[網站定價詳細資料]:/zh-tw/pricing/details/web-sites/#service-ssl
[驗證使用者]:/zh-tw/documentation/articles/web-sites-authentication-authorization/
[簡單驗證]:/blog/2014/11/13/azure-websites-authentication-authorization/
[授權使用者]:/zh-tw/documentation/articles/web-sites-authentication-authorization/

[Azure 信任中心]:/zh-tw/support/trust-center/
[MySQL]:/zh-tw/documentation/articles/web-sites-php-mysql-deploy-use-git/
[Azure SQL Database]:/zh-tw/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
[混合式連線]:/zh-tw/documentation/articles/web-sites-hybrid-connection-get-started/
[Azure 虛擬網路]:/zh-tw/documentation/articles/web-sites-integrate-with-vnet/

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




<!--HONumber=42-->
 