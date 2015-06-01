<properties
	pageTitle="Ubuntu 資源管理員範本上的 DataStax"
	description="了解如何使用 Azure PowerShell 或 Azure CLI 搭配 Azure Resource Manager 範本，在 Ubuntu VM 上輕鬆部署新的 DataStax 叢集"
	services="multiple"
	documentationCenter=""
	authors="karthmut"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="karthmut"/>

# Ubuntu 資源管理員範本上的 DataStax

DataStax 是知名的業界領導者，他們根據商用企業級 Apache Cassandra™ 開發以及提供各種解決方案，這是一種廣受市場認可、敏捷、永不停擺，並且可依照未來各種公司規模需要來進行調整的開放原始碼 NoSQL 分散式資料庫技術。DataStax 適用於企業 (DSE) 和社群 (DSC)，它提供的功能有記憶體內部運算、企業級安全性、快速且功能強大的整合式分析與企業搜尋。

除了提供 Azure Marketplace 已有的功能之外，現在您也可以使用 Azure PowerShell 或 Azure CLI 搭配 Resource Manager 範本，在 Ubuntu VM 上輕鬆部署新的 DataStax 叢集。

利用這個範本部署的新叢集會採用下圖描述的拓撲，不過您可以自訂顯示的方法，同樣能輕鬆實現其他的拓撲：

![cluster-architecture](media/virtual-machines-datastax-template/cluster-architecture.png)

基本上，您可以利用參數來定義要在新的 Apache Cassandra 叢集中部署的節點個數，除此之外，也會在同一個 VNET 的獨立式 VM 上部署一個 Datastax Operations Center 服務執行個體，讓您可以監視叢集和所有個別節點的狀態、新增/移除節點，以及執行叢集的各種相關管理工作。

部署完成之後，您可以使用設定的 DNS 位址，存取 Datastax Operations Center VM 執行個體。OpsCenter VM 會啟用 SSH 連接埠 22 以及針對HTTPS 啟用連接埠 8443。作業中心的 DNS 位址會包括 dnsName 和區域，也就是當您根據這個範本進行部署時所輸入的參數，格式為 `{dnsName}.{region}.cloudapp.azure.com`。進行部署時，如果在「美國西部」區域中，這個 `dnsName` 參數是設定成 "datastax"，您就可以存取 `https://datastax.westus.cloudapp.azure.com:8443` 區域部署的 Datastax Operations Center 虛擬機器。

> [AZURE.NOTE]部署時使用的憑證是一種自我簽署的憑證，會造成瀏覽器發出警告。您可以按照 [Datastax](http://www.datastax.com/) 網站的程序，將這個憑證換成自己的 SSL 憑證。

開始深入了解 Azure Resource Manager 以及此次部署時使用的範本之前，請確定您已經設定好 Azure、PowerShell 以及 Azure CLI 並準備就緒。

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../includes/xplat-getting-set-up-arm.md)]

## 利用 Resource Manager 範本和 Azure PowerShell，建立 DataStax 類型的 Cassandra 叢集

請依照下列步驟，搭配 Azure PowerShell 並使用 Github 範本儲存機制中的 Resource Manager 範本，建立 DataStax 類型的 Apache Cassandra 叢集。

### 步驟 1：下載範本和其他檔案的 JSON 檔案。

指派一個本機資料夾，當做 JSON 範本和其他檔案的存放位置，然後建立這個資料夾 (例如，C:\\Azure\\Templates\\DataStax)。

填寫資料夾名稱，然後執行以下命令：

	$folderName="C:\Azure\Templates\DataStax"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/azuredeploy-parameters.json"
	$filePath = $folderName + "\azuredeploy-parameters.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/dsenode.sh"
	$filePath = $folderName + "\dsenode.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/ephemeral-nodes-resources.json"
	$filePath = $folderName + "\ephemeral-nodes-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/metadata.json"
	$filePath = $folderName + "\metadata.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-install-resources.json"
	$filePath = $folderName + "\opscenter-install-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter-resources.json"
	$filePath = $folderName + "\opscenter-resources.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/opscenter.sh"
	$filePath = $folderName + "\opscenter.sh"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/datastax-on-ubuntu/shared-resources.json"
	$filePath = $folderName + "shared-resources.json"
	$webclient.DownloadFile($url,$filePath)

或者，您也可以使用自選的 git 用戶端，複製範本儲存機制，例如：

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

完成時，尋找 C:\\Azure\\Templates 中的 datastax-on-ubuntu 資料夾。

