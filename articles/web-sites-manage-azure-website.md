<properties title="Manage an Azure website" pageTitle="Manage an Azure website" description="Links to resources for managing a Microsoft Azure website." services="web-sites" solutions="web" documentationCenter="" authors="mwasson" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="mwasson" />

# 管理 Azure 網站

本主題含有適合用來管理 Azure 網站之資源的連結。所謂的管理包括所有維持網站順暢運作的工作。

在網站的生命週期內，從初始部署到正常運作、維護及更新，都需要執行不同的管理工作。

-   [將網站部署到生產環境之前][將網站部署到生產環境之前]
-   [當網站運作時][當網站運作時]
-   [更新網站時][更新網站時]

您可以在 Azure 入口網站中執行許多網站管理工作。如需詳細資訊，請參閱[如何管理網站][如何管理網站]。

## 將網站部署到生產環境之前

### 選擇階層。

我們提供的 Azure 網站可分為四個階層：免費、共用、基本和標準。如需各階層之功能和定價的相關資訊，請參閱[網站定價詳細資料][網站定價詳細資料]。

-   [Web 主控方案][Web 主控方案] (英文) 可讓您將多個網站分組在同一個階層下。
-   建立網站後，您可以隨時[切換階層][切換階層]。

### 組態

使用 [Azure 管理入口網站][Azure 管理入口網站]來設定各種組態選項。如需詳細資料，請參閱[如何設定網站][如何設定網站]。以下是簡短的檢查清單：

-   視需要選取 .NET、PHP、Java 或 Python 的**執行階段版本**。
-   如果您的網站使用 WebSocket 通訊協定，請啟用 **WebSockets** (這包括使用 [ASP.NET SignalR][ASP.NET SignalR] (英文) 或 [socket.io][socket.io] 的應用程式)。
-   您是否執行連續性的 Web 工作？如果是的話，請啟用 [Always On]。
-   設定**預設文件** (如 index.html)。

除了以上基本組態設定之外，您也許還想要設定以下各項：

-   **安全通訊端層 (SSL)** 加密。若要使用 SSL 搭配自訂網域名稱，您必須取得 SSL 憑證並設定網站來加以使用。請參閱[對 Azure 網站啟用 HTTPS][對 Azure 網站啟用 HTTPS]。
-   **自訂網域名稱。**您的網站會自動取得 azurewebsites.net 下的子網域。您可以使其與自訂網域名稱 (如 contoso.com) 相關聯。請參閱[設定 Azure 網站的自訂網域名稱][設定 Azure 網站的自訂網域名稱]。

語言特有組態：

-   **PHP**：[如何在 Azure 網站中設定 PHP][如何在 Azure 網站中設定 PHP]。
-   **Python**：[在 Azure 網站上設定 Python][在 Azure 網站上設定 Python]

## 當網站運作時

當網站運作時，您會想要確認網站是否可供使用，以及是否能根據使用者流量而調整。您可能也需要疑難排解錯誤。

### 監視

-   您可以透過管理入口網站來[新增效能度量][新增效能度量] (如 CPU 使用率和用戶端要求數目)。
-   如需深入探索，請使用 New Relic 來監視及管理效能。請參閱 [Azure 網站上的 New Relic 應用程式效能管理][Azure 網站上的 New Relic 應用程式效能管理]。
-   [調整網站][切換階層]以因應流量變化。您可以根據階層來調整 VM 的數目和/或 VM 執行個體的大小。對於標準方案，您還可以設定自動調整，使網站得以按照固定排程或根據負載自動調整。

### 備份

-   設定網站的[自動備份][自動備份]。透過[本視訊][本視訊]深入了解備份。
-   深入了解 Azure SQL Database 中的[資料庫復原][資料庫復原]選項。

### 疑難排解

-   發生問題時，您可以在雲端使用診斷記錄和即時偵錯，以[在 Visual Studio 中疑難排解 Azure 網站][在 Visual Studio 中疑難排解 Azure 網站]。
-   在 Visual Studio 之外，還有各種方法可用來收集診斷記錄。請參閱[對 Azure 網站啟用診斷記錄][對 Azure 網站啟用診斷記錄]。
-   若是 Node.js 應用程式，請參閱[如何在 Azure 網站中偵錯 Node.js 應用程式][如何在 Azure 網站中偵錯 Node.js 應用程式]。

### 還原資料

-   [還原][還原]先前已備份的網站。

## 更新網站時

如果您尚未啟用自動備份，可以建立[手動備份][自動備份]。

請考慮使用[預備部署][預備部署]。此選項可讓您將更新發佈到與生產部署並行運作的預備部署。

如果您使用 Visual Studio Online，可以從原始檔控制設定連續部署：

-   [使用 Team Foundation 版本控制 (TFVC)][使用 Team Foundation 版本控制 (TFVC)]
-   [使用 Git (英文)][使用 Git (英文)]



  [將網站部署到生產環境之前]: #before-you-deploy-your-site-to-production
  [當網站運作時]: #while-your-website-is-running
  [更新網站時]: #when-you-update-your-website
  [如何管理網站]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-manage/
  [網站定價詳細資料]: http://azure.microsoft.com/zh-tw/pricing/details/websites/
  [Web 主控方案]: http://azure.microsoft.com/zh-tw/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview
  [切換階層]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-scale/
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [如何設定網站]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-configure/
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [socket.io]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-nodejs-chat-app-socketio/
  [對 Azure 網站啟用 HTTPS]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-configure-ssl-certificate/
  [設定 Azure 網站的自訂網域名稱]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-custom-domain-name/
  [如何在 Azure 網站中設定 PHP]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-php-configure/
  [在 Azure 網站上設定 Python]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-python-configure/
  [新增效能度量]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-monitor
  [Azure 網站上的 New Relic 應用程式效能管理]: http://azure.microsoft.com/zh-tw/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/
  [自動備份]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-backup/
  [本視訊]: http://azure.microsoft.com/zh-tw/documentation/videos/azure-websites-automatic-and-easy-backup/
  [資料庫復原]: http://msdn.microsoft.com/zh-tw/library/azure/hh852669.aspx
  [在 Visual Studio 中疑難排解 Azure 網站]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug
  [對 Azure 網站啟用診斷記錄]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-enable-diagnostic-log/
  [如何在 Azure 網站中偵錯 Node.js 應用程式]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-nodejs-debug/
  [還原]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-restore/
  [預備部署]: http://azure.microsoft.com/zh-tw/documentation/articles/web-sites-staged-publishing/
  [使用 Team Foundation 版本控制 (TFVC)]: http://azure.microsoft.com/zh-tw/documentation/articles/cloud-services-continuous-delivery-use-vso/
  [使用 Git (英文)]: http://azure.microsoft.com/zh-tw/documentation/articles/cloud-services-continuous-delivery-use-vso-git/
