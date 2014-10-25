<properties linkid="manage-scenarios-choose-web-app-service" urlDisplayName="Web Options for Azure" pageTitle="Azure Websites, Cloud Services and Virtual Machines comparison" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Azure Websites, Cloud Services, and Virtual Machines for hosting web applications. Review a feature comparison." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Azure 網站、雲端服務與虛擬機器之比較

Azure 提供數種裝載 Web 應用程式的方式，例如「[Azure 網站][]」、「[雲端服務][]」及「[虛擬機器][]」。看到這些不同選項後，您可能不確定哪個選項最符合自己的需求，或可能不清楚 IaaS 與 PaaS 有何不同等概念。本文可協助您了解這些選項，讓您更能做出適合自己 Web 案例的選擇。雖然所有這三個選項都可讓您在 Azure 執行可高度調整的 Web 應用程式，但其彼此之間仍有差異，您可據以做出較適當的選擇。

在許多情況下，「Azure 網站」是最佳選項。其提供了簡單而靈活的部署與管理選項，而且能夠裝載大量網站。您可以利用 Web 應用程式庫中的熱門軟體 (例如 WordPress) 迅速建立新網站，也可以將現有網站移至「Azure 網站」。您也可以使用 [Azure WebJobs SDK][] (英文) (目前為預覽版) 新增背景工作處理。

您也可以選擇在「Azure 雲端服務」或「Azure 虛擬機器」上裝載 Web 應用程式。當您的 Web 層需要這些選項所提供多一層的控制與自訂能力時，這些選項會是很好的選擇；不過，更多的控制能力，代表應用程式的建立、管理與部署方式也一定更複雜。下圖比較這三個選項的優劣。

![ChoicesDiagram][]

「網站」的設定、管理與監視方式更容易，但是您可運用的組態選項也更少。重點是，當沒有夠強的理由要將 Web 前端置於「雲端服務」或「虛擬機器」上時，請使用「Azure 網站」。本文件的其餘內容將提供更多的資訊來幫助您做更好的選擇。其中包括：

-   [案例][]
-   [服務摘要][]
-   [功能比較][]

## <a name="scenarios"></a>案例

### 我是小型企業業主，需要有便宜的方式來裝載網站，但又想顧及網站未來成長的可能性

「Azure 網站」(WAWS) 是這個案例的絕佳解決方案，因為您可先免費使用它，等到需要更多功能時再加入功能即可。例如，每個免費網站都擁有 Azure 提供的網域 (*your\_company*.azurewebsites.net)。當您準備好開始使用自己的網域時，可以加入該網域，且每月只需支付 $9.80 美元 (2014 年 1 月的價格)。另外還有其他許多服務與調整選項，可讓網站隨使用者需求的增加來發展。使用 Azure 網站，您可以：

-   從免費版本開始，視需要進行向上調整。
-   使用應用程式庫，迅速設定熱門的 Web 應用程式，例如 WordPress。
-   視需要新增其他 Azure 服務與功能至您的應用程式。
-   以 *your\_company*.azurewebsites.net 網域名稱隨附的憑證，用 HTTPS 保護您網站的安全。

### 我是網頁或平面設計師，想要幫客戶設計及建立網站

對於 Web 開發人員而言，Azure 網站可提供您建立精緻 Web 應用程式所需的一切。「網站」可與 Visual Studio 和 SQL Database 等工具緊密整合。使用**網站**，開發人員可以：

-   使用命令列工具執行[自動化工作][] (英文)。
-   使用熱門語言，例如 [.Net][] (英文)、[PHP][] (英文)、[Node.js][] (英文) 及 [Python][] (英文)。
-   有三種不同的調整層級可選，以向上調整至非常高的容量。
-   與其他 Azure 服務 (如 [SQL Database][]、[服務匯流排][]及[儲存體][])，或 [Azure 市集][]上的合作夥伴供應項目 (如 MySQL 和 MongoDB) 整合。
-   與 Visual Studio、Git、WebMatrix、WebDeploy、TFS 和 FTP 等工具整合。

