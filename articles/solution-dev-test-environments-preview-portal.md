<properties
   pageTitle="開發和測試環境 | Microsoft Azure"
   description="了解如何使用 Azure 資源管理員範本來快速且一致地建立及刪除開發和測試環境。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="jimdial"
   manager="carolz"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="jdial"/>

# Microsoft Azure 中的開發和測試環境

自訂應用程式通常會在部署到生產環境前部署到多個開發和測試環境。環境在內部部署建立時，會購買運算資源或配置給每個應用程式的每個環境。環境通常包含數個具有手動方式部署之特定組態或具有複雜自動化指令碼的實體或虛擬機器。部署通常需要數小時，並且在各環境中會導致不一致的組態。

## 案例 ##

當您在 Microsoft Azure 中佈建開發和測試環境時，您只需支付使用資源的費用。這篇文章說明如何使用 Azure 資源管理員範本和參數檔案，快速且一致地建立、維護和刪除開發及測試環境，如下所示。

![案例](./media/solution-dev-test-environments-preview-portal/scenario.png)

上方顯示三個開發和測試環境。每個環境都有一個 web 應用程式和 SQL 資料庫。每個環境中的應用程式和資料庫名稱都不同。這篇文章說明如何使用範本在各環境中部署相同的資源，並使用唯一的參數檔案在各環境中指定資源的不同組態。

如果您不熟悉 Azure 資源管理員概念，建議您在閱讀本文之前先閱讀 [Azure 資源管理員概觀](resource-group-overview.md)一文。

您可能會想要先進行這篇文章中所列的步驟，而不閱讀任何參考文章，以快速取得使用 Azure 資源管理員範本的經驗。當您進行這些步驟時，您一定會遭遇許多問題。一旦您完成這些步驟，您就可以藉由利用這些步驟實驗並閱讀參考文章，取得大部分問題的解答。

## 規劃 Azure 資源使用
一旦您的應用程式具備高階設計，您就可以定義：

- 您的應用程式所包含的 Azure 資源。您可能會建置您的應用程式並將它部署為具備 Azure SQL Database 的 Azure Web 應用程式。您可能會在虛擬機器中使用 PHP 和 MySQL 或 IIS 和 SQL Server 或其他元件建置您的應用程式。[Azure App Service、雲端服務與虛擬機器之比較](app-service-web/choose-web-site-cloud-service-vm.md)一文可協助您決定您想要用於應用程式的 Azure 資源。
- 您的應用程式符合哪些服務層級要求，例如可用性、安全性和級別。

## 使用 Azure 資源管理員範本
Azure 資源管理員範本會定義應用程式所使用的所有 Azure 資源。數個範本已存在，您可以直接在 Azure Preview 入口網站中部署，或在原始檔控制系統中利用應用程式程式碼下載、修改和儲存。現有存在的範本可能會包含您想要讓應用程式搭配使用的資源。您可以在 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/) GitHub 儲存機制中找到可用的範本清單。

在清單中，您會看到 "[201-web-app-sql-database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)" 資料夾。由於許多自訂應用程式包含 web 應用程式和 SQL 資料庫，此範本可做為這篇文章其餘部分的範例，協助您了解如何使用範本。完整說明此範本建立與設定的所有內容已超出本文的範圍，但是如果您打算使用它來建立組織中的實際環境，您會想要藉由閱讀[佈建 Web 應用程式與 SQL Database](app-service-web/app-service-web-arm-with-sql-database-provision.md) 一文來完全了解它。

> [AZURE.NOTE]您可以直接按一下[佈建 Web 應用程式與 SQL Database](http://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) 一文中的 [部署至 Azure] 按鈕以將範本部署至 Azure。您可能會發現這對了解範本很有幫助，但這麼做並不能讓您使用應用程式程式碼編輯、定義版本及儲存您的範本和參數值。本文中的步驟說明如何利用應用程式程式碼儲存您的範本和參數值和定義其版本。

  **步驟 1：**檢視 201-web-app-sql-database 資料夾中 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy.json) 檔案的內容。這是 Azure 資源管理員範本檔案。在檢視模式中，按一下 [原始](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy.json) 按鈕。利用您的滑鼠選取此檔案的全部內容，並將它以 "TestApp1-Template.json" 的檔案名稱儲存到您的電腦。

