<properties urlDisplayName="Website with SQL Database" pageTitle="將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 應用程式部署至 Azure 網站 " metaKeywords="Azure hello world 教學課程, Azure 入門教學課程, SQL Database 教學課程, Azure .NET hello world 教學課程, Azure C# hello world 教學課程, SQL Azure C# 教學課程" description="Learn how to develop an ASP.NET MVC 5 website with a SQL Database back-end deploy it to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title="Deploy a Secure ASP.NET MVC 5 app with Membership, OAuth, and SQL Database to an Azure Website" authors="riande"  solutions="" writer="riande" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="riande" /> 



# 將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 5 應用程式部署至 Azure 網站

***2014 年 10 月 12 日更新。***

本教學課程將示範如何建立可讓使用者以 Facebook 或 Google 認證登入的安全 ASP.NET MVC 5 Web 應用程式。您也會將應用程式部署至 Azure。

您可以免費申請 Azure 帳戶，而且如果您還沒有 Visual Studio 2013，SDK 會自動安裝 Visual Studio 2013 for Web Express。您可以開始免費進行 Azure 相關開發。

本教學課程假設您先前沒有使用 Azure 的經驗。完成此教學課程後，您將有個安全的資料驅動 Web 應用程式已在雲端中啟動並執行、並已在使用雲端資料庫。

您將了解：

