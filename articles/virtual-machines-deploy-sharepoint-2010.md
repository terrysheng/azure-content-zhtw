<properties linkid="manage-windows-other-resource-sharepoint-wp" urlDisplayName="SharePoint on Azure" pageTitle="SharePoint 2010 Deployment on Azure Virtual Machines" metaKeywords="" description="Understand the supported scenarios for using SharePoint 2010 on Azure virtual machines." metaCanonical="" services="virtual-machines" documentationCenter="" title="SharePoint Deployment on Azure Virtual Machines" authors="" solutions="" manager="" editor="" />

在 Azure 虛擬機器上部署 SharePoint
==================================

執行摘要
--------

Microsoft SharePoint Server 2010 提供充足的部署彈性，可協助組織依據其商業需求和目標，擬定出正確的部署方式。Azure 虛擬機器產品在雲端中受到代管與管理，能夠提供完整、可靠而適用的基礎結構來支援不同的隨需應用程式和資料庫工作負載，例如 Microsoft SQL Server 和 SharePoint 部署。

雖然 Azure 虛擬機器支援多種工作負載，但此文件將著重於 SharePoint 部署。Azure 虛擬機器可讓組織快速建立及管理其 SharePoint 基礎結構，而幾乎對所有類型的主機都可佈建和存取。它可用來充分掌控及管理 SharePoint 虛擬機器 (VM) 的處理器、RAM、CPU 範圍和其他資源。

Azure 虛擬機器可降低硬體需求，讓組織無須再煩惱先期成本和複雜性偏高的問題，而專注於建置及管理具規模的基礎結構。這表示，他們可在短短幾小時內完成新方案的採納、實驗和檢測，而不像傳統部署需要數天或數週。

### 哪些人應閱讀此文件？

本文件的適用對象是 IT 專業人員。再者，技術決策人員 (例如架構設計人員和系統管理員) 也可利用這項資訊和提供的案例，在 Azure 上規劃及設計虛擬化的 SharePoint 基礎結構。

### 為何要閱讀此文件？

此文件將說明組織如何在 Azure 虛擬機器內設定及部署 SharePoint。此外也會討論此類型的部署為何對多種規模的組織都有好處。

改用雲端運算
------------

根據 Gartner 的定義，雲端運算是指「透過網際網路技術，以服務的形式將高度延展性的 IT 功能遞送給外部客戶的運算模式」。在此定義中，最重要的就是延展性、服務和網際網路這三個詞彙。簡言之，雲端運算可定義為**透過網際網路部署及遞送**、並且**可隨需延展**的 IT 服務。

不可否認，雲端運算顯示出現今的 IT 環境正面臨重大轉變。過去，大家都在談整合和成本。而現在，我們關心的是雲端運算能帶來哪些新型態的優勢。大家都想知道，如何運用這股新力量，轉變 IT 為組織提供服務的方式。雲端運算完全改變了 IT 世界，且影響所及遍佈於各種角色，從服務提供者、系統架構設計人員、開發人員到一般使用者，都受到影響。

據研究顯示，靈活性、專注性和經濟性是大家採用雲端的三大誘因：

-   **靈活性**：雲端運算有助於組織快速掌握新商機，以及因應商業需求的變動。

-   **專注性**：雲端運算讓 IT 部門的基礎結構成本得以大幅下降。在基礎結構精簡化和資源集區化後，IT 的運作模式比較像是公用程式，而不再是各種複雜服務和系統的集合。此外，IT 現在已可轉換為較具創造力和策略性的角色。

-   **經濟性**：雲端運算可降低遞送 IT 資源的成本，並提高資料中心的利用率與效益。遞送成本之所以能下降，是因為採用雲端運算時，應用程式和資源都變得可自助使用，且這些資源的使用也可藉由更新、更精確的方式變得更為恰當。硬體使用率也得以提升，因為基礎結構資源 (儲存體、運算工具和網路) 現已集區化和精簡化。

雲端服務的遞送模式
------------------

簡單來說，雲端運算就是 IT 服務的擷取。這些服務的範圍很廣，從基本的基礎結構到完整的應用程式，都包含在內。使用者可直接要求及取用擷取的服務，而無須管理 (甚至完全不用瞭解) 這些服務的組成要素。業界目前認可的雲端服務遞送模式有三種，每種模式在控制性/彈性與整體成本之間各有不同的取捨：

-   **基礎結構即服務** (IaaS)：代管虛擬機器和大多數現有應用程式的虛擬基礎結構。

-   **平台即服務** (PaaS)：可提供隨需應用程式代管環境的雲端應用程式基礎結構。

-   **軟體即服務** (SaaS)：在這種雲端服務模式中，應用程式會透過網際網路來遞送，而客戶則依使用量付款 (例如 Microsoft Office 365 或 Microsoft CRM Online)。

圖 1 說明雲端服務的分類，以及這些分類如何對應至 IT 基礎結構中的元件。在內部部署模式中，客戶須負責管理整個架構，包括網路連線乃至於應用程式。使用 IaaS 時，較低層級的架構可由供應商管理，而客戶則負責管理作業系統和應用程式。使用 PaaS 時，從網路連線到執行階段一切都由平台供應商負責提供及管理。客戶只需管理應用程式和資料即可。(Azure 產品最適合採用此模式。)最後，在 SaaS 中，供應商會負責提供應用程式，以及擷取來自各種基礎元件的所有服務。

圖 1：雲端服務分類

![azure-sharepoint-wp-1](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-1.png)

Azure 虛擬機器
--------------

Azure 虛擬機器所提供的功能，可充分掌控及管理 VM 和範圍廣大的虛擬網路。這項產品可為組織帶來可觀的效益，例如：

-   **管理**：以充分的控制能力集中管理 VM，以設定和維護基礎結構。

