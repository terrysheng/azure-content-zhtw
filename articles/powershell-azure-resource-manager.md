<properties pageTitle="Using Windows PowerShell with Resource Manager" metaKeywords="ResourceManager, PowerShell, Azure PowerShell" description="Use Windows PowerShell to create a resource group" metaCanonical="" services="" documentationCenter="" title="Using Windows PowerShell with Resource Manager" authors="juneb" solutions="" manager="mbaldwin" editor="mollybos" />

將 Windows PowerShell 與資源管理員搭配使用
==========================================

資源管理員介紹一種看待 Azure 資源的嶄新方式。與其建立並管理個別資源，您首先想像一個複雜的服務，例如，部落格、相片庫、SharePoint 入口網站或 Wiki。使用範本 (服務的資源模型) 建立包含支援此服務所需資源的資源群組。然後，以邏輯單元的方式來管理與部署該資源群組。

在本教學課程中，您將了解如何在 Microsoft Azure 中將 Windows PowerShell 與資源管理員搭配使用。它會逐步引導您為搭配 SQL 資料庫的 Azure 託管網站 (或 Web 應用程式)，完成建立與部署資源群組的程序，並提供支援此群組所需的所有資源。

**預估完成時間：**15 分鐘

必要條件
--------

在將 Windows PowerShell 與資源管理員搭配使用之前，您必須具備下列項目：

