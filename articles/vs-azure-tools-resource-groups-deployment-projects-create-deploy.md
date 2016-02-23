<properties
   pageTitle="建立和部署 Azure 資源群組 Visual Studio 專案 | Microsoft Azure"
   description="使用 Visual Studio 建立 Azure 資源群組專案，並將資源部署至 Azure。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor="" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="tomfitz" />

# 透過 Visual Studio 建立與部署 Azure 資源群組

使用 Visual Studio 和 [Azure SDK](https://azure.microsoft.com/downloads/)，您可以建立專案，將您的基礎結構和程式碼部署至 Azure。例如，您可以為您的應用程式定義 Web 主機、網站和資料庫，並且部署該基礎結構與程式碼。或者，您可以定義虛擬機器、虛擬網路和儲存體帳戶，並且部署該基礎結構以及在虛擬機器上執行的指令碼。**Azure 資源群組**部署專案可以在單一、可重複執行作業中部署所有所需的資源。如需部署與管理資源的詳細資訊，請參閱 [Azure 資源管理員概觀](resource-group-overview.md)。

Azure 資源群組專案包含 Azure 資源管理員 JSON 範本，可定義部署到 Azure 的資源。若要了解資源管理員範本的元素，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。Visual Studio 可讓您編輯這些範本，並提供工具，可簡化範本的使用。

在本主題中，您將部署 Web 應用程式和 SQL Database。不過，任何類型資源的步驟幾乎相同。您可以輕鬆地部署虛擬機器和其相關的資源。Visual Studio 針對部署常見案例提供許多不同的入門範本。

## 建立 Azure 資源群組專案

在此程序中，您會利用 **Web 應用程式 + SQL** 範本建立 Azure 資源群組專案。

1. 在 Visual Studio 中，選擇 [檔案]、[新增專案]，再選擇 [C#] 或 [Visual Basic]。然後選擇 [雲端]，再選擇 [Azure 資源群組] 專案。

    ![雲端部署專案](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796668.png)

1. 選擇您想要部署至 Azure 資源管理員的範本。請注意，根據您想要部署的專案類型，有許多不同的選項。在此主題中，我們將選擇 [Web 應用程式 + SQL] 範本。

    ![選擇範本](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    您選擇的範本只是起點；您可以加入和移除資源，以滿足您的案例。

    >[AZURE.NOTE] 可用範本的清單可從線上擷取，而且可能有所變更。

    Visual Studio 會建立 web 應用程式和 SQL Database 的資源群組部署專案。

1. 展開部署專案中的節點以查看建立的內容。

    ![顯示節點](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    因為我們針對此範例選擇 Web 應用程式 + SQL 範本，所以您會看到下列檔案。

    |檔案名稱|說明|
    |---|---|
    |Deploy-AzureResourceGroup.ps1|叫用 PowerShell 命令來部署至 Azure 資源管理員的 PowerShell 指令碼。<br />**注意**此 PowerShell 指令碼可供 Visual Studio 用來部署您的範本。對此指令碼所進行的任何變更也會影響 Visual Studio 中的部署，因此請務必小心。|
    |WebSiteSQLDatabase.json|資源管理員範本 (定義您想要部署至 Azure 的基礎結構)，以及在部署期間您可以提供的參數。同時定義資源之間的相依性，以便以正確的順序部署資源。|
    |WebSiteSQLDatabase.parameters.json|參數檔案，包含範本所需的值。這些是您傳遞以自訂每個部署的值。|
    |AzCopy.exe|供 PowerShell 指令碼用來將檔案從本機儲存體置放路徑複製到儲存體帳戶容器的工具。只有在您設定部署專案以部署您的程式碼與範本時才會使用此工具。|

    所有資源群組部署專案都包含這四個基本檔案。其他專案可能包含其他檔案以支援其他功能。

## 自訂資源管理員範本

您可以藉由修改 JSON 範本 (描述您想要部署的資源) 來自訂部署專案。JSON 代表 JavaScript 物件標記法，而且是很容易使用的序列化資料格式。JSON 檔案使用的結構描述在每個檔案的頂端提供做為參考。如果您想要更了解這個結構描述，您可以下載並分析它。結構描述會定義允許哪些項目、欄位的類型和格式、可能的列舉值等等。若要了解資源管理員範本的元素，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。

若要使用您的範本，請開啟 **WebSiteSQLDatabase.json**。

Visual Studio 編輯器提供工具，協助您編輯資源管理員範本。[JSON 大綱] 視窗可讓您輕鬆查看在您的範本中定義的元素。

![顯示 JSON 大綱](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

選取大綱中的任何元素，會帶您前往範本的該部分，並且醒目提示對應的 JSON。

![瀏覽 JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

您可以藉由選取 [JSON 大綱] 視窗頂端的 [加入資源] 按鈕，或以滑鼠右鍵按一下 [資源] 然後選取 [加入新資源]，將新資源加入您的範本。

![新增資源](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

在本教學課程中，選取 [儲存體帳戶] 並提供它的名稱。儲存體帳戶名稱只能使用數字和小寫字母，且長度小於 24 個字元。專案會在您提供的名稱上加入 13 個字元唯一字串，以確定您的名稱不超過 11 個字元。

![加入儲存體](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

請注意，不只是加入資源，也會加入儲存體帳戶類型的參數，以及儲存體帳戶名稱的變數。

![顯示大綱](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

**WebSitePicturesType** 參數是以允許的類型與預設類型預先定義。您可以保留這些值，或針對您的案例進行編輯。如果您不想要允許任何人透過此範本部署 **Premium\_LRS** 儲存體帳戶，只要將其從允許的類型中移除即可，如下所示。

    "WebSitePicturesType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS"
      ]
    }

Visual Studio 也會提供 Intellisense 以協助您了解編輯範本時可以使用哪些屬性。例如，若要編輯您的 App Service 計劃的屬性，請瀏覽至 **HostingPlan** 資源，並針對 [屬性] 加入新值。請注意，Intellisense 會顯示可用的值，並提供該值的描述。

![顯示 intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

您可以將 **numberOfWorkers** 設定為 1。

    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }

## 將資源群組部署至 Azure

您現在已可開始部署您的專案。當您部署 Azure 資源群組專案時，您會將它部署至 Azure 資源群組，該群組只是 Azure 中的資源邏輯分組，例如 web 應用程式、資料庫等等。

1. 在部署專案節點的捷徑功能表上，選擇 [部署]、[新增部署]。

    ![部署，新的部署功能表項目](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796672.png)

    [部署到資源群組] 對話方塊隨即出現。

    ![[部署到資源群組] 對話方塊](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. 在 [資源群組] 下拉式方塊中，選擇現有資源群組或建立新群組。若要建立資源群組，請開啟 [資源群組] 下拉式方塊，然後選擇 **<Create New...>**。

    ![[部署到資源群組] 對話方塊](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)

    [建立資源群組] 對話方塊隨即出現。給予群組名稱與位置，然後選取 [建立] 按鈕。

    ![[建立資源群組] 對話方塊](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
   
1. 您可以選擇 [編輯參數] 按鈕以編輯部署的參數。提供參數的值，然後選取 [儲存] 按鈕。

    ![[編輯參數] 對話方塊](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
    
    [儲存密碼] 選項表示密碼會在 JSON 檔案中儲存為純文字。此選項並不安全。

1. 選擇 [部署] 按鈕以將專案部署至 Azure。您可在 [輸出] 視窗中查看部署的進度。部署可能需要幾分鐘才能完成，取決於您的組態。如果出現提示，輸入資料庫系統管理員密碼。

    >[AZURE.NOTE] 系統可能會要求您安裝 Azure PowerShell Cmdlet。因為要有這些 cmdlet 才能部署 Azure 資源群組，所以您需要安裝它們。
    
1. 當部署完成時，您應該會在 [輸出] 視窗中看到如下的訊息：

        ...
        15:19:19 - DeploymentName     : websitesqldatabase-0212-2318
        15:19:19 - CorrelationId      : 6cb43be5-86b4-478f-9e2c-7e7ce86b26a2
        15:19:19 - ResourceGroupName  : DemoSiteGroup
        15:19:19 - ProvisioningState  : Succeeded
        ...

1. 在瀏覽器中，開啟 [Azure 入口網站](https://portal.azure.com/)並登入您的帳戶。若要查看資源群組，請選取 [資源群組]，然後選取您部署所在的資源群組。

    ![選取群組](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)

1. 您會看到所有已部署的資源。

    ![顯示資源](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

1. 如果您進行變更並想要重新部署您的專案，您可以直接從 Azure 資源群組專案的捷徑功能表選擇現有的資源群組。在捷徑功能表上，選擇 [部署]，然後選擇您剛部署至其中的資源群組。

    ![已部署 Azure 資源群組](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## 以您的基礎結構部署程式碼

此時，您已為您的應用程式部署基礎結構，但是專案尚未部署實際程式碼。本主題說明如何在部署期間部署 Web 應用程式和 SQL Database 資料表。如果您是部署虛擬機器而不是 Web 應用程式，您想要在機器上執行一些程式碼做為部署的一部分。部署 Web 應用程式的程式碼或設定虛擬機器的程序幾乎完全相同。

1. 在 Visual Studio 解決方案中，加入 **ASP.NET Web 應用程式**。 

    ![加入 Web 應用程式](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
    
1. 選取 [MVC] 並且清除 [雲端中的主機] 欄位，因為資源群組專案會執行這項工作。

    ![選取 MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
    
1. 建立 Web 應用程式之後，將資源群組專案中的參考加入至 Web 應用程式專案。

    ![加入參考](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
    
    加入參考，您可以將 Web 應用程式專案連結至資源群組專案中，並設定三個重要屬性。[其他屬性] 包含 Web 部署封裝預備位置，將會推送至 Azure 儲存體。[包含檔案路徑] 包含建立封裝所在的路徑。[包含目標] 包含部署將會執行的命令。預設值 [建立封裝] 會讓部署建立 Web 部署封裝 (package.zip)。不需要發佈設定檔，因為部署會從屬性取得必要的資訊來建立封裝。
    
      ![查看參考](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
      
1. 將新的資源加入範本，這次請選取 [Web Deploy for Web Apps]。

    ![加入 Web 部署](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
    
1. 將您的資源群組專案重新部署至資源群組。這次還有一些新的參數。您不需要提供 **\_artifactsLocation** 或 **\_artifactsLocationSasToken** 的值，它們會自動產生。將資料夾和檔案名稱設為包含部署封裝的路徑。

    ![加入 Web 部署](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
    
    對於 [構件儲存體帳戶]，您可以搭配已部署的項目與此資源群組一起使用。
    
部署完成之後，您可以瀏覽至網站，並且注意到預設的 ASP.NET 應用程式已經部署。

![顯示已部署的應用程式](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## 後續步驟

- 若要了解透過入口網站管理資源，請參閱[使用 Azure 入口網站來管理您的 Azure 資源](./azure-portal/resource-group-portal.md)。
- 若要了解範本，請參閱[撰寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。

<!---HONumber=AcomDC_0218_2016-->