### 我想將含 Web 前端的多層式應用程式移轉至雲端

如果您是執行多層式應用程式 (例如一個會與資料庫伺服器通訊以儲存及擷取網站資料的網頁伺服器)，則您在 Azure 有數個選項可用。這些架構選項包括「網站」、「雲端服務」及「虛擬機器」。首先，**網站**對於您的解決方案的 Web 層是不錯的選項，而且可搭配 Azure SQL Database 來建立兩層式架構。「網站」也可讓您使用 Azure WebJobs SDK 預覽版來執行背景或長時間執行的處理序。如果您需要更複雜的架構或更靈活的調整選項，則「雲端服務」或「虛擬機器」是更好的選擇。

「雲端服務」可讓您：

-   將 Web、中層與後端服務裝載於可調整的 Web 與背景工作角色上。
-   僅將中介層與背景服務裝載於背景工作角色上，並讓前端留在Azure 網站上。
-   分開調整前端與後端服務。

「虛擬機器」可讓您：

-   更輕易地移轉高度自訂的環境做為虛擬機器映像。
-   執行「網站」或「雲端服務」上所無法設定的軟體或服務。

### 我的應用程式需要在高度自訂的 Windows 或 Linux 環境中才能運作

如果您的應用程式需要用到安裝或設定方式複雜的軟體與作業系統，則「虛擬機器」可能是最佳解決方案。使用「虛擬機器」，您可以：

-   使用虛擬機器庫來得到初步的作業系統，例如 Windows 或 Linux，然後根據自己的應用程式需求加以自訂。
-   建立並上傳現有內部部署伺服器的自訂映像，使其執行於 Azure 中的虛擬機器上。

### 我的網站使用開放原始碼軟體，我想要將該軟體裝載在 Azure 上

所有三個選項都可讓您裝載開放原始碼語言與架構。「雲端服務」需要您使用啟動工作，來安裝並設定任何在 Windows 上執行的必要開放原始碼軟體。「虛擬機器」則需要您在機器映像 (可以是 Windows 或 Linux 型) 上安裝並設定軟體。如果您的開放原始碼架構在「網站」上受到支援，則這會是裝載這類應用程式更簡單的方式，因為「網站」可以被自動設定成使用您的應用程式所需的語言與架構。**網站**可讓您：

-   使用許多熱門的開放原始碼語言，例如 [.NET][.Net] (英文)、[PHP][] (英文)、[Node.js][] (英文) 及 [Python][] (英文)。
-   設定 WordPress、Drupal、Umbraco、DNN 及其他許多協力廠商 Web 應用程式。
-   移轉現有的應用程式，或從應用程式庫建立新的應用程式。

### 我有個主要商務應用程式需要連線至公司網路

若要建立企業營運系統應用程式，您的網站可能需要直接存取公司網路上的服務或資料。這個動作在**網站**、**雲端服務**及**虛擬機器**上皆可行。您採取的方法會造成差異，包括：

-   「網站」可以透過使用服務匯流排轉送，來安全地連線至內部部署資源。如此可讓公司網路上的服務代 Web 應用程式執行工作，而不需將一切都移至「雲端」或者設定虛擬網路。
-   「雲端服務」及「虛擬機器」可以利用虛擬網路。事實上，虛擬網路可讓 Azure 中執行的機器連線至內部部署網路。如此一來，Azure 就成為公司資料中心的附屬延伸。

### 我想要裝載 REST API 或 Web 服務供行動用戶端使用

HTTP 型 Web 服務可讓您支援各種用戶端，包括行動用戶端。ASP.NET Web API 等架構會與 Visual Studio 整合，讓您更容易建立及取用 REST 服務。這些服務是透過 Web 端點公開，因此在 Azure 上可以使用任何 Web 裝載技術來支援此案例。不過，**網站**是裝載 REST API 的絕佳選擇。使用網站，您可以：

