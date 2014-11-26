<properties linkid="mobile-services-dotnet-backend-hybrid-connections-get-started" urlDisplayName="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" pageTitle="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections - Azure Mobile Services" metaKeywords="" description="Learn how to connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" metaCanonical="" services="" documentationCenter="Mobile" title="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg" />

# 使用混合式連線從 Azure 行動服務連接到內部部署 SQL Server

當企業轉換至雲端時，經常必須將某些資產保存在內部部署中，無論是基於技術、法規要求還是安全性原因。行動服務可讓您在這些資產之上輕鬆地建立雲端代理行動層，同時使用混合式連線從您的內部部署安全性連接到此行動層。支援的資產包括任何可在靜態 TCP 連接埠上執行的資源，例如 Microsoft SQL Server、MySQL、HTTP Web API 和大部分的自訂 Web 服務。

在本教學課程中，您將了解如何修改 .NET 後端行動服務，以使用本機內部部署 SQL Server 資料庫，而不要使用隨著您的服務佈建的預設 Azure SQL Database。雖然混合式連線支援 JavaScript 後端行動服務，但本主題只會討論 .NET 後端行動服務。

本主題將逐步引導您完成下列基本步驟：

1.  [必要條件][必要條件]
2.  [在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫][在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫]
3.  [建立混合式連線][建立混合式連線]
4.  [安裝內部部署混合式連線管理員以完成連線][安裝內部部署混合式連線管理員以完成連線]
5.  [修改行動服務以使用連線][修改行動服務以使用連線]

<a name="Prerequisites"></a>

## 必要條件

若要完成本教學課程，您將需要下列產品。所有產品都有免費版本可使用，因此您可以免費進行 Azure 相關開發。

-   **Visual Studio 2013** - 若要下載 Visual Studio 2013 的免費試用版，請參閱 [Visual Studio 下載][Visual Studio 下載]。請先安裝此項目再繼續作業。

-   **SQL Server 2014 Express with Tools** - 請在 [Microsoft Web Platform Database 頁面][Microsoft Web Platform Database 頁面]下載免費的 Microsoft SQL Server Express。選擇 \[Express**\] (非 LocalDB) 版本。[Express with Tools] 版本包含您將在此教學課程中使用的 SQL Server Management Studio。

您也將需要會使用混合式連線連接到 Azure 的內部部署機器。該機器必須符合下列條件：

-   可透過連接埠 5671 連接到 Azure
-   可連繫您要連接之內部部署資源的 *hostname*:*portnumber*。此資源不一定位於相同的機器上。

<a name="InstallSQL"></a>

## 在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫

若要透過混合式連線使用內部部署 SQL Server 或 SQL Server Express 資料庫，必須在靜態連接埠上啟用 TCP/IP。SQL Server 上的預設執行個體會使用靜態連接埠 1433，但指定的執行個體則否。

如需如何設定 SQL Server 而使其符合前述條件的詳細指示，請參閱[在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫][1]。如果您已在組態和符合前述條件的環境中安裝 SQL Server，您可以直接開始[在內部部署中建立 SQL Server 資料庫][在內部部署中建立 SQL Server 資料庫]。

基於本教學課程的目的，我們會假設資料庫名稱為 **OnPremisesDB**、資料庫執行於連接埠 **1433**，而機器的主機名稱為 **onPremisesServer**。

<a name="CreateHC"></a>

## 建立混合式連線

1.  在內部部署機器上，登入 [Azure 管理入口網站][Azure 管理入口網站]。

2.  在導覽窗格底部選取 [+新增]，然後選取 [應用程式服務]、[BizTalk 服務] 和 [自訂建立]

    ![Create BizTalk Service][Create BizTalk Service]

3.  提供 [BizTalk 服務名稱]，然後選取 [版本]。

    ![Configure new BizTalk Service][Configure new BizTalk Service]

    本教學課程使用 **mobile1**。您必須為新的 BizTalk 服務提供唯一名稱。

4.  BizTalk 服務建立後，請選取 [混合式連線] 索引標籤，然後按一下 [新增]。

    ![Add Hybrid Connection][Add Hybrid Connection]

    這會建立新的混合式連線。

5.  為您的混合式連線提供 [名稱] 和 [主機名稱]，然後將 [連接埠] 設為 `1433`.

    ![Configure Hybrid Connection][Configure Hybrid Connection]

    主機名稱是內部部署伺服器的名稱。這會設定混合式連線，使其存取在連接埠 1433 上執行的 SQL Server。

6.  新連線建立後，會出現在下表中。

    ![Hybrid Connection successfully created][Hybrid Connection successfully created]

    新連線的狀態會顯示為 [On-premises setup incomplete]。

現在，我們必須在內部部署電腦上安裝混合式連線管理。

<a name="InstallHCM"></a>

## 安裝內部部署混合式連線管理員以完成連線

混合式連線管理員可讓您的內部部署機器連接到 Azure 並轉送 TCP 流量。您必須在您嘗試要從 Azure 存取的內部部署電腦上安裝此軟體。

1.  選取您剛剛建立的連線，並在底列中按一下 [內部部署設定]。

    ![On-Premises Setup][On-Premises Setup]

2.  按一下 [安裝和設定]。

    ![Install Hybrid Connection Manager][Install Hybrid Connection Manager]

    這會安裝自訂的連線管理員執行個體，而且此執行個體已預先設定成使用您剛剛建立的混合式連線。

3.  完成連線管理員其餘的安裝步驟。

    ![Hybrid Connection Manager setup][Hybrid Connection Manager setup]

    安裝完成時，混合式連線狀態會變更為 [已連接 1 個執行個體]。您可能必須重新整理瀏覽器，並等候數分鐘。內部部署安裝現在已完成。

    ![Hybrid Connection connected][Hybrid Connection connected]

<a name="CreateService"></a>

## 修改行動服務以使用連線

### 將混合式連線與服務產生關聯

1.  在入口網站的 [行動服務] 索引標籤中，選取現有的行動服務或建立新的服務。

    > [WACOM.NOTE]請確實選取使用 .NET 後端建立的服務，或建立新的 .NET 後端行動服務。若想了解如何建立新的 .NET 後端行動服務，請參閱[開始使用行動服務][開始使用行動服務]。

2.  在行動服務的 [設定] 索引標籤上找出 [混合式連線] 區段，然後選取 [新增混合式連線]。

    ![Associate Hybrid Connection][Associate Hybrid Connection]

3.  選取我們剛剛在 [BizTalk 服務] 索引標籤上建立的混合式連線，然後按 [確定]。

    ![Pick associated Hybrid Connection][Pick associated Hybrid Connection]

行動服務現在已與新的混合式連線產生關聯。

### 更新服務以使用內部部署連接字串

最後，我們必須建立應用程式設定，以將連接字串的值儲存到內部部署 SQL Server。接著，我們必須修改行動服務，以使用新的連接字串。

1.  在 [應用程式設定] 中的 [設定] 索引標籤上，新增名為`onPremisesDatabase` 的新應用程式設定 (且具有如下的值 `Server=onPremisesServer,1433;Database=OnPremisesDB;User ID=sa;Password={password}`.

    ![App setting for connection string][App setting for connection string]

    將`{password}` 取代為您內部部署資料庫的安全密碼。

2.  按 [儲存] 以儲存混合式連線，然後按我們剛剛建立的應用程式設定。

3.  在 Visual Studio 2013 中，開啟定義 .NET 型行動服務的專案。

    若想了解如何下載 .NET 後端專案，請參閱[開始使用行動服務][開始使用行動服務]。

4.  在 [方案總管] 中展開 [模型] 資料夾，然後開啟以 *Context.cs* 結尾的資料模型檔案。

5.  修改 **DbContext** 執行個體建構函式，使其類似於下列程式碼片段：

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base(ConfigurationManager.AppSettings["onPremisesDatabase"])
            {
            }

            // snipped
        }

    服務現在會使用新的混合式連接字串來定義 Azure 中的應用程式設定。

6.  發佈您的變更，並使用連接到行動服務的用戶端應用程式叫用某些作業，以產生資料庫變更。

7.  在執行 SQL Server 的內部部署電腦上開啟 SQL Management Studio，然後在 [物件總管] 中展開 **OnPremisesDB** 資料庫，再展開 [資料表]

8.  以滑鼠右鍵按一下 **hybridService1.TodoItems** 資料表，然後選擇 [Select Top 1000 Rows] 以檢視結果。

    ![SQL Management Studio][SQL Management Studio]

您的應用程式中產生的變更已由行動服務推播至內部部署資料庫。

## 另請參閱

-   [混合式連線網站][混合式連線網站]
-   [混合式連線概觀][混合式連線概觀]
-   [BizTalk 服務：儀表板、監視器、調整、設定和混合式連線索引標籤][BizTalk 服務：儀表板、監視器、調整、設定和混合式連線索引標籤]

<!-- IMAGES -->

  [必要條件]: #Prerequisites
  [在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫]: #InstallSQL
  [建立混合式連線]: #CreateHC
  [安裝內部部署混合式連線管理員以完成連線]: #InstallHCM
  [修改行動服務以使用連線]: #CreateService
  [Visual Studio 下載]: http://www.visualstudio.com/downloads/download-visual-studio-vs
  [Microsoft Web Platform Database 頁面]: http://www.microsoft.com/web/platform/database.aspx
  [1]: /zh-tw/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#InstallSQL
  [在內部部署中建立 SQL Server 資料庫]: /zh-tw/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#CreateSQLDB
  [Azure 管理入口網站]: http://go.microsoft.com/fwlink/p/?linkid=213885&clcid=0x409
  [Create BizTalk Service]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/1.png
  [Configure new BizTalk Service]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/2.png
  [Add Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/3.png
  [Configure Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/4.png
  [Hybrid Connection successfully created]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5.png
  [On-Premises Setup]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5-1.png
  [Install Hybrid Connection Manager]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/6.png
  [Hybrid Connection Manager setup]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/7.png
  [Hybrid Connection connected]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/8.png
  [開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Associate Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/9.png
  [Pick associated Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/10.png
  [App setting for connection string]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png
  [SQL Management Studio]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/12.png
  [混合式連線網站]: http://azure.microsoft.com/zh-tw/services/biztalk-services/
  [混合式連線概觀]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [BizTalk 服務：儀表板、監視器、調整、設定和混合式連線索引標籤]: http://azure.microsoft.com/zh-tw/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
