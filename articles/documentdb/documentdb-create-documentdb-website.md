<properties 
	pageTitle="使用 Azure 資源管理員範本部署 DocumentDB 和 Azure App Service Web Apps | Azure" 
	description="了解如何使用 Azure 資源管理員範本的部署 DocumentDB 帳戶、Azure App Service Web Apps，以及範例 Web 應用程式。" 
	services="documentdb, app-service\web" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="stbaro"/>

# 使用 Azure 資源管理員範本部署 DocumentDB 和 Azure App Service Web Apps #

本教學課程示範如何使用 Azure 資源管理員範本來部署和整合 [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/)、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)、Web 應用程式及範例 Web 應用程式。

完成本教學課程後，您將能夠回答下列問題：

-	如何使用 Azure 資源管理員範本來部署和整合 DocumentDB 帳戶與 Azure App Service 中的 Web 應用程式？
-	如何使用 Azure 資源管理員範本來部署和整合 DocumentDB 帳戶、App Service Web Apps 中的 Web 應用程式，以及 Webdeploy 應用程式？

<a id="Prerequisites"></a>
## 必要條件 ##
> [AZURE.TIP]雖然本教學課程不會假設先前有使用 Azure 資源管理員範本、JSON 或 Azure PowerShell 的經驗，但是，如果您想要修改參考的範本或部署選項，則需要每個領域的知識。

在依照本教學課程中的指示進行之前，請先確定您已備妥下列項目：

- Azure 訂用帳戶。Azure 是訂用帳戶型平台。如需取得訂用帳戶的詳細資訊，請參閱[購買選項](http://azure.microsoft.com/pricing/purchase-options/)、[成員優惠](http://azure.microsoft.com/pricing/member-offers/)或[免費試用](http://azure.microsoft.com/pricing/free-trial/)。
- 一個 Azure 儲存體帳戶。如需相關指示，請參閱[關於 Azure 儲存體帳戶](../storage-whatis-account.md)。
- 具有 Azure PowerShell 的工作站。如需指示，請參閱[安裝並設定 Azure PowerShell](../install-configure-powershell.md)。

##<a id="CreateDB"></a>步驟 1：下載並解壓縮範例檔案 ##
讓我們從下載範例檔案開始，我們將在本教學課程中使用。

1. 將[建立 DocumentDB 帳戶、Web Apps 和部署示範應用程式範例](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip)下載至本機資料夾 (例如 C:\\DocumentDBTemplates) 並解壓縮檔案。此範例將會部署 DocumentDB 帳戶、App Service Web 應用程式和 Web 應用程式。它還會自動設定 Web 應用程式，以連線到 DocumentDB 帳戶。

2. 將[建立 DocumentDB 帳戶和 Web 應用程式範例](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip)下載至本機資料夾 (例如 C:\\DocumentDBTemplates) 並解壓縮檔案。此範例會將部署 DocumentDB 帳戶、App Service Web 應用程式，並將修改 Web 應用程式的組態來輕鬆地呈現 DocumentDB 連接資訊，但不包含 Web 應用程式。

> [AZURE.TIP]請注意，根據您電腦的安全性設定，您可能需要解除封鎖所擷取的檔案，方法是按一下滑鼠右鍵，並按一下 [**內容**]，然後按一下 [**解除封鎖**]。

![包含反白顯示 [解除封鎖] 按鈕的 [內容] 視窗的螢幕擷取畫面](./media/documentdb-create-documentdb-website/image1.png)

<a id="Build"></a>
##步驟 2：部署文件帳戶、App Service Web 應用程式與示範應用程式範例 ##

現在讓我們來部署第一個範本。

> [AZURE.TIP]範本不會驗證下面輸入的 Web 應用程式名稱和 DocumentDB 帳戶名稱 a) 是否有效且 b) 可用。強烈建議您在執行 PowerShell 部署指令碼之前，先確認您打算提供之名稱的可用性。

1. 開啟 Microsoft Azure PowerShell，然後瀏覽至您下載並解壓縮[建立 DocumentDB 帳戶、App Service Web 應用程式和部署示範應用程式範例](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebsiteTodo.zip)的資料夾 (例如 C:\\DocumentDBTemplates\\CreateDocDBWebsiteTodo)。


2. 我們即將執行 CreateDocDBWebsiteTodo.ps1 PowerShell 指令碼。指令碼採用下列必要參數：
	- WebsiteName：指定 App Service Web 應用程式名稱，並用來建構您將用來存取 Web 應用程式的 URL (例如：如果您指定 "mydemodocdbwebsite"，則將存取網站的 URL 會是 mydemodocdbwebsite.azurewebsites.net)。

	- ResourceGroupName：指定要部署的 Azure 資源群組名稱。如果指定的資源群組不存在，就會建立。

	- docDBAccountName：指定要建立之 DocumentDB 帳戶的名稱。

	- location：指定要在其中建立 DocumentDB 和 Web 應用程式資源的 Azure 位置。有效的值為：東亞、東南亞、美國東部、美國西部、北歐、西歐 (請注意，提供的位置值區分大小寫)。


