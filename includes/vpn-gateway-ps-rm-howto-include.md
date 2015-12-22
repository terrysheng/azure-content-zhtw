安裝模組有幾種不同方式：PowerShell 資源庫和 Web Platform Installer。最終結果十分類似，但您選取要執行安裝的方式將決定要在您的電腦上預設安裝的模組。

從 PowerShell 資源庫安裝時，檔案將預設位於您的 *%ProgramFiles%\\WindowsPowerShell\\Modules*。從 Web Platform Installer 安裝時，檔案將預設位於 *%ProgramFiles%\\Microsoft SDKs\\Azure\\PowerShell*。因此，您會想要固定使用其中一個，以避免在未來更新您的 Cmdlet 時發生錯誤。Web Platform Installer 將會每月收到更新的 Cmdlet。資源庫則是在 Cmdlet 推出時收到更新的版本。基於這個理由，有些人偏好使用資源庫。

如需安裝 Azure PowerShell 的其他資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

**從 PowerShell 資源庫安裝模組**

1. 若要直接從資源庫安裝資源管理員模組，請以系統管理員身分開啟 Windows PowerShell 並輸入下列命令：

		Install-Module AzureRM
		Install-AzureRM

2. 安裝模組之後，您必須匯入模組，才能加以使用：

		Import-AzureRM

**使用 Web Platform Installer 來安裝模組**

- 您可以使用 [Web Platform Installer](http://aka.ms/webpi-azps) 來安裝模組。按一下連結時，便會啟動安裝程式。

- 如果使用 Web Platform Installer 時收到錯誤，可能是因為您已使用資源庫安裝舊版的 Cmdlet。請參閱此[部落格文章](https://azure.microsoft.com/blog/azps-1-0/)，它可協助您移除舊版的模組，並讓您恢復並執行。一般錯誤發生在您使用 Web Platform Installer 並切換至資源庫 (或反向) 時。移除先前安裝的模組可解決這個問題，之後您就可以從新位置安裝。

<!---HONumber=AcomDC_1217_2015-->