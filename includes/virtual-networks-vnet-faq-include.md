## 虛擬網路基本概念

### 什麼是 Azure 虛擬網路 (VNet)？

您可以使用 VNet 在 Azure 中佈建和管理虛擬私人網路 (VPN)，也可以選擇性地連結 VNet 與其他 Azure 中的 VNet，或連結您的內部部署 IT 基礎結構，以便建立混合式或跨單位的解決方案。您建立的每個 VNet 皆具有自己的 CIDR 區塊，且只要 CIDR 區塊沒有衝突，就可以連結至其他 VNet 和內部部署網路。針對 VNet 和分割的 VNet 子網路，您也擁有 DNS 伺服器設定的控制項。

您可以使用 VNet：

- 建立專用的純私人雲端虛擬網路
									
	針對您的解決方案，有時候您不需要跨單位組態。當您建立 VNet 時，您的服務和 VNet 中的 VM 可以直接且安全地在雲端中彼此通訊。這可確保 VNet 中流量的安全性，但仍可讓您設定 VM 的端點連接，以及需要網際網路通訊作為解決方案一部分的服務。

- 安全地擴充資料中心
									
	您可以使用 VNet 建置傳統的站台對站台 (S2S) VPN，安全地擴充資料中心容量。S2S VPN 使用 IPSEC 在您的公司 VPN 閘道與 Azure 之間提供安全連接。

- 啟用混合式雲端案例
									
	VNet 可讓您彈性地支援許多混合式雲端案例。您可以將雲端型應用程式安全地連接到任何類型的內部部署系統，例如大型主機和 Unix 系統。

### 如何得知是否需要虛擬網路？

請瀏覽[虛擬網路概觀](../articles/virtual-network/virtual-networks-overview.md)，查看可協助您決定最佳網路設計選項的決策資料表。

### 如何開始使用？

