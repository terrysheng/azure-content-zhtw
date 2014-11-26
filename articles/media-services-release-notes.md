<properties pageTitle="媒體服務版本資訊" metaKeywords="Azure Media Services" description="媒體服務版本資訊" metaCanonical="" services="media-services" documentationCenter="Media" title="媒體服務版本資訊" authors="juliako" solutions="media" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="media" ms.devlang="dotnet" ms.topic="article" ms.date="10/15/2014" ms.author="juliako" />

# Azure 媒體服務版本資訊

這些版本資訊彙總了舊版的變更和已知問題。

> [AZURE.NOTE] 我們需要客戶提供意見，並努力修正會對您造成影響的問題。若要回報問題或提問，請在 [Azure 媒體服務 MSDN 論壇][Azure 媒體服務 MSDN 論壇]中貼文。

-   [目前的已知問題][目前的已知問題]
-   [REST API 版本歷程記錄][REST API 版本歷程記錄]
-   [2014 年 10 月版本][2014 年 10 月版本]
-   [2014 年 9 月版本][2014 年 9 月版本]
-   [2014 年 8 月版本][2014 年 8 月版本]
-   [2014 年 7 月版本][2014 年 7 月版本]
-   [2014 年 5 月版本][2014 年 5 月版本]
-   [2014 年 4 月版本][2014 年 4 月版本]
-   [2014 年 1\2 月版本][2014 年 1\2 月版本]
-   [2013 年 12 月版本][2013 年 12 月版本]
-   [2013 年 11 月版本][2013 年 11 月版本]
-   [2013 年 8 月版本][2013 年 8 月版本]
-   [2013 年 6 月版本][2013 年 6 月版本]
-   [2012 年 12 月版本][2012 年 12 月版本]
-   [2012 年 11 月版本][2012 年 11 月版本]
-   [2012 年 6 月預覽版本][2012 年 6 月預覽版本]

## <span id="issues"></span></a>目前的已知問題

### <span id="general_issues"></span></a>媒體服務一般問題

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">問題</th>
<th align="left">說明</yt>
</tr></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">有幾個常用的 HTTP 標頭未提供於 REST API 中。</td>
<td align="left">如果您使用 REST API 開發媒體服務應用程式，您會發現有些常用的 HTTP 標頭欄位 (包括 CLIENT-REQUEST-ID、REQUEST-ID 和 RETURN-CLIENT-REQUEST-ID) 不受支援。這些標頭將在未來的更新中加入。</td>
</tr>
<tr class="even">
<td align="left">使用包含逸出字元 (例如 %20) 的檔案名稱為資產編碼時，作業會失敗，並出現「MediaProcessor：找不到檔案」。</td>
<td align="left">要新增至資產並編碼的檔案，其名稱只能包含英數字元和空格。此問題將在未來的更新中修正。</td>
</tr>
<tr class="odd">
<td align="left">屬於 Azure Storage SDK 3.x 版的 ListBlobs 方法無法運作。</td>
<td align="left">媒體服務會根據 <a href="http://msdn.microsoft.com/zh-tw/library/azure/dn592123.aspx">2012-02-12</a> 版本產生 SAS URL。如果您要使用 Azure Storage SDK 列出 Blob 容器中的 Blob，請使用屬於 Azure Storage SDK 2.x 版的 <a href="http://msdn.microsoft.com/zh-tw/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx">CloudBlobContainer.ListBlobs</a> 方法。屬於 Azure Storage SDK 3.x 版的 ListBlobs 方法將會失敗。</td>
</tr>
<tr class="even">
<td align="left">媒體服務節流機制會針對向服務發出過多要求的應用程式限制資源使用量。服務可能會傳回「服務無法使用 (503)」HTTP 狀態碼。</td>
<td align="left">如需詳細資訊，請在 <a href="http://msdn.microsoft.com/zh-tw/library/azure/dn168949.aspx">Azure 媒體服務錯誤碼</a>主題中參閱 503 HTTP 狀態碼的說明。</td>
</tr>
</tbody>
</table>

### <span id="dotnet_issues"></span></a>Media Services SDK for .NET 問題

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">問題</th>
<th align="left">說明</yt>
</tr></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">SDK 中的媒體服務物件無法序列化，因此無法與 Azure 快取搭配運作。</td>
<td align="left">如果您嘗試序列化 SDK AssetCollection 物件以將其新增至 Azure 快取，將會擲回例外狀況。</td>
</tr>
</tbody>
</table>

