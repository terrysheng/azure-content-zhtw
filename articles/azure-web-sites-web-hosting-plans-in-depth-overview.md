<properties title="Azure Websites Web Hosting Plans In-Depth Overview" pageTitle="Azure Websites Web Hosting Plans In-Depth Overview - Windows Azure feature guide" description="Learn how Web Hosting Plans for Azure Websites work, and how they benefit your management experience." metaKeywords="Azure Web Sites, Azure Websites, WHP, Web Hosting Plan, Web Hosting Plans, Resource Groups" services="web-sites" solutions="web" documentationCenter="Infrastructure" authors="Byron Tardif and Yochay Kiryaty" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Byron Tardif and Yochay Kiryaty"></tags>
</br>

# Azure 網站 Web 主控方案深入概觀

</br>
Web 主控方案 (WHP) 代表可跨網站共用的一組特性和功能。Web 主控方案支援 4 個 Azure 網站價格層 (免費、共用、基本及標準)，每一層都有它自己的功能與容量。相同訂閱、資源群組及地理位置中的網站都能共用 Web 主控方案。共用一個 Web 主控方案的所有網站，均可利用 Web 主控方案層定義的所有功能和特性。與指定 Web 主控方案關聯的所有網站，可在 Web 主控方案定義的資源上執行。例如，如果您的 Web 主控方案設為使用兩個「小型」虛擬機器，與該 Web 主控方案關聯的所有網站將可在兩部虛擬機器上執行。如同 Azure 網站，您的網站執行所在的虛擬機器，完全可加以管理且高度可用。
</br>
在本文中，我們將探討重要的特性，例如 Web 主控方案的層級與規模，以及這些特性如何在管理您的網站時發揮效用。
</br>

## 網站、Web 主控方案和資源群組

</br>
在任意指定時間，一個網站只能與一個 Web 主控方案產生關聯。一個 Web 主控方案會與一個資源群組產生關聯。資源群組是 Azure 中的新概念，可作為其內含各項資源的生命週期界限。資源群組可讓您同時管理某個應用程式中的所有片段。
</br>
您可以在一個資源群組中有多個 Web 主控方案，而每個主控方案將有自己的一組特性與功能，供其關聯的網站使用。下圖說明這個關係：
</br>
</br>
![Resource Groups and Web Hosting Plans][]
</br>
</br>
能夠在單一資源群組中有多個 Web 主控方案，可讓您將不同網站配置到不同資源，主要是網站上執行的虛擬機器。例如，此功能可讓您在開發人員與測試和生產網站之間將資源區隔開，其中您可能想為生產網站配置一個 Web 主控方案 (擁有自己專屬的一組資源)，以及為開發人員和測試網站配置第二個 Web 主控方案。
</br>
在單一資源群組中有多個 Web 主控方案，也可讓您定義跨區域的應用程式。例如，在兩個區域中執行的高度可用網站將包括兩個網站，每個區域一個網站，而一個網站則與每個 Web 主控方案關聯。在這類情況中，所有網站將會與定義一個應用程式的單一資源群組相關聯。擁有資源群組的單一檢視，並有多個 Web 主控方案與多個網站，使得要管理、控制和檢視網站的健康情況極為容易。除了管理指定應用程式的網站資源和相關的網站以外，您還可以將任何 Azure 資源 (如 SQL-Azure 資料庫和小組專案) 產生關聯。
</br>

## 何時應建立新資源群組，以及何時應建立新 Web 主控方案？

</br>
建立新網站時，如果您即將建立代表新 Web 應用程式的網站，應該考慮建立新資源群組。在此情況下，建立新資源群組、關聯的 Web 主控方案和網站是正確選擇。透過新 Azure 入口網站預覽使用組件庫或新的網站 + SQL 選項來建立這類新網站時，入口網站將預設成為您的新網站建立新資源群組和 Web 主控方案。不過，如果有需要，您可以覆寫這些預設值。
</br>
</br>
![Creating a new Web Hosting Plan][]
</br>
</br>
您一律可以將新網站或任何其他資源加入現有的資源群組。從現有資源群組的內容建立新網站時，新網站精靈會預設為現有資源和 Web 主控方案。在這裡，您也可以視需要覆寫這些預設值。加入新網站到現有資源群組時，您可以選擇加入網站到現有 Web 主控方案 (這是新 Azure 入口網站預覽的預設選項)，或您也可以建立新 Web 主控方案，來加入這個網站。
</br>
建立新主控方案可讓您為網站配置一組新的資源，並讓您對資源配置能有更好的控制，因為每個主控方案都能獲得自己的一組虛擬機器。由於您可以在 Web 主控方案之間移動網站 (假設 Web 主控方案在相同區域)，決定是否建立新 Web 主控方案相形之下便較不重要了。如果指定的網站開始耗用太多資源，或您只是需要分隔一些網站，您可以建立新 Web 主控方案並將網站移至其中。
</br>
如果您想要在不同區域建立新網站，而該區域沒有現有的 Web 主控方案，將必須在該區域中建立新 Web 主控方案，才能夠有與其關聯的網站。
</br>
需記住的一個重要事項是，您無法在資源群組之間移動 Web 主控方案或網站。同時，您無法在位於兩個不同區域的兩個 Web 主控方案之間移動網站。
</br>

