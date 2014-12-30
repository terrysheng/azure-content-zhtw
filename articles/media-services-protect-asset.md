<properties urlDisplayName="Encrypt Assets in Media Services" pageTitle="如何為媒體服務中的資產加密 - Azure" metaKeywords="" description="Learn how to use Microsoft PlayReady Protection to encrypt an asset in Media Services. Code samples are written in C# and use the Media Services SDK for .NET. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Protect an Asset with PlayReady Protection" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





<h1><a name="playready"></a>作法：使用 PlayReady 保護來保護資產</h1>

本文是介紹 Azure 媒體服務程式設計的系列文章之一。上一個主題是＜[作法：檢查工作進度](../media-services-check-job-progress/)。

在 Azure 媒體服務中，您可以提交一個與 Microsoft PlayReady 保護整合的工作來加密資產。本節中的程式碼會取得輸入資料夾中的數個串流檔案、建立一項工作並使用 PlayReady 保護將這些檔案加密。 

下列範例示範如何建立一個簡單的工作來提供 PlayReady 保護。

   1. 擷取組態資料。您可以從 [Azure 媒體編碼程式的工作預設設定](http://msdn.microsoft.com/zh-tw/library/hh973610.aspx) 主題取得範例組態檔。
   2. 上傳 MP4 輸入檔
   3. 將 MP4 檔案轉換成 Smooth Streaming 資產
   4. 使用 PlayReady 將資產加密
   5. 提交工作

下列範例程式碼顯示如何實作這些步驟：

<pre><code>
private static IJob CreatePlayReadyProtectionJob(string inputMediaFilePath, string primaryFilePath, string configFilePath)
{
    // Create a storage-encrypted asset and upload the MP4. 
    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);

    // Declare a new job to contain the tasks.
    IJob job = _context.Jobs.Create("My PlayReady Protection job");

    // Set up the first task. 

    // Read the task configuration data into a string. 
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));

    // Get a media processor instance
    IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Packager");

    // Create a task with the conversion details, using the configuration data 
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
        processor,
        configMp4ToSmooth,
        _clearConfig);

    // Specify the input asset to be converted.
    task.InputAssets.Add(asset);

    // Add an output asset to contain the results of the job. We do not need 
    // to persist the output asset to storage, so set the shouldPersistOutputOnCompletion
    // parameter to false. 
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    IAsset smoothOutputAsset = task.OutputAssets[0];

    // Set up the encryption task. 

    // Read the configuration data into a string. 
    string configPlayReady = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaEncryptor_PlayReadyProtection.xml"));

    // Get a media processor instance
    IMediaProcessor playreadyProcessor = GetLatestMediaProcessorByName("Windows Azure Media Encryptor");

    // Create a second task, specifying a task name, the media processor, and configuration
    ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
        playreadyProcessor,
        configPlayReady,
        _protectedConfig);

    // Add the input asset, which is the smooth streaming output asset from the first task. 
    playreadyTask.InputAssets.Add(smoothOutputAsset);

    // Add an output asset to contain the results of the job. Persist the output by setting 
    // the shouldPersistOutputOnCompletion param to true.
    playreadyTask.OutputAssets.AddNew("PlayReady protected output asset",
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

</code></pre>
Weitere Informationen zum PlayReady-Schutz finden Sie unter:
<ul>
<li><a href="http://msdn.microsoft.com/zh-tw/library/dn189154.aspx">以 Microsoft PlayReady 保護資產</a></li>
<li><a href="http://www.microsoft.com/PlayReady/">Microsoft PlayReady (英文)</a></li>
</ul>

<h2>後續步驟</h2>
您已了解如何使用媒體服務來保護資產，現在可移至[如何管理資產](../media-services-manage-assets/) 主題。

<!--HONumber=35_1-->
