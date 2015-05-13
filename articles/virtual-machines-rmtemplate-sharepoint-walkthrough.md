<properties 
	pageTitle="高可用性 SharePoint 伺服器陣列資源管理員範本" 
	description="逐步講解高可用性 SharePoint 伺服器陣列的 Azure Resource Manager 範本。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# 高可用性 SharePoint 伺服器陣列資源管理員範本

本主題逐步講解高可用性 SharePoint 伺服器陣列的 azuredeploy.json 範本檔案結構。

## "parameters" 區段

"parameters" 會指定一些參數，系統會利用這些參數，將資料輸入到這個範本中。您最多可以定義 50 個參數。以下是 Azure 位置的參數範例：

	"deploymentLocation": {
		"type": "string",
		"allowedValues": [
			"West US",
			"East US",
			"West Europe",
			"East Asia",
			"Southeast Asia"
		],
		"metadata": {
			"Description": "The region to deploy the resources into"
		},
		"defaultValue":"West Europe"
	},

## "variables" 區段

"variables" 區段會指定一些變數，這些參數會全程用在這個範本中。您最多可以定義 100 個變數。這裡有一些範例：

	"RDPNAT":"RDP", 
	"spWebLB":"spWeb", 
	"SQLAOListener":"SQLAlwaysOnEndPointListener", 
	"SQLAOProbe":"SQLAlwaysOnEndPointProbe", 
	"staticSubnetName": "staticSubnet", 
	"sqlSubnetName": "sqlSubnet", 
	"spwebSubnetName": "spwebSubnet", 
	"spappSubnetName": "spappSubnet", 

## "resources" 區段

**"resources"** 區段會指定一些當您為資源群組中的 SharePoint 伺服器陣列部署資源時就會用到的資訊。您最多定義 250 個資源，但是資源相依性僅能定義 5 個層級深度。

本節包含下列小節：

### Microsoft.Storage/storageAccounts  

本章節會建立伺服器陣列中的所有 VHD 和磁碟資源的新儲存體帳戶。以下是儲存體帳戶的 JSON 程式碼：

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2014-12-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

這些區段會建立一組公用 IP 位址，有了這些網址之後，就可以透過網際網路連接每一個虛擬機器。下列是一個範例：

	{
		"apiVersion": "2014-12-01-preview",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('adpublicIPAddressName')]",
		"location": "[parameters('deploymentLocation')]",
		"properties": {
			"publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
			"dnsSettings": {
				"domainNameLabel": "[parameters('adDNSPrefix')]"
			}
		}
	},

### Microsoft.Compute/availabilitySets

這些區段會建立 4 個可用性設定組，每一個部署層都有一個：

- Active Directory 網域控制站
- SQL Server 叢集
- 應用程式層伺服器
- Web 層伺服器

下列是一個範例：

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2014-12-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

這個區段會建立一個純雲端虛擬網路而且會有 4 個子網路 \(每一個部署層各一個\)，虛擬機器就是放在這個虛擬網路。以下是 JSON 程式碼：

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkPrefix')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},



### Microsoft.Network/loadBalancers

這些區段會為每一個虛擬機器建立負載平衡器執行個體，以便為連入的網路網路流量，提供 NAT 和流量篩選功能。針對每一個負載平衡器，設定前端、後端以及傳入 NAT 規則。例如，每一個虛擬機器會有遠端桌面流量規則，另外還有一個規則，讓 Web 層伺服器可以利用網際網路的連入 Web 流量 \(TCP 連接埠 80\)。以下是 Web 層伺服器範例：

        {
            "apiVersion": "2014-12-01-preview",
            "name": "[variables('splbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "[variables('spLBFE')]",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
                            },
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "[variables('spWebLBBE')]"
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "[variables('spWebLB')]",
                        "properties": {
                        "frontendIPConfiguration": {
                            "id": "[variables('splbFEConfigID')]"
                        },
                        "probe": {
                            "id": "[variables('spwebProbeID')]"
                        },
                        "protocol": "tcp",
                        "frontendPort": 80,
                        "backendPort": 80,
                        "enableFloatingIP": false
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "[variables('spWebProbe')]",
                        "properties": {
                            "protocol": "http",
                            "port": "[variables('spWebProbePort')]",
                            "intervalInSeconds": "15",
                            "numberOfProbes": "5",
                            "requestPath":"/"
                        }
                    }
                ]
            }
        },

### Microsoft.Network/networkInterfaces

這些區段會為每一個虛擬機器建立一個網路介面，然後設定網域控制站的靜態 IP 位址。以下是主要網域控制站的網路介面範例：

	{
		"name": "[variables('adPDCNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('rdpLBName'))]"
		],
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress" :"[parameters('adPDCNicIPAddress')]",
						"subnet": {
							"id": "[variables('staticSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id":"[variables('adBEAddressPoolID')]"
							}
						],
						"loadBalancerInboundNatRules": [
							{
								"id": "[variables('adRDPNATRuleID')]"
							}
						]
					}
				}
			]
		}
	},


### Microsoft.Compute/virtualMachines

這些區段會在該部署中建立以及設定 9 個虛擬機器。

前兩個主要區段會在該部署中建立和設定網域控制站。每一個區段：

- 指定儲存體帳戶、可用性設定組、網路介面和每個網域控制站虛擬機器的負載平衡器執行個體
- 將額外的磁碟新增到每一個網域控制站虛擬機器
- 執行 PowerShell 指令碼，將虛擬機器設定為網域控制站