## Azure 預覽入口網站中的現有資源群組

</br>
如果您在 Azure 網站上已經有網站，您將會看到自己的所有網站都會顯示在 Azure 預覽入口網站上。按一下左側瀏覽窗格上的 [瀏覽] 按鈕，並選取 [網站]，即可看到所有網站的簡單列表：
</br>
</br>
![See all your website as a flat list][]
</br>
</br>
按一下左側瀏覽窗格上的 [瀏覽] 按鈕，並選取 [資源群組]，即可看到已為您建立的所有資源群組：
</br>
</br>
![See all the resource groups that have been created][]
</br>
</br>
您也會注意到，在您已擁有網站的每個區域中會有自動產生的預設資源群組。為網站自動產生的資源群組名稱為 *Default-Web-<location name>*，其中，位置名稱代表 Azure 區域 (例如，*Default-Web-WestUS*)。在每個資源群組中，您會找到群組區域的所有現有網站。在完整版 Azure 入口網站或 Azure 預覽入口網站中，您過去建立以及日後要建立的每個網站，將可在兩個入口網站上使用。
</br>
由於每個網站都必須與 Web 主控方案建立關聯，我們已在每個區域中根據下列慣例，為您的現有網站建立預設的 Web 主控方案：
</br>

-   您的所有**免費**網站都與**預設** Web 主控方案關聯，其價格層設為**免費**。
    </br>
-   您的所有**共用**網站都與**預設** Web 主控方案關聯，其價格層設為**共用**
    </br>
