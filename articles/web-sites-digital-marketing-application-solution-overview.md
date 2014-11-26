<properties linkid="websites-digital-marketing" urlDisplayName="Resources" pageTitle="Create a Digital Marketing Campaign on Azure Websites" metaKeywords="" description="This guide provides a technical overview of how to use Azure Websites to create digital marketing campaigns. This includes deployment, social media integration, scaling strategies, and monitoring." metaCanonical="" services="" documentationCenter="" title="Create a Digital Marketing Campaign on Azure Websites" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />

# 在 Azure 網站建立數位行銷活動

本指南提供如何使用 Azure 網站來建立數位行銷活動的技術概觀。數位行銷活動通常是用來促成短期行銷目標的短期實體。有兩個需要考量的主要案例。在第一個案例中，協力廠商行銷公司在促銷期間為其客戶建立及管理行銷活動。第二個案例則涉及行銷公司建立數位行銷活動資源擁有權，然後將該擁有權轉移給其客戶。客戶接著便可自行執行及管理數位行銷活動。

[Azure 網站][Azure 網站]對於這兩個案例皆適用。它提供快速建立、支援多個架構和語言、可依使用者需求進行調整的功能，並且還可容納許多部署和原始檔控制系統。藉由使用 Azure，您還能夠存取其他可增強行銷活動的 Azure 服務 (如媒體服務)。

雖然可以使用 [Azure 雲端服務][Azure 雲端服務] (英文) 或 [Azure 虛擬機器][Azure 虛擬機器] (英文) 來裝載網站，但除非 Azure 網站並未提供某項必要功能，否則這對於此案例而言並非最佳選擇。若要了解相關選項，請參閱 [Azure 網站、雲端服務和 VM：使用時機][Azure 網站、雲端服務和 VM：使用時機]。

本指南涵蓋下列領域：

-   [部署現有的網站][部署現有的網站]
-   [與社交媒體整合][與社交媒體整合]
-   [隨使用者需求進行調整][隨使用者需求進行調整]
-   [與其他服務整合][與其他服務整合]
-   [監視行銷活動][監視行銷活動]

<div class="dev-callout">
<strong>注意</strong>
<p>本指南呈現一些與公用端 .COM 網站開發整合的最常見領域和工作。不過，還有其他 Azure 網站功能可供您在特定的實作中使用。若要檢閱這些功能，請一併參閱<a href="http://www.windowsazure.com/zh-TW/manage/services/web-sites/global-web-presence-solution-overview/">全域網站空間</a> (英文) 和 <a href="http://www.windowsazure.com/zh-TW/manage/services/web-sites/business-application-solution-overview">商務應用程式</a> (英文) 上的其他指南。</p>
</div>

## <a name="deployexisting"></a>部署現有網站

在全域網站空間案例中，我們討論了各種建立及部署新網站的選項。如果您是 Azure 網站新手，建議您[檢閱該資訊][檢閱該資訊] (英文)。如果您經常建立數位行銷活動，您可能已經有針對不同的促銷自訂的 Web 資產。在本節中，我們將著重於從原始檔控制來部署各類型網站的選項。

如果您為了因應多個目的而重複使用 Web 資產，您應該強烈考慮使用原始檔控制管理系統 (如果尚未使用的話)。這可讓您儲存可針對特定客戶進行分支和自訂的常用 Web 方案範本。網站提供與許多不同原始碼儲存機制進行同步處理的選項。請在 [儀表板] 索引標籤上，選取 [設定從原始檔控制進行部署] 連結。

![DigitalMarketingDeploy1][DigitalMarketingDeploy1]

這會顯示一個含有多個原始程式碼控制選項的對話方塊。這些除了包含簡單的部署方案 (例如 Dropbox) 之外，也包含功能完整的原始檔控制系統 (例如 TFS)。

![DigitalMarketingDeploy2][DigitalMarketingDeploy2]

您可以使用各種原始檔控制技術來管理以現有基準為基礎的新專案。例如，您可以複製一個先前已儲存的基準儲存機制以開始進行新專案，或是建立一個新分支以追蹤目前專案的自訂進度。如需使用分支以管理來自相同原始檔控制儲存機制之各種不同部署的絕佳範例，請參閱[多個環境搭配 Azure 網站][多個環境搭配 Azure 網站] (英文)。這篇文章示範如何使用 Git 分支來管理預備環境和生產環境。

