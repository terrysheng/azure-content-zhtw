<properties urlDisplayName="Get Started with Media Services" pageTitle="開始使用媒體服務 - Azure" metaKeywords="Azure media services" description="An introduction to using Media Services with Azure." metaCanonical="" services="media-services" documentationCenter="" title="Get started with Media Services" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





# <a name="getting-started"></a>開始使用媒體服務

本教學課程說明如何使用 Azure 媒體服務來開始開發。其中介紹基本的媒體服務工作流程，以及媒體服務開發最常用的程式設計物件和必要工作。完成本教學課程時，您將能夠播放您已上傳、編碼和下載的範例媒體檔案。或者，您也可以瀏覽至伺服器上的編碼資產並播放。 

這裡提供含有本教學課程之程式碼的 C# Visual Studio 專案：[下載](http://go.microsoft.com/fwlink/?linkid=253275)。

本教學課程將逐步引導您完成下列基本步驟：

* [設定專案](#Step1)
* [取得媒體服務伺服器內容](#Step2)
* [建立資產並將資產相關聯的檔案上傳至媒體服務](#Step3)
* [將資產編碼和下載輸出資產](#Step4)

## 必要條件
根據 Azure Media Services SDK 來逐步演練和開發之前需要有下列必要條件。

- 新的或現有 Azure 訂用帳戶中的媒體服務帳戶。如需詳細資料，請參閱[如何建立媒體服務帳戶] (英文)(http://go.microsoft.com/fwlink/?LinkId=256662)。
- 作業系統：Windows 7、Windows 2008 R2、Windows 8 或更新版本。
- .NET Framework 4.5 或 .NET Framework 4。
- Visual Studio 2012、Visual Studio 2010 SP1 (Professional、Premium、Ultimate 或 Express) 或更新版本。
- 安裝 **Azure SDK for .NET.**、**Azure Media Services SDK for .NET** 和 **WCF Data Services 5.0 for OData V3 程式庫** (使用 [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices) 封裝)。下一節示範如何安裝和加入這些參考。

>[WACOM.NOTE]
> 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">「Azure 免費試用」</a>。

<h2><a id="Step1"></a>設定專案</h2>

1. 在 Visual Studio 2012 或 Visual Studio 2010 SP1 中建立新的 C# 主控台應用程式。輸入 [**名稱**]、[**位置**] 和 [**方案名稱**]，然後按一下 [確定]。 

2. 加入 System.Configuration 組件的參考。

	若要使用 [**管理參考**] 對話方塊來加入參考，請執行下列動作。以滑鼠右鍵按一下 	[**方案總管**] 中的 [**參考**] 節點，然後選取 [**加入參考**]。在 	[**管理參考**] 對話方塊中，選取適當的組件 (在此案例中是 [System.Configuration])。

3. 如果尚未這麼做，加入 **Azure SDK for .NET**.(Microsoft.WindowsAzure.StorageClient.dll)、**Azure Media Services SDK for .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll) 和 **WCF Data Services 5.0 for OData V3** (Microsoft.Data.OData.dll) 程式庫的參考 (使用 <a href="http://nuget.org/packages/windowsazure.mediaservices">windowsazure.mediaservices Nuget</a> 封裝)。 

	若要使用 Nuget 加入參考，請執行下列動作。在 Visual Studio [主要功能表] 中，依序選取 [工具] -> [Library Package Manager] -> [Package Manager Console]。在主控台視窗中輸入 <i>Install-Package [封裝名稱]</i> 再按 Enter (在此情況下，請使用下列命令： <i>Install-Package windowsazure.mediaservices</i>。)

4. 將 *appSettings* 區段加入至 **app.config** 檔案，並設定 Azure 媒體服務帳戶名稱和帳戶金鑰的值。您已在帳戶設定過程中取得媒體服務帳戶名稱和帳戶金鑰。在 Visual Studio 專案的 app.config 檔案中，將這些值加入至每個設定的 value 屬性。

	> [WACOM.NOTE]
	> 在 Visual Studio 2012，預設會加入 App.config 檔案。在 Visual Studio 2010 中，您必須手動加入應用程式組態檔。

	<pre><code>
	<configuration>
  	. . . 
  	<appSettings>
    	<add key="accountName" value="Add-Media-Services-Account-Name" />
    	<add key="accountKey" value="Add-Media-Services-Account-Key" />
  	</appSettings>
	</configuration>
	</code></pre>

5. 在本機電腦上建立新資料夾，並命名為 supportFiles (在此範例中，supportFiles 位於 MediaServicesGettingStarted 專案目錄下)。Auch die Eigenschaften <a href="http://go.microsoft.com/fwlink/?linkid=253275">隨附此逐步解說的專案</a> 包含 supportFiles 目錄。您可以將此目錄的內容複製到 supportFiles 資料夾。

6. 在 Program.cs 檔案的開頭，使用下列程式碼來覆寫現有的 using 陳述式。

		using System;
		using System.Linq;
		using System.Configuration;
		using System.IO;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Collections.Generic;
		using Microsoft.WindowsAzure;
		using Microsoft.WindowsAzure.MediaServices.Client;


7. 加入下列類別層級路徑變數。**_supportFiles** 路徑應該指向您在先前步驟中建立的資料夾。 

		// Base support files path.  Update this field to point to the base path  
		// for the local support files folder that you create. 
		private static readonly string _supportFiles =
		            Path.GetFullPath(@"../..\supportFiles");
		
		// Paths to support files (within the above base path). You can use 
		// the provided sample media files from the "supportFiles" folder, or 
		// provide paths to your own media files below to run these samples.
		private static readonly string _singleInputFilePath =
		    Path.GetFullPath(_supportFiles + @"\multifile\interview2.wmv");
		private static readonly string _outputFilesFolder =
		    Path.GetFullPath(_supportFiles + @"\outputfiles");
		
8. 加入下列類別層級變數來擷取驗證和連接設定。這些設定取自於 App.Config 檔案中，需要它們才能連線到媒體服務、驗證和取得權杖，如此您才能存取伺服器內容。專案中的程式碼會參考這些變數，以建立伺服器內容的執行個體。
	
		private static readonly string _accountKey = ConfigurationManager.AppSettings["accountKey"];
		private static readonly string _accountName = ConfigurationManager.AppSettings["accountName"];

9. 加入下列類別層級變數做為伺服器內容的靜態參考。

		// Field for service context.
		private static CloudMediaContext _context = null;
		
<h2><a id="Step2"></a>取得媒體服務內容</h2>

媒體服務內容物件包含進行媒體服務程式設計時需要存取的所有基本物件和集合。內容包含重要集合的參考，包括作業、資產、檔案、存取原則、定位器和其他物件。大部分的媒體服務程式設計工作中都必須取得伺服器內容。

在 Program.cs 檔案中，加入下列程式碼當做 **Main** 方法中的第一個項目。此程式碼使用 app.config 檔案中的媒體服務帳戶名稱和帳戶金鑰值，以建立伺服器內容的執行個體。此執行個體要指派給您在類別層級上建立的 **_context** 變數。

	// Get the service context.
	_context = new CloudMediaContext(_accountName, _accountKey);
	
<h2><a id="Step3"></a>建立資產和上傳檔案</h2>

本節的程式碼會執行下列動作： 

1. 建立空資產<br/>
在建立資產時，您可以指定三種不同的選項來加密它們。 

	- **AssetCreationOptions.None**：不加密。如果要建立未加密的資產，您必須設定此選項。
	- **AssetCreationOptions.CommonEncryptionProtected**：適用於 Common Encryption Protected (CENC) 檔案。例如，已使用 PlayReady 加密的一組檔案。 
	- **AssetCreationOptions.StorageEncrypted**：儲存加密。將尚未上傳至 Azure 儲存體的純文字輸入檔加密。

		<div class="dev-callout"> 
	<strong>注意</strong> 
	<p>媒體服務提供磁碟上的儲存體加密，而不是在線上加密，例如數位版權管理員 (DRM)。</p> 
	</div>

2. 建立要與資產相關聯的 AssetFile 執行個體。
3. 建立 AccessPolicy 執行個體，以定義存取資產所需的權限和規定期間。
4. 建立可用來存取資產的定位器執行個體。
5. 將單一媒體檔案上傳至媒體服務。建立和上傳的程序也稱為吸收資產。

將下列方法加入至類別。

<pre><code>
static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
{
    var asset = _context.Assets.Create(assetName, assetCreationOptions);

    Console.WriteLine("Asset name: " + asset.Name);
    Console.WriteLine("Time created: " + asset.Created.Date.ToString());

    return asset;
}

static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
{
    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var fileName = Path.GetFileName(singleFilePath);

    var assetFile = asset.AssetFiles.Create(fileName);

    Console.WriteLine("Created assetFile {0}", assetFile.Name);

    var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(3),
                                                        AccessPermissions.Write | AccessPermissions.List);

    var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

    Console.WriteLine("Upload {0}", assetFile.Name);

    assetFile.Upload(singleFilePath);
    Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

    locator.Delete();
    accessPolicy.Delete();

    return asset;
}

</code></pre>

在 Main 方法中，在 **\_context = new CloudMediaContext(_accountName, _accountKey);** 行後面加入方法的呼叫。 

	IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, _singleInputFilePath)

<h2><a id="Step4"></a>將伺服器上的資產編碼並下載輸出資產</h2>

在 [媒體服務] 中，您可以建立作業，以數種方式處理媒體內容：編碼、加密、執行格式轉換等等。媒體服務作業通常包含一或多項工作來指定工作處理的細節。在本節中，您將建立基本的編碼工作，然後透過一項作業使用 Azure Media Encoder 來執行此工作。此工作使用預設字串來指定要執行的編碼類型。若要查看可用的編碼值，請參閱 [Azure Media Encoder 的工作預設字串](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj129582.aspx) 。媒體服務支援與 Microsoft Expression Encoder 相同的媒體檔案輸入和輸出格式。如需支援的格式清單，請參閱 [Azure Media Encoder 支援的轉碼器與檔案類型](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh973634.aspx)。

<ol>
<li>
將下列 <strong>CreateEncodingJob</strong> 方法定義加入至類別。此方法示範如何完成編碼作業的一些必要工作：
<ul>
<li>
Declare a new job.
</li>
<li>
宣告媒體處理器來處理作業。媒體處理器是一種負責處理編碼、加密、格式轉換和其他相關處理作業的元件。有數類可用的媒體處理器 (您可以使用 _context.MediaProcessors 逐一查看全部)。本逐步解說稍後所顯示的 GetLatestMediaProcessorByName 方法會傳回 Azure Media Encoder 處理器。
</li>
<li>
宣告新的工作。每個作業有一或多項工作。請注意，您需要在工作中傳入易記名稱、媒體處理器執行個體、工作組態字串和工作建立選項。組態字串指定編碼設定。此範例使用 <strong>H264 Broadband 720p</strong> 設定。此預設字串會產生單一 MP4 檔案。如需此預設字串和其他預設字串的詳細資訊，請參閱 <a href="http://msdn.microsoft.com/library/windowsazure/jj129582.aspx">Azure Media Encoder 的工作預設字串</a>.
</li>
<li>
將輸入資產加入至工作。在此範例中，輸入資產是您在上一節建立的資產。
</li>
<li>
將輸出資產加入至工作。若為輸出資產，請指定易記名稱、布林值 (用來表示是否在完成作業後將輸出儲存至伺服器) 及 <strong>AssetCreationOptions.None</strong> 值 (表示不加密用以儲存和傳輸的輸出)。 
</li>
<li>
提交作業。<br/>
提交作業是編碼作業所需的最後一步。
</li>
</ul>
此方法也會示範如何執行其他實用 (但選擇性) 的工作，例如追蹤作業進度和存取編碼作業所建立的資產。
<pre><code>
static IJob CreateEncodingJob(IAsset asset, string inputMediaFilePath, string outputFolder)
{
    // Declare a new job.
    IJob job = _context.Jobs.Create("My encoding job");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

    // Create a task with the encoding details, using a string preset.
    ITask task = job.Tasks.AddNew("My encoding task",
        processor,
        "H264 Broadband 720p",
        Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

    // Specify the input asset to be encoded.
    task.InputAssets.Add(asset);
    // Add an output asset to contain the results of the job. 
    // This output is specified as AssetCreationOptions.None, which 
    // means the output asset is not encrypted. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);
    // Use the following event handler to check job progress.  
    job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(StateChanged);

    // Launch the job.
    job.Submit();

    // Optionally log job details. This displays basic job details
    // to the console and saves them to a JobDetails-{JobId}.txt file 
    // in your output folder.
    LogJobDetails(job.Id);

    // Check job execution and wait for job to finish. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();

    // **********
    // Optional code.  Code after this point is not required for 
    // an encoding job, but shows how to access the assets that 
    // are the output of a job, either by creating URLs to the 
    // asset on the server, or by downloading. 
    // **********

    // Get an updated job reference.
    job = GetJob(job.Id);

    // If job state is Error the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
        return job;
    }

    // Get a reference to the output asset from the job.
    IAsset outputAsset = job.OutputMediaAssets[0];
    IAccessPolicy policy = null;
    ILocator locator = null;

    // Declare an access policy for permissions on the asset. 
    // You can call an async or sync create method. 
    policy =
        _context.AccessPolicies.Create("My 30 days readonly policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

    // Create a SAS locator to enable direct access to the asset 
    // in blob storage. You can call a sync or async create method.  
    // You can set the optional startTime param as 5 minutes 
    // earlier than Now to compensate for differences in time  
    // between the client and server clocks. 

    locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset,
        policy,
        DateTime.UtcNow.AddMinutes(-5));

    // Build a list of SAS URLs to each file in the asset. 
    List<String> sasUrlList = GetAssetSasUrlList(outputAsset, locator);

    // Write the URL list to a local file. You can use the saved 
    // SAS URLs to browse directly to the files in the asset.
    if (sasUrlList != null)
    {
        string outFilePath = Path.GetFullPath(outputFolder + @"\" + "FileSasUrlList.txt");
        StringBuilder fileList = new StringBuilder();
        foreach (string url in sasUrlList)
        {
            fileList.AppendLine(url);
            fileList.AppendLine();
        }
        WriteToFile(outFilePath, fileList.ToString());

        // Optionally download the output to the local machine.
        DownloadAssetToLocal(job.Id, outputFolder);
    }

    
    return job;
}

</code></pre>
</li>
<li>
新增呼叫至 <strong>CreateEncodingJob</strong> 方法，位於 <strong>Main</strong> 方法中，在先前新增的行後面。
<pre><code>
CreateEncodingJob(asset, _singleInputFilePath, _outputFilesFolder);
</code></pre>
</li>
<li>
將下列協助程式方法加入至類別。需要有這些方法來支援 <strong>CreateEncodingJob</strong> 方法的定義。以下是協助程式方法的摘要。
<ul>
<li>
Auch die Eigenschaften <strong>GetLatestMediaProcessorByName</strong> 方法傳回適當的媒體處理器來處理編碼、加密或其他相關的處理工作。您可以使用想要建立的處理器的適當字串名稱，建立媒體處理器。在此方法的 mediaProcessor 參數中可傳入的字串包括： <strong>Azure Media Encoder</strong>、 <strong>Windows Azure Media Packager</strong>、 <strong>Windows Azure Media Encryptor</strong>、 <strong>Storage Decryption</strong>。
<pre><code>
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    // The possible strings that can be passed into the 
    // method for the mediaProcessor parameter:
    //   Azure Media Encoder
    //   Windows Azure Media Packager
    //   Windows Azure Media Encryptor
    //   Storage Decryption

    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

</code></pre>
</li>
<li>
執行作業時，您通常需要設法追蹤作業進度。下列程式碼定義 StateChanged 事件處理常式。此事件處理常式可追蹤作業進度，並根據狀態來提供更新的狀態。程式碼也定義 LogJobStop 方法。此協助程式方法會記錄錯誤詳細資料。

<pre><code>
private static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);

    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("********************");
            Console.WriteLine("Job is finished.");
            Console.WriteLine("Please wait while local tasks or downloads complete...");
            Console.WriteLine("********************");
            Console.WriteLine();
            Console.WriteLine();
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
        case JobState.Error:
            // Cast sender as a job.
            IJob job = (IJob)sender;
            // Display or log error details as needed.
            LogJobStop(job.Id);
            break;
        default:
            break;
    }
}

