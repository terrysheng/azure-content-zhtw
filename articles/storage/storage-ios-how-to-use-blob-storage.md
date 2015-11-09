<properties
    pageTitle="如何使用 iOS 中的 Azure Blob 儲存體 | Microsoft Azure"
    description="了解如何使用 Azure Blob 儲存體來上傳、下載、列出及刪除 Blob 內容。範例是以 Objective-C 撰寫的。"
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Objective-C"
    ms.topic="article"
    ms.date="10/07/2015"
    ms.author="micurd"/>

# 如何使用 iOS 的 Blob 儲存體

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 概觀

本文將示範如何使用 Microsoft Azure Blob 儲存體執行一般案例。這些範例均以 Objective-C 撰寫，並使用 [Azure 儲存體 iOS 程式庫](https://github.com/Azure/azure-storage-ios)。所涵蓋的案例包括「上傳」、「列出」、「下載」及「刪除」Blob。如需 Blob 的詳細資訊，請參閱[後續步驟](#next-steps)一節。您也可以下載[範例應用程式](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample)，以快速查看在 iOS 應用程式中使用 Azure 儲存體的方法。

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建置架構檔案
為了使用 Azure 儲存體 iOS 程式庫，您必須先建置架構檔案。

1. 首先，請下載或複製 [azure-storage-ios repo](https://github.com/azure/azure-storage-ios)。

2. 移至 *azure-storage-ios* -> *Lib* -> *Azure 儲存體用戶端程式庫*，然後在 Xcode 中開啟 `Azure Storage Client Library.xcodeproj`。

3. 在 Xcode 左上方，將作用中配置從「Azure 儲存體用戶端程式庫」變更為「架構」。

4. 建置專案 (⌘ + B)。這會在您的桌面上建立 `Azure Storage Client Library.framework` 檔案。

## 將 Azure 儲存體 iOS 程式庫匯入您的應用程式中

您可以藉由下列動作，將 Azure 儲存體 iOS 程式庫匯入您的應用程式中：

1. 建立新的專案，或在 Xcode 中開啟現有的專案。

2. 按一下左側導覽列中的專案，然後按一下專案編輯器頂端的 [一般] 索引標籤。

3. 在 [連結的架構和程式庫] 區段下，按一下 [新增] 按鈕 (+)。

4. 按一下 [新增其他...]。導覽至您剛才建立的 `Azure Storage Client Library.framework` 檔案，並加以新增。

5. 在 [連結的架構和程式庫] 區段下，再按一下 [新增] 按鈕 (+)。

6. 在已提供的程式庫清單中搜尋 `libxml2.2.dylib`，並將它新增至您的專案。

##匯入陳述式
您必須在要叫用 Azure 儲存體 API 的檔案中納入下列匯入陳述式。

    // Include the following import statement to use blob APIs.
    #import <Azure Storage Client Library/Azure_Storage_Client_Library.h>

## 設定您的應用程式以存取 Blob 儲存體

有兩種方式可驗證您的應用程式對儲存體服務的存取權：

- 共用金鑰：使用僅供測試用途的共用金鑰
- 共用存取簽章 (SAS)：使用適用於生產應用程式的 SAS

###共用金鑰
共用金鑰驗證表示您的應用程式會使用您的帳戶名稱和帳戶金鑰來存取儲存體服務。為了快速示範如何從 iOS 使用 Blob 儲存體，我們將在此快速入門中使用共用金鑰驗證。

> [AZURE.WARNING (Only use Shared Key authentication for testing purposes!) ]您的帳戶名稱和帳戶金鑰 (會提供對相關儲存體帳戶的完整讀取/寫入權限) 將會分送給下載您的應用程式的每個人。這並**不是**的實作方式，因為您的金鑰有可能遭到不受信任的用戶端破壞。

使用共用金鑰驗證時，您將會建立「連接字串」。 連接字串包含：

- **DefaultEndpointsProtocol** - 您可以選擇 http 或 https。不過，強烈建議您使用 https。
- **帳戶名稱** - 您儲存體帳戶的名稱。
- **帳戶金鑰** - 如果使用[管理入口網站](manage.windowsazure.com)，您只要按一下*管理存取金鑰*即可找到它。如果您使用[預覽入口網站](portal.azure.com)，您可以按一下 [金鑰] 圖示來尋找這項資訊。

它您的應用程式中會呈現如下：

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=account_name;AccountKey=account_key"];

###共用存取簽章 (SAS)
對於 IOS 應用程式，對 Blob 儲存體驗證用戶端之要求的建議方法是使用共用存取簽章 (SAS)。SAS 可讓您授與對資源的用戶端存取權一段指定的時間，且限定於指定的權限集。身為儲存體帳戶擁有者，您必須產生 SAS 供您的 iOS 用戶端取用。為了產生 SAS，您可能會想要撰寫個別的服務，以產生會分送給用戶端的 SAS。針對測試目的，您也可以使用 Azure CLI 來產生 SAS。請注意，您的共用金鑰認證會用來產生 SAS，但您的用戶端接著將可使用 SAS URL 中封裝的驗證資訊來取用 SAS。當您建立 SAS 時，您可以指定 SAS 有效的時間間隔，和 SAS 授與給用戶端的權限。以 Blob 容器為例，SAS 可授與對容器中的 Blob 進行讀取、寫入或刪除的權限，以及列出容器中的 Blob 的列出權限。

下列範例說明如何使用 Azure CLI 來產生 SAS 權杖，且此權杖會授與容器*sascontainer* 的讀取和寫入權限，直到 2015 年 9 月 5 日凌晨 12:00 (UTC) 為止。

1. 首先，請依照此[指南](../xplat-cli/#how-to-install-the-azure-cli)，以了解如何安裝 Azure CLI 並連接到您的 Azure 訂用帳戶。

2. 接下來，請在 Azure CLI 中輸入下列命令，以取得帳戶的連接字串：

		azure storage account connectionstring show youraccountname

3. 使用您剛才產生的連接字串建立環境變數：

		export AZURE_STORAGE_CONNECTION_STRING=”your connection string”

4. 產生 SAS URL：

		azure storage container sas create --container sascontainer --permissions rw --expiry 2015-09-05T00:00:00

5. SAS URL 應顯示如下：

		https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

6. 在您的 iOS 應用程式中，您現在可以透過下列方式使用 SAS URL 取得您容器的參考：

		// Get a reference to a container in your Storage account
    	AZSCloudBlobContainer *blobContainer = [[AZSCloudBlobContainer alloc] initWithUrl:[NSURL URLWithString:@" your SAS URL"]];

如您所見，使用 SAS 權杖時，您並不會在 iOS 應用程式中公開您的帳戶名稱和帳戶金鑰。您可以參閱[共用存取簽章教學課程](../storage-dotnet-shared-access-signature-part-1)，以進一步了解 SAS。

##非同步作業
> [AZURE.NOTE]所有對服務執行要求的方法，都是非同步作業。在程式碼範例中，您會發現這些方法具有完成處理常式。完成處理常式內的程式碼會在要求完成**之後**執行。完成處理常式後面的程式碼會在進行要求**期間**執行。

## 建立容器
儲存體 Blob 中的每個 Blob 必須位於一個容器中。下列範例說明如何在您的儲存體帳戶中建立名為 *newcontainer* 的容器 (如果還不存在)。為您的容器選擇名稱時，請留意上面提到的命名規則。

     -(void)createContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

您可以查看[入口網站](portal.azure.com)或任何[儲存體總管](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)，並驗證 *newcontainer* 位於儲存體帳戶的容器清單中，以確認運作正常。

## 設定容器權限
依預設會針對 [私人] 存取設定容器的權限。不過，容器會提供幾個不同的容器存取選項：

- **私人**：只有帳戶擁有者可以讀取容器和 Blob 資料。

- **Blob**：您可以透過匿名要求讀取此容器內的 Blob 資料，但您無法使用容器資料。用戶端無法透過匿名要求列舉容器內的 Blob。

- **容器**：可以透過匿名要求讀取容器和 Blob 資料。用戶端可以透過匿名要求列舉容器內的 Blob，但無法列舉儲存體帳戶內的容器。

下列範例說明如何建立具有 [容器] 存取權限，而允許網際網路上的所有使用者進行公用、唯讀存取的容器：

     -(void)createContainerWithPublicAccess{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## 將 Blob 上傳至容器
如 [Blob 服務概念](#blob-service-concepts)一節中所述，Blob 儲存體提供三種不同類型的 Blob：區塊 Blob、附加 Blob 和分頁 Blob。目前，Azure 儲存體 iOS 程式庫僅支援區塊 Blob。在大多數情況下，建議使用區塊 Blob 的類型。

下列範例說明如何從 NSString 上傳區塊 Blob。如果此容器中有相同名稱的 Blob 存在，此 Blob 的內容將會被覆寫。

     -(void)uploadBlobToContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
         	 else{
				 // Create a local blob object
             	 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

             	 // Upload blob to Storage
             	 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                 	if (error){
						NSLog(@"Error in creating blob.");
                 	}             
             	 }];
			 }
         }];
     }

