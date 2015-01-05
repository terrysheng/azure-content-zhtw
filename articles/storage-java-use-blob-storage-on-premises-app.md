<properties urlDisplayName="Image Gallery w/ Storage" pageTitle="搭配 Blob 儲存體 (Java) 的內部部署應用程式 | Microsoft Azure" metaKeywords="Azure blob storage, Azure blob Java, Azure blob example, Azure blob tutorial" description="Learn how to create a console application that uploads an image to Azure, and then displays the image in your browser. Code samples in Java." metaCanonical="" services="storage" documentationCenter="Java" title="On-Premises Application with Blob Storage" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# 搭配 Blob 儲存體的內部部署應用程式

下列範例說明如何使用 Azure 儲存體
在 Azure 中儲存影像。以下程式碼用於主控台
應用程式將影像上傳到 Azure 然後建立
HTML 檔案在瀏覽器中顯示影像。

## 目錄

-   [必要條件][]
-   [使用 Azure Blob 儲存體上傳檔案][]
-   [刪除容器][]

## <a name="bkmk_prerequisites"> </a>必要條件

1.  已安裝 Java Developer Kit (JDK) 1.6 版或更新版本。
2.  已安裝 Azure SDK。
3.  已安裝 Azure Libraries for Java 的 JAR 和任何適用的相依性 JAR，且位於 Java 編輯器所使用的組建路徑中。如需有關安裝 Azure Libraries for Java 的詳細資訊，請參閱「下載 Azure SDK for Java]」(英文)。
4.  已設定 Azure 儲存體帳戶。以下程式碼將會使用此儲存體帳戶的帳戶名稱和帳戶金鑰。如需建立儲存體帳戶的詳細資訊，請參閱[如何建立儲存體帳戶] (英文)，如需擷取帳戶金鑰的詳細資訊，請參閱[如何管理儲存體帳戶] (英文)。
5.  您已建立一個已命名並儲存於路徑 c:\\myimages\\image1.jpg 中的本機影像檔案。或者，修改範例中的 **FileInputStream** 建構函式以使用其他影像路徑和檔案名稱。

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <a name="bkmk_uploadfile"> </a>使用 Azure Blob 儲存體上傳檔案

我們將在此提供逐步程序；如果想要跳過這些步驟，我們將在本主題後面提供完整程式碼。

程式碼一開始先匯入 Azure 核心儲存體
類別、Azure Blob 用戶端類別、Java IO 類別和
**URISyntaxException** 類別：

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