-   **應用程式行動性**：讓虛擬硬碟 (VHD) 能夠往返於內部部署與雲端環境之間。應用程式無須重新建置，即可在雲端中執行。

-   **對 Microsoft 伺服器應用程式的存取**：在雲端中可執行相同的內部部署應用程式和基礎結構，包括 Microsoft SQL Server、SharePoint Server、Windows Server 和 Active Directory。

Azure 虛擬機器是簡單、開放、彈性而功能強大的平台，讓組織在短短幾分鐘內即可部署及執行 Windows Server 和 Linux VM：

-   **簡單**：透過 Azure 虛擬機器，將可輕易地在雲端中建置、移轉、部署及管理 VM。組織可直接將工作負載移轉至 Azure 而無須變更現有程式碼，也可以透過簡單的操作在 Azure 中設定新的 VM。此產品也可整合 Azure 的 IaaS 與 PaaS 功能，以利開發新的雲端應用程式。

-   **開放且彈性**：Azure 是一個為組織提供充分彈性的開放平台。組織可使用映像庫中預先建置的映像，也可以建立及使用自訂和內部部署 VHD，並將其上傳至映像庫。此外也可使用 Linux 的社群和商用版本。

-   **功能強大**：Azure 是一個企業級的雲端平台，可用來在雲端中執行 SQL Server、SharePoint Server 或 Active Directory 等應用程式。組織可透過 VPN 連線，在 Azure 資料中心與本身的網路之間建立混合式內部部署和雲端解決方案。

Azure 虛擬機器上的 SharePoint
-----------------------------

SharePoint 2010 可彈性支援 Azure 虛擬機器部署中大部分的工作負載。對 FIS (SharePoint Server for Internet Sites) 與開發而言，Azure 虛擬機器是絕佳的搭配。同樣地，核心 SharePoint 工作負載也受支援。如果組織想要控管本身的 SharePoint 2010 實作，同時利用雲端中的虛擬化功能，在 Azure 虛擬機器上部署將是理想的選擇。

Azure 虛擬機器產品會在雲端中受到託管和管理。此方式可提供部署彈性，並減少因硬體購置而產生的資本支出，進而降低成本。受惠於基礎結構靈活性的提升，組織將可在短短幾小時內完成 SharePoint Server 的部署，而不需要數天或數週。Azure 虛擬機器也可讓組織採用「隨收隨付」模式，在雲端中部署 SharePoint 工作負載。當 SharePoint 工作負載增加時，組織可以快速擴充基礎結構；而在運算需求下降時，組織則可退回無須再使用的資源，而僅就使用的部分付費。

### IT 焦點的轉移

有許多組織將其 IT 基礎結構和管理的一般元件委外處理 (例如硬體、作業系統、安全性、資料儲存和備份)，而對於關鍵應用程式則選擇自行控管 (例如 SharePoint Server)。將 IT 平台的所有非關鍵服務層面委派給虛擬服務提供者後，組織即可將其 IT 重點工作轉移至關鍵的核心 SharePoint 服務，並且以 SharePoint 專案提供商業價值，而無須在基礎結構的設定上耗費太多時間。

### 更快速的部署

支援及部署大型 SharePoint 基礎結構，可能會拖累 IT 快速支援商業需求的機動性。要建置、測試及準備 SharePoint 伺服器和伺服器陣列，並將其部署至實際執行環境，可能需要數週甚至數個月的時間，視組織的處理程序和限制而定。Azure 虛擬機器可讓組織快速部署其 SharePoint 工作負載，且無須額外購置硬體。如此，組織將可充分利用基礎結構的靈活性，在短短幾小時內完成部署，而不需要數天或數週。

### 延展性

組織在省去部署、測試及準備實體 SharePoint 伺服器和伺服器陣列等工作後，將可依需求即時擴充及縮減運算容量。當 SharePoint 工作負載需求上升時，組織可快速擴充其雲端中的基礎結構。同理，當運算需求減少時，組織可以縮減資源，而僅就使用的部分付費。Azure 虛擬機器可降低先期費用和長期負擔，讓組織得以建置及管理具規模的 SharePoint 基礎結構。同樣地，這表示這些組織可在短短幾小時內完成新方案的採納、實驗和檢測，而不像傳統部署需要數天或數週。

### 使用計量

Azure 虛擬機器可為 SharePoint 案例提供運算功能、記憶體和儲存體，而其價格通常以資源用量為基礎。組織僅需就使用的部分付費，且服務會提供所有執行 SharePoint 基礎結構所需的功能。如需定價和計費的詳細資訊，請參閱 [Azure 定價詳細資料](/en-us/pricing/details/)。請注意，從內部部署網路將 Azure 雲端中的儲存體和資料移出時，須收取少許費用。但 Azure 不會對上傳資料收費。

### 彈性

Azure 虛擬機器可讓開發人員有彈性地選擇其所需的語言或執行階段環境，並且也提供對 .NET、Node.js、Java 和 PHP 的正式支援。開發人員也可選擇支援 Microsoft Visual Studio、WebMatrix、Eclipse 和文字編輯器的工具。此外，Microsoft 也提供低成本、低風險的雲端使用途徑，以及易於佈建和部署的雲端報告功能，讓使用者能夠在各種裝置和位置上存取商業智慧 (BI)。最後，透過 Azure 產品，使用者不僅可將 VHD 移至雲端，也可將 VHD 重新複製到本機上執行，或透過其他雲端提供者執行 (必須取得相關授權)。

佈建程序
--------

