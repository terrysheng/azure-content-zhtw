<properties 
	pageTitle="使用 Resource Manager 範本和 PowerShell 部署以及管理 Azure 虛擬機器" 
	description="使用 Resource Manager 範本和 PowerShell 輕鬆部署以及管理 Azure 虛擬機器最常用的設定。" 
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

# 使用 Azure Resource Manager 範本和 PowerShell 部署以及管理虛擬機器

本文章會教您如何利用 Azure Resource Manager 範本和 Powershell，自動化部署和管理 Azure 虛擬機器的一般工作。

開始之前，確定 Azure PowerShell 已經準備就緒。

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

## 了解 Azure 資源範本和資源群組

在 Microsoft Azure 中部署和執行的應用程式，大部分在建立時會使用不同雲端資源類型的組合 \(例如一或多個 VM 和儲存體帳戶、SQL 資料庫、虛擬網路或 CDN\)。有了 *Azure Resource Manager 範本*之後，您就可以使用 JSON 來敘述資源、相關設定和部署參數，來部署和管理不同的資源。

定義好 JSON 資源範本後，您可以使用 PowerShell 命令來執行這個範本，以及將範本中定義的資源部署到 Azure。您可以在 PowerShell 命令殼層中獨立執行以下命令，或者合併到內含其他自動化還輯的指令碼。

您使用 Azure Resource Manager 範本建立的資源，會部署到新的或現有 Azure 資源群組。*Azure 資源群組*可以讓您將多個部署的資源當做一個邏輯群組，統一進行管理。這樣您就可以管理群組/應用程式的整體週期，另外也提供一個管理 API，讓您可以：

- 一次性停止、啟動或刪除群組內的所有資源。 
- 將角色型存取控制 \(RBAC\) 規則套用至鎖定它們的安全權限。 
- 稽核作業。 
- 利用其他中繼資料標記資源，方便追蹤。 

如需深入了解 Azure Resource Manager ，請參閱[這裡](virtual-machines-azurerm-versus-azuresm.md)。

## 一般工作：部署 Windows VM

按照本節中的操作方法，使用 Resource Manager 範本和 Azure PowerShell 部署新的 Azure VM。這個範本會在單一子網路的新虛擬網路上，建立單一虛擬機器。

![](./media/virtual-machines-deploy-rmtemplates-powershell/windowsvm.png)
 
請依照下列步驟，搭配 Azure PowerShell 並使用 Github 範本儲存機制中的 Resource Manager 範本建立 Windows VM。

### 步驟 1：檢查範本的 JSON 檔案。

以下是範本的 JSON 檔案內容。

	{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "newStorageAccountName": {
            "type": "string",
            "metadata": {
                "Description": "Unique DNS Name for the Storage Account where the Virtual Machine's disks will be placed."
            }
        },
        "adminUsername": {
            "type": "string",
            "metadata": {
               "Description": "Username for the Virtual Machine."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "Description": "Password for the Virtual Machine."
            }
        },
        "dnsNameForPublicIP": {
            "type": "string",
            "metadata": {
                  "Description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "windowsOSVersion": {
            "type": "string",
            "defaultValue": "2012-R2-Datacenter",
            "allowedValues": [
                "2008-R2-SP1", 
                "2012-Datacenter", 
                "2012-R2-Datacenter", 
                "Windows-Server-Technical-Preview"
            ],
            "metadata": {
                "Description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
            }
        }
    },
    "variables": {
        "location": "West US",
        "imagePublisher": "MicrosoftWindowsServer", 
        "imageOffer": "WindowsServer", 
        "OSDiskName": "osdiskforwindowssimple",
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16", 
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "storageAccountType": "Standard_LRS",
        "publicIPAddressName": "myPublicIP",
        "publicIPAddressType": "Dynamic",
        "vmStorageAccountContainerName": "vhds",
        "vmName": "MyWindowsVM",
        "vmSize": "Standard_D1",
        "virtualNetworkName": "MyVNET",        
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
    },    
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('newStorageAccountName')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[variables('publicIPAddressName')]",
            "location": "[variables('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[variables('virtualNetworkName')]",
            "location": "[variables('location')]",
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
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[variables('nicName')]",
            "location": "[variables('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[variables('location')]",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                    "computername": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "[variables('imagePublisher')]",
                        "offer": "[variables('imageOffer')]",
                        "sku" : "[parameters('windowsOSVersion')]",
                        "version":"latest"
                    },
                   "osDisk" : {
                        "name": "osdisk",
                        "vhd": {
                            "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                        },
                        "caching": "ReadWrite",
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                }
            }
        }
    ]
	} 