private static void LogJobStop(string jobId)
{
    StringBuilder builder = new StringBuilder();
    IJob job = GetJob(jobId);

    builder.AppendLine("\nThe job stopped due to cancellation or an error.");
    builder.AppendLine("***************************");
    builder.AppendLine("Job ID: " + job.Id);
    builder.AppendLine("Job Name: " + job.Name);
    builder.AppendLine("Job State: " + job.State.ToString());
    builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
    builder.AppendLine("Media Services account name: " + _accountName);
    // Log job errors if they exist.  
    if (job.State == JobState.Error)
    {
        builder.Append("Error Details: \n");
        foreach (ITask task in job.Tasks)
        {
            foreach (ErrorDetail detail in task.ErrorDetails)
            {
                builder.AppendLine("  Task Id: " + task.Id);
                builder.AppendLine("    Error Code: " + detail.Code);
                builder.AppendLine("    Error Message: " + detail.Message + "\n");
            }
        }
    }
    builder.AppendLine("***************************\n");
    // Write the output to a local file and to the console. The template 
    // for an error output file is:  JobStop-{JobId}.txt
    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
    WriteToFile(outputFile, builder.ToString());
    Console.Write(builder.ToString());
}

private static void LogJobDetails(string jobId)
{
    StringBuilder builder = new StringBuilder();
    IJob job = GetJob(jobId);

    builder.AppendLine("\nJob ID: " + job.Id);
    builder.AppendLine("Job Name: " + job.Name);
    builder.AppendLine("Job submitted (client UTC time): " + DateTime.UtcNow.ToString());
    builder.AppendLine("Media Services account name: " + _accountName);

    // Write the output to a local file and to the console. The template 
    // for an error output file is:  JobDetails-{JobId}.txt
    string outputFile = _outputFilesFolder + @"\JobDetails-" + JobIdAsFileName(job.Id) + ".txt";
    WriteToFile(outputFile, builder.ToString());
    Console.Write(builder.ToString());
}
        
