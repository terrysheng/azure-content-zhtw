<properties 
	pageTitle="Microsoft Azure 網站上的分段部署" 
	description="了解如何在 Microsoft Azure 網站使用分段發行。" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/9/2014" 
	ms.author="cephalin"/>

<a name="Overview"></a>
#Microsoft Azure 網站上的分段部署#
當您將應用程式部署至 Azure 網站時，可以部署至個別的部署位置，而不是實際的作用中網站與其主機名稱所在的預設生產位置。「標準」****虛擬主機方案中提供此選項。您甚至可以在兩個部署位置之間 (包含生產位置)，交換網站和網站組態。將應用程式部署至部署位置具有下列優點：

- 您可以先確認分段部署位置中的網站變更，再將分段部署位置與生產位置進行交換。

- 交換之後，先前具有預備網站的位置，現在已經有之前的生產網站。若交換到生產位置的變更不是您需要的變更，您可以立即執行相同的交換，以取回「上一個已知良好的網站」。 
 
- 先將網站部署至位置，再將網站交換到生產位置，可以確保所有位置的執行個體在交換到生產位置之前都已經準備就緒。這麼做可以排除部署網站時的停機情況。交換作業期間所有的流量都能順暢地重新導向，而且不會捨棄任何要求封包。 

除了生產位置之外，「標準」****方案中的每個網站還支援四個部署位置。 

