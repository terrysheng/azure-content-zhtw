
<properties 
   pageTitle="使用 Azure 資源管理員範本建立應用程式閘道 | Microsoft Azure"
   description="本頁面提供使用 Azure 資源管理員範本，建立 Azure 應用程式閘道的指示。"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/10/2015"
   ms.author="joaoma"/>


# 使用 ARM 範本建立應用程式閘道

應用程式閘道是第 7 層負載平衡器。它提供在不同伺服器之間進行容錯移轉、效能路由傳送 HTTP 要求，而不論它們是在雲端或內部部署中。應用程式閘道具有下列應用程式傳遞功能：HTTP 負載平衡、以 Cookie 為基礎的工作階段同質性、SSL 卸載。

> [AZURE.SELECTOR]
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template](application-gateway-create-gateway-arm-template.md)

<BR>

您將了解如何從 GitHub 下載和修改現有 ARM 範本，並從 GitHub、PowerShell 和 Azure CLI 部署範本。

如果您只需直接從 GitHub 部署 ARM 範本而不做任何變更，請跳至＜從 GitHub 部署範本＞。


>[AZURE.IMPORTANT]使用 Azure 資源之前，請務必了解 Azure 目前有「資源管理員」和「傳統」兩種部署模型。使用任何 Azure 資源之前，請先確認您了解[部署模型和工具](azure-classic-rm.md)。您可以按一下本文頂端的索引標籤，檢視不同工具的文件。本文件將說明使用 Azure 資源管理員建立應用程式閘道的方式。若要使用傳統的版本，請移至[使用 PowerShell 建立應用程式閘道傳統部署](application-gateway-create-gateway.md)。




## 案例

在此案例中，您將建立下列項目：

- 含有 2 個執行個體的應用程式閘道；
- 名為 VirtualNetwork1 且含有 10.0.0.0/16 保留 CIDR 區塊的 Vnet；
- 名為 Appgatewaysubnet 且使用 10.0.0.0/28 做為其 CIDR 區塊的子網路；
- 安裝程式 2 先前已針對您想要用來為流量進行負載平衡的 Web 伺服器設定後端 IP。在此範本範例中，所使用的後端 IP 將會是 10.0.1.10 和 10.0.1.11

>[AZURE.NOTE]這些都是適用於此範本的參數。您可以變更規則、接聽程式，以及開啟 azuredeploy.json 來自訂範本的 SSL。



![arm-scenario](./media/application-gateway-create-gateway-arm-template/scenario-arm.png)



## 下載 ARM 範本並了解使用方式

您可以下載現有的 ARM 範本，以透過 Github 建立 VNet 和兩個子網路，然後進行任何需要的變更，並重複使用該範本。若要這樣做，請依照下列步驟執行。

1. 瀏覽至 https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-create-application-gateway/
2. 依序按一下 [azuredeploy.json] 和 [RAW]。
3. 將檔案儲存至您電腦上的本機資料夾。
4. 如果您熟悉 ARM 範本的使用方式，請跳至步驟 7。
5. 開啟您剛儲存的檔案，並查看第 5 行中 **parameters** 下方的內容。ARM 範本的參數提供值的預留位置，可以在部署期間填寫。

	| 參數 | 說明 |
	|---|---|
	| **位置** | 將建立應用程式閘道的 Azure 區域 |
	| **VirtualNetwork1** | 新 VNet 的名稱 |
	| **addressPrefix** | 以 CIDR 格式表示的 VNet 位址空間 |
	| **ApplicationGatewaysubnet** | 適用於應用程式閘道子網路的名稱 |
	| **subnetPrefix** | 適用於應用程式閘道子網路的 CIDR 區塊 |
	| **skuname** | sku 執行個體大小 |
	| **容量** | 執行個體數目 |
	| **backendaddress1** | 第一部 Web 伺服器的 IP 位址 |
	| **backendaddress2** | 第二部 Web 伺服器的 IP 位址 |
	

>[AZURE.IMPORTANT]Github 所管理的 ARM 範本內容可能會隨著時間改變。使用範本前，請務必先檢查當中的內容。
	
6. 檢查 **resources** 下方的內容，並注意以下項目：

	- **type**。範本所建立的資源類型。在此案例中為 **Microsoft.Network/applicationGateways**，代表應用程式閘道。
	- **name**。資源的名稱。請注意 **[parameters('applicationGatewayName')]** 的用法，這表示此名稱是在部署期間由使用者輸入的內容，或是由參數檔案所提供。
	- **properties**。資源屬性的清單。此範本會在應用程式閘道建立期間，使用虛擬網路與公用 IP 位址。

