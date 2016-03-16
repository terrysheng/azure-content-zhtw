<properties
 pageTitle="Azure VM 代理程式和延伸模組 |Microsoft Azure"
 description="提供代理程式和延伸模組的概觀，以及如何使用傳統部署模型來安裝代理程式。"
 services="virtual-machines"
 documentationCenter=""
 authors="squillace"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>

<tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="01/04/2016"
 ms.author="rasquill"/>

#有關虛擬機器代理程式和擴充功能

Azure 虛擬機器代理程式 (VM 代理程式) 用來安裝、設定、管理和執行 Azure 虛擬機器擴充功能 (VM 擴充功能)。VM 擴充功能提供 Microsoft 和其他協力廠商提供的動態功能。代理程式和擴充功能主要是透過管理入口網站來加入，但您也可以在建立 VM 或使用現有的 VM 時使用 [Powershell](../powershell-install-configure.md) Cmdlet 或 [Azure CLI](../xplat-cli-install.md) 來加以加入及設定。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。



VM 擴充功能可協助您：

-   修改安全性與身分識別的功能，例如重設帳戶值和使用反惡意程式碼
-   啟動、停止或設定監視和診斷
-   重設或安裝連線功能，例如 RDP 和 SSH
-   診斷、監視和管理您的 VM

還有許多其他功能。會定期發行新的 VM 擴充功能。這篇文章描述 Windows 和 Linux 的 Azure VM 代理程式，以及它們如何支援 VM 擴充功能。如需依功能分類的 VM 延伸模組清單，請參閱 [Azure VM 延伸模組與功能](virtual-machines-extensions-features.md)。

##適用於 Windows 和 Linux 的 Azure VM 代理程式

Azure 虛擬機器代理程式 (VM 代理程式) 是一個安全、輕量級程序，它會安裝、設定和移除 Azure 虛擬機器執行個體映像庫和自訂 VM 執行個體上的 VM 擴充功能，如果已安裝 VM 代理程式的話。VM 代理程式會做為 Azure VM 的安全本機控制服務。代理程式載入的擴充功能提供特定功能，以提升使用執行個體時的產能。

有兩種 Azure VM 代理程式，一種適用於 Windows VM，一種適用於 Linux VM。根據預設，VM 代理程式會在您從映像庫建立 VM 時自動安裝，但您也可以在建立執行個體之後安裝 VM 代理程式，或將它安裝在您自行上傳的自訂 VM 映像。

>[AZURE.IMPORTANT]這些 VM 代理程式是非常輕量級的服務，能夠進行虛擬機器執行個體的安全管理。有些情況下您可能不想使用 VM 代理程式。若是如此，請務必建立不會安裝 VM 代理程式的 VM。雖然可以實際移除 VM 代理程式，但是執行個體上的任何 VM 擴充功能的行為並未定義。因此，一旦安裝 VM 代理程式之後，目前並不支援加以移除。

在下列情況下會啟用 VM 代理程式：

-   當您在管理入口網站中使用 [快速建立] 方法，或在管理入口網站中使用 [自訂建立] 方法，並確認已選取 [安裝 VM 代理程式] 核取方塊 (如下圖所示)，來建立虛擬機器的執行個體時。如需詳細資訊，請參閱[如何建立自訂虛擬機器](virtual-machines-create-custom.md)。

    ![VM 代理程式核取方塊](media/virtual-machines-extensions-agent-about/IC719409.png)

-   當您使用 [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) 或 [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx) Cmdlet 來建立虛擬機器的執行個體時。您可以藉由將 **–DisableGuestAgent** 參數加入 [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) Cmdlet，來建立 VM 而不需安裝 VM 代理程式。

-   手動下載並在現有 VM 執行個體上安裝 VM 代理程式 (Windows 或 Linux 版本)，然後使用 Powershell 或 REST 呼叫來將 **ProvisionGuestAgent** 值設為 **true**。(如果您以手動方式安裝 VM 代理程式之後未設定此值，則不會正確偵測到加入 VM 代理程式。) 下列程式碼範例示範如何使用 PowerShell 執行此動作，其中 `$svc` 和 `$name` 引數都已經確定。

        $vm = Get-AzureVM –serviceName $svc –Name $name
        $vm.VM.ProvisionGuestAgent = $TRUE
        Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

-   藉由先建立已安裝 VM 代理程式的 VM 映像，然後才上傳到 Azure。若為 Windows VM，請下載 [Windows VM Agent.msi 檔案](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)，然後安裝 VM 代理程式。若為 Linux VM，您將從位於 <https://github.com/Azure/WALinuxAgent> 的 GitHub 儲存機制進行安裝。如需如何在 Linux 上安裝 VM 代理程式的詳細資訊，請參閱 [Azure Linux VM 代理程式使用者指南](virtual-machines-linux-agent-user-guide.md)。

>[AZURE.NOTE]在 PaaS 中，VM 代理程式稱為 **GuestAgent**，而且一律可在 Web 和背景工作角色 VM 中使用(如需詳細資訊，請參閱 [Azure 角色架構](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx))。 現在角色 VM 的 VM 代理程式可以將擴充功能加入雲端服務 VM，其方法與永續性虛擬機器相同。角色 VM 與持續性 VM 上的 VM 擴充功能，兩者之間最大的差別是使用角色 VM 時，擴充功能會先加入雲端服務，然後加入該雲端服務中的部署。

>使用 [Get AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) Cmdlet，來列出所有可用的角色 VM 延伸模組。

##尋找、加入、更新和移除 VM 擴充功能  

如需這些工作的詳細資訊，請參閱[加入、尋找、更新及移除 Azure VM 延伸模組](virtual-machines-extensions-install.md)。

<!---HONumber=AcomDC_0107_2016-->