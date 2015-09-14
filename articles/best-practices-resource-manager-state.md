<properties
	pageTitle="在「Azure 資源管理員」範本中處理狀態的最佳做法"
	description="示範透過「Azure 資源管理員」範本與連結的範本，使用複雜物件來共用狀態資料的建議做法"
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2015"
	ms.author="mmercuri"/>

# Azure 資源管理員範本中的共用狀態

此主題示範在「Azure 資源管理員」範本內以及跨連結的範本，管理及共用狀態的最佳做法。本主題所顯示的參數與變數為您可以定義的物件類型範例，方便您用來組織部署需求。在這些範例中，您可以實作自己的物件與您環境適用的屬性值。

## 使用複雜物件來共用狀態

除了單一值參數，您可以在「Azure 資源管理員」範本中使用複雜物件做為參數。利用複雜物件，您可以為特定區域實作和參考資料集合，例如 T 恤大小 (用於描述虛擬機器)、網路設定、作業系統 (OS) 設定，以及可用性設定。

下列範例將顯示如何定義包含複雜物件以代表資料集合的變數。此集合定義的值用於虛擬機器大小、網路設定、作業系統設定，以及可用性設定。

    "tshirtSizeLarge": {
      "vmSize": "Standard_A4",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
      "vmCount": 3,
      "slaveCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1,1],
        "jumpbox": 0
      }
    },
    "osSettings": {
      "scripts": [
        "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
      ],
      "imageReference": {
				"publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    },
    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    },
    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

您稍後可以在範本中參考這些變數。參考具名變數和其屬性的能力可簡化範本語法，而且可以讓您更容易了解內容。下列範例使用如上所示的物件來設定值，以定義要部署的資源。例如，請注意 VM 大小是透過擷取 `variables('tshirtSize').vmSize` 的值來設定，而磁碟大小的值則是擷取自 `variables('tshirtSize').diskSize`。此外，連結的範本之 URI 是使用 `variables('tshirtSize').vmTemplate` 的值所設定。

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
	    "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## 將狀態傳遞到範本和與其連結的範本

您可以透過下列方式，將狀態資訊共用到範本和與其連結的範本：

- 在部署期間直接提供給主要範本的參數
- 主要範本和連結的範本共用的參數、靜態變數，和產生的變數

### 提供給主要範本的通用參數

下表列出在範本中的常用參數。

**傳遞到主要範本的常用參數**

名稱 | 值 | 說明
---- | ----- | -----------
location | 來自 Azure 區域之條件約束清單的字串 | 將部署資源的位置。
storageAccountNamePrefix | String | 將放置 VM 磁碟之儲存體帳戶的唯一 DNS 名稱
domainName | String | 可公開存取的 JumpBox VM 網域名稱 (格式：**{domainName}.{location}.cloudapp.com**) 例如：**mydomainname.westus.cloudapp.azure.com**
adminUsername | String | VM 的使用者名稱
adminPassword | String | VM 的密碼
tshirtSize | 來自提供 T 恤大小之條件約束清單的字串 | 要佈建的具名縮放單位大小。例如，"Small"、"Medium"、"Large"
virtualNetworkName | String | 取用者想使用的虛擬網路名稱。
enableJumpbox | 來自條件約束清單的字串 (enabled/disabled) | 識別是否要為環境啟用 JumpBox 的參數。值："enabled"、"disabled"

### 傳送到連結的範本之參數

當連線到連結的範本時，您將經常混合使用靜態變數與產生的變數。

#### 靜態變數

靜態變數通常用於提供基底值，例如在範本中全程使用的 URL，或用來組合動態變數值的值。

在下面的範本摘要中，*templateBaseUrl* 指定範本在 GitHub 中的根位置。下一行會建置新的變數 *sharedTemplateUrl*，它會串連 *templateBaseUrl* 的值與共用資源範本的已知名稱。接下來，複雜物件變數用來儲存 T 恤大小，其中會串連 *templateBaseUrl* 以指定 *vmTemplate* 屬性中儲存的已知組態範本位置。

這個方法的好處是您可以輕鬆地移動、分岔，或使用範本做為新範本的基礎。如果範本位置變更，您只需要在一個地方 (亦即主要範本) 變更靜態變數，這樣就可以將變更傳遞到所有範本。

    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "tshirtSizeSmall": {
      "vmSize": "Standard_A1",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
      "vmCount": 2,
      "slaveCount": 1,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 1,
        "pool": "db",
        "map": [0,0],
        "jumpbox": 0
      }
    }

#### 產生的變數

除了靜態變數，有一些變數是動態產生的。本節將說明一些產生的變數的常見類型。

##### tshirtSize

當呼叫主要範本時，您可以從固定數目的選項中選擇 T 恤大小，通常會包含像是 *Small*、*Medium* 和 *Large* 的值。