本小節將討論 Azure 的基本架構。Azure 中的**映像庫**會提供預先配置的可用 VM 清單。使用者可將 SharePoint Server、SQL Server、Windows Server 和其他 ISO/VHD 發佈至映像庫。為簡化 VM 的建立作業，系統會建立基礎映像，並將其發佈至映像庫。授權使用者可使用這些映像產生所需的 VM。如需詳細資訊，請移至 Azure 網站上的[建立執行 Windows Server 2008 R2 的虛擬機器](/en-us/manage/windows/tutorials/virtual-machine-from-gallery/)。圖 2 顯示使用 Azure 管理入口網站建立 VM 的基本步驟：

圖 2：VM 建立步驟概觀

![azure-sharepoint-wp-2](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-2.png)

使用者也可在 Azure 管理入口網站上傳已執行過 Sysprep 的映像。如需詳細資訊，請移至[建立和上傳虛擬硬碟](/en-us/manage/windows/common-tasks/upload-a-vhd/)。圖 3 顯示上傳映像以建立 VM 的基本步驟：

圖 3：映像上傳步驟概觀

![azure-sharepoint-wp-3](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-3.png)

### 在 Azure 上部署 SharePoint 2010

您可以依照下列步驟，在 Azure 上部署 SharePoint 2010：

