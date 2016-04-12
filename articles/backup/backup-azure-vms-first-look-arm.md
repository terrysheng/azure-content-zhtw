<properties
	pageTitle="使用 Azure 備份保護 ARM VM | Microsoft Azure"
	description="使用 Azure 備份服務保護 ARM VM。使用 ARM VM 的備份來保護您的資料。建立和註冊復原服務保存庫。在 Azure 中註冊 VM、建立原則和保護 VM。"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keyword="backups; vm backup"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="03/31/2016"
	ms.author="markgal; jimpark"/>


# 初步了解：將 ARM VM 備份到復原服務保存庫

> [AZURE.SELECTOR]
- [備份 ARM VM](backup-azure-vms-first-look-arm.md)
- [備份傳統模式 VM](backup-azure-vms-first-look.md)

本教學課程會帶領您完成一組步驟以建立復原服務保存庫和備份 Azure 虛擬機器 (VM)。本教學課程適用於復原服務保存庫，其可用來保護 IaaS v.2 VM 或以 Azure Resource Manager (ARM) 為基礎的 VM。

>[AZURE.NOTE] 本教學課程假設您的 Azure 訂用帳戶中已有 VM，且您已採取措施以允許備份服務存取 VM。Azure 有兩種用來建立和使用資源的部署模型：[Resource Manager 和傳統](../resource-manager-deployment-model.md)。本文適用於 Resource Manager VM 和以 ARM 為基礎的 VM。

概括而言，您會完成以下這些步驟。

1. 建立 VM 的復原服務保存庫。
2. 使用 Azure 入口網站選取案例、設定原則，以及識別要保護的項目。
3. 執行初始備份。



## 步驟 1 - 建立 VM 的復原服務保存庫

復原服務保存庫是一個實體，會儲存歷來建立的所有備份和復原點。復原服務保存庫也包含套用至受保護 VM 的備份原則。

>[AZURE.NOTE] 備份 VM 是本機程序。您無法將某個位置的 VM 備份到另一個位置的復原服務保存庫。因此，對於每個具有要備份之 VM 的 Azure 位置，該位置至少必須有一個復原服務保存庫。


