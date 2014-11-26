<properties linkid="dev-net-tutorials-web-app-with-sql-azure" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title=" OAuth" authors="riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="riande" />

# 將使用成員資格、OAuth 和 SQL Database 的安全 ASP.NET MVC 應用程式部署至 Azure 網站

***2013 年 10 月 15 日更新。***

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/develop/net/tutorials/web-site-with-sql-database/" title="Visual Studio 2013">Visual Studio 2013</a><a href="/zh-tw/develop/net/tutorials/web-site-with-sql-database-vs2012/" title="Visual Studio 2012" class="current">Visual Studio 2012</a></div>

<div class="dev-callout"><strong>注意</strong><p>現已提供<a href="/zh-tw/develop/net/tutorials/web-site-with-sql-database/">新版的教學課程</a>。如果您想要使用 Visual Studio 2012，仍舊可以遵循此版本步驟進行，但是新版課程的步驟將大幅簡化許多。</p></div>

本教學課程將示範如何建立可讓使用者以 Facebook、Yahoo 與 Google 認證登入的安全 ASP.NET MVC 4 Web 應用程式。您也會將應用程式部署至 Azure。

您可以免費申請 Azure 帳戶，而且如果您還沒有 Visual Studio 2012，SDK 會自動安裝 Visual Studio 2012 for Web Express。您可以開始免費進行 Azure 相關開發。

本教學課程假設您先前沒有使用 Azure 的經驗。完成此教學課程後，您將有個安全的資料驅動 Web 應用程式已在雲端中啟動並執行、並已在使用雲端資料庫。

您將了解：

-   如何安裝 Azure SDK 好讓電腦適合用於進行 Azure 開發。
-   如何建立安全的 ASP.NET MVC 4 專案，並將該專案發行至 Azure 網站。
-   如何使用 OAuth 與 ASP.NET 成員資格資料庫來確保您的應用程式安全。
-   如何將成員資格資料庫部署至 Azure。
-   如何使用 SQL 資料庫在 Azure 中儲存資料。
-   如何使用 Visual Studio 來更新與管理成員資格資料庫。

您將建立一個簡單的連絡人清單 Web 應用程式，該應用程式建立於 ASP.NET MVC 4 之上，並使用 ADO.NET Entity Framework 進行資料庫存取。下圖顯示完成之應用程式的登入頁面：

![login page][login page]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

本教學課程內容：

-   [設定開發環境][設定開發環境]
-   [設定 Azure 環境][設定 Azure 環境]
-   [建立 ASP.NET MVC 4 應用程式][建立 ASP.NET MVC 4 應用程式]
-   [將應用程式部署至 Azure][將應用程式部署至 Azure]
-   [新增資料庫至應用程式][新增資料庫至應用程式]
-   [新增 OAuth 提供者][新增 OAuth 提供者]
-   [新增角色至成員資格資料庫][新增角色至成員資格資料庫]
-   [建立資料部署指令碼][建立資料部署指令碼]
-   [將應用程式部署至 Azure][1]
-   [更新成員資格資料庫][更新成員資格資料庫]
-   [後續步驟][後續步驟]

## <a name="bkmk_setupdevenv"></a>設定開發環境

首先，安裝 Azure SDK for the .NET Framework，以設定您的開發環境。

1.  若要安裝 Azure SDK for .NET，請按一下底下連結：如果您尚未安裝 Visual Studio 2012，按下該連結會進行安裝。本教學課程需要安裝 Visual Studio 2012。
    [Azure SDK for Visual Studio 2012][Azure SDK for Visual Studio 2012] (英文)
2.  當系統提示您執行或儲存安裝可執行檔時，請按一下 [執行]。
3.  在 [Web Platform Installer] 視窗中，按一下 [安裝] 並繼續進行安裝。

![Web Platform Installer - Azure SDK for .NET][Web Platform Installer - Azure SDK for .NET]

安裝完成時，您即可開始進行開發。

## <a name="bkmk_setupwindowsazure"></a>設定 Azure 環境

接下來，建立 Azure 網站和 SQL 資料庫，以設定 Azure 環境。

### 在 Azure 中建立網站和 SQL 資料庫

您的 Azure 網站會在共用主控環境中執行，意即會在與其他 Azure 用戶端共用的虛擬機器 (VM) 上執行。共用主控環境是一種在雲端中開始營運的低成本方法。因為應用程式是在專用 VM 上執行，所以如果日後您的 Web 流量增加，可依需求對應用程式進行延展。如果您需要更複雜的架構，可以移轉至 Azure 雲端服務。雲端服務是執行於您可視本身需求來設定的專用 VM 上。

Azure SQL Database 是以 SQL Server 技術為基礎來建置的雲端型關聯式資料庫服務。工具和應用程式如果使用 SQL Server，同樣也可以使用 SQL Database。

1.  在 [Azure 管理入口網站][Azure 管理入口網站]中，按一下左側索引標籤中的 [網站]，然後按一下 [新增]。

    ![New button in Management Portal][New button in Management Portal]

1.  按一下 [Custom Create]。

    ![Create with Database link in Management Portal][Create with Database link in Management Portal]

[新網站 - 自訂建立] 精靈隨即開啟。

1.  在精靈的 [新網站] 步驟中，於 [URL] 方塊中輸入字串，做為您應用程式的唯一 URL。完整的 URL 將包含您在此處輸入的字串，加上您在文字方塊旁看到的尾碼。下圖顯示 "contactmgr2"，不過該 URL 可能已被佔用，因此您必須選擇不同的 URL。

    ![Create with Database link in Management Portal][2]

2.  在 [資料庫] 下拉式清單中，選擇 [Create a new SQL database]。

3.  從 [區域] 下拉式清單中，選擇您已為網站選取的相同區域。此設定會指定您的 VM 將在哪個資料庫中執行。請在 [DB CONNECTION STRING NAME] 中，輸入 *connectionString1*。

    ![Create a New Website step of New Website - Create with Database wizard][2]