## <span id="rest_version_history"></span></a>REST API 版本歷程記錄

如需媒體服務 REST API 版本歷程記錄的相關資訊，請參閱 [Azure 媒體服務 REST API 參考][Azure 媒體服務 REST API 參考]。

## <span id="october_changes_14"></span></a>2014 年 10 月版本

### <span id="new_encoder_release"></span></a>Media Services Encoder 版本

發表新版的 Media Services Azure Media Encoder。使用最新的 Azure Media Encoder 時，您只需要為輸出 GB 付費，但新的編碼程式是與 Windows Azure Media Encoder 相容的功能。如需詳細資訊，請參閱[行動服務定價詳細資料][行動服務定價詳細資料])。

### <span id="oct_sdk"></span></a>媒體服務 .NET SDK

Media Services SDK for .NET 目前的版本為 3.0.0.8。

Media Services SDK for .NET 延伸目前的版本為 2.0.0.3。

## <span id="september_changes_14"></span></a>2014 年 9 月版本

媒體服務 REST 中繼資料目前的版本為 2.7。如需最新 REST 更新的詳細資訊，請參閱 [Azure 媒體服務 REST API 參考][Azure 媒體服務 REST API 參考]。

Media Services SDK for .NET 目前的版本為 3.0.0.7。

### <span id="sept_14_breaking_changes"></span></a>重大變更

-   **原始來源**已重新命名為 [StreamingEndpoint][StreamingEndpoint]。
-   使用 **Azure 管理入口網站**編碼並發行 MP4 檔案的預設行為已有所變更。

    過去，在使用管理入口網站發行單一檔案 MP4 視訊資產時，會建立 SAS URL (SAS URL 可讓您從 Blob 儲存體下載視訊)。現在，當您使用管理入口網站編碼並發行單一檔案 MP4 視訊資產時，產生的 URL 會指向 Azure 媒體服務串流端點。這項變更並不會影響未由 Azure 媒體服務編碼、而直接上傳至媒體服務並發行的 MP4 視訊。

    現在，您有下列兩個選項可以解決問題。

    -   啟用串流單元，並使用動態封裝功能將 .mp4 資產串流處理為 Smooth Streaming 簡報。

    -   建立 SAS URL 以下載 (或累進播放) .mp4。如需如何建立 SAS 定位器的詳細資訊，請參閱[傳遞內容][傳遞內容]。

### <span id="sept_14_GA_changes"></span></a>GA 版本中的新功能\\案例

-   **索引器媒體處理器**。如需詳細資訊，請參閱[使用 Azure 媒體索引器編製媒體檔案的索引][使用 Azure 媒體索引器編製媒體檔案的索引]。

-   [StreamingEndpoint][StreamingEndpoint] 實體現在可讓您新增自訂網域 (主機) 名稱。

    對於要作為媒體服務串流端點名稱的自訂網域名稱，您必須將自訂主機名稱新增至您的串流端點。請使用媒體服務 REST API 或 .NET SDK 來新增自訂主機名稱。

    您必須考量下列事項：

    -   您必須具有自訂網域名稱的擁有權。

    -   網域名稱的擁有權必須通過 Azure 媒體服務的驗證。

    如需詳細資訊，請參閱 [StreamingEndpoint][StreamingEndpoint] 主題中的 **CustomHostNames** 屬性。

### <span id="sept_14_preview_changes"></span></a>公用預覽版本中的新功能\\案例

-   即時資料流預覽。如需詳細資訊，請參閱[使用 Azure 媒體服務即時資料流][使用 Azure 媒體服務即時資料流]。

-   金鑰傳遞服務。如需詳細資訊，請參閱[使用 AES-128 動態加密和金鑰傳遞服務][使用 AES-128 動態加密和金鑰傳遞服務]。

-   AES 動態加密。如需詳細資訊，請參閱[使用 AES-128 動態加密和金鑰傳遞服務][使用 AES-128 動態加密和金鑰傳遞服務]。

-   PlayReady 授權傳遞服務。如需詳細資訊，請參閱[使用 PlayReady 動態加密和授權傳遞服務][使用 PlayReady 動態加密和授權傳遞服務]。

-   PlayReady 動態加密。如需詳細資訊，請參閱[使用 PlayReady 動態加密和授權傳遞服務][使用 PlayReady 動態加密和授權傳遞服務]。

