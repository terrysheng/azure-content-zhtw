<properties
	pageTitle="使用範本部署 Azure 資源 | Microsoft Azure"
	description="了解如何使用 Azure 資源管理程式庫中可用的部分用戶端，以部署虛擬機器、虛擬網路和儲存體帳戶。"
	services="virtual-machines,virtual-networks,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="davidmu"/>

# 使用 .NET 程式庫和範本部署 Azure 資源

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。


藉由使用資源群組和範本，您可以管理所有能夠一起支援您的應用程式的資源。本教學課程將示範如何使用 Azure 資源管理程式庫中可用的部分用戶端，以及如何建立範本以部署虛擬機器、虛擬網路和儲存體帳戶。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

若要完成本教學課程，您也需要：

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Azure 儲存體帳戶](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 或 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

>[AZURE.NOTE]您此時建立的儲存體帳戶是用來儲存範本。當您部署用來儲存虛擬機器磁碟的範本時會建立另一個儲存體帳戶。在這個儲存體帳戶中建立一個名為「範本」的容器。

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

執行這些步驟需要 30 分鐘左右。

## 步驟 1：將應用程式新增至 Azure AD，並設定權限

若要使用 Azure AD 驗證對 Azure 資源管理員的要求，必須將應用程式新增至預設目錄中。執行下列操作來新增應用程式：

1. 開啟 Azure PowerShell 命令提示字元，然後執行此命令，並在出現提示時，輸入您的訂用帳戶的認證：

	    Login-AzureRmAccount

2. 將下列命令中的 {password} 取代成您想要使用的密碼，然後執行該命令以建立應用程式：

	    New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE]記下建立應用程式後傳回的應用程式識別碼，因為下一個步驟會用到。您也可以在 Azure 入口網站「Active Directory」區段中應用程式的 [用戶端識別碼] 欄位內尋找應用程式識別碼。

3. 將 {application-id} 取代成您剛才記錄的識別碼，然後建立應用程式的服務主體：

        New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. 設定使用應用程式的權限：

	    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## 步驟 2：建立 Visual Studio 專案、範本檔案，以及參數檔案

###建立範本檔案

有了 Azure Resource Manager 範本之後，您就可以使用 JSON 來敘述資源、相關設定和部署參數，一起部署和管理 Azure 資源。您在本教學課程中建立的範本，類似於資源庫中提供的範本。如需深入了解範本，請參閱[在美國西部部署簡單的 Windows VM](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)。

在 Visual Studio 中，執行下列操作：

1. 按一下 [**檔案**] > [**新增**] > [**專案**]。

2. 在 [**範本**] > [**Visual C#**] 中，選取 [**主控台應用程式**]，輸入專案的名稱和位置，然後按一下 [**確定**]。

3. 在 [方案總管] 中，以滑鼠右鍵按一下專案，然後按一下 [**加入**] > [**新增項目**]。

4.	在 [加入新項目] 視窗中，選取 [**文字檔**]，輸入 *VirtualMachineTemplate.json* 做為名稱，然後按一下 [**新增**]。

5.	開啟 VirtualMachineTemplate.json 檔案，然後加入開頭和結尾括號、必要的結構描述元素和必要的 contentVersion 元素：

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
	}
	```

6. [參數](../resource-group-authoring-templates.md#parameters)並非必要項目，但是可以讓範本管理更容易。它們會說明值類型、預設值 (如有需要)，以及可能的參數允許值。對於此教學課程，用來建立虛擬機器、儲存體帳戶和虛擬網路的參數會加入至範本。

    在 ContentVersion 元素之後加入參數元素及其子元素：

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUserName": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter" ]
			}
		},
 	}
	```