在您將網站連線到原始檔控制之後，便可以從入口網站設定及追蹤部署。如需有關將原始檔控制與網站搭配使用的詳細資訊，請參閱[從原始檔控制發佈至 Azure 網站][從原始檔控制發佈至 Azure 網站]。

使用現有的 Web 資產時，具有能夠裝載許多不同類型網站的靈活性也相當重要。在 [設定] 索引標籤上，您可以為您的網站同時選取 .NET 和 PHP 支援。

![DigitalMarketingFrameworkVersions][DigitalMarketingFrameworkVersions]

除了這些組態選項之外，網站還自動提供 Python 2.7 和 Node.js 支援。預設的 Node.js 版本為 0.10.5。

Azure 網站的其中一個額外優點就是將預備網站部署至 Web 的速度。在網站的計劃、原型設計及初期開發期間，代理商及其客戶可以在行銷活動網站上線之前，先查看網站的實際運作版本。在網站準備好投入生產環境之後，代理商可以為客戶管理生產部署，或是將 Web 資產提供給客戶去部署及管理。

## <a name="socialmedia"></a>與社交媒體整合

大部分數位行銷活動皆運用了社交媒體網站，例如 Facebook 或 Twitter。其中一項整合重點就是使用社交媒體身分識別來進行驗證。如需在 ASP.NET 應用程式使用這個方法的範例，請參閱[將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 應用程式部署至 Azure 網站][將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 應用程式部署至 Azure 網站] (英文)。

不過，許多數位行銷活動都不僅僅只是使用驗證功能，而是利用社交媒體整合做為其策略的一個主要部分。社交媒體網站通常有一個開發人員區段，當中說明將應用程式與其服務整合的各種不同方式。就提供 REST API 的服務而言，幾乎從任何 Web 架構都可使用它。不過，通常會有針對您所選擇語言的特定資訊。例如，Twitter 提供了[支援 Twitter API 的可用程式庫清單][支援 Twitter API 的可用程式庫清單] (英文)，包括適用於 .NET、Node.js 及 PHP 的程式庫。這是其中一例，您應該直接從您選擇做為目標的每個社交媒體網站尋找類似的開發人員指引。

針對以 Facebook 為目標的 ASP.NET 開發人員，Visual Studio 有為 MVC 4 Facebook 應用程式提供了範本。

![DigitalMarketingFacebook][DigitalMarketingFacebook]

如需將此範本與網站搭配使用的快速逐步解說，請參閱[使用 ASP.NET MVC Facebook 範本建立 Facebook 應用程式並將它們免費裝載於 Azure 網站][使用 ASP.NET MVC Facebook 範本建立 Facebook 應用程式並將它們免費裝載於 Azure 網站] (英文)。如需更詳細的教學課程和範例應用程式，請參閱 [ASP.NET MVC Facebook 生日應用程式][ASP.NET MVC Facebook 生日應用程式] (英文) 和[適用於 ASP.NET MVC 的新 Facebook 應用程式範本和程式庫][適用於 ASP.NET MVC 的新 Facebook 應用程式範本和程式庫] (英文)。

如果您是 ASP.NET 開發人員，了解您與社交媒體的整合並不受 Visual Studio 所提供的範本限制相當重要。範本只是協助讓程序更加簡單。但是，就如先前所述，每個社交媒體網站通常都有提供有關從 .NET 及許多其他語言和架構進行連線的其他方法資訊。

## <a name="scale"></a>隨使用者需求進行調整

雲端運算對於無法預測的工作負載相當實用。數位行銷活動也在這個類別範圍內。要預測相對上短期的行銷網站相當困難，因為許多因素取決於擷取使用者興趣及促成較多網站流量的相關社交媒體互動。Azure 提供數個可調整網站和雲端服務的選項。

-   透過 [Azure 管理入口網站][Azure 管理入口網站]以手動方式調整。
-   透過[服務管理 API][服務管理 API] 或 [PowerShell 指令碼][PowerShell 指令碼] (英文) 以程式設計方式調整。
-   透過自動調整 (預覽) 功能以自動方式調整。

