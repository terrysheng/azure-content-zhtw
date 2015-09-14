<properties
			pageTitle="如何搭配 PowerShell 與 .NET 使用 Azure 檔案儲存體 | Microsoft Azure"
	description="了解如何使用 Azure 檔案儲存體來建立檔案共用和管理檔案內容。檔案儲存體可讓企業將依賴 SMB 檔案共用的應用程式移到 Azure。保留虛擬機器的儲存體帳戶認證，以便重新啟動時連接到檔案共用。"
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="adinah"
	editor=""/>

<tags ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/04/2015"
	ms.author="tamram"/>

# 如何搭配 PowerShell 與 .NET 使用 Azure 檔案儲存體

## 概觀

Azure 檔案服務會公開使用標準 SMB 2.1 通訊協定的檔案共用。在 Azure 中執行的應用程式可使用標準和類似的檔案系統 API (如 ReadFile 和 WriteFile) 在 VM 之間輕鬆共用檔案。此外，檔案也可以同時透過 REST 介面存取，這樣會開啟各種混合式案例。最後，Azure 檔案服務是使用與 Blob、資料表和佇列服務相同的技術所建置，這表示 Azure 檔案服務能夠運用現有的可用性、持續性、延展性和建置於 Azure 儲存體平台內的異地備援。

## 關於本教學課程

本入門教學課程將說明使用 Microsoft Azure 檔案儲存體的基本概念。在此教學課程中，我們將：

- 使用 PowerShell 示範如何建立新的 Azure 檔案服務共用、新增目錄、將本機檔案上傳至共用及列出目錄中的檔案。
- 從 Azure 虛擬機器裝載檔案共用，就如同您裝載任何 SMB 共用一樣。
- 使用 .NET 適用的 Azure 儲存體用戶端程式庫，從內部部署應用程式存取檔案共用。建立主控台應用程式，並使用檔案共用執行這些動作：
	- 將共用中的檔案內容寫入主控台視窗。
	- 設定檔案共用的配額 (大小上限)。
	- 為使用共用上所定義之共用存取原則的檔案建立共用存取簽章。
	- 將檔案複製到相同儲存體帳戶中的另一個檔案。
	- 將檔案複製到相同儲存體帳戶中的 Blob。

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]


## 建立 Azure 儲存體帳戶

Azure 檔案儲存體目前為預覽版。若要要求預覽版的存取權，請瀏覽至 [Azure Preview 入口網站](/services/preview/)，然後要求 **Azure 檔案**的存取權。要求一經核准，您就會收到您可以存取檔案儲存體預覽版的通知。接著，您可以建立儲存體帳戶以存取檔案儲存體。

> [AZURE.NOTE]檔案儲存體目前僅適用於新的儲存體帳戶。在您的訂閱被賦予檔案儲存體的存取權之後，請建立新的儲存體帳戶以與本指南配合使用。
>
> Azure 檔案儲存體目前不支援共用存取簽章。

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 使用 PowerShell 管理檔案共用

接下來，我們將使用 Azure PowerShell 建立檔案共用。檔案共用建立之後，您可以從任何支援 SMB 2.1 的檔案系統掛接此共用。

### 安裝適用於 Azure 儲存體的 PowerShell Cmdlet

若要準備使用 PowerShell，請下載並安裝 Azure PowerShell Cmdlet。如需安裝點和安裝指示的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)。

> [AZURE.NOTE]只有最新的 Azure PowerShell 模組 0.8.5 版和更新版本才提供適用於檔案服務的 PowerShell Cmdlet。建議您下載和安裝或升級至最新的 Azure PowerShell 模組。

透過按一下 [啟動]，然後輸入 **Azure PowerShell** 來開啟 Azure PowerShell 視窗。Azure PowerShell 視窗便會為您載入 Azure Powershell 模組。

### 建立儲存體帳戶和金鑰的內容

