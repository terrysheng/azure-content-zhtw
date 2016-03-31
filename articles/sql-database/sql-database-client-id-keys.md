<properties
   pageTitle="註冊應用程式並取得從程式碼連接到 SQL Database 所使用的用戶端識別碼和金鑰 | Microsoft Azure"
   description="取得從程式碼存取 SQL Database 所使用的用戶端識別碼和金鑰。"
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="03/15/2016"
   ms.author="sstein"/>

# 取得從程式碼連接到 SQL Database 所使用的用戶端識別碼和金鑰

若要從程式碼建立和管理 SQL Database，您必須在與建立 Azure 資源所使用的訂用帳戶相關聯的 Azure Active Directory (AAD) 網域中註冊您的應用程式。當您註冊應用程式時，Azure 將會產生一個，您的程式碼中需要這個用戶端識別碼和金鑰，才能驗證您的應用程式。如需詳細資訊，請參閱 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)。

## 註冊原生用戶端應用程式，並取得用戶端識別碼

若要建立新的應用程式並且加以註冊，請執行下列動作：

1. 登入[傳統入口網站](https://manage.windowsazure.com/) (目前，註冊應用程式必須在傳統入口網站中進行)。
1. 在功能表中找出 **Active Directory** 並加以選取。

    ![AAD][1]

2. 選取要驗證您的應用程式的目錄並按一下該目錄的**名稱**。

    ![目錄][4]

3. 在目錄頁面上，按一下 [應用程式]。

    ![[應用程式]][5]

4. 按一下 [新增] 以新增新的應用程式。

    ![新增應用程式][6]

5. 提供應用程式的 [名稱]，然後選取 [原生用戶端應用程式]。

    ![新增應用程式][7]

6. 提供**重新導向 URI**。它不需要是實際的端點，只要是有效的 URI 即可。

    ![新增應用程式][8]

7. 建立應用程式完畢之後，按一下 [設定]，然後複製 [用戶端識別碼] \(這是您在程式碼中需要的值)。

    ![取得用戶端識別碼][9]


1. 向下捲動頁面，並按一下 [新增應用程式]。
1. 選取 [Microsoft 應用程式]。
1. 選取 [Windows Azure 服務管理 API]，然後完成精靈。
2. 在**其他應用程式的權限**區段中找出 [Windows Azure 服務管理 API]，然後按一下 [委派的權限]。
3. 選取 [存取 Azure 服務管理...]。

    ![權限][2]

2. 按一下頁面底部的 [儲存]。



## 註冊 Web 應用程式 (或 Web API)，並取得用戶端識別碼和金鑰

若要建立新的應用程式並且在正確的 Active Directory 中註冊，請執行下列動作：

1. 登入[傳統入口網站](https://manage.windowsazure.com/)。
1. 在功能表中找出 **Active Directory** 並加以選取。

    ![AAD][1]

2. 選取要驗證您的應用程式的目錄並按一下該目錄的**名稱**。

    ![目錄][4]

3. 在目錄頁面上，按一下 [應用程式]。

    ![[應用程式]][5]

4. 按一下 [新增] 以新增新的應用程式。

    ![新增應用程式][6]

5. 提供應用程式的 [名稱]，然後選取 [Web 應用程式和/或 Web API]。

    ![新增應用程式][10]

6. 提供 [登入 URL] 和 [應用程式識別碼 URI]。它不需要是實際的端點，只要是有效的 URI 即可。

    ![新增應用程式][11]

7. 建立應用程式完畢後，按一下 [設定]。

    ![設定][12]

8. 捲動到**金鑰**區段，然後在 [選取持續時間] 清單中選取 [1 年]。金鑰值將會在您儲存後顯示，因此我們稍後會再回來複製金鑰。

    ![設定金鑰持續時間][13]



1. 向下捲動頁面，並按一下 [新增應用程式]。
1. 選取 [Microsoft 應用程式]。
1. 找出並選取 [Windows Azure 服務管理 API]，然後完成精靈。
2. 在**其他應用程式的權限**區段中找出 [Windows Azure 服務管理 API]，然後按一下 [委派的權限]。
3. 選取 [存取 Azure 服務管理...]。

    ![權限][2]

2. 按一下頁面底部的 [儲存]。
3. 儲存完成後，找出並儲存用戶端識別碼和金鑰︰

    ![Web 應用程式密碼][14]



## 取得您的網域名稱

您的驗證程式碼有時候需要網域名稱。可以輕易地識別正確的網域名稱的方式是：

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 將滑鼠停留在右上角的名稱，並記下出現在快顯視窗的網域。

    ![識別網域名稱][3]




## 範例主控台應用程式


使用 Visual Studio 的[封裝管理員主控台](http://docs.nuget.org/Consume/Package-Manager-Console) ([工具] > [NuGet 封裝管理員] > [封裝管理員主控台]) 安裝下列封裝，以取得必要的管理程式庫：


    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


建立名稱為 **SqlDbAuthSample** 的主控台應用程式，並使用下列程式碼取代 **Program.cs** 的內容：

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbAuthSample
    {
    class Program
    {
        static void Main(string[] args)
        {
            token = GetAccessTokenforNativeClient();
            // token = GetAccessTokenUsingUserCredentials(new UserCredential("<email address>"));
            // token = GetAccessTokenForWebApp();

            Console.WriteLine("Signed in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Press Enter to continue...");
            Console.ReadLine();
        }


        // authentication variables (native)
        static string nclientId = "<your client id>";
        static string nredirectUri = "<your redirect URI>";
        static string ndomainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        private static AuthenticationResult GetAccessTokenforNativeClient()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + ndomainName /* Tenant ID or AAD domain */);

            token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    nclientId,
            new Uri(nredirectUri),
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }


        // authentication variables (web)
        static string wclientId = "<your client id>";
        static string wkey = "<your key>";
        static string wdomainName = "<i.e. microsoft.onmicrosoft.com>";

        private static AuthenticationResult GetAccessTokenForWebApp()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + wdomainName /* Tenant ID or AAD domain */);

            ClientCredential cc = new ClientCredential(wclientId, wkey);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                cc);

            return token;
        }


        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + ndomainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                nclientId /* application client ID from AAD*/,
                new Uri(nredirectUri) /* redirect URI */,
                PromptBehavior.Auto,
                new UserIdentifier(userCredential.UserName, UserIdentifierType.RequiredDisplayableId));

            return token;
        }
    }
    }


