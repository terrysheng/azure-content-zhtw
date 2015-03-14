<properties 
	pageTitle="如何使用 Azure Media Encoder 為資產編碼" 
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
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="juliako"/>


# 如何使用 Azure Media Encoder 為資產編碼

這篇文章屬於[要求工作流程上的媒體服務視訊](../media-services-video-on-demand-workflow)系列。 

## 概觀
若要透過網際網路傳遞數位視訊，您必須壓縮媒體。數位視訊檔案十分龐大，而且可能太大而無法透過網際網路傳遞，或者您客戶的裝置無法正確顯示。編碼是一個程序，可壓縮視訊和音訊，以讓您的客戶可以檢視您的媒體。

編碼工作是媒體服務中的其中一個最常見處理作業。您可以建立編碼工作，以將媒體檔案從某種編碼轉換為另一種編碼。編碼時，您可以使用媒體服務內建 Media Encoder。您也可以使用媒體服務合作夥伴所提供的編碼器；第三方編碼器可透過 Azure Marketplace 取得。使用針對編碼器所定義的預設字串，或使用預設組態檔，即可指定編碼工作的詳細資料。若要查看可用的預設類型，請參閱＜Azure 媒體服務的工作預設＞。如果您使用第三方編碼器，則應該[驗證檔案](https://msdn.microsoft.com/zh-tw/library/azure/dn750842.aspx)。

建議一律將夾層檔編碼為調適性位元速率 MP4 集，然後使用[動態封裝](https://msdn.microsoft.com/zh-tw/library/azure/jj889436.aspx)將該集合轉換為所要的格式。


## 建立具有單一編碼工作 (Task) 的工作 (Job) 

使用 Azure Media Encoder 編碼時，您可以使用[這裡](https://msdn.microsoft.com/zh-tw/library/azure/dn619389.aspx)指定的工作組態預設。

### 使用 Media Services SDK for .NET  

下列 **EncodeToAdaptiveBitrateMP4Set** 方法會建立編碼工作 (Job)，並將單一編碼工作 (Task) 新增至工作 (Job)。此工作 (Task) 會使用 "Azure Media Encoder" 編碼為 "H264 Adaptive Bitrate MP4 Set 720p"。 

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
    {
        var encodingPreset = "H264 Adaptive Bitrate MP4 Set 720p";

        IJob job = _context.Jobs.Create(String.Format("Encoding {0} into to {1}",
                                inputAsset.Name,
                                encodingPreset));

        var mediaProcessors = GetLatestMediaProcessorByName("Azure Media Encoder");

        ITask encodeTask = job.Tasks.AddNew("Encoding", mediaProcessors, encodingPreset, TaskOptions.None);
        
        encodeTask.InputAssets.Add(inputAsset);

        // Specify the storage-encrypted output asset.
        encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 
            AssetCreationOptions.StorageEncrypted);


        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
        job.Submit();
        job.GetExecutionProgressTask(CancellationToken.None).Wait();

        return job.OutputMediaAssets[0];
    }

    private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
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
                break;
            default:
                break;
        }
    }

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
           ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

### 使用 Media Services SDK for .NET 延伸模組

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
    {
        // 1. Prepare a job with a single task to transcode the specified mezzanine asset
        //    into a multi-bitrate asset.
        IJob job = _context.Jobs.CreateWithSingleTask(
            MediaProcessorNames.AzureMediaEncoder,
            MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
            asset,
            "Adaptive Bitrate MP4",
            AssetCreationOptions.None);

        Console.WriteLine("Submitting transcoding job...");

        // 2. Submit the job and wait until it is completed.
        job.Submit();
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    } 

## 建立具有鏈結工作 (Task) 的工作 (Job) 

在許多應用程式案例中，開發人員都想要建立一連串的處理工作 (Task)。在媒體服務中，您可以建立一連串的鏈結工作 (Task)。每項工作 (Task) 都會執行不同的處理步驟，並且可以使用不同的媒體處理器。鏈結工作 (Task) 可以將資產從某個工作 (Task) 傳遞給另一個工作 (Task)，並對資產執行連續的工作 (Task)。不過，工作 (Job) 中所執行的工作 (Task) 不一定要連續執行。建立鏈結工作 (Task) 時，會在單一 **IJob** 物件中建立鏈結 **ITask** 物件。

>[AZURE.NOTE] 目前的限制是每個工作 (Job) 有 30 個工作 (Task)。如果您需要鏈結 30 個以上的工作 (Task)，請建立多個工作 (Job) 來包含這些工作 (Task)。

下列 **CreateChainedTaskEncodingJob** 方法會建立含有兩個鏈結工作 (Task) 的工作 (Job)。因此，此方法會傳回含有兩個輸出資產的工作 (Job)。

	
    public static IJob CreateChainedTaskEncodingJob(IAsset asset)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("My task-chained encoding job");

        // Set up the first task to encode the input file.

        // Get a media processor reference
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
           "H264 Adaptive Bitrate MP4 Set 720p",
            TaskOptions.ProtectedConfiguration);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Specify the storage-encrypted output asset.
        task.OutputAssets.AddNew("My storage-encrypted output asset",
            AssetCreationOptions.StorageEncrypted);

        // Set up the second task to decrypt the encoded output file from 
        // the first task.

        // Get another media processor instance
        IMediaProcessor decryptProcessor = GetLatestMediaProcessorByName("Storage Decryption");

        // Declare the decryption task. 
        ITask decryptTask = job.Tasks.AddNew("My decryption task",
            decryptProcessor,
            string.Empty,
            TaskOptions.None);

        // Specify the input asset to be decrypted. This is the output 
        // asset from the first task. 
        decryptTask.InputAssets.Add(task.OutputAssets[0]);

        // Specify an output asset to contain the results of the job. 
        // This should have AssetCreationOptions.None. 
        decryptTask.OutputAssets.AddNew("My decrypted output asset",
            AssetCreationOptions.None);

        // Use the following event handler to check job progress. 
        job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(JobStateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        //return job that contains two output assets.
        return job;
    }


## 後續步驟
現在，您知道如何建立編碼資產的工作 (Job)，請移至[如何使用媒體服務檢查工作進度](../media-services-check-job-progress/)主題。

[Azure Marketplace]: https://datamarket.azure.com/
[編碼器預設]: http://msdn.microsoft.com/library/dn619392.aspx
[做法：取得媒體處理器執行個體]:http://go.microsoft.com/fwlink/?LinkId=301732
[做法：上傳加密的資產]:http://go.microsoft.com/fwlink/?LinkId=301733
[做法：透過下載來傳遞資產]:http://go.microsoft.com/fwlink/?LinkId=301734
[如何檢查工作進度]:http://go.microsoft.com/fwlink/?LinkId=301737
[Azure Media Packager 的工作預設]:http://msdn.microsoft.com/library/windowsazure/hh973635.aspx

<!--HONumber=45--> 