-   您的所有**標準**網站都與預設 Web 主控方案關聯，其價格層設為**標準**。
    </br>
    這個 Web 主控方案的名稱為 **DefaultServerFarm**。選擇此名稱是為了支援舊版 API。**ServerFarm** 名稱有時會使人誤以為它指的是 **Web 主控方案**，但務必注意它是 Web 主控方案的名稱，並非屬於自身的實體。
    </br>

    ## Web 主控方案常見問題集

    </br>
    **問**：如何建立 Web 主控方案？
    </br>
    **答**：Web 主控方案是容器，因此您不能建立空白的 Web 主控方案。不過，新 Web 主控方案是在網站建立期間明確建立。
    </br>
    若要使用新 [Azure 入口網站預覽] 中的 UI 來執行此動作，請按一下 [新增] 並選取 [網站]，即可開啟網站建立分頁。在以下的第一個影像中，您可以在左下角看到 [新增] 圖示，而在第二個影像中，您可以看到 [網站] 建立分頁、[Web 主控方案] 分頁和 [價格層] 分頁：
    </br>
    </br>
    ![Create a new website][]
    </br>
    </br>
    ![Website, Web Hosting Plan and pricing tier blades][]
    </br>
    </br>
    在這個範例中，我們選擇建立一個名為 **contosomarketing** 的新網站，並將它放在名為 **contoso** 的新 Web 主控方案中。為這個 Web 主控方案選取的價格層為**小型標準**。如需 Web 主控方案價格層，以及每個層級所提供之功能、價格和調整選項的詳細資料，請造訪 [Azure 網站 Web 主控方案規格][]。
    </br>
    您也應該知道，您也可以在現有的 Azure 入口網站中建立 Web 主控方案。實現方式是從 [Web 主控方案] 下拉式清單選取 [建立新 Web 主控方案]，利用 [快速建立] 精靈進行：
    </br>
    </br>
    ![Create new web hosting plan in the existing portal][]
    </br>
    </br>
    在這個範例中，我們選擇建立一個名為 **northwind** 的新網站，以及選擇建立新的 Web 主控方案。此作業的結果會是名為 **default0** 的新 Web 主控方案，其包含 **northwind** 網站。透過此方式建立的所有 Web 主控方案將遵循此命名慣例，而 Web 主控方案在建立之後即不能加以重新命名。同時，透過此程序建立的 Web 主控方案將建立在**免費**價格層。
    </br>
    **問**：如何指派網站至 **Web 主控方案**？
    </br>
    **答**：網站是在網站建立程序期間指派給 Web 主控方案。若要使用新 [Azure 入口網站預覽] 中的 UI 來執行此動作，請按一下 [新增]，並選取 [網站]：
    </br>
    </br>
    ![Create a new website][1]
    </br>
    </br>
    然後，在網站建立分頁選取主控方案：
    </br>
    </br>
    ![Select a hosting plan][]
    </br>
    </br>
    您也可以使用現有的 Azure 入口網站，將網站建立到特定的 Web 主控方案。這是在 [快速建立] 精靈中完成。輸入網站 URL 之後，使用 [Web 主控方案] 下拉式清單來選取要加入網站的方案：
    </br>
    </br>
    ![Select a hosting plan in the existing portal][]
    </br>
    </br>
    **問**：如何將網站移至不同的 Web 主控方案？
    </br>
    **答**：您可以使用 Azure 預覽入口網站，將網站移至不同的 Web 主控方案。網站也可以在隸屬於相同資源群組的相同地理區中的 Web 主控方案之間移動。
    </br>
    若要將網站移至其他方案，請導覽至您要移動之網站的網站分頁。然後按一下 [Web 主控方案]：
    </br>
    </br>
    ![Choose a new or existing web hosting plan][]
    </br>
    </br>
    這隨即會開啟 [Web 主控方案] 分頁。在此時，您可以挑選現有的 Web 主控方案，或建立新的方案。在不同地理位置或資源群組中的方案，會呈現灰色停用狀態，而無法加以選取。
    </br>
    請注意，每個 Web 主控方案都有自己的價格層。當您將網站從**免費**層 Web 主控方案移至**標準** Web 主控方案，您的網站將可以利用標準層的所有功能和資源。
    </br>
    </br>
    **問**：如何擴大 Web 主控方案？
    </br>
    **答**：有兩種方式可擴大 Web 主控方案。一個方式是向上擴大您的 Web 主控方案，以及與該 Web 主控方案關聯的所有網站。透過變更 Web 主控方案的價格層，在該 Web 主控方案中的所有網站將受限於該價格層定義的所有功能和資源。
    </br>
    在以下的影像中，您可以看到 **Web 主控方案**分頁以及**價格層**分頁。按一下 [Web 主控方案] 分頁中的 [價格層] 部分，可展開 [價格層] 分頁，您可以在其中變更 Web 主控方案的價格層：
    </br>
    </br>
    ![The Web Hosting Plan blade and the Pricing Tier][]
    </br>
    </br>
    擴大方案的第二個方式是，利用增加 Web 主控方案中執行個體的數量，來向外擴大。在以下的影像中，您可以看到 **Web 主控方案**分頁和**調整規模**分頁。按一下 [Web 主控方案] 分頁中的 [調整規模] 區域可將其展開，並允許變更方案的執行個體計數：
    </br>
    </br>
    ![Changing the instance count of a hosting plan][]
    </br>
    </br>
    由於上圖中的 Web 主控方案是設為使用**標準**價格層，隨即會啟用 [自動調整規模] 選項。
    </br>
    在完整的 Azure 入口網站中執行此動作，可在 [調整規模] 索引標籤中進行，如下圖所示：
    </br>
    </br>
    ![Changing the instance count of a hosting plan in the existing portal][]
    </br>
    </br>
    **問**：如何刪除 Web 主控方案？
    </br>
    **答**：若要刪除 Web 主控方案，您必須先刪除與其關聯的所有網站。一旦 Web 主控方案中的所有網站都已被刪除，便可從 [Web 主控方案] 分頁刪除 Web 主控方案：
    </br>
    </br>
    ![Deleting a web hosting plan][]
    </br>
    </br>
    在完整版 Azure 入口網站中，刪除 Web 主控方案中的最後一個網站，將會自動刪除關聯的 Web 主控方案。
    </br>
    **問**：如何監視 Web 主控方案？
    </br>
    **答**：您可以使用 [Web 主控方案] 分頁中的 [監視] 部分來監視 Web 主控方案：
    </br>
    </br>
    ![Monitoring a web hosting plan][]
    </br>
    </br>
    監視控制項可透過在控制項上按一下滑鼠右鍵並選取 [編輯查詢] 加以自訂：
    </br>
    </br>
    ![Editing the monitoring controls][]
    </br>
    </br>
    公開的度量為：
    </br>
-   CPU 百分比
    </br>
-   記憶體百分比
    </br>
-   磁碟佇列長度
    </br>
-   HTTP 佇列長度。
    </br>
    此度量代表屬於 Web 主控方案之執行個體的平均使用量。所有度量均可用來設定警示以及「自動調整規模」規則。
    </br>

    ## 重點與結論

    </br>
    Web 主控方案代表可跨網站共用的一組特性和功能。「Web 主控方案」提供您將網站配置到特定資源集虛擬機器的彈性，並進一步將您的 Azure 資源配置與網站使用量最佳化。

  [Resource Groups and Web Hosting Plans]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview01.png
  [Creating a new Web Hosting Plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview02.png
  [See all your website as a flat list]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview03.png
  [See all the resource groups that have been created]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview04.png
  [Create a new website]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview05.png
  [Website, Web Hosting Plan and pricing tier blades]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview06.png
  [Azure 網站 Web 主控方案規格]: http://go.microsoft.com/?linkid=9845586
  [Create new web hosting plan in the existing portal]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview07.png
  [1]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview08.png
  [Select a hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview09.png
  [Select a hosting plan in the existing portal]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview10.png
  [Choose a new or existing web hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview22.png
  [The Web Hosting Plan blade and the Pricing Tier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview16.png
  [Changing the instance count of a hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview17.png
  [Changing the instance count of a hosting plan in the existing portal]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview18.png
  [Deleting a web hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview19.png
  [Monitoring a web hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview20.png
  [Editing the monitoring controls]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview21.png
