## 負載平衡器
當您想要調整應用程式時，會使用負載平衡器。典型部署案例包含在多個 VM 執行個體上執行的應用程式。VM 執行個體受到負載平衡器保護，以協助將網路流量散發到各種執行個體。

![單一 VM 上的 NIC](./media/resource-groups-networking/figure8.png)

| 屬性 | 說明 |
|---|---|
| *前端 IP 組態* | 負載平衡器可以包括一個或多個前端 IP 位址 (亦稱為虛擬 IP (VIP))。這些 IP 位址做為流量的輸入，可以是公用 IP 或私人 IP。 |
|*後端位址集區* | 這些是與 VM NIC 相關聯的 IP 位址，而負載會散發到 VM NIC |
|*負載平衡規則* | 規則屬性會將指定的前端 IP 與連接埠組合對應到一組後端 IP 位址與連接埠組合。使用負載平衡器資源的單一定義，您可以定義多個負載平衡規則，而每個規則都會反映與虛擬機器相關聯的前端 IP 與連接埠以及後端 IP 與連接埠組合。此規則為前端集區中的一個連接埠對應到後端集區中的多部虛擬機器 |  
| *探查* | 探查可讓您追蹤 VM 執行個體的健全狀況。如果健全狀況探查失敗，則虛擬機器執行個體不會自動進入輪替 |
| *傳入的 NAT 規則* | 定義流經前端 IP 並散發到特定虛擬機器執行個體之後端 IP 之輸入流量的 NAT 規則。NAT 規則為前端集區中的一個連接埠對應到後端集區中的一部虛擬機器 | 

Json 格式的負載平衡器範本範例：

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "dnsNameforLBIP": {
	      "type": "string",
	      "metadata": {
	        "description": "Unique DNS name"
	      }
	    },
	    "location": {
	      "type": "string",
	      "allowedValues": [
	        "East US",
	        "West US",
	        "West Europe",
	        "East Asia",
	        "Southeast Asia"
	      ],
	      "metadata": {
	        "description": "Location to deploy"
	      }
	    },
	    "addressPrefix": {
	      "type": "string",
	      "defaultValue": "10.0.0.0/16",
	      "metadata": {
	        "description": "Address Prefix"
	      }
	    },
	    "subnetPrefix": {
	      "type": "string",
	      "defaultValue": "10.0.0.0/24",
	      "metadata": {
	        "description": "Subnet Prefix"
	      }
	    },
	    "publicIPAddressType": {
	      "type": "string",
	      "defaultValue": "Dynamic",
	      "allowedValues": [
	        "Dynamic",
	        "Static"
	      ],
	      "metadata": {
	        "description": "Public IP type"
	      }
	    }
	  },
	  "variables": {
	    "virtualNetworkName": "virtualNetwork1",
	    "publicIPAddressName": "publicIp1",
	    "subnetName": "subnet1",
	    "loadBalancerName": "loadBalancer1",
	    "nicName": "networkInterface1",
	    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
	    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
	    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]",
	    "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
	    "nicId": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]",
	    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
	    "backEndIPConfigID": "[concat(variables('nicId'),'/ipConfigurations/ipconfig1')]"
	  },
	  "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[parameters('location')]",
      "properties": {
        "publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsNameforLBIP')]"
        }
      }
    },
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[parameters('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "subnet": {
                "id": "[variables('subnetRef')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(variables('lbID'), '/backendAddressPools/LoadBalancerBackend')]"
                }
              ],
              "loadBalancerInboundNatRules": [
                {
                  "id": "[concat(variables('lbID'),'/inboundNatRules/RDP')]"
                }
              ]
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-05-01-preview",
      "name": "[variables('loadBalancerName')]",
      "type": "Microsoft.Network/loadBalancers",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]"
      ],
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "loadBalancerFrontEnd",
            "properties": {
              "publicIPAddress": {
                "id": "[variables('publicIPAddressID')]"
              }
            }
          }
        ],
        "backendAddressPools": [
          {
            "name": "loadBalancerBackEnd"
          }
        ],
        "inboundNatRules": [
          {
            "name": "RDP",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[variables('frontEndIPConfigID')]"
              },
              "protocol": "tcp",
              "frontendPort": 3389,
              "backendPort": 3389,
              "enableFloatingIP": false
            }
          }
        ]
      }
    }
	  ]
	}

### 其他資源

如需詳細資訊，請參閱[負載平衡器 REST API](https://msdn.microsoft.com/library/azure/mt163651.aspx)。

<!---HONumber=Nov15_HO4-->