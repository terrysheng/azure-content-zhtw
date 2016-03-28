<properties
   pageTitle="在入口網站中建立 AD 應用程式和服務主體 | Microsoft Azure"
   description="描述如何建立可以與 Azure 資源管理員中的角色型存取控制搭配使用來管理資源存取權的新 Active Directory 應用程式和服務主體。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="tomfitz"/>

# 使用入口網站建立 Active Directory 應用程式和服務主體

## 概觀
當您的自動化程序或應用程式必須存取或修改資源時，您可以使用傳統入口網站來建立 Active Directory 應用程式。當您經由入口網站建立 Active Directory 應用程式時，入口網戰其實會建立應用程式和服務主體。您可以用應用程式自己的身分識別或應用程式之登入使用者的身分識別執行應用程式。這兩種應用程式驗證方法分別稱為互動式 (使用者登入) 和非互動式 (應用程式提供自己的認證) 方法。在非互動式模式中，您必須將服務主體指派給具有正確權限的角色。

本主題說明如何使用 Azure 入口網站來建立新的應用程式和服務主體。目前，您必須使用傳統入口網站來建立新的 Active Directory 應用程式。Azure 入口網站會在稍後的版本中新增這項功能 。您可以使用入口網站來將應用程式指派給角色。您也可以透過 Azure PowerShell 或 Azure CLI 執行下列步驟。如需了解搭配服務主體來使用 PowerShell 或 CLI 的詳細資訊，請參閱[使用 Azure 資源管理員驗證服務主體](resource-group-authenticate-service-principal.md)。

## 概念
1. Azure Active Directory (AAD) - 雲端的身分識別與存取管理服務組建。如需更多詳細資訊，請參閱：[什麼是 Azure Active Directory](active-directory/active-directory-whatis.md)
2. 服務主體 - 目錄中應用程式的執行個體。
3. AD 應用程式 - AAD 中向 AAD 識別應用程式的目錄記錄。 

如需應用程式和服務主體的詳細說明，請參閱[應用程式物件和服務主體物件](active-directory/active-directory-application-objects.md)。如需 Active Directory 驗證的詳細資訊，請參閱 [Azure AD 的驗證案例](active-directory/active-directory-authentication-scenarios.md)。


## 建立應用程式

對於互動式和非互動式應用程式，您必須建立和設定 Active Directory 應用程式。

