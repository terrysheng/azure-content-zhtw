## 應用程式閘道

應用程式閘道會根據第 7 層負載平衡，提供 Azure 管理的 HTTP 負載平衡解決方案。應用程式負載平衡可讓以 HTTP 為基礎的網路流量使用路由規則。

| 屬性 | 說明 | 
|---|---|
|*後端伺服器集區* | 後端伺服器集區的伺服器 IP 位址清單。列出的 IP 位址應屬於虛擬網路子網路或公用 IP/VIP 或是私人 IP。 |
| *後端伺服器集區設定* | 每個集區都有一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質。這些設定會繫結至集區，並套用至集區內所有伺服器。 |
| *前端連接埠* | 此連接埠是在應用程式閘道上開啟的公用連接埠。流量會達到此連接埠，然後重新導向至其中一個後端伺服器。 |
| *接聽程式* | 接聽程式具有前端連接埠、通訊協定 (Http 或 Https，都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。 |
| *規則* | 規則會繫結接聽程式和後端伺服器集區，並定義流量達到特定接聽程式時應該導向至哪個後端伺服器集區。目前只支援基本規則。基本規則是循環配置資源的負載分配。 |

範例應用程式閘道 Json 範本：

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "location": {
	      "type": "string",
	      "metadata": {
	        "description": "Location to deploy to"
	      }
	    },
	    "addressPrefix": {
	      "type": "string",
	      "defaultValue": "10.0.0.0/16",
	      "metadata": {
	        "description": "Address prefix for the Virtual Network"
	      }
	    },
	    "subnetPrefix": {
	      "type": "string",
	      "defaultValue": "10.0.0.0/28",
	      "metadata": {
	        "description": "Subnet prefix"
	      }
	    },
	    "skuName": {
	      "type": "string",
	      "allowedValues": [
	        "Standard_Small",
	        "Standard_Medium",
	        "Standard_Large"
	      ],
	      "defaultValue": "Standard_Medium",
	      "metadata": {
	        "description": "Sku Name"
	      }
	    },
	    "capacity": {
	      "type": "int",
	      "defaultValue": 2,
	      "metadata": {
	        "description": "Number of instances"
	      }
	    },
	    "backendIpAddress1": {
	      "type": "string",
	      "metadata": {
	        "description": "IP Address for Backend Server 1"
	      }
	    },
	    "backendIpAddress2": {
	      "type": "string",
	      "metadata": {
	        "description": "IP Address for Backend Server 2"
	      }
	    }
	  },
	  "variables": {
	    "applicationGatewayName": "applicationGateway1",
	    "publicIPAddressName": "publicIp1",
	    "virtualNetworkName": "virtualNetwork1",
	    "subnetName": "appGatewaySubnet",
	    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    	"subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
    	"publicIPRef": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]",
    	"applicationGatewayID": "[resourceId('Microsoft.Network/applicationGateways',variables('applicationGatewayName'))]",
		"apiVersion": "2015-05-01-preview"
  	},
 	 "resources": [
    	{
    	  "apiVersion": "[variables('apiVersion')]",
    	  "type": "Microsoft.Network/publicIPAddresses",
    	  "name": "[variables('publicIPAddressName')]",
    	  "location": "[parameters('location')]",
    	  "properties": {
    	    "publicIPAllocationMethod": "Dynamic"
    	  }
    	},
    	{
    	  "apiVersion": "[variables('apiVersion')]",
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
    	  "apiVersion": "[variables('apiVersion')]",
    	  "name": "[variables('applicationGatewayName')]",
    	  "type": "Microsoft.Network/applicationGateways",
    	  "location": "[parameters('location')]",
    	  "dependsOn": [
    	    "[concat('Microsoft.Network/virtualNetworks/', variables	('virtualNetworkName'))]",
    	    "[concat('Microsoft.Network/publicIPAddresses/', variables	('publicIPAddressName'))]"
    	  ],
    	  "properties": {
    	    "sku": {
    	      "name": "[parameters('skuName')]",
    	      "tier": "Standard",
    	      "capacity": "[parameters('capacity')]"
    	    },
    	    "gatewayIPConfigurations": [
    	      {
    	        "name": "appGatewayIpConfig",
    	        "properties": {
    	          "subnet": {
    	            "id": "[variables('subnetRef')]"
    	          }
    	        }
    	      }
    	    ],
    	    "frontendIPConfigurations": [
    	      {
    	        "name": "appGatewayFrontendIP",
    	        "properties": {
    	          "PublicIPAddress": {
    	            "id": "[variables('publicIPRef')]"
    	          }
    	        }
    	      }
    	    ],
    	    "frontendPorts": [
    	      {
    	        "name": "appGatewayFrontendPort",
    	        "properties": {
    	          "Port": 80
    	        }
    	      }
    	    ],
    	    "backendAddressPools": [
    	      {
    	        "name": "appGatewayBackendPool",
    	        "properties": {
    	          "BackendAddresses": [
    	            {
    	              "IpAddress": "[parameters('backendIpAddress1')]"
    	            },
    	            {
    	              "IpAddress": "[parameters('backendIpAddress2')]"
    	            }
    	          ]
    	        }
    	      }
    	    ],
    	    "backendHttpSettingsCollection": [
    	      {
    	        "name": "appGatewayBackendHttpSettings",
    	        "properties": {
    	          "Port": 80,
    	          "Protocol": "Http",
    	          "CookieBasedAffinity": "Disabled"
    	        }
    	      }
    	    ],
    	    "httpListeners": [
    	      {
    	        "name": "appGatewayHttpListener",
    	        "properties": {
    	          "FrontendIPConfiguration": {
    	            "Id": "[concat(variables('applicationGatewayID'), '/	frontendIPConfigurations/appGatewayFrontendIP')]"
    	          },
    	          "FrontendPort": {
    	            "Id": "[concat(variables('applicationGatewayID'), '/	frontendPorts/appGatewayFrontendPort')]"
    	          },
    	          "Protocol": "Http",
    	          "SslCertificate": null
    	        }
    	      }
    	    ],
    	    "requestRoutingRules": [
    	      {
    	        "Name": "rule1",
    	        "properties": {
    	          "RuleType": "Basic",
    	          "httpListener": {
    	            "id": "[concat(variables('applicationGatewayID'), '/	httpListeners/appGatewayHttpListener')]"
    	          },
    	          "backendAddressPool": {
    	            "id": "[concat(variables('applicationGatewayID'), '/	backendAddressPools/appGatewayBackendPool')]"
    	          },
    	          "backendHttpSettings": {
    	            "id": "[concat(variables('applicationGatewayID'), '/	backendHttpSettingsCollection/	appGatewayBackendHttpSettings')]"
    	          }
    	        }
    	      }
    	    ]
    	  }
    	}
  	]	
	}

### 其他資源

如需詳細資訊，請閱讀[應用程式閘道 REST API](https://msdn.microsoft.com/library/azure/mt299388.aspx)。

<!---HONumber=Nov15_HO4-->