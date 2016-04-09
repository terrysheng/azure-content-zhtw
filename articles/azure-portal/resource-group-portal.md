<properties 
	pageTitle="使用 Azure 入口網站管理 Azure 資源 | Microsoft Azure" 
	description="使用 Azure 入口網站和 Azure Resource Manager 來部署及管理資源。示範如何標記資源和檢視稽核記錄檔。" 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="03/29/2016" 
	ms.author="tomfitz"/>


# 使用 Azure 入口網站來部署及管理 Azure 資源

## 簡介

本主題示範如何使用 [Azure 入口網站](https://portal.azure.com)搭配 [Azure Resource Manager](../resource-group-overview.md) 來部署和管理您的 Azure 資源。

目前並非所有服務都支援入口網站或資源管理員。針對這些服務，您必須使用[傳統入口網站](https://manage.windowsazure.com)。如需每個服務的狀態，請參閱 [Azure 入口網站可用性圖表](https://azure.microsoft.com/features/azure-portal/availability/)。

您也可以透過 Azure PowerShell 和 Azure CLI 來管理資源。如需有關使用這些介面的詳細資訊，請參閱[搭配使用 Azure PowerShell 和 Azure Resource Manager](../powershell-azure-resource-manager.md) 及[搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure Resource Manager](../xplat-cli-azure-resource-manager.md)。如需有關如何透過 Visual Studio 部署解決方案的詳細資訊，請參閱[透過 Visual Studio 建立和部署 Azure 資源群組](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

## 建立和管理資源群組

若要建立空的資源群組，請選取 [新增]、[管理] 和 [資源群組]。

![建立空的資源群組](./media/resource-group-portal/create-empty-group.png)

您可提供其名稱和位置，再視需要選取訂用帳戶。

![設定群組值](./media/resource-group-portal/set-group-properties.png)

建立資源群組之後，您可以資源部署至該群組。若要開始部署，只需選取 [新增] 以及您想要部署的資源類型。

![部署資源](./media/resource-group-portal/deploy-resource.png)

如果看不到您想要部署的資源類型，您可以在 Marketplace 中進行搜尋。

![搜尋 Marketplace](./media/resource-group-portal/search-resource.png)

根據您選取的資源類型，您必須在部署前設定相關的屬性集合。這些選項並未在這裡顯示，因為它們會隨著資源類型而有所不同。對於所有類型，您必須選取目的地資源群組。下圖顯示如何建立新的 Web 應用程式並將它部署至您剛建立的資源群組。

![建立資源群組](./media/resource-group-portal/select-existing-group.png)

或者，您也可以決定在部署資源時建立新的資源群組。請選取 [新增] 並提供資源群組的名稱，而不是在訂用帳戶中選取其中一個現有的資源群組。

![建立新的資源群組](./media/resource-group-portal/select-new-group.png)

您的部署即將開始。這可能需要幾分鐘的時間。部署完成後，您就會看到通知。

![檢視通知](./media/resource-group-portal/view-notification.png)

### 將資源新增至現有的資源群組

您可以在資源群組刀鋒視窗上使用 **Add** 命令，將資源新增至資源群組。

![新增資源](./media/resource-group-portal/add-resource.png)

您可以從可用的清單中選取您要的資源。

### 瀏覽資源群組

您可以按一下 [資源群組] 來瀏覽所有資源群組。

![瀏覽資源群組](./media/resource-group-portal/browse-groups.png)

當您選取特定的資源群組時，您會看到資源群組刀鋒視窗，其中提供該資源群組的相關資訊，包括群組中所有資源的清單。

![資源群組摘要](./media/resource-group-portal/group-summary.png)

選取摘要下方的 [新增區段]，即可將更多圖形和資料表新增至資源群組刀鋒視窗。

![新增區段](./media/resource-group-portal/add-section.png)

您會看到圖格資源庫，以便選取您要包含在刀鋒視窗中的資訊。顯示的圖格類型會依資源類型篩選。選取不同的資源將會變更可用的圖格。

![新增區段](./media/resource-group-portal/tile-gallery.png)

將您需要的圖格拖曳至可用的空間。

![拖曳圖格](./media/resource-group-portal/drag-tile.png)

選取入口網站頂端的 [完成] 之後，新的檢視就是刀鋒視窗的一部分。

![顯示圖格](./media/resource-group-portal/show-lens.png)

若要快速存取資源群組，您可以將此刀鋒視窗釘選到您的儀表板。

![釘選資源群組](./media/resource-group-portal/pin-group.png)

或者，您可以選取區段上方的省略符號 (...)，將此刀鋒視窗的區段釘選到您的儀表板。您也可以自訂刀鋒視窗中的區段大小或完全予以移除。下圖顯示如何釘選、自訂或移除 [CPU 和記憶體] 區段。

![釘選區段](./media/resource-group-portal/pin-cpu-section.png)

將此區段釘選到儀表板之後，您會在儀表板上看見摘要。

![檢視儀表板](./media/resource-group-portal/view-startboard.png)

而且，立即加以選取，您即可看到更多有關資料的詳細資訊。

### 刪除資源群組

由於資源群組可讓您管理所有內含資源的生命週期，因此若刪除資源群組，將一併刪除群組內包含的所有資源。您也可以刪除資源群組內的個別資源。在刪除資源群組時應多加留意，因為可能會有其他資源連結至該群組。您可以檢視資源對應中的連結資源，並執行必要的步驟，以避免在刪除資源群組時出現非預期的結果。不會刪除連結的資源，但它們可能無法如預期般運作。

![刪除群組](./media/resource-group-portal/delete-group.png)


## 檢視過去的部署

在資源群組刀鋒視窗內，您可以看見此資源群組上次部署的日期和狀態。選取連結，即可顯示群組的部署歷程記錄。

![上次部署](./media/resource-group-portal/last-deployment.png)

選取歷程記錄中的任何部署，即可顯示有關該部署的詳細資訊。

![部署摘要](./media/resource-group-portal/deployment-summary.png)

您可以看到在部署期間執行的個別作業。下圖顯示一個成功作業和一個失敗作業。

![作業詳細資料](./media/resource-group-portal/operation-details.png)

如需有關針對部署進行疑難排解的詳細資訊，請參閱[透過 Azure 入口網站針對資源群組部署進行疑難排解](../resource-manager-troubleshoot-deployments-portal.md)。

選取 [匯出範本]，即可擷取用於部署的範本。

![匯出範本](./media/resource-group-portal/export-template.png)

您會看到此部署所用的確切範本。

![顯示範本](./media/resource-group-portal/show-template.png)

這不是資源群組的完整呈現；如果您在此部署以外新增或刪除了資源，這些動作並不會反映在範本中。此刀鋒視窗包含範本、要搭配此範本使用的參數檔案，以及用來部署範本的 PowerShell 指令碼。選取 [儲存至檔案]，即可下載這 3 個檔案。

## 檢視稽核記錄檔

稽核記錄檔不只包含部署作業，還包含對您的訂用帳戶中的資源進行的所有管理作業。例如，您可以在稽核記錄檔中查看貴組織中的某個人停止應用程式的時間。若要查看稽核記錄檔，請選取 [**全部瀏覽**] 和 [**稽核記錄檔**]。

![瀏覽稽核記錄檔](./media/resource-group-portal/browse-audit-logs.png)

在 [作業] 區段中，您可以查看您的訂用帳戶中已執行的個別作業。

![檢視稽核記錄檔](./media/resource-group-portal/view-audit-log.png)

選取任何作業，您可以看到更多詳細資訊，包括哪個使用者執行此作業。

如需檢視稽核記錄檔的詳細資訊，請參閱[使用 Resource Manager 來稽核作業](../resource-group-audit.md)。

## 標記資源

您可以將標籤套用至資源群組和資源，以便以邏輯方式組織您的資產。如需有關透過入口網站使用標記的資訊，請參閱[使用標記來組織您的 Azure 資源](../resource-group-using-tags.md)。

## 部署自訂範本

如果您想要執行部署，但不使用 Marketplace 中中的任何範本，您可建立自訂範本以定義您的解決方案的基礎結構。如需有關範本的詳細資訊，請參閱[編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

若要透過入口網站來部署自訂的範本，請選取 [新增]，開始搜尋 [範本部署]，直到您可以從選項中選取它為止。

![搜尋範本部署](./media/resource-group-portal/search-template.png)

從可用的資源中選取 [範本部署]。

![選取範本部署](./media/resource-group-portal/select-template.png)

啟動範本部署之後，您可以建立自訂範本及設定部署的值。

![建立範本](./media/resource-group-portal/show-custom-template.png)

## 檢視訂用帳戶和成本

您可以檢視訂用帳戶的相關資訊和所有資源的彙總成本。選取 [訂用帳戶] 及您想要查看的訂用帳戶。您可能只有一個訂用帳戶可選取。

![訂用帳戶)](./media/resource-group-portal/select-subscription.png)

在訂用帳戶刀鋒視窗內，您會看到完工速率。

![完工速率](./media/resource-group-portal/burn-rate.png)

還有依資源類型的成本分析。

![資源成本](./media/resource-group-portal/cost-by-resource.png)

## Azure 儀表板的存取控制

入口網站中大多數圖格所顯示資訊的存取權是由 Azure [角色型存取控制](../active-directory/role-based-access-control-configure.md)所控管。為了將儀表板完美地整合到生態系統中，所有已發佈的儀表板會當作 Azure 資源實作。從存取控制觀點來看，儀表板與虛擬機器或儲存體帳戶並無不同。

範例如下。假設您有 Azure 訂用帳戶，而且您小組的各種成員已被指派**擁有者**、**參與者**或**讀者**角色。身為擁有者或參與者的使用者將能夠列出、檢視、建立、修改或刪除訂用帳戶內的儀表板。身為讀者的使用者將能夠列出和檢視儀表板，但無法進行修改或刪除。具有讀者存取權的使用者將能夠對已發佈的儀表板進行本機編輯 (例如在針對問題進行疑難排解時)，但不會提供將這些變更發佈回到伺服器的選項。他們會有為自己製作儀表板的私人複本的選項。

請注意，儀表板上的個別圖格將會根據其顯示資料的資源，強制執行自己的存取控制需求。這表示您可以設計一個更廣泛共用而同時仍會保護個別圖格上資料的儀表板。

## 後續步驟

- 如需資源管理員中的概念簡介，請參閱 [Azure 資源管理員概觀](../resource-group-overview.md)。
- 如需部署資源時使用 Azure PowerShell 的簡介，請參閱[將 Azure PowerShell 與 Azure 資源管理員搭配使用](../powershell-azure-resource-manager.md)。
- 如需部署資源時使用 Azure CLI 的簡介，請參閱[使用適用於 Mac、Linux 和 Windows 的 Azure CLI 搭配 Azure 資源管理](../xplat-cli-azure-resource-manager.md)。

<!---HONumber=AcomDC_0330_2016-->