-   Windows PowerShell 3.0 或更新版本。若要找出 Windows PowerShell 的版本，請輸入：`$PSVersionTable`，並確認 `PSVersion` 的值是否為 3.0 或更大值。若要安裝新版，請參閱 [Windows Management Framework 3.0](http://www.microsoft.com/en-us/download/details.aspx?id=34595) 或 [Windows Management Framework 4.0](http://www.microsoft.com/en-us/download/details.aspx?id=40855)。

-   Azure PowerShell 0.8.0 或更新版本。若要安裝最新版本，並將它與 Azure 訂閱建立關聯，請參閱[如何安裝和設定 Windows Azure PowerShell](http://www.windowsazure.com/en-us/documentation/articles/install-configure-powershell/) (英文)。

本教學課程是專為 Windows PowerShell 初學者所設計的，但它會假設您已了解基本概念，例如模組、Cmdlet 和工作階段。如需 Windows PowerShell 的詳細資訊，請參閱[開始使用 Windows PowerShell](http://technet.microsoft.com/en-us/library/hh857337.aspx) (英文)。

若要取得您在本教學課程中任何所見 Cmdlet 的詳細說明，請使用 Get-Help Cmdlet。

    Get-Help <cmdlet-name> -Detailed

例如，如需取得 Add-AzureAccount Cmdlet 的說明，請輸入：

    Get-Help Add-AzureAccount -Detailed

本教學課程內容
--------------

-   [關於 Azure Powershell 模組](#about)
-   [建立資源群組](#create)
-   [管理資源群組](#manage)
-   [疑難排解資源群組](#troubleshoot)
-   [後續步驟](#next)

關於 Azure Powershell 模組
--------------------------

自 0.8.0 版開始，Azure PowerShell 安裝包括三個 Windows PowerShell 模組：

-   **Azure**：包括管理個別資源 (例如儲存體帳戶、網站、資料庫、虛擬機器和媒體服務) 的傳統 Cmdlet。如需詳細資訊，請參閱 [Azure 服務管理 Cmdlet](http://msdn.microsoft.com/en-us/library/jj152841.aspx)。

-   **AzureResourceManager**：包括建立、管理和部署資源群組之 Azure 資源的 Cmdlet。如需詳細資訊，請參閱 [Azure 資源管理員 Cmdlet](http://go.microsoft.com/fwlink/?LinkID=394765)。

-   **AzureProfile**：包括通用於這兩個模組的 Cmdlet，例如，Add-AzureAccount、Get-AzureSubscription 和 Switch-AzureMode。如需詳細資訊，請參閱 [Azure 設定檔 Cmdlet](http://go.microsoft.com/fwlink/?LinkID=394766)。

> [ WACOM.NOTE] Azure 資源管理員模組目前為預覽版。它可能沒有如 Azure 服務管理模組所提供的相同管理功能。

Azure 和 Azure 資源管理員模組並非設計用於相同的 Windows PowerShell 工作階段。為了要在兩者間輕易切換，我們已將新的 Cmdlet (**Switch-AzureMode**) 新增至 Azure 設定檔模組。

當您使用 Azure PowerShell 時，系統依預設會匯入 Azure 和 Azure 設定檔模組中的 Cmdlet。若要切換至 Azure 資源管理員模組，請使用 Switch-AzureMode Cmdlet。它會將 Azure 模組從您的工作階段中移除，並匯入 Azure 資源管理員 (和 Azure 設定檔) 模組。

若要切換至 AzureResoureManager 模組，請輸入：

    PS C:PS C:\> Switch-AzureMode -Name AzureResourceManager


若要切回至 Azure 模組，請輸入：

    PS C:PS C:\> Switch-AzureMode -Name AzureServiceManagement


依預設，Switch-AzureMode 只會影響目前的工作階段。若要讓切換在所有的 Windows PowerShell 工作階段中生效，請使用 Switch-AzureMode 的 **Global** 參數。

如需 Switch-AzureMode Cmdlet 的說明，請輸入：`Get-Help Switch-AzureMode` 或請參閱 [Switch-AzureMode](http://go.microsoft.com/fwlink/?LinkID=394398)。

若要取得 AzureResourceManager 模組中的 Cmdlet 清單及說明概要，請輸入：

	PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

	Name                                   Synopsis
	----                                   --------
	Get-AzureLocation                      Gets the Azure data center locations and the resource types that they support
	Get-AzureResource                      Gets Azure resources
	Get-AzureResourceGroup                 Gets Azure resource groups
	Get-AzureResourceGroupDeployment       Gets the deployments in a resource group.
	Get-AzureResourceGroupGalleryTemplate  Gets resource group templates in the gallery
	Get-AzureResourceGroupLog              Gets the deployment log for a resource group
	New-AzureResource                      Creates a new resource in a resource group
	New-AzureResourceGroup                 Creates an Azure resource group and its resources
	New-AzureResourceGroupDeployment       Add an Azure deployment to a resource group.
	Remove-AzureResource                   Deletes a resource
	Remove-AzureResourceGroup              Deletes a resource group.
	Save-AzureResourceGroupGalleryTemplate Saves a gallery template to a JSON file
	Set-AzureResource                      Changes the properties of an Azure resource.
	Stop-AzureResourceGroupDeployment      Cancels a resource group deployment
	Test-AzureResourceGroupTemplate        Detects errors in a resource group template or template parameters



To get full help for a cmdlet, type a command with the format:

    Get-Help <cmdlet-name> -Full

例如，

    Get-Help Get-AzureLocation -Full

建立網站和資料庫的資源群組
==========================

本教學課程的這個部分會逐步引導您為搭配 SQL 資料庫的網站，建立與部署資源群組的程序。

您不需要是 Azure、SQL、網站或資源管理等方面的專家就可以進行此工作。範本提供了資源群組的模型，其中包括您可能需要的所有資源。並因為使用 Windows PowerShell 來自動化工作，您可以使用這些程序作為撰寫大規模工作指令碼的模型。

步驟 1：切換至 Azure 資源管理員
-------------------------------

1.  啟動 Windows PowerShell。您可以任意使用任何主機程式，例如，Windows PowerShell 主控台或 Windows PowerShell ISE。

2.  使用 **Switch-AzureMode** Cmdlet 來匯入 AzureResourceManager 和 AzureProfile 模組中的 Cmdlet。

    `PS C:PS C:\>Switch-AzureMode AzureResourceManager`gt;Switch-AzureMode AzureResourceManager

3.  若要將您的 Azure 帳戶新增至 Windows PowerShell 工作階段，請使用 **Add-AzureAccount** Cmdlet。

    `PS C:PS C:\> Add-AzureAccount`gt; Add-AzureAccount

此 Cmdlet 會提示您輸入電子郵件地址和密碼。接著，它會下載您的帳戶設定以供 Windows PowerShell 使用。

帳戶設定已過期，因此您必須偶爾重新整理這些設定。若要重新整理帳戶設定，請再次執行 **Add-AzureAccount**。

> [ WACOM.NOTE] AzureResourceManager 模組需要 Add-AzureAccount。發行設定檔案不符合需求。

步驟 2：選取組件庫範本
----------------------

建立資源群組及其資源的方式有幾種，但最簡單的方式是使用資源群組範本。*resource group template* 是可定義資源群組中資源的 JSON 字串。此字串包含使用者定義值的預留位置 (稱為 "parameters")，例如名稱和大小。

Azure 主控一個資源群組範本的組件庫，您也可以從頭開始或透過編輯組件庫範本以建立自己的範本。在本教學課程中，我們將使用組件庫範本。

若要搜尋 Azure 資源群組範本組件庫中的範本，請使用 **Get-AzureResourceGroupGalleryTemplate** Cmdlet。

在 Windows Powershell 提示字元中，請輸入：

    PS C:PS C:\> Get-AzureResourceGroupGalleryTemplate


此 Cmdlet 會傳回包含發行者和身分識別屬性的組件庫範本清單。您可以使用 **Identity** 屬性來識別命令中的範本。

    Publisher       Identity
    ---------       --------
    Ghost           Ghost.Ghost.0.1.0-preview1
    Joomla          Joomla.Joomla.0.1.0-preview1
    Microsoft       Microsoft.ASPNETEmptySite.0.1.0-preview1
    Microsoft       Microsoft.ASPNETstarterSite.0.1.0-preview1
    Microsoft       Microsoft.Bakery.0.1.0-preview1
    Microsoft       Microsoft.Boilerplate.0.1.0-preview1
    ...

秘訣若要重新叫用上一個命令，請按向上鍵。

Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json 範本看起來很有意思。若要取得組件庫範本的詳細資訊，請使用 **Identity** 參數。Identity 參數的值是範本的身分識別。

    PS C:PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json


此 Cmdlet 會傳回包含此範本更多詳細資訊 (包括說明) 的物件。

    <p>Windows Azure Web Sites offers secure and flexible development, 
    deployment and scaling options for any sized web application.Leverage 
    your existing tools to create and deploy applications without the hassle 
    of managing infrastructure.</p>

此範本看起來可以滿足我們的需求。我們可以先將它儲存至磁碟，並仔細檢視。

步驟 3：檢查範本
----------------

我們可以將範本儲存至磁碟上的 JSON 檔案。這不是必要步驟，但它可讓檢視範本變得更加容易。若要儲存範本，請使用 **Save-AzureResourceGroupGalleryTemplate** Cmdlet。使用其 **Identity** 參數來指定範本，並使用 **Path** 參數來指定磁碟上的路徑。

Save-AzureResourceGroupGalleryTemplate 會儲存範本，並傳回 JSON 範本檔案的檔案名稱路徑。

    PS C:PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1.json -Path D:\Azure\Templates

    Path
    ----
    D:\Azure\Templates\Microsoft.WebSite.0.1.0-preview1.json


您可以在文字編輯器 (例如記事本) 中檢視範本檔案。每個範本包含 **resources** 區段和 **parameters** 區段。

範本的 **resources** 區段會列出該範本建立的資源。此範本會建立 SQL 資料庫伺服器和 SQL 資料庫、伺服器陣列和網站，以及數個管理設定。

每個資源定義會包括其屬性 (例如，名稱、類型和位置)，以及使用者定義值的參數。例如，此範本區段定義 SQL 資料庫。它包括資料庫名稱 ([parameters('databaseName')])、資料庫伺服器位置 [parameters('serverLocation')] 和定序屬性 [parameters('collation')] 等參數。

     {
    "name":"[parameters('databaseName')]",
    "type":"databases",
    "location":"[parameters('serverLocation')]",
    "apiVersion": "2.0",
    "dependsOn": [
    "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
    "properties": {
    "edition":"Web",
    "collation":"[parameters('collation')]",
    "maxSizeBytes": "1073741824"
          }
        },

範本的 **[參數]** 區段是在所有資源中定義的參數集合。它包括 databaseName、serverLocation 和定序屬性。

    "parameters": {
    ...    

    "serverLocation": {
    "type":"string"
    }, 
    ...

    "databaseName": {
    "type":"string"
    },
    "collation": {
    "type":"string",
    "defaultValue":"SQL_Latin1_General_CP1_CI_AS"
    }

部分參數已有預設值。當您使用範本時，您無需提供這些參數的值。如果您沒有指定值，系統便會使用預設值。

    "collation": {
    "type":"string",
    "defaultValue":"SQL_Latin1_General_CP1_CI_AS"
        }

當參數具有列舉值時，有效值會與參數會一起列出。例如，**sku** 參數可接受 [免費]、[共用]、[基本] 和 [標準] 等值。如果您沒有為 **sku** 參數指定值，則它會使用預設值 ([免費])。

    "sku": {
    "type":"string",
    "allowedValues": [
    "Free",
    "Shared",
    "Basic",
    "Standard"
      ],
    "defaultValue":"Free"
    },

請注意，**administratorLoginPassword** 參數會使用安全字串，而非純文字。當您提供安全字串的值時，此值便會被遮住。

    "administratorLoginPassword": {
    "type":"securestring"
    },

我們大致上準備好開始使用範本，但在使用範本之前，我們必須找到各個資源的位置。

步驟 4：取得資源類型位置
------------------------

大多數的範本會要求您指定資源群組中各個資源的位置。每個資源都位於 Azure 資料中心內，但不是每個 Azure 資料中心都支援每種資源類型。

選取支援資源類型的任何位置。資源群組中的資源無需位於相同位置，且無需與資源群組或訂閱位於相同位置。

若要取得支援各種資源類型的位置，請使用 **Get-AzureLocation** Cmdlet。以下是輸出中的例外狀況。(此輸出可能與您的略有不同。詳細資料很有可能會隨著時間而改變。)

    Name                                 Locations
    ----                                 ---------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North Central US,
                                         South Central US, Central US, North Europe, West Europe

    Microsoft.Sql/servers/databases      Brazil South, Central US, East Asia, East US, East US 2, Japan
    East, Japan West, North Central US, North Europe, South Central US,
    Southeast Asia, West Europe, West US

    Microsoft.Web/serverFarms            Brazil South, East Asia, East US, Japan East, Japan West, North
    Central US, North Europe, West Europe, West US

    Microsoft.Web/sites                  Brazil South, East Asia, East US, Japan East, Japan West, North
    Central US, North Europe, West Europe, West US

現在我們已經有了建立資源群組所需的資訊。

步驟 5：建立資源群組
--------------------

在此步驟中，我們將使用群組範本建立資源群組。如需參考資訊，請開啟磁碟上的 Microsoft.WebSiteSQLDatabase.0.1.0-preview1 JSON 檔案，並依照指示進行。

若要建立資源群組，請使用 **New-AzureResourceGroup** Cmdlet。

此命令會使用 **Name** 參數來指定資源群組的名稱，並使用 **Location** 參數來指定其位置。使用 **Get-AzureLocation** 的輸出來選取資源群組的位置。它會使用 **GalleryTemplateIdentity** 參數來指定組件庫範本。

    PS C:PS C:\> New-AzureResourceGroup ` 
            -Name TestRG1 `
            -Location "East Asia" `
            -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
            ....


在您輸入範本名稱之後，New-AzureResourceGroup 便會立即擷取範本、剖析範本，並將範本參數動態新增至命令。這會讓指定範本參數值變得再容易不過了。另外，如果您忘記必要參數值，則 Windows PowerShell 會出現此值的提示。

**動態範本參數**

若要取得參數，請輸入減號 (-) 以顯示參數名稱，然後按 TAB 鍵。或者，輸入參數名稱的前幾個字母，例如 siteName，然後按 TAB 鍵。

     PS C: PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -si<TAB>


Windows PowerShell 會完成參數名稱。若要循環顯示參數名稱，請重複按 TAB 鍵。

     PS C: PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName 


輸入網站名稱，並針對每個參數重複 TAB 程序。具有預設值的參數為選擇性參數。若要接受預設值，請略過來自命令的參數。

當範本參數具有列舉值時 (例如本範本中的 sku 參數)，若要循環顯示參數值，請按 TAB 鍵。

     PS C: PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku <TAB>

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku Free<TAB>

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku Basic<TAB>


以下是 New-AzureResourceGroup 命令的範例，將只指定必要範本參數和 **Verbose** 命令參數。請注意，將略過 **administratorLoginPassword**。(倒單引號 (\`) 是 Windows PowerShell 行接續字元。)

    PS C:PS C:\> New-AzureResourceGroup 
    -Name TestRG `
    -Location "East Asia" `
    -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
    -siteName TestSite `
    -hostingPlanName TestPlan `
    -siteLocation "North Europe" `
    -serverName testserver `
    -serverLocation "West US" `
    -administratorLogin Admin01 `
    -databaseName TestDB `
    -Verbose


當您輸入命令時，系統會提示您輸入遺漏的必要參數 **administratorLoginPassword**。並在您輸入密碼時，此安全字串值便會被遮住。如此，就不會有以純文字方式提供密碼的風險。

    cmdlet New-AzureResourceGroup at command pipeline position 1
    Supply values for the following parameters:
    (Type !
     for Help.)
    administratorLoginPassword: **********

**New-AzureResourcGroup** returns the resource group that it created and deployed.Here is the output of the command, including the verbose output.

	VERBOSE: 3:47:30 PM - Create resource group 'TestRG' in location 'East Asia'
	VERBOSE: 3:47:30 PM - Template is valid.
	VERBOSE: 3:47:31 PM - Create template deployment 'Microsoft.WebSiteSQLDatabase.0.1.0-preview1'
	using template https://gallerystoreprodch.blob.core.windows.net/prod-microsoft-windowsazure-gallery/8D6B920B-10F4-4B5A-B3DA-9D398FBCF3EE.PUBLICGALLERYITEMS.MICROSOFT.WEBSITESQLDATABASE.0.1.0-PREVIEW1/DeploymentTemplates/Website_NewHostingPlan_SQL_NewDB-Default.json.
	VERBOSE: 3:47:43 PM - Resource Microsoft.Sql/servers 'testserver' provisioning status is succeeded
	VERBOSE: 3:47:43 PM - Resource Microsoft.Web/serverFarms 'TestPlan' provisioning status is
	succeeded
	VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/databases 'testserver/TestDB' provisioning status is succeeded
	VERBOSE: 3:47:47 PM - Resource microsoft.insights/autoscalesettings 'TestPlan-TestRG'
	provisioning status is succeeded
	VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/firewallrules
	'testserver/AllowAllWindowsAzureIps' provisioning status is succeeded
	VERBOSE: 3:47:50 PM - Resource Microsoft.Web/Sites 'TestSite' provisioning status is succeeded
	VERBOSE: 3:47:54 PM - Resource Microsoft.Web/Sites/config 'TestSite/web' provisioning status is succeeded
	VERBOSE: 3:47:54 PM - Resource microsoft.insights/alertrules 'ServerErrors-TestSite' provisioning
	status is succeeded
	VERBOSE: 3:47:57 PM - Resource microsoft.insights/components 'TestSite' provisioning status is succeeded
	
	
	ResourceGroupName : TestRG
	Location          : eastasia
	ProvisioningState : Succeeded
	Resources         :
                    Name                   Type                                  Location
                    =====================  ====================================  =========
                    ServerErrors-TestSite  microsoft.insights/alertrules         eastus
                    TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
                    TestSite               microsoft.insights/components         centralus
                    testserver             Microsoft.Sql/servers                 westus
                    TestDB                 Microsoft.Sql/servers/databases       westus
                    TestPlan               Microsoft.Web/serverFarms             westus
                    TestSite               Microsoft.Web/sites                   westus

只需幾個步驟，即可建立與部署複雜網站所需的資源。組件庫範本提供了執行此工作所需的幾乎所有資訊。另外，還可輕易自動化此工作。

管理資源群組
============

建立資源群組之後，您可以使用 AzureResourceManager 模組中的 Cmdlet，來管理資源群組、變更資源群組、將資源新增至資源群組，及移除資源群組。

-   若要取得訂閱中的資源群組，請使用 **Get-AzureResourceGroup** Cmdlet：

		PS C:>Get-AzureResourceGroup

		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Resources         :
	                    Name                   Type                                  Location
	                    =====================  ====================================  =========
	                    ServerErrors-TestSite  microsoft.insights/alertrules         eastus
	                    TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	                    TestSite               microsoft.insights/components         centralus
	                    testserver             Microsoft.Sql/servers                 westus
	                    TestDB                 Microsoft.Sql/servers/databases       westus
	                    TestPlan               Microsoft.Web/serverFarms             westus
	                    TestSite               Microsoft.Web/sites                   westus

-   若要取得資源群組中的資源，請使用 **GetAzureResource** Cmdlet 及其 ResourceGroupName 參數。在沒有使用參數的情況下，Get-AzureResource 會取得 Azure 訂閱中的所有資源。

		PS C:\> Get-AzureResource -ResourceGroupName TestRG
		
		Name                   ResourceType                          Location
		----                   ------------                          --------
		ServerErrors-TestSite  microsoft.insights/alertrules         eastus
	    TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	    TestSite               microsoft.insights/components         centralus
	    testserver             Microsoft.Sql/servers                 westus
	    TestDB                 Microsoft.Sql/servers/databases       westus
	    TestPlan               Microsoft.Web/serverFarms             westus
	    TestSite               Microsoft.Web/sites                   westus

-   若要將資源新增至資源群組，請使用 **New-AzureResource** Cmdlet。此命令會將新的網站新增至 TestRG 資源群組。由於此命令沒有使用範本，所以稍微有點複雜。

		PS C:\>New-AzureResource -Name TestSite2 `
		-Location "North Europe" `
		-ResourceGroupName TestRG `
		-ResourceType "Microsoft.Web/sites" `
		-ApiVersion 2004-04-01 `
		-PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

-   若要將新的範本型部署新增至資源群組，請使用 **New-AzureResourceGroupDeployment** 命令。

		PS C:\>New-AzureResourceGroupDeployment ` 
		-ResourceGroupName TestRG `
		-GalleryTemplateIdentity Microsoft.WebSite.0.1.0-preview1 `
		-siteName TestWeb2 `
		-hostingPlanName TestDeploy2 `
		-siteMode Limited `
		-computeMode Dedicated `
		-siteLocation "North Europe" 
		-subscriptionID "9b14a38b-4b93-4554-8bb0-3cefb47abcde" `
		-resourceGroup TestRG

-   若要將資源從資源群組中刪除，請使用 **Remove-AzureResource** Cmdlet。此 Cmdlet 會刪除資源，但不會刪除資源群組。

    此命令會將 TestSite2 網站從 TestRG 資源群組中移除。

          Remove-AzureResource -Name TestSite2 `
        -Location "North Europe" `
        -ResourceGroupName TestRG `
        -ResourceType "Microsoft.Web/sites" `
        -ApiVersion 2004-04-01

-   若要刪除資源群組，請使用 **Remove-AzureResourceGroup** Cmdlet。此 Cmdlet 會刪除資源群組及其資源。

          PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
            
        Confirm
        Are you sure you want to remove resource group 'TestRG'
        [Y] Yes  [N] No  [S] Suspend  [
          ] Help (default is "Y"):Y

疑難排解資源群組
================

在嘗試使用 AzureResourceManager 模組中的 Cmdlet 後，您很有可能會遇到錯誤。請使用本節中的秘訣來解決這些錯誤。

防止錯誤
--------

AzureResourceManager 模組包含可協助您防止錯誤的 Cmdlet。

-   **Get-AzureLocation**：此 Cmdlet 會取得支援每種資源類型的位置。在您輸入資源的位置之前，請使用此 Cmdlet 來確認該位置是否支援此資源類型。

-   **Test-AzureResourceGroupTemplate**：使用範本和範本參數之前，請先進行測試。請輸入自訂或組件庫範本，以及您打算使用的範本參數值。此 Cmdlet 會測試範本是否達到內部的一致性，以及您的參數值設定是否與範本相符。

修正錯誤
--------

-   **Get-AzureResourceGroupLog**：此 Cmdlet 會取得每個資源群組部署記錄中的項目。如果出現問題，便會首先檢查部署記錄。

-   **Verbose and Debug**：AzureResourceManager 模組中的 Cmdlet 會呼叫實際執行工作的 REST API。若要查看 API 傳回的訊息，請將 \$DebugPreference 變數設為 "Continue"，並在您的命令中使用 Verbose 命令參數。這些訊息通常會提供任何失敗原因的重要線索。

-   **您的 Azure 認證尚未設定或已過期**：若要重新整理 Windows PowerShell 工作階段中的認證，請使用 Add-AzureAccount Cmdlet。發行設定檔中的認證無法滿足 AzureResourceManager 模組中 Cmdlet 的需求。

後續步驟
========

若要深入了解如何將 Windows PowerShell 與資源管理員搭配使用：

-   [Azure 資源管理員 Cmdlet](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409)：了解如何使用 AzureResourceManager 模組中的 Cmdlet。
-   [Azure 部落格](http://blogs.msdn.com/windowsazure)：深入了解 Azure 的新功能。
-   [Windows PowerShell 部落格](http://blogs.msdn.com/powershell)：深入了解 Windows PowerShell 的新功能。
-   ["Hey, Scripting Guy!"部落格](http://blogs.technet.com/b/heyscriptingguy/)：從社群中取得實際的秘訣及訣竅。
-   [將 Azure 跨平台命令列介面與資源管理員搭配使用](http://www.windowsazure.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/)：了解自動化資源管理員操作的替代方式。

