<properties  linkid="dev-net-tutorials-web-app-with-sql-azure-vs2013" urlDisplayName="Web Site with SQL Database" pageTitle="Deploy a Secure ASP.NET MVC app with Membership, OAuth, and SQL Database to an Azure Web Site" metaKeywords="Azure hello world tutorial, Azure getting started tutorial, SQL Database tutorial, Azure .NET hello world tutorial, Azure C# hello world tutorial, SQL Azure C# tutorial" description="Learn how to develop an ASP.NET MVC 5 web site with a SQL Database back-end deploy it to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter=".NET" title=" OAuth" authors="" solutions="" writer="riande" manager="wpickett" editor="mollybos" />

# 將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 5 應用程式部署至 Azure 網站

***作者：[Rick Anderson][1] 和 Tom Dykstra。2014 年 4 月 2 日更新。***

本教學課程將示範如何建立可讓使用者以 Facebook 或 Google 認證登入的安全 ASP.NET MVC 5 Web 應用程式。您也會將應用程式部署至 Azure。

您可以免費申請 Azure 帳戶，而且如果您還沒有 Visual Studio 2013，SDK 會自動安裝 Visual Studio 2013 for Web Express。您可以開始免費進行 Azure 相關開發。若要使用 Visual Studio 2012，請參閱[前版教學課程](/en-us/develop/net/tutorials/web-site-with-sql-database-vs2012/)。這版教學課程遠比前版簡單。

本教學課程假設您先前沒有使用 Azure 的經驗。完成此教學課程後，您將有個安全的資料驅動 Web 應用程式已在雲端中啟動並執行、並已在使用雲端資料庫。

您將了解：

* 如何建立安全的 ASP.NET MVC 5 專案，並將該專案發行至 Azure 網站。
* 如何使用 [OAuth][2]、[OpenID][3] 和 ASP.NET 成員資格資料庫來保護應用程式的安全。
* 如何使用新的成員資格 API 來新增使用者和角色。
* 如何使用 SQL 資料庫在 Azure 中儲存資料。

您將建立一個簡單的連絡人清單 Web 應用程式，該應用程式建立於 ASP.NET MVC 5 之上，並使用 ADO.NET Entity Framework 進行資料庫存取。下圖顯示完成之應用程式的登入頁面：

![登入頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png)

> [WACOM.NOTE] 若要完成此教學課程，您需要 Microsoft Azure 帳戶。如果您沒有這類帳戶，可以[啟用自己的
> MSDN
> 訂戶權益](/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)或是[申請免費試用](/en-us/pricing/free-trial/?WT.mc_id=A261C142F)

本教學課程內容：

