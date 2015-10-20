<properties
   pageTitle="開始使用內部負載平衡器 | Microsoft Azure"
   description="設定內部負載平衡器，以及如何在虛擬機器和雲端部署中實作。"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="joaoma" />

# 開始設定內部負載平衡器

> [AZURE.SELECTOR]
- [Azure Classic steps](load-balancer-internal-getstarted.md)
- [Resource Manager PowerShell steps](load-balancer-internal-arm-powershell.md)

Azure 內部負載平衡 (ILB) 可在位於雲端服務或虛擬網路 (具有區域範圍) 中的虛擬機器之間提供負載平衡。如需使用和設定具有區域範圍之虛擬網路的相關資訊，請參閱[區域虛擬網路](virtual-networks-migrate-to-regional-vnet.md)。已針對同質群組設定的現有虛擬網路無法使用 ILB。

## 若要建立虛擬機器的內部負載平衡集合

若要建立 Azure 內部負載平衡集合以及會將其流量傳送至該集合的伺服器，您必須執行下列作業：

1. 建立將會是連入流量端點的內部負載平衡執行個體，連入流量會在負載平衡集合的不同伺服器之間進行負載平衡。

1. 新增對應到虛擬機器 (將會接收連入流量) 的端點。

1. 設定即將傳送流量進行負荷平衡的伺服器將其流量傳送到內部負載平衡執行個體的虛擬 IP (VIP) 位址。

### 步驟 1︰建立內部負載平衡執行個體

在現有的雲端服務或在區域虛擬網路下部署的雲端服務中，您可以使用下列 Windows PowerShell 命令來建立內部負載平衡執行個體：

	$svc="<Cloud Service Name>"
	$ilb="<Name of your ILB instance>"
	$subnet="<Name of the subnet within your virtual network>"
	$IP="<The IPv4 address to use on the subnet-optional>"

	Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP


若要使用這些命令，請填入值並移除 < and >。下列是一個範例：

	$svc="WebCloud-NY"
	$ilb="SQL-BE"
	$subnet="Farm1"
	$IP="192.168.98.10"
	Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP


### 步驟 2：將端點加入至內部負載平衡執行個體

在現有的虛擬機器中，您可以使用下列命令將端點新增至內部負載平衡執行個體：

	$svc="<Cloud service name>"
	$vmname="<Name of the VM>"
	$epname="<Name of the endpoint>"
	$lbsetname="<Name of the load balancer set>"
	$prot="tcp" or "udp"
	$locport=<local port number>
	$pubport=<public port number>
	$ilb="<Name of your ILB instance>"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbsetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

若要使用這些命令，請填入值並移除 < and >。

請注意，使用 [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) Windows PowerShell Cmdlet 會使用 DefaultProbe 參數集。如需其他參數集的詳細資訊，請參閱 [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx)。

下列是一個範例：

	$svc="AZ-LOB1"
	$vmname="SQL-LOBAZ1"
	$epname="SQL1"
	$lbsetname="SQL-LB"
	$prot="tcp"
	$locport=1433
	$pubport=1433
	$ilb="SQL ILB"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


### 步驟 3：設定您的伺服器將其流量傳送到新的內部負載平衡端點

您必須設定其流量即將進行負載平衡的伺服器使用內部負載平衡執行個體的新 IP 位址 (VIP)。這是內部負載平衡執行個體所接聽的位址。在大部分情況下，您只需要針對內部負載平衡執行個體的 VIP 新增或修改 DNS 記錄。

如果您在建立內部負載平衡執行個體的過程中指定 IP 位址，則您已具有 VIP。否則，您可以使用下列命令查看 VIP：

	$svc="<Cloud Service Name>"
	Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer



若要使用這些命令，請填入值並移除 < and >。下列是一個範例：

	$svc="WebCloud-NY"
	Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer


在 Get-AzureInternalLoadBalancer 命令的顯示中，請記下 IP 位址，並對伺服器或 DNS 記錄進行必要的變更，以確保流量會被傳送到 VIP。

>[AZURE.NOTE]Microsoft Azure 平台會對各種管理案例使用靜態、可公開路由傳送的 IPv4 位址。IP 位址是 168.63.129.16。此 IP 位址不應該遭到任何防火牆封鎖，因為可能會造成非預期的行為。採用 Azure 內部負載平衡，來自負載平衡器的監視探查會使用此 IP 位址，藉此判斷虛擬機器在負載平衡集的健全狀態。如果網路安全性群組已用來限制傳輸至內部負載平衡集中 Azure 虛擬機器的流量，或已套用至虛擬網路子網路，請確定您已新增網路安全性規則，允許來自 168.63.129.16 的流量。



