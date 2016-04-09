
如果您對遠端桌面 (RDP) 連線到以 Windows 為基礎的 Azure 虛擬機器，或 SSH 連線到以 Linux 為基礎的 Azure 虛擬機器，在疑難排解時遇到困難，則此文章將協助您自行減輕困難，而不用尋求支援或重新調整虛擬機器大小。當您透過 Azure PowerShell 叫用重新部署作業時，Microsoft Azure 將會自動重新部署您的虛擬機器。

請注意，此作業完成之後，將會遺失暫時磁碟機資料，且將會更新與虛擬機器關聯的動態 IP 位址。


## 使用 Azure PowerShell

確定您的電腦上已安裝最新版本的 Azure PowerShell 1.x。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../articles/powershell-install-configure.md)。

使用這個 Azure PowerShell 命令來重新部署您的虛擬機器：

	Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


命令執行的時候，在 [Azure 入口網站](https://portal.azure.com)查看您的虛擬機器。請注意，VM 的 [狀態] 變更如下 ︰

1. 初始 [狀態] 是 [正在執行]

	![重新部署的初始狀態](./media/virtual-machines-common-redeploy-to-new-node/statusrunning1.png)

2. [狀態] 變為 [正在更新]

	![重新部署狀態 [正在更新]](./media/virtual-machines-common-redeploy-to-new-node/statusupdating.png)

3. [狀態] 變為 [正在啟動]

	![重新部署狀態 [正在啟動]](./media/virtual-machines-common-redeploy-to-new-node/statusstarting.png)

4. [狀態] 變回 [正在執行]

	![重新部署的最終狀態](./media/virtual-machines-common-redeploy-to-new-node/statusrunning2.png)

當 [狀態] 變回 [正在執行] 時，表示 VM 已重新部署成功。

<!---HONumber=AcomDC_0309_2016-->