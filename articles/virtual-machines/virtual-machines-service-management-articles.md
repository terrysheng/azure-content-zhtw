<properties
	pageTitle="服務管理中針對虛擬機器的文章 | Microsoft Azure"
	description="本文列出的資源有助於您在 Azure 服務管理中建立並管理虛擬機器。"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-multiple"
	ms.workload="infrastructure-services"
	ms.date="07/28/2015"
	ms.author="danlep"/>

# 服務管理中針對虛擬機器的文章
這是在 Azure 服務管理中使用虛擬機器的文章清單。針對新的部署，我們建議使用 Azure 資源管理員為基礎的虛擬機器和其他資源。如需資源管理員優點的說明，請參閱 [Azure 資源管理員底下的 Azure 運算、網路和儲存體提供者](virtual-machines-azurerm-versus-azuresm.md)。

## 開始使用

[在 Azure 入口網站中建立執行 Windows 的虛擬機器](virtual-machines-windows-tutorial-classic-portal.md)

[在 Azure 中建立一個執行 Windows 的自訂虛擬機器](virtual-machines-windows-create-custom.md)

[在 Azure 中建立執行 Linux 的自訂虛擬機器](virtual-machines-linux-create-custom.md)

[教學課程：在 Azure 中建立純雲端虛擬網路](create-virtual-network.md)

## 自動化

[使用 Azure PowerShell 建立和預先設定以 Linux 為基礎的虛擬機器](virtual-machines-ps-create-preconfigure-linux-vms.md)

[使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](virtual-machines-ps-create-preconfigure-windows-vms.md)

[使用 Azure PowerShell 建立和管理以 Windows 為基礎的虛擬機器](virtual-machines-create-windows-powershell-service-manager.md)

[使用 Azure 命令列介面 (Azure CLI) 建立 VM](virtual-machines-xplat-getting-started.md)

[使用 Azure CLI 建立多個 VM 的部署](virtual-machines-create-multi-vm-deployment-xplat-cli.md)

## 規劃

[Azure 基礎結構服務實作指導方針](virtual-machines-infrastructure-services-implementation-guidelines.md)

[我需要虛擬網路嗎？](../virtual-network/virtual-networks-overview.md)

## 建立

[建立並上傳 Windows Server VHD 到 Azure](virtual-machines-create-upload-vhd-windows-server.md)

[建立及上傳包含 Linux 作業系統的虛擬硬碟](virtual-machines-linux-create-upload-vhd.md)

[如何擷取以 Windows 為基礎的虛擬機器作為映像使用](virtual-machines-capture-image-windows-server.md)


[如何擷取以 Linux 為基礎的虛擬機器作為範本使用](virtual-machines-linux-capture-image.md)

[如何使用 CLI 擷取以 Linux 為基礎的虛擬機器以做為範本](virtual-machines-vm-capture-image-cli.md)


## 管理

[如何登入執行 Windows Server 的虛擬機器](virtual-machines-log-on-windows-server.md)

[如何登入執行 Linux 的虛擬機器](virtual-machines-linux-how-to-log-on.md)

[如何將資料磁碟連接至以 Windows 為基礎的虛擬機器](storage-windows-attach-disk.md)

[如何從以 Windows 為基礎的虛擬機器卸離資料磁碟](storage-windows-detach-disk.md)

[如何將資料磁碟連接至以 Linux 為基礎的虛擬機器](virtual-machines-linux-how-to-attach-disk.md)

[如何從以 Linux 為基礎的虛擬機器卸離資料磁碟](virtual-machines-linux-how-to-detach-disk.md)

[如何連接虛擬機器與虛擬網路或雲端服務](cloud-services-connect-virtual-machine.md)

[如何設定虛擬機器的可用性集合](virtual-machines-how-to-configure-availability.md)

[開始設定內部負載平衡器](../load-balancer/load-balancer-internal-getstarted.md)

[開始設定網際網路面向的負載平衡器](../load-balancer/load-balancer-internet-getstarted.md)

[如何設定虛擬機器的端點](virtual-machines-set-up-endpoints.md)

## 建立工作負載

[Active Directory](https://msdn.microsoft.com/library/azure/jj156090.aspx)

[BizTalk Server](https://msdn.microsoft.com/library/azure/jj248689)

[Cassandra](virtual-machines-linux-nodejs-running-cassandra.md)

[Docker](virtual-machines-docker-with-xplat-cli.md)

[Exchange Server](https://technet.microsoft.com/library/jj619301.aspx)

[HPC Pack](https://msdn.microsoft.com/library/azure/dn518135.aspx)

[LAMP 堆疊](virtual-machines-linux-install-lamp-stack.md)

[訊息佇列 (MSMQ)](https://msdn.microsoft.com/library/azure/dn529082.aspx)

[Microsoft Dynamics AX](https://technet.microsoft.com/library/dn741581.aspx)

[Microsoft Dynamics NAV](https://msdn.microsoft.com/library/azure/dn168977.aspx)

[在 Windows Server 上的 MongoDB](virtual-machines-install-mongodb-windows-server.md)

[在 Windows Server 上的 MySQL](virtual-machines-mysql-windows-server-2008r2.md)

[Oracle](virtual-machines-oracle-azure-virtual-machines.md)

[SAP](https://msdn.microsoft.com/library/azure/dn745892.aspx)

[SharePoint Server](virtual-machines-workload-intranet-sharepoint-farm.md)

[SQL Server](virtual-machines-sql-server-infrastructure-services.md)

[System Center App Controller](https://technet.microsoft.com/library/dn249764.aspx)

[System Center Operations Manager](https://technet.microsoft.com/library/dn249696.aspx#BKMK_Azure)

[Team Foundation Server](https://msdn.microsoft.com/library/azure/dn769056.aspx)

[Windows Server Essentials 經驗](https://msdn.microsoft.com/library/azure/dn520827.aspx)

## 監視

[在 Azure 雲端服務和虛擬機器中啟用診斷](../cloud-services/cloud-services-dotnet-diagnostics.md)

[在 Visual Studio 中進行雲端服務或虛擬機器的偵錯](https://msdn.microsoft.com/library/azure/ff683670.aspx)

[使用 Windows PowerShell 指令碼來發行至開發和測試環境](https://msdn.microsoft.com/library/azure/dn642480.aspx)

## 疑難排解

[疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線](virtual-machines-troubleshoot-remote-desktop-connections.md)

[疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線](virtual-machines-troubleshoot-ssh-connections.md)

## 參考

[Azure 服務管理 Cmdlet](https://msdn.microsoft.com/library/azure/dn708504.aspx)

[服務管理 REST API 參考](https://msdn.microsoft.com/library/azure/ee460799.aspx)

[使用適用於 Mac、Linux 和 Windows 的 Azure CLI 搭配 Azure 服務管理](virtual-machines-command-line-tools.md)

<!---HONumber=August15_HO9-->