4.  按一下指向方塊右下角的箭號。精靈隨即前進至 [資料庫設定] 步驟。

5.  在 [名稱] 方塊中，輸入 *ContactDB*

6.  在 [伺服器] 方塊中，選取 [New SQL Database server] 或者，如果您已建立 SQL Server 資料庫，可從下拉式清單控制項選取該 SQL Server。

7.  輸入系統管理員的 [登入名稱] 和 [密碼]。如果選取 [New SQL Database server]，則不要在此處輸入現有的名稱和密碼，而是輸入新的名稱和密碼；您現在定義的名稱和密碼將供未來存取資料庫時使用。如果選取先前建立的 SQL Server，系統會提示您提供先前建立之 SQL Server 帳戶名稱的密碼。在本教學課程中，我們不會核取 [進階] 方塊。[進階] 方塊可讓您設定資料庫大小 (預設值為 1 GB，不過您可以增加到 150 GB) 和定序。

8.  按一下方塊底部的核取方塊來表示完成。

    ![Database Settings step of New Website - Create with Database wizard][Database Settings step of New Website - Create with Database wizard]

    下圖顯示使用現有的 SQL Server 並登入。
    ![Database Settings step of New Website - Create with Database wizard][3]

    [管理入口網站] 隨即返回 [網站] 頁面，且 [狀態] 欄會顯示正在建立網站。稍待片刻 (通常不到一分鐘)，[狀態] 欄就會顯示建立網站成功。在左側的導覽列中，[網站] 圖示旁會出現您的帳戶中已有的網站數；[SQL Database] 圖示旁則出現資料庫數目。

## <a name="bkmk_createmvc4app"></a>建立 ASP.NET MVC 4 應用程式

您已建立 Azure 網站，但網站中還沒有內容。接下來的步驟是建立要發行至 Azure 的 Visual Studio Web 應用程式。

### 建立專案