-   迅速建立一個網站，以將 HTTP Web 服務裝載於 Azure 分散全球的其中一個資料中心內。
-   移轉現有服務或建立新服務 (或許是透過在 Visual Studio 中利用 ASP.NET Web API)。
-   以單一執行個體達到可用性 SLA，或向外延展至多部專用機器。
-   使用已發佈的網站來提供 REST API 給任何 HTTP 用戶端，包括行動用戶端。

## <a name="services"></a>服務摘要

[Azure 網站][]可讓您在 Azure 迅速建置可高度調整的網站。您可以使用 Azure 入口網站或命令列工具，以熱門語言 (例如 .NET、PHP、Node.js 及 Python) 設定網站。受支援的架構已經部署好，不需要其他安裝步驟。Azure 網站庫包含許多協力廠商應用程式 (例如 Drupal 及 WordPress) 以及開發架構 (例如 Django 及 CakePHP)。建立站台後，您可以移轉現有網站，或建置全新網站。「網站」可省去管理實體硬體的需要，而且還提供數個調整選項。您可以從共用的多租用戶模型，移至有專用機器來處理連入流量的標準模式。「網站」也可讓您與其他 Azure 服務 (例如 SQL Database、服務匯流排和儲存體) 整合。使用 [Azure WebJobs SDK][] (英文)預覽版，您可以新增背景處理。簡言之，「Azure 網站」可支援各種語言、開放原始碼應用程式和部署方法 (FTP、Git、Web Deploy 或 TFS)，使您更容易專心開發應用程式。如果您沒有非用「雲端服務」或「虛擬機器」不可的特殊需求，則「Azure 網站」通常是最佳選擇。

[雲端服務][]可讓您在豐富的平台即服務 (PaaS) 環境中建立高度可用、可調整的 Web 應用程式。不同於「網站」，雲端服務是先建立於開發環境 (例如 Visual Studio) 中，然後再部署至 Azure。若要使用 PHP 等架構，則需要採取自訂的部署步驟或工作，以在角色啟動時安裝架構。「雲端服務」的主要優點是能夠支援更複雜的多層式架構。單一雲端服務可由一個前端 Web 角色及一或多個背景工作角色組成。每層都可以獨立受到調整。您對 Web 應用程式基礎結構的控制程度也更高。例如，您可以透過桌面遠端連線到執行角色執行個體的機器。您也可以撰寫指令碼，以在角色啟動時執行更進階的 IIS 與機器組態變更，包括需要系統管理員控制權的工作。

[虛擬機器][]可讓您在 Azure 中的虛擬機器上執行 Web 應用程式。此功能也稱為基礎結構即服務 (IaaS)。您可以透過入口網站建立新的 Windows Server 或 Linux 機器，或上傳現有的虛擬機器映像。「虛擬機器」讓您對作業系統、組態和已安裝的軟體與服務擁有最多控制。若要將複雜的內部部署 Web 應用程式迅速移轉至雲端，這是不錯的選項，因為您可以移動整個機器的內容。透過虛擬網路，您也可以將這些虛擬機器連線至內部部署公司網路。如同「雲端服務」，您可以遠端存取這些機器，而且能夠在系統管理層級執行組態變更。不過，不同於「網站」和「雲端服務」，您必須完全在基礎結構層級管理虛擬機器映像與應用程式架構。一個基本的例子是您必須將自己的修補程式套用至作業系統。

## <a name="features"></a>功能比較