-   媒體服務 PlayReady 授權範本。如需詳細資訊，請參閱[媒體服務 PlayReady 授權範本概觀][媒體服務 PlayReady 授權範本概觀]。

-   串流儲存體加密資產。如需詳細資訊，請參閱[串流儲存體加密內容][串流儲存體加密內容]。

## <span id="august_changes_14"></span></a>2014 年 8 月版本

當您為資產編碼時，在完成編碼工作時將會產生輸出資產。在此版本之前，Azure Media Services Encoder 會產生輸出資產的相關中繼資料。自此版本開始，編碼程式會同時產生輸入資產的相關中繼資料。如需詳細資訊，請參閱[輸入中繼資料][輸入中繼資料]和[輸出中繼資料][輸出中繼資料]主題。

## <span id="july_changes_14"></span></a>2014 年 7 月版本

Azure Media Services Packager 和 Encryptor 完成了下列錯誤修正：

-   將即時封存資產多工轉換為 HTTP 即時資料流時，只會播放音訊 – 此問題已獲得修正，現在已會同時播放音訊和視訊。

-   將資產封裝為 HTTP 即時資料流和 AES 128 位元信封加密時，封裝的串流無法在 Android 裝置上播放 – 此問題已獲得修正，封裝的串流已可在支援 HTTP 即時資料流的 Android 裝置上播放。

## <span id="may_changes_14"></span></a>2014 年 5 月版本

### <span id="may_14_changes"></span></a>一般媒體服務更新

您現在可以使用[動態封裝][動態封裝]來串流處理 HTTP 即時資料流 (HLS) v3。若要串流處理 HLS v3，請將下列格式新增至原始定位器路徑：\*.ism/manifest(format=m3u8-aapl-v3)。如需詳細資訊，請參閱 [Nick Drouin 的部落格][Nick Drouin 的部落格]。

動態封裝現在也支援根據使用 PlayReady 靜態加密的 Smooth Streaming，來傳遞使用 PlayReady 加密的 HLS (v3 和 v4)。如需如何使用 PlayReady 為 Smooth Streaming 加密的相關資訊，請參閱[使用 PlayReady 保護 Smooth Stream][使用 PlayReady 保護 Smooth Stream]。

### <a name="may_14_donnet_changes"></a>媒體服務 .NET SDK 更新

媒體服務 .NET SDK 3.0.0.5 版包含以下改良：

-   上傳/下載媒體資產的速度和恢復能力優於以往。

-   重試邏輯和暫時性例外狀況處理已有改善：

    -   因查詢、儲存變更、上傳或下載檔案而造成的例外狀況，在暫時性錯誤偵測和重試邏輯方面已有改善。

    -   當 Web 例外狀況出現時 (例如，在 ACS 權杖要求期間)，您會發現嚴重錯誤現在的失效速度已變快。

如需詳細資訊，請參閱 [Media Services SDK for .NET 中的重試邏輯][Media Services SDK for .NET 中的重試邏輯]。

## <span id="april_changes_14"></span></a>2014 年 4 月編碼程式版本

### <a name="april_14_enocer_changes"></a>Media Services Encoder 更新

-   已新增對使用 Grass Valley EDIUS 非線性編輯器撰寫的 AVI 檔案進行擷取的支援；其中的視訊會使用 Grass Valley HQ/HQX 轉碼器略為壓縮。如需詳細資訊，請參閱 [Grass Valley 發表透過雲端的 EDIUS 7 Streaming][Grass Valley 發表透過雲端的 EDIUS 7 Streaming]。

-   新增了為 Media Encoder 所產生的檔案指定命名慣例的支援。如需詳細資訊，請參閱[控制 Media Service Encoder 輸出檔案名稱][控制 Media Service Encoder 輸出檔案名稱]。

-   新增了視訊和 (或) 音訊重疊的支援。如需詳細資訊，請參閱[建立重疊][建立重疊]。

-   新增了結合多個視訊片段的支援。如需詳細資訊，請參閱[結合視訊片段][結合視訊片段]。

-   修正了與 MP4 相關的轉碼；其中，音訊是使用 MPEG-1 Audio Layer 3 (aka MP3) 編碼的。

## <span id="jan_feb_changes_14"></span></a>2014 年 1\\2 月版本

### <a name="jan_fab_14_donnet_changes"></a>Azure 媒體服務 .NET SDK 3.0.0.1、3.0.0.2 和 3.0.0.3

3.0.0.1 和 3.0.0.2 中的變更包括：