private static string JobIdAsFileName(string jobID)
{
    return jobID.Replace(":", "_");
}
</code></pre>
</li>
<li>
WriteToFile 方法將檔案寫入指定的輸出資料夾。
<pre><code>
static void WriteToFile(string outFilePath, string fileContent)
{
    StreamWriter sr = File.CreateText(outFilePath);
    sr.Write(fileContent);
    sr.Close();
}

</code></pre>
</li>
<li>
在服務媒體中將資產編碼之後，您可以存取從編碼作業產生的輸出資產。此逐步解說顯示兩種方法來存取編碼作業的輸出：
<ul>
<li>
對伺服器上的資產建立 SAS URL。 
</li>
<li>
從伺服器下載輸出資產。
</li>
</ul>
GetAssetSasUrlList 方法對資產中的所有檔案建立 SAS URL 清單。 
<pre><code>
static List<String> GetAssetSasUrlList(IAsset asset, ILocator locator)
{
    // Declare a list to contain all the SAS URLs.
    List<String> fileSasUrlList = new List<String>();

    // If the asset has files, build a list of URLs to 
    // each file in the asset and return. 
    foreach (IAssetFile file in asset.AssetFiles)
    {
        string sasUrl = BuildFileSasUrl(file, locator);
        fileSasUrlList.Add(sasUrl);
    }

    // Return the list of SAS URLs.
    return fileSasUrlList;
}

