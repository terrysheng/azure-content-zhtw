### 建立主控台應用程式並取得組件

若要在 Visual Studio 中建立新的主控台應用程式，並安裝包含 Azure 儲存體用戶端程式庫的 NuGet 封裝：

1. 在 Visual Studio 中，依序選擇 [檔案] -> [新增專案]，然後從 Visual C# 範本清單中選擇 [Windows] -> [主控台應用程式]。
2. 提供主控台應用程式的名稱，然後按一下 [確定]。
3. 建立專案後，在 [方案總管] 中以滑鼠右鍵按一下該專案，然後選擇 [管理 NuGet 封裝]。在線上搜尋 "WindowsAzure.Storage"，再按一下 [安裝] 以安裝適用於 .NET 封裝與相依性的 Azure 儲存體用戶端程式庫。

本文中的程式碼範例也使用 [Microsoft Azure Configuration Manager 程式庫](https://msdn.microsoft.com/library/azure/mt634646.aspx)以從主控台應用程式中的 app.config 檔案擷取儲存體連接字串。透過 Azure Configuration Manager，無論應用程式是在 Microsoft Azure 中執行還是從桌面、行動或 Web 應用程式執行，您都可以在執行階段擷取連接字串。

若要安裝 Azure Configuration Manager 封裝，請以滑鼠右鍵按一下 [方案總管] 中的專案，然後選擇 [管理 NuGet 封裝]。在線上搜尋 "ConfigurationManager"，再按一下 [安裝] 以安裝封裝。

是否使用 Azure Configuration Manager 可由您選擇。您也可以使用 API，例如 .NET Framework 的 [ConfigurationManager 類別](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx)。

<!---HONumber=AcomDC_0309_2016-->