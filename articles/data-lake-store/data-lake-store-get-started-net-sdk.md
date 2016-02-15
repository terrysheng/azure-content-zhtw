<properties 
   pageTitle="使用資料湖存放區 .NET SDK 來開發應用程式 | Azure" 
   description="使用 Azure 資料湖存放區 .NET SDK 來開發應用程式" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/04/2016"
   ms.author="nitinme"/>

# 使用 .NET SDK 開始使用 Azure 資料湖存放區

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)

了解如何使用 Azure 資料湖存放區 .NET SDK 以建立 Azure 資料湖帳戶並執行基本作業，例如建立資料夾、上傳和下載資料檔案、刪除您的帳戶等等。如需有關資料湖的詳細資訊，請參閱 [Azure 資料湖存放區](data-lake-store-overview.md)。

## 必要條件

* Visual Studio 2013 或 2015。以下指示使用 Visual Studio 2015。
* **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
* **啟用您的 Azure 訂用帳戶**以使用資料湖存放區公開預覽版。請參閱[指示](data-lake-store-get-started-portal.md#signup)。

## 建立 .NET 應用程式

1. 開啟 Visual Studio，建立主控台應用程式。

2. 從 [檔案] 功能表中，按一下 [新增]，再按 [專案]。

3. 在 [**新增專案**] 中，輸入或選取下列值：

	| 屬性 | 值 |
	|----------|-----------------------------|
	| 類別 | 範本/Visual C#/Windows |
	| 範本 | 主控台應用程式 |
	| 名稱 | CreateADLApplication |

4. 按一下 [確定] 以建立專案。

5. 將 Nuget 封裝新增至您的專案。

	1. 在方案總管中以滑鼠右鍵按一下專案名稱，然後按一下 [**管理 NuGet 封裝**]。
	2. 在 [Nuget 封裝管理員] 索引標籤中，請確定 [封裝來源] 設為 [nuget.org]，且已選取 [包含發行前版本] 核取方塊。
	3. 搜尋並安裝下列資料湖存放區封裝：
	
		* Microsoft.Azure.Management.DataLake.Store
		* Microsoft.Azure.Management.DataLake.StoreFileSystem
		* Microsoft.Azure.Management.DataLake.StoreUploader

		![新增 Nuget 來源](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "建立新的 Azure 資料湖帳戶")

	4. 您也應安裝 **Microsoft.Azure.Common.Authentication** 封裝。這也是發行前版本封裝，是對 Azure 資料湖存放區進行驗證時的必要項目。

		![新增 Nuget 來源](./media/data-lake-store-get-started-net-sdk/adl.install.azure.auth.png "建立新的 Azure 資料湖帳戶")

	4. 關閉 [**Nuget 封裝管理員**]。

7. 開啟 [**Program.cs**]，並以下列程式碼取代現有的程式碼區塊。此外，請為程式碼片段中的參數 (例如 subscriptionId、dataLakeAccountName 和 localPath) 提供值。

	這個程式碼會進行建立資料湖存放區的程序，在存放區中建立資料夾、上傳檔案、下載檔案，以及最後刪除帳戶。如果您要尋找一些可上傳的範例資料，您可以從 [Azure 資料湖 Git 儲存機制](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)取得 **Ambulance Data** 資料夾。
	
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Security;
		using System.IO;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.DataLake.Store;
		using Microsoft.Azure.Management.DataLake.Store.Models;
		using Microsoft.Azure.Management.DataLake.StoreFileSystem;
		using Microsoft.Azure.Management.DataLake.StoreFileSystem.Models;
		using Microsoft.Azure.Management.DataLake.StoreUploader;
		using Microsoft.Azure.Common.Authentication.Factories;
		
		
		namespace CreateADLApplication
		{
		    class CreateADLApplication
		    {
		        private static DataLakeStoreManagementClient _dataLakeStoreClient;
		        private static DataLakeStoreFileSystemManagementClient _dataLakeStoreFileSystemClient;
		        private const string ResourceGroupName = "<resource_grp_name>"; //THIS SHOULD ALREADY EXIST
		
		        static void Main(string[] args)
		        {
		            var subscriptionId = new Guid("<subscription_ID>");
		            var _credentials = GetAccessToken();
		            string dataLakeAccountName = "<data_lake_store_name>";
		            string localPath = @"C:\local_path\file.txt"; //Change this
		            string remoteFolder = "/data_lake_path/";
		            string remotePath = remoteFolder + "file.txt";
		            
		            string location = "East US 2";
		
		            _credentials = GetCloudCredentials(_credentials, subscriptionId);
		            _dataLakeStoreClient = new DataLakeStoreManagementClient(_credentials);
		            _dataLakeStoreFileSystemClient = new DataLakeStoreFileSystemManagementClient(_credentials);
		
		            var parameters = new DataLakeStoreAccountCreateOrUpdateParameters();
		            parameters.DataLakeStoreAccount = new DataLakeStoreAccount
		            {
		                Name = dataLakeAccountName,
		                Location = location
		            };
		
		            // Create a Data Lake Store account
		            Console.WriteLine("Creating an Azure Data Lake Store account ...");
		            _dataLakeStoreClient.DataLakeStoreAccount.Create(ResourceGroupName, parameters);
		
		            Console.WriteLine("Account created. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Create a directory in the store
		            Console.WriteLine("Creating a directory under the Azure Data Lake Store account");
		            CreateDir(_dataLakeStoreFileSystemClient, dataLakeAccountName, remoteFolder, "777");
		            Console.WriteLine("Directory created. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Upload a file under the new folder
		            Console.WriteLine("Uploading a file to the Azure Data Lake Store account");
		            bool force = false; //Set this to true if you want to overwrite existing data
		            UploadFile(_dataLakeStoreFileSystemClient, dataLakeAccountName, localPath, remotePath, force);
		            Console.WriteLine("File uploaded. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // List the files in the Data Lake Store
		            Console.WriteLine("Listing all files in the Azure Data Lake Store account");
		            var fileList = ListItems(_dataLakeStoreFileSystemClient, dataLakeAccountName, remoteFolder);
		            var fileMenuItems = fileList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix)).ToList();
		            foreach (var filename in fileMenuItems)
		            {
		                Console.WriteLine(filename);
		
		            }
		            Console.WriteLine("Files listed. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Download the files from the Data Lake Store
		            Console.WriteLine("Downloading files from an Azure Data Lake Store account");
		            DownloadFile(_dataLakeStoreFileSystemClient, dataLakeAccountName, remotePath, localPath, force);
		            Console.WriteLine("Files downloaded. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Delete the Data Lake Store account
		            Console.WriteLine("Azure Data Lake Store account will be deleted. Press ENTER to continue...");
		            _dataLakeStoreClient.DataLakeStoreAccount.Delete(ResourceGroupName, dataLakeAccountName);
		            Console.ReadLine();
		            Console.WriteLine("Account deleted. Press ENTER to exit...");
		            Console.ReadLine();
		        }
		
		        public static SubscriptionCloudCredentials GetAccessToken(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		            return new TokenCloudCredentials(authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		        public static bool CreateDir(DataLakeStoreFileSystemManagementClient dataLakeStoreFileSystemClient, string dlAccountName, string path, string permission)
		        {
		            dataLakeStoreFileSystemClient.FileSystem.Mkdirs(path, dlAccountName, permission);
		            return true;
		        }
		
		        public static bool UploadFile(DataLakeStoreFileSystemManagementClient dataLakeStoreFileSystemClient, string dlAccountName, string srcPath, string destPath, bool force = true)
		        {
		            var parameters = new UploadParameters(srcPath, destPath, dlAccountName, isOverwrite: force);
		            var frontend = new DataLakeStoreFrontEndAdapter(dlAccountName, dataLakeStoreFileSystemClient);
		            var uploader = new DataLakeStoreUploader(parameters, frontend);
		            uploader.Execute();
		            return true;
		        }
		        
		        public static bool AppendToFile(DataLakeStoreFileSystemManagementClient dataLakeStoreFileSystemClient, string dlAccountName, string path, string content)
		        {
		            var stream = new MemoryStream(System.Text.Encoding.UTF8.GetBytes(content));
		            
		            dataLakeStoreFileSystemClient.FileSystem.DirectAppend(path, dlAccountName, stream, null);
		            return true;
		        }
		
		        public static void DownloadFile(DataLakeStoreFileSystemManagementClient dataLakeFileSystemClient,
		        string dataLakeAccountName, string srcPath, string destPath, bool force)
		        {
		            var beginOpenResponse = dataLakeFileSystemClient.FileSystem.BeginOpen(srcPath, dataLakeAccountName,
		                new FileOpenParameters());
		            var openResponse = dataLakeFileSystemClient.FileSystem.Open(beginOpenResponse.Location);
		            if (force || !File.Exists(destPath))
		                File.WriteAllBytes(destPath, openResponse.FileContents);
		        }
		
		        public static List<FileStatusProperties> ListItems(DataLakeStoreFileSystemManagementClient dataLakeFileSystemClient, string dataLakeAccountName, string path)
		        {
		            var response = dataLakeFileSystemClient.FileSystem.ListFileStatus(path, dataLakeAccountName, new DataLakeStoreFileSystemListParameters());
		            return response.FileStatuses.FileStatus.ToList();
		        }
		    }
		}


8. 建置並執行應用程式。請依照提示來執行並完成應用程式。

## 建立資料湖存放區帳戶的其他方式

- [使用入口網站開始使用資料湖存放區](data-lake-store-get-started-portal.md)
- [使用 PowerShell 開始使用資料湖存放區](data-lake-store-get-started-powershell.md)
- [使用 Azure CLI 開始使用資料湖存放區](data-lake-store-get-started-cli.md)


## 後續步驟

- [保護資料湖存放區中的資料](data-lake-store-secure-data.md)
- [搭配資料湖存放區使用 Azure 資料湖分析](data-lake-analytics-get-started-portal.md)
- [搭配資料湖存放區使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0204_2016-->