### 步驟 2：(選用) 熟悉範本參數。

部署重量級解決方案時，例如 DataStax 類型的 DataStax Apache Cassandra 叢集，您必須指定一組設定參數來處理一些必要的設定。您可以在範本定義中宣告這些參數，這樣一來當您透過外部檔案或者在命令列進行部署的時候，就可以指定每一個值。

到 azuredeploy.json 檔案開頭尋找 "parameters" 區段，您會發現當設定 DataStax 叢集時，範本所需要的參數集。以下是 azuredeploy.json 範本的 parameters 區段範例：

	"parameters": {
		"region": {
			"type": "string",
			"defaultValue": "West US",
			"metadata": {
				"Description": "Location where resources will be provisioned"
			}
		},
		"storageAccountPrefix": {
			"type": "string",
			"defaultValue": "uniqueStorageAccountName",
			"metadata": {
				"Description": "Unique namespace for the Storage Account where the Virtual Machine's disks will be placed"
			}
		},
		"dnsName": {
			"type": "string",
			"metadata" : {
				"Description": "DNS subname for the opserations center public IP"
			}
		},
		"virtualNetworkName": {
			"type": "string",
			"defaultValue": "myvnet",
			"metadata": {
				"Description": "Name of the virtual network provisioned for the cluster"
			}
		},
		"adminUsername": {
			"type": "string",
			"metadata": {
				"Description": "Administrator user name used when provisioning virtual machines"
			}
		},
		"adminPassword": {
			"type": "securestring",
			"metadata": {
				"Description": "Administrator password used when provisioning virtual machines"
			}
		},
		"opsCenterAdminPassword": {
			"type": "securestring",
			"metadata": {
				"Description": "Sets the operations center admin user password"
			}
		},
		"clusterVmSize": {
			"type": "string",
			"defaultValue": "Standard_D3",
			"allowedValues": [
				"Standard_D1",
				"Standard_D2",
				"Standard_D3",
				"Standard_D4",
				"Standard_D11",
				"Standard_D12",
				"Standard_D13",
				"Standard_D14"
			],
			"metadata": {
				"Description": "The size of the virtual machines used when provisioning cluster nodes"
			}
		},
		"clusterNodeCount": {
			"type": "int",
			"metadata": {
				"Description": "The number of nodes provisioned in the cluster"
			}
		},
		"clusterName": {
			"type": "string",
			"metadata": {
				"Description": "The name of the cluster provisioned"
			}
		}
	}

透過描述必要的參數 (包括像是資料類型、允許的值等詳細資料) ，您在互動模式 (例如 PowerShell 或 Azure CLI) 執行範本時，這個區段可以幫助我們了解任何傳遞的參數值會涉及到的驗證工作。同樣地，分析必要的參數清單以及描述之後，我們也可以清楚掌握任何可以動態建立的自我發現 UI。

### 步驟 3：利用範本部署新的 DataStax 叢集。

準備好部署前需要的參數檔案，表示您會建立一個 JSON 檔案而且其中包含所有參數的執行階段值。這些值會以單一實體的形式，分別傳遞至部署命令。

以下是您可以在 azuredeploy-parameters.json 檔案中找到的範例：

	{
		"storageAccountPrefix": {
			"value": "scorianisa"
		},
		"dnsName": {
			"value": "scorianids"
		},
		"virtualNetworkName": {
			"value": "datastax"
		},
		"adminUsername": {
			"value": "scoriani"
		},
		"adminPassword": {
			"value": ""
		},
		"region": {
			"value": "West US"
		},
		"opsCenterAdminPassword": {
			"value": ""
		},
		"clusterVmSize": {
			"value": "Standard_D3"
		},
		"clusterNodeCount": {
			"value": 3
		},
		"clusterName": {
			"value": "cl1"
		}
	}

填寫 Azure 部署名稱、資源群組名稱、Azure 位置、儲存的 JSON 檔案放在哪一個資料夾，然後執行以下命令：

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\DataStax>"
	$templateFile= $folderName + "\azuredeploy.json"
	$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

	New-AzureResourceGroup –Name $RGName –Location $locName

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

執行 **New-AzureResourceGroupDeployment** 命令時，會從 JSON 檔案中擷取參數值，然後開始執行範本。定義以及使用不同環境 (例如測試、正式作業等) 所需的參數檔案，有利範本的重複使用以及簡化複雜的多重環境解決方案。