3. 以下是執行指令碼的範例命令：

    	PS C:\DocumentDBTemplates\CreateDocDBWebAppTodo> .\CreateDocDBWebsiteTodo.ps1 -WebSiteName "mydemodocdbwebapp" -ResourceGroupName "myDemoResourceGroup" -docDBAccountName "mydemodocdbaccount" -location "West US"

	> [AZURE.TIP]請注意，系統會提示您輸入您的 Azure 帳戶使用者名稱和密碼做為執行指令碼的一部分。完整部署需要 10 到 15 分鐘才能完成。

4. 同時，以下是產生輸出的範例：

		VERBOSE: 1:06:00 PM - Created resource group 'myDemoResourceGroup' in location westus'
		VERBOSE: 1:06:01 PM - Template is valid.
		VERBOSE: 1:06:01 PM - Create template deployment 'Microsoft.DocumentDBWebSiteTodo'.
		VERBOSE: 1:06:08 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is running
		VERBOSE: 1:06:10 PM - Resource Microsoft.Web/serverFarms 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:14 PM - Resource microsoft.insights/alertrules 'CPUHigh mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/autoscalesettings 'mydemodocdbwebapp-myDemoResourceGroup' provisioning status is succeeded
		VERBOSE: 1:06:16 PM - Resource microsoft.insights/alertrules 'LongHttpQueue mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:21 PM - Resource Microsoft.Web/Sites 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:23 PM - Resource microsoft.insights/alertrules 'ForbiddenRequests mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/alertrules 'ServerErrors mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:06:25 PM - Resource microsoft.insights/components 'mydemodocdbwebapp' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:22 PM - Resource Microsoft.DocumentDb/databaseAccounts 'mydemodocdbaccount' provisioning status is succeeded
		VERBOSE: 1:16:24 PM - Resource Microsoft.Web/Sites/config 'mydemodocdbwebapp/web' provisioning status is succeeded
		VERBOSE: 1:16:27 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is running
		VERBOSE: 1:16:35 PM - Resource Microsoft.Web/Sites/Extensions 'mydemodocdbwebapp/MSDeploy' provisioning status is succeeded

		ResourceGroupName : myDemoResourceGroup
		Location          : westus
		Resources         : {mydemodocdbaccount, CPUHigh mydemodocdbwebapp, ForbiddenRequests mydemodocdbwebapp, LongHttpQueue mydemodocdbwebapp...}
		ResourcesTable    :
                    Name                                    Type                                   Location
                    ======================================  =====================================  =========
                    mydemodocdbaccount                      Microsoft.DocumentDb/databaseAccounts  westus
                    CPUHigh mydemodocdbwebapp              microsoft.insights/alertrules          eastus
                    ForbiddenRequests mydemodocdbwebapp    microsoft.insights/alertrules          eastus
                    LongHttpQueue mydemodocdbwebapp        microsoft.insights/alertrules          eastus
                    ServerErrors mydemodocdbwebapp         microsoft.insights/alertrules          eastus
                    mydemodocdbwebapp-myDemoResourceGroup  microsoft.insights/autoscalesettings   eastus
                    mydemodocdbwebapp                      microsoft.insights/components          centralus
                    mydemodocdbwebapp                      Microsoft.Web/serverFarms              westus
                    mydemodocdbwebapp                      Microsoft.Web/sites                    westus

		ProvisioningState : Succeeded


5. 在查看我們的範例應用程式之前，讓我們了解完成的範本部署：

	- App Service Web 應用程式隨即建立。

	- DocumentDB 帳戶隨即建立。

	- Web 部署封裝部署到 App Service Web 應用程式

	- 修改了 Web 應用程式組態，因此 DocumentDB 端點和主要金鑰呈現為應用程式設定。

	- 建立了一系列的預設監視規則。

	
6. 為使用應用程式，只要瀏覽至 Web 應用程式 URL (上述範例中的 URL 會是 http://mydemodocdbwebapp.azurewebsites.net))。您會看到下列的 Web 應用程式：

	![範例待辦事項應用程式](./media/documentdb-create-documentdb-website/image2.png)

7. 請繼續進行，並建立幾個工作，然後讓我們開啟 [Microsoft Azure Preview 入口網站](https://portal.azure.com)。

8. 選擇瀏覽資源群組，並選取我們部署期間建立的資源群組 (以上範例的 myDemoResourceGroup)。

	![包含反白顯示 myDemoResourceGroup 的 Azure 入口網站的螢幕擷取畫面](./media/documentdb-create-documentdb-website/image3.png)
9.  請注意 [摘要] 透鏡中的資源對應如何顯示我們所有的相關資源 (DocumentDB 帳戶、App Service Web 應用程式、監視)。

	![[摘要] 透鏡的螢幕擷取畫面](./media/documentdb-create-documentdb-website/image4.png)
10.  按一下您的 DocumentDB 帳戶，並啟動「查詢總管」中 (靠近 [帳戶] 刀鋒視窗的底部)。

	![包含反白顯示 [查詢總管] 的 [資源群組] 和 [帳戶] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-create-documentdb-website/image8.png)

11. 執行預設查詢，"SELECT * FROM c"，並檢查結果。請注意查詢已擷取您在步驟 7 所建立待辦事項的 JSON 表示法。任意嘗試查詢；例如，嘗試執行 SELECT * FROM c WHERE c.isComplete = true，傳回所有已標示為完成的待辦事項。


	![顯示查詢結果的 [查詢總管] 和 [結果] 刀鋒視窗的螢幕擷取畫面](./media/documentdb-create-documentdb-website/image5.png)
12. 任意瀏覽 DocumentDB 入口網站體驗，或修改範例 Todo 應用程式。當您準備好時，讓我們來部署另一個範本。
	
<a id="Build"></a>
## 步驟 3：部署文件帳戶和 Web 應用程式範例 ##

現在讓我們來部署第二個範本。

> [AZURE.TIP]範本不會驗證下面輸入的 Web 應用程式名稱和 DocumentDB 帳戶名稱 a) 是否有效且 b) 可用。強烈建議您在執行 PowerShell 部署指令碼之前，先確認您打算提供之名稱的可用性。

