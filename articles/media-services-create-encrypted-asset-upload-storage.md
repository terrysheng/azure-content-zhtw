<properties linkid="develop-media-services-how-to-guides-create-assets" urlDisplayName="Create Encrypted Asset and Upload to Storage" pageTitle="Create Encrypted Asset and Upload to Storage Azure" metaKeywords="" description="Learn how to get media content into Media Services by creating and uploading an encrypted asset." metaCanonical="" services="media-services" documentationCenter="" title="How to: Create an encrypted Asset and upload to storage" authors="migree" solutions="" manager="" editor="" />

作法：建立加密資產並上傳至儲存體
================================

本文是介紹 Azure 媒體服務程式設計的系列文章之一。上一個主題是[進行電腦的媒體服務設定](http://go.microsoft.com/fwlink/?LinkID=301751&clcid=0x409) (英文)。

若要將媒體內容導入媒體服務中，請先建立資產並為其新增檔案，然後上傳資產。我們將此程序稱為嵌入內容。

在建立資產時，您可以指定三種不同的加密選項。

-   **AssetCreationOptions.None**：不加密。如果您要建立未加密的資產，您必須設定此選項。
-   **AssetCreationOptions.CommonEncryptionProtected**：適用於 Common Encryption Protected (CENC) 檔案。例如，一組已執行 PlayReady 加密的檔案，即屬於此類檔案。
-   **AssetCreationOptions.StorageEncrypted**：儲存體加密。在明文輸入檔上傳至 Azure 儲存體之前予以加密。

**注意**：媒體服務所提供的是磁碟式儲存體加密，而不是透過數位版權管理員 (Digital Rights Manager, DRM) 等線路的加密。

下方的範例程式碼會執行下列動作：

-   建立空資產。
-   建立要與資產相關聯的 AssetFile 執行個體。
-   建立 AccessPolicy 執行個體，以定義存取資產的權限和持續期間。
-   建立可用來存取資產的定位器執行個體。
-   將單一媒體檔案上傳至媒體服務中。

``` {}
static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
{
var asset = _context.Assets.Create(assetName, assetCreationOptions);

Console.WriteLine("Asset name:" + asset.Name);
Console.WriteLine("Time created:" + asset.Created.Date.ToString());

return asset;
}

static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
{
var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
var asset = CreateEmptyAsset(assetName, assetCreationOptions);

var fileName = Path.GetFileName(singleFilePath);

var assetFile = asset.AssetFiles.Create(fileName);

Console.WriteLine("Created assetFile {0}", assetFile.Name);
Console.WriteLine("Upload {0}", assetFile.Name);

assetFile.Upload(singleFilePath);
Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

return asset;
}
```

下列程式碼將說明如何建立資產並上傳多個檔案。

``` {}
static public IAsset CreateAssetAndUploadMultipleFiles( AssetCreationOptions assetCreationOptions, string folderPath)
{
var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

var asset = CreateEmptyAsset(assetName, assetCreationOptions);

var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
AccessPermissions.Write | AccessPermissions.List);
var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

var blobTransferClient = new BlobTransferClient();
    blobTransferClient.NumberOfConcurrentTransfers = 20;
blobTransferClient.ParallelTransferThreadCount = 20;

blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

var filePaths = Directory.EnumerateFiles(folderPath);

Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

if (!filePaths.Any())
    {
throw new FileNotFoundException(String.Format("No files in directory, check folderPath:{0}", folderPath));
    }

var uploadTasks = new List<Task>();
foreach (var filePath in filePaths)
    {
var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
Console.WriteLine("Created assetFile {0}", assetFile.Name);
                
// It is recommended to validate AccestFiles before upload. 
Console.WriteLine("Start uploading of {0}", assetFile.Name);
uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
    }

Task.WaitAll(uploadTasks.ToArray());
Console.WriteLine("Done uploading the files");

blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

locator.Delete();
accessPolicy.Delete();

return asset;
}

static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
{
if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
    {
Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
    }
}
```

後續步驟
--------

您已將資產上傳至媒體服務，現在請移至[如何取得媒體處理器](http://go.microsoft.com/fwlink/?LinkID=301732&clcid=0x409) (英文) 主題。

