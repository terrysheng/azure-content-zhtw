<properties 
	pageTitle="將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 應用程式部署至 Azure 網站" 
	description="了解如何開發 ASP.NET MVC 5 網站並且將 SQL Database 後端部署至 Azure。" 
	services="web-sites, sql-database" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="1/28/2015" 
	ms.author="riande"/> 



# 將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 5 應用程式部署至 Azure 網站

本教學課程將示範如何建立可讓使用者以 Facebook 或 Google 認證登入的安全 ASP.NET MVC 5 Web 應用程式。您也會將應用程式部署至 Azure。

您可以免費申請 Azure 帳戶，而且如果您還沒有 Visual Studio 2013，SDK 會自動安裝 Visual Studio 2013 for Web Express。您可以開始免費進行 Azure 相關開發。

本教學課程假設您先前沒有使用 Azure 的經驗。完成此教學課程後，您將有個安全的資料驅動 Web 應用程式已在雲端中啟動並執行、並已在使用雲端資料庫。

您將學到:

* 如何建立安全的 ASP.NET MVC 5 專案，並將該專案發行至 Azure 網站。
* 如何使用 [OAuth](http://oauth.net/ "http://oauth.net/") 與 ASP.NET 成員資格資料庫來確保您的應用程式安全。
* 如何使用 SQL 資料庫在 Azure 中儲存資料。

您將建立一個簡單的連絡人清單 Web 應用程式，該應用程式建立於 ASP.NET MVC 5 之上，並使用 ADO.NET Entity Framework 進行資料庫存取。下圖顯示完成之應用程式的登入頁面:

![登入頁面][rxb]

>[AZURE.NOTE] 若要完成此教學課程，您需要 Microsoft Azure 帳戶。如果您沒有這類帳戶，可以<a href="/zh-tw/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">啟用自己的 MSDN 訂戶權益</a>或是<a href="/zh-tw/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">申請免費試用</a>。如果您要在註冊帳戶前開始使用 Azure 網站，請移至 <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>，您可以在 Azure 網站中立即建立短期的免費 ASP.NET 簡易版網站。無需使用信用卡，也不用簽約。


在此教學課程中:

- [設定開發環境](#setupdevenv)
- [建立 ASP.NET MVC 5 應用程式][createapplication]
- [將應用程式部署至 Azure][deployapp1]
- [將資料庫新增至應用程式][adddb]
- [新增 OAuth 提供者][]
- [將應用程式部署至 Azure][deployapp11]
- [後續步驟][]


[AZURE.INCLUDE [僅安裝 sdk-2013](../includes/install-sdk-2013-only.md)]

若要針對 localhost 使用新的 SSL 憑證，您需要安裝 [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?LinkId=390521) 或更高版本。

<h2><a name="bkmk_createmvc4app"></a>建立 ASP.NET MVC 5 應用程式</h2>

### 建立專案

1. 從 **[檔案]** 功能表，按一下 **[新增專案]**。

	![[檔案] 功能表中的 [新增專案]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

1. 在 **[新增專案]** 對話方塊中，展開 **[C#]** 並選取 **[已安裝的範本]** 下的 **[Web]**，然後選取 **[ASP.NET Web 應用程式]**。

1. 將應用程式命名為 **ContactManager**，然後按一下 **[確定]**。

	![[新增專案] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
	**注意:** 請確定您輸入的是 "ContactManager"。您稍後將要複製的程式碼區塊會假設專案名稱為 ContactManager。 

1. 在 **[新增 ASP.NET 專案]** 對話方塊中，選取 **[MVC]** 範本。確認 **[驗證]** 是設為 **[個別使用者帳戶]**、已勾選 **[雲端主機]**，且已選取 **[網站]**。

	[New ASP.NET Project]![](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG) 對話方塊

1. 組態精靈將會根據  *ContactManager* 建議唯一名稱 (請參閱下圖)。選取您附近的區域。您可以使用 [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") 尋找最短延遲的資料中心。 
2. 如果您之前未建立過資料庫伺服器，請選取 **[建立新的伺服器]**，輸入資料庫使用者名稱和密碼。

	![設定 Azure 網站](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configAz.PNG)

如果您有資料庫伺服器，請用它來建立新的資料庫。資料庫伺服器是非常寶貴的資源，而且您通常會想要在同一部伺服器上建立多個資料庫來進行測試和開發，而非在每個資料庫中各建立一個資料庫伺服器。請確定您的網站和資料庫位於相同區域。

![設定 Azure 網站](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configWithDB.PNG)

### 設定頁首及頁尾


1. 在 **[方案總管]** 中，開啟 *Layout.cshtml* 資料夾中的  *Views\Shared* 檔案。

	![[方案總管] 中的 _Layout.cshtml][newapp004]

1. 以下列程式碼取代 *Layout.cshtml* 檔案中的標記。下面會將所做的變更以反白方式顯示。

<pre>
			&lt;!DOCTYPE html&gt;
			&lt;html&gt;
			&lt;head&gt;
			    &lt;meta charset="utf-8" /&gt;
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
			    &lt;title&gt;@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title&gt;
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head&gt;
			&lt;body&gt;
			    &lt;div class="navbar navbar-inverse navbar-fixed-top"&gt;
			        &lt;div class="container"&gt;
			            &lt;div class="navbar-header"&gt;
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                &lt;/button&gt;
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div&gt;
			            &lt;div class="navbar-collapse collapse"&gt;
			                &lt;ul class="nav navbar-nav"&gt;
			                    &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt;
			                &lt;/ul&gt;
			                @Html.Partial("_LoginPartial")
			            &lt;/div&gt;
			        &lt;/div&gt;
			    &lt;/div&gt;
			    &lt;div class="container body-content"&gt;
			        @RenderBody()
			        &lt;hr /&gt;
			        &lt;footer&gt;
			            &lt;p&gt;&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p&gt;
			        &lt;/footer&gt;
			    &lt;/div&gt;
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
			    @RenderSection("scripts", required: false)
			&lt;/body&gt;
			&lt;/html&gt;
</pre>


![程式碼變更](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### 在本機執行應用程式

1. 按 CTRL+F5 執行應用程式。

	應用程式首頁隨即出現在預設瀏覽器中。

	![在本機執行的網站](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

只需執行上述作業，即可建立稍後要部署至 Azure 的應用程式。 

## 對專案啟用 SSL ##

1. 啟用 SSL。在 [方案總管] 中，按一下 **ContactManager** 專案，然後按一下 F4 來顯示 [屬性] 對話方塊。將 **[SSL 已啟用]** 變更為 true。複製 **SSL URL**。除非您先前已建立 SSL 網站，否則 SSL URL 將會是 https://localhost:44300/。

	![啟用 SSL][rxSSL]
 
1. 在 [方案總管] 中，於 **Contact Manager** 專案上按一下滑鼠右鍵，然後按一下 **[屬性]**。
1. 在左側索引標籤中，按一下 **[Web]**。
1. 將 **[專案 URL]** 變更為使用 **SSL URL**，並儲存頁面 (Control S))。

	![啟用 SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. 驗證 Internet Explorer 是 Visual Studio 所啟動的瀏覽器，如下圖所示:

	![預設瀏覽器](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)

	瀏覽器選取器可讓您指定 Visual Studio 啟動的瀏覽器。

 	![瀏覽器選取器](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

	您可以選取多個瀏覽器，並讓 Visual Studio 在您進行變更時更新每個瀏覽器。如需詳細資訊，請參閱[在 Visual Studio 2013 中使用瀏覽器連結](http://www.asp.net/visual-studio/overview/2013/using-browser-link).


1. 按 CTRL+F5 執行應用程式。遵循指示來信任 IIS Express 產生的自我簽署憑證。

	 ![信任 IIS Express 產生的自我簽署憑證的指示](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

1. 閱讀 **[安全性警告]** 對話方塊，如果您要安裝代表 **localhost** 的憑證，請按一下 **[是]**。

 	![localhost IIS Express 憑證警告](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1. IE 會顯示*Home*，且不會出現 SSL 警告。

	 ![IE 出現 localhost SSL，沒有出現警告](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)

	Google Chrome 也會接受憑證，並且不出現警告便顯示 HTTPS 內容。Firefox 會使用自己的憑證存放區，因此會顯示警告。

	 ![FireFox 憑證警告](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

<h2><a name="bkmk_deploytowindowsazure1"></a>將應用程式部署至 Azure</h2>

1. 在 Visual Studio 中，於 **[方案總管]** 中的專案上按一下滑鼠右鍵，然後選取操作功能表中的 **[發行]**。

	![在專案內容功能表中發行](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
   此時會開啟 **[發行 Web]** 精靈。

1. 在 **[發行 Web]** 對話方塊中，按一下 **[發行]**。

	![發行](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

	您建立的應用程式現在正在雲端中執行。下次您部署應用程式時，只會部署變更的 (或新的) 檔案。

	![在雲端中執行](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>將資料庫新增至應用程式</h2>

接下來，您將更新應用程式，以加上顯示及更新資料庫中的連絡人，以及在資料庫中儲存資料的功能。應用程式將會使用 Entity Framework (EF) 來建立資料庫以及讀取和更新資料。

### 新增連絡人的資料模型類別

首先，您會在程式碼中建立簡單的資料模型。

1. 在 **[方案總管]** 中，於 Models 資料夾上按一下滑鼠右鍵，按一下 **[新增]**，然後按一下 **[類別]**。

	![在模型資料夾內容功能表中加入類別](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. 在 **[加入新項目]** 對話方塊中，將新的類別檔案命名為  *Contact.cs*，然後按一下 **[新增]**。

	![[加入新項目] 對話方塊][adddb002]

3. 以下列程式碼取代 Contacts.cs 檔案的內容。

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
**Contacts** 類別定義您將為每個連絡人儲存的資料，加上資料庫需要的主要索引鍵 *ContactID*。

### 建立可讓應用程式使用者使用連絡人的網頁

ASP.NET MVC 樣板功能可自動產生程式碼來執行建立、讀取、更新和刪除 (CRUD) 動作。

<h2><a name="bkmk_addcontroller"></a>新增控制器和資料檢視</h2>

1. 建置專案 **(Ctrl+Shift+B)**。(使用樣板機制前必須先建置專案。) 
1. 在 **[方案總管]** 中，於 Controllers 資料夾上按一下滑鼠右鍵並按一下 **[新增]**，然後按一下 **[控制器]**。

	![在 [控制器] 資料夾內容功能表中加入控制器][addcode001]

5. 在 **[新增 Scaffold]** 對話方塊中，選取 **[使用 EF 且含檢視的 MVC 5 控制器]**，然後按一下 **[新增]**。
	
	![[新增 Scaffold] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)


1. 在 **[模型類別]** 下拉式方塊中選取 [**Contact (ContactManager.Models)**]。((請參閱下圖)。)
1. 在 **[資料內容類別]** 中，選取 [**ApplicationDbContext (ContactManager.Models)**]。**ApplicationDbContext** 將用於成員資格資料庫和我們的連絡人資料。
1. 在 **[控制器名稱]** 文字輸入方塊中，輸入 "CmController" 作為控制器名稱。 

	![[新增資料內容] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

1. 按一下 **[新增]**。

   Visual Studio 就會針對 **Contact** 物件的 CRUD 資料庫操作建立控制器方法與檢視。

## 啟用移轉、建立資料庫、新增範例資料和資料初始設定式 ##

下一個工作是啟用 [Code First 移轉](http://msdn.microsoft.com/library/hh770484.aspx)功能，以便根據您建立的資料模型建立資料庫。

1. 在 **[工具]** 功能表中，選取 **[NuGet 封裝管理員]**，然後選取 **[封裝管理員主控台]**。
	![[工具] 功能表中的 [封裝管理員主控台]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2. 在 **[封裝管理員主控台]** 視窗中，輸入下列命令:

		enable-migrations
	**enable-migrations** 命令會建立 *Migrations* 資料夾，並在該資料夾置入 *Configuration.cs* 檔案，您可以編輯該檔案來植入資料庫及設定「移轉」。 

2. 在 **[封裝管理員主控台]** 視窗中，輸入下列命令:

		add-migration Initial


	**add-migration Initial** 命令會在 *Migrations* 資料夾中產生名為 **&lt;date_stamp&gt;Initial** 的檔案，此檔案會建立資料庫。第一個參數 ( **Initial** ) 是一個任意參數，可用來建立檔案的名稱。您可以在 **[方案總管]** 中看到新的類別檔案。
	在 **Initial** 類別中，**Up** 方法會建立 Contacts 資料表，**Down** 方法 (當您希望回到前一個狀態時使用) 則會捨棄該資料表。
3. 開啟 *Migrations\Configuration.cs* 檔案。 
4. 新增下列命名空間。 

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

	此程式碼會以連絡人資訊初始化 (植入) 該資料庫。如需植入資料庫的詳細資訊，請參閱[植入及偵錯 Entity Framework (EF) 資料庫](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)。


6. 在 **[封裝管理員主控台]** 中輸入命令:

		update-database

	![封裝管理員主控台命令][addcode009]

	**update-database** 會執行第一次移轉，這會建立資料庫。根據預設，資料庫會以 SQL Server Express LocalDB 資料庫的形式建立。 

7. 按 CTRL+F5 執行應用程式，然後按一下 **[CM Demo]** 連結；或瀏覽到 http://localhost:(port#)/Cm。 

	應用程式隨即顯示種子資料並提供編輯、詳細資料和刪除連結。您可以建立、編輯、刪除及檢視資料。

	![MVC 資料檢視][rx2]



<h2><a name="addOauth"></a>新增 OAuth2 提供者</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") 是一種開放式通訊協定，可讓 Web、行動和桌面應用程式以簡單、標準的方法執行安全授權。ASP.NET MVC 網際網路範本使用 OAuth 來公開 Facebook、Twitter、Google 和 Microsoft 的驗證提供者身分。雖然本教學課程僅使用 Google 作為驗證提供者，但您可以很容易地修改程式碼來使用任何提供者。實作其他提供者的步驟，與您將在本教學課程中看到的步驟極為類似。若要使用 Facebook 作為驗證提供者，請參閱我的教學課程 [MVC 5 應用程式與 Facebook、Twitter、LinkedIn 和 Google OAuth2 登入](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)。

除了驗證，本教學課程也會使用角色來實作授權。只有您新增至 *canEdit* 角色的使用者才能變更資料 (也就是建立、編輯或刪除連絡人)。

遵循在我的教學課程 [MVC 5 應用程式與 Facebook、Twitter、LinkedIn 和 Google OAuth2 登入 ](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog)中，**建立 OAuth2 的 Google 應用程式，以設定 OAuth2 的 Google 應用程式**下的指示。執行和測試應用程式，以確認您可以使用 Google 驗證登入。

<h2><a name="mbrDB"></a>使用成員資格 API</h2>
在本節中，您會將本機使用者和 *canEdit* 角色新增至成員資格資料庫。只有 *canEdit* 角色中的使用者才能編輯資料。最佳做法是依角色可執行的動作來命名角色，因此將角色命名為 *canEdit* 會較命名為 *admin* 更好。隨著應用程式發展，您可以新增如 *canDeleteMembers* 等新角色，而非新增較欠缺描述性的 *superAdmin*。

1. 開啟 *migrations\configuration.cs* 檔案並新增下列  `using` 陳述式:

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. 將下列 **AddUserAndRole** 方法新增至類別:

    
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

1. 從 **Seed** 方法呼叫新方法:
	<pre>
        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            <mark>AddUserAndRole(context);</mark>
            context.Contacts.AddOrUpdate(p => p.Name,
                // Code removed for brevity
        }
	</pre>  
<span></span>
	下圖顯示對 *Seed* 方法的變更:

	![程式碼影像](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)

   此程式碼會建立名為 *canEdit* 的新角色、建立新的本機使用者 *user1@contoso.com*，並將 *user1@contoso.com* 新增至 *canEdit* 角色。如需詳細資訊，請參閱 [ASP.NET 身分識別資源頁面](http://curah.microsoft.com/55636/aspnet-identity)。

## 使用暫時程式碼，將新的社交登入使用者新增至 canEdit 角色 ##
在本節中，您將在帳戶控制器中暫時修改 **ExternalLoginConfirmation** 方法，以將使用 OAuth 提供者註冊的新使用者新增至 *canEdit* 角色。我們會暫時修改 **ExternalLoginConfirmation** 方法，以將新使用者自動新增至管理角色。在我們提供工具來新增及管理角色前，我們將暫時使用以下的自動註冊程式碼。我們希望將來能提供類似 [WSAT](http://msdn.microsoft.com/library/ms228053.aspx) 的工具，讓您能建立及編輯使用者帳戶與角色。稍後在本教學課程中我將告訴您如何使用 **[伺服器總管]** 新增使用者至角色。  

1. 開啟 **Controllers\AccountController.cs** 檔案並瀏覽到 **ExternalLoginConfirmation** 方法。
1. 將下列呼叫新增至位於 **SignInAsync** 呼叫前面的 **AddToRoleAsync**。

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   上述程式碼會將新註冊的使用者新增至 "canEdit" 角色，讓這些使用者能存取用來變更 (編輯) 資料的動作方法。下圖顯示程式碼變更：

   ![程式碼](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

稍後在本教學課程中，您會將應用程式部署至 Azure，並在後者使用 Google 或其他協力廠商驗證提供者來登入。這會將您新註冊的帳戶新增至 *canEdit* 角色。任何人只要有網站 URL 和 Google ID，都可以註冊及更新您的資料庫。若要防止其他人這麼做，您可以停止網站。您可以檢查資料庫來確認 *canEdit* 角色中有誰。

在 **[封裝管理員主控台]** 中，點擊向上鍵以顯示下列命令:

		Update-Database

執行 **Update-Database** 命令，以執行 **Seed** 方法並進而執行您剛剛新增的 **AddUserAndRole**。**AddUserAndRole** 將建立使用者 *user1@contoso.com*，並將她新增至 *canEdit* 角色。

## 使用 SSL 和 Authorize 屬性保護應用程式 ##

在本節中，您將套用 [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 屬性來限制對動作方法的存取。匿名使用者只能檢視首頁控制器的 **Index** 動作方法。註冊的使用者能夠查看連絡人資料 (Cm 控制器的 **[索引]** 和 **[詳細資料]** 頁面)、[關於] 及 [連絡人] 頁面。只有  *canEdit* 角色中的使用者才能存取用來變更資料的動作方法。

1. 將 [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 篩選器和 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 篩選器新增至應用程式。替代的方法是將 [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 屬性和 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 屬性新增至每個控制器，但將這些屬性套用至整個應用程式是最安全的做法。藉由全面新增這些屬性，您所新增的每個新控制器和動作方法都會自動受到保護，而不需要您記得哪些已套用、哪些未套用。如需詳細資訊，請參閱[保護您的 ASP.NET MVC 應用程式和新 AllowAnonymous 屬性](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx)。開啟 *App_Start\FilterConfig.cs* 檔案，並以下列程式碼 (其會新增兩個篩選器): 取代  *RegisterGlobalFilters* 方法：
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>




	上述程式碼中套用的 [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) 篩選器將防止匿名使用者存取應用程式中的任何方法。您將使用 [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 屬性來選擇略過一些方法中的授權需求，讓匿名使用者可登入及檢視首頁。使用 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 時，所有對 Web 應用程式的存取都必須透過 HTTPS 進行。

1. 將 [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 屬性新增至首頁控制器的 **Index** 方法。[AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) 屬性能讓您將想要選擇略過授權的方法加到白名單。下圖顯示 HomeController 的片段內容:	

  	![程式碼](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

2. 全域搜尋 *AllowAnonymous*，您就會發現帳戶控制器的登入與註冊方法中都有使用。
1. 在 *CmController.cs* 中，將 `[Authorize(Roles = "canEdit")]` 新增至 *Cm* 控制器中用來變更資料的 HttpGet 和 HttpPost 方法 (Create、Edit、Delete，也就是 Index 和 Details 外的每個動作方法)。以下顯示完整程式碼的片段內容: 

   	![程式碼影像](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)



1. 如果您在上一個工作階段仍保持登入狀態，請點擊 **[登出]** 連結。
1. 按一下 **[關於]** 或 **[連絡人]** 連結。因為匿名使用者無法檢視那些頁面，所以系統會將您重新導向至登入頁面。 
1. 按一下 **[註冊為新使用者]** 連結並新增電子郵件為 *joe@contoso.com* 的本機使用者。確認 *Joe* 可檢視 [首頁]、[關於] 和 [連絡人] 頁面。 

	![登入](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

1. 按一下 *CM Demo* 連結並確認您有看到資料。 
1. 按一下頁面上的編輯連結，系統會將您重新導向至登入頁面 (因為未將新的本機使用者新增至 *canEdit* 角色)。
1. 以 *user1@contoso.com* 的身分和密碼 "P_assw0rd1" ("word" 中的 "0" 是數字零) 登入。系統隨即將您重新導向到先前選取的編輯頁面。 <br/>
   如果無法以該帳戶和密碼登入，請嘗試複製並貼上原始程式碼中的密碼。如果仍然無法登入，請檢查 **AspNetUsers** 資料表的 **UserName** 欄以確認已新增  *user1@contoso.com*。 

1. 確認您可進行資料變更。

<h2><a name="bkmk_deploytowindowsazure11"></a>將應用程式部署至 Azure</h2>

1. 在 Visual Studio 中，於 **[方案總管]** 中的專案上按一下滑鼠右鍵，然後選取操作功能表中的 **[發行]**。

	![在專案內容功能表中發行][firsdeploy003]

	此時會開啟 **[發行 Web]** 精靈。

1. 按一下 **[發行 Web]** 對話方塊左側的 **[設定]** 索引標籤。按一下 **[v]** 圖示，以選取 **ApplicationDbContext** 的 **[遠端連線字串]**，然後選取 **ContactManagerNN_db**。

   
	![設定](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. 在 **ContactManagerContext** 下選取 **[執行 Code First 移轉]**。

	![設定](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. 按一下 **[發行]**。
1. 以 *user1@contoso.com* (使用密碼 "P_assw0rd1") 身分登入，並確認您可以編輯資料。
1. 登出。
1. 移至 [Google 開發人員主控台](https://console.developers.google.com/)，並在 **[認證]** 索引標籤上更新重新導向 URI 和 JavaScript Orgins 以使用 Azure URL。
1. 使用 Google 或 Facebook 登入。這會將 Google 或 Facebook 帳戶新增至 **canEdit** 角色。如果您收到 HTTP 400 錯誤與訊息*「要求中的重新導向 URI: https://contactmanager{my version}.azurewebsites.net/signin-google 與已註冊的重新導向 URI 不符。您必須等到您所做的變更都已傳播。」*若您在超過 1 分鐘後收到此錯誤，請確認 URI 是否正確。

### 停止網站以防止其他人註冊  

1. 在 **[伺服器總管]** 中，瀏覽至 **[網站]**。
4. 在每個網站執行個體上按一下滑鼠右鍵，然後選取 **[停止網站]**. 

	![停止網站](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png) 

	或者，您可以從 Azure 管理入口網站中選取網站，然後按一下頁面下方的 **[停止]** 圖示。

	![停止網站](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### 移除 AddToRoleAsync、發行和測試

1. 在帳戶控制器中，從 **ExternalLoginConfirmation** 方法註解化下列程式碼或將程式碼移除: 
                `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. 建置專案 (如此會儲存檔案變更並確認沒有任何編譯錯誤)。
5. 在 **[方案總管]** 中以滑鼠右鍵按一下專案，然後選取 **[發行]**。

	   ![在專案內容功能表中發行](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. 按一下 **[開始預覽]** 按鈕。只會部署需要更新的檔案。
5. 從 Visual Studio 或透過入口網站啟動網站。**您無法在網站停止時發行**。

	![啟動網站](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

5. 返回 Visual Studio 並按一下 **[發行]**。
3. 您的 Azure 應用程式隨即在預設瀏覽器中開啟。如果您已登入，請登出，以便您可以使用匿名使用者的身分檢視首頁。  
4. 按一下 **[關於]** 連結。系統會將您重新導向至 [登入] 頁面。
5. 按一下 [登入] 頁面上的 **[註冊]** 連結並建立本機帳戶。我們將使用此本機帳戶，確認您可以存取唯讀頁面但無法存取用來變更資料的頁面 (這些頁面受 *canEdit* 角色保護)。本教學課程稍後將移除本機帳戶存取權。 

	![註冊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

1. 確認您可以瀏覽 [*About*] 和 [*Contact*] 頁面。

	![登出](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

1. 按一下 **CM Demo** 連結以瀏覽到 **Cm** 控制器。或者，您也可以在 URL 後加上 *Cm*。 

	![CM 頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. 按一下 [編輯] 連結。系統會將您重新導向至登入頁面。在 **[使用其他服務登入]** 下，按一下 [Google] 或 [Facebook]，並以您先前註冊的帳戶登入。((如果您的動作夠快，同時工作階段 Cookie 未逾時，將使用您先前所使用的 Google 或 Facebook 帳戶為您自動登入。)
2. 確認您在登入該帳戶後可編輯資料。
 	**注意:**您無法在從此應用程式登出 Google 後，又使用相同瀏覽器登入不同的 Google 帳戶。若是使用一個瀏覽器，則必須瀏覽到 Google 再登出。您可以使用不同瀏覽器，以同一協力廠商驗證者 (例如 Google) 的其他帳戶登入。

如果您並未填寫 Google 帳戶資訊中的姓名，則會發生 NullReferenceException。


## 檢查 SQL Azure DB ##

1. 在 **[伺服器總管]** 中，瀏覽到 **ContactDB**
2. 在 **ContactDB** 上按一下滑鼠右鍵並選取 **[在 SQL Server 物件總管中開啟]**。
 
	![在 SSOX 中開啟](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)
 
**注意:**若您無法展開 **[SQL 資料庫]**，並且在 Visual Studio 中*can't*看到 **ContactDB**，則必須依照下列指示開放一或多個防火牆連接埠。遵循 **[設定 Azure 防火牆規則]** 中的指示。新增防火牆規則後，您可能必須等候數分鐘才能存取資料庫。
 
1. 在 **[AspNetUsers]** 資料表上按一下滑鼠右鍵並選取 **[檢視資料]**。

	![CM 頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. 記下您以 Google 帳戶註冊且要加到 **canEdit** 角色中的識別碼，以及 *user1@contoso.com* 的識別碼。這些應為 **canEdit** 角色中僅有的使用者((您將會在下一個步驟進行確認。)

	![CM 頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. 在 **[SQL Server 物件總管]** 中，於 **AspNetUserRoles** 上按一下滑鼠右鍵，並選取 **[檢視資料]**。

	![CM 頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
確認所列的 **UserId** 屬於 *user1@contoso.com* 和您註冊的 Google 帳戶。 


## 設定 Azure 防火牆規則 ##

如果您無法從 Visual Studio 連線至 SQL Azure，或收到指出"無法開啟伺服器"的錯誤對話方塊時，請遵循本節的步驟。

![防火牆錯誤](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

您需要將您的 IP 位址新增至允許的 IP。

1. 在 Azure 入口網站中，選取左側索引標籤中的 **[SQL 資料庫]**。

	![選取 SQL][rx6]

1. 按一下 **ContactDB**。

1. 按一下 **[設定此 IP 位址的 Azure 防火牆規則]** 連結。

	![防火牆規則][rx7]

1. 當系統提示您"現有的防火牆規則不含目前的 IP 位址 xxx.xxx.xxx.xxx。是否要更新防火牆規則？"時，請按一下 **[是]**。在一些公司防火牆後新增此位址通常並不夠，您將需要新增某個範圍的 IP 位址。

下一步是新增允許的 IP 位址範圍。

1. 在 Azure 入口網站中，按一下 **[SQL 資料庫]**。
1. 選取 **[伺服器]** 索引標籤，然後按一下您要設定的伺服器。

	![Azure 中的 [伺服器] 索引標籤](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

1. 按一下 **[設定]** 索引標籤。

1. 新增規則名稱以及開頭與結尾 IP 位址。

	![IP 範圍][rx9]

1. 按一下頁面底部的 **[儲存]**。
1. 若您需要新增 IP 位址範圍來連線，請留下意見反應來讓我知道。

最後，您可以從 SQL Server 物件總管 (SSOX) 連線至 SQL Database 執行個體

1. 從 [檢視] 功能表，按一下 **[SQL Server 物件總管]**。
1. 在 **[SQL Server]** 上按一下滑鼠右鍵，然後選取 **[加入 SQL Server]**。
1. 在 **[連線到伺服器]** 對話方塊中，將 **[驗證]** 設為 **[SQL Server 驗證]**。您會從 Azure 入口網站取得 **[伺服器名稱]** 和 **[登入]**。
1. 在瀏覽器中，瀏覽到入口網站並選取 **[SQL 資料庫]**。
1. 選取 **ContactDB**，然後按一下 **[檢視 SQL 資料庫連線字串]**。
1. 從 **[連線字串]** 頁面中，複製 **[伺服器]** 和 **[使用者識別碼]**。
 
	![連接字串](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)
1. 將 **[伺服器]** 和 **[使用者識別碼]** 值貼到 Visual Studio 中的 **[連線到伺服器]** 對話方塊。**[使用者識別碼]** 值會填入到 **[登入]** 項目。接著輸入密碼來建立 SQL DB。

	![[連接至伺服器] 對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

您現在可依照稍早提供的指示，瀏覽到連絡人 DB。


## 編輯資料庫資料表來將使用者新增至 canEdit 角色

在本教學課程稍早的章節中，您使用程式碼將使用者新增至 canEdit 角色。替代的方法是直接在成員資格資料表中操作資料。下列步驟顯示如何使用此替代方法，將使用者新增至角色。

2. 在 **[SQL Server 物件總管]** 中，於 **AspNetUserRoles** 上按一下滑鼠右鍵，並選取 **[檢視資料]**。

	![CM 頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. 複製 *RoleId* 並將其貼至空白 (新) 資料列。
	
	![CM 頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. 在 **[AspNetUsers]** 資料表中尋找要置於角色中的使用者，複製該使用者的*Id*，然後將它貼至 **[AspNetUserRoles]** 資料表的 **[UserId]** 資料欄。

我們正在開發一項工具，讓使用者和角色管理起來簡單得多。


<h2><a name="nextsteps"></a>後續步驟</h2>

請遵循以此範例為基礎的教學課程:

1.	[建立具備登入、電子郵件確認及密碼重設功能之安全的 ASP.NET MVC 5 Web 應用程式](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[使用 SMS 和電子郵件雙因素驗證的 ASP.NET MVC 5 應用程式](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[將密碼和其他機密資料部署到 ASP.NET 和 Azure 的最佳做法](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)
4.	[建立使用 Facebook 及 Google OAuth2 的 ASP.NET MVC 5 應用程式](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on )包含關於如何新增設定檔資料至使用者註冊 DB 的指示，以及關於使用 Facebook 作為驗證提供者的詳細指示。
5.	[開始使用 ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

若要啟用此教學課程頂端顯示的社交登入按鈕，請參閱 [ASP.NET MVC 5 的美觀社交登入按鈕](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/)。

Tom Dykstra's 見解精闢的[開始使用 EF 和 MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) 將示範更進階的 MVC 和 EF 程式設計。

本教學課程和範例應用程式是由 [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT))) 在 Tom Dykstra 和 Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) 的協助下所撰寫。 

如果您發現喜歡的地方或希望我們改善的地方 (不論是針對本教學課程或課程中示範的產品)，都***歡迎留下意見反應***。您的意見反應將協助我們訂出優先改善要務。您也可以在[使用程式碼示範](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code)提出並對新主題進行投票。

<!-- bookmarks -->
[新增 OAuth 提供者]: #addOauth
[使用成員資格 API]:#mbrDB
[建立資料部署指令碼]:#ppd
[更新成員資格資料庫]:#ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png

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

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png


[有關 Azure 網站中 ASP.NET 的重要資訊]: #aspnetwindowsazureinfo
[後續步驟]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png


























<!--HONumber=42-->
