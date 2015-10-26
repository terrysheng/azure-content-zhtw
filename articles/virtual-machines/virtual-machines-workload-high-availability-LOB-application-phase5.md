<properties 
	pageTitle="企業營運應用程式第 5 階段 | Microsoft Azure" 
	description="在 Azure 企業營運應用程式第 5 階段中，建立可用性群組並將應用程式資料庫加入其中。" 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# 企業營運應用程式工作負載第 5 階段：建立可用性群組並新增應用程式資料庫

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。


在 Azure 基礎結構服務部署高可用性企業營運應用程式的最終階段中，您將建立 AlwaysOn 可用性群組並新增應用程式的資料庫。

如需所有階段的相關資訊，請參閱[在 Azure 中部署高可用性的企業營運應用程式](virtual-machines-workload-high-availability-LOB-application-overview.md)。

## 建立可用性群組並新增資料庫

對於每個企業營運應用程式的資料庫：

1.	在主要 SQL Server 虛擬機器上製作資料庫的完整備份和交易記錄備份。
2.	在備份 SQL Server 虛擬機器上還原完整備份和記錄備份。

備份並還原資料庫之後，就可以將資料庫新增至可用性群組。SQL Server 只允許將已備份 (至少一次)，並已還原到另一部機器的資料庫新增至群組中。

### 共用備份資料夾

若要啟用備份和還原，您必須能夠從次要資料庫伺服器存取備份檔案 (.bak)。請使用下列程序：

1.	以 **[網域]\\sqladmin** 登入主要資料庫伺服器。 
2.	導覽至 F:\\ 磁碟。 
3.	以滑鼠右鍵按一下 [**備份**] 資料夾，然後按一下 [**共用對象**]，再按一下 [**特定人員**]。
4.	在 [檔案共用] 對話方塊中，輸入**[網域]\\sqlservice**，然後按一下 [新增]。
5.	按一下 **sqlservice** 帳戶名稱的 [**權限等級**] 資料行，然後按一下 [**讀取/寫入**]。 
6.	按一下 [**共用**]，然後按一下 [**完成**]。

在次要資料庫伺服器上執行上述程序，但在步驟 5 中，不需給予 F:\\Backup 資料夾 sqlservice 帳戶的**讀取**權限。

### 備份和還原資料庫

您必須為每一個欲新增至可用性群組的資料庫重複執行下列程序。

使用下列步驟備份資料庫。

1.	在主要資料庫伺服器的開始畫面中，輸入 **SQL Studio**，然後按一下 [SQL Server Management Studio]。
2.	按一下 [連接]。
3.	在左側窗格中展開 [**資料庫**] 節點。
4.	以滑鼠右鍵按一下資料庫以進行備份，指向 [**工作**]，然後按一下 [**備份**]。
5.	在 [**目的地**] 區段中按一下 [**移除**]，移除備份檔案的預設檔案路徑。
6.	按一下 [新增]。在 [檔案名稱] 中鍵入 **\\[machineName]\\backup[databaseName].bak**，其中的 **machineName** 是主要 SQL **Server 電腦**的名稱，而 **databaseName** 是資料庫名稱。按一下 [**確定**]，待備份完成的對話方塊出現後，再按一下 [**確定**]。
7.	在左側窗格中以滑鼠右鍵按一下 [databaseName]，指向 [工作]，然後按一下 [備份]。
8.	在 [**備份類型**] 中選取 [**交易紀錄**]，然後按兩下 [**確定**]。
9.	讓此遠端桌面工作階段保持開啟。

使用下列步驟還原資料庫。

1.	以 [domainName]\\sp\_farm\_db 登入次要資料庫伺服器。
2.	在 [開始] 畫面中輸入 **SQL Studio**，然後按一下 [**SQL Server Management Studio**]。
3.	按一下 [連接]。
4.	在左側窗格中，以滑鼠右鍵按一下 [**資料庫**]，然後按一下 [**還原資料庫**]。
5.	在 [**來源**] 區段中選取 [**裝置**]，然後按一下省略符號 (…) 按鈕。
6.	在 [**選取備份裝置**] 中按一下 [**新增**]。
7.	在 [備份檔案位置] 中輸入 **\\[機器名稱]\\backup**，按 **Enter**，選取 **[資料庫名稱].bak**，然後按兩次 [確定]。您現在可以在 [**要還原的備份組**] 區段中看到完整備份和記錄備份。
8.	在 [**選取頁面**] 中按一下 [**選項**]。在 [**還原選項**] 區段的 [**復原狀態**] 中，選取 [**使用 NORECOVERY 還原**]，然後按一下 [**確定**]。 
9.	出現提示時，按一下 [**確定**]。

