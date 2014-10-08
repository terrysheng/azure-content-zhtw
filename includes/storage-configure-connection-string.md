Azure Storage Client Library for .NET 可支援使用儲存體連接字串，來設定存取儲存服務時所需的端點與認證。建議您在組態檔中維護儲存體連接字串，而非將它以硬式編碼的方式寫入您的應用程式。您有兩個選項可以用來儲存連接字串：

-   如果您的應用程式會在 Azure 雲端服務中執行，則使用 Azure 服務組態系統 (`*.csdef` and`*.cscfg` 檔案) 來儲存您的連接字串。
-   如果您的應用程式會在 Azure 虛擬機器中執行，或如果您打算建置在 Azure 外部執行的 .NET 應用程式，則使用 .NET 組態系統 (例如`web.config` 或`app.config` 檔案) 來儲存您的連接字串。

稍後在本指南中，我們將說明如何從您的程式碼中擷取連接字串。

### 從 Azure 雲端服務設定連接字串

Azure 雲端服務具備獨特的服務組態機制，可讓您從 Azure 管理入口網站動態變更組態設定，而無需重新部署應用程式。

若要在 Azure 服務設定中設定連接字串：

1.  在 Visual Studio 的 [方案總管] 內，在 Azure 部署專案的 **Roles**
    資料夾中，以滑鼠右鍵按一下
    Web 角色或背景工作角色，然後按一下 [屬性]。  
    ![Select the properties on a Cloud Service role in Visual Studio][]

2.  按一下 [設定] 索引標籤，並按 [新增設定] 按鈕。  
    ![Add a Cloud Service setting in visual Studio][]

    設定清單中隨即顯示新的 [Setting1] 項目。

3.  在新 **Setting1** 項目的 [類型] 下拉式清單中，選擇 [連接字串]。  
    ![Set connection string type][]

4.  按一下 **Setting1** 項目右側的 [...] 按鈕。
    [儲存體帳戶連接字串] 對話方塊便會隨即開啟。

5.  選擇要以儲存體模擬器 (本機電腦上模擬的 Windows
    Azure 儲存體)，還是雲端中的儲存體
    帳戶為目標。不論您選擇哪個
    選項，本指南的程式碼都適用。如您想要以稍早於 Azure 上建立的儲存體帳戶為目標，
    請輸入本教學課程在稍早步驟中所複製的 [主要存取金鑰] 值。

    > [WACOM.NOTE] 您可以選擇以儲存體模擬器為目標，以避免產生與 Windows Azure 儲存體相關的任何費用。不過，如果您選擇以雲端中的 Azure 儲存體帳戶為目標，則執行本教學課程的費用可以忽略不計。

    ![Select target environment][]

6.  將 [名稱] 項目從 **Setting1** 變更成更容易記住的名稱，如 **StorageConnectionString**。
    您稍後將在本指南的程式碼中參照此連接字串。  
    ![Change connection string name][]

### 使用 .NET 設定來設定連接字串

如果您要撰寫的應用程式並非 Azure 雲端服務 (請參閱上節)，建議您使用 .NET 組態系統 (例如`web.config` 或`app.config`). 這類應用程式包括 Azure 網站或 Azure 虛擬機器，以及設計成在 Azure 外執行的應用程式。您可以使用`<appSettings>` 元素來儲存連接字串，如下所示。將`account-name` 以您的儲存體帳戶名稱取代，並將`account-key` 以您的帳戶存取金鑰取代：

    <configuration>
        <appSettings>
            <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
        </appSettings>
    </configuration>

例如，組態檔中的組態設定可能如下所示：

    <configuration>
        <appSettings>
            <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln9fT7bvY+rxu2iWAEyzPNITGkhM88J8HUoyofpK7C8fHcZc2kIZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />
        </appSettings>
    </configuration>

如需儲存體連接字串的詳細資訊，請參閱[設定連接字串][]。

現在您已準備就緒，可以開始執行本指南的「作法」工作。

  [Select the properties on a Cloud Service role in Visual Studio]: ./media/storage-configure-connection-string/connection-string1.png
  [Add a Cloud Service setting in visual Studio]: ./media/storage-configure-connection-string/connection-string2.png
  [Set connection string type]: ./media/storage-configure-connection-string/connection-string3.png
  [Select target environment]: ./media/storage-configure-connection-string/connection-string4.png
  [Change connection string name]: ./media/storage-configure-connection-string/connection-string5.png
  [設定連接字串]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
