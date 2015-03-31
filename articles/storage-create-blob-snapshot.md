<properties 
	pageTitle="建立 Blob 的快照集" 
	description="建立 Azure 儲存體 Blob 快照集的做法指南" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

#建立 Blob 的快照集

您可以建立 Blob 的快照集。快照集是在某個時間點取得的 Blob 唯讀版本。一旦建立快照集後，即可加以讀取、複製或刪除，但不能修改。快照集提供在某個時間點備份 Blob 的方法。

Blob 的快照集名稱與從中擷取該快照集的基底 Blob 名稱相同，其中並附加了日期時間值，以表示擷取快照當時的時間。例如，如果分頁 Blob URI 為 http://storagesample.core.blob.windows.net/mydrives/myvhd，則快照集 URI 將類似 http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z。此值可能用來參考快照集，以便在進一步作業時使用。Blob 的快照集會共用它的 URI，而且只能透過這個日期時間值來區分。在用戶端程式庫的程式碼中，Blob 的快照集屬性會傳回日期時間值，可唯一識別相對於其基底 Blob 的快照集。您可以使用此值，在快照集上執行進一步作業。

Blob 可包含任意數目的快照集。快照集會保留，直到您明確刪除它們為止。快照集的存留期間無法超越其來源 Blob。您可以列舉與 Blob 相關聯的快照集，以追蹤目前的快照集。

##繼承屬性

當您建立 Blob 的快照集時，系統屬性都會使用相同值複製到快照集中。此清單顯示針對 .NET 儲存體用戶端程式庫複製的系統屬性：

- ContentType 

- ContentEncoding 

- ContentLanguage

- Length

- CacheControl

- ContentMd5 


與基底 Blob 相關聯的租用不會複製到快照集。快照集無法租用。

##複製快照集 

涉及 Blob 和快照集的複製作業會遵循下列規則：

- 您可以將快照集複製到其基底 Blob 之上。藉由將快照集升級到基底 Blob 的位置，您可以還原舊版的 Blob。快照集會保留，但您能使用可同時讀取和寫入的複本來覆寫它的來源。

- 您可以將快照集複製到不同名稱的目的地 Blob。所產生的目的地 Blob 是可寫入的 Blob，而不是快照集。

- 複製來源 Blob 時，不會將來源 Blob 的任何快照集複製到目的地。使用某個複本覆寫目的地 Blob 時，與目的地 Blob 相關聯的任何快照集都會讓名稱保持不變。 

- 當您建立區塊 Blob 的快照集時，該 Blob 的認可區塊清單也會複製到快照集。系統不會複製任何未認可的區塊。

##指定存取條件 

您可以指定存取條件，如此一來，只有在符合條件時，才會建立快照集。若要指定存取條件，請使用 AccessCondition 屬性。如果不符合指定的條件，就不會建立快照集，而且 Blob 服務會傳回狀態碼 HTTPStatusCode.PreconditionFailed。

##刪除快照集 

除非快照集也會一併刪除，否則無法刪除具有這些快照集的 Blob。您可以個別刪除快照集，或告知儲存體服務在刪除來源 Blob 時刪除所有快照集。如果您嘗試刪除仍具有快照集的 Blob，您的呼叫將傳回錯誤。

##快照集與高階儲存體
搭配使用快照集與高階儲存體需遵循下列規則：

- 高階儲存體帳戶中每個分頁 Blob 的快照集數目限制為 100 個。如果超出該限制，快照集 Blob 作業就會傳回錯誤碼 409 (SnapshotCountExceeded)。

- 高階儲存體帳戶中分頁 Blob 的快照集可能每十分鐘擷取一次。如果超出該頻率，快照集 Blob 作業就會傳回錯誤碼 409 (SnaphotOperationRateExceeded)。

- 不支援透過取得 Blob 來讀取高階儲存體帳戶中分頁 Blob 的快照集。在高階儲存體帳戶的快照集上呼叫取得 Blob 會傳回錯誤碼 400 (無效的作業)。不過，支援針對快照集呼叫取得 Blob 屬性和取得 Blob 中繼資料。

- 若要讀取快照集，您可以使用複製 Blob 作業，將快照集複製到帳戶中的其他分頁 Blob。複製作業的目的地 Blob 不可以包含任何現有的快照集。如果目的地 Blob 具有快照集，則複製 Blob 會傳回錯誤碼 409 (SnapshotsPresent)。

##建構快照集的絕對 URI 

這個程式碼範例會建構來自其基底 Blob 物件的快照集絕對 URI。

C#

	var snapshot = blob.CreateSnapshot();
	var uri = Microsoft.WindowsAzure.StorageClient.Protocol.BlobRequest.Get
    (snapshot.Uri, 
    0, 
    snapshot.SnapshotTime.Value, 
    null).Address.AbsoluteUri;

<!--HONumber=47-->