1.  啟動 Visual Studio 2012。
2.  從 [檔案] 功能表，按一下 [新增專案]。
3.  在 [新增專案] 對話方塊中，展開 [Visual C\#] 並選取 [已安裝的範本] 下的 [Web]，再選取 [ASP.NET MVC 4 Web 應用程式]。保留預設值 [.NET Framework 4.5]。將應用程式命名為 **ContactManager**，再按一下 [確定]。

    ![New Project dialog box][New Project dialog box]

4.  在 [New ASP.NET MVC 4 Project] 對話方塊中，選取 [網際網路應用程式] 範本。保留預設的 Razor [檢視引擎]，然後按一下 [確定]。

    ![New ASP.NET MVC 4 Project dialog box][New ASP.NET MVC 4 Project dialog box]

### 設定頁首及頁尾

1.  在 [方案總管] 中，展開 Views\\Shared 資料夾，然後開啟 *\_Layout.cshtml* 檔案。

    ![\_Layout.cshtml in Solution Explorer][\_Layout.cshtml in Solution Explorer]

2.  以 "Contact Manager" 取代每次出現的 "My ASP.NET MVC Application" 字樣。
3.  以 "CM Demo" 取代 "your logo here"。

### 在本機執行應用程式

1.  按 CTRL+F5 執行應用程式。應用程式首頁隨即出現在預設瀏覽器中。
    ![To Do List home page][To Do List home page]

只需執行上述作業，即可建立稍後要部署至 Azure 的應用程式。稍後您將新增資料庫功能。

## <a name="bkmk_deploytowindowsazure1"></a>將應用程式部署至 Azure

1.  在瀏覽器中開啟 [Azure 管理入口網站][4]。

2.  在 [網站] 索引標籤中，按一下您先前建立的網站名稱。

    ![Contact manager application in Management Portal Websites tab][Contact manager application in Management Portal Websites tab]

3.  在視窗右側，按一下 [Download publish profile]。

    ![Quickstart tab and Download Publishing Profile button][Quickstart tab and Download Publishing Profile button]

    此步驟會下載一個檔案，內含您要將應用程式部署至網站所需要的所有設定。您將把此檔案匯入 Visual Studio，這樣您就不用手動輸入此資訊。

4.  將 .*publishsettings* 檔案儲存到資料夾中，以便您從 Visual Studio 存取。

    ![saving the .publishsettings file][saving the .publishsettings file]

    [WACOM.INCLUDE [publishsettingsfilewarningchunk](../includes/publishsettingsfilewarningchunk.md)]

5.  在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下專案，再選取內容功能表中的 [發行]。

    ![Publish in project context menu][Publish in project context menu]

    此時會開啟 [發行 Web] 精靈。

6.  在 [Publish Web] 精靈的 [設定檔] 索引標籤中，按一下 [匯入]。

    ![Import publish settings][Import publish settings]

    [匯入發行設定檔] 對話方塊隨即出現。

7.  如果您之前尚未將 Azure 訂閱新增至 Visual Studio，請執行下列步驟。在以下步驟中，您將新增訂閱，以便讓 [從 Azure 網站匯入] 下方的下拉式清單納入您的網站。

    a. 在 [匯入發行設定檔] 對話方塊中，按一下 [新增 Azure 訂閱]。

    ![add win az sub][add win az sub]

    b. 在 [匯入 Azure 訂閱] 對話方塊中，按一下 [下載訂閱檔案]。

    ![download sub][download sub]

    c. 在您的瀏覽器視窗中，儲存 *.publishsettings* 檔案。

    ![download pub file][download pub file]

    > [WACOM.NOTE]
    > .publishsettings 檔案包含用來管理 Azure 訂閱和服務的認證 (未編碼)。這個檔案的安全性最佳作法是暫時儲存在來源目錄之外 (例如在 Libraries\\Documents 資料夾)，然後在匯入完成後予以刪除。惡意使用者若取得 .publishsettings 檔案的存取權，就可以編輯、建立和刪除您的 Azure 服務。

    d. 在 [匯入 Azure 訂閱] 對話方塊中，按一下 [瀏覽] 並瀏覽至 *.publishsettings* 檔案。

    ![download sub][download sub]

    e. 按一下 [匯入]。

    ![import][import]

8.  在 [匯入發行設定檔] 對話方塊中，選取 [從 Azure 網站匯入]，接著從下拉式清單選取網站，再按一下 [確定]。

    ![Import Publish Profile][Import Publish Profile]

    您建立的應用程式現在正在雲端中執行。下次您部署應用程式時，只會部署變更的 (或新的) 檔案。

    ![To Do List home page running in Azure][To Do List home page running in Azure]

## <a name="bkmk_addadatabase"></a>新增資料庫至應用程式

接下來，您將更新 MVC 應用程式，以加上顯示和更新資料庫中的連絡人，以及在資料庫中儲存資料的能力。應用程式將使用 Entity Framework，以建立資料庫以及讀取和更新資料庫中的資料。

### 新增連絡人的資料模型類別

首先，您會在程式碼中建立簡單的資料模型。

1.  在 [方案總管]，於 Models 資料夾上按一下滑鼠右鍵，按一下 [新增]，再按一下 [類別]。

    ![Add Class in Models folder context menu][Add Class in Models folder context menu]

1.  在 [加入新項目] 對話方塊中，將新的類別檔案命名為 *Contact.cs*，再按一下 [新增]。

    ![Add New Item dialog box][Add New Item dialog box]

1.  以下列程式碼取代 Contacts.cs 檔案的內容。

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

## <a name="bkmk_addcontroller"></a>新增控制器和資料檢視

1.  建置專案 **(Ctrl+Shift+B)**。(使用樣板機制前必須先建置專案。)
2.  在 [方案總管]，於 Controllers 資料夾上按一下滑鼠右鍵，按一下 [新增]，再按一下 [控制器]。

    ![Add Controller in Controllers folder context menu][Add Controller in Controllers folder context menu]

3.  在 [加入控制器] 對話方塊中，輸入 "HomeController" 作為控制器名稱。
4.  將 [Scaffolding options] 範本設定為 [MVC Controller with read/write actions and views, using Entity Framework]。
5.  選取 [連絡人] 作為您的模型類別，並選取 [\<New data context...\>] 作為您的資料內容類別。

    ![Add Controller dialog box][Add Controller dialog box]

6.  在 [New Data Context] 對話方塊中，接受預設值 *ContactManager.Models.ContactManagerContext*。
    ![Add Controller dialog box][5]

7.  按一下 [確定]，然後在 [新增控制器] 對話方塊中按一下 [新增]。
8.  在 [新增控制器] 覆寫對話方塊中，確保所有選項皆已勾選，然後按一下 [確定]。

    ![Add Controller message box][Add Controller message box]

Visual Studio 隨即針對 **Contact** 物件的 CRUD 資料庫操作，建立控制器方法與檢視。

## 啟用移轉、建立資料庫、新增範例資料和資料初始設定式

下一個工作是啟用 [Code First 移轉][Code First 移轉]功能，以便根據建立的資料模型建立資料庫。

1.  在 [工具] 功能表中，依序選取 [Library Package Manager] 及 [Package Manager Console]。

    ![Package Manager Console in Tools menu][Package Manager Console in Tools menu]

2.  在 [Package Manager Console] 視窗中，輸入下列命令：

        enable-migrations -ContextTypeName ContactManagerContext

    ![enable-migrations][enable-migrations]
     您必須指定內容類型名稱 (**ContactManagerContext**)，因為該專案包含兩個 [DbContext][DbContext] 衍生的類別，包括我們剛建立的 **ContactManagerContext**，以及用在成員資格資料庫的 **UsersContext**。**ContactManagerContext** 類別已由 Visual Studio 硬板精靈新增。

     **enable-migrations** 命令會建立 *Migrations* 資料夾，並在該資料夾置入 *Configuration.cs* 檔案，您可以編輯該檔案來設定 [移轉]。

3.  在 [Package Manager Console] 視窗中，輸入下列命令：

        add-migration Initial

     **add-migration Initial** 命令會在 *Migrations* 資料夾中產生名為 **\<date\_stamp\>Initial** 的檔案，此檔案會建立資料庫。第一個參數 (**Initial**) 是任意的，用於建立檔案的名稱。您可以在 [方案總管] 中看到新的類別檔案。

    在 **Initial** 類別中，**Up** 方法會建立 Contacts 資料表，**Down** 方法 (當您希望返回前個狀態時使用) 則會捨棄該資料表。

4.  開啟 *Migrations\\Configuration.cs* 檔案。
5.  新增下列命名空間。

         using ContactManager.Models;

6.  以下列程式碼取代 *Seed* 方法：

        protected override void Seed(ContactManager.Models.ContactManagerContext context)
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

    以上的這個程式碼會以連絡人資訊初始化資料庫。如需植入資料庫的詳細資訊，請參閱[植入及偵錯 Entity Framework (EF) DB][植入及偵錯 Entity Framework (EF) DB]。

7.  在 [Package Manager Console] 中輸入命令：

        update-database

    ![Package Manager Console commands][Package Manager Console commands]

     **update-database** 會執行第一次移轉，使資料庫建立。根據預設，資料庫會以 SQL Server Express LocalDB 資料庫的形式建立。(除非您已安裝 SQL Server Express，這樣就能使用 SQL Server Express 執行個體來建立資料庫。)

8.  按 CTRL+F5 執行應用程式。

應用程式隨即顯示種子資料並提供編輯、詳細資料和刪除連結。

![MVC view of data][MVC view of data]

## <a name="addOauth"></a><span class="short-header">OAuth</span>新增 OAuth 提供者

[OAuth][OAuth] 是一種開放式通訊協定，可讓 Web、行動和桌面應用程式以簡單、標準的方法執行安全授權。ASP.NET MVC 網際網路範本使用 OAuth 來公開 Facebook、Twitter、Google、Yahoo 和 Microsoft 的驗證提供者身分。雖然本教學課程僅使用 Facebook、Google 與 Yahoo 作為驗證提供者，但您可以輕易修改程式碼來使用任何提供者。實作其他提供者的步驟，與您將在本教學課程中看到的步驟極為類似。

除了驗證，本教學課程也會使用角色來實作授權。只有您新增至 canEdit 角色的使用者才能建立、編輯或刪除連絡人。

## 使用外部提供者註冊

若要以一些外部提供者的認證來驗證使用者，您必須向網站註冊提供者並取得金鑰與連線密碼。Google 與 Yahoo 不會要求您註冊並取得金鑰。

本教學課程不會顯示註冊這些提供者時必須執行的所有步驟。這些步驟都很普通，不會非常困難。若要成功註冊您的網站，只需遵循這些網站上所提供的步驟即可。要開始註冊您的網站，請參閱以下提供者網站：

-   [Facebook][Facebook]
-   [Microsoft][Microsoft]
-   [Twitter][Twitter]

瀏覽至 [][]<https://developers.facebook.com/apps> 頁面，必要時請登入。按一下 [Register as a Developer] 按鈕並完成註冊程序。完成註冊後，按一下 [Create New App]。輸入應用程式的名稱。您不需要輸入應用程式命名空間。

![Create New FB app][Create New FB app]

在 [App Domain] 中輸入 localhost，並在 [網站 URL] 中輸入 \<http://localhost/\>。針對 [Sandbox Mode] 按一下 [啟用]，再按 [儲存變更]。

您需要 [應用程式識別碼] 與 [應用程式密鑰] 以便在此應用程式中實作 OAuth。
![New FB app][New FB app]

## 建立測試使用者

在左窗格的 [設定] 下方，按一下 [Developer Roles]。按一下 [測試使用者] 列 (而非 [測試者] 列) 上的 **[建立]連結 。

![FB testers][FB testers]

按一下 [修改] 連結以取得測試使用者電子郵件 (以便您用來登入應用程式)。按一下 [See More] 連結，然後按一下 [編輯] 來設定測試使用者密碼。

## 新增來自提供者的應用程式識別碼與密碼

開啟 *App\_Start\\AuthConfig.cs* 檔案。從 *RegisterFacebookClient* 方法中移除註解字元，然後新增應用程式識別碼與密碼。使用您取得的值，以下所示的值將無法運作。移除 *OAuthWebSecurity.RegisterGoogleClient* 呼叫中的註解字元，然後如下所示新增 *OAuthWebSecurity.RegisterYahooClient*。Google 與 Yahoo 提供者不會要求您註冊並取得金鑰。
警告：將您的應用程式識別碼與密碼放在安全的地方。擁有您的應用程式識別碼與密碼的惡意使用者將可假冒為您的應用程式。

     public static void RegisterAuth()
        {
            OAuthWebSecurity.RegisterFacebookClient(
                appId: "enter numeric key here",
                appSecret: "enter numeric secret here");

            OAuthWebSecurity.RegisterGoogleClient();
            OAuthWebSecurity.RegisterYahooClient();
        }

1.  執行應用程式並按一下 [登入] 連結。
2.  按一下 \[Facebook\] 按鈕。
3.  輸入您的 Facebook 認證或是其中一個測試使用者認證。
4.  按一下 [確定] 允許您的應用程式存取您的 Facebook 資源。
5.  系統會將您重新導向至 [註冊] 頁面。如果您是使用測試帳戶登入，則您可以將 [使用者名稱] 變更為較短的字串，例如 "Bill FB test"。按一下 [註冊] 按鈕，會將使用者名稱與電子郵件別名儲存至成員資格資料庫。
6.  註冊另一位使用者。目前登入系統內的程式錯誤，將無法讓您登出後以另一位使用者身分重新登入相同的服務提供者 (亦即，您無法登出 Facebook 帳戶後，以另一位 Facebook 帳戶身分重新登入)。若要解決這個問題，請使用不同的瀏覽器登入網站，並註冊另一位使用者。系統會將一位使用者新增至管理員角色，使其得以編輯應用程式，而另一位使用者則只能存取網站上的非編輯方法。匿名使用者只能存取首頁。
7.  向不同的提供者註冊另一位使用者。
8.  **選擇性**：您也可以建立與其中一個提供者沒有關聯的本機帳戶。本教學課程稍後將移除建立本機帳戶的能力。若要建立本機帳戶，按一下 [登出] 連結 (如果您目前登入的話)，然後按 [Register link]。您可能會想要建立未與任何外部驗證提供者關聯的本機帳戶以利進行管理。

## <a name="mbrDB"></a><span class="short-header">成員資格資料庫</span>新增角色至成員資格資料庫

在本節中，您會將 *canEdit* 角色新增至成員資格資料庫。只有 canEdit 角色中的使用者才能編輯資料。最佳做法是依角色可執行的動作來命名角色，因此將角色命名為 *canEdit* 會較命名為 *admin* 更好。隨著應用程式發展，您可以新增如 *canDeleteMembers* 等新角色，而非 *superAdmin*。

1.  在 [檢視] 功能表中，按一下 [伺服器總管]。

2.  在 [伺服器總管] 中，展開 [DefaultConnection]，然後展開 [資料表]。

3.  以滑鼠右鍵按一下 [UserProfile]，然後按一下 \[Show Table Data\]。

    ![Show table data][Show table data]

4.  記下即將擁有 canEdit 角色的使用者之 **UserId**。在下圖中，具有 **UserId** 2 的使用者 *ricka* 將擁有該網站的 canEdit 角色。

    ![user IDs][user IDs]

5.  以滑鼠右鍵按一下 [webpages\_Roles]，然後按一下 \[Show Table Data\]。
6.  在 **RoleName** 儲存格中輸入 **canEdit**。如果這是您首次新增角色，則 **RoleId** 將為 1。記下 RoleID。請記得裡面沒有尾端字元空格，角色資料表中的 "canEdit " 與控制器程式碼中的 "canEdit" 並不相符。

    ![roleID][roleID]

7.  以滑鼠右鍵按一下 [webpages UsersInRoles]，然後按一下 \[Show Table Data\]。針對您想要授予 *canEdit* 存取權的使用者輸入 **UserId** 與 **RoleId**。

    ![usr role ID tbl][usr role ID tbl]

**webpages\_OAuthMembership** 資料表內含每一位註冊之 OAuth 使用者的 OAuth 提供者、提供者 UserID 與 UserID。<!-- Don't replace "-" with "_" or it won't validate -->**webpages-Membership** 資料表包含 ASP.NET 成員資格角色。您可以透過註冊連結將使用者新增至此資料表。使用未與 Facebook 或另一個第三方授權提供者關聯的 *canEdit* 角色新增使用者是很理想的辦法，因為這樣一來，即便第三方授權提供者無法提供服務，您還是隨時擁有 *canEdit* 存取權。本教學課程稍後將停用 ASP.NET 成員資格註冊。

## 使用 Authorize 屬性保護應用程式

在本節中，我們將套用 [Authorize][Authorize] 屬性來限制對動作方法的存取。匿名使用者將只能檢視首頁。註冊使用者將能檢視連絡人詳細資料、關於頁面與連絡人頁面。只有 *canEdit* 角色中的使用者才能存取用來變更資料的動作方法。

1.  將 [Authorize][Authorize] 篩選器和 [RequireHttps][RequireHttps] 篩選器新增至應用程式。替代的方法是將 [Authorize][Authorize] 屬性和 [RequireHttps][RequireHttps] 屬性新增至每個控制器，但將這些屬性套用至整個應用程式是最安全的做法。藉由全面新增這些屬性，您所新增的每個新控制器和動作方法都會自動受到保護，而不需要您記得哪些已套用、哪些未套用。如需詳細資訊，請參閱[保護您的 ASP.NET MVC 4 應用程式和新 AllowAnonymous 屬性][保護您的 ASP.NET MVC 4 應用程式和新 AllowAnonymous 屬性]。開啟 *App\_Start\\FilterConfig.cs* 檔案並使用下列方法取代 *RegisterGlobalFilters* 方法。

        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());
        }