-   修正了使用 OrderBy 陳述式進行 LINQ 查詢的用法相關問題。

-   將 [Github][Github] 中的測試解決方案分成單元測試和案例測試。

如需這些變更的詳細資訊，請參閱：[Azure 媒體服務 .NET SDK 3.0.0.1 和 3.0.0.2 版][Azure 媒體服務 .NET SDK 3.0.0.1 和 3.0.0.2 版]。

3.0.0.3 中做了下列變更：

-   已升級 Azure 儲存體相依性而使用 3.0.3.0 版。

-   已修正 3.0.*.* 版的回溯相容性問題。

## <span id="december_changes_13"></span></a>2013 年 12 月版本

### <a name="dec_13_donnet_changes"></a>Azure 媒體服務 .NET SDK 3.0.0.0

> [AZURE.NOTE] 3.0.x.x 版不具備回溯至 2.4.x.x 版的相容性。

媒體服務 SDK 目前的最新版本為 3.0.0.0。您可以從 Nuget 下載最新套件，或從 [Github][Github] 取得。

從媒體服務 SDK 3.0.0.0 版開始，您可以重複使用 [Azure Active Directory 存取控制服務 (ACS)][Azure Active Directory 存取控制服務 (ACS)] 權杖。如需詳細資訊，請參閱[使用 Media Services SDK for .NET 連接到媒體服務][使用 Media Services SDK for .NET 連接到媒體服務]主題中的「重複使用存取控制服務權杖」一節。

### <a name="dec_13_donnet_ext_changes"></a>Azure 媒體服務 .NET SDK 延伸 2.0.0.0

Azure 媒體服務 .NET SDK 延伸是一組延伸方法和協助程式函數，可簡化您的程式碼以及使用 Azure 媒體服務進行開發的工作。您可以從 [Azure 媒體服務 .NET SDK 延伸][Azure 媒體服務 .NET SDK 延伸]取得最新版本。

## <span id="november_changes_13"></span></a>2013 年 11 月版本

### <a name="nov_13_donnet_changes"></a>Azure 媒體服務 .NET SDK 變更

自此版本起，Media Services SDK for .NET 已可處理在呼叫媒體服務 REST API 層時可能發生的暫時性錯誤。

## <span id="august_changes_13"></span></a>2013 年 8 月版本

### <a name="aug_13_powershell_changes"></a>Azure Sdk 工具中包含的媒體服務 PowerShell Cmdlet

[azure-sdk-tools][azure-sdk-tools] 中現在包含下列媒體服務 PowerShell Cmdlet。

-   Get-AzureMediaServices

    例如，`Get-AzureMediaServicesAccount`。

-   New-AzureMediaServicesAccount

    例如，`New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`。

-   New-AzureMediaServicesKey

    例如，`New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`。

-   Remove-AzureMediaServicesAccount

    例如，`Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`。

## <span id="june_changes_13"></span></a>2013 年 6 月版本

### <a name="june_13_general_changes"></a>Azure 媒體服務變更

本節說明的變更是 2013 年 6 月媒體服務版本所包含的更新。

-   能夠將多個儲存體帳戶連結至媒體服務帳戶。

    StorageAccount

    Asset.StorageAccountName 和 Asset.StorageAccount

-   能夠更新 Job.Priority。

-   實體和屬性的相關通知：

    JobNotificationSubscription

    NotificationEndPoint

    Job

-   Asset.Uri

-   Locator.Name

### <a name="june_13_dotnet_changes"></a>Azure 媒體服務 .NET SDK 變更

2013 年 6 月媒體服務 SDK 版本包含下列變更。GitHub 提供的最新媒體服務 SDK。

-   自 2.3.0.0 版起，媒體服務 SDK 已可支援將多個儲存體帳戶連結至媒體服務帳戶的作業。支援此功能的 API 如下：

    IStorageAccount 類型。

    Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts 屬性。

    StorageAccount 屬性。

    StorageAccountName 屬性。

    如需詳細資訊，請參閱[管理多個儲存體帳戶間的媒體服務資產][管理多個儲存體帳戶間的媒體服務資產]。

-   API 的相關通知。自 2.2.0.0 版起，您已能夠聽取 Azure 佇列儲存體通知。如需詳細資訊，請參閱[處理媒體服務工作通知][處理媒體服務工作通知]。

    Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions 屬性。

    Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint 類型。

    Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription 類型。

    Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection 類型。

    Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType 類型。

    Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState 類型。

-   對 Azure Storage Client SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll) 的相依性。

