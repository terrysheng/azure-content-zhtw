<properties urlDisplayName="Media Services" pageTitle="如何使用媒體服務 (Java) | Azure 功能指南" metaKeywords="Azure Media Services, Azure media, Azure streaming, azure media, azure streaming, azure encoding" description="說明如何使用 Azure 媒體服務執行一般工作，包括資源的編碼、加密和串流。" metaCanonical="" services="media-services" documentationCenter="Java" title="How to Use Media Services" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="robmcm" />

#如何使用媒體服務

本指南說明如何在 Azure 媒體服務中使用 Java 開始程式設計。本指南包含媒體服務的技術概觀、針對媒體服務來設定 Azure 帳戶的步驟，以及示範如何完成一般程式設計工作的程式碼。 

## 目錄

-   [什麼是媒體服務？](#media_services)
-   [設定媒體服務的 Azure 帳戶](#setup-account)
-   [設定媒體服務開發](#setup-dev)
-   [作法：搭配使用媒體服務和 Java](#connect)
-   [其他資源](#additional-resources)

 
##<a id="media_services"></a>什麼是媒體服務

Azure 媒體服務形成一個可延伸媒體平台，可整合 Microsoft Media Platform 精華與 Azure 中的協力廠商媒體元件。媒體服務提供雲端中的一個媒體管道，讓業界合作夥伴能夠擴充或取代元件技術。ISV 和媒體提供者可以使用媒體服務來建置端對端媒體解決方案。此概觀說明媒體服務的一般架構和常見開發案例。

下圖說明基本的媒體服務架構。

![Media Services Architecture](./media/media-services-dotnet-how-to-use/wams-01.png)

###媒體服務功能支援
最新版本的媒體服務提供下列一組功能，以開發雲端中的媒體應用程式。 

- **Ingest**。Ingest 作業會將資產帶入系統中，例如，在將資產放入 Azure 儲存體之前將資產上傳和加密。透過 RTM 版本，媒體服務將能夠與合作夥伴的元件整合，以提供快速 UDP (使用者資料包通訊協定) 上傳解決方案。
- **Encode**。Encode 作業包括編碼、變形和轉換媒體資產。您可以使用媒體服務中所含的 Media Encoder 在雲端中執行編碼工作。編碼選項如下：
   - 使用 Azure Media Encoder 並採用一系列的標準轉碼器和格式 (包括領先業界的 IIS Smooth Streaming、MP4 以及轉換為 Apple HTTP 即時資料流)。
   - 在對輸入和輸出有完整控制的情況下，轉換整個程式庫或個別檔案。
   - 支援眾多檔案類型、格式和轉碼器 (請參閱 [Azure Media Encoder 支援的轉碼器與檔案類型][])。
   - 支援的格式轉換。媒體服務可讓您將 ISO MP4 (.mp4) 轉換為 Smooth Streaming 檔案格式 (PIFF 1.3) (.ismv；.isma)。您也可以將 Smooth Streaming 檔案格式 (PIFF) 轉換為 Apple HTTP 即時資料流 (.msu8、.ts)。
- **Protect**。保護內容，表示將即時串流或隨選內容加密，以進行安全的傳輸、儲存和傳遞。媒體服務提供跨 DRM 技術的解決方案來保護內容。目前支援的 DRM 技術有 Microsoft PlayReady 保護和 MPEG Common Encryption。未來將再支援其他 DRM 技術。 
- **Stream**。串流內容，表示將內容立即或隨選傳送至用戶端，或者您也可以從雲端擷取或下載特定媒體檔案。媒體服務提供跨格式的解決方案來串流內容。媒體服務提供 Smooth Streaming、Apple HTTP 即時資料流和 MP4 格式的串流來源支援。未來將再支援其他格式。您也可以使用 Azure CDN 或協力廠商 CDN，啟用調整為數百萬位使用者的選項，以順暢地傳遞串流內容。   

###媒體服務開發案例
媒體服務支援數個常見媒體開發案例，如下表所述。 
<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>案例</th>
       <th>說明</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>建置端對端工作流程</td>
        <td>完全在雲端中建置完整媒體工作流程。從上傳媒體到配送內容，媒體服務都提供一系列可合併使用的元件，以處理特定應用程式工作流程。目前功能包括上傳、儲存、編碼、格式轉換、內容保護和隨選串流傳遞。</td>
    </tr>
    <tr>
        <td>建置混合式工作流程</td>
        <td>您可以將媒體服務整合現有工具和程序。例如，編碼站台上的內容，然後將其上傳至媒體服務以轉碼為多種格式，並透過 Azure CDN 或協力廠商 CDN 進行傳遞。您可以透過標準 REST API 來個別呼叫媒體服務，以與外部應用程式和服務整合。</td>
    </tr>
    <tr>
        <td>提供媒體播放程式的雲端支援</td>
        <td>您可以跨多個裝置 (包括 iOS、Android 和 Windows 裝置) 和平台來建立、管理和傳遞媒體。</td>
    </tr>
  </tbody>
</table>

###媒體服務用戶端開發
使用 SDK 和播放程式架構延伸媒體服務解決方案的範圍，以建置媒體用戶端應用程式。如果開發人員想要讓建置的媒體服務應用程式能夠跨一系列的裝置和平台提供吸引人的使用者體驗，就適合使用這些用戶端。視您要建置用戶端應用程式的裝置而定，有 Microsoft 和其他協力廠商合作夥伴提供的 SDK 與播放程式架構可選。  

下列提供可用之用戶端 SDK 和播放程式架構的清單。如需這些和其他計劃性 SDK 與播放程式架構以及其支援之功能的詳細資訊，請參閱[媒體服務用戶端開發]。 

####Mac 和 PC 用戶端支援  
針對 PC 和 Mac，您可以將目標設為使用 Microsoft Silverlight 或 Adobe Open Source Media Framework 的串流體驗。

-	[Smooth Streaming Client for Silverlight](http://www.iis.net/download/smoothclient)(英文)
-	[Microsoft Media Platform：Player Framework (Silverlight 版)](http://smf.codeplex.com/documentation) (英文)
-	[Smooth Streaming Plugin for OSMF 2.0](http://go.microsoft.com/fwlink/?LinkId=275022) (英文)。如需關於如何使用此外掛程式的詳細資訊，請參閱[如何使用 Smooth Streaming Plugin for Adobe Open Source Media Framework](http://go.microsoft.com/fwlink/?LinkId=275034)。

####Windows 8 應用程式
針對 Windows 8，您可以使用任何支援的開發語言和建構 (例如 HTML、Javascript、XAML、C# 和 C+) 來建置 Windows 市集應用程式。

-	[Smooth Streaming Client SDK for Windows 8](http://go.microsoft.com/fwlink/?LinkID=246146) (英文)。如需關於如何使用此 SDK 建立 Windows 市集應用程式的詳細資訊，請參閱[如何建置 Smooth Streaming Windows 市集應用程式](http://go.microsoft.com/fwlink/?LinkId=271647)。如需關於如何使用 HTML5 建立 Smooth Streaming 播放程式的詳細資訊，請參閱[逐步介紹：建置您的第一個 HTML5 Smooth Streaming 播放程式](http://msdn.microsoft.com/zh-tw/library/jj573656.aspx) (英文)。

-	[Microsoft Media Platform：Player Framework (Windows 8 Windows 市集應用程式版)](http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home) (英文)

####Xbox
Xbox 支援可取用 Smooth Streaming 內容的 Xbox LIVE 應用程式。Xbox LIVE Application Development Kit (ADK) 包含：

-	Xbox LIVE ADK 的 Smooth Streaming 用戶端
-	Microsoft Media Platform：Player Framework (Xbox LIVE ADK 版)

####內嵌或專用裝置
如連接的電視、機上盒、藍光播放機、OTT 電視盒以及具有自訂應用程式開發架構和自訂媒體管道的行動裝置等裝置。Microsoft 提供下列可授權、並讓合作夥伴能夠針對此平台來移植 Smooth Streaming 播放的移植套件。

-	[Smooth Streaming Client Porting Kit](http://www.microsoft.com/zh-tw/mediaplatform/sspk.aspx) (英文)
-	[Microsoft PlayReady Device Porting Kit](http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx) (英文)

####Windows Phone
Microsoft 提供可用來建置 Windows Phone 版優質視訊應用程式的 SDK。 

-	[Smooth Streaming Client for Silverlight](http://www.iis.net/download/smoothclient) (英文)
-	[Microsoft Media Platform：Player Framework (Silverlight 版)](http://smf.codeplex.com/documentation) (英文)

####iOS 裝置
針對 iOS 裝置 (包括 iPhone、iPod 和 iPad)，Microsoft 隨附 SDK，供您用來建置這些平台的應用程式，以提供優質視訊內容，這個 SDK 就是：Smooth Streaming SDK for iOS Devices with PlayReady。只有被授權人才能取得 SDK，因此，如需詳細資訊，請[傳送電子郵件給 Microsoft](mailto:askdrm@microsoft.com)。如需 iOS 開發的詳細資訊，請參閱 [iOS 開發人員中心](https://developer.apple.com/devcenter/ios/index.action) (英文)。

####Android 裝置
有幾家 Microsoft 合作夥伴都提供 Android 平台的 SDK，可新增在 Android 裝置上播放 Smooth Streaming 的功能。如需這些合作夥伴的詳細資料，請參閱[傳送電子郵件給 Microsoft](mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices) 。


##<a id="setup-account"></a>設定媒體服務的 Azure 帳戶

若要設定媒體服務帳戶，請使用 Azure 管理入口網站。請參閱主題[如何建立媒體服務帳戶][] (英文)。在管理入口網站中建立帳戶之後，就可以準備設定電腦來進行媒體服務開發。 

##<a id="setup-dev"></a>設定媒體服務開發

本節包含使用 Media Services SDK for Java 進行媒體服務開發的一般必要條件。

###必要條件

-   新的或現有 Azure 訂用帳戶中的媒體服務帳戶。請參閱主題[如何建立媒體服務帳戶][] (英文)。
-   Azure Libraries for Java，可從 [Azure Java 開發人員中心][] (英文) 進行安裝。

##<a if="connect"></a>作法：搭配使用媒體服務和 Java

下列程式碼將示範如何建立資產、上傳媒體檔案到資產、使用工作 (task) 執行工作 (job) 來轉換此資產，以及下載轉換資產的輸出檔案。

使用此程式碼前，您必須先設定媒體服務帳戶。如需設定帳戶的相關資訊，請參閱[如何建立媒體服務帳戶](http://www.windowsazure.com/zh-tw/manage/services/media-services/how-to-create-a-media-services-account/) (英文)。

替換您的 `clientId` 和 `clientSecret` 變數的值。此程式碼還需要用到儲存在本機的檔案，`c:/media/MPEG4-H264.mp4`。您必須提供您自己的檔案以供使用。此程式碼也需要輸出資料夾 (`c:/output`)，以供存放下載的輸出檔案。

	import java.io.*;
	import java.net.URI;
	import java.net.URISyntaxException;
	import java.security.NoSuchAlgorithmException;
	import java.util.EnumSet;
	import java.util.List;
	
	import com.microsoft.windowsazure.services.blob.client.*;
	import com.microsoft.windowsazure.services.core.Configuration;
	import com.microsoft.windowsazure.services.core.ServiceException;
	import com.microsoft.windowsazure.services.core.storage.StorageException;
	import com.microsoft.windowsazure.services.media.*;
	import com.microsoft.windowsazure.services.media.models.*;
	
	public class HelloMediaServices 
	{
	
	    private static MediaContract mediaService;
	    private static AssetInfo assetToEncode, encodedAsset;
	
	    public static void main(String[] args) 
	    {
	        try 
	        {
	
	            // Set up the MediaContract object to call into the media services.
	            Init();
	            
	            // Upload a local file to a media asset.
	            Upload();
	
	            // Transform the asset.
	            Transform();
	
	            // Retrieve the URL of the asset's transformed output.
	            Download();
	
	            // Delete all assets. 
	            // When you want to delete the assets that have been uploaded, 
	            // comment out the calls to Upload(), Transfer(), and Download(), 
	            // and uncomment the following call to Cleanup().
	            //Cleanup();
	
	            System.out.println("Application completed.");
	        }
	        catch (ServiceException se) 
	        {
	            System.out.println("ServiceException encountered.");
	            System.out.println(se.getMessage());
	        }
	        catch (Exception e) 
	        {
	            System.out.println("Exception encountered.");
	            System.out.println(e.getMessage());
	        }
	    }
	
	    // Initialize the server context to get programmatic access to the Media Services programming objects.
	    // The media services URI, OAuth URI and scope can be used exactly as shown.
	    // Substitute your media service account name and access key for the clientId and clientSecret variables.
	    // You can obtain your media service account name and access key from the Media Services section
	    // of the Azure Management portal, https://manage.windowsazure.com.
	    private static void Init() throws ServiceException 
	    {
	        String mediaServiceUri = "https://media.windows.net/API/";
	        String oAuthUri = "https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13";
	        String clientId = "your_client_id";  // Use your media service account name.
	        String clientSecret = "your_client_secret"; // Use your media service access key. 
	        String scope = "urn:WindowsAzureMediaServices";
	
	        // Specify the configuration values to use with the MediaContract object.
	        Configuration configuration = MediaConfiguration
	                .configureWithOAuthAuthentication(mediaServiceUri, oAuthUri, clientId, clientSecret, scope);
	
	        // Create the MediaContract object using the specified configuration.
	        mediaService = MediaService.create(configuration);
	        
	    }
	
	    // Upload a media file to your Media Services account.
	    // This code creates an asset, an access policy (using Write access) and a locator, 
	    // and uses those objects to upload a local file to the asset.
	    private static void Upload() throws ServiceException, FileNotFoundException, NoSuchAlgorithmException 
	    {
	    	
	    	WritableBlobContainerContract uploader;
	    	
	    	AccessPolicyInfo uploadAccessPolicy;
	    	LocatorInfo uploadLocator = null;
	    	
	        // Create an asset.
	    	assetToEncode = mediaService.create(Asset.create().setName("myAsset").setAlternateId("altId"));
	        System.out.println("Created asset with id: " + assetToEncode.getId());
	
	        // Create an access policy that provides Write access for 15 minutes.
	        uploadAccessPolicy = mediaService.create(AccessPolicy.create("uploadAccessPolicy", 
	        		                                                     15.0, 
	        		                                                     EnumSet.of(AccessPolicyPermission.WRITE)));
	        System.out.println("Created upload access policy with id: "
	                + uploadAccessPolicy.getId());
	
	        // Create a locator using the access policy and asset.
	        // This will provide the location information needed to add files to the asset.
	        uploadLocator = mediaService.create(Locator.create(uploadAccessPolicy.getId(),
	        		assetToEncode.getId(), LocatorType.SAS));
	        System.out.println("Created upload locator with id: " + uploadLocator.getId());
	
	        // Create the blob writer using the locator.
	        uploader = mediaService.createBlobWriter(uploadLocator);
	
	        // The name of the file as it will exist in your Media Services account.
	        String fileName = "MPEG4-H264.mp4";  
	
	        // The local file that will be uploaded to your Media Services account.
	        InputStream input = new FileInputStream(new File("c:/media/" + fileName));
	
	        // Upload the local file to the asset.
	        uploader.createBlockBlob(fileName, input);
	
	        // Inform Media Services about the uploaded files.
	        mediaService.action(AssetFile.createFileInfos(assetToEncode.getId()));
	        System.out.println("File uploaded.");
	        
	       
	        System.out.println("Deleting upload locator and access policy.");
	        mediaService.delete(Locator.delete(uploadLocator.getId()));
	        mediaService.delete(AccessPolicy.delete(uploadAccessPolicy.getId()));
	        
	    }
	
	    // Create a job that contains a task to transform the asset.
	    // In this example, the asset will be transformed using the Azure Media Encoder.
	    private static void Transform() throws ServiceException, InterruptedException 
	    {
	        // Use the Azure Media Encoder, by specifying it by name.
	        // Retrieve the list of media processors that match this name.   	
	    	ListResult<MediaProcessorInfo> mediaProcessors = mediaService
	    			.list(MediaProcessor.list()
	    			.set("$filter", "Name eq 'Azure Media Encoder'"));
	    	
	    	// Use the latest version of the media processor.
	    	MediaProcessorInfo mediaProcessor = null;
	    	for (MediaProcessorInfo info : mediaProcessors)
	    	{
	    		if (null == mediaProcessor || info.getVersion().compareTo(mediaProcessor.getVersion()) > 0)
	    		{
	    			mediaProcessor = info;
	    		}
	    	}
	
	    	System.out.println("Using processor: " + mediaProcessor.getName() +
	    			" " + mediaProcessor.getVersion());
	
	        // Create a task with the specified media processor, in this case to transform the original asset to the H264 Broadband 720p preset.
	        // Information on the various configurations can be found at
	        // http://msdn.microsoft.com/zh-tw/library/windowsazure/jj129582.aspx.
	        // This example uses only one task, but others could be added.
	        Task.CreateBatchOperation task = Task.create(
	                mediaProcessor.getId(),
	                "<taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>")
	                .setConfiguration("H264 Broadband 720p").setName("MyTask");
	
	        // Create a job creator that specifies the asset, priority and task for the job. 
	        Job.Creator jobCreator = Job.create()
	            .setName("myJob")
	            .addInputMediaAsset(assetToEncode.getId())
	            .setPriority(2)
	            .addTaskCreator(task);
	
	        // Create the job within your Media Services account.
	        // Creating the job automatically schedules and runs it.
	        JobInfo jobInfo = mediaService.create(jobCreator);
	        String jobId = jobInfo.getId();
	        System.out.println("Created job with id: " + jobId);
	        // Check to see if the job has completed.
	        CheckJobStatus(jobId);
	        // Done with the job.
	
	        // Retrieve the output asset.
	        ListResult<AssetInfo> outputAssets = mediaService.list(Asset.list(jobInfo.getOutputAssetsLink()));
	        encodedAsset = outputAssets.get(0);
	    }
	
	    // Download the output assets of the transformed asset.
	    private static void Download() throws ServiceException, URISyntaxException, FileNotFoundException, StorageException, IOException 
	    {
	    	
	    	AccessPolicyInfo downloadAccessPolicy = null;
	
	        downloadAccessPolicy =
	                mediaService.create(AccessPolicy.create("Download", 15.0, EnumSet.of(AccessPolicyPermission.READ)));
	        System.out.println("Created download access policy with id: "
	                + downloadAccessPolicy.getId());
	    	
	    	LocatorInfo downloadLocator = null;
	        downloadLocator = mediaService.create(
	        		Locator.create(downloadAccessPolicy.getId(), encodedAsset.getId(), LocatorType.SAS));
	        System.out.println("Created download locator with id: " + downloadLocator.getId());        
	
	        System.out.println("Accessing the output files of the encoded asset.");
	        // Iterate through the files associated with the encoded asset.
	        for(AssetFileInfo assetFile: mediaService.list(AssetFile.list(encodedAsset.getAssetFilesLink())))
	        {
	            String fileName = assetFile.getName();
	            
	            System.out.print("Downloading file: " + fileName + ". ");
	            String locatorPath = downloadLocator.getPath();
	            int startOfSas = locatorPath.indexOf("?");
	
	            String blobPath = locatorPath + fileName;
	            if (startOfSas >= 0) 
	            {
	                blobPath = locatorPath.substring(0, startOfSas) + "/" + fileName + locatorPath.substring(startOfSas);
	            }
	            URI baseuri = new URI(blobPath);
	            CloudBlobClient blobClient;
	            blobClient = new CloudBlobClient(baseuri);
	            
	            // Ensure that you have a c:\output folder, or modify the path specified in the following statement.
	            String localFileName = "c:/output/" + fileName;
	            
	            CloudBlockBlob sasBlob;
	            sasBlob = new CloudBlockBlob(baseuri, blobClient);
	            File fileTarget = new File(localFileName);
	            
	            sasBlob.download(new FileOutputStream(fileTarget));
	            System.out.println("Download complete.");
	            
	        }
	
	        System.out.println("Deleting download locator and access policy.");
	        mediaService.delete(Locator.delete(downloadLocator.getId()));
	        mediaService.delete(AccessPolicy.delete(downloadAccessPolicy.getId()));
	      
	    }
	    
	    // Remove all assets from your Media Services account.
	    // You could instead remove assets by name or ID, etc., but for 
	    // simplicity this example removes all of them.
	    private static void Cleanup() throws ServiceException 
	    {
	        // Retrieve a list of all assets.
	        List<AssetInfo> assets = mediaService.list(Asset.list());
	
	        // Iterate through the list, deleting each asset.
	        for (AssetInfo asset: assets)
	        {
	        	System.out.println("Deleting asset named " + asset.getName() + " (" + asset.getId() + ")");
	            mediaService.delete(Asset.delete(asset.getId()));
	        }
	    }
	
	    // Helper function to check to on the status of the job.
	    private static void CheckJobStatus(String jobId) throws InterruptedException, ServiceException
	    {
	        int maxRetries = 12; // Number of times to retry. Small jobs often take 2 minutes.
	        JobState jobState = null;
	        while (maxRetries > 0) 
	        {
	            Thread.sleep(10000);  // Sleep for 10 seconds, or use another interval.
	            // Determine the job state.
	            jobState = mediaService.get(Job.get(jobId)).getState();
	            System.out.println("Job state is " + jobState);
	
	            if (jobState == JobState.Finished || 
	                jobState == JobState.Canceled || 
	                jobState == JobState.Error) 
	            {
	                // The job is done.
	                break;
	            }
	            // The job is not done. Sleep and loop if max retries 
	            // has not been reached.
	            maxRetries--;
	        }
	  
	    }
	
	}

您建立的資產會儲存在 Azure 儲存體中。但是，Azure 媒體服務 API (而非 Azure 儲存體 API) 只能用來新增、更新或刪除資產。

###判斷可用的媒體處理器

上述程式碼透過特定媒體處理器名稱來存取媒體處理器，以此方式使用媒體處理器 (如果有多個版本，則會使用最新版本)。若要判斷有哪些媒體處理器可供使用，您可以使用下列程式碼。

    for (MediaProcessorInfo mediaProcessor:  mediaService.list(MediaProcessor.list()))
    {
        System.out.print(mediaProcessor.getName() + ", ");
        System.out.print(mediaProcessor.getId() + ", ");  
        System.out.print(mediaProcessor.getVendor() + ", ");  
        System.out.println(mediaProcessor.getVersion());  
    }

此外，下列程式碼示範如何依名稱擷取媒體處理器的識別碼。

    String mediaProcessorName = "Storage Decryption"; 
    EntityListOperation<MediaProcessorInfo> operation;
    MediaProcessorInfo processor;

    operation = MediaProcessor.list();
    operation.getQueryParameters().putSingle("$filter", "Name eq '" + mediaProcessorName + "'");
    processor = mediaService.list(operation).get(0); 
    System.out.println("Processor named " + mediaProcessorName + 
                       " has ID of " + processor.getId());

###取消工作
如果您必須取消尚未處理好的工作，下列程式碼示範如何依工作識別碼取消工作。

    mediaService.action(Job.cancel(jobId));

##< id="additional-resources"></a>其他資源

如需媒體服務 Javadoc 文件，請參閱 [Azure Libraries for Java 文件][] (英文)。

<!-- URLs. -->

  [如何建立媒體服務帳戶]: http://go.microsoft.com/fwlink/?linkid=256662
  [Azure Java 開發人員中心]: http://www.windowsazure.com/zh-tw/develop/java/
  [Azure Libraries for Java 文件]: http://dl.windowsazure.com/javadoc/
  [媒體服務用戶端開發]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn223283.aspx


<!--HONumber=35.1-->