2.  將 [AllowAnonymous][保護您的 ASP.NET MVC 4 應用程式和新 AllowAnonymous 屬性] 屬性新增至 **Index** 方法。[AllowAnonymous][保護您的 ASP.NET MVC 4 應用程式和新 AllowAnonymous 屬性] 屬性能讓您將想要選擇略過授權的方法加到白名單。
3.  將 [Authorize(Roles = "canEdit")] 新增至可改變資料 (建立、編輯、刪除) 的 Get 與 Post 方法中。
4.  新增 *About* 與 *Contact* 方法。以下顯示完整程式碼的片段內容。

        public class HomeController : Controller
        {
            private ContactManagerContext db = new ContactManagerContext();
            [AllowAnonymous]
            public ActionResult Index()
            {
                return View(db.Contacts.ToList());
            }

            public ActionResult About()
            {
                return View();
            }

            public ActionResult Contact()
            {
                return View();
            }

            [Authorize(Roles = "canEdit")]
            public ActionResult Create()
            {
                return View();
            }
            // Methods moved and omitted for clarity.
        }

5.  移除 ASP.NET 成員資格註冊。專案中目前的 ASP.NET 成員資格註冊並未支援密碼重設，且不會確認註冊者是否為真人 (例如使用 [CAPTCHA][CAPTCHA])。使用者只要以其中一家協力廠商提供者通過驗證後，即可註冊。在 AccountController 中，從 GET 和 POST *Register* 方法移除 *[AllowAnonymous]*。它們將防止 Bot 和匿名使用者進行註冊。
6.  在 *Views\\Shared\_LoginPartial.cshtml* 中移除 Register 動作連結。
7.  啟用 SSL。在 [方案總管]，按一下 [ContactManager] 專案，再按一下 F4 帶出 [屬性] 對話方塊。將 [SSL 已啟用] 變更為 true。複製 [SSL URL]。

    ![enable SSL][enable SSL]

