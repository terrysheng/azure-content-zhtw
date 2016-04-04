<properties
	pageTitle="自動調整虛擬機器調整集 | Microsoft Azure"
	description="使用 Azure CLI 開始建立及管理您的第一個 Azure 虛擬機器調整集"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="davidmu"/>

# 在虛擬機器調整集中自動調整機器



虛擬機器調整集可讓您部署和管理一組完全相同的虛擬機器。調整集可為超大規模的應用程式提供高度擴充和可自訂的計算層，並且可支援 Windows 平台映像、Linux 平台映像、自訂映像和延伸模組。如需調整集的詳細資訊，請參閱[虛擬機器調整集](virtual-machines-linux-scale-sets-overview.md)。

本教學課程說明如何使用 Ubuntu Linux 最新的修正版本建立 Linux 虛擬機器的虛擬機器調整集，以及如何對調整集內的機器進行自動調整。您可以藉由使用 Azure PowerShell 建立 Azure 資源管理員範本並加以部署，來達到此目的。如需範本的詳細資訊，請參閱[編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

您在本教學課程中建立的範本，類似於資源庫中提供的範本。若要深入了解，請參閱[Deploy a simple VM Scale Set with Linux VMs and a Jumpbox (使用 Linux VM 和 Jumpbox 部署簡易 VM 擴充集)](https://azure.microsoft.com/documentation/templates/201-vmss-linux-jumpbox/)。

開始本教學課程的步驟之前，請[安裝 Azure CLI](../xplat-cli-install.md)。

[AZURE.INCLUDE [virtual-machines-vmss-preview-cli](../../includes/virtual-machines-vmss-preview-cli-include.md)]

## 步驟 1：建立資源群組和儲存體帳戶

1. **登入 Microsoft Azure** - 在命令列介面 (Bash、終端機、命令提示字元) 中，輸入 `azure config mode arm` 以確定您在資源管理員模式，然後輸入 `azure login` 以[使用您的工作或學校識別碼登入](../xplat-cli-connect.md#use-the-log-in-method)，接著依照您的 Azure 帳戶互動式登入體驗的提示進行。

	> [AZURE.NOTE] 如果您有工作或學校識別碼，而且知道未啟用雙因素驗證，則可以使用 `azure login -u` 以及工作或學校識別碼，在沒有互動式工作階段的情況下進行登入。如果沒有工作或學校識別碼，您可以[從個人 Microsoft 帳戶建立工作或學校識別碼](virtual-machines-windows-create-aad-work-id.md)。

2. **建立資源群組** – 所有資源都必須部署至資源群組。在本教學課程中，我們將資源群組命名為 **vmsstest1**：

	```
	azure group create vmsstestrg1 westus
	```

3. **將儲存體帳戶部署到新的資源群組中** – 本教學課程使用數個儲存體帳戶，以利虛擬機器調整集的運作。建立名為 **vmsstestsa** 的儲存體帳戶。將命列介面視窗保持在開啟狀態，供本教學課程後續的步驟使用：

	```
	azure storage account create --type LRS -g vmsstestrg1 -l westus vmsstestsa
	```

## 步驟 2：建立範本
有了 Azure 資源管理員範本之後，您就可以使用 JSON 來說明資源、相關設定和部署參數，一起部署和管理 Azure 資源。

1. 在您慣用的文字編輯器中，建立檔案 C:\\VMSSTemplate.json ，並新增初始的 JSON 結構以支援範本。

	```
	{
		"$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
		}
		"variables": {
		}
		"resources": [
		]
	}
	```

2. 參數並非必要項目，但是可以讓範本管理更容易。它們可用來指定範本的值、說明值類型、預設值 (如有需要)，以及可能的參數允許值。

	在您新增至範本的參數父元素下，新增下列參數：

	- 用來對調整集內的機器進行存取之 Jumpbox 虛擬機器的名稱。
	- 用來存放範本之儲存體帳戶的名稱。
	- 最初在調整集內建立的虛擬機器執行個體數目。
	- 虛擬機器之系統管理帳戶的名稱和密碼。
	- 在資源群組中建立之資源的前置詞。


	```
	"vmName": {
		"type": "string"
	},
	"vmSSName": {
		"type": "string"
	},
	"instanceCount": {
		"type": "string"
	},
	"adminUsername": {
		"type": "string"
	},
	"adminPassword": {
		"type": "securestring"
	},
	"resourcePrefix": {
		"type": "string"
	}
	```

3. 變數可以在範本中用來指定會經常變更的值或需要透過參數值組合建立的值。

	在您新增至範本的變數父元素下，新增下列變數：

	- 網路介面所使用的 DNS 名稱。
	- 調整集內使用的虛擬機器大小。如需關於虛擬機器大小的詳細資訊，請參閱[虛擬機器大小](virtual-machines-linux-sizes.md)。
	- 用來定義將在調整集內的虛擬機器上執行之作業系統的平台映像資訊。如需關於選取映像的詳細資訊，請參閱[使用 Windows PowerShell 和 Azure CLI 瀏覽和選取 Azure 虛擬機器映像](virtual-machines-linux-cli-ps-findimage.md)。

	- 虛擬網路和子網路的 IP 位址名稱和前置詞。
	- 虛擬網路、負載平衡器和網路介面的名稱和識別碼。
	- 與調整集內的機器相關聯之帳戶的儲存體帳戶名稱。
	- 安裝在虛擬機器上的診斷延伸模組的設定。如需診斷延伸模組的詳細資訊，請參閱[使用 Azure 資源管理員範本建立具有監控和診斷功能的 Windows 虛擬機器](virtual-machines-windows-extensions-diagnostics-template.md)。

	```
	"apiVersion": "2015-06-15"
	"dnsName1": "[concat(parameters('resourcePrefix'),'dn1')] ",
	"dnsName2": "[concat(parameters('resourcePrefix'),'dn2')] ",
	"vmSize": "Standard_A0",
	"imagePublisher": "Canonical",
	"imageOffer": "UbuntuServer",
	"imageVersion": "15.10",
	"addressPrefix": "10.0.0.0/16",
	"subnetName": "Subnet",
	"subnetPrefix": "10.0.0.0/24",
	"publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
	"publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
	"loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
	"virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
	"nicName1": "[concat(parameters('resourcePrefix'),'nc1')]",
	"nicName2": "[concat(parameters('resourcePrefix'),'nc2')]",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
	"publicIPAddressID1": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP1'))]",
	"publicIPAddressID2": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP2'))]",
	"lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
	"nicId": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName2'))]",
	"frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
	"storageAccountType": "Standard_LRS",
	"storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
	"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg><DiagnosticMonitorConfiguration>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor\\PercentProcessorTime" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU percentage guest OS" locale="zh-TW"/></PerformanceCounterConfiguration>",
	"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
	"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
	"wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
	```

4. 在本教學課程中，您可以部署下列資源和延伸模組：

 - Microsoft.Storage/storageAccounts
 - Microsoft.Network/virtualNetworks
 - Microsoft.Network/publicIPAddresses
 - Microsoft.Network/loadBalancers
 - Microsoft.Network/networkInterfaces
 - Microsoft.Compute/virtualMachines
 - Microsoft.Compute/virtualMachineScaleSets
 - Microsoft.Insights.VMDiagnosticsSettings
 - Microsoft.Insights/autoscaleSettings

	如需「資源管理員」資源的詳細資訊，請參閱 [Azure 資源管理員下的 Azure 計算、網路和儲存體提供者](virtual-machines-windows-compare-deployment-models.md)。

	在您新增至範本的資源父元素下，新增下列儲存體帳戶資源：此範本使用迴圈來建立作業系統磁碟和診斷資料儲存於其中的 5 個建議的儲存體帳戶。這組帳戶最多可在一個調整集內支援 100 個虛擬機器，這是目前的最大值。每個儲存體帳戶的命名方式都相同，即變數中所定義的字母指示項，加上您在參數中為範本提供的後置詞。

	```
	{
		"type": "Microsoft.Storage/storageAccounts",
		"name": "[concat(parameters('resourcePrefix'), parameters('storageAccountSuffix')[copyIndex()])]",
		"apiVersion": "2015-05-01-preview",
		"copy": {
			"name": "storageLoop",
			"count": 5
		},
		"location": "[resourceGroup().location]",
		"properties": {
			"accountType": "[variables('storageAccountType')]"
		}
	},
	```

5. 新增虛擬網路資源。如需詳細資訊，請參閱[網路資源提供者](../virtual-network/resource-groups-networking.md)。

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/virtualNetworks",
		"name": "[variables('virtualNetworkName')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"addressSpace": {
				"addressPrefixes": [
					"[variables('addressPrefix')]"
				]
			},
			"subnets": [
				{
					"name": "[variables('subnetName')]",
					"properties": {
						"addressPrefix": "[variables('subnetPrefix')]"
					}
				}
			]
		}
	},
	```

6. 新增負載平衡器和網路介面所使用的公用 IP 位址資源。

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('publicIP1')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"publicIPAllocationMethod": "Dynamic",
			"dnsSettings": {
				"domainNameLabel": "[variables('dnsName1')]"
			}
		}
	},
	{
		"apiVersion": "[variables('apiVersion')]",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('publicIP2')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"publicIPAllocationMethod": "Dynamic",
			"dnsSettings": {
				"domainNameLabel": "[variables('dnsName2')]"
			}
		}
	},
	```

