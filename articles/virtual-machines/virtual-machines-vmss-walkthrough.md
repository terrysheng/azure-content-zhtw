<properties
	pageTitle="自動調整虛擬機器調整集 | Microsoft Azure"
	description="開始建立及管理您的第一個 Azure 虛擬機器調整集"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="guybo"/>

# 在虛擬機器調整集中自動調整機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md)。


本文可快速引導您開始建立及管理您的第一個 Azure 虛擬機器調整集 (在公用預覽期間)。

本指南假設您已熟悉如何使用 Azure 資源管理員 (ARM) 範本來部署 Azure 資源，例如虛擬機器和 VNET。若非如此，「資源」一節中的前 3 個連結會提供資源管理員的概觀。

## 什麼是 VM 調整集？為何要使用它？

VM 調整集是一組您可以用來部署和管理一組相同 VM 的 Azure 計算資源。

藉由將所有的 VM 進行相同設定，設計 VM 調整集以支援真正的自動調整 (不需要預先佈建 VM)，因而能夠更輕鬆地建置以大型計算、巨量資料、容器化工作負載，以及需要相應放大和相應縮小計算資源的任何應用程式為目標的大規模服務。調整作業會隱含地平衡分散到容錯網域和更新網域。如需 VM 調整集的簡介，請參閱最近的 [Azure 部落格通知](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview)。

## 建立和使用 VM 調整集

VM 調整集可以使用 JSON 範本和 REST 呼叫來定義和部署，如同個別的 Azure 資源管理員 VM 一樣。因此，可以使用任何標準 Azure 資源管理員部署方法，而本文件會引導您完成一些範例。

特定的 VMSS 整合領域 (例如指令碼編寫和程式設計語言中的命令式命令支援)，和完整的入口網站整合目前正在開發中，將在預覽階段發行。

## 使用 Azure 入口網站部署和管理 VM 調整集