您可以查看[入口網站](portal.azure.com)或任何[儲存體總管](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)，並驗證容器 *containerpublic* 包含 Blob *sampleblob*，以確認運作正常。在此範例中我們使用公用容器，所以您也可以移至 Blob URI 來確認運作是否正常：

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

除了從 NSString 上傳區塊 Blob 以外，NSData、NSInputStream 或本機檔案也有類似的方法。

## 列出容器中的 Blob
下列範例說明如何列出容器中的所有 Blob。執行此作業時，請留意下列參數：

- **continuationToken** - 接續權杖表示列出作業應從哪裡開始。如果未提供權杖，則會從頭列出 Blob。可以列出任何數目的 Blob，從零到設定的最大值皆可。即使此方法傳回零個結果，如果 `results.continuationToken` 不是 nil，則可能會有服務上的更多 Blob 未列出。
- **prefix** -您可以指定要用於 Blob 列出作業的前置詞。只有開頭為此前置詞的 Blob 才會列出。
- **useFlatBlobListing** - 如[命名與參考容器和 Blob](#naming-and-referencing-containers-and-blobs) 一節中所述，雖然 Blob 服務是一般儲存體配置，雖然您可以路徑資訊為 Blob 命名，以建立虛擬階層。不過目前不支援非一般列出；不久後將可支援。目前，此值應為 `YES`
- **blobListingDetails** - 您可以指定列出 Blob 時所要包含的項目
	- `AZSBlobListingDetailsNone`：僅列出已認可的 Blob，且不傳回 Blob 中繼資料。
	- `AZSBlobListingDetailsSnapshots`：列出已認可的 Blob 和 Blob 快照。
	- `AZSBlobListingDetailsMetadata`：為清單中傳回的每個 Blob 擷取 Blob 中繼資料。
	- `AZSBlobListingDetailsUncommittedBlobs`：列出已認可及未認可的 Blob。
	- `AZSBlobListingDetailsCopy`：在清單中包含複製屬性。
	- `AZSBlobListingDetailsAll`：列出所有可用的已認可 Blob、未認可的 Blob 和快照，並傳回這些 Blob 的所有中繼資料和複製狀態。
- **maxResults** - 要為此作業傳回的結果數目上限。使用 -1 則不會設定限制。
- **completionHandler** - 要執行的程式碼區塊與列出作業的結果。

在此範例中，將會在每次傳回接續權杖時使用 Helper 方法以遞迴方式呼叫列出 Blob 方法。

    -(void)listBlobsInContainer{
      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      //List all blobs in container
      [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
         if (error != nil){
             NSLog(@"Error in creating container.");
         }
      }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
      [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
         if (error)
         {
             completionHandler(error);
         }
         else
         {
             for (int i = 0; i < results.blobs.count; i++) {
                 NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
             }
             if (results.continuationToken)
             {
                 [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
             }
             else
             {
                 completionHandler(nil);
             }
         }
      }];
    }

## 下載 Blob

下列範例說明如何將 Blob 下載到 NSString 物件。

     -(void)downloadBlobToString{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob    
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
			else{
            	NSLog(@"%@",text);
			}
        }];
    }

## 刪除 Blob

下列範例說明如何刪除 Blob。

     -(void)deleteBlob{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
          if (error) {
            NSLog(@"Error in deleting blob.");
          }
        }];
     }

## 刪除 Blob 容器

下列範例說明如何刪除容器。

     -(void)deleteContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Delete container
        [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
            if(error){
                NSLog(@"Error in deleting container");
            }
        }];
    }

## 後續步驟

了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

- [Azure 儲存體 iOS 程式庫]
- [Azure 儲存體 REST API]
- [Azure 儲存體團隊部落格]

如果您有關於此程式庫的問題，歡迎您貼文到我們的 [MSDN Azure 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/zh-TW/home?forum=windowsazuredata)或[堆疊溢位](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files)。如果您有 Azure 儲存體功能方面的建議，請貼文到 [Azure 儲存體意見反應](http://feedback.azure.com/forums/217298-storage)。

[Azure 儲存體 iOS 程式庫]: https://github.com/azure/azure-storage-ios
[Azure 儲存體 REST API]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage

<!---HONumber=Nov15_HO1-->