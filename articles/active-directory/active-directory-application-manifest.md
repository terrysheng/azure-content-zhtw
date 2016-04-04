<properties
   pageTitle="了解 Azure Active Directory 應用程式資訊清單 | Microsoft Azure"
   description="詳細說明如何使用 Azure Active Directory 應用程式資訊清單；此清單代表應用程式在 Azure AD 租用戶中的身分識別組態，可用來加速 OAuth 授權、同意體驗和更多作業。"
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/16/2016"
   ms.author="dkershaw;bryanla"/>

# 了解 Azure Active Directory 應用程式資訊清單

與 Azure Active Directory (AD) 整合的應用程式必須向 Azure AD 租用戶註冊，提供應用程式的持續性身分識別組態。在執行階段參考此組態，啟用允許應用程式透過 Azure AD 外部和代理驗證/授權的案例。如需 Azure AD 應用程式模型的詳細資訊，請參閱[新增、更新及移除應用程式][ADD-UPD-RMV-APP]一文。

## 更新應用程式的身分識別組態

實際上有多個可用的選項可更新應用程式的身分識別組態屬性，隨著功能與困難度而有所不同，包括下列選項：

- **[Azure 傳統入口網站的][AZURE-CLASSIC-PORTAL] Web 使用者介面**可讓您更新應用程式的最常見內容。這是更新應用程式屬性最快速且最不容易有錯誤的方法，但無法提供您所有屬性的完整存取權，如同下面兩個方法。
- 對於更進階的案例 (您在其中必須更新 Azure 傳統入口網站中未公開的屬性)，您可以修改**應用程式資訊清單**。這是本文的重點，並且會在下一節中開始詳細討論。
- 它也可**撰寫使用[圖形 API][GRAPH-API] 的應用程式**來更新您的應用程式，這樣是最費力的。如果您要撰寫管理軟體或需要自動定期更新應用程式屬性，這可能是個不錯的選擇。

## 使用應用程式資訊清單來更新應用程式的身分識別組態
透過 [Azure 傳統入口網站][AZURE-CLASSIC-PORTAL]，您可以藉由下載和上傳 JSON 檔案表示法 (又稱為應用程式資訊清單) 管理應用程式的身分識別組態。沒有實際的檔案儲存在目錄中 - 應用程式資訊清單只是 Azure AD 圖形 API 應用程式實體上的 HTTP GET 作業，且上傳是應用程式實體上的 HTTP PATCH 作業。

因此，若要了解應用程式資訊清單的格式和屬性，您必須參考圖形 API [應用程式實體][APPLICATION-ENTITY] 文件。可透過應用程式資訊清單上傳執行的更新範例包括：

- 宣告您的 web API 所公開的權限範圍 (oauth2Permissions)。如需使用 oauth2Permissions 委派權限範圍實作使用者模擬的相關資訊，請參閱[整合應用程式與 Azure Active Directory][INTEGRATING-APPLICATIONS-AAD] 中的「公開 Web API 至其他應用程式」主題。如先前所述，所有應用程式實體屬性都記錄在圖形 API [實體和複雜類型參考][APPLICATION-ENTITY]參考文章中，包括屬於 [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION] 類型之集合的 oauth2Permissions 屬性。
- 宣告您的應用程式所公開的應用程式角色 (appRoles)。應用程式實體的 appRole 屬性是 [AppRole][APPLICATION-ENTITY-APP-ROLE] 類型的集合。請參閱[雲端應用程式中使用 Azure AD 的角色型存取控制][RBAC-CLOUD-APPS-AZUREAD]一文以取得實作範例。
- 宣告已知的用戶端應用程式 (knownClientApplications)，可讓您以邏輯方式將指定用戶端應用程式的同意繫結至資源/Web API。
- 要求 Azure AD 對登入使用者發出群組成員資格宣告 (groupMembershipClaims)。注意：可設定為額外發出有關使用者目錄角色成員資格的宣告。請參閱[雲端應用程式中使用 AD 群組的授權][AAD-GROUPS-FOR-AUTHORIZATION]一文以取得實作範例。
- 可讓您的應用程式支援 OAuth 2.0 隱含授權流程 (oauth2AllowImplicitFlow)。此類型的授權流程可用於內嵌的 JavaScript 網頁或單一頁面應用程式 (SPA)。
- 啟用 X509 憑證做為秘密金鑰 (keyCredentials)。如需實作範例，請參閱[在 Office 365 中建置服務與精靈應用程式][O365-SERVICE-DAEMON-APPS]和[利用 Azure 資源管理員 API 進行驗證開發人員指南][DEV-GUIDE-TO-AUTH-WITH-ARM]等文章。

應用程式資訊清單也會提供追蹤應用程式註冊狀態的好方法。因為它可供 JSON 格式使用，所以檔案表示法可以簽入您的原始檔控制，以及應用程式的原始程式碼。

