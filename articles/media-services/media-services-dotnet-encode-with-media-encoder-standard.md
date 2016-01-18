<properties 
	pageTitle="如何使用 Media Encoder Standard 為資產編碼" 
	description="本主題說明如何使用 .NET 來使用 Media Encoder Standard 為資產編碼。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,anilmur" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/21/2015"  
	ms.author="juliako"/>


#如何使用 Media Encoder Standard 為資產編碼

編碼工作是媒體服務中最常見的處理作業。您建立編碼工作以將媒體檔案從一種編碼轉換成另一種編碼。編碼時，您可以使用媒體服務內建的 Media Encoder。您也可以使用媒體服務合作夥伴提供的編碼器；第三方編碼器可透過 Azure Marketplace 取得。

本主題說明如何使用 .NET 透過 Media Encoder Standard (MES) 將您的資產編碼。Media Encoder Standard 使用[這裡](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)描述的其中一個編碼器預設值進行設定。

建議一律將夾層檔編碼為調適性位元速率 MP4 集，然後使用[動態封裝](media-services-dynamic-packaging-overview.md)將該集合轉換為所要的格式。若要利用動態封裝，您必須先取得至少一個串流端點的隨選串流單位，您打算從中傳遞您的內容。如需詳細資訊，請參閱[如何調整媒體服務](media-services-manage-origins.md#scale_streaming_endpoints)。

如果您的輸出資產是儲存體加密，必須設定資產傳遞原則。如需詳細資訊，請參閱[設定資產傳遞原則](media-services-dotnet-configure-asset-delivery-policy.md)。

>[AZURE.NOTE]MES 會產生一個輸出檔案，其名稱包含輸入檔案名稱的前 32 個字元。名稱是以預設檔案中指定的名稱為基礎。例如，"FileName": "{Basename}\_{Index}{Extension}"。{Basename} 會由輸入檔案名稱的前 32 個字元取代。

###MES 格式

[格式和轉碼器](media-services-media-encoder-standard-formats.md)

###MES 預設值

Media Encoder Standard 使用[這裡](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)描述的其中一個編碼器預設值進行設定。

###MES 輸入和輸出中繼資料

[這裡](http://msdn.microsoft.com/library/azure/dn783120.aspx)說明編碼器輸入中繼資料。

[這裡](http://msdn.microsoft.com/library/azure/dn783217.aspx)說明編碼器輸出中繼資料。


##下載範例

從[這裡](http://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/)取得並執行範例。

##範例

下列程式碼範例使用媒體服務 .NET SDK 執行下列工作：

- 建立編碼工作。
- 取得對 Media Encoder Standard 編碼器的參考
- 指定要使用「H264 多重位元速率 720p」預設值。您可以在[這裡](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)看到所有預設值。您也可以在[這裡](https://msdn.microsoft.com/library/mt269962.aspx)的主題檢查這些預設值必須符合的結構描述。
- 將單一編碼工作加入工作。 
- 指定要編碼的輸入資產。
- 建立將包含已編碼資產的輸出資產。
- 加入事件處理常式來檢查工作進度。
- 提交作業。
		
		static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
		{
		    // Declare a new job.
		    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		    // Get a media processor reference, and pass to it the name of the 
		    // processor to use for the specific task.
		    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		

		    // Create a task with the encoding details, using a string preset.
		    // In this case "H264 Multiple Bitrate 720p" preset is used.
		    ITask task = job.Tasks.AddNew("My encoding task",
		        processor,
		        "H264 Multiple Bitrate 720p",
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


##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##另請參閱 

[如何使用 Media Encoder Standard 搭配 .NET 產生縮圖](media-services-dotnet-generate-thumbnail-with-mes.md) [媒體服務編碼概觀](media-services-encode-asset.md)

<!---HONumber=AcomDC_0107_2016-->