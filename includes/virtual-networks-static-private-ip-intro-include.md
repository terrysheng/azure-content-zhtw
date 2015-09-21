# 靜態內部 IP

您的 IaaS 虛擬機器 (VM) 和虛擬網路中的 PaaS 角色執行個體會從您指定的範圍自動接收內部 IP 位址。在受到解除委任之前，VM 和角色執行個體會保留該位址。您藉由從 PowerShell、Azure CLI 或 Azure 入口網站停止 VM 或角色執行個體來加以解除委任。在這些情況下，一旦 VM 或角色執行個體再次開始，即會收到來自 Azure 基礎結構的可用 IP 位址，其可能與先前的不同。在某些情況下，您不會希望 VM 或角色執行個體具有動態 IP 位址，例如，如果您的 VM 即將執行 DNS 或將成為網域控制站。

>[AZURE.NOTE]如果您從客體作業系統關閉 VM，VM 會保留動態內部 IP 位址。只有從 Azure 入口網站、PowerShell 或 Azure CLI 停止 VM 時才可加以解除委任。

<!---HONumber=Sept15_HO2-->