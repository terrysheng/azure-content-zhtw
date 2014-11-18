<properties urlDisplayName="File Service" pageTitle="如何使用 Azure 檔案儲存體 | Microsoft Azure" metaKeywords="Get started Azure file  Azure file share  Azure file shares  Azure file   Azure file storage   Azure file .NET   Azure file C#   Azure file PowerShell" description="Learn how to use Microsoft Azure File storage to create file shares and manage file content. Samples are written in PowerShell and C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure File storage in .NET" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# 如何使用 Azure 檔案儲存體

在本入門指南中，我們將說明使用 Microsoft Azure 檔案儲存體的基本概念。首先，我們會使用 PowerShell 說明如何建立新的 Azure 檔案共用、新增目錄、上傳本機檔案至共用，及列出目錄中的檔案。然後，我們會說明如何從 Azure 虛擬機器掛接檔案共用，就好像您掛接任何 SMB 共用一樣。

針對想要從內部部署應用程式，以及 Azure 虛擬機器或雲端服務中存取共用中檔案的使用者，我們將說明如何使用 Azure .NET 儲存體用戶端程式庫，從桌面應用程式中使用檔案共用。

> [WACOM.NOTE] 若要執行本指南中的 .NET 程式碼範例，您需要 Azure .NET 儲存體用戶端程式庫 4.x 或更新版本。您可透過 [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) 取得儲存體用戶端程式庫。


##目錄

-   [什麼是檔案儲存體？][]
-   [檔案儲存體概念][]
-   [建立 Azure 儲存體帳戶][]
-   [使用 PowerShell 建立檔案共用][]
-	[從 Azure 虛擬機器掛接共用][]
-   [建立內部部署應用程式以存取檔案儲存體][]
-   [後續步驟][]


##<a name="what-is-file-storage"></a><span class="short-header">什麼是 Azure 檔案儲存體？</span>什麼是 Azure 檔案儲存體？

檔案儲存體為使用標準 SMB 2.1 通訊協定的應用程式提供共用儲存體。Microsoft Azure 虛擬機器和雲端服務可以透過掛接的共用，在應用程式元件之間共用檔案資料，而內部部署應用程式可以透過檔案儲存體 API，存取共用中的檔案資料。

在 Azure 虛擬機器或雲端服務中執行的應用程式，可掛接檔案儲存體共用來存取檔案資枓，就像桌面應用程式掛接一般 SMB 共用一樣。可同時掛接和存取檔案儲存體共用的 Azure 虛擬機器或角色數量沒有限制。

因為檔案儲存體是標準 SMB 2.1 檔案共用，所以 Azure 中執行的應用程式可透過檔案 I/O API 來存取共用中的資料。因此，開發人員可利用現有的程式碼和技能來移轉現有的應用程式。IT 專業人員在管理 Azure 應用程式時，可以使用 PowerShell Cmdlet 來建立、掛接和管理檔案儲存體共用。本指南將說明這兩者的範例。

檔案儲存體的一般用途包括：

- 移轉必須在 Azure 虛擬機器或雲端服務上執行檔案共用的內部部署應用程式，而無需支付昂貴的重新撰寫程式費用。
- 儲存共用應用程式設定 (例如，在組態檔中)
- 在共用位置儲存診斷資料 (例如記錄、度量和損毀傾印) 
- 儲存開發或管理 Azure 虛擬機器或雲端服務所需的工具和公用程式

##<a name="file-storage-concepts"></a><span class="short-header">檔案儲存體概念</span>檔案儲存體概念

檔案儲存體包含下列元件：

![files-concepts][files-concepts]