現在，請建立儲存體帳戶內容。內容包含儲存體帳戶名稱和帳戶金鑰。如需從 Azure 入口網站複製帳戶金鑰的指示，請參閱[檢視、複製和重新產生儲存體存取金鑰](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。

使用下列範例中的儲存體帳戶名稱和金鑰來取代`storage-account-name` 和 `storage-account-key`

	# create a context for account and key
	$ctx=New-AzureStorageContext storage-account-name storage-account-key

### 建立新的檔案共用

接著，建立名為 `logs` 的新共用。

	# create a new share
	$s = New-AzureStorageShare logs -Context $ctx

現在，您的檔案儲存體中便有一個檔案共用。接下來，我們將新增目錄和檔案。

> [AZURE.IMPORTANT]您的檔案共用名稱必須是全部小寫。如需有關為檔案共用與檔案命名的完整詳細資料，請參閱[命名和參考共用、目錄、檔案及中繼資料](https://msdn.microsoft.com/library/azure/dn167011.aspx)。

### 在檔案共用中建立目錄

接下來，我們將在共用中建立目錄。在下列範例中，目錄的名稱為 `CustomLogs`

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path CustomLogs

### 上傳本機檔案至目錄

現在，請上傳本機檔案至目錄。下列範例會從 `C:\temp\Log1.txt` 上傳檔案。編輯檔案路徑，以指向本機機器上的有效檔案。

    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs

### 列出目錄中的檔案

若要查看目錄中的檔案，您可以列出目錄的檔案。此命令也會列出子目錄，但在本範例中因為沒有子目錄，所以只會列出檔案。

	# list files in the new directory
	Get-AzureStorageFile -Share $s -Path CustomLogs

### 複製檔案

從 Azure PowerShell 0.9.7 版開始，您可以將檔案複製到另一個檔案、將檔案複製到 Blob 或將 Blob 複製到檔案。下列示範如何使用 Cmdlet 執行這些複製作業。

	# copy a file to the new directory
    Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx
    # copy a blob to a file directory
    Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx


## 從執行 Windows 的 Azure 虛擬機器掛接共用

為說明如何掛接 Azure 檔案共用，我們現在將建立執行 Windows 的 Azure 虛擬機器，並遠端進入該虛擬機器以掛接共用。

1. 首先，依照[建立執行 Windows Server 的虛擬機器](../virtual-machines-windows-tutorial.md)中的指示建立新的 Azure 虛擬機器。
2. 接著依照[如何登入執行 Windows Server 的虛擬機器](../virtual-machines-log-on-windows-server.md)中的指示遠端進入此虛擬機器。
3. 在虛擬機器上開啟 PowerShell 視窗。

### 在虛擬機器中保留您的儲存體帳戶認證

在掛接到檔案共用之前，請先將儲存體帳戶認證保留在虛擬機器上。此步驟可讓 Windows 在虛擬機器重新開機時自動重新連線到檔案共用。若要保留您的帳戶認證，請從虛擬機器的 PowerShell 視窗內執行 `cmdkey` 命令。使用您的儲存體帳戶名稱來取代 `<storage-account-name>`，並使用您的儲存體帳戶金鑰來取代 `<storage-account-key>`

	cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>

Windows 現在便可在虛擬機器重新開機時重新連線到檔案共用。您可以驗證是否已重新連線共用，方法是從 PowerShell 視窗執行 `net use` 命令。

### 使用保留的認證掛裝檔案共用

在遠端連線到虛擬機器後，您可以使用下列語法執行 `net use` 命令以掛接檔案共用。使用您的儲存體帳戶名稱來取代 `<storage-account-name>`，並使用您的檔案儲存體共用名稱來取代 `<share-name>`。

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name>

	example :
	net use z: \\samples.file.core.windows.net\logs

由於您已在上一個步驟中保留儲存體帳戶認證，因此您無需使用 `net use` 命令提供這些認證。如果您尚未保存認證，則請將它們當作傳送到 `net use` 命令的參數包括在其中，如下列範例所示。

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name> /u:<storage-account-name> <storage-account-key>

	example :
	net use z: \\samples.file.core.windows.net\logs /u:samples <storage-account-key>

您現在可以從虛擬機器使用檔案儲存體共用，就好像操作任何其他磁碟機一樣。您可以從命令提示字元中發佈標準檔案命令，或從 [檔案總管] 中檢視掛接的共用及其內容。您也可以使用標準 Windows 檔案 I/O API (例如 .NET Framework 中 [System.IO namespaces](http://msdn.microsoft.com/library/gg145019.aspx) 所提供的那些 API)，在可存取檔案共用的虛擬機器內執行程式碼。

您也可以從在 Azure 雲端服務上執行的角色來掛接檔案共用，方法是透過遠端進入此角色。

## 建立內部部署應用程式來與檔案儲存體搭配使用

您可以從在 Azure 執行的虛擬機器或雲端服務掛接檔案共用，如先前所述。不過，您無法從內部部署應用程式掛接檔案共用。若要從內部部署應用程式存取共用資料，您必須使用檔案儲存體 API。本範例將說明如何透過 [Azure .NET 儲存體用戶端程式庫](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)使用檔案共用。

為說明如何從內部部署應用程式使用 API，我們將建立在桌面上執行的簡單主控台應用程式。

### 建立主控台應用程式並取得組件

若要在 Visual Studio 中建立新的主控台應用程式，並安裝 Azure 儲存體 NuGet 封裝：

1. 在 Visual Studio 中，依序選擇 [檔案] -> [新增專案]，然後從 Visual C# 範本清單中選擇 [Windows] -> [主控台應用程式]。
2. 提供主控台應用程式的名稱，然後按一下 [確定]。
3. 建立專案後，在 [方案總管] 中以滑鼠右鍵按一下該專案，然後選擇 [管理 NuGet 封裝]。在線上搜尋 "WindowsAzure.Storage"，再按一下 [安裝] 以安裝 Azure 儲存體封裝與相依性。

### 將您的儲存體帳戶認證儲存到 app.config 檔案

接著，將您的認證儲存到專案的 app.config 檔案。編輯 app.config 檔案，使其看起來類似於下列範例，使用您的儲存體帳戶名稱來取代 `myaccount`，並使用您的儲存體帳戶金鑰來取代 `mykey`

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

> [AZURE.NOTE]最新版本的 Azure 儲存體模擬器不支援檔案儲存體。您的連接字串必須以雲端中具有檔案儲存體預覽版存取權的 Azure 儲存體帳戶為目標。


### 新增命名空間宣告

在 [方案總管] 中開啟 program.cs 檔案，並在檔案的開頭處加入下列命名空間宣告。

	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.WindowsAzure.Storage.File;

### 以程式設計的方法擷取連接字串

您可以使用 `Microsoft.WindowsAzure.CloudConfigurationManager` 類別或 `System.Configuration.ConfigurationManager ` 類別，從 app.config 檔案中擷取所儲存的認證。Microsoft Azure Configuration Manager 套件包含 `Microsoft.WindowsAzure.CloudConfigurationManager` 類別，並且可在 [Nuget](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) 上使用。

此處的範例說明如何使用 `CloudConfigurationManager` 類別來擷取認證，並將他們包含在 `CloudStorageAccount` 類別中。在 program.cs 的 `Main()` 方法中新增下列程式碼。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString")); 

### 以程式設計方式存取檔案共用

接著，在 `Main()` 方法 (上述程式碼後面) 加入下列程式碼，以擷取連接字串。此程式碼會取得稍早所建立檔案的參考，並將其內容輸出到主控台視窗。

	// Create a CloudFileClient object for credentialed access to File storage.
	CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	// Get a reference to the file share we created previously.
	CloudFileShare share = fileClient.GetShareReference("logs");

	// Ensure that the share exists.
	if (share.Exists())
	{
	    // Get a reference to the root directory for the share.
	    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

	    // Get a reference to the directory we created previously.
	    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

	    // Ensure that the directory exists.
	    if (sampleDir.Exists())
	    {
	        // Get a reference to the file we created previously.
	        CloudFile file = sampleDir.GetFileReference("Log1.txt");

	        // Ensure that the file exists.
	        if (file.Exists())
	        {
	            // Write the contents of the file to the console window.
	            Console.WriteLine(file.DownloadTextAsync().Result);
	        }
	    }
	}

執行主控台應用程式以查看此輸出。

## 設定檔案共用的大小上限

從 Azure 儲存體用戶端程式庫 5.x 版開始，您可以設定以 GB 為單位的檔案共用配額 (或大小上限)。您也可以檢查有多少資料目前儲存在共用上。

藉由設定共用的配額，您可以限制儲存在共用上的檔案大小總計。如果共用上的檔案大小總計超過共用上設定的配額，則用戶端將無法增加現有檔案的大小或建立新的檔案 (除非這些檔案為空白)。

下列範例示範如何檢查共用的目前使用狀況，以及如何設定共用的配額。

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    // Ensure that the share exists.
    if (share.Exists())
    {
        // Check current usage stats for the share.
		// Note that the ShareStats object is part of the protocol layer for the File service.
        Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
        Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

        // Specify the maximum size of the share, in GB.
        // This line sets the quota to be 10 GB greater than the current usage of the share.
        share.Properties.Quota = 10 + stats.Usage;
        share.SetProperties();

        // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
        share.FetchAttributes();
        Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
    }

## 產生檔案或檔案共用的共用存取簽章

從 Azure 儲存體用戶端程式庫 5.x 版開始，您可以產生檔案共用或個別檔案的共用存取簽章 (SAS)。您也可以在檔案共用上建立共用存取原則，以管理共用存取簽章。建議您建立共用存取原則，因為如果必須洩漏 SAS，它提供了一種撤銷 SAS 的方式。

下列範例會在共用上建立共用存取原則，然後使用該原則為共用中檔案上的 SAS 提供條件約束。

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    // Ensure that the share exists.
    if (share.Exists())
    {
        string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

        // Create a new shared access policy and define its constraints.
        SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
                Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
            };

        // Get existing permissions for the share.
        FileSharePermissions permissions = share.GetPermissions();

        // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        share.SetPermissions(permissions);

        // Generate a SAS for a file in the share and associate this access policy with it.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
        CloudFile file = sampleDir.GetFileReference("Log1.txt");
        string sasToken = file.GetSharedAccessSignature(null, policyName);
        Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

        // Create a new CloudFile object from the SAS, and write some text to the file.
        CloudFile fileSas = new CloudFile(fileSasUri);
        fileSas.UploadText("This write operation is authenticated via SAS.");
        Console.WriteLine(fileSas.DownloadText());
    }

如需建立與使用共用存取簽章的詳細資訊，請參閱[共用存取簽章：了解 SAS 模型](storage-dotnet-shared-access-signature-part-1.md)和[透過 Blob 服務建立與使用 SAS](storage-dotnet-shared-access-signature-part-2.md)。

## 複製檔案

從 Azure 儲存體用戶端程式庫 5.x 版開始，您可以將檔案複製到另一個檔案、將檔案複製到 Blob 或將 Blob 複製到檔案。在後續各節中，我們將示範如何以程式設計方式執行這些複製作業。

您也可以使用 AzCopy 將檔案複製到另一個檔案，或將 Blob 複製到檔案或反向操作。如需使用 AzCopy 複製檔案的詳細資訊，請參閱[如何搭配使用 AzCopy 與 Microsoft Azure 儲存體](storage-use-azcopy.md#copy-files-in-azure-file-storage-with-azcopy-preview-version-only)。

> [AZURE.NOTE]如果要將 Blob 複製到檔案，或將檔案複製到 Blob，您必須使用共用存取簽章 (SAS) 驗證來源物件，即使是在相同的儲存體帳戶內進行複製也一樣。

### 將檔案複製到另一個檔案

下列範例會將檔案複製到相同共用中的另一個檔案。由於此複製作業是在相同儲存體帳戶中的檔案間進行複製，所以您可以使用共用金鑰驗證執行複製。

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("logs");

    // Ensure that the share exists.
    if (share.Exists())
    {
        // Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

        // Get a reference to the directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

        // Ensure that the directory exists.
        if (sampleDir.Exists())
        {
            // Get a reference to the file we created previously.
            CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

            // Ensure that the source file exists.
            if (sourceFile.Exists())
            {
                // Get a reference to the destination file.
                CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

                // Start the copy operation.
                destFile.StartCopy(sourceFile);

                // Write the contents of the destination file to the console window.
                Console.WriteLine(destFile.DownloadText());
            }
        }
    }


### 將檔案複製到 Blob

下列範例會建立檔案，並將其複製到相同儲存體帳戶內的 Blob。此範例會建立來源檔案的 SAS，供服務用來在複製作業期間驗證來源檔案存取權。

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    // Create a new file share, if it does not already exist.
    CloudFileShare share = fileClient.GetShareReference("sample-share");
    share.CreateIfNotExists();

    // Create a new file in the root directory.
    CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
    sourceFile.UploadText("A sample file in the root directory.");

    // Get a reference to the blob to which the file will be copied.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
    CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

    // Create a SAS for the file that's valid for 24 hours.
    // Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
    // to authenticate access to the source object, even if you are copying within the same
    // storage account.
    string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
    {
        // Only read permissions are required for the source file.
        Permissions = SharedAccessFilePermissions.Read,
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
    });

    // Construct the URI to the source file, including the SAS token.
    Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

    // Copy the file to the blob.
    destBlob.StartCopy(fileSasUri);

    // Write the contents of the file to the console window.
    Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
    Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());

您可以用相同方式將 Blob 複製到檔案。如果來源物件為 Blob，則請建立 SAS，以便在複製作業期間驗證該 Blob 存取權。

## 透過 Linux 使用檔案儲存體

若要從 Linux 建立並管理檔案共用，請使用 Azure CLI。如需透過檔案儲存體使用 Azure CLI 的相關資訊，請參閱[搭配 Azure 儲存體使用 Azure CLI](storage-azure-cli.md#create-and-manage-file-shares)。

您可以從執行 Linux 的虛擬機器裝載 Azure 檔案共用。當您建立 Azure 虛擬機器時，可以從 Azure 映像庫指定支援 SMB 2.1 的 Linux 映像，例如 Ubuntu 最新版。不過，任何支援 SMB 2.1 的 Linux 發行版本都可裝載 Azure 檔案共用。

若要深入了解如何在 Linux 上裝載 Azure 檔案共用，請參閱 Cannel 9 上的[透過 Azure 檔案預覽在 Linux 上使用共用儲存體 - 第 1 部分](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)。

## 後續步驟

請參閱這些連結以取得 Azure 檔案儲存體的相關詳細資訊。

### 教學課程與參考

- [Storage Client Library for .NET 參考資料](https://msdn.microsoft.com/library/azure/dn261237.aspx)
- [檔案服務 REST API 參考](http://msdn.microsoft.com/library/azure/dn167006.aspx)
- [搭配使用 AzCopy 與 Microsoft Azure 儲存體](storage-use-azcopy.md)
- [搭配使用 Azure PowerShell 與 Azure 儲存體](storage-powershell-guide-full.md)
- [使用 Azure CLI 搭配 Azure 儲存體](storage-azure-cli.md)

### 部落格文章

- [Microsoft Azure 檔案服務簡介](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [保留與 Microsoft Azure 檔案的連線](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

<!---HONumber=September15_HO1-->