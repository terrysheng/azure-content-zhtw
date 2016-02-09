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
   ms.date="01/22/2016"
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

> [AZURE.NOTE] SQL Database Library for .NET 目前為預覽狀態。

<br>

若要完成這篇文章中的步驟，您需要下列項目︰

- Azure 訂用帳戶。如果需要 Azure 訂用帳戶，可以先按一下此頁面頂端的 [免費試用]，然後再回來完成這篇文章。
- 。如需免費的 Visual Studio，請參閱 [Visual Studio 下載](https://www.visualstudio.com/downloads/download-visual-studio-vs)頁面。


## 安裝必要的程式庫

若要以 C# 設定 SQL Database，請使用 Visual Studio 的[封裝管理員主控台](http://docs.nuget.org/Consume/Package-Manager-Console) ([工具] > [NuGet 封裝管理員] > [封裝管理員主控台]) 安裝下列封裝，即可取得必要的管理程式庫：

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## 使用 Azure Active Directory 設定驗證

您必須先啟用您的用戶端應用程式才可存取 SQL Database 服務，方法是設定必要的驗證。

若要根據目前的使用者驗證您的用戶端應用程式，您必須先在與訂用帳戶 (已在其下建立 Azure 資源) 相關聯的 AAD 網域中註冊您的應用程式。如果您的 Azure 訂用帳戶是以 Microsoft 帳戶而不是工作或學校帳戶建立的，您便已經有預設 AAD 網域。

若要建立新的應用程式並且在正確的 Active Directory 中註冊，請執行下列動作：

1. 前往 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
1. 選取右側的 [Active Directory]，然後選取要驗證您的應用程式的目錄並按一下該目錄的 [名稱]。

    ![試用 SQL Database：設定 Azure Active Directory (AAD)。][1]

2. 在目錄頁面上，按一下 [應用程式]。

    ![包含應用程式的目錄頁面。][5]

4. 按一下 [新增] 以新增新的應用程式。

5. 選取 [新增組織正在開發的應用程式]。

5. 提供應用程式的 [名稱]，然後選取 [原生用戶端應用程式]。

    ![提供 SQL C# 應用程式的相關資訊。][7]

6. 提供**重新導向 URI**。它不需要是實際的端點，只要是有效的 URI 即可。

    ![新增 SQL C# 應用程式的重新導向 URL。][8]

7. 完成建立應用程式，按一下 [設定]，然後複製 [用戶端識別碼] (稍後在程式碼中需要用戶端識別碼)。

    ![取得 SQL C# 應用程式的用戶端識別碼。][9]


1. 在頁面底部按一下 [新增應用程式]。
1. 選取 [Microsoft 應用程式]。
1. 選取 [Azure 服務管理 API]，然後完成精靈。
2. 選取 API 之後，您現在必須授與必要的存取權以存取此 API，方法是選取 [**存取 Azure 服務管理 (預覽)**]。

    ![設定權限。][2]

2. 按一下 [儲存]。



### 識別網域名稱

您的程式碼需要網域名稱。可以輕易地識別正確的網域名稱的方式是：

1. 移至 [Azure 入口網站](http://portal.azure.com)。
2. 將滑鼠停留在右上角的名稱，並記下出現在快顯視窗的網域。

    ![識別網域名稱。][3]





**其他 AAD 資源**

使用 Azure Active Directory 進行驗證的其他資訊可以在[此有用的部落格文章](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/)中找到。


### 擷取目前使用者的存取權杖 

用戶端應用程式必須擷取目前使用者的應用程式存取權杖。使用者第一次執行此程式碼時，系統會提示使用者輸入其使用者認證，產生的權杖會在本機快取。後續執行會從快取擷取權杖，並且在權杖過期時僅提示使用者登入。

此程式碼會傳回您在下面其他程式碼片段中需要的 Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationResult。

        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }



> [AZURE.NOTE] 這篇文章中的範例使用每個 API 要求的同步表單，並且封鎖直到基礎服務上的 REST 呼叫完成。有可用的非同步方法。



## 建立資源群組

使用資源管理員時，必須在資源群組中建立所有資源。資源群組是保留應用程式相關資源的容器。使用 Azure SQL Database，必須在現有的資源群組中建立資料庫伺服器。

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }


## 建立伺服器 

SQL Database 包含在伺服器中。伺服器名稱在全域的所有 Azure SQL Server 中必須是唯一的，因此如果伺服器名稱已被採用，您會收到錯誤。另外值得注意的是，此命令可能需要數分鐘才能完成。

    static void CreateServer()
    {
        // Create a SQL Database management client
        SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

        // Create a server
        ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
        {
            Location = location,
            Properties = new ServerCreateOrUpdateProperties()
            {
                AdministratorLogin = administratorLogin,
                AdministratorLoginPassword = administratorPassword,
                Version = serverVersion
            }
        };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
    }


### 建立外部伺服器系統管理員


    // Create a server external admin
    ServerAdministratorCreateOrUpdateParameters aadAdminParameters = new ServerAdministratorCreateOrUpdateParameters()
    {
        Properties = new ServerAdministratorCreateOrUpdateProperties()
        {
            AdministratorType = "ActiveDirectory",
            Login = "<login>",
            Sid = new Guid("<Azure AD admin sid>"),
            TenantId = new Guid("<Azure AD tenant id>")
        }
    };

    var adminResult = sqlClient.ServerAdministrators.CreateOrUpdate(resourceGroupName, serverName, "ActiveDirectory", aadAdminParameters);
    var adminGetResult = sqlClient.ServerAdministrators.Get(resourceGroupName, serverName, "ActiveDirectory");




## 設定伺服器防火牆規則以允許存取伺服器

根據預設，無法從任何位置連接到伺服器。為了連接到伺服器或伺服器上的任何資料庫，必須定義[防火牆規則](https://msdn.microsoft.com/library/azure/ee621782.aspx)以允許從用戶端 IP 位址存取。

下列範例會建立規則，從任何 IP 位址開啟伺服器的存取權。建議您建立適當的 SQL 登入和密碼以保護您的資料庫，不要依賴防火牆規則做為防禦入侵的主要防衛措施。


        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server 
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }




若要允許其他 Azure 服務存取伺服器，則新增防火牆規則並且將 tartIpAddress 和 EndIpAddress 都設為 0.0.0.0。請注意，這可讓來自*任何* Azure 訂用帳戶的 Azure 流量存取伺服器。


## 使用 C&#x23; 建立 SQL Database

如果伺服器上沒有相同名稱的資料庫存在，則下列 C# 命令會建立新的 SQL Database；如果具有相同名稱的資料庫已存在，則會更新。


        static void CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
        }



## 範例 C&#x23; 主控台應用程式

下列範例會建立資源群組、伺服器、防火牆規則和 SQL Database。本文頂端的「使用 Azure Active Directory 設定驗證」一節會顯示何處可取得 clientId、redirectUri 和 domainName 變數的值。


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    
    namespace SqlDbConsoleApp
    {
    class Program
    {
        // Get these values from the Azure portal
        static string subscriptionId = "<Azure subscription id>";
        static string clientId = "<Azure App clientId>";
        static string redirectUri = "<Azure App redirectURI>";
        static string domainName = "<domain>";

        // You create these values 
        static string resourceGroupName = "<your resource group name>";
        static string location = "<Azure data center location>";

        static string serverName = "<your server name>";
        static string administratorLogin = "<your server admin>";
        
        // store your password securely!
        static string administratorPassword = "<your server admin password>";
        static string serverVersion = "12.0";

        static string firewallRuleName = "<your firewall rule name>";

        static string databaseName = "dbfromcsharparticle";
        static string databaseEdition = "Basic"; // Basic, Standard, or Premium
        static string databasePerfLevel = "";

        static AuthenticationResult token;
        static Microsoft.Rest.TokenCredentials creds;

        static SqlManagementClient sqlClient;


        static void Main(string[] args)
        {
            Console.WriteLine("Logging in...");

            token = GetAccessToken();

            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Creating resource group...");
            CreateResourceGroup();

            sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            Console.WriteLine("Creating server...");
            CreateServer();

            Console.WriteLine("Creating firewall rule...");
            CreateFirewallRule();

            Console.WriteLine("Creating database...");

            DatabaseCreateOrUpdateResponse dbResponse = CreateDatabase();
            Console.WriteLine("Status: " + dbResponse.Status.ToString() 
                + " Code: " + dbResponse.StatusCode.ToString());

            Console.WriteLine("Press enter to exit...");
            Console.ReadLine();
        }

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }

        static void CreateServer()
        {
            // Create a SQL Database management client
            //SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = location,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = administratorLogin,
                    AdministratorLoginPassword = administratorPassword,
                    Version = serverVersion
                }
            };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
        }

        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server 
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    // replace with your client IP address
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }

        static DatabaseCreateOrUpdateResponse CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }

        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
    }
    }




## 後續步驟
既然您已試用 SQL Database 並以 C# 設定資料庫，您就可以進行下列文章：

- [使用 SQL Server Management Studio 連接到 SQL Database 並執行範例 T-SQL 查詢](sql-database-connect-query-ssms.md)

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

<!---HONumber=AcomDC_0204_2016-->