// Create and return a SAS URL to a single file in an asset. 
static string BuildFileSasUrl(IAssetFile file, ILocator locator)
{
    // Take the locator path, add the file name, and build 
    // a full SAS URL to access this file. This is the only 
    // code required to build the full URL.
    var uriBuilder = new UriBuilder(locator.Path);
    uriBuilder.Path += "/" + file.Name;

    // Optional:  print the locator.Path to the asset, and 
    // the full SAS URL to the file
    Console.WriteLine("Locator path: ");
    Console.WriteLine(locator.Path);
    Console.WriteLine();
    Console.WriteLine("Full URL to file: ");
    Console.WriteLine(uriBuilder.Uri.AbsoluteUri);
    Console.WriteLine();


    //Return the SAS URL.
    return uriBuilder.Uri.AbsoluteUri;
}

</code></pre>
</li>
<li>
Auch die Eigenschaften <strong>DownloadAssetToLocal</strong> 方法將資產中的每個檔案下載至本機資料夾。在此範例中，由於資產是以一個輸入媒體檔案建立，因此輸出資產檔案集合包含兩個檔案：加密的媒體檔案 (.mp4 檔案)，和包含資產相關中繼資料的 .xml 檔案。此方法會下載這兩個檔案。
<pre><code>
static IAsset DownloadAssetToLocal(string jobId, string outputFolder)
{
    // This method illustrates how to download a single asset. 
    // However, you can iterate through the OutputAssets
    // collection, and download all assets if there are many. 

    // Get a reference to the job. 
    IJob job = GetJob(jobId);
    // Get a reference to the first output asset. If there were multiple 
    // output media assets you could iterate and handle each one.
    IAsset outputAsset = job.OutputMediaAssets[0];

    IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
    ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);
    BlobTransferClient blobTransfer = new BlobTransferClient
    {
        NumberOfConcurrentTransfers = 10,
        ParallelTransferThreadCount = 10
    };

    var downloadTasks = new List<Task>();
    foreach (IAssetFile outputFile in outputAsset.AssetFiles)
    {
        // Use the following event handler to check download progress.
        outputFile.DownloadProgressChanged += DownloadProgress;

        string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

        Console.WriteLine("File download path:  " + localDownloadPath);

        downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

        outputFile.DownloadProgressChanged -= DownloadProgress;
    }

    Task.WaitAll(downloadTasks.ToArray());

    return outputAsset;
}

