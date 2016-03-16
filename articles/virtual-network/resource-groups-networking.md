<properties 
   pageTitle="網路資源提供者概觀 | Microsoft Azure"
   description="深入了解 Azure 資源管理員中新的網路資源提供者"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# 網路資源提供者
現今企業成功的基礎是可以使用靈活、具彈性、安全且可重複的方式來建置和管理大規模網路感知應用程式。Azure 資源管理員 (ARM) 可讓您建立這類應用程式，做為資源群組中的單一資源集合。這類資源是透過 ARM 下的各種資源提供者進行管理。

Azure 資源管理員會仰賴不同的資源提供者來提供資源的存取權。有三個主要的資源提供者：網路、儲存體和運算。本文件討論網路資源提供者的特性和優點，包括：

- **中繼資料** - 您可以使用標記新增資源的資訊。這些標記可以用來追蹤資源群組和訂用帳戶中的資源使用。
- **更進一步控制網路** - 網路資源是進行鬆散偶合，而您可以更精密地控制它們。這表示可以更彈性地管理網路資源。
- **設定更快速** - 因為網路資源是進行鬆散偶合，所以您可以平行建立並協調網路資源。這已經大幅減少設定時間。
- **角色型存取控制** - 除了允許建立自訂角色進行安全管理之外，RBAC 還提供具有特定安全性範圍的預設角色。 
- **更易於管理和部署** - 更容易部署和管理應用程式，因為您可以建立整個應用程式堆疊做為資源群組中的單一資源集合。而且可以更快速地部署，因為只要提供範本 JSON 裝載即可進行部署。
- **快速自訂** - 您可以使用宣告樣式範本進行可重複且快速自訂的部署。 
- **可重複自訂** - 您可以使用宣告樣式範本進行可重複且快速自訂的部署。
- **管理介面** - 您可以使用任何下列任一介面來管理您的資源：
	- REST 型 API
	- PowerShell
	- .NET SDK
	- Node.JS SDK
	- Java SDK
	- Azure CLI
	- 預覽入口網站
	- ARM 範本語言

## 網路資源 
您現在可以獨立地管理網路資源，而不是透過單一計算資源 (虛擬機器) 進行管理。這確保具有較高程度的彈性和靈活性可在資源群組中撰寫複雜且大型的基礎結構。

含有多介層應用程式之範例部署的概念檢視顯示如下。您看到的每項資源 (如 NIC、公用 IP 位址和 VM 等) 都可以分開管理。

![網路資源模型](./media/resource-groups-networking/Figure2.png)

每項資源均包含一組通用屬性，以及其個別的屬性集。通用屬性包括：

|屬性|說明|範例值|
|---|---|---|
|**name**|唯一的資源名稱。每個資源類型都有自己的命名限制。|PIP01、VM01、NIC01|
|**位置**|資源所在的 Azure 區域|westus、eastus|
|**id**|唯一的 URI 型識別|/subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP|

您可以在下列各節中查看資源的個別屬性。

[AZURE.INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## 管理介面
您可以使用不同的介面管理您的 Azure 網路資源。在本文件中，我們將著重在指引這些介面：REST API 和範本。

### REST API 
如前所述，網路資源可以透過各種介面進行管理，包括 REST API、.NET SDK、Node.JS SDK、Java SDK、PowerShell、CLI、Azure 入口網站和範本。

Rest API 符合 HTTP 1.1 通訊協定規格。API 的一般 URI 結構顯示如下：

	https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

而以大括弧括住的參數代表下列元素：

- **subscription-id** - Azure 訂用帳戶識別碼。
- **resource-provider-namespace** - 正在使用之提供者的命名空間。網路資源提供者的值是 *Microsoft.Network*。
- **region-name** - Azure 區域名稱

呼叫 REST API 時，支援下列 HTTP 方法：

- **PUT** - 用來建立所指定類型的資源、修改資源屬性，或變更資源之間的關聯。 
- **GET** - 用來擷取已佈建資源的資訊。
- **DELETE** - 用來刪除現有資源。

要求和回應都符合 JSON 裝載格式。如需詳細資料，請參閱 [Azure 資源管理 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)。

### ARM 範本語言
除了以命令方式 (透過 API 或 SDK) 管理資源之外，您還可以使用宣告式程式設計樣式，利用 ARM 範本語言建置和管理網路資源。

範本的範例表示法提供如下 –

	{
	  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	  "contentVersion": "<version-number-of-template>",
	  "parameters": { <parameter-definitions-of-template> },
	  "variables": { <variable-definitions-of-template> },
	  "resources": [ { <definition-of-resource-to-deploy> } ],
	  "outputs": { <output-of-template> }    
	}

範本主要是透過參數所插入之資源和執行個體值的 JSON 描述。下列範例可以用來建立具有 2 個子網路的虛擬網路。

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
	    "contentVersion": "1.0.0.0",
	    "parameters" : {
	      "location": {
	        "type": "String",
	        "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
	        "metadata" : {
	          "Description" : "Deployment location"
	        }
	      },
	      "virtualNetworkName":{
	        "type" : "string",
	        "defaultValue":"myVNET",
	        "metadata" : {
	          "Description" : "VNET name"
	        }
	      },
	      "addressPrefix":{
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/16",
	        "metadata" : {
	          "Description" : "Address prefix"
	        }
	
	      },
	      "subnet1Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-1",
	        "metadata" : {
	          "Description" : "Subnet 1 Name"
	        }
	      },
	      "subnet2Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-2",
	        "metadata" : {
	          "Description" : "Subnet 2 name"
	        }
	      },
	      "subnet1Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/24",
	        "metadata" : {
	          "Description" : "Subnet 1 Prefix"
	        }
	      },
	      "subnet2Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.1.0/24",
	        "metadata" : {
	          "Description" : "Subnet 2 Prefix"
	        }
	      }
	    },
	    "resources": [
	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Network/virtualNetworks",
	      "name": "[parameters('virtualNetworkName')]",
	      "location": "[parameters('location')]",
	      "properties": {
	        "addressSpace": {
	          "addressPrefixes": [
	            "[parameters('addressPrefix')]"
	          ]
	        },
	        "subnets": [
	          {
	            "name": "[parameters('subnet1Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet1Prefix')]"
	            }
	          },
	          {
	            "name": "[parameters('subnet2Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet2Prefix')]"
	            }
	          }
	        ]
	      }
	    }
	    ]
	}