## 目錄 ##
- [新增部署位置至網站](#Add)
- [關於部署位置組態](#AboutConfiguration)
- [交換部署位置](#Swap)
- [將生產網站回復至預備網站](#Rollback)
- [刪除網站位置](#Delete)
- [用於網站位置的 Azure PowerShell Cmdlet](#PowerShell)
- [用於網站位置的 Azure 跨平台命令列介面 (xplat-cli) 命令](#CLI)

<a name="Add"></a>
##將部署位置加入網站##

必須以「標準」****虛擬主機方案執行網站，才能啟用多個部署位置。 

1. 在 [快速啟動] 頁面，或是在您網站的 [儀表板] 頁面中的 [快速概覽] 區段，按一下 [加入新的部署位置]****。 
	
	![Add a new deployment slot][QGAddNewDeploymentSlot]
	
	> [AZURE.NOTE]
	> 如果網站還未處於「標準」**** 模式，將顯示「您必須處於標準模式，才能啟用預備發行」****訊息。此時，您可以選取 [升級]****，並瀏覽至網站的 [調整規模]**** 索引標籤後再繼續。
	
2. 在 [加入新的部署位置]**** 對話方塊中，指定位置名稱，並選取是否要複製其他現有部署位置的網站組態。按一下核取記號繼續。 
	
	![Configuration Source][ConfigurationSource1]
	
	第一次建立位置時，您將只有兩個選擇：從生產環境的預設位置複製組態，或完全不複製。 
	
	建立數個位置後，就可以從生產位置以外的位置複製組態：
	
	![Configuration sources][MultipleConfigurationSources]

5. 在網站清單中，展開網站名稱左側的標記以顯示部署位置。這會使網站名稱後面有部署位置名稱。 
	
	![Site List with Deployment Slot][SiteListWithStagedSite]
	
4. 當您按一下部署網站位置中的名稱之後，將開啟具有一組索引標籤的頁面，就像其他網站一樣。<strong><i>your-website-name</i>(<i>deployment-slot-name</i>)</strong> 將出現在入口網站頁面頂端，提醒您您正在檢視部署網站位置。
	
	![Deployment Slot Title][StagingTitle]
	
5. 按一下儀表板檢視中的網站 URL。請注意，部署位置有自己的主機名稱，同時也是作用中的網站。若要限制對部署位置的公用存取，請參閱 [Azure 網站 - 封鎖對非生產部署位置的 Web 存取](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)。

	-	 

其中沒有內容。您可以從不同的儲存機制分支，或從整個不同的儲存機制部署至位置。您也可以變更位置的組態。更新內容時，請使用與部署位置相關聯的發行設定檔或部署認證。例如，您可以[使用 Git 發行至此位置](http://azure.microsoft.com/documentation/articles/web-sites-publish-source-control/)。

<a name="AboutConfiguration"></a>
##關於部署位置組態##
當您複製其他部署位置的組態時，可以編輯複製的組態。以下清單顯示當您交換位置時會變更的組態。

**將變更位置交換的組態**：

- 一般設定
- 連接字串
- 處理常式對應
- 監視與診斷設定

**將不會變更位置交換的組態**：

- 發行端點
- 自訂網域名稱
- SSL 憑證與繫結
- 規模調整設定

**注意**：

- 只有在「標準」****虛擬主機方案的網站中才提供多個部署位置。

- 當網站具有多個位置時，無法變更虛擬主機方案。

- 您打算與生產位置交換的位置，需要具備與生產位置一模一樣的組態。

- 依預設，部署位置會指向與生產網站相同的資料庫。不過，您可以將部署位置設定為指向替代資料庫，方法是變更部署位置的資料庫連接字串。接下來，您可以在將部署位置與生產位置交換之前，先在部署位置上還原原始的資料庫連接字串。


<a name="Swap"></a>
##交換部署位置##

1. 若要交換部署位置，請在網站清單中選取您要交換的部署位置，然後按一下命令列中的 [交換]**** 按鈕。 
	
	![Swap Button][SwapButtonBar]
	
2. [交換部署] 對話方塊隨即顯示。此對話方塊可讓您選擇要成為來源與目的地的網站位置。
	
	![Swap Deployments Dialog][SwapDeploymentsDialog]
	
3. 按一下核取記號完成操作。操作完成時，網站位置已經交換完畢。


<a name="Rollback"></a>
##將生產網站回復至預備網站##
若交換位置後，在生產位置中識別出錯誤，可以立即交換相同的兩個位置，將位置還原成交換前的狀態。 

<a name="Delete"></a>
##刪除網站位置##

在 Azure 網站入口網站頁面底部的命令列中，按一下 [刪除]****。您可以選擇刪除網站與所有部署位置，或是僅刪除部署位置。 

![Delete a Site Slot][DeleteStagingSiteButton]

**注意**：

- 非生產的位置無法使用調整規模。只有生產位置可使用此功能。

- 非生產位置不支援連結的資源管理。 

- 如果您需要的話，仍舊可以直接發行至生產位置。

- 根據預設，您的部署位置 (網站) 將與您的生產位置 (網站) 共用相同的資源並在相同的 VM 上執行。如果您在預備位置執行壓力測試，生產環境將會出現相似的壓力負載。 
	
	> [AZURE.NOTE] 您只有在 [Azure 預覽入口網站](https://portal.azure.com)中，才能藉由暫時將非生產位置移到其他虛擬主機方案，避免這種對生產位置的潛在影響。請注意，測試和生產位置必須重新共用相同的虛擬主機方案，您才能將測試位置交換到生產位置。

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
##用於網站位置的 Azure PowerShell Cmdlet 

Azure PowerShell 模組提供透過 Windows PowerShell 來管理 Azure 的 Cmdlet，包括支援管理 Azure 網站部署位置。 

- 如需安裝及設定 Azure PowerShell，以及使用您的 Windows Azure 訂用帳戶驗證 Azure PowerShell 的詳細資訊，請參閱[如何安裝及設定 Windows Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell)。  

- 若要列出 PowerShell 中可供 Azure 網站使用的 Cmdlet，請呼叫 `help AzureWebsite`。 

----------

###Get-AzureWebsite
**Get-AzureWebsite** Cmdlet 可顯示目前訂用帳戶的 Azure 網站資訊，如下列範例所示。 

`Get-AzureWebsite siteslotstest`

----------

###New-AzureWebsite
您可以使用 **New-AzureWebsite** Cmdlet 並指定網站名稱與位置名稱，為「標準」模式中的所有網站建立網站位置。您還可以指出相同的區域，作為部署位置建立作業所需的網站，如以下範例所示。 

`New-AzureWebsite siteslotstest -Slot staging -Location "West US"`

----------

###Publish-AzureWebsiteProject
您可以針對內容部署使用 **Publish-AzureWebsiteProject** Cmdlet，如下列範例所示。 

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

----------

###Show-AzureWebsite
當內容與組態更新都套用到新的位置後，您就可以使用 **Show-AzureWebsite** Cmdlet 瀏覽至該位置以驗證更新，如下列範例所示。

`Show-AzureWebsite -Name siteslotstest -Slot staging`

----------

###Switch-AzureWebsiteSlot
**Switch-AzureWebsiteSlot** Cmdlet 可執行交換作業，將已更新的部署位置轉變為生產網站，如下列範例所示。生產網站不會出現任何停機事件，也不會進行冷啟動。 

`Switch-AzureWebsiteSlot -Name siteslotstest`

----------

###Remove-AzureWebsite
如果不再需要某個部署位置，可以使用 **Remove-AzureWebsite** Cmdlet 將它刪除，如下列範例所示。

`Remove-AzureWebsite -Name siteslotstest -Slot staging` 

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
##用於網站位置的 Azure 跨平台命令列介面 (xplat-cli) 命令

Azure 跨平台命令列介面 (xplat-cli) 提供您處理 Azure 的跨平台命令，包括支援管理 Azure 網站上的部署位置。 

- 如需安裝及設定 xplat-cli 的相關說明，包括如何將 xplat-cli 與您的 Azure 訂用帳戶連線的詳細資訊，請參閱[安裝與設定 Azure 跨平台命令列介面](http://azure.microsoft.com/documentation/articles/xplat-cli)。 

-  若要列出 xplat-cli 中可供 Azure 網站使用的命令，請呼叫 `azure site -h`。 

----------
###azure site list
如需目前訂用帳戶中 Azure 網站的相關資訊，請呼叫 **azure site list**，如下列範例所示。
 
`azure site list siteslotstest`

----------
###azure site create
若要為「標準」模式中的任何網站建立網站位置，請呼叫 **azure site create** 並指定現有網站的名稱與要建立之位置的名稱，如下列範例所示。

`azure site create siteslotstest --slot staging`

若要為新位置啟用來源控制，請使用 **--git** 選項，如下列範例所示。
 
`azure site create --git siteslotstest --slot staging`

----------
###azure site swap
若要將已更新的部署位置轉變成生產網站，請使用 **azure site swap** 命令執行交換作業，如下列範例所示。生產網站不會出現任何停機事件，也不會進行冷啟動。 

`azure site swap siteslotstest`

----------
###azure site delete
若要刪除不再需要的部署位置，請使用 **azure site delete** 命令，如下列範例所示。

`azure site delete siteslotstest --slot staging`

----------
## 後續步驟 ##
[Azure 網站 - 封鎖對非生產部署位置的 Web 存取](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Microsoft Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)


<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png


<!--HONumber=42-->