-   對 OData 5.5 (Microsoft.Data.OData.dll) 的相依性。

## <span id="december_changes_12"></span></a>2012 年 12 月版本

### <a name="dec_12_dotnet_changes"></a>Azure 媒體服務 .NET SDK 變更

-   Intellisense：新增了許多類型遺漏的 Intellisense 文件。

-   Microsoft.Practices.TransientFaultHandling.Core：修正了 SDK 對此組件的舊版仍有相依性的問題。SDK 現已參考此組件的 5.1.1209.1 版。

修正在 2012 年 11 月 SDK 中發現的問題：

-   IAsset.Locators.Count：現在，當所有定位器刪除後，此計數已會正確地在新的 IAsset 介面上報告。

-   IAssetFile.ContentFileSize：現在，當 IAssetFile.Upload(filepath) 完成上傳後，此值已會正確設定。

-   IAssetFile.ContentFileSize：現在，此屬性已可在建立資產檔案後設定。此檔案過去是唯讀的。

-   IAssetFile.Upload(filepath)：已修正此同步上傳方法在將多個檔案上傳至資產時會擲回下列錯誤的問題。此錯誤為「伺服器無法驗證要求。請確定授權標頭的值 (包括簽章在內) 格式正確。」

-   IAssetFile.UploadAsync：已修正無法同時上傳超過 5 個檔案的問題。

-   IAssetFile.UploadProgressChanged：SDK 現在已提供此事件。

-   IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken)：現在已提供此方法多載。

-   IAssetFile.DownloadAsync：已修正無法同時下傳超過 5 個檔案的問題。

-   IAssetFile.Delete()：已修正若未針對 IAssetFile 上傳檔案，呼叫刪除時即會擲回例外狀況的問題。

-   Jobs：已修正在使用工作範本鏈結「MP4 至 Smooth Streaming 工作」與「PlayReady 保護工作」時不會建立任何工作的問題。

-   EncryptionUtils.GetCertificateFromStore()：此方法已不會再因為無法根據憑證組態問題找到憑證而擲回 Null 參考例外狀況。

## <span id="november_changes_12"></span></a>2012 年 11 月版本

本節說明的變更是 2012 年 11 月 (2.0.0.0 版) SDK 所包含的更新。進行這些變更時，可能必須修改或重寫為 2012 年 6 月預覽 SDK 版本撰寫的程式碼。

-   Assets

    IAsset.Create(assetName) 是唯一的資產建立函數。IAsset.Create 已不會在方法呼叫期間上傳檔案。請使用 IAssetFile 進行上傳。

    IAsset.Publish 方法和 AssetState.Publish 列舉值已從服務 SDK 中移除。任何依存於此值的程式碼都必須重寫。

-   FileInfo

    此類別已移除，並由 IAssetFile 取代。

    IAssetFiles

    IAssetFile 已取代 FileInfo，且具有不同的行為。若要加以使用，請具現化 IAssetFiles 物件，然後使用媒體服務 SDK 或 Azure 儲存體 SDK 進行檔案上傳。您可以使用下列 IAssetFile.Upload 多載：

    -   IAssetFile.Upload(filePath)：一個會封鎖執行緒的同步方法，建議僅在上傳單一檔案時使用。

    -   IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken)：一個非同步方法。這是一般常用的上傳機制。

    已知錯誤：使用 cancellationToken 將可實際取消上傳；但工作的取消狀態可能會是多種狀態的任一種。您必須正確捕捉並處理例外狀況。

-   定位器

    原始來源的特定版本已移除。SAS 特定 context.Locators.CreateSasLocator(asset, accessPolicy) 即將由 GA 標示為過時或移除。如需更新行為的相關資訊，請參閱「新功能」下的「定位器」一節。

## <span id="june_changes_12"></span></a>2012 年 6 月預覽版本

以下是 SDK 的 11 月版本中包含的新功能。

-   刪除實體

    IAsset、IAssetFile、ILocator、IAccessPolicy、IContentKey 物件現已會在物件層級上刪除 (即 IObject.Delete())，而無須在「集合」中刪除 (即 cloudMediaContext.ObjCollection.Delete(objInstance))。

-   定位器

    定位器現在必須使用 CreateLocator 方法來建立，並使用 LocatorType.SAS 或 LocatorType.OnDemandOrigin 列舉值作為您要建立之特定定位器類型的引數。

    定位器已新增新的屬性，以便取得您的內容可使用的 URI。定位器的這項重新設計旨在為日後的協力廠商擴充能力提供更大的彈性，以及提高媒體用戶端應用程式的易用性。

