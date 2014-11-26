<properties linkid="web-sites-hybrid-connection" title="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" pageTitle="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" description="Create a connection between an Azure website and an on-premises resource that uses a static TCP port" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm" />

# 使用混合式連線將 Azure 網站連線到內部部署資源

您可以在 Microsoft Azure 上將網站連線到使用靜態 TCP 連接埠的任何內部部署資源，例如 SQL Server、MySQL、HTTP Web API、行動服務及最高程度的自訂 Web 服務。本文章顯示如何在 Azure 網站和內部部署 SQL Server 資料庫之間建立混合式連線。

> [WACOM.NOTE]「混合式連線」功能的「網站」部分僅適用於 [Azure 預覽入口網站][Azure 預覽入口網站]。若要在 BizTalk 服務中建立連線，請參閱[混合式連線][混合式連線]。

## 必要條件

-   Azure 訂閱。若要取得免費訂閱，請參閱 [Azure 免費試用][Azure 免費試用]。

-   若要透過混合式連線使用內部部署 SQL Server 或 SQL Server Express 資料庫，必須在靜態連接埠上啟用 TCP/IP。建議您使用 SQL Server 上的預設執行個體，原因是它會使用靜態連接埠 1433。如需安裝與設定 SQL Server Express 以搭配混合式連線使用的相關資訊，請參閱[使用混合式連線從 Azure 網站連線到內部部署 SQL Server][使用混合式連線從 Azure 網站連線到內部部署 SQL Server]。

-   本文稍後將會針對安裝內部部署混合式連線管理員代理程式的電腦加以說明：

    -   必須能夠透過連接埠 5671 連線到 Azure
    -   必須能夠連繫內部部署資源的 *hostname*:*portnumber*。

> [WACOM.NOTE] 本文中的步驟會假設您在主控內部部署混合式連線代理程式的電腦中使用瀏覽器。

## 本文內容

[在 Azure 預覽入口網站中建立網站][在 Azure 預覽入口網站中建立網站]

[建立混合式連線和 BizTalk 服務][建立混合式連線和 BizTalk 服務]

[安裝內部部署混合式連線管理員以完成連線][安裝內部部署混合式連線管理員以完成連線]

[後續步驟][後續步驟]

## 在 Azure 預覽入口網站中建立網站

> [WACOM.NOTE] 如果您已在 Azure 預覽入口網站中建立本教學課程要使用的網站，則您可以直接跳到[建立混合式連線和 BizTalk 服務][建立混合式連線和 BizTalk 服務]，並從那裡開始操作。

1.  在 [Azure 預覽入口網站][Azure 預覽入口網站]的左下角按一下 [新增]，然後選擇 [網站]。

    ![New button][New button]

    ![New website][New website]

2.  在 [網站] 分頁上提供網站的名稱，然後按一下 [建立]。

    ![Website name][Website name]

3.  經過一段時間之後，網站即會建立，並顯示其網站分頁。此分頁是垂直捲動的儀表板，可供您管理網站。

    ![Website running][Website running]

4.  若要確認網站是否已上線啟用，您可以按一下 [瀏覽] 圖示以顯示預設頁面。

    ![Click browse to see your website][Click browse to see your website]

    ![Default website page][Default website page]

接著，您會為網站建立混合式連線和 BizTalk 服務。

<a name="CreateHC"></a>

## 建立混合式連線和 BizTalk 服務

1.  在 [預覽入口網站] 中，向下捲動網站的分頁，然後選擇 \[混合式連線\]。

    ![Hybrid connections][Hybrid connections]

2.  在 \[混合式連線] 分頁上，按一下 [新增]。

    ![Add a hybrid connnection][Add a hybrid connnection]

3.  [新增混合式連線] 分頁隨即開啟。由於這是您的第一個混合式連線，因此會預先選取 [新增混合式連線] 選項，並為您開啟 [建立混合式連線] 分頁。

    ![Create a hybrid connection][Create a hybrid connection]

    在 [建立混合式連線分頁] 上：

    -   在 [名稱] 中，提供連線的名稱。
    -   在 [主機名稱] 中，輸入主控資源的內部部署電腦名稱。
    -   在 [連接埠] 中，輸入內部部署資源使用的連接埠號碼 (若是 SQL Server 預設執行個體，請輸入 1433)。
    -   按一下 [Biz Talk 服務]

4.  [建立 Biz Talk 服務] 分頁隨即開啟。輸入 BizTalk 服務的名稱，然後按一下 [確定]。

    ![Create BizTalk service][Create BizTalk service]

    [建立 BizTalk 服務] 分頁隨即關閉，而您會回到 [建立混合式連線] 分頁。

5.  在 [建立混合式連線] 分頁上，按一下 [確定]。

    ![Click OK][Click OK]

6.  程序完成時，入口網站中的 [通知] 區域會通知您已成功建立連線。

    ![Success notification][Success notification]