1. 開啟 Microsoft Azure PowerShell，然後瀏覽至您下載並解壓縮[建立 DocumentDB 帳戶和 Web 應用程式範例](https://portalcontent.blob.core.windows.net/samples/CreateDocDBWebSite.zip)的資料夾 (例如 C:\\DocumentDBTemplates\\CreateDocDBWebsite)。


2. 我們即將執行 CreateDocDBWebsite.ps1 PowerShell 指令碼。指令碼採用與我們部署的第一個範本相同的參數，即：
	- WebsiteName：指定 App Service Web 應用程式名稱，並用來建構您將用來存取 Web 應用程式的 URL (例如：如果您指定 "myotherdocumentdbwebapp"，則將存取網站的 URL 會是 myotherdocumentdbwebapp.azurewebsites.net)。

	- ResourceGroupName：指定要部署的 Azure 資源群組名稱。如果指定的資源群組不存在，就會建立。

	- docDBAccountName：指定要建立之 DocumentDB 帳戶的名稱。

	- 	location：指定要在其中建立 DocumentDB 和 Web 應用程式資源的 Azure 位置。有效的值為：東亞、東南亞、美國東部、美國西部、北歐、西歐 (請注意，提供的位置值區分大小寫)。

3. 以下是執行指令碼的範例命令：

    	PS C:\DocumentDBTemplates\CreateDocDBWebSite> .\CreateDocDBWebSite.ps1 -WebSiteName "myotherdocumentdbwebapp" -ResourceGroupName "myOtherDemoResourceGroup" -docDBAccountName "myotherdocumentdbdemoaccount" -location "East US"

	> [AZURE.TIP]請注意，系統會提示您輸入您的 Azure 帳戶使用者名稱和密碼做為執行指令碼的一部分。完整部署需要 10 到 15 分鐘才能完成。

4. 部署輸出將非常類似第一個範本範例。
5. 在我們開啟 Azure Preview 入口網站之前，讓我們先了解完成的範本部署：

	- App Service Web 應用程式隨即建立。

	- DocumentDB 帳戶隨即建立。

	- 	修改了 Web 應用程式組態，因此 Azure DocumentDB 端點、主要金鑰和次要金鑰呈現為應用程式設定。

	- 	建立了一系列的預設監視規則。

6. 讓我們開啟 [Azure Preview 入口網站](https://portal.azure.com)，並選擇瀏覽資源群組，然後選取我們在部署期間建立的資源群組 (在上面的範例中，是 myOtherDemoResourceGroup)。
7. 在 [摘要] 透鏡中，按一下剛部署的 Web 應用程式。

	![包含反白顯示 myotherdocumentdbwebapp Web 應用程式的 [摘要] 透鏡的螢幕擷取畫面](./media/documentdb-create-documentdb-website/image6.png)
8. 在 Web 應用程式刀鋒視窗中，依序按一下 [**所有設定**]、[**應用程式設定**]，並注意出現的 DocumentDB 端點和每個 DocumentDB 主要金鑰的應用程式設定。

	![Web 應用程式、設定和應用程式設定刀鋒視窗的螢幕擷取畫面](./media/documentdb-create-documentdb-website/image7.png)
9. 任意繼續瀏覽探索 Azure Preview 入口網站，或遵循其中一個 DocumentDB [範例](http://go.microsoft.com/fwlink/?LinkID=402386)來建立您自己的 DocumentDB 應用程式。

	
	
<a name="NextSteps"></a>
## 後續步驟

恭喜！ 您已使用 Azure 資源管理員範本部署了 DocumentDB、App Service Web 應用程式以及範例 Web 應用程式。

- 若要深入了解 DocumentDB，請按一下[這裡](http://azure.com/docdb)。
- 若要深入了解 Azure App Service Web Apps，請按一下[這裡](http://go.microsoft.com/fwlink/?LinkId=325362)。
- 若要深入了解 Azure 資源管理員範本，請按一下[這裡](https://msdn.microsoft.com/library/azure/dn790549.aspx)。


## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊的入口網站變更為新入口網站的指南，請參閱：[瀏覽 Azure 入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡，無需承諾。
 

<!---HONumber=July15_HO3-->