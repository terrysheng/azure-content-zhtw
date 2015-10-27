本主題包含使用 Azure PowerShell Cmdlet 的範例。Azure PowerShell 目前提供兩個版本 - 1.0 Preview 和 0.9.8。如果您有現有的指令碼，而且不想要立即變更它們，可以繼續使用 0.9.8 版本。當使用 1.0 Preview 版本時，您應該在預先生產環境中仔細測試您的指令碼，然後才在生產環境中使用以避免產生非預期的影響。

在大部分情況下，這兩個版本的唯一差異在於 1.0 Preview 的 Cmdlet 命名遵循 {動詞}-AzureRm{名詞} 的格式，而 0.9.8 的命名則不包含 **Rm** (例如，New-AzureRmResourceGroup，而非 New-AzureResourceGroup)。當版本間有更大差異時，此主題會針對不同版本顯示範例。

在使用 Azure PowerShell 0.9.8 時，您必須先執行 **Switch-AzureMode AzureResourceManager** 命令啟用資源管理員模式。1.0 Preview 不需要執行此命令。

如需 1.0 Preview 的詳細資訊，包括如何安裝及解除安裝版本，請參閱 [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/)。如需資源管理員命令中重大變更的相關資訊，請參閱 [Azure 資源管理員管理 PowerShell Cmdlet 的變更](../articles/powershell-preview-resource-manager-changes.md)。

<!---HONumber=Oct15_HO4-->