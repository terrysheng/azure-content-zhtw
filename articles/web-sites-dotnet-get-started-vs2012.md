<properties urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Get started with Azure Websites and ASP.NET" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra"></tags>

# 開始使用 Azure 網站和 ASP.NET

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/develop/net/tutorials/get-started/" title="Visual Studio 2013">Visual Studio 2013</a><a href="/en-us/develop/net/tutorials/get-started-vs2012/" title="Visual Studio 2012" class="current">Visual Studio 2012</a></div>

<div class="dev-callout"><strong>注意</strong><p>現已提供<a href="/en-us/develop/net/tutorials/get-started/">新版的教學課程</a>。如果您想要使用 Visual Studio 2012，仍舊可以遵循此版本的教學課程說明，但是可能缺少某些最新的 Azure SDK 功能說明。</p></div>

本教學課程示範如何使用 Visual Studio 2012 中或 Visual Studio 2012 for Web Express 中的 [Publish Web] 精靈，以將 ASP.NET Web 應用程式部署至 Azure 網站。您可以視個人偏好，使用 Visual Studio 2010 或 Visual Web Developer Express 2010 來遵循此教學課程步驟。

您可以免費申請 Azure 帳戶，而且如果您還沒有 Visual Studio 2012，SDK 會自動安裝 Visual Studio 2012 for Web Express。如此您就能開始免費進行 Azure 相關開發。

本教學課程假設您先前沒有使用 Azure 的經驗。完成此教學課程後，您將有個簡單的 Web 應用程式已在雲端中啟動並執行。

您將了解：

-   如何安裝 Azure SDK 好讓電腦適合用於進行 Azure 開發。
-   如何建立 Visual Studio ASP.NET MVC 4 專案，並將它發行至 Azure 網站。

下圖顯示完成的應用程式：

![網站範例][]

<div class="dev-callout"><p><strong>注意</strong>若要完成此教學課程，您需要 Azure 帳戶。如果您沒有這類帳戶，可以<a href="/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">啟用自己的 MSDN 訂戶權益</a>或是<a href="/en-us/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">申請免費試用</a>。</p></div>

### 教學課程區段

1.  [設定開發環境][]
2.  [在 Azure 中建立網站][]
3.  [建立 ASP.NET MVC 4 應用程式][]
4.  [將應用程式部署至 Azure][]
5.  [後續步驟][]

[WACOM.INCLUDE [install-sdk-2012-only][]]

## <a name="setupwindowsazure"></a><span class="short-header">建立網站</span>

以下步驟說明如何建立 Azure 網站。

1.  在 [Azure 管理入口網站][]中，按一下 [網站]，然後按一下 [新增]。

    ![新網站][]

1.  按一下 [快速建立]。

    ![Quick create][]

2.  在精靈的 [建立網站] 步驟中，於 [URL] 方塊輸入字串做為應用程式的唯一 URL。完整的 URL 將包含您在此輸入的字串，加上文字方塊旁邊顯示的尾碼。下圖顯示「範例 1」，但是如果有人已經用了該字串做為 URL，您必須重新輸入不同的值。

3.  在 [區域] 下拉式清單中，選擇最接近您的區域。
    此設定會指定您的網站將在哪個資料中心執行。

4.  按一下 [建立網站] 箭頭。

    ![Create a new web site][]

    [管理入口網站] 隨即返回 [網站] 頁面，且 [狀態] 欄會顯示正在建立網站。稍待片刻 (通常不到一分鐘)，[狀態] 欄就會顯示建立網站成功。在左側的瀏覽列中，您的儲存體中的網站數量會顯示在 [網站] 圖示旁邊。

    ![Web Sites page of Management Portal, web site created][]

## <a name="createmvc4app"></a><span class="short-header">建立應用程式</span>建立 ASP.NET MVC 4 應用程式

您已建立 Azure 網站，但網站中還沒有內容。接下來的步驟是建立要發行至 Azure 的 Visual Studio Web 應用程式。

