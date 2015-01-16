<properties title="Azure Websites Web Hosting Plans In-Depth Overview" pageTitle="Azure 網站 Web 主控方案深入概觀 - Microsoft Azure 功能指南" description="了解 Web Hosting Plans for Azure Websites 的運作方式，以及對於管理所發揮的效益。" metaKeywords="Azure Web Sites, Azure Websites, WHP, Web Hosting Plan, Web Hosting Plans, Resource Groups" services="web-sites" solutions="web" documentationCenter="Infrastructure" authors="Byron Tardif and Yochay Kiryaty" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Byron Tardif and Yochay Kiryaty" />
</br>
#Azure 網站 Web 主控方案深入概觀#
</br>
Web 主控方案 (WHPs) 代表一組可以在您的網站之間共用的特性和功能。  Web 主控方案支援 4 個 Azure 網站價格層 (免費、共用、基本和標準)，其中每個層級有它自己的功能和容量。  在相同的訂閱、資源群組和地理位置中的網站可以共用 Web 主控方案。共用一個 Web 主控方案的所有網站，均可利用 Web 主控方案層定義的所有功能和特性。與指定 Web 主控方案關聯的所有網站，可在 Web 主控方案定義的資源上執行。例如，如果您的 Web 主控方案設為使用兩個「小型」虛擬機器，與該 Web 主控方案關聯的所有網站將可在兩部虛擬機器上執行。就像 Azure 網站一樣，執行網站的虛擬機器受到完整管理且具有高可用性。
</br>
在本文中，我們將探討重要的特性，例如 Web 主控方案的層級與規模，以及這些特性如何在管理您的網站時發揮效用。 
</br>
##網站、Web 主控方案和資源群組##
</br>
在任意指定時間，一個網站只能與一個 Web 主控方案產生關聯。一個 Web 主控方案會與一個資源群組產生關聯。資源群組是 Azure 中的新概念，可作為其內含各項資源的生命週期界限。資源群組可讓您集中管理應用程式的一切層面。 
</br>
您在一個資源群組中可以有多個 Web 主控方案，每個主控方案有自己的一套供其相關聯網站所使用的功能。  下圖說明此關係：
</br>
</br>
![Resource Groups and Web Hosting Plans](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview01.png)
</br>
</br>
在單一資源群組中有多個 Web 主控方案可讓您將不同的網站配置到不同的資源，主要是執行您網站的虛擬機器。例如，這樣可以區隔開發和測試及生產站台之間的資源，您可以將一個 Web 主控方案及其自己的一組專用資源配置給生產網站，將第二個 Web 主控方案配置給開發和測試網站。 
</br>
在單一資源群組中有多個 Web 主控方案也可讓您定義一個跨越不同區域的應用程式。例如，在兩個區域中執行的高可用性網站將包括兩個 Web 主控方案，每個區域各有一個方案，而每個 Web 主控方案有一個相關聯的網站。在這類情況中，所有網站將會與單一資源群組相關聯，也就定義一個應用程式。擁有資源群組的單一檢視，並有多個 Web 主控方案與多個網站，使得要管理、控制和檢視網站的健康情況極為容易。除了管理指定應用程式的網站資源和相關的網站以外，您還可以將任何 Azure 資源 (如 SQL-Azure 資料庫和 Team 專案) 產生關聯。 
</br>
##何時應建立新資源群組，以及何時應建立新 Web 主控方案？##
</br>
建立新網站時，如果您即將建立代表新 Web 應用程式的網站，應該考慮建立新資源群組。在此情況下，建立新資源群組、關聯的 Web 主控方案和網站是正確選擇。透過新 Azure 入口網站預覽使用組件庫或新的網站 + SQL 選項來建立這類新網站時，入口網站將預設成為您的新網站建立新資源群組和 Web 主控方案。不過，如果需要的話，您可以覆寫這些預設值。
</br>
</br>
![Creating a new Web Hosting Plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview02.png)
</br>
</br>
您隨時可以將新的網站或其他任何資源群組加入至現有的資源群組。從現有資源群組的內容建立新網站時，新網站精靈會預設為現有資源和 Web 主控方案。在這裡，您也可以視需要覆寫這些預設值。將新的網站加入至現有的資源群組時，您可以選擇將網站加入至現有的 Web 主控方案 (這是 Azure 入口網站預覽中的預設選項)，或建立新的 Web 主控方案來加入網站。
</br>
建立新的主控方案可讓您為網站配置一組新的資源，並讓您更有效地控制資源配置，因為每個 Web 主控方案會取得其自己的一組虛擬機器。由於您可以在 Web 主控方案之間移動網站 (假設 Web 主控方案在相同區域)，決定是否建立新 Web 主控方案相形之下便較不重要了。如果某個網站開始耗用太多資源，或您只需要將幾個網站區隔出來，則您可以建立新的 Web 主控方案，再將網站移入其中。
</br>
如果您想要在不同區域中建立新的網站，而該區域目前沒有 Web 主控方案，您必須在該區域中建立新的 Web 主控方案，才能夠有與其相關聯的網站。 
</br>
切記，您無法在資源群組之間移動 Web 主控方案或網站。同時，您無法在位於兩個不同區域的兩個 Web 主控方案之間移動網站。 
</br>
##Azure 預覽入口網站中的現有資源群組##
</br>
如果您在 Azure 網站上已經有網站，您將會看到自己的所有網站都會顯示在 Azure 預覽入口網站上。您可以按一下左導覽窗格中的 [**瀏覽**] 按鈕，然後選取 [**網站**]，以查看所有網站的簡單列表：
</br>
</br>
![See all your website as a flat list](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview03.png)
</br>
</br>
您也可以按一下左導覽窗格中的 [**瀏覽**] 按鈕，然後選取 [**資源群組**]，以查看已為您建立的所有資源群組：
</br>
</br>
![See all the resource groups that have been created](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview04.png)
</br>
</br>
在您已有網站的每個區域中，您也會發現有自動產生的預設資源群組。為網站自動產生的資源群組名稱為 *Default-Web-<LOCATION NAME>*，其中，位置名稱代表 Azure 區域 (例如 *Default-Web-WestUS*)。在每個資源群組中，您會找到群組區域的所有現有網站。您在過去建立且將在未來 Azure 入口網站或 Azure Preview 入口網站中建立的每個網站，將會出現在這兩個入口網站上。 
</br>
因為每個網站都必須與 Web 主控方案產生關聯，我們已根據以下慣例，在每個區域中為現有的網站建立預設 Web 主控方案：
</br>
* 您的所有「**免費**」網站都與「**預設**」Web 主控方案相關聯，且價格層設為「**免費**」。 
</br>
* 您的所有「**共用**」網站都與「**預設**」Web 主控方案相關聯，且價格層設為「**共用**」
</br>
* 您的所有「**標準**」網站都與預設 Web 主控方案相關聯，且價格層設為「**標準**」。 
</br>
Web 主控方案的名稱為 **DefaultServerFarm**。選擇此名稱是為了支援舊版 API。**ServerFarm** 名稱有時會使人誤以為它指的是 **Web 主控方案**，但務必注意它是 Web 主控方案的名稱，並非屬於自身的實體。 
</br>
##Web 主控方案常見問題集##
</br>
**問**：如何建立 Web 主控方案？
</br>
**答**:Web 主控方案是容器，因此您不能建立空白的 Web 主控方案。不過，建立網站期間會明確建立新的 Web 主控方案。
</br>
若要使用新的 **Azure 入口網站預覽**的 UI 來執行這項操作，請按一下 [**新增**]，然後選取 [**網站**]，將會開啟網站建立分頁。在下方第一個影像中，您可以在左下方看到 [**新增**] 圖示，在第二個影像中，您可以看到 [**網站**] 建立分頁、[**Web 主控方案**] 分頁和 [**定價層**] 分頁：
</br>
</br>
![Create a new website](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview05.png)
</br>
</br>
![Website, Web Hosting Plan and pricing tier blades](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview06.png)
</br>
</br>
在此範例中，我們選擇建立一個稱為 **contosomarketing** 的新網站，並將它放入稱為 **contoso** 的新 Web 主控方案中。為這個 Web 主控方案選取的價格層為**小型標準**。如需 Web 主控方案定價層的詳細資訊，以及各層提供的功能、定價和調整選項，請瀏覽 [Azure 網站 Web 主控方案規格](http://go.microsoft.com/?linkid=9845586)。 
</br>
也請注意，Web 主控方案也可以建立在現有的 Azure 入口網站。這是透過 [**快速建立**] 精靈進行，請從 [**WEB 主控方案**] 下拉式清單中選取 [**Create new web hosting plan**]：
</br>
</br>
![Create new web hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview07.png)
</br>
</br>
在此範例中，我們建立一個稱為 **northwind** 的新網站，且選擇建立新的 Web 主控方案。此作業的結果會是名為 **default0** 的新 Web 主控方案，其包含 **northwind** 網站。透過此方式建立的所有 Web 主控方案將遵循此命名慣例，而 Web 主控方案在建立之後即不能加以重新命名。同時，透過此程序建立的 Web 主控方案將建立在「**免費**」價格層。
</br>
**問**：如何指派網站至 **Web 主控方案**？
</br>
**答**:網站是在網站建立程序期間指派給 Web 主控方案。若要使用新的 **Azure 入口網站預覽**的 UI 來執行這項操作，請按一下 [**新增**]，然後選取 [**網站**]：
</br>
</br>
![Create a new website](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview08.png)
</br>
</br>
然後，在網站建立分頁中，選取主控方案：
</br>
</br>
![Select a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview09.png)
</br>
</br>
使用現有的 Azure 入口網站，也可以將網站建立到特定的 Web 主控方案中。這是在 [**快速建立**] 精靈中完成。輸入網站 URL 之後，使用 [**WEB 主控方案**] 下拉式清單來選取要加入網站的方案：
</br>
</br>
![Select a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview10.png)
</br>
</br>
**問**：如何將網站移至不同的 Web 主控方案？
</br>
**答**:您可以使用 Azure 預覽入口網站，將網站移至不同的 Web 主控方案。網站可以在相同地理區域中屬於相同資源群組的 Web 主控方案之間移動。
</br>
若要將網站移至另一個方案，請瀏覽至您要移動的網站的網站分頁。  然後按一下 [**Web 主控方案**]：
</br>
</br>
![Choose a new or existing web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview22.png)
</br>
</br>
這會開啟 [Web 主控方案] 分頁。在此時，您可以挑選現有的 Web 主控方案，或建立新的方案。不同地理位置或資源群組中的方案會呈現灰色而且無法選取。
</br>
請注意，每個 Web 主控方案都有自己的定價層。當您將網站從**免費**層 Web 主控方案移至**標準** Web 主控方案時，您的網站將可以利用標準層的所有功能和資源。 
</br>
</br>
**問**：如何調整 Web 主控方案？
</br>
**答**:有兩種方式可擴大 Web 主控方案。一個方式是向上擴大您的 Web 主控方案，以及與該 Web 主控方案關聯的所有網站。藉由變更 Web 主控方案價格層，該 Web 主控方案中的所有網站將受限於該價格層所定義的功能和資源。 
</br>
在下方的影像中，您可以看到 [**Web 主控方案**] 分頁和 [**定價層**] 分頁。按一下 [**Web 主控方案**] 分頁的 [**定價層**] 部分會展開 [**定價層**] 分頁，其中可讓您變更 Web 主控方案的定價層：
</br>
</br>
![The Web Hosting Plan blade and the Pricing Tier](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview16.png)
</br>
</br>
調整方案的第二種方法是增加 Web 主控方案中的執行個體數目，以相應擴大方案。在以下的影像中，您可以看到 **Web 主控方案**分頁和**調整規模**分頁。按一下 [**Web 主控方案**] 分頁中的 [調整] 區域會展開它，可讓您變更方案的執行個體計數：
</br>
</br>
![Changing the instance count of a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview17.png)
</br>
</br>
因為上方影像中的 Web 主控方案已設定為使用「**標準**」定價層，所以會啟用 [**自動調整**] 選項。 
</br>
在完整 Azure 入口網站中，可以在 [**調整**] 索引標籤中執行這項操作，如下所示：
</br>
</br>
![Changing the instance count of a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview18.png)
</br>
</br>
**問**：如何刪除 Web 主控方案？
</br>
**答**:若要刪除 Web 主控方案，您必須先刪除與其關聯的所有網站。刪除 Web 主控方案中的所有網站後，就可以從 [Web 主控方案] 分頁中刪除 Web 主控方案：
</br>
</br>
![Deleting a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview19.png)
</br>
</br>
在完整 Azure 網站入口網站中，刪除 Web 主控方案中的最後一個網站時，將會自動刪除相關聯的 Web 主控方案。
</br>
**問**：如何監視 Web 主控方案？
</br>
**答**:您可以使用 [Web 主控方案] 分頁的 [監視] 部分來監視 Web 主控方案：
</br>
</br>
![Monitoring a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview20.png)
</br>
</br>
用滑鼠右鍵控一下監視控制項，然後選取 [**編輯查詢**]，即可自訂這些控制項：
</br>
</br>
![Editing the monitoring controls](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview21.png)
</br>
</br>
公開的度量如下：
</br>
* CPU 百分比
</br>
* 記憶體百分比
</br>
* 磁碟佇列長度 
</br>
* HTTP 佇列長度。 
</br>
此度量代表屬於 Web 主控方案之執行個體的平均使用量。所有度量均可用來設定警示以及「自動調整規模」規則。
</br>
##重點與結論##
</br>
Web 主控方案代表可跨網站共用的一組特性和功能。  「Web 主控方案」提供您將網站配置到特定資源集虛擬機器的彈性，並進一步將您的 Azure 資源配置與網站使用量最佳化。 
