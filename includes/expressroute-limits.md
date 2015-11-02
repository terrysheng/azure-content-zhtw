#### ExpressRoute 限制

下列限制適用於每個訂用帳戶的 ExpressRoute 資源。

| 資源 | 預設限制 |
|---|---|
| 每個訂用帳戶的 ExpressRoute 電路 | 10 |
| ARM 的每個訂用帳戶每個區域的 ExpressRoute 電路 | 10 |
| 具有 ExpressRoute Standard 的 Azure 私人對等互連的最大路由數目 | 4,000 |
| 具有 ExpressRoute Premium 附加元件的 Azure 私人對等互連的最大路由數目 | 10,000 |
| 具有 ExpressRoute Standard 的 Azure 公用對等互連的最大路由數目 | 200 |
| 具有 ExpressRoute Premium 附加元件的 Azure 公用對等互連的最大路由數目 | 200 |
| 具有 ExpressRoute Standard 的 Azure Microsoft 對等互連的最大路由數目 | 200 |
| 具有 ExpressRoute Premium 附加元件的 Azure Microsoft 對等互連的最大路由數目 | 200 |
| 每個 ExpressRoute 電路允許的虛擬網路連結數目 | 請參閱下表 |

#### 每個 ExpressRoute 電路的虛擬網路數目

| **電路大小** | **Standard 的 VNet 連結數目** | **具有 Premium 附加元件的 VNet 連結數目** |
|---|---|---|
| 10 Mbps | 10 | 不支援 |
| 50 Mbps | 10 | 20 |
| 100 Mbps | 10 | 25 |
| 200 Mbps | 10 | 25 |
| 500 Mbps | 10 | 40 |
| 1 Gbps | 10 | 50 |
| 2 Gbps | 10 | 60 |
| 5 Gbps | 10 | 75 |
| 10 Gbps | 10 | 100 |

<!---HONumber=Oct15_HO4-->