### 建立專案

1.  啟動 Visual Studio 2012 或 Visual Studio 2012 for Web Express。

2.  從 [檔案] 功能表中，按一下 [新增]，再按 [專案]。

    ![New Project in File menu][]

1.  在 [新增專案] 對話方塊中，展開 [C\#] 並選取 [已安裝的範本] 下的 [Web]，再選取 [ASP.NET MVC 4 Web 應用程式]。

2.  確定已選取 [.NET Framework 4.5] 做為目標架構。

3.  將應用程式命名為 **MyExample**，再按一下 [確定]。

    ![New Project dialog box][]

1.  在 [New ASP.NET MVC 4 Project] 對話方塊中，選取 [網際網路應用程式] 範本，再按一下 [確定]。

    ![New ASP.NET MVC 4 Project dialog box][]

### 在本機執行應用程式

1.  按 **CTRL**+**F5** 執行該應用程式。應用程式首頁隨即出現在預設瀏覽器中。

    ![Web site running locally][]

以上就是要建立可供您部署至 Azure 的簡單應用程式所需的一切動作。

## <a name="deploytowindowsazure"></a><span class="short-header">部署應用程式</span>將應用程式部署至 Azure

1.  在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下專案，再選取內容功能表中的 [發行]。

    ![Publish in project context menu][]

    此時會開啟 [發行 Web] 精靈。

1.  在 [Publish Web] 精靈的 [設定檔] 索引標籤中，按一下 [匯入]。

    ![Import publish settings][]

[匯入發行設定檔] 對話方塊隨即出現。

1.  如果您之前尚未將 Azure 訂閱新增至 Visual Studio，請執行下列步驟。在以下步驟中，您將新增訂閱，以便讓 [從 Azure 網站匯入] 下方的下拉式清單納入您的網站。

    -   在 [匯入發行設定檔] 對話方塊中，按一下 [從 Azure 網站匯入]，再按 [新增 Azure 訂閱]。

    ![add Azure subscription][]

    -   在 [匯入 Azure 訂閱] 對話方塊中，按一下 [下載訂閱檔案]。

    ![download subscription file][]

    -   在您的瀏覽器視窗中，儲存 *.publishsettings* 檔案。

    ![download .publishsettings file][]

    -   在 [匯入 Azure 訂閱] 對話方塊中，按一下 [瀏覽] 並瀏覽至 *.publishsettings* 檔案。

    ![download sub][]

    -   按一下 [匯入]。

    ![import][]

    > [WACOM.NOTE]
    > .publishsettings 檔案包含用來管理 Azure 訂閱和服務的認證 (未編碼)。這個檔案的安全性最佳作法是暫時儲存在來源目錄之外 (例如在 Libraries\\Documents 資料夾)，然後在匯入完成後予以刪除。惡意使用者若取得 .publishsettings 檔案的存取權，就可以編輯、建立和刪除您的 Azure 服務。

2.  在 [匯入發行設定檔] 對話方塊中，選取 [從 Azure 網站匯入]，接著從下拉式清單選取網站，再按一下 [確定]。

    ![Import Publish Profile][]

3.  在 [連線] 索引標籤中，按一下 [驗證連線] 以確定設定正確。

    ![Validate connection][]

4.  當連線通過驗證後，[驗證連線] 按鈕旁邊會顯示綠色核取方塊。按 [下一步]。

    ![Successfully validated connection][]

5.  在 [設定] 索引標籤中，取消勾選 [Use this connection string at runtime] 選項，因為此應用程式並未使用資料庫。對於此頁面的其他剩餘項目，您可以接受預設設定。您是要部署「發行」建置組態，且不需要刪除目的地伺服器上的檔案、預先編譯應用程式，或排除 App\_Data folder 中的檔案。
按 [下一步]。

    ![Settings tab][]

6.  在 [預覽] 索引標籤中，按一下 [開始預覽]。

    ![StartPreview button in the Preview tab][]

    此索引標籤會列出要複製至伺服器的檔案。顯示預覽並非要發行應用程式時的必要動作，但是是可以知道的有用功能。在此情況中，您不需要對顯示的檔案清單進行任何處理。

    ![StartPreview file output][]

7.  按一下 [發行]。Visual Studio 隨即開始進行將檔案複製至 Azure 伺服器的程序。

8.  [輸出] 視窗會顯示已採取的部署動作，並報告部署作業已順利完成。

    ![Output window reporting successful deployment][]

9.  部署成功時，即會自動在預設瀏覽器中開啟已部署之網站的 URL。
    您建立的應用程式現在正在雲端中執行。

    ![Web site running in Azure][網站範例]

## <a name="nextsteps"></a><span class="short-header">後續步驟</span>後續步驟

在本教學課程中，您已了解如何將簡單的 Web 應用程式部署至 Azure 網站。我們也提供了其他資源，說明如何進行網站的管理、調整與疑難排解，如何新增資料庫、驗證與授權功能，以及如何決定您的應用程式是否應該在 Azure 雲端服務 (而非 Azure 網站) 中執行。

### 如何管理網站

當您不再使用網站時，您可以將之刪除，或是將其暫時離線或是變更網站設定。您可以直接從 Visual Studio 的 [伺服器總管] 中執行一些功能。

![Azure Web Sites in Server Explorer][]

![Web Site Configuration in Visual Studio][]

若要刪除您的網站，您可以使用 Azure 管理入口網站。下列螢幕擷取畫面顯示管理入口網站中 [儀表板] 索引標籤裡的 [停止]、[重新啟動] 和 [刪除] 按鈕。

![Management Portal Dashboard Tab][]

您可以在 **[設定]** 索引標籤上變更網站設定。如需詳細資訊，請參閱[如何管理網站][]。

### 如何調整網站

如果您的網站是公開網站，而且開始收到愈來愈多的流量，則回應時間可能會變慢。若要修補該問題，您可以在管理入口網站的 [調整] 索引標籤中輕鬆地新增伺服器資源。

![Management Portal Scale Tab][]

如需詳細資訊，請參閱[如何調整網站][]。(新增伺服器資源來調整網站並不是免費的。)

### 如何進行網站的疑難排解

您可能會想要查看追蹤或記錄輸出，以便進行疑難排解。Visual Studio 提供內建工具，讓您能輕鬆檢視即時產生的 Azure 記錄。

![Visual Studio 中的記錄][]

如需詳細資訊，請參閱[在 Visual Studio 中疑難排解 Azure 網站][]。

### 如何新增資料庫與授權功能

大部分生產網站都會使用資料庫，並限制部分網站功能只有特定授權使用者才能使用。如需觀看教學課程來了解如何開始使用資料庫存取、驗證和授權，請參閱[將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 應用程式部署至 Azure 網站][]。

### 如何決定您的應用程式是否應該在雲端服務中執行

在某些情況下，您可能會想要在 Azure 雲端服務 (而非 Azure 網站) 中執行應用程式。如需詳細資訊，請參閱 [Azure 執行模型][]和 [Azure 網站、雲端服務與虛擬機器之比較][]。如需觀看教學課程系列來了解如何建立多層式 ASP.NET Web 應用程式並將它部署至雲端服務，請參閱[使用儲存體資料表、佇列和 Blob 的 .NET 多層式應用程式][]。

  [Visual Studio 2013]: /en-us/develop/net/tutorials/get-started/ "Visual Studio 2013"
  [Visual Studio 2012]: /en-us/develop/net/tutorials/get-started-vs2012/ "Visual Studio 2012"
  [新版的教學課程]: /en-us/develop/net/tutorials/get-started/
  [網站範例]: ./media/web-sites-dotnet-get-started-vs2012/DeployedWebSite.png
  [啟用自己的 MSDN 訂戶權益]: /en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [申請免費試用]: /en-us/pricing/free-trial/?WT.mc_id=A261C142F
  [設定開發環境]: #setupdevenv
  [在 Azure 中建立網站]: #setupwindowsazure
  [建立 ASP.NET MVC 4 應用程式]: #createmvc4app
  [將應用程式部署至 Azure]: #deploytowindowsazure
  [後續步驟]: #nextsteps
  [install-sdk-2012-only]: ../includes/install-sdk-2012-only.md
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [新網站]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteNew.png
  [Quick create]: ./media/web-sites-dotnet-get-started-vs2012/ClickQuickCreate.png
  [Create a new web site]: ./media/web-sites-dotnet-get-started-vs2012/CreateWebsite.png
  [Web Sites page of Management Portal, web site created]: ./media/web-sites-dotnet-get-started-vs2012/WebSiteStatusRunning.png
  [New Project in File menu]: ./media/web-sites-dotnet-get-started-vs2012/NewVSProject.png
  [New Project dialog box]: ./media/web-sites-dotnet-get-started-vs2012/NewMVC4WebApp.png
  [New ASP.NET MVC 4 Project dialog box]: ./media/web-sites-dotnet-get-started-vs2012/InternetAppTemplate.png
  [Web site running locally]: ./media/web-sites-dotnet-get-started-vs2012/AppRunningLocally.png
  [Publish in project context menu]: ./media/web-sites-dotnet-get-started-vs2012/PublishVSSolution.png
  [Import publish settings]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishSettings.png
  [add Azure subscription]: ./media/web-sites-dotnet-get-started-vs2012/rzAddWAsub.png
  [download subscription file]: ./media/web-sites-dotnet-get-started-vs2012/rzDownLoadDownload.png
  [download .publishsettings file]: ./media/web-sites-dotnet-get-started-vs2012/rzDown2.png
  [download sub]: ./media/web-sites-dotnet-get-started-vs2012/rzDownLoadBrowse.png
  [import]: ./media/web-sites-dotnet-get-started-vs2012/rzImp.png
  [Import Publish Profile]: ./media/web-sites-dotnet-get-started-vs2012/ImportPublishProfile.png
  [Validate connection]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnection.png
  [Successfully validated connection]: ./media/web-sites-dotnet-get-started-vs2012/ValidateConnectionSuccess.png
  [Settings tab]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebSettingsTab.png
  [StartPreview button in the Preview tab]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreview.png
  [StartPreview file output]: ./media/web-sites-dotnet-get-started-vs2012/PublishWebStartPreviewOutput.png
  [Output window reporting successful deployment]: ./media/web-sites-dotnet-get-started-vs2012/PublishOutput.png
  [Azure Web Sites in Server Explorer]: ./media/web-sites-dotnet-get-started-vs2012/ServerExplorerWSSettings.png
  [Web Site Configuration in Visual Studio]: ./media/web-sites-dotnet-get-started-vs2012/WSConfigurationInVS.png
  [Management Portal Dashboard Tab]: ./media/web-sites-dotnet-get-started-vs2012/MPStopStartDelete.png
  [如何管理網站]: /en-us/manage/services/web-sites/how-to-manage-websites/
  [Management Portal Scale Tab]: ./media/web-sites-dotnet-get-started-vs2012/MPScale.png
  [如何調整網站]: /en-us/manage/services/web-sites/how-to-scale-websites/
  [Visual Studio 中的記錄]: ./media/web-sites-dotnet-get-started-vs2012/LogsInVS.png
  [在 Visual Studio 中疑難排解 Azure 網站]: /en-us/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 應用程式部署至 Azure 網站]: /en-us/develop/net/tutorials/web-site-with-sql-database/
  [Azure 執行模型]: /en-us/develop/net/fundamentals/compute/
  [Azure 網站、雲端服務與虛擬機器之比較]: http://azure.microsoft.com/zh-tw/documentation/articles/choose-web-site-cloud-service-vm/
  [使用儲存體資料表、佇列和 Blob 的 .NET 多層式應用程式]: /en-us/develop/net/tutorials/multi-tier-web-site/1-overview/