static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
{
    Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
}
</code></pre>
</li>
<li>
GetJob 和 GetAsset 協助程式方法會根據指定的識別碼，以查詢並傳回作業物件和資產物件的參考。您可以使用類似的 LINQ 查詢來傳回伺服器上其他媒體服務物件的參考。
<pre><code>
static IJob GetJob(string jobId)
{
    // Use a Linq select query to get an updated 
    // reference by Id. 
    var jobInstance =
        from j in _context.Jobs
        where j.Id == jobId
        select j;
    // Return the job reference as an Ijob. 
    IJob job = jobInstance.FirstOrDefault();

    return job;
}
static IAsset GetAsset(string assetId)
{
    // Use a LINQ Select query to get an asset.
    var assetInstance =
        from a in _context.Assets
        where a.Id == assetId
        select a;
    // Reference the asset as an IAsset.
    IAsset asset = assetInstance.FirstOrDefault();

    return asset;
}
</code></pre>
</li>
</ul>
</li>
</ol>

## 測試程式碼
執行程式 (按 F5)。主控台會顯示如下的輸出：

<pre><code>
Asset name: UploadSingleFile_11/14/2012 10:09:11 PM
Time created: 11/14/2012 12:00:00 AM
Created assetFile interview2.wmv
Upload interview2.wmv
Done uploading of interview2.wmv using Upload()