7. [變數](../resource-group-authoring-templates.md#variables)可以在範本中用來指定會經常變更的值或需要透過參數值組合建立的值。

    在參數區段之後加入變數元素：

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": ["2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
			"OSDiskName": "osdiskforwindowssimple",
			"nicName": "myVMnic1",
			"addressPrefix": "10.0.0.0/16",
			"subnetName": "Subnet",
			"subnetPrefix": "10.0.0.0/24",
			"storageAccountType": "Standard_LRS",
			"publicIPAddressName": "myPublicIP",
			"publicIPAddressType": "Dynamic",
			"vmStorageAccountContainerName": "vhds",
			"vmName": "MyWindowsVM",
			"vmSize": "Standard_A2",
			"virtualNetworkName": "MyVNET",
			"vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
			"subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"
		},
	}
	```

8.	[資源](../resource-group-authoring-templates.md#resources)，例如虛擬機器、虛擬網路，以及儲存體帳戶接下來會在範本中定義。

    在變數區段之後加入資源區段：

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
			"OSDiskName": "osdiskforwindowssimple",
			"nicName": "myVMnic1",
			"addressPrefix": "10.0.0.0/16",
			"subnetName": "Subnet",
			"subnetPrefix": "10.0.0.0/24",
			"storageAccountType": "Standard_LRS",
			"publicIPAddressName": "myPublicIP",
			"publicIPAddressType": "Dynamic",
			"vmStorageAccountContainerName": "vhds",
			"vmName": "MyWindowsVM",
			"vmSize": "Standard_A2",
			"virtualNetworkName": "MyVNET",
			"vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
			"subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"
		},
		"resources": [
			{
  			"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Storage/storageAccounts",
				"name": "[parameters('newStorageAccountName')]",
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
						"addressPrefixes": [ "[variables('addressPrefix')]" ]
					},
					"subnets": [ {
						"name": "[variables('subnetName')]",
						"properties": {
							"addressPrefix": "[variables('subnetPrefix')]"
						}
					} ]
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
					"ipConfigurations": [ {
						"name": "ipconfig1",
						"properties": {
							"privateIPAllocationMethod": "Dynamic",
							"publicIPAddress": {
								"id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
							},
							"subnet": {
								"id": "[variables('subnetRef')]"
							}
						}
					} ]
				}
			},
			{
				"apiVersion": "2015-06-15",
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
						"computerName": "[variables('vmName')]",
						"adminUsername": "[parameters('adminUsername')]",
						"adminPassword": "[parameters('adminPassword')]",
					},
					"storageProfile": {
						"imageReference": {
							"publisher": "[variables('imagePublisher')]",
							"offer": "[variables('imageOffer')]",
							"sku": "[parameters('windowsOSVersion')]",
							"version" : "latest"
						},
						"osDisk": {
							"name": "osdisk",
							"vhd": {
								"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
							},
							"caching": "ReadWrite",
							"createOption": "FromImage"
						}
					},
					"networkProfile": {
						"networkInterfaces" : [ {
							"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
						} ]
					}
				}
			} ]
		}
		```

9.	儲存您所建立的範本檔案。

###建立參數檔案

若要為範本中所定義的資源參數指定值，您可以建立參數檔案，其中包含值並且將其提交至具有範本的資源管理員。在 Visual Studio 中，執行下列操作：

1.	在 [方案總管] 中，以滑鼠右鍵按一下專案，然後依序按一下 [**加入**]、[**新增項目**]。

2.	在 [加入新項目] 視窗中，選取 [**文字檔**]，輸入 *Parameters.json* 做為名稱，然後按一下 [**新增**]。

3.	開啟 parameters.json 檔案，然後加入下列 JSON 內容：

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "value": "mytestsa1" },
			"adminUserName": { "value": "mytestacct1" },
			"adminPassword": { "value": "mytestpass1" },
			"dnsNameForPublicIP": { "value": "mytestdns1" }
		}
	}
	```

    >[AZURE.NOTE]本教學課程中會建立執行 Windows Server 作業系統版本的虛擬機器。若要深入了解如何選取其他映像，請參閱[使用 Windows PowerShell 和 Azure CLI 瀏覽和選取 Azure 虛擬機器映像](resource-groups-vm-searching.md)。


4.	儲存您所建立的參數檔案。

### 上傳檔案

範本檔案和參數檔案可由 Azure 資源管理員從 Azure 儲存體帳戶存取。若要在您建立的第一個儲存體中置入檔案，請執行以下作業：

1.	開啟 [伺服器總管]，並且瀏覽至您要將檔案放在其中之儲存體帳戶的容器。對於此教學課程，範本所在的容器會命名為範本。

2.	在 [範本] 容器窗格右上角，按一下 [上傳 Blob] 圖示，瀏覽至您建立的 VirtualMachineTemplate.json 檔案，然後按一下 [**開啟**]。

3. 再按一下 [上傳 Blob] 圖示，瀏覽至您建立的 Parameters.json 檔案，然後按一下 [**開啟**]。

##步驟 3：安裝程式庫

NuGet 封裝是安裝完成本教學課程所需程式庫最簡單的方式。您必須安裝 Azure Resource Management Library 和Azure Active Directory Authentication Library。若要在 Visual Studio 中取得這些程式庫，請執行下列操作：

1.	在 [方案總管] 中，以滑鼠右鍵按一下專案名稱，然後按一下 [**管理 NuGet 封裝**]。

2.	在搜尋方塊中輸入 *Active Directory* ，對 Active Directory Authentication Library 封裝按一下 [**安裝**]，然後依照指示，安裝封裝。

3.      在頁面的頂端，選取 [包含發行前版本]。在搜尋方塊中輸入 *Microsoft.Azure.Management.Resources*，對 Microsoft Azure Resource Management Libraries 按一下 [安裝]，然後依照指示安裝封裝。

您現在已經準備就緒，可以開始使用程式庫建立您的應用程式。

##步驟 4：建立用來驗證要求的認證

既然已經建立 Azure Active Directory 應用程式並安裝驗證程式庫，您要將應用程式資訊格式化成用來驗證對 Azure 資源管理員的要求的認證。執行下列動作：

1.	開啟您建立之專案的 Program.cs 檔案，然後將下列 using 陳述式加入至檔案的頂端：

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Rest;
	```

