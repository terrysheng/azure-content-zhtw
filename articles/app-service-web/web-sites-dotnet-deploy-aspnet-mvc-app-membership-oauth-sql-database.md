<properties 
	pageTitle="使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式並部署至 Azure App Service" 
	description="了解如何開發具有 SQL Database 後端的 ASP.NET MVC 5 應用程式、加入驗證和授權，並將它部署至 Azure。" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/07/2015" 
	ms.author="riande"/>

# 使用驗證和 SQL DB 建立 ASP.NET MVC 應用程式並部署至 Azure App Service

本教學課程將示範如何建立可讓使用者以 Facebook 或 Google 認證登入的安全 ASP.NET MVC 5 Web 應用程式。此應用程式是簡單的連絡人清單，會針對資料庫存取使用 ADO.NET Entity Framework。您會將應用程式部署到 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)。

完成此教學課程後，您將有個安全的資料驅動 Web 應用程式在雲端中上線運作，並已在使用雲端資料庫。下圖顯示完成之應用程式的登入頁面：

![登入頁面][rxb]

您將了解：

* 如何在 Visual Studio 中建立安全的 ASP.NET MVC 5 Web 專案。
* 如何驗證和授權使用 Google 或 Facebook 帳戶認證登入的使用者 (使用 [OAuth 2.0](http://oauth.net/2 "http://oauth.net/2") 的社交提供者驗證)。
* 如何驗證及授權使用者，這些使用者會在應用程式管理的資料庫中登錄 (使用 [ASP.NET 身分識別](http://asp.net/identity/)的本機驗證)。
* 如何使用 ADO.NET Entity Framework 6 Code First 在 SQL Database 中讀取和寫入資料。
* 如何使用 Entity Framework Code First 移轉來部署資料庫。
* 如何使用 Azure SQL Database 在雲端中儲存關聯式資料。
* 如何部署 Web 專案，以便將資料庫用於 Azure App Service 中的 [Web 應用程式](http://go.microsoft.com/fwlink/?LinkId=529714)。

>[AZURE.NOTE]這個教學課程時間很長。如果您想快速認識 Azure App Service 和 Visual Studio Web 專案，請參閱[在 Azure App Service 中建立 ASP.NET Web 應用程式](web-sites-dotnet-get-started.md)。如需疑難排解資訊，請參閱[疑難排解](#troubleshooting)一節。
>
>或者，如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期的入門 Web 應用程式。不需要信用卡；無需承諾。

## 必要條件

若要完成此教學課程，您需要 Microsoft Azure 帳戶。如果您沒有這類帳戶，可以[啟用自己的 MSDN 訂戶權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)或是[申請免費試用](/pricing/free-trial/?WT.mc_id=A261C142F)。

若要設定開發環境，您必須安裝 [Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkId=390521) 或更高版本，以及最新版本的 [Azure SDK for .NET](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409)。本文專為 Visual Studio Update 4 和 SDK 2.7.1 所撰寫。相同的指示也適用於已安裝最新 [Azure SDK for.NET](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409) 的 Visual Studio 2015，但是有些畫面看起來和插圖不同。

## 建立 ASP.NET MVC 5 應用程式

### 建立專案

1. 從 [檔案] 功能表，按一下 [新增專案]。

	![[檔案] 功能表中的 [新增專案]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/gs13newproj.png)

1. 在 [新增專案] 對話方塊中，展開 [C#] 並選取 [已安裝的範本] 下的 [Web]，再選取 [ASP.NET Web 應用程式]。

1. 將應用程式命名為 **ContactManager**，然後再按一下 [確定]。

	![New Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13newprojdb.png)
 
	**注意：**請確定您輸入的是 ContactManager。您稍後將要複製的程式碼區塊會假設專案名稱為 ContactManager。

1. 在 [新增 ASP.NET 專案] 對話方塊中，選取 [MVC] 範本。確認已將 [驗證] 設為 [個別使用者帳戶]、已勾選 [雲端主機]，且已選取 [Web 應用程式]。

	![[New ASP.NET Project] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newproject.png)

1. 按一下 [確定]。

3. 當 [設定 Microsoft Azure Web 應用程式設定] 對話方塊出現時，請確定您已登入 Azure：如果您還沒有登入，請登入，如果您的登入已過期，請重新輸入您的認證。

	![重新輸入認證](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/reentercredentials.png)

2. 如果您想要指定 web 應用程式的名稱，請變更 [Web 應用程式名稱] 方塊中的值。

	Web 應用程式的 URL 將會是 {name}.azurewebsites.net，因此該名稱在 azurewebsites.net 網域中必須是唯一的。組態精靈建議唯一的名稱是將數字附加至專案名稱 "ContactManager"，這也適用於本教學課程。

5. 在 [App Service 計劃] 下拉式清單中，選取 [建立新的 App Service 計劃] 並輸入名稱，例如下圖所示的 "StandardWeb"。

	您也可以依照意願選取您已有的 App Service 計劃。如需 App Service 方案的資訊，請參閱 [Azure App Service 方案深入概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。

5. 在 [資源群組] 下拉式清單中，選取 [建立新的資源群組] 並輸入名稱，例如下圖所示的 "ExampleMVC"。

	您也可以依照意願選取您已有的資源群組。但如果您建立新的資源群組並僅用於此教學課程，就可在您完成時輕易刪除您為教學課程所建立的所有 Azure 資源。如需資源群組的資訊，請參閱 [Azure 資源管理員概觀](../resource-group-overview.md)。

7. 選取您附近的區域。

	還不要按 [確定]。在下一個步驟中，您將設定資料庫資源。此對話方塊現在看起來像下圖。

	![新方案和資源群組](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newplanandgroup.png)
 
2. 選取 [建立新的伺服器]，輸入伺服器名稱、使用者名稱和密碼。

	伺服器名稱必須是唯一的。它可以包含小寫字母、數字和連字號。它不能包含結尾連字號。使用者名稱和密碼是您要為新伺服器建立的新認證。

	如果您已經有資料庫伺服器，您可以選取該伺服器而不是建立一個。資料庫伺服器是非常寶貴的資源，通常您會想要在相同伺服器上建立多個資料庫進行測試和開發，而非在每個資料庫上建立資料庫伺服器。不過，在此教學課程中，您只是暫時需要伺服器，而且藉由在相同的資源群組中建立伺服器，您可以在完成教學課程時輕易地刪除 web 應用程式和資料庫資源。

	如果您選取現有的資料庫伺服器，請確定您的 web 應用程式和資料庫位於相同的區域。

	![使用新資料庫](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newdb.png)

4. 按一下 [確定]。

	Visual Studio 會建立 ContactManager web 專案、建立資源群組和您指定的 App Service 計劃，並在您指定名稱的 Azure App Service 中建立 web 應用程式。

### 設定頁首及頁尾

1. 在 [方案總管]，開啟 *Views\\Shared* 資料夾中的 *Layout.cshtml* 檔案。

	![方案總管中的 \_Layout.cshtml][newapp004]

1. 以下列程式碼取代 *Layout.cshtml* 檔案的內容。

		<!DOCTYPE html>
		<html>
		<head>
		    <meta charset="utf-8" />
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <title>@ViewBag.Title - Contact Manager</title>
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		
		</head>
		<body>
		    <div class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		                @Html.ActionLink("CM Demo", "Index", "Cm", new { area = "" }, new { @class = "navbar-brand" })
		            </div>
		            <div class="navbar-collapse collapse">
		                <ul class="nav navbar-nav">
		                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
		                    <li>@Html.ActionLink("About", "About", "Home")</li>
		                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
		                </ul>
		                @Html.Partial("_LoginPartial")
		            </div>
		        </div>
		    </div>
		    <div class="container body-content">
		        @RenderBody()
		        <hr />
		        <footer>
		            <p>&copy; @DateTime.Now.Year - Contact Manager</p>
		        </footer>
		    </div>
		
		    @Scripts.Render("~/bundles/jquery")
		    @Scripts.Render("~/bundles/bootstrap")
		    @RenderSection("scripts", required: false)
		</body>
		</html>

	此程式碼會變更頁首和頁尾的應用程式名稱，從 "My ASP.NET Application" 和 "Application name" 變更為 "Contact Manager" 和 "CM Demo"。
 
### 在本機執行應用程式

1. 按 CTRL+F5 執行應用程式。

	應用程式首頁隨即出現在預設瀏覽器中。

	![本機執行的 Web 應用程式](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr2.png)

只需執行上述作業，即可建立稍後要部署至 Azure 的應用程式。

## 將應用程式部署至 Azure

1. 在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下專案，再選取內容功能表中的 [發行]。

	![專案內容功能表中的 [發行]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
	此時會開啟 [發行 Web] 精靈。

1. 在 [發行 Web] 對話方塊中，按一下 [發行]。

	![Publish](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr3.png)

	您建立的應用程式現在正在雲端中執行。下次您部署應用程式時，只會部署變更的 (或新的) 檔案。

	![在雲端中執行](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss4.PNG)

## 對專案啟用 SSL ##

1. 在 [方案總管] 中，按一下 [ContactManager] 專案，再按一下 F4 開啟 [屬性] 視窗。

3. 將 [SSL 已啟用] 變更為 **True**。

4. 複製 [SSL URL]。

	除非您先前已建立 SSL Web 應用程式，否則 SSL URL 將是 https://localhost:44300/。

	![enable SSL][rxSSL]
 
1. 在 [方案總管] 中，以滑鼠右鍵按一下 [ContactManager] 專案，再按一下 [屬性]。

1. 按一下 [Web] 索引標籤。

1. 將 [專案 URL] 變為使用 [SSL URL]，並且儲存頁面 (Control S)。

	![enable SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr1.png)
 
1. 驗證 Internet Explorer 是 Visual Studio 所啟動的瀏覽器，如下圖所示：

	![預設瀏覽器](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss12.PNG)

	瀏覽器選取器可讓您指定 Visual Studio 啟動的瀏覽器。您可以選取多個瀏覽器，並讓 Visual Studio 在您進行變更時更新每個瀏覽器。如需詳細資訊，請參閱[在 Visual Studio 2013 中使用瀏覽器連結](http://www.asp.net/visual-studio/overview/2013/using-browser-link)。

 	![瀏覽器選取器](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss13.png)

1. 按 CTRL+F5 執行應用程式。按一下 [是] 以開始信任 IIS Express 產生之自我簽署憑證的程序。

	 ![信任 IIS Express 產生的自我簽署憑證的指示](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss26.PNG)

1. 閱讀 [安全性警告] 對話方塊，如果您要安裝代表 **localhost** 的憑證，請按一下 [是]。

 	![localhost IIS Express 憑證警告](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss27.PNG)

1. IE 顯示*首頁*，沒有出現 SSL 警告。

	 ![IE 出現 localhost SSL，沒有出現警告](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss28.PNG)

	 當您使用 SSL 時，Internet Explorer 是個不錯的選擇，因為它會接受憑證並顯示 HTTPS 內容而不會發出警告。Microsoft Edge 和 Google Chrome 也接受該憑證。Firefox 會使用自己的憑證存放區，因此會顯示警告。

	 ![FireFox 憑證警告](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss30.PNG)

## 新增資料庫至應用程式

接下來，您將更新應用程式，以新增顯示和更新資料庫中的連絡人，以及在資料庫中儲存資料的能力。應用程式會使用 Entity Framework (EF) 來建立資料庫及讀取和更新資料。

### 新增連絡人的資料模型類別

首先，您會在程式碼中建立簡單的資料模型。

1. 在 [方案總管]，於 Models 資料夾上按一下滑鼠右鍵，按一下 [新增]，再按一下 [類別]。

	![在 Models 資料夾內容功能表中新增類別](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr5.png)

2. 在 [加入新項目] 對話方塊中，將新的類別檔案命名為 *Contact.cs*，再按一下 [新增]。

	![[加入新項目] 對話方塊][adddb002]

3. 以下列程式碼取代 Contact.cs 檔案的內容。

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
            {
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }
**Contact** 類別定義您將為每個連絡人儲存的資料，加上資料庫需要的主要索引鍵 *ContactID*。

### 建立可讓應用程式使用者使用連絡人的網頁

ASP.NET MVC 樣板功能可自動產生程式碼來執行建立、讀取、更新和刪除 (CRUD) 動作。

1. 建置專案 **(Ctrl+Shift+B)**。(使用樣板機制前必須先建置專案)。
 
1. 在 [方案總管]，於 Controllers 資料夾上按一下滑鼠右鍵，按一下 [新增]，再按一下 [控制器]。

	![在 Controllers 資料夾內容功能表中新增控制器][addcode001]

5. 在 [Add Scaffold] 對話方塊中，選取 [MVC 5 Controller with views, using EF]，再按一下 [新增]。
	
	![[Add Scaffold] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr6.png)

1. 在 [模型類別] 下拉式方塊中選取 [Contact (ContactManager.Models)]。(請參閱下圖)。

1. 在 [資料內容類別] 中選取 [ApplicationDbContext (ContactManager.Models)]。[ApplicationDbContext] 將用於成員資格 DB 和我們的連絡人資料。

1. 在 [控制器名稱] 文字輸入方塊中，輸入 "CmController" 作為控制器名稱。

	![新增資料內容對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss5.PNG)

1. 按一下 [新增]。

   Visual Studio 會利用 **Contact** 物件的 CRUD 資料庫操作方法與檢視來建立控制器。

## 啟用移轉、建立資料庫、新增範例資料和資料初始設定式 ##

下一個工作是啟用 [Code First 移轉](http://msdn.microsoft.com/library/hh770484.aspx)功能，以便根據您建立的資料模型來建立資料庫。

1. 在 [工具] 功能表中，依序選取 [NuGet Package Manager] 及 [Package Manager Console]。

	![[工具] 功能表中的 Package Manager Console](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/SS6.png)

2. 在 [Package Manager Console] 視窗中，輸入下列命令：

		enable-migrations

	**enable-migrations** 命令會建立 *Migrations* 資料夾，並在該資料夾置入 *Configuration.cs* 檔案，您可以編輯該檔案來植入資料庫及設定 [移轉]。

2. 在 [Package Manager Console] 視窗中，輸入下列命令：

		add-migration Initial


	**add-migration Initial** 命令會在 *Migrations* 資料夾中產生名為 **&lt;date\_stamp&gt;Initial** 的檔案。這個檔案中的程式碼會建立資料庫資料表。第一個參數 (**Initial**) 可用來建立檔案的名稱。您可以在 [方案總管] 中看到新的類別檔案。

	在 **Initial** 類別中，**Up** 方法會建立 Contacts 資料表，**Down** 方法 (當您希望返回前個狀態時使用) 則會捨棄該資料表。

3. 開啟 *Migrations\\Configuration.cs* 檔案。

4. 加入下列 `using` 陳述式。

    	 using ContactManager.Models;

5. 以下列程式碼取代 *Seed* 方法：

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                }
                );
        }

	此程式碼會以連絡人資訊初始化 (植入) 該資料庫。如需植入資料庫的詳細資訊，請參閱[植入及偵錯 Entity Framework (EF) DB](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)。

6. 在 [Package Manager Console] 中輸入命令：

		update-database

	![Package Manager Console commands][addcode009]

	**update-database** 會執行第一次移轉，使資料庫建立。根據預設，資料庫會以 SQL Server Express LocalDB 資料庫的形式建立。

7. 按 CTRL+F5 執行應用程式，再按一下 [**CM Demo**] 連結；或瀏覽 https://localhost:(port#)/Cm。

	應用程式隨即顯示種子資料並提供編輯、詳細資料和刪除連結。您可以建立、編輯、刪除及檢視資料。

	![資料的 MVC 檢視][rx2]

## 新增 OAuth2 提供者

>[AZURE.NOTE]如需如何使用 Google 和 Facebook 開發人員入口網站的詳細指示，此教學課程可連結至 ASP.NET 網站上的教學課程。不過，Google 和 Facebook 變更其網站的頻率高於這些教學課程的更新速度，而且現在已過期。如果您在遵循下列指示上遇到問題，請在本教學課程結尾的精選 Disqus 註解查看已變更項目的清單。

[OAuth](http://oauth.net/ "http://oauth.net/") 是一種開放式通訊協定，可讓 Web、行動和桌面應用程式以簡單、標準的方法執行安全授權。ASP.NET MVC 網際網路範本使用 OAuth 來公開 Facebook、Twitter、Google 和 Microsoft 的驗證提供者身分。雖然本教學課程僅使用 Google 作為驗證提供者，但您可以輕易修改程式碼來使用任何其中一個提供者。實作其他提供者的步驟，與您在本教學課程中看到的步驟極為類似。若要使用 Facebook 作為驗證提供者，請參閱[使用 Facebook、Twitter、LinkedIn 和 Google OAuth2 登入的 MVC 5 應用程式](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)。

除了驗證，本教學課程使用角色來實作授權。只有您新增至 *canEdit* 角色的使用者才能變更資料 (也就是建立、編輯或刪除連絡人)。

1. 依照 [建立 Google app for OAuth 2 以設定 Google app for OAuth2] 下 [使用 Facebook、Twitter、LinkedIn 和 Google OAuth2 登入的 MVC 5 App][](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog) 的指示進行。

3. 執行與測試應用程式，以確認您可以使用 Google 驗證登入。

2. 如果您想要建立具有特定提供者圖示的社交登入按鈕，請參閱[美觀的 ASP.NET MVC 5 社交登入按鈕](http://www.jerriepelser.com/blog/pretty-social-login-buttons-for-asp-net-mvc-5)

## 使用成員資格 API

在本節中，您會將本機使用者和 *canEdit* 角色新增至成員資格資料庫。只有 *canEdit* 角色中的使用者才能編輯資料。最佳做法是依角色可執行的動作來命名角色，因此將角色命名為 *canEdit* 會較命名為 *admin* 更好。隨著應用程式發展，您可以新增如 *canDeleteMembers* 等新角色，而非新增較欠缺描述性的 *superAdmin*。

1. 開啟 *migrations\\configuration.cs* 檔案並新增下列 `using` 陳述式：

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. 將下列 **AddUserAndRole** 方法新增至類別：

		bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
		{
		    IdentityResult ir;
		    var rm = new RoleManager<IdentityRole>
		        (new RoleStore<IdentityRole>(context));
		    ir = rm.Create(new IdentityRole("canEdit"));
		    var um = new UserManager<ApplicationUser>(
		        new UserStore<ApplicationUser>(context));
		    var user = new ApplicationUser()
		    {
		        UserName = "user1@contoso.com",
		    };
		    ir = um.Create(user, "P_assw0rd1");
		    if (ir.Succeeded == false)
		        return ir.Succeeded;
		    ir = um.AddToRole(user.Id, "canEdit");
		    return ir.Succeeded;
		}

1. 從 **Seed** 方法呼叫新方法：

		protected override void Seed(ContactManager.Models.ApplicationDbContext context)
		{
		    AddUserAndRole(context);
		    context.Contacts.AddOrUpdate(p => p.Name,
		        // Code removed for brevity
		}

	下圖顯示對 *Seed* 方法的變更：

	![程式碼影像](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss24.PNG)

	此程式碼會建立名為 *canEdit* 的新角色、建立新本機使用者 **user1@contoso.com*，並將 **user1@contoso.com* 新增至 *canEdit* 角色。如需詳細資訊，請參閱 ASP.NET 網站上的 [ASP.NET 身分識別教學課程](http://www.asp.net/identity/overview/features-api)。

## 使用暫時程式碼，將新的社交登入使用者新增至 canEdit 角色  ##

在本節中，您將在帳戶控制器中暫時修改 **ExternalLoginConfirmation** 方法，以將使用 OAuth 提供者註冊的新使用者新增至 *canEdit* 角色。我們希望將來能提供類似 [WSAT](http://msdn.microsoft.com/library/ms228053.aspx) 的工具，讓您能建立及編輯使用者帳戶與角色。在那之前，您可以使用暫時程式碼來完成相同的功能。

1. 開啟 **Controllers\\AccountController.cs** 檔案並瀏覽到 **ExternalLoginConfirmation** 方法。

1. 將下列呼叫新增至就在 **SignInAsync** 呼叫前面的 **AddToRoleAsync**。

		await UserManager.AddToRoleAsync(user.Id, "canEdit");

   上述程式碼會將新註冊的使用者新增至 "canEdit" 角色，讓這些使用者能存取用來變更 (編輯) 資料的動作方法。下列程式碼片段會顯示內容中新的一行程式碼。

		  // POST: /Account/ExternalLoginConfirmation
		  [HttpPost]
		  [AllowAnonymous]
		  [ValidateAntiForgeryToken]
		  public async Task ExternalLoginConfirmation(ExternalLoginConfirmationViewModel model, string returnUrl)
		  {
		     if (User.Identity.IsAuthenticated)
		     {
		        return RedirectToAction("Index", "Manage");
		     }
		     if (ModelState.IsValid)
		     {
		        // Get the information about the user from the external login provider
		        var info = await AuthenticationManager.GetExternalLoginInfoAsync();
		        if (info == null)
		        {
		           return View("ExternalLoginFailure");
		        }
		        var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
		        var result = await UserManager.CreateAsync(user);
		        if (result.Succeeded)
		        {
		           result = await UserManager.AddLoginAsync(user.Id, info.Login);
		           if (result.Succeeded)
		           {
		              await UserManager.AddToRoleAsync(user.Id, "canEdit");
		              await SignInManager.SignInAsync(user, isPersistent: false, rememberBrowser: false);
		              return RedirectToLocal(returnUrl);
		           }
		        }
		        AddErrors(result);
		     }
		     ViewBag.ReturnUrl = returnUrl;
		     return View(model);
		  }

稍後在本教學課程中，您會將應用程式部署至 Azure，並在後者使用 Google 或其他協力廠商驗證提供者來登入。這會將您新註冊的帳戶新增至 *canEdit* 角色。任何人只要有您 Web 應用程式的 URL 和 Google ID，就可以註冊及更新您的資料庫。若要防止其他人這麼做，您可以停止網站。您可以檢查資料庫來確認 *canEdit* 角色中有誰。

在 [Package Manager Console] 點擊向上鍵以帶出下列命令：

		Update-Database

**Update-database** 命令會執行**植入**方法，並藉此執行您先前新增的 **AddUserAndRole** 方法。**AddUserAndRole** 方法會建立使用者 **user1@contoso.com*，並將她新增至 *canEdit* 角色。

## 使用 SSL 和 Authorize 屬性保護應用程式 ##

在本節中，您會套用 [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 屬性來限制對動作方法的存取。匿名使用者只能檢視首頁控制器的 **Index** 動作方法。註冊的使用者能夠查看連絡人資料 (Cm 控制器的 [索引] 和 [詳細資料] 頁面)、[關於] 頁面和 [連絡人] 頁面。只有 *canEdit* 角色中的使用者才能存取用來變更資料的動作方法。

1. 開啟 *App\_Start\\FilterConfig.cs* 檔案，並以下列程式碼 (其會新增兩個篩選器) 取代 *RegisterGlobalFilters* 方法：

		public static void RegisterGlobalFilters(GlobalFilterCollection filters)
		{
		    filters.Add(new HandleErrorAttribute());
		    filters.Add(new System.Web.Mvc.AuthorizeAttribute());
		    filters.Add(new RequireHttpsAttribute());
		}
		
	此程式碼會將 [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 篩選器和 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 篩選器新增至應用程式。[Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 篩選器會防止匿名使用者存取應用程式中的任何方法。您將使用 [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 屬性來選擇略過一些方法中的授權需求，讓匿名使用者可登入及檢視首頁。使用 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 時，所有對 Web 應用程式的存取都必須透過 HTTPS 進行。

	替代的方法是將 [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 屬性和 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 屬性新增至每個控制器，但將這些屬性套用至整個應用程式是最安全的做法。藉由全面新增這些屬性，您所新增的每個新控制器和動作方法都會自動受到保護，而不需要您記得套用它們。如需詳細資訊，請參閱[保護您的 ASP.NET MVC 應用程式和新 AllowAnonymous 屬性](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx)。

1. 將 [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 屬性新增至首頁控制器的 **Index** 方法。[AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 屬性能讓您將想要選擇略過授權的方法加到白名單。

		public class HomeController : Controller
		{
		  [AllowAnonymous]
		  public ActionResult Index()
		  {
		     return View();
		  }

	如果您全面搜尋 *AllowAnonymous*，您將會發現帳戶控制器的登入與註冊方法中都使用了它。

1. 在 *CmController.cs* 中，將 `[Authorize(Roles = "canEdit")]` 新增至 *Cm* 控制器中用來變更資料的 HttpGet 和 HttpPost 方法 (Create、Edit、Delete，也就是 Index 和 Details 外的每個動作方法)。以下顯示完整程式碼的片段內容：

		// GET: Cm/Create
		[Authorize(Roles = "canEdit")]
		public ActionResult Create()
		{
		   return View(new Contact { Address = "123 N 456 W",
		    City="Great Falls", Email = "ab@cd.com", Name="Joe Smith", State="MT",
		   Zip = "59405"});
		}
		// POST: Cm/Create
		// To protect from overposting attacks, please enable the specific properties you want to bind to, for 
		// more details see http://go.microsoft.com/fwlink/?LinkId=317598.
		[HttpPost]
		[ValidateAntiForgeryToken]
		 [Authorize(Roles = "canEdit")]
		public ActionResult Create([Bind(Include = "ContactId,Name,Address,City,State,Zip,Email")] Contact contact)
		{
		    if (ModelState.IsValid)
		    {
		        db.Contacts.Add(contact);
		        db.SaveChanges();
		        return RedirectToAction("Index");
		    }
		    return View(contact);
		}
		// GET: Cm/Edit/5
		[Authorize(Roles = "canEdit")]
		public ActionResult Edit(int? id)
		{
		    if (id == null)
		    {
		        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
		    }
		    Contact contact = db.Contacts.Find(id);
		    if (contact == null)
		    {
		        return HttpNotFound();
		    }
		    return View(contact);
		}
		
1. 按 CTRL+F5 執行應用程式。

1. 如果您在前個工作階段仍保持登入狀態，請點擊 [登出] 連結。

1. 按一下 [關於] 或 [連絡人] 連結。因為匿名使用者無法檢視那些頁面，所以系統會將您重新導向至登入頁面。

1. 按一下 [註冊為新使用者] 連結，並使用電子郵件 **joe@contoso.com* 新增本機使用者。確認 *Joe* 可檢視 [首頁]、[關於] 和 [連絡人] 頁面。

	![登入](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss14.PNG)

1. 按一下 [CM Demo] 連結並確認看到資料。

1. 按一下頁面上的編輯連結，系統會將您重新導向至登入頁面 (因為未將新的本機使用者新增至 *canEdit* 角色)。

1. 使用 **user1@contoso.com* 的身分和密碼 "P\_assw0rd1" ("word" 中的 "0" 是數字零) 登入。系統隨即將您重新導向到先前選取的編輯頁面。

	如果無法以該帳戶和密碼登入，請嘗試複製並貼上原始程式碼中的密碼。如果仍然無法登入，請檢查 [AspNetUsers] 資料表的 [UserName] 欄，確認已新增 **user1@contoso.com*。

1. 確認您可進行資料變更。

## 將應用程式部署至 Azure

1. 在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下專案，再選取內容功能表中的 [發行]。

	![專案內容功能表中的 [發行]][firsdeploy003]

	此時會開啟 [發行 Web] 精靈。

1. 按一下 [發行 Web] 對話方塊左側的 [設定] 索引標籤。

2. 按一下 **v** 圖示以選取 [ApplicationDbContext] 的 [遠端連接字串]，並且選取您建立專案時建立的資料庫。
   
	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc2.png)

1. 在 [ContactManagerContext] 下選取 [Execute Code First Migrations]。

	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc3.png)

1. 按一下 [發佈]。

1. 以 **user1@contoso.com* (使用密碼 "P\_assw0rd1") 身分登入，並確認您可以編輯資料。

1. 登出。

1. 請移至 [[Google Developers Console](https://console.developers.google.com/)]，並在 [**認證**] 索引標籤上更新 [重新導向 URI] 和 [JavaScript Orgins] 以使用 Azure URL。

1. 使用 Google 或 Facebook 登入。如此將使 Google 或 Facebook 帳戶新增至 **canEdit** 角色。如果您收到 HTTP 400 錯誤訊息：*要求中的重新導向 URI：https://contactmanager{my https://contactmanager{my version}.azurewebsites.net/signin-google 與已註冊的重新導向 URI 不符。*，您必須等到您所做的變更都已傳播為止。如果您是在超過數分鐘之後收到此錯誤，請確認 URI 是正確的。

### 停止 Web 應用程式以防止其他人註冊  

1. 在 [伺服器總管] 中，瀏覽至 **Azure > App Service > {您的資源群組} > {您的 web 應用程式}**。

4. 以滑鼠右鍵按一下 Web 應用程式，然後選取 [停止]。

	或者，您也可以從 [Azure Preview 入口網站][](https://portal.azure.com/) 移至 web 應用程式的刀鋒視窗，然後按一下刀鋒視窗頂端的**停止**圖示。

	![停止 Web 應用程式入口網站](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/stopweb.png)

### 移除 AddToRoleAsync、發行和測試

1. 在帳戶控制器中，從 **ExternalLoginConfirmation** 方法註解化下列程式碼或將其移除：

		await UserManager.AddToRoleAsync(user.Id, "canEdit");

1. 建置專案 (如此會儲存檔案變更並確認沒有任何編譯錯誤)。

5. 在 [方案總管] 中以滑鼠右鍵按一下專案，再選取 [發行]。

	   ![專案內容功能表中的 [發行]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
4. 按一下 [開始預覽] 按鈕。只會部署需要更新的檔案。

5. 從 Visual Studio 或透過入口網站啟動 Web 應用程式。**您無法在 Web 應用程式停止時進行發行**。

	![啟動 Web 應用程式](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss15.png)

5. 返回 Visual Studio 並按一下 [發行]。

3. 您的 Azure 應用程式隨即在預設瀏覽器中開啟。如果您已登入，請登出，以便您可以使用匿名使用者的身分檢視首頁。

4. 按一下 [關於] 連結。系統會將您重新導向至 [登入] 頁面。

5. 按一下 [登入] 頁面上的 [註冊] 連結並建立本機帳戶。我們將使用此本機帳戶，確認您可以存取唯讀頁面但無法存取用來變更資料的頁面 (這些頁面受 *canEdit* 角色保護)。本教學課程稍後將移除本機帳戶存取權。

	![註冊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss16.PNG)

1. 確認您可瀏覽到 [關於] 和 [連絡人] 頁面。

	![登出](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss17.PNG)

1. 按一下 [CM Demo] 連結並瀏覽到 **Cm** 控制器。或者，您也可以在 URL 後加上 *Cm*。

	![CM 頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr4.png)
 
1. 按一下 [編輯] 連結。

	系統會將您重新導向至 [登入] 頁面。

2. 在 [Use another service to log in] 下按一下 [Google] 或 [Facebook]，並以您先前註冊的帳戶登入。(如果您的動作夠快，同時工作階段 Cookie 未逾時，將使用您先前所使用的 Google 或 Facebook 帳戶為您自動登入。)

2. 確認您在登入該帳戶後可編輯資料。

	**注意：**您無法在從此應用程式登出 Google 後，又使用相同瀏覽器登入不同的 Google 帳戶。若是使用一個瀏覽器，則必須瀏覽到 Google 再登出。您可以使用不同瀏覽器，以自己在相同協力廠商驗證者 (例如 Google) 那邊的其他帳戶登入。

	如果您並未填寫 Google 帳戶資訊中的姓名，則會發生 NullReferenceException。

## 檢查 SQL Azure DB ##

1. 在 [伺服器總管] 中，瀏覽至 **Azure > SQL Databases > {您的資料庫}**

2. 以滑鼠右鍵按一下資料庫，然後選取 [在 SQL Server 物件總管中開啟]。
 
	![在 SSOX 中開啟](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr12.png)
 
3. 若您先前未曾連線至此資料庫，系統將提示您新增防火牆規則，以允許您從目前 IP 位址存取資料庫。IP 位址將預先填入。只需按一下 [新增防火牆規則] 即可存取。

	![新增防火牆規則](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/addfirewallrule.png)

3. 使用您建立資料庫伺服器時指定的使用者名稱和密碼來登入資料庫。
 
1. 以滑鼠右鍵按一下 [AspNetUsers] 資料表並選取 [檢視資料]。

	![CM 頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr8.png)
 
1. 記下您以 Google 帳戶註冊且要加到 **canEdit** 角色中的識別碼，以及 **user1@contoso.com* 的識別碼。這些應為 **canEdit** 角色中唯一的使用者(您將會在下一個步驟進行確認)。

	![CM 頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s2.png)
 
2. 在 [SQL Server 物件總管]，於 [AspNetUserRoles] 上按一下滑鼠右鍵，再選取 [檢視資料]。

	![CM 頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rs1.png)
 
3. 確認所列的 **UserId** 屬於 **user1@contoso.com* 和您註冊的 Google 帳戶。

## 疑難排解

如果您碰到問題，以下是一些如何著手的建議。

* 佈建 SQL Database 時發生錯誤 - 請確定您已安裝最新的 SDK。2.7.1 之前的版本有個錯誤，在某些案例中，VS 嘗試建立資料庫伺服器或資料庫時會造成錯誤。
* 建立 Azure 資源時出現錯誤訊息「您的訂用帳戶優惠類型不支援此作業」- 與上述相同。
* 部署時發生錯誤 - 請考慮瀏覽[基本 ASP.NET 部署](web-sites-dotnet-get-started.md)一文。部署案例比較簡單，而且如果您有相同的問題，可能會比較容易隔離。例如，在某些企業環境中，公司防火牆可能會阻止 Web Deploy 對 Azure 進行它所需的連接類型。
* 部署時發佈 Web 精靈中沒有選項可選取連接字串 - 如果您使用不同的方法來建立您的 Azure 資源 (例如，您嘗試部署建立在入口網站中的 web 應用程式和 SQL 資料庫)，SQL 資料庫可能無法與 web 應用程式建立關聯。最簡單的解決方案是使用 VS 來建立新的 web 應用程式和資料庫，如教學課程中所示。您不需要從頭開始本教學課程 -- 在發佈 Web 精靈中，您可以選擇建立新的 web 應用程式，並取得與您建立專案時所取得的相同 Azure 資源建立對話方塊。
* Google 或 Facebook 開發人員入口網站的說明已過期 - 請參閱本教學課程結尾處的精選 Disqus 註解。

## 後續步驟

您已建立可驗證使用者的基本 ASP.NET MVC web 應用程式。如需有關一般驗證工作以及如何保護機密資料的詳細資訊，請參閱下列教學課程。

- [建立安全的 ASP.NET MVC 5 Web 應用程式，並重設登入、電子郵件確認與密碼](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
- [使用 SMS 和電子郵件雙因素驗證的 ASP.NET MVC 5 應用程式](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
- [將密碼和其他機密資料部署至 ASP.NET 和 Azure 的最佳做法](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
- [使用 Facebook 和 Google OAuth2 建立 ASP.NET MVC 5 App](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on) 這包括如何將設定檔資料新增至使用者註冊 DB 的指示，並取得使用 Facebook 作為驗證提供者的詳細指示。
- [開始使用 ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

如需如何使用 Entity Framework 的進階教學課程，請參閱[開始使用 EF 和 MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)。

本教學課程是由 [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) 在 Tom Dykstra 和 Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) 的協助下所撰寫。

如果您發現喜歡的地方或希望我們改善的地方 (不論是針對本教學課程或其示範的產品)，***歡迎留下意見反應***。您的意見反應將協助我們訂出優先改善要務。您也可以在[告訴我如何處理程式碼](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code) (英文) 提出並對新主題進行投票。

## 變更的項目

* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需 Azure 入口網站變更為 Azure Preview 入口網站的指南，請參閱：[瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Using the Membership API]: #mbrDB
[Create a Data Deployment Script]: #ppd
[Update the Membership Database]: #ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxSSL.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT2.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rr1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png

[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png

[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png

[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-migrations-package-manager-console.png


[Important information about ASP.NET in Azure web apps]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
 

<!---HONumber=Oct15_HO4-->