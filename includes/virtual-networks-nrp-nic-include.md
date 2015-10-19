## NIC
 
網路介面卡 (NIC) 資源提供 VNet 資源中現有子網路的網路連線能力。雖然您可以將 NIC 建立為獨立物件，但您必須將其關聯到另一個物件，才能實際提供連線。NIC 可用來將 VM 連接到子網路、公用 IP 位址或負載平衡器。

|屬性|說明|範例值|
|---|---|---|
|**virtualMachine**|與 NIC 相關聯的 VM。|/subscriptions/{guid}/../Microsoft.Compute/virtualMachines/vm1|
|**macAddress**|適用於 NIC 的 MAC 位址|介於 4 到 30 之間的任意值|
|**networkSecurityGroup**|關聯到 NIC 的 NSG|/subscriptions/{guid}/../Microsoft.Network/networkSecurityGroups/myNSG1|
|**dnsSettings**|適用於 NIC 的 DNS 設定。|請參閱 [PIP](#Public-IP-address)。|

網路介面卡或 NIC 代表可以關聯到虛擬機器 (VM) 的網路介面。一個 VM 可以有一或多個 NIC。

![單一 VM 上的 NIC](./media/resource-groups-networking/Figure3.png)

### IP 組態
NIC 具有名為 **ipConfigurations** 的子物件，其包含下列屬性：

|屬性|說明|範例值|
|---|---|---|
|**subnet**|NIC 連接到的子網路。|/subscriptions/{guid}/../Microsoft.Network/virtualNetworks/myvnet1/subnets/mysub1|
|**privateIPAddress**|子網路中適用於 NIC 的 IP 位址|10\.0.0.8|
|**privateIPAllocationMethod**|IP 配置方法|動態或靜態|
|**enableIPForwarding**|是否可以使用 NIC 來進行路由|True 或 False|
|**primary**|NIC 是否為 VM 的主要 NIC|True 或 False|
|**publicIPAddress**|與 NIC 相關聯的 PIP|請參閱 [DNS 設定](#DNS-settings)|
|**loadBalancerBackendAddressPools**|與 NIC 相關聯的後端位址集區||
|**loadBalancerInboundNatRules**|與 NIC 相關聯的輸入負載平衡器 NAT 規則||

JSON 格式的範例公用 IP 位址：

	{
	    "name": "lb-nic1-be",
	    "id": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be",
	    "etag": "W/"0027f1a2-3ac8-49de-b5d5-fd46550500b1"",
	    "type": "Microsoft.Network/networkInterfaces",
	    "location": "eastus",
	    "properties": {
	        "provisioningState": "Succeeded",
	        "resourceGuid": "e80fdad0-f0da-44ab-816a-828c9ac3c20e",
	        "ipConfigurations": [
	            {
	                "name": "NIC-config",
	                "id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/NIC-config",
	                "etag": "W/"0027f1a2-3ac8-49de-b5d5-fd46550500b1"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "privateIPAddress": "10.0.0.4",
	                    "privateIPAllocationMethod": "Dynamic",
	                    "subnet": {
	                        "id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet"
	                    },
	                    "loadBalancerBackendAddressPools": [
	                        {
	                            "id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool"
	                        }
	                    ],
	                    "loadBalancerInboundNatRules": [
	                        {
	                            "id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1"
	                        }
	                    ]
	                }
	            }
	        ],
	        "dnsSettings": {
	            "dnsServers": [],
	            "appliedDnsServers": []
	        },
	        "macAddress": "00-0D-3A-10-F1-29",
	        "enableIPForwarding": false,
	        "primary": true,
	        "virtualMachine": {
	            "id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/nrprg/providers/Microsoft.Compute/virtualMachines/web1"
	        }
	    }
	}

<!---HONumber=Oct15_HO2-->