* [設定開發環境](#setupdevenv)
* [設定 Azure 環境](#bkmk_setupwindowsazure)
* [建立 ASP.NET MVC 5 應用程式](#bkmk_createmvc4app)
* [將應用程式部署至 Azure](#bkmk_deploytowindowsazure1)
* [新增資料庫至應用程式](#bkmk_addadatabase)
* [新增 OAuth 提供者](#addOauth)
* [使用成員資格 API](#mbrDB)
* [將應用程式部署至 Azure](#bkmk_deploytowindowsazure11)
* [後續步驟](#nextsteps)

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

若要對 localhost 使用新的 SSL 憑證，您需要安裝 [Visual Studio 2013 Update 2 RC][4] 或更高版本。

<h2><a name="bkmk_setupwindowsazure"></a>設定 Azure 環境</h2>


接下來，建立一個 Azure 網站和一個 SQL 資料庫來設定 Azure 環境。

### 在 Azure 中建立網站和 SQL 資料庫

您的 Azure 網站會在共用主控環境中執行，意即其會在與其他 Azure 用戶端共用的虛擬機器 (VM) 上執行。共用主控環境是一種在雲端中開始營運的低成本方法。因為應用程式是在專用 VM 上執行，所以如果日後您的 Web 流量增加，可依需求對應用程式進行延展。如果您需要更複雜的架構，可以移轉至 Azure 雲端服務。雲端服務是執行於您可視本身需求來設定的專用 VM
上。

Azure SQL Database 是以 SQL Server 技術為基礎來建置的雲端型關聯式資料庫服務。工具和應用程式如果使用 SQL Server，同樣也可以使用 SQL Database。

1.  在 **Azure 管理入口網站][5]中，按一下左側索引標籤的 [網站**，再按一下 **新增**。
    
    ![管理入口網站中的 [新增]
    按鈕](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png)

2.  按一下 **網站**，再按一下 **自訂建立**。
    
    ![以管理入口網站中的 [資料庫]
    連結進行建立](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png)
    
    **新網站 - 自訂建立** 精靈隨即開啟。

3.  在精靈的 **建立網站** 步驟中，於 **URL** 方塊中輸入字串作為應用程式的唯一 URL。完整的 URL 將包含您在此處輸入的字串，加上您在文字方塊旁看到的尾碼。下圖顯示可能已採用的 URL，因此您必須選擇不同的 URL。
    
    ![以管理入口網站中的 [資料庫]
    連結進行建立](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png)

4.  在 **資料庫** 下拉式清單中，選擇 **Create a free SQL database**。

5.  從 **區域** 下拉式清單中，選擇您已為網站選取的相同區域。此設定指定 VM 將在哪個資料中心執行。
6.  在 **DB Connection String Name** 方塊中，保留預設值 *DefaultConnection*。
7.  按一下指向方塊右下角的箭號。精靈隨即前進至 **指定資料庫設定** 步驟。

8.  在 **名稱** 方塊中，輸入 *ContactDB* (請參閱下圖)。
9.  在 **伺服器** 方塊中，選取 **New SQL Database server**
    (請參閱下圖)。或者，如果您已建立 SQL Server 資料庫，可從下拉式清單控制項選取該 SQL Server。
10. 將 **區域** 設為當初建立網站時的相同區域。
11. 輸入系統管理員的 **登入名稱** 和 **密碼**。如果選取 **New SQL Database server**，則不要在此處輸入現有的名稱和密碼，而是輸入新的名稱和密碼；您現在定義的名稱和密碼將供未來存取資料庫時使用。如果選取先前建立的
    SQL Server，系統會提示您提供先前建立之 SQL Server 帳戶名稱的密碼。在本教學課程中，我們不會核取 **進階** 方塊。如果是免費 DB，您只能設定定序。
12. 按一下方塊右下角的打勾記號來表示完成。
    
    ![[新網站 - 使用資料庫建立] 精靈的 [資料庫設定]
    步驟](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png)
    
    下圖顯示如何使用現有的 SQL Server 和登入。
    
    ![[新網站 - 使用資料庫建立] 精靈的 [資料庫設定]
    步驟](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png)
    
    **管理入口網站] 隨即返回 [網站] 頁面，且 [狀態** 欄顯示正在建立網站。稍待片刻 (通常不到一分鐘)，**狀態** 欄就會顯示建立網站成功。在左側的導覽列中，**網站** 圖示旁會出現您帳戶中已有的網站數；**SQL Database** 圖示旁則出現資料庫數目。

<h2><a name="bkmk_createmvc4app"></a>建立 ASP.NET MVC 5 應用程式</h2>


您已建立 Azure 網站，但網站中還沒有內容。接下來的步驟是建立要發行至 Azure 的 Visual Studio Web 應用程式。

### 建立專案

1.  從 **檔案** 功能表，按一下 **新增專案**。
    
    ![[檔案] 功能表中的
    [新增專案]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

2.  在 **新增專案** 對話方塊中，展開 **C#** 並選取 **已安裝的範本** 下的 **Web**，再選取 **ASP.NET Web 應用程式**。

3.  將應用程式命名為 **ContactManager**，再按一下 **確定**。
    
    ![[新增專案]
    對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
    
    **注意：**圖中的名稱處顯示 "MyExample"，但請務必輸入 "ContactManager"。您稍後將要複製的程式碼區塊會假設專案名稱為 ContactManager。

4.  在 **New ASP.NET Project** 對話方塊中，選取 **MVC** 範本，**取消勾選** **Create remote resources** 核取方塊，然後按一下 **確定**。(此核取方塊可能標示為 **Host in the cloud**而非 **Create remote resources**。)
    
    ![[New ASP.NET Project]
    對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)

### 設定頁首及頁尾

1.  在 **方案總管**，開啟 *Views\\Shared* 資料夾中的 *Layout.cshtml* 檔案。
    
    ![方案總管中的
    _Layout.cshtml](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png)

2.  以 "Contact Manager" 取代兩個出現的 "My ASP.NET MVC Application" 字樣。

3.  以 "CM Demo" 取代 "Application name"。

4.  更新第一個 Action 連結，以 *Cm* 取代 *Home* 來使用 *Cm* 控制器。
    
    ![程式碼變更](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)

### 在本機執行應用程式

1.  按 CTRL+F5 執行應用程式。
    
    應用程式首頁隨即出現在預設瀏覽器中。
    
    ![在本機執行的網站](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

只需執行上述作業，即可建立稍後要部署至 Azure 的應用程式。稍後您將新增資料庫功能。

<h2><a name="bkmk_deploytowindowsazure1"></a>將應用程式部署至 Azure</h2>


1.  在 Visual Studio 的 **方案總管** 中以滑鼠右鍵按一下專案，再選取內容功能表中的 **發行**。
    
    ![專案內容功能表中的
    [發行]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
    
    此時會開啟 **發行 Web** 精靈。

2.  在 **發行 Web** 精靈的 **設定檔** 索引標籤中，按一下 **Azure 網站**。
    
    ![匯入發行設定](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss2.PNG)

3.  按一下 **登入** 按鈕來登入 Azure 入口網站。

	![登入](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss22.PNG)

	登入後，**Select Existing Web Site** 對話方塊隨即出現。

1.  選取您在此教學課程第一個部分所建立的網站，然後按一下 **確定**。

	![選取網站](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss3.png)

1.  在 **發行 Web** 對話方塊中，按一下 **發行**。
    
    ![發行](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)
    
    您建立的應用程式現在正在雲端中執行。下次您部署應用程式時，只會部署變更的 (或新的) 檔案。
    
    ![在雲端中執行](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>新增資料庫至應用程式</h2>


接下來，您將更新 MVC 應用程式，以加上顯示和更新資料庫中的連絡人，以及在資料庫中儲存資料的能力。應用程式將使用 Entity Framework，以建立資料庫以及讀取和更新資料庫中的資料。

### 新增連絡人的資料模型類別

首先，您會在程式碼中建立簡單的資料模型。

1.  在 **方案總管**，於 Models 資料夾上按一下滑鼠右鍵，按一下 **新增**，再按一下 **類別**。
    
    ![在 Models
    資料夾內容功能表中新增類別](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2.  在 **加入新項目** 對話方塊中，將新的類別檔案命名為 *Contact.cs*，再按一下 **新增**。
    
    ![加入 [新增項目]
    對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png)

3.  以下列程式碼取代 Contacts.cs 檔案的內容。
    
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


1.  建置專案 **(Ctrl+Shift+B)** (使用樣板機制前必須先建置專案)。 2.  在 **方案總管**，於 Controllers 資料夾上按一下滑鼠右鍵，按一下 **新增**，再按一下 **控制器**。
    
    ![在 Controllers
    資料夾內容功能表中新增控制器](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png)

3.  在 **Add Scaffold** 對話方塊中，選取 **MVC 5 Controller with views, using EF**，再按一下 **新增**。
    
    ![[Add Scaffold]
    對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)

4.  在 **模型類別** 下拉式方塊中選取 [Contact **(ContactManager.Models)**。(請參閱下圖)。
5.  在 **資料內容類別** 中選取 [ApplicationDbContext **(ContactManager.Models)**。**ApplicationDbContext** 將用於成員資格 DB 和我們的連絡人資料。
6.  在 **控制器名稱** 文字輸入方塊中，輸入 "CmController" 作為控制器名稱。
    
    ![新增資料內容對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

7.  按一下 **新增**。
    
    Visual Studio 隨即針對 **Contact** 物件的 CRUD 資料庫操作，建立控制器方法與檢視。

## 啟用移轉、建立資料庫、新增範例資料和資料初始設定式

下一個工作是啟用 [Code First 移轉][6]功能，以便根據建立的資料模型建立資料庫。

1.  在 **工具** 功能表中，依序選取 **NuGet Package Manager** 及 [Package Manager Console]****。![[工具] 功能表中的 Package Manager Console](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2.  在 **Package Manager Console** 視窗中，輸入下列命令：
    
         enable-migrations
    
    **enable-migrations** 命令會建立 *Migrations* 資料夾，並在該資料夾置入 *Configuration.cs* 檔案，您可以編輯該檔案來植入資料庫及設定 [移轉]。

3.  在 **Package Manager Console** 視窗中，輸入下列命令：
    
         add-migration Initial
    
    **add-migration Initial** 命令會在 *Migrations* 資料夾中產生名為 **<date\_stamp>Initial** 的檔案，此檔案會建立資料庫。第一個參數 (**Initial**) 是任意的，用於建立檔案的名稱。您可以在 **方案總管** 中看到新的類別檔案。在 **Initial** 類別中，**Up** 方法會建立 Contacts 資料表，**Down** 方法 (當您希望返回前個狀態時使用) 則會捨棄該資料表。

4.  開啟 *Migrations\\Configuration.cs* 檔案。
5.  新增下列命名空間。
    
    using ContactManager.Models;
6.  以下列程式碼取代 *Seed* 方法：
    
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
    
    此程式碼會以連絡人資訊初始化 (植入) 該資料庫。如需植入資料庫的詳細資訊，請參閱[植入及偵錯 Entity Framework (EF) DB][7]。

7.  在 **Package Manager Console** 中輸入命令：
    
         update-database
    
    ![Package Manager Console
    命令](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png)
    
    **update-database** 會執行第一次移轉，使資料庫建立。根據預設，資料庫會以 SQL Server Express LocalDB 資料庫的形式建立。

8.  按 CTRL+F5 執行應用程式，再按一下 **CM Demo** 連結；或瀏覽到 http://localhost:(port#)/Cm。
    
    應用程式隨即顯示種子資料並提供編輯、詳細資料和刪除連結。您可以建立、編輯、刪除及檢視資料。
    
    ![資料的 MVC
    檢視](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png)

<h2><a name="addOauth"></a><span  class="short-header">OAuth</span>新增 OAuth2 和 OpenID 提供者</h2>


[OAuth][2] 是一種開放式通訊協定，可讓 Web、行動和桌面應用程式以簡單、標準的方法執行安全授權。ASP.NET MVC 網際網路範本使用 OAuth 和 [OpenID][3]，公開 Facebook、Twitter、Google 和 Microsoft 作為驗證提供者。雖然本教學課程僅使用 Google 作為驗證提供者，但您可以輕易修改程式碼來使用任何提供者。實作其他提供者的步驟，與您將在本教學課程中看到的步驟極為類似。若要使用 Facebook 作為驗證提供者，請參閱我的教學課程：[使用 Facebook 及 Google OAuth2 和 OpenID 登入以建立 ASP.NET MVC 5 應用程式][8]。

除了驗證，本教學課程也會使用角色來實作授權。只有您新增至 *canEdit* 角色的使用者才能變更資料 (也就是建立、編輯或刪除連絡人)。

1.  開啟 *App\_Start\\Startup.Auth.cs* 檔案。移除
    *app.UseGoogleAuthentication()* 方法前的註解字元。

2.  執行應用程式並按一下 **登入** 連結。 3.  在 **Use another service to log in** 下，按一下 **Google** 按鈕。
    
    ![Google
    登入](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss23.PNG)

4.  輸入您的認證。
5.  Google 驗證伺服器將要求您提供應用程式的權限，以便檢視您的電子郵件地址和帳戶的基本資訊。按一下 **接受**。![Google
    要求提供權限](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss7.PNG)
6.  系統會將您重新導向至 **註冊] 頁面。使用者名稱將預設為您用來註冊的電子郵件別名，若需要您可加以變更。按一下 [註冊**。
    
    ![註冊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss8.PNG)

<h2><a name="mbrDB"></a><span  class="short-header">成員資格 DB</span>使用成員資格 API</h2>


在本節中，您會將本機使用者和 *canEdit* 角色新增至成員資格資料庫。只有 *canEdit* 角色中的使用者才能編輯資料。最佳做法是依角色可執行的動作來命名角色，因此將角色命名為 *canEdit* 會較命名為 *admin* 更好。隨著應用程式發展，您可以新增如 *canDeleteMembers* 等新角色，而非新增較欠缺描述性的 *superAdmin*。

1.  開啟 *migrations\\configuration.cs* 檔案並新增下列 `using` 陳述式：
    
         using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

2.  將下列 **AddUserAndRole** 方法新增至類別：
    
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

3.  從 **Seed** 方法呼叫新方法：
    
         protected override void Seed(ContactManager.Models.ApplicationDbContext context)
         {
        AddUserAndRole(context);
        context.Contacts.AddOrUpdate(p => p.Name,
        // 移除了程式碼以求簡單明瞭
         }
    
    下圖顯示對 *Seed* 方法的變更：
    
    ![程式碼影像](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)
    
    此程式碼會建立名為 *canEdit* 的新角色、建立新的本機使用者 *user1@contoso.com*，並將
    *user1@contoso.com* 新增至 *canEdit* 角色。如需詳細資訊，請參閱 [ASP.NET
    身分識別資源頁面][9] (英文)。

## 使用暫時程式碼，將新的社交登入使用者新增至 canEdit 角色

在本節中，您將在帳戶控制器中暫時修改 **ExternalLoginConfirmation** 方法，以將使用 OAuth 或 OpenID 提供者註冊的新使用者新增至 *canEdit* 角色。我們會暫時修改 **ExternalLoginConfirmation** 方法，以將新使用者自動新增至管理角色。在我們提供工具來新增及管理角色前，我們將暫時使用以下的自動註冊程式碼。我們希望將來能提供類似 **WSAT][10] 的工具，讓您能建立及編輯使用者帳戶與角色。我將在本教學課程稍後示範如何使用 [伺服器總管** 新增使用者至角色。

1.  開啟 **Controllers\\AccountController.cs** 檔案並瀏覽到
    **ExternalLoginConfirmation** 方法。
2.  將下列呼叫新增至就在 **SignInAsync** 呼叫前面的 **AddToRoleAsync**。
    
                 await UserManager.AddToRoleAsync(user.Id, "canEdit");
    
    上述程式碼會將新註冊的使用者新增至 "canEdit" 角色，讓這些使用者能存取用來變更 (編輯) 資料的動作方法。下圖顯示程式碼變更：
    
    ![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

稍後在本教學課程中，您會將應用程式部署至 Azure，並在後者使用 Google 或其他協力廠商驗證提供者來登入。這會將您新註冊的帳戶新增至 *canEdit* 角色。任何人只要有網站 URL 和 Google ID，都可以註冊及更新您的資料庫。若要防止其他人這麼做，您可以停止網站。您可以檢查資料庫來確認 *canEdit* 角色中有誰。

在 **Package Manager Console** 點擊向上鍵以帶出下列命令：

    	Update-Database

執行 **Update-Database** 命令，以執行 **Seed** 方法並進而執行您剛剛新增的 **AddUserAndRole**。**AddUserAndRole** 將建立使用者 *user1@contoso.com*，並將她新增至 *canEdit* 角色。

## 使用 SSL 和 Authorize 屬性保護應用程式

在本節中，您將套用 [Authorize][11] 屬性來限制對動作方法的存取。匿名使用者只能檢視首頁控制器的 **Index** 動作方法。註冊的使用者能夠查看連絡人資料 (Cm 控制器的 **索引** 和 **詳細資料** 頁面)、[關於] 和 [連絡人] 頁面。只有 *canEdit* 角色中的使用者才能存取用來變更資料的動作方法。

1.  將 [Authorize][11] 篩選器和 [RequireHttps][12] 篩選器新增至應用程式。替代的方法是將 [Authorize][11] 屬性和 [RequireHttps][12] 屬性新增至每個控制器，但將這些屬性套用至整個應用程式是最安全的做法。藉由全面新增這些屬性，您所新增的每個新控制器和動作方法都會自動受到保護，而不需要您記得哪些已套用、哪些未套用。如需詳細資訊，請參閱[保護您的 ASP.NET MVC 應用程式和新 AllowAnonymous 屬性][13]。開啟 *App\_Start\\FilterConfig.cs* 檔案，並以下列程式碼 (其會新增兩個篩選器) 取代 *RegisterGlobalFilters* 方法：
    
         public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
         {
        filters.Add(new HandleErrorAttribute());
        filters.Add(new System.Web.Mvc.AuthorizeAttribute());
        filters.Add(new RequireHttpsAttribute());
         }
    
    下圖顯示變更的程式碼：
    
    ![程式碼影像](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss10.PNG)
    
    上述程式碼中套用的 [Authorize][11] 篩選器將防止匿名使用者存取應用程式中的任何方法。您將使用 [AllowAnonymous][13] 屬性來選擇略過一些方法中的授權需求，讓匿名使用者可登入及檢視首頁。使用 [RequireHttps][12] 時，所有對 Web 應用程式的存取都必須透過 HTTPS 進行。

2.  將 [AllowAnonymous][13] 屬性新增至首頁控制器的 **Index** 方法。[AllowAnonymous][13] 屬性能讓您將想要選擇略過授權的方法加到白名單。下圖顯示 HomeController 的片段內容：

	![code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

1.  全面搜尋 *AllowAnonymous*，您會發現帳戶控制器的登入與註冊方法中都使用了它。 2.  在 *CmController.cs* 中，將 `[Authorize(Roles = "canEdit")]` 新增至 *Cm* 控制器中用來變更資料的 HttpGet 和 HttpPost 方法 (Create、Edit、Delete，也就是 Index 和 Details 外的每個動作方法)。以下顯示完整程式碼的片段內容：
    
    ![程式碼影像](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)

## 對專案啟用 SSL

1.  啟用 SSL。在 **方案總管]，按一下 [ContactManager** 專案，再按一下 F4 帶出 [屬性] 對話方塊。將 **SSL 已啟用** 變更為 true。複製 **SSL URL**。除非您先前已建立 SSL 網站，否則 SSL URL 將是 https://localhost:44300/。
    
    ![啟用
    SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png)

2.  在 **方案總管]，於 [Contact Manager** 專案上按一下滑鼠右鍵，再按一下 **屬性**。 3.  在左側索引標籤中按一下 **Web**。 4.  將 **專案 URL** 變為使用 **SSL URL**，並且儲存頁面 (Control S)。
    
    ![啟用
    SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)

5.  驗證 Internet Explorer 是 Visual Studio 所啟動的瀏覽器，如下圖所示：
    
    ![預設瀏覽器](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)
    
    瀏覽器選取器可讓您指定 Visual Studio 啟動的瀏覽器。

	![瀏覽器選取器](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

    您可以選取多個瀏覽器，並讓 Visual Studio 在您進行變更時更新每個瀏覽器。如需詳細資訊，請參閱 [在 Visual Studio 2013 中使用瀏覽器連結] (http://www.asp.net/visual-studio/overview/2013/using-browser-link)。

1.  按 CTRL+F5 執行應用程式。遵循指示來信任 IIS Express 產生的自我簽署憑證。
    
    ![信任 IIS Express
    產生的自我簽署憑證的指示](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

2.  閱讀 **安全性警告** 對話方塊，如果您要安裝代表 **localhost** 的憑證，請按一下 **是**。

	![localhost IIS Express 憑證警告](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1.  IE 顯示*首頁*，沒有出現 SSL 警告。
    
    ![IE 出現 localhost
    SSL，沒有出現警告](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)
    
    Google Chrome 也會接受憑證，並且不出現警告便顯示 HTTPS 內容。Firefox
    會使用自己的憑證存放區，因此會顯示警告。就我們的應用程式而言，您可以放心地按一下 **I Understand the
    Risks**。
    
    ![FireFox
    憑證警告](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

2.  如果您在前個工作階段仍保持登入狀態，請點擊 **登出** 連結。 3.  按一下 **關於** 或 **連絡人** 連結。因為匿名使用者無法檢視那些頁面，所以系統會將您重新導向至登入頁面。 4.  按一下 **Register as a new user** 連結並新增電子郵件為 *joe@contoso.com*
    的本機使用者。確認 *Joe* 可檢視 [首頁]、[關於] 和 [連絡人] 頁面。
    
    ![登入](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

5.  按一下 **CM Demo** 連結並確認看到資料。 6.  按一下頁面上的編輯連結，系統會將您重新導向至登入頁面 (因為未將新的本機使用者新增至 *canEdit* 角色)。 7.  以 *user1@contoso.com* 的身分和密碼 "P\_assw0rd1" ("word" 中的 "0" 是數字零) 登入。系統隨即將您重新導向到先前選取的編輯頁面。
    
    如果無法以該帳戶和密碼登入，請嘗試複製並貼上原始程式碼中的密碼。如果仍然無法登入，請檢查 **AspNetUsers** 資料表的 **UserName** 欄，確認已新增 *user1@contoso.com*。

8.  確認您可進行資料變更。

<h2><a name="bkmk_deploytowindowsazure11"></a>將應用程式部署至 Azure</h2>


1.  在 Visual Studio 的 **方案總管** 中以滑鼠右鍵按一下專案，再選取內容功能表中的 **發行**。
    
    ![專案內容功能表中的
    [發行]](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png)
    
    此時會開啟 **發行 Web** 精靈。

2.  按一下 **發行 Web** 對話方塊左側的 **設定** 索引標籤。按一下 **v** 圖示，為 **ApplicationDbContext** 選取 **遠端連接字串**，再選取 **ContactDB**
    
    ![設定](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

3.  在 **ContactManagerContext** 下選取 **Execute Code First Migrations**。
    
    ![設定](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

4.  按一下 **發行**。

5.  以 *user1@contoso.com* 身分登入並確認您可編輯資料。

6.  登出。

7.  使用 Google 或 Facebook 登入。如此將使 Google 或 Facebook 帳戶新增至 **canEdit** 角色。

### 停止網站以防止其他人註冊

1.  在 **伺服器總管** 中，瀏覽到 **網站**。 2.  在每個網站執行個體上按一下滑鼠右鍵，再選取 **停止網站**。
    
    ![停止網站](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png)
    
    或者，您也可以從 Azure 管理入口網站選取網站，然後按一下頁面底部的 **停止** 圖示。
    
    ![停止網站](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### 移除 AddToRoleAsync、發行和測試

1.  在帳戶控制器中，從 **ExternalLoginConfirmation** 方法註解化下列程式碼或將其移除：`await UserManager.AddToRoleAsync(user.Id, "canEdit");`
2.  建置專案 (如此會儲存檔案變更並確認沒有任何編譯錯誤)。 3.  在 **方案總管** 中以滑鼠右鍵按一下專案，再選取 **發行**。
    
	![Publish in project context menu](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)

4.  按一下 **開始預覽** 按鈕。只會部署需要更新的檔案。 5.  從 Visual Studio 或透過入口網站啟動網站。**您無法在網站停止時進行發行**。
    
    ![啟動網站](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

6.  返回 Visual Studio 並按一下 **發行**。 7.  您的 Azure 應用程式隨即在預設瀏覽器中開啟。如果您已登入，請登出，以便您可以使用匿名使用者的身分檢視首頁。 8.  按一下 **關於** 連結。系統會將您重新導向至 [登入] 頁面。 9.  按一下 **登入] 頁面上的 [註冊**
    連結並建立本機帳戶。我們將使用此本機帳戶，確認您可以存取唯讀頁面但無法存取用來變更資料的頁面 (這些頁面受 *canEdit* 角色保護)。本教學課程稍後將移除本機帳戶存取權。
    
    ![註冊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

10. 確認您可瀏覽到 [關於]** 和 [連絡人]** 頁面。
    
    ![登出](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

11. 按一下 **CM Demo** 連結並瀏覽到 **Cm** 控制器。或者，您也可以在 URL 後加上 *Cm*。
    
    ![CM
    頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)

12. 按一下 **編輯] 連結。系統會將您重新導向至登入頁面。在 [Use another service to log in** 下按一下 [Google] 或 [Facebook]，並以您先前註冊的帳戶登入。(如果您的動作夠快，同時工作階段 Cookie 未逾時，將使用您先前所使用的 Google 或 Facebook 帳戶為您自動登入。)
13. 確認您在登入該帳戶後可編輯資料。**注意：**您無法在從此應用程式登出 Google 後，又使用相同瀏覽器登入不同的 Google 帳戶。若是使用一個瀏覽器，則必須瀏覽到 Google 再登出。您可以使用不同瀏覽器，以自己在相同協力廠商驗證者 (例如 Google) 那邊的其他帳戶登入。

## 檢查 SQL Azure DB

1.  在 **伺服器總管** 中，瀏覽到 **ContactDB** 2.  在 **ContactDB** 上按一下滑鼠右鍵，再選取 **Open in SQL Server Object Explorer**。
    
    ![在 SSOX
    中開啟](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)

	**注意：**若您無法展開 **SQL Database**，並且在 Visual Studio
	中「看不到」****ContactDB**，則必須依照下列指示開放一或多個防火牆連接埠。遵循**設定 Azure
	防火牆規則**中的指示。新增防火牆規則後，您可能必須等候數分鐘才能存取資料庫。

1.  在 **AspNetUsers** 資料表上按一下滑鼠右鍵並選取 **檢視資料**。
    
    ![CM
    頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)

2.  記下您以 Google 帳戶註冊且要加到 **canEdit** 角色的識別碼，以及 *user1@contoso.com*
    的識別碼。這些應為 **canEdit** 角色中唯一的使用者 (您將會在下一個步驟進行確認)。
    
    ![CM
    頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)

3.  在 **SQL Server 物件總管**，於 **AspNetUserRoles** 上按一下滑鼠右鍵，再選取
    **檢視資料**。
    
    ![CM
    頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

確認所列的 **UserId** 屬於 *user1@contoso.com* 和您註冊的 Google 帳戶。

## 設定 Azure 防火牆規則

如果您無法從 Visual Studio 連線至 SQL Azure，或收到指出「無法開啟伺服器」的錯誤對話方塊時，請遵循本節的步驟。

![防火牆錯誤](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

您需要將您的 IP 位址新增至允許的 IP。

1.  在 Azure 入口網站，選取左側索引標籤中的 **SQL Database**。
    
    ![選取
    SQL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png)

2.  按一下 **ContactDB**。

3.  按一下 **Set up Azure firewall rules for this IP address** 連結。
    
    ![防火牆規則](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png)

4.  當系統提示您「現有的防火牆規則不含目前的 IP 位址 xxx.xxx.xxx.xxx。是否要更新防火牆規則？」時，請按一下 **是**。在一些公司防火牆後新增此位址通常並不夠，您將需要新增某個範圍的 IP 位址。

	下一步是新增允許的 IP 位址範圍。

1.  在 Azure 入口網站，按一下 **SQL Database**。 2.  選取 **伺服器** 索引標籤，然後按一下您要設定的伺服器。
    
    ![Azure 中的 [伺服器] 索引標籤
    ](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

3.  按一下 **設定** 索引標籤。

4.  新增規則名稱以及開頭與結尾 IP 位址。
    
    ![IP
    範圍](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png)

5.  按一下頁面底部的 **儲存**。 6.  若您需要新增 IP 位址範圍來連線，請留下意見反應來讓我知道。

	最後，您可以從 SQL Server 物件總管 (SSOX) 連線至 SQL Database 執行個體

1.  從 **檢視] 功能表，按一下 [SQL Server 物件總管**。 2.  在 **SQL Server** 上按一下滑鼠右鍵，再選取 **加入 SQL Server**。 3.  在 **Connect to Server** 對話方塊中，將 **驗證** 設為 **SQL Server Authentication**。您會從 Azure 入口網站取得 **伺服器名稱** 和 **登入**。 4.  在瀏覽器中，瀏覽到入口網站並選取 **SQL Database**。 5.  選取 **ContactDB**，然後按一下 **View SQL Database connection strings**。 6.  從 **連接字串** 頁面中，複製 **伺服器** 和 **使用者識別碼**。
    
    ![連接字串](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)

7.  將 **伺服器** 和 **使用者識別碼** 值複製到 Visual Studio 中的 **Connect to Server** 對話方塊。**使用者識別碼** 值要貼到 **登入** 項目。接著輸入密碼來建立 SQL DB。
    
    ![[Connect to Server]
    對話方塊](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

您現在可依照稍早提供的指示，瀏覽到連絡人 DB。

## 編輯資料庫資料表來將使用者新增至 canEdit 角色

在本教學課程稍早的章節中，您使用程式碼將使用者新增至 canEdit
角色。替代的方法是直接在成員資格資料表中操作資料。下列步驟顯示如何使用此替代方法，將使用者新增至角色。

1.  在 **SQL Server 物件總管**，於 **AspNetUserRoles** 上按一下滑鼠右鍵，再選取 **檢視資料**。
    
    ![CM
    頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

2.  複製 *RoleId* 並將其貼至空白 (新) 資料列。
    
    ![CM
    頁面](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)

3.  在 **AspNetUsers** 資料表中尋找要置於角色中的使用者，複製該使用者的「識別碼」**，然後將它貼至 **AspNetUserRoles** 資料表的 **UserId** 資料欄。

我們正在開發一項工具，讓使用者和角色管理起來簡單得多。

## 本機註冊考量

專案中目前的 ASP.NET 成員資格註冊並未支援密碼重設，且不會確認註冊者是否為真人 (例如使用 [CAPTCHA][14])。使用者只要以其中一家協力廠商提供者通過驗證後，即可註冊。如果您選擇停用本機註冊，請執行下列步驟：

1.  在 AccountController 中，從 GET 和 POST *Register* 方法移除 *[AllowAnonymous]* 屬性。它們將防止 Bot 和匿名使用者進行註冊。
2.  在 *Views\\Shared* 資料夾的 *\_LoginPartial.cshtml* 檔案中，移除 Register 動作連結。 3.  在 *Views\\Account\\Login.cshtml* 檔案中移除 Register 動作連結。 4.  部署應用程式。

<h2><a name="nextsteps"></a><span  class="short-header">後續步驟</span>後續步驟</h2>


如需關於如何新增設定檔資料至使用者註冊 DB 的指示，以及關於使用 Facebook 作為驗證提供者的詳細指示，請參閱我的教學課程：[使用 Facebook 及 Google OAuth2 和 OpenID 登入以建立 ASP.NET MVC 5 應用程式][8]。

若要啟用此教學課程頂端顯示的社交登入按鈕，請參閱 [ASP.NET MVC 5 的美觀社交登入按鈕][15] (英文)。

若要深入了解 ASP.NET MVC，我的[開始使用 ASP.NET MVC 5][16] 教學課程是不錯的起點。Tom Dykstra 見解精闢的[開始使用 EF 和 MVC][17] (英文) 將示範更進階的 MVC 和 EF 程式設計。

本教學課程和範例應用程式是由 [Rick Anderson][18] (Twitter [@RickAndMSFT][1]) 在 Tom Dykstra 和 Barry Dorrans (Twitter [@blowdart][19]) 的協助下所撰寫。

如果您發現喜歡的地方或希望我們改善的地方 (不論是針對本教學課程或其示範的產品)，歡迎留下意見反應。您的意見反應將協助我們訂出優先改善要務。您也可以在[告訴我如何處理程式碼][20]
(英文) 提出並對新主題進行投票。

<!-- bookmarks -->

<!-- images-->



[1]: https://twitter.com/RickAndMSFT
[2]: http://oauth.net/ "http://oauth.net/"
[3]: http://openid.net/
[4]: http://go.microsoft.com/fwlink/?LinkId=390521
[5]: https://manage.windowsazure.com
[6]: http://msdn.microsoft.com/library/hh770484.aspx
[7]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
[8]: http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on
[9]: http://curah.microsoft.com/55636/aspnet-identity
[10]: http://msdn.microsoft.com/en-us/library/ms228053.aspx
[11]: http://msdn.microsoft.com/en-us/library/system.web.mvc.authorizeattribute.aspx
[12]: http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx
[13]: http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx
[14]: http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership
[15]: http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/
[16]: http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started
[17]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[18]: http://blogs.msdn.com/b/rickandy/
[19]: https://twitter.com/blowdart
[20]: http://aspnet.uservoice.com/forums/228522-show-me-how-with-code