1.  使用您的帳戶登入 [Azure 管理入口網站](http://manage.windowsazure.com/)。
    -   如果您沒有 Azure 帳戶，請[註冊 Azure 的免費試用版](http://www.windowsazure.com/zh-tw/pricing/free-trial/)。

2.  以基礎作業系統建立 VM：在 Azure 管理入口網站上，依序按一下 **[+新增]**、**[虛擬機器]** 和 **[從組件庫]**。![azure-sharepoint-wp-4](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-4.png)
3.  **[VM OS Selection]** 對話方塊隨即顯示。按一下 **[平台映像]**，然後選取 **Windows Server 2008 R2 SP1** 平台映像。![azure-sharepoint-wp-5](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-5.png)
4.  ***[VM Configuration]*** 對話方塊隨即顯示。請提供下列資訊：
    -   輸入 **[虛擬機器名稱]**。
        -   此機器名稱應具有全域唯一性。
    -   將 **[新增使用者名稱]** 方塊保留為 Administrator。
    -   在 **[新密碼]** 方塊中，輸入強式密碼。
    -   在 **[確認密碼]** 方塊中，重新輸入密碼。
    -   選取適當的 **[大小]**。
        -   對於實際執行環境 (SharePoint 應用程式伺服器和資料庫)，建議使用 [大] *(4 核心、7GB 記憶體)*。

    ![azure-sharepoint-wp-6](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-6.png)
5.  ***[VM Mode]*** 對話方塊隨即顯示。請提供下列資訊：
    -   選取 [獨立虛擬機器]。
    -   在 **[DNS 名稱]** 方塊中，提供您所選擇之 DNS 名稱的第一個部分。
        -   此部分會以 MyService1.cloudapp.net 的格式完成名稱。
    -   在 **[儲存體帳戶]** 方塊中，選擇下列其中一項：
        -   選取用來存放 VHD 檔案的儲存體帳戶。
        -   選擇自動建立儲存體帳戶。
            -   每個區域只會自動建立一個儲存體帳戶。以此設定建立的所有其他 VM 都會在此儲存體帳戶中。
            -   您的儲存體帳戶限制為 20 個。
            -   如需詳細資訊，請移至[在 Azure 中建立儲存體帳戶](/en-us/manage/windows/common-tasks/upload-a-vhd/#createstorage)。
    -   在 **[區域/同質群組/虛擬網路]** 方塊中，選取要代管此虛擬映像的區域。

    ![azure-sharepoint-wp-7](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-7.png)
6.  ***[VM Options]*** 對話方塊隨即顯示。請提供下列資訊：
    -   在 **[可用性設定組]** 方塊中，選取 [(無)]。
    -   閱讀並接受法律條款。
    -   按一下核取記號以建立 VM。

    ![azure-sharepoint-wp-8](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-8.png)
7.  ***[VM Instances]*** 頁面隨即顯示。驗證您的 VM 已成功建立。 ![azure-sharepoint-wp-9](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-9.png)
8.  完成 VM 設定：
    -   使用遠端桌面開啟 VM。
    -   在 Azure 管理入口網站上選取您的 VM，然後選取 **[儀表板]** 頁面。
    -   按一下 **[連接]**。

    ![azure-sharepoint-wp-10](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-10.png)
9.  使用下列任何選項建置 SQL Server VM：
    -   依照前述的步驟 1 到 7 來建立 SQL Server 2012 VM，但**步驟 3** 除外；請使用 SQL Server 2012 映像，而不要使用 Windows Server 2008 R2 SP1 映像。如需詳細資訊，請移至[在 Azure 上佈建 SQL Server 虛擬機器](/en-us/manage/windows/common-tasks/install-sql-server/)。
        -   當您選擇此選項時，佈建程序會在 *C:\\SQLServer\_11.0\_Full* 目錄路徑中保存 SQL Server 2012 安裝檔案的複本，供您自訂安裝之用。例如，您可以使用授權金鑰，將 SQL Server 2012 的評估版安裝轉換為授權版本。
    -   使用 SQL Server 系統準備 (SysPrep) 工具，在基礎作業系統的 VM 上安裝 SQL Server (如前述步驟 1 到 7 所說明)。如需詳細資訊，請移至[使用 SysPrep 安裝 SQL Server 2012](http://msdn.microsoft.com/zh-tw/library/ee210664.aspx)。
    -   使用命令提示字元安裝 SQL Server。如需詳細資訊，請移至[使用命令提示字元安裝 SQL Server 2012](http://msdn.microsoft.com/zh-tw/library/ms144259.aspx#SysPrep)。
    -   使用支援的 SQL Server 媒體和您的授權金鑰，在基礎作業系統的 VM 上安裝 SQL Server (如前述步驟 1 到 7 所說明)。

10. 使用下列子步驟建置 SharePoint 伺服器陣列：
    -   子步驟 1：使用指令檔設定 Azure 訂閱。
    -   子步驟 2：以基礎作業系統建立另一個 VM (如前述步驟 1 到 7 所說明)，以佈建 SharePoint 伺服器。若要在此 VM 上建置 SharePoint 伺服器，請選擇下列其中一項：
        -   使用 SharePoint GUI 進行佈建：
            -   若要建立及佈建 SharePoint 伺服器陣列，請移至[建立 Microsoft SharePoint Server 伺服器陣列](http://technet.microsoft.com/en-us/library/ee805948.aspx#CreateConfigure)。
            -   若要在伺服器陣列中新增 Web 或應用程式伺服器，請移至[新增 Web 或應用程式伺服器至伺服器陣列 (SharePoint Server 2010)](http://technet.microsoft.com/en-us/library/cc261752.aspx)。
            -   若要在現有的伺服器陣列中新增資料庫伺服器，請移至[新增資料庫伺服器至現有的伺服器陣列](http://technet.microsoft.com/en-us/library/cc262781)。

                -   若要將 SQL Server 2012 用於您的 SharePoint 伺服器陣列，您必須在安裝應用程式並選擇不設定伺服器之後，安裝 SharePoint Server 2010 的 Service Pack 1。如需詳細資訊，請移至 [SharePoint Server 2010 的 Service Pack 1](http://www.microsoft.com/en-us/download/details.aspx?id=26623)。
                -   若要使用 SQL Server BI 功能，建議您將 SharePoint Server 安裝為伺服器陣列，而不是獨立伺服器。如需詳細資訊，請移至[安裝 SQL Server 2012 Business Intelligence 功能](http://technet.microsoft.com/en-us/library/hh231681(v=sql.110).aspx)。

        -   使用 Microsoft Windows PowerShell 進行佈建：您可以將 Psconfig 命令列工具作為替代介面，用以執行幾項控制 SharePoint 2010 產品之佈建方式的作業。如需詳細資訊，請移至 [Psconfig 命令列參照](http://technet.microsoft.com/en-us/library/cc263093.aspx)。
    -   子步驟 3：設定 SharePoint。在每個 SharePoint VM 都處於就緒狀態後，請使用下列其中一項，在每部伺服器上設定 SharePoint Server：
        -   從 GUI 設定 SharePoint。
        -   使用 Windows PowerShell 設定 SharePoint。如需詳細資訊，請移至[使用 Windows PowerShell 安裝 SharePoint Server 2010](http://technet.microsoft.com/en-us/library/cc262839.aspx)。
            -   您也可以使用 CodePlex 專案的 AutoSPInstaller；其中包含 Windows PowerShell 指令碼、一個 XML 輸入檔，和一個標準 Microsoft Windows 批次檔。AutoSPInstaller 會根據 Windows PowerShell 提供適用於 SharePoint 2010 安裝指令碼的架構。如需詳細資訊，請移至 [CodePlex：AutoSPInstaller](http://autospinstaller.codeplex.com/)。 **注意**：請務必設定管理入口網站端點的安全性，並在 VM 的 Windows 防火牆上設定輸入連接埠。接著，請以系統管理員認證開啟 Windows PowerShell 工作階段，以確認您可以對其中一個 SharePoint 應用程式伺服器啟動遠端 Windows PowerShell 工作階段。

11. 指令碼執行完成後，請使用 VM 儀表板連接到 VM。
12. 驗證 SharePoint 組態：登入 SharePoint 伺服器，然後使用「管理中心」來驗證組態。

### 建立及上傳虛擬硬碟

您也可以建立自己的映像，並將其上傳至 Azure 作為 VHD 檔案。若要在 Azure 上建立及上傳 VHD 檔案，請執行下列步驟：

1.  建立具有 Hyper-V 功能的映像：使用 Hyper-V 管理員建立具有 Hyper-V 功能的 VHD。如需詳細資訊，請移至[建立虛擬硬碟](http://technet.microsoft.com/en-us/library/cc742509)。
2.  在 Azure 中建立儲存體帳戶：必須要有 Azure 中的儲存體帳戶，才能上傳可用來建立 VM 的 VHD 檔案。此帳戶可使用 Azure 管理入口網站來建立。如需詳細資訊，請移至[在 Azure 中建立儲存體帳戶](/en-us/manage/windows/common-tasks/upload-a-vhd/)。
3.  準備要上傳的映像：映像在上傳至 Azure 之前，必須使用 SysPrep 命令進行一般化。如需詳細資訊，請移至[如何使用 SysPrep：簡介](http://technet.microsoft.com/en-us/library/bb457073.aspx) (英文)。
4.  將映像上傳至 Azure：若要上傳 VHD 檔案中包含的映像，您必須建立並安裝管理憑證。請取得憑證的指模和訂閱 ID。請設定連線，並使用 CSUpload 命令列工具上傳 VHD 檔案。如需詳細資訊，請移至[將映像上傳至 Azure](/en-us/manage/windows/common-tasks/upload-a-vhd/)。

使用案例
--------

本節將討論主要客戶使用 Azure 虛擬機器部署 SharePoint 的案例。每個案例都分成兩部分：關於案例的簡短說明，以及實際執行的步驟。

### 案例 1：簡單的 SharePoint 部署和測試環境

#### 說明

組織期望能以更便捷的方式建立 SharePoint 應用程式及設定 SharePoint 環境，以供境內/境外的開發與測試使用。基本上，他們想要縮短設定 SharePoint 應用程式開發專案的所需時間，並藉由測試環境使用率的提升來降低成本。例如，組織可能會想要在 SharePoint Server 上執行隨需負載測試，並以更多位於不同地理位置的並行使用者執行使用者接受度測試 (UAT)。同樣地，對現今許多組織而言，境內/境外工作團隊的整合，也是重要性漸增的商業需求。

此案例說明，組織如何使用預先設定的 SharePoint 伺服器陣列進行開發與測試工作。SharePoint 部署拓撲的外觀和操作性會與內部部署的虛擬化部署完全相同。現有的 IT 技能可完全對等地轉換至 Azure 虛擬機器部署，而其主要的優點反映在成本上：資本支出幾乎可完全轉化為營運支出，因為沒有先期購置實體伺服器的必要。組織的伺服器硬體資金成本得以減除，且因為用於測試和開發環境的 SharePoint 伺服器陣列在建立、設定或擴充上所需的佈建時間皆大幅縮短，組織將更具彈性。IT 可依據持續變動的測試和開發需求，以動態方式增加或移除容量。此外，IT 也可更加專注於 SharePoint 專案的商業價值傳遞，而無須耗費太多時間在基礎結構的管理上。

為了充分運用負載測試機器，組織可藉由 Windows Sever 2008 R2 的作業系統支援，在 Azure 上設定 SharePoint 虛擬化開發和測試機器。這可讓開發團隊建立及測試應用程式，並輕易地移轉至內部部署或雲端實際執行環境，而無須變更程式碼。相同的架構和工具集在內部部署與雲端中皆可使用，使分散於各處的團隊可存取相同的環境。使用者也可建立直接 VPN 連線，以存取內部部署的資料和應用程式。

#### 開始使用

圖 4 顯示 Azure VM 中的 SharePoint 開發與測試環境。若要建置此部署，首先請使用開發應用程式時所使用的相同內部部署 SharePoint 開發與測試環境。接著，請將應用程式上傳並部署至 Azure VM，以供測試與開發之用。如果您的組織決定將應用程式移回內部部署，應用程式並不需修改，即可將之移回。

圖 4：Azure 虛擬機器中的 SharePoint 開發與測試環境

![azure-sharepoint-wp-11](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-11.png)

#### 設定案例環境

若要在 Azure 上實作 SharePoint 開發與測試環境，請執行下列步驟：

1.  *佈建*：首先，請使用 Azure 虛擬網路佈建內部部署與 Azure 之間的 VPN 連線。(由於此處不會使用 Active Directory，因此需要有 VPN 通道。)如需詳細資訊，請移至 [Azure 虛擬網路 (設計考量和安全連線情境)](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156007.aspx)。接著，請在管理入口網站上使用映像庫中儲存的映像佈建新的 VM。
    -   您可以將內部部署的 SharePoint 開發與測試 VM 上傳至您的 Azure 儲存體帳戶，並透過映像庫參照這些 VM，以建置必要的環境。
    -   您可以使用 SQL Server 2012 映像，而不使用 Windows Server 2008 R2 SP1 映像。如需詳細資訊，請移至[在 Azure 上佈建 SQL Server 虛擬機器](/en-us/manage/windows/common-tasks/install-sql-server/)。

2.  *安裝*：使用遠端桌面連線，在 VM 上安裝 SharePoint Server、Visual Studio 和 SQL Server。
    -   選擇 SharePoint Server 的安裝選項：
        -   使用 SharePoint 2010 的檢視設定指令碼 (Easy Setup Script) 建置 SharePoint 開發人員機器。如需詳細資訊，請移至 [SharePoint 2010 簡易設定指令碼](http://www.microsoft.com/en-us/download/details.aspx?id=23415)。
        -   使用 Windows PowerShell。如需詳細資訊，請移至[使用 Windows PowerShell 安裝 SharePoint Server 2010](http://technet.microsoft.com/en-us/library/cc262839.aspx)。
        -   使用 CodePlex 專案的 AutoSPInstaller。如需詳細資訊，請移至 [CodePlex：AutoSPInstaller](http://autospinstaller.codeplex.com/)。
    -   安裝 Visual Studio。如需詳細資訊，請移至 [Visual Studio 安裝](http://msdn.microsoft.com/zh-tw/library/e2h7fzkw.aspx)。
    -   安裝 SQL Server。如需詳細資訊，請移至[使用 SysPrep 安裝 SQL Server](http://msdn.microsoft.com/zh-tw/library/ee210664.aspx)。
        -   參考為 SharePoint 伺服器陣列部署建立及設定 SQL Server 2012 的實際操作實驗室。[為 Azure 中的 SharePoint 設定 SQL Server 2012](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint)。
        -   參考藉由設定 Active Directory 及使用單一 SQL Server 資料庫來建立 SharePoint 伺服器陣列的實際操作實驗室。[使用 Azure 虛擬機器部署 SharePoint 伺服器陣列](https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs)。

3.  *開發應用程式和資料庫的部署封裝和指令碼*：如果您預計要使用映像庫中的可用 VM，所需的內部部署應用程式和資料庫將可部署在 Azure 虛擬機器上：
    -   使用 SQL Server Data Tools 和 Visual Studio，建立現有內部部署應用程式和資料庫的部署封裝。
    -   使用這些封裝，將應用程式和資料庫部署在 Azure 虛擬機器上。

4.  *部署 SharePoint 應用程式和資料庫*：
    -   設定管理入口網站端點的安全性，並在 VM 的 Windows 防火牆中設定輸入連接埠。
    -   使用在步驟 3 中建立的部署封裝和指令碼，將 SharePoint 應用程式和資料庫部署至 Azure 虛擬機器。
    -   測試已部署的應用程式和資料庫。

5.  *管理 VM*：
    -   使用管理入口網站監視 VM。
    -   使用 Visual Studio 和 SQL Server Management Studio 監視應用程式。
    -   您也可以使用 Microsoft System Center Operations Manager 之類的內部部署管理軟體，來監視及管理 VM。

### 案例 2：自訂的公用端 SharePoint 伺服器陣列

#### 說明

組織想建立在雲端中託管、並且可依據需求輕易擴充的網際網路平台服務。他們也想建立用來溝通協調的合作夥伴內部網站，並實作可在不同位置對網站內容進行撰寫和核准的簡單程序。最後，為了處理漸增的負載，這些組織想為其網站隨需提供容量。

此案例會以 SharePoint Server 作為代管公用端網站的基礎。如此，組織即可在安全而可擴充的雲端基礎結構上，快速部署、自訂及代管其商業網站。透過 Azure 上的 SharePoint 公用端網站，組織將可隨著流量成長進行調整，並僅就使用的部分付費。常用工具與內部部署所使用的相類似，可供 Azure 上的 SharePoint 用於內容撰寫、工作流程和核准。

除此之外，使用 Azure 虛擬機器也可讓組織輕鬆設定執行於 VM 的執行環境和實際執行環境。在 Azure 中建立的 SharePoint 公用端 VM 可備份至虛擬儲存體。此外，可供災害復原之用的「連續地理區域複寫」功能，可讓組織將執行於某個資料中心的 VM 自動備份至遠處的另一個資料中心。(如需地理區域複寫的詳細資訊，請移至 [Azure 儲存體的地理區域複寫簡介](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx))。

Azure 基礎結構中的 VM 經驗證可有效與其他 Microsoft 產品搭配運作，例如 SQL Server 與 SharePoint Server。Azure 與 SharePoint Server 的搭配運作性較佳：兩者同屬 Microsoft 系列，並且已密切整合、相互支援並測試，可提供最理想的使用性。兩者對於 SharePoint 應用程式和 Azure 基礎結構具有單一支援點。

#### 開始使用

在此案例中必須為 SharePoint Server 新增更多前端 Web 伺服器，以支援額外的流量。這些伺服器必須具有進階的安全性和「Active Directory 網域服務」網域控制站，以支援使用者驗證和授權。圖 5 顯示此案例的配置。

圖 5：自訂的公用端 SharePoint 伺服器陣列

![azure-sharepoint-wp-12](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-12.png)

#### 設定案例環境

若要在 Azure 上實作公用端 SharePoint 伺服器陣列，請執行下列步驟：

1.  *部署 Active Directory*：在 Azure 虛擬機器上部署 Active Directory 的基本需求，類似於 (但不等同於) 在內部部署 VM (和某些實體機器) 上的部署。如需其差異的詳細資訊，以及相關指引和其他注意事項，請移至[在 Azure 虛擬機器上部署 Active Directory 的指引](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156090)。若要在 Azure 中部署 Active Directory：
    -   定義及建立可讓 VM 指派至特定子網路的虛擬網路。如需詳細資訊，請移至[設定虛擬網路](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md)。
    -   使用管理入口網站，在 Azure 中的新 VM 上建立及部署網域控制站。如需詳細資訊，請移至[部署及建立網域控制站](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md)。
        -   您也可以參照 Windows PowerShell 指令碼，使用 Azure 虛擬機器和虛擬網路在雲端中部署獨立網域。如需詳細資訊，請移至[在 Azure 中部署 Active Directory (Windows PowerShell)](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS)。
        -   如需使用 Azure 虛擬網路在 VM 上建立新 Active Directory 樹系的詳細資訊，請移至[在 Azure 中安裝新的 Active Directory 樹系](/en-us/manage/services/networking/active-directory-forest/)。

2.  *佈建 VM*：使用管理入口網站，從映像庫中儲存的映像佈建新的 VM。
3.  *部署 SharePoint 伺服器陣列*：
    -   使用新佈建的 VM 來安裝 SharePoint 及產生可重複使用的映像。如需安裝 SharePoint Server 的詳細資訊，請移至[使用 Windows PowerShell 安裝及設定 SharePoint Server 2010](http://technet.microsoft.com/en-us/library/cc262839.aspx) 或 [CodePlex：AutoSPInstaller](http://autospinstaller.codeplex.com/)。
    -   設定要建立並連接到 SharePoint 伺服器陣列的 SharePoint VM。
    -   使用管理入口網站設定負載平衡。
        -   設定 VM 端點，選取要對現有端點的流量進行負載平衡的選項，然後指定負載平衡 VM 的名稱。
        -   在現有的 SharePoint 伺服器陣列中新增其他前端 Web VM，以因應額外流量。

4.  *管理 VM*：
    -   使用管理入口網站監視 VM。
    -   使用「管理中心」監視 SharePoint 伺服器陣列。

### 案例 3：擴充以因應更多 BI 服務的伺服器陣列

#### 說明

要想認清重要情勢，並做出迅速而健全的決策，商業智慧是不可或缺的。隨著組織改採內部部署以外的方式，他們將現有的 BI 應用程式部署到雲端時，並不想要變更原有的 BI 環境。他們想要在高持續性和高可用性的環境中，控管來自 SQL Server Analysis Services (SSAS) 或 SQL Server Reporting Services (SSRS) 的報告，同時保有對 BI 應用程式的完整掌控，且在維護方面都不需要耗費太多時間與預算。

此案例說明組織如何使用 Azure 虛擬機器來代管關鍵 BI 應用程式。組織可在 Azure 虛擬機器中部署 SharePoint 伺服器陣列，並擴充應用程式伺服器 VM 的 BI 元件，例如 SSRS 或 Excel Services。將耗費資源的元件調節到雲端後，組織將可更輕鬆而有效地支援特殊工作負載。請留意到，Azure 虛擬機器中的 SQL Server 具有良好的執行效能，因為 SQL Server 執行個體可以輕易調整，從小型到超大型安裝皆可。隨之而來的彈性，讓組織得以根據即時的工作負載需求，動態佈建 (擴充) 或取消佈建 (縮減) BI 執行個體。

將現有的 BI 應用程式移轉至 Azure，可達到更有效的調整。藉由 SSAS、SSRS 和 SharePoint Server 的支援，組織將可建立功能強大、並且可向上或向下調整的 BI 和報告應用程式與儀表板。這些應用程式和儀表板也將可更安全地與內部部署資料和應用程式整合。Azure 可確保資料中心符合並支援 ISO 27001。如需詳細資訊，請移至 [Azure 信任中心](/en-us/support/trust-center/compliance/)。

#### 開始使用

若要擴充 BI 元件的部署，必須安裝具有 PowerPivot、Power View、Excel Services 或 PerformancePoint Services 等服務的新應用程式伺服器。或者，必須在現有的伺服器陣列中新增 SSAS 或 SSRS 之類的 SQL Server BI 執行個體，以支援更多查詢處理。已安裝 SharePoint 2010 Server 或 SQL Server 的伺服器，可新增為新的 Azure VM。接著，可以在該伺服器上安裝、部署及設定 BI 元件 (圖 6)。

圖 6：擴充以因應更多 BI 服務的 SharePoint 伺服器陣列

![azure-sharepoint-wp-13](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-13.png)

#### 設定案例環境

若要在 Azure 上擴充 BI 環境，請執行下列步驟：

1.  *佈建*：
    -   使用 Azure 虛擬網路，佈建內部部署與 Azure 之間的 VPN 連線。如需詳細資訊，請移至 [Azure 虛擬網路 (設計考量和安全連線情境)](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156007.aspx)。
    -   使用管理入口網站，從映像庫中儲存的映像佈建新的 VM。
        -   您可以將 SharePoint Server 或 SQL Server BI 工作負載映像上傳至映像庫，且任何授權使用者皆可選用這些 BI 元件 VM 來建置擴充的環境。

2.  *安裝*：如果您的組織沒有預先建置的 SharePoint Server 或 SQL Server BI 元件映像，請使用遠端桌面連線在 VM 上安裝 SharePoint Server 和 SQL Server。
    -   如需安裝 SharePoint 的詳細資訊，請移至[使用 Windows PowerShell 安裝 SharePoint Server 2010](http://technet.microsoft.com/en-us/library/cc262839.aspx) 或 [CodePlex：AutoSPInstaller](http://autospinstaller.codeplex.com/)。
    -   如需安裝 SQL Server 的詳細資訊，請移至[使用 SysPrep 安裝 SQL Server](http://msdn.microsoft.com/zh-tw/library/ee210664.aspx)。
    -   參考為 SharePoint 伺服器陣列部署建立及設定 SQL Server 2012 的實際操作實驗室。[為 Azure 中的 SharePoint 設定 SQL Server 2012](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingSQLServerForSharePoint)。
    -   參考藉由設定 Active Directory 及使用單一 SQL Server 資料庫來建立 SharePoint 伺服器陣列的實際操作實驗室。[使用 Azure 虛擬機器部署 SharePoint 伺服器陣列](https://github.com/WindowsAzure-TrainingKit/HOL-DeploySharePointVMs)。

3.  *新增 BI VM*：
    -   設定管理入口網站端點的安全性，並在 VM 的 Windows 防火牆中設定輸入連接埠。
    -   將新建立的 BI VM 新增至現有的 SharePoint 或 SQL Server 伺服器陣列。

4.  *管理 VM*：
    -   使用管理入口網站監視 VM。
    -   使用「管理中心」監視 SharePoint 伺服器陣列。
    -   使用 Microsoft System Center Operations Manager 之類的內部部署管理軟體，來監視及管理 VM。

### 案例 4：完全自訂的 SharePoint 型網站

#### 說明

有愈來愈多的組織想要在雲端中建立完整自訂的 SharePoint 網站。他們需要高持續性與高可用性的環境，以便有充分的掌控力可維護在雲端中執行的複雜應用程式，但並不想為此花費大把的時間和預算。

在此案例中，組織可將其整個 SharePoint 伺服器陣列部署至雲端中，並動態調整所有元件以取得更多容量，或是在必要時，將其內部部署延伸至雲端，以提升容量並改善效能。此案例的主要適用對象，是想要在應用程式開發與企業內容管理上獲得完整 SharePoint 使用性的組織。更複雜的網站也可納入增強式報告、Power View、PerformancePoint、PowerPivot、深入圖表和其他大部分的 SharePoint 網站功能，以取得端對端的完整功能。

組織可用 Azure 虛擬機器，在符合成本效益、具有高度安全性的雲端基礎結構中代管自訂的應用程式和相關聯的元件。他們也可使用內部部署的 Microsoft System Center，作為內部部署和雲端應用程式的一般管理工具。

#### 開始使用

若要在 Azure 上實作完全自訂的 SharePoint 網站，組織必須在雲端中部署 Active Directory 網域，並在該網域中佈建新的 VM。接著，必須要建立執行 SQL Server 2012 的 VM，並將其設定為 SharePoint 伺服器陣列的一部分。最後，必須建立 SharePoint 伺服器陣列、為其進行負載平衡，然後連接到 Active Directory 和 SQL Server (圖 7)。

圖 7：完全自訂的 SharePoint 型網站

![azure-sharepoint-wp-14](./media/virtual-machines-deploy-sharepoint-2010/azure-sharepoint-wp-14.png)

#### 設定案例環境

下列步驟說明，如何從映像庫中預先建置的可用映像建立自訂的 SharePoint 伺服器陣列環境。但請注意，您也可以將 SharePoint 伺服器陣列 VM 上傳至映像庫，而授權使用者則可以選用這些 VM，在 Azure 上建置必要的 SharePoint 伺服器陣列。

1.  部署 Active Directory*：在 Azure 虛擬機器上部署 Active Directory 的基本需求，類似於 (但不等同於) 在內部部署 VM (和某些實體機器) 上的部署。如需其差異的詳細資訊，以及相關指引和其他注意事項，請移至[在 Azure 虛擬機器上部署 Active Directory 的指引](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156090)。若要在 Azure 中部署 Active Directory：*
    -   定義及建立可讓 VM 指派至特定子網路的虛擬網路。如需詳細資訊，請移至[設定虛擬網路](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md)。
    -   使用管理入口網站，在 Azure 中的新 VM 上建立及部署網域控制站。如需詳細資訊，請移至[部署及建立網域控制站](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectory/blob/master/HOL.md)。
        -   您也可以參照 Windows PowerShell 指令碼，使用 Azure 虛擬機器和虛擬網路在雲端中部署獨立網域。如需詳細資訊，請移至[在 Azure 中部署 Active Directory (Windows PowerShell)](https://github.com/WindowsAzure-TrainingKit/HOL-DeployingActiveDirectoryPS)。
        -   如需使用 Azure 虛擬網路在 VM 上建立新 Active Directory 樹系的詳細資訊，請移至[在 Azure 中安裝新的 Active Directory 樹系](/en-us/manage/services/networking/active-directory-forest/)。

2.  *部署 SQL Server*：
    -   使用管理入口網站，從映像庫中儲存的映像佈建新的 VM。
    -   在 VM 上設定 SQL Server。如需詳細資訊，請移至[使用 SysPrep 安裝 SQL Server](http://msdn.microsoft.com/zh-tw/library/ee210664.aspx)。
    -   將 VM 加入新建立的 Active Directory 網域中。

3.  *部署多伺服器 SharePoint 伺服器陣列*：
    -   建立虛擬網路。如需詳細資訊，請移至 [Azure 虛擬網路 (設計考量和安全連線情境)](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156007.aspx)。
        -   在部署 SharePoint VM 時，您必須為 SharePoint Server 提供子網路，如此，本機 Active Directory 方塊中的 DNS 位址在佈建期間才可供使用。
    -   使用管理入口網站建立 VM。
    -   在此 VM 上安裝 SharePoint Server，並產生可重複使用的映像。如需安裝 SharePoint Server 的詳細資訊，請移至[使用 Windows PowerShell 安裝及設定 SharePoint Server 2010](http://technet.microsoft.com/en-us/library/cc262839.aspx) 或 [CodePlex：AutoSPInstaller](http://autospinstaller.codeplex.com/)。
    -   設定要建立的 SharePoint VM，並使用 [Join-SharePointFarm](http://technet.microsoft.com/en-us/library/ff607979.aspx) 命令連接到 SharePoint 伺服器陣列。
    -   使用管理入口網站設定負載平衡：
        -   設定 VM 端點，選取要對現有端點的流量進行負載平衡的選項，然後指定負載平衡 VM 的名稱。
            -   如需在 Azure 虛擬機器上部署 SharePoint 伺服器陣列的詳細資訊，請檢視此 [TechEd North America 2012 視訊](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2012/AZR327)。

4.  *透過 System Center 管理 SharePoint 伺服器陣列*：
    -   使用 Operations Manager 代理程式和新的 Azure Integration Pack，將您的內部部署 System Center 連接到 Azure 虛擬機器。
    -   使用內部部署 App Controller 和 Orchestrator 執行管理功能。

結論
----

雲端運算正在轉變 IT 為組織提供服務的方式。這是因為雲端運算可帶來新形態的優勢，其中包括大幅降低成本，同時提高 IT 專注性、靈活性和彈性。Azure 提供了簡單、開放、彈性而強大的虛擬基礎結構，可說是雲端運算領域的先行者。Azure 虛擬機器可降低硬體需求，讓組織得以建置具規模的基礎結構，而降低成本和複雜性，同時保有充分的掌控力和精簡的管理。

Azure 虛擬機器可用來執行完整而連續的 SharePoint 部署。Azure 經測試可與其他 Microsoft 應用程式密切搭配運作，提供最理想的使用性。因此，組織將可在 Azure 內設定及部署 SharePoint Server，無論是為新的 SharePoint 部署佈建基礎結構，還是擴充現有部署，皆可輕易完成。當業務工作負載增加時，組織可以快速擴充其 SharePoint 基礎結構。同理，當工作負載需求下降時，組織則可依需求縮減資源，而僅就使用的部分付費。Azure 虛擬機器可為各式各樣的商業需求提供絕佳的基礎結構，如本文先前討論的四種 SharePoint 案例所說明。

要在 Azure 虛擬機器上成功部署 SharePoint Server，必須經過縝密的規劃，尤其是要考量關鍵伺服器陣列基礎結構和部署選項的範圍。本文提供的深入探討和最佳作法，可協助組織擬定決策，以實作完善的 SharePoint 部署。

其他資源
--------

-   Azure 訓練套件：實際操作實驗室和簡報

    <http://windowsazure-trainingkit.github.com/labs.htm>

-   開始使用 Azure PowerShell

    <http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156055>

-   Azure 管理 Cmdlet

    <http://msdn.microsoft.com/zh-tw/library/windowsazure/jj152841>

-   適用於不同作業系統的命令列工具和 PowerShell Cmdlet

    [https://www.windowsazure.com/en-us/manage/downloads/](/en-us/manage/downloads/)

-   使用說明和最佳作法文件

    [https://www.windowsazure.com/en-us/manage/windows/](/en-us/manage/windows/)


