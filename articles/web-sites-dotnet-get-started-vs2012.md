<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Get started with Azure and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

開始使用 Azure 和 ASP.NET
=========================

[Visual Studio 2013](/en-us/develop/net/tutorials/get-started/ "Visual Studio 2013")[Visual Studio 2012](/en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012")

**注意**

現已提供[新版的教學課程](/en-us/develop/net/tutorials/get-started/)。如果您想要使用 Visual Studio 2012，仍舊可以遵循此版本的教學課程說明，但是可能缺少某些最新的 Azure SDK 功能說明。

本教學課程示範如何使用 Visual Studio 2012 中或 Visual Studio 2012 for Web Express 中的 [Publish Web] 精靈，以將 ASP.NET Web 應用程式部署至 Azure 網站。您可以視個人偏好，使用 Visual Studio 2010 或 Visual Web Developer Express 2010 來遵循此教學課程步驟。

您可以免費申請 Azure 帳戶，而且如果您還沒有 Visual Studio 2012，SDK 會自動安裝 Visual Studio 2012 for Web Express。如此您就能開始免費進行 Azure 相關開發。

本教學課程假設您先前沒有使用 Azure 的經驗。完成此教學課程後，您將有個簡單的 Web 應用程式已在雲端中啟動並執行。

您將了解：

-   如何安裝 Azure SDK 好讓電腦適合用於進行 Azure 開發。
-   如何建立 Visual Studio ASP.NET MVC 4 專案，並將它發行至 Azure 網站。

下圖顯示完成的應用程式：

![網站範例](./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png)

**注意**若要完成此教學課程，您需要 Azure 帳戶。如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)或是[申請免費試用](/en-us/pricing/free-trial/?WT.mc_id=A261C142F)。

### 教學課程區段

