<properties
	pageTitle="備份 Azure 虛擬機器 | Microsoft Azure"
	description="利用 Azure 虛擬機器備份的這些程序來探索、註冊及備份您的虛擬機器。"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="虛擬機器備份; 備份虛擬機器; 備份和災害復原; vm 備份"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/22/2016"
	ms.author="trinadhk; jimpark; markgal;"/>


# 備份 Azure 虛擬機器
本文提供如何備份現有 Azure 虛擬機器 (VM)，以根據您的公司的備份和災害復原原則保護 VM 的程序。

首先，您必須注意幾件事，才能備份 Azure 虛擬機器。如果您尚未這樣做，請先完成[先決條件](backup-azure-vms-prepare.md)為您的環境進行 VM 備份的準備工作，再繼續作業。

如需詳細資訊，請參閱[在 Azure 中規劃 VM 備份基礎結構](backup-azure-vms-introduction.md)和 [Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)的文件。

備份 Azure 虛擬機器需要三個主要步驟：

![備份 Azure IaaS VM 的三個步驟](./media/backup-azure-vms/3-steps-for-backup.png)

>[AZURE.NOTE] 備份虛擬機器是本機的程序。您無法將虛擬機器從一個區域備份到另一個區域中的備份保存庫。因此，對於每一個有 VM 需要備份的 Azure 區域，必須在該區域中至少建立一個備份保存庫。

## 步驟 1 - 探索 Azure 虛擬機器
探索程序一律為首要執行步驟，以確保能夠識別任何加入至訂用帳戶的新虛擬機器。此程序會在 Azure 中查詢訂用帳戶中的虛擬機器清單，以及其他資訊，例如雲端服務名稱、區域等。

1. 在 Azure 入口網站中，瀏覽至 [復原服務] 下的備份保存庫，然後按一下 [註冊的項目]。

2. 從下拉式選單中選取 [Azure 虛擬機器]。

    ![選取工作負載](./media/backup-azure-vms/discovery-select-workload.png)

3. 按一下頁面底部的 [**探索**]。![探索按鈕](./media/backup-azure-vms/discover-button-only.png)

    在列表顯示虛擬機器時，探索程序可能需花費幾分鐘的時間。畫面底部會有通知讓您知道程序正在執行中。

    ![探索 VM](./media/backup-azure-vms/discovering-vms.png)

    程序完成時，通知隨即變更。

    ![探索完成](./media/backup-azure-vms/discovery-complete.png)

##  步驟 2 - 註冊 Azure 虛擬機器
您必須註冊 Azure 虛擬機器，使其與 Azure 備份服務相關聯。這通常是一次性活動。

1. 在 Azure 入口網站中，瀏覽至 [復原服務] 下的備份保存庫，然後按一下 [註冊的項目]。

2. 從下拉式選單中選取 [Azure 虛擬機器]。

    ![選取工作負載](./media/backup-azure-vms/discovery-select-workload.png)

3. 按一下頁面底部的 [註冊]。![註冊按鈕](./media/backup-azure-vms/register-button-only.png)

4. 在 [註冊項目] 捷徑功能表中，選取您想要註冊的虛擬機器。如果有兩個以上同名的虛擬機器，請使用雲端服務加以區別。

    >[AZURE.TIP] 您可以同時註冊多個虛擬機器。

    系統會為您所選取的每個虛擬機器建立一個工作。

5. 按一下通知中的 [檢視工作]，以移至 [工作] 頁面。

    ![註冊作業](./media/backup-azure-vms/register-create-job.png)

    虛擬機器也會連同註冊作業的狀態，出現在已註冊的項目清單中。

    ![註冊狀態 1](./media/backup-azure-vms/register-status01.png)

    作業完成時，狀態會改變以反映已註冊的狀態。

    ![註冊狀態 2](./media/backup-azure-vms/register-status02.png)

## 步驟 3 - 保護 Azure 虛擬機器
現在，您可以設定虛擬機器的備份和保留原則。使用單一保護動作可以保護多個虛擬機器。

2015 年 5 月之後建立的 Azure 備份保存庫，會隨附內建於保存庫的預設原則。這項預設原則會隨附 30 天預設保留和每日一次的備份排程。

