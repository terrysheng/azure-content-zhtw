## 虛擬網路
虛擬網路 (VNET) 和子網路資源有助於定義在 Azure 中執行之工作負載的安全性範圍。VNET 可由一組位址空間集合所界定 (此集合定義為 CIDR 區塊)。

>[AZURE.NOTE]網路管理員都熟悉 CIDR 標記法。如果您不熟悉 CIDR，請[深入了解](http://whatismyipaddress.com/cidr)。

![含有多個子網路的 VNET](./media/resource-groups-networking/Figure4.png)

VNET 包含下列屬性：

|屬性|說明|範例值|
|---|---|---|
|**addressSpace**|CIDR 標記法中，構成 VNET 的位址首碼的集合|192\.168.0.0/16|
|**子網路**|構成 VNET 的子網路的集合|請參閱下方的[子網路](#Subnets)。|
|**ipAddress**|IP 位址已指派給物件。這是唯讀屬性。|104\.42.233.77|

### 子網路
子網路是 VNET 的子資源，而且有助於使用 IP 位址首碼來定義 CIDR 區塊內位址空間的區段。NIC 可以加入子網路和連接到 VM，提供各種工作負載的連線。

子網路包含下列屬性：

|屬性|說明|範例值|
|---|---|---|
|**addressPrefix**|CIDR 標記法中，構成的子網路的單一位址首碼|192\.168.1.0/24|
|**networkSecurityGroup**|套用至子網路的 NSG|請參閱 [NSG](#Network-Security-Group)|
|**routeTable**|套用至子網路的路由表|請參閱 [UDR](#Route-table)|
|**ipConfigurations**|NIC 用於連線至子網路的 IP 設定物件的集合|請參閱 [UDR](#Route-table)|


JSON 格式的範例 VNET：

	{
	    "name": "TestVNet",
	    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
	    "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	    "type": "Microsoft.Network/virtualNetworks",
	    "location": "westus",
	    "tags": {
	        "displayName": "VNet"
	    },
	    "properties": {
	        "provisioningState": "Succeeded",
	        "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	        "addressSpace": {
	            "addressPrefixes": [
	                "192.168.0.0/16"
	            ]
	        },
	        "subnets": [
	            {
	                "name": "FrontEnd",
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
	                "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "addressPrefix": "192.168.1.0/24",
	                    "networkSecurityGroup": {
	                        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
	                    },
	                    "routeTable": {
	                        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
	                    },
	                    "ipConfigurations": [
	                        {
	                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
	                        },
	                        ...]
	                }
	            },
	            ...]
	    }
	}

### 其他資源

- 取得 [VNET](virtual-networks-overview.md) 的詳細資訊。
- 閱讀關於 VNET 的 [REST API 參考文件](https://msdn.microsoft.com/library/azure/mt163650.aspx)。
- 閱讀關於子網路的 [REST API 參考文件](https://msdn.microsoft.com/library/azure/mt163618.aspx)。

<!---HONumber=Oct15_HO3-->