1.  [設定開發環境](#setupdevenv)
2.  [在 Azure 中建立網站](#setupwindowsazure)
3.  [建立 ASP.NET MVC 4 應用程式](#createmvc4app)
4.  [將應用程式部署至 Azure](#deploytowindowsazure)
5.  [後續步驟](#nextsteps)

[WACOM.INCLUDE [install-sdk-2012-only](../includes/install-sdk-2012-only.md)]

建立網站建立網站
----------------

以下步驟說明如何建立 Azure 網站。

1.  在 [Azure 管理入口網站](http://manage.windowsazure.com)中，按一下 **[網站]**，再按 **[新增]**。

![新網站](./media/web-sites-dotnet-get-started-vs2012/WebSiteNew.png)

1.  按一下 **[快速建立]**。

    ![快速建立](./media/web-sites-dotnet-get-started-vs2012/ClickQuickCreate.png)

2.  在精靈的 [建立網站] 步驟****中，於 [URL] 方塊輸入字串做為應用程式的唯一 URL。完整的 URL 將包含您在此輸入的字串，加上文字方塊旁邊顯示的尾碼。****下圖顯示「範例 1」****，但是如果有人已經用了該字串做為 URL，您必須重新輸入不同的值。

3.  在 **[區域]** 下拉式清單中，選擇最接近您的區域。此設定指定您的網站將執行於的資料中心。

4.  按一下 **[建立網站]** 箭頭。

    ![建立新網站](./media/web-sites-dotnet-get-started-vs2012/CreateWebsite.png)

    [管理入口網站] 隨即返回 [網站] 頁面，且 **[狀態]** 欄顯示正在建立網站。稍待片刻 (通常不到一分鐘)，**[狀態]** 欄就會顯示建立網站成功。在左側的瀏覽列中，您的儲存體中的網站數量會顯示在 [網站] 圖示旁邊****。

    ![管理入口網站的網站頁面 - 已建立網站](./media/web-sites-dotnet-get-started-vs2012/WebSiteStatusRunning.png)

建立應用程式建立 ASP.NET MVC 4 應用程式
---------------------------------------

您已建立 Azure 網站，但網站中還沒有內容。接下來的步驟是建立要發行至 Azure 的 Visual Studio Web 應用程式。

### 建立專案

1.  啟動 Visual Studio 2012 或 Visual Studio 2012 for Web Express。

2.  從 **[檔案]** 功能表中，按一下 **[新增]**，再按 **[專案]**。

![[檔案] 功能表中的 [新增專案]](./media/web-sites-dotnet-get-started-vs2012/NewVSProject.png)

1.  在 **[新增專案]** 對話方塊中，展開 **[C\#]** 並選取 **[已安裝的範本]** 下的 **[Web]**，再選取 **[ASP.NET MVC 4 Web 應用程式]**。

2.  確定已選取 **[.NET Framework 4.5]** 做為目標架構。

3.  將應用程式命名為 **MyExample**，再按一下 **[確定]**。

![[新增專案] 對話方塊](./media/web-sites-dotnet-get-started-vs2012/NewMVC4WebApp.png)

1.  在 **[New ASP.NET MVC 4 Project]** 對話方塊中，選取 **[網際網路應用程式]** 範本，再按一下 **[確定]**。

![[New ASP.NET MVC 4 Project] 對話方塊](./media/web-sites-dotnet-get-started-vs2012/InternetAppTemplate.png)

### 在本機執行應用程式

1.  按 **CTRL**+**F5** 執行該應用程式。應用程式首頁隨即出現在預設瀏覽器中。

![在本機執行的網站](./media/web-sites-dotnet-get-started-vs2012/AppRunningLocally.png)

以上就是要建立可供您部署至 Azure 的簡單應用程式所需的一切動作。

部署應用程式將應用程式部署至 Azure
----------------------------------

1.  在 Visual Studio 的 **[方案總管]** 中以滑鼠右鍵按一下專案，再選取內容功能表中的 **[發行]**。

    ![專案內容功能表中的 [發行]](./media/web-sites-dotnet-get-started-vs2012/PublishVSSolution.png)

此時會開啟 **[發行 Web]** 精靈。

1.  在 **[Publish Web]** 精靈的 **[設定檔]** 索引標籤中，按一下 **[匯入]**。

    ![匯入發行設定](./media/web-sites-dotnet-get-started-vs2012/ImportPublishSettings.png)

**[匯入發行設定檔]** 對話方塊隨即出現。

1.  如果您之前尚未將 Azure 訂閱新增至 Visual Studio，請執行下列步驟。在以下步驟中，您將新增訂閱，以便讓 **[從 Azure 網站匯入]** 下方的下拉式清單納入您的網站。

    -   在 **[匯入發行設定檔]** 對話方塊中，按一下 **[從 Azure 網站匯入]**，再按 **[新增 Azure 訂閱]**。

    ![新增 Azure 訂閱](./media/web-sites-dotnet-get-started-vs2012/rzAddWAsub.png)

    -   在 **[匯入 Azure 訂閱]** 對話方塊中，按一下 **[下載訂閱檔案]**。

    ![下載訂閱檔案](./media/web-sites-dotnet-get-started-vs2012/rzDownLoadDownload.png)

    -   在您的瀏覽器視窗中，儲存 *.publishsettings* 檔案。

    ![下載 .publishsettings 檔案](./media/web-sites-dotnet-get-started-vs2012/rzDown2.png)

    -   在 **[匯入 Azure 訂閱]** 對話方塊中，按一下 **[瀏覽]** 並瀏覽至 *.publishsettings* 檔案。

    ![下載訂閱](./media/web-sites-dotnet-get-started-vs2012/rzDownLoadBrowse.png)

    -   按一下 **[匯入]**。

    ![匯入](./media/web-sites-dotnet-get-started-vs2012/rzImp.png)

    > [WACOM.NOTE] 
    > .publishsettings 檔案包含用來管理 Azure 訂閱和服務的認證 (未編碼)。這個檔案的安全性最佳作法是暫時儲存在來源目錄之外 (例如在 Libraries\\Documents 資料夾)，然後在匯入完成後予以刪除。惡意使用者若取得 .publishsettings 檔案的存取權，就可以編輯、建立和刪除您的 Azure 服務。

2.  在 **[匯入發行設定檔]** 對話方塊中，選取 **[從 Azure 網站匯入]**，接著從下拉式清單選取網站，再按一下 **[確定]**。

    ![匯入發行設定檔](./media/web-sites-dotnet-get-started-vs2012/ImportPublishProfile.png)

3.  在 **[連線]** 索引標籤中，按一下 **[驗證連線]** 以確定設定正確。

    ![驗證連線](./media/web-sites-dotnet-get-started-vs2012/ValidateConnection.png)

4.  當連線通過驗證後，**[驗證連線]** 按鈕旁邊會顯示綠色打勾記號。按 **[下一步]**。

    ![連線驗證成功](./media/web-sites-dotnet-get-started-vs2012/ValidateConnectionSuccess.png)

5.  在 **[設定]** 索引標籤中，取消勾選 **[Use this connection string at runtime]** 選項，因為此應用程式並未使用資料庫。對於此頁面的其他剩餘項目，您可以接受預設設定。您是要部署「發行」建置組態，且不需要刪除目的地伺服器上的檔案、預先編譯應用程式，或排除 App\_Data folder 中的檔案。按 **[下一步]**。

    ![[設定] 索引標籤](./media/web-sites-dotnet-get-started-vs2012/PublishWebSettingsTab.png)

6.  在 **[預覽]** 索引標籤中，按一下 **[開始預覽]**。

    ![[預覽] 索引標籤中的 [開始預覽] 按鈕](./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreview.png)

    此索引標籤會列出要複製至伺服器的檔案。顯示預覽並非要發行應用程式時的必要動作，但是是可以知道的有用功能。在此情況中，您不需要對顯示的檔案清單進行任何處理。

    ![開始預覽檔案輸出](./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreviewOutput.png)

7.  按一下 **[發行]**。Visual Studio 隨即開始進行將檔案複製至 Azure 伺服器的程序。

8.  **[輸出]** 視窗會顯示已採取的部署動作，並報告部署作業已順利完成。

    ![[輸出] 視窗報告部署成功](./media/web-sites-dotnet-get-started-vs2012/PublishOutput.png)

9.  部署成功時，即會自動在預設瀏覽器中開啟已部署之網站的 URL。您建立的應用程式現在正在雲端中執行。

    ![在 Azure 中執行的網站](./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png)

後續步驟後續步驟
----------------

在本教學課程中，您已了解如何將簡單的 Web 應用程式部署至 Azure 網站。我們也提供了其他資源，示範如何進行網站的管理、調整與疑難排解，如何新增資料庫、驗證與授權功能，以及如何決定您的應用程式是否應該在 Azure 雲端服務 (而非 Azure 網站) 中執行。

### 如何管理網站

當您不再使用網站時，您可以將之刪除，或是將其暫時離線或是變更網站設定。您可以直接從 Visual Studio 的 **[伺服器總管]** 中執行一些功能。

![[伺服器總管] 中的 Azure 網站](./media/web-sites-dotnet-get-started-vs2012/ServerExplorerWSSettings.png)

![Visual Studio 中的網站組態](./media/web-sites-dotnet-get-started-vs2012/WSConfigurationInVS.png)

若要刪除您的網站，您可以使用 Azure 管理入口網站。下列螢幕擷取畫面顯示管理入口網站中 **[儀表板]** 索引標籤裡的 **[停止]**、**[重新啟動]** 和 **[刪除]** 按鈕。

![管理入口網站的 [儀表板] 索引標籤](./media/web-sites-dotnet-get-started-vs2012/MPStopStartDelete.png)

您可以在 **[設定]** 索引標籤上變更網站設定。如需詳細資訊，請參閱[如何管理網站](/en-us/manage/services/web-sites/how-to-manage-websites/)。

### 如何調整網站

如果您的網站是公開網站，而且開始收到愈來愈多的流量，則回應時間可能會變慢。若要修補該問題，您可以在管理入口網站的 **[調整]** 索引標籤中輕鬆地新增伺服器資源。

![管理入口網站的 [調整] 索引標籤](./media/web-sites-dotnet-get-started-vs2012/MPScale.png)

如需詳細資訊，請參閱[如何調整網站](/en-us/manage/services/web-sites/how-to-scale-websites/)。(新增伺服器資源來調整網站並不是免費的。)

### 如何疑難排解網站

您可能會想要查看追蹤或記錄輸出，以便進行疑難排解。Visual Studio 提供內建工具，讓您能輕鬆檢視即時產生的 Azure 記錄。

![Visual Studio 中的記錄](./media/web-sites-dotnet-get-started-vs2012/LogsInVS.png)

如需詳細資訊，請參閱[在 Visual Studio 中疑難排解 Azure 網站](/en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)。

### 如何新增資料庫與授權功能

大部分生產網站都會使用資料庫，並限制部分網站功能只有特定授權使用者才能使用。如需觀看教學課程來了解如何開始使用資料庫存取、驗證和授權，請參閱[將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 應用程式部署至 Azure 網站](/en-us/develop/net/tutorials/web-site-with-sql-database/)。

### 如何決定您的應用程式是否應該在雲端服務中執行

在部分情況下，您可能會想要在 Azure 雲端服務 (而非 Azure 網站) 中執行應用程式。如需詳細資訊，請參閱 [Azure 執行模型](/en-us/develop/net/fundamentals/compute/)和 [Azure 網站、雲端服務和 VM：使用時機](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj218759.aspx)。如需觀看教學課程系列來了解如何建立多層式 ASP.NET Web 應用程式並將它部署至雲端服務，請參閱[使用儲存體資料表、佇列和 Blob 的 .NET 多層式應用程式](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/)。