一開始，您無法在 Azure 入口網站中從頭建立 VM 調整集。入口網站支援正在建立中，在這項工作的第一個階段中，您只能在入口網站中看到您已建立的調整集，且必須使用範本/指令碼方法來建立它們。在任何情況下，「入口網站」皆表示新的 Azure 入口網站：[https://portal.azure.com/](https://portal.azure.com/)。

不過您可以利用入口網站的功能來部署範本，進而部署任何資源，包括調整集在內。部署簡易調整集的簡單方法之一，是使用如下的連結：

_https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>_

在 Azure 快速入門 Teamplates GitHub 儲存機制中可以找到 VM 調整集的範例範本集：[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) - 尋找標題中包含 _vmss_ 的範本。

在這些 Tempaltes 的讀我檔案中，您會看到像這樣的按鈕，連結至入口網站部署功能。

若要部署調整集，請按一下該按鈕，然後在入口網站中填入所需的任何參數。附註：如果您不確定某項資源是否支援大寫或混合大小寫，一律使用小寫參數值會比較安全。

**在入口網站/資源總管中監視您的 VM 調整集資源**

調整集部署完成後，入口網站會顯示其基本檢視，但在預覽期間，一開始並不會顯示詳細內容，或允許您向下切入至 在 VM 調整集中執行的 VM。

在完整的入口網站整合完成之前，建議您使用 **Azure 資源總管** ([https://resources.azure.com](https://resources.azure.com)) 來檢視 VM 調整集。VM 調整集是 Microsoft.Compute 之下的一項資源，因此您可以展開下列連結，從這個網站加以檢視：

訂用帳戶 -> 您的訂用帳戶 -> resourceGroups -> 提供者 -> Microsoft.Compute -> virtualMachineScaleSets -> 您的 VM 調整集 -> 等等

## 使用 PowerShell 部署和管理 VM 調整集

您可以使用任何目前的 Azure PowerShell 版本，來部署 VMSS 範本和查詢資源。

**重要事項：**顯示於下方的範例適用於 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/)，其中導入許多命令的 _AzureRm_ 前置詞。如果您使用舊版的 PowerShell (例如 0.9.8 或更舊的版本)，請從範例命令中移除 "**Rm**"。這些範例也假設您已在 PowerShell 環境中建立 Azure 的登入連線 (_Login-AzureRmAccount_)。

範例：

&#35; **建立資源群組**

New-AzureRmResourcegroup -name myrg -location 'Southeast Asia'

&#35; **建立 2 個 VM 的 VM 調整集**

New-AzureRmResourceGroupDeployment -name dep1 -vmSSName myvmss -instanceCount 3 -ResourceGroupName myrg -TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

若有任何遺漏的參數 (在此範例中為位置、使用者名稱、密碼)，系統會提示您。

&#35; **取得 VM 調整集詳細資料**

Get-AzureRmResource -name myvmss -ResourceGroupName myrg -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

&#35; 或者，若要較為詳盡，請透過 | ConvertTo-Json -Depth 10 進行管道處理

&#35; 或者，若要更加詳盡，請在您的命令中新增 –debug。

&#35; 相應放大或相應縮小

New-AzureRmResourceGroupDeployment -name dep2 -vmSSName myvmss -instanceCount 2 -ResourceGroupName myrg –TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json)

&#35; **移除調整集**

&#35; 簡易：移除整個資源群組及其中的所有項目：

Remove-AzureRmResourceGroup -Name myrg

&#35; 精確：移除特定資源：

Remove-AzureRmResource -Name myvmss -ResourceGroupName myrg -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

### 調整集的命令式 PowerShell 命令

即將推出的 Azure PowerShell 組建將包含一組命令式命令，可直接用來建立及管理 VM 調整集，而無須使用範本。此組建可在預覽中取得：[https://github.com/AzureRT/azure-powershell/releases](https://github.com/AzureRT/azure-powershell/releases)。使用命令式 API 的範例可以在這裡找到：

[https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1](https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1)

## 使用 Azure CLI 部署和管理 VM 調整集

您可以使用任何目前的 Azure CLI 版本，來部署 VMSS 範本和查詢資源。

要安裝 CLI，最簡單的方法是從 Docker 容器安裝。如需安裝資訊，請參閱：[https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/](https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/)

如需 CLI 的使用資訊，請參閱：[https://azure.microsoft.com/documentation/articles/xplat-cli/](https://azure.microsoft.com/documentation/articles/xplat-cli/)

### VM 調整集 CLI 範例

&#35; **建立資源群組**

azure group create myrg "Southeast Asia"

&#35; **建立 VM 調整集**

azure group deployment create -g myrg -n dep2 --template-uri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

&#35; 此作業應會動態要求參數，或者，您可以將其指定為引數

&#35; **取得調整集詳細資料**

azure resource show -n vmssname -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g myrg

&#35; 或者，如需詳細資訊：

azure resource show –n vmssname –r Microsoft.Compute/virtualMachineScaleSets –o 2015-06-15 –g myrg –json –vv

即將推出的 Azure CLI 組建將會包含命令式命令，可直接用來部署和管理 VM 調整集而無需使用範本，以及在 VM 調整集中的 VM 上執行作業。您可以在這裡找到此組建：[https://github.com/AzureRT/azure-xplat-cli/releases/](https://github.com/AzureRT/azure-xplat-cli/releases/)

## VM 調整集範本

本節將逐步解說一個簡單的 Azure 範本，用以建立 VM 調整集，並將其與用來建立單一執行個體虛擬機器的範本對照。此外，此處也提供方便觀看的 VM 調整集範本視訊剖析：[https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

### 範本的剖析

首先，我們從建立 Ubuntu 虛擬機器之 VM 調整集的簡易範本開始，然後再將其細分為元件。此範例也會建立 VM 調整集相依的資源，例如 VNET、儲存體帳戶等。此範例位於下列位置：[https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json](https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json) - 請注意，在此 "hello world" 範例中，並沒有直接的方式可以從您的 VNET 以外連接到 VM 調整集中的 VM，因為 VM 被指派外部 IP 位址。請參閱「案例」一節和 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates)中的範例，以了解如何使用負載平衡器或「跳躍箱」從外部連接。

### 範本標頭。

指定結構描述和內容版本：

{

   "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json",

   "contentVersion": "1.0.0.0",

### 參數

如同任何 Azure 資源管理員 (ARM) 範本，本節會定義在部署期間指定的參數，在此範例中包括 VM 調整集的名稱、開始的 VM 執行個體數目、建立儲存體帳戶時所使用的唯一字串 (為儲存體帳戶之類的物件輸入值時請一律使用小寫，除非您知道正確的大小寫)。

````
 "parameters": {

    "vmSSName": {

      "type": "string",

      "metadata": {

        "description": "Scale Set name, also used in this template as a base for naming resources (hence limited less than 10 characters)."

      },

      "maxLength": 10

    },

    "instanceCount": {

      "type": "int",

      "metadata": {

        "description": "Number of VM instances"

      },

      "maxValue": 100

    },

    "adminUsername": {

      "type": "string",

      "metadata": {

        "description": "Admin username on all VMs."

      }

    },

    "adminPassword": {

      "type": "securestring",

      "metadata": {

        "description": "Admin password on all VMs."

      }

    }

  },
````

### 變數

一個標準 ARM 範本元件，會定義您後續在範本中參考的變數。在此範例中，我們將使用變數來定義我們要的 OS、某些網路組態詳細資料、儲存體設定和位置。針對位置我們將使用 _location()_，從其部署所在的資源群組中加以挑選。

請注意，對於儲存體帳戶前置詞，會定義唯一字串的陣列。如需相關說明，請參閱「儲存體」一節。

````
  "variables": {

    "apiVersion": "2015-06-15",

    "newStorageAccountSuffix": "[concat(parameters('vmssName'), 'sa')]",

    "uniqueStringArray": [

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '0')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '1')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '2')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '3')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '4')))]"

    ],

    "vmSize": "Standard\_A1",

    "vhdContainerName": "[concat(parameters('vmssName'), 'vhd')]",

    "osDiskName": "[concat(parameters('vmssName'), 'osdisk')]",

    "virtualNetworkName": "[concat(parameters('vmssName'), 'vnet')]",

    "subnetName": "[concat(parameters('vmssName'), 'subnet')]",

    "nicName": "[concat(parameters('vmssName'), 'nic')]",

    "ipConfigName": "[concat(parameters('vmssName'), 'ipconfig')]",

    "addressPrefix": "10.0.0.0/16",

    "subnetPrefix": "10.0.0.0/24",

    "storageAccountType": "Standard\_LRS",

    "location": "[resourceGroup().location]",

    "osType": {

      "publisher": "Canonical",

      "offer": "UbuntuServer",

      "sku": "15.10",

      "version": "latest"

    },

    "imageReference": "[variables('osType')]"

  },
