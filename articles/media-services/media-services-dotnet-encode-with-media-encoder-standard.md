<properties 
	pageTitle="如何使用 Media Encoder Standard 為資產編碼"
	description="本主題說明如何使用 .NET 來使用 Media Encoder Standard 為資產編碼。"
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
	ms.date="08/24/2015"
	ms.author="juliako"/>


#概觀

編碼工作是媒體服務中最常見的處理作業。您建立編碼工作以將媒體檔案從一種編碼轉換成另一種編碼。編碼時，您可以使用媒體服務內建的 Media Encoder。您也可以使用媒體服務合作夥伴提供的編碼器；第三方編碼器可透過 Azure Marketplace 取得。

本主題說明如何使用 .NET 透過 Media Encoder Standard 將您的資產編碼。Media Encoder Standard 使用[這裡](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)描述的其中一個編碼器預設值進行設定。

>[AZURE.NOTE]目前版本的媒體處理器會要求您以整個 XML 或 JSON 字串傳遞作為編碼預設值。支援以具名字串傳遞，例如「H264 Multiple Bitrate 720p」，透過服務更新很快就可以使用。

建議一律將夾層檔編碼為調適性位元速率 MP4 集，然後使用[動態封裝](media-services-dynamic-packaging-overview.md)將該集合轉換為所要的格式。若要利用動態封裝，您必須先取得至少一個串流端點的隨選串流單位，您打算從中傳遞您的內容。如需詳細資訊，請參閱[如何調整媒體服務](media-services-manage-origins.md#scale_streaming_endpoints)。

如果您的輸出資產是儲存體加密，必須設定資產傳遞原則。如需詳細資訊，請參閱[設定資產傳遞原則](media-services-dotnet-configure-asset-delivery-policy.md)。

##範例

下列程式碼範例使用媒體服務 .NET SDK 執行下列工作：

- 建立編碼工作。
- 取得對 Media Encoder Standard 編碼器的參考
- 從[這裡](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)顯示的其中一個預設值，載入預設 XML。
- 將單一編碼工作加入工作。 
- 指定要編碼的輸入資產。
- 建立將包含已編碼資產的輸出資產。
- 加入事件處理常式來檢查工作進度。
- 提交作業。
		
		static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset, string pathToLocalPresetFile)
		{
		    // Declare a new job.
		    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		    // Get a media processor reference, and pass to it the name of the 
		    // processor to use for the specific task.
		    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		
		    // Load the XML (or JSON) from the local file
		    string configuration = File.ReadAllText(pathToLocalPresetFile);
		
		    // Create a task
		    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
		        processor,
		        configuration,
		        TaskOptions.None);
		
		    // Specify the input asset to be encoded.
		    task.InputAssets.Add(asset);
		    // Add an output asset to contain the results of the job. 
		    // This output is specified as AssetCreationOptions.None, which 
		    // means the output asset is not encrypted. 
		    task.OutputAssets.AddNew("Output asset",
		        AssetCreationOptions.None);
		
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

##另請參閱 

[媒體服務編碼概觀](media-services-encode-asset.md)

<!---HONumber=August15_HO9-->