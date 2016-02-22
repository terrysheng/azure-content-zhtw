<properties 
	pageTitle="在 Azure App Service 中使用混合式連線存取內部部署資源" 
	description="在 Azure App Service 中的 Web 應用程式和使用靜態 TCP 連接埠的內部部署資源之間建立連線" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="cephalin"/>

#在 Azure App Service 中使用混合式連線存取內部部署資源

您可以將 Azure App Service 應用程式連接到任何使用靜態 TCP 連接埠 (例如 SQL Server、MySQL、HTTP Web API 和大部分自訂 Web 服務) 的內部部署資源。本文章示範如何在 App Service 和內部部署的 SQL Server 資料庫之間建立混合式連線。

> [AZURE.NOTE] 「混合式連線」功能的 Web Apps 部分僅適用於 [Azure 入口網站](https://portal.azure.com)。若要在 BizTalk 服務中建立連線，請參閱[混合式連線](http://go.microsoft.com/fwlink/p/?LinkID=397274)。
> 
> 此內容也適用於 Azure App Service 中的 Mobile Apps。

## 必要條件
- Azure 訂閱。若要取得免費訂閱，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。 
 
	如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

- 若要透過混合式連線使用內部部署 SQL Server 或 SQL Server Express 資料庫，必須在靜態連接埠上啟用 TCP/IP。建議在 SQL Server 上使用預設執行個體，因為其使用靜態連接埠 1433。如需安裝及設定 SQL Server Express 以搭配混合式連線使用的相關資訊，請參閱[使用混合式連線從 Azure 網站連線到內部部署 SQL Server](http://go.microsoft.com/fwlink/?LinkID=397979)。

- 本文稍後將會針對安裝內部部署混合式連線管理員代理程式的電腦加以說明：

	- 必須能夠透過連接埠 5671 連線到 Azure
	- 必須能夠連繫內部部署資源的 *hostname*:*portnumber*。 

> [AZURE.NOTE] 本文中的步驟假設您使用將主控內部部署混合式連線代理程式之電腦中的瀏覽器。


## 在 Azure 入口網站中建立 Web 應用程式 ##

> [AZURE.NOTE] 如果您已在 Azure 入口網站中建立本教學課程要使用的 Web 應用程式或行動應用程式後端，則您可以直接跳到[建立混合式連線和 BizTalk 服務](#CreateHC)，並從那裡開始操作。

1. 在 [Azure 入口網站](https://portal.azure.com)左上角，依序按一下 [新增] > [Web + 行動] > [Web 應用程式]。
	
	![新 Web 應用程式][NewWebsite]
	
2. 在 [Web 應用程式] 刀鋒視窗上，提供 URL，然後按一下 [建立]。
	
	![Website name][WebsiteCreationBlade]
	
3. 經過一段時間之後，Web 應用程式會建立，並顯示它的 Web 應用程式分頁。此分頁是垂直捲動的儀表板，可供您管理網站。
	
	![Website running][WebSiteRunningBlade]
	
4. 若要確認網站是否已上線啟用，您可以按一下 [瀏覽] 圖示以顯示預設頁面。
	
	![按一下 [瀏覽] 以查看您的 Web 應用程式][Browse]
	
	![預設 Web 應用程式頁面][DefaultWebSitePage]
	
接著，您將為 Web 應用程式建立混合式連線和 BizTalk 服務。

<a name="CreateHC"></a>
## 建立混合式連線和 BizTalk 服務 ##

1. 在您的 [Web 應用程式] 刀鋒視窗中按一下 [所有設定] > [網路] > [設定混合式連接端點]。
	
	![Hybrid connections][CreateHCHCIcon]
	
2. 在 [混合式連線] 分頁上，按一下 [新增]。
	
	<!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
	
3. [新增混合式連線] 分頁隨即開啟。由於這是您的第一個混合式連線，因此會預先選取 [新增混合式連線] 選項，並為您開啟 [建立混合式連線] 分頁。
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	在 [建立混合式連線分頁] 上：- 在 [名稱] 中，提供連線的名稱。- 在 [主機名稱] 中，輸入主控資源的內部部署電腦名稱。- 在 [連接埠] 中，輸入內部部署資源使用的連接埠號碼 (若是 SQL Server 預設執行個體，請輸入 1433)。- 按一下 [Biz Talk 服務]


4. [建立 BizTalk 服務] 分頁隨即開啟。輸入 BizTalk 服務的名稱，然後按一下 [確定]。
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	[建立 BizTalk 服務] 分頁隨即關閉，而您會回到 [建立混合式連線] 分頁。
	
5. 在 [建立混合式連線] 分頁上，按一下 [確定]。
	
	![Click OK][CreateBTScomplete]
	
6. 當程序完成時，入口網站中的通知區域會通知您已成功建立連線。
	<!--- TODO

Everything fails at this step. I can't create a BizTalk service in the dogfood portal. I switch to the classic portal
(full portal) and created the BizTalk service but it doesn't seem to let you connnect them - When you finish the
Create hybrid conn step, you get the following error
Failed to create hybrid connection RelecIoudHC. The 
resource type could not be found in the namespace 
'Microsoft.BizTaIkServices for api version 2014-06-01'.

The error indicates it couldn't find the type, not the instance.
![Success notification][CreateHCSuccessNotification]
-->
7. 在 Web 應用程式的刀鋒視窗上，[混合式連線] 圖示現在會顯示已建立 1 個混合式連線。
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
至此，您已完成雲端混合式連線基礎結構的重要部分。接下來，您將建立對應的內部部署部分。

<a name="InstallHCM"></a>
## 安裝內部部署混合式連線管理員以完成連線 ##

1. 在 [Web 應用程式] 刀鋒視窗中按一下 [所有設定] > [網路] > [設定混合式連接端點]。 
	
	![Hybrid connections icon][HCIcon]
	
2. 在 [混合式連線] 分頁上，最近新增之端點的 [狀態] 欄會顯示為 [未連線]。請按一下連線加以設定。
	
	![Not connected][NotConnected]
	
	[混合式連線] 分頁隨即開啟。
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. 在此分頁上，按一下 [Listener Setup]。
	
	![Click Listener Setup][ClickListenerSetup]
	
4. [混合式連線屬性] 分頁隨即開啟。在 [內部部署混合式連線管理員] 下，選擇 [按一下此處進行安裝]。
	
	![Click here to install][ClickToInstallHCM]
	
5. 在 [應用程式執行] 安全性警告對話方塊中，選擇 [執行] 以繼續作業。
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	在 [使用者帳戶控制] 對話方塊中，選擇 [是]。
	
	![Choose Yes][UAC]
	
7. 系統會為您下載並安裝混合式連線管理員。
	
	![安裝][HCMInstalling]
	
8. 安裝完成後，請按一下 [關閉]。
	
	![Click Close][HCMInstallComplete]
	
	在 [混合式連線] 分頁上，[狀態] 欄此時會顯示為 [未連線]。
	
	![Connected Status][HCStatusConnected]

現在，您已完成混合式連線基礎結構，您可以使用它來建立混合式應用程式。

>[AZURE.NOTE]下列各節示範如何搭配 Mobile Apps .NET 後端專案使用混合式連線。

## 設定行動應用程式 .NET 後端專案以連線到 SQL Server 資料庫

在 App Service 中，Mobile Apps .NET 後端專案只是一個已安裝並初始化其他 Mobile Apps SDK 的 ASP.NET Web 應用程式。若要使用 Web 應用程式做為 Mobile Apps 後端，您必須[下載並初始化 Mobile Apps .NET 後端 SDK](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#install-sdk)。

針對 Mobile Apps，您也需要定義內部部署資料庫的連接字串與修改後端，以使用此連線。

1. 在 [Visual Studio 方案總管] 中開啟您的行動應用程式 .NET 後端的 Web.config 檔案，找出 **connectionStrings** 區段，新增會指向內部部署 SQL Server 資料庫的 SqlClient 項目，如下所示：

	    <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

	請記得將此字串中的 `<**secure_password**>` 取代為您為 *HybridConnectionLogin* 建立的密碼。

3. 在 Visual Studio 中按一下 [儲存]，以儲存 Web.config 檔案。

	> [AZURE.NOTE]在本機電腦上執行時，會使用此連線設定。在 Azure 中執行時，則會以入口網站中所定義的連線設定覆寫這個設定。

4. 展開 **Models** 資料夾，然後開啟以 *Context.cs* 結尾的資料模型檔案。

6. 修改 **DbContext** 執行個體建構函式，以將 `OnPremisesDBConnection` 值傳遞至類似下列程式碼片段的基底 **DbContext** 建構函式：

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

	現在，服務將會使用新的 SQL Server 資料庫連線。

## 更新行動應用程式後端，以使用內部部署連接字串

接下來，您必須為這個新的連接字串新增應用程式設定，以便能夠從 Azure 使用。

1. 回到 [Azure 入口網站](https://portal.azure.com)，在行動應用程式的 Web 應用程式後端程式碼中，按一下 [所有設定] > [應用程式設定]。

3. 在 [**Web 應用程式設定**] 刀鋒視窗中，向下捲動至 [**連接字串**]，然後以 `Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>` 之類的值新增名稱為 `OnPremisesDBConnection` 的新 **SQL Server** 連接字串。

	將 `<**secure_password**>` 替換為您內部部署資料庫的安全密碼。

	![Connection string for on-premises database](./media/web-sites-hybrid-connection-get-started/set-sql-server-database-connection.png)

2. 按下 [**儲存**]，以儲存您剛剛建立的混合式連線和連接字串。

現在可以重新發佈伺服器專案，並測試與現有的 Mobile Apps 用戶端之間的新連線。將會使用混合式連線，讀取內部部署資料庫中的資料並寫入。

<a name="NextSteps"></a>
## 後續步驟 ##

- 如需建立使用混合式連線的 ASP.NET Web 應用程式相關資訊，請參閱[使用混合式連線從 Azure 網站連線到內部部署 SQL Server](http://go.microsoft.com/fwlink/?LinkID=397979)。 

### 其他資源

[混合式連線概觀](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist 介紹混合式連線 (第 9 頻道視訊)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[混合式連線網站](https://azure.microsoft.com/services/biztalk-services/)

[BizTalk 服務：儀表板、監視器、調整、設定和混合式連線索引標籤](../biztalk-dashboard-monitor-scale-tabs/)

[透過絕佳的應用程式可攜性建置真實的混合式雲端 (第 9 頻道視訊)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server (第 9 頻道視訊)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[New]: ./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
 

<!---HONumber=AcomDC_0211_2016-->