8.  在 [方案總管]，於 [Contact Manager] 專案上按一下滑鼠右鍵，再按一下 [屬性]。
9.  在左側索引標籤中按一下 [Web]。
10. 將 [專案 URL] 變更為使用 [SSL URL]。
11. 按一下 [建立虛擬目錄]。

    ![enable SSL][6]

12. 按 CTRL+F5 執行應用程式。瀏覽器將顯示憑證警告。就我們的應用程式而言，您可以放心地按一下 [繼續瀏覽此網站] 連結。確認只有 *canEdit* 角色中的使用者才能變更資料。確認匿名使用者只能檢視首頁。

    ![cert Warn][cert Warn]

    ![cert Warn][7]

Azure 網站內含有效的安全憑證，因此當您部署至 Azure 時，不會看到這個警告。

## <a name="ppd"></a><span class="short-header">準備資料庫</span>建立資料部署指令碼

</p>
成員資格資料庫並不是由 Entity Framework Code First 管理，因此您無法使用 Migrations 方式來加以部署。我們將使用 [dbDacFx][dbDacFx] 提供者來部署資料庫結構描述，並將發行設定檔設定為執行可將初始成員資格資料插入成員資格資料表中的指令碼。

本教學課程將使用 SQL Server Management Studio (SSMS) 來建立資料部署指令碼。

從 [Microsoft SQL Server 2012 Express 下載中心][Microsoft SQL Server 2012 Express 下載中心]安裝 SSMS：

-   適用 64 位元系統的 [ENU\x64\SQLManagementStudio\_x64\_ENU.exe][ENU\x64\SQLManagementStudio\_x64\_ENU.exe]。
-   適用 32 位元系統[ENU\x86\SQLManagementStudio\_x86\_ENU.exe][ENU\x86\SQLManagementStudio\_x86\_ENU.exe]。

如果您選擇了系統無法使用的程式，該程式將無法安裝，這時請嘗試下載另一個程式。

(請注意，此下載檔案大小有 600 MB。需要一些時間來安裝，並可能需要重新啟動電腦。)