## 內部負載平衡的端對端範例

若要為兩個範例組態逐步完成建立負載平衡集合的端對端程序，請參閱下列各節。

### 網際網路面向的多層式應用程式

Contoso Corporation 想要提供一組網際網路面向 Web 伺服器和一組資料庫伺服器之間的負載平衡。這兩組伺服器都會裝載於單一 Azure 雲端服務。進入 TCP 通訊埠 1433 的 Web 伺服器流量必須分配到資料庫層中的三部虛擬機器。圖 1 顯示組態。

![資料庫層的內部負載平衡集合](./media/load-balancer-internal-getstarted/IC736321.png)

圖 1：網際網路面向的多層式應用程式範例

組態包含下列各項：

- 裝載虛擬機器的現有雲端服務會命名為 Contoso-PartnerSite。

- 三部現有的資料庫伺服器會命名為 PARTNER-SQL-1、PARTNER-SQL-2 和 PARTNER-SQL-3。

- Web 層中的 Web 伺服器會使用 DNS 名稱 partner-sql.external.contoso.com 連接到資料庫層中的資料庫伺服器。

下列命令會設定名為 PARTNER-DBTIER 的新內部負載平衡執行個體，並將端點新增至對應到三部資料庫伺服器的虛擬機器：

	$svc="Contoso-PartnerSite"
	$ilb="PARTNER-DBTIER"
	Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb

	$prot="tcp"
	$locport=1433
	$pubport=1433
	$epname="DBTIER1"
	$lbsetname="SQL-LB"
	$vmname="PARTNER-SQL-1"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="DBTIER2"
	$vmname="PARTNER-SQL-2"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="DBTIER3"
	$vmname="PARTNER-SQL-3"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

接下來，Contoso 會使用下列命令來確定內部負載平衡的 PARTNER-DBTIER 執行個體 VIP：

	Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer

在這個命令的顯示中，Contoso 會記下 VIP 位址 100.64.65.211，並設定名稱 partner-sql.external.contoso.com 的 DNS 位址 (A) 記錄，以便使用這個新位址。

### 在 Azure 中裝載的 LOB 應用程式

Contoso Corporation 想要在 Azure 的一組 Web 伺服器上裝載特定業務 (LOB) 應用程式。進入 TCP 連接埠 80 的用戶端流量必須負載平衡到跨單位虛擬網路執行的三部虛擬機器。圖 2 顯示組態。

![LOB 應用程式的內部負載平衡](./media/load-balancer-internal-getstarted/IC744148.png)

圖 2：在 Azure 中裝載的 LOB 應用程式範例

組態包含下列各項：

- 裝載虛擬機器的現有雲端服務會命名為 Contoso-Legal。

- LOB 伺服器所在位置上的子網路會命名為 LOB-LEGAL，而且 Contoso 已選擇位址 198.168.99.145 做為內部負載平衡器的 VIP 位址。

- 三部現有的 LOB 伺服器會命名為 LEGAL-1、LEGAL-2 和 LEGAL-3。

- 內部網路 Web 用戶端會使用 DNS 名稱 legalnet.corp.contoso.com 連接至 LOB 伺服器。

下列命令會建立名為 LEGAL-ILB 的內部負載平衡執行個體，並將端點新增至對應到三部 LOB 伺服器的虛擬機器：


	$svc="Contoso-Legal"
	$ilb="LEGAL-ILB"
	$subnet="LOB-LEGAL"
	$IP="198.168.99.145"
	Add-AzureInternalLoadBalancer –ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

	$prot="tcp"
	$locport=80
	$pubport=80
	$epname="LOB1"
	$lbsetname="LOB-LB"
	$vmname="LEGAL-1"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname-LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="LOB2"
	$vmname="LEGAL2"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="LOB3"
	$vmname="LEGAL3"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


接下來，Contoso 會設定 legalnet.corp.contoso.com 名稱的 DNS A 記錄使用 198.168.99.145。

## 將虛擬機器新增至內部負載平衡

