<properties
	pageTitle="設定 AlwaysOn 可用性群組 (GUI) | Microsoft Azure"
	description="使用 Azure 虛擬機器建立 AlwaysOn 可用性群組。本教學課程主要是透過使用者介面作業，而非編寫指令碼。"
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="12/04/2015"
	ms.author="jroth" />

# 在 Azure VM (GUI) 中設定 AlwaysOn 可用性群組

> [AZURE.SELECTOR]
- [Portal - Resource Manager](virtual-machines-sql-server-alwayson-availability-groups-gui-arm.md)
- [Portal - Classic](virtual-machines-sql-server-alwayson-availability-groups-gui.md)
- [PowerShell - Classic](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)

<br/>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


本端對端教學課程將示範如何透過在 Azure 虛擬機器上執行的 SQL Server AlwaysOn 實作可用性群組。

>[AZURE.NOTE] 在 Azure 管理入口網站中，提供具有接聽程式的 AlwaysOn 可用性群組專用的新資源庫設定。這可自動設定 AlwaysOn 可用性群組所需的所有項目。如需詳細資訊，請參閱 [Microsoft Azure 傳統入口網站資源庫提供的 SQL Server AlwaysOn](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)。若要使用 PowerShell，請參閱[使用 PowerShell 設定 Azure 中的 AlwaysOn 可用性群組](virtual-machines-sql-server-alwayson-availability-groups-powershell.md)中，相同案例的教學課程。

在本教學課程結束時，您 Azure 中的 SQL Server AlwaysOn 解決方案將包含下列項目：

- 包含多個子網路 (前端和後端子網路) 的虛擬網路

- 具有 Active Directory (AD) 網域的網域控制站

- 兩個 SQL Server VM 已部署至後端子網路並加入 AD 網域

- 具有節點多數仲裁模型的 3 節點 WSFC 叢集

- 具有兩份可用性資料庫同步認可複本的可用性群組

下圖將解決方案以圖形呈現。

![在 Azure 中針對 AG 測試實驗室架構](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC791912.png)

請注意以下這是一個可用的組態。比方說，您可以將雙複本可用性群組的 VM 數量減到最少，以縮短在 Azure 中的運算時數，方法是在 2 節點 WSFC 叢集中使用網域控制站作為仲裁檔案共用見證。此方法可讓上述組態減少一個 VM。

本教學課程假設您已句備下列條件：

- 您已經有 Azure 帳戶。

- 您已經知道如何透過 GUI 佈建來自虛擬機器資源庫的 SQL Server VM。如需詳細資訊，請參閱[在 Azure 上佈建 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md)

