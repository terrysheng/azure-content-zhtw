<properties 
	pageTitle="試用 SQL Database：使用 C# 建立 SQL Database | Microsoft Azure" 
	description="嘗試用 SQL Database 開發 SQL 和 C# 應用程式，然後使用 SQL Database Library for .NET 以 C# 建立 Azure SQL Database。" 
	keywords="試用 sql, sql c#"   
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="12/01/2015"
   ms.author="sstein"/>

# 試用 SQL Database：透過 SQL Database Library for .NET 使用 C&#x23; 建立 SQL Database 

**單一資料庫**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)



了解如何使用 C# 命令透過 [Azure SQL Database Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql) 建立 Azure SQL Database。

您可使用 SQL 和 C# 建立單一資料庫，試用 SQL Database。若要建立彈性資料庫，請參閱[建立彈性資料庫集區](sql-database-elastic-pool-portal.md)。

為了清楚起見，將個別程式碼片段分別列出，範例主控台應用程式會將所有命令整合在本文底端的區段中。

Azure SQL Database Library for .NET 提供 [Azure 資源管理員](resource-group-overview.md)式 API，它會包裝[資源管理員式 SQL Database REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx)。此用戶端程式庫遵循資源管理員式用戶端程式庫的常見模式。資源管理員需要資源群組，並且使用 [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD) 進行驗證。

<br>

> [AZURE.NOTE]SQL Database Library for .NET 目前為預覽狀態。

<br>

若要完成這篇文章中的步驟，您需要下列項目︰

