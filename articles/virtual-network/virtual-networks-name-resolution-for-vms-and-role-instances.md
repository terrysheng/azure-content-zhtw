<properties 
   pageTitle="VM 與角色執行個體的解析"
   description="Azure IaaS、混合式解決方案、不同雲端服務之間、Active Directory 以及使用專屬 DNS 伺服器的名稱解析案例"
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2015"
   ms.author="joaoma" />

# VM 與角色執行個體的名稱解析

根據您使用 Azure 來裝載 IaaS、PaaS 以及混合式解決方案的方式，您可能需要允許您建立的 VM 與角色執行個體彼此通訊。雖然可以使用 IP 位址來進行這項通訊，但是使用能輕鬆記住且不會變更的名稱會更加簡單。

當 Azure 中裝載的角色執行個體和 VM 必須將網域名稱解析為內部 IP 位址時，可以使用兩種方法中的其中一種：

- [Azure 提供的名稱解析](#azure-provided-name-resolution)

- [使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)

您使用的名稱解析類型取決於 VM 和角色執行個體如何彼此通訊。

**下表說明各種案例和對應的名稱解析方案：**

| **案例** | **名稱解析提供者：** | **如需詳細資訊，請參閱：** |
|--------------|----------------------------------|-------------------------------|
| 位於相同雲端服務中的角色執行個體或 VM 之間的名稱解析 | Azure 提供的名稱解析 | [Azure 提供的名稱解析](#azure-provided-name-resolution)|
| 位於相同虛擬網路中的 VM 和角色執行個體之間的名稱解析 | Azure 提供的名稱解析 (僅 ARM 型部署) 或使用專屬 DNS 伺服器的名稱解析 | [Azure 提供的名稱解析](#azure-provided-name-resolution)、[使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)和 [DNS 伺服器需求](#dns-server-requirements) |
| 位於不同虛擬網路中的 VM 和角色執行個體之間的名稱解析 | 使用專屬 DNS 伺服器的名稱解析| [使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)和 [DNS 伺服器需求](#dns-server-requirements)|
| 跨單位：在 Azure 中的角色執行個體或 VM 與內部部署電腦之間的名稱解析| 使用專屬 DNS 伺服器的名稱解析| [使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)和 [DNS 伺服器需求](#dns-server-requirements)|
| 內部 IP 的反向對應| 使用專屬 DNS 伺服器的名稱解析| [使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)和 [DNS 伺服器需求](#dns-server-requirements)|
| 非公用網域 (例如 Active Directory 網域) 的名稱解析| 使用專屬 DNS 伺服器的名稱解析| [使用專屬 DNS 伺服器的名稱解析](#name-resolution-using-your-own-dns-server)和 [DNS 伺服器需求](#dns-server-requirements)|
| 位於不同雲端服務，不在虛擬網路中的角色執行個體之間的名稱解析| 不適用。虛擬網路外部不支援不同雲端服務中 VM 和角色執行個體之間的連線。| 不適用。|


## Azure 提供的名稱解析

除了公用 DNS 名稱的解析之外，Azure 也提供位於相同虛擬網路或雲端服務內的 VM 和角色執行個體的內部名稱解析。雲端服務中的 VM/執行個體共用相同的 DNS 尾碼，所以單獨的主機名稱就已足夠。在傳統的虛擬網路中，不同的雲端服務有不同的 DNS 尾碼，因此需要 FQDN。在 ARM 型虛擬網路中，DNS 尾碼跨虛擬網路通用，因此不需要 FQDN，DNS 名稱可以指派給 NIC 或虛擬機器。雖然 Azure 提供的名稱解析不需要任何設定，但它不適用於所有部署案例，如上表所示。

> [AZURE.NOTE]如果是 Web 和背景工作角色，您也可以使用 Azure 服務管理 REST API，根據角色名稱與執行個體數目，存取角色執行個體的內部 IP 位址。如需詳細資訊，請參閱[服務管理 REST API 參考](https://msdn.microsoft.com/library/azure/ee460799.aspx)。

### 功能和注意事項

**功能：**

- 方便使用：不需要設定，就能使用 Azure 提供的名稱解析。

- Azure 提供的名稱解析服務都高度可用，可省去您建立和管理 DNS 伺服器叢集的需求。

- 提供相同雲端服務內角色執行個體或 VM 之間的名稱解析，不需要 FQDN。

- 提供 ARM 型虛擬網路中 VM 之間的名稱解析，不需要 FQDN，在不同的雲端服務中解析名稱時，傳統虛擬網路需要 FQDN。

- 您可以建立最能描述您部署的主機名稱，而不要使用自動產生的名稱。

**注意事項：**

- 無法使用虛擬網路之間以及 Azure 和內部部署機器之間的名稱解析。

- Azure 建立的 DNS 尾碼不能修改。

- 您無法手動註冊您自己的記錄。

- 不支援 WINS 和 NetBIOS。(所以您無法在 Windows 檔案總管的網路瀏覽器中列出您的虛擬機器)。

- 主機名稱必須是 DNS 相容 (只能使用 0-9、a-z 和 '-'，無法以 '-' 開始或結束。請參閱 RFC 3696 第 2 節)。

- DNS 查詢流量會根據每個 VM 進行節流。這應該不會影響大部分的應用程式。如果觀察到要求節流，請確定用戶端快取已啟用。如需詳細資料，請參閱[充分利用 Azure 提供的名稱解析](#Getting-the-most-from-Azure-provided-name-resolution)。

- 只有前 180 個雲端服務中的 VM 會為每個傳統虛擬網路註冊。這並不適用於 ARM 型虛擬網路。


### 充分利用 Azure 提供的名稱解析
**用戶端快取：**

並非所有的 DNS 查詢都需要透過網路傳送。用戶端快取可藉由解決本機快取的週期性 DNS 查詢，協助減少延遲以及改善網路標誌的恢復能力。DNS 記錄包含存留時間 (TTL)，可讓快取盡可能長時間儲存記錄而不會影響記錄的有效性，所以用戶端快取適用於大部分的情況。

預設 Windows DNS 用戶端有內建的 DNS 快取。根據預設，某些 Linux 散發版本不包含快取，建議新增快取至每個 Linux VM。有許多不同 DNS 快取封裝可用，例如 dnsmasq，以下是在最常見的散發版本上安裝 dnsmasq 的步驟：

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

[AZURE.NOTE]：'dnsmasq' 封裝只是許多適用於 Linux 之 DNS 快取的其中一個。使用它之前，請檢查特定需求的適用性，而且沒有安裝其他快取。

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

如果您的名稱解析需求超過 Azure 所提供的功能，您可以選擇使用您自己的 DNS 伺服器。當您使用自己的 DNS 伺服器時，您就必須負責管理 DNS 記錄。

> [AZURE.NOTE]除非您的部署案例需要，否則建議避免使用外部的 DNS 伺服器。

## DNS 伺服器需求

如果您打算使用不由 Azure 提供的名稱解析，您指定的 DNS 伺服器必須支援下列情況：

- DNS 伺服器必須接受透過「動態 DNS (DDNS)」通訊協定註冊的動態 DNS，否則您必須建立必要的記錄。

- 如果依賴動態 DNS，DNS 伺服器應該關閉記錄清除功能。在 Azure 中，IP 位址有很長的 DHCP 租用期，可能會導致在清除過程中移除 DNS 伺服器上的記錄。

- DNS 伺服器必須啟用遞迴功能以允許外部網域名稱的解析。

- DNS 伺服器必須 (在 TCP/UDP 連接埠 53) 可供要求名稱解析的用戶端存取，以及供將註冊其名稱的服務和虛擬機器存取。

- 也建議您保護 DNS 伺服器避免從網際網路存取，因為許多 bot 會掃描開啟遞迴的 DNS 解析程式。

- 為了達到最佳效能，使用 Azure VM 做為 DNS 伺服器時，應該停用 IPv6 且 [執行個體層級公用 IP](virtual-networks-instance-level-public-ip.mp) 應該指派給每個 DNS 伺服器 VM。

## 指定 DNS 伺服器

您可以指定多個 DNS 伺服器，讓您的 VM 和角色執行個體使用。每次進行 DNS 查詢，用戶端會先嘗試慣用的 DNS 伺服器，只有當慣用的伺服器沒有回應時，才會嘗試替代的伺服器，也就是 DNS 查詢不會跨不同的 DNS 伺服器進行負載平衡。基於這個原因，請確認您有針對您的環境以正確順序排列 DNS 伺服器。

> [AZURE.NOTE]如果您變更已部署好之虛擬網路其網路組態檔上的 DNS 設定，您必須重新啟動每個 VM，變更才會生效。

### 在管理入口網站中指定 DNS 伺服器

當您在管理入口網站中建立虛擬網路時，您可以指定想要使用的 DNS 伺服器的 IP 位址和名稱。虛擬網路建立好之後，您部署到虛擬網路的虛擬機器和角色執行個體會自動設定為您指定的 DNS 設定。針對特定雲端服務 (Azure 傳統) 或網路介面卡 (ARM 型部署) 指定的 DNS 伺服器，其優先順序高於針對虛擬網路指定的項目。請參閱[關於設定管理入口網站中的虛擬網路](virtual-networks-settings.md)。

### 使用組態檔指定 DNS 伺服器 (Azure 傳統)

對於傳統虛擬網路，您可以使用兩個不同的組態檔指定 DNS 設定：「網路組態檔」和「服務組態檔」。

網路組態檔說明您的訂用帳戶中的虛擬網路。當您將角色執行個體或 VM 新增至虛擬網路中的雲端服務時，網路組態檔中的 DNS 設定就會套用到每個角色執行個體或 VM，除非已指定雲端服務特定 DNS 伺服器。

您加入 Azure 的每個雲端服務，都會建立服務組態檔。當您將角色執行個體或 VM 加入雲端服務時，服務組態檔中的 DNS 設定就會套用到每個角色執行個體或 VM。

> [AZURE.NOTE]服務組態檔中的 DNS 伺服器會覆寫網路組態檔中的設定。


## 後續步驟

[Azure 服務組態結構描述](https://msdn.microsoft.com/library/azure/ee758710)

[虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100)

[關於設定管理入口網站中的虛擬網路設定](virtual-networks-settings.md)

[使用網路組態檔設定虛擬網路](virtual-networks-using-network-configuration-file.md)

<!---HONumber=Sept15_HO2-->