您可以選擇在使用範本時手動提供參數值，也可以使用參數檔案。下列範例顯示一組可能的參數值以與上述範本搭配使用：

	{
	  "location": {
	      "value": "East US"
	  },
	  "virtualNetworkName": {
	      "value": "VNET1"
	  },
	  "subnet1Name": {
	      "value": "Subnet1"
	  },
	  "subnet2Name": {
	      "value": "Subnet2"
	  },
	  "addressPrefix": {
	      "value": "192.168.0.0/16"
	  },
	  "subnet1Prefix": {
	      "value": "192.168.1.0/24"
	  },
	  "subnet2Prefix": {
	      "value": "192.168.2.0/24"
	  }
	}


使用範本的主要優點如下：

- 您可以透過宣告樣式在資源群組中建置複雜的基礎結構。建立資源 (包括相依性管理) 的協調是由 ARM 所處理。 
- 只要變更參數，即可透過可重複的方式在各種區域和某個區域內建立基礎結構。 
- 宣告樣式可讓建置範本以及推出基礎結構的前置時間變短。 

如需範例範本，請參閱 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates)。

如需 ARM 範本語言的詳細資訊，請參閱 [Azure 資源管理員範本語言](../resource-group-authoring-templates.md)。

上述範例範本使用虛擬網路和子網路資源。下列是其他您可以使用的網路資源：

### 使用範本

您可以使用 PowerShell 或 AzureCLI 從範本部署服務到 Azure，或執行按一下動作來從 GitHub 部署。若要在 GitHub 中從範本部署服務，請執行下列步驟：

1. 從 GitHub 開啟 template3 檔案。在此範例中，請開啟[具有兩個子網路的虛擬網路](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network)。
2. 按一下 [部署至 Azure]，然後使用您的認證登入 Azure 入口網站。
3. 驗證範本，然後按一下 [儲存]。
4. 按一下 [編輯參數]並選取位置 (例如 [West US]) 做為 vnet 和子網路。
5. 如有必要，請變更 [ADDRESSPREFIX] 和 [SUBNETPREFIX] 參數，然後按一下 [確定]。
6. 按一下 [選取資源群組]，然後按一下您想要對其新增 vnet 和子網路的資源群組。或者，您可以按一下 [或建立新的] 來建立新的資源群組。
3. 按一下 [建立]。請注意顯示 [佈建範本部署] 的磚。完成部署之後，您將會看到類似下面所示的畫面。

![範例範本部署](./media/resource-groups-networking/Figure6.png)


## 另請參閱

[Azure 網路 API 參考](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Azure PowerShell 網路參考](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Azure 資源管理員範本語言](../resource-group-authoring-templates.md)

[Azure 網路 - 常用範本](https://github.com/Azure/azure-quickstart-templates)

[計算資源提供者](../virtual-machines-azurerm-versus-azuresm)

[Azure 資源管理員概觀](../resource-group-overview)

[Azure 資源管理員中的角色存取控制](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[在 Azure 資源管理員中使用標記](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[範本部署](https://msdn.microsoft.com/library/azure/dn790549.aspx)

現今的

<!---HONumber=AcomDC_1217_2015-->