### 建立可用性群組

準備好至少一個資料庫 (使用備份和還原方法) 之後，則可建立可用性群組。

1.	回到主要資料庫伺服器的遠端桌面工作階段。
2.	在 [**SQL Server Management Studio**] 的左側窗格中，以滑鼠右鍵按一下 [**AlwaysOn 高可用性**]，然後按一下 [**新增可用性群組精靈**]。
3.	在 [簡介] 頁面上，按 [下一步]。 
4.	在 [指定可用性群組名稱] 頁面的 [可用性群組名稱] 中鍵入您的可用性群組名稱 (例如：AG1)，然後按 [下一步]。
5.	在 [選取資料庫] 頁面中選取已備份的應用程式資料庫，然後按 [下一步]。這些資料庫符合可用性群組的必要條件，因為您為預定主要複本建立了至少一份完整備份。
6.	在 [指定複本] 頁面中按一下 [新增複本]。
7.	在 [連線到伺服器] 中輸入次要資料庫伺服器名稱，然後按一下 [連線]。 
8.	在 [指定複本] 頁面中，次要資料庫伺服器會在 [可用複本] 中列出。為兩個執行個體設定下列選項值： 

初始角色 | 選項 | 值 
--- | --- | ---
主要 | 自動容錯移轉 (最多 2) | 已選取
次要 | 自動容錯移轉 (最多 2) | 已選取
主要 | 同步認可 (最多 3) | 已選取
次要 | 同步認可 (最多 3) | 已選取
主要 | 可讀取次要 | 是
次要 | 可讀取次要 | 是
		
9.	按 [下一步]。
10.	在 [選取初始資料同步處理] 頁面中，按一下 [僅聯結]，然後按 [下一步]。資料同步處理以手動方式的進行，先在主要伺服器上建立完整備份和交易備份，然後在備份上還原。您可以改為選取 [**完整**] 讓「新增可用性群組精靈」為您執行資料同步處理。不過，某些企業中的大型資料庫不建議採用這種同步處理方式。
11.	在 [驗證] 頁面中按 [下一步]。畫面中會出現遺漏接聽程式組態的警告，因為尚未設定可用性群組接聽程式。 
12.	在 [摘要] 頁面中按一下 [完成]。精靈完成後，檢查 [**結果**] 頁面，確認已成功建立可用性群組。如果已確認，按一下 [**關閉**] 結束精靈。 
13.	在 [開始] 畫面中輸入 **Failover**，然後按一下 [**容錯移轉叢集管理員**]。在左側窗格中開啟您的叢集名稱，然後按一下 [**角色**]。畫面中應該會出現使用您可用性群組名稱的新角色。

您已經成功為您的應用程式資料庫設定 SQL Server AlwaysOn 可用性群組。

![](./media/virtual-machines-workload-high-availability-LOB-application-phase5/workload-lobapp-phase4.png)

您現在已準備好開始向內部網路使用者推出此新的應用程式。

## 設定 AlwaysOn 可用性群組的接聽程式。

您可選擇是否要為 AlwaysOn 可用性群組的接聽程式設定組態。若要進行這些步驟，請參閱[教學課程：AlwaysOn 可用性群組的接聽程式組態](https://msdn.microsoft.com/library/dn425027.aspx)。這些指示會逐步引導您僅建立單一接聽程式 (建議) 並使用內部負載平衡執行個體的靜態 IP 位址。

一旦接聽程式完成設定，就必須設定所有 Web 伺服器虛擬機器使用該接聽程式，而非叢集裡第一部 SQL Server 的名稱。請將 Web 伺服器虛擬機器設定為使用 SQL Alias，而非使用新的 DNS 名稱和對應至內部負載平衡執行個體的虛擬 IP 位址記錄。如需詳細資料與步驟，請參閱[在 SharePoint 中使用 SQL 別名](http://blogs.msdn.com/b/priyo/archive/2013/09/13/sql-alias-for-sharepoint.aspx)。

## 其他資源

[在 Azure 中部署高可用性的企業營運應用程式](virtual-machines-workload-high-availability-LOB-application-overview.md)

[企業營運應用程式架構藍圖](http://msdn.microsoft.com/dn630664)

[在混合式雲端中設定 Web 型 LOB 應用程式進行測試](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Azure 基礎結構服務實作指導方針](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure 基礎結構服務工作負載：SharePoint Server 2013 陣列](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Oct15_HO3-->