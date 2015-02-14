<properties 
	pageTitle="如何為媒體服務的資產編碼 - Azure" 
	description="了解如何使用 Azure Media Encoder 為媒體服務上的媒體內容編碼。程式碼範例以 C# 撰寫，並使用 Media Services SDK for .NET。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>


# 作法：為資產編碼
本文是介紹 Azure 媒體服務程式設計的系列文章之一。上一個主題是＜[作法：取得媒體處理器](../media-services-get-media-processor/)。

對於伺服器上的媒體內容，您可以使用 Azure 媒體編碼器，以多種媒體編碼和格式為內容編碼。您也可以使用媒體服務合作夥伴所提供的編碼器；第三方編碼器可透過 [Azure Marketplace][] 取得。您可以使用[編碼器預設][]字串或組態檔，來指定編碼工作的詳細資料。 

## 編碼為 MP4 調適性位元速率集
建議您將 mezzanine 檔案編碼為 MP4 調適性位元速率集，然後使用動態封裝來傳遞內容。如需詳細資訊，請參閱[使用 Media Services SDK for .NET 建立編碼工作](http://msdn.microsoft.com/zh-tw/library/azure/dn282273.aspx)、[動態封裝](http://msdn.microsoft.com/zh-tw/library/azure/jj889436.aspx)和[傳遞內容](http://msdn.microsoft.com/zh-tw/library/azure/hh973618.aspx)。

## 編碼為 MP4
下列方法會上傳單一資產，並建立會使用 "H264 Broadband 720p" 預設將資產編碼為 MP4 的工作，而建立使用 H264 編碼、解析度為 720p 的單一 MP4：
<pre><code>
	static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder)
	{
    	//Create an encrypted asset and upload to storage.
		IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
			inputMediaFilePath);

		// Declare a new job.

    	IJob job = _context.Jobs.Create("My encoding job");
	
		// Get a reference to the Azure Media Encoder
		IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
    
		// Create a task with the encoding details, using a string preset.
    	ITask task = job.Tasks.AddNew("My encoding task",
        	processor,
	        "H264 Broadband 720p",
        	_protectedConfig);
    
		// Specify the input asset to be encoded.
    	task.InputAssets.Add(asset);
    
		// Add an output asset to contain the results of the job. 
    	// This output is specified as AssetCreationOptions.None, which 
    	// means the output asset is in the clear (unencrypted). 
    	task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
    
		// Use the following event handler to check job progress.  
    	job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);
    
		// Launch the job.
    	job.Submit();
    
		// Optionally log job details. This displays basic job details
    	// to the console and saves them to a JobDetails-JobId.txt file 
    	// in your output folder.
    	LogJobDetails(job.Id);
    
		// Check job execution and wait for job to finish. 
    	Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    	progressJobTask.Wait();
    
		// If job state is Error, the event handling 
    	// method for job progress should log errors.  Here we check 
    	// for error state and exit if needed.
    	if (job.State == JobState.Error)
    	{
	        Console.WriteLine("\nExiting method due to job error.");
        	return job;
    	}
    
		// Perform other tasks. For example, access the assets that are the output of a job, 
    	// either by creating URLs to the asset on the server, or by downloading. 
    	return job;
	}

	private static void StateChanged(object sender, JobStateChangedEventArgs e)
	{
		Console.WriteLine("Job state changed event:");
	    Console.WriteLine("  Previous state: " + e.PreviousState);
	    Console.WriteLine("  Current state: " + e.CurrentState);
	    switch (e.CurrentState)
	    {
        	case JobState.Finished:
           	Console.WriteLine();
           	Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
</code></pre>
<h2>編碼為 Smooth Streaming</h2>
如果您要將視訊編碼為 Smooth Streaming，您有兩種選擇：
<ul>
<li> 直接編碼為 Smooth Streaming </li>
<li> 先編碼為 MP4，再轉換為 Smooth Streaming</li>
</ul>

若要直接編碼為 Smooth Streaming，請使用前述程式碼，但應使用其中一個 Smooth Streaming 編碼器預設。如需編碼器預設的完整清單，請參閱 [Azure 媒體編碼器的工作預設字串](http://msdn.microsoft.com/zh-tw/library/jj129582.aspx)。 

若要將 MP4 轉換為 Smooth Streaming，請使用 Azure Media Packager。Azure Media Packager 不支援字串預設，因此您必須以 XML 指定組態選項。將 MP4 轉換為 Smooth Streaming 時所需的 XML，可在 [Azure Media Packager 的工作預設][]中找到。複製此 XML，並將其貼到您的專案中名為 MediaPackager_MP4ToSmooth.xml 的檔案上。下列程式碼說明如何將 MP4 資產轉換為 Smooth Streaming。下列方法會取用現有資產並加以轉換。 
<pre><code>
private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath)
 {
	// Declare a new job to contain the tasks
    IJob job = _context.Jobs.Create("Convert to Smooth Streaming job");
    // Set up the first Task to convert from MP4 to Smooth Streaming. 
    // Read in task configuration XML
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));
    // Get a media packager reference
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");
    // Create a task with the conversion details, using the configuration data
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
           processor,
           configMp4ToSmooth,
           TaskOptions.None);
    // Specify the input asset to be converted.
    task.InputAssets.Add(assetToConvert);
    // Add an output asset to contain the results of the job.
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    // Use the following event handler to check job progress. 
	// The StateChange method is the same as the one in the previous sample
    job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);
    // Launch the job.
    job.Submit();
    // Check job execution and wait for job to finish. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();
    // Get a refreshed job reference after waiting on a thread.
    job = GetJob(job.Id);
    // Check for errors
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
    }
    return job;
}
</code></pre>

如需處理資產的詳細資訊，請參閱：
<ul>
<li><a href="http://msdn.microsoft.com/zh-tw/library/jj129580.aspx">使用 Media Services SDK for .NET 處理資產</a></li>
<li><a href="http://msdn.microsoft.com/zh-tw/library/jj129574.aspx">使用媒體服務 REST API 處理資產</a></li>
</ul>

## 後續步驟
您已了解如何建立為資產編碼的工作，現在請移至[如何使用媒體服務檢查工作進度](../media-services-check-job-progress/)主題。

[Azure Marketplace]: https://datamarket.azure.com/
[編碼器預設]: http://msdn.microsoft.com/zh-tw/library/dn619392.aspx
[做法：取得媒體處理器執行個體]:http://go.microsoft.com/fwlink/?LinkId=301732
[做法：上傳加密的資產]:http://go.microsoft.com/fwlink/?LinkId=301733
[做法：透過下載來傳遞資產]:http://go.microsoft.com/fwlink/?LinkId=301734
[如何檢查工作進度]:http://go.microsoft.com/fwlink/?LinkId=301737
[Azure Media Packager 的工作預設]:http://msdn.microsoft.com/zh-tw/library/windowsazure/hh973635.aspx
<!--HONumber=42-->
