<properties
   pageTitle="使用 Visual Studio 設定 Azure 雲端服務的角色 | Microsoft Azure"
   description="了解如何使用 Visual Studio 設定 Azure 雲端服務的角色。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="01/30/2016"
   ms.author="tarcher" />

# 使用 Visual Studio 設定 Azure 雲端服務的角色

Azure 雲端服務可以有一或多個背景工作角色或 web 角色。您必須為每個角色定義其設定方式，並設定該角色的執行方式。若要深入了解雲端服務中的角色，請參閱視訊 [Azure 雲端服務簡介](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services)。您的雲端服務資訊儲存在下列檔案中：

- **ServiceDefinition.csdef**

    服務定義檔可定義雲端服務的執行階段設定，包括需要哪些角色、端點和虛擬機器大小。當您的角色在執行時，儲存在此檔案中的資料不可變更。

- **ServiceConfiguration.cscfg**

    服務組態檔可設定一個角色可以執行的執行個體數目，以及為角色定義的設定值。當您的角色在執行時，儲存在此檔案中的資料可以變更。

若要能夠為角色執行方式的這些設定儲存不同的值，您可以有多個服務組態。您可以將不同的服務組態用於每個部署環境。例如，您可以設定儲存體帳戶連接字串，在本機服務組態中使用本機 Azure 儲存體模擬器，並建立另一個服務組態，在雲端中使用 Azure 儲存體。

當您在 Visual Studio 中建立新的 Azure 雲端服務時，預設會建立兩個服務組態。這些組態會新增至您的 Azure 專案。組態命名為：

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## 設定 Azure 雲端服務

您可以從 Visual Studio 中的 [方案總管] 設定 Azure 雲端服務，如下圖所示。