在管理入口網站中，移至您網站的 [調整] 索引標籤。有數個調整選項。第一個選項會決定網站模式，可設為 [免費]、[共用] 或 [標準]。

![DigitalMarketingScale][DigitalMarketingScale]

延展性功能和選項會逐層增加。例如，[免費] 模式網站無法橫向擴充為多個執行個體，但是 [共用] 網站可以橫向擴充為 6 個執行個體。您還可以選擇透過選取 [標準] 模式來進行垂直擴充。這個模式會在專用的虛擬機器上執行您的網站，並提供小型、中型及大型機器大小選項。決定虛擬機器的大小之後，您還可以選擇將橫向擴充為多個執行個體。在 [標準] 模式下，您可以將橫向擴充為 10 個執行個體。如需每個模式之間差異的完整清單，請參閱[網站定價詳細資料][網站定價詳細資料]。

選取 [標準] 模式時，您將可以額外選擇啟用自動調整 (預覽) 功能。這可讓您根據 CPU 進行調整。[目標 CPU] 百分比是 Azure 為您的網站執行個體設定的處理器使用率範圍。如果平均 CPU 低於這個目標，Azure 就會降低執行個體計數，因為將相同的負載散佈在較少的執行個體上將可讓剩餘執行個體的使用率增加。不過，它無法將執行個體計數降低到低於 [執行個體計數] 下限值。同樣地，如果平均 CPU 高於 [目標 CPU]，Azure 就會增加執行個體數目。將相同的負載散佈至額外的電腦可達到讓每部電腦 CPU 使用率降低的效果。新增的執行個體數目會受到 [執行個體計數] 上限值的限制。

![DigitalMarketingAutoScale][DigitalMarketingAutoScale]

自動調整可讓資源的使用更有效率。例如，數位行銷活動可能在夜間和週末最為活躍。這也可以有效地處理行銷活動熱門程度意外升高的案例。自動調整可協助有效地處理增加的負載，然後在負載降低時減少執行個體 (及成本)。

如需有關調整網站的詳細資訊，請參閱[如何調整網站][如何調整網站]。這個主題也與監視緊密相關。如需詳細資訊，請參閱本指南中[監視行銷活動][監視行銷活動]一節。

<div class="dev-callout">
<strong>注意</strong>
<p>對於選擇使用雲端服務和 Web 角色的 Web 應用程式，有一個額外的選項，就是根據佇列中的項目長度進行調整。在雲端服務中，處理後端佇列的角色是常見的架構模式。如需有關雲端調整的詳細資訊，請參閱<a href="http://www.windowsazure.com/zh-TW/manage/services/cloud-services/how-to-scale-a-cloud-service/">如何調整雲端服務</a> (英文)。</p>
</div>

## <a name="integrate"></a>與其他服務整合

數位行銷網站經常會納入多媒體，例如視訊串流。在 Azure 中裝載這些網站可提供與相關 Azure 服務的緊密整合。例如，您可以使用 Azure 媒體服務為您的網站進行視訊編碼和串流。如需有關媒體服務的詳細資訊，請參閱 [Azure 媒體服務概念與案例][Azure 媒體服務概念與案例] (英文)。

其他 Azure 服務可用來建立較強固的應用程式。例如，網站可以使用新 [Azure 快取服務 (預覽)][Azure 快取服務 (預覽)] 所提供的分散式快取功能。或是您可以使用 Azure 儲存體服務來儲存應用程式資料和資源。例如，圖形、視訊及其他大型檔案可以持久地儲存在 Blob 中。另外，也提供了資料庫服務 (例如 Azure SQL Database 和 MySQL) 來因應關聯式資料需求。

## <a name="monitor"></a>監視行銷活動

[監視] 索引標籤包含可協助您評估數位行銷網站的成就和績效的計量。

![DigitalMarketingMonitor][DigitalMarketingMonitor]

例如，您可以查看 [CPU 時間]、[要求] 及 [資料輸出] 等計量來查看使用模式和使用程度。所有這些計量都會隨著行銷活動的熱門程度提升而增加。使用狀況的相關資訊可用來決定橫向擴充或垂直擴充的時機。如需詳細資訊，請參閱[如何監視網站][如何監視網站] (英文)。