## 逐步執行範例
現在可逐步了解透過應用程式資訊清單更新您的應用程式身分識別組態所需的步驟：我們會加強說明上述範例之一，顯示如何宣告資源應用程式新的權限範圍：

1. 瀏覽至 [Azure 傳統入口網站][AZURE-CLASSIC-PORTAL]並使用具有服務管理員或共同管理員權限的帳戶登入。

2. 在您驗證之後，向下捲動並選取左瀏覽面板中的 Azure "Active Directory" 延伸模組 (1)，然後按一下您的應用程式註冊所在的 Azure AD 租用戶 (2)。

    ![選取 Azure AD 租用戶][SELECT-AZURE-AD-TENANT]

3. 出現 [目錄] 頁面時，按一下頁面頂端的 [應用程式] (1) 以查看在租用戶中註冊的應用程式清單。然後找到您要在清單中更新的應用程式並按一下它 (2)。

    ![選取 Azure AD 租用戶][SELECT-AZURE-AD-APP]

4. 既然您已選取應用程式的主頁面，請注意頁面底部的「管理資訊清單」功能 (1)。如果您按一下此連結，系統將提示您下載或上傳 JSON 資訊清單檔案。按一下 [下載資訊清單] (2)，其後會立即出現下載確認對話方塊，提示您按一下 [下載資訊清單] (3) 加以確認，然後在本機開啟或儲存檔案 (4)。

    ![管理資訊清單、下載選項][MANAGE-MANIFEST-DOWNLOAD]

    ![下載資訊清單][DOWNLOAD-MANIFEST]

5. 在此範例中，我們在本機儲存檔案，讓我們可以在編輯器中開啟、對 JSON 進行變更然後再次儲存。以下是 JSON 結構在 Visual Studio JSON 編輯器內的外觀。請注意，它會遵循[應用程式實體][APPLICATION-ENTITY]的結構描述，如同我們先前所述：

    ![更新資訊清單 JSON][UPDATE-MANIFEST]

    例如，假設我們想要在資源應用程式 (API) 上實作/公開名為 "Employees.Read.All" 的新權限，您只需將新的/第二個元素新增至 oauth2Permissions 集合即可，即：

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    項目必須是唯一的，因此您必須為 `"id"` 屬性產生新的全域唯一識別碼 (GUID)。在此情況下，由於我們已指定 `"type": "User"`，此權限可以由資源/API 應用程式註冊所在的 Azure AD 租用戶所驗證的任何帳戶進行同意，授與代表帳戶加以存取的用戶端應用程式權限。在同意期間會使用說明和顯示名稱字串，並且顯示在 Azure 傳統入口網站中。

6. 當您完成更新資訊清單，請返回 Azure 傳統入口網站中的 Azure AD 應用程式頁面，再次按一下 [管理資訊清單] 功能 (1)，但是這次請選取 [上傳資訊清單] 選項 (2)。類似於下載，您將會看見第二個對話方塊，提示您 JSON 檔案的位置。按一下 [瀏覽檔案...] (3)，然後使用 [選擇要上傳的檔案] 對話方塊來選取 JSON 檔案 (4) 並按下 [開啟]。一旦對話方塊消失，請選取 [確定] 核取記號 (5)，隨即會上傳您的資訊清單。

    ![管理資訊清單、上傳選項][MANAGE-MANIFEST-UPLOAD]

    ![上傳資訊清單 JSON][UPLOAD-MANIFEST]

    ![上傳資訊清單 JSON - 確認][UPLOAD-MANIFEST-CONFIRM]

在資訊清單儲存後，現在您可以將已註冊的用戶端應用程式存取權提供到我們先前新增的新權限，但這次您可以使用 Azure 傳統入口網站的 Web UI，而無須編輯用戶端應用程式的資訊清單：

1. 首先，移至要新增新 API 之存取權的用戶端應用程式的 [設定] 頁面，然後按一下 [新增應用程式] 按鈕。
2. 畫面中會顯示租用戶中已註冊的資源應用程式 (API) 的清單。按一下資源應用程式名稱旁的加號 (+)，加以選取。  
3. 然後，按一下右下角的核取記號。
4. 當您回到用戶端組態頁面的 [新增應用程式] 區段時，您會在清單中看到新的資源應用程式。如果您將游標移至該資料列右側的 [委派的權限] 區段上方，您會看到顯示的下拉式清單。按一下該清單，然後選取新的權限，以將其新增至用戶端要求的權限清單。注意：這個新的權限會儲存在用戶端應用程式身分識別組態的 "requiredResourceAccess" 集合屬性中。

![其他應用程式的權限][PERMS-TO-OTHER-APPS]

![其他應用程式的權限][PERMS-SELECT-APP]

![其他應用程式的權限][PERMS-SELECT-PERMS]

就這麼簡單。現在，您的應用程式將會使用其新的身分識別組態來執行。

## 後續步驟
請使用以下的 DISQUS 註解區段提供意見反應，並協助我們改善與形塑我們的內容。

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#AppRoleType
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

<!---HONumber=AcomDC_0323_2016-->