Job ID: nb:jid:UUID:ea8d5a66-86b8-9b4d-84bc-6d406259acb8
Job Name: My encoding job
Job submitted (client UTC time): 11/14/2012 10:09:39 PM
Media Services account name: Add-Media-Services-Account-Name
Media Services account location: Add-Media-Services-account-location-name

Job(My encoding job) state: Queued.
Please wait...

Job(My encoding job) state: Processing.
Please wait...

********************
Job(My encoding job) is finished.
Please wait while local tasks or downloads complete...
********************

Locator path:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
xylq3oESc%3D

Full URL to file:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62/interview2.mp4?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3
A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2F
BxERnav8Jb6hL7fxylq3oESc%3D

Locator path:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
xylq3oESc%3D

Full URL to file:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62/interview2_metadata.xml?st=2012-11-14T22%3A07%3A01Z&se=2012-1
1-14T23%3A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8F
od3NsV%2FBxERnav8Jb6hL7fxylq3oESc%3D

Downloads are in progress, please wait.

File download path:  C:\supportFiles\outputfiles\interview2.mp4
1.70952185308162 % download progress.
3.68508804454907 % download progress.
6.48870388360293 % download progress.
6.83808741232649 % download progress.
. . . 
99.0763740574049 % download progress.
99.1522674787341 % download progress.
100 % download progress.
File download path:  C:\supportFiles\outputfiles\interview2_metadata.xml
100 % download progress.