- 您已非常熟悉 AlwaysOn 可用性群組的功能。如需詳細資訊，請參閱 [AlwaysOn 可用性群組 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。

>[AZURE.NOTE] 如想將 AlwaysOn 可用性群組與 SharePoint 搭配使用，另請參閱[為 SharePoint 2013 設定 SQL Server 2012 AlwaysOn 可用性群組 ](https://technet.microsoft.com/library/jj715261.aspx)。

## 建立虛擬網路和網域控制站伺服器

一開始先建立新的 Azure 試用帳戶。完成帳戶設定，系統會將您導向 Azure 傳統入口網站的主畫面。

1. 如下方所示，按一下頁面左下角的 [新增] 按鈕。

	![在入口網站中按一下 [新增]](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665511.gif)

1. 如下方所示，按一下 [網絡服務]，然後按一下 [虛擬網路]，再按一下 [自訂建立]。

	![建立虛擬網路](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665512.gif)

1. 在 [建立虛擬網路] 對話方塊中，透過套用下方設定以通過精靈中的頁面，來建立新的虛擬網路。

	|Page|設定|
|---|---|
|虛擬網路詳細資料|**名稱 = ContosoNET**<br/>**地區 = 美國西部**|
|DNS 伺服器和 VPN 連線能力|None|
|虛擬網路位址空間|設定如以下螢幕擷取畫面所示：![建立虛擬網路](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784620.png)|

1. 接下來，建立要作為網域控制站 (DC) 的 VM。如下方所示，再次依序按一下 [新增]、[運算]、[虛擬機器]，然後 [從資源庫]。

	![建立 VM](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784621.png)

1. 在 [建立虛擬機器] 對話方塊中，透過套用下方設定以通過精靈中的頁面，來設定新的 VM。

	|Page|設定|
|---|---|
|選取虛擬機器作業系統|Windows Server 2012 R2 Datacenter|
|虛擬機器組態|**版本發行日期** = (最新)<br/>**虛擬機器名稱** = ContosoDC<br/>**層** = 標準<br/>**大小** = A2 (2 核心)<br/>**新使用者名稱** = AzureAdmin<br/>**新密碼**= Contoso!000<br/>**確認** = Contoso!000|
|虛擬機器組態|**雲端服務** = 建立新的雲端服務<br/>**雲端服務 DNS 名稱** = 唯一的雲端服務名稱<br/>**DNS 名稱** = 唯一的名稱 (例如：ContosoDC123)<br/>**區域/同質群組/虛擬網路** = ContosoNET<br/>**虛擬網路子網路** = 後端 (10.10.2.0/24)<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性集合** = (無)|
|虛擬機器選項|使用預設值|

新的 VM 設定完畢後，請等候系統佈建 VM。此程序需要一些時間才能完成，如果您按一下 Azure 傳統入口網站中的 [虛擬機器] 索引標籤，您會看到 ContosoDC 的循環狀態從 [啟動中 (佈建中)] 變成 [停止]、[啟動中]、[執行中 (佈建中)]，最後 [執行中]。

現在已成功佈建 DC 伺服器。接下來，您將在這個 DC 伺服器上設定 Active Directory 網域。

## 設定網域控制站。

下列步驟將帶您把 ContosoDC 電腦設定為 corp.contoso.com 的網域控制站。

1. 在入口網站中，選取 [ContosoDC] 電腦。在 [儀表板] 索引標籤上按一下 [連接]，開啟遠端桌面存取的 RDP 檔案。

	![連接至虛擬機器](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784622.png)

1. 以所設定的系統管理員帳戶 (**\\AzureAdmin**) 和密碼 (**Contoso! 000**) 登入。

1. 根據預設，應會顯示 [伺服器管理員] 儀表板。

1. 按一下儀表板上的 [新增角色與功能] 連結。

	![透過 [伺服器總管] 新增角色](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784623.png)

1. 連續選取 [下一步]，直到到達 [伺服器角色] 區段。

1. 選取 [Active Directory 網域服務] 和 [DNS 伺服器] 角色。出現提示時，加入這些角色所需的所有其他功能。

	>[AZURE.NOTE] 螢幕會顯示無靜態 IP 位址的驗證警告。如果您是要測試組態，請按一下 [繼續]。實際操作時，請[使用 PowerShell 設定網域控制站電腦的靜態 IP 位址](./virtual-network/virtual-networks-reserved-private-ip.md)。

	![新增角色對話方塊](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784624.png)

1. 連續按一下 [下一步] 直到到達 [確認] 區段。選取 [必要時自動重新啟動目的地伺服器] 核取方塊。

1. 按一下 [Install]。

1. 功能安裝完畢後，請回到 [伺服器管理員] 儀表板。

1. 在左側窗格中選取新的 [AD DS] 選項。

1. 按一下黃色警告列上的 [更多] 連結。

	![在 DNS 伺服器 VM 上新增 AD DS 對話方塊](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784625.png)

1. 在 [所有伺服器工作詳細資料] 對話方塊的 [動作] 欄中，按一下 [將此伺服器升級為網域控制站]。

1. 在 **Active Directory 網域服務組態精靈**中，使用下列值：

	|Page|設定|
|---|---|
|部署組態|**新增樹系** = 已選取<br/>**根網域名稱** = corp.contoso.com|
|網域控制站選項|**密碼** = Contoso!000<br/>**確認密碼** = Contoso!000|

1. 按一下 [下一步]，以通過精靈中的其他頁面。在 [檢查先決條件] 頁面上，確認是否出現下列訊息：**已順利通過所有先決條件檢查**。請注意，務必要檢閱所有適用您情況的警告訊息，但是仍可以繼續進行安裝。

1. 按一下 [Install]。**ContosoDC** 虛擬機器便會自動重新開機。

## 設定網域帳戶

接下來的步驟，將帶您設定 Active Directory (AD) 帳戶，以供之後使用。

1. 重新登入 **ContosoDC** 電腦。

1. 在 [伺服器管理員] 中，選取 [工具]，然後按一下 [Active Directory 管理中心]。

	![Active Directory 管理中心](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784626.png)

1. 在 [Active Directory 管理中心] 中，選取左窗格中的 [企業 (本機)]。

1. 在右側的 [工作] 窗格中，選取 [新增]，然後按一下 [使用者]。套用下列設定：

	|設定|值|
|---|---|
|**名字**|安裝|
|**使用者 SamAccountName**|安裝|
|**密碼**|Contoso! 000|
|**確認密碼**|Contoso! 000|
|**其他密碼選項**|已選取|
|**密碼永久有效**|已檢查|

1. 按一下 [確定]，以建立**安裝**使用者。此帳戶將用來設定容錯移轉叢集和可用性群組。

1. 透過相同的步驟建立兩個額外的使用者：**CORP\\SQLSvc1** 和 **CORP\\SQLSvc2**。這些帳戶將用於 SQL Server 執行個體。接下來，您必須提供 **CORP\\Install** 必要的權限，以設定 Windows 服務容錯移轉叢集 (WSFC)。

1. 在 [Active Directory 管理中心] 中，選取左窗格中的 [企業 (本機)]。然後在右側的 [工作] 窗格中，按一下 [內容]。

	![企業使用者內容](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784627.png)

1. 選取 [延伸模組]，然後按一下 [安全性] 索引標籤上的 [進階] 按鈕 。

1. 在 [企業的進階安全性設定] 對話方塊上。按一下 [新增]。

1. 再按一下 [選取主體]。然後搜尋 **CORP\\Install**。按一下 [確定]。

1. 選取 [讀取所有內容] 和 [建立電腦物件] 權限。

	![企業使用者權限](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784628.png)

1. 按一下 [確定]，再按一下 [確定]。關閉 [企業內容] 視窗。

現在 Active Directory 和使用者物件便已設定完畢，請建立三個 SQL Server VM，並將這些 VM 加入此網域。

## 建立 SQL Server VM

接下來，建立三個 VM，包括 WSFC 叢集節點和兩個 SQL Server VM。為了逐一建立每個 VM，請回到 Azure 傳統入口網站，依序按一下 [**新增**]、[**計算**]、[**虛擬機器**] 和 [**從資源庫**]。然後使用下表中的範本建立 VM。

|Page|VM1|VM2|VM3|
|---|---|---|---|
|選取虛擬機器作業系統|**Windows Server 2012 R2 Datacenter**|**SQL Server 2014 RTM Enterprise**|**SQL Server 2014 RTM Enterprise**|
|虛擬機器組態|**版本發行日期** = (最新)<br/>**虛擬機器名稱** = ContosoWSFCNode<br/>**層** = 標準<br/>**大小** = A2 (2 核心)<br/>**新的使用者名稱** = AzureAdmin<br/>**新密碼** = Contoso!000<br/>**確認** = Contoso!000|**版本發行日期** = (最新)<br/>**虛擬機器名稱** = ContosoSQL1<br/>**層** = 標準<br/>**大小** = A3 (4 核心)<br/>**新的使用者名稱** = AzureAdmin<br/>**新密碼** = Contoso!000<br/>**確認** = Contoso!000|**版本發行日期** = (最新)<br/>**虛擬機器名稱** = ContosoSQL2<br/>**層** = 標準<br/>**大小** = A3 (4 核心)<br/>**新的使用者名稱** = AzureAdmin<br/>**新密碼** = Contoso!000<br/>**確認** = Contoso!000|
|虛擬機器組態|**雲端服務** = 先前建立的唯一雲端服務 DNS 名稱 (例如：ContosoDC123)<br/>**區域/同質群組/虛擬網路** = ContosoNET<br/>**虛擬網路子網路** = 後端 (10.10.2.0/24)<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性集合** = 建立可用性集合<br/>**可用性集合名稱** = SQLHADR|**雲端服務** = 先前建立的唯一雲端服務 DNS 名稱 (例如：ContosoDC123)<br/>**區域/同質群組/虛擬網路** = ContosoNET<br/>**虛擬網路子網路** = 後端 (10.10.2.0/24)<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性集合名稱** = SQLHADR (也可以在虛擬機器建立後，再設定可用性集合。三部虛擬機器都必須指派至 SQLHADR 可用性集合)。|**雲端服務** = 先前建立的唯一雲端服務 DNS 名稱 (例如：ContosoDC123)<br/>**區域/同質群組/虛擬網路** = ContosoNET<br/>**虛擬網路子網路** = 後端 (10.10.2.0/24)<br/>**儲存體帳戶** = 使用自動產生的儲存體帳戶<br/>**可用性集合名稱** = SQLHADR (也可以在虛擬機器建立後，再設定可用性集合。三部虛擬機器都必須指派至 SQLHADR 可用性集合)。|
|虛擬機器選項|使用預設值|使用預設值|使用預設值|

<br/>

>[AZURE.NOTE] 先前的設定建議標準層虛擬機器，因為基本層機器不支援後續建立可用性群組接聽程式所需的負載平衡端點。此外，此處建議的機器大小是為了在 Azure VM 中測試可用性群組。為獲得生產工作負載的最佳效能，請參閱 [Azure 虛擬機器中的 SQL Server 效能最佳作法](virtual-machines-sql-server-performance-best-practices.md)中關於 SQL Server 機器大小和組態的建議。

完整佈建三個 VM 後，您必須將它們加入 **corp.contoso.com** 網域，並將 CORP\\Install 管理權限授與給這些虛擬機器。若要這樣做，請針對每個 VM 執行下列步驟。

1. 首先，變更慣用的 DNS 伺服器位址。然後在清單中選取 VM，並按一下 [連接] 按鈕，將每個 VM 的遠端桌面 (RDP) 檔案下載至您的本機目錄。若要選取 VM，請如下方所示，在資料列中第一個儲存格以外的任何地方按一下。

	![下載 RDP 檔案](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664953.jpg)

1. 啟動您所下載的 RDP 檔案，並使用所設定的系統管理員帳戶 (**BUILTIN\\AzureAdmin**) 和密碼 (**Contoso! 000**) 登入 VM。

1. 登入後，會顯示 [伺服器管理員] 儀表板。按一下左窗格中的 [本機伺服器]。

1. 選取 [DHCP 指派的 IPv4 位址，支援 IPv6] 連結。

1. 在 [網路連接] 視窗中，選取網路圖示。

	![變更 VM 慣用的 DNS 伺服器](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784629.png)

1. 在命令列上按一下 [變更此連接的設定] \(視您的視窗大小而定，可能需按一下雙向右箭頭才能看到此命令)。

1. 選取 [網際網路通訊協定第 4 版 (TCP/IPv4)]，然後按一下 [內容]。

1. 選取使用下列的 DNS 伺服器位址，並指定 [慣用 DNS 伺服器] 中的 [10.10.2.4]。

1. **10.10.2.4** 位址是指派給 Azure 虛擬網路的 10.10.2.0/24 子網路中的 **ContosoDC** VM 的位址。若要確認 **ContosoDC**的 IP 位址，請如下方所示，在命令提示字元中加入 **nslookup contosodc**。

	![使用 NSLOOKUP 尋找 DC 的 IP 位址](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC664954.jpg)

1. 依序按一下 [確**定**]、[關閉] 以認可變更。現在您可以將 VM 加入 **corp.contoso.com**。

1. 回到 [本機伺服器] 視窗中，按一下 [工作群組] 連結。

1. 在 [電腦名稱] 區段中，按一下 [變更]。

1. 選取 [網域] 核取方塊，然後在文字方塊中輸入 **corp.contoso.com**。按一下 [確定]。

1. 在 [Windows 安全性] 快顯對話方塊中，指定預設網域的系統管理員帳戶 (**CORP\\AzureAdmin**) 和密碼 (**Contoso! 000**) 的認證。

1. 出現「歡迎使用 corp.contoso.com 網域」的訊息時，請按一下 [確定]。

1. 按一下 [關閉]，然後按一下快顯對話方塊中的 [立即重新啟動]。

### 將 Corp\\Install 使用者新增為每個 VM 的系統管理員：

1. 等候 VM 重新啟動，然後重新啟動 RDP 檔案，以使用 **BUILTIN\\AzureAdmin** 帳戶登入 VM。

1. 在 [伺服器管理員] 中選取 [工具]，然後按一下 [電腦管理]。

	![電腦管理](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784630.png)

1. 在 [電腦管理 ] 視窗中，展開 [本機使用者和群組]，然後選取 [群組]。

1. 按兩下 [系統管理員] 群組。

1. 在 [系統管理員內容] 對話方塊中，按一下 [新增] 按鈕。

1. 輸入 **CORP\\Install** 使用者，然後按一下 [確定]。當系統提示您輸入認證時，請輸入 **AzureAdmin** 帳戶和 **Contoso! 000** 密碼。

1. 按一下 [確定]，以關閉 [系統管理員內容] 對話方塊。

### 將 [容錯移轉叢集] 功能新增至每個 VM。

1. 在 [伺服器管理員] 儀表板中按一下 [新增角色及功能]。

1. 在**新增角色及功能精靈**中，連續按一下 [下一步] 直到到達 [功能] 頁面。

1. 選取 [容錯移轉叢集]。出現提示時，請新增所有其他相依功能。

	![將 [容錯移轉叢集] 功能新增至 VM](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784631.png)

1. 按一下 [下一步]，然後按一下 [確認] 頁面上的 [安裝]。

1. **容錯移轉叢集**功能安裝完畢後，按一下 [關閉]。

1. 登出 VM。

1. 針對三個伺服器 **ContosoWSFCNode**、**ContosoSQL1**，和 **ContosoSQL2** 重複本節中的步驟。

現在 SQL Server VM 已完成佈建並執行中，但這些 VM 所安裝的是使用預設值的 SQL Server。

## 建立 WSFC 叢集

本節將帶您建立 WSFC 叢集；該叢集將裝載您稍後會建立的可用性群組。到目前為止，您已經針對將用於 WSFC 叢集中的每個 VM，完成下列作業：

- 在 Azure 中完整佈建

- 將 VM 加入網域

- 將 **CORP\\Install** 新增至本機系統管理員群組

- 新增 [容錯移轉叢集] 功能

以上都是將 VM 加入 WSFC 叢集前，每個 VM 所需完成的先決條件。

此外，請注意 Azure 虛擬網路的運作方式和其在內部部署網路中的運作方式不同。您必須依照下列順序建立叢集：

1. 在其中一個節點上 (**ContosoSQL1**) 建立單一節點叢集。

1. 將叢集的 IP 位址修改成未使用的 IP 位址 (**10.10.2.101**)。

1. 讓叢集名稱上線。

1. 新增其他節點 (**ContosoSQL2** 和 **ContosoWSFCNode**)。

請執行下列步驟以完成這些可完整設定叢集的工作。

1. 啟動 **ContosoSQL1** 的 RDP 檔案，並使用網域帳戶 **CORP\\Install** 登入。

1. 在 [伺服器管理員] 儀表板中，選取 [工具]，然後按一下 [容錯移轉叢集管理員]。

1. 如下方所示，在左窗格中，以滑鼠右鍵按一下 [容錯移轉叢集管理員]，再按一下 [建立叢集]。

	![建立叢集](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784632.png)

1. 在建立叢集精靈中，透過套用下方設定以通過精靈中的頁面，來建立單節點叢集：

	|Page|設定|
|---|---|
|開始之前|使用預設值|
|選取伺服器|在 [輸入伺服器名稱] 中輸入 **ContosoSQL1**，然後按一下 [新增]。|
|驗證警告|選取 [**否。我不需要 Microsoft 提供此叢集的支援，也不需要執行驗證測試。請在我按一下 [下一步] 後，繼續建立叢集**]。|
|用於管理叢集的存取點|在 [叢集名稱] 中輸入 **Cluster1**|
|確認|除非您使用的是儲存空間，否則請使用預設值。請詳閱此表之後的備註。|

	>[AZURE.WARNING] 如果您使用的是會將多個磁碟組成存放集區的[儲存空間](https://technet.microsoft.com/library/hh831739)，則請務必取消勾選 [確認] 頁面上的 [將所有合格的儲存體新增至叢集] 核取方塊。如果不取消勾選此選項，在群集程序進行期間虛擬磁碟會中斷連結。因此，虛擬磁碟不會顯示在 [磁碟管理員] 或 [總管] 中，直到您將儲存空間從叢集中移除，並使用 PowerShell 重新連接虛擬磁碟。

1. 在左窗格中，展開 [容錯移轉叢集管理員]，然後按一下 [Cluster1.corp.contoso.com]。

1. 在中央窗格中向下捲動至 [叢集核心資源] 區段，並展開 [名稱：Clutser1] 的詳細資料。在 [失敗] 狀態中，應該會同時出現 [名稱] 和 [IP 位址] 資源 。由於指派給叢集的 IP 位址與虛擬機器的的 IP 位址相同，是個重複的位址，因此無法讓該 IP 位址資源上線。

1. 以滑鼠右鍵按一下失敗的 [**IP 位址**]資源，然後按一下 [**內容**]。

	![叢集屬性](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784633.png)

1. 選取 [靜態 IP 位址]，並在 [位址] 文字方塊中指定 [10.10.2.101]。然後按一下 **[確定]**。

1. 在 [叢集核心資源] 區段中，以滑鼠右鍵按一下 [名稱：Cluster1]，再按一下 [上線]。然後等待兩個資源上線。叢集名稱資源上線後，會以新的 AD 電腦帳戶更新 DC 伺服器。此 AD 帳戶稍後將用來執行可用性群組叢集服務。

1. 最後，將剩餘的節點新增至叢集。如下方所示，在瀏覽器樹狀目錄中以滑鼠右鍵按一下 [Cluster1.corp.contoso.com]，再按一下 [新增節點]。

	![將節點新增至叢集](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC784634.png)

1. 在 [新增節點精靈] 中，按 [下一步]。在 [輸入伺服器名稱] 中輸入伺服器名稱，然後按一下 [新增]，於 [選取伺服器] 頁面上，將 **ContosoSQL2** 和 **ContosoWSFCNode** 新增至清單。完成之後，按 [下一步]。

1. 在 [驗證警告] 頁面上，按一下 [否] \(實際操作時，請執行驗證測試)。然後按 [下一步]。

1. 在 [確認] 頁面中按 [下一步]，以新增節點。

	>[AZURE.WARNING] 如果您使用的是會將多個磁碟組成存放集區[儲存空間](https://technet.microsoft.com/library/hh831739)，則請務必取消勾選 [將所有合格的儲存體新增至叢集] 核取方塊。如果不取消勾選此選項，在群集程序進行期間虛擬磁碟會中斷連結。因此，虛擬磁碟不會顯示在 [磁碟管理員] 或 [總管] 中，直到您將儲存空間從叢集中移除，並使用 PowerShell 重新連接虛擬磁碟。

1. 將節點新增至叢集後，請按一下 [完成]。容錯移轉叢集管理員現在應該會顯示您的叢集具有三個節點，並將這些節點列在**節點**容器中。

1. 登出遠端桌面工作階段。

## 針對可用性群組準備 SQL Server 執行個體

本節將針對 **ContosoSQL1** 和 **contosoSQL2** 進行下列工作：

- 為具有預設 SQL Server 執行個體必要權限的 **NT AUTHORITY\\System** 新增登入

- 將 **CORP\\Install** 新增為預設 SQL Server 執行個體的系統管理員角色

- 開啟防火牆以供 SQL Server 進行遠端存取

- 啟用 [AlwaysOn 可用性群組] 功能

- 將 SQL Server 服務帳戶變更為 **CORP\\SQLSvc1** 和 **CORP\\SQLSvc2**

執行這些動作沒有順序限制。不過，下列步驟將依順序執行這些動作。針對 **ContosoSQL1** 和 **ContosoSQL2**，依照下列步驟執行：

1. 如果您尚未將 VM 登出遠端桌面工作階段，請現在登出。

1. 啟動 **ContosoSQL1** 和 **ContosoSQL2** 的 RDP 檔案，並以 **BUILTIN\\AzureAdmin** 的身分登入。

1. 首先，先將 **NT AUTHORITY\\System** 和必要權限新增至 SQL Server 登入。啟動 **SQL Server Management Studio**。

1. 按一下 [連接]，連接至預設 SQL Server 執行個體。

1. 在 [物件總管] 中，依序展開 [安全性]、[登入]。

1. 以滑鼠右鍵按一下 [NT AUTHORITY\\System] 登入，然後按一下 [內容]。

1. 在 [安全性實體] 頁面中，對本機伺服器，針對下列權限選取 [授與]，然後按一下 [確定]。

	- 更改所有可用性群組

	- 連接 SQL

	- 檢視伺服器狀態

1. 接下來，將 **CORP\\Install** 新增為預設 SQL Server 執行個體的**系統管理員**角色在 [物件總管] 中，以滑鼠右鍵按一下 [登入]，再按一下 [新增登入]。

1. 在 [登入名稱] 中輸入 **CORP\\Install**。

1. 在 [伺服器角色] 頁面上，選取 [系統管理員]。然後按一下 **[確定]**。建立登入之後，展開 [物件總管] 中的 [登入] 便可看到該登入。

1. 接下來，建立 SQL Server 的防火牆規則。在 [開始] 畫面中啟動 [具備進階安全性的 Windows 防火牆]。

1. 在左側窗格中，選取 [內送規則]。在右窗格中，按一下 [新增規則]。

1. 在 [規則類型] 頁面中，選取 [程式]，然後按 [下一步]。

1. 在 [程式] 頁面中，選取 [此程式路徑]，並在文字方塊中輸入 **%ProgramFiles%\\Microsoft SQL Server\\MSSQL12.MSSQLSERVER\\MSSQL\\Binn\\sqlservr.exe** (如果您使用的是 SQL Server 2012、則 SQL Server 的目錄為 **MSSQL11.MSSQLSERVER**)。然後按 [下一步]。

1. 在 [動作] 頁面中，保持選取 [允許連接]，然後按 [下一步]。

1. 在 [設定檔] 頁面中，接受預設設定，然後按 [下一步]。

1. 在 [名稱] 頁面中，指定規則名稱，例如在 [名稱] 文字方塊中指定 [SQL Server (程式規則)]，然後按一下 [完成]。

1. 接下來，啟用 [AlwaysOn 可用性群組] 功能。在 [開始] 畫面中，啟動 [SQL Server 組態管理員]。

1. 在瀏覽器樹狀目錄中按一下 [SQL Server 服務]，然後以滑鼠右鍵按一下 [SQL Server (MSSQLSERVER)] 服務，再按一下 [內容]。

1. 如下方所示，按一下 [AlwaysOn 高可用性] 索引標籤，然後選取 [啟用 AlwaysOn 可用性群組]，再按一下 [套用]。按一下快顯對話方塊中的 [確定]，先不要關閉 [內容] 視窗。變更服務帳戶之後，將重新啟動 SQL Server 服務。

	![啟用 AlwaysOn 可用性群組](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665520.gif)

1. 接下來，變更 SQL Server 服務帳戶。按一下 [登入] 索引標籤，在 [帳戶名稱] 中輸入 **CORP\\SQLSvc1** (若為 **ContosoSQL1**)，或 **CORP\\SQLSvc2** (若為 **ContosoSQL2**)，填入密碼並加以確認，然後按一下 [確定]。

1. 在快顯視窗中按一下 [是]，重新啟動 SQL Server 服務。SQL Server 服務重新啟動之後，您在 [內容] 視窗中所進行的變更便會生效。

1. 登出所有 VM。

## 建立可用性群組。

現在已準備就緒，可以開始設定可用性群組。以下列出需執行的動作：

- 在 **ContosoSQL1** 上建立新的資料庫 (**MyDB1**)

- 建立資料庫的完整備份和交易記錄備份。

- 透過 [NORECOVERY] 選項將完整備份和記錄備份還原至 **ContosoSQL2**

- 透過同步認可、自動容錯移轉，和可讀取的次要複本建立可用性群組 (**AG1**)

### 在 ContosoSQL1 上建立 MyDB1 資料庫：

1. 如果您有您尚未將 **ContosoSQL1** 和 **ContosoSQL2** 登出遠端桌面工作階段，請現在登出

1. 啟動 **ContosoSQL1** 的 RDP 檔案，並以 **CORP\\Install** 的身分登入。

1. 在 [檔案總管] 中，於 **C:** 磁碟下方，建立名為**備份**的目錄。此目錄將用來備份和還原您的資料庫。

1. 如下方所示，以滑鼠右鍵按一下新目錄，指向 [共用對象]，然後按一下 [特定人員]。

	![建立備份資料夾](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665521.gif)

1. 如下方所示，新增 **CORP\\SQLSvc1** 並授與 **讀取/寫入**權限，再新增 **CORP\\SQLSvc2** 並授與**讀取**權限，然後按一下 [共用]。檔案共用程序完成後，按一下 [完成]。

	![將權限授與備份資料夾](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665522.gif)

1. 接下來，建立資料庫。透過 [開始] 功能表，啟動 **SQL Server Management Studio**，然後按一下 [連接]，連接至預設的 SQL Server 執行個體。

1. 在 [物件總管] 中，以滑鼠右鍵按一下 [資料庫]，再按一下 [新增資料庫]。

1. 在 [資料庫名稱] 中，輸入 **MyDB1**，然後按一下 [確定]。

### 建立 MyDB1 的完整備份，然後將其還原至 ContosoSQL2：

1. 接下來，備份資料庫的所有內容。在 [物件總管] 中，展開 [資料庫]，以滑鼠右鍵按一下 [MyDB1]，指向 [工作]，然後按一下 [備份]。

1. 在 [來源] 區段中，讓 [備份類型] 的設定保持為 [完整]。在 [**目的地**] 區段中按一下 [**移除**]，移除備份檔案的預設檔案路徑。

1. 在 [目的地] 區段中，按一下 [新增]。

1. 在 [檔案名稱] 文字方塊中，輸入 **\\ContosoSQL1\\backup\\MyDB1.bak**。接著按一下 [確定]，再按一下 [確定]，以備份資料庫。備份作業完成後，再次按一下 [確定]，關閉對話方塊。

1. 接下來，備份資料庫的交易記錄。在 [物件總管] 中，展開 [資料庫]，以滑鼠右鍵按一下 [MyDB1]，指向 [工作]，然後按一下 [備份]。

1. 在 [備份類型] 中，選取 [交易記錄]。讓**目的地**的檔案路徑設定保持為您稍早指定的路徑，並按一下 [確定]。備份作業完成後，再次按一下 [確定]。

1. 接下來，將完整備份和交易記錄備份還原至 **ContosoSQL2**。啟動 **ContosoSQL2** 的 RDP 檔案，並以 **CORP\\Install** 的身分登入。讓 **ContosoSQL1** 的遠端桌面工作階段保持開啟。

1. 透過 [開始] 功能表，啟動 **SQL Server Management Studio**，然後按一下 [連接]，連接至預設的 SQL Server 執行個體。

1. 在 [物件總管] 中，以滑鼠右鍵按一下 [資料庫]，再按一下 [還原資料庫]。

1. 在 [來源] 區段中，選取 [裝置]，然後按一下 […] 按鈕。

1. 在 [**選取備份裝置**] 中按一下 [**新增**]。

1. 在 [備份檔案位置] 中，鍵入 \\ContosoSQL1\\backup，按一下 [重新整理]，選取 [MyDB1.bak]，按一下 [確定]，然後再按一次 [確定]。現在 [還原] 窗格的備份集，應會顯示可以在完整備份和記錄備份。

1. 移至 [選項] 頁面中，在還原狀態中選取 [使用 NORECOVERY 還原]，然後按一下 [確定]，以還原資料庫。還原作業完成後，按一下 [確定]。

### 建立可用性群組：

1. 回到 **ContosoSQL1** 的遠端桌面工作階段。如下方所示，在 SSMS 的 [物件總管] 中，以滑鼠右鍵按一下 [AlwaysOn 高可用性]，再按一下 [新增可用性群組精靈]。

	![啟動新增可用性群組精靈](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665523.gif)

1. 在 [簡介] 頁面上，按 [下一步]。在 [指定可用性群組名稱] 頁面，於 [可用性群組名稱] 中輸入 [AG1]，然後再次按 [下一步]。

	![新增 AG 精靈：指定 AG 名稱](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665524.gif)

1. 在 [選取資料庫] 頁面中，選取 [MyDB1]，然後按 [下一步]。由於您已經為預定的主要複本建立了至少一份完整備份，所以現在這些資料庫已符合建立可用性群組的先決條件。

	![新增 AG 精靈：選取資料庫](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665525.gif)

1. 在 [指定複本] 頁面中，按一下 [新增複本]。

	![新增 AG 精靈：指定複本](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665526.gif)

1. 隨即跳出 [連接至伺服器] 對話方塊。在 [伺服器名稱] 中輸入 **ContosoSQL2**，然後按一下 [連接]。

	![新增 AG 精靈：連接至伺服器](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665527.gif)

1. 回到 [指定複本] 頁面，現在 [可用複本] 中應會列出 **ContosoSQL2**。如下方所示，設定複本。完成後，請按 [下一步]。

	![新增 AG 精靈：指定複本 (完成)](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665528.gif)

1. 在 [**選取初始資料同步處理**] 頁面中，按一下 [**僅聯結**]，然後按一下 [**下一步**]。您在建立 **ContosoSQL1** 的完整備份和交易備份，並將備份還原至 **ContosoSQL2** 時，便已手動執行資料同步處理。您可以改為選擇不在資料庫上執行備份與還原作業，並選取 [完整] 讓新可用性群組精靈為您執行資料同步處理。不過，不建議針對某些企業中的超大型資料庫採用這種同步處理方式。

	![新增 AG 精靈：選取初始資料同步處理](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665529.gif)

1. 在 [驗證] 頁面中按 [下一步]。此頁面的外觀類似於下圖。由於您尚未設定可用性群組接聽程式，所以出現了關於接聽程式組態的警告。您可以忽略此警告，因為本教學課程不會示範設定接聽程式的步驟。完成此教學課程之後，若要設定接聽程式，請參閱[在 Azure 中為 AlwaysOn 可用性群組設定 ILB 接聽程式](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)。

	![新增 AG 精靈：驗證](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665530.gif)

1. 在 [摘要] 頁面中，按一下 [完成]，然後等候精靈設定新的可用性群組。在 [進度] 頁面中，按一下 [詳細資料] 可檢視詳細的進度。精靈完成後，如下方所示，檢查 [結果] 頁面，確認是否已成功建立可用性群組，然後按一下 [關閉] 以結束精靈

	![新增 AG 精靈：結果](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665531.gif)

1. 在 [物件總管] 中，展開 [AlwaysOn 高可用性]，再展開 [可用性群組]。新的可用性群組應會顯示在此容器中。以滑鼠右鍵按一下 [AG1 (主要)]，再按一下 [顯示儀表板]。

	![顯示 AG 儀表板](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665532.gif)

1. [AlwaysOn 儀表板] 的外觀應類似於下圖。當中會顯示複本、每個複本的容錯移轉模式和同步處理狀態。

	![AG 儀表板](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665533.gif)

1. 回到 [伺服器管理員]，選取 [工具]，然後啟動 [容錯移轉叢集管理員]。

1. 展開 [Cluster1.corp.contoso.com]，然後展開 [服務和應用程式]。選取 [角色]。請注意，先前已建立了 **AG1** 的可用性群組角色。請注意，由於您未設定接聽程式，所以 AG1 不具備任何資料庫用戶端可用來連接至可用性群組的 IP 位址。直接連接至主要節點，可進行讀寫作業，連接至次要節點，則可進行唯讀查詢。

	![容錯移轉叢集管理員中的 AG](./media/virtual-machines-sql-server-alwayson-availability-groups-gui/IC665534.gif)

>[AZURE.WARNING] 請勿嘗試透過容錯移轉叢集管理員，容錯移轉可用性群組。所有容錯移轉作業都應在 SSMS 的 **AlwaysOn 儀表板**中執行。如需詳細資訊，請參閱[容錯移轉叢集和 AlwaysOn 可用性群組 (SQL Server)](https://msdn.microsoft.com/library/ff929171.aspx)。

## 後續步驟
現在，您已透過在 Azure 中建立可用性群組的方式，成功實作 SQL Server AlwaysOn。若要為此可用性群組設定接聽程式，請參閱[在 Azure 中為 AlwaysOn 可用性群組設定 ILB 接聽程式](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)。

如需在 Azure 中使用 SQL Server 的其他資訊，請參閱 [Azure 虛擬機器上的 SQL Server](../articles/virtual-machines/virtual-machines-sql-server-infrastructure-services.md)。

<!---HONumber=AcomDC_0211_2016-->
