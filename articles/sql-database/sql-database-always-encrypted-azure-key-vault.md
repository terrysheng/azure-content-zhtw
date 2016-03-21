<properties
	pageTitle="透過資料庫加密保護 SQL Database 中的機密資料 | Microsoft Azure"
	description="在數分鐘內開始保護您 SQL 資料庫中的機密資料。"
	keywords="資料加密, 加密金鑰, 雲端加密"	
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="cgronlun"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/02/2016"
	ms.author="sstein"/>

# 透過資料加密並將您的加密金鑰儲存在 Azure 金鑰保存庫，來保護 SQL Database 中的機密資料

> [AZURE.SELECTOR]
- [Azure 金鑰保存庫](sql-database-always-encrypted-azure-key-vault.md)
- [Windows 憑證存放區](sql-database-always-encrypted.md)


本文將告訴您如何使用 [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx) 中的 [Always Encrypted Wizard (永遠加密精靈)](https://msdn.microsoft.com/library/mt459280.aspx) 來進行資料加密，並將每個加密金鑰儲存在 Azure 金鑰保存庫，來保護 SQL Database 中的機密資料。

「永遠加密」是 Azure SQL Database 和 SQL Server 中新的資料加密技術，可保護伺服器上的機密待用資料、在機密資料於用戶端與伺服器之間移動時保護資料，以及在資料使用時保護資料，以確保機密資料永遠不會在資料庫系統中以純文字方式顯示。設定資料加密之後，只有具備金鑰存取權的用戶端應用程式或應用程式伺服器才可以存取純文字資料。如需詳細資訊，請參閱[永遠加密 (資料庫引擎)](https://msdn.microsoft.com/library/mt163865.aspx)。


將資料庫設定為使用 [永遠加密] 之後，我們將搭配 Visual Studio 使用 C# 建立一個用戶端應用程式來使用加密資料。

遵循本文章中的步驟，並了解如何為 Azure SQL Database 設定 [永遠加密]。在本文章中，您將學習到如何執行下列工作：

- 在 SSMS 中使用 Always Encrypted Wizard (永遠加密精靈) 來建立[永遠加密金鑰](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)
    - 建立[資料行主要金鑰 (CMK)](https://msdn.microsoft.com/library/mt146393.aspx)。
    - 建立[資料行加密金鑰 (CEK)](https://msdn.microsoft.com/library/mt146372.aspx)。
- 建立資料庫資料表，並將某些資料行加密。
- 建立可插入、選取及顯示加密資料行資料的應用程式。

> [AZURE.NOTE] Azure SQL Database 的 [永遠加密] 功能目前為預覽狀態。


## 先決條件

本教學課程中，您將需要：

- 在開始之前，您需要有 Azure 帳戶和訂用帳戶。如果您沒有帳戶，請註冊[免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 13.0.700.242 版或更新版本。
- [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) 或更新版本 (於用戶端電腦上)。
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)。
- [Azure PowerShell](../powershell-install-configure.md)，最低版本為 1.0。
    - 請輸入 **(Get-Module azure -ListAvailable).Version** 來查看您正在執行的 PowerShell 版本。



## 啟用用戶端應用程式以存取 SQL Database 服務

您必須先藉由設定必要的驗證，並於下列程式碼中取得驗證應用程式所需的 *ClientId* 和*密碼*，來啟用用戶端應用程式以存取 SQL Database 服務。

1. 開啟[傳統入口網站](http://manage.windowsazure.com)。
2. 選取左功能表中的 [Active Directory]，然後按一下您的應用程式將會使用的 Active Directory。
3. 按一下 [應用程式]，然後按一下 [新增] (位於底部)。
4. 輸入您應用程式的名稱 (例如：*myClientApp*)，選取 [WEB 應用程式]，然後按一下箭頭以繼續。
5. 針對 [登入 URL] 和 [應用程式識別碼 URI]，您只需輸入有效的 URL (例如：**http://myClientApp*) 並繼續。
6. 按一下 [**設定**]。
7. 複製您的 [用戶端識別碼] (您之後在程式碼中將需要此值)。
8. 在 [金鑰] 區段中，將 [選取持續時間] 下拉式清單設定為 [1 年] (我們將會在下方儲存後複製金鑰)。
11. 向下捲動並按一下 [新增應用程式]。
12. 將 [顯示] 保持設定為 [Microsoft 應用程式]，然後尋找並選取 [Windows Azure 服務管理]，並按一下核取記號以繼續。
13. 在 [Windows Azure 服務管理員...] 列上，按一下 [委派的權限] 下拉式清單，然後選取 [存取 Azure 服務管理]。
14. 按一下 [儲存] (位於底部)。
15. 儲存完成後，請尋找並複製 [金鑰] 區段中的金鑰值 (您之後在程式碼中將需要此值)。 



## 建立 Azure 金鑰保存庫來儲存您的金鑰

現在您的用戶端應用程式已完成設定，您也取得您的用戶端識別碼，是時候建立 Azure 金鑰保存庫並設定它的存取原則，以讓您和您的應用程式可以存取保存庫的密碼 ([永遠加密] 金鑰)。如果要搭配 Azure 金鑰保存庫使用金鑰，便需要 *create*、*get*、*list*、*sign*、*verify*、*wrapKey*，以及 *unwrapKey* 權限以建立新的資料行主要金鑰，以及利用 SQL Server Management Studio 設定加密。

若要快速建立 Azure 金鑰保存庫，您可以執行下列指令碼。如需這些 Cmdlet 的詳細說明，以及建立及設定 Azure 金鑰保存庫的詳細資訊，請參閱[開始使用 Azure 金鑰保存庫](../Key-Vault/key-vault-get-started.md)



    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $clientId = '<client ID that you copied in step 7 above>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'
    

    Login-AzureRmAccount
    $subscriptionId = (Get-AzureRmSubscription -SubscriptionName $subscriptionName).SubscriptionId
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup –Name $resourceGroupName –Location $location
    New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzureRmKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $clientId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list




## 建立空白 SQL Database
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下 [新增] > [資料 + 儲存體] > [SQL Database]。
3. 在新的或現有伺服器上建立名稱為**實務課程**的**空白**資料庫。如需在 Azure 入口網站中建立資料庫的詳細指示，請參閱[快速建立 SQL Database](sql-database-get-started.md)。

	![建立空白資料庫](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

在本教學課程稍後您將需要連接字串，在資料庫建立之後瀏覽至新的實務課程資料庫，並複製連接字串 (您隨時都可以取得連接字串，但是我們已經在入口網站中，因此現在可以輕鬆地將它複製)。

1. 按一下 [SQL Database] > [實務課程] > [顯示資料庫連接字串]。
2. 複製 **ADO.NET** 的連接字串：

	![複製連接字串](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)


## 使用 SSMS 連接到資料庫

開啟 SSMS 並連接到包含實務課程資料庫的伺服器。


1. 開啟 SSMS (如果沒有開啟，請按一下 [連線] > [資料庫引擎...] 以開啟 [連接到伺服器] 視窗)。
2. 輸入您的伺服器名稱和認證。可以在 SQL 資料庫刀鋒視窗上找到此伺服器名稱和稍早複製的連接字串。輸入完整的伺服器名稱，包括 *database.windows.net*。

	![複製連接字串](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

3. 如果 [新防火牆規則] 視窗開啟，請登入 Azure，讓 SSMS 為您建立新的防火牆規則。


## 建立資料表

首先，我們將建立資料表來保存病患的資料 (剛開始不會加密 – 下一節中，我們將會設定加密)。

1. 展開 [資料庫]。
1. 以滑鼠右鍵按一下 [實務課程] 資料庫，然後按一下 [新增查詢]。
2. 將下列 TRANSACT-SQL (T-SQL) 貼到新的查詢視窗中並 [執行] 它：


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1), 
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL, 
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## 加密某些資料行 (設定永遠加密)

SSMS 提供一個精靈，透過設定加密資料行主要金鑰 (CMK)、資料行加密金鑰 (CEK) 與加密資料行，來為您輕鬆設定永遠加密。

1. 展開 [資料庫] > [實務課程] > [資料表]。
2. 以滑鼠右鍵按一下 [病患] 資料表，然後選取 [加密資料行...] 以開啟 [永遠加密] 精靈：

    ![加密資料行](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

3. **資料行選取**

    按一下 [簡介] 頁面上的 [下一步] 開啟 [資料行選取] 頁面，其中您可以選取想要加密的資料行，要使用的[加密類型，以及資料行加密金鑰 (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2)。

    針對每位病患，我們想要加密其 **SSN** 與 **BirthDate** 資訊。SSN 資料行將使用決定性加密，可支援等式查閱、聯結與群組依據。BirthDate 資料行將使用不支援操作的隨機加密。

    選取並將 SSN 資料行的 [加密類型] 設定為 [決定性]，並將 BirthDate 資料行設定為 [隨機]，然後按一下 [下一步]。

    ![加密資料行](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

4. **主要金鑰設定** (CMK)

    [主要金鑰設定] 頁面是您設定資料行主要金鑰 (CMK) 並選取儲存 CMK 之金鑰存放區提供者的位置。目前，您可以在 Windows 憑證存放區、Azure 金鑰保存庫或硬體安全性模組 (HSM) 中儲存 CMK。

    本教學課程說明如何在 Azure 金鑰保存庫中儲存您的金鑰。

    1.     選取 **Azure 金鑰保存庫**。
    1.     從下拉式清單中選取所需的金鑰保存庫。
    1.     按 [下一步]。

    ![主要金鑰設定](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)


5. **驗證**

    您現在可以加密資料行，或儲存為 PowerShell 指令碼以供日後執行。針對這個教學課程，請選取 [繼續以立即完成]，然後按一下 [下一步]。

6. **摘要**

    確認設定都正確，然後按一下 [完成] 以完成 [永遠加密] 的設定。


    ![summary](./media/sql-database-always-encrypted-azure-key-vault/summary.png)


### 精靈實際上做了什麼？

精靈完成作業後，您的資料庫會設定為 [永遠加密]，並會完成下列作業：

- 已建立資料行主要金鑰 (CMK) 並將它儲存在您的 Azure 金鑰保存庫中。
- 已建立資料行加密金鑰 (CEK) 並將它儲存在您的 Azure 金鑰保存庫中。
- 設定選取的資料行以進行加密。(我們的 [病患] 資料表目前尚未有任何資料，但現在選取的資料行中的現有資料會加密)。

您可以展開 [實務課程] > [安全性] > [永遠加密金鑰]，確認 SSMS 中是否已建立金鑰。您現在可以看到精靈為您產生的新金鑰。


## 建立搭配加密資料使用的用戶端應用程式

既然已經設定 [永遠加密]，我們可以開始建置在加密資料行上執行一些 INSERTS 和 SELECTS 的應用程式。

> [AZURE.IMPORTANT] 傳送純文字資料至具有 [永遠加密] 資料行的伺服器時，您的應用程式必須使用 [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) 物件。在不使用 SqlParameter 物件的情況下傳遞常值會導致例外狀況。


1. 開啟 Visual Studio，建立新的 C# 主控台應用程式。請確定您的專案設定為 **.NET Framework 4.6** 或更新版本。
2. 將專案命名為 **AlwaysEncryptedConsoleAKVApp**，並按一下 [確定]。


	![新的主控台應用程式](./media/sql-database-always-encrypted-azure-key-vault/console-app.png)


3. 按一下 [工具] > [NuGet 封裝管理員] > [封裝管理員主控台]，以安裝下列 NuGet 封裝。

在 [封裝管理員主控台] 中執行下列 2 行程式碼：
    
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory


   
## 修改連接字串，以啟用 [永遠加密]

本節只說明如何在您的資料庫連接字串中啟用 [永遠加密]。在下一節的**永遠加密範例主控台應用程式** 中，您將會實際修改您剛剛建立的主控台應用程式。


若要啟用 [永遠加密]，您必須將 [資料行加密設定] 關鍵字新增至您的連接字串，並將它設定為 [啟用]。

您可以直接在連接字串中設定，或您可以使用 [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx) 設定它。下一節中的範例應用程式會示範如何使用 **SqlConnectionStringBuilder**。



### 在連接字串中啟用 [永遠加密]

在連接字串中加入下列關鍵字：

    Column Encryption Setting=Enabled


### 使用 SqlConnectionStringBuilder 啟用永遠加密

下列程式碼示範如何藉由將 [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) 設定為[啟用](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx)來啟用 [永遠加密]。

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder = 
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting = 
       SqlConnectionColumnEncryptionSetting.Enabled;

## 註冊 Azure 金鑰保存庫提供者

下列程式碼示範如何使用 ADO.NET 驅動程式註冊 Azure 金鑰保存庫提供者︰

    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(clientId, clientSecret);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }



## 永遠加密範例主控台應用程式

這個範例會示範如何：

- 修改連接字串，以啟用 [永遠加密]。
- 將 Azure 金鑰保存庫註冊為應用程式的金鑰存放區提供者。  
- 將資料插入加密資料行。
- 在加密資料行中篩選特定值來選取記錄。

使用下列程式碼取代 **Program.cs** 的內容。從 Azure 入口網站使用您的有效連接字串，直接在 Main 方法上的列中取代全域 connectionString 變數的連接字串。這是此程式碼唯一需要進行的變更。

現在執行應用程式，以查看作用中的 [永遠加密]。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;
    
    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure Portal.
        static string connectionString = @"<connection string from the portal>";
        static string clientId = @"<client id from step 7 above>";
        static string clientSecret = "<key from step 13 above>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted 
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(clientId, clientSecret);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }



## 確認資料已加密

我們可以輕鬆地使用 SSMS (使用我們目前尚未啟用資料行加密設定的連線) 查詢病患資料，快速檢查伺服器上的實際資料是否已加密。

在實務課程資料庫上執行下列查詢：

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

您可以看到加密的資料行不包含任何純文字資料。

   ![新的主控台應用程式](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)


若要使用 SSMS 來存取純文字資料，我們可以將 Column Encryption Setting=enabled 參數新增至連線。

1. 在 SSMS 中，於 [物件總管] 中以滑鼠右鍵按一下您的伺服器並 [中斷連線]。
2. 按一下 [連線] > [資料庫引擎] 開啟 [連線到伺服器] 視窗，並按一下 [選項]。
3. 按一下 [其他連接參數] 並輸入 Column Encryption Setting=enabled。

	![新的主控台應用程式](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)

4. 在實務課程資料庫上執行下列查詢：

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     您現在可以在加密的資料行中看到純文字資料。


	![新的主控台應用程式](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## 後續步驟
建立使用 [永遠加密] 的資料庫之後，您可以執行下列操作：

- [旋轉和清除金鑰](https://msdn.microsoft.com/library/mt607048.aspx)。
- [Migrate data that is already encrypted with Always Encrypted (移轉已使用 [永遠加密] 加密的資料)](https://msdn.microsoft.com/library/mt621539.aspx)



## 相關資訊

- [Always Encrypted (client Development) (永遠加密 (用戶端開發))](https://msdn.microsoft.com/library/mt147923.aspx)
- [透明資料加密](https://msdn.microsoft.com/library/bb934049.aspx)
- [SQL Server 加密](https://msdn.microsoft.com/library/bb510663.aspx)
- [Always Encrypted Wizard (永遠加密精靈)](https://msdn.microsoft.com/library/mt459280.aspx)
- [Always Encrypted Blog (永遠加密部落格)](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

<!---HONumber=AcomDC_0309_2016-->