請造訪[虛擬網路文件](https://azure.microsoft.com/documentation/services/virtual-network/)以開始使用。此頁面的連結包含常見組態步驟，以及可幫助您了解設計虛擬網路時應考慮事項的相關資訊。

### VNet 可以與哪些服務搭配使用？

VNet 可以搭配各種不同的 Azure 服務使用，例如雲端服務 (PaaS)、虛擬機器和 Web Apps。不過，有幾個服務不支援 VNet。請檢查您想要使用的特定服務，並確認相容。

### 我可以使用不包含跨單位連線的 VNet 嗎？

是。您可以使用不包含站對站連線能力的 VNet。如果您想要在 Azure 中執行網域控制站和 SharePoint 伺服器陣列，這會特別有用。

## 虛擬網路組態

### 我可以使用哪些工具來建立 VNet？

您可以使用下列工具來建立或設定虛擬網路：

- Azure 入口網站 (適用於傳統與資源管理員 VNet)。

- 網路組態檔 (netcfg - 僅適用於傳統 VNet)。請參閱[使用網路組態檔設定虛擬網路](../articles/virtual-network/virtual-networks-using-network-configuration-file.md)。

- PowerShell (適用於傳統與資源管理員 VNet)。

- Azure CLI (適用於傳統與資源管理員 VNet)。

### 我可以在 VNet 中使用哪些位址範圍？

您可以使用公用 IP 位址範圍，以及任何 [RFC 1918](http://tools.ietf.org/html/rfc1918) 中定義的 IP 位址範圍。

### 我可以在 VNet 擁有公用 IP 位址嗎？

是。如需有關公用 IP 位址範圍的詳細資訊，請參閱[虛擬網路 (VNet) 中的公用 IP 位址空間](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md)。請記住，您的公用 IP 將無法直接從網際網路存取。

### 我的虛擬網路中的子網路數目是否有限制？

您可以在 VNet 中使用的子網路數目沒有限制。所有子網路皆必須完全包含在虛擬網路位址空間中，且不應彼此重疊。

### 在這些子網路內使用 IP 位址是否有任何限制？

Azure 會在每個子網路中保留一些 IP 位址。子網路的第一個和最後一個 IP 位址會保留給相容的通訊協定，以及用於 Azure 服務的額外 3 個位址。

### VNet 和子網路的大小限制為何？

我們支援的最小子網路是 /29，最大則是 /8 (使用 CIDR 子網路定義)。

### 我可以使用 VNet 將 VLAN 帶到 Azure 嗎？

否。VNet 是 Layer-3 重疊。Azure 不支援任何 Layer-2 語意。

### 可以在我的 Vnet 和子網路上指定自訂路由原則嗎？

是。您可以使用「使用者定義的路由」(UDR)。如需有關 UDR 的詳細資訊，請造訪[使用者定義的路由和 IP 轉送](../articles/virtual-network/virtual-networks-udr-overview.md)。

### VNet 是否支援多點傳送或廣播？

否。我們不支援多點傳送或廣播。

### 我可以在 VNet 中使用哪些通訊協定？

您可以在 VNet 內使用標準的 IP 型通訊協定。不過，多點傳送、廣播、IP-in-IP 封裝式封包和 Generic Routing Encapsulation (GRE) 封包在 VNet 內會遭到封鎖。適用的標準通訊協定包含：

- TCP
- UDP
- ICMP

### 我可以在 VNet 中偵測我的預設路由器嗎？

編號

### 我可以使用 tracert 來診斷連線嗎？

編號

### 我可以在建立 VNet 之後新增子網路嗎？

是。子網路隨時可以加入至 VNet，前提是子網路位址不是 VNet 中另一個子網路的一部分。

### 我可以在建立 VNet 之後修改 VNet 大小嗎？

如果其中沒有部署任何 VM 或服務，您可以使用 PowerShell Cmdlet 或 NETCFG 檔案來新增、移除、展開或壓縮子網路。您也可以新增、移除、展開或壓縮任何前置詞，前提是包含 VM 或服務的子網路不會受到此變更的影響。

### 我可以在建立子網路之後進行修改嗎？

是。您可以加入、移除和修改 VNet 所使用的 CIDR 區塊。

### 如果我在 VNet 中執行服務，我可以連線到網際網路嗎？

是。部署在 VNet 中的所有服務皆可連線到網際網路。Azure 中部署的每個雲端服務皆已指派公開可定址的 VIP。您必須定義 PaaS 角色的輸入端點和虛擬機器的端點，啟用這些服務以接受來自網際網路的連接。

### VNet 是否支援 IPv6？

否。目前您無法使用 IPv6 搭配 VNet。

### VNet 可以跨區域嗎？

否。VNet 僅限於單一區域。

### 我可以將 VNet 連線到 Azure 中的另一個 VNet 嗎？

是。您可以使用 REST API 或 Windows PowerShell 來建立 VNet 對 VNet 通訊。

## 名稱解析 (DNS)

### 適用於 VNet 的 DNS 選項為何？

使用 [[VM 和角色執行個體的名稱解析](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)] 頁面上的決策資料表來引導您完成所有可用的 DNS 選項。

### 我可以指定適用於 VNet 的 DNS 伺服器嗎？

是。您可以在 VNet 設定中指定 DNS 伺服器 IP 位址。這將會針對 VNet 中的所有 VM 套用為預設 DNS 伺服器。

### 我可以指定多少部 DNS 伺服器？

您可以指定最多 12 部 DNS 伺服器。

### 我可以在建立虛擬網路之後修改 DNS 伺服器嗎？

是。您可以隨時針對 VNet 變更 DNS 伺服器清單。如果您變更 DNS 伺服器清單，您必須重新啟動 VNet 中的每個 VM，使其挑選新的 DNS 伺服器。


### 什麼是 Azure 提供的 DNS，以及它是否可搭配 VNet 使用？

Azure 提供的 DNS 是由 Microsoft 所提供的多租用戶 DNS 服務。Azure 會註冊您在此服務中的所有 VM 和角色執行個體。這個服務可根據相同雲端服務內所包含 VM 和角色的主機名稱，以及根據 VM 的 FQDN 和相同 VNet 中的角色執行個體提供名稱解析功能。

> [AZURE.NOTE] 目前虛擬網路中的前 100 個雲端服務具有使用 Azure 所提供 DNS 進行跨租用戶名稱解析的限制。如果您使用自己的 DNS 伺服器，則不適用這項限制。

### 我是否可以根據每個 VM/服務來覆寫 DNS 設定？

是。您可以根據雲端服務設定 DNS 伺服器，以便覆寫預設網路設定。不過，我們建議您盡可能使用全網路 DNS。

### 我可以加上自己的 DNS 尾碼嗎？

否。您無法針對 VNet 指定自訂的 DNS 尾碼。

## VNet 和 VM

### 我可以將 VM 部署至 VNet 嗎？

是。

### 我可以將 Linux VM 部署至 VNet 嗎？

是。您可以部署 Azure 所支援的任何 Linux 散發版本。

### 公開 VIP 和內部 IP 位址之間的差異為何？

- 內部 IP 位址是 VNet 中由 DHCP 指派至每個 VM 的 IP 位址。它不會對外公開。如果您已建立 VNet，則會根據您在 VNet 子網路設定中所指定的範圍來指派內部 IP 位址。如果您沒有 VNet，系統仍會指派內部 IP 位址。內部 IP 位址的存留期將取決於 VM，除非 VM 解除配置。

- 公開 VIP 是指派至雲端服務或負載平衡器的公用 IP 位址。它不會直接指派至 VM NIC。VIP 會與指派的雲端服務一併保留，直到雲端服務中的所有 VM 解除配置或刪除。當所有 VM 解除配置或刪除時，系統便會釋放它。

### 我的 VM 將接收到的 IP 位址為何？

- **內部 IP 位址 -** 如果您將 VM 部署到 VNet，VM 會從您指定的內部 IP 位址的集區接收內部 IP 位址。VM 會使用內部 IP 位址在 VNet 中進行通訊。儘管 Azure 會指派一個動態內部 IP 位址，您可以為您的 VM 要求一個靜態位址。若要深入了解靜態內部 IP 位址，請造訪[如何設定靜態內部 IP](../articles/virtual-network/virtual-networks-reserved-private-ip.md)。

- **VIP -** 您的 VM 也會與 VIP 相關聯，不過 VIP 永遠不會直接指派至 VM。VIP 是可以指派至雲端服務的公用 IP 位址。您可以選擇性地為雲端服務保留 VIP。

- **ILPIP -** 您也可以設定執行個體層級公用 IP 位址 (ILPIP)。ILPIP 直接與 VM，而非與雲端服務相關聯。若要深入了解 ILPIP，請造訪[執行個體層級公用 IP 概觀](../articles/virtual-network/virtual-networks-instance-level-public-ip.md)。

### 我可以為稍後建立的 VM 保留內部 IP 位址嗎？

否。您不能保留內部 IP 位址。如果有可用的內部 IP 位址，則會由 DHCP 伺服器指派至 VM 或角色執行個體。該 VM 可能不是您想要指派內部 IP 位址的目的地。不過，您可以將已建立 VM 的內部 IP 位址變更為任何可用的內部 IP 位址。

### VNet 中的內部 IP 位址會根據 VM 進行變更嗎？

是。內部 IP 位址的存留期取決於 VM，除非 VM 解除配置。當 VM 解除配置時，系統會釋放內部 IP 位址，除非您已為 VM 定義靜態內部 IP 位址。如果只是停止 VM (而非放置於**已停止 (已解除配置) 狀態**)，則 IP 位址仍會指派至 VM。

### 我可以在 VM 中將 IP 位址手動指派至 NIC 嗎？

否。您不得變更 VM 的任何介面屬性。任何變更皆可能導致失去與 VM 的連線。

### 如果我將 VM 關機，則我的 IP 位址會受到什麼影響？

不會受到影響。IP 位址 (包含公開 VIP 和內部 IP 位址) 的變更取決於您的雲端服務或 VM。

> [AZURE.NOTE] 如果您想要只將 VM 關機，請勿使用管理入口網站執行此操作。目前，[關機] 按鈕會將虛擬機器解除配置。

### 我可以將 VM 從子網路移動至 VNet 中的其他子網路而不需重新部署嗎？

是。您可以在[這裡](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md)找到詳細資訊。

### 我可以針對 VM 設定靜態 MAC 位址嗎？

否。MAC 位址無法以靜態方式設定。

### 當我建立 VM 之後 MAC 位址會保持相同的狀態嗎？

否，只有在 VM 的狀態為已停止 (已重新分配) 時才會變更。如果您變更 VM 大小、重新開機或有服務修復或規劃了主機伺服器維護等情況，就會保留 MAC 位址。

### 我可以從 VNet 中的 VM 連線到網際網路嗎？

是。部署在 VNet 中的所有服務皆可連線到網際網路。此外，部署在 Azure 中的每個雲端服務已指派公開可定址的 VIP。您必須定義 PaaS 角色的輸入端點和 VM 的端點，啟用這些服務以接受來自網際網路的連接。

## VNet 和服務

### VNet 可以與哪些服務搭配使用？

您只能使用 VNet 內的計算服務。計算服務受限於雲端服務 (Web 和背景工作角色) 和 VM。

### 我可以使用 Web Apps 搭配虛擬網路嗎？

是。您可以使用 ASE (App Service 環境) 在 VNet 中部署 Web Apps。除此之外，如果您已針對 VNet 設定點對站台，則 Web Apps 可以安全地連線並存取 Azure VNet 中的資源。如需詳細資訊，請參閱下列主題：


- [在 App Service 環境中建立 Web Apps](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)

- [Web Apps 虛擬網路整合](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)

- [使用 VNet 整合和混合式連接搭配 Web Apps](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)

- [將 Web 應用程式與 Azure 虛擬網路整合](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### 我可以在 VNet 中使用 Web 和背景工作角色 (PaaS) 部署雲端服務嗎？

是。您可以在 VNet 中部署 PaaS 服務。

### 如何將 PaaS 角色部署至 VNet？

您可以在服務組態的網路組態區段中，透過指定 VNet 名稱和角色/子網路對應來完成。您不需要更新任何二進位檔。

### 我可以將服務移入和移出 VNet 嗎？

否。您無法將服務移入和移出 VNet。您必須刪除並重新部署服務，才能將它移動到另一個 VNet。

## VNet 與安全性

### 什麼是 VNet 的安全性模型？

VNet 會與另一個 VNet，以及其他裝載於 Azure 基礎結構中的服務完全隔離。VNet 是一種信任邊界。

### 我可以在 VNet 上定義 ACL 或 NSG 嗎？

否。您無法將 ACL 或 NSG 與 VNet 建立關聯。不過，ACL 可以定義於已部署至 VNet 的 VM 輸入端點，而 NSG 可以關聯至子網路或 NIC。

### 是否有 VNet 安全性白皮書？

是。您可以從[這裡](http://go.microsoft.com/fwlink/?LinkId=386611)下載。

## API、結構描述和工具

### 我可以從程式碼管理 VNet 嗎？

是。您可以使用 REST API 來管理 VNet 和跨單位連線。如需詳細資訊，請參閱[這裡](http://go.microsoft.com/fwlink/?LinkId=296833)。

### 是否有工具支援 VNet？

是。您可以針對各種平台使用 PowerShell 和命令列工具。如需詳細資訊，請參閱[這裡](http://go.microsoft.com/fwlink/?LinkId=317721)。

<!---HONumber=AcomDC_0323_2016-->