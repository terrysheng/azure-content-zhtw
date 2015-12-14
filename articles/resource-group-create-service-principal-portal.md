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
   ms.date="12/01/2015"
   ms.author="tomfitz"/>

# 使用入口網站建立 Active Directory 應用程式和服務主體

## 概觀
當您有需要存取或修改訂用帳戶中資源的自動化程序或應用程式時，您可以使用入口網站來建立 Active Directory 應用程式，並將它指派給擁有正確權限的角色。當您經由入口網站建立 Active Directory 應用程式時，將會同時建立應用程式和服務主體。於設定權限時使用服務主體。

本主題顯示如何使用 Azure 入口網站建立新的應用程式和服務主體。目前，您必須使用 Microsoft Azure 入口網站來建立新的 Active Directory 應用程式。在更新的版本中，會將這項功能加入至 Azure Preview 入口網站。您可以使用 Preview 入口網站將應用程式指派給角色。您也可以透過 Azure PowerShell 或 Azure CLI 執行下列步驟。如需詳細資訊，請參閱[使用 Azure 資源管理員驗證服務主體](resource-group-authenticate-service-principal.md)。

## 概念
1. Azure Active Directory (AAD) - 雲端的身分識別與存取管理服務組建。如需詳細資料，請參閱：[什麼是 Azure Active Directory](active-directory/active-directory-whatis.md)。
2. 服務主體 - 目錄中應用程式的執行個體。
3. AD 應用程式 - AAD 中向 AAD 識別應用程式的目錄記錄。 

如需應用程式和服務主體的詳細說明，請參閱[應用程式物件和服務主體物件](active-directory/active-directory-application-objects.md)。如需 Active Directory 驗證的詳細資訊，請參閱 [Azure AD 的驗證案例](active-directory/active-directory-authentication-scenarios.md)。


## 建立應用程式和服務主體物件

1. 透過[入口網站](https://manage.windowsazure.com/)登入 Azure 帳戶。

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

6. 填寫應用程式名稱，然後選取您想要使用的應用程式類型。因為我們想要搭配使用這個應用程式的服務主體與 Azure 資源管理員來進行驗證，所以將選擇建立 **WEB 應用程式和 (或) WEB API**，然後按 [下一步] 按鈕。

     ![名稱應用程式][9]

7. 填寫您應用程式的屬性。針對 [**登入 URL**]，提供描述您應用程式之網站的 URI。不會驗證網站是否存在。針對 [**應用程式識別碼 URI**]，提供識別您應用程式的 URI。不會驗證端點的唯一性或其是否存在。按一下 [**完成**] 建立 AAD 應用程式。

     ![應用程式屬性][4]

## 為應用程式建立驗證金鑰
入口網站現在應該已選取您的應用程式。

1. 按一下 [**設定**] 索引標籤設定您應用程式的密碼。

     ![設定應用程式][3]

2. 向下捲動至 [**金鑰**] 區段，並選取您想要的密碼有效時間。

     ![金鑰][7]

3. 選取 [**儲存**] 建立金鑰。

     ![儲存][13]

     即會顯示儲存的金鑰，而且您可以進行複製。您將無法在稍後擷取金鑰，所以您應該會想現在就複製金鑰。

     ![儲存的金鑰][8]

4. 您現在可以使用金鑰來驗證為服務主體。除了 [**金鑰**] 之外，您還需要 [**用戶端識別碼**] 才能登入。移至 [**用戶端識別碼**] 並複製它。
  
     ![用戶端識別碼][5]

5. 在某些情況下，您需要將您的驗證要求傳遞給租用戶識別碼。您可以擷取租用戶識別碼，方法是選取 [檢視端點]，然後擷取識別碼，如下所示。

     ![租用戶識別碼](./media/resource-group-create-service-principal-portal/save-tenant.png)

您的應用程式現在已就緒，並且已在租用戶上建立服務主體。以服務主體身分登入時，請務必使用：

* **用戶端識別碼** - 做為使用者名稱。
* **金鑰** - 做為密碼。

## 將應用程式指派給角色

您必須將應用程式指派給某個角色，以便授與它執行動作的權限。您可以使用 [Preview 入口網站](https://portal.azure.com)指派 Active Directory 應用程式到具有正確權限的角色。

若要開始使用 Preview 入口網站中的存取控制，請選取**存取**圖示。

![選取使用者](./media/resource-group-create-service-principal-portal/select-users.png)

選取您想要指派給應用程式的角色，並搜尋此應用程式。

![選取使用者](./media/resource-group-create-service-principal-portal/assign-to-role.png)

如需透過入口網站將使用者和應用程式指派給角色的詳細資訊，請參閱[使用 Azure 管理入口網站管理存取權](../role-based-access-control-configure/#manage-access-using-the-azure-management-portal)。

## 以程式碼取得存取權杖

如果您使用 .NET，便可以利用下列程式碼為應用程式擷取存取權杖。

首先，您必須將 Active Directory Authentication Library 安裝至您的 Visual Studio 專案。執行這項工作最簡單的方式便是使用 NuGet 封裝。開啟 [Package Manager 主控台]，並輸入下列命令。

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

在您的應用程式中，新增如下所示的方法來擷取權杖。

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

## 後續步驟

- 若要深入了解指定安全性原則，請參閱[管理和稽核資源存取權](resource-group-rbac.md)。  
- 若要取得這些步驟的示範影片，請參閱[利用 Azure Active Directory 啟用 Azure 資源的程式管理](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory)。
- 若要了解如何使用 Azure PowerShell 或 Azure CLI 搭配 Active Directory 應用程式和服務主體，包括如何使用憑證進行驗證，請參閱[使用 Azure 資源管理員驗證服務主體](./resource-group-authenticate-service-principal.md)。
- 如需實作 Azure 資源管理員安全性的指導，請參閱 [Azure 資源管理員的安全性考量](best-practices-resource-manager-security.md)。


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

<!---HONumber=AcomDC_1203_2015-->