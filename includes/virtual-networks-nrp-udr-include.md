## 路由表
路由表資源包含路由，用來定義流量如何在 Azure 基礎結構內流動。您可以使用使用者定義的路由 (UDR) 將所有流量從給定的子網路傳送到虛擬應用裝置，例如防火牆或入侵偵測系統 (IDS)。您可以將路由表與子網路建立關聯。

路由表包含下列屬性。

|屬性|說明|範例值|
|---|---|---|
|**routes**|路由表中使用者定義的路由的集合|請參閱[使用者定義的路由](#User-defined-routes)|
|**子網路**|路由表套用的子網路的集合|請參閱[子網路](#Subnets)|


### 使用者定義的路由
您可以根據流量的目的地位址建立 UDR 以指定流量應傳送到何處。您可以將路由想成是根據網路封包的目的地位址定義的預設閘道。

UDR 包含下列屬性。

|屬性|說明|範例值|
|---|---|---|
|**addressPrefix**|目的地的位址首碼或完整 IP 位址|192\.168.1.0/24, 192.168.1.101|
|**nextHopType**|流量將傳送的目標裝置類型。|VirtualAppliance, VPN Gateway, Internet|
|**nextHopIpAddress**|下個躍點的 IP 位址|192\.168.1.4|


JSON 格式的範例路由表：

	{
	    "name": "UDR-BackEnd",
	    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd",
	    "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	    "type": "Microsoft.Network/routeTables",
	    "location": "westus",
	    "properties": {
	        "provisioningState": "Succeeded",
	        "routes": [
	            {
	                "name": "RouteToFrontEnd",
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd/routes/RouteToFrontEnd",
	                "etag": "W/"v"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "addressPrefix": "192.168.1.0/24",
	                    "nextHopType": "VirtualAppliance",
	                    "nextHopIpAddress": "192.168.0.4"
	                }
	            }
	        ],
	        "subnets": [
	            {
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd"
	            }
	        ]
	    }
	}

### 其他資源

- 取得 [UDR](virtual-networks-udr-overview.md) 的詳細資訊。
- 閱讀關於路由表的 [REST API 參考文件](https://msdn.microsoft.com/library/azure/mt502549.aspx)。
- 閱讀關於使用者定義路由的 [REST API 參考文件](https://msdn.microsoft.com/library/azure/mt502539.aspx)。

<!---HONumber=Oct15_HO3-->