![設定雲端服務](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### 設定 Azure 雲端服務

1. 若要從 [方案總管] 設定 Azure 專案中的每個角色，請在 Azure 專案中開啟該角色的捷徑功能表，然後選擇 [屬性]。

    在 Visual Studio 編輯器中會顯示具有角色名稱的頁面。此頁面會顯示 [組態] 索引標籤的欄位。

1. 在 [服務組態] 清單中，選擇您想要編輯的服務組態名稱。

    如果您想要變更此角色的所有服務組態，您可以選擇 [所有組態]。

    >[AZURE.IMPORTANT] 如果您選擇特定服務組態，某些屬性會停用，因為它們只能針對所有組態設定。若要編輯這些屬性，您必須選擇 [所有組態]。

    您現在可以選擇索引標籤，以在該檢視上更新任何已啟用的屬性。

## 變更角色執行個體的數目

若要改善雲端服務的效能，您可以根據使用者數目或特定角色的預期負載，變更正在執行之角色的執行個體數目。雲端服務在 Azure 中執行時，會為角色的每個執行個體建立個別的虛擬機器。這會影響這個雲端服務的部署計費。如需計費的詳細資訊，請參閱[了解 Microsoft Azure 帳單](billing-understand-your-bill.md)。

### 若要變更角色的執行個體數目

1. 選擇 [組態] 索引標籤。

1. 在 [服務組態] 清單中，選擇您想要更新的服務組態。

    >[AZURE.NOTE] 您可以設定特定服務組態或所有服務組態的執行個體計數。

1. 在 [執行個體計數] 文字方塊中，輸入您要為此角色啟動的執行個體數目。

    >[AZURE.NOTE] 當您將雲端服務發佈至 Azure，每個執行個體都會在個別的虛擬機器上執行。

1. 選擇工具列上的 [儲存] 按鈕來將這些變更儲存到服務組態檔。

## 管理儲存體帳戶的連接字串

您可以新增、移除或修改服務組態的連接字串。針對不同的服務組態，您可能會想要不同的連接字串。例如，針對具有 `UseDevelopmentStorage=true` 值的本機服務組態，您可能想要本機連接字串。您也能也會想要設定在 Azure 中使用儲存體帳戶的雲端服務組態。

>[AZURE.CAUTION] 當您輸入儲存體帳戶連接字串的 Azure 儲存體帳戶金鑰資訊時，這項資訊會儲存在本機服務組態檔中。不過，這項資訊目前不會儲存為加密文字。

針對每個服務組態使用不同的值，您就不必在您的雲端服務中使用不同的連接字串，或在您將雲端服務發佈至 Azure 時修改程式碼。您可以在程式碼中將相同名稱用於連接字串，根據您在建置雲端服務或發佈該服務時選取的服務組態，此值將會不同。

### 管理儲存體帳戶的連接字串

1. 選擇 [設定] 索引標籤。

1. 在 [服務組態] 清單中，選擇您想要更新的服務組態。

    >[AZURE.NOTE] 您可以更新特定服務組態的連接字串，但如果您需要新增或刪除連接字串，您必須選取 [所有組態]。

1. 若要新增連接字串，請選擇 [新增設定] 按鈕。將新項目新增至清單。

1. 在 [名稱] 文字方塊中，輸入您要用於連接字串的名稱。

1. 選取 [類型] 下拉式清單中的 [連接字串]。

1. 若要變更連接字串的值，請選擇省略符號 (...) 按鈕。[建立儲存體連接字串] 對話方塊會隨即出現。

1. 若要使用本機儲存體帳戶模擬器，請選擇 [Microsoft Azure 儲存體模擬器] 選項按鈕，然後選擇 [確定] 按鈕。

1. 若要在 Azure 中使用儲存體帳戶，請選擇 [訂用帳戶] 選項按鈕並選取所需的儲存體帳戶。

1. 若要使用自訂認證，請選擇 [手動輸入的認證] 選項按鈕。輸入儲存體帳戶名稱和主要或次要金鑰。如需有關如何建立儲存體帳戶以及如何在 [建立儲存體連接字串] 對話方塊中輸入儲存體帳戶的詳細資料等資訊，請參閱[準備從 Visual Studio 發佈或部署 Azure 應用程式](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)。

1. 若要刪除連接字串，請選取連接字串，然後選擇 [移除設定] 按鈕。

1. 選擇工具列上的「儲存」圖示來將這些變更儲存到服務組態檔。

1. 若要存取服務組態檔中的連接字串，您必須取得組態設定的值。下列程式碼會顯示建立 blob 儲存體的範例，以及當使用者為 Azure 雲端服務選擇 web 角色中 Default.aspx 頁面上的 **Button1** 時，使用來自服務組態檔的連接字串 `MyConnectionString` 上傳的資料。將下列 using 陳述式新增至 Default.aspx.cs：

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. 在設計檢視中開啟 Default.aspx.cs 並從工具箱新增按鈕。將下列程式碼新增至 `Button1_Click` 方法。此程式碼使用 `GetConfigurationSettingValue` 從服務組態檔取得連接字串的值。然後在連接字串 `MyConnectionString` 中所參考的儲存體帳戶中建立 blob，最後程式會將文字新增至 blob。

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## 新增要在 Azure 雲端服務中使用的自訂設定

服務組態檔中的自訂設定可讓您為特定服務組態的字串新增名稱和值。您可以藉由讀取設定的值並使用該值控制程式碼中的邏輯，即可選擇使用此設定在您的雲端服務中設定功能。您可以變更這些服務組態值，而不需要重建服務封裝或在雲端服務執行時。您的程式碼可以檢查設定變更時的通知。請參閱 [RoleEnvironment.Changing 事件](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx)。

您可以新增、移除或修改服務組態的自訂設定。針對不同的服務組態，您可能會想要這些連接字串的不同值。

針對每個服務組態使用不同的值，您就不必在您的雲端服務中使用不同的字串，或在您將雲端服務發佈至 Azure 時修改程式碼。您可以在程式碼中將相同名稱用於字串，根據您在建置雲端服務或發佈該服務時選取的服務組態，此值將會不同。

### 新增要在 Azure 雲端服務中使用的自訂設定

1. 選擇 [設定] 索引標籤。

1. 在 [服務組態] 清單中，選擇您想要更新的服務組態。

    >[AZURE.NOTE] 您可以更新特定服務組態的字串，但如果您需要新增或刪除字串，您必須選取 [所有組態]。

1. 若要新增字串，請選擇 [新增設定] 按鈕。將新項目新增至清單。

1. 在 [名稱] 文字方塊中，輸入您要用於字串的名稱。

1. 選取 [類型] 下拉式清單中的 [字串]。

1. 若要新增或變更字串的值，請在 [值] 文字方塊中輸入新值。

1. 若要刪除字串，請選取字串，然後選擇 [移除設定] 按鈕。

1. 選擇工具列上的 [儲存] 按鈕來將這些變更儲存到服務組態檔。

1. 若要存取服務組態檔中的字串，您必須取得組態設定的值。

    您必須確定已準備好將下列 using 陳述式新增至 Default.aspx.cs，就像您在上一個程序所做的一樣。

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. 將下列程式碼新增至 `Button1_Click` 方法以利用存取連接字串的相同方式來存取這個字串。然後您的程式碼就可以根據所使用之服務組態檔的設定字串值執行一些特定的程式碼。

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## 管理每個角色執行個體的本機儲存體

您可以為角色的每個執行個體新增本機檔案系統儲存體。您可以在此儲存其他角色不需要存取的本機資料。您不需要儲存至資料表、blob 或 SQL Database 儲存體的任何資料都可以儲存在這裡。例如，您可以使用這個本機儲存體快取可能需要再次使用的資料。角色的其他執行個體無法存取在此儲存的資料。如需有關本機儲存體資源的詳細資訊，請參閱[設定本機儲存體資源](/cloud-services/cloud-services-configure-local-storage-resources.md)。

本機儲存體設定會套用至所有服務組態。您只能新增、移除或修改所有服務組態的本機儲存體。

### 管理每個角色執行個體的本機儲存體

1. 選擇 [本機儲存體] 索引標籤。

1. 在 [服務組態] 清單中，選擇 [所有組態]。

1. 若要新增本機儲存體項目，請選擇 [新增本機儲存體] 按鈕。將新項目新增至清單。

1. 在 [名稱] 文字方塊中，輸入您要用於此本機儲存體的名稱。

1. 在 [大小] 文字方塊中，輸入此本機儲存體所需的大小 (以 MB 為單位)。

1. 若要在回收這個角色的虛擬機器時將此本機儲存體中的資料移除，請選取 [於角色回收時清除] 核取方塊。

1. 若要編輯現有的本機儲存體項目，請選擇您需要更新的資料列。然後您可以編輯欄位，如先前步驟所述。

1. 若要刪除本機儲存體項目，請選擇清單中的儲存體項目，然後選擇 [移除本機儲存體] 按鈕。

1. 若要將這些變更儲存到服務組態檔，請選擇工具列上的「儲存」圖示。

1. 若要存取您已新增至服務組態檔的本機儲存體，您必須取得本機資源組態設定的值。使用下列幾行的程式碼存取這個值，建立稱為 **MyStorageTest.txt** 的檔案，並將一行測試資料寫入該檔案。您可以將此程式碼新增至 `Button_Click` 您在先前程序中使用的方法：

1. 您必須確定下列 using 陳述式已新增至 Default.aspx.cs：

    ```
    using System.IO;
    using System.Text;
    ```

1. 將下列程式碼新增至 `Button1_Click` 方法。這會在本機儲存體中建立檔案並將測試資料寫入該檔案。

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. (選用) 若要檢視您在本機執行雲端服務時所建立的這個檔案，請使用下列步驟：

  1. 執行 web 角色，然後按一下 [Button1] 以確定呼叫 `Button1_Click` 內的程式碼。

  1. 在通知區域中，開啟 Azure 圖示的捷徑功能表並選擇 [顯示計算模擬器 UI]。[Azure 計算模擬器] 對話方塊隨即出現。

  1. 選取 Web 角色。

  1. 在功能表列上，選擇 [工具]、[開啟本機儲存體]。[Windows 檔案總管] 視窗隨即出現。

  1. 在功能表列上，輸入 **MyStorageTest.txt** 到 [搜尋] 文字方塊，然後選擇 [Enter] 以開始搜尋。

    檔案會顯示在搜尋結果中。

  1. 若要檢視檔案的內容，請開啟檔案的捷徑功能表並選擇 [開啟]。

## 收集雲端服務診斷

您可以為您的 Azure 雲端服務收集診斷資料。這項資料會新增至儲存體帳戶。針對不同的服務組態，您可能會想要不同的連接字串。例如，針對具有 UseDevelopmentStorage=true 值的本機服務組態，您可能想要本機儲存體帳戶。您也能也會想要設定在 Azure 中使用儲存體帳戶的雲端服務組態。如需 Azure 診斷的詳細資訊，請參閱使用 Azure 診斷收集記錄資料。

>[AZURE.NOTE] 本機服務組態已設定為使用本機資源。如果您使用雲端服務組態來發佈 Azure 雲端服務，您在發佈時指定的連接字串也會用於診斷連接字串，除非您已指定連接字串。如果您使用 Visual Studio 封裝雲端服務，不會變更服務組態中的連接字串。

### 收集雲端服務診斷

1. 選擇 [組態] 索引標籤。

1. 在 [服務組態] 清單中，選擇您想要更新的服務組態或選擇 [所有組態]。

    >[AZURE.NOTE] 您可以更新特定服務組態的儲存體帳戶，但如果您想要啟用或停用診斷，您必須選擇 [所有組態]。

1. 若要啟用診斷，請選取 [啟用診斷] 核取方塊。

1. 若要變更儲存體帳戶的值，請選擇省略符號 (...) 按鈕。

    [建立儲存體連接字串] 對話方塊會隨即出現。

1. 若要使用本機連接字串，請選擇 Azure 儲存體模擬器選項，然後選擇 [確定] 按鈕。

1. 若要使用與您的 Azure 訂用帳戶相關聯的儲存體帳戶，請選擇 [您的訂用帳戶] 選項。

1. 若要使用本機連接字串的儲存體帳戶，請選擇 [手動輸入的認證] 選項。

    如需有關如何建立儲存體帳戶以及如何在 [建立儲存體連接字串] 對話方塊中輸入儲存體帳戶的詳細資料等資訊，請參閱[準備從 Visual Studio 發佈或部署 Azure 應用程式](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)。

1. 選擇您要在 [帳戶名稱] 使用的儲存體帳戶。

    如果您手動輸入儲存體帳戶認證，請在 [帳戶金鑰]中複製或輸入您的主要金鑰。可從 Azure 管理入口網站複製這個金鑰。若要複製這個金鑰，請遵循 Azure 管理入口網站中**儲存體帳戶**檢視的下列步驟：

  1. 選取您要用於雲端服務的儲存體帳戶。

  1. 選擇位於畫面底部的 [管理存取金鑰] 按鈕。[管理存取金鑰] 對話方塊隨即出現。

  1. 若要複製存取金鑰，請選擇 [複製到剪貼簿] 按鈕。您現在可以將這個金鑰貼入 [帳戶金鑰] 欄位。

1. 若要使用您提供的儲存體帳戶做為您將雲端服務發佈至 Azure 時的診斷 (和快取) 連接字串，請選取 [發行到 Azure 時，使用 Azure 儲存體帳戶認證更新用於診斷和快取的開發儲存體連接字串] 核取方塊。

1. 選擇工具列上的 [儲存] 按鈕來將這些變更儲存到服務組態檔。

## 變更用於每個角色的虛擬機器大小

您可以設定每個角色的虛擬機器大小。您只能針對所有服務組態設定這個大小。如果您選取較小的機器大小，就會配置較少的 CPU 核心、記憶體和本機磁碟儲存體。配置的頻寬也會較小。如需有關這些配置大小和資源的詳細資訊，請參閱[雲端服務的大小](/cloud-services/cloud-services-sizes-specs.md)。

在 Azure 中每部虛擬機器所需的資源會影響在 Azure 中執行雲端服務的成本。如需 Azure 計費的詳細資訊，請參閱[了解 Microsoft Azure 帳單](billing-understand-your-bill.md)。

### 變更虛擬機器的大小

1. 選擇 [組態] 索引標籤。

1. 在 [服務組態] 清單中，選擇 [所有組態]。

1. 若要選取這個角色的虛擬機器大小，請從 [VM 大小] 清單選擇適當的大小。

1. 選擇工具列上的 [儲存] 按鈕來將這些變更儲存到服務組態檔。

## 管理角色的端點和憑證

您可藉由指定通訊協定、連接埠號碼和 (針對 HTTPS) SSL 憑證資訊來設定網路端點。2012 年 6 月之前的版本支援 HTTP、HTTPS 和 TCP。2012 年 6 月版支援那些通訊協定和 UDP。您不能將 UDP 用於計算模擬器中的輸入端點。您只能將該通訊協定用於內部端點。

若要改善 Azure 雲端服務的安全性，您可以建立使用 HTTPS 通訊協定的端點。比方說，如果您有客戶用來採購訂單的雲端服務，您會想要使用 SSL 確保他們的資訊是安全的。

您也可以新增可在內部或外部使用的端點。外部端點稱為輸入端點。輸入端點可提供雲端服務的另一個存取點給使用者。如果您有 WCF 服務，您可能會想要公開 web 角色的內部端點，用來存取此服務。

>[AZURE.IMPORTANT] 您只能針對所有服務組態更新端點。

如果您加入 HTTPS 端點，您需要使用 SSL 憑證。若要這樣做，您可以將憑證與所有服務組態的角色產生關聯，並將其用於您的端點。

>[AZURE.IMPORTANT] 這些憑證不會與您的服務一起封裝。您必須透過 Azure 平台管理入口網站將憑證分別上傳至 Azure

只有在您的雲端服務執行於 Azure 中時，才會套用與您的服務組態相關聯的管理憑證。當您的雲端服務在本機開發環境中執行時，它會使用由 Azure 計算模擬器所管理的標準憑證。

### 將憑證新增至角色

1. 選擇 [憑證] 索引標籤。

1. 在 [服務組態] 清單中，選擇 [所有組態]。

    >[AZURE.NOTE] 若要新增或移除憑證，您必須選取 [所有組態]。您可以視需要更新特定服務組態的名稱和指紋。

1. 若要將新增這個角色的憑證，請選擇 [新增憑證] 按鈕。將新項目新增至清單。

1. 在 [名稱] 文字方塊中，輸入憑證的名稱。

1. 在 [存放區位置] 清單中，選擇您想要加入的憑證位置。

1. 在 [存放區名稱] 清單中，選擇您想要用來選取憑證的存放區。

1. 若要新增憑證，請選擇省略符號 (...) 按鈕。[Windows 安全性] 對話方塊隨即出現。

1. 從清單選擇您想要使用的憑證，然後選擇 [確定] 按鈕。

    >[AZURE.NOTE] 當您從憑證存放區新增憑證時，所有中繼憑證都會自動新增至您的組態設定。這些中繼憑證也必須上傳至 Azure，以便針對 SSL 正確設定您的服務。

1. 若要刪除憑證，請選擇憑證，然後選擇 [移除憑證] 按鈕。

1. 選擇工具列中的「儲存」圖示來將這些變更儲存到服務組態檔。

### 管理角色的端點

1. 選擇 [端點] 索引標籤。

1. 在 [服務組態] 清單中，選擇 [所有組態]。

1. 若要新增端點，請選擇 [新增端點] 按鈕。將新項目新增至清單。

1. 在 [名稱] 文字方塊中，輸入您要用於此端點的名稱。

1. 從 [類型] 清單選擇您需要的端點類型。

1. 從 [通訊協定] 清單選擇您需要之端點的通訊協定。

1. 如果它是輸入端點，請在 [公用連接埠] 文字方塊中輸入要使用的公用連接埠。

1. 在 [私用連接埠] 文字方塊中輸入要使用的私用連接埠。

1. 如果端點需要 https 通訊協定，請在 [SSL 憑證名稱] 清單中選擇要使用的憑證。

    >[AZURE.NOTE] 此清單會顯示您在 [憑證] 索引標籤中為此角色新增的憑證。

1. 選擇工具列上的 [儲存] 按鈕來將這些變更儲存到服務組態檔。

## 後續步驟
藉由參閱[設定 Azure 專案](vs-azure-tools-configuring-an-azure-project.md)深入了解 Visual Studio 中的 Azure 專案。藉由參閱[結構描述參考](https://msdn.microsoft.com/library/azure/dd179398)深入了解雲端服務結構描述。

<!---HONumber=AcomDC_0204_2016-->