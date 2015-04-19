<properties 
	pageTitle="在 Azure 網站中使用 LOB 應用程式與 Azure AD" 
	description="瞭解如何在 Azure 網站中建立 ASP.NET LOB 應用程式來驗證 Azure Active Directory" 
	services="web-sites, active-directory" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="02/12/2015" 
	ms.author="cephalin"/>

# 在使用 Azure Active Directory 驗證的 Azure 網站中建立 ASP.NET MVC 特定業務應用程式 #

在本文中，您會學習如何將 [Azure Active Directory](http://azure.microsoft.com/services/active-directory/) (AAD) 做為身分識別提供者，在 [Azure 網站](http://azure.microsoft.com/services/websites/)中輕易建立 ASP.NET MVC 特定業務 (LOB) 應用程式。您也將學習如何在應用程式中使用 [Azure AD Graph 用戶端程式庫](http://blogs.msdn.com/b/aadgraphteam/archive/2014/06/02/azure-active-directory-graph-client-library-1-0-publish.aspx) (英文) 來查詢目錄資料。

您使用的 AAD 租用戶可以擁有純 Azure 目錄，或可以是與您的內部部署 Active Directory (AD) 同步處理的目錄，以便為內部部署或遠端的工作者建立單一登入體驗。

如需 Azure 網站的不同企業驗證和授權選項的概觀，請參閱[在 Azure 網站的 LOB 應用程式中驗證及授權使用者](web-sites-authentication-authorization.md)。

<a name="bkmk_build"></a>
## 將建置的項目 ##

您將在 Azure 網站中建置簡單的 LOB 建立-讀取-更新-刪除 (CRUD) 應用程式，使用下列功能追蹤工作項目：

- 針對 AAD 驗證使用者
- 登入和登出功能
- 使用 [`[Authorize]`] 來授權使用者使用不同的 CRUD 動作
- 使用 [Azure AD Graph API](http://msdn.microsoft.com/library/azure/hh974476.aspx) 查詢 AAD 資料
- 使用 ASP.NET 的明日之星 [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (而不是 Windows Identity Foundation，也就是 WIF) 進行驗證和授權的設定，會比使用 WIF 容易得多

<a name="bkmk_need"></a>
## 必要元件 ##

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

您需要下列項目完成本教學課程：

- AAD 租用戶與不同群組中的使用者
- 在 AAD 租用戶上建立應用程式的權限
- Visual Studio 2013 

<a name="bkmk_sample"></a>
## 使用 LOB 範本的範例應用程式 ##

在本教學課程中，範例應用程式 [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet) 是由 AAD 團隊所建立，可以當做範本來輕鬆建立新的 LOB 應用程式。它有下列內建功能：

- 使用 [OpenID Connect](http://openid.net/connect/) 以驗證 AAD
- 包含 AAD 搜尋篩選器的 `Roles` 控制器，可讓您輕鬆地將 AAD 使用者或群組對應至應用程式角色。
- 範例 `TaskTracker` 控制器，示範如何在應用程式中為特定的動作授權不同的角色，包括 `[Authorize]` 的標準用法。 

![](./media/web-sites-dotnet-lob-application-azure-ad/role-management.png)

<a name="bkmk_run" />
## 執行範例應用程式 ##

1.	在 [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet) 上，將範例解決方案複製或下載到您的本機目錄。
2.	依照 [README.md](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/GroupClaims/README.md) 的指示設定 AAD 應用程式和專案。

	> [AZURE.NOTE] AAD 應用程式中設定的權限只需要<strong>使用者</strong>角色，而非**全域管理員**。
	
3.	完成應用程式設定後，按 `F5` 執行應用程式。
4.	載入應用程式後，按一下 [**登入**]。 
5.	如果您正確設定 AAD 應用程式，並在 Web.config 中設定對應的設定，您應該重新導向至登入。只需使用您在 Azure 入口網站中用來建立 AAD 應用程式的帳戶即可登入，因為它是 AAD 應用程式的預設擁有者。 
	
	> [AZURE.NOTE] 在 Startup.Auth.cs 範例專案中，請注意應用程式有稱為做 <code>AddOwnerAdminClaim</code> 的方法，用來將應用程式擁有者加入至系統管理員角色。這可讓您立即在 <code>Roles</code> 控制站中開始管理應用程式角色。
	
4.	登入後，按一下 [**角色**] 管理應用程式角色。
5.	在 [**搜尋使用者/群組**] 中，開始輸入所需的使用者名稱或群組名稱，請注意下拉式清單會顯示從 AAD 租用戶篩選的使用者及/或群組清單。

	![](./media/web-sites-dotnet-lob-application-azure-ad/select-user-group.png) 

	> [AZURE.NOTE] 在 Views\Roles\Index.cshtml 中，您會看到檢視使用名為 <code>AadPicker</code> (定義於 Scripts\AadPickerLibrary.js) 的 JavaScript 物件來存取<code>角色</code>控制站中的 <code>Search</code> 動作。
		<pre class="prettyprint">var searchUrl = window.location.protocol + "//" + window.location.host + "<mark>/Roles/Search</mark>";
	...
    var picker = new <mark>AadPicker(searchUrl, maxResultsPerPage, input, token, tenant)</mark>;</pre>
		在 Controllers\RolesController.cs，您會看到 <code>Search</code> 動作，它會將實際的要求傳送至 AAD Graph API，並將回應傳回頁面。
		稍後您將使用相同的方法，在您的應用程式中建立簡單的功能。

6.	從下拉式清單中選取使用者或群組，選取角色，然後按一下 [**指派角色**]。

<a name="bkmk_deploy"></a>
## 將範例應用程式部署到 Azure 網站

您會在這裡將應用程式發行至 Azure 網站。[README.md](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/GroupClaims/README.md) 已有部署至 Azure 網站的指示，不過這些步驟也會使您本機偵錯環境中的組態失效。我將告訴您如何在保留偵錯組態的情況下部署。

1. 以滑鼠右鍵按一下專案，然後選取 [**發行**]。

	![](./media/web-sites-dotnet-lob-application-azure-ad/publish-app.png)

2. 選取 [**Microsoft Azure 網站**]。
3. 如果您還沒有登入 Azure，按一下 [**登入**] 和使用您的 Azure 訂閱的 Microsoft 帳戶登入。
4. 登入之後，按一下 [**新增**] 來建立新的網站。
5. 填寫所有必要欄位。您必須具備資料庫連線，此應用程式才能儲存角色對應、快取的權杖及任何應用程式資料。

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-website.png)

6. 按一下 [**建立**]。一旦建立網站後，會開啟 [發行 Web] 對話方塊。
7. 在 [**目的地 URL**] 中，將 [**http**] 變更為 [**https**]。將完整的 URL 複製到文字編輯器。您稍後將使用它。然後按 [**下一步**]。

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-change-to-https.png)

8. 清除 [**啟用組織驗證**] 核取方塊。

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-disable-organizational-authentication.png)

9. 不要按一下 [**發行**] 進行 web 發行，請按一下 [**關閉**]。按一下 [**是**] 將變更儲存至發行設定檔。
2. 在 [[Azure 管理入口網站](https://manage.windowsazure.com)] 中，移至 AAD 租用戶並按一下 [**應用程式**] 索引標籤。
2. 按一下頁面底部的 [**新增**]。
3. 選取 [**新增組織正在開發的應用程式**]。
4. 指定應用程式的名稱，並且按一下 [**下一步**]。
5. 在 [應用程式屬性] 中，將 [**登入 URL**] 設為您稍早儲存的網站 URL (例如  `https://<site-name>.azurewebsites.net`)，並將 [**應用程式識別碼 URI**] 設為 [ `https://<aad-tenanet-name>/<app-name>`]。然後，按一下 [**完成**]。

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-app-properties.png)

6. 建立應用程式後，按一下 [**設定**]。
7. 在 [**索引鍵**] 下，在下拉式清單中選取 [**1 年**] 來建立新索引鍵。
8. 在 [**其他應用程式的權限**] 下，於 [**委派權限**] 下拉式清單中為 [**Windows Azure Active Directory**] 項目選取 [**存取您的組織目錄**]。

	> [AZURE.NOTE] 您在這裡需要的實際權限取決於您應用程式所需的功能。有些權限需要設定**全域管理員**角色，但本教學課程所需的權限只需要**使用者**角色。

9.  按一下 [**儲存**]。  
10.  離開已儲存的組態頁面之前，請將下列資訊複製到文字編輯器。

	-	用戶端識別碼
	-	索引鍵 (如果離開該頁面，您將無法再看到索引鍵)

11. 在 Visual Studio，開啟專案中的 [**Web.Release.config**]。將下列 XML 插入 `<組態>` 標記中，並以您為新 AAD 應用程式儲存的資訊來取代每個索引鍵的值。  
	<pre class="prettyprint">
&lt;appSettings&gt;
   &lt;add key="ida:ClientId" value="<mark>[e.g. 82692da5-a86f-44c9-9d53-2f88d52b478b]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
   &lt;add key="ida:AppKey" value="<mark>[e.g. rZJJ9bHSi/cYnYwmQFxLYDn/6EfnrnIfKoNzv9NKgbo=]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
   &lt;add key="ida:PostLogoutRedirectUri" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
&lt;/appSettings&gt;</pre>

	請確定 ida:PostLogoutRedirectUri 值的結尾有一條斜線 "/"。

1. 在您的專案上按一下滑鼠右鍵，然後選取 [**發行**]。
2. 按一下 [**發行**] 以發行至 Azure 網站。

當您完成時，您會在 Azure 管理入口網站中設定兩個 AAD 應用程式，一個適用於 Visual Studio 中的偵錯環境，另一個適用於 Azure 中的已發行網站。在偵錯期間，Web.config 中的應用程式設定可讓您的**偵錯**組態使用 ADD，且在發行後 (預設會發行**版本**組態) 會上傳轉換的 Web.config，其中包含 Web.Release.config 的應用程式設定變更。

如果您想要將發行的網站附加到偵錯工具 (也就是您必須上傳已發行網站中的程式碼偵錯符號)，您可以建立偵錯組態的複本進行 Azure 偵錯，但使用其自訂的 Web.config 轉換 (例如 Web.AzureDebug.config)，從 Web.Release.config 使用 AAD 設定。這可讓您跨不同的環境維護靜態組態。

<a name="bkmk_crud"></a>
## 將 LOB 功能新增至範例應用程式

在這部分的教學課程中，您將學習如何根據範例應用程式建立所需的 LOB 功能。您將建立簡單 CRUD 工作項目追蹤程式，類似於 TaskTracker 控制器，但使用標準的 CRUD 建構和設計模式。您也會使用隨附的 Scripts\AadPickerLibrary.js，使用 AAD Graph API 的資料豐富您的應用程式。  

5.	在 Models 資料夾中，建立稱為 WorkItem.cs 的新模型，並以下列程式碼取代該程式碼：

	<pre>using System.ComponentModel.DataAnnotations;
		
		namespace WebAppGroupClaimsDotNet.Models
		{
		    public class WorkItem
		    {
		        [Key]
		        public int ItemID { get; set; }
		        public string AssignedToID { get; set; }
		        public string AssignedToName { get; set; }
		        public string Description { get; set; }
		        public WorkItemStatus Status { get; set; }
		    }
		
		    public enum WorkItemStatus
		    {
		        Open, 
		        Investigating, 
		        Resolved, 
		        Closed
		    }
		}</pre>

6.	開啟 DAL\GroupClaimContext.cs 並新增反白顯示的程式碼：  
	<pre class="prettyprint">
    public class GroupClaimContext : DbContext
    {
        public GroupClaimContext() : base("GroupClaimContext") { }

        public DbSet&lt;RoleMapping&gt; RoleMappings { get; set; }
        public DbSet&lt;Task&gt; Tasks { get; set; }
        <mark>public DbSet&lt;WorkItem&gt; WorkItems { get; set; }</mark>
        public DbSet&lt;TokenCacheEntry&gt; TokenCacheEntries { get; set; }
    }</pre>

7.	建置專案，讓 Visual Studio 中的建構邏輯可存取新的模型。
8.	將新的建構項目  `WorkItemsController` 新增至 Controllers 資料夾。若要這樣做，請以滑鼠右鍵按一下 [**控制器**]，指向 [**新增**]，然後選取 [**新增建構項目**]。 
9.	選取 [**使用 Entity Framework，包含檢視的 MVC 5 Controller**]，再按一下 [**新增**]。
10.	選取您剛才建立的模型，然後按一下 [**新增**]。

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-add-scaffolded-controller.png)

9.	開啟 Controllers\WorkItemsController.cs

11. 將反白顯示的 `[Authorize]` 裝飾新增至下列的相應動作。
	<pre class="prettyprint">
	...

    <mark>[Authorize(Roles = "Admin, Observer, Writer, Approver")]</mark>
    public class WorkItemsController : Controller
    {
		...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public ActionResult Create()
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Create([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Edit(int? id)
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Edit([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
        ...

        <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
        public async Task&lt;ActionResult&gt; Delete(int? id)
        ...

        <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
        public async Task&lt;ActionResult&gt; DeleteConfirmed(int id)
        ...
	}</pre>

	由於您負責 Roles 控制器中的角色對應，因此您只需要確定每個動作都授權適當的角色。

	> [AZURE.NOTE] 您可能已經在部分動作上注意到 <code>[ValidateAntiForgeryToken]</code> 裝飾。基於 [Brock Allen](https://twitter.com/BrockLAllen) 在 [MVC 4、AntiForgeryToken 和宣告 ](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/) 中描述的行為，您的 HTTP POST 的防偽權杖驗證可能會失敗，因為：
	> + AAD 不會傳送防偽權杖預設所需要的 http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider。
	> + 如果 AAD 目錄與 AD FS 進行同步處理，AD FS 信任預設不會傳送 http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider 宣告，但您可以手動設定 AD FS 來傳送此宣告。
	> 您會在下一步這麼做。

12.  在 App_Start\Startup.Auth.cs，在  `ConfigureAuth` 方法中加入下行程式碼：

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	`ClaimTypes.NameIdentifies` 會指定 AAD 提供的宣告 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`。既然您已完成授權處理 (說真的，不會太久)，您可以投入時間來執行實際的功能。 

13.	在 Create () 和 Edit() 中新增下列程式碼，讓 JavaScript 稍後可以使用一些變數：
            ViewData["token"] = GraphHelper.AcquireToken(ClaimsPrincipal.Current.FindFirst(Globals.ObjectIdClaimType).Value);
            ViewData["tenant"] = ConfigHelper.Tenant;
14.	在 Views\WorkItems\Create.cshtml (自動建構的項目) 中尋找 `Html.BeginForm` Helper 方法，並如下所示修改它：  
	<pre class="prettyprint">@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>))
	{
	    @Html.AntiForgeryToken()
	    
	    &lt;div class="form-horizontal"&gt;
	        &lt;h4&gt;WorkItem&lt;/h4&gt;
	        &lt;hr /&gt;
	        @Html.ValidationSummary(true, "", new { @class = "text-danger" })
	
	        &lt;div class="form-group"&gt;
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type=&quot;hidden&quot;</mark> } })
	                @Html.ValidationMessageFor(model =&gt; model.AssignedToID, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
	                @Html.ValidationMessageFor(model =&gt; model.AssignedToName, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.Description, new { htmlAttributes = new { @class = "form-control" } })
	                @Html.ValidationMessageFor(model =&gt; model.Description, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EnumDropDownListFor(model =&gt; model.Status, htmlAttributes: new { @class = "form-control" })
	                @Html.ValidationMessageFor(model =&gt; model.Status, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            &lt;div class="col-md-offset-2 col-md-10"&gt;
	                &lt;input type="submit" value="Create" class="btn btn-default" <mark>id="submit-button"</mark> /&gt;
	            &lt;/div&gt;
	        &lt;/div&gt;
	    &lt;/div&gt;
	
	    <mark>&lt;script&gt;
	            // People/Group Picker Code
	            var maxResultsPerPage = 14;
	            var searchUrl = window.location.protocol + "//" + window.location.host + "/Roles/Search";
	            var input = document.getElementById("AssignedToName");
	            var token = "@ViewData["token"]";
	            var tenant = "@ViewData["tenant"]";
	
	            var picker = new AadPicker(searchUrl, maxResultsPerPage, input, token, tenant);
	
	            // Submit the selected user/group to be asssigned.
	            $("#submit-button").click({ picker: picker }, function () {
	                if (!picker.Selected())
	                    return;
	                $("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
	            });
	    &lt;/script&gt;</mark>
	
	}</pre>

	在指令碼，AAD 選擇器物件會搜尋符合輸入的 AAD 使用者和群組的 `~/Roles/Search` 動作。然後，按一下 [提交] 按鈕時，AAD 選擇器物件會儲存使用者識別碼來隱藏 `AssignedToID` 欄位。  

15. 現在請在 Visual Studio 偵錯工具中執行應用程式，或是發行至 Azure 網站。以應用程式擁有者身分登入，並瀏覽至 `~/WorkItems/Create`。從我已發行的 LOB 應用程式中，瀏覽到 `https://mylobapp.azurewebsites.net/WorkItems/Create`。您現在會發現：您可以使用同一個 AAD 選擇器搜尋篩選器來挑選 AAD 使用者。

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-workitem.png)

16. 填寫表單的其餘部分，並按一下 [**建立**]。~/WorkItems/Index 頁面現在會顯示新建立的工作項目。您還會看到下面的螢幕擷取畫面，顯示我移除了 Views\WorkItems\Index.cshtml 中的 `AssignedToID` 資料行。 

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-workitem-index.png)

11.	現在，對 [**編輯**] 檢視做類似的變更。在 Views\WorkItems\Edit.cshtml 中，對  `Html.BeginForm` Helper 方法 (與上個步驟中 Views\WorkItems\Create.cshtml 的方法相同) 進行變更 (將上述反白顯示的程式碼中的 "Create" 取代成 "Edit")。

就這麼簡單！

既然您已為 WorkItems 控制器中的不同動作設定授權和 LOB 功能，您可以嘗試以不同應用程式角色的使用者身分登入。

![](./media/web-sites-dotnet-lob-application-azure-ad/11-edit-unauthorized.png)

<a name="bkmk_resources"></a>
## 進一步資源

- [使用 SSL 和 Authorize 屬性保護應用程式](../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/#protect-the-application-with-ssl-and-the-authorize-attribute)
- [在 Azure 網站的 LOB 應用程式中驗證及授權使用者](web-sites-authentication-authorization.md)
- [在使用 AD FS 驗證的 Azure 網站中建立 ASP.NET MVC 特定業務應用程式](web-sites-dotnet-lob-application-adfs.md)
- [Microsoft Azure Active Directory 範例與文件](https://github.com/AzureADSamples)
- [Vittorio Bertocci 的部落格](http://blogs.msdn.com/b/vbertocci/) (英文)
- [將 VS2013 Web 專案從 WIF 遷移到 Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/) (英文)
- [Azure 的新混合式連線不是您父執輩的 #hybridCloud](http://azure.microsoft.com/documentation/videos/new-hybrid-connections-not-your-fathers-hybridcloud/) (英文)
- [Active Directory 與 Azure AD 之間的相似處](http://technet.microsoft.com/ library/dn518177.aspx) (英文)
- [搭配單一登入的目錄同步作業實例](http://technet.microsoft.com/ library/dn441213.aspx) (英文)
- [Azure AD 支援的權杖和宣告類型](http://msdn.microsoft.com/library/azure/dn195587.aspx) (英文)

<!--HONumber=45--> 
