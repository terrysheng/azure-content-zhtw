<properties
   pageTitle="Windows VM 的技術文章 | Microsoft Azure"
   description="與 Windows 虛擬機器相關的 Microsoft Azure 說明文件文章完整清單"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   tags="azure-service-management,azure-resource-manager"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="danlep"/>

# Azure 中的 Windows VM 的技術文章


尋找在 Microsoft Azure 中建立和管理以 Windows 為基礎的虛擬機器所需的所有說明文件。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Resource Manager 部署模型中的 Windows VM

### 概觀

[關於虛擬機器](virtual-machines-windows-about.md)

[比較 VM、網站與雲端服務](../app-service-web/choose-web-site-cloud-service-vm.md)

[Azure 資源管理員提供的 Azure 運算、網路和儲存提供者](virtual-machines-windows-compare-deployment-models.md)

[了解 Azure Resource Manager 部署](../resource-group-overview.md)

[Azure 中的虛擬機器和容器](virtual-machines-windows-containers.md)

[虛擬機器調整集概觀](virtual-machines-windows-scale-sets-overview.md)



### 環境設定

[免費帳戶](https://azure.microsoft.com/free/)
 
[安裝 Azure PowerShell](../powershell-install-configure.md)

[安裝 Azure CLI](../xplat-cli-install.md)



### 開始使用
[Windows VM 的學習路徑](https://azure.microsoft.com/zh-TW/documentation/learning-paths/virtual-machines/)

[在 Azure 入口網站中建立 Windows 虛擬機器](virtual-machines-windows-classic-tutorial.md)

[使用資源管理員建立 Windows 虛擬機器的不同方式](virtual-machines-windows-creation-choices.md)

[以資源管理員和 PowerShell 建立 Windows VM](virtual-machines-windows-ps-create.md)

[如何登入執行 Windows Server 的虛擬機器](virtual-machines-windows-connect-logon.md)




### 規劃
[虛擬機器的大小](virtual-machines-windows-sizes.md)

[關於 A8、A9、A10 和 A11 密集運算執行個體](virtual-machines-windows-a8-a9-a10-a11-specs.md)

[Azure 虛擬機器的計劃性維護](virtual-machines-windows-planned-maintenance.md) [Azure 基礎結構服務實作指導方針](virtual-machines-windows-infrastructure-service-guidelines.md)

[管理虛擬機器的可用性](virtual-machines-windows-manage-availability.md)

[管理對資源的存取](../active-directory/role-based-access-control-configure.md)

[使用標記來組織 Azure 資源](../resource-group-using-tags.md)


### 部署
[使用 .NET 程式庫和範本部署 Azure 資源](virtual-machines-windows-csharp-template.md)

[如何在資源管理員部署模型中擷取 Windows 虛擬機器](virtual-machines-windows-capture-image.md)

[上傳 VM 映像](virtual-machines-windows-upload-image.md)

[使用 Chef 自動化 Azure 虛擬機器部署](virtual-machines-windows-chef-automation.md)

[利用資源管理員和 Azure PowerShell 建立及設定 Windows 虛擬機器](virtual-machines-windows-create-powershell.md)

[利用 Resource Manager 範本建立 Windows 虛擬機器](virtual-machines-windows-ps-template.md)

[使用 Azure Resource Manager 範本建立具有監視和診斷的 Windows 虛擬機器](virtual-machines-windows-extensions-diagnostics-template.md)

[使用 Azure 資源管理員範本來部署熱門的應用程式架構](virtual-machines-windows-app-frameworks.md)



### 管理
[使用 Azure 命令列介面之 VM 工作的對等資源管理員和服務管理命令](virtual-machines-windows-cli-manage.md)
	
[將傳統 VNET 連接至新的 VNET](../virtual-network/virtual-networks-arm-asm-s2s-howto.md)
	
[如何在 Azure 中標記虛擬機器](virtual-machines-windows-tag.md)

[使用自訂指令碼擴充功能搭配 Azure 資源管理員範本](virtual-machines-windows-extensions-customscript.md)

[使用 Azure 資源管理員和 PowerShell 部署以及管理虛擬機器](virtual-machines-windows-ps-manage.md)
	
[使用 Azure 資源管理員範本和 Azure CLI 部署和管理虛擬機器](virtual-machines-windows-cli-deploy-templates.md)
		
[Azure 虛擬機器中的 SQL Server 自動備份](virtual-machines-windows-classic-ps-sql-backup.md)

[Azure 虛擬機器中的 SQL Server 自動修補](virtual-machines-windows-classic-ps-sql-patch.md)

[在虛擬機器調整集中自動調整機器](virtual-machines-windows-ps-vmss-create.md)



### 設定

[如何重設 Windows VM 的密碼或遠端桌面服務](virtual-machines-windows-reset-rdp.md)

[有關虛擬機器擴充功能和功能](virtual-machines-windows-extensions-features.md)
	
[在 Azure 入口網站中建立完整格式的網域名稱](virtual-machines-windows-portal-create-fqdn.md)

### 儲存體

[有關 Azure 虛擬機器的磁碟和 VHD](virtual-machines-windows-about-disks-vhds.md)
	
[如何在 Azure 入口網站連接資料磁碟](virtual-machines-windows-attach-disk-portal.md)


### 網路

[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)
	
[使用 Azure 入口網站管理 NSG](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
	
[建立負載平衡器](../load-balancer/load-balancer-get-started-internet-arm-ps.md)

	

### 開發

[使用計算、網路和儲存體 .NET 程式庫部署 Azure 資源](virtual-machines-windows-csharp.md)


[使用 VM 延伸模組編寫 Azure Resource Manager 範本](virtual-machines-windows-extensions-authoring-templates.md)

[透過 Visual Studio 建立和部署](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
		
[編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)


### 工作負載

[HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md)

[SQL Server](virtual-machines-windows-classic-sql-overview.md)

[Azure 基礎結構服務工作負載：高可用性企業營運應用程式](virtual-machines-windows-lob.md)

[SharePoint](virtual-machines-windows-sharepoint-farm.md)


### 參考
[搭配使用 Mac、Linux 和 Windows 適用的 Azure CLI 與 Azure 資源管理員](azure-cli-arm-commands.md)

[計算 REST API](https://msdn.microsoft.com/library/azure/mt163647.aspx)

[網路 REST API](https://msdn.microsoft.com/library/azure/mt163658.aspx)

[儲存體 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[計算 .NET API](https://msdn.microsoft.com/library/azure/mt131911.aspx)

[網路 .NET API](https://msdn.microsoft.com/library/azure/dn973320.aspx)

[儲存體 .NET API](https://msdn.microsoft.com/library/azure/mt131037.aspx)

[PowerShell Cmdlet 參考文件](https://msdn.microsoft.com/library/azure/dn708514.aspx)

[Azure 範本社群參考頁面](https://azure.microsoft.com/documentation/templates/)



### 疑難排解

[疑難排解執行 Windows 之 Azure 虛擬機器的遠端桌面連線](virtual-machines-windows-troubleshoot-rdp-connection.md)

[針對 Azure VM 延伸模組的失敗進行疑難排解](virtual-machines-windows-extensions-troubleshoot.md)

[針對資源群組部署進行疑難排解](resource-group-deploy-debug.md)

[疑難排解在 Azure 虛擬機器上執行的應用程式存取](virtual-machines-windows-troubleshoot-app-connection.md)

[在 Azure 中建立、重新啟動或調整 VM 大小時，對配置失敗進行疑難排解](virtual-machines-windows-allocation-failure.md)




## 傳統部署模型中的 Windows VM

### 概觀

[關於虛擬機器](virtual-machines-windows-about.md)

[關於以傳統部署模型建立之 Azure 虛擬機器的常見問題集](virtual-machines-windows-classic-faq.md)

[比較 VM、網站與雲端服務](../app-service-web/choose-web-site-cloud-service-vm.md)

[Azure 中的虛擬機器和容器](virtual-machines-windows-containers.md)



### 環境設定

[免費帳戶](https://azure.microsoft.com/free/)
 
[安裝 Azure PowerShell](../powershell-install-configure.md)

[安裝 Azure CLI](../xplat-cli-install.md)


### 開始使用
[Windows VM 的學習路徑](https://azure.microsoft.com/zh-TW/documentation/learning-paths/virtual-machines/)

[在 Azure 傳統入口網站中建立 Windows 虛擬機器](virtual-machines-windows-classic-tutorial.md)

[如何登入執行 Windows Server 的傳統虛擬機器](virtual-machines-windows-classic-connect-logon.md)




### 規劃

[關於傳統虛擬機器的映像](virtual-machines-windows-classic-about-images.md)

[虛擬機器的大小](virtual-machines-windows-sizes.md)

[關於 A8、A9、A10 和 A11 密集運算執行個體](virtual-machines-windows-a8-a9-a10-a11-specs.md)

[Azure 虛擬機器的計劃性維護](virtual-machines-windows-planned-maintenance.md)

[Azure 基礎結構服務實作指導方針](virtual-machines-windows-infrastructure-service-guidelines.md)

[建立虛擬機器的可用性設定組](virtual-machines-windows-classic-configure-availability.md)


### 部署

[建立一個執行 Windows 的自訂虛擬機器](virtual-machines-windows-classic-createportal.md)

[擷取以傳統部署模型建立的 Windows 虛擬機器](virtual-machines-windows-classic-capture-image.md)

[使用 Powershell 建立並上傳傳統 Windows Server VHD](virtual-machines-windows-classic-createupload-vhd.md)

[使用 Chef 自動化 Azure 虛擬機器部署](virtual-machines-windows-chef-automation.md)

[在 Azure PowerShell 中建立及設定傳統 Windows 虛擬機器](virtual-machines-windows-classic-create-powershell.md)

[將自訂資料插入 Azure 虛擬機器](virtual-machines-windows-classic-inject-custom-data.md)


### 管理

[使用 Azure PowerShell 管理您的虛擬機器](virtual-machines-windows-classic-manage-psh.md)

[使用 Azure 命令列介面之 VM 工作的對等資源管理員和服務管理命令](virtual-machines-windows-cli-manage.md)
	
[將傳統 VNET 連接至新的 VNET](../virtual-network/virtual-networks-arm-asm-s2s-howto.md)
	
[有關虛擬機器代理程式和擴充功能](virtual-machines-windows-classic-agents-and-extensions.md)

[管理虛擬機器擴充功能](virtual-machines-windows-classic-manage-extensions.md)

[傳統 Windows 虛擬機器的自訂指令碼擴充功能](virtual-machines-windows-classic-extensions-customscript.md)

[Azure 虛擬機器中的 SQL Server 自動備份](virtual-machines-windows-classic-ps-sql-backup.md)

[Azure 虛擬機器中的 SQL Server 自動修補](virtual-machines-windows-classic-ps-sql-patch.md)



### 設定

[如何重設 Windows VM 的密碼或遠端桌面服務](virtual-machines-windows-reset-rdp.md)

[有關虛擬機器擴充功能和功能](virtual-machines-windows-extensions-features.md)

[如何在 Windows VM 上安裝和設定 Symantec Endpoint Protection](virtual-machines-windows-classic-install-symantec.md)
	
[如何在 Windows VM 上安裝和設定 Trend Micro Deep Security as a Service](virtual-machines-windows-classic-install-trend.md)

[如何以傳統部署模型設定虛擬機器的可用性設定組](virtual-machines-windows-classic-configure-availability.md)

[如何在傳統的 Azure 虛擬機器上設定端點](virtual-machines-windows-classic-setup-endpoints.md)

### 儲存體

[有關 Azure 虛擬機器的磁碟和 VHD](virtual-machines-windows-about-disks-vhds.md)
	
[如何將資料磁碟連接至傳統 Windows 虛擬機器](virtual-machines-windows-classic-attach-disk.md)

[如何從傳統 Windows 虛擬機器卸離資料磁碟](virtual-machines-windows-classic-detach-disk.md)

[使用 D 磁碟機做為 Windows VM 上的資料磁碟機](virtual-machines-windows-classic-change-drive-letter.md)

### 網路

[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)

[連接藉由虛擬網路或雲端服務，以傳統部署模型建立的虛擬機器](virtual-machines-windows-classic-connect-vms.md)
	
[使用 Azure PowerShell 管理 NSG](../virtual-network/virtual-networks-create-nsg-classic-ps.md)
	
[建立負載平衡器](../load-balancer/load-balancer-get-started-internet-classic-portal.md)

	

### 開發

[在 Visual Studio 中建立和管理 Azure 虛擬機器](virtual-machines-windows-classic-manage-visual-studio.md)

[使用 Visual Studio 建立 Web 應用程式的虛擬機器](virtual-machines-windows-classic-web-app-visual-studio.md)

[如何在 Azure 虛擬機器上以 .NET 執行需密集運算的工作](virtual-machines-windows-classic-run-compute-intensive-task.md)

[如何在虛擬機器上以 Java 執行大量運算工作](virtual-machines-windows-classic-java-run-compute-intensive-task.md)

[Windows Server VM 上的 Django Hello World Web 應用程式](virtual-machines-windows-classic-python-django-web-app.md)
		


### 工作負載

[高可用性的企業營運應用程式](virtual-machines-windows-lob.md)

[HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md)

[混合式雲端測試環境](virtual-machines-windows-classic-hybrid-test-env.md)

[MongoDB](virtual-machines-windows-classic-install-mongodb.md)

[MySQL](virtual-machines-windows-classic-mysql-2008r2.md)

[Oracle](virtual-machines-windows-classic-oracle.md)

[SQL Server](virtual-machines-windows-classic-sql-overview.md)

[SharePoint](virtual-machines-windows-sharepoint-farm.md)

[Tomcat](virtual-machines-windows-classic-java-run-tomcat-app-server.md)

### 參考
[使用適用於 Mac、Linux 和 Windows 的 Azure CLI 搭配 Azure 服務管理](../virtual-machines-command-line-tools.md)

[服務管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)

[服務管理 .NET API](https://msdn.microsoft.com/library/azure/mt420161.aspx)

[Azure 服務管理 PowerShell Cmdlet 參考文件](https://msdn.microsoft.com/library/azure/dn708504.aspx)


### 疑難排解

[疑難排解執行 Windows 之 Azure 虛擬機器的遠端桌面連線](virtual-machines-windows-troubleshoot-rdp-connection.md)

[疑難排解在 Azure 虛擬機器上執行的應用程式存取](virtual-machines-windows-troubleshoot-app-connection.md)

[在 Azure 中建立、重新啟動或調整 VM 大小時，對配置失敗進行疑難排解](virtual-machines-windows-allocation-failure.md)

<!---HONumber=AcomDC_0323_2016-->