1. 透過[傳統入口網站](https://manage.windowsazure.com/)登入 Azure 帳戶。

2. 從左窗格中，選取 [**Active Directory**]。

     ![選取 Active Directory][1]

3. 選取您想要用來建立新應用程式的目錄。

     ![選擇目錄][2]

3. 若要檢視目錄中的應用程式，請按一下 [**應用程式**]。

     ![檢視應用程式][11]

4. 如果您之前尚未在該目錄中建立應用程式，則應該會看到與下面類似的映像。按一下 [**新增應用程式**]。

     ![新增應用程式][6]

     或者，按一下下方窗格中的 [**新增**]。

     ![新增][12]

5. 選取您想要建立的應用程式類型。在本教學課程中，我們不會使用組件庫中的應用程式。

     ![新的應用程式][10]

6. 填寫應用程式名稱，然後選取您想要使用的應用程式類型。選取您要建立的應用程式類型。在本教學課程中，我們將選擇建立 [WEB 應用程式和/或 WEB API]，然後按 [下一步] 按鈕。

     ![名稱應用程式][9]

7. 填寫您應用程式的屬性。針對 [**登入 URL**]，提供描述您應用程式之網站的 URI。不會驗證網站是否存在。針對 [**應用程式識別碼 URI**]，提供識別您應用程式的 URI。不會驗證端點的唯一性或其是否存在。如果您已選取 [原生用戶端應用程式] 做為應用程式類型，您將需要提供 [重新導向 URI] 值。按一下 [**完成**] 建立 AAD 應用程式。

     ![應用程式屬性][4]

您已建立好應用程式。

## 取得用戶端識別碼和租用戶識別碼

以程式設計方式存取應用程式時，您需要應用程式的識別碼。選取 [設定] 索引標籤並複製 [用戶端識別碼]。
  
   ![用戶端識別碼][5]

在某些情況下，您需要將您的驗證要求傳遞給租用戶識別碼。針對 Web Apps 和 Web API Apps，您可以透過選取畫面底部的 [檢視端點]，然後擷取如下所示的識別碼，來擷取租用戶識別碼。

   ![租用戶識別碼](./media/resource-group-create-service-principal-portal/save-tenant.png)

端點不適用於原生用戶端應用程式。相反地，您可以透過 PowerShell 來擷取租用戶識別碼︰

    PS C:\> Get-AzureRmSubscription

或者，Azure CLI：

    azure account show --json

## 建立驗證金鑰

如果應用程式要使用自己的認證來執行，您必須為應用程式建立金鑰。

1. 按一下 [**設定**] 索引標籤設定您應用程式的密碼。

     ![設定應用程式][3]

2. 向下捲動至 [**金鑰**] 區段，並選取您想要的密碼有效時間。

     ![金鑰][7]

3. 選取 [**儲存**] 建立金鑰。

     ![儲存][13]

     即會顯示儲存的金鑰，而且您可以進行複製。您將無法在稍後擷取金鑰，所以您應該會想現在就複製金鑰。

     ![儲存的金鑰][8]

您的應用程式現在已就緒，並且已在租用戶上建立服務主體。以服務主體身分登入時，請務必使用：

* **用戶端識別碼** - 做為使用者名稱。
* **金鑰** - 做為密碼。

## 設定委派權限

如果應用程式代表登入使用者來存取資源，您必須對應用程式授與用來存取其他應用程式的委派權限。您可以在 [設定] 索引標籤的 [其他應用程式的權限] 區段中進行此作業。根據預設，Azure Active Directory 已啟用委派權限。請將這個委派權限保持不變。

1. 選取 [新增應用程式]。

2. 在清單中選取 [Azure 服務管理 API]。

      ![選取應用程式](./media/resource-group-create-service-principal-portal/select-app.png)

3. 為服務管理 API 新增 [存取 Azure 服務管理 (預覽)] 委派權限。

       ![選取權限](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. 儲存變更。

## 設定多租用戶應用程式

如果其他 Azure Active Directory 的使用者可以連線到應用程式並登入，您必須啟用多重租用。在 [設定] 索引標籤上，將 [應用程式為多租用戶] 設定為 [是]。

![多租用戶](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## 將應用程式指派給角色

如果應用程式不是用登入使用者的身分識別來執行，您必須將應用程式指派給角色，以授與它執行動作的權限。如要指派應用程式給角色，請從傳統入口網站切換到 [Azure 入口網站](https://portal.azure.com)。您必須決定要讓哪個角色新增應用程式，以及範圍為何。如要深入了解可用的角色，請參閱 [RBAC：內建角色](./active-directory/role-based-access-built-in-roles.md)。您可以針對訂用帳戶、資源群組或資源的層級設定範圍。較低的範圍層級會繼承較高層級的權限 (舉例來說，為資源群組的讀取者角色新增應用程式，代表該角色可以讀取資源群組及其所包含的任何資源)。

1. 在入口網站中，瀏覽至您想要讓應用程式指派至的範圍層級。而對於本主題，您可以瀏覽至某個資源群組，然後選取 [資源群組] 刀鋒視窗上的**存取**圖示。

     ![選取使用者](./media/resource-group-create-service-principal-portal/select-users.png)

2. 選取 [新增]。

     ![選取 [新增]](./media/resource-group-create-service-principal-portal/select-add.png)

3. 選取 [讀取者] 角色 (或是任何您想要讓應用程式指派至的角色)。

     ![選取角色](./media/resource-group-create-service-principal-portal/select-role.png)

4. 當您第一次看到您可以新增至該角色的使用者清單時，您不會看到應用程式，只會看到群組及使用者。

     ![顯示使用者](./media/resource-group-create-service-principal-portal/show-users.png)

5. 如要尋找您的應用程式，您必須搜尋它。請開始輸入您應用程式的名稱，此時畫面上的可用選項清單會逐漸變更。當您在清單中看到您的應用程式時，請選取該應用程式。

     ![指派至角色](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. 選取 [確定] 來結束角色的指派。現在您應該會在指派至該資源群組中某個角色的清單中，看到自己的應用程式。

     ![顯示](./media/resource-group-create-service-principal-portal/show-app.png)

如需透過入口網站將使用者和應用程式指派給角色的詳細資訊，請參閱[使用 Azure 管理入口網站管理存取權](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal)。

## 以程式碼取得存取權杖

如果您使用 .NET，便可以利用下列程式碼為應用程式擷取存取權杖。

首先，您必須將 Active Directory Authentication Library 安裝至您的 Visual Studio 專案。執行這項工作最簡單的方式便是使用 NuGet 封裝。開啟 [Package Manager 主控台]，並輸入下列命令。

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

若要使用用戶端識別碼和密碼來登入，請使用下列方法來擷取權杖。

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{client id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

若要代表使用者登入，請使用下列方法來擷取權杖。

    public static string GetAcessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", {client id}, new Uri({redirect uri});

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

您可以使用下列程式碼在要求標頭中傳遞權杖：

    string token = GetAcessToken();
    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);


## 後續步驟

- 如要了解如何指定安全性原則，請參閱[Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。  
- 若要取得這些步驟的示範影片，請參閱[利用 Azure Active Directory 啟用 Azure 資源的程式化管理](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory)。
- 若要了解如何使用 Azure PowerShell 或 Azure CLI 來搭配 Active Directory 應用程式和服務主體，包括如何使用憑證進行驗證，請參閱[使用 Azure 資源管理員驗證服務主體](./resource-group-authenticate-service-principal.md)。
- 如需使用 Azure 資源管理員來實作安全性的指南，請參閱 [Azure 資源管理員的安全性考量](best-practices-resource-manager-security.md)


<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!---HONumber=AcomDC_0316_2016-->