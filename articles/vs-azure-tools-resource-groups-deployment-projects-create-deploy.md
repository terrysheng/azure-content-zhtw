<properties 
   pageTitle="建立和部署 Azure 資源群組部署專案"
   description="建立和部署 Azure 資源群組部署專案"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2015"
   ms.author="kempb" />

# 建立和部署 Azure 資源群組部署專案

安裝 Azure SDK 2.6 之後可在 Visual Studio 中使用 **Azure 資源群組**部署專案範本。Azure 資源群組專案可讓您將多個相關的 Azure 資源分組在一起，並在單一部署作業中將其發佈。Azure 資源群組專案會使用一項稱為 **Azure 資源管理員**的技術執行其工作。**Azure 資源管理員**是一項 REST API 服務，可讓您定義 Azure 資源群組，其包含多個通常會一起使用並具有類似生命週期的 Azure 資源。藉由使用資源群組，您可以利用單一函數呼叫在一個群組中的所有資源上運作，而不需為每個個別資源呼叫不同的函數。若要深入了解 Azure 資源群組，請參閱[使用資源群組管理您的 Azure 資源](./azure-portal/azure-preview-portal-using-resource-groups/)。

Azure 資源群組專案包含 Azure 資源管理員 JSON 範本，可定義部署到資源群組的項目。如需詳細資訊，請參閱 [Azure 資源管理員範本語言](resource-group-authoring-templates.md)。

Azure 資源管理員有許多不同的資源提供者可供使用，可以用來部署 Ubuntu Server 和 Windows Server 2012 R2 等資源。本主題使用 **Web Apps** 資源，該資源會將基本的空白網站部署至 Azure。

## 建立 Azure 資源群組專案

在此程序中，您會學習如何利用 **Web 應用程式**範本建立 Azure 資源群組專案。

### 建立 Azure 資源群組專案

