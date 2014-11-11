<properties title="How to install and configure Symantec Endpoint Protection on an Azure VM" pageTitle="How to install and configure Symantec Endpoint Protection on an Azure VM" description="Describes installing and configuring Symantec Endpoint Protection on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# 如何在 Azure VM 上安裝和設定 Symantec Endpoint Protection

本文說明如何在執行 Windows Server 的新或現有虛擬機器 (VM) 上，安裝和設定 Symantec Endpoint Protection 用戶端。這是包括服務 (例如病毒和間諜軟體防護、防火牆及入侵防禦) 的完整用戶端。

透過使用 VM 代理程式，用戶端會安裝為安全性延伸模組。在新的虛擬機器上，您將安裝代理程式與端點用戶端。在沒有代理程式的現有虛擬機器上，您必須先下載與安裝代理程式。本文將探討這兩種狀況。

如果您已有 Symantec 的內部部署解決方案現有訂閱，您可以用它來保護 Azure 虛擬機器的安全。如果您還不是 Symantec 的客戶，您可以註冊試用訂閱。如需此解決方案的詳細資訊，請參閱 [Microsoft Azure 平台上的 Symantec Endpoint Protection][Microsoft Azure 平台上的 Symantec Endpoint Protection] (英文)。如果您已經是 Symantec 客戶，此頁面還提供授權資訊的連結，以及安裝用戶端的替代指示。

## 在新的虛擬機器上安裝 Symantec Endpoint Protection

當您使用 **[From Gallery]** 選項來建立虛擬機器時，[Azure 管理入口網站][Azure 管理入口網站]可讓您安裝 VM 代理程式和 Symantec 安全性延伸模組。如果您打算建立單一虛擬機器，使用此方法可輕易地新增 Symantec 的防護。

此 **[From Gallery]** 選項會開啟可協助您設定虛擬機器的精靈。您可以使用精靈的最後一個頁面，來安裝 VM 代理程式和 Symantec 安全性延伸模組。

如需一般指示，請參閱[建立執行 Windows Server 的虛擬機器][建立執行 Windows Server 的虛擬機器] (英文)。當您進入精靈的最後一個頁面時，請執行下列動作：

1.  在 [VM Agent] 下，勾選 **[Install VM Agent]**。

2.  在 [Security Extensions] 下，勾選 **[Symantec Endpoint Protection]**。

    ![Install the VM Agent and the Endpoint Protection Client][Install the VM Agent and the Endpoint Protection Client]

3.  按一下頁面底部的核取記號來建立虛擬機器。

## 在現有虛擬機器上安裝 Symantec Endpoint Protection

若要在現有虛擬機器上安裝 Deep Security Agent，您將需要下列項目：

-   Azure PowerShell 模組 0.8.2 版或更新版本。如需最新版本的指示與連結，請參閱[如何安裝和設定 Azure PowerShell][如何安裝和設定 Azure PowerShell]。

-   VM 代理程式。如需有關下載的指示與連結，請參閱部落格文章 [VM 代理程式與延伸模組 - 第 2 部分][VM 代理程式與延伸模組 - 第 2 部分] (英文)。

開啟 Azure PowerShell 工作階段，並執行下列命令。請確定在預留位置中替換成您自己的值，例如 MyServiceName。

1.  取得雲端服務名稱、虛擬機器名稱及 VM，然後將這些資訊以變數的方式儲存，以供下個命令使用：

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] 如果您不知道雲端服務和 VM 名稱，請執行 Get-AzureVM 以顯示目前訂閱中所有 VM 的該項資訊。

2.  將 Symantec Endpoint Protection 代理程式新增至虛擬機器。若要安裝最新版本，請執行：

    `Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -VM $vm.VM`

    > [WACOM.NOTE] 如果您想要安裝特定版本，請執行下列命令以取得可用版本清單：`Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`。
    > 然後，在執行 Set-AzureVMExtension 時加上 Version 參數。

3.  更新 VM，這會安裝 Symantec Endpoint Protection 代理程式：

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## 其他資源

[如何登入執行 Windows Server 的虛擬機器][如何登入執行 Windows Server 的虛擬機器]

[管理延伸模組][管理延伸模組]

<!--Link references-->

  [Microsoft Azure 平台上的 Symantec Endpoint Protection]: http://go.microsoft.com/fwlink/p/?LinkId=403942
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [建立執行 Windows Server 的虛擬機器]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Install the VM Agent and the Endpoint Protection Client]: ./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png
  [如何安裝和設定 Azure PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [VM 代理程式與延伸模組 - 第 2 部分]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [如何登入執行 Windows Server 的虛擬機器]: ../virtual-machines-log-on-windows-server/
  [管理延伸模組]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
