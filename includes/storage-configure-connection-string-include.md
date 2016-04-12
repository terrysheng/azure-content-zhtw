## 設定儲存體連接字串

Azure Storage Client Library for .NET 可支援使用儲存體連接字串，來設定存取儲存服務時所需的端點與認證。在組態檔中維護儲存體連接字串是最佳方式。

如需有關連接字串的詳細資訊，請參閱[設定 Azure 儲存體的連接字串](../articles/storage/storage-configure-connection-string.md)。

> [AZURE.NOTE] 儲存體帳戶金鑰很類似儲存體帳戶的根密碼。請務必小心保護您的儲存體帳戶金鑰。請避免轉發給其他使用者、進行硬式編碼，或將它儲存在其他人可以存取的純文字檔案。如果您認為金鑰可能遭到破解，請使用 Azure 入口網站重新產生金鑰。

### 決定您的目標環境

有兩個環境選項可供您執行本指南中的範例︰

- 您可以對 Azure 儲存體模擬器執行您的程式碼。儲存體模擬器是模擬雲端中 Azure 儲存體帳戶的本機環境。模擬器是一個免費選項，在開發您的應用程式時可用於測試和偵錯您的程式碼。模擬器會使用已知的帳戶和金鑰。如需詳細資訊，請參閱[使用 Azure 儲存體模擬器進行開發和測試](../articles/storage/storage-use-emulator.md)。
- 您可以對雲端中的 Azure 儲存體帳戶執行您的程式碼。 

如果您要選擇以雲端中的儲存體帳戶為目標，請從 Azure 入口網站複製您的儲存體帳戶的主要存取金鑰。如需詳細資訊，請參閱[檢視和複製儲存體存取金鑰](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys)。

> [AZURE.NOTE] 您可以選擇以儲存體模擬器為目標，以避免產生與 Azure 儲存體相關聯的任何費用。不過，如果您選擇以雲端中的 Azure 儲存體帳戶為目標，則執行本教學課程的費用可以忽略不計。
	
### 使用 .NET 組態來設定連接字串

如果您的應用程式在桌面或行動裝置上、在 Azure 虛擬機器上，或在 Azure Web 應用程式中執行，則使用 .NET 組態 (例如，在應用程式的 `web.config` 或 `app.config` 檔案中) 儲存連接字串。如下所示，使用 `<appSettings>` 元素儲存連接字串。以您的儲存體帳戶名稱取代 `account-name`，並以您的帳戶存取金鑰取代 `account-key`：

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

例如，組態設定會如下所示：

	<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=account-key" />

若要以儲存體模擬器為目標，您可以使用對應到已知帳戶名稱和金鑰的捷徑。在此情況下，您的連接字串設定會是︰

	<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />

### 設定 Azure 雲端服務的連接字串

如果您的應用程式在 Azure 雲端服務中執行，則使用 Azure 服務組態檔案 (`*.csdef` 和 `*.cscfg` 檔案) 儲存連接字串。如需有關 Azure 雲端服務組態的詳細資訊，請參閱[如何建立及部署雲端服務](../articles/cloud-services/cloud-services-how-to-create-deploy.md)。

<!---HONumber=AcomDC_0406_2016-->