針對 [免費] 和 [共用] 模式，您還應該注意資源配額。在 [儀表板] 索引標籤上，入口網站會顯示數個配額的目前使用量統計資料以及何時將重設這些配額。這些使用量統計資料會因您顯取的模式而有所不同。下列螢幕擷取畫面顯示針對 [免費] 模式顯示的統計資料。在 [共用] 模式中，並沒有用於 [資料輸出] 的配額。在 [標準] 模式中，只會顯示 [檔案系統儲存體] 和 [大小]。

![DigitalMarketingUsageOverview][DigitalMarketingUsageOverview]

當您發現快耗盡這些配額時，請考慮從 [免費] 移至 [共用] 模式，或是從 [共用] 移至 [標準] 模式。在 [標準] 模式中，會將專用的資源放在一或多個小型、中型或大型的虛擬機器上。

除了使用管理入口網站來提供這項資訊以外，也有一些協力廠商工具可為您的網站提供進階監視資料。例如，您可以從 Azure 市集安裝 New Relic 附加元件。如需有關使用 New Relic 進行監視的逐步解說，請參閱[在 Azure 上進行 New Relic 應用程式效能管理][在 Azure 上進行 New Relic 應用程式效能管理] (英文)。

最後，[標準] 模式網站可以啟用端點監視和警示功能。端點監視會定期嘗試連線至您的網站，然後針對回應時間做回報。警示會在回應時間超出指定的闕值時提供電子郵件通知。如需詳細資訊，請參閱[全域網站空間][檢閱該資訊] (英文) 案例的監視小節，以及主題[作法：在 Windows Azure 中接收警示通知及管理警示規則][作法：在 Windows Azure 中接收警示通知及管理警示規則]。

## <a name="summary"></a>摘要

Azure 網站對於針對個別行銷活動自訂的可重複使用 Web 內容而言是理想選擇。網站支援許多常用的語言、架構及原始檔控制系統，可更輕鬆地將這些資產和工作流程移轉至雲端。ASP.NET Facebook 應用程式範本可讓您較容易建立 Facebook 應用程式，但是您可以使用幾乎所有支援 Web 介面的協力廠商社交媒體整合。Azure 媒體服務及 Azure 上其他相關服務皆提供一些額外的工具，可建構設計完善的行銷活動網站。而在對於處理可能難以預測的使用者需求方面，多個手動和自動調整選項也相當實用。如需詳細資訊，請參閱下列技術文章。

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="top">領域</th>
   <th align="left" valign="top">資源</th>
</tr>
<tr>
   <td valign="middle"><strong>規劃</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/zh-TW/manage/services/web-sites/choose-web-app-service">Azure 網站、雲端服務和 VM：使用時機</a></td>
</tr>
<tr>
   <td valign="middle"><strong>建立</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/zh-TW/manage/services/web-sites/how-to-create-websites/">如何建立及部署網站</a></td>
</tr>
<tr>
   <td valign="middle"><strong>部署</strong></td>
   <td valign="top">- <a href="http://azure.microsoft.com/zh-TW/documentation/articles/web-sites-deploy/">如何部署 Azure 網站</a><br/>- <a href="http://www.windowsazure.com/zh-TW/develop/net/common-tasks/publishing-with-git/">從原始檔控制發行至 Azure 網站</a></td>
</tr>
<tr>
   <td valign="middle"><strong>社交媒體</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/zh-TW/develop/net/tutorials/web-site-with-sql-database/">部署使用成員資格、OAuth 和 SQL 資料庫的安全 ASP.NET MVC 應用程式</a><br/>- <a href="http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx">使用 ASP.NET MVC Facebook 範本建立 Facebook 應用程式</a><br/>- <a href="http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx">適用於 ASP.NET MVC 的 Facebook 應用程式範本和程式庫</a></td>
</tr>
<tr>
   <td valign="middle"><strong>調整</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/zh-TW/manage/services/web-sites/how-to-scale-websites/">如何調整網站</a></td>
</tr>
<tr>
   <td valign="middle"><strong>多媒體</strong></td>
   <td valign="top">- <a href="http://msdn.microsoft.com/zh-TW/library/windowsazure/dn223282.aspx">Azure 媒體服務概念與案例</a></td>