以下是主要網域控制站範例：

        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('adPDCVMName')]",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]",
                "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
                "[resourceId('Microsoft.Network/loadBalancers',variables('rdpLBName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('adVMSize')]"
                },
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
                },
                "osProfile": {
                    "computername": "[variables('adPDCVMName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]",
                    "windowsProfile": {
                        "provisionVMAgent": "true"
                    }
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('adSourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/')]",
                    "dataDisks": [
                        {
                            "vhd": {
                                "uri":"[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adPDCVMName'),'data-1.vhd')]"
                                },
                            "name":"[concat(variables('adPDCVMName'),'-data-disk1')]",
                            "caching" : "None",
                            "diskSizeGB": "[variables('adDataDiskSize')]",
                            "lun": 0
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]"
                        }
                    ]
                }
            },
            "resources" :[
                {
                    "type": "Microsoft.Compute/virtualMachines/extensions",
                    "name": "[concat(variables('adPDCVMName'),'/InstallDomainController')]",
                    "apiVersion": "2014-12-01-preview",
                    "location": "[parameters('deploymentLocation')]",
                    "dependsOn":[
                        "[resourceId('Microsoft.Compute/virtualMachines', variables('adPDCVMName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "1.7",
                        "settings": {
                            "ModulesUrl": "[variables('adPDCModulesURL')]",
                            "ConfigurationFunction": "[variables('adPDCConfigurationFunction')]",
                            "Properties": {
                                "DomainName": "[parameters('domainName')]",
                                "AdminCreds":{
                                    "UserName": "[parameters('adminUserName')]",
                                    "Password": "PrivateSettingsRef:AdminPassword"
                                }
                            }
                        },
                        "protectedSettings": {
                            "Items": {
                                "AdminPassword": "[parameters('adminPassword')]"
                            }
                        }
                    }
                }
            ]
        },


每個網域控制站後面而且開頭是 **"name": "UpdateVNetDNS"** 的其他區段，會將虛擬網路的 DNS 伺服器設定成使用兩個網域控制站的靜態 IP 位址。

接下來的 3 個  **"type": "Microsoft.Compute/virtualMachines"** 區段，會在該部署中建立 SQL Server 叢集虛擬機器。 每一個區段：

- 指定每一個虛擬機器的儲存體帳戶、可用性設定組、負載平衡器、虛擬機器以及網路介面
- 將額外兩個磁碟新增至 SQL Server

其他 **"Microsoft.Compute/virtualMachines/extensions"** 區段會呼叫 PowerShell 指令碼來設定 SQL Server 叢集虛擬機器、SQL Server 叢集以及啟用 AlwaysOn 可用性群組。

接下來的 4 個 **"type": "Microsoft.Compute/virtualMachines"** 區段會在該部署中建立 SharePoint 伺服器陣列虛擬機器。每一個區段會指定每一個虛擬機器的儲存體帳戶、可用性設定組、負載平衡器、虛擬機器以及網路介面。

其他 **"Microsoft.Compute/virtualMachines/extensions"** 區段會呼叫 PowerShell 指令碼，將 SharePoint 伺服器設定成 SharePoint 伺服器陣列。

請注意 JSON **"resources"** 區段的子區段整體組織：

1.	建立 Azure 基礎結構的元素，我們需要這元素來支援多個虛擬機器 \(儲存體帳戶、公用 IP 位址、可用性設定組、虛擬網路、網路介面、負載平衡器執行個體\)。
2.	建立網域控制站虛擬機器，它們會利用先前建立的 Azure 基礎結構一般和特定元素、新增資料磁碟以及執行 PowerShell 指令碼。此外，更新虛擬網路來使用網域控制站的靜態 IP 位址。
3.	建立 SQL Server 叢集虛擬機器，它們會使用先前為網域控制器建立的 Azure 基礎結構一般和特定元素、新增資料磁碟以及執行 PowerShell 指令碼來設定叢集和 SQL Server AlwaysOn 可用性群組。
4.	建立 SharePoint 伺服器虛擬機器，它們會利用先前建立的 Azure 基礎結構一般和特定元素、新增資料磁碟以及執行 PowerShell 指令碼來設定 SharePoint 伺服器陣列。

在 Azure 中使用您自己的 JSON 範本建立多層式基礎結構時，請按照以下的步驟執行：

1.	為您的部署建立所需的 Azure 基礎結構一般 \(儲存體帳戶、虛擬網路\)、層相關 \(可用性設定組\) 以及虛擬機器相關 \(公用 IP 位址、可用性設定組、網路介面、負載平衡器執行個體\) 元素。
2.	至於應用程式中的每一層 \(例如驗證、資料庫、Web\)，使用一般 \(儲存體帳戶、 虛擬網路\)、 特定層 \(可用性設定組\) 和虛擬機器特定公用 IP 位址、 網路介面 \(負載平衡器執行個體\) 元素，在該層中建立以及設定伺服器。

如需詳細資訊，請參閱 [Azure Resource Manager 範本語言](https://msdn.microsoft.com/library/azure/dn835138.aspx)。


## 其他資源

[Azure Resource Manager 提供的 Azure 運算、網路和儲存提供者](virtual-machines-azurerm-versus-azuresm.md)

[Azure Resource Manager 概觀](resource-group-overview.md)

[設計 Azure Resource Manager 範本](resource-group-authoring-templates.md)

[虛擬機器文件](http://azure.microsoft.com/documentation/services/virtual-machines/)


<!--HONumber=52-->