宣告名為 **StorageSample** 的類別，並加上左括號
**{**。

    public class StorageSample {

在 **StorageSample** 類別中，宣告字串變數將
包含預設端點通訊協定、您的儲存體帳戶名稱，以及
您的儲存體存取金鑰 (如同 Azure 儲存體中所指定)
。分別取代預留位置值 **your\_account\_name** 和
**your\_account\_key** 成為您自己的帳戶名稱和帳戶金鑰
。

    public static final String storageConnectionString = 
           "DefaultEndpointsProtocol=http;" + 
               "AccountName=your_account_name;" + 
               "AccountKey=your_account_name"; 

在 **main** 的宣告中加入，包含 **try** 區塊，並
加上必要的左括號 **{**。

    public static void main(String[] args) 
    {
        try
        {

宣告下列類型的變數 (該描述說明如何
用於此範例)：

-   **CloudStorageAccount**：可用來以 Azure 儲存體帳戶名稱和金鑰初始化帳戶物件，並可用來建立 Blob 用戶端物件。
-   **CloudBlobClient**：可用來存取 Blob 服務。
-   **CloudBlobContainer**：可用來建立 Blob 容器、列出容器中的 Blob，和刪除容器。
-   **CloudBlockBlob**：可用來將本機影像檔案上傳至容器。

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

對 **account** 變數指定值。

    account = CloudStorageAccount.parse(storageConnectionString);

對 **serviceClient** 變數指定值。

    serviceClient = account.createCloudBlobClient();

對 **container** 變數指定值。我們將取得
名為 **gettingstarted** 的容器參考。

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

// 建立容器。如果容器不存在，此方法將會建立容器 (並傳回 **true**)。如果容器存在，則會傳回 **false**。**createIfNotExist** 的替代方式是 **create** 方法 (如果容器已存在，將傳回錯誤訊息)。

    container.createIfNotExist();

設定容器的匿名存取。

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

取得區塊 Blob 的參考，此參考將可代表
Azure 儲存體中的 Blob。

    blob = container.getBlockBlobReference("image1.jpg");

使用 **File** 建構函式，來取得在本機建立且即將上傳之檔案的參考。(執行此程式碼之前，請確定您已建立此檔案。)

    File fileReference = new File ("c:\\myimages\\image1.jpg");

透過呼叫 **CloudBlockBlob.upload** 方法來上傳本機檔案。**CloudBlockBlob.upload** 方法的第一個參數是 **FileInputStream** 物件，代表將上傳至 Azure 儲存體的本機檔案。第二個參數是檔案的大小 (位元組)。

    blob.upload(new FileInputStream(fileReference), fileReference.length());

呼叫名為 **MakeHTMLPage** 的協助程式函數來製作基本的 HTML 頁面，此頁面將包含 **&lt;image&gt;** 元素，且來源設定為目前位於您 Azure 儲存體帳戶中的 Blob。(我們將在本主題後面討論 **MakeHTMLPage** 的程式碼。)

    MakeHTMLPage(container);

列印關於已建立之 HTML 頁面的狀態訊息和相關資訊。

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

透過插入右括號來結束 **try** 區塊：**}**

處理下列例外狀況：

-   **FileNotFoundException**：**FileInputStream**  或 **FileOutputStream** 建構函式可能擲回的例外狀況。
-   **StorageException**：Azure 用戶端儲存體程式庫可能擲回的例外狀況。
-   **URISyntaxException**：**ListBlobItem.getUri**  方法可能擲回的例外狀況。
-   **Exception**：一般例外狀況處理。

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

透過插入右括號來結束 **main**：**}**

建立名為 **MakeHTMLPage** 的方法，藉此建立基本的 HTML 頁面。此方法包含參數類型 **CloudBlobContainer**，可用來逐一取得已上傳的 Blob 清單。此方法將擲回例外狀況類型 **FileNotFoundException** (**FileOutputStream** 建構函式可能擲回的例外狀況) 和 **URISyntaxException** (**ListBlobItem.getUri** 方法可能擲回的例外狀況)。加上左括號 **{**。

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

建立名為 **index.html** 的本機檔案。

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

寫入至本機檔案，加入 **&lt;html&gt;**、**&lt;header&gt;** 和 **&lt;body&gt;** 元素。

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

逐一取得已上傳的 Blob 清單。在 HTML 頁面中，為每個 Blob 建立一個 **&lt;img&gt;** 元素，並將其 **src** 屬性傳送至 Azure 儲存體帳戶中之 Blob 的 URI。雖然您在此範例中僅新增一張影像，如果您要新增多張，此程式碼需要重複列舉全部影像。

為了方便起見，此範例假設每個已上傳的 Blob 是一張影像。如果您已更新除了影像以外的 Blob，或分頁 Blob 而非區塊 Blob，則請視需要調整程式碼。

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

結束 **&lt;body&gt;** 元素和 **&lt;html&gt;** 元素。

    stream.println("</body>");
    stream.println("</html>");

關閉本機檔案。

    stream.close();

透過插入右括號來結束 **MakeHTMLPage**：**}**

透過插入右括號來結束 **StorageSample**：**}**

下列是此範例的完整程式碼。務必分別修改預留位置值 **your\_account\_name** 和 **your\_account\_key** 使用您的帳戶名稱和帳戶金鑰。

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor 
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString = 
                "DefaultEndpointsProtocol=http;" + 
                   "AccountName=your_account_name;" + 
                   "AccountKey=your_account_name"; 

        public static void main(String[] args) 
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;
                
                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExist();
                
                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");
                File fileReference = new File ("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            }
            catch (FileNotFoundException fileNotFoundException)
            {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (URISyntaxException uriSyntaxException)
            {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

除了將本機影像檔案上傳至 Azure 儲存體以外，此範例程式碼還會建立本機檔案 namedindex.html，您可在瀏覽器中開啟此檔案以查看已上傳影像。

因為此程式碼包含您的帳戶名稱和帳戶金鑰，請確定您的來源程式碼安全無虞。

## <a name="bkmk_deletecontainer"> </a>刪除容器

由於您需支付儲存體的費用，因此，在您完成使用此範例之後，您可能會想要刪除 **gettingstarted** 容器。若要刪除容器，請使用 **CloudBlobContainer.delete** 方法：

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

若要呼叫 **CloudBlobContainer.delete** 方法，初始化 **CloudStorageAccount**、**ClodBlobClient**、**CloudBlobContainer** 物件的程序與 **createIfNotExist** 方法相同。下列是刪除名為 **gettingstarted** 之容器的完整範例。

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;

    public class DeleteContainer {

        public static final String storageConnectionString = 
                "DefaultEndpointsProtocol=http;" + 
                   "AccountName=your_account_name;" + 
                   "AccountKey=your_account_key"; 

        public static void main(String[] args) 
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                
                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();
                
                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

如需其他 Blob 儲存體類別和方法的概觀，請參閱[如何使用 Java 的 Blob 儲存體服務] (英文)。

  [必要條件]: #bkmk_prerequisites
  [使用 Azure Blob 儲存體上傳檔案]: #bkmk_uploadfile
  [刪除容器]: #bkmk_deletecontainer
  [下載 Azure SDK for Java]: http://www.windowsazure.com/zh-tw/develop/java/
  [如何建立儲存體帳戶]: http://www.windowsazure.com/zh-tw/manage/services/storage/how-to-create-a-storage-account/
  [如何管理儲存體帳戶]: http://www.windowsazure.com/zh-tw/manage/services/storage/how-to-manage-a-storage-account/
  [如何從 Python 使用 Blob 儲存體服務]: http://www.windowsazure.com/zh-tw/develop/java/how-to-guides/blob-storage/

<!--HONumber=35.1-->