````

### 資源

這一節會定義每個資源類型。

````
   "resources": [
````


**儲存體。** 在建立 VM 調整集時，您會指定儲存體帳戶的陣列。接著，VM 執行個體的 OS 磁碟會平均分散到每個帳戶之間。在未來的 VM 調整集中會改為使用受管理的磁碟方法，屆時您將無須管理儲存體帳戶，而只須在調整集定義中說明儲存體屬性即可。目前，您必須事先建立所需數量的儲存體帳戶。建議您建立 5 個儲存體帳戶，這將足以在調整集中支援高達 100 個 VM (目前的最大值)。

一組儲存體帳戶建立時，將會散發到儲存體戳記中的多個資料分割，而散發配置取決於儲存體帳戶名稱的第一個字母。因此，為達到最佳效能，建議您使用不同的字母建立每個儲存體帳戶。您可以手動命名，或如同此範例使用 uniqueString() 函式提供的虛擬隨機分佈：

````
    {

      "type": "Microsoft.Storage/storageAccounts",

      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "copy": {

        "name": "storageLoop",

        "count": 5

      },

      "properties": {

        "accountType": "[variables('storageAccountType')]"

      }

    },
````

**虛擬網路。** 建立 VNet。請注意，在相同的 VNET 中，您可以有多個調整集以及單一 VM。

````
    {

      "type": "Microsoft.Network/virtualNetworks",

      "name": "[variables('virtualNetworkName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

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
````

### virtualMachineScaleSets 資源

_virtualMachineScaleSets_ 資源在許多方面都很類似 _virtualMachines_ 資源。兩者都是由 Microsoft.Compute 資源提供者所提供，且位於相同層級。主要差異在於，您須提供 _capacity_ 值指出您建立的 VM 數量，以及您在此處的定義是用於 VM 調整集中的所有 VM 的。

請注意 _dependsOn_ 區段如何參考先前建立的儲存體帳戶和 VNET，以及 capacity 如何參考 _instanceCount_ 參數。

````
    {

      "type": "Microsoft.Compute/virtualMachineScaleSets",

      "name": "[parameters('vmssName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "dependsOn": [

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"

      ],

      "sku": {

        "name": "[variables('vmSize')]",

        "tier": "Standard",

        "capacity": "[parameters('instanceCount')]"

      },
````

**屬性**

VM 調整集具有 upgradePolicy 設定。未來的版本將支援配量更新 (例如，一次對 VM 的 20% 進行組態變更)，但目前 upgradePolicy只能設定為手動或自動。「手動」表示如果您變更範本並加以重新部署，必須等到建立新的 VM 或更新延伸模組時變更才會生效，例如，當您增加 _capacity_ 時。「自動」表示會立即更新所有的 VM，並重新啟動所有項目。一般而言，手動是比較安全的方法。您可以刪除個別的 VM，並視需要重新部署，而逐漸更新。

````
      "properties": {

         "upgradePolicy": {

         "mode": "Manual"

        },
````

**Properties->virtualMachineProfile**

在此您可以參考先前為 VM 建立做為容器的儲存體帳戶。您不需要預先建立實際容器，只需帳戶即可。

````
        "virtualMachineProfile": {

          "storageProfile": {

            "osDisk": {

              "vhdContainers": [

                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"

              ],

              "name": "[variables('osDiskName')]",

              "caching": "ReadOnly",

              "createOption": "FromImage"

            },

            "imageReference": "[variables('imageReference')]"

          },
````

**Properties->osProfile**

VM 調整集與個別 VM 有一項差異，即前者會以電腦名稱作為前置詞。VM 調整集中的 VM 執行個體則會以如下的名稱建立：myvm\_0, myvm\_1 etc。

````
          "osProfile": {

            "computerNamePrefix": "[parameters('vmSSName')]",

            "adminUsername": "[parameters('adminUsername')]",

            "adminPassword": "[parameters('adminPassword')]"

          },
````

**Properties->networkProfile**

在定義 VMSS 的網路設定檔時請記住，NIC 組態和 IP 組態都有其名稱。NIC 組態具有「_主要_」設定，而子網路識別碼會往回參考先前建立為 VNET 一部分的子網路資源。

````
          "networkProfile": {

            "networkInterfaceConfigurations": [

              {

                "name": "[variables('nicName')]",

                "properties": {

                  "primary": "true",

                  "ipConfigurations": [

                    {

                      "name": "[variables('ipConfigName')]",

                      "properties": {

                        "subnet": {

                          "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'), '/subnets/', variables('subnetName'))]"

                        }

                      }

                    }

                  ]

                }

              }

            ]

          }
````

**Properties->extensionProfile**

上面的簡易範例不需要延伸模組設定檔。在下列範本中，您可以看到一個設定檔使用 CustomScript 延伸模組來部署 Apache 和 PHP：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) - 請注意，在此範例中，網路 IP 屬性也會參考負載平衡器。負載平衡器將在「VM 調整集案例」一節中詳加說明。

## VM 調整集案例

本節將說明一些典型的 VM 調整集案例和範例範本。雖然有些案例目前需要範本，但其中有部分在未來將會整合到入口網站中。此外，某些較高階的 Azure 服務 (例如 Batch、Service Fabric、Azure 容器服務) 也會使用這些案例

## 對 VM 調整集執行個體的 RDP/SSH

VM 調整集會建立在 VNET 內，且不會為其中的個別 VM 配置公用 IP 位址。這是件好事，因為您通常不希望計算方格中，為了要將個別 IP 位址配置給所有的無狀態資源，而產生支出與管理負擔，而且您可以輕鬆地從 VNET 中的其他資源連接到這些 VM，包括具有公用 IP 位址的資源，像是負載平衡器或獨立虛擬機器。

以下說明兩種連接到 VMSS VM 的簡單方式：

### 使用 NAT 規則連接到 VM

您可以建立一個公用 IP 位址、將它指派給負載平衡器，然後定義將 IP 位址的連接埠對應至 VM 調整集內某個 VM 之連接埠的輸入 NAT 規則。例如

Public IP->Port 50000 -> vmss\_0->Port 22 Public IP->Port 50001 -> vmss\_1->Port 22 Public IP->Port 50002-> vmss\_2->Port 22

以下範例會建立一個使用 NAT 規則的 VM 調整集，以便能使用單一公用 IP，對調整集內的每個 VM 進行 SSH 連線：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

以下是使用 RDP 和 Windows 執行相同作業的範例：[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

### 使用「跳躍箱」連接到 VM

如果您在相同的 VNET 中建立 VM 調整集和獨立 VM，獨立 VM 與 VMSS VM 將可使用 VNET/子網路為它們定義的內部 IP 位址彼此連接。

如果您建立公用 IP 位址，並將它指派給獨立 VM，您可以透過 RDP 或 SSH 連接到獨立 VM，然後從該部機器連接到 VMSS 執行個體。此時您可能會發現，簡易 VM 調整集在本質上比在預設組態中設定了公用 IP 位址的獨立 VM 來得安全。

為了提供此方法的範例，此範本建立了一個簡易 Mesos 叢集，其中包含一個獨立的主要 VM，用來管理 VM 的 VM 調整集架構叢集：[https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

## 對 VM 調整集執行個體的循環配置資源負載平衡

如果您想要使用「循環配置資源」方法將工作傳送到 VM 的計算叢集，您可以使用負載平衡規則據以設定 Azure 負載平衡器。您也可以定義探查，藉由使用指定的通訊協定、間隔和要求路徑對連接埠執行 ping，以驗證您的應用程式正在執行中。

以下範例會為執行 IIS Web 伺服器的 VM 建立一個 VM 調整集，並使用負載平衡器來平衡每個 VM 獲得的負載。此外也會使用 HTTP 通訊協定對每個 VM 的特定 URL 執行 ping：[https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) - 請查看 Microsoft.Network/loadBalancers 資源類型，以及 virtualMachineScaleSet 中的 networkProfile 和 extensionProfile。

## 具有 Azure 自動調整功能的 VM 調整集執行個體

如果您想要根據 CPU/記憶體/磁碟使用量或其他事件來變更 VM 調整集的執行個體計數 (_capacity_)，Microsoft.Insights 資源提供者有豐富的自動調整設定可供您使用。您可以閱讀 Insights REST 文件中有關於可用設定的資訊：[https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx)。

「Insights 自動調整」可直接與 VM 調整集整合。若要加以設定，您必須定義具有會往回參考調整集的 _targetResourceUri_ 和 _metricResourceUri_ 的 Microsoft.Insights/autoscaleSettings 資源類型。您在定義調整集時會納入一個 _extensionProfile_，此設定檔會在 Windows 上安裝 Azure 診斷代理程式 (WAD)，或在 Linux 上安裝 Linux Diagnostic Extension (LDE)。以下 Linux 範例會在平均 CPU 使用量大於 50% 時，將 VM 新增至預先定義的上限數量 (時間精細度為取樣期間 5 分鐘內的 1 分鐘)：

[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale)。

在未來，VM 調整集的自動調整功能也將直接整合到 Azure 入口網站中。

## 在 PaaS 叢集管理員中，將 VM 調整集部署為計算叢集

VM 調整集有時會被稱為下一代背景工作角色。雖然這可說是有效的說明，但也可能導致調整集功能與 PaaS v1 背景工作角色功能令人混淆。

就某方面來說，VM 調整集提供了真正的「背景工作角色」或背景工作資源，因為它們提供了不會隨平台/執行階段而改變、可自訂且整合至 Azure 資源管理員 IaaS 的一般化計算資源。

PaaS v1 背景工作角色雖然在平台/執行階段支援方面有所限制 (僅限 Windows 平台映像)，但也包含像是 VIP 交換、可設定的升級設定、執行階段/應用程式部署的特定設定等服務，但這些服務_尚未_成為 VM 調整集內的可用項目，或將在其他較高階的 PaaS 服務 (如 Service Fabric) 中提供。了解這一點之後，您可以將 VM 調整集視為支援 PaaS 的基礎結構。亦即PaaS 解決方案 (如 Service Fabric) 或叢集管理員 (如 Mesos) 可以建置在 VM 調整集之上，作為可調整的計算層。

以下 Mesos 叢集範例會在與獨立 VM 相同的 VNET 中部署 VM 調整集。獨立 VM 是 Mesos 主機，而 VM 調整集則代表一組從屬節點：[https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)。未來的 [Azure 容器服務](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/)版本將根據 VM 調整集，為此案例部署更複雜/強化的版本。

## 相應放大和相應縮小 VM 調整集

若要增加或減少 VM 調整集內的虛擬機器數目，請直接變更 _capacity_ 屬性，並重新部署範本。這樣的單純性，可讓您在想要定義不受 Azure 自動調整支援的自訂調整事件時，能更輕易地撰寫您的自訂調整層。

如果您想要重新部署範本以變更容量，您可以定義小得多的範本，使其僅包含 SKU 和更新的容量。其範例顯示如下：[https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)。

## VM 調整集的效能和調整指南

- 在公用預覽期間，請勿同時在多個 VM 調整集內建立 500 個以上的 VM。此限制在未來將會放寬。
- 請勿為每個儲存體帳戶規劃超過 40 個 VM。
- 盡可能分散儲存體帳戶名稱的第一個字母。[Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/)中的範例 VMSS 範本提供如何執行這項操作的範例。
- 如果使用自訂 VM，在單一儲存體帳戶中，請勿為每個 VM 調整集規劃超過 40 個 VM。您必須將映像預先複製到儲存體帳戶中，才能開始進行 VM 調整集部署。如需詳細資訊，請參閱常見問題集。
- 請勿為每個 VNET 規劃超過 2048 個 VM。此限制在未來將會放寬。
- 在任何區域中，您可以建立的 VM 數目都會受到計算核心配額的限制。您可能需要連絡客戶支援人員，以提高您的計算配額限制，即使您目前用於雲端服務或 IaaS v1 的核心已具有較高的上限亦然。若要查詢您的配額，您可以執行下列 Azure CLI 命令：_azure vm list-usage_ 和下列 PowerShell 命令：_Get-AzureRmVMUsage_ (如果您使用 1.0 以下的 PowerShell 版本，請使用 _Get-AzureVMUsage_)。


## VM 調整集常見問題集

**問：您在 VM 調整集內可以有多少個 VM？**

答：如果您使用可以分散到多個儲存體帳戶的平台映像，則是 100 個。如果您使用自訂映像，則最多是 40 個，因為自訂映像在預覽期間受限於單一儲存體帳戶。

**VM 調整集有哪些其他資源的限制？**

答：現有的 Azure 服務限制包括：[https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/)

在預覽期間，也會有在多個調整集內為每個區域建立的 VM 不可超過 500 個的限制。

**問：在 VM 調整集內是否支援資料磁碟？**

答：在初始版本中不支援。您的資料儲存選項包括：

- Azure 檔案 (SMB 共用磁碟機)

- OS 磁碟機

- 暫存磁碟機 (本機，未受 Azure 儲存體支援)

- 外部資料服務 (例如遠端 DB、Azure 資料表、Azure blob)

**問：哪些 Azure 區域支援 VMSS？**

答：任何支援 Azure 資源管理員的區域，都支援 VM 調整集。

**問：要如何使用自訂映像建立 VMSS？**

答：將 vhdContainers 屬性保留為空白，例如：

````
"storageProfile": {
   "osDisk": {
      "name": "vmssosdisk",
      "caching": "ReadOnly",
      "createOption": "FromImage",
      "image": {
         "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
     },
     "osType": "Windows"
  }
},
````

**問：如果我將 VMSS 容量從 20 減少為 15，VM 會被移除嗎？**

答：最後 5 個 VM (最大索引鍵) 會被移除。

**問：如果我後續又將容量從 15 增加到 18 呢？**

如果增加到 18，將會建立具有索引鍵 15、16、17 的 VM。在這兩種情況下，VM 都會分散到 FD 和 UD 進行平衡。

**問：在一個 VM 調整集內使用多個延伸模組時，是否可以強制執行「執行順序」？**

答：並非直接，但對於 customScript 延伸模組，您的指令碼可以等候另一個延伸模組完成 (例如，藉由監視延伸模組記錄檔 - 請參閱 [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh))。

**問：VM 調整集是否可與 Azure 可用性設定組搭配使用？**

答：是。調整集是隱含的可用性設定組，具有 3 個 FD 和 5 個 UD。您不需要在 virtualMachineProfile 下進行任何設定。在未來的版本中，調整集有可能跨越多個租用戶，但目前調整集只是單一可用性設定組。

## 後續步驟

在 VM 調整集的預覽期間，文件將會持續修訂，並且將會開啟更多整合功能，例如入口網站和自動調整。

您的意見反應非常重要，將協助我們建置服務以提供您所需要的功能。請讓我們知道哪些功能可行、哪些不可行，以及哪些需要改進。您可以將意見反應提供到 [vmssfeedback@microsoft.com](mailto:vmssfeedback@microsoft.com)。

## 資源

| **主題** | **連結** |
| --- | --- |
| Azure 資源管理員概觀 | [https://azure.microsoft.com/documentation/articles/resource-group-overview/](https://azure.microsoft.com/documentation/articles/resource-group-overview/) |
| Azure 資源管理員範本 | [https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) |
| Azure 資源管理員範本函數 | [https://azure.microsoft.com/documentation/articles/resource-group-template-functions/](https://azure.microsoft.com/documentation/articles/resource-group-template-functions/) |
| 範例範本 (github) | [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) |
| VM 調整集 REST API 指南 | [https://msdn.microsoft.com/library/mt589023.aspx](https://msdn.microsoft.com/library/mt589023.aspx) |
| VM 調整集影片 | [https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) [https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player) |
| 自動調整設定 (MSDN) | [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx) |
| 在 Azure 中疑難排解資源群組部署 | [https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/](https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/) |

<!---HONumber=Nov15_HO3-->