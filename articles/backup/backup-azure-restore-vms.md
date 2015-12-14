
<properties
	pageTitle="從備份還原虛擬機器 | Microsoft Azure"
	description="了解如何還原 Azure 虛擬機器"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/29/2015" ms.author="trinadhk"; "jimpark"/>

# 還原 Azure 中的虛擬機器
您可以使用還原動作，利用儲存在 Azure 備份保存庫的備份，將虛擬機器還原至新的 VM。

## 還原工作流程
### 1\.選擇要還原的項目

1. 瀏覽至 [**受保護項目**] 索引標籤，然後選取您想要還原至新 VM 的虛擬機器。

    ![受保護項目](./media/backup-azure-restore-vms/protected-items.png)

    [**受保護項目**] 頁面中的 [**復原點**] 欄位會告訴您虛擬機器的復原點數目。[**最新復原點**] 欄位會告訴您虛擬機器可還原的最近備份時間。

2. 按一下 [**還原**] 以開啟 [**還原項目**] 精靈。

    ![還原項目](./media/backup-azure-restore-vms/restore-item.png)

### 2\.挑選復原點

1. 在 [**選取復原點**] 畫面中，您可以從最新的復原點或較舊的復原點進行還原。精靈開啟時預設選取的選項是 [最新復原點]。

    ![選取復原點](./media/backup-azure-restore-vms/select-recovery-point.png)

