<properties 
	pageTitle="設定 Azure 連接字串" 
	description="了解如何設定連接字串以連接到 Azure 中的儲存體帳戶。" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# 設定 Azure 儲存體連接字串

## 概觀

連接字串包含以程式設計方式存取 Azure 儲存體帳戶所需的資訊。您可以使用下列方式設定連接到 Azure 儲存體的連接字串：

- 當您在本機測試服務或應用程式時，連接到 Azure 儲存體模擬器。

- 使用儲存體服務的預設端點，連接到 Azure 中的儲存體帳戶。

- 使用明確的儲存體服務端點，連接到 Azure 中的儲存體帳戶。

如果您的應用程式是在 Azure 中執行的雲端服務，則您通常會將連接字串儲存在 [Azure 服務組態結構描述 (.cscfg) 檔](https://msdn.microsoft.com/library/ee758710.aspx) 中。如果您的應用程式在其他環境中執行 (例如，在桌面上)，則您通常會將連接字串儲存在 app.config 檔或另一個組態檔中。您可以使用 Azure `CloudConfigurationManager` 類別，在執行階段存取連接字串，而不論執行所在的地方為何。

## 建立儲存體模擬器的連接字串

儲存體模擬器是具有已知名稱和金鑰的本機帳戶。由於所有使用者的帳戶名稱和金鑰都一樣，因此，您可以使用捷徑字串格式來參考連接字串內的儲存體模擬器。將連接字串的值設為 `UseDevelopmentStorage=true`。

您也可以指定在對儲存體模擬器測試服務時要使用的 HTTP Proxy。當您正在對儲存體服務進行作業偵錯時，這對於觀察 HTTP 要求和回應非常有用。若要指定 Proxy，可將 `DevelopmentStorageProxyUri` 選項加入連接字串，並將其值設為 Proxy URI。例如，以下是指向儲存體模擬器並設定 HTTP Proxy 的連接字串：

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

## 建立 Azure 儲存體帳戶的連接字串

若要建立 Azure 儲存體帳戶的連接字串，請使用以下的連接字串格式。指出您是否要透過 HTTP 或 HTTPS (建議選項) 連接到儲存體帳戶、使用您的儲存體帳戶名稱來取代 `myAccountName`，以及使用您的帳戶存取金鑰來取代 `myAccountKey`：

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

例如，您的連接字串看起來會類似下列連接字串範例：

```        DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtB7wYQw33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIAy5l/Yhg==
```

> [AZURE.NOTE] Azure 儲存體服務可同時支援連接字串中的 HTTP 和 HTTPS；不過，強烈建議您使用 HTTPS。
    
## 建立明確儲存體端點的連接字串

如果出現下列情況，您可以在連接字串中明確指定服務端點：

- 您已經為 Blob 服務的儲存體帳戶對應一個自訂網域名稱。
- 您擁有可用來存取儲存體帳戶中儲存體資源的共用存取簽章。

若要建立指定明確 Blob 端點的連接字串，請使用下列格式來指定每個服務的完整服務端點，包括通訊協定規格 (HTTP 或 HTTPS)：

``` 
BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;FileEndpoint=myFileEndpoint;[credentials]
```

您必須至少指定一個服務端點，但不需指定全部的服務端點。例如，如果您正在建立連接字串來與自訂的 Blob 端點搭配使用，則指定佇列和資料表端點是選擇性的。請注意，如果您選擇在連接字串中省略佇列和資料表端點，則您將無法使用該連接字串，從程式碼中存取佇列和資料表服務。

當您在連接字串中明確指定服務端點時，您有兩個選項可以指定上述字串中的 `credentials`：

- 您可以指定帳戶名稱和金鑰： `AccountName=myAccountName;AccountKey=myAccountKey` 
- 您可以指定共用存取簽章： `SharedAccessSignature=base64Signature`

### 使用自訂網域名稱指定 Blob 端點 

如果您已向 Blob 服務註冊要使用的自訂網域名稱，則可能想要明確地在連接字串中設定 Blob 端點。連接字串中列出的端點值可用來建構連接到 Blob 服務的要求 URI，而它會要求任何傳回到您程式碼的 URI 形式。 

例如，位於自訂網域之 Blob 端點的連接字串可能會類似：

```
DefaultEndpointsProtocol=https;BlobEndpoint=www.mydomain.com;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtB7wYQw33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIAy5l/Yhg== 
```

### 指定具有共用存取簽章的 Blob 端點 

您可以建立具有明確端點的連接字串，透過共用存取簽章存取儲存體資源。在此情況下，您可以指定共用存取簽章做為連接字串的一部分，而不是帳戶名稱和金鑰認證。共用存取簽章權杖會封裝可存取的資源、該資源可供使用的時段，以及所授與權限的相關資訊。如需共用存取簽章的詳細資訊，請參閱 [使用共用存取簽章委派存取](https://msdn.microsoft.com/library/ee395415.aspx)。

若要建立包含共用存取簽章的連接字串，請以下列格式指定字串：

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

端點可以是預設服務端點或自訂端點。 `base64Signature` 會對應到共用存取簽章的簽章部分。簽章是透過有效簽署字串所計算的 HMAC，而金鑰使用的是 SHA256 演算法，然後使用 Base64 進行編碼。

### 建立包含端點尾碼的連接字串

若要建立區域或包含不同端點尾碼的執行個體 (例如 Azure China 或 Azure Governance) 中儲存體服務的連接字串，請使用下列連接字串格式。指出您是否要透過 HTTP 或 HTTPS 連接到儲存體帳戶、使用您的儲存體帳戶名稱來取代 `myAccountName`、使用您的帳戶存取金鑰來取代 `myAccountKey`，以及使用 URI 尾碼來取代 `mySuffix`：


	DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=mySuffix;


例如，您的連接字串應該看起來類似下列連接字串範例：

	DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtR7wYQk33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIHy5l/Yhg==;EndpointSuffix=core.chinacloudapi.cn;


<!--HONumber=52-->