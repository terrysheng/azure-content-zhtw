

自從自訂指令碼擴充功能可供使用後，已廣泛用來在 Windows 和 Linux VM 上設定工作負載。隨著 Azure 資源管理員範本的引入，使用者現在可以建立單一的範本，不只可用於佈建 VM，也可在 VM 上設定工作負載。

## 關於 Azure 資源管理員範本

Azure 資源管理員範本可讓您藉由定義資源之間的相依性，以宣告方式指定 JSON 語言中的 Azure IaaS 基礎結構。如需 Azure 資源管理員範本的詳細概觀，請參閱以下文章：

- [資源群組概觀](../articles/resource-group-overview.md)
- [以 Azure CLI 部署範本](../articles/virtual-machines/virtual-machines-linux-cli-manage.md)
- [以 Azure Powershell 部署範本](../articles/virtual-machines/virtual-machines-windows-ps-manage.md)

### 必要條件

1. 在[這裡](https://azure.microsoft.com/downloads/)下載適用於您作業系統的 Azure 命令列工具。
2. 如果將會在現有的 VM 上執行指令碼，請確定在該 VM 上已啟用 VM 代理程式；如未啟用，請遵循 [Linux](../articles/virtual-machines/virtual-machines-linux-classic-manage extensions.md) 或 [Windows](../articles/virtual-machines/virtual-machines-windows-classic-manage extensions.md) 的指引，安裝一個 VM 代理程式。
3. 將您想要在 VM 上執行的指令碼上傳到 Azure 儲存體。指令碼可以來自單一或多個儲存體容器。
4. 或者也能將指令碼上傳至 GitHub 帳戶。
5. 指令碼應該以由延伸模組依序要啟動的項目指令碼啟動其他指令碼的方式來撰寫。

## 使用自訂指令碼擴充功能

為了部署範本，我們使用 Azure 服務管理 API 中可用之相同版本的自訂指令碼延伸模組。本延伸模組支援將檔案上傳至 Azure 儲存體帳戶或 Github 位置的相同參數和案例。搭配範本使用時，主要差異在於應該指定正確版本的延伸模組，而不是以 majorversion.* 格式指定版本。

<!---HONumber=AcomDC_0330_2016-->