</tr>
<tr>
   <td valign="middle"><strong>監視</strong></td>
   <td valign="top">- <a href="http://www.windowsazure.com/zh-TW/manage/services/web-sites/how-to-monitor-websites/">如何監視網站</a><br/>- <a href="http://msdn.microsoft.com/library/windowsazure/dn306638.aspx">作法：在 Azure 中接收警示通知及管理警示規則</a></td>
</tr>
</table>


  [Azure 網站]: /zh-TW/documentation/services/web-sites/
  [Azure 雲端服務]: /zh-TW/documentation/services/cloud-services/
  [Azure 虛擬機器]: /zh-TW/documentation/services/virtual-machines/
  [Azure 網站、雲端服務和 VM：使用時機]: /zh-TW/manage/services/web-sites/choose-web-app-service
  [部署現有的網站]: #deployexisting
  [與社交媒體整合]: #socialmedia
  [隨使用者需求進行調整]: #scale
  [與其他服務整合]: #integrate
  [監視行銷活動]: #monitor
  [全域網站空間]: http://www.windowsazure.com/zh-TW/manage/services/web-sites/global-web-presence-solution-overview/
  [檢閱該資訊]: /zh-TW/manage/services/web-sites/global-web-presence-solution-overview/
  [DigitalMarketingDeploy1]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy1.png
  [DigitalMarketingDeploy2]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Deploy2.png
  [多個環境搭配 Azure 網站]: http://www.bradygaster.com/post/multiple-environments-with-windows-azure-web-sites
  [從原始檔控制發佈至 Azure 網站]: /zh-TW/develop/net/common-tasks/publishing-with-git/
  [DigitalMarketingFrameworkVersions]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_FrameworkVersions.png
  [將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 應用程式部署至 Azure 網站]: /zh-TW/develop/net/tutorials/web-site-with-sql-database/
  [支援 Twitter API 的可用程式庫清單]: https://dev.twitter.com/docs/twitter-libraries#dotnet
  [DigitalMarketingFacebook]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Facebook.png
  [使用 ASP.NET MVC Facebook 範本建立 Facebook 應用程式並將它們免費裝載於 Azure 網站]: http://blogs.msdn.com/b/africaapps/archive/2013/02/20/creating-a-facebook-app-using-asp-net-mvc-facebook-templates-and-hosting-them-for-free-on-windows-azure-websites.aspx
  [ASP.NET MVC Facebook 生日應用程式]: http://www.asp.net/mvc/tutorials/mvc-4/aspnet-mvc-facebook-birthday-app
  [適用於 ASP.NET MVC 的新 Facebook 應用程式範本和程式庫]: http://blogs.msdn.com/b/webdev/archive/2012/12/13/the-new-facebook-application-template-and-library-for-asp.net-mvc.aspx
  [Azure 管理入口網站]: http://manage.windowsazure.com/
  [服務管理 API]: http://msdn.microsoft.com/zh-TW/library/windowsazure/ee460799.aspx
  [PowerShell 指令碼]: http://msdn.microsoft.com/zh-TW/library/windowsazure/jj152841.aspx
  [DigitalMarketingScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Scale.png
  [網站定價詳細資料]: https://www.windowsazure.com/zh-TW/pricing/details/web-sites/
  [DigitalMarketingAutoScale]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_AutoScale.png
  [如何調整網站]: /zh-TW/manage/services/web-sites/how-to-scale-websites/
  [Azure 媒體服務概念與案例]: http://msdn.microsoft.com/zh-TW/library/windowsazure/dn223282.aspx
  [Azure 快取服務 (預覽)]: http://msdn.microsoft.com/zh-TW/library/windowsazure/dn386094.aspx
  [DigitalMarketingMonitor]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_Monitor.png
  [如何監視網站]: /zh-TW/manage/services/web-sites/how-to-monitor-websites/
  [DigitalMarketingUsageOverview]: ./media/web-sites-digital-marketing-application-solution-overview/DigitalMarketing_UsageOverview.png
  [在 Azure 上進行 New Relic 應用程式效能管理]: /zh-TW/develop/net/how-to-guides/new-relic/
  [作法：在 Windows Azure 中接收警示通知及管理警示規則]: http://msdn.microsoft.com/library/windowsazure/dn306638.aspx
