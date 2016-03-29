您必須為每個主控 Azure 複本的 VM 建立負載平衡端點。如果您的複本位於多個區域，則該區域的每個複本必須位於相同 VNet 中的相同雲端服務。建立跨越多個 Azure 區域的可用性群組複本需要設定多個 VNet。如需有關設定跨 VNet 連線的詳細資訊，請參閱〈[設定 VNet 對 VNet 連線](../articles/vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)〉。

1. 在 Azure 入口網站中，巡覽至每個主控複本的 VM 並檢視詳細資料。

1. 按一下每個 VM 的 [**端點**] 索引標籤。

1. 針對您想要使用的接聽程式端點，確認**名稱**和**公用連接埠**並未處於使用中狀態。在下列範例中，名稱是「MyEndpoint」，而通訊埠為「1433」。

1. 在您的本機用戶端上，下載並安裝[最新的 PowerShell 模組](https://azure.microsoft.com/downloads/)。

1. 啟動 **Azure PowerShell**。新的 PowerShell 工作階段會使用載入的 Azure 系統管理模組來開啟。

1. 執行 **Get-AzurePublishSettingsFile**。這個 Cmdlet 會將您導向瀏覽器，以便將發佈設定檔案下載至本機目錄。系統可能會提示您輸入 Azure 訂用帳戶的登入認證。

1. 使用您所下載發佈設定檔案的路徑來執行 **Import-AzurePublishSettingsFile** 命令：

		Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

	一旦匯入發佈設定檔案，您便可以在 PowerShell 工作階段中管理 Azure 訂用帳戶。
<!---HONumber=AcomDC_0128_2016-->