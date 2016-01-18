<properties
	pageTitle="使用 MSDeploy、主機名稱與 SSL 憑證部署 Web 應用程式"
	description="使用 Azure 資源管理員範本，透過 MSDeploy 及藉由設定自訂主機名稱與 SSL 憑證來部署 Web 應用程式"
	services="app-service\web"
	documentationCenter=""
	authors="jodehavi"
	/>

<tags
	ms.service="app-service-web"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/22/2015"
	ms.author="john.dehavilland"/>

# 使用 MSDeploy、自訂主機名稱與 SSL 憑證部署 Web 應用程式

本指南將逐步說明如何利用 MSDeploy，以及將自訂主機名稱和 SSL 憑證新增至 ARM 範本，以建立 Azure Web 應用程式的端對端部署。

如需關於建立範本的詳細資訊，請參閱[編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

###建立範例應用程式

您將部署 ASP.NET Web 應用程式。第一個步驟是建立簡單的 Web 應用程式 (或者，您可以選擇使用現有的應用程式；若是如此，您可以略過此步驟)。

開啟 Visual Studio 2015，然後選擇 [檔案] > [新增專案]。在出現的對話方塊上，選擇 [Web] > [ASP.NET Web 應用程式]。在 [範本] 下選擇 [Web]，然後選擇 MVC 範本。依序選取 [變更驗證類型] 和 [不需要驗證]。這是為了讓範例應用程式盡可能簡單。

此時，您將會有已備妥而可在部署程序中使用的基本 ASP.Net Web 應用程式。

###建立 MSDeploy 封裝

下一個步驟是建立用以將 Web 應用程式部署至 Azure 的封裝。若要這樣做，請儲存您的專案，然後從命令列中執行下列命令：

	msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

這將會在 PackageLocation 資料夾下建立壓縮的封裝。應用程式現在已可供部署；您現在可以建置 Azure 資源管理員範本來執行此作業。

###建立 ARM 範本
首先，我們先從會建立 Web 應用程式和主控方案的基本 ARM 範本開始 (請注意，為求簡潔，並不會顯示參數和變數)。

	{
		"name": "[parameters('appServicePlanName')]",
		"type": "Microsoft.Web/serverfarms",
		"location": "[resourceGroup().location]",
		"apiVersion": "2014-06-01",
		"dependsOn": [ ],
		"tags": {
		    "displayName": "appServicePlan"
		},
		"properties": {
		    "name": "[parameters('appServicePlanName')]",
		    "sku": "[parameters('appServicePlanSKU')]",
		    "workerSize": "[parameters('appServicePlanWorkerSize')]",
		    "numberOfWorkers": 1
		}
	},
	{
		"name": "[variables('webAppName')]",
		"type": "Microsoft.Web/sites",
		"location": "[resourceGroup().location]",
		"apiVersion": "2015-08-01",
		"dependsOn": [
		    "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
		],
		"tags": {
		    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
		    "displayName": "webApp"
		},
		"properties": {
		    "name": "[variables('webAppName')]",
		    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
		}
	}

接下來，您必須修改 Web 應用程式資源，以取用巢狀 MSDeploy 資源。這可讓您參考稍早建立的封裝，並指示 Azure 資源管理員使用 MSDeploy 將封裝部署至 Azure WebApp。以下顯示具有巢狀 MSDeploy 資源的 Microsoft.Web/網站資源：

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

現在您會注意到 MSDeploy 資源取用定義如下的 **packageUri** 屬性：

	"packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

此 **packageUri** 會取用一個儲存體帳戶 URI，該 URI 指向您的封裝 zip 所將上傳到的儲存體帳戶。當您部署範本時，Azure 資源管理員會利用[共用存取簽章](../storage/storage-dotnet-shared-access-signature-part-1.md)從儲存體帳戶將封裝提取到本機。此程序就會透過 PowerShell 指令碼而自動化，將封裝上傳，並呼叫 Azure 管理 API，以建立所需的金鑰，並將其傳入範本中做為參數 (*\_artifactsLocation* 和 *\_artifactsLocationSasToken*)。您將必須針對封裝在儲存體容器下所將上傳到的資料夾和檔案名稱定義參數。

接著，您必須在其他巢狀資源中新增，以設定主機名稱繫結來利用自訂網域。首先，您必須確定您擁有主機名稱，然後加以設定，使其由 Azure 驗證您具有其所有權 - 請參閱[在 Azure App Service 中設定自訂網域名稱](web-sites-custom-domain-name.md)。完成此動作後，您可以在 Microsoft.Web/網站資源區段下新增下列項目：

	{
		"apiVersion": "2015-08-01",
		"type": "hostNameBindings",
		"name": "www.yourcustomdomain.com",
		"dependsOn": [
		    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
		],
		"properties": {
		    "domainId": null,
		    "hostNameType": "Verified",
		    "siteName": "variables('webAppName')"
		}
	}

最後，您必須新增另一項最上層資源 Microsoft.Web/憑證。此資源將包含您的 SSL 憑證，且會與 Web 應用程式和主控方案存在於相同的層級。

	{
	    "name": "[parameters('certificateName')]",
	    "apiVersion": "2014-04-01",
	    "type": "Microsoft.Web/certificates",
	    "location": "[resourceGroup().location]",
	    "properties": {
	        "pfxBlob": "pfx base64 blob",
	        "password": "some pass"
	    }
	}

您必須具備有效的 SSL 憑證，才能設定這項資源。如果您已具備有效憑證，接著必須要以 base64 字串的形式擷取 pfx 位元組。要擷取此項目，使用下列 PowerShell 命令是選項之一：

	$fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

	[System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

接著，您可以將此項目傳遞至 ARM 部署範本做為參數。

至此，ARM 範本已準備就緒。

###部署範本

最後的步驟是將各個項目整合為完整的端對端部署。若要簡化部署，您可以利用您在 Visual Studio 中建立 Azure 資源群組專案時所新增的 **Deploy-AzureResourceGroup.ps1** PowerShell 指令碼，來上傳範本中所需的任何構件。若要這麼做，您必須事先建立您要使用的儲存體帳戶。在此範例中，我針對要上傳的 package.zip 建立了共用儲存體帳戶。指令碼會利用 AzCopy 將封裝上傳至儲存體帳戶。在您傳入構件資料夾位置後，指令碼會自動將該目錄中的所有檔案上傳至指定的儲存體容器。在呼叫 Deploy-AzureResourceGroup.ps1 之後，您必須更新 SSL 繫結，以對應自訂主機名稱與您的 SSL 憑證。

下列 PowerShell 顯示呼叫 Deploy-AzureResourceGroup.ps1 的完整部署：

	#Set resource group name
	$rgName = "Name-of-resource-group"

	#call deploy-azureresourcegroup script to deploy web app

	.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
									-ResourceGroupName $rgName `
									-UploadArtifacts "container-name" `
									-StorageAccountName "name-of-storage-acct-for-package" `
									-StorageAccountResourceGroupName "resource-group-name-storage-acct" `
									-TemplateFile "web-app-deploy.json" `
									-TemplateParametersFile "web-app-deploy-parameters.json" `
									-ArtifactStagingDirectory "C:\path\to\packagefolder"

	#update web app to bind ssl certificate to hostname. This has to be done after creation above.

	$cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

	$ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

	$props = $ar.Properties

	$props.HostNameSslStates[2].'SslState' = 1
	$props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
	$props.hostNameSslStates[2].'toUpdate' = $true

	Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

此時，您的應用程式應已部署，且您應能夠透過 https://www.yourcustomdomain.com 加以瀏覽

<!---HONumber=AcomDC_0107_2016-->