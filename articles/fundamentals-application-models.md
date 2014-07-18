<properties  umbracoNaviHide="0" pageTitle="Application Model" metaKeywords="Azure, Azure, application model, Azure application model, development model, Azure development model, hosted service, Azure hosted service, web role, worker role" description="Learn about the Azure hosted service application model. Understand core concepts, design considerations, defining and configuring your application, and scaling." linkid="dev-net-fundamentals-application-model" urlDisplayName="Application Model" headerExpose="" footerExpose="" disqusComments="1" title="Application Model" authors="" />

# Azure 執行模型

Azure 對於執行的應用程式提供不同的執行模型。

各個執行模型有不同的服務集，因此您選擇哪一個取決於您嘗試執行的動作。本文分別說明三種技術，並提供使用的範例。

## 目錄

* [虛擬機器](#VMachine)
* [網站](#WebSites)
* [雲端服務](#CloudServices)
* [我應該使用什麼？做決定](#WhatShouldIUse)

<h2><a  id="VMachine" ></a>虛擬機器</h2>


Azure 虛擬機器可以讓開發人員、IT 操作人員及其他人在雲端建立和使用虛擬機器。提供的*基礎結構即服務 (IaaS)* 這項技術可用於許多方面。[圖 1](#Fig1) 顯示其基本元件。

<a name="Fig1"></a>![01_CreatingVMs](./media/fundamentals-application-models/ExecModels_01_CreatingVMs.png)

**圖 1：Azure 虛擬機器提供「基礎結構即服務」。**

如圖所示，您可以使用 Azure 管理入口網站或 REST 型 Azure 服務管理 API 建立虛擬機器。從任何常用的瀏覽器均可存取管理入口網站，包括 Internet Explorer、Mozilla 及 Chrome。對於 REST API，Microsoft 提供 Windows、Linux 及 Macintosh 系統的用戶端指令碼工具。其他軟體也可以免費使用這個介面。

無論您如何存取該平台，建立新的 VM 都需要選擇 VM 映像的虛擬硬碟 (VHD)。這些 VHD 均儲存在 Azure Blob 中。

開始進行時，您有兩個選擇

1.  上傳您自己的 VHD 2.  使用 Microsoft 及其合作夥伴在 Azure 虛擬機器組件庫或 Microsoft 開放原始碼 [VMDepot][1] 網站提供的 VHD。

組件庫和 VMDepot 中的 VHD 均包含乾淨的 Microsoft 和 Linux 作業系統映像，以及其中安裝 Microsoft 和其他協力廠商產品的映像。這些選項隨著時間不斷增加。其中包含下列的各種版本、版次和組態：

* Windows Server
* Linux 伺服器，例如 Suse、Ubuntu 和 CentOS
* SQL Server
* BizTalk Server
* SharePoint Server

您可以藉由 VHD 指定新 VM 的大小。[Azure 的虛擬機器和雲端服務大小][2]列出各個大小的完整統計資料。

* **超小**，具備共用核心及 768 MB 記憶體。
* **小**，具備 1 個核心及 1.75GB 記憶體。
* **中**，具備 2 個核心及 3.5GB 記憶體。
* **大**，具備 4 個核心及 7GB 記憶體。
* **超大**，具備 8 個核心及 14GB 記憶體。
* **A6**，具備 4 個核心及 28GB 記憶體。
* **A7**，具備 8 個核心及 56GB 記憶體。

最後，您選擇應該執行新 VM 的美國、歐洲或亞洲 Azure 資料中心。

VM 執行後，您將對於執行的每個小時付費，移除該 VM 即停止計費。您付費的金額並非取決於使用的 VM 數目，而是取決於時間長度。VM 閒置 1 小時的費用與高度負載 1 小時的費用相同。

各個執行中的 VM 都有相關聯的*作業系統磁碟*在 Blob 中。使用組件庫 VHD 建立 VM 時，該 VHD 將複製到 VM 的作業系統磁碟。您對於執行中 VM 的作業系統所做的任何變更都會儲存在這裡。例如，如果您安裝修改 Windows 登錄的應用程式，該變更將儲存在 VM 的作業系統磁碟中。下次您從該作業系統磁碟建立 VM 時，VM 將接續您離開時所呈現的狀態繼續執行。對於組件庫中儲存的 VHD，Microsoft 會套用必要的更新，例如作業系統修補程式。對於您自己作業系統磁碟的 VHD，您必須負責套用這些更新 (雖然 Windows Update 預設為啟動)。

使用 Sysprep 工具也可以修改和擷取執行中的 VM。Sysprep 會移除機器名稱等特定資料，以便使用 VHD 映像建立一般組態相同的其他
VM。這些映像將連同上傳的映像儲存在管理入口網站中，以供用來建立其他 VM。

虛擬機器也會監控對於您建立的各個 VM 進行代管的硬體。如果執行 VM 的實體伺服器故障，平台會發現這個狀況，並啟動其他機器上相同的
VM。而且，假設您有正確的授權，就可以從作業系統磁碟複製變更後的 VHD，然後在其他位置執行，例如在內部部署的資料中心或其他雲端提供者。

除了作業系統磁碟之外，VM 也有一或多個資料磁碟。即使這些磁碟都像是 VM 的掛接磁碟機，不過各個磁碟的內容實際上都儲存在 Blob 中。對於資料磁碟進行的每次寫入都會一致儲存在基礎 Blob 中。對於所有 Blob，Azure 會在單一資料中心和跨資料中心複製這兩者，以防範故障。

使用管理入口網站、PowerShell 和其他指令碼工具，或直接透過 REST API 均可管理執行中 VM。(事實上，能夠透過管理入口網站執行的動作均可透過這個 API 以程式設計方式執行。)RightScale 及 ScaleXtreme 等 Microsoft 合作夥伴也提供透過 REST API 進行的管理服務。

Azure 虛擬機器可用於許多用途。Microsoft 著重的主要案例包括：

* **開發和測試的 VM。**開發群組一般需要有特定組態可建立應用程式的 VM。Azure 虛擬機器能夠以直截且經濟的方式建立和使用這些 VM，並且於不再需要時加以移除。
* **在雲端執行應用程式。**對於部分應用程式，在公用雲端執行具有經濟效益。以尖峰需求量相當大的應用程式為例。為資料中心購買足夠的機器來執行這個應用程式當然不成問題，但是大部分機器在大多數情況下很可能閒置不用。在 Azure 執行這個應用程式只會讓您在有必要的情況下付費使用額外的 VM，只要需求尖峰一過就可以關閉。另外，假設您立即需要隨選運算資源，但是不想受約束。同樣地，Azure 會是正確的選擇。
* **將您自己的資料中心擴展到公用雲端。**藉由 Azure 虛擬網路，貴公司可以建立虛擬網路 (VNET) 讓一組 Azure VM 成為您本身內部部署網路的一部分。這能夠在 Azure 上執行如 SharePoint 的應用程式和其他應用程式，因此比在資料中心部署和執行這些應用程式更簡單及/或更節省成本。
* **災害復原。**IaaS 型災害復原可以讓您在確實需要運算資源時才付費使用，而不需要將成本持續浪費在不常使用的備份資料中心上。例如，如果主要資料中心故障，您可以建立在 Azure 上執行的 VM 來執行基本的應用程式，然後於不再需要時關閉這些應用程式。

這些只是可能性，但是正好說明您可以如何使用 Azure 虛擬機器。

### 群組 VM：雲端服務

您使用 Azure 虛擬機器建立新的 VM 時，可以選擇獨立執行或在*雲端服務*的 VM 群組中執行。(儘管名稱類似，但是兩者並不相同，所以請勿將這個概念與 Azure 的 PaaS 技術名稱「雲端服務」相混淆。)各個獨立的 VM 都會被指派各自的公用 IP 位址，透過一個公用 IP 位址即可存取同一個雲端服務中所有的 VM。[圖 2](#Fig2) 顯示運作架構。

<a name="Fig2"></a>![02_CloudServices](./media/fundamentals-application-models/ExecModels_02_CloudServices.png)

**圖 2：各個獨立的 VM 都有各自的公用 IP 位址，透過一個公用 IP 位址即可存取分組為同一個雲端服務的 VM。**

例如，如果要建立虛擬機器以便建立和測試簡單的應用程式，您可以使用獨立 VM。如果要建立有 Web 前端、資料庫和甚至中層的多層式應用程式，可以將多個 VM 連接到雲端服務，如圖 2 所示。

將 VM 分組為雲端服務也可以讓您使用其他選項。Azure 對於相同雲端服務中的 VM 提供負載平衡，平均分配使用者要求。以這種方式連接的 VM 也能夠透過本機網路在 Azure 資料中心內直接相互通訊。

相同雲端服務中的 VM 也能夠分組為一或多個*可用性設定組*。若要了解為何這相當重要，可以試想執行多個前端 VM 的 Web 應用程式。如果在同一個實體機器或同一組機器中部署所有 VM，一個硬體故障就會導致全部失效。不過，如果將這些 VM 分組為可用性設定組，Azure 就會在資料中心部署這些 VM，以免任何一個 VM 連帶造成故障。

### 案例：使用 SQL Server 執行應用程式

為了更深入了解 Azure 虛擬機器的運作方式，可以更進一步探討一些案例。例如，假設您要建立可靠且可擴充的 Web 應用程式在 Azure 上執行。其中一種方式是在一或多個 Azure VM 中執行應用程式的邏輯，然後使用 SQL Server 進行資料管理。[圖 3](#Fig3) 顯示運作架構。

<a name="Fig3"></a>![03_AppUsingSQLServer](./media/fundamentals-application-models/ExecModels_03_AppUsingSQLServer.png)

**圖 3：在 Azure 虛擬機器中執行的應用程式可以使用 SQL Server 進行儲存。**

在這個範例中，兩種 VM 都是從組件庫中的標準 VHD 建立而成的。應用程式的邏輯會在 Windows Server 2008 R2 上執行，因此開發人員可以從這個 VHD 建立 3 個 VM，然後在各個 VM 中安裝自己的應用程式。由於所有 VM 都在同一個雲端服務中，因此開發人員可以設定硬體負載平衡平均分配要求。開發人員也可以從包含 SQL Server 2012 的組件庫 VHD 建立兩個 VM。這些 VM 執行之後，即可設定各個執行個體中的 SQL Server 使用具備自動容錯移轉功能的資料庫鏡像。這並非必要的功能，應用程式可以只使用一個 SQL Server 執行個體，不過採用這種作法可提升可靠性。

### 案例：執行 SharePoint 伺服器陣列

假設組織想要建立 SharePoint 伺服器陣列，但是不要在自己的資料中心執行該伺服器陣列。或許內部部署資料中心缺乏資源，或者建立伺服器陣列的業務單位不想要與內部 IT 群組往來。在這些情況下，在 Azure 虛擬機器執行 SharePoint 有其道理。[圖 4](#Fig4) 顯示運作架構。

<a name="Fig4"></a>![04_SharePointFarm](./media/fundamentals-application-models/ExecModels_04_SharePointFarm.png)

**圖 4：Azure 虛擬機器能夠讓 SharePoint 伺服器陣列在雲端運作。**

SharePoint 伺服器陣列有多個元件，分別在從不同 VHD 建立的 Azure VM 中執行。需要的元件如下：

* Microsoft SharePoint。組件庫中有試用映像，或者組織提供本身的 VHD。
* Microsoft SQL Server。SharePoint 需要 SQL Server，因此組織可以從標準組件庫 VHD 建立執行
  SQL Server 2012 的 VM。
* Windows Server Active Directory。SharePoint 也需要 Active
  Directory，因此組織可以使用組件庫中的 Windows Server
  映像在雲端中建立網域控制站。這並非絕對必要，另外也可以使用內部部署網域控制站，不過 SharePoint 會經常與 Active
  Directory 互動，因此，這裡顯示的作法可以達到較好的效能。

雖然圖中並未顯示，不過這個 SharePoint 伺服器陣列可能使用 Azure 虛擬網路連線到內部部署網路。這可以讓 VM 以及其中包含的應用程式出現在該網路的使用者近端。

如這些範例所示，您可以使用 Azure 虛擬機器建立新的應用程式並在雲端中執行、執行現有的應用程式，或以其他方式執行。無論選擇哪個選項，技術的目標都是相同的：提供公用雲端運算的通用基礎。

<h2><a  id="WebSites" ></a>網站</h2>


使用者可以用各種方式使用 Web 技術。公司可能需要移轉或設定能夠處理一週數百萬次點擊，並且可部署到全球多個資料中心的平台服務網站。Web 設計機構可能與開發人員團隊合作，建立能夠處理成千上萬個使用者的自訂 Web 應用程式。企業開發人員可能需要設定應用程式，對於經過驗證的使用者從使用者的企業 Active Directory 追蹤雲端中的費用報告。IT 顧問可能使用常用的開放原始碼應用程式，設定小型企業的內容管理系統。以上所有作業都可以使用 Azure 虛擬機器完成。不過，建立和管理原始 VM 需要一些技巧和努力。如果您需要實作網站或 Web 應用程式，有一個較簡單 (且較低成本) 的解決方案：這種方法通常稱為「平台即服務」(PaaS)。如圖 5 所示，Azure 對於網站提供這種方法。

<a name="Fig5"></a>![05_Websites](./media/fundamentals-application-models/ExecModels_05_Websites.png)

**圖 5：Azure 網站支援靜態網站、常用 Web 應用程式，以及使用各種技術建立的自訂 Web 應用程式。**

Azure 網站以 Azure 雲端服務為基礎，能夠建立最適合執行 Web 應用程式的「平台即服務」解決方案。如圖所示，網站在一組個別的 VM
上執行，這些 VM 可能包含多位使用者建立的多個網站，網站也在屬於個別使用者的標準 VM 上執行。VM 是 Azure 網站管理的資源集區一部分，因此可達到高可靠性和容錯性。開始使用相當簡單。藉由 Azure 網站，使用者可以選擇眾多應用程式、架構和範本，並且在短時間內建立網站。使用者可以接著使用自己最愛的開發工具 (WebMatrix、Visual Studio、其他任何編輯器) 和原始檔控制選項來設定持續整合，並且進行團隊開發。以 MySQL 資料庫為基礎的應用程式可以使用 Microsoft 合作夥伴 ClearDB 對於 Azure 提供的 MySQL 服務。開發人員可以建立網站的大型可擴充 Web 應用程式。這項技術支援使用 ASP.NET、PHP、Node.js 和 Python 建立應用程式。例如，應用程式可以使用黏性工作階段，而且現有的 Web 應用程式可以移到這個雲端平台，完全不需要變更。以網站為基礎的應用程式可以自由運用 Azure 的其他層面，例如服務匯流排、SQL 資料庫和 Blob 儲存體。您也可以在不同的 VM 中執行應用程式的多個複本，而且網站可自動進行要求的負載平衡。由於新的網站執行個體是在已經存在的 VM 中建立的，因此可以很快啟動新的應用程式執行個體，速度比等候新 VM 建立還快。如[圖 5](#Fig5) 所示，您可以透過多種方式，將程式碼和其他 Web 內容發佈到網站。您可以使用 FTP、FTPS 或 Microsoft 的 WebDeploy 技術。網站也支援從原始檔控制系統發佈程式碼，包括 Git、GitHub、CodePlex、BitBucket、Dropbox、Mercurial、Team Foundation Server 和雲端型 Team Foundation Service。

<h2><a  id="CloudServices" ></a>雲端服務</h2>


Azure 虛擬機器提供 IaaS，而 Azure 網站提供 Web 代管。第三個運算選項「雲端服務」提供*平台即服務 (PaaS)*。這項技術能夠支援可擴充、可靠且低操作成本的應用程式。開發人員也不需要擔心管理使用的平台，而能夠完全專注在應用程式上。[圖6](#Fig6) 闡明此概念。

<a name="Fig6"></a>![06_CloudServices2](./media/fundamentals-application-models/ExecModels_06_CloudServices2.png)

**圖 6：Azure 雲端服務提供「平台即服務」。**

和另一個 Azure 運算選項一樣，「雲端服務」也需要 VM。這項技術提供兩個略微不同的 VM 選項：*Web 角色*執行個體執行有 IIS 的Windows Server 變體，而*背景工作角色*執行個體則執行沒有 IIS 的同一個 Windows Server 變體。雲端服務應用程式需要這兩個選項的一些組合。

例如，簡單的應用程式可以只使用 Web 角色，而較複雜的應用程式可以使用 Web 角色處理使用者的連入要求，然後將這些要求產生的工作傳送給背景工作角色進行處理。(這個通訊會使用服務匯流排或 Azure 佇列。)

如圖所示，單一應用程式中所有的 VM 會在同一個雲端服務中執行，如同先前對於 Azure 虛擬機器的描述。因此，使用者可以透過單一公用 IP
位址存取應用程式，而且要求自動平均分配給應用程式的 VM。和使用 Azure 虛擬機器建立的雲端服務一樣，該平台將在雲端服務應用程式中部署 VM，以避免任何一個 VM 造成故障。

即使應用程式在虛擬機器中執行，也必須了解雲端服務提供 PaaS，而非 IaaS。其中一種思考的方式是：藉由 IaaS，例如 Azure 虛擬機器，您先建立並設定執行應用程式的環境，然後將應用程式部署到這個環境。您負責管理大部分的環境，處理在各個 VM 中部署作業系統新修補版本等作業。相反地，在 PaaS 中，環境似乎已經存在。您只需要部署您的應用程式。平台的管理會為您處理，包括部署作業系統的新版本。

藉由雲端服務，您不需要建立虛擬機器。您只需要提供組態檔，告知 Azure 您需要多少個執行個體，例如 3 個 Web 角色執行個體和 2 個背景工作角色執行個體，平台就會建立這些執行個體。您仍然可以選擇這些 VM 的大小，這些選項和 Azure VM 的選項一樣，不過您不需要自行建立這些 VM。如果應用程式需要處理較大的負載，您可以要求更多的 VM，Azure 將建立這些執行個體。如果負載減少，您可以關閉這些執行個體並停止付費。

雲端服務應用程式一般透過兩個步驟的程序提供給使用者使用。開發人員先將應用程式上傳到應用程式的預備區域。開發人員準備啟動應用程式時，使用 Azure 管理入口網站要求部署到生產環境。進行預備與生產之間的切換時，完全不會造成停機，因此執行中應用程式可以升級到新版本，而不干擾使用者。

雲端服務也提供監視。和 Azure 虛擬機器一樣，它將偵測故障的實體伺服器，並且在新機器上重新啟動原先在該伺服器上執行的 VM。不過，雲端服務也會偵測故障的 VM 和應用程式，而不只是硬體故障。和虛擬機器不同的是，它在各個 Web 角色和背景工作角色中都有代理程式，因此能夠在故障時啟動新的 VM 和應用程式執行個體。

雲端服務的 PaaS 性質也有其他意涵。其中一個最重要的意涵是，採用這項技術建立的應用程式應該在任何 Web 角色或背景工作角色執行個體故障時都能正常運作。為了實現這一點，雲端服務應用程式不應該在本身 VM 的檔案系統中保持狀態。和使用 Azure 虛擬機器建立的 VM 不一樣的是，對於雲端服務 VM 進行的寫入並不一致；不會出現類似虛擬機器資料磁碟的元件。雲端服務應用程式反而應該將所有狀態明確寫入 SQL 資料庫、Blob、表格或其他一些外部儲存體。以這種方式建立應用程式使得調整更簡單，而且更能夠因應故障，這是雲端服務的兩個重要目標。

<h2><a  id="WhatShouldIUse" ></a>我應該使用什麼？做決定</h2>


所有 3 個 Azure 執行模型可以讓您建立雲端中可擴充且可靠的應用程式。基於這個重要的相似性，您應該使用哪一個？答案取決於您嘗試進行的作業。

虛擬機器提供最通用的解決方案。如果您要盡可能最多的控制，或者您需要開發和測試之類的通用 VM，這是最佳的選項。虛擬機器也最適合執行雲端中的現成內部部署應用程式，如同先前的 SharePoint 範例所述。另外，由於您使用這項技術建立的 VM 看起來就像是內部部署 VM，因此也可能是災害復原的最佳選擇。當然，缺點是功能愈大責任愈多，IaaS 需要您進行一些管理作業。

網站是建立簡單網站的正確選項。如果您的網站使用現有的應用程式，例如 Joomla、WordPress 或 Drupal，情況更是如此。網站也適合用來建立低管理的 Web 應用程式、建議必須可擴充的應用程式，或將現有的 IIS Web 應用程式移到公用雲端。它也提供快速的部署，應用程式的新執行個體可以立即開始執行，而使用虛擬機器或雲端服務部署新的 VM 則需要幾分鐘的時間。

Azure 提供的初始執行模型「雲端服務」是明確的 PaaS 作法。PaaS 與 Web 代管之間的界限並不明確，而雲端服務與網站有一些重要的不同，如下所示：

* 和網站不同的是，雲端服務對於應用程式的 VM 提供系統管理權限。這可以讓您安裝應用程式需要的任意軟體，這一點是網站辦不到的。
* 由於雲端服務提供 Web 角色和背景工作角色，因此，對於業務邏輯需要個別 VM 的多層式應用程式而言，這是比網站更好的選擇。
* 雲端服務提供個別的預備和生產環境，因此應用程式更新比網站更順暢。
* 和網站不同的是，您可以使用 Azure 虛擬網路和 Azure Connect 之類的網路連線，將內部部署電腦銜接到雲端服務應用程式。
* 雲端服務可以讓您使用遠端桌面直接連接到應用程式的 VM，這一點是網站辦不到的。

由於它是 PaaS，因此雲端服務也展現優於 Azure 虛擬機器的一些優點。為您完成更多管理工作，例如部署作業系統更新，因此作業成本可能低於 Azure 虛擬機器的 IaaS 作法。

所有 3 個 Azure 執行模型各有利弊。做出最好的選擇需要了解這些優缺點、知道您嘗試完成的目標，然後選擇最適合的作法。

有時候，任何一個單一的執行模型都不是正確的選項。在這種情況下，可以併用多個選項。例如，假設您要建立發揮雲端服務 Web 角色管理效益的應用程式，而且您也基於相容性或效能因素而需要使用標準 SQL Server。在這種情況下，最好的選項是併用執行模型，如[圖7](#Fig7) 所示。

<a name="Fig7"></a>![07_CombineTechnologies](./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png)

**圖 7：單一應用程式可以使用多個執行模型。**

如圖所示，雲端服務 VM 在虛擬機器 VM 以外的個別雲端服務中執行。不過，兩者可以有效通訊，因此以這種方式建立應用程式有時候是最好的選項。

Azure 提供不同的執行模型，因為雲端平台需要支援許多不同的情況。可能包括您在內任何想要有效使用這個平台的人閱讀到這裡，都需要了解各個執行模型。



[1]: http://vmdepot.msopentech.com/
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/dn197896.aspx