在此範本檔案中，您會看到 [資源] 區段，其定義此範本所建立之 Azure 資源。在其他資源類型中，此範本會建立 [Azure Web 應用程式](app-service-web/app-service-web-overview.md)和 [Azure SQL Database](sql-database/sql-database-technical-overview.md) 資源。

您也會看到 [參數] 區段，其定義可用來設定每個資源的參數。在範本中指定的部分參數會有 "defaultValue" 屬性，其他參數則沒有。使用範本部署 Azure 資源時，您必須提供值給所有沒有 defaultValue 屬性的參數。如果您未提供值給具備 defaultValue 屬性的參數，則會使用範本中為 defaultValue 參數指定的值。

範本可定義建立的 Azure 資源，以及可用來設定每個資源的參數。通常您會想要在每個開發和測試環境中建立相同的資源。您可以藉由閱讀[設計 Azure 資源管理員範本的最佳做法](best-practices-resource-manager-design-templates.md)一文，進一步了解範本及如何設計您自己的範本。

## 建立參數檔案

您可能會想在每個環境中建立相同的 Azure 資源，但您可能會想要在每個環境中設定不同的資源。這是參數檔案的由來。

  **步驟 2：**檢視 201-web-app-sql-database 資料夾中 [azuredeploy-parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-sql-database/azuredeploy-parameters.json) 檔案的內容。這是您在步驟 1 中所儲存之範本檔案的參數檔案。在檢視模式中，按一下 [原始](https://github.com/Azure/azure-quickstart-templates/raw/master/201-web-app-sql-database/azuredeploy-parameters.json) 按鈕。使用您的滑鼠，選取此檔案的全部內容，並將它以下列名稱儲存至電腦上的三個個別檔案：

- TestApp1-Parameters-Development.json
- TestApp1-Parameters-Test.json
- TestApp1-Parameters-Pre-Production.json

  **步驟 3：**使用任何文字或 JSON 編輯器，編輯您在步驟 2 中建立的開發和測試環境參數檔案，以下列的值取代檔案中的現有值。

  --TestApp1-Parameters-Development.json--

| 參數 | 說明 |
|---|---|
| **siteName** | TestApp1Dev |
| **hostingPlanName** | TestApp1PlanDev |
| **siteLocation** | 美國中部 |
| **serverName** | testapp1dev |
| **serverLocation** | 美國中部 |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1dev |

--TestApp1-Parameters-Test.json--

| 參數 | 說明 |
|---|---|
| **siteName** | TestApp1Test |
| **hostingPlanName** | TestApp1PlanTest |
| **siteLocation** | 美國中部 |
| **serverName** | testapp1test |
| **serverLocation** | 美國中部 |
| **administratorLogin** | testapp1Admin |
| **administratorLoginPassword** | #testapp1XYZ |
| **databaseName** | testapp1test |

在步驟 6 中，這些參數檔案將在開發和測試環境中用來建立 Azure Web 應用程式和 Azure SQL Database 資源的唯一組態。

 **步驟 4：**編輯您在步驟 2 中建立的 TestApp1-Parameters-Pre-Production.json 參數檔案。將檔案的全部內容取代為下列內容：

	{
	  "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
	  "contentVersion" : "1.0.0.0",
	  "parameters" : {
	    "administratorLogin" : {
	      "value" : "testApp1Admin"
	    },
	    "administratorLoginPassword" : {
	      "value" : "testApp1XP49"
	    },
	    "databaseName" : {
	      "value" : "testapp1preprod"
	    },
	    "hostingPlanName" : {
	      "value" : "TestApp1PlanPreProd"
	    },
	    "serverLocation" : {
	      "value" : "Central US"
	    },
	    "serverName" : {
	      "value" : "testapp1preprod"
	    },
	    "siteLocation" : {
	      "value" : "Central US"
	    },
	    "siteName" : {
	      "value" : "TestApp1PreProd"
	    },
	    "sku" : {
	      "value" : "Standard"
	    },
		"requestedServiceObjectiveName" : {
		  "value" : "S1"
	    }
	  }
	}

在上述的生產前參數檔案中，**sku** 和 **requestedServiceObjectiveName** 參數為*已新增*，而它們並未新增至開發和測試參數檔案。這是因為範本中有為這些參數指定的預設值，且預設值也用於開發和測試環境中，但在生產前環境中用於這些參數的是非預設值。

在生產前環境中將非預設值用於這些參數的原因是要測試您的生產環境可能會偏好的這些參數的值。這些參數全都和應用程式使用的 Azure [Web 應用程式主控方案](http://azure.microsoft.com/pricing/details/app-service/)，或 **sku** 和 Azure [SQL Database](http://azure.microsoft.com/pricing/details/sql-database/)，或 **requestedServiceObjectiveName** 相關。不同的 sku 和服務目標名稱有不同的成本和功能，並支援不同的服務層級度量。

下表列出範本中指定參數的預設值，以及在生產前參數檔案中替代預設值所改用的值。

| 參數 | 預設值 | 參數檔案值 |
|---|---|---|
| **sku** | 免費 | 標準 |
| **requestedServiceObjectiveName** | S0 | S1 |

## 建立環境
所有的 Azure 資源必須建立於 [Azure 資源群組](azure-portal/resource-group-portal.md)內。資源群組可讓您將 Azure 資源分組，讓他們可以共同管理。[權限](resource-group-rbac.md)可以指派給資源群組，讓組織內的特定人員可以建立、修改、刪除或檢視它們和其中的資源。資源群組中資源的警示和計費資訊可以在 [Azure Preview 入口網站](https://portal.azure.com)中檢視。資源群組會在 Azure [位置](http://azure.microsoft.com/regions/)中建立。在本文中，所有資源都會在美國中部位置建立。當您開始建立實際環境時，您會選擇最適合您需求的位置。

  **步驟 5：**使用下列任何一種方法建立開發和測試環境的資源群組。這兩種方法會達到完全相同的結果。

  **方法 1：**Azure 命令列介面 (CLI)

  確定您已在Windows、OS X 或 Linux 電腦上[安裝](xplat-cli-install.md) CLI，且已將組織識別碼[連接](xplat-cli-connect.md)到 Azure 訂用帳戶。從 CLI 命令列，輸入下列命令來建立開發環境的資源群組。

	azure group create "TestApp1-Development" "Central US"

  如果命令成功會傳回下列內容：

	info:    Executing command group create
	+ Getting resource group TestApp1-Development
	+ Creating resource group TestApp1-Development
	info:    Created resource group TestApp1-Development
	data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
	data:    Name:                TestApp1-Development
	data:    Location:            centralus
	data:    Provisioning State:  Succeeded
	data:    Tags: null
	data:
	info:    group create command OK

  若要建立測試環境的資源群組，請輸入下列命令：

	azure group create "TestApp1-Test" "Central US"

  若要建立生產前環境的資源群組，請輸入下列命令：

	azure group create "TestApp1-Pre-Production" "Central US"

  **方法 2：**PowerShell

  確定您已在 Windows 電腦上安裝 PowerShell 並連接到[如何安裝及設定 Azure PowerShell](powershell-install-configure.md) 一文中詳細說明的訂用帳戶。從 PowerShell 命令提示字元，輸入下列命令來建立開發環境的資源群組。如果您是使用 Azure PowerShell 1.0 Preview，命令為 **New-AzureRmResourceGroup**，如下所示。如果您是使用 1.0 Preview 之前的 Azure PowerShell 版本，命令為 **New-AzureResourceGroup**。

	New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

  如果命令成功會傳回下列內容：

	WARNING: The output object of this cmdlet will be modified in a future release.


	ResourceGroupName : TestApp1-Development
	Location          : centralus
	ProvisioningState : Succeeded
	Tags              : 
	Permissions       : 
	                    Actions  NotActions
	                    =======  ==========
	                    *                  
	                    
	ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

  若要建立測試環境的資源群組，請輸入下列命令：

	New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

  若要建立生產前環境的資源群組，請輸入下列命令：

	New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

 **步驟 6：**使用應用程式的範本檔案，將 Azure 資源部署到每個環境的資源群組，並使用下列任何一種方法，將其部署到每個環境的參數檔案。這兩種方法會達到完全相同的結果。

  **方法 1：**Azure 命令列介面 (CLI)

  從 CLI 命令列，輸入下列命令將資源部署至您為開發環境建立的資源群組，將 [路徑] 取代為您在先前步驟中儲存的檔案路徑。

	azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

  如果命令成功會傳回下列內容：

	info:    Executing command group deployment create
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "Deployment1"
	+ Waiting for deployment to complete
	data:    DeploymentName     : Deployment1
	data:    ResourceGroupName  : TestApp1-Development
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
	data:    Mode               : Incremental
	data:    Name                           Type          Value
	data:    -----------------------------  ------------  ----------------------------
	data:    siteName                       String        TestApp1Dev
	data:    hostingPlanName                String        TestApp1PlanDev
	data:    siteLocation                   String        Central US
	data:    sku                            String        Free
	data:    workerSize                     String        0
	data:    serverName                     String        testapp1dev
	data:    serverLocation                 String        Central US
	data:    administratorLogin             String        testapp1Admin
	data:    administratorLoginPassword     SecureString  undefined
	data:    databaseName                   String        testapp1dev
	data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
	data:    edition                        String        Standard
	data:    maxSizeBytes                   String        1073741824
	data:    requestedServiceObjectiveName  String        S0
	info:    group deployment create command OKx

  如果命令未成功，請解決任何錯誤訊息並再試一次。常見的問題為使用未遵守 Azure 資源命名限制的參數值。其他疑難排解秘訣可在[在 Azure 中疑難排解資源群組部署](virtual-machines/resource-group-deploy-debug.md)一文中找到。

  從 CLI 命令列，輸入下列命令將資源部署至您為測試環境建立的資源群組，將 [路徑] 取代為您在先前步驟中儲存的檔案路徑。

	azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

  從 CLI 命令列，輸入下列命令將資源部署至您為生產前環境建立的資源群組，將 [路徑] 取代為您在先前步驟中儲存的檔案路徑。

	azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
  **方法 2：**PowerShell

  從 PowerShell 命令提示字元，輸入下列命令將資源部署至您為開發環境建立的資源群組，將 [路徑] 取代為您在先前步驟中儲存的檔案路徑。如果您是使用 Azure PowerShell 1.0 Preview，命令為 **New-AzureRmResourceGroupDeployment**，如下所示。如果您是使用 1.0 Preview 之前的 Azure PowerShell 版本，命令為 **New-AzureResourceGroupDeployment**。

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

  如果命令成功會傳回下列內容：

	DeploymentName    : Deployment1
	ResourceGroupName : TestApp1-Development
	ProvisioningState : Succeeded
	Timestamp         : XX/XX/XXXX 2:44:48 PM
	Mode              : Incremental
	TemplateLink      : 
	Parameters        : 
	                    Name             Type                       Value     
	                    ===============  =========================  ==========
	                    siteName         String                     TestApp1Dev
	                    hostingPlanName  String                     TestApp1PlanDev
	                    siteLocation     String                     Central US
	                    sku              String                     Free      
	                    workerSize       String                     0         
	                    serverName       String                     testapp1dev
	                    serverLocation   String                     Central US
	                    administratorLogin  String                     testapp1Admin
	                    administratorLoginPassword  SecureString                         
	                    databaseName     String                     testapp1dev
	                    collation        String                     SQL_Latin1_General_CP1_CI_AS
	                    edition          String                     Standard  
	                    maxSizeBytes     String                     1073741824
	                    requestedServiceObjectiveName  String                     S0        
	                    
	Outputs           :

  如果命令未成功，請解決任何錯誤訊息並再試一次。常見的問題為使用未遵守 Azure 資源命名限制的參數值。其他疑難排解秘訣可在[在 Azure 中疑難排解資源群組部署](virtual-machines/resource-group-deploy-debug.md)一文中找到。

  從 PowerShell 命令提示字元，輸入下列命令將資源部署至您為測試環境建立的資源群組，將 [路徑] 取代為您在先前步驟中儲存的檔案路徑。

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  從 PowerShell 命令提示字元，輸入下列命令將資源部署至您為生產前環境建立的資源群組，將 [路徑] 取代為您在先前步驟中儲存的檔案路徑。

	New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

在原始檔控制系統中可以使用應用程式程式碼定義範本和參數檔案的版本並加以維護。您也可以將上述命令儲存到指令碼檔案，並將它們和程式碼一起儲存。

## 維護環境
在整個開發過程中，不同環境中的 Azure 資源組態可能會出現有意或無意的不一致變更。這會在應用程式開發週期中造成不必要的疑難排解和問題解決。

  **步驟 7：**變更環境。開啟 [Azure Preview 入口網站](https://portal.azure.com) 並以您用來完成上述步驟所使用的相同帳戶登入。如下圖所示，按一下 \[瀏覽所有-->資源群組] (您可能需要向下捲動瀏覽刀鋒視窗以查看資源群組)。您會看到您使用先前步驟中的其中一個方法所建立的所有三個資源群組。按一下 TestApp1-Development 資源群組，如下所示。

  ![入口網站](./media/solution-dev-test-environments-preview-portal/portal1.png)

  按一下 TestApp1-Development 資源群組之後，您會看到刀鋒視窗，列出範本在您於上一個步驟完成的資源群組部署中所建立的資源。藉由按一下 [TestApp1Dev-->刪除] 來刪除 TestApp1Dev Web 應用程式資源，如下所示。

  ![入口網站](./media/solution-dev-test-environments-preview-portal/portal2.png)

  當入口網站提示您是否確定要刪除資源時，按一下 [是]。資源群組的內容現在與其應有內容不同。您可以從多個資源群組刪除多個資源來進一步實驗，或甚至變更部分資源的組態設定。

> [AZURE.NOTE]如果不使用 Azure Preview 入口網站從資源群組刪除資源，您可以使用 PowerShell [Remove-AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) 命令或來自 CLI 的 "azure resource delete" 命令來完成相同工作。

  **步驟 8：**使用您在步驟 6 中使用的相同命令，將環境重新部署到資源群組，但將 "Deployment1" 取代為 "Deployment2"。 如下圖的 [摘要] 區段所示，您會看到來自範本的所有資源再次存在於 TestApp1-Development 資源群組中。利用 Azure 資源管理員範本部署環境的優點之一是您可以隨時輕鬆地將環境重新部署回已知狀態。

  ![入口網站](./media/solution-dev-test-environments-preview-portal/portal3.png)

  如果您按一下圖中 [上次部署] 底下的文字，您會看到刀鋒視窗顯示資源群組的部署歷程記錄。因為您將名稱 "Deployment1" 用於第一個部署，將 "Deployment2" 用於第二個部署，所以您將有兩個項目。按一下部署會顯示刀鋒視窗，其顯示每個部署的結果。

## 刪除環境
一旦您完成環境，您會想要刪除它，讓您不必負擔您不再使用之 Azure 資源的使用費用。刪除環境比建立它們還要容易。在先前的步驟中，曾為每個環境建立個別的 Azure 資源群組。當您刪除資源群組時，也會一併刪除所有包含在內的資源。請謹記這一點，下列任何一種方法都會刪除環境 (資源群組) 以及您先前在其中部署的所有 Azure 資源。

  **步驟 9：**使用下列任何一種方法刪除環境。這兩種方法會達到完全相同的結果。

  **方法 1：Azure CLI**

  收到 CLI 提示時，輸入下列內容：

	azure group delete "TestApp1-Development"

  如果您在收到提示時輸入 "y"，命令會傳回下列內容：

	info:    Executing command group delete
	Delete resource group TestApp1-Development? [y/n] y
	+ Deleting resource group TestApp1-Development
	info:    group delete command OK

  收到 CLI 提示時，輸入下列內容以刪除剩餘的環境：

	azure group delete "TestApp1-Test"
	azure group delete "TestApp1-Pre-Production"
  
  **方法 2：**PowerShell

  如果您是使用 Azure PowerShell 1.0 Preview，要刪除資源群組的命令為 **Remove-AzureRmResourceGroup**，如下所示。如果您是使用 1.0 Preview 之前的 Azure PowerShell 版本，命令為 **Remove-AzureResourceGroup**。收到 PowerShell 提示時，輸入下列內容：

	Remove-AzureRmResourceGroup -Name TestApp1-Development

  如果您在收到提示時輸入 "y"，命令會傳回下列內容：

	Confirm
	Are you sure you want to remove resource group 'TestApp1-Development'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

  收到 PowerShell 提示時，輸入下列內容以刪除剩餘的環境：

	Remove-AzureRmResourceGroup -Name TestApp1-Test
	Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

不論您使用哪種方法，一旦命令完成執行，資源群組及其包含的所有資源將不再存在，而且您不再需要負擔資源的費用。

若要最小化您在應用程式開發期間使用 Azure 資源所負擔的費用，您可以使用 [Azure 自動化](automation/automation-intro.md)排程工作：

- 在每天結束時停止虛擬機器並在每天開始時重新啟動它們。
- 在每天結束時刪除整個環境並在每天開始時重新建立它們。
 
既然您已體驗過建立、維護和刪除開發及測試環境有多容易，您可以藉由利用上述步驟進一步實驗並閱讀本文中包含的參考內容，深入了解您剛執行的內容。

## 後續步驟

- 藉由指派 Microsoft Azure AD 群組或使用者至有能力在 Azure 資源上執行作業子集的特定角色，在每個環境中[委派系統管理控制](role-based-access-control-configure.md)到不同的資源。
- [指派標籤](resource-group-using-tags.md)至每個環境的資源群組及/或個別資源。您可能會將「環境」標籤新增至資源群組並設定其值以對應至您的環境名稱。當您需要組織資源以進行計費或管理時，標記可能特別有用。
- 在 [Azure Preview 入口網站](https://portal.azure.com)中監視資源群組中資源的警示和計費。

## 其他資源

- 利用已安裝的 Azure SDK 2.6 [在 Visual Studio 中建立和部署 Azure 資源管理員範本](http://msdn.microsoft.com/library/azure/Dn872471.aspx)。
- 使用 [Visual Studio Enterprise](https://www.visualstudio.com/products/visual-studio-enterprise-vs)、[Visual Studio Code](http://www.visualstudio.com/products/code-vs) 或 [Web Matrix](http://www.microsoft.com/web/webmatrix/) 建立應用程式。
- [部署 Web 應用程式](app-service-web/web-sites-deploy.md)到您建立的環境。
- 使用 [Visual Studio 發行管理](http://msdn.microsoft.com/Library/vs/alm/Release/overview)建立受管理的連續部署管線以快速、輕鬆且經常發行。
- 要求 [Azure 研發/測試實驗室](http://azure.microsoft.com/campaigns/devtest-lab/)的預覽邀請。它可讓您使用範本管理開發和測試實驗室環境，並設定組織內的使用配額和原則。

<!----HONumber=Nov15_HO2-->