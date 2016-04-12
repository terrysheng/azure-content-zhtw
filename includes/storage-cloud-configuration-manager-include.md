### 解析連接字串

您在上面參考的 Microsoft Azure Configuration Manager 程式庫會提供一個類別，可用來剖析組態檔中的連接字串。[CloudConfigurationManager 類別](https://msdn.microsoft.com/library/azure/mt634650.aspx)可剖析組態設定，無論用戶端應用程式是在桌面、在行動裝置上、在 Azure 虛擬機器中，或在 Azure 雲端服務中執行。

將下列程式碼新增至 `Main()` 方法：

    // Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

是否使用 Azure Configuration Manager 可由您選擇。您也可以使用 API，例如 .NET Framework 的 [ConfigurationManager 類別](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx)。

<!---HONumber=AcomDC_0406_2016-->