### 步驟 2：使用範本建立虛擬機器。

填寫 Azure 部署名稱、資源群組名稱、Azure 資料中心位置，然後執行以下命令。

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

執行 **New-AzureResourceGroupDeployment** 命令時，會提示您提供 JSON 檔案 "parameters" 區段中的參數值。指定所有需要的參數值後，這個命令會建立資源群組和虛擬機器。

範例如下。

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

您會看到類似下列畫面：

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: newsaacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso
	VERBOSE: 10:56:59 AM - Template is valid.
	VERBOSE: 10:56:59 AM - Create template deployment 'TestDeployment'.
	VERBOSE: 10:57:08 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 10:57:11 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 10:57:11 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is running
	VERBOSE: 10:57:38 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is succeeded
	VERBOSE: 10:57:40 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 10:57:45 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 10:57:45 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:01:59 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded
	
	
	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 6:02:13 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     newsaacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

在新的資源群組中，您現在擁有新的 Windows 虛擬機器，名稱是 MyWindowsVM。

## 一般工作：建立自訂的 VM 映像

請按照本節描述的操作方法，在 Azure 中使用 Resource Manager 範本搭配 Azure PowerShell 建立自訂的 VM 映像。這個範本會從指定的虛擬硬碟 \(VHD\) 建立單一虛擬機器。

### 步驟 1：檢查範本的 JSON 檔案。

以下是範本的 JSON 檔案內容。

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	    "contentVersion": "1.0.0.0",
	    "parameters": {
	        "osDiskVhdUri": {
	            "type": "string",
	            "metadata": {
	                "Description": "Uri of the existing VHD"
	            }
	        },
	        "osType": {
	            "type": "string",
	            "allowedValues": [
	                "windows",
	                "linux"
	            ],
	            "metadata": {
	                "Description": "Type of OS on the existing vhd"
	            }
	        },
	        "location": {
	            "type": "String",
	            "defaultValue": "West US",
	            "metadata": {
	                "Description": "Location to create the VM in"
	            }
	        },
	        "vmSize": {
	            "type": "string",
	            "defaultValue": "Standard_A2",
	            "metadata": {
	                "Description": "Size of the VM"
	            }
	        },
	        "vmName": {
	            "type": "string",
	            "defaultValue": "myVM",
	            "metadata": {
	                "Description": "Name of the VM"
	            }
	        },
	        "nicName": {
	            "type": "string",
	            "defaultValue": "myNIC",
	            "metadata": {
	                "Description": "NIC to attach the new VM to"
	            }
	        }
	    },
	    "resources": [{
	        "apiVersion": "2014-12-01-preview",
	        "type": "Microsoft.Compute/virtualMachines",
	        "name": "[parameters('vmName')]",
	        "location": "[parameters('location')]",
	        "properties": {
	            "hardwareProfile": {
	                "vmSize": "[parameters('vmSize')]"
	            },
	            "storageProfile": {
	                "osDisk": {
	                    "name": "[concat(parameters('vmName'),'-osDisk')]",
	                    "osType": "[parameters('osType')]",
	                    "caching": "ReadWrite",
	                    "vhd": {
	                        "uri": "[parameters('osDiskVhdUri')]"
	                    }
	                }
	            },
	            "networkProfile": {
	                "networkInterfaces": [{
	                    "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
	                }]
	            }
	        }
	    }]
	}

### 步驟 2：取得 VHD。

