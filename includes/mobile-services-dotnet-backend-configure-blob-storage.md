
已更新現有的 **TodoItemController**，因此 **PostTodoItem** 方法會在插入新 Todo 項目時產生 SAS。

1.  如果您尚未建立儲存體帳戶，請參閱[如何建立儲存體帳戶](/en-us/manage/services/storage/how-to-create-a-storage-account)。

2.  在管理入口網站中，按一下 **[儲存體]**、儲存體帳戶和 **[管理金鑰]**。

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account.png)

1.  請記下 **[儲存體帳戶名稱]** 和 **[存取金鑰]**。

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png)

2.  在行動服務中，按一下 **[設定]** 索引標籤，向下捲動至 **[應用程式設定]**，並輸入您從儲存體帳戶取得之下列各項的 **[名稱]** 和 **[值]** 組，然後按一下 **[儲存]**。

    -   `STORAGE_ACCOUNT_NAME`
    -   `STORAGE_ACCOUNT_ACCESS_KEY`

    ![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

    儲存體帳戶存取金鑰會以加密方式儲存在應用程式設定中。您可以在執行期間從任何伺服器指令碼存取此金鑰。如需詳細資訊，請參閱[應用程式設定](http://msdn.microsoft.com/en-us/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7)。

3.  在 Visual Studio 的 [方案總管] 中，為行動服務專案開啟 Web.config 檔案，並新增下列新應用程式設定，以您在入口網站中設為應用程式設定的儲存體帳戶名稱和存取金鑰來取代預留位置：

         <add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
        <add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

    在本機電腦上執行行動服務時，該服務會使用這些儲存的設定，讓您先測試程式碼再發佈。在 Azure 中執行時，行動服務會改用入口網站中設定的應用程式設定值，並忽略這些專案設定。

4.  在 Controllers 資料夾中，開啟 TodoItemController.cs 檔案並新增下列 **using** 指示詞：

        using System;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Blob;

5.  使用下列程式碼來取代現有的 **PostTodoItem** 方法：

        var azure = require('azure');
        var qs = require('querystring');
        var appSettings = require('mobileservice-config').appSettings;
            
        function insert(item, user, request) {
        // Get storage account settings from app settings. 
        var accountName = appSettings.STORAGE_ACCOUNT_NAME;
        var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
        var host = accountName + '.blob.core.windows.net';
            
        if ((typeof item.containerName !== "undefined") && (
        item.containerName !== null)) {
        // Set the BLOB store container name on the item, which must be lowercase.
        item.containerName = item.containerName.toLowerCase();
            
        // If it does not already exist, create the container 
        // with public read access for blobs.        
        var blobService = azure.createBlobService(accountName, accountKey, host);
        blobService.createContainerIfNotExists(item.containerName, {
        publicAccessLevel:'blob'
        }, function(error) {
        if(!error){
            
        // Provide write access to the container for the next 5 mins.        
        var sharedAccessPolicy = {
        AccessPolicy: {
        Permissions:azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
        Expiry:new Date(new Date().getTime() + 5 * 60 * 1000)
                            }
                        };
            
        // Generate the upload URL with SAS for the new image.
        var sasQueryUrl = 
        blobService.generateSharedAccessSignature(item.containerName, 
        item.resourceName, sharedAccessPolicy);
            
        // Set the query string.
        item.sasQueryString = qs.stringify(sasQueryUrl.queryString);
            
        // Set the full path on the new new item, 
        // which is used for data binding on the client. 
        item.imageUri = sasQueryUrl.baseUrl + sasQueryUrl.path;
            
        } else {
        console.error(error);
                    }
        request.execute();
                });
        } else {
        request.execute();
            }
        }

	![](./media/mobile-services-configure-blob-storage/mobile-insert-script-blob.png) 

	這會以新指令碼取代 TodoItem 資料表發生插入時所叫用的函數。這個新指令碼會對插入產生新的 SAS (有效期間為 5 分鐘)，以及將產生的 SAS 值指派給所傳回項目的 `sasQueryString` 屬性。`imageUri` 屬性也會設定為新 BLOB 的資源路徑，以便在用戶端 UI 中繫結期間顯示映像。

    >[WACOM.NOTE] 此程式碼會為個別 BLOB 建立 SAS。如果您需要使用相同的 SAS 將多個 BLOB 上傳至容器，可以用空白的 BLOB 資源名稱呼叫 <a href="http://go.microsoft.com/fwlink/ LinkId=390455" target="_blank">generateSharedAccessSignature method</a>，如下所示： 
    <pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

接著，您將會更新快速入門應用程式，以使用插入時產生的 SAS 來新增映像上傳功能。

