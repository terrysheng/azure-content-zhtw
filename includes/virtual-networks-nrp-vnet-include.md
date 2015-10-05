## 虛擬網路
虛擬網路 (VNET) 和子網路有助於定義在 Azure 中執行之工作負載的安全性範圍。VNET 可由一組位址空間集合 (也稱為 CIDR 區塊) 所界定。

子網路是 VNET 的子資源，而且有助於使用 IP 位址首碼來定義 CIDR 區塊內位址空間的區段。NIC 可以加入子網路和連接到 VM，提供各種工作負載的連線。

![單一 VM 上的 NIC](./media/resource-groups-networking/Figure4.png)

VNET 資源的重要屬性包括：

- IP 位址空間 (CIDR 區塊) 
- VNET 名稱
- 子網路
- DNS 伺服器

VNET 也可以與下列網路資源相關聯：

- VPN 閘道

### 子網路

子網路的重要屬性包括：

- IP 位址首碼
- 子網路名稱

子網路也可以與下列網路資源相關聯：

- NSG

<!---HONumber=Sept15_HO4-->