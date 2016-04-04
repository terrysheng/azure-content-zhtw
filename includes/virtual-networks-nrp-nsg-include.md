## 網路安全性群組
透過實作允許和拒絕規則，NSG 資源會建立工作負載的安全性界限。這類規則可以套用至 VM、 NIC 或子網路。

|屬性|說明|範例值|
|---|---|---|
|**子網路**|NSG 套用到的子網路的 ID 清單。|/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd|
|**securityRules**|構成 NSG 的安全性規則的清單|請參閱下方的[安全性規則](#Security-rule)|
|**defaultSecurityRules**|出現在每個 NSG 中的預設安全性規則的清單|請參閱下方的[預設安全性規則](#Default-security-rules)|

- **安全性規則** - NSG 可以定義多個安全性規則。每個規則都可以允許或拒絕不同類型的流量。

### 安全性規則
安全性規則是 NSG 的子資源，包含下列屬性。

|屬性|說明|範例值|
|---|---|---|
|**description**|規則的說明|在子網路 X 中允許所有 VM 的輸入流量|
|**protocol**|規則要符合的通訊協定|TCP, UDP, 或 *| 
|**sourcePortRange**|規則要符合的來源連接埠範圍|80, 100-200, *| 
|**destinationPortRange**|規則要符合的目的地連接埠範圍|80, 100-200, *| 
|**sourceAddressPrefix**|規則要符合的來源位址首碼|10\.10.10.1, 10.10.10.0/24, VirtualNetwork|
|**destinationAddressPrefix**|規則要符合的目的地位址首碼|10\.10.10.1, 10.10.10.0/24, VirtualNetwork|
|**direction**|規則要符合的流量方向|inbound (輸入) 或 outbound (輸出)|
|**優先順序**|規則的優先順序。系統會依照規則優先順序檢查規則，一旦套用規則，就不會再測試規則是否符合。|10, 100, 65000|
|**access**|如果規則符合，要套用的存取類型|allow (允許) 或 deny (拒絕)|

JSON 格式的範例 NSG：

	{
	    "name": "NSG-BackEnd",
	    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
	    "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	    "type": "Microsoft.Network/networkSecurityGroups",
	    "location": "westus",
	    "tags": {
	        "displayName": "NSG - Front End"
	    },
	    "properties": {
	        "provisioningState": "Succeeded",
	        "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	        "securityRules": [
	            {
	                "name": "rdp-rule",
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/rdp-rule",
	                "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "description": "Allow RDP",
	                    "protocol": "Tcp",
	                    "sourcePortRange": "*",
	                    "destinationPortRange": "3389",
	                    "sourceAddressPrefix": "Internet",
	                    "destinationAddressPrefix": "*",
	                    "access": "Allow",
	                    "priority": 100,
	                    "direction": "Inbound"
	                }
	            }
	        ],
	        "defaultSecurityRules": [
	            { [...],
	        "subnets": [
	            {
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
	            }
	        ]
	    }
	}

### 預設安全性規則
在安全性規則中，預設安全性規則具有相同的屬性。它們的存在可以在有套用 NSG 的資源之間提供基本連線能力。請確定您知道哪些[預設安全性規則](../articles/virtual-network/virtual-networks-nsg.md#Default-Rules)存在。

### 其他資源

- 取得 [NSG](../articles/virtual-network/virtual-networks-nsg.md) 的詳細資訊。
- 閱讀關於 NSG 的 [REST API 參考文件](https://msdn.microsoft.com/library/azure/mt163615.aspx)。
- 閱讀關於安全性規則的 [REST API 參考文件](https://msdn.microsoft.com/library/azure/mt163580.aspx)。

<!---HONumber=AcomDC_0323_2016-->