7. 新增調整集所使用的負載平衡器資源。如需詳細資料，請參閱 [Azure 資源管理員的負載平衡器支援](../load-balancer/load-balancer-arm.md)。

	```
	{
		"apiVersion": "[variables('apiVersion')]",
		"name": "[variables('loadBalancerName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "loadBalancerFrontEnd",
					"properties": {
						"publicIPAddress": {
							"id": "[variables('publicIPAddressID1')]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "bepool1"
				}
			],
			"inboundNatPools": [
				{
					"name": "natpool1",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('frontEndIPConfigID')]"
						},
						"protocol": "tcp",
						"frontendPortRangeStart": 50000,
						"frontendPortRangeEnd": 50500,
						"backendPort": 3389
					}
				}
			]
		}
	},
	```

8. 新增 Jumpbox 虛擬機器所使用的網路介面資源。由於虛擬機器調整集內的虛擬機器無法直接使用公用 IP 位址來存取，因此必須在與調整集相同的虛擬網路中建立 Jumpbox 虛擬機器，並用它從遠端存取調整集內的機器。

	```
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/networkInterfaces",
		"name": "[variables('nicName1')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
			"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
		],
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Dynamic",
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
						},
						"subnet": {
							"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
						}
					}
				}
			]
		}
	},
	```


