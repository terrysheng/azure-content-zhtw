<properties linkid="develop-dotnet-aspnet-mvc-4-mobile-website" urlDisplayName="ASP.NET MVC 4 mobile website" pageTitle=".NET ASP.NET MVC 4 mobile web site - Azure tutorials" metaKeywords="Azure tutorial, Azure web app tutorial, Azure mobile app, Azure ASP.NET MVC 4,,ASP.NET MVC" description="A tutorial that teaches you how to deploy a web application to an Azure web site using mobile features in ASP.NET MVC 4 web application." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Deploy an ASP.NET MVC Mobile Web Application on Azure Web Sites" authors="tdykstra" solutions="" manager="" editor="" />

在 Azure 網站上部署 ASP.NET MVC 行動 Web 應用程式
=================================================

***作者：[Rick Anderson](https://twitter.com/RickAndMSFT)，更新日期：2013 年 6 月 26 日。***

本教學課程交講述如何將 Web 應用程式部署至 Azure 網站的基本知識。為方便本教學課程進行，我們將使用 ASP.NET MVC 4 Web 應用程式中的行動功能。若要執行本教學課程裡的步驟，請使用 Microsoft Visual Studio 2012。您也可以使用 [Visual Studio Express 2012](http://www.microsoft.com/visualstudio/eng/products/visual-studio-express-products) (Microsoft Visual Studio 免費版本)。

您將了解：
----------

-   ASP.NET MVC 4 範本如何運用 HTML5 viewport 屬性與調適性轉譯功能來提升行動裝置上的顯示效果。
-   如何建立行動裝置專屬的檢視。
-   如何建立檢視應用程式切換器，以讓使用者切換行動檢視與桌面檢視。
-   如何將 Web 應用程式部署至 Azure。

在本教學課程中，您將把行動功能新增至入門專案裡提供的簡單會議清單應用程式。以下螢幕擷取畫面顯示如 Windows 7 Phone 模擬器所示的已完成應用程式主頁面。

![MVC4 conference application main page.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/FinishedAPPMainScreen.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

設定開發環境
------------

安裝 Azure SDK for the .NET Framework，以設定您的開發環境。

1.  若要安裝 Azure SDK for .NET，請按一下底下連結：如果您尚未安裝 Visual Studio 2012，按下該連結會進行安裝。本教學課程需要安裝 Visual Studio 2012。[Azure SDK for Visual Studio 2012](http://go.microsoft.com/fwlink/?LinkId=254364) (英文)
2.  當系統提示您執行或儲存安裝可執行檔時，請按一下 **[執行]**。
3.  在 [Web Platform Installer] 視窗中，按一下 **[安裝]** 並繼續進行安裝。

![Web Platform Installer - Azure SDK for .NET](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk20NetVS12.png)

您還需要一個行動瀏覽器模擬器。下列任一項目都可使用：

-   [Windows 7 Phone 模擬器](http://msdn.microsoft.com/zh-tw/library/ff402530(VS.92).aspx)。(本教學課程中大多數螢幕擷取畫面都是採用此模擬器加以完成。)
-   變更使用者代理程式字串以模擬 iPhone。請參閱 How-To Geek 網頁上的[這個部落格項目](http://www.howtogeek.com/113439/how-to-change-your-browsers-user-agent-without-installing-any-extensions/) (英文)。
-   [Opera Mobile 模擬器](http://www.opera.com/developer/tools/mobile/)。
-   [Apple Safari](http://www.apple.com/safari/download/) (使用者代理程式設為 iPhone)。如需如何在 Safari 中將使用者代理程式設為「iPhone」，請參閱 David Alison 部落格上的[如何讓 Safari 假裝為 IE](http://www.davidalison.com/2008/05/how-to-let-safari-pretend-its-ie.html) (英文)。
-   [FireFox](http://www.bing.com/search?q=firefox+download) 以及 [FireFox 使用者代理程式切換器](https://addons.mozilla.org/en-US/firefox/addon/user-agent-switcher/)。

本教學課程顯示 C\# 程式碼。不過，入門專案與完成專案將可於 Visual Basic 中使用。我們提供 Visual Studio 專案與 Visual Basic 和 C\# 原始碼來幫助您完成此主題：

-   [入門專案下載](http://go.microsoft.com/fwlink/?LinkId=228307)
-   [完成專案下載](http://go.microsoft.com/fwlink/?LinkId=228306)

本教學課程中的步驟
------------------

-   [建立 Azure 網站](#bkmk_CreateWebSite)
-   [設定入門專案](#bkmk_setupstarterproject)
-   [覆寫檢視、配置與部分檢視](#bkmk_overrideviews)
-   [使用 jQuery Mobile 來定義行動瀏覽器介面](#bkmk_usejquerymobile)
-   [改善演講者清單](#bkmk_Improvespeakerslis)
-   [建立行動演講者檢視](#bkmk_mobilespeakersview)
-   [改善標籤清單](#bkmk_improvetags)
-   [改善日期清單](#bkmk_improvedates)
-   [改善 SessionsTable 檢視](#bkmk_improvesessionstable)
-   [改善 SessionByCode 檢視](#bkmk_improvesessionbycode)
-   [將應用程式部署至 Azure 網站](#bkmk_deployapplciation)

### 在 Azure 中建立網站

您的 Azure 網站會在共用主控環境中執行，意即其會在與其他 Azure 用戶端共用的虛擬機器 (VM) 上執行。共用主控環境是一種在雲端中開始營運的低成本方法。因為應用程式是在專用 VM 上執行，所以如果日後您的 Web 流量增加，可依需求對應用程式進行延展。如果您需要更複雜的架構，可以移轉至 Azure 雲端服務。雲端服務是執行於您可視本身需求來設定的專用 VM 上。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com)。在管理入口網站按一下 **[新增]**。

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_1.png)

2.  按一下 **[網站]**，再按 **[快速建立]**。

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_2.png)

3.  在 **[建立新網站] 中**，於 **[URL]** 方塊中輸入字串作為應用程式的唯一 URL。

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_3.png)

    完整的 URL 將包含您在此處輸入的字串，加上您在文字方塊下方看到的尾碼。下圖顯示 "MyMobileMVC4WebSite"，不過如果有人已經先佔用該 URL，您必須選擇不同的 URL。選取您所在的 **[區域]**。

4.  按一下方塊底部的核取方塊來表示完成。

[管理入口網站] 隨即返回 [網站] 頁面，且 [狀態] 欄顯示正在建立網站。稍待片刻 (通常不到一分鐘)，[狀態] 欄就會顯示建立網站成功。在左側的導覽列中，[網站] 圖示旁會出現您帳戶中已有的網站數；[SQL Databases] 圖示旁則出現資料庫數目。

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_4.png)

### 設定入門專案。

1.  下載[會議清單應用程式入門專案](http://go.microsoft.com/fwlink/?LinkId=228307)。

2.  接著在 [Windows 檔案總管] 中，以滑鼠右鍵按一下 MvcMobileStarterBeta.zip 檔案並選擇 *Properties*。

3.  在 MvcMobileRTMStarter.zip [內容] 對話方塊中，選擇 [取消封鎖] 按鈕。(取消封鎖後，當您嘗試使用從網路下載的 .zip 檔案時，就不會出現安全性警告。)

    ![Properties dialog box.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/propertiespopup.png)

4.  以滑鼠右鍵按一下 MvcMobile.zip 檔案並選取 [全部解壓縮] 以解壓縮檔案。

5.  在 Visual Studio 中，開啟 MvcMobile.sln 檔案。

### 執行入門專案

1.  按 CTRL+F5 鍵執行應用程式，使其顯示在您的桌面瀏覽器上。
2.  啟動您的行動瀏覽器模擬器，將會議應用程式 URL 複製到模擬器中，然後按一下 [依標籤瀏覽] 連結。

    -   如果您是使用 Windows Phone 模擬器，請按一下 URL 列並按 Pause 鍵以存取鍵盤。下圖顯示 AllTags 檢視 (透過選擇 [Browse by tag])。

    ![Browse by tag page.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/BrowseByTagWithCallout.png)

該顯示內容在行動裝置上非常清楚易讀。選擇 ASP.NET 連結。

![Browse sessions tagged as ASP.NET.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ASPNetPage.png)

ASP.NET 標籤檢視雜亂無章。例如，[資料] 欄就會非常難以閱讀。本教學課程稍後將建立專為行動瀏覽器設計的 AllTags 檢視版本，以讓顯示內容清楚易讀。

覆寫檢視、配置與部分檢視
------------------------

本節將建立行動裝置專屬的配置檔案。

ASP.NET MVC 4 中新增了一項簡單但重大的新功能，一般而言能讓您針對個別的行動瀏覽器 (或特定瀏覽器) 覆寫其任何檢視 (包括配置與部分檢視)。若要提供行動裝置專屬的檢視，您可以複製檢視檔案並將 .Mobile 新增至檔案名稱。舉例來說，若要建立行動索引檢視，請將 *Views\\Home\\Index.cshtml* 複製到 *Views\\Home\\Index.Mobile.cshtml*。

一開始，請將 *Views\\Shared\\\_Layout.cshtml* 複製到 *Views\\Shared\\\_Layout.Mobile.cshtml*。開啟 *\_Layout.Mobile.cshtml* 並將標題從 **MVC4 Conference** 變更為 **Conference (Mobile)**。

在每次的 **Html.ActionLink** 呼叫當中，移除每個 ActionLink 連結中的 "Browse by"。下列程式碼顯示行動配置檔案的已完成內容區段。

     <body>
        <div class="page">
            <div id="header">
                 <div id="logindisplay"></div>
                 <div id="title">
                      <h1> Conference (Mobile)</h1>
                 </div>
                 <div id="menucontainer">
                       <ul id="menu">
                           <li>@Html.ActionLink("Home", "Index", "Home")</li>
                           <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
                           <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
                           <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
                       </ul>
                 </div>
           </div>
           <div id="main">
               @RenderBody()
           </div>
           <div id="footer">
           </div>
        </div>
    </body>

將 *Views\\Home\\AllTags.cshtml* 檔案複製到 *Views\\Home\\AllTags.Mobile.cshtml*。開啟新檔案並將 &lt;h2\> 元素從 "Tags" 變更為 "Tags (M)"：

     <h2>Tags (M)</h2>

使用桌面瀏覽器及行動瀏覽器模擬器，瀏覽至標籤頁面。行動瀏覽器模擬器會顯示您所做的兩項變更。

![Show changes to tags page](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p2m_layouttags_mobile_thumb.png)

相較之下，桌面顯示則沒有變更。

![Show desktop tags view](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p2_layoutTagsDesktop_thumb.png)

使用 jQuery Mobile 來定義行動瀏覽器介面
---------------------------------------

本節將安裝 jQuery.Mobile.MVC NuGet 封裝，以便安裝 jQuery Mobile 與檢視切換器 Widget。

[jQuery Mobile](http://jquerymobile.com/demos/1.0b3/#/demos/1.0b3/docs/about/intro.html) 程式庫所提供的使用者介面架構能夠通用於所有主要的行動瀏覽器。jQuery Mobile 可將漸進式增強功能套用至支援 CSS 與 JavaScript 的行動瀏覽器上。漸進式增強功能可讓所有瀏覽器顯示網頁的基本內容，並讓更強大的瀏覽器與裝置顯示更豐富的內容。內含 jQuery Mobile 樣式的 JavaScript 與 CSS 檔案具有許多元素，無須進行任何標記變更即可適用於行動瀏覽器上。

1.  刪除您先前建立的 *Shared\\\_Layout.Mobile.cshtml* 檔案。

2.  將 *Views\\Home\\AllTags.Mobile.cshtml* 重新命名為 *Views\\Home\\AllTags.Mobile.cshtml.hide* (您稍後將再次使用此檔案)。由於該檔案不再具備 .cshtml 副檔名，因此無法被 ASP.NET MVC 執行階段用來呈現 *AllTags* 檢視。

3.  執行下列步驟以安裝 jQuery.Mobile.MVC NuGet 封裝：

    a. 在 **[工具]** 功能表中，依序選取 **[Package Manager Console]** 及 **[Library Package Manager]**。

         ![Library package manager][jquery1]

    b. 在 **[Package Manager Console]** 中，輸入 *Install-Package jQuery.Mobile.MVC -version 1.0.0*

         ![Package manager console][jquery2]

jQuery.Mobile.MVC NuGet 封裝會安裝下列項目：

-   *App\_Start\\BundleMobileConfig.cs* 檔案 - 您需要這個檔案以參照新增的 jQuery JavaScript 與 CSS 檔案。請遵循以下指示，並參照此檔案定義的行動套件。
-   jQuery Mobile CSS 檔案。
-   ViewSwitcher 控制器 Widget (*Controllers\\ViewSwitcherController.cs)*。
-   jQuery Mobile JavaScript 檔案。
-   jQuery Mobile 樣式配置檔案 (*Views\\Shared\_Layout.Mobile.cshtml*)。
-   View-switcher 部分檢視 (*MvcMobile\\Views\\Shared\_ViewSwitcher.cshtml*) - 會在每頁上方提供一個連結，供您在桌面檢視與行動檢視之間切換。
-   Content\\images 資料夾中的 Several .png 與 .gif 圖片檔。

開啟 *Global.asax* 檔案並將下列程式碼新增為 Application\_Start 方法的最後一行。

	BundleMobileConfig.RegisterBundles(BundleTable.Bundles);

下列程式碼顯示完整的 Global.asax 檔案。

	using System; 
	using System.Web.Http; 
	using System.Web.Mvc; 
	using System.Web.Optimization; 
	using System.Web.Routing; 
	using System.Web.WebPages; 
	 
	namespace MvcMobile 
	{ 
	 
	    public class MvcApplication : System.Web.HttpApplication 
	    { 
	        protected void Application_Start() 
	        { 
	            DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone") 
	            { 
	                ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf 
	                    ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0) 
	            }); 
	            AreaRegistration.RegisterAllAreas(); 
	 
	            WebApiConfig.Register(GlobalConfiguration.Configuration); 
	            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters); 
	            RouteConfig.RegisterRoutes(RouteTable.Routes); 
	            BundleConfig.RegisterBundles(BundleTable.Bundles); 
	            BundleMobileConfig.RegisterBundles(BundleTable.Bundles); 
	        } 
	    } 
	}

開啟 *MvcMobile\\Views\\Shared\\\_Layout.Mobile.cshtml* 檔案並將下列標記直接新增在 *Html.Partial* 呼叫之後：

	<div data-role="header" align="center">
	    @Html.ActionLink("Home", "Index", "Home")
	    @Html.ActionLink("Date", "AllDates")
	    @Html.ActionLink("Speaker", "AllSpeakers")
	    @Html.ActionLink("Tag", "AllTags")
	</div>

完整的內容區段如下所示：

	<body>
	    <div data-role="page" data-theme="a">
	        @Html.Partial("_ViewSwitcher")
	        <div data-role="header" align="center">
	            @Html.ActionLink("Home", "Index", "Home")
	            @Html.ActionLink("Date", "AllDates")
	            @Html.ActionLink("Speaker", "AllSpeakers")
	            @Html.ActionLink("Tag", "AllTags")
	        </div>
	        <div data-role="header">
	            <h1>@ViewBag.Title</h1>
	        </div>
	        <div data-role="content">
	            @RenderSection("featured", false)
	            @RenderBody()
	        </div>
	    </div>
	</body>

建立應用程式，並在您的行動瀏覽器模擬器中瀏覽至 AllTags 檢視。這時會顯示下列項目：

![After install jquery through nuget.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_afternuget_thumb.png)

**注意**

您可以將 IE 或 Chrome 的使用者代理程式字串設定為 iPhone，然後使用 F-12 開發人員工具，對行動專屬的程式碼進行偵錯程序。如果您的行動瀏覽器沒有顯示 **[主頁]**、**[演講者]**、**[標籤]** 與 **[日期]** 連結按鈕的話，則 jQuery Mobile 指令碼與 CSS 檔案的參照大概也不正確。

除了樣式變更之外，您可以檢視 **[Displaying mobile view]** 與可供您切換行動檢視與桌面檢視的連結。選擇 **[桌面檢視連結]**，這時桌面檢視隨即顯示。

桌面檢視並沒有提供您直接瀏覽到原本的行動檢視的方式。現在您將修正這個問題。開啟 *Views\\Shared\\\_Layout.cshtml* 檔案。將下列程式碼直接新增到 &lt;body\> 元素下方，以便顯示 view-switcher Widget：

    @Html.Partial("_ViewSwitcher")

以下為完成的程式碼：

	<body>
	    @Html.Partial("_ViewSwitcher")
	
	    <div id="title">
	        <h1> MVC4 Conference </h1>
	    </div>

		@*Items removed for clarity.*@
	</body>

重新整理行動瀏覽器中的 **[AllTags]** 檢視。現在您可以在桌面與行動檢視之間自由瀏覽。

![Navigate to mobile views.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_desktopviewwithmobilelink_thumb.png)

**注意**

當您使用已經使用者代理程式字串設為行動裝置的瀏覽器時，可以將下列程式碼新增至 Views\\Shared\\\_ViewSwitcher.cshtml 以便對檢視進行偵錯。

``` {}
    else 
    { 
         @:Not Mobile/Get 
    } 
```

然後將下列標題新增至 Views\\Shared\\\_Layout.cshtml 檔案。

``` {}
    <h1>Non Mobile Layout MVC4 Conference</h1>
```

瀏覽至桌面瀏覽器中的 [AllTags] 頁面。view-switcher Widget 並不會顯示在桌面瀏覽器中，因為它只能新增至行動配置頁面。本教學課程稍後將說明如何將 view-switcher Widget 新增至桌面檢視。

![View desktop experience.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Windows-Live-Writer_ASP_NET-MVC-4-Mobile-Features_D2FF_p3_desktopBrowser_thumb.png)

改善演講者清單
--------------

在行動瀏覽器中，選取 **[演講者]** 連結。由於沒有行動檢視 (*AllSpeakers.Mobile.cshtml*)，預設的演講者顯示內容 (*AllSpeakers.cshtml*) 會透過行動配置檢視 (*\_Layout.Mobile.cshtml*) 來呈現。

![View the mobile speakers list.](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersdesktop_thumb.png)

您可以在 *Views\_ViewStart.cshtml* 檔案中將 RequireConsistentDisplayMode 設為 true，以全域停用預設 (非行動) 檢視，使其無法在行動配置內顯示，如以下所示：

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_speakersconsistent_thumb.png)

    @{
    Layout = "~/Views/Shared/_Layout.cshtml";
    DisplayModes.RequireConsistentDisplayMode = true;
    }

當 *RequireConsistentDisplayMode* 設為 true，行動配置 (*\_Layout.Mobile.cshtml*) 僅能供行動檢視使用。(也就是說，檢視檔案格式為 ViewName.Mobile.cshtml。) 如果您的行動配置無法與您的非行動檢視搭配運作，您可能想要將 *RequireConsistentDisplayMode* 設為 true。當您將 *RequireConsistentDisplayMode* 設為 true 時，下方螢幕擷取畫面會顯示 [演講者] 頁面顯示方式。

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png)

您可以在檢視檔案中將 *RequireConsistentDisplayMode* 設為 false，以停用檢視中的一致顯示模式。*Views\\Home\\AllSpeakers.cshtml* 檔案中的下列標記會將 *RequireConsistentDisplayMode* 設為 false：

    @model IEnumerable<string>
    @{
    ViewBag.Title = "All speakers";
    DisplayModes.RequireConsistentDisplayMode = false;
    }

建立行動演講者檢視
------------------

如您適才所見，行動裝置上的 [演講者] 檢視已可讀取，但是連結卻非常微小而不容易點選。本節將仿效現代化行動應用程式建立行動裝置特定的 [演講者] 檢視，呈現出大尺寸、容易點選的連結，並內含搜尋方塊以便快速找到演講者。

1.  將 *AllSpeakers.cshtml* 複製到 *AllSpeakers.Mobile.cshtml*。開啟 *AllSpeakers.Mobile.cshtml* 檔案並移除 &lt;h2\> 標題元素。
2.  在 **&lt;ul\>** 索引標籤中，新增 data-role 屬性並將其值設為 *listview*。就像其他 *data-** attributes, \*data-role="listview"* 一樣，此舉可讓大型清單項目更容易點選。以下是完成的標記樣貌：

	    @model IEnumerable<string>
	    @{
	        ViewBag.Title = "All speakers";
	    }
	    <ul data-role="listview">
	        @foreach(var speaker in Model) {
	            <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
	        }
	    </ul>

3.  重新整理行動瀏覽器。更新的檢視如下所示：

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_updatedspeakerview1_thumb.png)

4.  在 **&lt;ul\>** 索引標籤中，新增 data-filter 屬性並將其設為 true。下方程式碼會顯示 ul 標記。

        <ul data-role="listview" data-filter="true">

下圖顯示 data-filter 屬性在頁面上方產生的搜尋篩選方塊。

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_thumb.png)

當您將個別字母輸入到搜尋方塊時，jQuery Mobile 會如下圖所示篩選清單。

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb.png)

改善標籤清單
------------

就像預設的 [演講者] 檢視，您可以讀取行動裝置上的 [標籤] 檢視，但是其連結卻非常細微而難以點選。本節將仿效 [演講者] 檢視修正方式，修正 [標籤] 檢視。

1.  將 *Views\\Home\\AllTags.Mobile.cshtml.hide* 檔案重新命名為 *Views\\Home\\AllTags.Mobile.cshtml*。開啟重新命名的檔案，並移除 **&lt;h2\>** 元素。

2.  將 data-role 與 data-filter 屬性新增至 **&lt;ul\>** 標籤，如以下所示：

         <ul data-role="listview" data-filter="true">

    下圖顯示會篩選字母 J 的標籤頁面。

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_tags_j_thumb.png)

改善日期清單
------------

您可以仿效 **[演講者]** 與 **[標籤]** 檢視的改善方式來改善 [日期] 檢視，以便更容易運用在行動裝置上。

1.  將 *Views\\Home\\AllDates.Mobile.cshtml* 檔案複製到 *Views\\Home\\AllDates.Mobile.cshtml*。
2.  開啟此新檔案，並移除 **&lt;h2\>** 元素。
3.  如以下所示將 *data-role="listview"* 新增至 &lt;ul\> 標籤：

         <ul data-role="listview">

下圖顯示內含 data-role 屬性的 **[日期]** 頁面樣貌。

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates1_thumb.png)

以下列程式碼取代 *Views\\Home\\AllDates.Mobile.cshtml* 檔案內容：

    @model IEnumerable<DateTime>
    @{
        ViewBag.Title = "All dates";
        DateTime lastDay = default(DateTime);
    }
    <ul data-role="listview">
        @foreach(var date in Model) {
            if (date.Date != lastDay) {
                lastDay = date.Date;
                <li data-role="list-divider">@date.Date.ToString("ddd, MMM dd")</li>
            }
            <li>@Html.ActionLink(date.ToString("h:mm tt"), "SessionsByDate", new { date })</li>
        }
    </ul>

此程式碼會依天數來群組化所有工作階段。它會為新的每一天建立清單分隔符號，並在分隔符號下方列出每一天的所有工作階段。以下是此程式碼執行時的樣貌：

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_dates2_thumb.png)

改善 SessionsTable 檢視
-----------------------

本節將建立行動裝置專屬的工作階段檢視。我們所做的變更，將比我們建立的其他檢視更為周延廣泛。

在行動瀏覽器中，點選 **[演講者]** 按鈕，然後在搜尋方塊中輸入 Sc。

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_1.png)

點選 **[Scott Hanselman]** 連結。

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png)

如您所見，行動瀏覽器上的顯示器不容易閱讀。日期欄不容易閱讀，而標籤欄也超出檢視範圍。若要修正此問題，將 Views\*Home\\SessionsTable.cshtml\* 複製到 *Views\\Home\\SessionsTable.Mobile.cshtml*，然後使用下列程式碼取代檔案內容：

    @using MvcMobile.Models
    @model IEnumerable<Session>
	
    <ul data-role="listview">
        @foreach(var session in Model) {
            <li>
                <a href="@Url.Action("SessionByCode", new { session.Code })">
                    <h3>@session.Title</h3>
                    <p><strong>@string.Join(", ", session.Speakers)</strong></p>
                    <p>@session.DateText</p>
                </a>
            </li>
        }
    </ul>

此程式碼會移除空間與標籤欄位，然後以垂直方式格式化標題、演講者與日期，以便在行動瀏覽器上輕鬆閱讀所有此類資訊。下圖反映了程式碼變更。

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_sessionsbyscottha_thumb.png)

改善 SessionByCode 檢視
-----------------------

最後，您將建立專屬行動裝置的 **SessionByCode** 檢視。在行動瀏覽器中，點選 **[演講者]** 按鈕，然後在搜尋方塊中輸入 Sc。

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_data_filter_sc_thumb_2.png)

點選 **[Scott Hanselman]** 連結。Scott Hanselman 工作階段隨即顯示。

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_scottha_thumb.png)

選擇 **[An Overview of the MS Web Stack of Love]** 連結。

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_ps_love_thumb.png)

預設的桌面檢視沒問題，但是您可以讓它更好。

將 *Views\\Home\\SessionByCode.cshtml* 複製到 *Views\\Home\\SessionByCode.Mobile.cshtml*，並使用下列標記取代 *Views\\Home\\SessionByCode.Mobile.cshtml* 檔案內容：

    @model MvcMobile.Models.Session
	
    @{
        ViewBag.Title = "Session details";
    }
    <h2>@Model.Title</h2>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>
	
    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Speakers</li>
        @foreach (var speaker in Model.Speakers) {
            <li>@Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker })</li>
        }
    </ul>
	
    <p>@Model.Description</p>
    <h4>Code: @Model.Code</h4>

    <ul data-role="listview" data-inset="true">
        <li data-role="list-divider">Tags</li>
        @foreach (var tag in Model.Tags) {
            <li>@Html.ActionLink(tag, "SessionsByTag", new { tag })</li>
        }
    </ul>