若是 Windows 型虛擬機器，請參閱[建立 Windows Server VHD 並上傳至 Azure](virtual-machines-create-upload-vhd-windows-server.md)。

若是 Linux 型虛擬機器，請參閱[建立 Linux VHD 並上傳至 Azure](virtual-machines-linux-create-upload-vhd.md)。

### 步驟 3：使用範本建立虛擬機器。

若要利用 VHD 建立新的虛擬機器，請將 "\< \>" 裡面的元素換成您的特定資訊，然後執行以下命令：

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

系統會提示您輸入 JSON 檔案的 "parameters" 區段中的參數值。指定所有的參數值後，Azure Resource Manager 會建立資源群組和虛擬機器。

下列是一個範例：

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI


您會收到下列類型的資訊：

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	osDiskVhdUri: http://saacct.blob.core.windows.net/vhds/osdiskforwindows.vhd
	osType: windows
	location: West US
	vmSize: Standard_A3
	...

## 一般工作：部署一個多重 VM 應用程式，它會使用虛擬網路和外部負載平衡器

按照下列各節的操作方法，部署一個多重 VM 應用程式，它會使用 Azure PowerShell 搭配 Github 範本儲存機制中的 Resource Manager 範本，然後就可以使用虛擬網路和負載平衡器。這個範本會在新雲端服務中單一子網路的新虛擬網路中建立兩個虛擬機器，然後將它們新增至外部負載平衡集，負責 TCP 連接埠 80 的連入流量。

![](./media/virtual-machines-deploy-rmtemplates-powershell/multivmextlb.png)

按照下列步驟部署一個多重 VM 應用程式，它會利用 Azure PowerShell 命令使用 Github 範本儲存機制中的 Resource Manager 範本，然後就可以使用虛擬網路和負載平衡器。

### 步驟 1：檢查範本的 JSON 檔案。