請記住，部署的時候需要建立新的 Azure 儲存體帳戶，所以您提供的名稱 (當做儲存體帳戶參數) 絕對不可以和其他名稱重複，同時還要符合 Azure 儲存體帳戶的所有要求。

部署期間以及部署結束之後，您可以檢查所有佈建期間所進行的要求，包括任何發生的錯誤。

若要這樣做，請移至 [Azure 入口網站](https://portal.azure.com)，然後執行下列動作：

- 按一下左側導覽列上的 [瀏覽]、向下捲動後按一下 [ 資源群組]。  
- 按一下剛才建立的資源群組後，系統會顯示 [ 資源群組] 分頁。  
- 在 [資源群組] 分頁的 [監視] 部分，按一下 [事件] 長條圖，就會看到整個部署的事件：
- 按一下個別事件之後，系統會詳細列出不是由範本親自進行的各項操作。

測試之後，如果想移除這個資源群組和其所有資源 (儲存體帳戶、虛擬機器和虛擬網路)，請使用這個命令。

	Remove-AzureResourceGroup –Name "<resource group name>" -Force

### 步驟 3 b：使用 Azure CLI 搭配 Resource Manager 範本部署 DataStax 叢集

功能與上面列出的 PowerShell 方法相當，透過 Azure CLI 部署 Apache Cassandra 叢集的時候，必須指定名稱和位置，先建立一個資源群組：

	azure group create dsc "West US"

接下來，叫用部署建立和傳遞資源群組名稱、參數檔案和實際範本，如下所示：

	azure group deployment create dsc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

您也可以叫用下列命令來檢查個別的部署狀態：

	azure group deployment list dsc

## 示範如何建立範本結構和檔案組織，以便在 Ubuntu 上部署 DataStax

為了讓 Azure Resource Manager 範本的設計更加完善而且可重複使用，當我們部署 DataStax 此類的複雜解決方案的時候，必須考慮清楚如何安排一連串複雜，但又彼此有關的工作。除了透過相關延伸模組執行指令碼之外，還可以利用 ARM **範本連結** 功能、**資源迴圈**，這樣就可以實現一種模組方法，而且可以重複使用到任何範本式部署。

下圖描述此次部署時，從 GitHub 下載的所有檔案，其彼此之間的關係：

![datastax-files](media/virtual-machines-datastax-template/datastax-files.png)

我們已經提過 **azuredeploy-parameters.json** 的角色，當我們執行範本時，會利用它來傳遞一組指定的參數值，不過這種部署方法的核心部分是包含在 **azuredeploy.json** 裡面。如前文所述，跳過 parameters 區段，以下區段會用 **"variables"** 表示。基本上，這會包含數個欄位 (JSON 資料類型或片段)，它們會在執行階段被設定成常數或計算值，就像下列範例中看到的一樣：

	"variables": {
	"templateBaseUrl": "https://raw.githubusercontent.com/trentmswanson/azure-quickstart-templates/master/datastax-on-ubuntu/",
	"sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
	"clusterNodesTemplateUrl": "[concat(variables('templateBaseUrl'), 'ephemeral-nodes-resources.json')]",
	"opsCenterTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-resources.json')]",
	"opsCenterInstallTemplateUrl": "[concat(variables('templateBaseUrl'), 'opscenter-install-resources.json')]",
	"opsCenterVmSize": "Standard_A1",
	"networkSettings": {
		"virtualNetworkName": "[parameters('virtualNetworkName')]",
		"addressPrefix": "10.0.0.0/16",
		"subnet": {
			"dse": {
				"name": "dse",
				"prefix": "10.0.0.0/24",
				"vnet": "[parameters('virtualNetworkName')]"
			}
		},
		"statics": {
			"clusterRange": {
				"base": "10.0.0.",
				"start": 5
			},
			"opsCenter": "10.0.0.240"
		}
	},
	"osSettings": {
		"imageReference": {
			"publisher": "Canonical",
			"offer": "UbuntuServer",
			"sku": "14.04.2-LTS",
			"version": "latest"
		},
		"scripts": [
			"[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
			"[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
			"https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
		]
	},
	"sharedStorageAccountName": "[concat(parameters('storageAccountPrefix'),'cmn')]",
	"nodeList": "[concat(variables('networkSettings').statics.clusterRange.base, variables('networkSettings').statics.clusterRange.start, '-', parameters('clusterNodeCount'))]"
	},

深入研究這個範例之後，就會看到兩種不同的方法。在第一個程式碼片段中，"osSettings" 變數會被設定至巢狀 JSON 元素中，裡面有 4 組機碼值組：

	"osSettings": {
	      "imageReference": {
	        "publisher": "Canonical",
	        "offer": "UbuntuServer",
	        "sku": "14.04.2-LTS",
	        "version": "latest"
	      },

	 
在第二個程式碼片段中，"scripts" 變數則是一個 JSON 陣列，在執行階段會使用範本語言函式 (concat) 以及另一個變數的值再加上字串常數，計算這個陣列中的每一個元素：

	      "scripts": [
	        "[concat(variables('templateBaseUrl'), 'dsenode.sh')]",
	        "[concat(variables('templateBaseUrl'), 'opscenter.sh')]",
	        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
	      ]

絕大多數的動作就是在 **"resources"** 區段進行的。仔細看這個區段，您會立即找出兩個不同的案例：第一個是被定義為 `Microsoft.Resources/deployments` 類型的元素，基本上表示叫用第一個主要檔案裡面的巢狀部署。透過 `templateLink` 元素 (和相關的版本屬性)，就可以指定一個連結範本檔案，而且叫用這個檔案的時候，會傳遞一組參數當做輸入，就像您在這個片段中看到的一樣：

	{
	      "name": "shared",
	      "type": "Microsoft.Resources/deployments",
	      "apiVersion": "2015-01-01",
	      "properties": {
	        "mode": "Incremental",
	        "templateLink": {
	          "uri": "[variables('sharedTemplateUrl')]",
	          "contentVersion": "1.0.0.0"
	        },
	        "parameters": {
	          "region": {
	            "value": "[parameters('region')]"
	          },
	          "networkSettings": {
	            "value": "[variables('networkSettings')]"
	          },
	          "storageAccountName": {
	            "value": "[variables('sharedStorageAccountName')]"
	          }
	        }
	      }
	    },

在這個第一個範例中，我們知道 **azuredeploy.json** 在這個案例是當做一種協調機制，負責叫用其他幾個範本檔案，而且每一個檔案又負責必要的部署活動。

特別是，下列連結的範本將用於此部署：

-	**shared-resource.json**：包含所有會在部署時共用的資源定義。例如，儲存 VM 的作業系統磁碟或虛擬網路時會用到儲存體帳戶。
-	**opscenter-resources.json**：部署 OpsCenter VM 和所有相關資源，例如網路介面、公用 IP 位址等。
-	**opscenter-install-resources.json**：部署 OpsCenter VM 延伸模組 (適用於 Linux 的自訂指令碼)，而且會叫用特定的 bash 指令碼檔案 (**opscenter.sh**)，用來在 VM 內安裝 OpsCenter 服務。
-	**ephemeral-nodes-resources.json**：部署所有叢集節點的 VM 和連接的資源 (例如網路卡，私用 IP 等)。此範本也會部署 VM 擴充功能 (適用於 Linux 的自訂指令碼)，並叫用 bash 指令碼 (**dsenode.sh**)，在每一個節點上安裝 Apache Cassandra 程式碼。

讓我們深入了解最後一個範本，因為從範本開發角度來看，這是最有趣的範本之一。在此要強調一個重要的概念，那就是一個範本檔案可以重複部署某一種資源類型，而且每一個執行個體都可以為必要的設定指定唯一的值。這個概念稱為**資源迴圈**。

叫用主要 **azuredeploy.json** 檔案裡面的 **ephemeral-nodes-resources.json** 時，事實上會在參數清單中提供一個 **nodeCount** 參數。在子系範本中，那些需要部署到多個複本中的每一個資源，就會在 **"copy"** 元素使用這個參數，如下列程式碼片段標示的一樣。想了解當您為每一個部署的資源指定唯一的值時，哪些是必要的設定，那麼可以使用 **copyindex()** 函數來取得一個數值，指出在建立的特殊資源迴圈中，現在的索引為何。在下列程式碼片段中，您會看到為叢集節點建立 VM 時，多次運用這種概念：

			   {
			      "apiVersion": "2015-05-01-preview",
			      "type": "Microsoft.Compute/virtualMachines",
			      "name": "[concat(parameters('namespace'), 'vm', copyindex())]",
			      "location": "[parameters('region')]",
			      "copy": {
			        "name": "[concat(parameters('namespace'), 'vmLoop')]",
			        "count": "[parameters('nodeCount')]"
			      },
			      "dependsOn": [
			        "[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]",
			        "[concat('Microsoft.Compute/availabilitySets/', parameters('namespace'), 'set')]",
			        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]"
			      ],
			      "properties": {
			        "availabilitySet": {
			          "id": "[resourceId('Microsoft.Compute/availabilitySets', concat(parameters('namespace'), 'set'))]"
			        },
			        "hardwareProfile": {
			          "vmSize": "[parameters('vmSize')]"
			        },
			        "osProfile": {
			          "computername": "[concat(parameters('namespace'), 'vm', copyIndex())]",
			          "adminUsername": "[parameters('adminUsername')]",
			          "adminPassword": "[parameters('adminPassword')]"
			        },
			        "storageProfile": {
			          "imageReference": "[parameters('osSettings').imageReference]",
			          "osDisk": {
			            "name": "osdisk",
			            "vhd": {
			              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/vhds/', variables('vmName'), copyindex(), '-osdisk.vhd')]"
			            },
			            "caching": "ReadWrite",
			            "createOption": "FromImage"
			          },
			          "dataDisks": [
			            {
			              "name": "datadisk1",
			              "diskSizeGB": 1023,
			              "lun": 0,
			              "vhd": {
			                "Uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/','vhds/', variables('vmName'), copyindex(), 'DataDisk1.vhd')]"
			              },
			              "caching": "None",
			              "createOption": "Empty"
			            }
			          ]
			        },
			        "networkProfile": {
			          "networkInterfaces": [
			            {
			              "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('namespace'), 'nic', copyindex()))]"
			            }
			          ]
			        }
			      }
			    },

建立資源時還有一個重要的概念，那就是指定資源之間的相依性和優先順序，如同您在 **dependsOn** JSON 陣列中看到的一樣。在這個特殊的範本中，每一個節點也會附加 1 TB 磁碟 (請參閱 `dataDisks`)，可以用來裝載 Apache Cassandra 執行個體的備份和快照。

當 **dsenode.sh** 指令碼檔執行時，會進行各種節點準備活動，其中之一就是格式化連接的磁碟。事實上，這個指令碼的第一列會呼叫另一個指令碼：

	bash vm-disk-utils-0.1.sh

vm-disk-utils-0.1.sh 是 **shared_scripts\\ubuntu** 資料夾的一部分，就在 azure-quickstart-tempates github repo 裡面，而且包含實用的功能，例如磁碟裝載、格式化以及條分狀配置，每次建立範本的時候，就可以在執行相同工作時重複使用。

我們繼續探索另一個與 CustomScriptForLinux VM 擴充功能有關而且很有趣的程式碼片段。這些都是當做不同類型而安裝的資源，每一個叢集節點 (以及 OpsCenter 執行個體) 彼此存在相依性，利用描述虛擬機器時的相同資源迴圈機制：

	{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat(parameters('namespace'), 'vm', copyindex(), '/installdsenode')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "[concat(parameters('namespace'), 'vmLoop')]",
		"count": "[parameters('nodeCount')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', parameters('namespace'), 'vm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', parameters('namespace'), 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": "[parameters('osSettings').scripts]",
			"commandToExecute": "bash dsenode.sh"
		}
	}
	}