</code></pre>

1. 執行此應用程式的結果如下：

2. 將 .wmv 檔案上傳至媒體服務。 

3. 然後，使用 **Azure Media Encoder** 的 **H264 Broadband 720p** 預設字串將檔案編碼。

4. 在 \supportFiles\outputFiles 資料夾中建立 FileSasUrlList.txt 檔案。此檔案包含編碼資產的 URL。 

	若要播放媒體檔案，請從文字檔中複製資產的 URL，再將 URL 貼到瀏覽器中。 

5. 將 .mp4 媒體檔案和 _metadata.xml 檔案下載至 outputFiles 資料夾。

>[WACOM.NOTE]
> 在媒體服務物件模型中，資產是一種媒體服務內容集合物件，代表一個以上的檔案。定位器路徑提供的 Azure Blob URL 是此資產在 Azure 儲存體中的基本路徑。若要存取資產內的特定檔案，請將檔案名稱加入至基本定位器路徑。

<h2>後續步驟</h2>
此逐步解說已示範建立簡單的媒體服務應用程式所需的一連串程式設計工作。您已學到基本的媒體服務程式設計工作，包括取得伺服器內容、建立資產、將資產編碼，以及下載或存取伺服器上的資產。關於後續步驟和更進階的開發工作，請參閱：

- <a href="http://azure.microsoft.com/zh-tw/develop/media-services/resources/">如何使用媒體服務</a>
- <a href="http://msdn.microsoft.com/zh-tw/library/windowsazure/hh973618.aspx">使用 Media Services REST API 建立應用程式</a>

<h2>其他資源</h2>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - 立即在線上取得您的影片！</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - 動態封裝和行動裝置</a>

<!-- Anchors. -->
[開始使用行動服務]:#getting-started
[建立新的行動服務]:#create-new-service
[定義行動服務執行個體]:#define-mobile-service-instance
[後續步驟]:#next-steps


<!--HONumber=35.1-->
