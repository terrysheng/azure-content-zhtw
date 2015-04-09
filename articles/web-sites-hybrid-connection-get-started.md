<properties 
	pageTitle="在 Azure App Service 中使用混合式連線存取內部部署資源" 
	description="在 Azure App Service 中的 Web 應用程式和使用靜態 TCP 連接埠的內部部署資源之間建立連線。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

#在 Azure App Service 中使用混合式連線存取內部部署資源

您可以在 Azure App Service 上將 Web 應用程式連接到使用靜態 TCP 連接埠的任何內部部署資源，例如 SQL Server、MySQL、HTTP Web API、行動服務及最高程度的自訂 Web 服務。本文示範如何在 App Service 中的 Web 應用程式和內部部署的 SQL Server 資料庫之間建立混合式連線。

> [AZURE.NOTE] 混合式連線功能的 Web Apps 部分僅適用於 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)。若要在 BizTalk 服務中建立連線，請參閱[混合式連線](http://go.microsoft.com/fwlink/p/?LinkID=397274)。  

## 必要條件
- Azure 訂閱。若要取得免費訂用帳戶，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。 

- 若要透過混合式連線使用內部部署 SQL Server 或 SQL Server Express 資料庫，必須在靜態連接埠上啟用 TCP/IP。建議在 SQL Server 上使用預設執行個體，因為其使用靜態連接埠 1433。如需安裝及設定 SQL Server Express 以搭配混合式連線使用的相關資訊，請參閱[使用混合式連線從 Azure 網站連線到內部部署 SQL Server](http://go.microsoft.com/fwlink/?LinkID=397979)

- 本文稍後將會針對安裝內部部署混合式連線管理員代理程式的電腦加以說明：

	- 必須能夠透過連接埠 5671 連線到 Azure
	- 必須能夠連繫內部部署資源的 *hostname*:*portnumber*。 

> [AZURE.NOTE] 本文中的步驟假設您使用將主控內部部署混合式連線代理程式之電腦中的瀏覽器。


## 在 Azure 入口網站中建立 Web 應用程式 ##

> [AZURE.NOTE] 如果您已經在 Azure 入口網站中建立本教學課程要使用的 Web 應用程式，就能直接跳到[建立混合式連線和 BizTalk 服務](#CreateHC) ，並從那裡開始操作。

1. 在 [Azure 入口網站](https://portal.azure.com)左下角，依序按一下 [新增] > [Web + 行動] > [網站]。
	
	![New button][New]
	
	![New web app][NewWebsite]
	
2. 在 [Web 應用程式] 刀鋒視窗中提供 URL，然後按一下 [建立]。 
	
	![Website name][WebsiteCreationBlade]
	
3. Web 應用程式會在一段時間之後建立，並顯示它的 Web 應用程式刀鋒視窗。此分頁是垂直捲動的儀表板，可供您管理網站。
	
	![Website running][WebSiteRunningBlade]
	
4. 若要確認網站是否已上線啟用，您可以按一下 [瀏覽] 圖示來顯示預設頁面。
	
	![Click browse to see your web app][Browse]
	
	![Default web app page][DefaultWebSitePage]
	
接著，您將為 Web 應用程式建立混合式連線和 BizTalk 服務。

<a name="CreateHC"></a>
## 建立混合式連線和 BizTalk 服務 ##

1. 向下捲動 Web 應用程式的刀鋒視窗，然後選擇 [混合式連線]。
	
	![Hybrid connections][CreateHCHCIcon]
	
2. 在 [混合式連線] 分頁上，按一下 [新增]。
	
	<!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
	
3. [新增混合式連線] 刀鋒視窗隨即開啟。由於這是您的第一個混合式連線，因此系統會預先選取 [新增混合式連線] 選項，並為您開啟 [建立混合式連線] 刀鋒視窗。
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	在 [建立混合式連線] 刀鋒視窗上：
	- 針對 [名稱] 提供連線的名稱。
	- 在 [主機名稱] 中，輸入裝載資源的內部部署電腦名稱。
	- 在 [連接埠] 中，輸入內部部署資源使用的連接埠號碼 (若是 SQL Server 預設執行個體，請輸入 1433)。
	- 按一下 [Biz Talk 服務]


4. [建立 BizTalk 服務] 刀鋒視窗隨即開啟。輸入 BizTalk 服務的名稱，然後按一下 [確定]。
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	[建立 BizTalk 服務] 刀鋒視窗隨即關閉，而您會回到 [建立混合式連線] 刀鋒視窗。
	
5. 在 [建立混合式連線] 分頁上，按一下 [確定]。 
	
	![Click OK][CreateBTScomplete]
	
6. 當程序完成時，入口網站中的通知區域會通知您已成功建立連線。
	<!-- 待辦事項

    在這個步驟中，所有項目都會失敗。我無法在狗食入口網站中建立 BizTalk 服務。我切換到舊的入口網站
	(完整的入口網站) 並建立 BizTalk 服務，但似乎無法讓您連接它們 - 當您完成
	「建立混合式連線」步驟時，您看見下列錯誤：
	無法建立混合式連線 RelecIoudHC。這個 
	資源類型無法在 
	「適用於 API 版本 2014-06-01 的 Microsoft.BizTaIkServices」命名空間中找到。
	
	此錯誤表示找不到該類型，而不是該執行個體。
	![Success notification][CreateHCSuccessNotification]
	-->
7. 在 Web 應用程式的刀鋒視窗上，[混合式連線] 圖示現在顯示已建立 1 個混合式連線。
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
至此，您已完成雲端混合式連線基礎結構的重要部分。接下來，您將建立對應的內部部署部分。

<a name="InstallHCM"></a>
## 安裝內部部署混合式連線管理員以完成連線 ##

1. 在 Web 應用程式的刀鋒視窗上，按一下 [混合式連線] 圖示。 
	
	![Hybrid connections icon][HCIcon]
	
2. 在 [混合式連線] 分頁上，最近新增之端點的 [狀態] 欄會顯示為 [未連線]。請按一下連線加以設定。
	
	![Not connected][NotConnected]
	
	[混合式連線] 分頁隨即開啟。
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. 在刀鋒視窗上，按一下 [接聽程式設定]。
	
	![Click Listener Setup][ClickListenerSetup]
	
4. [混合式連線屬性] 刀鋒視窗隨即開啟。在 [內部部署混合式連線管理員] 下，選擇 [按一下此處進行安裝]。
	
	![Click here to install][ClickToInstallHCM]
	
5. 在 [應用程式執行] 安全性警告對話方塊中，選擇 [執行] 以繼續作業。
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	在 [使用者帳戶控制] 對話方塊中，選擇 [是]。
	
	![Choose Yes][UAC]
	
7. 系統會為您下載並安裝混合式連線管理員。 
	
	![Installing][HCMInstalling]
	
8. 安裝完成後，按一下 [關閉]。
	
	![Click Close][HCMInstallComplete]
	
	在 [混合式連線] 刀鋒視窗上，[狀態] 欄此時會顯示為 [未連線]。 
	
	![Connected Status][HCStatusConnected]

現在，您已完成混合式連線基礎結構，您可以使用它來建立混合式應用程式。 

>[AZURE.NOTE] 如果您想要在註冊 Azure 帳戶之前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，讓您能夠在 App Service 中立即建立短期的入門 Web 應用程式。不需要信用卡，無需承諾。

<a name="NextSteps"></a>
## 後續步驟 ##

- 如需建立使用混合式連線的 ASP.NET Web 應用程式相關資訊，請參閱[使用混合式連線從 Azure 網站連線到內部部署 SQL Server](http://go.microsoft.com/fwlink/?LinkID=397979)。

- 如需將混合式連線與行動服務搭配使用的相關資訊，請參閱[使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server](mobile-services-dotnet-backend-hybrid-connections-get-started.md)。

### 其他資源

[混合式連線概觀](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist 介紹混合式連線 (第 9 頻道視訊)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[混合式連線網站](http://azure.microsoft.com/services/biztalk-services/)

[BizTalk 服務：儀表板、監視器、調整規模、設定和混合式連線索引標籤](../biztalk-dashboard-monitor-scale-tabs/)

[透過絕佳的應用程式可攜性建置真實的混合式雲端 (第 9 頻道視訊)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server (第 9 頻道視訊)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## 相關變更
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊版入口網站變更為新版入口網站的指南，請參閱：[關於 Azure App Service 中網站和 Web 應用程式的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png

<!--HONumber=49-->