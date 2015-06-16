<properties 
	pageTitle="使用混合式連線從 Azure 行動服務連線至內部佈署 SQL Server - Azure 行動服務" 
	description="了解如何使用混合式連線從 Azure 行動服務連線至內部佈署 SQL Server" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="glenga"/>

  
# 使用混合式連線從 Azure 行動服務連線至內部佈署 SQL Server 

當企業轉換至雲端時，經常必須將某些資產保存在內部部署中，無論是基於技術、法規要求還是安全性原因。行動服務可讓您在這些資產之上輕鬆地建立雲端代理行動層，同時使用混合式連線從您的內部部署安全性連接到此行動層。支援的資產包括任何可在靜態 TCP 連接埠上執行的資源，例如 Microsoft SQL Server、MySQL、HTTP Web API 和大部分的自訂 Web 服務。

在本教學課程中，您將了解如何修改 .NET 後端行動服務，以使用本機內部部署 SQL Server 資料庫，而不要使用隨著您的服務佈建的預設 Azure SQL Database。雖然混合式連線支援 JavaScript 後端行動服務，但本主題只會討論 .NET 後端行動服務。

本主題將逐步引導您完成下列基本步驟：

1. [必要條件](#Prerequisites)
2. [在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫](#InstallSQL)
3. [建立混合式連線](#CreateHC)
4. [安裝內部部署混合式連線管理員以完成連線](#InstallHCM)
5. [修改行動服務以使用連線](#CreateService)

<a name="Prerequisites"></a>
##必要條件##
若要完成本教學課程，您將需要下列產品。所有產品都有免費版本可使用，因此您可以免費進行 Azure 相關開發。

- **Visual Studio 2013** - 若要下載 Visual Studio 2013 的免費試用版，請參閱 [Visual Studio 下載](http://www.visualstudio.com/downloads/download-visual-studio-vs)。請先安裝此項目再繼續作業。

- **SQL Server 2014 Express with Tools** - 請在 [Microsoft Web Platform Database 頁面](http://www.microsoft.com/web/platform/database.aspx)下載免費的 Microsoft SQL Server Express。選擇 [Express]**** (非 LocalDB) 版本。[Express with Tools]**** 版本包含您將在此教學課程中使用的 SQL Server Management Studio。

您也將需要會使用混合式連線連接到 Azure 的內部部署機器。該機器必須符合下列條件：

- 可透過連接埠 5671 連接到 Azure
- 可聯繫您要連接之內部部署資源的 *hostname*:*portnumber*。此資源不一定位於相同的機器上。 

<a name="InstallSQL"></a>
## 在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫

若要透過混合式連線使用內部部署 SQL Server 或 SQL Server Express 資料庫，必須在靜態連接埠上啟用 TCP/IP。SQL Server 上的預設執行個體會使用靜態連接埠 1433，但指定的執行個體則否。

如需如何設定 SQL Server 而使其符合前述條件的詳細指示，請參閱[在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫](app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md#InstallSQL)。如果您已在組態和符合前述條件的環境中安裝 SQL Server，您可以直接開始[在內部部署中建立 SQL Server 資料庫](app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md#CreateSQLDB)。

基於本教學課程的目的，我們會假設資料庫名稱為 **OnPremisesDB**、資料庫執行於連接埠 **1433**，而機器的主機名稱為 **onPremisesServer**。

<a name="CreateHC"></a>
## 建立混合式連線
1. 在內部部署機器上，登入 [Azure 管理入口網站](http://go.microsoft.com/fwlink/p/?linkid=213885&clcid=0x409)。

2. 在導覽窗格底部選取 [+新增]****，然後選取 [應用程式服務]****、[BizTalk 服務]**** 和 [自訂建立]****

	![Create BizTalk Service][CreateBTS]

3. 提供 [BizTalk 服務名稱]****，然後選取 [版本]****。

	![Configure new BizTalk Service][ConfigureBTS]

	本教學課程使用 **mobile1**。您必須為新的 BizTalk 服務提供唯一名稱。

4. BizTalk 服務建立後，請選取 [混合式連線]**** 索引標籤，然後按一下 [新增]****。

	![Add Hybrid Connection][AddHC]

	這會建立新的混合式連線。

5. 提供混合式連線的 [名稱]**** 和 [主機名稱]****，然後將 [連接埠]**** 設為 `1433`。
  
	![Configure Hybrid Connection][ConfigureHC]

	主機名稱是內部部署伺服器的名稱。這會設定混合式連線，使其存取在連接埠 1433 上執行的 SQL Server。

6. 新連線建立後，會出現在下表中。
 
	![Hybrid Connection successfully created][HCCreated]
	
	新連線的狀態會顯示為 [On-premises setup incomplete]****。

現在，我們必須在內部部署電腦上安裝混合式連線管理。

<a name="InstallHCM"></a>
## 安裝內部部署混合式連線管理員以完成連線

混合式連線管理員可讓您的內部部署機器連接到 Azure 並轉送 TCP 流量。您必須在您嘗試要從 Azure 存取的內部部署電腦上安裝此軟體。

1. 選取您剛剛建立的連線，並在底列中按一下 [內部部署設定]****。

	![On-Premises Setup][DownloadHCM]

4. 按一下 [安裝和設定]****。

	![Install Hybrid Connection Manager][InstallHCM]

	這會安裝自訂的連線管理員執行個體，而且此執行個體已預先設定成使用您剛剛建立的混合式連線。

3. 完成連線管理員其餘的安裝步驟。

	![Hybrid Connection Manager setup][HCMSetup]

	安裝完成時，混合式連線狀態會變更為 [已連接 1 個執行個體]****。您可能必須重新整理瀏覽器，並等候數分鐘。內部部署安裝現在已完成。

	![Hybrid Connection connected][HCConnected]

<a name="CreateService"></a>
## 修改行動服務以使用連線
### 將混合式連線與服務產生關聯
1. 在入口網站的 [行動服務]**** 索引標籤中，選取現有的行動服務或建立新的服務。 

	>[AZURE.NOTE]請確實選取使用 .NET 後端建立的服務，或建立新的 .NET 後端行動服務。若想了解如何建立新的 .NET 後端行動服務，請參閱[開始使用行動服務](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md)。

2. 在行動服務的 [設定]**** 索引標籤上找出 [混合式連線]**** 區段，然後選取 [新增混合式連線]****。

	![Associate Hybrid Connection][AssociateHC]

3. 選取我們剛剛在 [BizTalk 服務] 索引標籤上建立的混合式連線，然後按 [確定]****。

	![Pick associated Hybrid Connection][PickHC]

行動服務現在已與新的混合式連線產生關聯。

### 更新服務以使用內部部署連接字串
最後，我們必須建立應用程式設定，以將連接字串的值儲存到內部部署 SQL Server。接著，我們必須修改行動服務，以使用新的連接字串。

1. 在 [連接字串]**** 的 [設定]**** 索引標籤上，加入名為 `OnPremisesDatabase` 的新連接字串，其值類似 `Server=onPremisesServer,1433;Database=OnPremisesDB;User ID=sa;Password={password}`。

	![Connection string for on-premises database][ConnectionString]

	將 `{password}` 替換為您內部部署資料庫的安全密碼。

2. 按下 [儲存]****，以儲存我們剛剛建立的混合式連線和連接字串。

3. 在 Visual Studio 2013 中，開啟定義 .NET 型行動服務的專案。

	若想了解如何下載 .NET 後端專案，請參閱[開始使用行動服務](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md)。
 
4. 在 [方案總管] 中展開 [模型]**** 資料夾，然後開啟以 *Context.cs* 結尾的資料模型檔案。

6. 修改 **DbContext** 執行個體建構函式，使其類似於下列程式碼片段：

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDatabase")
            {
            }
        
            // snipped
        }

	現在，服務會使用 Azure 中定義的新混合式連接字串。

5. 發佈您的變更，並使用連接到行動服務的用戶端應用程式叫用某些作業，以產生資料庫變更。

6. 在執行 SQL Server 的內部部署電腦上開啟 SQL Management Studio，然後在 [物件總管] 中展開 **OnPremisesDB** 資料庫，再展開 [資料表]****。

9. 以滑鼠右鍵按一下 **hybridService1.TodoItems** 資料表，然後選擇 [Select Top 1000 Rows]**** 以檢視結果。

	![SQL Management Studio][SMS]

您的應用程式中產生的變更已由行動服務推播至內部部署資料庫。

##另請參閱##
 
+ [混合式連線網站](http://azure.microsoft.com/services/biztalk-services/)
+ [混合式連線概觀](http://go.microsoft.com/fwlink/p/?LinkID=397274)
+ [BizTalk 服務：儀表板、監視器、調整、設定和混合式連線索引標籤](biztalk-dashboard-monitor-scale-tabs)

<!-- IMAGES -->
[CreateBTS]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/1.png
[ConfigureBTS]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/2.png
[AddHC]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/3.png
[ConfigureHC]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/4.png
[HCCreated]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5.png
[InstallHCM]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/6.png
[HCMSetup]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/7.png
[HCConnected]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/8.png
[AssociateHC]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/9.png
[PickHC]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/10.png
[ConnectionString]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png
[SMS]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/12.png
[DownloadHCM]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5-1.png

<!--HONumber=54-->