1. 在 Azure 入口網站中，瀏覽至 [復原服務] 下的備份保存庫，然後按一下 [註冊的項目]。
2. 從下拉式選單中選取 [Azure 虛擬機器]。

    ![在入口網站中選取工作負載](./media/backup-azure-vms/select-workload.png)

3. 按一下頁面底部的 [保護]。

    [保護項目] 精靈隨即出現。此精靈只會列出已註冊且不受保護的虛擬機器。您可以在此處選取要保護的虛擬機器。

    如果有兩個以上同名的虛擬機器，請使用雲端服務來區別虛擬機器。

    >[AZURE.TIP] 您可以同時保護多個虛擬機器。

    ![設定大規模保護](./media/backup-azure-vms/protect-at-scale.png)

4. 選擇 [備份排程]，以備份您所選取的虛擬機器。您可以從現有的一組原則中挑選，或定義新的原則。

    每一個備份原則可以有多個相關聯的虛擬機器。但無論何時，虛擬機器只能與一個原則相關聯。

    ![使用新的原則來保護](./media/backup-azure-vms/policy-schedule.png)

    >[AZURE.NOTE] 備份原則中包含排定備份的保留配置。如果您選取現有的備份原則，將無法在下一個步驟中修改保留選項。

5. 選擇要與備份相關聯的 [保留範圍]。

    ![使用彈性保留來保護](./media/backup-azure-vms/policy-retention.png)

    保留期原則會指定儲存備份的時間長度。您可以根據進行備份的時間指定不同的保留原則。例如，為了稽核目的，每一季結尾所進行的備份可能需要保留較長期間，而每日進行的備份 (可充當作業的復原點) 則只需要保留 90 天。

    ![搭配復原點備份虛擬機器](./media/backup-azure-vms/long-term-retention.png)

    在此範例影像中：

    - **每日的保留原則**：每日所進行的備份會儲存 30 天。
    - **每週的保留原則**：每個星期天所進行的備份會保留 104 週。
    - **每月的保留原則**：每月最後一個星期日所進行的備份會保留 120 個月。
    - **每年的保留原則**：每年一月第一個星期日所進行的備份會保留 99 年。

    建立的工作可設定保護原則，並將虛擬機器與您所選取的每個虛擬機器的該項原則相關聯。

6. 按一下 [工作]，然後選擇正確的篩選器來檢視 [設定保護] 工作的清單。

    ![設定保護工作](./media/backup-azure-vms/protect-configureprotection.png)

## 初始備份
虛擬機器受到原則保護之後，就會出現在 [受保護的項目] 索引標籤下，狀態為 [受保護 - (擱置中的初始備份)]。根據預設，第一個排定的備份是*初始備份*。

若要在設定保護之後立即觸發初始備份：

1. 按一下位於 [受保護的項目] 頁面底部的 [立即備份] 按鈕。

    Azure 備份服務會初始備份作業建立備份工作。

2. 按一下 [工作] 索引標籤來檢視工作清單。

    ![備份進行中](./media/backup-azure-vms/protect-inprogress.png)

>[AZURE.NOTE] 在備份工作進行時，Azure 備份服務會發出命令給每個虛擬機器中的備份擴充功能，以排清所有寫入並取得一致的快照。

初始備份完成後，[受保護的項目] 索引標籤中的虛擬機器狀態會顯示為 [受保護]。

![搭配復原點備份虛擬機器](./media/backup-azure-vms/protect-backedupvm.png)

## 檢視備份狀態和詳細資料
虛擬機器受到保護後，[**儀表板**] 頁面摘要中的虛擬機器計數也會遞增。[儀表板] 頁面也會顯示過去 24 小時內*成功*、*失敗*及仍在*進行中*的工作數目。按一下任何一個類別，即可在 [工作] 頁面中深入查看該類別。

![儀表板頁面中的備份狀態](./media/backup-azure-vms/dashboard-protectedvms.png)

儀表板中的值會每隔 24 小時重新整理一次。

## 錯誤疑難排解
如果您在備份虛擬機器時遇到問題，請參閱此[疑難排解](backup-azure-vms-troubleshoot.md)指引以取得說明。

## 後續步驟

- [管理和監視虛擬機器](backup-azure-manage-vms.md)
- [還原虛擬機器](backup-azure-restore-vms.md)

<!---HONumber=AcomDC_0128_2016-->