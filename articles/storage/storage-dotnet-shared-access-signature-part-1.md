<properties 
	pageTitle="共用存取簽章：了解 SAS 模型 | Microsoft Azure" 
	description="深入了解使用共用的存取簽章 (SAS) 委派存取至 Azure 儲存體資源，包括 Blob、佇列、資料表及檔案。利用共用的存取簽章，您可以保護您的儲存體帳戶金鑰，同時將帳戶中資源的存取權授與其他使用者。您可以控制您授與的權限和 SAS 的有效時間間隔。如果您也可以建立預存的存取原則，您就可以在擔心帳戶安全性遭到入侵時撤銷 SAS。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/27/2015" 
	ms.author="tamram"/>



# 共用存取簽章，第 1 部分：了解 SAS 模型

## 概觀

若要在無需提供您帳戶金鑰的情況下，將儲存體帳戶中物件的限制存取授與其他用戶端，則使用共用存取簽章 (SAS) 會是個佷有效的方式。在本教學課程有關共用存取簽章的第 1 部分中，我們將提供 SAS 模型的概觀並檢閱 SAS 最佳做法。本教學課程的[第 2 部分](storage-dotnet-shared-access-signature-part-2.md)會逐步引導您，使用 Blob 服務來完成建立共用存取簽章的程序。

## 共用存取簽章為何？

共用存取簽章可提供您儲存體帳戶中資源的委派存取。這表示您可以在無需分享您帳戶存取金鑰的情況下，將您儲存體帳戶中的物件有限權限授與用戶端，該用戶端便可在指定的時間期間內及使用指定的權限集來進行存取。SAS 是一種 URI，URI 會在其查詢參數中包含通過驗證存取儲存體資源的所有必要資訊。若要使用 SAS 存取儲存體資源，用戶端只需在適當的建構函式或方法中傳入 SAS 即可。

## 使用共用存取簽章的時機？

當您想要將儲存體帳戶中的資源存取權提供給無法放心託付帳戶金鑰的用戶端時，您可以使用 SAS。您的儲存體帳戶金鑰包括主要和次要金鑰，兩者皆可授與帳戶及帳戶內所有資源的系統管理存取權。提供任一帳戶金鑰都會讓您的帳戶受到惡意或粗心使用的可能性。共用存取簽章提供一個安全的替代方式，無需帳戶金鑰便可讓其他用戶端根據他們被授與的權限，來讀取、寫入及刪除儲存體帳戶中的資料。

證明 SAS 非常有用的一個常見案例，就是使用者在您的儲存體帳戶中讀取和寫入自己的資料。在儲存體帳戶儲存使用者資料的案例中，典型的設計模式有兩種：


1\.用戶端通過前端 Proxy 服務 (執行驗證) 來上傳與下載資料。此前端 Proxy 服務有個好處，那就是允許商務規則的驗證，但在大量資料或大量交易的情況下，建立可調整以符合需求的服務可能十分昂貴或困難。

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2\.輕量型服務可視需要驗證用戶端，然後產生 SAS。在用戶端收到 SAS 之後，他們可以使用 SAS 所定義的權限，並在 SAS 允許的間隔內直接存取儲存體帳戶資源。SAS 可減輕透過前端 Proxy 服務路由所有資料的需求。

![sas-storage-provider-service][sas-storage-provider-service]

視所涉及的案例而定，許多實際服務可能會混合運用這兩種方法，在部分資料透過前端 Proxy 進行處理與驗證時，其他資料會使用 SAS 直接儲存與/或讀取。

此外，在某些情況下，您必須使用 SAS 來驗證複製作業中的來源物件：

- 當您將 Blob 複製到另一個位於不同的儲存體帳戶的 Blob 時，您必須使用 SAS 來驗證來源 Blob。有了 2015-04-05 版，您也可以選擇性地使用 SAS 來驗證目的地 Blob。
- 當您將檔案複製到另一個位於不同儲存體帳戶的檔案時，您必須使用 SAS 來驗證來源檔案。有了 2015-04-05 版，您也可以選擇性地使用 SAS 來驗證目的地檔案。
- 當您將 Blob 複製到檔案，或將檔案複製到 Blob 時，您必須使用 SAS 來驗證來源物件，即使來源和目的地位於相同的儲存體帳戶內也一樣。

