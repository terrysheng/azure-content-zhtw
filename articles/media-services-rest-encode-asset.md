<properties 
	pageTitle="如何使用 Azure Media Encoder 為資產編碼" 
	description="了解如何使用 Azure Media Encoder 為媒體服務上的媒體內容編碼。程式碼範例會使用 REST API。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="juliako"/>


# 如何使用 Azure Media Encoder 為資產編碼

這篇文章是[媒體服務點播視訊工作流程](media-services-video-on-demand-workflow.md)系列的一部分。 

## 概觀
若要透過網際網路傳遞數位視訊，您必須壓縮媒體。數位視訊檔案十分龐大，而且可能太大而無法透過網際網路傳遞，或是太大而使您客戶的裝置無法正確顯示。編碼是壓縮視訊和音訊，好讓客戶能檢視您的媒體的程序。

編碼工作是媒體服務中最常見的處理作業。您建立編碼工作以將媒體檔案從一種編碼轉換成另一種編碼。編碼時，您可以使用媒體服務內建的 Media Encoder。您也可以使用媒體服務合作夥伴提供的編碼器；第三方編碼器可透過 Azure Marketplace 取得。您可以使用針對您的編碼器定義的預設字串，或使用預設組態檔，指定編碼工作的詳細資料。若要查看可用的預設類型，請參閱＜Azure 媒體服務的工作預設＞。如果您使用第三方編碼器，則應該[驗證檔案](https://msdn.microsoft.com/library/azure/dn750842.aspx)。

建議一律將夾層檔編碼為調適性位元速率 MP4 集，然後使用[動態封裝](https://msdn.microsoft.com/library/azure/jj889436.aspx)將該集合轉換為所要的格式。

如果您的輸出資產是儲存體加密，必須設定資產傳遞原則。如需詳細資訊，請參閱[設定資產傳遞原則](media-services-rest-configure-asset-delivery-policy.md)。

## 建立具有單一編碼工作的工作 

>[AZURE.NOTE] 使用媒體服務 REST API 時，適用下列考量事項：
>
>在媒體服務中存取實體時，您必須在 HTTP 要求中設定特定的標頭欄位和值。如需詳細資訊，請參閱[媒體服務 REST API 開發的設定](media-services-rest-how-to-use.md)。

>順利連接到 https://media.windows.net 後，您會收到指定另一個媒體服務 URI 的 301 重新導向。後續的呼叫必須向新的 URI 提出，如[使用 REST API 連接至媒體服務](media-services-rest-connect_programmatically.md)中所述。 


下列範例會示範如何建立和張貼工作，並將一個工作設為在特定的解析度與品質將視訊編碼。當透過 Azure Media Encoder 編碼時，您可以使用[這裡](https://msdn.microsoft.com/library/azure/dn619389.aspx)指定的工作組態預設。
	
要求：

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d
	Host: media.windows.net
	Content-Length: 476
	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

回應：
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1017
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: d2052a71-95b1-4a52-9420-ccca1202a5fb
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Fri, 11 May 2012 21:32:40 GMT
	
	{"d":{"__metadata":{"id":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"},"Tasks":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/Tasks"}},"OutputMediaAssets":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/OutputMediaAssets"}},"InputMediaAssets":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/InputMediaAssets"}},"Id":"nb:jid:UUID:40dc7bef-6bd9-2247-9f3d-d80bc257d715","Name":"NewTestJob","Created":"/Date(1336771959431)/","LastModified":"/Date(1336771959431)/","EndTime":null,"Priority":0,"RunningDuration":0,"StartTime":null,"State":0,"TemplateId":null}}

## 建立具有鏈結工作的工作

在許多應用程式案例中，開發人員想要建立一連串的處理工作。在媒體服務中，您可以建立一連串的鏈結工作。每一項工作會執行不同的處理步驟，而且可以使用不同的媒體處理器。鏈結工作可以將資產從一個工作遞交到另一個工作，對資產執行一連串的工作。不過，在工作中執行的工作不必按照順序。當您建立鏈結工作時，鏈結的 **ITask** 物件會建立在單一 **IJob** 物件中。

>[AZURE.NOTE] 目前有每個工作裡 30 個工作的限制。如果您需要鏈結超過 30 個工作，請建立多個工作來包含這些工作。


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-4e75-2233-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-16"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}


## 後續步驟
現在，您知道如何建立編碼資產的工作 (Job)，請移至[如何使用媒體服務檢查工作進度](media-services-rest-check-job-progress.md)主題。

[Azure Marketplace]: https://datamarket.azure.com/
[編碼器預設]: http://msdn.microsoft.com/library/dn619392.aspx
[做法：取得媒體處理器執行個體]:http://go.microsoft.com/fwlink/?LinkId=301732
[做法：上傳加密的資產]:http://go.microsoft.com/fwlink/?LinkId=301733
[做法：透過下載來傳遞資產]:http://go.microsoft.com/fwlink/?LinkId=301734
[如何檢查工作進度]:http://go.microsoft.com/fwlink/?LinkId=301737
[Azure Media Packager 的工作預設]:http://msdn.microsoft.com/library/windowsazure/hh973635.aspx


<!--HONumber=52-->