在 SQL Server 安裝中心的第一頁，按一下 [新的 SQL Server 獨立安裝或將功能加入到現有安裝]，然後遵循指示進行，並接受預設選擇。下圖顯示會安裝 SSMS 的步驟。

![SQL Install][SQL Install]

### 建立開發資料庫指令碼

1.  執行 SSMS。
2.  在 [Connect to Server] 對話方塊中，輸入 *(localdb)\\v11.0* 作為伺服器名稱，並保留 [驗證] 設定為 [Windows 驗證]，然後按一下 [連線]。如果您已安裝 SQL Express，請輸入 **.\\SQLEXPRESS**。

    ![con to srvr dlg][con to srvr dlg]

3.  在 [物件總管] 視窗中，展開 [資料庫] 並以滑鼠右鍵按一下 [aspnet-ContactManager]，接著依序按一下 [工作] 及 [產生指令碼]。

    ![Gen Scripts][Gen Scripts]

4.  在 **Generate and Publish Scripts** 對話方塊中，按一下 [設定指令碼編寫選項]。
    您可以略過 [選擇物件] 步驟，因為預設是對整個資料庫與所有資料庫物件編寫指令碼，而這正是您要的結果。

5.  按一下 [進階]。

    ![Set scripting options][Set scripting options]

6.  在 [進階編寫指令碼選項] 對話方塊中，向下捲動至 [要編寫指令碼的資料類型]，然後按一下下拉式清單中的 [Data only] 選項。(請參閱下個步驟下方的圖片)。

7.  將 [編寫 USE DATABASE 的指令碼] 變更為 [False]。請在測試環境中，使用不適用 Azure SQL Database 且不需要部署至 SQL Server Express 中的陳述式。

    ![Set scripting options][8]

8.  按一下 [確定]。
9.  在 [產生並發佈指令碼] 對話方塊中，[檔案名稱] 方塊指定了要建立指令碼的位置。將路徑變更為您的解決方案資料夾 (內含您的 *Contacts.sln* 檔案的資料夾)，並將檔名變更為 *aspnet-data-membership.sql*。
10. 按一下 [下一步] 前往 [摘要] 索引標籤，然後再按 [下一步] 建立指令碼。

    ![Save or pub][Save or pub]

11. 按一下 [完成]。

## <a name="bkmk_deploytowindowsazure11"></a>將應用程式部署至 Azure

1.  開啟應用程式根目錄 *Web.config* 檔案。找到 *DefaultConnection* 標記，然後將其複製並貼上 *DefaultConnection* 標記線下方。重新命名複製的元素 *DefaultConnectionDeploy*。您將需要這個連接字串，以便將使用者資料部署至成員資格資料庫。
    ![3 cons str][3 cons str]

2.  建置應用程式。
3.  在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下專案，再選取內容功能表中的 [發行]。

    ![Publish in project context menu][9]

此時會開啟 [發行 Web] 精靈。

1.  按一下 [設定] 索引標籤。按一下 [v] 圖示，為 **ContactManagerContext** 與 **DefaultConnectionDeploy** 選取 [遠端連接字串]。列出的這三個資料庫將使用相同的連接字串。**ContactManagerContext** 資料庫會儲存連絡人，**DefaultConnectionDeploy** 則僅用來將使用者帳戶資料部署至成員資格資料庫，而 **UsersContext** 資料庫則是成員資格資料庫。

    ![settings][settings]

2.  在 [ContactManagerContext] 下勾選 [Execute Code First Migrations]。

    ![settings][10]

3.  在 **DefaultConnectionDeploy** 下方勾選 [更新資料庫]，然後按一下 [Configure database updates] 連結。
4.  按一下 [加入 SQL 指令碼] 連結，並瀏覽至 *aspnet-data-membership.sql* 檔案。您只需要做一次這個動作。下次部署時，您可以取消勾選 [更新資料庫]，因為您不會需要將使用者資料新增至成員資格資料表中。

    ![add sql][add sql]

5.  按一下 [發行]。
6.  瀏覽至 [][]<https://developers.facebook.com/apps></a> 頁面並將 [新增網域] 與 [網站 URL] 設定變更為 Azure URL。
7.  測試應用程式。確認只有 *canEdit* 角色中的使用者才能變更資料。確認匿名使用者只能檢視首頁。確認驗證的使用者能夠瀏覽至不會變更資料的所有連結。
8.  下次當您發行應用程式時，請務必取消勾選 **DefaultConnectionDeploy** 下方的 [更新資料庫]。

    ![settings][10]

## <a name="ppd2"></a><span class="short-header">更新資料庫</span>更新成員資格資料庫

一旦網站部署至 Azure，而且您增加了更多的註冊使用者，您可能想要將某些使用者新增為 *canEdit* 角色的成員。本節將使用 Visual Studio 連接至 SQL 資料庫，並將使用者新增至 *canEdit* 角色。

![settings][10]

1.  在 [方案總管] 中，於專案上按一下滑鼠右鍵，再按一下 [發行]。
    ![Publish][Publish]

2.  按一下 [設定] 索引標籤。
3.  複製連接字串。例如，本範例使用以下連接字串：
    Data Source=tcp:d015leqjqx.database.windows.net,1433;
    Initial Catalog=ContactDB2;User <Id=ricka0@d015lxyze;Password=xyzxyz>
4.  關閉發行對話方塊。
5.  在 [檢視] 功能表中，按一下 \[伺服器總管**\]。

6.  按一下 [連接至資料庫] 圖示。

    ![Publish][11]

7.  如果系統提示您輸入 [資料來源]，按一下 [Microsoft SQL Server]。
    ![Publish][12]

8.  複製並貼上前面帶有 *tcp* 字樣的 \[伺服器名稱] (請參見下圖)。
9.  按一下 [使用 SQL Server 驗證]
10. 輸入您的 [使用者名稱] 與 \[密碼] (內含在您所複製的連接字串裡面)。
11. 輸入資料庫名稱 (ContactDB，如果您的資料庫不叫做 ContactDB 的話，則為資料庫中 "Initial Catalog=" 之後的字串)。如果出現錯誤對話方塊，請參閱下一節。
12. 按一下 [測試連接]。如果出現錯誤對話方塊，請參閱下一節。
    ![add con dlg][add con dlg]