## 共用存取簽章的類型

2015-04-05 版的 Azure 儲存體推出新類型的共用存取簽章，即帳戶 SAS。您現在可以建立兩個類型的共用存取簽章：

- **帳戶 SAS。** 帳戶 SAS 則將存取權限委派給一或多個儲存體服務的資源。可透過服務 SAS 取得的所有作業也可透過帳戶 SAS 取得。此外，利用帳戶 SAS，您可以委派適用指定的服務作業 (例如：**取得/設定服務屬性**和**取得服務統計資料**) 的存取。您也可以將 Blob 容器、資料表、佇列和檔案共用的讀取、寫入和刪除作業的存取權限，委派給本無權限的服務 SAS。如需有關建構帳戶 SAS 權杖的深入資訊，請參閱[建構帳戶 SAS](https://msdn.microsoft.com/library/mt584140.aspx)。

- **服務 SAS。** 服務 SAS 只會將存取權限委派給一種儲存體服務資源：Blob、佇列、資料表或檔案服務。如需有關建構服務 SAS 權杖的深入資訊，請參閱[建構服務 SAS](https://msdn.microsoft.com/library/dn140255.aspx) 和[服務 SAS 範例](https://msdn.microsoft.com/library/dn140256.aspx)。

## 共用存取簽章的運作方式

共用存取簽章是指向一或多個儲存體資源，並包括含有一組特殊的查詢參數權杖的 URI。權杖指出用戶端可以如何存取資源。簽章是查詢參數的其中一個，根據 SAS 參數所建構並使用帳戶金鑰進行簽署。Azure 儲存體會使用此簽章來驗證 SAS。

帳戶 SAS 和服務 SAS 權杖包含一些常見的參數，並且採取幾個不同參數。

### 帳戶 SAS 和服務 SAS 權杖的通用參數

- **API 版本** 選擇性參數，指定要用來執行要求的儲存體服務版本。 
- **服務版本** 必要參數，指定要用於驗證要求的儲存體服務版本。
- **開始時間。** 這是指 SAS 生效的時間。共用存取簽章的開始時間是選擇性選項，如果略過，則 SAS 會立即生效。 
- **到期時間。** 這是指 SAS 何時失效的時間。最佳做法建議您為 SAS 指定過期時間，或將它與預存存取原則建立關聯 (請參閱以下詳細資訊)。
- **權限。** 在 SAS 上指定的權限表示用戶端可以使用 SAS 來對儲存體資源執行哪些作業。帳戶 SAS 和服務 SAS 的可用權限不同。
- **IP。** 選用參數，可指定要從中接受要求且位於 Azure 外部的 IP 位址或 IP 位址範圍 (請參閱適用於 Express Route 的[路由工作階段組態狀態](../expressroute/expressroute-workflows.md#routing-session-configuration-state)一節)。 
- **通訊協定。** 選擇性參數，指定對要求允許的通訊協定。可能的值為 HTTPS 和 HTTP (https、http)，也就是預設值或僅限 HTTPS (https)。請注意，僅 HTTP 是不允許的值。
- **簽章。** 簽章是從其他參數建構，指定為權杖的一部分，然後加密。它是用來驗證 SAS。

### 帳戶 SAS 權杖的參數

- **一或多個服務。** 帳戶 SAS 可以委派存取給一或多個儲存體服務。例如，您可以建立委派存取 Blob 和檔案服務的帳戶 SAS。或者您可以建立委派存取給全部四個服務 (Blob、佇列、表格和檔案) 的 SAS。
- **儲存體資源類型。** 帳戶 SAS 適用於一或多個類別的儲存體資源，而不是特定資源。您可以建立帳戶 SAS 來委派存取給：
	- 對儲存體帳戶資源呼叫的服務層級 API。範例包括**取得/設定服務屬性**、**取得服務統計資料**和**列出容器/佇列/資料表/共用**。
	- 容器層級 API，會針對每個服務的容器物件呼叫：Blob 容器、佇列、資料表和檔案共用。範例包括**建立/刪除容器**、**建立/刪除佇列**、**建立/刪除資料表**、**建立/刪除共用**和**列出 Blob/檔案和目錄**。
	- 物件層級 API，針對 Blob、佇列訊息、資料表實體和檔案呼叫。例如，**放置 Blob**、**查詢實體**、**取得訊息**和**建立檔案**。

### 服務 SAS 權杖的參數

- **儲存體資源。** 可以委派對服務 SAS 存取的儲存體資源包括：
	- 容器和 Blob
	- 檔案共用及檔案
	- 佇列
	- 資料表和資料表實體範圍。

## SAS URI 的範例

以下是提供讀取和寫入 Blob 權限的服務 SAS URI 範例。此資料表會細分 URI 的每一部分，以了解它會如何影響 SAS：

	https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

名稱|SAS 部分|說明
---|---|---
Blob URI|https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt |Blob 的位址。請注意，我們強烈建議您使用 HTTPS。
儲存體服務版本|sv=2015-04-05|若是儲存體服務版本 2012-02-12 和更新版本，此參數表示要使用的版本。
開始時間|st=2015-04-29T22%3A18%3A26Z|指定採用 ISO 8061 格式。如果您想要 SAS 立即生效，請略過開始時間。
過期時間|se=2015-04-30T02%3A23%3A26Z|指定採用 ISO 8061 格式。
資源|sr=b|此資源是 Blob。
權限|sp=rw|SAS 所授與的權限包括讀取 (r) 和寫入 (w)。
IP 範圍|sip=168.1.5.60-168.1.5.70|將從中接受要求的 IP 位址範圍。
通訊協定|spr=https|僅允許使用 HTTPS 的要求。
簽章|sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D|用來驗證對 Blob 的存取權。此簽章是 HMAC 根據要簽署字串和金鑰，使用 SHA256 演算法進行計算，然後使用 Base64 方式進行編碼而來的。

而以下是對權杖使用相同的通用參數的 SAS 帳戶的範例。由於上面說明了這些參數，在此處將不說明。下表只會說明帳戶 SAS 的特定參數。

	https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B

名稱|SAS 部分|說明
---|---|---
資源 URI|https://myaccount.blob.core.windows.net/?restype=service&comp=properties|The Blob 服務端點，具有用來取得服務屬性 (使用 GET 呼叫時) 或設定服務屬性 (使用 SET 呼叫時) 的參數。
服務|ss=bf|SAS 適用於 Blob 和檔案服務
資源類型|srt=s|SAS 適用於服務層級的作業。
權限|sp=rw|此權限可授與讀取和寫入作業的存取權。  

假設權限僅限於服務層級，此 SAS 可存取的作業是**取得 Blob 服務屬性** (讀取) 和**設定 Blob 服務屬性** (寫入)。不過，利用不同的資源 URI，相同的 SAS 權杖也可以用來委派存取給**取得 Blob 服務統計資料** (讀取)。

## 使用預存存取原則控制 SAS ##

共用存取簽章可以接受以下兩種格式其中之一：

- **臨機操作 SAS：**建立臨機操作 SAS 時，SAS 的開始時間、到期時間和權限都會在 SAS URI 上進行指定 (或暗示，在此情況下則會略過開始時間)。這種類型的 SAS 可能會建立為帳戶 SAS 或服務 SAS。 

- **具有預存存取原則的 SAS：**預存存取原則會在資源容器 (Blob 容器、資料表、佇列或檔案共用) 中定義，且可用來管理一或多個共用存取簽章的限制。當您將 SAS 與預存存取原則建立關聯時，SAS 會繼承為該預存存取原則所定義的限制 (開始時間、過期時間和權限)。

>[AZURE.NOTE]目前，帳戶 SAS 必須是臨機操作 SAS。帳戶 SAS 尚不支援預存的存取原則。

這兩種格式間的差異對於以下這一個重要案例而言相當重要：撤銷。SAS 是一種 URL，因此取得 SAS 的任何人都可以使用它，無論起先要求的人是誰。如果是公開發佈 SAS，則全世界的人都可以使用此 SAS。散佈的 SAS 在發生以下四個情況其中之一之前都會持續有效：

1.	已到達 SAS 上指定的過期時間。
2.	已到達在 SAS 所參考之預存存取原則上所指定的過期時間 (如果參考的是預存存取原則，而且如果此預存存取原則指定了過期時間)。發生的原因有可能是因為已經超過指定的間隔時間，或因為您已修改預存存取原則，將過期時間設定為過去的日期，這是撤銷 SAS 的一種方法。
3.	已刪除 SAS 所參考之預存存取原則，這是撤銷 SAS 的另外一種方法。請注意，如果您使用完全相同的名稱來重新建立預存存取原則，則現有的所有 SAS 權杖會根據與該預存存取原則有關的權限再次有效 (假設 SAS 上的過期時間尚未過去)。如果您打算撤銷 SAS，且如果您要使用未來的過期時間來重新建立存取原則，則務必使用不同的名稱。
4.	系統會重新產生用來建立 SAS 的帳戶金鑰。請注意，這麼做將會導致所有使用該帳戶金鑰的應用程式元件無法進行驗證，直到他們已更新為使用其他有效帳戶金鑰或重新產生帳戶金鑰為止。

>[AZURE.IMPORTANT]共用存取簽章 URI 會與用來建立簽章的帳戶金鑰，以及相關聯的預存的存取原則 (如果有的話) 產生關聯。如果未指定任何預存的存取原則，則撤銷共用存取簽章的唯一方式是變更帳戶金鑰。

## 共用存取簽章的範例

下面是兩種類型共用存取簽章 (帳戶 SAS 和服務 SAS) 的一些範例。

### 帳戶 SAS 範例

下列程式碼範例會建立適用於 Blob 和檔案服務的帳戶 SAS，並提供用戶端權限讀取、寫入和列出權限來存取服務層級 API。帳戶 SAS 會將通訊協定限制為 HTTPS，因此必須使用 HTTPS 提出要求。

    static string GetAccountSASToken()
    {
        // To create the account SAS, you need to use your shared key credentials.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create a new access policy for the account.
        SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
            {
                Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
                Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
                ResourceTypes = SharedAccessAccountResourceTypes.Service,
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
                Protocols = SharedAccessProtocol.HttpsOnly
            };

        // Return the SAS token.
        return storageAccount.GetSharedAccessSignature(policy);
    }

若要使用 帳戶 SAS 來存取 Blob 服務的服務層級 API，請使用 SAS 及儲存體帳戶的 Blob 儲存體端點來建構 Blob 用戶端物件。

    static void UseAccountSAS(string sasToken)
    {
        // In this case, we have access to the shared key credentials, so we'll use them
        // to get the Blob service endpoint.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create new storage credentials using the SAS token.
        StorageCredentials accountSAS = new StorageCredentials(sasToken);
        // Use these credentials and the Blob storage endpoint to create a new Blob service client.
        CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, blobClient.StorageUri, null, null, null);
        CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

        // Now set the service properties for the Blob client created with the SAS.
        blobClientWithSAS.SetServiceProperties(new ServiceProperties()
        {
            HourMetrics = new MetricsProperties()
            {
                MetricsLevel = MetricsLevel.ServiceAndApi,
                RetentionDays = 7,
                Version = "1.0"
            },
            MinuteMetrics = new MetricsProperties()
            {
                MetricsLevel = MetricsLevel.ServiceAndApi,
                RetentionDays = 7,
                Version = "1.0"
            },
            Logging = new LoggingProperties()
            {
                LoggingOperations = LoggingOperations.All,
                RetentionDays = 14,
                Version = "1.0"
            }
        });

        // The permissions granted by the account SAS also permit you to retrieve service properties.
        ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
        Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
        Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
        Console.WriteLine(serviceProperties.HourMetrics.Version);
    }

### 服務 SAS 範例

下列程式碼範例會在容器上建立預存的存取原則，然後為容器產生服務 SAS。然後可以將此 SAS 提供給用戶端，以取得容器上的讀寫權限：

    // The connection string for the storage account.  Modify for your account.
    string storageConnectionString =
       "DefaultEndpointsProtocol=https;" +
       "AccountName=myaccount;" +
       "AccountKey=<account-key>";
    
    // As an alternative, you can retrieve storage account information from an app.config file. 
    // This is one way to store and retrieve a connection string if you are 
    // writing an application that will run locally, rather than in Microsoft Azure.
    
    // string storageConnectionString = ConfigurationManager.AppSettings["StorageAccountConnectionString"];
    
    // Create the storage account with the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
       
    // Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Get a reference to the container for which shared access signature will be created.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();
    
    // Get the current permissions for the blob container.
    BlobContainerPermissions blobPermissions = container.GetPermissions();
    
    // The new shared access policy provides read/write access to the container for 24 hours.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // To ensure SAS is valid immediately, don’t set the start time.
       // This way, you can avoid failures caused by small clock differences.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
       Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
    });
    
    // The public access setting explicitly specifies that 
    // the container is private, so that it can't be accessed anonymously.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;
    
    // Set the permission policy on the container.
    container.SetPermissions(blobPermissions);
    
    // Get the shared access signature token to share with users.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

擁有服務 SAS 的用戶端可以在其程式碼中用它來驗證對容器中 Blob 的讀取或寫入要求。例如，下列程式碼會使用 SAS 權杖在容器中建立新的區塊 Blob：

    Uri blobUri = new Uri("https://myaccount.blob.core.windows.net/mycontainer/myblob.txt");
    
    // Create credentials with the SAS token. The SAS token was created in previous example.
    StorageCredentials credentials = new StorageCredentials(sasToken);
    
    // Create a new blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);
    
    // Upload the blob. 
    // If the blob does not yet exist, it will be created. 
    // If the blob does exist, its existing content will be overwritten.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Test\myblob.txt"))
    {
    	blob.UploadFromStream(fileStream);
    }


## 使用共用存取簽章的最佳做法

當您在應用程式中使用共用存取簽章時，您必須留意兩個潛在風險：

- 如果 SAS 洩漏出去，則取得該 SAS 的任何人都可以使用它，這有可能會洩露您的儲存體帳戶。
- 如果提供給用戶端應用程式的 SAS 已過期，且此應用程式無法從您的服務擷取新的 SAS，那麼該應用程式的功能可能會受到影響。  

下列關於使用共用存取簽章的建議將協助您平衡這些風險：

1. **永遠使用 HTTPS** 來建立 SAS 或散佈 SAS。如果透過 HTTP 來傳遞 SAS 並被攔截，執行攔截式攻擊的攻擊者將能夠讀取並使用 SAS (就如同預期使用者執行般)，這有可能會洩露敏感資料或允許惡意使用者損毀資料。
2. **可能的話，參考預存存取原則。** 預存存取原則提供了撤銷權限且無需重新產生儲存體帳戶金鑰的選項。將到期日設在很久之後 (或無限) 的日期，並確定定期更新到期日以將到期日再往未來移動。
3. **在臨機操作 SAS 上使用短期的到期時間。** 如此一來，即使 SAS 在不知不覺中被洩露，它也只會在短時間內可用。如果您無法參考預存存取原則，此做法格外重要。此做法也可協助限制可寫入 Blob 的資料量，方法是限制可對其上傳的可用時間。
4. **讓用戶端視需要自動更新 SAS。** 用戶端應在預期的到期日之前就更新 SAS，以便如果提供 SAS 的服務無法使用的話，還有時間可以進行重試。如果您打算將 SAS 用於少量的即時短期操作 (預計可在指定的到期時間內完成的操作)，則此建議可能沒有必要，因為沒有更新 SAS 的打算。不過，如果您有定期透過 SAS 做出要求的用戶端，則到期的可能性便有可能發生。主要考量是要平衡下列兩個需求：短期的 SAS (如上所述)，與確保用戶端提早要求更新以避免成功更新之前因 SAS 過期而中斷。
5. **請小心使用 SAS 開始時間。** 如果您將 SAS 的開始時間設為 [現在]，則由於時鐘誤差 (根據不同機器會有不同的目前時間)，前幾分鐘可能偶爾會被視為失敗。一般而言，請將開始時間設為至少 15 分鐘之前或根本不要進行設定，這麼做會讓 SAS 在所有情況下立即有效。同樣的道理通常也可套用到過期時間，請記住，您可以在任何要求上保留前後多達 15 分鐘的時鐘誤差。請注意，若是用戶端使用 2012-02-12 之前的 REST 版本，則不參考預存存取原則之 SAS 的最大持續期限是 1 個小時，且任何指定比 1 個小時還要長的原則都會失敗。
6.	**請具體指出要存取的資源。** 典型的安全性最佳做法是提供使用者最低需求權限。如果使用者只需要單一實體的讀取存取權，則授與他們該單一實體的讀取存取權，而非授與他們所有實體的讀取/寫入/刪除存取權。這有助於減輕洩露 SAS 的威脅，因為 SAS 落入攻擊者的手中時，就無法發揮固有的功能。
7.	**了解您帳戶的任何方式將會被收取費用，包括以 SAS 方式完成的部分。** 如果您提供 Blob 的寫入存取權，則使用者可能會選擇上傳 200GB 的 Blob。如果您也同時提供使用者讀取存取權，則他們可能會選擇下載10 次，您便會產生 2TB 的出口成本。再次強調，提供有限的權限有助於減少潛在的惡意使用者。使用短期 SAS 以降低此威脅 (但請注意結束時間的時鐘誤差)。
8.	**使用 SAS 驗證寫入資料。** 當用戶端應用程式將資料寫入您的儲存體帳戶時，請留意該資料可能會造成問題。如果您的應用程式要求在開始使用資料之前先驗證或授權資料，則您應在寫入資料之後但應用程式尚未開始使用資料之前執行此驗證。此做法也可防止正確取得 SAS 的使用者或是利用洩漏 SAS 的使用者，損毀資料或將惡意資料寫入您的帳戶。
9. **請勿一直使用 SAS。** 有時候，在儲存體帳戶中執行特定作業的相關風險可能大過 SAS 的好處。針對此類作業，請建立一個中介層服務，在執行商務規則驗證、驗證及稽核之後才寫入您的儲存體帳戶。另外，有時候以其他方式管理存取權可能比較簡單。例如，如果您要讓容器中的所有 Blob 都可公開讀取，則您可以將此容器設定為 [公用]，而不是將 SAS 提供給每個用戶端進行存取。
10.	**使用儲存體分析來監視您的應用程式。** 您可以使用記錄和度量來觀察由於 SAS 提供者服務中斷或不小心移除預存存取原則，而造成的任何驗證失敗急劇增加。如需額外資訊，請參閱 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) (英文)。

## 結論 ##

若要提供您儲存體帳戶的有限權限給沒有帳戶金鑰的用戶端，則共用存取簽章是非常有用的方式。因此，對於任何使用 Azure 儲存體的應用程式而言，共用存取簽章是安全性模型不可或缺的一部分。如果您依照此處所列的最佳做法進行，則您可以使用 SAS 來提供更大的彈性以存取儲存體帳戶中的資源，且不會影響應用程式的安全性。

## 後續步驟 ##

- [共用存取簽章，第 2 部分：透過 Blob 服務來建立與使用 SAS](storage-dotnet-shared-access-signature-part-2.md)
- [如何搭配 Azure 檔案儲存體使用 Windows](storage-dotnet-how-to-use-files.md)
- [管理 Azure 儲存體資源的存取](storage-manage-access-to-resources.md)
- [使用共用存取簽章來委派存取權](http://msdn.microsoft.com/library/azure/ee395415.aspx)
- [資料表和佇列 SAS 簡介](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx) [sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png [sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png


 

<!---HONumber=Oct15_HO2-->