2. 若要挑選較舊的時間，請選擇下拉式清單中的 [選取日期] 選項，然後透過按一下**行事曆圖示**後，在行事曆控制項中選取一個日期。在控制項中，所有擁有復原點的日期會以淺灰色陰影填滿，並可供使用者選取。

    ![選取日期](./media/backup-azure-restore-vms/select-date.png)

    一旦您按一下行事曆控制項中的日期時，該日可用的復原點將會顯示在下方的復原點資料表中。[**時間**] 欄位會指出擷取快照集的時間。[**類型**] 欄位會顯示復原點的[一致性](https://azure.microsoft.com/documentation/articles/backup-azure-vms/#consistency-of-recovery-points)。資料表標頭會在括號裡顯示該日可用的復原點數目。

    ![復原點](./media/backup-azure-restore-vms/recovery-points.png)

3. 從 [復原點] 資料表中選取復原點，然後按一下 [下一步] 箭號以移至下一個畫面。

### 3\.指定目的地位置

1. 在 [**選取還原執行個體**] 畫面中，指定要將虛擬機器還原至何處的詳細資料。

  - 指定虛擬機器名稱：在指定的雲端服務中，虛擬機器名稱應該是唯一的。如果您打算使用相同的名稱取代現有的 VM，先刪除現有的 VM 和資料磁碟，然後從 Azure 備份還原資料。
  - 選取 VM 的雲端服務：這是建立 VM 的必要步驟。您可以選擇使用現有的雲端服務，或建立新的雲端服務。

        Whatever cloud service name is picked should be globally unique. Typically, the cloud service name gets associated with a public-facing URL in the form of [cloudservice].cloudapp.net. Azure will not allow you to create a new cloud service if the name has already been used. If you choose to create select create a new cloud service, it will be given the same name as the virtual machine – in which case the VM name picked should be unique enough to be applied to the associated cloud service.

        We only display cloud services and virtual networks that are not associated with any affinity groups in the restore instance details. [Learn More](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

2. 選取 VM 的儲存體帳戶：這是建立 VM 的必要步驟。您可以選取與 Azure 備份保存庫位於相同區域的現有儲存體帳戶。我們不支援區域備援或進階儲存體類型的儲存體帳戶。

    如果沒有支援組態的儲存體帳戶，請在啟動還原作業之前建立一個支援組態的儲存體帳戶。

    ![選取虛擬網路](./media/backup-azure-restore-vms/restore-sa.png)

3. 選取虛擬網路：在建立 VM 時應該已經選取虛擬機器的虛擬網路 (VNET)。還原 UI 會顯示此訂用帳戶內所有可以使用的 VNET。為已還原的 VM 選取 VNET 並非必要步驟 – 即使不套用 VNET，您仍然可透過網際網路連接到已還原的虛擬機器。

    如果選取的雲端服務與虛擬網路相關聯，則您無法變更虛擬網路。

    ![選取虛擬網路](./media/backup-azure-restore-vms/restore-cs-vnet.png)

4. 選取子網路：如果 VNET 有子網路，預設選取的選項為第一個子網路。從下拉式清單選項中選擇您想要的子網路。若需子網路的詳細資訊，請移至[入口網站首頁](https://manage.windowsazure.com/)中的 [網路] 擴充功能，然後移至 [虛擬網路] 並在選取虛擬網路後，向下切入至 [設定] 以查看子網路的詳細資料。

    ![選取子網路](./media/backup-azure-restore-vms/select-subnet.png)

5. 按一下精靈中的 [提交] 圖示以提交詳細資料和建立還原工作。

## 追蹤還原作業
一旦您輸入還原精靈中的所有資訊和提交，Azure 備份將會嘗試建立一項工作以追蹤還原作業。

![正在建立還原工作](./media/backup-azure-restore-vms/create-restore-job.png)

如果成功建立工作，您會看到快顯通知指出工作已建立。您可以按一下 [檢視工作] 按鈕進入 [工作] 索引標籤取得更多詳細資料。

![已建立還原工作](./media/backup-azure-restore-vms/restore-job-created.png)

在還原作業完成時，系統會在 **工作**] 索引標籤中將其標示為已完成。

![已完成還原工作](./media/backup-azure-restore-vms/restore-job-complete.png)

還原虛擬機器後，您可能需要重新安裝原始虛擬機器 (VM) 上現有的擴充功能，並為 Azure 入口網站中的虛擬機器[修改端點](virtual-machines-set-up-endpoints)。

## 還原網域控制站 VM
備份網域控制站 (DC) 虛擬機器是 Azure 備份支援的案例。不過在還原程序期間必須小心。對於單一 DC 組態中的網域控制站 VM 與在多 DC 組態中的 VM 而言，還原體驗會大為不同。

### 單一 DC
VM 可以從 Azure 入口網站或使用 PowerShell 還原 (就像任何其他 VM)。

### 多 DC
當您擁有多 DC 環境時，網域控制站會有自己的方式將資料保持同步。在*沒有適當預防措施*的情況下還原較舊的備份點時，USN 回復程序可能會造成多 DC 環境中的破壞。復原這類 VM 的正確方法是以 DSRM 模式將其啟動。

由於Azure 中不存在 DSRM 模式，就會產生的挑戰。因此若要還原這類的 VM，您無法使用 Azure 入口網站。唯一受支援的還原機制是使用 PowerShell 進行以磁碟為基礎的還原。

>[AZURE.WARNING]對於多 DC 環境中的網域控制站 VM，請勿使用 Azure 入口網站還原！ 僅支援以 PowerShell 為基礎的還原

深入了解 [USN 回復問題](https://technet.microsoft.com/library/dd363553) 以及修正此問題的建議策略。

## 使用特殊網路組態還原 VM
Azure 備份支援備份虛擬機器的以下特殊網路組態。

- 負載平衡器底下的 VM (內部與外部)
- 具有多個保留的 IP 的 VM
- 具有多個 NIC 的 VM

這些組態可在還原組態時授權以下考量項目。

>[AZURE.TIP]請使用 PowerShell 型還原流程重新建立 VM 後續還原的特殊網路組態。

### 從 UI 還原：
從 UI 還原時，請**一律選擇新的雲端服務**。請注意，入口網站在還原流程中只接受強制參數，使用 UI 還原的 VM 將會失去它們擁有的特殊網路組態。也就是說，還原後的 VM 將會是一般的 VM，不具有負載平衡器組態、多個 NIC 或多個保留的 IP。

### 從 PowerShell 還原：
PowerShell 能夠只從備份還原 VM 磁碟，而不建立虛擬機器。在還原需要上述特殊網路組態的虛擬機器時，這很有用。

為了完整重新建立虛擬機器後續還原磁碟，請依照以下步驟執行：

1. 使用 [Azure 備份 PowerShell](../backup-azure-vms-automation.md#restore-an-azure-vm) 從備份保存庫還原磁碟

2. 使用 PowerShell Cmdlet 建立負載平衡器/多個 NIC/多個保留的 IP 所需的 VM 組態，並使用該組態建立具備想要之組態的 VM。
	- 使用[內部負載平衡器](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/)在雲端服務中建立 VM
	- 建立 VM 來連線至[網際網路對向負載平衡器](https://azure.microsoft.com/zh-TW/documentation/articles/load-balancer-internet-getstarted)
	- 建立具有[多個 NIC](https://azure.microsoft.com/documentation/articles/virtual-networks-multiple-nics) 的 VM
	- 建立具有[多個保留的 IP](https://azure.microsoft.com/documentation/articles/virtual-networks-reserved-public-ip/) 的 VM
  

## 後續步驟
- [錯誤疑難排解](backup-azure-vms-troubleshoot.md#restore)
- [管理虛擬機器](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_1203_2015-->