如需與 Azure AD 驗證相關的特定程式碼範例，請參閱 MSDN 上的 [SQL Server 安全性部落格](http://blogs.msdn.com/b/sqlsecurity/)。

## 另請參閱

- [以 C# 建立 SQL Database](sql-database-get-started-csharp.md)
- [使用 Azure Active Directory 驗證連線到 SQL Database](sql-database-aad-authentication.md)



<!--Image references-->
[1]: ./media/sql-database-client-id-keys/aad.png
[2]: ./media/sql-database-client-id-keys/permissions.png
[3]: ./media/sql-database-client-id-keys/getdomain.png
[4]: ./media/sql-database-client-id-keys/aad2.png
[5]: ./media/sql-database-client-id-keys/aad-applications.png
[6]: ./media/sql-database-client-id-keys/add.png
[7]: ./media/sql-database-client-id-keys/add-application.png
[8]: ./media/sql-database-client-id-keys/add-application2.png
[9]: ./media/sql-database-client-id-keys/clientid.png
[10]: ./media/sql-database-client-id-keys/add-application-web.png
[11]: ./media/sql-database-client-id-keys/add-application-app-properties.png
[12]: ./media/sql-database-client-id-keys/configure.png
[13]: ./media/sql-database-client-id-keys/key-duration.png
[14]: ./media/sql-database-client-id-keys/web-secrets.png

<!---HONumber=AcomDC_0316_2016-->