以下是範本的 JSON 檔案內容。

	{
	"$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	    "contentVersion": "1.0.0.0",
    "parameters": {
        "region": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string",
            "defaultValue": "uniqueStorageAccountName"
        },
        "adminUsername": {
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring"
        },
        "dnsNameforLBIP": {
            "type": "string",
            "defaultValue": "uniqueDnsNameforLBIP"
        },
        "backendPort": {
            "type": "int",
            "defaultValue": 3389
        },
        "vmNamePrefix": {
            "type": "string",
            "defaultValue": "myVM"
        },
        "vmSourceImageName": {
            "type": "string",
            "defaultValue": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"
        },
        "lbName": {
            "type": "string",
            "defaultValue": "myLB"
        },
        "nicNamePrefix": {
            "type": "string",
            "defaultValue": "nic"
        },
        "publicIPAddressName": {
            "type": "string",
            "defaultValue": "myPublicIP"
        },
        "vnetName": {
            "type": "string",
            "defaultValue": "myVNET"
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_A1",
            "allowedValues": [
                "Standard_A0",
                "Standard_A1",
                "Standard_A2",
                "Standard_A3",
                "Standard_A4"
            ]
        }
    },
    "variables": {
        "storageAccountType": "Standard_LRS",
        "vmStorageAccountContainerName": "vhds",
        "availabilitySetName": "myAvSet",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet-1",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
        "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
        "numberOfInstances": 2,
        "nicId1": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 0))]",
        "nicId2": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 1))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LBFE')]",
        "backEndIPConfigID1": "[concat(variables('nicId1'),'/ipConfigurations/ipconfig1')]",
        "backEndIPConfigID2": "[concat(variables('nicId2'),'/ipConfigurations/ipconfig1')]",
        "sourceImageName": "[concat('/', subscription().subscriptionId,'/services/images/',parameters('vmSourceImageName'))]",
        "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/LBBE')]",
        "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2014-12-01-preview",
            "location": "[parameters('region')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            "apiVersion": "2014-12-01-preview",
            "location": "[parameters('region')]",
            "properties": {}
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('region')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameforLBIP')]"
                }
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('region')]",
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
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
            "location": "[parameters('region')]",
            "copy": {
                "name": "nicLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "name": "[parameters('lbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('region')]",
            "dependsOn": [
                "nicLoop",
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LBFE",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[variables('publicIPAddressID')]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "LBBE",
                        "properties": {
                            "backendIPConfigurations": [
                                {
                                    "id": "[variables('backEndIPConfigID1')]"
                                },
                                {
                                    "id": "[variables('backEndIPConfigID2')]"
                                }
                            ]
                        }
                    }
                ],
                "inboundNatRules": [
                    {
                        "name": "RDP-VM1",
                        "properties": {
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
                            "backendIPConfiguration": {
                                "id": "[variables('backEndIPConfigID1')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 50001,
                            "backendPort": 3389,
                            "enableFloatingIP": false
                        }
                    },
                    {
                        "name": "RDP-VM2",
                        "properties": {
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
                            "backendIPConfiguration": {
                                "id": "[variables('backEndIPConfigID2')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 50002,
                            "backendPort": 3389,
                            "enableFloatingIP": false
                        }
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "LBRule",
                        "properties": {
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
                            "backendAddressPool": {
                                "id": "[variables('lbPoolID')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 5,
                            "probe": {
                                "id": "[variables('lbProbeID')]"
                            }
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "tcpProbe",
                        "properties": {
                            "protocol": "tcp",
                            "port": 80,
                            "intervalInSeconds": "5",
                            "numberOfProbes": "2"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
            "copy": {
                "name": "virtualMachineLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "location": "[parameters('region')]",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
                "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
            ],
            "properties": {
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
                },
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[concat(parameters('vmNamePrefix'), copyIndex())]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('sourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/')]"
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
                        }
                    ]
                }
            }
        }
    ]
	}


### 步驟 2：使用範本建立部署。

填寫 Azure 部署名稱、資源群組名稱、Azure 位置，然後執行以下命令。

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

執行 New-AzureResourceGroupDeployment 命令時，會提示您提供 JSON 檔案的參數值。指定所有的參數值後，這個命令會建立資源群組和部署。

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

您應該看到類似這樣的結果。

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	adminUserName: WebAdmin1
	adminPassword: *******
	dnsNameforLBIP: web07
	backendPort: 80
	vmNamePrefix: WEBFARM
	...

## 移除資源群組

您可以利用 **Remove-AzureResourceGroup** 命令，移除任何您建立的資源群組。以正確的名稱取代引號中的所有內容，包括 < and > 字元。

	Remove-AzureResourceGroup  -Name "<resource group name>"

您會看到如下的資訊：

	Confirm
	Are you sure you want to remove resource group 'BuildRG'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

## 登入 Windows 虛擬機器

如需詳細步驟，請參閱[如何登入執行 Windows Server 的虛擬機器](virtual-machines-log-on-windows-server.md)。

## 顯示虛擬機器的相關資訊

您可以使用 **Get-AzureVM** 命令，查看 VM 的相關資訊。這個命令會傳回一個 VM 物件，然後您可以使用其他各種 Cmdlet 即可更新 VM 的狀態。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	Get-AzureVM –ResourceGroupName "<resource group name>" –Name "<VM name>"

即會顯示虛擬機器的相關資訊，如下所示：

	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          : {
	                             "VirtualMachineSize": "Standard_D1"
	                           }
	Id                       : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/BuildRG/providers/Microso
	                           ft.Compute/virtualMachines/MyWindowsVM
	InstanceView             : null
	Location                 : westus
	Name                     : MyWindowsVM
	NetworkProfile           : {
	                             "NetworkInterfaces": [
	                               {
	                                 "Primary": null,
	                                 "ReferenceUri": "/subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/Bu
	                           ildRG/providers/Microsoft.Network/networkInterfaces/myVMNic"
	                               }
	                             ]
	                           }
	OSProfile                : {
	                             "AdminPassword": null,
	                             "AdminUsername": "WinAdmin1",
	                             "ComputerName": "MyWindowsVM",
	                             "CustomData": null,
	                             "LinuxConfiguration": null,
	                             "Secrets": [],
	                             "WindowsConfiguration": {
	                               "AdditionalUnattendContents": [],
	                               "EnableAutomaticUpdates": true,
	                               "ProvisionVMAgent": true,
	                               "TimeZone": null,
	                               "WinRMConfiguration": null
	                             }
	                           }
	Plan                     : null
	ProvisioningState        : Succeeded
	StorageProfile           : {
	                             "DataDisks": [],
	                             "ImageReference": {
	                               "Offer": "WindowsServer",
	                               "Publisher": "MicrosoftWindowsServer",
	                               "Sku": "2012-R2-Datacenter",
	                               "Version": "latest"
	                             },
	                             "OSDisk": {
	                               "OperatingSystemType": "Windows",
	                               "Caching": "ReadWrite",
	                               "CreateOption": "FromImage",
	                               "Name": "osdisk",
	                               "SourceImage": null,
	                               "VirtualHardDisk": {
	                                 "Uri": "http://buildsaacct.blob.core.windows.net/vhds/osdiskforwindowssimple.vhd"
	                               }
	                             },
	                             "SourceImage": null
	                           }
	Tags                     : {}
	Type                     : Microsoft.Compute/virtualMachines


## 啟動虛擬機器

您可以使用 **Start-AzureVM** 命令啟動 VM。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	Start-AzureVM –ResourceGroupName "<resource group name>" –Name "<VM name>"

您會看到如下的資訊：

	EndTime             : 4/28/2015 11:11:41 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	Status              : Succeeded
	TrackingOperationId : e1705973-d266-467e-8655-920016145347
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	StatusCode          : OK

## 停止虛擬機器

您可以使用 **Stop-AzureVM** 命令，就可以停止 VM。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	Stop-AzureVM –ResourceGroupName "<resource group name>" –Name "<VM name>"

您會看到如下的資訊：

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
	
	
	EndTime             : 4/28/2015 11:09:08 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:06:55 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 0c94dc74-c553-412c-a187-108bdb29657e
	RequestId           : 5cc9ddba-0643-4b5e-82b6-287b321394ee
	StatusCode          : OK

##重新啟動虛擬機器

您可以使用 **Restart-AzureVM** 命令重新啟動 VM。以正確的名稱取代引號中的所有內容，包括 < and > 字元。

	Restart-AzureVM –ResourceGroupName "<resource group name>" –Name "<VM name>"

您會看到如下的資訊：

	EndTime             : 4/28/2015 11:16:26 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:16:25 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 390571e0-c804-43ce-88c5-f98e0feb588e
	RequestId           : 7dac33e3-0164-4a08-be33-96205284cb0b
	StatusCode          : OK

## 刪除虛擬機器

您可以使用 **Remove-AzureVM** 命令刪除 VM。以正確的名稱取代引號中的所有內容，包括 < and > 字元。使用 **– Force** 參數，略過確認提示。

	Remove-AzureVM –ResourceGroupName "<resource group name>" –Name "<VM name>"

您會看到如下的資訊：

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
	
	
	EndTime             : 4/28/2015 11:21:55 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:20:13 AM -07:00
	Status              : Succeeded
	TrackingOperationId : f74fad9e-f6bc-46ae-82b1-bfad3952aa44
	RequestId           : 6a30d2e0-63ca-43cf-975b-058631e048e7
	StatusCode          : OK

## 其他資源

[Azure Resource Manager 提供的 Azure 運算、網路和儲存提供者](virtual-machines-azurerm-versus-azuresm.md)

[Azure Resource Manager 概觀](resource-group-overview.md)

[使用 Azure Resource Manager 範本和 Azure CLI 部署和管理虛擬機器](virtual-machines-deploy-rmtemplates-azure-cli.md)

[虛擬機器文件](http://azure.microsoft.com/documentation/services/virtual-machines/)

[如何安裝和設定 Azure PowerShell](install-configure-powershell.md)


<!--HONumber=52-->