## 無法開啟伺服器登入錯誤

如果出現「無法開啟伺服器」開頭字串的錯誤對話方塊，則您需要將 IP 位址新增至允許的 IP 清單中。

![firewall error][firewall error]

1.  在 Azure 入口網站，選取左側索引標籤中的 [SQL Database]。

    ![Select SQL][Select SQL]

2.  選取要開啟的資料庫。

3.  按一下 [Set up Azure firewall rules for this IP address] 連結。

    ![firewall rules][firewall rules]

4.  當系統提示您「現有的防火牆規則不含目前的 IP 位址 xxx.xxx.xxx.xxx。是否要更新防火牆規則？」時，請按一下 [是]。新增此位址一般來說還不夠，您需要新增一段 IP 位址範圍。

## 新增一段允許的 IP 位址範圍

1.  在 Azure 入口網站，按一下 [SQL Database]。
2.  按一下主控您資料庫的 [伺服器]。

    ![db server][db server]

3.  按一下頁面頂端的 [設定]。
4.  新增規則名稱以及開頭與結尾 IP 位址。
    ![ip range][ip range]

5.  按一下頁面底部的 [儲存]。
6.  您現在可以使用上述的步驟編輯成員資格資料庫。

## <a name="nextsteps"></a><span class="short-header">後續步驟</span>後續步驟

本教學課程和範例應用程式是由 [Rick Anderson][Rick Anderson] (Twitter [@RickAndMSFT][@RickAndMSFT]) 在 Tom Dykstra、Tom FitzMacken 和 Barry Dorrans (Twitter [@blowdart][@blowdart]) 的協助下所撰寫。

如果您發現喜歡的地方或希望我們改善的地方 (不論是針對本教學課程或其示範的產品)，歡迎留下意見反應。您的意見反應將協助我們訂出優先改善要務。我們非常希望能了解您對於將設定和部署成員資格資料庫之程序更進一步自動化的期待為何。

若要檢視色彩豐富的 Facebook、Google 與 Yahoo 登入按鈕，請參閱部落格文章[自訂 ASP.NET MVC 4 中的外部登入按鈕][自訂 ASP.NET MVC 4 中的外部登入按鈕] (英文)。如需使用 Windows 驗證的詳細資訊，請參閱以下說明：

-   [Azure 驗證][Azure 驗證]
-   [如何使用 ASP.NET MVC 建立內部網站][如何使用 ASP.NET MVC 建立內部網站]

另一個儲存 Azure 應用程式資料的方法是使用 Azure 儲存體，它能以 Blob 和資料表的形式提供非關聯式的資料儲存。以下連結提供 ASP.NET MVC 及 Window Azure 的詳細資訊。

-   [使用儲存體資料表、佇列與 Blob 的 .NET 多層式應用程式][使用儲存體資料表、佇列與 Blob 的 .NET 多層式應用程式]。
-   [ASP.NET MVC 4 入門][ASP.NET MVC 4 入門]
-   [使用 MVC 的 Entity Framework 入門][使用 MVC 的 Entity Framework 入門]
-   [OAuth 2.0 與登入][OAuth 2.0 與登入]

您已了解如何將 Web 應用程式部署至 Azure 網站。如需深入了解如何設定、管理與調整 Azure 網站大小，請參閱[常見工作][常見工作]頁面上的〈作法〉主題。

如需了解如何偵錯 Azure 網站，請參閱[在 Visual Studio 中疑難排解 Azure 網站][在 Visual Studio 中疑難排解 Azure 網站]。

如需瞭解如何將應用程式部署至 Azure 雲端服務，請參閱[本教學課程的雲端版本][本教學課程的雲端版本]與[使用 Azure 開發 Web 應用程式][使用 Azure 開發 Web 應用程式]。您可能會因為下列某些原因，選擇在 Azure 雲端服務下 (而不是使用 Azure 網站) 執行 ASP.NET Web 應用程式：

-   您想要取得執行該應用程式的 Web 伺服器管理員權限。
-   您想要使用遠端桌面連線存取執行該應用程式的 Web 伺服器。
-   您的應用程式為多層次，而您想要將工作平均分配到多個虛擬伺服器上 (Web 與工作者)。

如需同時瞭解 SQL Database 與 Azure 儲存體的詳細資訊，請參閱 [Azure 的資料儲存方案][Azure 的資料儲存方案] (英文)。

如需深入了解如何使用 SQL Database，請參閱[在 ASP.NET 資料存取內容地圖中使用 Azure SQL Database][在 ASP.NET 資料存取內容地圖中使用 Azure SQL Database] (英文)。

如需深入了解 Entity Framework 與 Code First 移轉，請參閱下列資源：

