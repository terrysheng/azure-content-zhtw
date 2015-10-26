<properties 
	pageTitle="佈建 SQL Server 虛擬機器 | Microsoft Azure" 
	description="本教學課程會教您如何在 Azure 上建立及設定 SQL Server VM。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg" 
	editor="monicar"
	tags="azure-service-management"
	/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/26/2015" 
	ms.author="jroth"/>

# 在 Azure 中佈建 SQL Server 虛擬機器

> [AZURE.SELECTOR]
- [Portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)

## 概觀

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。

Azure 虛擬機器組件庫涵蓋數個包含 Microsoft SQL Server 的映像。您可以從組件庫選取任一個虛擬機器映像，只要點擊幾下便可以將虛擬機器佈建至 Azure 環境。

在本教學課程中，您將：

* [連接到 Azure 管理入口網站並從組件庫佈建虛擬機器](#Provision)
* [使用遠端桌面開啟虛擬機器並完成設定](#RemoteDesktop)
* [完成在另一部電腦上使用 SQL Server Management Studio 連接到虛擬機器的組態步驟](#SSMS)
* [後續步驟](#Optional)

>[AZURE.NOTE]本文說明如何使用現有的 Azure 管理入口網站佈建 SQL Server VM。不過，也可以在[新的入口網站](https://manage.windowsazure.com)建立及管理 SQL Server VM。新的入口網站有一些優點，例如預設使用進階儲存體，以及其他選項，例如自動修補、自動備份及 AlwaysOn 組態。後續內容會提供逐步指示。

##<a id="Provision">從資源庫佈建 SQL Server 虛擬機器</a>

1. 使用您的帳戶登入 [Azure 管理入口網站](http://manage.windowsazure.com)。如果您沒有 Azure 帳戶，請造訪 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

2. 在 Azure 管理入口網站中，依序按一下網頁左下角的 [+NEW]、[運算]、[虛擬機器] 和 [從組件庫]。

3. 在 [選擇映像] 頁面上，按一下 [SQL SERVER]。然後選取 SQL Server 映像。按一下頁面右下方的 [下一步] 箭頭。

	![選擇映像](./media/virtual-machines-provision-sql-server/choose-sql-vm.png)

若想了解在 Azure 上受支援之 SQL Server 映像的最新資訊，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-sql-server-infrastructure-services.md)。

>[AZURE.NOTE]如果您擁有以 SQL Server Evaluation Edition 平台映像建立的虛擬機器，該虛擬機器無法升級為組件庫內以分鐘計費之版本的映像。您可以在以下兩個選項中選擇其中一項：
>
> - 您可以使用資源庫中以分計費的 SQL Server 版本建立新的虛擬機器，然後依照[將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-migrate-onpremises-database) 中的步驟，將資料庫檔案移轉至這部新的虛擬機器
> - 您也可以依照[升級為不同的 SQL Server 版本](http://azure.microsoft.com/pricing/license-mobility/)中的步驟，在 [Azure 上透過軟體保證的授權流動性](https://msdn.microsoft.com/library/cc707783.aspx)合約的規範下，將現有的 SQL Server 評估版執行個體升級為其他版本的 SQL Server。如需如何購買授權版本之 SQL Server 的詳細資訊，請參閱[如何購買 SQL Server](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx)。

4. 在第一個 [**虛擬機器組態**] 頁面，請提供下列資訊：
	- **版本發行日期**。如果有多個映像，請選取最新映像。
	- 唯一的 [虛擬機器名稱]。
	- 在 [新的使用者名稱] 方塊中，輸入此機器之本機系統管理員帳戶的唯一使用者名稱。
	- 在 [新增密碼] 方塊中，輸入增強式密碼。 
	- 在 [確認密碼] 方塊中，重新輸入密碼。
	- 從 [**大小**] 下拉式清單中選取適當的大小。 

	![VM 組態](./media/virtual-machines-provision-sql-server/4VM-Config.png)

	>[AZURE.NOTE]虛擬機器的大小可在佈建期間指定：
 	>
	> - 生產環境工作負載使用的進階儲存體，建議至少為下列大小：SQL Server Enterprise Edition 為 **DS3**，SQL Server Standard Edition 為 **DS2**。如需詳細資訊，請參閱 [Azure 虛擬機器中 SQL Server 的效能最佳作法](virtual-machines-sql-server-performance-best-practices.md)。
	> - 所選大小會限制可設定的資料磁碟數目。如需可用之虛擬機器大小和可連接至虛擬機器之資料磁碟數目的最新資訊，請參閱[虛擬機器](virtual-machines-size-specs.md)。

5. 輸入 VM 組態的詳細資料後，請按一下右下角的下一步箭頭以繼續。

5. 在第二個 [虛擬機器組態] 頁面上，請設定網路、儲存體和可用性的資源：
	- 在 [雲端服務] 方塊中，選擇 [Create a new cloud service]。
	- 在 [Cloud Service DNS Name] 方塊中，提供選擇之 DNS 名稱的第一個部分，使其形成 **TESTNAME.cloudapp.net** 格式的名稱 
	- 如果您有多個訂用帳戶可供選擇，請選取 [訂用帳戶]。此選擇會決定可使用哪些 **儲存體帳戶**。
- 在 [REGION/AFFINITY GROUP/VIRTUAL NETWORK] 方塊中，選取代管這個虛擬映像的所在區域。
	- 在 [儲存體帳戶] 中，可以自動產生帳戶，或從清單中選取一個帳戶。變更 [訂用帳戶] 以查看更多帳戶。 
	- 在 [可用性設定組] 方塊中，選取 [(無)]。
	- 閱讀及接受法律條款。
	

6. 按 [下一步] 箭頭以繼續。


7. 按一下右下角的核取記號以繼續操作。

8. 等待 Azure 準備好您的虛擬機器。虛擬機器應該會依序經歷以下狀態變更：

	- **啟動中 (佈建中)**
	- **已停止**
	- **啟動中 (佈建中)**
	- **執行中 (佈建中)**
	- **執行中**
	

##<a id="RemoteDesktop">使用遠端桌面開啟 VM 以完成安裝</a>

1. 佈建完成時，請按一下虛擬機器的名稱以前往 [儀表板] 頁面。按一下頁面底部的 [**連接**]。

2. 按一下 [開啟] 按鈕。

	![按一下 [開啟] 按鈕](./media/virtual-machines-provision-sql-server/click-open-to-connect.png)

3. 在 [Windows 安全性] 對話方塊中，按一下 [使用其他帳戶]。

	![按一下 [使用其他帳戶]](./media/virtual-machines-provision-sql-server/credentials.png)

4. 以虛擬機器的名稱做為網域名稱，並在後面加上您的系統管理員名稱，格式如下：`machinename\username`。輸入您的密碼並連線到虛擬機器。

4. 當您第一次登入時，需要完成數個程序，包括設定桌面、Windows Update，以及完成 Windows 初始組態工作 (sysprep)。待 Windows sysprep 完成後，SQL Server 安裝程式會完成組態工作。當這些工作完成時，會導致幾分鐘的時間延遲。要等到 SQL Server 安裝程式完成時，`SELECT @@SERVERNAME` 才會傳回正確的名稱，而且 SQL Server Management Studio 才會出現在起始頁面上。

使用 Windows 遠端桌面連接到虛擬機器之後，虛擬機器的運作方式與任何其他電腦很像。請依照正常方法使用 SQL Server Management Studio (於虛擬機器上運作) 連接 SQL Server 的預設執行個體。

##<a id="SSMS">透過另一部電腦上的 SSMS 連線到 SQL Server VM 執行個體</a>

[AZURE.INCLUDE [連接至 VM 中的 SQL Server](../../includes/virtual-machines-sql-server-connection-steps.md)]

## <a id="cdea">從應用程式連線到資料庫引擎</a>

如果您可以使用 Management Studio 連接於 Azure 虛擬機器上運作的 SQL Server 執行個體，應該能使用與下文相似的連線字串進行連接。

	connectionString = "Server=tutorialtestVM.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

如需詳細資訊，請參閱[如何疑難排解 SQL Server Database Engine 連線](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) (英文)。

##<a id="Optional">後續步驟</a>

您已了解如何使用平台映像來建立及設定 Azure 虛擬機器上的 SQL Server。在許多情況下下，下一個步驟是將資料庫移轉到這個新的 SQL Server VM。如需資料庫移轉的指引，請參閱[將資料庫移轉至 Azure VM 上的 SQL Server](virtual-machines-migrate-onpremises-database.md)。

下列清單提供 Azure 虛擬機器中 SQL Server 的其他資源。

### 針對 Azure VM 上的 SQL Server 的建議參考資源：
- [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-sql-server-infrastructure-services.md)

- [連接 Azure 上的 SQL Server 虛擬機器](virtual-machines-sql-server-connectivity.md)

- [Azure 虛擬機器中的 SQL Server 效能最佳作法](virtual-machines-sql-server-performance-best-practices.md)

- [Azure 虛擬機器中的 SQL Server 安全注意事項](virtual-machines-sql-server-security-considerations.md)

### 高可用性和災害復原：
- [Azure 虛擬機器中的 SQL Server 高可用性和災害復原](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md)

- [Azure 虛擬機器中的 SQL Server 備份和還原](virtual-machines-sql-server-backup-and-restore.md)

### Azure 中的 SQL Server 工作負載：
- [Azure 虛擬機器中的 SQL Server Business Intelligence](virtual-machines-sql-server-business-intelligence.md)

### 白皮書：
- [了解 Azure 虛擬機器中的 Azure SQL Database 和 SQL Server](sql-database/data-management-azure-sql-database-and-sql-server-iaas.md)

- [Azure 虛擬機器中的 SQL Server 應用程式模式和開發策略](virtual-machines-sql-server-application-patterns-and-development-strategies.md)

<!---HONumber=Oct15_HO3-->