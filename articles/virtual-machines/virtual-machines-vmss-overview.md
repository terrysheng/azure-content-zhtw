<properties
	pageTitle="虛擬機器調整集概觀 | Microsoft Azure"
	description="深入了解虛擬機器調整集"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="guybo"/>

# 虛擬機器調整集概觀

虛擬機器調整集是一個您可以用來部署和管理一組相同 VM 的 Azure 計算資源。藉由將所有的 VM 進行相同設定，設計 VM 調整集以支援真正的自動調整 (不需要預先佈建 VM)，因而能夠更輕鬆地建置以大型計算、巨量資料和容器化工作負載為目標的大規模服務。

[AZURE.INCLUDE [virtual-machines-vmss-preview](../../includes/virtual-machines-vmss-preview-ps-include.md)]

對於需要相應放大計算資源的應用程式，調整作業會隱含地平衡分散到容錯網域和更新網域。如需 VM 調整集的簡介，請參閱最近的 [Azure 部落格通知](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview)。

若想進一步了解 VM 調整集，請觀看下列影片：

 - [Mark Russinovich 講述 Azure 調整集](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  

 - [Guy Bowerman 與虛擬機器調整集](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## 建立和管理 VM 調整集

VM 調整集可以使用 JSON 範本和 [REST API](https://msdn.microsoft.com/library/mt589023.aspx) 來定義和部署，如同個別的 Azure 資源管理員 VM 一樣。因此，您可以使用任何標準 Azure 資源管理員部署方法。如需範本的詳細資訊，請參閱[編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

在 Azure 快速入門範本 GitHub 儲存機制中可以找到 VM 調整集的範例範本集：

[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) - 尋找標題中包含 _vmss_ 的範本。

在這些範本的詳細資料頁面中，您會看到連結至入口網站部署功能的按鈕。若要部署 VM 調整集，請按一下該按鈕，然後在入口網站中填入所需的任何參數。如果您不確定某項資源是否支援大寫或混合大小寫，一律使用小寫參數值會比較安全。此外也有 VM 調整集範本的視訊剖析，非常容易存取：

[VM 調整集範本剖析](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## 相應放大和相應縮小 VM 調整集

若要增加或減少 VM 調整集內的虛擬機器數目，請直接變更 _capacity_ 屬性，並重新部署範本。這樣的單純性，可讓您在想要定義不受 Azure 自動調整支援的自訂調整事件時，能更輕易地撰寫您的自訂調整層。

如果您想要重新部署範本以變更容量，您可以定義小得多的範本，使其僅包含 SKU 和更新的容量。其範例顯示如下：[https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)。

若要逐步完成相關步驟以建立會自動調整的調整集，請參閱[在虛擬機器調整集中自動調整機器](virtual-machines-vmss-walkthrough.md)

## 監視 VM 調整集

就目前而言，建議您使用 [Azure 資源總管](https://resources.azure.com)來檢視 VM 調整集。VM 調整集是 Microsoft.Compute 之下的一項資源，因此您可以展開下列連結，從這個網站加以檢視：

	subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## VM 調整集案例

本節列出一些典型的 VM 調整集案例。某些較高階的 Azure 服務 (例如 Batch、Service Fabric、Azure 容器服務) 也會使用這些案例。

 - **透過 RDP/SSH 連接到 VM 調整集執行個體** - VM 調整集會建立在 VNET 內，且不會為其中的個別 VM 配置公用 IP 位址。這是件好事，因為您通常不希望計算方格中，為了要將個別 IP 位址配置給所有的無狀態資源，而產生支出與管理負擔，而且您可以輕鬆地從 VNET 中的其他資源連接到這些 VM，包括具有公用 IP 位址的資源，像是負載平衡器或獨立虛擬機器。

 - **使用 NAT 規則連接到 VM** - 您可以建立一個公用 IP 位址、將它指派給負載平衡器，然後定義將 IP 位址的連接埠對應至 VM 調整集內某個 VM 之連接埠的輸入 NAT 規則。例如

	Public IP->Port 50000 -> vmss\_0->Port 22 Public IP->Port 50001 -> vmss\_1->Port 22 Public IP->Port 50002-> vmss\_2->Port 22

	以下範例會建立一個使用 NAT 規則的 VM 調整集，以便能使用單一公用 IP，對調整集內的每個 VM 進行 SSH 連線：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

	以下是使用 RDP 和 Windows 執行相同作業的範例：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

 - **使用 Jumpbox 連接到 VM** - 如果您在相同的 VNET 中建立 VM 調整集和獨立 VM，獨立 VM 與 VMSS VM 將可使用 VNET/子網路為它們定義的內部 IP 位址彼此連接。如果您建立公用 IP 位址，並將它指派給獨立 VM，您可以透過 RDP 或 SSH 連接到獨立 VM，然後從該部機器連接到 VM 調整集執行個體。此時您可能會發現，簡易 VM 調整集在本質上比在預設組態中設定了公用 IP 位址的獨立 VM 來得安全。

	為了提供此方法的範例，此範本建立了一個簡易 Mesos 叢集，其中包含一個獨立的主要 VM，用來管理 VM 的 VM 調整集架構叢集：[https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **對 VM 調整集執行個體進行循環配置資源負載平衡** - 如果您想要使用「循環配置資源」方法將工作傳送到 VM 的計算叢集，您可以使用負載平衡規則據以設定 Azure 負載平衡器。您也可以定義探查，藉由使用指定的通訊協定、間隔和要求路徑對連接埠執行 ping，以驗證您的應用程式正在執行中。

	以下範例會為執行 IIS Web 伺服器的 VM 建立一個 VM 調整集，並使用負載平衡器來平衡每個 VM 獲得的負載。此外也會使用 HTTP 通訊協定對每個 VM 的特定 URL 執行 ping：[https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) - 請查看 Microsoft.Network/loadBalancers 資源類型，以及 virtualMachineScaleSet 中的 networkProfile 和 extensionProfile。

 - **在 PaaS 叢集管理員中，將 VM 調整集部署為計算叢集** - VM 調整集有時會被稱為新一代的背景工作角色。雖然這可說是有效的說明，但也可能導致調整集功能與 PaaS v1 背景工作角色功能令人混淆。就某方面來說，VM 調整集提供了真正的「背景工作角色」或背景工作資源，因為它們提供了不會隨平台/執行階段而改變、可自訂且整合至 Azure 資源管理員 IaaS 的一般化計算資源。

	PaaS v1 背景工作角色雖然在平台/執行階段支援方面有所限制 (僅限 Windows 平台映像)，但也包含像是 VIP 交換、可設定的升級設定、執行階段/應用程式部署的特定設定等服務，但這些服務_尚未_成為 VM 調整集內的可用項目，或將在其他較高階的 PaaS 服務 (如 Service Fabric) 中提供。了解這一點之後，您可以將 VM 調整集視為支援 PaaS 的基礎結構。亦即PaaS 解決方案 (如 Service Fabric) 或叢集管理員 (如 Mesos) 可以建置在 VM 調整集之上，作為可調整的計算層。

	以下 Mesos 叢集範例會在與獨立 VM 相同的 VNET 中部署 VM 調整集。獨立 VM 是 Mesos 主機，而 VM 調整集則代表一組從屬節點：[https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)。未來的 [Azure 容器服務](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/)版本將根據 VM 調整集，為此案例部署更複雜/強化的版本。

## VM 調整集的效能和調整指南

- 在公用預覽期間，請勿同時在多個 VM 調整集內建立 500 個以上的 VM。
- 請勿為每個儲存體帳戶規劃超過 40 個 VM。
- 盡可能分散儲存體帳戶名稱的第一個字母。[Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/)中的範例 VMSS 範本提供如何執行這項操作的範例。
- 如果使用自訂 VM，在單一儲存體帳戶中，請勿為每個 VM 調整集規劃超過 40 個 VM。您必須將映像預先複製到儲存體帳戶中，才能開始進行 VM 調整集部署。如需詳細資訊，請參閱常見問題集。
- 請勿為每個 VNET 規劃超過 2048 個 VM。此限制在未來將會放寬。
- 在任何區域中，您可以建立的 VM 數目都會受到計算核心配額的限制。您可能需要連絡客戶支援人員，以提高您的計算配額限制，即使您目前用於雲端服務或 IaaS v1 的核心已具有較高的上限亦然。若要查詢您的配額，您可以執行下列 Azure CLI 命令：_azure vm list-usage_ 和下列 PowerShell 命令：_Get-AzureRmVMUsage_ (如果您使用 1.0 以下的 PowerShell 版本，請使用 _Get-AzureVMUsage_)。

## VM 調整集常見問題集

**問：** 您在 VM 調整集內可以有多少個 VM？

**答：** 如果您使用可以分散到多個儲存體帳戶的平台映像，則是 100 個。如果您使用自訂映像，則最多是 40 個，因為自訂映像在預覽期間受限於單一儲存體帳戶。

**問：**VM 調整集有哪些其他資源的限制？

**答：** 在預覽期間，會有在多個調整集內為每個區域建立的 VM 不可超過 500 個的限制。適用現有的 [Azure 訂用帳戶服務限制/](../azure-subscription-service-limits.md)。

**問：** 在 VM 調整集內是否支援資料磁碟？

**答：** 在初始版本中不支援。您的資料儲存選項包括：

- Azure 檔案 (SMB 共用磁碟機)

- OS 磁碟機

- 暫存磁碟機 (本機，未受 Azure 儲存體支援)

- 外部資料服務 (例如遠端 DB、Azure 資料表、Azure blob)

**問：** 哪些 Azure 區域支援 VM 調整集？

**答：** 任何支援 Azure 資源管理員的區域，都支援 VM 調整集。

**問：** 要如何使用自訂映像建立 VM 調整集？

**答：** 將 vhdContainers 屬性保留為空白，例如：

	"storageProfile": {
		"osDisk": {
			"name": "vmssosdisk",
			"caching": "ReadOnly",
			"createOption": "FromImage",
			"image": {
				"uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
			},
			"osType": "Windows"
		}
	},


**問：** 如果我將 VMS 調整集容量從 20 減少為 15，VM 會被移除嗎？

**答：** 虛擬機器會跨升級網域和容錯網域從調整集平均地移除，以達到最大的可用性。

**問：** 如果我後續又將容量從 15 增加到 18 呢？

**答：** 如果增加到 18，將會建立具有索引鍵 15、16、17 的 VM。在這兩種情況下，VM 都會分散到 FD 和 UD 進行平衡。

**問：** 在一個 VM 調整集內使用多個延伸模組時，是否可以強制執行「執行順序」？

**答：** 並非直接，但對於 customScript 延伸模組，您的指令碼可以等候另一個延伸模組完成 (例如，藉由監視延伸模組記錄檔 - 請參閱 [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh))。

**問：** VM 調整集是否可與 Azure 可用性設定組組搭配使用？

**答：** 是。VM 調整集是隱含的可用性設定組，具有 3 個 FD 和 5 個 UD。您不需要在 virtualMachineProfile 下進行任何設定。在未來的版本中，VM 調整集有可能跨越多個租用戶，但目前調整集只是單一可用性設定組。

<!---HONumber=AcomDC_0114_2016-->