7.  在網站分頁上，\[混合式連線\] 圖示現在會顯示已建立 1 個混合式連線。

    ![One hybrid connection created][One hybrid connection created]

至此，您已完成雲端混合式連線基礎結構的重要部分。接下來，您將建立對應的內部部署部分。

<a name="InstallHCM"></a>

## 安裝內部部署混合式連線管理員以完成連線

1.  在網站分頁上，按一下 \[混合式連線] 圖示。

    ![Hybrid connections icon][Hybrid connections icon]

2.  在 \[混合式連線] 分頁上，最近新增之端點的 [狀態] 欄會顯示為 [未連線]。請按一下連線加以設定。

    ![Not connected][Not connected]

    \[混合式連線] 分頁隨即開啟。

    ![NotConnectedBlade][NotConnectedBlade]

3.  在此分頁上，按一下 [Listener Setup]。

    ![Click Listener Setup][Click Listener Setup]

4.  [混合式連線屬性] 分頁隨即開啟。在 [內部部署混合式連線管理員] 下，選擇 [按一下此處進行安裝]。

    ![Click here to install][Click here to install]

5.  在 [應用程式執行] 安全性警告對話方塊中，選擇 [執行] 以繼續作業。

    ![Choose Run to continue][Choose Run to continue]

6.  在 [使用者帳戶控制] 對話方塊中，選擇 [是]。

    ![Choose Yes][Choose Yes]

7.  系統會為您下載並安裝混合式連線管理員。

    ![安裝][安裝]

8.  安裝完成後，請按一下 [關閉]。

    ![Click Close][Click Close]

    在 \[混合式連線\] 分頁上，[狀態] 欄此時會顯示為 [未連線]。

    ![Connected Status][Connected Status]

現在，您已完成混合式連線基礎結構，您可以使用它來建立混合式應用程式。

<a name="NextSteps"></a>

## 後續步驟

-   如需建立使用混合式連線的 ASP.NET Web 應用程式相關資訊，請參閱[使用混合式連線從 Azure 網站連線到內部部署 SQL Server][使用混合式連線從 Azure 網站連線到內部部署 SQL Server]。

-   如需將混合式連線與行動服務搭配使用的相關資訊，請參閱[使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server][使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server]。

### 其他資源

[混合式連線概觀][混合式連線]

[Josh Twist 介紹混合式連線 (第 9 頻道視訊)][Josh Twist 介紹混合式連線 (第 9 頻道視訊)]

[混合式連線網站][混合式連線網站]

[BizTalk 服務：儀表板、監視器、調整、設定和混合式連線索引標籤][BizTalk 服務：儀表板、監視器、調整、設定和混合式連線索引標籤]

[透過絕佳的應用程式可攜性建置真實的混合式雲端 (第 9 頻道視訊)][透過絕佳的應用程式可攜性建置真實的混合式雲端 (第 9 頻道視訊)]

[使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server (第 9 頻道視訊)][使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server (第 9 頻道視訊)]

<!-- IMAGES -->

  [Azure 預覽入口網站]: https://portal.azure.com
  [混合式連線]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Azure 免費試用]: http://azure.microsoft.com/zh-tw/pricing/free-trial/
  [使用混合式連線從 Azure 網站連線到內部部署 SQL Server]: http://go.microsoft.com/fwlink/?LinkID=397979
  [在 Azure 預覽入口網站中建立網站]: #CreateSite
  [建立混合式連線和 BizTalk 服務]: #CreateHC
  [安裝內部部署混合式連線管理員以完成連線]: #InstallHCM
  [後續步驟]: #NextSteps
  [New button]: ./media/web-sites-hybrid-connection-get-started/B01New.png
  [New website]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
  [Website name]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
  [Website running]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
  [Click browse to see your website]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
  [Default website page]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
  [Hybrid connections]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
  [Add a hybrid connnection]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
  [Create a hybrid connection]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
  [Create BizTalk service]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
  [Click OK]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
  [Success notification]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
  [One hybrid connection created]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
  [Hybrid connections icon]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
  [Not connected]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
  [NotConnectedBlade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
  [Click Listener Setup]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
  [Click here to install]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
  [Choose Run to continue]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
  [Choose Yes]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
  [安裝]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
  [Click Close]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
  [Connected Status]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
  [使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server]: http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/
  [Josh Twist 介紹混合式連線 (第 9 頻道視訊)]: http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections
  [混合式連線網站]: http://azure.microsoft.com/zh-tw/services/biztalk-services/
  [BizTalk 服務：儀表板、監視器、調整、設定和混合式連線索引標籤]: http://azure.microsoft.com/zh-tw/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
  [透過絕佳的應用程式可攜性建置真實的混合式雲端 (第 9 頻道視訊)]: http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=
  [使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server (第 9 頻道視訊)]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections
