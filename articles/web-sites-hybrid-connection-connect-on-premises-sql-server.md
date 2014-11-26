<properties linkid="web-sites-hybrid-connection-getting-started" title="Hybrid Connections Step-by-Step: Connect to on-premises SQL Server from an Azure website" pageTitle="Hybrid Connections Step-by-Step: Connect to on-premises SQL Server from an Azure website" description="Create a a website on Microsoft Azure and connect it to an on-premises SQL Server database" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm" />

# 使用混合式連線從 Azure 網站連接到內部部署 SQL Server

## 簡介

「混合式連線」可將 Microsoft Azure 網站連接到使用靜態 TCP 連接埠的內部部署資源。支援的資源包括 Microsoft SQL Server、MySQL、HTTP Web API、行動服務和大部分的自訂 Web 服務。

在本教學課程中，您將了解如何在 Azure 預覽入口網站中建立網站、使用新的「混合式連線」功能將網站連接到您的本機內部部署 SQL Server 資料庫、建立將使用混合式連線的簡易 ASP.NET Web 應用程式，以及將應用程式部署至 Azure 網站。Azure 上的已完成網站會將使用者認證儲存在內部部署的成員資格資料庫中。本教學課程假設您沒有使用 Azure 或 ASP.NET 的經驗。

> [WACOM.NOTE]「混合式連線」功能的「網站」部分僅適用於 [Azure 預覽入口網站][Azure 預覽入口網站]。若要在 BizTalk 服務中建立連線，請參閱[混合式連線][混合式連線]。

## 必要條件

若要完成本教學課程，您將需要下列產品。所有產品都有免費版本可使用，因此您可以免費進行 Azure 相關開發。

-   **Azure 訂閱** - 如需免費訂閱，請參閱 [Azure 免費試用][Azure 免費試用]。

-   **Visual Studio 2013** - 若要下載 Visual Studio 2013 的免費試用版，請參閱 [Visual Studio 下載][Visual Studio 下載]。請先安裝此項目再繼續作業。

-   **Microsoft .NET Framework 3.5 Service Pack 1** - 如果您的作業系統是 Windows 8.1、Windows Server 2012 R2、Windows 8、Windows Server 2012、Windows 7 或 Windows Server 2008 R2，您可以在 [控制台] \> [程式和功能] \> [開啟或關閉 Windows 功能] 中啟用此項目。否則，您可以從 [Microsoft 下載中心][Microsoft 下載中心]下載。

-   **SQL Server 2014 Express with Tools** - 請在 [Microsoft Web Platform Database 頁面][Microsoft Web Platform Database 頁面]下載免費的 Microsoft SQL Server Express。選擇 [Express] (非 LocalDB) 版本。[Express with Tools] 版本包含您將在此教學課程中使用的 SQL Server Management Studio。

-   **SQL Server Management Studio Express** - 此項目隨附於前述的 SQL Server 2014 Express with Tools 下載中，但您必須個別加以安裝，您可以從 [SQL Server Express 下載頁面][Microsoft Web Platform Database 頁面]加以下載並安裝。

本教學課程假設您具有 Azure 訂閱、您已安裝 Visual Studio 2013，並且已安裝或啟用 .NET Framework 3.5。本教學課程將說明如何在可與 Azure 混合式連線功能妥善搭配運作的組態中安裝 SQL Server 2014 Express (使用靜態 TCP 連接埠的預設執行個體)。在開始本教學課程之前，如果您尚未安裝 SQL Server，請先從前述位置下載 SQL Server 2014 Express with Tools。

### 注意事項

若要透過混合式連線使用內部部署 SQL Server 或 SQL Server Express 資料庫，必須在靜態連接埠上啟用 TCP/IP。SQL Server 上的預設執行個體會使用靜態連接埠 1433，但指定的執行個體則否。

安裝內部部署混合式連線管理員代理程式的電腦：

-   必須能夠透過連接埠 5671 連線到 Azure
-   必須能夠連繫內部部署資源的 *hostname*:*portnumber*。

本文中的步驟假設您使用將主控內部部署混合式連線代理程式之電腦中的瀏覽器。

如果您已在組態和符合前述條件的環境中安裝 SQL Server，您可以直接開始[在內部部署中建立 SQL Server 資料庫][在內部部署中建立 SQL Server 資料庫]。

## 本文內容

[A. 在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫][A. 在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫]

[B. 在 Azure 預覽入口網站中建立網站][B. 在 Azure 預覽入口網站中建立網站]

[C. 建立混合式連線和 BizTalk 服務][C. 建立混合式連線和 BizTalk 服務]

[D. 安裝內部部署混合式連線管理員以完成連線][D. 安裝內部部署混合式連線管理員以完成連線]

[E. 建立基本 ASP.NET Web 專案、編輯資料庫連接字串，和在本機執行專案][E. 建立基本 ASP.NET Web 專案、編輯資料庫連接字串，和在本機執行專案]

[F. 將 Web 應用程式發行至 Azure 並加以測試][F. 將 Web 應用程式發行至 Azure 並加以測試]

<a name="InstallSQL"></a>

## A. 在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫

本節說明如何安裝 SQL Server Express、啟用 TCP/IP 及建立資料庫，讓您的 Web 應用程式可在 Azure 預覽環境中運作。

### 安裝 SQL Server Express

1.  若要安裝 SQL Server Express，請執行您已下載的 **SQLEXPRWT\_x64\_ENU.exe** 或 **SQLEXPR\_x86\_ENU.exe** 檔案。[SQL Server 安裝中心] 精靈會隨即出現。

    ![SQL Server Install][SQL Server Install]

2.  選擇 [新的 SQL Server 獨立安裝或將功能加入到現有安裝]。遵循指示接受預設選項和設定，直到您進入 [執行個體組態] 頁面。

3.  在 [執行個體組態] 頁面上，選擇 [預設執行個體]。

    ![Choose Default Instance][Choose Default Instance]

    根據預設，SQL Server 的預設執行個體會在靜態連接埠 1433 上接聽來自 SQL Server 用戶端的要求，而這正是混合式連線功能的需求。指定的執行個體會使用動態連接埠和 UDP，而混合式連線並不加以支援。

4.  接受 [伺服器組態] 頁面上的預設值。

5.  在 [資料庫引擎組態] 頁面上的 [驗證模式] 下，選擇 [混合模式 (SQL Server 驗證和 Windows 驗證)]，並提供密碼。

    ![Choose Mixed Mode][Choose Mixed Mode]

    在本教學課程中，您將使用 SQL Server 驗證。請務必記住您提供的密碼，因為後續將會用到。

6.  逐步完成精靈的其餘步驟，以完成安裝。

### 啟用 TCP/IP

若要啟用 TCP/IP，您必須使用您在安裝 SQL Server Express 時所安裝的 SQL Server 組態管理員。請先執行[為 SQL Server 啟用 TCP/IP 網路通訊協定][為 SQL Server 啟用 TCP/IP 網路通訊協定]中的步驟，再繼續作業。

<a name="CreateSQLDB"></a>

### 在內部部署中建立 SQL Server 資料庫

您的 Visual Studio Web 應用程式需要可由 Azure 存取的成員資格資料庫。這必須要有 SQL Server 或 SQL Server Express 資料庫 (不是 MVC 範本依預設使用的 LocalDB 資料庫)，因此您接下來將會建立成員資格資料庫。

1.  在 SQL Server Management Studio 中，連接到您剛剛安裝的 SQL Server。(如果 [Connect to Server] 對話方塊未自動出現，請導覽至左窗格中的 [物件總管]、按一下 [連接]，然後按一下 [資料庫引擎]。)
    ![Connect to Server][Connect to Server]

    針對 [伺服器類型]，選擇 [資料庫引擎]。對於 [伺服器名稱]，您可以使用 **localhost** 或您要使用之電腦的名稱。選擇 [SQL Server 驗證]，然後以您先前建立的 sa 使用者名稱和密碼登入。

2.  若要使用 SQL Server Management Studio 建立新資料庫，請在 [物件總管] 中以滑鼠右鍵按一下 [資料庫]，然後按一下 [New Database]。

    ![Create new database][Create new database]

3.  在 [New Database] 對話方塊中，輸入 MembershipDB 做為資料庫名稱，然後按一下 [確定]。

    ![Provide database name][Provide database name]

    請注意，到目前為止您並未對資料庫做任何變更。成員資格資訊後續會在您執行 Web 應用程式時由該應用程式自動新增。

4.  在 [物件總管] 中，如果您展開 [資料庫]，您會發現成員資格資料庫已建立。

    ![MembershipDB created][MembershipDB created]

<a name="CreateSite"></a>

## B. 在 Azure 預覽入口網站中建立網站

> [WACOM.NOTE] 如果您已在 Azure 預覽入口網站中建立要用於此教學課程的網站，您可以直接跳到[建立混合式連線和 BizTalk 服務][C. 建立混合式連線和 BizTalk 服務]繼續作業。

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

## C. 建立混合式連線和 BizTalk 服務

1.  在 [預覽入口網站] 中，向下捲動網站的分頁，然後選擇 \[混合式連線]。

    ![Hybrid connections][Hybrid connections]

2.  在 \[混合式連線] 分頁上，按一下 [新增]。

    ![Add a hybrid connnection][Add a hybrid connnection]

3.  [新增混合式連線] 分頁隨即開啟。由於這是您的第一個混合式連線，因此會預先選取 [新增混合式連線] 選項，並為您開啟 [建立混合式連線] 分頁。

    ![Create a hybrid connection][Create a hybrid connection]

    在 [建立混合式連線分頁] 上：

    -   在 [名稱] 中，提供連線的名稱。
    -   針對 [主機名稱]，輸入您的 SQL Server 主機電腦的電腦名稱。
    -   針對 [連接埠]，輸入 1433 (SQL Server 的預設連接埠)。
    -   按一下 [Biz Talk 服務]

4.  [建立 Biz Talk 服務] 分頁隨即開啟。輸入 BizTalk 服務的名稱，然後按一下 [確定]。

    ![Create BizTalk service][Create BizTalk service]

    [建立 BizTalk 服務] 分頁隨即關閉，而您會回到 [建立混合式連線] 分頁。

5.  在 [建立混合式連線] 分頁上，按一下 [確定]。

    ![Click OK][Click OK]

6.  程序完成時，入口網站中的 [通知] 區域會通知您已成功建立連線。

    ![Success notification][Success notification]

7.  在網站分頁上，\[混合式連線] 圖示現在會顯示已建立 1 個混合式連線。

    ![One hybrid connection created][One hybrid connection created]

至此，您已完成雲端混合式連線基礎結構的重要部分。接下來，您將建立對應的內部部署部分。

<a name="InstallHCM"></a>

## D. 安裝內部部署混合式連線管理員以完成連線

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

    在 \[混合式連線] 分頁上，[狀態] 欄此時會顯示為 [未連線]。

    ![Connected Status][Connected Status]

現在，混合式連線基礎結構已完成，您將建立使用此基礎結構的 Web 應用程式。

<a name="CreateASPNET"></a>

## E. 建立基本 ASP.NET Web 專案、編輯資料庫連接字串，和在本機執行專案

### 建立基本 ASP.NET 專案

1.  在 Visual Studio 的 [檔案] 功能表上，建立新的專案：

    ![New Visual Studio project][New Visual Studio project]

2.  在 [新增專案] 對話方塊的 [範本] 區段中選取 [Web]，再選擇 [ASP.NET Web 應用程式]，然後按一下 [確定]。

    ![Choose ASP.NET Web Application][Choose ASP.NET Web Application]

3.  在 [新增 ASP.NET 專案] 對話方塊中，選擇 [MVC]，然後按一下 [確定]。

    ![Choose MVC][Choose MVC]

4.  建立專案之後，會出現應用程式 Readme 頁面。請還不要執行 Web 專案。

    ![Readme page][Readme page]

### 編輯應用程式的資料庫連接字串

在此步驟中您會編輯連接字串，以指示應用程式應至何處尋找您的本機 SQL Server Express 資料庫。連接字串位於應用程式的 Web.config 檔案中，其中包含應用程式的組態資訊。

> [WACOM.NOTE] 為確保您的應用程式使用的是您在 SQL Server Express 中建立的資料庫，而不是 Visual Studio 的預設 LocalDB 中的資料庫，請務必先完成此步驟，再執行您的專案。

1.  在 [方案總管] 中，按兩下 Web.config 檔案。

    ![Web.config][Web.config]

2.  依照下列範例中的語法編輯 [connectionStrings] 區段，將 SQL Server 資料庫指向您的本機電腦：

    ![Connection string][Connection string]

    編譯連接字串時，請留意下列事項：

    -   如果您要連接到指定的執行個體而非預設執行個體 (例如 YourServer\\SQLEXPRESS)，您必須將 SQL Server 設定成使用靜態連接埠。如需設定靜態連接埠的相關資訊，請參閱[如何將 SQL Server 設定成在特定連接埠上接聽][如何將 SQL Server 設定成在特定連接埠上接聽]。根據預設，指定的執行個體會使用 UDP 和動態連接埠，而混合式連線並不加以支援。

    -   建議您指定連接埠 (依預設為 1433，如範例所示) 和連接字串，以確定您的本機 SQL Server 會啟用 TCP 並使用正確的連接埠。

    -   請務必使用 SQL Server 驗證進行連接，以在您的連接字串中指定使用者識別碼和密碼。

3.  在 Visual Studio 中按一下 [儲存]，以儲存 Web.config 檔案。

### 在本機執行專案和註冊新使用者

1.  現在，請按一下 [偵錯] 下的瀏覽按鈕，在本機執行您新的 Web 專案。此範例使用 Internet Explorer。

    ![Run project][Run project]

2.  在預設網頁的右上方，選擇 [註冊] 以註冊新帳戶：

    ![Register a new account][Register a new account]

3.  輸入使用者名稱和密碼：

    ![Enter user name and password][Enter user name and password]

    這會在您的本機 SQL Server 上自動建立一個資料庫，存放您應用程式的成員資格資訊。其中一個資料表 (**dbo.AspNetUsers**) 包含如同您剛剛輸入的網站使用者認證。稍後在教學課程中會看見此資料表。

4.  關閉預設網頁的瀏覽器視窗。這會停止 Visual Studio 中的應用程式。

現在您已可執行下一個步驟，也就是將應用程式發行至 Azure，並加以測試。

<a name="PubNTest"></a>

## F. 將 Web 應用程式發行至 Azure 並加以測試

現在，您會將應用程式發行至您在 Azure 上的網站並加以測試，以確認您先前設定的混合式連線是否可用來將網站應用程式連接到本機電腦上的資料庫。

### 發行 Web 應用程式

1.  您可以在 Azure 入口網站中下載網站的發行設定檔。在您網站的分頁上，選擇 \[Download publish profile]，然後將檔案儲存至您的電腦。

    ![Download publish profile][Download publish profile]

    ![Publish profile in downloads folder][Publish profile in downloads folder]

    接著，您會將此檔案匯入 Visual Studio Web 應用程式中。

2.  在 Visual Studio 的 [方案總管] 中，以滑鼠右鍵按一下專案名稱，並選取 [發行]。

    ![Select publish][Select publish]

3.  在 [Publish Web] 對話方塊的 [設定檔] 索引標籤上，選擇 [匯入]。

    ![Import][Import]

4.  瀏覽至您已下載的發行設定檔並加以選取，然後按一下 [確定]。

    ![Browse to profile][Browse to profile]

5.  您的發行資訊會匯入並顯示在對話方塊的 [連線] 索引標籤上。

    ![Click Publish][Click Publish]

    按一下 [發行]。

    發行完成後，瀏覽器將會啟動並顯示您熟悉的網站，差別在於網站現在已運作於 Azure 雲端中。

接著，您將使用即時 Web 應用程式來檢視其運作中的混合式連線。

### 測試 Azure 上已完成的 Web 應用程式

1.  在您位於 Azure 上的網頁中，從右上方選擇 [登入]。

    ![Test log in][Test log in]

2.  您的 Azure 網站現在已連接到您本機電腦上的 Web 應用程式成員資格資料庫。若要驗證這一點，請以您先前在本機資料庫中輸入的相同認證登入。

    ![Hello greeting][Hello greeting]

3.  若要進一步測試新的混合式連線，請登出您的 Azure Web 應用程式，再以另一名使用者的身分註冊。提供新的使用者名稱和密碼，然後按一下 [註冊]。

    ![Test register another user][Test register another user]

4.  若要驗證新使用者的認證已透過混合式連線儲存在您的本機資料庫中，請在本機電腦上開啟 SQL Management Studio。在 [物件總管] 中展開 **MembershipDB** 資料庫，然後展開 [資料表]。以滑鼠右鍵按一下 **hybridService1.TodoItems** 成員資格資料表，然後選擇 [Select Top 1000 Rows] 以檢視結果。

    ![View the results][View the results]

5.  您的本機成員資格資料表此時會將兩個帳戶都顯示出來 - 您在本機建立的帳戶，以及您在 Azure 雲端中建立的帳戶。您在雲端中建立的帳戶已透過 Azure 的混合式連線功能儲存到您的內部部署資料庫。

    ![Registered users in on-premises database][Registered users in on-premises database]

現在，您已在 Azure 雲端中的網站與內部部署 SQL Server 資料庫之間，建立並部署使用混合式連線的 ASP.NET Web 應用程式。恭喜！

## 另請參閱

[混合式連線概觀][混合式連線]

