<properties 
   pageTitle="VM 與角色執行個體的解析"
   description="Azure IaaS、混合式解決方案、不同雲端服務之間、Active Directory 以及使用專屬 DNS 伺服器的名稱解析案例"
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/20/2016"
   ms.author="joaoma" />

# VM 與角色執行個體的名稱解析

根據您使用 Azure 來裝載 IaaS、PaaS 以及混合式解決方案的方式，您可能需要允許您建立的 VM 與角色執行個體彼此通訊。雖然可以使用 IP 位址來進行這項通訊，但是使用能輕鬆記住且不會變更的名稱會更加簡單。

當 Azure 中裝載的角色執行個體和 VM 必須將網域名稱解析為內部 IP 位址時，可以使用兩種方法中的其中一種：

- [Azure 提供的名稱解析](#azure-provided-name-resolution)

- [使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server) (可將查詢轉送到 Azure 提供的 DNS 伺服器)

您使用的名稱解析類型取決於 VM 和角色執行個體如何彼此通訊。

**下表說明各種案例和對應的名稱解析方案：**

| **案例** | **解決方案** | **尾碼** |
|--------------|--------------|----------|
| 位於相同雲端服務或虛擬網路中的角色執行個體或 VM 之間的名稱解析 | [Azure 提供的名稱解析](#azure-provided-name-resolution)| 主機名稱或 FQDN |
| 位於不同虛擬網路中的角色執行個體或 VM 之間的名稱解析 | 客戶管理的 DNS 伺服器將 vnet 之間的查詢轉送供 Azure (DNS Proxy) 解析。請參閱[使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)| 僅 FQDN |
| 解析 Azure 中角色執行個體或 VM 的內部部署電腦及伺服器名稱 | 客戶管理的 DNS 伺服器 (例如內部部署的網域控制站、本機唯讀網域控制站或使用區域傳輸同步的次要 DNS)。請參閱[使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)|僅 FQDN |
| 從內部部署電腦解析 Azure 主機名稱 | 將查詢轉送到所對應 vnet 中客戶管理的 DNS Proxy 伺服器，Proxy 伺服器將查詢轉送給 Azure 進行解析。請參閱[使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)| 僅 FQDN |
| 內部 IP 的反向 DNS | [使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server) | n/a |
| 位於不同雲端服務，不在虛擬網路中的 VM 或角色執行個體之間的名稱解析| 不適用。虛擬網路外部不支援不同雲端服務中 VM 和角色執行個體之間的連線。| n/a |



## Azure 提供的名稱解析

除了公用 DNS 名稱的解析之外，Azure 也提供位於相同虛擬網路或雲端服務內的 VM 和角色執行個體的內部名稱解析。雲端服務中的 VM/執行個體共用相同的 DNS 尾碼，因此主機名稱本身就已足夠；但在傳統虛擬網路中，不同的雲端服務有不同的 DNS 尾碼，因此需要 FQDN 來解析不同雲端服務的名稱。在 ARM 型虛擬網路中，DNS 尾碼在虛擬網路之間是一致的，因此不需要 FQDN，而 DNS 名稱則可以指派給 NIC 和虛擬機器。雖然 Azure 提供的名稱解析不需要任何設定，但它不適用於所有部署案例，如上表所示。

> [AZURE.NOTE] 如果是 Web 和背景工作角色，您也可以使用 Azure 服務管理 REST API，根據角色名稱與執行個體數目，存取角色執行個體的內部 IP 位址。如需詳細資訊，請參閱[服務管理 REST API 參考](https://msdn.microsoft.com/library/azure/ee460799.aspx)。

### 功能和注意事項

**功能：**

- 方便使用：不需要設定，就能使用 Azure 提供的名稱解析。

- Azure 提供的名稱解析服務都高度可用，可省去您建立和管理 DNS 伺服器叢集的需求。

- 可與您自己的 DNS 伺服器搭配使用，以解析內部部署及 Azure 主機名稱。

- 提供名稱解析給相同雲端服務中的角色執行個體/VM，不需要 FQDN。

- 提供 ARM 型虛擬網路中 VM 之間的名稱解析，不需要 FQDN，在不同的雲端服務中解析名稱時，傳統虛擬網路需要 FQDN。

- 您可以使用最能描述部署的主機名稱，而不是使用自動產生的名稱。

**注意事項：**

- Azure 建立的 DNS 尾碼不能修改。

- 您無法手動註冊您自己的記錄。

- 不支援 WINS 和 NetBIOS。(您無法在「Windows 檔案總管」中看到您的 VM)。

- 主機名稱必須是 DNS 相容 (只能使用 0-9、a-z 和 '-'，無法以 '-' 開始或結束。請參閱 RFC 3696 第 2 節)。

- 每個 VM 的 DNS 查詢流量已經過節流。這應該不會影響大部分的應用程式。如果觀察到要求節流，請確定用戶端快取已啟用。如需詳細資料，請參閱[充分利用 Azure 提供的名稱解析](#Getting-the-most-from-Azure-provided-name-resolution)。

- 只有前 180 個雲端服務中的 VM 會為每個傳統虛擬網路註冊。這並不適用於 ARM 型虛擬網路。


### 充分利用 Azure 提供的名稱解析
**用戶端快取：**

並非所有的 DNS 查詢都需要透過網路傳送。用戶端快取可藉由解決本機快取的週期性 DNS 查詢，協助減少延遲以及改善網路標誌的恢復能力。DNS 記錄包含存留時間 (TTL)，可讓快取盡可能長時間儲存記錄而不會影響記錄的有效性，所以用戶端快取適用於大部分的情況。

預設 Windows DNS 用戶端有內建的 DNS 快取。某些 Linux 發行版預設不包含快取功能，因此我們建議您 (在已經確認沒有本機快取之後) 為每個 Linux VM 新增快取。

有許多不同 DNS 快取封裝可用，例如 dnsmasq，以下是在最常見的散發版本上安裝 dnsmasq 的步驟：

- **Ubuntu (使用 resolvconf)**：
	- 只安裝 dnsmasq 封裝 (“sudo apt-get install dnsmasq”)。
- **SUSE (使用 netconf)**：
	- 安裝 dnsmasq 封裝 (“sudo zypper install dnsmasq”) 
	- 啟用 dnsmasq 服務 (“systemctl enable dnsmasq.service”) 
	- 啟動 dnsmasq 服務 (“systemctl start dnsmasq.service”) 
	- 編輯 “/etc/sysconfig/network/config” 並將 NETCONFIG\_DNS\_FORWARDER="" 變更為 ”dnsmasq”
	- 更新 resolv.conf ("netconfig update") 來設定快取做為本機 DNS 解析程式
- **OpenLogic (使用 NetworkManager)**：
	- 安裝 dnsmasq 封裝 (“sudo yum install dnsmasq”)
	- 啟用 dnsmasq 服務 (“systemctl enable dnsmasq.service”)
	- 啟動 dnsmasq 服務 (“systemctl start dnsmasq.service”)
	- 將 “prepend domain-name-servers 127.0.0.1;” 新增至 “/etc/dhclient-eth0.conf”
	- 重新啟動網路服務 (「服務網路重新啟動」) 來設定快取做為本機 DNS 解析程式

> [AZURE.NOTE]：'dnsmasq' 封裝只是許多適用於 Linux 之 DNS 快取的其中一個。使用它之前，請檢查特定需求的適用性，而且沒有安裝其他快取。

**用戶端重試：**

DNS 主要是 UDP 通訊協定。因為 UDP 通訊協定並不保證訊息傳遞，所以重試邏輯會在 DNS 通訊協定本身處理。每個 DNS 用戶端 (作業系統) 可以展現不同的重試邏輯，根據建立者喜好設定而定：

 - Windows 作業系統會在 1 秒後重試，然後再依序隔 2、4、4 秒後重試。 
 - 預設 Linux 安裝程式會在 5 秒之後重試。建議您變更為以 1 秒的間隔重試 5 次。  

檢查 Linux VM 上目前的設定，'cat /etc/resolv.conf' 並查看 [選項] 行，例如：

	options timeout:1 attempts:5

resolv.conf 檔案通常是自動產生的，且不可編輯。新增 [選項] 行的特定步驟會因散發版本而有所不同：

- **Ubuntu** (使用 resolvconf)：
	- 將選項行新增至 '/etc/resolveconf/resolv.conf.d/head' 
	- 執行 'resolvconf -u' 以進行更新
- **SUSE** (使用 netconf)：
	- 將 'timeout:1 attempts:5' 新增至 '/etc/sysconfig/network/config' 中的 NETCONFIG\_DNS\_RESOLVER\_OPTIONS="" 參數 
	- 執行 'netconfig update' 以進行更新
- **OpenLogic** (使用 NetworkManager)：
	- 將 'echo "options timeout:1 attempts:5"' 新增至 '/etc/NetworkManager/dispatcher.d/11-dhclient' 
	- 執行 'service network restart' 以進行更新

## 使用專屬 DNS 伺服器的名稱解析
在某些情況下，您的名稱解析需要可能會超過 Azure 所能提供的功能，例如使用 Active Directory 網域時，或當您需要虛擬網路 (vnet) 之間的 DNS 解析時。為了涵蓋這些案例，Azure 提供可讓您使用專屬 DNS 伺服器的能力。

虛擬網路內的 DNS 可以將要求轉送到 Azure 內的遞迴解析程式，以解析該虛擬網路內的主機名稱。例如，在 Azure 中執行的網域控制站 (DC) 可以回應其網域的 DNS 要求，並將所有其他要求轉送到 Azure。這樣可讓 VM 查看您的內部部署資源 (透過 DC) 以及 Azure 提供的主機名稱 (透過轉送)。存取 Azure 的遞迴解析程式是透過所提供的虛擬 IP 168.63.129.16。

DNS 轉送也實現 vnet 之間的 DNS 解析，並使內部部署電腦能夠解析 Azure 提供的主機名稱。為了解析 VM的主機名稱，DNS 伺服器 VM 必須位於同一個虛擬網路中，且設定為將主機名稱要求轉送到 Azure。因為每個 vnet 的 DNS 尾碼都不同，所以您可以使用條件性轉送規則來將 DNS 要求傳送到正確的 vnet 進行解析。下圖顯示兩個 vnet 及一個內部部署網路使用此方法進行 vnet 之間的 DNS 解析：

![虛擬網路之間的 DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

使用 Azure 提供的名稱解析時，會提供內部 DNS 尾碼給每部使用 DHCP 的 VM。使用您自己的名稱解析解決方案時，不會提供 VM 此尾碼，因為它會干擾其他 DNS 架構。若要透過 FQDN 參照電腦，或要設定 VM 上的尾碼，可使用 PowerShell 或 API 來決定尾碼：

-  對於 ARM 管理的 vnet，可透過[網路介面卡](https://msdn.microsoft.com/library/azure/mt163668.aspx)資源或 [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) Cmdlet 來取得尾碼。    
-  對於傳統部署，可透過[取得部署 API](https://msdn.microsoft.com/library/azure/ee460804.aspx) 呼叫，或透過 [Get-AzureVM -Debug](https://msdn.microsoft.com/library/azure/dn495236.aspx) Cmdlet 來取得尾碼。


如果將查詢轉送到 Azure 不符合您的需求，您會需要提供專屬的 DNS 解決方案。您的 DNS 解決方案將需要：

-  提供適當的主機名稱解析，例如透過 [DDNS](virtual-networks-name-resolution-ddns.md)。請注意，如果使用 DDNS，您可能需要停用 DNS 記錄清除功能，因為 Azure 的 DHCP 租用期很長，所以 DNS 記錄可能會提前被清除。 
-  提供適當的遞迴解析來允許外部網域名稱的解析。
-  可從其服務的用戶端存取 (連接埠 53 上的 TCP 和 UDP)，且能夠存取網際網路。
-  受保護以防止來自網際網路的存取，降低外部代理程式的威脅。

> [AZURE.NOTE] 為了達到最佳效能，使用 Azure VM 做為 DNS 伺服器時，應該停用 IPv6 且 [執行個體層級公用 IP](virtual-networks-instance-level-public-ip.mp) 應該指派給每個 DNS 伺服器 VM。如果您選擇以 Windows Server 做為 DNS 伺服器，[這篇文章](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx)提供了其他的效能分析和最佳化方法。



## 指定 DNS 伺服器

您可以指定多個 DNS 伺服器，讓您的 VM 和角色執行個體使用。每次進行 DNS 查詢，用戶端會先嘗試慣用的 DNS 伺服器，只有當慣用的伺服器沒有回應時，才會嘗試替代的伺服器，也就是 DNS 查詢不會跨不同的 DNS 伺服器進行負載平衡。基於這個原因，請確認您有針對您的環境以正確順序排列 DNS 伺服器。

> [AZURE.NOTE] 如果您變更已部署好之虛擬網路其網路組態檔上的 DNS 設定，您必須重新啟動每個 VM，變更才會生效。

### 在管理入口網站中指定 DNS 伺服器

當您在管理入口網站中建立虛擬網路時，您可以指定想要使用的 DNS 伺服器的 IP 位址和名稱。虛擬網路建立好之後，您部署到虛擬網路的虛擬機器和角色執行個體會自動設定為您指定的 DNS 設定。針對特定雲端服務 (Azure 傳統) 或網路介面卡 (ARM 型部署) 指定的 DNS 伺服器，其優先順序高於針對虛擬網路指定的項目。

### 使用組態檔指定 DNS 伺服器 (Azure 傳統)

對於傳統虛擬網路，您可以使用兩個不同的組態檔指定 DNS 設定：「網路組態檔」和「服務組態檔」。

網路組態檔描述您的訂用帳戶中的虛擬網路。當您將角色執行個體或 VM 新增至虛擬網路中的雲端服務時，網路組態檔中的 DNS 設定就會套用到每個角色執行個體或 VM，除非已指定雲端服務特定 DNS 伺服器。

您加入 Azure 的每個雲端服務，都會建立服務組態檔。當您將角色執行個體或 VM 加入雲端服務時，服務組態檔中的 DNS 設定就會套用到每個角色執行個體或 VM。

> [AZURE.NOTE] 服務組態檔中的 DNS 伺服器會覆寫網路組態檔中的設定。


## 後續步驟

[Azure 服務組態結構描述](https://msdn.microsoft.com/library/azure/ee758710)

[虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100)

[使用網路組態檔設定虛擬網路](virtual-networks-using-network-configuration-file.md)

<!---HONumber=AcomDC_0224_2016-->