9. 在與調整集相同的網路中新增 Jumpbox 虛擬機器。

	```
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[parameters('vmName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"storageLoop",
			"[concat('Microsoft.Network/networkInterfaces/', variables('nicName1'))]"
		],
		"properties": {
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[parameters('vmName')]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]"
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[variables('imagePublisher')]",
					"offer": "[variables('imageOffer')]",
					"sku": "[variables('imageVersion')]",
					"version": "latest"
				},
				"osDisk": {
					"name": "osdisk1",
					"vhd": {
						"uri":  "[concat('https://',parameters('resourcePrefix'),'saa.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'osdisk1.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
					{
						"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName1'))]"
					}
				]
			}
		}
	},
	```

10.	新增虛擬機器調整集資源，並指定在調整集內的所有虛擬機器上安裝的診斷延伸模組。此資源有許多設定都與虛擬機器資源相類似。以下是兩者的主要差異：

	- **capacity** - 指定在調整集內應初始化的虛擬機器數目。您可以指定 instanceCount 參數的值，以設定此值。

	- **upgradePolicy** – 指定如何對調整集內的虛擬機器進行更新。「手動」會指定在重新部署範本時，範本中的變更只會影響到新的虛擬機器。「自動」會指定調整集內的所有機器都會更新並重新啟動。

	在依照 dependsOn 元素的指定建立所有的儲存體帳戶之前，將不會建立虛擬機器調整集。

	```
	{
		"type": "Microsoft.Compute/virtualMachineScaleSets",
		"apiVersion": "[variables('apiVersion')]",
		"name": "[parameters('vmSSName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"storageLoop",
			"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
			"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
		],
		"sku": {
			"name": "[variables('vmSize')]",
			"tier": "Standard",
			"capacity": "[parameters('instanceCount')]"
		}
		"properties": {
			"upgradePolicy": {
				"mode": "Manual"
			},
			"virtualMachineProfile": {
				"storageProfile": {
					"osDisk": {
						"vhdContainers": [
							"[concat('https://', parameters('resourcePrefix'), 'saa.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sag.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sam.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'sas.blob.core.windows.net/vmss')]",
							"[concat('https://', parameters('resourcePrefix'), 'say.blob.core.windows.net/vmss')]"
						],
						"name": "vmssosdisk",
						"caching": "ReadOnly",
						"createOption": "FromImage"
					},
					"imageReference": {
						"publisher": "[variables('imagePublisher')]",
						"offer": "[variables('imageOffer')]",
						"sku": "[variables('imageVersion')]",
						"version": "latest"
					}
				},
				"osProfile": {
					"computerNamePrefix": "[parameters('vmSSName')]",
					"adminUsername": "[parameters('adminUsername')]",
					"adminPassword": "[parameters('adminPassword')]"
				},
				"networkProfile": {
					"networkInterfaceConfigurations": [
						{
							"name": "[variables('nicName2')]",
							"properties": {
								"primary": "true",
								"ipConfigurations": [
									{
										"name": "ip1",
										"properties": {
											"subnet": {
												"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
											},
											"loadBalancerBackendAddressPools": [
												{
													"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
												}
											],
											"loadBalancerInboundNatPools": [
												{
													"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
												}
											]
										}
									}
								]
							}
						}
					]
				},
				"extensionProfile": {
					"extensions": [
						{
							"name":"LinuxDiagnostic",
							"properties": {
								"publisher":"Microsoft.OSTCExtensions",
								"type":"LinuxDiagnostic",
								"typeHandlerVersion":"2.1",
								"autoUpgradeMinorVersion":false,
								"settings": {
									"xmlCfg":"[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
									"storageAccount":"[variables('diagnosticsStorageAccountName')]"
								},
								"protectedSettings": {
									"storageAccountName":"[variables('diagnosticsStorageAccountName')]",
									"storageAccountKey":"[listkeys(variables('accountid'), variables('apiVersion')).key1]",
									"storageAccountEndPoint":"https://core.windows.net"
								}
							}
						}
					]
				}
			}
		}
	},
	```

