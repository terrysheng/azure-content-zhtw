<properties 
	pageTitle="什麼是 Web 主控方案？" 
	description="Web 主控方案概觀" 
	services="web-sites" 
	authors="adamabdelhamed" 
	manager="wpickett" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="adamab"/>

# 什麼是 Web 主控方案？#

Web 主控方案代表可跨網站共用的一組特性和功能。Web 主控方案支援一些價格層 (例如，免費、共用、基本及標準)，每一層都有它自己的功能。相同訂閱、資源群組及地理位置中的網站都能共用 Web 主控方案。您可以快速變更網站不會停機的主控方案，但是主控方案必須存在於相同的資源群組與地理位置。

## Web 主控方案的功能 ##

每個價格層 (如「免費」、「共用」、「基本」及「標準」) 都有專屬的功能集。[請移至此處](http://go.microsoft.com/fwlink/?LinkID=394421)以取得最新的功能和價格資訊。

以下是有關 Web 主控方案和功能的一些實用提示：

- 您可以在零停機情況下隨時變更 Web 主控方案的價格層。
- 為了讓費用得到最大效益，最好是相同訂閱、位置和資源群組中的網站都能共用 Web 主控方案。 
- 相同訂閱、位置和資源群組中的網站都能共用 Web 主控方案。 
- 個別網站與部署位置可以變更為零停機的主控方案，但是目標主控方案必須與原始方案存在於相同的資源群組中。
- 可鎖定 Web 主控方案來執行自動調整工作之類的功能。若要自動調整個別的網站，您應向該網站擬定專屬的 Web 主控方案。

[深入瞭解 Web 主控方案的管理](http://go.microsoft.com/fwlink/?LinkID=394411)。

## Web 主控方案和功能 ##

免費和共用層中的 Web 主控方案將共用基礎結構提供給網站，這意謂您的網站會與其他客戶的網站共用資源。  

基本和標準層中的 Web 主控方案會將專用的基礎結構提供給網站，這意謂只有您選擇與此方案相關聯的網站才能使用該資源運作。您可在此層上設定 Web 主控方案，以使用一或多個虛擬機器執行個體。支援小型、中型和大型執行個體。我們會為您管理這些虛擬機器，這代表您永遠不需要擔心作業系統更新及類似情況。  

共用 (預覽) 層相關附註。您可以根據層及選擇的容量，為  'Shared' 以外的所有層支付單一的 Web 主控方案價格，且使用方案的每個網站不需要額外支付費用。共用 Web 主控方案不同。基於共用基礎結構的性質，必須對方案中每個網站個別收費。  

### Web 主控方案及新的 Azure 預覽入口網站 ###

新的 Azure 預覽入口網站可讓您建立現有或新網站與 Web 主控方案之間的關聯。事實上，已根據現有的所有網站之訂閱、地理位置和目前的價格層，將這些網站自動指派給 Web 主控方案。  

建立新網站時，入口網站會詢問您要關聯哪些 Web 主控方案。您可以建立新的 Web 主控方案，或選取現有的方案。若要使用現有的方案，您的新網站與該方案必須位於相同的訂閱、地理位置和資源群組。建立新的空白網站時，會將 Azure 預設為前次使用的 Web 主控方案，協助您發揮已建立之方案的最大功效。建立包含資料庫的網站時，將無法選擇重複使用現有的方案。

您可以查看所有訂閱的所有相關 Web 主控方案，方法是使用左功能表列的 [瀏覽] 按鈕，然後按一下螢幕上出現的活動窗格右上角的 [Web 主控方案]。

![][BrowseEverythingScreenshot]

您也可以查看網站刀鋒視窗頂端出現的基本資訊區段，檢視每個網站所關聯的 Web 主控方案。按一下方案將啟動刀鋒，您可使用刀鋒來管理 Web 主控方案。[深入瞭解 Web 主控方案的管理](http://go.microsoft.com/fwlink/?LinkID=394411) (英文)。

![][WebsiteEssentials]

### 後續步驟 ###

若要開始使用 Azure，請參閱 [Microsoft Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

<!-- Images. -->
[BrowseEverythingScreenshot]: ./media/web-sites-web-hosting-plan-overview/browse-everything.png
[WebsiteEssentials]: ./media/web-sites-web-hosting-plan-overview/web-hosting-plan-resource-map.png




<!--HONumber=52-->