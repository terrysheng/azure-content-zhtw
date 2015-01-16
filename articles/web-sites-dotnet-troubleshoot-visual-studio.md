<properties title="Troubleshooting Azure Websites in Visual Studio" pageTitle="疑難排解 Visual Studio 中的 Azure 網站 " metaKeywords="疑難排解偵錯 azure 網站追蹤記錄" description="了解如何使用 Visual Studio 2013 內建的遠端偵錯、追蹤和記錄工具進行 Azure 網站的疑難排解。" metaCanonical="" services="web-sites" documentationCenter=".NET" authors="tdykstra" manager="wpickett" solutions="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/13/2014" ms.author="tdykstra" />

# 疑難排解 Visual Studio 中的 Azure 網站

本教學課程將示範如何使用 Visual Studio 工具，來協助應用程式在 Azure 網站中執行時進行偵錯，方法是藉由在 [偵錯模式](http://www.visualstudio.com/zh-tw/get-started/debug-your-app-vs.aspx) 中遠端執行，或檢視應用程式與 Web 伺服器記錄。

您將了解：

* Visual Studio 中所提供的 Azure 網站管理功能有哪些。
* 如何使用 Visual Studio 遠端檢視對遠端網站進行快速變更更。
* 如何在 Azure 中還在執行專案時，從遠端執行偵測模式。
* 如何建立應用程式追蹤記錄，並在應用程式建立記錄時加以檢視。
* 如何檢視 Web 伺服器記錄，包括詳細的錯誤訊息與失敗的要求追蹤。
* 如何將診斷記錄傳送至 Azure 儲存體帳戶並在該處檢視。

如果您有 Visual Studio Ultimate，您也可以使用 [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) 進行偵錯。本教學課程未涵蓋 IntelliTrace。

### 教學課程區段

- [必要條件](#prerequisites)
- [網站組態與管理](#sitemanagement)
- [在 [伺服器總管] 中存取網站檔案](#remoteview)
- [遠端偵錯](#remotedebug)
	- 網站的遠端偵錯
	- WebJobs 的遠端偵錯
	- 遠端偵錯注意事項 
- [診斷記錄概觀](#logsoverview)
- [建立並檢視應用程式追蹤記錄](#apptracelogs)
- [檢視 Web 伺服器記錄](#webserverlogs)
- [檢視詳細的錯誤訊息記錄](#detailederrorlogs)
- [下載檔案系統記錄](#downloadlogs)
- [檢視儲存體記錄](#storagelogs)
- [檢視失敗要求記錄](#failedrequestlogs)
- [後續步驟](#nextsteps)

<h2><a name="prerequisites"></a>必要條件</h2>

本教學課程可運用於開發環境、Web 專案與您在 [開始使用 Azure 和 ASP.NET][GetStarted] 中所設定的 Azure 網站。在 WebJobs 區段中，您將會用到您在 [開始使用 Azure WebJobs SDK][GetStartedWJ] 中所建立的應用程式。

本教學課程中所提供的程式碼範例適用於 C# MVC Web 應用程式，但是疑難排解程序則是與 Visual Basic 和 Web Form 應用程式一樣。

遠端偵錯需要 Visual Studio 2013 或 Visual Studio 2012 (含 Update 4)。WebJobs 的遠端偵錯和**伺服器總管**功能需要 [Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkID=510314) 或更新版本。本教學課程中所示的其他功能也適用於 Visual Studio 2013 Express for Web 和 Visual Studio 2012 Express for Web。 

串流記錄功能僅適用於鎖定 .NET Framework 4 或更新版本的應用程式。

<h2><a name="sitemanagement"></a>網站組態與管理</h2>

Visual Studio 可讓您存取管理入口網站中可用的網站管理功能與組態設定功能的子集。本節將說明可用的項目。

1. 如果您尚未在 Visual Studio 中登入 Azure，請按一下 [**伺服器總管**] 中的 [**連線到 Azure**] 按鈕。

替代方式為安裝可讓您存取帳戶的管理憑證。管理憑證可讓 [**伺服器總管**] 得以存取其他的 Azure 服務 (SQL 資料庫與行動服務)。如果您選擇安裝憑證，請以滑鼠右鍵按一下 [**伺服器總管**] 中的 [**Azure**] 節點，然後按一下內容功能表中的 [**管理訂用帳戶**]。在 [**管理 Azure 訂用帳戶**] 對話方塊中，按一下 [**憑證**] 索引標籤，再按一下 [**匯入**]。請依照指示下載，然後匯入您 Azure 帳戶的訂用帳戶檔案 (亦稱為 *.publishsettings* 檔案)。

	> [WACOM.NOTE]
如果您下載了訂用帳戶檔案，請將其儲存到原始程式碼目錄以外的資料夾 (例如在 Downloads 資料夾)，然後在匯入完成後刪除該檔案。惡意使用者一旦能夠存取此訂用帳戶檔案，就能夠編輯、建立和刪除您的 Azure 服務。

如需從 Visual Studio 連線至 Azure 資源的詳細資訊，請參閱 [管理帳戶、訂用帳戶和系統管理角色](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert)。

2. 在 [**伺服器總管**] 中展開 **Azure**，然後再展開 [**網站**]。

3. 以滑鼠右鍵按一下您在 [開始使用 Azure 和 ASP.NET][GetStarted] 中建立的網站節點，然後按一下 [**檢視設定**]。

	![View Settings in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

[**Azure 網站**] 索引標籤會隨即顯示，您會看到 Visual Studio 中所提供的網站管理與組態工作。

	![Azure Website window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

在本教學課程中，您將使用記錄與追蹤下拉式清單。	您也會使用遠端偵錯功能，但是將以不同的方式來加以啟用。
   
如需此視窗中的應用程式設定與連接字串方塊的詳細資訊，請參閱 [Azure 網站：應用程式字串與連接字串的運作方式](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx) (英文)。

如果您想要執行無法在此視窗中完成的網站管理工作，可以按一下 [**完整網站設定**] 來開啟管理入口網站的瀏覽器視窗。如需詳細資訊，請參閱 [如何設定網站](/zh-tw/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig)。

<h2><a name="remoteview"></a>在 [伺服器總管] 中存取網站檔案</h2>

部署網站時，通常會將 Web.config 檔案中的 `customErrors` 旗標設定為 `On` 或 `RemoteOnly`，這表示出現問題時，您將不會收到有用的錯誤訊息。對許多錯誤而言，您只會看到如下列之一的頁面。

**'/' 應用程式中有伺服器錯誤：**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**發生錯誤：**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**網站無法顯示頁面**

![Unhelpful error page](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

要找到錯誤原因最簡單的方式，往往就是啟用詳細的錯誤訊息，而以上第一個螢幕擷取畫面說明的是其做法。該做法需要在部署的 Web.config 檔案中進行變更。您可以編輯專案中的 *Web.config* 檔案，然後重新部署專案，或是建立一個 [Web.config 轉換](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations)，並部署偵錯組建，但是以下方式更快：在 [**方案總管**] 中，您可以使用 [*遠端檢視*] 功能，直接檢視與編輯遠端網站上的檔案。

1. 在 [**伺服器總管**] 中，依序展開 [**Azure**] 和 [**網站**]，然後展開您要部署之網站的節點。

	您會看到可供您存取網站內容檔案與記錄檔的節點。

	![File and log files](./media/web-sites-dotnet-troubleshoot-visual-studio/fileandlogfiles.png)

2. 展開 [**檔案**] 節點，然後按兩下 *Web.config* 檔案。

	![Open Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

Visual Studio 會從遠端網站開啟 Web.config 檔案，然後在標題列中於檔案名稱旁邊顯示 [[遠端]]。

3. 將下列程式碼行新增至 `system.web` 元素：

	`<customErrors mode="off"></customErrors>`

	![Edit Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4. 重新整理顯示沒有幫助的錯誤訊息的瀏覽器，現在您就會看到詳細的錯誤訊息，例如以下範例：

	![Detailed error messaeg](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

	(透過在 *Views\Home\Index.cshtml* 中加入紅色字行來建立顯示的錯誤。)

能夠讀取與編輯您的 Azure 網站上的檔案，使得疑難排解作業更為輕鬆，編輯 Web.config 檔案僅僅是其中一個範例案例。

<h2><a name="remotedebug"></a>遠端偵錯</h2>

如果詳細的錯誤訊息無法提供足夠的資訊，而且您無法在本機重現錯誤，另一個疑難排解的方式則是在遠端於偵錯模式中執行。您可以設定中斷點、直接操作記憶體、逐步執行程式碼，甚至變更程式碼路徑。 

遠端偵錯無法在 Visual Studio 的 Express 版本中運作。

### 網站的遠端偵錯

本節說明如何使用您在 [開始使用 Azure 和 ASP.NET][GetStarted] 中所建立的專案進行遠端偵錯。

1. 開啟您在 [開始使用 Azure 和 ASP.NET][GetStarted] 中建立的 Web 專案。

1. 開啟 *Controllers\HomeController.cs*。

2. 刪除 `About()` 方法，然後插入下列程式碼加以取代。

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2. 在 `ViewBag.Message` 程式碼上 [設定中斷點](http://www.visualstudio.com/zh-tw/get-started/debug-your-app-vs.aspx)。

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下專案，然後按一下 [**發佈**]。

2. 在 [**設定檔**] 下拉式清單中，選取您在 [開始使用 Azure 和 ASP.NET][GetStarted] 中使用的相同設定檔。

3. 按一下 [**設定**] 索引標籤，並將 [**組態**] 變更為 [**偵錯**]，然後按一下 [**發佈**]。

	![Publish in debug mode](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. 部署完成後，您的瀏覽器會開啟至網站的 Azure URL，這時請關閉瀏覽器。

5. 若是 Visual Studio 2013：在 [**伺服器總管**] 中，依序展開 [**Azure**]、[**網站**]，然後以滑鼠右鍵按一下您的網站，接著按一下 [**連結偵錯工具**]。 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

瀏覽器會自動開啟至您在 Azure 中執行的首頁。您可能需要等候 20 秒左右的時間，讓 Azure 設定要偵錯的伺服器。通常只有當您第一次在網站上執行偵錯模式時，才會出現這個延遲現象。後續 48 小時內再次啟動的偵錯程序將不會再出現任何延遲。

6. 針對 Visual Studio 2012 含 Update 4：<a id="vs2012"></a>

	* 在 Azure 管理入口網站中，前往網站的 [**設定**] 索引標籤，然後向下捲動至 [**網站診斷**] 區段。

	* 將 [**遠端偵錯**] 設為 [**開啟**]，並將 [**Remote Debugging Visual Studio Version**] 設為 **2012**。

	![Set remote debugging in management portal](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debuginportal.png)
   
	* 在 Visual Studio 的 [**偵錯**] 功能表中，按一下 [**附加至處理序**]。

	* 在 [**限定詞**] 方塊中，輸入您的網站 URL (不含 `http://` 前置詞)。 

	* 選取 [**顯示所有使用者的處理序**]。

	* 當系統提示您輸入認證時，請輸入具有發佈網站權限的使用者名稱與密碼。若要取得這些認證，請前往管理入口網站中網站的 [儀表板] 索引標籤，然後按一下 [**下載發佈設定檔**]。在文字編輯器中開啟檔案，這時您會在第一個出現的 **userName=** 與 **userPWD=** 的後面看到使用者名稱與密碼。 

	* 當 [**Available Processes**] 資料表中出現處理序時，選取 **w3wp.exe**，然後按一下 [**附加**]。

	* 開啟瀏覽器以瀏覽至您的網站 URL。

您可能需要等候 20 秒左右的時間，讓 Azure 設定要偵錯的伺服器。通常只有當您第一次在網站上執行偵錯模式時，才會出現這個延遲現象。後續 48 小時內再次啟動的偵錯程序將不會再出現任何延遲。

6. 按一下功能表中的 [**關於**]。

	Visual Studio 會在中斷點處停止，而程式碼是在 Azure 中執行，而不是在您的本機電腦上執行。

7. 將滑鼠移至 `currentTime` 變數上，以查看時間值。

	![View variable in debug mode running in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

	您在 Azure 伺服器時間上看到的時間，可能與您的本機電腦所在的時區不同。

8. 為 `currentTime` 變數輸入新的值，例如 "Now running in Azure"。

5. 按 F5 繼續執行。

	Azure 中執行的 [關於] 頁面會顯示您輸入到 currentTime 變數中的新值。

	![About page with new value](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

### <a name="remotedebugwj"></a> WebJobs 的遠端偵錯

本節說明如何使用您在 [開始使用 Azure WebJobs SDK](../websites-dotnet-webjobs-sdk) 中所建立的專案和網站進行遠端偵錯。本節上所顯示的功能僅適用於 Visual Studio 2013 with Update 4。

1. 開啟您在 [開始使用 Azure WebJobs SDK][GetStartedWJ] 中所建立的 Web 專案。

1. 在 ContosoAdsWebJob 專案中，開啟 *Functions.cs*。

2. [在 `GnerateThumbnail` 方法的第一個陳述式上設定中斷點](http://www.visualstudio.com/zh-tw/get-started/debug-your-app-vs.aspx)。

	![Set breakpoint](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下 Web 專案 (不是 WebJob 專案)，然後按一下 [**發佈**]。

2. 在 [**設定檔**] 下拉式清單中，選取您在 [開始使用 Azure WebJobs SDK](../websites-dotnet-webjobs-sdk) 中所使用的相同設定檔。

3. 按一下 [**設定**] 索引標籤，並將 [**組態**] 變更為 [**偵錯**]，然後按一下 [**發佈**]。

	Visual Studio 會部署 Web 和 WebJob 專案，且您的瀏覽器會開啟至網站的 Azure URL。

5. 在 [**伺服器總管**] 中，依序展開 [**Azure**] > [**網站**] > 您的網站 > [**WebJobs**] > [**連續**]，然後以滑鼠右鍵按一下 **ContosoAdsWebJob**。

7. 按一下 [**連結偵錯工具**]。 

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

瀏覽器會自動開啟至您在 Azure 中執行的首頁。您可能需要等候 20 秒左右的時間，讓 Azure 設定要偵錯的伺服器。通常只有當您第一次在網站上執行偵錯模式時，才會出現這個延遲現象。如果您在 48 小時內執行，則下一次連結偵錯工具時將不會出現延遲。

6. 在開啟至 Contoso 廣告首頁的 Web 瀏覽器中，建立新的廣告。 

建立廣告會建立將由 WebJob 挑選並處理的佇列訊息。當 WebJobs SDK 呼叫函數來處理佇列訊息時，程式碼便會叫用中斷點。

7. 當偵錯工具在中斷點出現中斷時，您可以檢查並變更在雲端執行程式時的變數值。在下圖中，偵錯工具顯示已傳遞到 GenerateThumbnail 方法的 blobInfo 物件內容。

	![blobInfo object in debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
 
5. 按 F5 繼續執行。

	GenerateThumbnail 方法完成建立縮圖。

6. 在瀏覽器中重新整理索引頁面，您便會看見縮圖。

6. 在 Visual Studio 中，按 SHIFT+F5 停止偵錯。

7. 在 [**伺服器總管**] 中，以滑鼠右鍵按一下 ContosoAdsWebJob 節點，然後按一下 [**檢視儀表板**]。

8. 使用您的 Azure 認證登入，然後按一下 WebJob 名稱，可前往 WebJob 的頁面。 

	![Click ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

	儀表板會顯示最近執行的 GenerateThumbnail 函數。

(下次按一下 [**檢視儀表板**] 時，您無需登入，瀏覽器便會直接進入您的 WebJob 頁面。)

9. 按一下函數名稱可查看執行函數的詳細資料。

	![Function details](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

如果函數會[撰寫記錄](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs)，您可以按一下 **ToggleOutput** 查看這些記錄。

### 遠端偵錯注意事項

* 不建議在生產環境中執行偵錯模式。如果您的生產網站尚未橫向擴充為多個伺服器執行個體，則偵錯會防止網頁伺服器回應其他要求。如果您不具備多個 Web 伺服器執行個體，當您連結至偵錯工具時，會取得一個隨機產生的執行個體，而且您將無法確認後續瀏覽器要求是否會通往該執行個體。此外，通常我們不會將偵錯組建部署到生產環境中，而且版本組建的編譯器最佳化作業將會使系統無法逐行顯示您的原始程式碼中所發生的事情。針對生產環境問題的疑難排解，您的最佳資源將是應用程式追蹤與 Web 伺服器記錄。

* 進行遠端偵錯時，避免長時間在中斷點停止運作。Azure 會將停止超過幾分鐘的處理序視為無回應的處理序，並將其關閉。

* 在偵錯期間，伺服器會將資料傳送至 Visual Studio，進而影響頻寬付費情況。如需關於頻寬費率的詳細資訊，請參閱 [Azure 定價](/zh-tw/pricing/calculator/)。

* 請確定已將 *Web.config* 檔案中 `compilation` 元素的 `debug` 屬性設定為 true。在發佈偵錯組建組態時，該值預設會設為 true。

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

* 如果您發現偵錯工具無法逐步執行您要偵錯的程式碼，可能需要變更 [Just My Code] 設定。如需詳細資訊，請參閱 [將逐步執行限制於 Just My Code](http://msdn.microsoft.com/zh-tw/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code)。

* 當您啟用遠端偵錯功能時，伺服器上會啟動計時器，並在 48 小時後自動關閉此功能。此 48 小時的限制是為了安全與效能起見而設計的功能。若需要，您可以輕鬆開啟這項功能，次數不限。當您不需要偵錯時，建議您將其保持為停用。

* 您不僅可以手動將偵錯工具連結至網站處理序 (w3wp.exe)，還可以連結至任何處理序。如需如何在 Visual Studio 中使用偵錯模式的詳細資訊，請參閱 [Visual Studio 偵錯](http://msdn.microsoft.com/zh-tw/library/vstudio/sc65sadd.aspx)。

<h2><a name="logsoverview"></a>診斷記錄概觀</h2>

在 Azure 網站中執行的 ASP.NET 應用程式，可建立下列各種記錄：

* **應用程式追蹤記錄**<br/>
應用程式會呼叫 [System.Diagnostics.Trace](http://msdn.microsoft.com/zh-tw/library/system.diagnostics.trace.aspx) 類別的方法來建立這些記錄。
* **Web 伺服器記錄**<br/>
  Web 伺服器會為每個通往網站的 HTTP 要求建立記錄項目。
* **詳細的錯誤訊息記錄**<br/>
Web 伺服器會針對失敗的 HTTP 要求 (產生狀態碼 400 或以上的要求) 建立含有一些額外資訊的 HTML 頁面。 
* **失敗要求追蹤記錄**<br/>
Web 伺服器會針對失敗的 HTTP 要求建立含有詳細追蹤資訊的 XML 檔案。Web 伺服器會一併提供 XSL 檔案，在瀏覽器中格式化 XML。
  
記錄作業會影響網站效能，因此 Azure 可讓您視需要啟用或停用每一種記錄類型。對於應用程式記錄，您可以指定只寫入高於特定嚴重性層級的記錄。在您建立新網站時，預設會停用所有記錄功能。

記錄會寫入網站的檔案系統中 *LogFiles* 資料夾內的檔案，而且可以透過 FTP 存取此檔案。Web 伺服器記錄與應用程式記錄也可以寫入至 Azure 儲存體帳戶。您可以在儲存體帳戶中保留大於檔案系統可容納的記錄檔數量。當您使用檔案系統時，最大的記錄檔大小為 100 MB。(檔案系統記錄僅適用於短期保留之用。Azure 會在達到此上限之後刪除舊的記錄檔以騰出空間給新的記錄檔使用。)  

<h2><a name="apptracelogs"></a>建立並檢視應用程式追蹤記錄</h2>

您將在本節執行下列工作：

* 將追蹤陳述式新增至您在 [開始使用 Azure 和 ASP.NET][GetStarted] 所建立的 Web 專案。
* 當您在本機上執行專案時檢視記錄。
* 依原樣檢視 Azure 中執行的應用程式所產生的記錄。 

如需如何在 WebJobs 中建立應用程式記錄的詳細資訊，請參閱 [如何運用 WebJobs SDK 來使用 Azure 佇列儲存體 - 如何寫入記錄](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs)。下列有關在 Azure 中檢視記錄和控制記錄儲存方式的指示也同樣適用於 WebJobs 所建立的應用程式記錄。 

### 將追蹤陳述式新增至應用程式

1. 開啟 *Controllers\HomeController.cs*，然後使用下列程式碼來取代檔案內容，以便為 `System.Diagnostics` 新增 `Trace` 陳述式與 `using` 陳述式：

		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.Diagnostics;
		using System.Linq;
		using System.Web;
		using System.Web.Configuration;
		using System.Web.Mvc;
		namespace MyExample.Controllers
		{
 public class HomeController :Controller
		    {
		        public ActionResult Index()
		        {
		            Trace.WriteLine("Entering Index method");
		            ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
		            Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Leaving Index method");
		            return View();
		        }
		
		        public ActionResult About()
		        {
		            Trace.WriteLine("Entering About method");
		            ViewBag.Message = "Your app description page.";
		            Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
		            Trace.WriteLine("Leaving About method");
		            return View();
		        }
		
		        public ActionResult Contact()
		        {
		            Trace.WriteLine("Entering Contact method");
		            ViewBag.Message = "Your contact page.";
		            Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Leaving Contact method");
		            return View();
		        }
		    }
		}
		
				
### 在本機檢視追蹤輸出

3. 按 F5 以在偵錯模式中執行應用程式。

預設的追蹤接聽程式會將所有追蹤輸出連同其他偵錯輸出，一起寫入到 [**輸出**] 視窗。下圖顯示您新增至 `Index` 方法的追蹤陳述式的輸出。

	![Tracing in Debug window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

	以下步驟說明如何在網頁中檢視追蹤輸出，而不需在偵錯模式中編譯。

2. 開啟應用程式 Web.config 檔案 (位於專案資料夾中的檔案)，並將 `<system.diagnostics>` 元素加入檔案最後的結尾 `</configuration>` 元素前面：

  		<system.diagnostics>
		    <trace>
		      <listeners>
		        <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener, 
                    System.Web, 
                    Version=4.0.0.0, 
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
		      </listeners>
		    </trace>
		  </system.diagnostics>

	「WebPageTraceListener」可讓您瀏覽至「/trace.axd」以便檢視追蹤輸出。

3. 在 Web.config 檔案的 `<system.web>` 下方新增 <a href="http://msdn.microsoft.com/zh-tw/library/vstudio/6915t83k(v=vs.100).aspx">trace 元素</a>，如以下範例所示：

		<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. 按 CTRL+F5 執行應用程式。

4. 在瀏覽器視窗的網址列中，將 *trace.axd* 新增至 URL，然後按 Enter (URL 會類似於 http://localhost:53370/trace.axd)。

5. 在 [**應用程式追蹤**] 頁面上，按一下第一行 (不是 BrowserLink 行) 上的 [**檢視詳細資料**]。

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

[**要求詳細資訊**] 頁面隨即顯示，而且在 [**追蹤資訊**] 區段中，您會看到先前新增至 `Index` 方法的 trace 陳述式輸出。

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

根據預設，`trace.axd` 只能在本機使用。如果您想在遠端站台使用它，可以將 `localOnly="false"` 新增至 *Web.config* 檔案中的 `trace` 元素，如以下範例所示：

		<trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

	然而，為了安全起見，通常我們不建議在生產網站上啟用 `trace.axd`，而以下各節將為您說明如何用更簡易的方式在 Azure 網站中讀取追蹤記錄。

### 在 Azure 中檢視追蹤輸出

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下 Web 專案，然後按一下 [**發佈**]。

2. 在 [**發佈 Web**] 對話方塊中，按一下 [**發佈**]。

當 Visual Studio 成功發佈您的更新後，瀏覽器視窗會開啟至您的首頁 (假設您並未清除 [**連線**] 索引標籤上的 [**目的地 URL**])。

3. 在 [**伺服器總管**] 中，以滑鼠右鍵按一下您的網站，並選取 [**檢視串流記錄**]。 

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

[**輸出**] 視窗會顯示您已連線至記錄串流服務，並每一分鐘將沒有要顯示的記錄新增一行通知文字。

	![View Streaming Logs in context menu](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. 在顯示您的應用程式首頁的瀏覽器視窗中，按一下 [**連絡人**]。

幾秒鐘後，您新增至 `Contact` 方法的錯誤層級追蹤輸出便會顯示在 [**輸出**] 視窗。

	![Error trace in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

Visual Studio 只會顯示錯誤層級追蹤，因為那是您在啟用記錄監視服務時的預設設定。建立新的 Azure 網站時，預設會停用所有記錄功能，正如同稍早您開啟網站設定頁面時所見：

	![Application Logging off](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)


不過，當您選取 [**檢視串流記錄**] 時，Visual Studio 會自動將 [**Application Logging (File System)**] 變更為 [**錯誤**]，代表回報的會是錯誤層級記錄。為了查看所有的追蹤記錄，您可以將此設定變更為 [**詳細資訊**]。當您選取低於錯誤的嚴重性層級時，將一併回報較高嚴重性層級的所有記錄。因此當您選取詳細資訊時，您會同時看到資訊、警告與錯誤記錄。  

4. 在 [**伺服器總管**] 中，以滑鼠右鍵按一下網站，然後按一下 [**檢視設定**] (如同稍早所做的動作)。

5. 將 [**Application Logging (File System)**] 變更為 [**詳細資訊**]，然後按一下 [**儲存**]。
 
	![Setting trace level to Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

6. 在目前正顯示 [**連絡人**] 頁面的瀏覽器視窗中，依序按一下 [**首頁**]、[**關於**] 及 [**連絡人**]。

在幾秒鐘內，[**輸出**] 窗就會顯示您的所有追蹤輸出。

	![Verbose trace output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

在本節中，您已使用 Azure 網站設定來啟用與停用記錄功能。您也可以修改 Web.config 檔案，來啟用與停用追蹤接聽程式。不過，修改 Web.config 檔案會導致應用程式網域回收，而透過網站啟用記錄功能則不會有這種現象。如果此問題需要經過長時間才會重現或是間歇性出現，則回收應用程式網域可能「修正」此問題，並強迫您等候問題再次發生。在 Azure 中啟用診斷功能不會出現此情況，因此您可以立即開始擷取錯誤資訊。

### 輸出視窗功能

[**輸出**] 視窗的 [**Azure 記錄**] 索引標籤具有多個按鈕與一個文字方塊：

![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

這些物件可執行下列功能：

* 清除 [**輸出**] 視窗。
* 啟用或停用自動換行。
* 啟動或停止監視記錄。
* 指定要監視的記錄。
* 下載記錄。
* 依據搜尋字串或規則運算式篩選記錄。
* 關閉 [**輸出**] 視窗。

如果您輸入搜尋字串或是規則運算式，則 Visual Studio 會篩選用戶端的記錄資訊。亦即，您可以等到 [**輸出**] 視窗顯示記錄後輸入條件，這樣您不需重新產生記錄便能直接變更篩選條件。

<h2><a name="webserverlogs"></a>檢視 Web 伺服器記錄</h2>

Web 伺服器記錄會記下網站上的所有 HTTP 活動。為了在 [**輸出**] 視窗中檢視記錄，您必須在網站上啟用記錄，然後告訴 Visual Studio 您想要監視記錄。 

1. 在您從 [**伺服器總管**] 開啟的 [**Azure 網站組態**] 索引標籤中，將 [Web 伺服器記錄] 變更為 [**開啟**]，然後按一下 [**儲存**]。

	![Enable web server logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2. 在 [**輸出**] 視窗中，按一下 [**Specify which Azure logs to monitor**] 按鈕。
	
	![Specify which Azure logs to monitor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3. 在 [**Azure 記錄選項**] 對話方塊中，選取 [**Web 伺服器記錄**]，然後按一下 [**確定**]。

	![Monitor web server logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. 在顯示網站的瀏覽器視窗中，依序按一下 [**首頁**]、[**關於**] 及 [**連絡人**]。

通常會先產生應用程式記錄，然後才是 Web 伺服器記錄。您可能需要等候一小段時間，記錄才會顯示。 

	![Web server logs in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


根據預設，當您第一次使用 Visual Studio 啟用 Web 伺服器記錄時，Azure 會將記錄寫入檔案系統。做為替代方式，您可以使用管理入口網站來指定應該將 Web 伺服器記錄寫入儲存體帳戶中的某個 Blob 容器。如需詳細資訊，請參閱 **網站診斷**一節 ([如何設定網站](/zh-tw/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig). 

If you use the management portal to enable web server logging to an Azure storage account, and then disable logging in Visual Studio, when you re-enable logging in Visual Studio your storage account settings are restored. 

<h2><a name="detailederrorlogs"></a>View detailed error message logs</h2>

Detailed error logs provide some additional information about HTTP requests that result in error response codes (400 or above). In order to see them in the **Output** window, you have to enable them on the site and tell Visual Studio that you want to monitor them.

1. In the **Azure Website Configuration** tab that you opened from **Server Explorer**, change **Detailed Error Messages** to **On**, and then click **Save**.

	![Enable detailed error messages](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. In the **Output** Window, click the **Specify which Azure logs to monitor** button.

3. In the **Azure Logging Options** dialog box, click **All logs**, and then click **OK**.

	![Monitor all logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. In the address bar of the browser window, add an extra character to the URL to cause a 404 error (for example, `http://localhost:53370/Home/Contactx`), and press Enter.

	After several seconds the detailed error log appears in the Visual Studio **Output** window.

	![Detailed error log in Output window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

	Control+click the link to see the log output formatted in a browser:

	![Detailed error log in browser window](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

<h2><a name="downloadlogs"></a>Download file system logs</h2>

Any logs that you can monitor in the **Output** window can also be downloaded as a *.zip* file. 

1. In the **Output** window, click **Download Streaming Logs**.

	![Logs tab buttons](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

	File Explorer opens to your *Downloads* folder with the downloaded file selected.

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. Extract the *.zip* file, and you see the following folder structure:

	![Downloaded file](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

	* Application tracing logs are in *.txt* files in the *LogFiles\Application* folder.
	* Web server logs are in *.log* files in the *LogFiles\http\RawLogs* folder. You can use a tool such as [Log Parser](http://www.microsoft.com/zh-tw/download/details.aspx?displaylang=en&id=24659) to view and manipulate these files.
	* Detailed error message logs are in *.html* files in the *LogFiles\DetailedErrors* folder.

	(The *deployments* folder is for files created by source control publishing; it doesn't have anything related to Visual Studio publishing. The *Git* folder is for traces related to source control publishing and the log file streaming service.)  

<h2><a name="storagelogs"></a>View storage logs</h2>

Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the management portal, and view them in the **Logs** tab of the **Azure Website** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination. 

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

5. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

3. In the **Create Storage Account** dialog, enter a name for the storage account. 

	The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

	The URL to access your storage account will be *{name}*.core.windows.net. 

5. Set the **Region or Affinity Group** drop-down list to the region closest to you.

	This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production site you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The website (which you'll create later) should be as close as possible to the browsers accessing your site in order to minimize latency.

6. Set the **Replication** drop-down list to **Locally redundant**. 

	When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [How To Manage Storage Accounts](/zh-tw/documentation/articles/storage-manage-storage-account/).

5. 按一下 [**建立**]。 

	![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)	

1. 在 Visual Studio 的 [**Azure 網站**] 視窗中，按一下 [**記錄**] 索引標籤，然後按一下 [**Configure Logging in Management Portal**]。

	![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

這麼做會在管理入口網站中開啟您網站的 [**設定**] 索引標籤。要到達此頁面的另一個方法，就是依序按一下 [**網站**] 索引標籤、您的網站、[**設定**] 索引標籤。

2. 在管理入口網站的 [**設定**] 索引標籤中，向下捲動至應用程式診斷區段，然後將 [**Application Logging (Table Storage)**] 變更為 [**開啟**]。

3. 將 [**記錄層級**] 變更為 [**資訊**]。

4. 按一下 [**管理資料表儲存體**]。

	![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

如果您擁有不只一個儲存體帳戶，則可以在 [**Manage table storage for application diagnostics**] 方塊中選擇您的儲存體帳戶。您可以建立新的資料表，或是使用現有的資料表。

	![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. 在 [**Manage table storage for application diagnostics**] 方塊中，按一下核取方塊以關閉該方塊。

6. 在管理入口網站的 [**設定**] 索引標籤中，按一下 [**儲存**]。

7. 在顯示應用程式網站的瀏覽器視窗中，依序按一下 [**首頁**]、[**關於**] 及[**連絡人**]。

	瀏覽這些網頁所產生的記錄資訊將會寫入儲存體帳戶。

8. 在 Visual Studio 的 [**Azure 網站**] 視窗中，按一下 [**記錄**] 索引標籤中 [**Diagnostic Summary**] 下的 [**重新整理**]。

	![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

[**Diagnostic Summary**] 區段預設會顯示最後 15 分鐘的記錄。您可以變更期間以檢視更多記錄。 

	(如果出現「找不到資料表」錯誤，請確認在您啟用 [**Application Logging (Storage)**] 並按一下 [**儲存**] 之後，您所瀏覽的頁面能夠進行追蹤作業。)

	![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

請注意，在此檢視中您會看到每個記錄的 [**處理序識別碼**] 與 [**執行緒識別碼**]，而這是檔案系統記錄無法提供的資訊。您可以直接檢視 Azure 儲存體資料表來查看其他欄位。

8. 按一下 [**View all application logs**]。

	追蹤記錄資料表會顯示在 Azure 儲存體資料表檢視器中。
   
(如果出現「序列未包含項目」錯誤，請開啟 [**伺服器總管**] 並展開 [**Azure**] 節點下方的儲存體帳戶節點，然後以滑鼠右鍵按一下 [**資料表**] 並按一下 [**重新整理**]。)

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetableinse.png)

	![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

此檢視會顯示在其他任何檢視中都不會看到的額外欄位。此檢視還可讓您藉由特殊的查詢產生器 UI 來篩選記錄，以便建構查詢。如需詳細資訊，請參閱[使用伺服器總管瀏覽儲存體資源](http://msdn.microsoft.com/zh-tw/library/windowsazure/ff683677.aspx)中的「使用表格資源 - 篩選實體」。

7. 若要查看單一資料列的詳細資訊，請按兩下其中一個資料列。

	![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

<h2><a name="failedrequestlogs"></a>檢視失敗要求追蹤記錄</h2>

當您需要了解 IIS 如何處理 HTTP 要求的詳細資料時，例如在 URL 重新寫入或是出現驗證問題等情況，失敗要求追蹤記錄就會很有用。 

Azure 網站會使用 IIS 7.0 及更新版本所提供的相同失敗要求追蹤功能。但您無法存取用來設定哪些錯誤會被記錄下來的 IIS 設定。當您啟用失敗要求追蹤時，將擷取所有錯誤。 

您可以使用 Visual Studio 來啟用失敗要求追蹤，但是您無法在 Visual Studio 中加以檢視。這些記錄是 XML 檔案。串流記錄服務只會監視被當作可在純文字模式中讀取的檔案：*.txt*, *.html* 和 *.log* 檔案。

您可以直接在瀏覽器中透過 FTP，或是在本機使用 FTP 工具將記錄下載到本機電腦中之後檢視失敗要求追蹤記錄。在本節中，您將在瀏覽器中直接檢視這些記錄。

1. 在從 [**伺服器總管**] 開啟的 [**Azure 網站**] 視窗中，將 [**組態**] 索引標籤中的 [**失敗要求追蹤**] 變更為 [**開啟**]，然後按一下 [**儲存**]。

	![Enable failed request tracing](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. 在顯示該網站的瀏覽器視窗網址列中，將額外字元加入 URL 並按一下 Enter 鍵以引發 404 錯誤。

	這麼做會讓系統建立失敗要求追蹤記錄，以下步驟將說明如何檢視或下載記錄。

2. 在 Visual Studio 的 [**Azure 網站**] 視窗中，按一下 [**組態**] 索引標籤的 [**在管理入口網站中開啟**]。

3. 在管理入口網站中，按一下 [**儀表板**]，然後按一下 [**快速概覽**] 區段中的 [**重設您的部署認證**]。

	![Reset FTP credentials link in Dashboard](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-resetftpcredentials.png)

4. 輸入新的使用者名稱與密碼。

	![New FTP user name and password](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

5. 在管理入口網站的 [**儀表板**] 索引標籤中，按 F5 以重新整理頁面，然後向下捲動至顯示 [**Deployment / FTP User**] 的位置。請注意，該使用者名稱前面有加上網站名稱前置詞。**當您登入時，必須使用此完整的使用者名稱，加上此處所示的網站名稱前置詞。**

5. 在新瀏覽器視窗中，前往顯示在網站的管理入口網站頁面 [**儀表板**] 索引標籤中 [**FTP 主機名稱**] 下方的 URL。[**FTP 主機名稱**] 位於 [**快速概覽**] 區段的 [**Deployment / FTP User**] 附近。

6. 使用您先前建立的 FTP 認證登入 (包括該使用者名稱的網站名稱前置詞)。

	瀏覽器會顯示網站的根資料夾。

6. 開啟 *LogFiles* 資料夾。

	![Open LogFiles folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. 開啟名為 W3SVC 並加上數值的資料夾。

	![Open W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

	該資料夾包含了一些 XML 檔案 (內含任何您在啟用失敗要求追蹤功能後所記錄的錯誤)，以及一個可供瀏覽器用來格式化該 XML 檔案的 XSL 檔案。

	![W3SVC folder](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. 按一下 XML 檔案，以取得您想要檢視其追蹤資訊的失敗要求。

	下圖顯示錯誤範例追蹤資訊的片段。

	![Failed request tracing in browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


<h2><a name="nextsteps"></a>後續步驟</h2>

在了解 Visual Studio 如何讓您輕鬆地檢視 Azure 網站所建立的記錄之後，下列各節提供相關主題的更多資源連結：

* Azure 網站疑難排解
* 在 Visual Studio 中偵錯 
* 在 Azure 中遠端偵錯
* 在 ASP.NET 應用程式中追蹤
* 分析 Web 伺服器記錄
* 分析失敗要求追蹤記錄
* 偵錯雲端服務

### Azure 網站疑難排解

如需疑難排解 Azure 網站 (WAWS) 的詳細資訊，請參閱下列資源：

* [如何監視網站](/zh-tw/manage/services/web-sites/how-to-monitor-websites/)
* [使用 Visual Studio 2013 調查 Azure 網站中的記憶體流失](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx) (英文)。Microsoft ALM 部落格文章，討論 Visual Studio 中分析 Managed 記憶體問題的功能。
* [您應該知道的 Windows Azure 網站的線上工具](/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/) (英文)。取自 Amit Apple 的部落格文章。

如需特定疑難排解問題的說明，請在下列任一個論壇中開啟一段討論串：

* [ASP.NET 網站上的 Azure 論壇](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET)。
* [MSDN 上的 Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/)。
* [StackOverflow.com](http://www.stackoverflow.com)。

### 在 Visual Studio 中偵錯 

如需如何在 Visual Studio 中使用偵錯模式的詳細資訊，請參閱 [在 Visual Studio 中偵錯](http://msdn.microsoft.com/zh-tw/library/vstudio/sc65sadd.aspx) MSDN 主題與 [Visual Studio 2010 的偵錯秘訣](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx)。

### 在 Azure 中遠端偵錯

如需 Azure 網站和 WebJobs 的遠端偵錯詳細資訊，請參閱下列資源：

* [介紹 Azure 網站的遠端偵錯](/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/) (英文)。
* [介紹 Azure 網站的遠端偵錯 (第 2 部分) - 內部遠端偵錯](/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [介紹 Azure 網站的遠端偵錯 (第 3 部分) - 多執行個體環境和 GIT](/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs 偵錯 (影片)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

如果您的網站使用 Azure Web API 或是行動服務後端，而您需要加以偵錯，請參閱 [在 Visual Studio 中對 .NET 後端進行偵錯](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx) (英文)。

### 在 ASP.NET 應用程式中追蹤

網際網路上找不到關於 ASP.NET 追蹤功能詳盡且具時效性的說明。您所能做的，就是從專為 Web Form 所撰寫的一些舊有簡介資料下手，因為 MVC 是最近才問世的技術，並以著重在特定議題的較新的部落格文章來做為補充。以下資源是您開始了解這項技術的一些好去處：

* [監視與遙測 (運用 Azure 建構真實的雲端應用程式)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry) (英文)。<br> 
  針對追蹤 Azure 雲端應用程式所建議的電子書章節。
* [ASP.NET 追蹤](http://msdn.microsoft.com/zh-tw/library/ms972204.aspx)<br/>
  雖然舊但仍是該主題的基本簡介的良好資源。
* [追蹤接聽程式](http://msdn.microsoft.com/zh-tw/library/4y5y10s7.aspx)<br/>
內含有關追蹤接聽程式的資訊，但是沒有提到 [WebPageTraceListener](http://msdn.microsoft.com/zh-tw/library/system.web.webpagetracelistener.aspx)。
* [逐步介紹：整合 ASP.NET 追蹤與 System.Diagnostics 追蹤](http://msdn.microsoft.com/zh-tw/library/b0ectfxd.aspx)<br/>
  此資訊同樣很舊，但包含了簡介文章沒有提到的一些其他資訊。
* [追蹤 ASP.NET MVC Razor 檢視](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
除了追蹤 Razor 檢視之外，該文同時說明了如何建立錯誤篩選條件以便記錄 MVC 應用程式所出現的所有未處理的例外。如需如何記錄 Web Form 應用程式中所有未處理的例外項目的詳細資訊，請參閱 MSDN 上[完整的錯誤處理常式範例](http://msdn.microsoft.com/zh-tw/library/bb397417.aspx) (英文) 的 Global.asax 範例。無論是 MVC 還是 Web Form，如果您想要記錄特定例外，但是讓預設的架構處理功能生效，則您可以如以下範例所示捕捉並重新擲回這些例外：

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception:" + ex.ToString());
            throw;
        } 

* [從 Azure 命令列串流診斷追蹤記錄 (加上 Glimpse！)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
如何使用命令列來執行本教學課程所示範的 Visual Studio 步驟。[Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) (英文) 工具可供您偵錯 ASP.NET 應用程式。 
* [使用 Azure 網站記錄與診斷功能 - 與 David Ebbo 一起](http://www.windowsazure.com/zh-tw/documentation/videos/azure-web-site-logging-and-diagnostics/) (英文) 與 [從 Azure 網站串流記錄 - 與 David Ebbo 一起](http://www.windowsazure.com/zh-tw/documentation/videos/log-streaming-with-azure-web-sites/)<br> (英文)
  Scott Hanselman 和 David Ebbo 提供的影片。

針對錯誤記錄，做為撰寫自己的追蹤程式碼的替代方法，便是使用開放原始碼的記錄架構，例如 [ELMAH](http://nuget.org/packages/elmah/)。如需詳細資訊，請參閱 [Scott Hanselman 關於 ELMAH 的部落格文章](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx) (英文)。

此外，如果您想要從 Azure 取得串流記錄，則您不需要使用 ASP.NET 或 System.Diagnostics 追蹤功能。Azure 網站串流記錄服務會串流它在 *LogFiles* 資料夾所找到的任何 *.txt*、*.html* 或 *.log* 檔案。因此，您可以建立自己的記錄系統以便寫入網站的檔案系統，而且您的檔案將會自動進行串流與下載。您只需撰寫會在 *d:\home\logfiles* 資料夾中建立相關檔案的應用程式碼。 

### 分析 Web 伺服器記錄

如需分析 Web 伺服器記錄的詳細資訊，請參閱下列資源：

* [LogParser](http://www.microsoft.com/zh-tw/download/details.aspx?id=24659)<br/>
  在 Web 伺服器記錄中檢視資料的工具 (*.log* 檔案)。
* [疑難排解 IIS 效能問題或使用 LogParser 的應用程式錯誤 ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  介紹可用來分析 Web 伺服器記錄的記錄檔剖析器工具。
* [Robert McMurray 關於使用 LogParser 的部落格文章](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [IIS 7.0、IIS 7.5 與 IIS 8.0 中的 HTTP 狀態碼](http://support.microsoft.com/kb/943891)

### 分析失敗要求追蹤記錄

Microsoft TechNet 網站內的 [使用失敗要求追蹤](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) (英文) 一節，可能對於了解如何使用這些記錄有所幫助。不過，本文主要著重在 IIS 內設定失敗要求追蹤功能，這是您無法在 Azure 網站中執行的功能。

### 偵錯雲端服務

如果您想對 Azure 雲端服務 (而非網站) 進行偵錯，請參閱 [偵錯雲端服務](http://msdn.microsoft.com/zh-tw/library/windowsazure/ee405479.aspx) (英文)。




[GetStarted]: ../web-sites-dotnet-get-started/
[GetStartedWJ]: ../websites-dotnet-webjobs-sdk/

