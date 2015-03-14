<properties 
	pageTitle="如何在 Azure VM 上安裝和設定 Symantec Endpoint Protection" 
	description="說明如何在 Azure 的 VM 上安裝和設定 Symantec Endpoint Protection" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/26/2015" 
	ms.author="kathydav"/>

#如何在 Azure VM 上安裝和設定 Symantec Endpoint Protection

本文說明如何在執行 Windows Server 的新或現有虛擬機器 (VM) 上，安裝和設定 Symantec Endpoint Protection 用戶端。這是包括服務 (例如病毒和間諜軟體防護、防火牆及入侵防禦) 的完整用戶端。 

透過使用 VM 代理程式，用戶端會安裝為安全性延伸模組。在新的虛擬機器上，您將安裝代理程式與端點用戶端。在沒有代理程式的現有虛擬機器上，您必須先下載與安裝代理程式。本文將探討這兩種狀況。

如果您已有 Symantec 的內部部署解決方案現有訂閱，您可以用它來保護 Azure 虛擬機器的安全。如果您還不是 Trend 的客戶，您可以註冊試用訂閱。如需此解決方案的詳細資訊，請參閱 [Microsoft Azure 平台上的 Symantec Endpoint Protection](http://go.microsoft.com/fwlink/p/?LinkId=403942) (英文)。如果您已經是 Symantec 客戶，此頁面還提供授權資訊的連結，以及安裝用戶端的替代指示。

##在新的虛擬機器上安裝 Symantec Endpoint Protection

當您使用 [From Gallery] 選項來建立虛擬機器時，[Azure 管理入口網站](http://manage.windowsazure.com)可讓您安裝 VM 代理程式和 Symantec 安全性延伸模組。如果您打算建立單一虛擬機器，使用此方法可輕易地新增 Symantec 的防護。 

這個 [從組件庫] 選項會開啟可協助您設定虛擬機器的精靈。您可以使用精靈的最後一個頁面，來安裝 VM 代理程式和 Symantec 安全性延伸模組。 

如需一般指示，請參閱[建立執行 Windows Server 的虛擬機器](http://go.microsoft.com/fwlink/p/?LinkId=403943)。當您進入精靈的最後一個頁面時：

1.	在 [VM 代理程式] 底下，應該已經勾選 [安裝 VM 代理程式]。

2.	在 [安全性延伸模組] 底下，勾選 [Symantec Endpoint Protection]。


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	按一下頁面底部的核取記號來建立虛擬機器。

## 在現有虛擬機器上安裝 Symantec Endpoint Protection

在開始之前，您將需要下列項目：

- Azure PowerShell 模組 0.8.2 版或更新版本。如需最新版本的指示與連結，請參閱[如何安裝和設定 Azure PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320552)。  

- VM 代理程式。如需有關下載的指示與連結，請參閱部落格文章 [VM 代理程式與延伸模組 - 第 2 部分](http://go.microsoft.com/fwlink/p/?LinkId=403947)。

在現有的虛擬機器上安裝 Symantec 安全性延伸模組：

1.	取得雲端服務名稱和虛擬機器名稱。如果您不知道它們，請使用 **Get-AzureVM** 命令，以顯示該資訊適用於目前訂閱中的所有 VM。然後，取代括號內部的任何內容，包括 < 和 > 字元，然後執行這些命令：

	<p>`$servicename = "<YourServiceName>"`
<p>`$name = "<YourVmName>"`
<p>`$vm = Get-AzureVM -ServiceName $servicename -Name $name`
<p>`Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`

2.	從 Get-AzureVMAvailableExtension 命令顯示的內容中，記下版本內容的版本號碼，然後執行這些命令：

	<p>`$ver=<version number from the Version property>`
<p>`Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -Version $ver -VM $vm.VM`
<p>`Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

確認 Symantec 安全性延伸模組已安裝且是最新版本：

1.	登入虛擬機器。
2.	如果是 Windows Server 2008 R2，請按一下 [開始] > [所有程式] > [Symantec Endpoint Protection]。如果是 Windows Server 2012，請從開始畫面，輸入 **Symantec**，然後按一下 [Symantec Endpoint Protection]。
3.	如有需要，請從狀態視窗套用更新。

## 其他資源
[如何登入執行 Windows Server 的虛擬機器][How to Log on to a Virtual Machine Running Windows Server]

[管理延伸模組][Manage Extensions]

<!--Link references-->
[How to Log on to a Virtual Machine Running Windows Server]: ../virtual-machines-log-on-windows-server/

[Manage Extensions]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409



<!--HONumber=42-->