- Azure 訂用帳戶。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- 。如需免費的 Visual Studio，請參閱 [Visual Studio 下載](https://www.visualstudio.com/downloads/download-visual-studio-vs)頁面。


## 安裝必要的程式庫

若要以 C# 設定 SQL Database，使用[封裝管理員主控台](http://docs.nuget.org/Consume/Package-Manager-Console)安裝下列封裝，即可取得必要的管理程式庫：

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


## 使用 Azure Active Directory 設定驗證

您必須先啟用您的用戶端應用程式以存取 REST API，方法是設定必要的驗證。

[Azure 資源管理員 REST API](https://msdn.microsoft.com/library/azure/dn948464.aspx) 使用 Azure Active Directory 進行驗證。

若要根據目前的使用者驗證您的用戶端應用程式，您必須先在與訂用帳戶 (已在其下建立 Azure 資源) 相關聯的 AAD 網域中註冊您的應用程式。如果您的 Azure 訂用帳戶是以 Microsoft 帳戶而不是工作或學校帳戶建立的，您便已經有預設 AAD 網域。您可以在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中完成應用程式註冊。

若要建立新的應用程式並且在正確的 Active Directory 中註冊，請執行下列動作：

1. 捲動左側的功能表以找出 **Active Directory** 服務並且開啟。

    ![試用 SQL Database：設定 Azure Active Directory (AAD)。][1]

2. 選取要驗證您的應用程式的目錄並按一下該目錄的 [名稱]。

    ![選取要驗證您的 SQL C# 的目錄。][4]

3. 在目錄頁面上，按一下 [應用程式]。

    ![包含應用程式的目錄頁面。][5]

4. 按一下 [新增] 為 SQL Database 建立新的 C# 應用程式。

    ![新增 SQL C# 應用程式。][6]

5. 選取 [新增組織正在開發的應用程式]。

5. 提供應用程式的 [名稱]，然後選取 [原生用戶端應用程式]。

    ![提供 SQL C# 應用程式的相關資訊。][7]

6. 提供 [重新導向 URI]。它不需要是實際的端點，只要是有效的 URI 即可。

    ![新增 SQL C# 應用程式的重新導向 URL。][8]

7. 完成應用程式建立，按一下 [設定]，然後複製 [用戶端識別碼] (您在程式碼中需要用戶端識別碼)。

    ![取得 SQL C# 應用程式的用戶端識別碼。][9]


1. 在頁面底部按一下 [新增應用程式]。
1. 選取 [Microsoft 應用程式]。
1. 選取 [Azure 服務管理 API]，然後完成精靈。
2. 選取 API 之後，您現在必須授與必要的存取權以存取此 API，方法是選取 [存取 Azure 服務管理 (預覽)]。

    ![設定權限。][2]

2. 按一下 [儲存]。



### 識別網域名稱

您的程式碼需要網域名稱。可以輕易地識別正確的網域名稱的方式是：

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 將滑鼠停留在右上角的名稱，並記下出現在快顯視窗的網域。

    ![識別網域名稱。][3]





**其他 AAD 資源**

使用 Azure Active Directory 進行驗證的其他資訊可以在[此有用的部落格文章](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/)中找到。


### 擷取目前使用者的存取權杖 

用戶端應用程式必須擷取目前使用者的應用程式存取權杖。使用者第一次執行此程式碼時，系統會提示使用者輸入其使用者認證，產生的權杖會在本機快取。後續執行會從快取擷取權杖，並且在權杖過期時僅提示使用者登入。

    /// <summary>
    /// Prompts for user credentials when first run or if the cached credentials have expired.
    /// </summary>
    /// <returns>The access token from AAD.</returns>
    private static AuthenticationResult GetAccessToken()
    {
        AuthenticationContext authContext = new AuthenticationContext
            ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

        AuthenticationResult token = authContext.AcquireToken
            ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                clientId,
        new Uri(clientAppUri) /* redirect URI */,
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }



> [AZURE.NOTE]這篇文章中的範例使用每個 API 要求的同步表單，並且封鎖直到基礎服務上的 REST 呼叫完成。有可用的非同步方法。



## 建立資源群組

使用資源管理員時，必須在資源群組中建立所有資源。資源群組是保留應用程式相關資源的容器。使用 Azure SQL Database，必須在現有資源群組中建立資料庫伺服器，然後在伺服器上建立資料庫。然後在應用程式可以連接到伺服器或資料庫之前，您也必須在伺服器上建立防火牆規則以開啟用戶端 IP 位址的存取權。


    // Create a resource management client 
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));
    
    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };
    
    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("ResourceGroupName", resourceGroupParameters);



## 建立伺服器 

SQL Database 包含在伺服器中。伺服器名稱在全域的所有 Azure SQL Server 中必須是唯一的，因此如果伺服器名稱已被採用，您會收到錯誤。另外值得注意的是，此命令可能需要數分鐘才能完成。


    // Create a SQL Database management client
    SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken));

    // Create a server
    ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ServerCreateOrUpdateProperties()
        {
            AdministratorLogin = "ServerAdmin",
            AdministratorLoginPassword = securelyStoredPassword,
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("ResourceGroupName", "ServerName", serverParameters);




## 設定伺服器防火牆規則以允許存取伺服器

根據預設，無法從任何位置連接到伺服器。為了連接到伺服器或伺服器上的任何資料庫，必須定義[防火牆規則](https://msdn.microsoft.com/library/azure/ee621782.aspx)以允許從用戶端 IP 位址存取。

下列範例會建立規則，從任何 IP 位址開啟伺服器的存取權。建議您建立適當的 SQL 登入和密碼以保護您的資料庫，不要依賴防火牆規則做為防禦入侵的主要防衛措施。


    // Create a firewall rule on the server to allow TDS connection 
    FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
    {
        Properties = new FirewallRuleCreateOrUpdateProperties()
        {
            StartIpAddress = "0.0.0.0",
            EndIpAddress = "255.255.255.255"
        }
    };

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("ResourceGroupName", "ServerName", "FirewallRuleName", firewallParameters);




若要允許其他 Azure 服務存取伺服器，則新增防火牆規則並且將 tartIpAddress 和 EndIpAddress 都設為 0.0.0.0。請注意，這可讓來自任何 Azure 訂用帳戶的 Azure 流量存取伺服器。


## 使用 C&#x23; 建立基本 SQL Database

如果伺服器上沒有相同名稱的資料庫存在，則下列 C# 命令會建立新的基本 SQL Database；如果具有相同名稱的資料庫已存在，則會更新。

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("ResourceGroupName", "ServerName").Server;
 
        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Standard",
                RequestedServiceObjectiveName = "S0",
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("ResourceGroupNname", "ServerName", "Database1", newDatabaseParameters);






## 範例 C&#x23; 主控台應用程式


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    
    namespace CreateSqlDatabase
    {
     class Program
     {

        #region user variables
        
        // Azure subscription id
        private static string subscriptionId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";
     
        
        // application client ID from Azure Active Directory (AAD)
        private static string clientAppUri = "http://app1";
        private static string clientId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";

        private static string resourcegroupName = "rg1";

        private static string dataCenterLocation = "Japan West";

        private static string databaseName = "newDatabaseName";
        private static string databaseEdition = "Standard";
        private static string databasePerformanceLevel = "S0";

        private static string serverName = "serverName";
        private static string serverAdmin = "admin";
        private static string serverAdminPassword = securelyStoredPassword;
        private static string serverVersion = "12.0";

        private static string firewallRuleName = "rule1";
        private static string firewallRuleStartIp = "0.0.0.0";
        private static string firewallRuleEndIp = "255.255.255.255";



        private static string domainName = "microsoft.onmicrosoft.com";

        private static string serverLocation = "Japan West";

        #endregion


        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity: {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = dataCenterLocation
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourcegroupName, resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourcegroupName, serverName, serverParameters);

            var serverGetResult = sqlClient.Servers.Get(resourcegroupName, serverName);


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection 

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = firewallRuleStartIp,
                    EndIpAddress = firewallRuleEndIp
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourcegroupName, serverName, firewallRuleName, firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code: {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);

            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourcegroupName, serverName).Server;

            // Create a database
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerformanceLevel,
                }
            };

            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourcegroupName, serverName, databaseName, newDatabaseParameters);

            Console.WriteLine("Database {0} created with status code: {1}. Service Objective: {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);
           
            Console.WriteLine(System.Environment.NewLine + "Press any key to exit.");
            Console.ReadKey();
        }

        /// <summary>
        /// Prompts for user credentials when first run or if the cached credentials have expired.
        /// </summary>
        /// <returns>The access token from AAD.</returns>
        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(clientAppUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
      }
    }


## 後續步驟
既然您已試用 SQL Database 並以 C# 設定資料庫，您就可以進行下列文章：

- [使用 C# 連接及查詢您的 SQL Database](sql-database-connect-query.md)
- [連接 SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)

## 其他資源

- [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)





<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png

<!---HONumber=AcomDC_1203_2015-->