7. 瀏覽回到 https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-create-application-gateway/azuredeploy.json。
8. 依序按一下 [azuredeploy-paremeters.json] 和 [RAW]。
9. 將檔案儲存至您電腦上的本機資料夾。
10. 開啟您剛儲存的檔案，編輯參數的值。使用下列值來部署本文案例所述的應用程式閘道。

		{
		  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
		{
    	"location" : {
        "value" : "West US"
    	},
    	"addressPrefix": {
        "value": "10.0.0.0/16"
    	},
    	"subnetPrefix": {
        "value": "10.0.0.0/24"
    	},
    	"skuName": {
        "value": "Standard_Small"
    	},
    	"capacity": {
        "value": 2
    	},
    	"backendIpAddress1": {
        "value": "10.0.1.10"
    	},
    	"backendIpAddress2": {
        "value": "10.0.1.11"
    	}
		}

11. 儲存檔案。您可以使用線上 json 驗證工具 (例如 [JSlint.com](http://www.jslint.com/))，來測試 Json 範本和參數範本。
 
## 使用 PowerShell 部署 ARM 範本

1. 如果您從未用過 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)，並遵循其中的所有指示登入 Azure，然後選取您的訂用帳戶。
2. 在 Azure PowerShell 提示字元中執行 **Switch-AzureMode** Cmdlet，以切換至資源管理員模式，如下所示。

		Switch-AzureMode AzureResourceManager
	
預期的輸出：

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

>[AZURE.WARNING]Switch-AzureMode Cmdlet 即將被汰除。屆時將重新命名所有的資源管理員 Cmdlet。
	
3. 如有需要，請使用 `New-AzureResourceGroup` Cmdlet，來建立新的資源群組。在下列範例中，您將在 East US 位置中建立名為 AppgatewayRG 的新資源群組：

		PS C:\> New-AzureResourceGroup -Name AppgatewayRG -Location "East US"
		VERBOSE: 5:38:49 PM - Created resource group 'AppgatewayRG' in location 'eastus'


		ResourceGroupName : AppgatewayRG
		Location          : eastus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
	                 Actions  NotActions
	                 =======  ==========
	                  *

		ResourceId        : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/AppgatewayRG

4. 執行 New-AzureResourceGroupDeployment Cmdlet，使用先前下載並修改的範本和參數檔案來部署新的 VNet。

		New-AzureResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
 		   -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

命令列將產生下列輸出項目：

		DeploymentName    : testappgatewaydeployment
		ResourceGroupName : appgatewayRG
		ProvisioningState : Succeeded
		Timestamp         : 9/19/2015 1:49:41 AM
		Mode              : Incremental
		TemplateLink      :
		Parameters        :
                   Name             Type                       Value
                   ===============  =========================  ==========
                   location         String                     East US
                   addressPrefix    String                     10.0.0.0/16
                   subnetPrefix     String                     10.0.0.0/24
                   skuName          String                     Standard_Small
                   capacity         Int                        2
                   backendIpAddress1  String                     10.0.1.10
                   backendIpAddress2  String                     10.0.1.11
					
		Outputs           :


## 使用 Azure CLI 部署 ARM 範本

若要使用 Azure CLI 部署您下載的 ARM 範本，請依照下列步驟執行。

1. 如果您從未使用過 Azure CLI，請參閱[安裝和設定 Azure CLI](xplat-cli-install.md)，並依照指示進行，直到選取您的 Azure 帳戶和訂用帳戶。
2. 執行 **azure config mode** 命令，以切換為資源管理員模式，如下所示。

		azure config mode arm

此為上述命令的預期輸出內容：

		info:	New mode is arm

3. 如有必要，請執行 **azure group create** 來建立新的資源群組，如下所示。請查看命令的輸出內容。輸出後顯示的清單可說明所使用的參數。如需資源群組的詳細資訊，請造訪 [Azure 資源管理員概觀](resource-group-overview.md)。

		azure group create -n appgatewayRG -l eastus

**-n (或 --name)**。新資源群組的名稱。在本文案例中為 *appgatewayRG*。

**-l (或 --location)**。將會在當中建立新資源群組的 Azure 區域。在本文案例中為 *Eastus*。

4. 執行 **azure group deployment create** Cmdlet，使用先前下載並修改的範本和參數檔案來部署新的 VNet。輸出後顯示的清單可說明所使用的參數。

		azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

此為上述命令的預期輸出內容：

		azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
		info:    Executing command group deployment create
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "TestAppgatewayDeployment"
		+ Waiting for deployment to complete
		data:    DeploymentName     : TestAppgatewayDeployment
		data:    ResourceGroupName  : appgatewayRG
		data:    ProvisioningState  : Succeeded
		data:    Timestamp          : 2015-09-21T20:50:27.5129912Z
		data:    Mode               : Incremental
		data:    Name               Type    Value
		data:    -----------------  ------  --------------
		data:    location           String  East US
		data:    addressPrefix      String  10.0.0.0/16
		data:    subnetPrefix       String  10.0.0.0/24	
		data:    skuName            String  Standard_Small
		data:    capacity           Int     2
		data:    backendIpAddress1  String  10.0.1.10
		data:    backendIpAddress2  String  10.0.1.11
		info:    group deployment create command OK

**-g (或 --resource-group)**。要建立新 VNet 的資源群組名稱。

**-f (或 --template-file)**。ARM 範本檔案的路徑。

**-e (或 --parameters-file)**。ARM 參數檔案的路徑。

## 使用「按一下即部署」部署 ARM 範本

「按一下即部署」是另一種使用 ARM 範本的方式。這是將範本與 Azure 入口網站搭配使用的簡便方法。


### 步驟 1 
使用「[按一下來部署應用程式閘道](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/)」連結，會將您重新導向至應用程式閘道的入口網站範本頁面。


### 步驟 2 

按一下 [部署至 Azure]。

![arm-scenario](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### 步驟 3

在入口網站上填寫適用於部署範本的參數，然後按一下 [確定]

![arm-scenario](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### 步驟 4

選取 [法律條款]，然後按一下 [購買]

### 步驟 5

在 [自訂部署] 刀鋒視窗上，按一下 [建立]。


 
## 後續步驟

如果您想要設定 SSL 卸載，請參閱[設定應用程式閘道以進行 SSL 卸載](application-gateway-ssl.md)。

如果您想要將應用程式閘道設為與 ILB 搭配使用，請參閱[建立具有內部負載平衡器 (ILB) 的應用程式閘道](application-gateway-ilb.md)。

如果您想進一步了解一般負載平衡選項，請參閱：

- [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_1203_2015-->