11.	新增 autoscaleSettings 資源，以定義如何根據調整集內各機器的處理器使用情形進行調整集的調整。在本教學課程中，以下是重要的值：

 - **metricName** - 這與我們在 wadperfcounter 變數中定義的效能計數器相同。使用該變數時，診斷擴充功能將會收集 **Processor\\PercentProcessorTime** 計數器。
 - **metricResourceUri** - 這是虛擬機器調整集的資源識別碼。
 - **timeGrain** – 這是所收集之計量的精細度。此範本中，此值設為 1 分鐘。
 - **statistic** – 這會決定如何結合計量以因應自動調整動作的需要。可能的值為：Average、Min、Max。在此範本中，我們將求取調整集內各個虛擬機器之間的平均 CPU 使用率總計。
 - **timeWindow** – 這是收集執行個體資料的時間範圍。其值必須介於 5 分鐘到 12 小時之間。
 - **timeAggregation** – 這會決定收集的資料應如何隨著時間結合。預設值為 Average。可能的值為：Average、Minimum、Maximum、Last、Total、Count。
 - **operator** – 這是用來比較計量資料和臨界值的運算子。可能的值為：Equals、NotEquals、GreaterThan、GreaterThanOrEqual、LessThan、LessThanOrEqual。
 - **threshold** – 這是觸發調整動作的值。在此範本中，會在調整集內的機器之間的平均 CPU 使用率超過 50% 時，將機器新增至調整集。
 - **direction** – 這會決定達到臨界值時所採取的動作。可能的值為 Increase 或 Decrease。在此範本中，如果臨界值在定義的時間範圍內超過 50%，則會增加調整集內的虛擬機器數目。
 - **type** – 這是所應採取的動作類型，必須設為 ChangeCount。
 - **value** – 這是在調整集內新增或移除的虛擬機器數目。此值必須是 1 或更大。預設值為 1。在此範本中，會在達到臨界值時，將調整集內的機器數目加 1。
 - **cooldown** – 這是在上一個調整動作之後、下一個動作執行之前的等待時間量。其值必須介於 1 分鐘到 1 週之間。

	```
	{
		"type": "Microsoft.Insights/autoscaleSettings",
		"apiVersion": "2015-04-01",
		"name": "[concat(parameters('resourcePrefix'),'as1')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
		],
		"properties": {
			"enabled": true,
			"name": "[concat(parameters('resourcePrefix'),'as1')]",
			"profiles": [
				{
					"name": "Profile1",
					"capacity": {
						"minimum": "1",
						"maximum": "10",
						"default": "1"
					},
					"rules": [
						{
							"metricTrigger": {
								"metricName": "\\Processor\\PercentProcessorTime",
								"metricNamespace": "",
								"metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
								"timeGrain": "PT1M",
								"statistic": "Average",
								"timeWindow": "PT5M",
								"timeAggregation": "Average",
								"operator": "GreaterThan",
								"threshold": 50.0
							},
							"scaleAction": {
								"direction": "Increase",
								"type": "ChangeCount",
								"value": "1",
								"cooldown": "PT5M"
							}
						}
					]
				}
			],
			"targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
		}
	}
	```

