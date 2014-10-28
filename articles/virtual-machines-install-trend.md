<properties title="How to install and configure Trend on an Azure VM" pageTitle="How to install and configure Trend Micro Deep Security as a Service on an Azure VM" description="Describes installing and configuring Trend Micro security on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# 如何在 Azure VM 上安裝和設定 Trend Micro Deep Security as a Service

本文說明如何在執行 Windows Server 的新或現有虛擬機器 (VM) 上，安裝和設定 Trend Micro Deep Security as a Service。Deep Security as a Service 提供的防護包括反惡意程式碼防護、防火牆、入侵防禦系統及完整監視。

透過使用 VM 代理程式，用戶端會安裝為安全性延伸模組。在新的虛擬機器上，您將安裝 VM 代理程式與 Deep Security 代理程式。在沒有 VM 代理程式的現有虛擬機器上，您必須先下載與安裝 VM 代理程式。本文將探討這兩種狀況。

如果您已有 Trend 的內部部署解決方案現有訂閱，您可以用它來保護 Azure 虛擬機器的安全。如果您還不是 Trend 的客戶，您可以註冊試用訂閱。如需此解決方案的詳細資訊，請參閱部落格文章[適用於 Deep Security 的 Microsoft Azure VM 代理程式延伸模組][適用於 Deep Security 的 Microsoft Azure VM 代理程式延伸模組] (英文)。

## 在新的虛擬機器上安裝 Deep Security 代理程式

當您使用 [From Gallery] 選項來建立虛擬機器時，[Azure 管理入口網站][Azure 管理入口網站]可讓您安裝 VM 代理程式和 Trend 安全性延伸模組。如果您打算建立單一虛擬機器，使用此方法可輕易地新增 Trend 的防護。

此 [From Gallery] 選項會開啟可協助您設定虛擬機器的精靈。您可以使用精靈的最後一個頁面，來安裝 VM 代理程式和 Trend 安全性延伸模組。如需一般指示，請參閱[建立執行 Windows Server 的虛擬機器][建立執行 Windows Server 的虛擬機器] (英文)。當您進入精靈的最後一個頁面時，請執行下列動作：

1.  在 [VM Agent] 下，勾選 **[Install VM Agent]**。

2.  在 [Security Extensions] 下，勾選 **[Trend Micro Deep Security Agent]**。

3.  按一下核取記號以建立虛擬機器。

    ![Install the VM Agent and the Deep Security Agent][Install the VM Agent and the Deep Security Agent]

## 在現有虛擬機器上安裝 Deep Security 代理程式

若要執行此作業，您將需要下列項目：

-   Azure PowerShell 模組 0.8.2 版或更新版本。如需最新版本的指示與連結，請參閱[如何安裝和設定 Azure PowerShell][如何安裝和設定 Azure PowerShell]。

-   VM 代理程式。如需有關下載的指示與連結，請參閱部落格文章 [VM 代理程式與延伸模組 - 第 2 部分][VM 代理程式與延伸模組 - 第 2 部分] (英文)。

開啟 Azure PowerShell 工作階段，並執行下列命令。請確定在預留位置中替換成您自己的值，例如 MyServiceName。

1.  取得雲端服務名稱、虛擬機器名稱及 VM，然後將這些資訊以變數的方式儲存，以供下個命令使用：

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] 如果您不知道雲端服務和 VM 名稱，請執行 Get-AzureVM 以顯示目前訂閱中所有 VM 的該項資訊。

2.  將 Deep Security 代理程式新增至虛擬機器：

    `Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA -VM $vm.VM`

    > [WACOM.NOTE] 如果您想要安裝特定版本，請執行下列命令以取得可用版本清單： `Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA`。然後，在執行 Set-AzureVMExtension 時加上 Version 參數。

3.  更新 VM，這會安裝 SDeep Security 代理程式：

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## 後續步驟

安裝代理程式之後，將需要幾分鐘的時間才能開始執行。之後，您必須在虛擬機器上啟用 Deep Security，才能由 Deep Security Manager 進行管理。請參閱下列內容：

-   與此解決方案相關的 Trend 文章：[Microsoft Azure 的即時雲端安全性][Microsoft Azure 的即時雲端安全性] (英文)。
-   設定虛擬機器的 [Windows PowerShell 指令碼範例][Windows PowerShell 指令碼範例] (英文)。
-   範例的[指示][指示] (英文)。

## 其他資源

[如何登入執行 Windows Server 的虛擬機器][如何登入執行 Windows Server 的虛擬機器]

[管理延伸模組][管理延伸模組]

<!--Link references-->

  [適用於 Deep Security 的 Microsoft Azure VM 代理程式延伸模組]: http://go.microsoft.com/fwlink/p/?LinkId=403945
  [Azure 管理入口網站]: http://manage.windowsazure.com
  [建立執行 Windows Server 的虛擬機器]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Install the VM Agent and the Deep Security Agent]: ./media/virtual-machines-install-trend/InstallVMAgentandTrend.png
  [如何安裝和設定 Azure PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [VM 代理程式與延伸模組 - 第 2 部分]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Microsoft Azure 的即時雲端安全性]: http://go.microsoft.com/fwlink/?LinkId=404101
  [Windows PowerShell 指令碼範例]: http://go.microsoft.com/fwlink/?LinkId=404100
  [指示]: http://go.microsoft.com/fwlink/?LinkId=404099
  [如何登入執行 Windows Server 的虛擬機器]: ../virtual-machines-log-on-windows-server/
  [管理延伸模組]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
