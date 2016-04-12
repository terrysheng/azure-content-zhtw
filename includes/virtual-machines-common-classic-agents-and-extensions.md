



VM 擴充功能可協助您：

-   修改安全性與身分識別的功能，例如重設帳戶值和使用反惡意程式碼
-   啟動、停止或設定監視和診斷
-   重設或安裝連線功能，例如 RDP 和 SSH
-   診斷、監視和管理您的 VM

還有許多其他功能。會定期發行新的 VM 擴充功能。這篇文章描述 Windows 和 Linux 的 Azure VM 代理程式，以及它們如何支援 VM 擴充功能。如需依功能分類的 VM 延伸模組清單，請參閱 [Azure VM 延伸模組與功能](../articles/virtual-machines/virtual-machines-windows-extensions-features.md)。

##適用於 Windows 和 Linux 的 Azure VM 代理程式

Azure 虛擬機器代理程式 (VM 代理程式) 是一個安全、輕量級程序，它會安裝、設定和移除 Azure 虛擬機器執行個體映像庫和自訂 VM 執行個體上的 VM 擴充功能，如果已安裝 VM 代理程式的話。VM 代理程式會做為 Azure VM 的安全本機控制服務。代理程式載入的擴充功能提供特定功能，以提升使用執行個體時的產能。

有兩種 Azure VM 代理程式，一種適用於 Windows VM，一種適用於 Linux VM。VM 代理程式預設會在從映像庫建立 VM 時自動安裝，但您也可以在建立執行個體之後安裝 VM 代理程式，或將它安裝在您之後會自行上傳的自訂 VM 映像中。

>[AZURE.IMPORTANT] 這些 VM 代理程式非常精簡的服務，能夠進行虛擬機器執行個體的安全管理。有些情況下您可能不想使用 VM 代理程式。若是如此，請務必建立不會安裝 VM 代理程式的 VM。雖然可以實際移除 VM 代理程式，但是執行個體上的任何 VM 擴充功能的行為並未定義。因此，一旦安裝 VM 代理程式之後，目前並不支援將其移除。

在下列情況下會啟用 VM 代理程式：

-   當您在 Azure 傳統入口網站中使用「快速建立」方法，或在 Azure 傳統入口網站中使用「自訂建立」方法，建立虛擬機器的執行個體時，要確認已選取 [安裝 VM 代理程式] 核取方塊 (如下圖所示)。如需詳細資訊，請參閱[如何建立自訂虛擬機器](../articles/virtual-machines/virtual-machines-windows-classic-createportal.md)。

    ![VM 代理程式核取方塊](./media/virtual-machines-common-classic-agents-and-extensions/IC719409.png)

-   當您使用 [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) 或 [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx) Cmdlet 來建立虛擬機器的執行個體時。您可以藉由將 **–DisableGuestAgent** 參數加入 [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) Cmdlet，來建立 VM 而不需安裝 VM 代理程式。

-   手動下載並在現有 VM 執行個體上安裝 VM 代理程式 (Windows 版或 Linux 版本)，然後使用 PowerShell 或 REST 呼叫將 **ProvisionGuestAgent** 值設定為 **true**。(如果您以手動方式安裝 VM 代理程式之後未設定此值，則不會正確偵測到加入 VM 代理程式。) 下列程式碼範例示範如何使用 PowerShell 執行此動作，其中 `$svc` 和 `$name` 引數都已經確定。

        $vm = Get-AzureVM –ServiceName $svc –Name $name
        $vm.VM.ProvisionGuestAgent = $TRUE
        Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

-   方法是先建立已安裝 VM 代理程式的 VM 映像，然後再將其上傳到 Azure。若為 Windows VM，請下載 [Windows VM Agent.msi 檔案](http://go.microsoft.com/fwlink/?LinkID=394789)，然後安裝 VM 代理程式。若為 Linux VM，將從位於 <https://github.com/Azure/WALinuxAgent> 的 GitHub 存放庫進行安裝。如需如何在 Linux 上安裝 VM 代理程式的詳細資訊，請參閱 [Azure Linux VM 代理程式使用者指南](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md)。

>[AZURE.NOTE]在 PaaS 中，VM 代理程式稱為 **GuestAgent**，且在 Web 和背景工作角色 VM 中皆可使用。(如需詳細資訊，請參閱 [Azure 角色架構](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx))。 角色 VM 的 VM 代理程式現已可將延伸模組加入雲端服務 VM，其方法與永續性虛擬機器相同。角色 VM 與持續性 VM 上的 VM 擴充功能，兩者之間最大的差別是使用角色 VM 時，擴充功能會先加入雲端服務，然後加入該雲端服務中的部署。

>使用 [Get AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) Cmdlet，來列出所有可用的角色 VM 延伸模組。

##尋找、加入、更新和移除 VM 擴充功能  

如需這些工作的詳細資訊，請參閱[加入、尋找、更新及移除 Azure VM 延伸模組](../articles/virtual-machines/virtual-machines-windows-classic-manage-extensions.md)。

<!---HONumber=AcomDC_0330_2016-->