-   [使用 MVC 的 Entity Framework 入門][使用 MVC 的 Entity Framework 入門]
-   [Code First 移轉][13]



  [login page]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb.png
  [設定開發環境]: #bkmk_setupdevenv
  [設定 Azure 環境]: #bkmk_setupwindowsazure
  [建立 ASP.NET MVC 4 應用程式]: #bkmk_createmvc4app
  [將應用程式部署至 Azure]: #bkmk_deploytowindowsazure1
  [新增資料庫至應用程式]: #bkmk_addadatabase
  [新增 OAuth 提供者]: #addOauth
  [新增角色至成員資格資料庫]: #mbrDB
  [建立資料部署指令碼]: #ppd
  [1]: #bkmk_deploytowindowsazure11
  [更新成員資格資料庫]: #ppd2
  [後續步驟]: #nextsteps
  [Azure SDK for Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=254364
  [Web Platform Installer - Azure SDK for .NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/WebPIAzureSdk20NetVS12.png
  [Azure 管理入口網站]: https://manage.windowsazure.com
  [New button in Management Portal]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxWSnew2.png
  [Create with Database link in Management Portal]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB.png
  [2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxCreateWSwithDB_2.png
  [Database Settings step of New Website - Create with Database wizard]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-004.png
  [3]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxPrevDB.png
  [New Project dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-002.png
  [New ASP.NET MVC 4 Project dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxb2.png
  [\_Layout.cshtml in Solution Explorer]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-createapp-004.png
  [To Do List home page]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxa.png
  [4]: http://manage.windowsazure.com "入口網站"
  [Contact manager application in Management Portal Websites tab]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-setup-azure-site-006.png
  [Quickstart tab and Download Publishing Profile button]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-download-profile.png
  [saving the .publishsettings file]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-save-profile.png
  [Publish in project context menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/PublishVSSolution.png
  [Import publish settings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishSettings.png
  [add win az sub]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzAddWAsub.png
  [download sub]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDownLoad.png
  [download pub file]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzDown2.png
  [import]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rzImp.png
  [Import Publish Profile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/ImportPublishProfile.png
  [To Do List home page running in Azure]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/newapp005.png
  [Add Class in Models folder context menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-001.png
  [Add New Item dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-adddatabase-002.png
  [Add Controller in Controllers folder context menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-context-menu.png
  [Add Controller dialog box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-controller-add-controller-dialog.png
  [5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNewCtx.png
  [Add Controller message box]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxOverwrite.png
  [Code First 移轉]: http://msdn.microsoft.com/library/hh770484.aspx
  [Package Manager Console in Tools menu]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-menu.png
  [enable-migrations]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxE.png
  [DbContext]: http://msdn.microsoft.com/zh-tw/library/system.data.entity.dbcontext(v=VS.103).aspx
  [植入及偵錯 Entity Framework (EF) DB]: http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx
  [Package Manager Console commands]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-migrations-package-manager-console.png
  [MVC view of data]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx2.png
  [OAuth]: http://oauth.net/ "http://oauth.net/"
  [Facebook]: http://developers.facebook.com/
  [Microsoft]: http://go.microsoft.com/fwlink/?LinkID=144070
  [Twitter]: http://dev.twitter.com/
  []: https://developers.facebook.com/apps/
  [Create New FB app]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBapp.png
  [New FB app]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFB.png
  [FB testers]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxFBt.png
  [Show table data]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSTD.png
  [user IDs]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUid.png
  [roleID]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxRoleID.png
  [usr role ID tbl]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxUR.png
  [Authorize]: http://msdn.microsoft.com/zh-tw/library/system.web.mvc.authorizeattribute(v=vs.100).aspx
  [RequireHttps]: http://msdn.microsoft.com/zh-tw/library/system.web.mvc.requirehttpsattribute(v=vs.108).aspx
  [保護您的 ASP.NET MVC 4 應用程式和新 AllowAnonymous 屬性]: http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx
  [CAPTCHA]: http://www.asp.net/web-pages/tutorials/security/16-adding-security-and-membership
  [enable SSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSSL.png
  [6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxS2.png
  [cert Warn]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT.png
  [7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxNOT2.png
  [dbDacFx]: http://msdn.microsoft.com/zh-tw/library/dd394698.aspx
  [Microsoft SQL Server 2012 Express 下載中心]: http://www.microsoft.com/zh-tw/download/details.aspx?id=29062
  [SQL Install]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSS.png
  [con to srvr dlg]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxC2S.png
  [Gen Scripts]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxGenScripts.png
  [Set scripting options]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx11.png
  [8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAdv.png
  [Save or pub]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx1.png
  [3 cons str]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxd.png
  [9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/dntutmobile-deploy1-publish-001.png
  [settings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxD2.png
  [10]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxSettings.png
  [add sql]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxAddSQL2.png
  [Publish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxP.png
  [11]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rxc.png
  [12]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx3.png
  [add con dlg]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx4.png
  [firewall error]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx5.png
  [Select SQL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx6.png
  [firewall rules]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx7.png
  [db server]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx8.png
  [ip range]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2012/rx9.png
  [Rick Anderson]: http://blogs.msdn.com/b/rickandy/
  [@RickAndMSFT]: https://twitter.com/RickAndMSFT
  [@blowdart]: https://twitter.com/blowdart
  [自訂 ASP.NET MVC 4 中的外部登入按鈕]: http://www.beabigrockstar.com/customizing-external-login-buttons-in-asp-net-mvc-4/
  [Azure 驗證]: http://www.asp.net/vnext/overview/fall-2012-update/windows-azure-authentication
  [如何使用 ASP.NET MVC 建立內部網站]: http://msdn.microsoft.com/zh-tw/library/gg703322(v=vs.98).aspx
  [使用儲存體資料表、佇列與 Blob 的 .NET 多層式應用程式]: http://www.windowsazure.com/zh-tw/develop/net/tutorials/multi-tier-web-site/1-overview/
  [ASP.NET MVC 4 入門]: http://www.asp.net/mvc/tutorials/mvc-4/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4
  [使用 MVC 的 Entity Framework 入門]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
  [OAuth 2.0 與登入]: http://blogs.msdn.com/b/vbertocci/archive/2013/01/02/oauth-2-0-and-sign-in.aspx
  [常見工作]: http://www.windowsazure.com/zh-tw/develop/net/common-tasks/
  [在 Visual Studio 中疑難排解 Azure 網站]: /zh-tw/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [本教學課程的雲端版本]: http://www.windowsazure.com/zh-tw/develop/net/tutorials/cloud-service-with-sql-database/
  [使用 Azure 開發 Web 應用程式]: http://msdn.microsoft.com/zh-tw/library/Hh674484
  [Azure 的資料儲存方案]: http://social.technet.microsoft.com/wiki/contents/articles/data-storage-offerings-on-the-windows-azure-platform.aspx
  [在 ASP.NET 資料存取內容地圖中使用 Azure SQL Database]: http://go.microsoft.com/fwlink/p/?LinkId=282414#ssdb
  [13]: http://msdn.microsoft.com/zh-tw/library/hh770484