新的標記使用 **data-role** 屬性來改善檢視配置。

重新整理行動瀏覽器。下圖反映您剛做的程式碼變更：

![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/windows-live-writer_asp_net-mvc-4-mobile-features_d2ff_p3_love2_thumb.png)

將應用程式部署至 Azure 網站
---------------------------

1.  在 Visual Studio 的 **[方案總管]** 中以滑鼠右鍵按一下專案，再選取內容功能表中的 **[發行]**。

    ![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/PublishVSSolution.png)

    此時會開啟 **[發行 Web]** 精靈。

2.  在 **[Publish Web]** 精靈的 **[設定檔]** 索引標籤中，按一下 **[匯入]**。

    ![Import publish settings](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishSettings.png)

    **[匯入發行設定檔]** 對話方塊隨即出現。

3.  如果您之前尚未將 Azure 訂閱新增至 Visual Studio，請執行下列步驟。在以下步驟中，您將新增訂閱，以便讓 **[從 Azure 網站匯入]** 下方的下拉式清單納入您的網站。

    1.  在 **[匯入發行設定檔]** 對話方塊中，按一下 **[新增 Azure 訂閱]**。

    ![add win az sub](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzAddWAsub.png)

    1.  在 **[匯入 Azure 訂閱]** 對話方塊中，按一下 **[下載訂閱檔案]**。

    ![download sub](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)

    1.  在您的瀏覽器視窗中，儲存 *.publishsettings* 檔案。

    ![download pub file](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDown2.png)

    [WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)]

    1.  在 **[匯入 Azure 訂閱]** 對話方塊中，按一下 **[瀏覽]** 並瀏覽至 *.publishsettings* 檔案。

    ![download sub](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzDownLoad.png)

    1.  按一下 **[匯入]**。

    ![import](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rzImp.png)

    1.  在 **[匯入發行設定檔]** 對話方塊中，選取 **[從 Azure 網站匯入]**，接著從下拉式清單選取網站，再按一下 **[確定]**。

    ![Import Publish Profile](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ImportPublishProfile.png)

    1.  在 **[連線]** 索引標籤中，按一下 **[驗證連線]** 以確定設定正確。

    ![Validate connection](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/ValidateConnection.png)

    1.  連線一經驗證，**[驗證連線]** 按鈕旁會顯示綠色的勾選標記。

      ![connection successful icon and Next button in Connection tab](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-005.png) 

    2.  您可以接受此頁面上的所有預設設定。您正在部署「版本發行」組建組態，而且不需要刪除目的地伺服器上的檔案。[資料庫] 下方的 **UsersContext (DefaultConnection)** 項目**** 來自於使用 DefaultConnection 字串的 *UsersContext:DbContext* 類別。按 **[下一步]**。

    ![connection successful icon and Next button in Connection tab](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/rxPWS.png)

    1.  在 **[預覽]** 索引標籤中，按一下 **[開始預覽]**。此索引標籤會列出要複製至伺服器的檔案。顯示預覽並非要發行應用程式時的必要動作，但是是可以知道的有用功能。在此情況中，您不需要對顯示的檔案清單進行任何處理。當您下次發行時，唯有變更過的檔案會出現在預覽清單中。

    ![StartPreview button in the Preview tab](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/dntutmobile-deploy1-publish-007.png)

    1.  按一下 **[發行]**。

    Visual Studio 隨即開始進行將檔案複製至 Azure 伺服器的程序。**[輸出]** 視窗會顯示已採取的部署動作，並報告部署作業已順利完成。

    1.  預設瀏覽器會自動開啟已部署之網站的 URL。您建立的應用程式現在正在雲端中執行。

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/depoly_mobile_new_website_15.png)

您可以在行動瀏覽器中瀏覽至網站 URL，透過電話模擬器測試您的即時網站。