此次部署時，其他所需的範本檔案會建立所有必要資源的單一執行個體，因此可以視為是這個 **ephemeral-nodes-resources.json** 的簡易版本。

只要熟悉此次部署時其他包含的檔案，就能掌握當您安排和協調複雜部署策略時，所需的一切細節和最佳作法，以此制定多重節點解決方案並採用任何技術以及利用 Azure Resource Manager 範本。在此建議一種範本檔案建構方法，請自行決定是否採用，如下圖重點標示的部分：

![datastax-template-structure](media/virtual-machines-datastax-template/datastax-template-structure.png)

基本上，這種方法會建議：

-	將您的核心範本檔案定義成所有特定部署活動的協調中心，利用範本連結功能來叫用子範本的執行。
-	建立特定的範本檔案，用來部署其他特定部署作業會共用的一切資源 (例如儲存體帳戶、vnet 設定)。如果不同的部署之間，其一般基礎結構的需求皆類似，就可以高度重複使用。
-	包含選用資源範本，用於特定資源的場地需求
-	至於資源群組中的成員相同時 (例如，叢集中的節點)，可以建立一些會利用資源迴圈功能的範本，以便部署多個屬性皆不同的執行個體。
-	所有的張貼部署工作 (例如產品安裝、設定等) 都會利用指令碼部署擴充功能以及建立每一種技術獨有的指令碼

如需詳細資訊，請參閱 [Azure Resource Manager 範本語言](https://msdn.microsoft.com/library/azure/dn835138.aspx)。

<!--HONumber=52-->
