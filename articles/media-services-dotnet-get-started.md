<properties 
	pageTitle="開始使用 Media Services SDK for .NET- Azure" 
	description="本教學課程會引導您執行使用 Media Services SDK for .NET 實作 Video-on-Demand (VoD) 內容傳遞應用程式的步驟。" 
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
	ms.date="01/12/2015" 
	ms.author="juliako"/>


# 使用 Media Services SDK for .NET 傳遞 Video-on-Demand 

[AZURE.INCLUDE [media-services-selector-get-started](../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> 若要完成此教學課程，您需要 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-tw/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">「Azure 免費試用」</a>。

本教學課程會引導您執行使用 Azure Media Services (AMS) SDK for .NET 實作 Video-on-Demand (VoD) 內容傳遞應用程式的步驟。 

本教學課程介紹基本的媒體服務工作流程，以及媒體服務開發最常用的程式設計物件和必要工作。完成本教學課程時，您將能夠串流處理或下載您已上傳、編碼和下載的範例媒體檔案。  

若要實作 VoD 內容傳遞應用程式，您可以使用不同的技術 (例如 .NET、REST 或 Java) 或工具 (Azure 管理入口網站或 Azure 媒體服務總管)，或搭配使用兩者。 

本教學課程將使用 Azure 管理入口網站和 Media Services SDK for .NET 完成下列工作：     


1.  [建立媒體服務帳戶使用入口網站](#create_ams)。
2.  [設定使用入口網站的串流單元](#configure_streaming_units)。
3.  [建立和設定 Visual Studio 專案](#configure_VS)
4.  [使用 .NET 上傳、編碼及傳遞內容](#use_dotnet)
	5.  [連接到媒體服務帳戶](#connect)。
	1.  [建立新資產並上傳視訊檔案](#upload)。
	1.  [將來源檔案編碼成調適性位元速率 MP4 檔案集](#encode)。
	1.  [設定編碼資產的傳遞原則](#configure_delivery_method)。
	2.  [(選擇性) 設定動態內容保護](#configure_content_protection)。 
	1.  [發佈資產及取得串流和漸進式下載 URL](#publish_get_urls)。 
1.  [播放您的內容](#play)。 

## 必要條件
以下是開始使用 Media Services SDK for .NET 進行開發所必須具備的必要條件。

- 作業系統：Windows 7、Windows 2008 R2、Windows 8 或更新版本。
- .NET Framework 4.5 或 .NET Framework 4.0
- Visual Studio 2013、Visual Studio 2012、Visual Studio 2010 SP1 (Professional、Premium、Ultimate 或 Express)。


## <a id="create_ams"></a>使用入口網站建立媒體服務帳戶

1. 在[管理入口網站][]中，依序按一下 [**新增**]、[**媒體服務**] 和 [**快速建立**]。
   
	![媒體服務快速建立](./media/media-services-create-account/wams-QuickCreate.png)

2. 在 [**名稱**] 中，輸入新帳戶的名稱。媒體服務帳戶名稱是全部小寫且不含空格的數字或字母，而且長度是 3 到 24 個字元。 

3. 在 [**區域**] 中，選取將用來儲存您媒體服務帳戶之中繼資料記錄的地理區域。只有可用的媒體服務區域才會出現在下拉式清單中。 

4. 在 [**儲存體帳戶**] 中，選取儲存體帳戶以從媒體服務帳戶提供媒體內容的 Blob 儲存體。您可以選取與媒體服務帳戶相同地理區域中的現有儲存體帳戶，也可以建立新的儲存體帳戶。新的儲存體帳戶會建立於相同的區域中。 

5. 如果您已建立新的儲存體帳戶，請在 [**NEW STORAGE ACCOUNT NAME**] 中輸入儲存體帳戶的名稱。儲存體帳戶名稱的規則會與媒體服務帳戶相同。

6. 按一下表單底部的 [**快速建立**]。

	您可以在視窗底端的訊息區域監視程序的狀態。

	帳戶成功建立後，狀態會變更為 [作用中]。 
	
	在頁面底部，會出現 [**管理金鑰**] 按鈕。當您按一下此按鈕時，將會顯示一個對話方塊，內含媒體服務帳戶名稱以及主要和次要金鑰。您必須要有帳戶名稱和主要金鑰資訊，才能以程式設計方式存取媒體服務帳戶。 

	
	![Media Services Page](./media/media-services-create-account/wams-mediaservices-page.png)

	當您按兩下帳戶名稱時，預設會顯示 [快速入門] 頁面。此頁面可讓您執行一些在入口網站的其他頁面也可以執行的管理工作。例如，您可以從此頁面上傳視訊檔案，也可以從 [內容] 頁面上傳視訊檔案。

	 
## <a id="configure_streaming_units"></a>使用入口網站設定串流單元

使用 Azure 媒體服務時最常見的案例之一，是將調適性位元速率串流傳遞至您的用戶端。透過調適性位元速率串流，用戶端可以在視訊顯示時，根據目前的網路頻寬、CPU 使用率和其他因素，切換至較高或較低的位元速率串流。媒體服務支援下列調適性位元速率串流技術：HTTP 即時資料流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/Access 授權)。 

媒體服務所提供的動態封裝，可讓您以媒體服務支援的串流格式 (MPEG DASH、HLS、Smooth Streaming、HDS) 傳遞調適性位元速率 MP4 或 Smooth Streaming 編碼內容，而無須重新封裝成這些串流格式。 

若要使用動態封裝，您必須執行下列動作：

- 將您的 mezzanine (來源) 檔案編碼或轉換成一組調適性位元速率 MP4 檔案或調適性位元速率 Smooth Streaming 的檔案 (本教學課程稍後將示範編碼步驟)，  
- 針對您要傳遞內容的串流端點，取得至少一個隨選串流單元。

透過動態封裝，您只需要儲存及支付一種儲存格式的檔案，媒體服務將會根據用戶端的要求建立及提供適當的回應。 

請注意，除了能夠使用動態封裝功能以外，隨選串流保留單元也為您提供可購買的專用流出容量 (以 200 Mbps 為增量來購買)。依預設，隨選串流是以共用執行個體模型來設定，可與其他所有使用者共用伺服器資源 (例如，運算、流出容量等)。若要改進隨選串流輸送量，建議購買隨選串流保留單元。


若要變更隨選串流保留單元的數目，請執行下列動作：

1. 在[管理入口網站](https://manage.windowsazure.com/)中，按一下 [**媒體服務**]。接著，按一下媒體服務的名稱。

2. 選取 [串流端點] 頁面。然後，按一下您要修改的串流端點。

3. 若要指定串流單元數目，請選取 [調整] 索引標籤，然後移動 [**reserved capacity**] 滑動軸。

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. 按 [儲存] 以儲存您的變更。

	任何新的隨選串流單元大約需要 20 分鐘才能配置完成。 

	 
	>[AZURE.NOTE] 目前，從隨選串流單元的任何正值回到無，可能會使隨選串流停用長達一小時。
	>
	> 計算成本時會使用 24 小時內指定的最大單元數。如需定價詳細資料的相關資訊，請參閱[媒體服務定價詳細資料](http://go.microsoft.com/fwlink/?LinkId=275107)。



## <a id="configure_VS"></a>建立和設定 Visual Studio 專案

1. 在 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 SP1 中建立新的 C# 主控台應用程式。輸入 [**名稱**]、[**位置**] 和 [**方案名稱**]，然後按一下 [確定]。 

2. 使用 [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) Nuget 封裝來安裝 **Azure 媒體服務 .NET SDK 延伸**。  媒體服務 .NET SDK 延伸是一組延伸方法和協助程式函數，可簡化您的程式碼以及使用媒體服務進行開發的工作。安裝此封裝時也會安裝**媒體服務 .NET SDK**，並產生所有其他的必要相依性。
 
3. 加入 System.Configuration 組件的參考。此組件包含用來存取組態檔 (例如 App.config) 的 System.Configuration.ConfigurationManager 類別。 

4. 開啟 App.config 檔案 (如果依預設未新增，請將此檔案新增至您的專案)，並將  *appSettings* 一節新增至檔案中。設定 Azure 媒體服務的帳戶名稱和帳戶金鑰的值，如下列範例所示。若要取得帳戶名稱和金鑰資訊，請開啟 Azure 管理入口網站，選取您的媒體服務帳戶，然後按一下 [**管理金鑰**] 按鈕。


	<pre><code>
	&lt;configuration&gt;
        &lt;appSettings&gt;
    	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" /&gt;
        	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" /&gt;
  	    &lt;/appSettings&gt;
	&lt;/configuration&gt;
	</code></pre>


5. 在 Program.cs 檔案的開頭，使用下列程式碼來覆寫現有的 using 陳述式。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

6. 在專案目錄下建立新的資料夾，然後複製您要編碼和串流處理或漸進式下載的 .mp4 或 .wmv 檔案。在此範例中，我們使用 "C:\VideoFiles" 路徑。 

## <a id="use_dotnet"></a>請使用 .NET 來上傳、編碼及傳遞內容 

本節中的程式碼示範如何執行下列工作：

1. 連接到媒體服務帳戶。
1. 建立新資產及上傳視訊檔案。
1. 將來源檔案編碼成一組調適性位元速率 MP4 檔案。
1. 設定編碼資產的傳遞原則。
2. (選擇性) 設定動態內容保護。
1. 發佈資產並取得 URL。 


### <a id="connect"></a>連接到媒體服務帳戶

使用採用 .NET 的媒體服務時，您必須將 **CloudMediaContext** 類別用於大部分的媒體服務程式設計工作：連接到媒體服務帳戶；建立、更新、存取和刪除下列物件：資產、資產檔案、工作、存取原則、定位器等等。 
 
使用下列程式碼覆寫預設程式類別。此程式碼示範如何從 App.config 檔案中讀取連線值，以及如何建立 CloudMediaContext 物件以連接到媒體服務。如需連接到媒體服務的詳細資訊，請參閱[使用 Media Services SDK for .NET 連接到媒體服務](http://msdn.microsoft.com/zh-tw/library/azure/jj129571.aspx)。

**Main** 函數會呼叫將在本節中進一步定義的方法。
	
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                ConfigureClearAssetDeliveryPolicy(encodedAsset);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new 
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

### <a id="upload"></a>建立新資產及上傳視訊檔案

在媒體服務 中，您可以將數位檔案上傳 (或擷取) 至資產中。**資產**實體可包含視訊、音訊、影像、縮圖集合、文字播放軌及隱藏式字幕檔案 (和這些檔案的相關中繼資料。)  檔案上傳後，您的內容會安全地儲存在雲端中，以供進一步處理和串流處理。資產中的檔案稱為**資產檔案**。

下方定義的 **UploadFile** 方法會呼叫 **CreateFromFile** (定義於 .NET SDK 延伸中)。**CreateFromFile** 會建立指定的來源檔案所要上傳到的新資產。 

**CreateFromFile** 方法會採用 **AssetCreationOptions**，它可讓您指定下列其中一個資產建立選項：
 
- **None** - 不使用加密。這是預設值。請注意，使用此選項時，您的內容在傳輸中或在儲存體中處於靜止狀態時將不受保護。
如果您要使用漸進式下載來傳遞 MP4，請使用此選項。 
- **StorageEncrypted** - 使用 AES 256 位元在本機加密明文內容，然後將其上傳至 Azure 儲存體，以加密儲存並置於靜止狀態。以儲存體加密保護的資產在進行編碼前會自動解除加密，並放在加密的檔案系統中，且在重新上傳做為新的輸出資產之前，可選擇性地重新加密。儲存體加密的主要使用案例，是當您想要使用強式加密，在磁碟上保護處於靜止狀態的高品質輸入媒體檔案時。
- **CommonEncryption** - 如果您要上傳已加密並使用一般加密或 PlayReady DRM 保護的內容 (例如，使用 PlayReady DRM 保護的 Smooth Streaming)，請使用此選項。
- **EnvelopeEncrypted** - 如果您要上傳以 AES 加密的 HLS，請使用此選項。請注意，檔案必須已編碼，並由 Transform Manager 加密。

**CreateFromFile** 方法也可讓您指定回呼，以報告檔案的上傳進度。

在下列範例中，我們指定 **None** 做為資產選項。

將下列方法新增至 Program 類別。

	static public IAsset UploadFile(string fileName, AssetCreationOptions options)
	{
	    IAsset inputAsset = _context.Assets.CreateFromFile(
	        fileName,
	        options,
	        (af, p) =>
	        {
	            Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	        });
	
	    Console.WriteLine("Asset {0} created.", inputAsset.Id);
	
	    return inputAsset;
	}


### <a id="encode"></a>將來源檔案編碼成調適性位元速率 MP4 檔案集

將資產擷取至媒體服務後，媒體在傳遞至用戶端之前，可以先進行編碼、轉碼多工處理，浮水印處理等。這些活動會根據多個背景角色執行個體而排程和執行，以確保高效能與可用性。這些活動稱為「工作 (job)」，每個工作 (job) 由不可部分完成、且不會在資產檔案上實際運作的工作 (task) 所組成。 

如前所述，使用 Azure Media Services 時最常見的案例之一，是將調適性位元速率串流傳遞至您的用戶端。媒體服務可動態地將彈性位元速率 MP4 檔案集封裝成下列格式之一：HTTP 即時資料流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/Access 授權)。 

若要使用動態封裝，您必須執行下列動作：

- 將您的 mezzanine (來源) 檔案編碼或轉碼成調適性位元速率 MP4 檔案或調適性位元速率 Smooth Streaming 檔案，  
- 針對您要傳遞內容的來源串流端點，取得至少一個隨選串流單元。 

下列程式碼將說明如何提交編碼工作。此工作 (job) 所包含的一項工作 (task)，會指定要使用 **Azure Media Encoder** 將 mezzanine 檔案轉碼成一組調適性位元速率 MP4。程式碼會提交工作，並等待工作完成。 

工作完成後，您即可串流處理資產，或漸進式下載轉碼後所建立的 MP4 檔案。
請注意，您不需要取得隨選串流單元，即可漸進式下載 MP4 檔案。 


將下列方法新增至 Program 類別。

	static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
	{
		// Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

	    IJob job = _context.Jobs.CreateWithSingleTask(
	        MediaProcessorNames.AzureMediaEncoder,
	        MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
	        asset,
	        "Adaptive Bitrate MP4",
	        options);
	
		Console.WriteLine("Submitting transcoding job...");
	

	    // Submit the job and wait until it is completed.
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


### <a id="configure_content_protection"></a>(選擇性) 設定動態內容保護

如需如何設定內容保護的相關資訊，請參閱下列文章：

- [使用 AES 128 動態加密和金鑰傳遞服務](http://msdn.microsoft.com/zh-tw/library/azure/dn783457.aspx)
- [使用 PlayReady 動態加密和授權傳遞服務](http://msdn.microsoft.com/zh-tw/library/azure/dn783467.aspx)
- [提供儲存體加密內容](http://msdn.microsoft.com/zh-tw/library/azure/dn783451.aspx)

### <a id="configure_delivery_method"></a>設定編碼資產的傳遞原則

媒體服務內容傳遞工作流程中的其中一個步驟，是設定資產傳遞原則。資產傳遞原則組態包括：哪些通訊協定可用來傳遞資產 (例如 MPEG DASH、HLS、HDS、Smooth Streaming 或以上皆是)，您是否可以動態加密資產及其方法 (信封或一般加密)。 

下列 **ConfigureClearAssetDeliveryPolicy** 方法會指定不套用動態加密，而以下列任何通訊協定傳遞串流：  MPEG DASH、HLS 和 Smooth Streaming 通訊協定。 
  
將下列方法新增至 Program 類別。

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }

透過此傳遞組態，您將可要求採用下列格式的 Smooth、HLS 或 MPEG DASH 串流：

Smooth Streaming：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 


### <a id="publish_get_urls"></a>發佈資產及取得串流漸進式下載 URL

若要串流處理或下載資產，您必須先建立定位器來「發佈」資產。定位器會提供資產中所含檔案的存取權。媒體服務支援兩種類型的定位器：OnDemandOrigin 定位器可用來串流處理媒體 (例如 MPEG DASH、HLS 或 Smooth Streaming)，而存取簽章 (SAS) 定位器可用來下載媒體檔案。

建立定位器後，您即可建立用來串流處理或下載檔案的 URL。 


Smooth Streaming 的隨選 URL 具有下列格式：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS 的隨選 URL 具有下列格式：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH 的隨選 URL 具有下列格式：

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

用來下載檔案的 SAS URL 具有下列格式：

	{blob container name}/{asset name}/{file name}/{SAS signature}

媒體服務 .NET SDK 延伸提供了便利的協助程式方法，可針對已發佈的資產傳回格式化 URL。
 
下列程式碼會使用 .NET SDK 延伸來建立定位器，取得串流和漸進式下載 URL。此程式碼也說明如何將檔案下載至本機資料夾。

將下列方法新增至 Program 類別。

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming, 
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming, 
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get progressive download URLs for each MP4 file that was generated as a result
		// of encoding.
		List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

		// Display the progressive download URLs.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

## <a id="play"></a>播放您的內容  

在您執行上一節中定義的程式後，主控台視窗中將會顯示如下的 URL。

調適性串流 URL：

Smooth Streaming

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

漸進式下載 URL (音訊與視訊)。    
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


若要測試 Smooth Streaming，請使用 [http://amsplayer.azurewebsites.net/](http://amsplayer.azurewebsites.net/) 或 [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)。

若要測試 MPEG DASH，請使用[http://dashif.org](http://dashif.org/reference/players/javascript/)。

若要測試 HLS、請使用 iOS 或 Safari 裝置，或是 [3ivx-hls-player](http://apps.microsoft.com/windows/zh-tw/app/3ivx-hls-player/f79ce7d0-2993-4658-bc4e-83dc182a0614)。 


若要測試漸進式下載，請將 URL 貼入瀏覽器中 (例如 IE、Chrome、Safari)。


<h2>其他資源</h2>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - 立即在線上取得您的影片！</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - 動態封裝和行動裝置</a>


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [管理入口網站]: http://manage.windowsazure.com/




<h2>其他資源</h2>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - 立即在線上取得您的影片！</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - 動態封裝和行動裝置</a>
<!--HONumber=42-->