1. 在 Visual Studio 中，選擇 [檔案]、[新專案]，選擇 [C#] 或 [Visual Basic]。然後選擇 [雲端]，然後選擇 [Azure 資源群組] 專案。

    ![雲端部署專案](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796668.png)

1. 選擇您想要部署至 Azure 資源管理員的範本。在此範例中，我們將選擇 **Web 應用程式**範本。

    ![選擇範本](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796669.png)

    稍後您也可以將更多資源加入至資源群組。

    >[AZURE.NOTE]可用範本的清單可從線上擷取，而且可能有所變更。

    Visual Studio 會建立 web 應用程式的 Azure 資源群組部署專案。

1. 展開部署專案中的節點以查看建立的內容。

    因為我們選擇此範例的 Web 應用程式範本，所以您會看到下列檔案。

|檔案名稱|說明|
|---|---|
|Deploy-AzureResourceGroup.ps1|叫用 PowerShell 命令部署至 Azure 資源管理員的 PowerShell 指令碼。

**注意** Visual studio 會使用此 PowerShell 指令碼部署您的範本。您對這個指令碼進行的任何變更也會影響 Visual Studio 中的部署，因此請謹慎。| !WebSite.json|指定您想要部署至 Azure 資源管理員之所有詳細資料的組態檔。| |WebSite.param.dev.json|包含組態檔所需之特定值的參數檔案。| |AzCopy.exe|PowerShell 指令碼用來將檔案從本機儲存體置放路徑複製到儲存體帳戶容器的工具。只有在您設定部署專案以部署您的程式碼與範本時才會使用此工具。|

所有的 Azure 資源群組部署專案都包含這四個基本檔案。其他專案可能包含其他檔案以支援其他功能。

## 自訂 Azure 資源群組專案

您可以藉由修改 JSON 範本檔案 (描述您想要部署的 Azure 資源) 來自訂部署專案。JSON 代表 JavaScript 物件標記法，而且是很容易使用的序列化資料格式。

Azure 資源群組專案在 [方案總管] 中的**範本**節點下有兩個範本檔案可供您修改：Azure 資源管理員範本檔案和參數檔案。

- **Azure 資源管理員範本檔案** (具有副檔名 .json) 指定的檔案會包含您需要的資源，以及部署專案所需之參數，例如網站名稱和位置。它們也會在 Azure 資源群組和其屬性中指定元件的相依性，例如名稱、標籤及觸發程序的規則。您可以修改這個檔案以新增您自己的功能。比方說，您無法將資料庫加入至範本。若要找出您需要提供的參數，請參考每個資源提供者的文件。如需詳細資訊，請參閱[資源提供者](https://msdn.microsoft.com/library/azure/dn790572.aspx)。

- **參數檔案** (具有副檔名 .param.*.json) 包含每個資源提供者所需之組態檔中所指定的參數值。在此範例中，web 應用程式 (WebSite.json) 的組態檔會定義 siteName 和 siteLocation 的參數。在部署期間，系統會要求您提供範本檔案中的參數值，而這些值會儲存在參數檔案中。您也可以直接編輯參數檔案。」

JSON 檔案可以在 Visual Studio 編輯器中編輯。如果您安裝 [PowerShell Tools for Visual Studio](https://visualstudiogallery.msdn.microsoft.com/c9eb3ba8-0c59-4944-9a62-6eee37294597)，則您也能夠取得語法反白顯示、括號對稱和 IntelliSense，讓讀取及編輯 PowerShell 指令碼更容易。如果尚未安裝 PowerShell 工具，編輯器頂端會出現安裝 PowerShell 工具的連結。

![安裝 PowerShell 工具](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796671.png)

JSON 檔案使用的結構描述在每個檔案的頂端提供做為參考。如果您想要更了解這個結構描述，您可以下載並分析它。結構描述會定義允許哪些項目、欄位的類型和格式、可能的列舉值等等。

如果您想要部署到不同的組態或經常變更設定，您可以建立*參數*檔案的不同複本。嘗試將相同的範本用於所有環境。

## 將 Azure 資源群組專案部署至 Azure 資源群組

當您部署 Azure 資源群組專案時，您會將它部署至 Azure 資源群組，該群組只是 Azure 中的資源邏輯分組，例如 web 應用程式、資料庫等等。

1. 在部署專案節點的捷徑功能表上，選擇 [部署]、[新增部署]。

    ![部署，新的部署功能表項目](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796672.png)

    [部署到資源群組] 對話方塊隨即出現。

    ![[部署到資源群組] 對話方塊](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796673.png)

1. 在 [資源群組] 下拉式方塊中，選擇現有的資源群組或建立一個新的。若要建立資源群組，請開啟 [資源群組] 下拉式方塊並選擇 [<Create New...>]。

    [建立資源群組] 對話方塊隨即出現。

    ![[建立資源群組] 對話方塊](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796674.png)

    >[AZURE.NOTE]通常當您開始新的部署專案時，您會想要建立新的資源群組以部署至其中。

1. 輸入資源群組的名稱和位置，然後選擇 [建立] 按鈕。

    資源群組的位置不需要和資源的位置相同，因為群組中的資源可以跨區域。

1. 選擇您想要用於此部署的範本組態和參數檔案，或接受預設值。

    您可以選擇 [編輯參數] 按鈕以編輯資源的屬性。如果在部署時遺漏必要參數，[編輯參數] 對話方塊會隨即出現，以便您可以提供參數。遺漏值的參數都有 **<null>**，會出現在它們旁邊的 [值] 方塊中。在此範例 (web 應用程式資源) 中，必要參數包含網站名稱、主控方案和網站位置。(如果您還記得，這些參數值會在參數檔案中預設為 null。) 其他參數已有預設值。

    ![[編輯參數] 對話方塊](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796675.png)

1. 在 [編輯參數] 對話方塊中，輸入網站名稱、網站位置、主控方案名稱並檢查任何其他屬性的值。完成時，請選擇 [儲存] 按鈕。

    - *siteName* 參數是網頁 URL 的第一個部分。例如，對於 URL mywebsitename.azurewebsites.net 而言，網站名稱是 mywebsitename。

    - *hostingPlanName* 參數會指定您的主控方案。在此範例中，您可以使用「免費」。如需主控方案的詳細資訊，請參閱 [Azure 網站主控方案深入概觀](http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/)。

    - *siteLocation* 參數是指裝載網站所在的 Azure 區域，例如「美國西部」。如需可用區域的清單，請參閱 [Azure 區域](http://azure.microsoft.com/regions/)。

1. 選擇 [部署] 按鈕以將專案部署至 Azure。

    您可在 [**輸出**] 視窗中查看部署的進度。部署可能需要幾分鐘才能完成，取決於您的組態。

    >[AZURE.NOTE]系統可能會要求您安裝 Microsoft Azure PowerShell cmdlet。因為要有這些 cmdlet 才能部署 Azure 資源群組，所以您需要安裝它們。

1. 在瀏覽器中，開啟 [Azure 入口網站](https://portal.azure.com/)。由於這是新的變更，[通知] 索引標籤中應該會提供新的通知訊息。選擇它以檢視新 Azure 資源群組的詳細資料。若要查看所有可用資源群組的清單，您可以選擇 [瀏覽] 索引標籤，然後選擇 [資源群組]。

    ![佈建的 Azure 資源群組](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796676.png)

1. 如果您進行變更並想要重新部署您的專案，您可以直接從 Azure 資源群組專案的捷徑功能表選擇現有的資源群組。在捷徑功能表上，選擇 [部署]，然後選擇您剛部署至其中的資源群組。

    ![已部署 Azure 資源群組](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796677.png)

    部署 Azure 資源群組只會部署該專案。如果您的方案有程式碼專案或其他任何專案，您必須將它們個別部署。

## 透過 Azure SDK 2.6 使用 Azure SDK 2.5 雲端部署專案

如果您使用以 Azure SDK 2.5 建立的雲端部署專案，您會想要升級為 Azure SDK 2.6，才能夠使用編輯和部署 Azure 資源範本的新功能。若要重複使用您利用 Azure SDK 2.5 建立的範本，最簡單的方式是建立專案的 Azure SDK 2.6 版本、將您的範本移至該專案並進行一些修改。

### 透過 Azure SDK 2.6 使用 Azure SDK 2.5 雲端部署專案

1. 將新的 Azure SDK 2.6 Azure 資源群組專案新增到您的方案。

    1. 開啟包含 Azure SDK 2.5 雲端部署專案的方案。

    1. 從 [檔案] 功能表，選擇 [新增]、[專案]。

    1. 在 [新增專案] 對話方塊中，尋找 ****Visual C#**/**雲端**下的 Azure 資源群組**或 **Visual Basic**/**雲端**。
    
         專案範本名稱已經從**雲端部署**變更為 Azure 資源群組。

    1. 提供名稱給專案。

    1. 將 [方案] 下拉式方塊變更為 [新增至方案]。

    1. 接下來，系統會要求您挑選範本。由於您會從 Azure SDK 2.5 雲端部署專案移動現有的範本，所以您可以選擇任何範本，讓我們選擇清單底部的空白範本。

    1. 選擇 [確定] 按鈕。
    
        新的專案會新增至您的方案。

1. 將您的範本和參數檔案從 Azure SDK 2.5 雲端部署專案複製到您的 Azure SDK 2.6 資源群組專案。

    1. 在 [方案總管] 中，尋找您想要在 Azure SDK 2.5 部署專案中複製的範本和參數檔案，並將其選取與複製。
    
    2. 將檔案貼到新的 Azure SDK 2.6 資源群組專案中的**範本**資料夾。

1. 在 [方案總管] 中，尋找您想要在 Azure SDK 2.5 部署專案中複製的範本和參數檔案，並將其選取與複製。

1. 將檔案貼到新的 Azure SDK 2.6 資源群組專案中的範本資料夾。

1. 如果您也要利用範本部署 web 應用程式，您必須從新的 Azure SDK 2.6 資源群組專案建立 Web 應用程式的參考。

    1. 在 [方案總管] 中的新 Azure SDK 2.6 資源群組專案的**參考**節點內容功能表中，選擇 [加入參考]。

    1. 在專案清單中選取 Web 應用程式旁的方塊，然後選擇 [確定] 按鈕。

1. 在 [方案總管] 中的新 Azure SDK 2.6 資源群組專案的參考節點內容功能表中，選擇 [加入參考]。

1. 在專案清單中選取 Web 應用程式旁的方塊，然後選擇 [確定] 按鈕。

1. 將所有出現的參數 *dropLocation* 和 *dropLocationSasToken* 重新命名為 *\_artifactsLocation* 和 *\_artifactsLocationSasToken*。

1. 如果您不打算使用它們，您可以將建立 Azure SDK 2.6 專案時自動加入專案的空白範本和參數檔案刪除。

    1. 刪除檔案 DeployTemplate.json。

    1. 刪除檔案 DeploymentTemplate.param.dev.json。

1. 如果您已在 Azure SDK 2.5 專案中變更 Publish-AzureResourceGroup.ps1 指令碼，您必須將這些變更移至 Azure SDK 2.6 專案中的 Deploy-AzureResourceGroup.ps1 指令碼。

    現在您可以使用部署命令在 Azure SDK 2.6 Azure 資源群組專案中部署範本，並利用 Azure SDK 2.6 中編輯範本的新功能。一旦您如願開始使用 2.6 專案之後，您就可以從方案移除 Azure SDK 2.5 專案。

## 專案需要更新的理由

在範本中所做的一些變更會部署於 Azure SDK 2.6 中，使 Azure SDK 2.5 部署指令碼和範本不相容。第一個也是最大的變更，就是啟動的部署。在 Azure SDK 2.5 中，我們已編譯使用 Azure REST API 的程式碼，以上傳您的範本並開始部署。我們聽到許多開發人元的意見回應，他們希望 Visual Studio 只啟動包含在專案中的 PowerShell 指令碼。因此，在 Azure SDK 2.6 中，部署命令會啟動包含在專案中的 PowerShell 指令碼以部署範本。這樣可讓您自訂部署並且讓自訂內容一律執行，無論您從命令列使用 Azure PowerShell 部署或透過使用部署命令的 VS 部署皆可執行。若要從 Visual Studio 部署，您必須在安裝 Azure SDK 2.6 時使用 Azure SDK 2.6 部署 PowerShell 指令碼。

某些變數名稱和建置工作也會進行調整，使其更符合 TFS 自動化組建和 Microsoft 內其他專案中的命名慣例。Visual Studio 中的程式碼 (會收集啟動 PowerShell 指令碼所需的變數與值) 會尋找這些新的名稱。

## 後續步驟

若要了解如何將資源新增至 Visual Studio 中的 Azure 資源群組，請參閱[將資源新增至 Azure 資源群組](https://msdn.microsoft.com/library/azure/mt125415.aspx)。

<!---HONumber=Sept15_HO2-->