* 如何建立安全的 ASP.NET MVC 5 專案，並將該專案發行至 Azure 網站。
* 如何使用 [OAuth](http://oauth.net/ "http://oauth.net/") 與 ASP.NET 成員資格資料庫來確保您的應用程式安全。
* 如何使用 SQL 資料庫在 Azure 中儲存資料。

您將建立一個簡單的連絡人清單 Web 應用程式，該應用程式建立於 ASP.NET MVC 5 之上，並使用 ADO.NET Entity Framework 進行資料庫存取。下圖顯示完成之應用程式的登入頁面：

![login page][rxb]

>[WACOM.NOTE] 若要完成此教學課程，您需要 Microsoft Azure 帳戶。如果您沒有這類帳戶，可以<a href="/zh-tw/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">啟用自己的 MSDN 訂戶權益</a>或是<a href="/zh-tw/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">申請免費試用</a>


本教學課程內容：

- [設定開發環境](#setupdevenv)
- [建立 ASP.NET MVC 5 應用程式][createapplication]
- [將應用程式部署至 Azure][deployapp1]
- [新增資料庫至應用程式][adddb]
- [新增 OAuth 提供者][]
- [將應用程式部署至 Azure][deployapp11]
- [後續步驟][]


[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

若要對 localhost 使用新的 SSL 憑證，您需要安裝 [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?LinkId=390521) 或更高版本。

<h2><a name="bkmk_createmvc4app"></a>建立 ASP.NET MVC 5 應用程式</h2>

### 建立專案

1. 從 [**檔案**] 功能表中，按一下 [**新增專案**]。

	![New Project in File menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

1. 在 [**新增專案**] 對話方塊中，展開 [**C#**]，並選取 [**已安裝的範本**] 下的 [**Web**]，然後選取 [**ASP.NET Web 應用程式**]。

1. 將應用程式命名為 **ContactManager**，然後按一下 [**確定**]。

	![New Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
	**注意：** 請確定您輸入 "ContactManager"。您稍後將要複製的程式碼區塊會假設專案名稱為 ContactManager。 

1. 在 [**New ASP.NET Project**] 對話方塊中，選取 [**MVC**] 範本。確認 [**驗證**] 已設為 [**個別使用者帳戶*]、[**Host in the cloud**] 已勾選，且已選取 [**網站**]。

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)

1. [組態] 精靈會根據 *ContactManager* 建議唯一名稱 (請參閱下圖)。選取您附近的區域。若要尋找最低延遲的資料中心，您可以使用 [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com")。 
2. 如果您之前尚未建立資料庫伺服器，請選取 [**建立新的伺服器**]，並輸入資料庫使用者名稱和密碼。

	![Configure Azure Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configAz.PNG)

如果您有資料庫伺服器，請用它來建立新的資料庫。資料庫伺服器是非常寶貴的資源，通常您會想要在相同伺服器上建立多個資料庫進行測試和開發，而非在每個資料庫上建立資料庫伺服器。請確定您的網站和資料庫位於相同的區域。

![Configure Azure Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configWithDB.PNG)

### 設定頁首及頁尾


1. 在 [**方案總管**] 中開啟 *Views\Shared* 資料夾內的 *Layout.cshtml* 檔案。

	![_Layout.cshtml in Solution Explorer][newapp004]

1. 使用下列程式碼來取代 *Layout.cshtml* 檔案中的標記。變更重點如下。

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
			    @RenderSection("scripts", required:false)
			&lt;/body&gt;
			&lt;/html&gt;
</pre>


![code changes](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### 在本機執行應用程式

1. 按 CTRL+F5 執行應用程式。

	應用程式首頁隨即出現在預設瀏覽器中。

	![Web site running locally](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

只需執行上述作業，即可建立稍後要部署至 Azure 的應用程式。 

## 對專案啟用 SSL ##

1. 啟用 SSL。在 [方案總管]，按一下 [**ContactManager**] 專案，再按一下 F4 帶出 [屬性] 對話方塊。將 [**SSL 已啟用**] 變更為 true。複製 [**SSL URL**]。除非您先前已建立 SSL 網站，否則 SSL URL 將是 https://localhost:44300/。

	![enable SSL][rxSSL]
 
1. 在 [方案總管]，於 [**Contact Manager**] 專案上按一下滑鼠右鍵，再按一下 [**屬性**]。
1. 在左側索引標籤中，按一下 [**Web**]。
1. 將 [**專案 URL**] 變更為使用 [**SSL URL**]，並儲存頁面 (Control S)。

	![enable SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. 驗證 Internet Explorer 是 Visual Studio 所啟動的瀏覽器，如下圖所示：

	![default browser](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)

	瀏覽器選取器可讓您指定 Visual Studio 啟動的瀏覽器。

 	![browser selector](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

您可以選取多個瀏覽器，並讓 Visual Studio 在您進行變更時更新每個瀏覽器。如需詳細資訊，請參閱 [在 Visual Studio 2013 中使用瀏覽器連結](http://www.asp.net/visual-studio/overview/2013/using-browser-link)。


1. 按 CTRL+F5 執行應用程式。遵循指示來信任 IIS Express 產生的自我簽署憑證。

	 ![instructions to trust the self-signed certificate that IIS Express has generated](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

1. 如果您想要安裝代表 **localhost** 的憑證，請閱讀 [**安全性警告**] 對話方塊，然後按一下 [**是**]。

 	![localhost IIS Express certificate warning ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1. IE 會顯示「首頁」，且不會出現 SSL 警告。

	 ![IE with localhost SSL and no warnings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)

Google Chrome 也會接受憑證，並且不出現警告便顯示 HTTPS 內容。Firefox 會使用自己的憑證存放區，因此會顯示警告。

	 ![FireFox Cert Warning](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

<h2><a name="bkmk_deploytowindowsazure1"></a>將應用程式部署至 Azure</h2>

1. 在 Visual Studio 的 [**方案總管**] 中以滑鼠右鍵按一下專案，再選取內容功能表中的 [**發佈**]。

	![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
[**發佈 Web**] 精靈隨即開啟。

1. 在 [**發佈 Web**] 對話方塊中，按一下 [**發佈**]。

	![Publish](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

您建立的應用程式現在正在雲端中執行。下次您部署應用程式時，只會部署變更的 (或新的) 檔案。

	![Running in Cloud](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>新增資料庫至應用程式</h2>

接下來，您將更新應用程式，以新增顯示和更新資料庫中的連絡人，以及在資料庫中儲存資料的能力。應用程式會使用 Entity Framework (EF) 來建立資料庫及讀取和更新資料。

### 新增連絡人的資料模型類別

首先，您會在程式碼中建立簡單的資料模型。

1. 在 [**方案總管**] 中，以滑鼠右鍵按一下 Models 資料夾，按一下 [**新增**]，然後按一下 [**類別**]。

	![Add Class in Models folder context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. 在 [**加入新項目**] 對話方塊中，將新的類別檔案命名為 *Contact.cs*，再按一下 [**新增**]。

	![Add New Item dialog box][adddb002]

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
**Contacts** 類別會定義您將為每個連絡人儲存的資料，加上資料庫需要的主要索引鍵 *ContactID*。

### 建立可讓應用程式使用者使用連絡人的網頁

ASP.NET MVC 樣板功能可自動產生程式碼來執行建立、讀取、更新和刪除 (CRUD) 動作。

<h2><a name="bkmk_addcontroller"></a>新增控制器和資料檢視</h2>

1. 建置專案 **(Ctrl+Shift+B)**。(使用樣板機制前必須先建置專案)。 
1. 在 [**方案總管**]，以滑鼠右鍵按一下 Controllers 資料夾，按一下 [**新增**]，然後按一下 [**Controller**]。

	![Add Controller in Controllers folder context menu][addcode001]

5. 在 [**Add Scaffold**] 對話方塊中，選取 [**MVC 5 Controller with views, using EF**]，再按一下 [**新増**]。
	
	![Add Scaffold dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)


1. 在 [**模型類別**] 下拉式方塊中，選取 [**Contact (ContactManager.Models)**]。(請參閱下圖)。
1. 在 [**資料內容類別**] 中選取 [**ApplicationDbContext (ContactManager.Models)**]。[**ApplicationDbContext**] 將用於成員資格 DB 和我們的連絡人資料。
1. 在 [**控制器名稱**] 文字輸入方塊中，輸入 "CmController" 作為控制器名稱。 

	![New data ctx dlg](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

1. 按一下 [**新增**]。

Visual Studio 隨即針對 **Contact** 物件的 CRUD 資料庫操作，建立控制器方法與檢視。

## 啟用移轉、建立資料庫、新增範例資料和資料初始設定式 ##

下一個工作是啟用 [Code First 移轉](http://msdn.microsoft.com/library/hh770484.aspx)功能，以便根據建立的資料模型建立資料庫。

1. 在 [**工具**] 功能表中，依序選取 [**NuGet 封裝管理員**] 和 [**Package Manager Console**]。
	![Package Manager Console in Tools menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2. 在 [**Package Manager Console**] 視窗中，輸入下列命令：

		enable-migrations
**enable-migrations** 命令會建立 *Migrations* 資料夾，並在該資料夾置入 *Configuration.cs* 檔案，您可以編輯該檔案來植入資料庫及設定 [移轉]。 

2. 在 [**Package Manager Console**] 視窗中，輸入下列命令：

		add-migration Initial


**add-migration Initial** 命令會在建立資料庫的 *Migrations* 資料夾中產生名為 **&lt;date_stamp&gt;Initial** 的檔案。第一個參數 (**Initial**) 是任意的，用於建立檔案的名稱。您可以在 [**方案總管**] 中看到新的類別檔案。
在 **Initial** 類別中，**Up** 方法會建立 Contacts 資料表，**Down** 方法 (當您希望返回前個狀態時使用) 則會捨棄該資料表。
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

此程式碼會以連絡人資訊來初始化 (植入) 該資料庫。如需植入資料庫的詳細資訊，請參閱[植入及偵錯 Entity Framework (EF) DBs](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)。


6. 在 [**Package Manager Console**] 中輸入下列命令：

		update-database

	![Package Manager Console commands][addcode009]

**update-database** 會執行第一次移轉，使資料庫建立。根據預設，資料庫會以 SQL Server Express LocalDB 資料庫的形式建立。 

7. 按 CTRL+F5 執行應用程式，再按一下 [**CM Demo**] 連結；或瀏覽到 http://localhost:(port#)/Cm。

應用程式隨即顯示種子資料並提供編輯、詳細資料和刪除連結。您可以建立、編輯、刪除及檢視資料。

	![MVC view of data][rx2]



<h2><a name="addOauth"></a>新增 OAuth2 提供者</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") 是一種開放式通訊協定，可讓 Web、行動和桌面應用程式以簡單、標準的方法執行安全授權。ASP.NET MVC 網際網路範本使用 OAuth 來公開 Facebook、Twitter、Google 和 Microsoft 的驗證提供者身分。雖然本教學課程僅使用 Google 作為驗證提供者，但您可以輕易修改程式碼來使用任何其中一個提供者。實作其他提供者的步驟，與您將在本教學課程中看到的步驟極為類似。若要使用 Facebook 作為驗證提供者，請參閱 [使用 Facebook、Twitter、LinkedIn 和 Google OAuth2 登入的 MVC 5 應用程式](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on) 教學課程。

除了驗證，本教學課程也會使用角色來實作授權。只有您新增至 *canEdit* 角色的使用者才能變更資料 (也就是建立、編輯或刪除連絡人)。

依照 [**建立 Google app for OAuth 2 以設定 Google app for OAuth2**] 下 [[使用 Facebook、Twitter、LinkedIn 和 Google OAuth2 登入的 MVC 5 應用程式](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog)] 的教學課程指示進行。執行與測試應用程式，以確認您可以使用 Google 驗證登入。

<h2><a name="mbrDB"></a>使用成員資格 API</h2>
在本節中，您會將本機使用者和 *canEdit* 角色新增至成員資格資料庫。只有 *canEdit* 角色中的使用者才能編輯資料。最佳做法是依角色可執行的動作來命名角色，因此將角色命名為 *canEdit* 會較命名為 *admin* 更好。隨著應用程式發展，您可以新增如 *canDeleteMembers* 等新角色，而非新增較欠缺描述性的 *superAdmin*。

1. 開啟 *migrations\configuration.cs* 檔案，並新增下列 `using` 陳述式：

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
	<pre>
        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            <mark>AddUserAndRole(context);</mark>
            context.Contacts.AddOrUpdate(p => p.Name,
                // 移除了程式碼以求簡單明瞭
        }
	</pre>  
<span></span>
	The following images shows the changes to *Seed* method:

	![code image](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)

   This code creates a new role called *canEdit*, creates a new local user *user1@contoso.com*, and adds *user1@contoso.com* to the *canEdit* role. For more information, see the [ASP.NET Identity resource page](http://curah.microsoft.com/55636/aspnet-identity).

## Use Temporary Code to Add New Social Login Users to the canEdit Role  ##
In this section you will temporarily modify the **ExternalLoginConfirmation** method in the Account controller to add new users registering with an OAuth provider to the *canEdit* role. We will temporarily modify the **ExternalLoginConfirmation** method to automatically add new users to an administrative role. Until we provide a tool to add and manage roles, we'll use the temporary automatic registration code below. We hope to provide a tool similar to [WSAT](http://msdn.microsoft.com/zh-tw/library/ms228053.aspx) in the future which allow you to create and edit user accounts and roles. Later in the tutorial I'll show how you can use **Server Explorer** to add users to roles.  

1. Open the **Controllers\AccountController.cs** file and navigate to the **ExternalLoginConfirmation** method.
1. Add the following call to **AddToRoleAsync** just before the **SignInAsync** call.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   The code above adds the newly registered user to the "canEdit" role, which gives them access to action methods that change (edit) data. An image of the code change is shown below:

   ![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

Later in the tutorial you will deploy the application to Azure, where you will log-on with Google or another third party authentication provider. This will add your newly registered account to the *canEdit* role. Anyone who finds your site's URL and has a Google ID can then register and update your database. To prevent other people from doing that, you can stop the site. You'll be able to verify who is in the *canEdit* role by examining the database.

In the **Package Manager Console** hit the up arrow key to bring up the following command:

		Update-Database

Run the  **Update-Database** command which will run the **Seed** method, and that will run the **AddUserAndRole** you just added. The **AddUserAndRole** will create the user *user1@contoso.com* and add her to the *canEdit* role.

## Protect the Application with SSL and the Authorize Attribute ##

In this section you will apply the [Authorize](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.authorizeattribute.aspx) attribute to restrict access to the action methods. Anonymous users will be able to view the **Index** action method of the home controller only. Registered users will be able to see contact data (The **Index** and **Details** pages of the Cm controller), the About, and the Contact pages. Only users in the *canEdit* role will be able to access action methods that change data.

1. Add the [Authorize](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.authorizeattribute.aspx) filter and the [RequireHttps](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.requirehttpsattribute.aspx) filter to the application. An alternative approach is to add the [Authorize](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.authorizeattribute.aspx) attribute and the [RequireHttps](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.requirehttpsattribute.aspx) attribute to each controller, but it's considered a security best practice to apply them to the entire application. By adding them globally, every new controller and action method you add will automatically be protected, you won't need to remember to apply them. For more information see [Securing your ASP.NET MVC  App and the new AllowAnonymous Attribute](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Open the *App_Start\FilterConfig.cs* file and replace the *RegisterGlobalFilters* method with the following (which adds the two filters):
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>


	The following image shows the changed code:


	The [Authorize](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.authorizeattribute.aspx) filter applied in the code above will prevent anonymous users from accessing any methods in the application. You will use the [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute to opt out of the authorization requirement in a couple methods, so anonymous users can log in and can view the home page. The  [RequireHttps](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.requirehttpsattribute.aspx) will require all access to the web app be through HTTPS.

1. Add the [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute to the **Index** method of the Home controller. The [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) attribute enables you to white-list the methods you want to opt out of authorization. An image of a portion of the HomeController is shown below:	

  	![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

2. Do a global search for *AllowAnonymous*, you can see it is used in the log in and registration methods of the Account controller.
1. In *CmController.cs*, add `[Authorize(Roles = "canEdit")]` to the HttpGet and HttpPost methods that change data (Create, Edit, Delete, every action method except Index and Details) in the *Cm* controller. A portion of the completed code is shown below: 

   	![img of code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)



1. If you are still logged in from a previous session, hit the **Log out** link.
1. Click on the **About** or **Contact** links. You will be redirected to the log in page because anonymous users cannot view those pages. 
1. Click the **Register as a new user** link and add a local user with email *joe@contoso.com*. Verify *Joe* can view the Home, About and Contact pages. 

	![login](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

1. Click the *CM Demo* link and verify you see the data. 
1. Click an edit link on the page, you will be redirected to the log in page (because a new local user is not added to the *canEdit* role).
1. Log in as *user1@contoso.com* with password of "P_assw0rd1" (the "0" in "word" is a zero). You will be redirected to the edit page you previously selected. <br/>
   If you can't log in with that account and password, try copying the password from the source code and pasting it. If you still can't log in, check the **UserName** column of the **AspNetUsers** table to verify *user1@contoso.com* was added. 

1. Verify you can make data changes.

<h2><a name="bkmk_deploytowindowsazure11"></a>將應用程式部署至 Azure</h2>

1. 在 Visual Studio 的 [**方案總管**] 中以滑鼠右鍵按一下專案，再選取內容功能表中的 [**發佈**]。

	![Publish in project context menu][firsdeploy003]

[**發佈 Web**] 精靈隨即開啟。

1. 按一下 [**發佈 Web**] 對話方塊中左側的 [**設定**] 索引標籤。按一下 [**v**] 圖示以選取 **ApplicationDbContext** 的 [**遠端連接字串**]，並選取 **ContactManagerNN_db**。

   
	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. 在 **ContactManagerContext** 下選取 [**Execute Code First Migrations**]。

	![settings](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. 按一下 [**發佈**]。
1. 以 *user1@contoso.com* (使用密碼 "P_assw0rd1") 身分登入，並確認您可以編輯資料。
1. 登出。
1. 請移至 [[Google Developers Console](https://console.developers.google.com/)]，並在 [**認證**] 索引標籤上更新 [重新導向 URI] 和 [JavaScript Orgins] 以使用 Azure URL。
1. 使用 Google 或 Facebook 登入。如此將使 Google 或 Facebook 帳戶新增至 **canEdit** 角色。如果您收到 HTTP 400 錯誤，內含訊息 *The redirect URI in the request: https://contactmanager{my version}.azurewebsites.net/signin-google did not match a registered redirect URI.*, you'll have to wait until the changes you made are propagated. If you get this error after more than a minute, verify the URIs are correct.

### 停止網站以防止其他人註冊  

1. 在 [**伺服器總管**] 中瀏覽至 [**網站**]。
4. 在每個網站執行個體上按一下滑鼠右鍵，再選取 [**停止網站**]。 

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png) 

或者，您也可以從 Azure 管理入口網站選取網站，然後按一下頁面底部的 [**停止**] 圖示。

	![stop web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### 移除 AddToRoleAsync、發行和測試

1. 在帳戶控制器中，從 **ExternalLoginConfirmation** 方法註解化下列程式碼或將其移除： 
                `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. 建置專案 (如此會儲存檔案變更並確認沒有任何編譯錯誤)。
5. 以滑鼠右鍵按一下 [**方案總管**] 中的專案，並選取 [**發佈**]。

	   ![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. 按一下 [**開始預覽**] 按鈕。只會部署需要更新的檔案。
5. 從 Visual Studio 或透過入口網站啟動網站。**您無法在網站停止時進行發佈**。

	![start web site](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

5. 返回 Visual Studio，並按一下 [**發佈**]。
3. 您的 Azure 應用程式隨即在預設瀏覽器中開啟。如果您已登入，請登出，以便您可以使用匿名使用者的身分檢視首頁。  
4. 按一下 [**關於**] 連結。系統會將您重新導向至 [登入] 頁面。
5. 按一下 [登入] 頁面上的 [**註冊**] 連結並建立本機帳戶。我們將使用此本機帳戶，確認您可以存取唯讀頁面但無法存取用來變更資料的頁面 (這些頁面受 *canEdit* 角色保護)。本教學課程稍後將移除本機帳戶存取權。 

	![Register](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

1. 確認您可以瀏覽 [*關於*] 和 [*連絡人*] 頁面。

	![Log off](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

1. 按一下 [**CM Demo**] 連結，並瀏覽到 **Cm** 控制器。或者，您也可以在 URL 後加上 *Cm*。 

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. 按一下 [編輯] 連結。系統會將您重新導向至登入頁面。在 [**Use another service to log in**] 下按一下 Google 或 Facebook，並以您先前註冊的帳戶登入。(如果您的動作夠快，同時工作階段 Cookie 未逾時，將使用您先前所使用的 Google 或 Facebook 帳戶為您自動登入。)
2. 確認您在登入該帳戶後可編輯資料。
 	**注意：**您無法在從此應用程式登出 Google 後，又使用相同瀏覽器登入不同的 Google 帳戶。若是使用一個瀏覽器，則必須瀏覽到 Google 再登出。您可以使用不同瀏覽器，以自己在相同協力廠商驗證者 (例如 Google) 那邊的其他帳戶登入。

如果您並未填寫 Google 帳戶資訊中的姓名，則會發生 NullReferenceException。


## 檢查 SQL Azure DB ##

1. 在 [**伺服器總管**] 中瀏覽至 [**ContactDB**]
2. 在 [**ContactDB**] 上按一下滑鼠右鍵，再選取 [**Open in SQL Server Object Explorer**]。
 
	![open in SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)
 
**注意：**若您無法展開 [**SQL Database**]，並且在 Visual Studio 中「看不到」[**ContactDB**]，則必須依照下列指示開放一或多個防火牆連接埠。遵循**設定 Azure 防火牆規則**中的指示。新增防火牆規則後，您可能必須等候數分鐘才能存取資料庫。
 
1. 在 [**AspNetUsers**] 資料表上按一下滑鼠右鍵，並選取 [**檢視資料**]。

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. 記下您以 Google 帳戶註冊且要加到 **canEdit** 角色的識別碼，以及 *user1@contoso.com* 的識別碼。這些應為 **canEdit** 角色中唯一的使用者 (您將會在下一個步驟進行確認)。

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. 在 [**SQL Server 物件總管**] 中，以滑鼠右鍵按一下 **AspNetUserRoles** 並選取 [**檢視資料**]。

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
確認所列的 **UserId** 屬於 *user1@contoso.com* 和您註冊的 Google 帳戶。 


## 設定 Azure 防火牆規則 ##

如果您無法從 Visual Studio 連線至 SQL Azure，或收到指出「無法開啟伺服器」的錯誤對話方塊時，請遵循本節的步驟。

![firewall error](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

您需要將您的 IP 位址新增至允許的 IP。

1. 在 Azure 入口網站，選取左側索引標籤中的 [**SQL Database**]。

	![Select SQL][rx6]

1. 按一下 [**ContactDB**]。

1. 按一下 [**Set up Azure firewall rules for this IP address**] 連結。

	![firewall rules][rx7]

1. 當系統提示您「現有的防火牆規則不含目前的 IP 位址 xxx.xxx.xxx.xxx。是否要更新防火牆規則？」時，請按一下 [**是**]。在一些公司防火牆後新增此位址通常並不夠，您將需要新增某個範圍的 IP 位址。

下一步是新增允許的 IP 位址範圍。

1. 在 Azure 入口網站，按一下 [**SQL Database**]。
1. 選取 [**伺服器**] 索引標籤，然後按一下您要設定的伺服器。

	![Servers tab in Azure ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

1. 按一下 [**設定**] 索引標籤。

1. 新增規則名稱以及開頭與結尾 IP 位址。

	![ip range][rx9]

1. 按一下頁面底部的 [**儲存**]。
1. 若您需要新增 IP 位址範圍來連線，請留下意見反應來讓我知道。

最後，您可以從 SQL Server 物件總管 (SSOX) 連線至 SQL Database 執行個體

1. 從 [檢視] 功能表，按一下 [**SQL Server 物件總管**]。
1. 在 [**SQL Server**] 上按一下滑鼠右鍵，再選取 [**加入 SQL Server**]。
1. 在 [**連接至伺服器**] 對話方塊中，將 [**驗證**] 設定為 [**SQL Server 驗證**]。您會從 Azure 入口網站取得 [**伺服器名稱**] 和 [**登入**]。
1. 在瀏覽器中，瀏覽到入口網站並選取 [**SQL Database**]。
1. 選取 [**ContactDB**]，然後按一下 [**View SQL Database connection strings**]。
1. 從 [**連接字串**] 頁面中，複製 [**伺服器**] 和 [**使用者識別碼**]。
 
	![con string](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)
1. 將 [**伺服器**] 和 [**使用者識別碼**] 值貼入 Visual Studio 中的 [**連接至伺服器**] 對話方塊。[**使用者識別碼**] 值要貼到 [**登入**] 項目。接著輸入密碼來建立 SQL DB。

	![Connect to Server DLG](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

您現在可依照稍早提供的指示，瀏覽到連絡人 DB。


## 編輯資料庫資料表來將使用者新增至 canEdit 角色

在本教學課程稍早的章節中，您使用程式碼將使用者新增至 canEdit 角色。替代的方法是直接在成員資格資料表中操作資料。下列步驟顯示如何使用此替代方法，將使用者新增至角色。

2. 在 [**SQL Server 物件總管**] 中，以滑鼠右鍵按一下 **AspNetUserRoles** 並選取 [**檢視資料**]。

	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. 複製 *RoleId* 並將其貼至空白 (新) 資料列。
	
	![CM page](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. 在 **AspNetUsers** 資料表中尋找您要放入角色的使用者，複製該使用者的識別碼，然後將它貼到 **AspNetUserRoles** 資料表中的 **UserId** 欄。

我們正在開發一項工具，讓使用者和角色管理起來簡單得多。


<h2><a name="nextsteps"></a>後續步驟</h2>

請遵循以此範例為基礎的教學課程：

1.	[建立安全的 ASP.NET MVC 5 Web 應用程式，並重設登入、電子郵件確認與密碼](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[使用 SMS 和電子郵件雙因素驗證的 ASP.NET MVC 5 應用程式](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[將密碼和其他機密資料部署至 ASP.NET 和 Azure 的最佳做法](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)
4.	[使用 Facebook 和 Google OAuth2 建立 ASP.NET MVC 5 應用程式](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on )這包括如何將設定檔資料新增至使用者註冊 DB 的指示，並取得使用 Facebook 作為驗證提供者的詳細指示。
5.	[開始使用 ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

若要啟用此教學課程頂端顯示的社交登入按鈕，請參閱 [ASP.NET MVC 5 的美觀社交登入按鈕](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/) (英文)。

Tom Dykstra 見解精闢的[開始使用 EF 和 MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) (英文) 將示範更進階的 MVC 和 EF 程式設計。

本教學課程和範例應用程式是由 [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT))在 Tom Dykstra 和 Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) 的協助下所撰寫。 

***歡迎留下意見反應***，告訴我們您喜歡的地方或希望我們改善的地方 (不論是針對本教學課程或其示範的產品)。您的意見反應將協助我們訂出優先改善要務。您也可以在 [告訴我如何處理程式碼](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code) (英文) 提出並對新主題進行投票。

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
























