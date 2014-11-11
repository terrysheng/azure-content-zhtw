<properties linkid="dev-ruby-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Ruby) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Ruby" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Blob Service from Ruby" authors="guayan" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# 如何使用 Ruby 的 Blob 服務

本指南將示範如何使用 Azure Blob 服務執行一般案例。這些範例使用 Ruby API 撰寫。所涵蓋的案例包括「上傳」、「列出」、「下載」和「刪除」Blob。如需 Blob 的詳細資訊，請參閱[後續步驟][後續步驟]一節。

## 目錄

-   [什麼是 Blob 服務？][什麼是 Blob 服務？]
-   [概念][概念]
-   [建立 Azure 儲存體帳戶][建立 Azure 儲存體帳戶]
-   [建立 Ruby 應用程式][建立 Ruby 應用程式]
-   [設定您的應用程式以存取儲存體][設定您的應用程式以存取儲存體]
-   [設定 Azure 儲存體連接][設定 Azure 儲存體連接]
-   [作法：建立容器][作法：建立容器]
-   [作法：將 Blob 上傳至容器][作法：將 Blob 上傳至容器]
-   [作法：列出容器中的 Blob][作法：列出容器中的 Blob]
-   [作法：下載 Blob][作法：下載 Blob]
-   [作法：刪除 Blob][作法：刪除 Blob]
-   [後續步驟][1]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <span id="CreateAccount"></span></a>建立 Azure 儲存體帳戶

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <span id="CreateRubyApp"></span></a>建立 Ruby 應用程式

建立 Ruby 應用程式。如需指示，請參閱[在 Azure 上建立 Ruby 應用程式][在 Azure 上建立 Ruby 應用程式] (英文)。

## <span id="ConfigAccessStorage"></span></a>設定您的應用程式以存取儲存體

若要使用 Azure 儲存體，您需要下載並使用 Ruby azure 套件，這包含一組便利程式庫，能與儲存體 REST 服務通訊。

### 使用 RubyGems 來取得套件

1.  使用命令列介面，例如 **PowerShell** (Windows)、**Terminal** (Mac) 或 **Bash** (Unix)。

2.  在命令視窗中鍵入 "gem install azure" 以安裝 Gem 和相依性。

### 匯入封裝

使用您偏好的文字編輯器，將以下內容新增至您打算使用儲存體的 Ruby 檔案頂端：

    require "azure"

## <span id="SetupStorageConnection"></span></a>設定 Azure 儲存體連接

azure 模組會讀取環境變數 **AZURE\_STORAGE\_ACCOUNT** 及 **AZURE\_STORAGE\_ACCESS\_KEY**，以取得連接 Azure 儲存體帳戶所需的資訊。如果尚未設定這些環境變數，您必須使用下列程式碼，在使用 **Azure::BlobService** 之前指定帳戶資訊：

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

若要取得這些值，請執行下列動作：

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。
2.  瀏覽到您要使用的儲存體帳戶
3.  按一下導覽窗格底部的 [管理金鑰]。
4.  在快顯對話方塊中，您將會看到儲存體帳戶名稱、主要存取金鑰和次要存取金鑰。如需存取金鑰，您可使用主要存取金鑰或次要存取金鑰。

## <span id="CreateContainer"></span></a>作法：建立容器

**Azure::BlobService** 物件可讓您操作容器及 Blob。若要建立容器，請使用 **create\_container()** 方法。

下列範例將建立容器或列印錯誤訊息 (若有的話)。

    azure_blob_service = Azure::BlobService.new
    begin
      container = azure_blob_service.create_container("test-container")
    rescue
      puts $!
    end

如果您想讓容器中的檔案成為公用性質，可以設定容器的權限。

您可以只修改 **create\_container()** 呼叫以傳遞 **:public\_access\_level** 選項：

    container = azure_blob_service.create_container("test-container", 
      :public_access_level => "<public access level>")

**:public\_access\_level** 選項的有效值包括：

-   **blob：** 指定容器和 Blob 資料的完整公用讀取權限。用戶端可以透過匿名要求列舉容器內的 Blob，但無法列舉儲存體帳戶內的容器。

-   **容器：** 指定 Blob 的公用讀取權限。您可以透過匿名要求讀取此容器內的 Blob 資料，但您無法使用容器資料。用戶端無法透過匿名要求列舉容器內的 Blob。

或者，您可以使用 **set\_container\_acl()** 方法指定公用存取等級，藉此修改容器的公用存取等級。

下列範例會將公用存取等級變更為 **container**：

    azure_blob_service.set_container_acl('test-container', "container")

## <span id="UploadBlob"></span></a>作法：將 Blob 上傳至容器

若要將內容上傳至 Blob，請使用 **create\_block\_blob()** 方法建立 Blob，使用檔案或字串作為 Blob 的內容。

下列程式碼將上傳檔案 **test.png** 作為容器中的新 Blob (名為 "image-blob")。

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

## <span id="ListBlobs"></span></a>作法：列出容器中的 Blob

若要列出容器，請使用 **list\_containers()** 方法。若要列出容器內的 Blob，請使用 **list\_blobs()** 方法。

這會輸出該帳戶所有容器中所有 Blob 的 URL。

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

## <span id="DownloadBlobs"></span></a>作法：下載 Blob

若要下載 Blob，請使用 **get\_blob()** 方法以擷取內容。

下列範例示範使用 **get\_blob()** 來下載 "image-blob" 的內容，並將它寫入本機檔案。

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

## <span id="DeleteBlob"></span></a>作法：刪除 Blob

最後，若要刪除 Blob，請使用 **delete\_blob()** 方法。下列範例示範如何刪除 Blob。

    azure_blob_service.delete_blob(container.name, "image-blob")

## <span id="NextSteps"></span></a>後續步驟

了解 Blob 儲存體的基礎概念之後，請參考下列連結以了解如何執行更複雜的儲存工作。

-   請參閱 MSDN 參考：[在 Azure 中儲存和存取資料][在 Azure 中儲存和存取資料]
-   造訪 [Azure 儲存體團隊部落格][Azure 儲存體團隊部落格] (英文)。
-   請造訪 GitHub 上的 [Azure SDK for Ruby][Azure SDK for Ruby] 儲存機制 (英文)。

  [後續步驟]: #next-steps
  [什麼是 Blob 服務？]: #what-is
  [概念]: #concepts
  [建立 Azure 儲存體帳戶]: #CreateAccount
  [建立 Ruby 應用程式]: #CreateRubyApp
  [設定您的應用程式以存取儲存體]: #ConfigAccessStorage
  [設定 Azure 儲存體連接]: #SetupStorageConnection
  [作法：建立容器]: #CreateContainer
  [作法：將 Blob 上傳至容器]: #UploadBlob
  [作法：列出容器中的 Blob]: #ListBlobs
  [作法：下載 Blob]: #DownloadBlobs
  [作法：刪除 Blob]: #DeleteBlob
  [1]: #NextSteps
  [howto-blob-storage]: ../includes/howto-blob-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [在 Azure 上建立 Ruby 應用程式]: /zh-tw/develop/ruby/tutorials/web-app-with-linux-vm/
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [在 Azure 中儲存和存取資料]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg433040.aspx
  [Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
  [Azure SDK for Ruby]: https://github.com/WindowsAzure/azure-sdk-for-ruby