若要在建立虛擬機器時將它新增至內部負載平衡執行個體，您可以使用 New-AzureInternalLoadBalancerConfig 和 New-AzureVMConfig Cmdlet。

下列是一個範例：

	$svc="AZ-LOB1"
	$ilb="LOB-ILB"
	$vnet="LOBNet_Azure"
	$subnet="LOBServers"
	$vmname="LOB-WEB1"
	$adminuser="Lando"
	$adminpw="Platform327"
	$regionname="North Central US"

	$myilbconfig=New-AzureInternalLoadBalancerConfig -InternalLoadBalancerName $ilb -SubnetName $subnet
	$images = Get-AzureVMImage
	New-AzureVMConfig -Name $vmname -InstanceSize Small -ImageName $images[50].ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $adminuser -Password $adminpw | New-AzureVM -ServiceName $svc -InternalLoadBalancerConfig $myilbconfig -Location $regionname –VNetName $vnet

## 若要設定雲端服務的內部負載平衡


虛擬機器和雲端服務都支援內部負載平衡。在區域虛擬網路外的雲端服務中所建立的內部負載平衡端點將只能在雲端服務內進行存取。

內部負載平衡組態必須在雲端服務建立第一個部署的過程中進行設定，如以下範例所示。

>[AZURE.IMPORTANT]執行下列步驟的必要條件是已經為雲端部署建立虛擬網路。您需要虛擬網路名稱和子網路名才能建立內部負載平衡。

### 步驟 1

在 Visual Studio 中開啟雲端部署的服務組態檔 (.cscfg) 並新增下列區段，以在網路組態的最後一個 "`</Role>`" 項目下建立內部負載平衡。




	<NetworkConfiguration>
	  <LoadBalancers>
	    <LoadBalancer name="name of the load balancer">
	      <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
	    </LoadBalancer>
	  </LoadBalancers>
	</NetworkConfiguration>


讓我們新增網路組態檔的值，以顯示看起來如何。在此範例中，假設您利用稱為 test\_subnet 的子網路 10.0.0.0/24 和靜態 IP 10.0.0.4 建立稱為 "test\_vnet" 的子網路。負載平衡器將會命名為 testLB。

	<NetworkConfiguration>
	  <LoadBalancers>
	    <LoadBalancer name="testLB">
	      <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
	    </LoadBalancer>
	  </LoadBalancers>
	</NetworkConfiguration>

如需關於負載平衡器結構描述的詳細資訊，請參閱[新增負載平衡器](https://msdn.microsoft.com/library/azure/dn722411.aspx)

### 步驟 2


變更服務定義 (.csdef) 檔案，以將端點新增至內部負載平衡。建立角色執行個體時，服務定義檔會將角色執行個體新增至內部負載平衡。


	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
	    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
	  </Endpoints>
	</WorkerRole>

遵循與上述範例相同的值，讓我們將值新增至服務定義檔。

	<WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
	    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
	  </Endpoints>
	</WorkerRole>

網路流量會使用 testLB 負載平衡器進行負載平衡，使用連接埠 80 進行連入要求，也在連接埠 80 上傳送背景工作角色執行個體。


## 移除內部負載平衡組態

若要將虛擬機器從內部負載平衡執行個體的端點中移除，請使用下列命令：

	$svc="<Cloud service name>"
	$vmname="<Name of the VM>"
	$epname="<Name of the endpoint>"
	Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

若要使用這些命令，請填入值並移除 < and >。

下列是一個範例：

	$svc="AZ-LOB1"
	$vmname="SQL-LOBAZ1"
	$epname="SQL1"
	Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

若要將內部負載平衡執行個體從雲端服務中移除，請使用下列命令：

	$svc="<Cloud service name>"
	Remove-AzureInternalLoadBalancer -ServiceName $svc

若要使用這些命令，請填入值並移除 < and >。

下列是一個範例：

	$svc="AZ-LOB1"
	Remove-AzureInternalLoadBalancer -ServiceName $svc



## 內部負載平衡 Cmdlet 的其他資訊


若要取得內部負載平衡 Cmdlet 的詳細資訊，請在 Windows PowerShell 提示字元中執行下列命令：

- Get-help New-AzureInternalLoadBalancerConfig -full

- Get-help Add-AzureInternalLoadBalancer -full

- Get-help Get-AzureInternalLoadbalancer -full

- Get-help Remove-AzureInternalLoadBalancer -full

## 另請參閱

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Oct15_HO3-->