在主要範本中，這個選項顯示為參數，例如 *tshirtSize*：

    "tshirtSize": {
      "type": "string",
      "defaultValue": "Small",
      "allowedValues": [
        "Small",
        "Medium",
        "Large"
      ],
      "metadata": {
        "Description": "T-shirt size of the MongoDB deployment"
      }
    }

在主要範本內部，變數對應到每個大小。例如，如果可用的大小是 Small、Medium 和 Large，變數區段將會包含名為 *tshirtSizeSmall*、*tshirtSizeMedium* 和 *tshirtSizeLarge* 的變數。

如以下範例所示，這些變數會定義特定 T 恤大小的屬性。每個變數都會識別 VM 類型、磁碟大小、要做為連結目標的關聯縮放單位資源範本、執行個體數目、儲存體帳戶詳細資訊，和 JumpBox 狀態。

儲存體帳戶名稱前置詞取自使用者提供的參數，且連結的範本是由範本的基底 URL 及特定縮放單位資源範本的檔案名稱串連。

    "tshirtSizeSmall": {
      "vmSize": "Standard_A1",
			"diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
      "vmCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 1,
        "pool": "db",
        "map": [0,0],
        "jumpbox": 0
      }
    },
    "tshirtSizeMedium": {
      "vmSize": "Standard_A3",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
      "vmCount": 2,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1],
        "jumpbox": 0
      }
    },
    "tshirtSizeLarge": {
      "vmSize": "Standard_A4",
      "diskSize": 1023,
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
      "vmCount": 3,
      "storage": {
        "name": "[parameters('storageAccountNamePrefix')]",
        "count": 2,
        "pool": "db",
        "map": [0,1,1],
        "jumpbox": 0
      }
    }

*tshirtSize* 變數出現在變數區段的更下方。您提供的 T 恤大小 (*Small*、*Medium* 和 *Large*) 結尾會與文字 *tshirtSize* 串連，來為該 T 恤大小擷取關聯的複雜物件變數：

    "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",

此變數將傳遞到連結的縮放單位資源範本。

##### networkSettings

在容量、功能或端對端範圍的解決方案範本中，連結的範本通常會建立存在於網路上的資源。一個直接的方法是使用複雜物件來儲存網路設定，並將其傳送到連結的範本。

通訊網路設定的範例如下所示。

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

##### availabilitySettings

連結的範本中所建立的資源通常會放置在可用性集合中。在下列範例中，已指定可用性集合名稱，以及要使用的容錯網域和更新網域計數。

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

如果您需要多個可用性集合 (例如，一個用於主要節點，其他用於資料節點)，您可以使用名稱做為前置詞，指定多個可用性集合，或依照稍早所示的模型建立特定 T 恤大小的變數。

##### storageSettings

儲存體詳細資料通常會與連結的範本共用。在下面的範例中，*storageSettings* 物件提供有關儲存體帳戶和容器名稱的詳細資料。

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

##### osSettings

利用連結的範本，您可能需要傳遞作業系統設定到各種節點類型，並橫跨不同的已知組態類型。複雜物件是一種用來儲存及共用作業系統資訊的簡單方式，也讓支援多個作業系統部署的選擇變得更輕鬆。

下列範例顯示 *osSettings* 的物件：

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

##### machineSettings

一個產生的變數，*machineSettings* 是複雜物件，包含用來建立新 VM 的混合核心變數：系統管理員使用者名稱與密碼、VM 名稱的前置詞，以及作業系統映像參考，如下所示：

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

請注意，*osImageReference* 從 *osSettings* 變數 (定義於主要範本) 擷取值。這表示您可以輕鬆地變更 VM 的作業系統—完全地或根據範本取用者的喜好設定。

##### vmScripts

*vmScripts* 物件包含有關在 VM 執行個體上下載及執行的指令碼詳細資料，包括外部和內部參考。外部參考包含基礎結構。內部參考包含已安裝的軟體和組態。

使用 *scriptsToDownload* 屬性列出要下載到 VM的指令碼。

如以下範例所示，此物件也包含對不同動作類型之命令列引數的參考。這些動作包括為每個個別節點執行預設安裝、部署所有節點後所執行的安裝，以及可能為給定範本指定的任何其他指令碼。

這個範例來自用於部署 MongoDB 的範本，需要有仲裁程式以提供高可用性。*arbiterNodeInstallCommand* 已新增到 *vmScripts* 以安裝仲裁程式。

您可以在變數區段中找到定義特定文字以搭配適當的值執行指令碼的變數。

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## 從範本傳回狀態

您不只可以將資料傳遞到範本中，也可以與發出呼叫的範本共用資料。在連結的範本 **outputs** 區段中，您可以提供可由來源範本取用的機碼值組。

下列範例顯示如何傳遞在連結的範本中產生的私人 IP 位址。

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].privateIPAddress]",
            "type": "string"
         }
    }

在主要範本中，您可以透過下列語法使用該資料：

    "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]"
    }

## 後續步驟
- [編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)
- [Azure 資源管理員範本函數](resource-group-template-functions.md)

<!---HONumber=September15_HO1-->