2.	將下列方法加入至 Program 類別，以取得建立認證所需的權杖：

	```
	private static string GetAuthorizationHeader()
	{
		ClientCredential cc = new ClientCredential("{application-id}", "{password}");
		var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
		var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
		if (result == null)
		{
			throw new InvalidOperationException("Failed to obtain the JWT token");
		}

		string token = result.AccessToken;

		return token;
	}
	```

	將 {application-id} 取代成您先前記錄的應用程式識別碼、將 {password} 取代成您為 AD 應用程式選擇的密碼，並將 {tenant-id} 取代成您的訂用帳戶的租用戶識別碼。您可以透過執行 Get-AzureSubscription 來尋找租用戶識別碼。

3. 將下列程式碼加入至 Program.cs 檔案的 Main 方法以建立認證：

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

4. 儲存 Program.cs 檔案。


## 步驟 5：加入程式碼以部署範本

資源一律會從範本部署到資源群組。您可以使用 [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) 和 [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) 類別建立要在其中部署資源的資源群組。

1. 將下列方法加入至 Program 類別以建立資源群組：

	```
	public static void CreateResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the resource group...");
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var resourceGroup = new ResourceGroup {
			Location = location
		};
		var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
		Console.WriteLine(rgResult.StatusCode.Properties.ProvisioningState);
	}
	```

2. 將下列程式碼加入至 Main 方法，以呼叫您剛才加入的方法：

	```
	CreateResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}",
		"{location}");
		Console.ReadLine();
	```

	使用您想要用於資源群組的名稱取代 {group-name}。使用您的訂閱識別碼取代 {subscription-id}，您可以執行 Get-AzureSubscription 取得此識別碼。使用您想要建立資源的區域 (例如 "West US") 取代位置。

3. 將下列方法加入至 Program 類別，以使用您所定義的範本，將資源部署至資源群組：

	```
	public static void CreateTemplateDeployment(
		TokenCredentials credential,
		string groupName,
		string storageName,
		string deploymentName,
		string subscriptionId)
	{
		Console.WriteLine("Creating the template deployment...");
		var deployment = new Deployment();
		deployment.Properties = new DeploymentProperties
		{
			Mode = DeploymentMode.Incremental,
			TemplateLink = new TemplateLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json"
			},
			ParametersLink = new ParametersLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/Parameters.json"
			}
		};
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var dpResult = resourceManagementClient.Deployments.CreateOrUpdate(
			groupName,
			deploymentName,
			deployment);
		Console.WriteLine(dpResult.Properties.ProvisioningState);
	}
	```

	使用您先前放置檔案之帳戶的名稱取代 {storage-name}。

4. 將下列程式碼加入至 Main 方法，以呼叫您剛才加入的方法：

	```
	CreateTemplateDeployment(
		credential,
		"{group-name}",
		"{storage-name}",
		"{deployment-name}",
		"{subscription-id}");
	Console.ReadLine();
	```

    使用您建立之資源群組的名稱取代 {group-name}。使用您放置範本檔案之儲存體帳戶的名稱取代 {storage-name}。使用您想要用來識別資源集部署的名稱取代 {deployment-name}。使用您的訂閱識別碼取代 {subscription-id}，您可以執行 Get-AzureSubscription 取得此識別碼。

##步驟 6：加入程式碼以刪除資源

您將為 Azure 中所使用的資源支付費用，因此，刪除不再需要的資源永遠是最好的做法。您不需要從資源群組個別刪除每個資源。您可以刪除資源群組，且其所有資源都將會自動刪除。

1.	將下列方法加入至 Program 類別以刪除資源群組：

	```
	public static void DeleteResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId)
	{
		Console.WriteLine("Deleting resource group...");
		var resourceGroupClient = new ResourceManagementClient(credential);
		resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
	}
	```

2.	將下列程式碼加入至 Main 方法，以呼叫您剛才加入的方法：

	```
	DeleteResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}");
	Console.ReadLine();
	```
    使用您建立之資源群組的名稱取代 {group-name}。使用您的訂閱識別碼取代 {subscription-id}，您可以執行 Get-AzureSubscription 取得此識別碼。

##步驟 7：執行主控台應用程式

1.	若要執行主控台應用程式，按一下 Visual Studio 中的 [啟動]，然後使用搭配您的訂用帳戶使用的相同使用者名稱和密碼，登入 Azure AD。

2.	在顯示 [已接受] 狀態之後按 **Enter**。

	此主控台應用程式從開始到完成的完整執行應該需要 5 分鐘左右。在您按下 Enter 以開始刪除資源之前，可能需要幾分鐘時間，先確認 Azure 入口網站中建立的資源，然後再刪除。

3. 瀏覽至 Azure 入口網站中的稽核記錄，以查看資源的狀態：

	![建立 AD 應用程式](./media/arm-template-deployment/crpportal.png)

<!---HONumber=AcomDC_0107_2016-->