[Josh Twist 介紹混合式連線 (第 9 頻道視訊)][Josh Twist 介紹混合式連線 (第 9 頻道視訊)]

[混合式連線網站][混合式連線網站]

[BizTalk 服務：儀表板、監視器、調整、設定和混合式連線索引標籤][BizTalk 服務：儀表板、監視器、調整、設定和混合式連線索引標籤]

[透過絕佳的應用程式可攜性建置真實的混合式雲端 (第 9 頻道視訊)][透過絕佳的應用程式可攜性建置真實的混合式雲端 (第 9 頻道視訊)]

[使用混合式連線從 Azure 行動服務連接到內部部署 SQL Server][使用混合式連線從 Azure 行動服務連接到內部部署 SQL Server]

[使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server (第 9 頻道視訊)][使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server (第 9 頻道視訊)]

[ASP.NET 身分識別概觀][ASP.NET 身分識別概觀]

<!-- IMAGES -->

  [Azure 預覽入口網站]: https://portal.azure.com
  [混合式連線]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [Azure 免費試用]: http://azure.microsoft.com/zh-tw/pricing/free-trial/
  [Visual Studio 下載]: http://www.visualstudio.com/downloads/download-visual-studio-vs
  [Microsoft 下載中心]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22
  [Microsoft Web Platform Database 頁面]: http://www.microsoft.com/web/platform/database.aspx
  [在內部部署中建立 SQL Server 資料庫]: #CreateSQLDB
  [A. 在內部部署中安裝 SQL Server Express、啟用 TCP/IP 及建立 SQL Server 資料庫]: #InstallSQL
  [B. 在 Azure 預覽入口網站中建立網站]: #CreateSite
  [C. 建立混合式連線和 BizTalk 服務]: #CreateHC
  [D. 安裝內部部署混合式連線管理員以完成連線]: #InstallHCM
  [E. 建立基本 ASP.NET Web 專案、編輯資料庫連接字串，和在本機執行專案]: #CreateASPNET
  [F. 將 Web 應用程式發行至 Azure 並加以測試]: #PubNTest
  [SQL Server Install]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
  [Choose Default Instance]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
  [Choose Mixed Mode]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
  [為 SQL Server 啟用 TCP/IP 網路通訊協定]: http://technet.microsoft.com/zh-tw/library/hh231672%28v=sql.110%29.aspx
  [Connect to Server]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
  [Create new database]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
  [Provide database name]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
  [MembershipDB created]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
  [New button]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
  [New website]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
  [Website name]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
  [Website running]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
  [Click browse to see your website]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
  [Default website page]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
  [Hybrid connections]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
  [Add a hybrid connnection]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
  [Create a hybrid connection]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
  [Create BizTalk service]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
  [Click OK]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
  [Success notification]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
  [One hybrid connection created]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
  [Hybrid connections icon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
  [Not connected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
  [NotConnectedBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
  [Click Listener Setup]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
  [Click here to install]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
  [Choose Run to continue]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
  [Choose Yes]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
  [安裝]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
  [Click Close]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
  [Connected Status]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
  [New Visual Studio project]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
  [Choose ASP.NET Web Application]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
  [Choose MVC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
  [Readme page]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
  [Web.config]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
  [Connection string]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
  [如何將 SQL Server 設定成在特定連接埠上接聽]: http://support.microsoft.com/kb/823938
  [Run project]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
  [Register a new account]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
  [Enter user name and password]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
  [Download publish profile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
  [Publish profile in downloads folder]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
  [Select publish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
  [Import]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
  [Browse to profile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
  [Click Publish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
  [Test log in]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
  [Hello greeting]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
  [Test register another user]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
  [View the results]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
  [Registered users in on-premises database]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png
  [Josh Twist 介紹混合式連線 (第 9 頻道視訊)]: http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections
  [混合式連線網站]: http://azure.microsoft.com/zh-tw/services/biztalk-services/
  [BizTalk 服務：儀表板、監視器、調整、設定和混合式連線索引標籤]: http://azure.microsoft.com/zh-tw/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
  [透過絕佳的應用程式可攜性建置真實的混合式雲端 (第 9 頻道視訊)]: http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=
  [使用混合式連線從 Azure 行動服務連接到內部部署 SQL Server]: http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/
  [使用混合式連線從 Azure 行動服務連線到內部部署 SQL Server (第 9 頻道視訊)]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections
  [ASP.NET 身分識別概觀]: http://www.asp.net/identity
