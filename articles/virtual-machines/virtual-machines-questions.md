<properties
	pageTitle="VM 常見問題集 | Microsoft Azure"
	description="針對以傳統部署模型建立的 Azure 虛擬機器，提供一些相關常見問題的解答。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/17/2015"
	ms.author="cynthn"/>

# 關於以傳統部署模型建立之 Azure 虛擬機器的常見問題集

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。


本文可解決使用者提出之有關以傳統部署模型建立之 Azure 虛擬機器的一些常見問題，這些問題來自 Azure VM 支援小組以及論壇、新聞群組和其他文章中的留言。如需基本資訊，請先從[關於虛擬機器](virtual-machines-about.md)開始。

## 我可以在 Azure VM 上執行什麼？

所有的訂閱者都可以在 Azure 虛擬機器上執行伺服器軟體。此外，MSDN 訂閱者可以存取 Azure 提供的特定 Windows 用戶端映像。

針對伺服器軟體，您可以在 Azure 虛擬機器上執行最新版本的 Windows Server、各種 Linux 散發套件，以及託管不同的伺服器工作負載與服務。如需支援的詳細資料，請參閱：

• 針對 Windows VM -- [Azure 虛擬機器的 Microsoft 伺服器軟體支援](http://go.microsoft.com/fwlink/p/?LinkId=393550)

• 針對 Linux VM -- [Azure 背書之散發套件上的 Linux](http://go.microsoft.com/fwlink/p/?LinkId=393551)

針對 Windows 用戶端映像，特定版本的 Windows 7 和 Windows 8.1 可供 MSDN Azure 權益訂閱者和 MSDN 開發與測試隨用隨付訂閱者 (針對開發與測試工作) 使用。如需詳細資訊 (包括指示和限制)，請參閱 [MSDN 訂閱者的 Windows 用戶端映像](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/)。

## 我可以使用多少的儲存體搭配虛擬機器？

每個資料磁碟最多可達 1 TB。可使用的資料磁碟數量取決於虛擬機器的大小。如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-size-specs.md)。

Azure 儲存體帳戶提供作業系統磁碟和任何資料磁碟的儲存空間。每個磁碟是以分頁 Blob 方式儲存的 .vhd 檔案。如需定價的詳細資料，請參閱[儲存體定價詳細資料](http://go.microsoft.com/fwlink/p/?LinkId=396819)。

## 可以使用哪些虛擬硬碟類型？

Azure 支援固定的 VHD 格式虛擬硬碟。如果您想要在 Azure 中使用 VHDX 格式磁碟，請使用 Hyper-V 管理員或 [convert-VHD](http://go.microsoft.com/fwlink/p/?LinkId=393656) Cmdlet 來加以轉換。接著，請使用 [Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) Cmdlet (在服務管理模式中) 將 VHD 上傳到 Azure 中的儲存體帳戶，您變可以在虛擬機器上使用。Cmdlet 會將動態 VHD 轉換成固定 VHD，但不會從 VHDX 轉換成 VHD。

- 如需適用於 Linux 的指示，請參閱[建立及上傳含有 Linux 作業系統的虛擬硬碟](virtual-machines-linux-create-upload-vhd.md)。

- 如需適用於 Windows 的指示，請參閱[建立及上傳 Windows Server VHD 至 Azure](virtual-machines-create-upload-vhd-windows-server.md)。

如需上傳資料磁碟的指示，請參閱 Linux 或 Windows 文章，並從連線至 Azure 的步驟開始。

## 這些虛擬機器與 Hyper-V 虛擬機器是一樣的嗎？

在許多方面來說，它們與「第一代」Hyper-V VM 類似，但並非完全相同。這兩種類型都提供虛擬的硬體，以及可相容 VHD 格式虛擬硬碟。這表示您可以在 Hyper-V 和 Azure 之間移動它們。有時讓 Hyper-V 使用者感到驚訝的三個主要差異為：

- Azure 不會提供虛擬機器的主控台存取權。
- 多數[大小](virtual-machines-size-specs.md)的 Azure VM 僅有 1 個虛擬網路介面卡，表示這些 VM 也可能只有 1 個外部 IP 位址。(A8 和 A9 大小會使用第二個網路介面卡，讓應用程式在有限案例中的執行個體之間進行通訊。)
- Azure VM 不支援第 2 代 Hyper-V VM 功能。如需這些功能的相關詳細資料，請參閱 [Hyper-V 的虛擬機器規格](http://technet.microsoft.com/library/dn592184.aspx)。

## 這些虛擬機器可以使用我現有的內部部署網路基礎結構嗎？

對於服務管理中建立的虛擬機器，您可以使用 Azure 虛擬網路來擴充現有的基礎結構。這個方法像是在設立一個分公司。您可以在 Azure 中佈建及管理虛擬私人網路 (VPN)，並使用內部部署 IT 基礎結構安全地連接這些網路。如需詳細資訊，請參閱[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

當建立虛擬機器時，將需要指定您想要虛擬機器隸屬的網路。這表示，例如，您不能將現有的虛擬機器加入到虛擬網路。然而，您可以透過從現有的虛擬機器中斷虛擬硬碟 (VHD) 連結，然後使用該虛擬硬碟建立含有您想要之網路組態的新虛擬機器以解決這個問題。

## 如何存取我的虛擬機器？

您需要建立遠端連線以登入虛擬機器，針對 Windows VM，請使用遠端桌面連線，針對 Linux VM，請使用安全殼層 (SSH)。如需相關指示，請參閱：

- [如何登入執行 Windows Server 的虛擬機器](virtual-machines-log-on-windows-server.md)。最多支援 2 個並行連線，除非伺服器設定為遠端桌面服務工作階段主機。  
- [如何登入執行 Linux 的虛擬機器](virtual-machines-linux-how-to-log-on.md)。根據預設，SSH 允許最多 10 個並行連線。您可以編輯組態檔以增加這個數字。

如果您遇到遠端桌面或 SSH 的相關問題，請安裝並使用 [VMAccess](http://go.microsoft.com/fwlink/p/?LinkId=396856) 擴充功能來協助修正問題。對於 Windows VM，其他選項包括：

- 在 Azure 預覽入口網站中，找出 VM，然後從命令列按一下 [重設遠端存取]。
- 檢閱[疑難排解以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線](virtual-machines-troubleshoot-remote-desktop-connections.md)。
- 使用 Windows PowerShell 遠端功能以連線到 VM，或建立其他資源的額外端點來連線至 VM。如需詳細資訊，請參閱[如何設定虛擬機器的端點](virtual-machines-set-up-endpoints.md)。

如果您熟悉 Hyper-V，您可能正在尋找類似「虛擬機器連接」的工具。Azure 沒有提供類似的工具，因為並不支援主控台存取虛擬機器。

## 我可以使用 D: 磁碟機 (Windows) 或 /dev/sdb1 (Linux) 嗎？

您不應使用 D: 磁碟機 (Windows) 或 /dev/sdb1 (Linux)。它們僅提供暫存空間，因此您會有遺失資料且無法復原的風險。導致這種狀況發生的一種常見方式，是當虛擬機器移動到不同的主機時。可能要移動虛擬機器的一些原因是調整虛擬機器的大小、更新主機，或主機上的硬體故障等等。

## 如何變更暫存磁碟的磁碟機代號？

在 Windows 虛擬機器上，您可以透過移動分頁檔並重新指派磁碟機代號來變更磁碟機代號，但您必須確定以特定的順序執行這些步驟。如需相關指示，請參閱[變更 Windows 暫存磁碟的磁碟機代號](virtual-machines-windows-change-drive-letter.md)。

## 如何升級客體作業系統？

升級這個詞彙通常是指移動至較新版的作業系統，但仍在相同硬體上。對於 Azure VM，Linux 和 Windows 移動至較新版的處理程序有所不同：

- 針對 Linux VM，使用套件管理工具和適合散發的程序。
- 針對 Windows 虛擬機器，使用 Windows Server 移轉工具。請勿嘗試升級位於 Azure 的客體 OS。不支援此動作是因為有失去虛擬機器存取權的風險。如果在升級期間發生問題，您可能會無法啟動遠端桌面工作階段，而且將無法疑難排解問題。如需關於此工具和程序的一般詳細資料，請參閱[移轉角色與功能至 Windows Server](http://go.microsoft.com/fwlink/p/?LinkId=396940)。如需升級至 Windows Server 2012 R2 的詳細資料，請參閱 [Windows Server 2012 R2 的升級選項](https://technet.microsoft.com/library/dn303416.aspx)。

## 虛擬機器上的預設使用者名稱和密碼是什麼？

Azure 提供的映像沒有預先設定的使用者名稱和密碼。當您使用這些映像的其中一個來建立虛擬機器時，您將需要提供用來登入虛擬機器的使用者名稱和密碼。

如果您已經忘記使用者名稱或密碼，而且已經安裝 VM 代理程式，您可以安裝並使用 VMAccess 擴充功能來修正問題。

其他詳細資料：

- 針對 Linux 映像，如果您使用管理入口網站，則 'azureuser' 指定為預設的使用者名稱，但是您可以使用 [從資源庫] 代替 [快速建立] 的方式建立虛擬機器來變更預設使用者名稱。使用 [從資源庫] 也可讓您決定是否要使用密碼、SSH 金鑰，或同時使用兩者來登入。此使用者帳戶是非特殊權限使用者，但有 'sudo' 存取權限可以執行特殊權限命令。'root' 帳戶已停用。
- 針對 Windows 映像，當您建立 VM 時需要提供使用者名稱和密碼。帳戶會加入至系統管理員群組。

## Azure 可以在我的虛擬機器上執行防毒軟體嗎？

Azure 提供數個防毒軟體解決方案的選項，但管理則掌握在您手中。例如，您可能需要個別訂閱反惡意程式碼軟體，且需要決定何時執行掃描和安裝更新。當您建立 Windows 虛擬機器或在稍後的時間點，可以使用適用於 Microsoft Antimalware、Symantec Endpoint Protection，或 TrendMicro Deep Security 代理程式的 VM 延伸模組，新增防毒軟體支援。Symantec 和 TrendMicro 延伸模組提供您使用免費的限時試用訂用帳戶或現有的企業訂用帳戶。Microsoft Antimalware 則是免費的。如需詳細資訊，請參閱：

- [如何在 Azure VM 上安裝和設定 Symantec Endpoint Protection](http://go.microsoft.com/fwlink/p/?LinkId=404207)
- [如何在 Azure VM 上安裝和設定 Trend Micro Deep Security as a Service](http://go.microsoft.com/fwlink/p/?LinkId=404206)
- [在 Azure 虛擬機器上部署反惡意程式碼解決方案](http://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## 備份和復原有哪些選擇？

Azure 備份在特定地區以預覽版提供。如需詳細資訊，請參閱[備份 Azure 虛擬機器](backup-azure-vms.md)。來自認證合作夥伴的其他解決方案也可供使用。若要了解目前可用的項目，請搜尋 Azure Marketplace。

其他選項是使用 Blob 儲存體的快照集功能。若要這樣做，您需要在任何依賴 Blob 快照集的作業前關閉 VM。這樣會儲存擱置的資料寫入，並讓檔案系統保持一致的狀態。

## Azure 如何對我的 VM 收費？

Azure 可依據 VM 的大小和作業系統，以每小時價格方式收費。針對不足一小時的部分，Azure 只會收取使用分鐘數的費用。如果您以包含特定預先安裝軟體的 VM 映像建立 VM，可能會收取額外的每小時軟體費用。Azure 針對 VM 作業系統和資料磁碟的儲存體個別收費。暫存磁碟儲存體是免費的。

當 VM 狀態為「執行中」或「已停止」都會向您收費，但當 VM 狀態為「已停止 (已取消配置)」則不會收費。若要讓 VM 進入「已停止 (已取消配置)」狀態，請執行下列其中一項：

- 從管理入口網站關閉或刪除 VM。
- 使用 Stop-AzureVM Cmdlet (在 Azure PowerShell 模組中可用)。
- 在服務管理 REST API 中使用關機角色作業，並為 PostShutdownAction 元素指定 StoppedDeallocated。

如需更多詳細資料，請參閱[虛擬機器定價](http://azure.microsoft.com/pricing/details/virtual-machines/)。

## Azure 會因為維護重新啟動我的 VM 嗎？

一般而言，只要您需要，隨時都可以啟動、停止或重新啟動 VM。Azure 有時會重新啟動您的 VM，這是 Azure 資料中心中定期、計劃性維護更新的一部份。當 Azure 偵測到嚴重的硬體問題可能會影響您的 VM 時，會發生非計劃性維護事件。對於非計劃性事件，Azure 會自動地移轉 VM 至狀況良好的主機並重新啟動 VM。

針對任何獨立的 VM (表示 VM 並非可用性集合的一部份)，Azure 在計劃性維護之前，至少每一個星期會使用電子郵件通知訂用帳戶的服務管理員，因為 VM 可能會在更新期間重新啟動。在 VM上執行的應用程式可能會遭遇停機時間。

當因為計畫性維護而發生重新啟動時，您也可以使用 Azure 入口網站或 Azure PowerShell 來檢視重新啟動記錄。如需詳細資訊，請參閱[檢視 VM 重新啟動記錄檔](http://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/) (英文)。

若要提供備援，請在相同的可用性集合中放入兩個以上同樣設定的 VM。這有助於確保在計劃性或非計劃性的維護期間，至少有一個 VM 仍可使用。Azure 保證此組態的 VM 可用性特定層級。如需詳細資訊，請參閱[管理虛擬機器的可用性](virtual-machines-manage-availability.md)。

## 其他資源

[關於 Azure 虛擬機器](virtual-machines-about.md)

[建立 Linux 虛擬機器的不同方式](virtual-machines-linux-choices-create-vm.md)

[建立 Windows 虛擬機器的不同方式](virtual-machines-windows-choices-create-vm.md)

<!---HONumber=Oct15_HO3-->