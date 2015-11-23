<properties 
	pageTitle="使用 Azure Preview 入口網站管理 Azure 資源 | Microsoft Azure" 
	description="將多個資源分組為邏輯群組，使該邏輯群組成為所含之資源的生命週期界限。" 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/11/2015" 
	ms.author="tomfitz"/>


# 使用 Azure Preview 入口網站管理 Azure 資源

## 簡介

過去在 Microsoft Azure 中管理資源 (例如資料庫伺服器、資料庫或 Web 應用程式) 時，您必須逐一對每項資源執行作業。如果您有某個應用程式是由多項資源所組成，您必須將變更手動調節至應用程式基礎結構。在 Azure Preview 入口網站中，您可以使用 Azure 資源管理員建立資源群組，以同時部署和管理某個應用程式中的所有資源。

一般而言，一個資源群組包含一個特定應用程式的相關資源。例如，一個群組可能會包含一個代管公用網站的 Web 應用程式、一個儲存網站所使用之關聯式資料的 SQL Database，和一個儲存非關聯式資產的儲存體帳戶。資源群組中的每項資源應共用相同的存留期。如需資源管理員的詳細資訊，請參閱[資源管理員概觀](../resource-group-overview.md)。

本主題簡單說明如何在 Azure Preview 入口網站中使用資源群組。目前不是每個服務都支援 Preview 入口網站或資源管理員。針對這些服務，您必須使用[入口網站](https://manage.windowsazure.com)。如需每個服務的狀態，請參閱[資源管理員對於服務、區域和 API 版本的支援](resource-manager-supported-services.md)

## 建立資源群組和資源

如果您需要建立空的資源群組，您可以選取 [新增]、[管理] 和 [資源群組]。

![建立空的資源群組](./media/resource-group-portal/create-empty-group.png)

您可提供其名稱和位置，再視需要選取訂用帳戶。

![設定群組值](./media/resource-group-portal/set-group-properties.png)

不過，您不需要明確建立空的資源群組。您可以在建立新群組時，選擇建立新的資源群組，或使用現有的資源群組。下圖顯示如何利用選取現有資源群組或建立新資源群組的選項來建立新的 Web 應用程式。

![建立資源群組](./media/resource-group-portal/select-existing-group.png)

## 瀏覽資源群組

按一下 [全部瀏覽] 和 [資源群組]，即可瀏覽所有資源群組。

![瀏覽資源群組](./media/resource-group-portal/browse-groups.png)

當您選取特定的資源群組時，您會看到資源群組刀鋒視窗，其中提供該資源群組的相關資訊，包括群組中所有資源的清單。

![資源群組摘要](./media/resource-group-portal/group-summary.png)

此資源群組刀鋒視窗也會為您提供資源群組中各項資源的計費和監視資訊的統合檢視。

![監視和計費](./media/resource-group-portal/monitoring-billing.png)

## 自訂介面

若要快速存取資源群組摘要，您可以將此刀鋒視窗釘選到「開始面板」。

![釘選資源群組](./media/resource-group-portal/pin-group.png)

或者，您可以選取區段上方的省略符號 (...)，將此刀鋒視窗的區段釘選到「開始面板」。您也可以自訂刀鋒視窗中的區段大小或完全予以移除。下圖顯示如何釘選、自訂或移除 [事件] 區段。

![釘選區段](./media/resource-group-portal/pin-section.png)

將 [事件] 區段釘選到「開始面板」之後，您會在「開始面板」上看見事件的摘要。

![事件開始面板](./media/resource-group-portal/events-startboard.png)

而且，立即加以選取，您即可看到更多有關事件的詳細資訊。

## 檢視過去的部署

在資源群組刀鋒視窗內，您可以看見此資源群組上次部署的日期和狀態。選取連結，即可顯示群組的部署歷程記錄。

![上次部署](./media/resource-group-portal/last-deployment.png)

選取歷程記錄中的任何部署，即可顯示有關該部署的詳細資訊。

![部署摘要](./media/resource-group-portal/deployment-summary.png)

您可以看到在部署期間執行的個別作業。下圖顯示一個成功作業和一個失敗作業。

![作業詳細資料](./media/resource-group-portal/operation-details.png)

選取任何作業，即可顯示更多有關作業的詳細資訊。這在作業失敗時特別有用，如下所示。它可以協助您疑難排解部署失敗的原因。在下圖中，您可以看到因為名稱並非唯一而未部署網站。

![作業訊息](./media/resource-group-portal/operation-message.png)

## 檢視稽核記錄檔

稽核記錄檔不只包含部署作業，還包含對您的訂用帳戶中的資源進行的所有管理作業。例如，您可以在稽核記錄檔中查看貴組織中的某個人停止應用程式的時間。若要查看稽核記錄檔，請選取 [全部瀏覽] 和 [稽核記錄檔]。

![瀏覽稽核記錄檔](./media/resource-group-portal/browse-audit-logs.png)

在 [作業] 區段中，您可以查看您的訂用帳戶中已執行的個別作業。

![檢視稽核記錄檔](./media/resource-group-portal/view-audit-log.png)

選取任何作業，您可以看到更多詳細資訊，包括哪個使用者執行此作業。

選取 [篩選]，即可篩選稽核記錄檔中所顯示的內容。

![篩選記錄檔](./media/resource-group-portal/filter-logs.png)

您可以選取在指定的時間範圍內要顯示的作業類型、其所屬的資源群組或資源、由特定的呼叫端所起始，或作業的層級。

![篩選選項](./media/resource-group-portal/filter-options.png)

## 將資源新增至資源群組

您可以在資源群組刀鋒視窗上使用 **Add** 命令，將資源新增至資源群組。

![新增資源](./media/resource-group-portal/add-resource.png)

您可以從可用的清單中選取您要的資源。

## 刪除資源群組

由於資源群組可讓您管理所有內含資源的生命週期，因此若刪除資源群組，將一併刪除群組內包含的所有資源。您也可以刪除資源群組內的個別資源。在刪除資源群組時應多加留意，因為可能會有其他資源連結至該群組。您可以檢視資源對應中的連結資源，並執行必要的步驟，以避免在刪除資源群組時出現非預期的結果。不會刪除連結的資源，但它們可能無法如預期般運作。

![刪除群組](./media/resource-group-portal/delete-group.png)

## 標記資源

您可以將標籤套用至資源群組和資源，以便以邏輯方式組織您的資產。如需透過 Preview 入口網站使用標記的詳細資訊，請參閱[使用標記來組織您的 Azure 資源](../resource-group-using-tags.md)。

## 部署自訂範本

如果您想要執行部署，但不使用 Marketplace 中中的任何範本，您可建立自訂範本以定義您的解決方案的基礎結構。如需範本的詳細資訊，請參閱[編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

若要透過 Preview 入口網站部署自訂的範本，請選取 [新增]、[Marketplace] 和 [所有項目]。

![尋找範本部署](./media/resource-group-portal/launch-template.png)

搜尋 [範本部署]，然後從傳回的清單中選取。

![搜尋範本部署](./media/resource-group-portal/search-template.png)

啟動範本部署之後，您可以建立自訂範本及設定部署的值。

![建立範本](./media/resource-group-portal/show-custom-template.png)

## 後續步驟
開始使用

- 如需資源管理員中的概念簡介，請參閱 [Azure 資源管理員概觀](../resource-group-overview.md)。  
- 如需部署資源時使用 Azure PowerShell 的簡介，請參閱[搭配使用 Azure PowerShell 與 Azure 資源管理員](../powershell-azure-resource-manager.md)。
- 如需部署資源時使用 Azure CLI 的簡介，請參閱[搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理](../xplat-cli-azure-resource-manager.md)。 
  


 

<!---HONumber=Nov15_HO3-->