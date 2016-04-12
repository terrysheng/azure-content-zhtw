## 設定開發環境

接下來，在 Visual Studio 中設定您的開發環境，以便您開始嘗試本指南中提供的程式碼範例。

### 建立 Windows 主控台應用程式專案

在 Visual Studio 中，建立新的 Windows 主控台應用程式，如下所示：

![建立 Windows 主控台應用程式](./media/storage-development-environment-include/storage-development-environment-include-1.png)

您可以在任何類型的 .NET 應用程式 (包括 Azure 雲端服務、Azure Web 應用程式、桌面應用程式或行動應用程式) 中使用 Azure Storage Client Library。在本指南中，為求簡化，我們將使用主控台應用程式。

### 使用 NuGet 來安裝必要的封裝

您必須將下列兩個封裝安裝至您的專案，才能完成本教學課程︰

- [適用於 .NET 的 Microsoft Azure 儲存體用戶端資源庫](https://www.nuget.org/packages/WindowsAzure.Storage/)︰此封裝可供以程式設計方式存取儲存體帳戶中的資料資源。
- [適用於 .NET 的 Microsoft Azure Configuration Manager 程式庫](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)︰此封裝提供一個類別，無論您的應用程式於何處執行，均可用來剖析組態檔中的連接字串。

您可以使用 NuGet 來取得這兩個封裝。請遵循下列步驟：

1. 在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [管理 NuGet 封裝]。
2. 在線上搜尋 "WindowsAzure.Storage"，然後按一下 [安裝] 以安裝 Storage Client Library 與其相依項目。
3. 在線上搜尋 "ConfigurationManager"，然後按一下 [安裝] 以安裝 Azure Configuration Manager。

>[AZURE.NOTE] Storage Client Library 封裝也包含在 [適用於 .NET 的 Azure SDK](https://azure.microsoft.com/downloads/) 中。不過，我們建議您也從 NuGet 安裝 Storage Client Library，以確保永遠有最新版的用戶端程式庫。

### 決定您的目標環境

有兩個環境選項可供您執行本指南中的範例︰

- 您可以對雲端中的 Azure 儲存體帳戶執行您的程式碼。 
- 您可以對 Azure 儲存體模擬器執行您的程式碼。儲存體模擬器是模擬雲端中 Azure 儲存體帳戶的本機環境。模擬器是一個免費選項，在開發您的應用程式時可用於測試和偵錯您的程式碼。模擬器會使用已知的帳戶和金鑰。如需詳細資訊，請參閱[使用 Azure 儲存體模擬器進行開發和測試](../articles/storage/storage-use-emulator.md)。

如果您要選擇以雲端中的儲存體帳戶為目標，請從 Azure 入口網站複製您的儲存體帳戶的主要存取金鑰。如需詳細資訊，請參閱[檢視和複製儲存體存取金鑰](../articles/storage/storage-create-storage-account.md#view-and-copy-storage-access-keys)。

> [AZURE.NOTE] 您可以選擇以儲存體模擬器為目標，以避免產生與 Azure 儲存體相關聯的任何費用。不過，如果您選擇以雲端中的 Azure 儲存體帳戶為目標，則執行本教學課程的費用可以忽略不計。

### 設定儲存體連接字串

Azure Storage Client Library for .NET 可支援使用儲存體連接字串，來設定存取儲存服務時所需的端點與認證。在組態檔中維護儲存體連接字串是最佳方式。

如需有關連接字串的詳細資訊，請參閱[設定 Azure 儲存體的連接字串](../articles/storage/storage-configure-connection-string.md)。

> [AZURE.NOTE] 儲存體帳戶金鑰很類似儲存體帳戶的根密碼。請務必小心保護您的儲存體帳戶金鑰。請避免轉發給其他使用者、進行硬式編碼，或將它儲存在其他人可以存取的純文字檔案。如果您認為金鑰可能遭到破解，請使用 Azure 入口網站重新產生金鑰。

若要設定連接字串，請從 Visual Studio 中的 [方案總管] 開啟 `app.config` 檔案。如下所示，加入 `<appSettings>` 元素的內容。以您的儲存體帳戶名稱取代 `account-name`，並以您的帳戶存取金鑰取代 `account-key`：

	<configuration>
	    <startup> 
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
	    </startup>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
  		</appSettings>
	</configuration>

例如，組態設定會如下所示：

	<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=nYV0gln9fT7bvY+rxu2iWAEyzPNITGkhM88J8HUoyofpK7C8fHcZc2kIZp6cKgYRUM74lHI84L50Iau1+9hPjB==" />

若要以儲存體模擬器為目標，您可以使用對應到已知帳戶名稱和金鑰的捷徑。在此情況下，您的連接字串設定會是︰

	<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />

<!---HONumber=AcomDC_0406_2016-->