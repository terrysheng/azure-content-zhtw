<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Developing Single Tenant Applications with Azure Active Directory" authors="" solutions="" manager="" editor="" />

使用 Azure Active Directory 開發單一租用戶應用程式
==================================================

簡介
----

本教學課程說明如何利用 Azure Active Directory 來驗證目錄使用者，以及從目錄中讀取其資料。您將了解如何：

-   在客戶的租用戶中佈建 Web 應用程式
-   使用 WS-同盟保護應用程式
-   使用 Graph API 來存取目錄

### 必要條件

此逐步解說需要下列開發環境必要條件：

-   Visual Studio 2010 SP1
-   Microsoft .NET Framework 4.0
-   [ASP.NET MVC 3](http://www.microsoft.com/en-us/download/details.aspx?id=4211)
-   [Windows Identity Foundation 1.0 Runtime](http://www.microsoft.com/en-us/download/details.aspx?id=17331)
-   [Windows Identity Foundation 3.5 SDK](http://www.microsoft.com/en-us/download/details.aspx?id=4451)
-   [WCF Data Services for OData V3](http://www.microsoft.com/download/en/details.aspx?id=29306)
-   Internet Information Services (IIS) 7.5 (已啟用 SSL)
-   Windows PowerShell
-   [Office 365 PowerShell Commandlets](http://onlinehelp.microsoft.com/en-us/office365-enterprises/ff652560.aspx)

### 目錄

-   [簡介](#introduction)
-   [步驟 1：建立 ASP.NET MVC 應用程式](#createapp)
-   [步驟 2：在公司的目錄租用戶中佈建應用程式](#provisionapp)
-   [步驟 3：使用 WS-同盟進行員工登入來保護應用程式](#protectapp)
-   [步驟 4：使用 Graph API 來讀取目錄資料](#readdata)
-   [摘要](#summary)

步驟 1：建立 ASP.NET MVC 應用程式
---------------------------------

此步驟描述如何建立將代表受保護資源的簡單 ASP.NET MVC 應用程式。將透過公司 STS 管理的結盟驗證，授與資源此的存取權，稍後在教學課程會有所描述。

1.  以系統管理員身分開啟 Visual Studio。
2.  從 **[檔案]** 功能表，按一下 **[新增專案]**。
3.  從左邊的範本功能表中，選取 **[Web]**，然後按一下 **[ASP.NET MVC 3 Web 應用程式]**。將專案命名為 *OrgIdFederationSample*。
4.  在 **[新 ASP.NET MVC 3 專案]** 對話方塊中，選取 **[空白]** 範本，然後按一下 **[確定]**。
5.  在 Visual Studio 產生新專案之後，在 **[方案總管]** 的 **Controllers** 資料夾上按一下滑鼠右鍵、按一下 **[新增]**，然後按一下 **[控制器]**。
6.  在 **[新增控制器]** 對話方塊上，將新的控制器命名為 *HomeController.cs*，然後按一下 **[新增]**。
7.  將建立並開啟 **HomeController.cs** 檔案。在程式碼檔案的 ***Index()*** 方法上按一下滑鼠右鍵，然後按一下 **[新增檢視...]**。
8.  在 **[新增檢視]** 對話方塊上，按一下 **[確定]**。將在名稱為 **Home** 的新資料夾中建立 **Index.cshtml** 檔案。
9.  在 **[方案總管]** 的 **OrgIdFederationSample** 專案上按一下滑鼠右鍵，然後按一下 **[內容]**。
10. 在內容視窗上，按一下左功能表上的 **[Web]**，然後選取 **[使用本機 IIS Web 伺服器]**。可能出現一個對話方塊，要求您為專案建立一個虛擬目錄；按一下對話方塊上的 **[是]**。
11. 建置並執行應用程式。首頁控制器的索引頁面將出現。

步驟 2：在公司的目錄租用戶中佈建應用程式
----------------------------------------

此步驟描述 Azure Active Directory 客戶的系統管理員如何在其租用戶中佈建 MVC 應用程式，並設定單一登入。在完成此步驟之後，公司的員工可以使用其 Office 365 帳戶進行 Web 應用程式驗證。

佈建程序開始先為應用程式建立新的服務主體。服務主體是由 Azure Active Directory 用來向目錄註冊並驗證應用程式。

1.  下載並安裝 Office 365 PowerShell Commandlets (如果尚未這樣做)。
2.  從 **[開始]** 功能表中，執行 **[適用於 Windows PowerShell 的 Microsoft Online Services 模組]** 主控台。此主控台提供命令行環境，用於設定 Office 365 租用戶的相關屬性，例如建立和修改服務主體。
3.  若要匯入必要的 **MSOnlineExtended** 模組，請輸入下列命令並按 Enter 鍵：

         Import-Module MSOnlineExtended -Force

4.  若要連接至 Office 365 目錄，您將需要提供公司的系統管理員認證。輸入下列命令並按 Enter 鍵，然後在提示中輸入您的認證：

         Connect-MsolService

5.  現在您將為應用程式建立新的服務主體。輸入下列命令並按 Enter 鍵：

         New-MsolServicePrincipal -ServicePrincipalNames @("OrgIdFederationSample/localhost") -DisplayName "Federation Sample Web Site" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    此步驟將輸出如下的資訊：

         The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
        DisplayName           :Federation Sample Web Site
        ServicePrincipalNames :{OrgIdFederationSample/localhost}
        ObjectId              :59cab09a-3f5d-4e86-999c-2e69f682d90d
        AppPrincipalId        :7829c758-2bef-43df-a685-717089474505
        TrustedForDelegation  :False
        AccountEnabled        :True
        KeyType               :Symmetric
        KeyId                 :f1735cbe-aa46-421b-8a1c-03b8f9bb3565
        StartDate             :12/01/2012 08:00:00 a.m.
        EndDate               :12/01/2013 08:00:00 a.m.
        Usage                 :Verify 

    **注意**

    您應該儲存此輸出，尤其是產生的對稱金鑰。此金鑰只會在建立服務主體期間顯示給您，而且日後您將無法擷取它。需要其他值，才能使用圖形 API 來讀取和寫入目錄中的資訊。

6.  最終步驟為您的應用程式設定回覆 URL。回覆 URL 為嘗試驗證之後傳送回應的目的地。輸入下列命令並按 Enter 鍵：

         $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost/OrgIdFederationSample" 

        Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

現在已在目錄中佈建 Web 應用程式，而且公司員工可以使用它，進行 Web 單一登入。

步驟 3：使用 WS-同盟進行員工登入來保護應用程式
----------------------------------------------

此步驟顯示如何使用 Windows Identity Foundation (WIF) 新增結盟登入的支援。您也將新增登入頁面，並在應用程式與目錄租用戶之間設定信任。

1.  在 Visual Studio 的 **OrgIdFederationSample** 專案上按一下滑鼠右鍵，然後選取 **["Add STS reference..."]**.安裝 WIF SDK 時，已新增此內容功能表。
2.  在 **[Federation Utility]** 對話方塊的 **[應用程式 URI]** 下，您需要提供下列格式的 URI：

         spn:<Your AppPrincipalId>@<Your Directory Domain>

    **spn** 指定 URI 為服務主體名稱、**Your AppPrincpalId** 為您建立服務主體時所產生的 **AppPrincipalId** GUID 值，以及 **Your Directory Domain** 為目錄租用戶的 \*.onmicrosoft.com 網域。對於此範例應用程式，會指定完整應用程式 URI 值，如下所示：

         spn:7829c758-2bef-43df-a685-717089474505@awesomecomputers.onmicrosoft.com

    在輸入了應用程式 URI 之後，請按 **[下一步]**。

3.  將出現警告對話方塊，描述「未在安全 https 連接上主控應用程式。」原因為同盟公用程式無法辨識 **spn:**格式，但是應用程式無論如何仍將使用安全連接。按一下 **[是]** 以繼續。

4.  在同盟公用程式的下一個頁面上，選取 **[Use an existing STS]**，然後在 **[STS WS-Federation metadata document location]** 下，輸入 WS-同盟中繼資料文件的 URL。此 URL 是以下列格式指定：

         https://accounts.accesscontrol.windows.net/<Domain Name or Tenant ID>/FederationMetadata/2007-06/FederationMetadata.xml 

    對於此應用程式，會指定 WS-同盟中繼資料位置，如下所示：

         https://accounts.accesscontrol.windows.net/fabrikam.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml 

    在輸入了中繼資料位置之後，請按 **[下一步]**。

5.  在同盟公用程式的下一個頁面上，選取 **[Disable certificate chain validation]**，然後按 **[下一步]**。

6.  在同盟公用程式的下一個頁面上，選取 **[不加密]**，然後按 **[下一步]**。

7.  同盟公用程式的下一個頁面即會顯示 STS 所提供的宣告。檢閱宣告，然後按 **[下一步]**。

8.  接著，您將對開發環境設定 Internet Information Services (IIS)，以支援 SSL。若要開啟 IIS 管理員，您可以在 **[執行]** 提示中輸入 *inetmgr*。

9.  在 IIS 管理員中，展開左功能表中的 **Sites** 資料夾，然後按一下 **[預設網站首頁]**。從右邊的 **[動作]** 中，按一下 **[繫結...]**。

10. 在 **[站台繫結]** 對話方塊上，按一下 **[新增]**。在 **[新增站台繫結]** 對話方塊上，將 **[類型]** 下拉式清單變更為 ***https***，然後在 **[SSL 憑證]** 下，選取 **[IIS Express Development Certificate]**。按一下 **[確定]**。

11. 在 IIS 管理員中，按一下左功能表中的 **[應用程式集區]**，然後選取清單中的 **[ASP.NET v4.0]** 項目，並從右邊的 **[動作]** 功能表按一下 **[進階設定]**。

12. 在 **[進階設定]** 對話方塊上，將 **[載入使用者設定檔]** 內容設為 **true**。按一下 **[確定]**。

13. 在 Visual Studio 中，從 **[方案總管]** 的專案根目錄中開啟 **Web.config** 檔案.

14. 在 **Web.config** 檔案中，找出 **wsFederation** 區段，並新增 **reply** 屬性，其值與您建立服務主體時所指定的 **\$replyUrl** 變數值相同。例如：

        <wsFederation passiveRedirectEnabled="true" issuer="https://accounts.accesscontrol.windows.net/v2/wsfederation" realm="spn:7829c758-2bef-43df-a685-717089474505" requireHttps="false" reply="https://localhost/OrgIdFederationSample" /> 

15. 在 **system.web** 區段內新增 **httpRuntime** 節點，將 **requestValidationMode** 屬性設為 **2.0**。例如：

        <system.web>
        <httpRuntime requestValidationMode="2.0" /> 
            ...

    儲存 **Web.config** 檔案。

16. 既然您已對 Web 應用程式啟用驗證，那麼應該修改 **[索引]** 頁面，以呈現已驗證的使用者宣告。開啟位於 **Views** 資料夾的 **Home** 資料夾的 **Index.cshtml** 檔案。

17. 新增下列程式碼片段至 **Index.cshtml** 檔案，並儲存它：

        <p>
        @if (User.Identity.IsAuthenticated)
            {
        <ul>
        @foreach (string claim in ((Microsoft.IdentityModel.Claims.IClaimsIdentity)this.User.Identity).Claims.Select(c => c.ClaimType + " :" + c.Value))
                {
        <li>@claim</li>
                }
        </ul>
            }
        </p> 

18. 在您已將變更儲存至 **Index.cshtml** 檔案之後，請按 **F5** 來執行應用程式。您將被重新導向至 Office 365 身分識別提供者頁面，在此頁面中您可以使用目錄租用戶認證進行登入。例如，*john.doe@awesomecomputers.onmicrosoft.com*。

19. 在已使用認證進行登入之後，您將被重新導向至首頁控制品的索引頁面，您帳戶的宣告會顯示在此頁面中。這會示範使用者順利使用 Azure Active Directory 所提供的單一登入進行應用程式驗證。

步驟 4：使用 Graph API 來讀取目錄資料
-------------------------------------

此步驟說明如何使用 Graph API 來連線至 Azure Active Directory 租用戶並讀取資料。為了協助您開始使用 Graph API，請下載下列 ASP.NET 應用程式：[Graph API 的範例應用程式與撰寫支援](http://code.msdn.microsoft.com/Write-Sample-App-for-79e55502)。此應用程式包含協助方法讓您輕鬆地驗證和要求 Graph API。

您也需要將權限加入至您在步驟 2 中建立的應用程式服務主體。若要加入這些權限，您需要 AppPrincipalId 值。

1.  下載範例應用程式並解壓縮至您要的資料夾。
2.  您必須先提供其他權限給服務主體，才能使用範例程式碼。這些權限可讓服務主體使用 Graph API 來讀取資料。從 **[開始]** 功能表中，執行 **[適用於 Windows PowerShell 的 Microsoft Online Services 模組]** 主控台。
3.  您需要將服務主體加入至服務支援管理員角色，以提供讀取權限給它。如需有關指派角色給服務主體的詳細資訊，請參閱＜[Graph API 的以角色為基礎的存取控制](http://msdn.microsoft.com/en-us/library/hh974466.aspx)。輸入下列命令並按 Enter 鍵：

         Add-MsolRoleMember -RoleMemberType "ServicePrincipal" -RoleName "Service Support Administrator" -RoleMemberObjectId $appPrincipal.ObjectId 

4.  **\$appPrincipal.ObjectId** 變數是服務主體的 objectId，可輸入下列命令並按 Enter 來取得：

         Get-MsolServicePrincipal -AppPrincipalId 7829c758-2bef-43df-a685-717089474505 

    **注意**

    **在步驟 2 中建立服務主體時會傳回上述的 AppPrincipalId 值。

5.  將角色加入至服務主體之後，啟動 Visual Studio 並開啟範例應用程式的根 **Web.config** 檔案。

6.  在 **Web.config** 中找出 *&lt;configuration\>* 區段，然後瀏覽至 *&lt;appSettings\>* 區段。將 *TenantDomainName*、*AppPrincipalId* 和 *SymmetricKey* 機碼的值變更為服務主體的值。例如：

         <appSettings> 
             ...
        <add key="TenantDomainName" value="awesomecomputers.onmicrosoft.com"/> 
             ...
        <add key="AppPrincipalId" value="7829c758-2bef-43df-a685-717089474505"/>     
             ...
        <add key="SymmetricKey" value="qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=" /> 
             ...
        </appSettings>

7.  儲存 **Web.config** 檔案，然後按 **F5** 執行應用程式。

8.  範例應用程式會顯示功能表以存取所有使用者、公司管理員等。按一下其中一個連結會使用 Graph API 來傳回租用戶中儲存的資訊。

摘要
----

本教學課程已顯示如何建立和設定單一租用戶應用程式，來使用 Azure Active Directory 的單一登入功能。此外，您也使用 Graph API 來存取租用戶的目錄資料。建議您探索範例應用程式，以了解如何在您自己的應用程式中善用 Graph API。

若要深入了解 API，[您可以在 MSDN 上閱讀相關資訊](http://msdn.microsoft.com/en-us/library/hh974476.aspx)。您也可以閱讀下列教學課程，為 Azure Active Directory 建立多租用戶應用程式：[利用 Azure Active Directory 開發多租用戶雲端應用程式](http://g.microsoftonline.com/0AX00en/121) (英文)。