12.	儲存範本檔案。    

## 步驟 3：將範本上傳至儲存體

只要您知道您在步驟 1 中建立之儲存體帳戶的帳戶名稱和主要金鑰，即可從您的命令列介面上傳範本。

1. 在您的命令列介面 (Bash、終端機、命令提示字元) 中，執行下列命令來設定存取儲存體帳戶所需的環境變數：

		export AZURE_STORAGE_ACCOUNT={account_name}
		export AZURE_STORAGE_ACCESS_KEY={key}

	您可以在 Azure 入口網站中檢視儲存體帳戶資源，並按一下金鑰圖示，以取得此金鑰。使用 Windows 命令提示字元時，請輸入 **set** 而不是 export。

2. 建立儲存範本的容器：

		azure storage container create -p Blob templates

3. 將範本檔案上傳至新的容器。

		azure storage blob upload VMSSTemplate.json templates VMSSTemplate.json

## 步驟 4：部署範本

建立範本後，您就可以開始部署資源。使用下列命令啟動程序：

	azure group deployment create --template-uri https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json vmsstestrg1 vmsstestdp1

當您按 Enter 鍵時，系統會提示您提供您所指派的變數值。請提供下列值：

	vmName: vmsstestvm1
	vmSSName: vmsstest1
	instanceCount: 5
	adminUserName: vmadmin1
	adminPassword: VMpass1
	resourcePrefix: vmsstest

所有資源要順利部署完成約需要 15 分鐘，。

>[AZURE.NOTE]您也可以利用入口網站的功能來部署資源。若要這樣做，請使用下列連結：https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>

## 步驟 5：監視資源

您可以使用下列方法，取得關於虛擬機器調整集的某些資訊：

 - Azure 入口網站 - 目前，使用入口網站可以取得限定數量的資訊。
 - [Azure 資源總管](https://resources.azure.com/) - 這是最適合用來瀏覽調整集現行狀態的工具。按照此路徑，您應該會看到您所建立之調整集的執行個體檢視：

		subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Azure CLI - 使用下列命令可取得某些資訊：

		azure resource show -n vmsstest1 -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g vmsstestrg1

 - 比照任何其他機器連接到 Jumpbox 虛擬機器，您即可從遠端存取調整集內的虛擬機器，以監視個別程序。

>[AZURE.NOTE]在[虛擬機器調整集](https://msdn.microsoft.com/library/mt589023.aspx)中可以找到用來取得調整集相關資訊的完整 REST API。

## 步驟 6：移除資源

您將為 Azure 中所使用的資源支付費用，因此，刪除不再需要的資源永遠是最好的做法。您不需要從資源群組個別刪除每個資源。您可以刪除資源群組，且其所有資源都將會自動刪除。

		azure group delete vmsstestrg1

## 後續步驟

請參閱[自動調整執行 Ubuntu/Apache/PHP 應用程式範本以設定 LAMP 堆疊的 VM 調整集](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale)，以執行虛擬機器調整集的自動調整功能。

<!---HONumber=AcomDC_0323_2016-->