下表比較「網站」、「雲端服務」與「虛擬機器」的功能，以協助您做出最佳選擇。如果方塊中有星號，表示表格之後的附註中將有詳細說明。

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">功能</th>
<th align="left">網站</th>
<th align="left">雲端服務 (Web 角色)</th>
<th align="left">虛擬機器</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>存取服務匯流排、儲存體、SQL Database 等服務。</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>裝載多層式架構的 Web 或 Web 服務層</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>裝載多層式架構的中間層</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>整合 MySQL 即服務的支援</p></td>
<td align="left">X</td>
<td align="left">X <sup>1</sup></td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>支援 ASP.NET、傳統 ASP、Node.js、PHP、Python</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>向外延展至多個執行個體而不重新部署</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X <sup>2</sup></td>
</tr>
<tr class="odd">
<td align="left"><p>支援 SSL</p></td>
<td align="left">X <sup>3</sup></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>整合 Visual Studio</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>遠端偵錯</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>利用 TFS 來部署程式碼</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>利用 GIT、FTP 來部署程式碼</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>利用 Web Deploy 來部署程式碼</p></td>
<td align="left">X</td>
<td align="left"><sup>4</sup></td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>支援 WebMatrix</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>近乎即時的部署</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>各執行個體一起共用內容和組態</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>向上調整至更大的機器而不重新部署</p></td>
<td align="left">X</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>多個部署環境 (生產環境和預備環境)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left"><p>利用 Azure 虛擬網路進行網路隔離</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>支援 Azure 流量管理員</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>透過桌面遠端來存取伺服器</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="odd">
<td align="left"><p>能夠定義/執行啟動工作</p></td>
<td align="left"></td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>自動管理 OS 更新</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left"><p>整合式端點監視</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left">X</td>
</tr>
<tr class="even">
<td align="left"><p>順暢切換平台 (32 位元/64 位元)</p></td>
<td align="left">X</td>
<td align="left">X</td>
<td align="left"></td>
</tr>
</tbody>
</table>

<sup>1</sup> Web 或背景工作角色可透過 ClearDB 的供應項目來整合 MySQL 即服務，但此動作無法透過管理入口網站工作流程進行。

<sup>2</sup> 雖然「虛擬機器」可向外延展至多個執行個體，但這些機器上執行的服務必須是寫成可應付這個向外延展情況。必須多設定一個負載平衡器，以在機器間路由傳送要求。最後，應該為所有參與相同角色的機器建立一個同質群組，以保護它們不會在維護失敗或硬體故障時同時重新啟動。

<sup>3</sup> 若為「網站」，只支援在標準模式針對自訂網域名稱使用 SSL。如需對「網站」使用 SSL 的詳細資訊，請參閱[設定 Azure 網站的 SSL 憑證][]。

<sup>4</sup> 若為雲端服務，則支援使用 Web Deploy 來部署至單一執行個體角色。不過，生產角色需要有多個執行個體才能達到 Azure SLA。因此，對於正式運作的雲端服務，Web Deploy 不是適合的部署機制。

  [Azure 網站]: http://go.microsoft.com/fwlink/?LinkId=306051
  [雲端服務]: http://go.microsoft.com/fwlink/?LinkId=306052
  [虛擬機器]: http://go.microsoft.com/fwlink/?LinkID=306053
  [Azure WebJobs SDK]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png
  [案例]: #scenarios
  [服務摘要]: #services
  [功能比較]: #features
  [自動化工作]: http://www.windowsazure.com/zh-tw/documentation/scripts/?services=web-sites
  [.Net]: http://www.windowsazure.com/zh-tw/develop/net/
  [PHP]: http://www.windowsazure.com/zh-tw/develop/php/
  [Node.js]: http://www.windowsazure.com/zh-tw/develop/nodejs/
  [Python]: http://www.windowsazure.com/zh-tw/develop/python/
  [SQL Database]: http://www.windowsazure.com/zh-tw/documentation/services/sql-database/
  [服務匯流排]: http://www.windowsazure.com/zh-tw/documentation/services/service-bus/
  [儲存體]: http://www.windowsazure.com/zh-tw/documentation/services/storage/
  [Azure 市集]: http://www.windowsazure.com/zh-tw/gallery/store/
  [設定 Azure 網站的 SSL 憑證]: http://www.windowsazure.com/zh-tw/develop/net/common-tasks/enable-ssl-web-site/
