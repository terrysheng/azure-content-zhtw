<properties 
	pageTitle="更換儲存體存取金鑰之後更新媒體服務" 
	description="本文章會教您如何在更換儲存體存取金鑰之後更新媒體服務。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,milangada,cenkdin" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/15/2015"
	ms.author="juliako"/>

#如何：更換儲存體存取金鑰之後更新媒體服務

當您建立新的 Azure 媒體服務帳戶時，系統也會要求您選取一個 Azure 儲存體帳戶，用來儲存媒體內容。請注意，您可以[新增多個儲存體帳戶](meda-services-managing-multiple-storage-accounts.md)至媒體服務帳戶中。

建立新的儲存體帳戶時，Azure 會產生兩個 512 位元儲存體存取金鑰，用來驗證儲存體帳戶的存取權。為了讓儲存體連線更加安全，建議您定期重新產生並更換儲存體存取金鑰。您會收到兩個存取金鑰(主要和次要)，這樣當您重新產生其他存取金鑰時，您就可以使用其中一個存取金鑰保持儲存體連線不中斷。此程序也稱為 「 更換存取金鑰 」。

媒體服務取決於所提供的儲存體金鑰。具體而言，定位器是要串流傳送或下載您的資產，這會根據指定的儲存體存取金鑰而定。建立 AMS 帳戶時，它預設會相依於主要儲存體存取金鑰，但身為使用者，您可以更新 AMS 所擁有的儲存體金鑰。您必須確定會讓媒體服務知道本主題中所述之下列步驟所使用的金鑰。此外，更換儲存體存取金鑰時，請您一定要更新定位器，這樣您的串流服務才不會停擺 (此步驟也會於本主題中加以說明)。

>[AZURE.NOTE]如果您有多個儲存體帳戶，請針對每一個儲存體帳戶執行此程序。
>
>在實際商用的帳戶上執行本文章描述的步驟之前，請事先在測試用帳戶上進行測試。


## 步驟 1：重新產生次要儲存體存取金鑰

請先重新產生次要儲存體金鑰。根據預設，媒體服務不會使用次要金鑰。如需如何更換儲存體金鑰的相關資訊，請參閱[做法：檢視、複製及重新產生儲存體存取金鑰](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。
  
##<a id="step2"></a>步驟 2：更新媒體服務以使用新的次要儲存體金鑰

更新媒體服務，開始使用次要儲存體存取金鑰。您可以使用下列兩種方法的其中一個，將重新產生的儲存體金鑰與媒體服務同步。

- 使用 Azure 傳統入口網站：選取您的媒體服務帳戶，然後按一下入口網站視窗底部的 [管理金鑰] 圖示。根據您想要為媒體服務同步的儲存體金鑰，選取同步處理主要金鑰或同步處理次要金鑰按鈕。在本案例中，使用次要金鑰。

- 使用媒體服務管理 REST API。

下列程式碼範例示範如何建構 https://endpoint/<subscriptionId>/services/mediaservices/Accounts/<accountName>/StorageAccounts/<storageAccountName>/Key 要求，以便將指定的儲存體金鑰同步到媒體服務。在本情況中，會使用次要儲存體金鑰。如需詳細資訊，請參閱[做法：使用媒體服務管理 REST API](http://msdn.microsoft.com/library/azure/dn167656.aspx)。
 
		public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
		{
		    var clientCert = GetCertificate(CertThumbprint);
		
		    HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
		        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
		    request.Method = "PUT";
		    request.ContentType = "application/json; charset=utf-8";
		    request.Headers.Add("x-ms-version", "2011-10-01");
		    request.Headers.Add("Accept-Encoding: gzip, deflate");
		    request.ClientCertificates.Add(clientCert);
		
		
		    using (var streamWriter = new StreamWriter(request.GetRequestStream()))
		    {
		        streamWriter.Write(""");
		        streamWriter.Write(storageAccountKey);
		        streamWriter.Write(""");
		        streamWriter.Flush();
		    }
		
		    using (var response = (HttpWebResponse)request.GetResponse())
		    {
		        string jsonResponse;
		        Stream receiveStream = response.GetResponseStream();
		        Encoding encode = Encoding.GetEncoding("utf-8");
		        if (receiveStream != null)
		        {
		            var readStream = new StreamReader(receiveStream, encode);
		            jsonResponse = readStream.ReadToEnd();
		        }
		    }
		}

在此步驟之後，請更新現有的定位器 (和舊的儲存體金鑰存在相依性)，如下列步驟所示。

>[AZURE.NOTE]等待 30 分鐘，然後透過媒體服務執行任何操作 (例如，建立新定位器)，以免影響到待處理的工作。

##步驟 3：更新定位器 

>[AZURE.NOTE]更換儲存體存取金鑰時，請您一定要更新現有的定位器，這樣您的串流服務才不會停擺。

將新的儲存體金鑰同步到 AMS 之後，請至少等候 30 分鐘。接著，您可以重新建立 OnDemand 定位器，這樣定位器就會對指定的儲存體金鑰產生相依性，並維護現有的 URL。

請注意，當您更新 (或重新建立) SAS 定位器時，URL 一律隨之變更。

>[AZURE.NOTE]若要確定能保留 OnDemand 定位器現有的 URL，您必須刪除現有的定位器並建立一個具有相同識別碼的新定位器。
 
下列 .NET 範例示範如何重新建立一個識別碼相同的定位器。
	
	private static ILocator RecreateLocator(CloudMediaContext context, ILocator locator)
	{
	    // Save properties of existing locator.
	    var asset = locator.Asset;
	    var accessPolicy = locator.AccessPolicy;
	    var locatorId = locator.Id;
	    var startDate = locator.StartTime;
	    var locatorType = locator.Type;
	    var locatorName = locator.Name;
	
	    // Delete old locator.
	    locator.Delete();
	
	    if (locator.ExpirationDateTime <= DateTime.UtcNow)
	    {
	        throw new Exception(String.Format(
	            "Cannot recreate locator Id={0} because its locator expiration time is in the past",
	            locator.Id));
	    }
	
	    // Create new locator using saved properties.
	    var newLocator = context.Locators.CreateLocator(
	        locatorId,
	        locatorType,
	        asset,
	        accessPolicy,
	        startDate,
	        locatorName);
	
	
	
	    return newLocator;
	}


##步驟 5：重新產生主要儲存體存取金鑰

重新產生主要儲存體存取金鑰。如需如何更換儲存體金鑰的相關資訊，請參閱[做法：檢視、複製及重新產生儲存體存取金鑰](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。

##步驟 6：更新媒體服務，開始使用新的主要儲存體金鑰
	
按照[步驟 2](media-services-roll-storage-access-keys.md#step2) 描述的相同程序，這一次只將新的主要儲存體存取金鑰同步到媒體服務帳戶。

>[AZURE.NOTE]等待 30 分鐘，然後透過媒體服務執行任何操作 (例如，建立新定位器)，以免影響到待處理的工作。

##步驟 7：更新定位器  

30 分鐘後，您可以重新建立 OnDemand 定位器，這樣定位器就會對新的主要儲存體金鑰產生相依性，並會維護現有的 URL。

使用[步驟 3](media-services-roll-storage-access-keys.md#step-3-update-locators) 中所述的相同程序。


##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



###通知 

我們想要向下列為建立此文件貢獻心力的人員致謝：Cenk Dingiloglu、Milan Gada、Seva Titov。

<!---HONumber=AcomDC_1217_2015-->