-   **儲存體帳戶：** 所有對 Azure 儲存體的存取皆
    透過儲存體帳戶。如需關於儲存體帳戶容量的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](http://msdn.microsoft.com/zh-tw/library/dn249410.aspx) (英文)。

-   **共用：** 檔案儲存體共用是指在 Azure 中共用的 SMB 2.1 檔案。 
    所有的目錄和檔案都必須在父共用中建立。帳戶可以包含
    無限制數目的共用，而共用可儲存無限制
    數目的檔案，最多可達儲存體帳戶的容量限制。

-   **Directory: ** 選擇性的目錄階層。 

-	**檔案：** 共用中的檔案。檔案的大小可高達 1 TB。

-   **URL 格式：** 可利用下列 URL 格式來定址
    檔案：   
    https://`<storage
    account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`  
    
    下列範例 URL 可用來定址上圖的其中一個
    檔案：  
    `http://acmecorp.file.core.windows.net/cloudfiles/diagnostics/log.txt`



如需有關如何為共用、目錄和檔案命名的詳細資料，請參閱[命名和參考共用、目錄、檔案及中繼資料](http://msdn.microsoft.com/zh-tw/library/azure/dn167011.aspx)。

##<a name="create-account"></a><span class="short-header">建立 Azure 儲存體帳戶</span>建立 Azure 儲存體帳戶

Azure 檔案儲存體目前為預覽版。若要要求預覽版的存取權，請瀏覽至 [Microsoft Azure 預覽版頁面](/zh-tw/services/preview/)，然後要求 **Azure 檔案**的存取權。要求一經核准，您就會收到您可以存取檔案儲存體預覽版的通知。接著，您可以建立儲存體帳戶以存取檔案儲存體。

> [WACOM.NOTE] 檔案儲存體目前僅適用於新的儲存體帳戶。在您的訂閱被賦予檔案儲存體的存取權之後，請建立新的儲存體帳戶以與本指南配合使用。

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="use-cmdlets"></a><span class="short-header">使用 PowerShell 建立檔案共用</span>使用 PowerShell 建立檔案共用

###安裝適用於 Azure 儲存體的 PowerShell Cmdlet

若要準備使用 PowerShell，請下載並安裝 Azure PowerShell Cmdlet。如需安裝點和安裝指示的詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](/zh-tw/documentation/articles/install-configure-powershell/)。

> [WACOM.NOTE] 只有最新的 Azure PowerShell 模組 0.8.5 版和更新版本才提供適用於檔案服務的 PowerShell Cmdlet。建議您下載和安裝或升級至最新的 Azure PowerShell 模組。

透過按一下 [**啟動**]，然後輸入 **Windows Azure PowerShell** 來開啟 Azure PowerShell 視窗。Azure PowerShell 視窗便會為您載入 Azure Powershell 模組。

###建立儲存體帳戶和金鑰的內容

現在，請建立儲存體帳戶內容。內容包含帳戶名稱和帳戶金鑰。使用下列範例中的帳戶名稱和金鑰來取代 `account-name` 和 `account-key`：

    # 建立帳戶和金鑰的內容
    $ctx=New-AzureStorageContext account-name account-key
    
###建立新的檔案共用

接著，在本範例中建立名為 `sampleshare` 的新共用：

    # 建立新的共用
    $s = New-AzureStorageShare sampleshare -Context $ctx

現在，您的檔案儲存體中便有一個檔案共用。接下來，我們將新增目錄和檔案。

###在檔案共用中建立目錄

接下來，我們將在共用中建立目錄。在下列範例中，目錄的名稱為 `sampledir`：

    # 在共用中建立目錄
    New-AzureStorageDirectory -Share $s -Path sampledir

###上傳本機檔案至目錄

現在，請將本機檔案上傳至目錄。下列範例會將 `C:\temp\samplefile.txt` 中的檔案上傳。編輯檔案路徑，以指向本機機器上的有效檔案： 
    
    # 將本機檔案上傳至目錄
    Set-AzureStorageFileContent -Share $s -Source C:\temp\samplefile.txt -Path sampledir

###列出目錄中的檔案

若要查看目錄中的檔案，您可以列出目錄的檔案。此命令也會列出子目錄，但在本範例中因為沒有子目錄，所以只會列出檔案。  

    # 列出目錄中的檔案
    Get-AzureStorageFile -Share $s -Path sampledir

##<a name="mount-share"></a><span class="short-header">從 Azure 虛擬機器掛接共用</span>從 Azure 虛擬機器掛接共用

為說明如何掛接 Azure 檔案共用，我們現在將建立 Azure 虛擬機器，並遠端進入該虛擬機器以掛接共用。 

1. 首先，依照[建立執行 Windows Server 的虛擬機器](/zh-tw/documentation/articles/virtual-machines-windows-tutorial/)中的指示建立新的 Azure 虛擬機器。
2. 接著依照[如何登入執行 Windows Server 的虛擬機器](/zh-tw/documentation/articles/virtual-machines-log-on-windows-server/)中的指示遠端進入此虛擬機器。
3. 在虛擬機器上開啟 PowerShell 視窗。 

###在虛擬機器中保留您的儲存體帳戶認證

在掛接到檔案共用之前，請先將儲存體帳戶認證保留在虛擬機器上。此步驟可讓 Windows 在虛擬機器重新開機時自動重新連線到檔案共用。若要保留您的帳戶認證，請從虛擬機器的 PowerShell 視窗內執行 `cmdkey` 命令。使用您的儲存體帳戶名稱來取代 `<storage-account>`，並使用您的儲存體帳戶金鑰來取代 `<account-key>`：

	cmdkey /add:<storage-account>.file.core.windows.net /user:<storage-account> /pass:<account-key>

Windows 現在便可在虛擬機器重新開機時重新連線到檔案共用。您可以驗證是否已重新連線共用，方法是從 PowerShell 視窗內執行 `net use` 命令。

###使用保留的認證掛裝檔案共用

在遠端連線到虛擬機器後，您可以使用下列語法執行 `net use` 命令以掛接檔案共用。使用您的儲存體帳戶名稱來取代 `<storage-account>`，並使用您的檔案儲存體共用名稱來取代 `<share-name>`。

	net use z:\\<storage-account>.file.core.windows.net\<share-name>

> [WACOM.NOTE] 由於您已在上一個步驟中保留儲存體帳戶認證，因此您無需使用 `net use` 命令提供這些認證。如果您尚未保留認證，則將他們作為傳送到 `net use` 命令的參數包括在其中。使用您的儲存體帳戶名稱來取代 `<storage-account>`、使用您的檔案儲存體共用名稱來取代 `<share-name>`，並使用您的儲存體帳戶金鑰來取代 `<account-key>`：
	   
	net use z:\\<storage-account>.file.core.windows.net\<share-name> /u:<storage-account> <account-key>

您現在可以從虛擬機器使用檔案儲存體共用，就好像操作任何其他磁碟機一樣。您可以從命令提示字元中發佈標準檔案命令，或從 [檔案總管] 中檢視掛接的共用及其內容。您也可以使用標準 Windows 檔案 I/O API，例如 .NET Framework 中 [System.IO namespaces](http://msdn.microsoft.com/zh-tw/library/gg145019(v=vs.110).aspx) 所提供的那些 API，在可存取檔案共用的虛擬機器內執行程式碼。 

您也可以從在 Azure 雲端服務上執行的角色來掛接檔案共用，方法是透過遠端進入此角色。

##<a name="create-console-app"></a><span class="short-header">建立內部部署應用程式以使用檔案儲存體</span>建立內部部署應用程式以使用檔案儲存體

您可以從在 Azure 執行的虛擬機器或雲端服務中掛接檔案儲存體共用，如上所述。不過，您無法從內部部署應用程式掛接檔案儲存體共用。若要從內部部署應用程式存取共用資料，您必須使用檔案儲存體 API。本範例將說明如何透過 [Azure .NET 儲存體用戶端程式庫](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)使用檔案共用。 

為說明如何從內部部署應用程式使用 API，我們將建立在桌面上執行的簡單主控台應用程式。

###建立主控台應用程式並取得組件

若要在 Visual Studio 中建立新的主控台應用程式，並安裝 Azure 儲存體 NuGet 封裝：

1. 在 Visual Studio 中，依序選擇 [**檔案**] -> [**新增專案**]，然後從 Visual C# 範本清單中選擇 [**Windows**] -> [**主控台應用程式**]。
2. 提供主控台應用程式的名稱，然後按一下 [**確定**]。
3. 建立專案後，在 [方案總管] 中以滑鼠右鍵按一下該專案，然後選擇 [**管理 NuGet 封裝**]。在線上搜尋 "WindowsAzure.Storage"，再按一下 [**安裝**] 以安裝 Azure 儲存體封裝與相依性。

###將您的儲存體帳戶認證儲存到 app.config 檔案

接著，將您的認證儲存到專案的 app.config 檔案。編輯 app.config 檔案，使其看起來類似於下列範例，使用您的儲存體帳戶名稱來取代 `myaccount`，並使用您的儲存體帳戶金鑰來取代 `mykey`：

    <?xml version="1.0" encoding="utf-8"?>
	<configuration>
		<startup> 
			<supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		</startup>
		<appSettings>
			<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey" />
		</appSettings>
	</configuration>

> [WACOM.NOTE] 最新版本的 Azure 儲存體模擬器不支援檔案儲存體。您的連接字串必須以雲端中具有檔案預覽版存取權的 Azure 儲存體帳戶為目標。


###新增命名空間宣告
在 [方案總管] 中開啟 program.cs 檔案，並在檔案的開頭處加入下列命名空間宣告：

    using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.File;

###以程式設計的方法擷取連接字串
您可以使用 `Microsoft.WindowsAzure.CloudConfigurationManager` 類別或 `System.Configuration.ConfigurationManager `類別，從 app.config 檔案中擷取所儲存的認證。此處的範例說明如何使用 `CloudConfigurationManager` 類別來擷取認證，並將他們包含在 `CloudStorageAccount` 類別中。在 program.cs 的 `Main()` 方法中新增下列程式碼：

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

###以程式設計的方式存取檔案儲存體共用

接著，在 `Main()` 方法的上述程式碼後面加入下列程式碼，以擷取連接字串。此程式碼會取得稍早所建立檔案的參考，並將其內容輸出到主控台視窗。

	//Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	//Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("sampleshare");

	//Ensure that the share exists.
    if (share.Exists())
    {
		//Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

		//Get a reference to the sampledir directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("sampledir");

		//Ensure that the directory exists.
        if (sampleDir.Exists())
        {
			//Get a reference to the file we created previously.
            CloudFile file = sampleDir.GetFileReference("samplefile.txt");

			//Ensure that the file exists.
            if (file.Exists())
            {
				//Write the contents of the file to the console window.
                Console.WriteLine(file.DownloadTextAsync().Result);
            }
        }
    }

執行主控台應用程式以查看此輸出。

## <a name="next-steps"></a><span  class="short-header">後續步驟</span>後續步驟

了解檔案儲存體的基礎概念之後，請參考下列連結
以了解詳細資訊。
<ul>
<li>如需可用 API 的完整詳細資訊，請檢視檔案服務參考文件：
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Storage Client Library for .NET 參考資料</a>
    </li>
    <li><a href="http://msdn.microsoft.com/zh-tw/library/azure/dn167006.aspx">檔案服務 REST API 參考</a></li>
  </ul>
</li>
<li>檢視與檔案服務相關的 Azure 儲存體團隊部落格文章：
  <ul>
    <li><a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx">Microsoft Azure 檔案服務簡介</a>
    </li>
    <li><a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx">保留與 Microsoft Azure 檔案的連線</a></li>
  </ul>
</li><li>如需了解 Azure 中的其他資料儲存選項，請檢視更多功能指南。
  <ul>
    <li>使用<a href="/zh-tw/documentation/articles/storage-dotnet-how-to-use-blobs/">佇列儲存體</a>儲存非結構化資料。</li>
    <li>使用<a href="/zh-tw/documentation/articles/storage-dotnet-how-to-use-tables/">資料表儲存體</a>儲存結構化資料。</li>
    <li>使用<a href="/zh-tw/documentation/articles/storage-dotnet-how-to-use-queues/">佇列儲存體</a>可靠地儲存訊息。</li>
    <li>使用 <a href="/zh-tw/documentation/articles/sql-database-dotnet-how-to-use/">SQL Database</a> 儲存關聯式資料。</li>
  </ul>
</li>
</ul>

[後續步驟]: #next-steps
[什麼是檔案儲存體？]: #what-is-file-storage 
[檔案儲存體概念]: #file-storage-concepts
[建立 Azure 儲存體帳戶]: #create-account
[使用 PowerShell 建立檔案共用]: #use-cmdlets
[從 Azure 虛擬機器掛接共用]: #mount-share
[建立內部部署應用程式以存取檔案儲存體]: #create-console-app

[files-concepts]: ./media/storage-dotnet-how-to-use-files/files-concepts.png