若要建立復原服務保存庫：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 在 [中樞] 功能表上按一下 [瀏覽]，然後在資源清單中輸入**復原服務**。當您開始輸入時，清單將會根據您輸入的文字進行篩選。按一下 [復原服務保存庫]。

    ![建立復原服務保存庫的步驟 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    隨即會顯示 [復原服務保存庫] 清單。

3. 在 [復原服務保存庫] 功能表上，按一下 [新增]。

    ![建立復原服務保存庫的步驟 2](./media/backup-azure-vms-first-look-arm/rs-vault-menu.png)

    [復原服務保存庫] 刀鋒視窗隨即開啟，並提示您提供 [名稱]、[訂用帳戶]、[資源群組] 和 [位置]。

    ![建立復原服務保存庫的步驟 5](./media/backup-azure-vms-first-look-arm/rs-vault-attributes.png)

4. 在 [名稱] 中，輸入易記名稱來識別保存庫。必須是 Azure 訂用帳戶中唯一的名稱。輸入包含 2 到 50 個字元的名稱。該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。

5. 按一下 [訂用帳戶] 以查看可用的訂用帳戶清單。如果您不確定要使用哪個訂用帳戶，請使用預設 (或建議) 的訂用帳戶。只有在您的組織帳戶與多個 Azure 訂用帳戶相關聯時，才會有多個選擇。

6. 按一下 [資源群組] 以查看可用的資源群組清單，或按一下 [新增] 以建立新的資源群組。如需資源群組的完整資訊，請參閱[使用 Azure 入口網站來部署及管理 Azure 資源](../azure-portal/resource-group-portal.md)。

7. 按一下 [位置] 以選取保存庫的地理區域。保存庫**必須**與您想要保護的虛擬機器位於相同區域。

    >[AZURE.IMPORTANT] 如果您不確定 VM 的所在位置，請關閉保存庫建立對話方塊，並移至入口網站的虛擬機器清單。如果您在多個區域中有虛擬機器，您必須在每個區域中建立復原服務保存庫。請先在第一個位置建立保存庫，再進入下一個位置。儲存備份資料時，不需要指定儲存體帳戶，復原服務保存庫和「Azure 備份」服務會自動處理此作業。

8. 按一下 [建立]。要等復原服務保存庫建立好，可能需要一些時間。請監視入口網站右上方區域中的狀態通知。保存庫一旦建立好，就會在入口網站中開啟。

9. 在您的保存庫中，按一下 [所有設定] > [備份組態] 以檢視 [儲存體複寫類型]。選擇保存庫的儲存體複寫選項。

    ![備份保存庫的清單](./media/backup-azure-vms-first-look-arm/choose-storage-configuration.png)

    根據預設，保存庫具有異地備援儲存體。如果您使用 Azure 做為主要的備份儲存體端點，則建議您繼續使用異地備援儲存體。如果您使用 Azure 做為非主要的備份儲存體端點，則可以考慮選擇本地備援儲存體，以減少在 Azure 中儲存資料的成本。在此[概觀](../storage/storage-redundancy.md)中，深入了解[異地備援](../storage/storage-redundancy.md#geo-redundant-storage)和[本地備援](../storage/storage-redundancy.md#locally-redundant-storage)儲存體選項。

    選擇好保存庫的儲存體選項後，就可以開始建立 VM 與保存庫的關聯。若要開始關聯，請探索及註冊 Azure 虛擬機器。

## 步驟 2 - 選取案例設定原則並定義要保護的項目
在向保存庫註冊 VM 前，請先執行探索程序，以確保能夠識別任何加入至訂用帳戶的新虛擬機器。此程序會在 Azure 中查詢訂用帳戶中的虛擬機器清單，以及其他資訊，例如雲端服務名稱、區域等。

1. 如果您已開啟復原服務保存庫，請繼續步驟 2。如果您並未開啟復原服務保存庫，但位於 Azure 入口網站中，請在 [中樞] 功能表上按一下 [瀏覽]。

    - 在資源清單中輸入**復原服務**。
    - 當您開始輸入時，清單將會根據您輸入的文字進行篩選。當您看到 [復原服務保存庫] 時，請按一下它。

    ![建立復原服務保存庫的步驟 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>

    隨即會出現 [復原服務保存庫] 清單。

    - 在 [復原服務保存庫] 清單中選取保存庫。

    選取的保存庫儀表板隨即開啟。

    ![開啟 [保存庫] 刀鋒視窗](./media/backup-azure-vms-first-look-arm/vault-settings.png)

2. 在 [保存庫儀表板] 功能表中按一下 [備份] 以開啟 [備份] 刀鋒視窗。

    ![開啟 [備份] 刀鋒視窗](./media/backup-azure-vms-first-look-arm/backup-button.png)

    刀鋒視窗開啟時，備份服務會搜尋訂用帳戶中的任何新 VM。

    ![探索 VM](./media/backup-azure-vms-first-look-arm/discovering-new-vms.png)

3. 在 [備份] 刀鋒視窗中按一下 [案例] 以開啟 [案例] 刀鋒視窗。

    ![開啟 [案例] 刀鋒視窗](./media/backup-azure-vms-first-look-arm/select-backup-scenario-one.png)

4. 在 [案例] 刀鋒視窗上，於 [備份類型] 功能表上選取 [Azure 虛擬機器備份]，然後按一下 [確定]。

    ![開啟 [案例] 刀鋒視窗](./media/backup-azure-vms-first-look-arm/select-rs-backup-scenario-two.png)

    [案例] 刀鋒視窗隨即關閉，然後開啟 [備份原則] 刀鋒視窗。

5. 在 [備份] 刀鋒視窗中選取您要套用至保存庫的備份原則，然後按一下 [確定]。

    ![選取備份原則](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy.png)

    預設原則便會列在詳細資料中。如果您想要建立新原則，請選取 [建立新的]。如需定義備份原則的指示，請參閱[定義備份原則](backup-azure-vms-first-look-arm.md#defining-a-backup-policy)。一旦您按一下 [確定] 時，備份原則便會與保存庫建立關聯。接下來選擇要與保存庫建立關聯的 VM。

6. 選擇要與指定原則建立關聯的虛擬機器，然後按一下 [選取]。

    ![選取工作負載](./media/backup-azure-vms-first-look-arm/select-vms-to-backup.png)

    如果清單中沒有看到所需 VM，請按一下 [重新整理]。如果還是沒看到所需 VM，請確認它是否已存在於相同 Azure 位置中做為復原服務保存庫。

7. 現在您已定義保存庫的所有設定，接下來在 [備份] 刀鋒視窗中按一下頁面底部的 [啟用備份]。這會將原則部署到保存庫和 VM。

    ![啟用備份](./media/backup-azure-vms-first-look-arm/enable-backup-settings.png)


## 步驟 3 - 初始備份

在虛擬機器上部署好備份原則後，並不表示您已備份好資料。根據預設，第一個排定的備份 (如備份原則中所定義) 即為初始備份。在執行初始備份之前，[備份工作] 刀鋒視窗上的 [上次備份狀態] 會顯示為 [警告 (待執行初始備份)]。

![待備份](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

除非您的初始備份預計會馬上開始，否則建議您執行 [立即備份]。

若要執行 [立即備份]：

1. 在保存庫儀表板的 [備份] 圖格上，按一下 [Azure 虛擬機器] <br/> ![設定圖示](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    [備份項目] 刀鋒視窗隨即開啟。

2. 在 [備份項目] 刀鋒視窗中，以滑鼠右鍵按一下您要備份的保存庫，然後按一下 [立即備份]。

    ![設定圖示](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    備份作業便會觸發。<br/>

    ![備份作業已觸發](./media/backup-azure-vms-first-look-arm/backup-triggered.png)

3. 若要檢視初始備份是否已完成，請在保存庫儀表板的 [備份工作] 圖格上按一下 [Azure 虛擬機器]。

    ![備份工作圖格](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    [備份工作] 刀鋒視窗隨即開啟。

4. 在 [備份工作] 刀鋒視窗中，您可以看到所有工作的狀態。

    ![備份工作圖格](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

    >[AZURE.NOTE] 在備份工作進行時，Azure 備份服務會發出命令給每個虛擬機器中的備份擴充功能，以排清所有寫入並取得一致的快照。

    當備份作業完成時，狀態會是 [完成]。

## 定義備份原則

備份原則會定義由資料快照擷取時間和這些快照的保留時間長度所構成的矩陣。在定義 VM 的備份原則時，您可以「一天一次」地觸發備份作業。當您建立新原則時，該原則將會套用至保存庫。備份原則介面的外觀如下︰

![備份原則](./media/backup-azure-vms-first-look-arm/backup-policy-daily-raw.png)

若要建立原則︰

1. 在 [原則名稱] 中提供原則的名稱。

2. 資料快照可依 [每日] 或 [每週] 間隔來擷取。使用 [備份頻率] 下拉式功能表來選擇要 [每日] 或 [每週] 擷取資料快照。

    - 如果您選擇 [每日] 間隔，可使用醒目提示的控制項來選取要在一天當中的什麼時間擷取快照。若要變更小時，請取消選取小時，然後選取新的小時。

    ![每日備份原則](./media/backup-azure-vms-first-look-arm/backup-policy-daily.png) <br/>

    - 如果您選擇 [每週] 間隔，可使用醒目提示的控制項來選取要在星期幾的什麼時間擷取快照。在 [天] 功能表中選取某天或多天。在 [小時] 功能表中選取某個小時。若要變更小時，請取消選取所選的小時，然後選取新的小時。

    ![每週備份原則](./media/backup-azure-vms-first-look-arm/backup-policy-weekly.png)

3. 根據預設，會選取所有 [保留範圍] 選項。請取消勾選任何您不想使用的保留範圍限制。

    >[AZURE.NOTE] 在保護 VM 時，備份作業會每天執行一次。每個保留範圍的備份執行時間都相同。

    在對應的控制項中指定要使用的間隔。[每月] 和 [每年] 保留範圍可讓您根據每週或每日增量指定快照。

4. 設定原則的所有選項後，在刀鋒視窗底部按一下 [確定]。

    一旦完成復原服務保存庫設定，新的原則就會設定為套用到保存庫。返回[選取案例設定原則並定義要保護的項目](backup-azure-vms-first-look-arm.md#step-2---select-scenario-set-policy-and-define-items-to-protect)一節的步驟 6

## 在虛擬機器中安裝 VM 代理程式

如果需要便會提供此資訊。Azure VM 代理程式必須安裝在 Azure 虛擬機器上，備份擴充功能才能運作。不過，如果 VM 是建立自 Azure 資源庫，則 VM 代理程式已存在於虛擬機器上。從內部部署資料中心移轉的 VM 不會安裝 VM 代理程式。在這種情況下，必須安裝 VM 代理程式。如果您在備份 Azure VM 時遇到問題，請先確定已在虛擬機器上正確安裝 Azure VM 代理程式 (請參閱下表)。如果您建立自訂 VM，請先[確定已選取 [安裝 VM 代理程式] 核取方塊](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md)，再佈建虛擬機器。

深入了解 [VM 代理程式](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409)和[如何安裝](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md)。

下表提供適用於 Windows 和 Linux VM 之 VM 代理程式的其他資訊。

| **作業** | **Windows** | **Linux** |
| --- | --- | --- |
| 安裝 VM 代理程式 | <li>下載並安裝[代理程式 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。您需要有系統管理員權限，才能完成安裝。<li>[更新 VM 屬性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)以表示已安裝代理程式。 | <li>從 GitHub 安裝最新的 [Linux 代理程式](https://github.com/Azure/WALinuxAgent)。您需要有系統管理員權限，才能完成安裝。<li> [更新 VM 屬性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)以表示已安裝代理程式。 |
| 更新 VM 代理程式 | 更新 VM 代理程式與重新安裝 [VM 代理程式二進位檔](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)一樣簡單。<br>確定在更新 VM 代理程式時，沒有任何執行中的備份作業。 | 請遵循[更新 Linux VM 代理程式](../virtual-machines-linux-update-agent.md)上的指示。<br>確定在更新 VM 代理程式時，沒有任何執行中的備份作業。 |
| 驗證 VM 代理程式安裝 | <li>瀏覽至 Azure VM 中的 C:\\WindowsAzure\\Packages 資料夾。<li>您應該會發現有 WaAppAgent.exe 檔案。<li> 在該檔案上按一下滑鼠右鍵，移至 [屬性]，然後選取 [詳細資料] 索引標籤。[產品版本] 欄位應為 2.6.1198.718 或更高版本。 | N/A |


### 備份擴充功能

虛擬機器上安裝了 VM 代理程式後，Azure 備份服務就會在 VM 代理程式上安裝備份擴充功能。Azure 備份服務無需使用者介入，即可順暢地升級和修補備份擴充功能。

無論 VM 是否在執行，備份服務都會安裝備份擴充功能。執行中的 VM 提供了取得應用程式一致復原點的絕佳機會。不過，即使 VM 已關閉而無法安裝擴充功能，Azure 備份服務仍會繼續備份 VM。這稱為離線 VM。在此情況下，復原點將會是「當機時保持一致」。



## 疑難排解資訊
如果您在完成本文中的某些工作時遇到問題，請參閱[疑難排解指引](backup-azure-vms-troubleshoot.md)。


## 有疑問嗎？
如果您有問題，或希望我們加入任何功能，請[傳送意見反應給我們](http://aka.ms/azurebackup_feedback)。

<!---HONumber=AcomDC_0406_2016-->