-   非同步方法支援

    所有方法皆已新增非同步支援。

  

  [Azure 媒體服務 MSDN 論壇]: http://social.msdn.microsoft.com/forums/azure/zh-tw/home?forum=MediaServices
  [目前的已知問題]: #issues
  [REST API 版本歷程記錄]: #rest_version_history
  [2014 年 10 月版本]: #october_changes_14
  [2014 年 9 月版本]: #september_changes_14
  [2014 年 8 月版本]: #august_changes_14
  [2014 年 7 月版本]: #july_changes_14
  [2014 年 5 月版本]: #may_changes_14
  [2014 年 4 月版本]: #april_changes_14
  [2013 年 12 月版本]: #december_changes_13
  [2013 年 11 月版本]: #november_changes_13
  [2013 年 8 月版本]: #august_changes_13
  [2013 年 6 月版本]: #june_changes_13
  [2012 年 12 月版本]: #december_changes_12
  [2012 年 11 月版本]: #november_changes_12
  [2012 年 6 月預覽版本]: #june_changes_12
  [Azure 媒體服務 REST API 參考]: http://msdn.microsoft.com/zh-tw/library/azure/hh973617.aspx
  [行動服務定價詳細資料]: http://azure.microsoft.com/zh-tw/pricing/details/media-services/
  [StreamingEndpoint]: http://msdn.microsoft.com/zh-tw/library/azure/dn783468.aspx
  [傳遞內容]: http://msdn.microsoft.com/zh-tw/library/azure/hh973618.aspx
  [使用 Azure 媒體索引器編製媒體檔案的索引]: http://msdn.microsoft.com/zh-tw/library/azure/dn783455.aspx
  [使用 Azure 媒體服務即時資料流]: http://msdn.microsoft.com/zh-tw/library/azure/dn783466.aspx
  [使用 AES-128 動態加密和金鑰傳遞服務]: http://msdn.microsoft.com/zh-tw/library/azure/dn783457.aspx
  [使用 PlayReady 動態加密和授權傳遞服務]: http://msdn.microsoft.com/zh-tw/library/azure/dn783467.aspx
  [媒體服務 PlayReady 授權範本概觀]: http://msdn.microsoft.com/zh-tw/library/azure/dn783459.aspx
  [串流儲存體加密內容]: http://msdn.microsoft.com/zh-tw/library/azure/dn783451.aspx
  [輸入中繼資料]: http://msdn.microsoft.com/zh-tw/library/azure/dn783120.aspx
  [輸出中繼資料]: http://msdn.microsoft.com/zh-tw/library/azure/dn783217.aspx
  [動態封裝]: http://msdn.microsoft.com/zh-tw/library/azure/jj889436.aspx
  [Nick Drouin 的部落格]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
  [使用 PlayReady 保護 Smooth Stream]: http://msdn.microsoft.com/zh-tw/library/azure/dn189154.aspx
  [Media Services SDK for .NET 中的重試邏輯]: http://msdn.microsoft.com/zh-tw/library/azure/dn745650.aspx
  [Grass Valley 發表透過雲端的 EDIUS 7 Streaming]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
  [控制 Media Service Encoder 輸出檔案名稱]: http://msdn.microsoft.com/zh-tw/library/azure/dn303341.aspx
  [建立重疊]: http://msdn.microsoft.com/zh-tw/library/azure/dn640496.aspx
  [結合視訊片段]: http://msdn.microsoft.com/zh-tw/library/azure/dn640504.aspx
  [Github]: https://github.com/Azure/azure-sdk-for-media-services
  [Azure 媒體服務 .NET SDK 3.0.0.1 和 3.0.0.2 版]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
  [Azure Active Directory 存取控制服務 (ACS)]: http://msdn.microsoft.com/zh-tw/library/hh147631.aspx
  [使用 Media Services SDK for .NET 連接到媒體服務]: http://msdn.microsoft.com/zh-tw/library/azure/jj129571.aspx
  [Azure 媒體服務 .NET SDK 延伸]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
  [azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
  [管理多個儲存體帳戶間的媒體服務資產]: http://msdn.microsoft.com/zh-tw/library/azure/dn271889.aspx
  [處理媒體服務工作通知]: http://msdn.microsoft.com/zh-tw/library/azure/dn261241.aspx
