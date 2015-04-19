<properties 
	pageTitle="開始使用 ASP.NET 的 Azure 網站" 
	description="本教學課程說明如何在 Visual Studio 2013 中建立 ASP.NET Web 專案，並將它部署到 Azure 網站。在 15 分鐘內，您便會有在雲端中已啟動並執行的應用程式。" 
	services="web-sites" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="11/12/2014" 
	ms.author="tdykstra"/>

# 開始使用 Azure 網站和 ASP.NET

本教學課程說明如何使用 Visual Studio 2013 中或 Visual Studio 2013 for Web Express 精靈，以建立 ASP.NET Web 應用程式並將其部署至 Azure 網站。本教學課程假設您先前沒有使用 Azure 或 ASP.NET 的經驗。完成此教學課程後，您將有個簡單的 Web 應用程式已在雲端中啟動並執行。

您將學到：

* 如何安裝 Azure SDK 好讓電腦適合用於進行 Azure 開發。
* 如何建立 Visual Studio ASP.NET Web 專案，並將該專案部署至 Azure 網站。
* 如何對 Web 專案進行變更，並重新部署應用程式。
* 說明如何使用 Azure 管理入口網站監控及管理您的網站。

要完成此教學課程，您必須要有 Azure 帳戶：

* 您可以[免費申請 Azure 帳戶](/zh-tw/pricing/free-trial/?WT.mc_id=A261C142F) - 您將取得可試用付費 Azure 服務的額度，且即使在額度用完後，您仍可保留該帳戶，並使用免費的 Azure 服務，例如「網站」。
* 您可以[啟用 MSDN 訂閱者權益](/zh-tw/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - 您的 MSDN 訂閱每月會提供您額度，讓您可用於 Azure 付費服務。</li>

> [AZURE.NOTE]
> 如果您要在註冊帳戶前開始使用 Azure 網站，請移至 <a href="https://trywebsites.azurewebsites.net">https://trywebsites.azurewebsites.net</a>，您可以在 Azure 網站中立即建立短期的免費 ASP.NET 簡易版網站。

下圖顯示完成的應用程式：

![Web site home page](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

### 教學課程區段

* [註冊 Microsoft Azure (影片)](#video)
* [設定開發環境](#set-up-the-development-environment)
* [在 Visual Studio 中建立 ASP.NET Web 應用程式](#create-an-aspnet-web-application)
* [將應用程式部署至 Azure](#deploy-the-application-to-azure)
* [進行變更並重新部署](#make-a-change-and-redeploy)
* [在管理入口網站中監控及管理網站](#monitor-and-manage-the-site-in-the-management-portal)
* [後續步驟](#next-steps)

##<a name="video"></a>註冊 Microsoft Azure (影片)

在此影片中，Scott Hanselman 介紹註冊免費試用的 Microsoft Azure 有多容易。(持續時間：1:58)

> [AZURE.VIDEO sign-up-for-microsoft-azure]

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## 建立 ASP.NET Web 應用程式

您的第一個步驟是要建立 Web 應用程式專案。Visual Studio 將自動建立 Azure 網站，您稍後會將專案部署至此。下圖說明這兩個步驟的作用。

![Diagram showing project creation and deployment steps](./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png)

1. 開啟 Visual Studio 2013 或 Visual Studio 2013 Express for Web。

2. 從 [檔案]**** 功能表，按一下 [新增專案]****。

3. 在 [新增專案]**** 對話方塊中，按一下 [C#]**** > [Web]**** > [ASP.NET Web 應用程式]****。如有需要，您可以選擇 [Visual Basic]****。

3. 確定已選取 [.NET Framework 4.5]**** 作為目標架構。

4. 將應用程式命名為 **MyExample**，然後按一下 [確定]****。

	![New Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

5. 在 [新增 ASP.NET 專案]**** 對話方塊中，選取 [MVC]**** 範本。如果您想使用 ASP.NET Web Form，您可以選取 [Web Forms]**** 範本。 

	[MVC 和 Web Form](http://www.asp.net/get-started/websites) 是用來開發網站的 ASP.NET 架構。在本教學課程中，您可以任選其中之一，但如果您選擇 Web Form，將必須在後續教學課程指示您編輯 *Index.cshtml* 時編輯 *Default.aspx*。

7. 按一下 [變更驗證]****。 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

6. 在 [變更驗證]**** 對話方塊中，按一下 [不需要驗證]****，然後按一下 [確定]****。

	![No Authentication](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	您建立的範例應用程式將不會讓使用者登入。[後續步驟](#next-steps) 實作驗證和授權之教學課程的區段連結。

5. 在 [新增 ASP.NET 專案]**** 對話方塊中，保留 [Azure]**** 下的設定，然後按一下 [確定]****。 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

	預設設定會指定 Visual Studio 將為您的 Web 專案建立 Azure 網站。在教學課程的下一節中，您會將 Web 專案部署至新建立的網站。

	(核取方塊的標題可能是 [雲端中的主機]**** 或 [建立遠端資源]****。無論是哪一個，效果都是相同的。)
	
5. 如果您尚未登入 Azure，Visual Studio 會提示您登入。按一下 [登入]****。

	![Sign in to Azure](./media/web-sites-dotnet-get-started-vs2013/signin.png)

6. 在 [登入 Azure]**** 對話方塊中，輸入您用來管理 Azure 訂閱之帳戶的識別碼和密碼。
	
	當您登入時，[設定 Azure 網站設定]**** 對話方塊會詢問您要建立哪些資源。

	![Signed in to Azure](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

3. Visual Studio 會提供預設的**網站名稱**，Azure 會將其做為應用程式 URL 的前置詞。如果需要，您可以輸入不同的網站名稱。

	完整的 URL 將包含您在此處輸入的內容，並加上 *.azurewebsites.net* (如 [網站名稱]**** 文字方塊旁所示)。例如，如果網站名稱是 `MyExample6442`, the URL will be `MyExample6442.azurewebsites.net`。URL 必須是唯一的。如果其他人已使用您輸入的 URL，您會在右邊看到紅色驚嘆號，而非綠色核取記號，這代表您需要輸入不同的網站名稱。

4. 在 [區域]**** 下拉式清單中，選擇最接近您的位置。

	此設定指定您的網站將執行所在的 Azure 資料中心。您可以在此教學課程中選取任何區域，這不會造成顯著的差異，但若是實際執行的網站，您的 Web 伺服器就應盡可能靠近存取您網站的瀏覽器，以將[延遲](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)降到最低。

5. 保留資料庫欄位不變。

	在本教學課程中，您不會使用資料庫。本教學課程最後的[後續步驟](#next-steps) 小節會提供示範如何使用資料庫的教學課程連結。

6. 按一下 [確定]****。

	在數秒後，Visual Studio 會在您指定的資料夾中建立 Web 專案，並在您指定的 Azure 區域中建立網站。  

	[方案總管]**** 視窗會顯示新專案中的檔案和資料夾。

	![Solution Explorer](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)

	[Web 發行活動]**** 視窗會顯示已建立網站。

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)

	而且您可以在 [伺服器總管] 中檢視網站。

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/siteinse.png)



## 將應用程式部署至 Azure

7. 在 [Web 發行活動]**** 視窗中，按一下 [立即將 MyExample 發行至這個網站]****。

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)

	[發行 Web]**** 精靈會在數秒後出現。 

	Visual Studio 要將您的專案部署至 Azure 所需的設定，已儲存在 *publish profile* 中。精靈可讓您檢查及變更這些設定。

8. 在 [發行 Web]**** 精靈的 [連線]**** 索引標籤中，按一下 [驗證連線]**** 確定 Visual Studio 可以連線至 Azure，以部署 Web 專案。

	![Validate connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)

	當連線通過驗證後，[驗證連線]**** 按鈕旁邊會顯示綠色核取方塊。 

9. 按 [下一步]****。

	![Successfully validated connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

10. 在 [設定]**** 索引標籤中，按 [下一步]****。

	![Settings tab](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	您可以接受 [組態]**** 和 [檔案發行選項]**** 的預設值。

	[組態]**** 下拉式清單可讓您部署用於遠端偵錯的偵錯組建。[後續步驟](#next-steps) 小節會連結至說明如何從遠端以偵錯模式執行 Visual Studio 的教學課程。

	如果您展開 [檔案發行選項]****，就會看見數個可讓您處理不適用於此教學課程之案例的設定：
 
	* 在目的地移除多餘的檔案。
	  
		在伺服器上刪除任何不在您專案中的檔案。如果您將專案部署至先前曾經部署不同專案的網站，就可能需要執行此動作。

	* 在發行期間預先編譯。 
	 
		可為大型網站縮短第一個要求的預備時間。

	* 從 App_Data 資料夾中排除檔案。 
	 
		進行測試時，您的 App_Data 中有時會有您不想部署至實際執行環境的 SQL Server 資料庫檔案。
	
11. 在 [預覽]**** 索引標籤中，按一下 [開始預覽]****。

	![StartPreview button in the Preview tab](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)

	此索引標籤會列出要複製至伺服器的檔案。顯示預覽並非要發行應用程式時的必要動作，但是是可以知道的有用功能。

12. 按一下 [發行]****。

	![StartPreview file output](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)

	Visual Studio 隨即開始進行將檔案複製至 Azure 伺服器的程序。

	[輸出]**** 和 [Web 發行活動]**** 視窗會顯示已採取的部署動作，並報告部署作業已順利完成。

	![Output window reporting successful deployment](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)

	部署成功時，即會自動在預設瀏覽器中開啟已部署之網站的 URL，而
	您建立的應用程式目前正在雲端中執行。瀏覽器網址列中的 URL 可顯示網站是從網際網路載入。

	![Web site running in Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

13. 關閉瀏覽器。

## 進行變更並重新部署

在教學課程的這一節中，您會變更首頁的 **h1** 標題、在開發電腦中本機執行專案以驗證變更，然後將變更部署至 Azure。

2. 在 [方案總管]**** 中開啟 *Views/Home/Index.cshtml* 或 *.vbhtml* 檔案、將 **h1** 標題從 "ASP.NET" 變更為 "ASP.NET and Azure"，然後儲存檔案。 

	![MVC index.cshtml](./media/web-sites-dotnet-get-started-vs2013/index.png)

	![MVC h1 change](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

1. 在您的本機電腦上執行網站，然後按 CTRL+F5 檢視更新的標題。

	![Web site running locally](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)

	 `http://localhost` URL 顯示它正在您的本機電腦上執行。根據預設，它會在 IIS Express 中執行，其為 IIS 的輕量型版本，專為在 Web 應用程式開發期間使用而設計。


1. 關閉瀏覽器。

1. 在 [方案總管]**** 中以滑鼠右鍵按一下專案，然後選擇 [發行]****。

	![Chooose Publish](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)

	[發行 Web]**** 精靈的 [預覽] 索引標籤隨即出現。以往如果需要變更任何發行設定，您會選擇不同的索引標籤，但現在您只需要使用相同的設定來重新部署。

2. 在 [發行 Web]**** 精靈中，按一下 [發行]****。

	![Click Publish](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)

	Visual Studio 會將專案部署至 Azure，並在預設瀏覽器中開啟網站。

	![Changed site deployed](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

**提示：**您可以啟用 [Web 單鍵發行]**** 工具列，以加快部署速度。按一下 [檢視]**** > [工具列]****，然後選取 [Web 單鍵發行]****此工具列可讓您選取設定檔、按一下按鈕進行發行，或按一下按鈕來開啟 [發行 Web]**** 精靈。

![Web One Click Publish Toolbar](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

## 在管理入口網站中監控及管理網站

[Azure 管理入口網站](/zh-tw/services/management-portal/)是一個 Web 介面，讓您用來管理及監控 Azure 服務 (例如，您剛建立的網站)。在教學課程的這一節中，您會了解可在入口網站中執行的一些作業。

1. 在您的瀏覽器中，移至 [http://manage.windowsazure.com](),，然後以您的 Azure 認證登入。

	入口網站會顯示您的 Azure 服務清單。

2. 按一下您的網站名稱。

	![Portal home page with new web site called out](./media/web-sites-dotnet-get-started-vs2013/portalhome.png)
  
3. 按一下 [儀表板]**** 索引標籤。

	[儀表板]**** 索引標籤會顯示使用量統計資料的概觀，以及多個常用網站管理功能的連結。在 [快速概覽]**** 下方，您會看見您應用程式首頁的連結。

	![Portal web site dashboard tab](./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png)
  
	此時您的網站並沒有太多流量，且在圖形中可能不會顯示任何項目。如果您瀏覽至您的應用程式，請重新整理頁面數次，然後重新整理入口網站的 [儀表板]**** 頁面，您就會看見一些統計資料。如需詳細資訊，您可以按一下 [監視]**** 索引標籤。

4. 按一下 [設定]**** 索引標籤。

	[[設定](/zh-tw/documentation/articles/web-sites-configure//)] 索引標籤可讓您控制用於網站的 .NET 版本、啟用 [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) 和[診斷記錄](web-sites-enable-diagnostic-log.md)等功能，以及設定 [connection string values](/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) 等等。 

	![Portal web site configure tab](./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png)
  
5. 按一下 [調整]**** 索引標籤。

	針對網站服務的付費層，[[調整](web-sites-scale.md)] 索引標籤可讓您控制為您的 Web 應用程式提供服務之機器的大小和數量，以因應流量的變動。

	您可以手動調整或設定自動調整的準則或排程。

	![Portal website scale tab](./media/web-sites-dotnet-get-started-vs2013/portalscale.png)

這些只是管理入口網站的部分功能。您也可以建立新網站、刪除現有網站、停止及重新啟動網站，以及管理其他類型的 Azure 服務，例如資料庫和虛擬機器。  

**提示：**預覽版提供新的管理入口網站，最終將會取代您到目前為止使用的入口網站。如需詳細資訊，請參閱 [Azure 預覽入口網站](/zh-tw/overview/preview-portal/).

## 後續步驟

在本教學課程中，您已了解如何建立簡易的 Web 應用程式，並將其部署至 Azure 網站。以下是有助於您深入了解的一些相關主題和資源。

* 部署 Web 專案的其他方式

	在此教學課程中，您看到在一項作業中建立網站並部署網站的最快速方式。如需概略了解使用 Visual Studio 或從[來源控制系統](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)[自動部署](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)的其他部署方式，請參閱[如何部署 Azure 網站](/zh-tw/documentation/articles/web-sites-deploy/")。 

	Visual Studio 也可產生能夠讓您將部署自動化的 Windows PowerShell 指令碼。如需詳細資訊，請參閱[將每個動作自動化 (使用 Azure 建置真實世界的雲端應用程式)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)。

* 如何在 Visual Studio 中管理網站

	如需可在 [伺服器總管]**** 中執行之網站管理功能的相關資訊，請參閱[在 Visual Studio 中疑難排解 Azure 網站](/zh-tw/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)。

* 如何進行網站的疑難排解

	Visual Studio 提供的功能，讓您能輕鬆檢視即時產生的 Azure 記錄。您也可以在 Azure 中遠端執行偵錯模式。如需詳細資訊，請參閱[在 Visual Studio 中疑難排解 Azure 網站](/zh-tw/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)。

* 如何新增資料庫與授權功能

	如需觀看教學課程來了解如何存取資料庫以及將某些網站功能限定給授權使用者，請參閱[將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 應用程式部署至 Azure 網站](/zh-tw/develop/net/tutorials/web-site-with-sql-database/).

* 如何新增自訂網域名稱和 SSL

	如需如何使用 SSL 和您自己的網域 (例如 www.contoso.com，而非 contoso.azurewebsites.net) 的相關資訊，請參閱下列資源：

	* [設定 Azure 網站的自訂網域名稱](web-sites-custom-domain-name.md). 
	* [對 Azure 網站啟用 HTTPS](http://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/)

* 如何避免閒置逾時之後的喚醒等待時間 

	根據預設，網站如果閒置一段時間，就會卸載。其後的第一個要求必須等待網站重新載入。若要避免這種等待時間，您可以啟用 AlwaysOn 功能。如需詳細資訊，請參閱[如何設定網站](http://azure.microsoft.com/documentation/articles/web-sites-configure/)中的組態選項。

* 如何新增即時功能，例如交談

	如果您的網站將包含即時功能 (例如交談服務、遊戲、股市行情等)，您可以使用 [ASP.NET SignalR](http://www.asp.net/signalr) 與 [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) 傳輸方法獲得最佳效能。如需詳細資訊，請參閱[在 Windows Azure 網站中使用 SignalR](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites)。 

* 如何從 Azure 網站、雲端服務和 VM 中擇一使用於 Web 應用程式

	在 Azure 中，您可以在「網站」中執行 Web 應用程式 (如本教學課程所說明)，或在「雲端服務」或「虛擬機器」中執行。如需詳細資訊，請參閱 [Azure 執行模型](/zh-tw/develop/net/fundamentals/compute/)和 [Azure 網站、雲端服務與虛擬機器：使用時機?](/zh-tw/manage/services/web-sites/choose-web-app-service/)。


<!--HONumber=42-->
