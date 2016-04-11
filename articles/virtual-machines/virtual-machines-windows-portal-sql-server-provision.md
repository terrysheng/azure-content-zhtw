<properties
	pageTitle="在 Azure 入口網站中佈建 SQL Server 虛擬機器 | Microsoft Azure"
	description="在 Azure Resource Manager 模式下建立 SQL Server 虛擬機器。本教學課程主要是透過使用者介面作業，而非編寫指令碼。"
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="MikeRayMSFT"
    editor=""
	manager="jeffreyg"
	tags="azure-resource-manager" />


<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="03/24/2016"
	ms.author="mikeray" />

# 在 Azure 入口網站中佈建 SQL Server 虛擬機器

## 概觀

這個端對端教學課程將示範如何使用「Azure 資源管理員」模型在入口網站中佈建 Azure 虛擬機器，並從 Azure 資源庫中的範本設定 SQL Server。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。

Azure 虛擬機器 (VM) 資源庫涵蓋數個包含 Microsoft SQL Server 的映像。您可以從資源庫中選取其中一個 VM 映像，然後按幾下，即可將 VM 佈建到您的 Azure 環境。

在本教學課程中，您將：

- [連線到 Azure 入口網站並使用資源管理員部署模型從資源庫佈建 SQL VM 映像](#Provision)

- [設定虛擬機器和 SQL Server 設定](#ConfigureVM)

- [使用遠端桌面來開啟虛擬機器](#Open)

- [在另一部電腦上使用 SQL Server Management Studio 來連線到 SQL Server 執行個體](#Connect)

- [後續步驟](#Next)

本教學課程假設您已經有 Azure 帳戶。如果您沒有 Azure 帳戶，請造訪 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a id="Provision">使用資源管理員部署模型從資源庫佈建 SQL VM 映像

1. 使用您的帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 Azure 入口網站上，按一下 [+新增]。入口網站將會開啟 [新增] 刀鋒視窗。SQL Server VM 範本位於 Marketplace 的 [計算] 群組中。

1. 在 [新增] 刀鋒視窗中，按一下 [計算]。
1. 若要查看 [計算] 刀鋒視窗中的所有資源類型，請按一下 [查看全部]。<br/> ![Azure [計算] 刀鋒視窗](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade.png) <br/>
1. 在 [資料庫伺服器] 底下，按一下 [SQL Server] 來查看可用於 SQL Server 的所有範本。您可能必須向下捲動才能找到 [資料庫伺服器]。
1. 	每個範本皆識別一個 SQL Server 版本和一個作業系統。請從清單中選取這些映像的其中一個，以顯示包含其詳細資料的刀鋒視窗。
1.	詳細資料刀鋒視窗會提供此虛擬機器映像的描述，並可讓您選取部署模型。在 [選取部署模型] 底下，選取 [資源管理員]，然後按一下 [建立]。<br/> ![Azure [計算] 刀鋒視窗](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png) <br/>

## <a id="ConfigureVM"> 設定 VM
Azure 入口網站中有五個用來設定 SQL Server 虛擬機器的刀鋒視窗。

1.	設定基本設定
1.	選擇虛擬機器大小
1.	設定虛擬機器設定
1.	設定 SQL Server
1.	檢閱摘要

## 1\.設定基本設定
在 [建立虛擬機器] 刀鋒視窗的 [基本資料] 底下，提供下列資訊：

* 唯一的 [虛擬機器名稱]。
* [使用者名稱] 方塊中，該機器之本機系統管理員帳戶的唯一使用者名稱。這個帳戶也將會是 SQL Server 系統管理員 (sysadmin) 固定伺服器角色的成員。
* 在 [密碼] 方塊中，輸入強式密碼。
* 如果您有多個訂用帳戶，請確認訂用帳戶是否適用於您即將建置的 VM。
* 在 [資源群組] 方塊中，輸入資源群組的名稱。或者，若要使用現有的資源群組，請按一下 [選取現有項目]。資源群組是 Azure 中相關服務的集合。如需有關資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../resource-group-overview.md)。確認 [位置] 符合您的需求。
* 按一下 [確定] 來儲存變更。<br/>

>![SQL ARM 基本概念](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-basic.png) <br/>

## 2\.選擇虛擬機器大小
在 [建立虛擬機器] 刀鋒視窗的 [大小] 底下，選擇虛擬機器大小。Azure 入口網站將會顯示建議的大小。如需有關虛擬機器大小的詳細資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。大小是根據您選取的範本。大小可預估執行 VM 的每月成本。選取伺服器的 VM 大小。如需有關 SQL Server VM 大小的考量，請參閱 [Azure 虛擬機器中的 SQL Server 效能最佳作法](virtual-machines-windows-classic-sql-perf.md)。

## 3\.設定虛擬機器設定
在 [建立虛擬機器] 刀鋒視窗的 [設定] 底下，設定虛擬機器的 Azure 儲存體、網路功能及監視功能。

- 在 [儲存體] 底下，指定磁碟類型。針對生產環境工作負載，建議使用進階儲存體。

>[AZURE.NOTE] 預設會啟用進階儲存體。這會自動將您機器的大小調整成支援「進階儲存體」的大小。如果您停用「進階儲存體」，則會使用您先前選取的機器大小。

- 在 [儲存體帳戶] 底下，您可以接受自動佈建的儲存體帳戶名稱，或是按一下 [儲存體帳戶] 以選擇現有的帳路並設定儲存體帳戶類型。Azure 預設會建立具有本地備援儲存體的新儲存體帳戶。

- 在 [網路] 底下，您可以接受自動填入的功能值，或按一下每個功能以設定 [虛擬網路]、[子網路]、[公用 IP 位址] 及 [網路安全性群組]。Azure 預設會自動設定這些值。

- Azure 預設會使用為 VM 指定的相同儲存體帳戶來啟用 [監視]。您可以在這裡變更這些設定。

- 在 [可用性設定組] 底下，指定可用性設定組。基於本教學課程的目的，您可以選取 [無]。如果您打算設定「SQL AlwaysOn 可用性群組」，請設定可用性以避免重新建立虛擬機器。如需詳細資訊，請參閱[管理虛擬機器的可用性](virtual-machines-windows-manage-availability.md)。

## 4\.設定 SQL Server
在 [建立虛擬機器] 刀鋒視窗的 [設定 SQL Server] 底下，設定 SQL Server 的特定設定和最佳化。您可以設定的 SQL Server 設定包括：

- 連線能力
- 驗證
- 儲存體最佳化
- 修補
- 備份
- 金鑰保存庫整合

### 連線能力
在 [SQL 連線] 底下，指定 [公用 (網際網路)] 以允許從網際網路上的電腦或服務連線到 SQL Server。在已選取此選項的情況下，Azure 會自動設定防火牆和網路安全性群組以允許連接埠 1433 上的流量。<br/>![SQL ARM 連線](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png) <br/>

為了透過網際網路連線到 SQL Server，您將需要一併啟用「SQL Server 驗證」。

>[AZURE.NOTE]為了安全起見，請使用「網路安全性群組」來限制來源連接埠。如需詳細資訊，請參閱[什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)。

如果您偏好不要自動啟用透過網際網路連線到 Database Engine 的功能，請選擇下列其中一個選項：
- [本機 (僅限在 VM 內)] 只允許從 VM 內連接到 SQL Server。
- [私人 (在虛擬網路內)] 允許從相同虛擬網路中的電腦或服務連接到 SQL Server。


[連接埠] 預設為 1433。您可以指定其他連接埠號碼。如需詳細資訊，請參閱[連接到 SQL Server 虛擬機器 (資源管理員) | Microsoft Azure](virtual-machines-windows-sql-connect.md)。



### 驗證
如果您需要「SQL Server 驗證」，請按一下 [SQL 驗證] 底下的 [啟用]。

<br/>![SQL ARM 驗證](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png) <br/>


如果您啟用 [SQL Server 驗證]，請指定 [登入名稱] 和 [密碼]。這個使用者名稱將會是「SQL Server 驗證」登入，以及系統管理員 (sysadmin) 固定伺服器角色的成員。如需驗證模式的詳細資訊，請參閱[選擇驗證模式](http://msdn.microsoft.com/library/ms144284.aspx)。SQL Server 預設不會啟用「SQL Server 驗證」。在此情況下，虛擬機器上的本機系統管理員可以連線到 SQL Server 執行個體。

>[AZURE.NOTE] 如果您打算透過網際網路存取 SQL Server (也就是 [公用] 連線選項)，您應該在這裡啟用 SQL 驗證。對 SQL Server 進行公用存取需要使用「SQL 驗證」。

### 儲存體最佳化
按一下 [儲存體組態] 以指定儲存體需求。您可以用每秒輸入/輸出作業數 (IOPs)、輸送量 (單位為 MB/s) 及總儲存體大小來指定需求。請使用滑動標尺來設定這些項目。入口網站會自動根據這些需求計算磁碟數目。

Azure 預設會針對 5000 IOPs、200 MBs 及 1 TB 的儲存體空間進行最佳化。您可以根據工作負載變更這些儲存體設定。在 [儲存體最佳化] 底下，選取下列其中之一

- [一般] 是預設設定，支援大多數工作負載。
- [交易式處理] 可將儲存體最佳化來處理傳統資料庫 OLTP 工作負載。
- [資料倉儲] 可將儲存體最佳化來處理分析和報告工作負載。

下列影像顯示 [儲存體組態] 刀鋒視窗。<br/>![SQL ARM 儲存體](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png) <br/>

>[AZURE.NOTE] 儲存體組態限制取決於虛擬機器大小。如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。

### 修補
預設會啟用 [SQL 自動修補]。自動修補可讓 Azure 自動修補 SQL Server 和作業系統。請為維護期間指定一週當中的某一天、時間及持續時間。Azure 將會在維護期間執行修補。維護期間排程會使用 VM 地區設定做為時間。如果您不想要讓 Azure 自動修補 SQL Server 和作業系統，請按一下 [停用]。

<br/>![SQL ARM 修補](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png) <br/>

如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server 自動修補](virtual-machines-windows-classic-ps-sql-patch.md)。

### 備份
在 [SQL 自動備份] 底下，可以為所有資料庫啟用自動資料庫備份。當您啟用 SQL 自動備份時，您可以設定下列各項：

- 備份保留期限天數
- 要使用哪個儲存體帳戶來進行備份
- 是否要加密備份。若要加密備份，請按一下 [啟用]。如果將自動備份加密，請指定密碼。Azure 會建立憑證來加密備份，並使用指定的密碼來保護該憑證。

<br/>![SQL ARM 備份](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup.png) <br/>

 如需詳細資訊，請參閱 [Azure 虛擬機器中 SQL Server 的自動化備份](virtual-machines-windows-classic-ps-sql-backup.md)。

### 金鑰保存庫整合
若要在 Azure 中儲存用於加密的安全性密碼，請按一下 [Azure 金鑰保存庫整合]，然後按一下 [啟用]。

<br/>![SQL ARM Azure 金鑰保存庫整合](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png) <br/>

下表列出設定「Azure 金鑰保存庫整合」時所需的參數。

|參數|描述|範例|
|----------|----------|-------|
|**金鑰保存庫 URL** | 金鑰保存庫的位置。|https://contosokeyvault.vault.azure.net/ |
|**AKV 主體名稱** |Azure Active Directory 服務主體名稱。這也稱為「用戶端識別碼」。 |fde2b411-33d5-4e11-af04eb07b669ccf2|
| **AKV 主體密碼**|「AKV 整合」會在 SQL Server 內建立認證，以便讓 VM 能夠存取金鑰保存庫。選擇此認證的名稱。 | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=|
|**認證名稱**|選擇一個名稱來識別此認證。| mycred1|

如需詳細資訊，請參閱[在 Azure VM 上設定 SQL Server 的 Azure 金鑰保存庫整合](virtual-machines-windows-classic-ps-sql-keyvault.md)。

## 5\.檢閱摘要
檢閱摘要，然後按一下 [確定] 來建立為此 VM 指定的 SQL Server、資源群組及資源。您可以從 Azure 入口網站監視部署。畫面頂端的 [通知] 按鈕會顯示基本的部署狀態。

##<a id="Open">使用遠端桌面開啟虛擬機器並完成設定
請依照下列步驟使用「遠端桌面」來開啟虛擬機器：

1.	建置 Azure VM 之後，VM 的圖示將會出現在 Azure 儀表板上。按一下該圖示即可查看 VM 的相關資訊。
1.	在 VM 刀鋒視窗頂端，按一下 [連接]。瀏覽器將會為 VM 下載 .rdp 檔案。開啟 .rdp 檔案。
1.	「遠端桌面連線」將會通知您無法識別這個遠端連線的發行者，並詢問您是否仍然要進行連線。按一下 [連接]。
1.	在 [Windows 安全性] 對話方塊中，按一下 [使用其他帳戶]。針對 [使用者名稱]，輸入您設定 VM 時所指定的 <machine name><使用者名稱>。

連線到 SQL Server 虛擬機器之後，您可以啟動 SQL Server Management Studio，然後使用您的本機系統管理員認證透過「Windows 驗證」進行連線。這也可以讓您在佈建後，視需要變更防火牆設定或 SQL Server 組態設定。

##<a id="Connect">透過網際網路連接 SQL Server

如果您想要從網際網路連線到您的 SQL Server 資料庫引擎，將需要執行數個步驟，例如設定防火牆、啟用 SQL Server 驗證，以及設定您的網路安全性群組。您必須要有一個「網路安全性群組」規則來允許連接埠 1433 上的 TCP 流量。

如果您使用入口網站以資源管理員佈建 SQL Server 虛擬機器映像，則當您在 SQL 連線選項選取 [公用] 並啟用 SQL Server 驗證時，即已為您完成這些步驟。不過，還有一些剩餘的步驟要完成，才能透過網際網路存取您的 SQL Server 執行個體。

>[AZURE.NOTE] 如果在佈建時您沒有選取 [公用]，則還必須執行其他步驟，才能透過網際網路存取您的 SQL Server 執行個體。如需詳細資訊，請參閱[連接到 SQL Server 虛擬機器 (資源管理員) | Microsoft Azure](virtual-machines-windows-sql-connect.md)。

如果您只需要在本機或從相同的「虛擬網路」內存取您的虛擬機器，則不需要執行下列步驟。

> [AZURE.INCLUDE [在 VM 資源管理員中連線到 SQL Server](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

##<a id="Next">後續步驟
如需在 Azure 中使用 SQL Server 的其他資訊，請參閱 [Azure 虛擬機器上的 SQL Server](virtual-machines-windows-classic-sql-overview.md)。

<!---HONumber=AcomDC_0330_2016-->