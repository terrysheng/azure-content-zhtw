<properties
	pageTitle="準備環境以備份 Azure 虛擬機器 | Microsoft Azure"
	description="確認備份 Azure 虛擬機器的環境已準備就緒"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/23/2015"
	ms.author="trinadhk; aashishr; jimpark; markgal"/>

# 準備環境以備份 Azure 虛擬機器
備份 Azure 虛擬機器之前，您需要完成下面準備環境的必要條件。若您已經完成，便可以開始 [[備份您的 VM](backup-azure-vms.md)]，若還沒有，請繼續進行下列所有步驟，以確保環境準備就緒。

## 1\.備份保存庫
若要開始備份 Azure 虛擬機器，首先您必須建立備份保存庫。保存庫是一個實體，會儲存所有時間以來建立的所有備份和復原點。保存庫也包含備份虛擬機器時將套用的備份原則。

此影像顯示各種 Azure 備份實體之間的關係：![Azure 備份實體和其關聯性](./media/backup-azure-vms-prepare/vault-policy-vm.png)

若要建立備份保存庫：

1. 登入[管理入口網站](http://manage.windowsazure.com/)。

2. 按一下 [**新增**] > [**資料服務**] > [**復原服務**] > [**備份保存庫**] > [**快速建立**]。如果您有多個與組織帳戶相關聯的訂用帳戶，請選擇與備份保存庫相關聯的正確訂用帳戶。每個 Azure 訂用帳戶皆能擁有多重備份保存庫，用以組合管理受保護的虛擬機器。

3. 在 [**名稱**] 中，輸入保存庫的易記識別名稱。每個訂用帳戶皆需為唯一名稱。

4. 在 [**區域**] 中，選取保存庫的地理區域。保存庫必須與您想要保護的虛擬機器位於相同區域。如果您的虛擬機器在不同區域，每個區域皆需建立保存庫。儲存備份資料不需指定儲存體帳戶，備份保存庫和 Azure 備份服務會自動處理此動作。

    ![建立備份保存庫](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. 按一下 [建立保存庫]。要等備份保存庫建立好，可能需要一些時間。監視位於入口網站底部的狀態通知。

    ![建立保存庫快顯通知](./media/backup-azure-vms-prepare/creating-vault.png)

6. 將有一則訊息確認保存庫已成功建立，並且該保存庫會在「復原服務」頁面中列為 [使用中] 狀態。保存庫建立之後，請立即確認是否選擇了適當的儲存體備援選項。進一步了解[在備份保存庫中設定儲存體備援選項](backup-configure-vault.md#azure-backup---storage-redundancy-options)。

    ![備份保存庫的清單](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. 按一下備份保存庫，前往 [**快速啟動**] 頁面，上面將會顯示備份 Azure 虛擬機器的相關指示。

    ![虛擬機器的備分指示在 [儀表板] 頁面上](./media/backup-azure-vms-prepare/vmbackup-instructions.png)

## 2\.VM 代理程式
在您備份 Azure 虛擬機器之前，請先確定 Azure VM 代理程式已正確安裝在虛擬機器上。由於 VM 代理程式在虛擬機器建立時為選擇性元件，所以佈建虛擬機器之前，請先確定已選取 VM 代理程式的核取方塊。

深入了解 [VM 代理程式](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409)和[如何安裝](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)。

### 備份擴充功能
為了備份虛擬機器，Azure 備份服務會安裝 VM 代理程式的擴充功能。Azure 備份服務無需使用者介入，即可順暢地升級和修補備份擴充功能。

如果 VM 正在執行，表示已安裝備份擴充功能。執行中的 VM 也非常有可能讓您取得應用程式一致復原點。不過，即使 VM 已關閉且無法安裝擴充功能 (也就是離線 VM)，Azure 備份服務仍會繼續備份 VM。在此情況下，復原點將如前所述為*損毀一致*。

## 3\.網路連接
備份擴充功能必須網際網路連接才能正確運作，因為它需傳送命令至 Azure 儲存體端點 (HTTP URL) 以管理 VM 的快照集。若無網際網路連線，這些來自 VM 的 HTTP 要求會逾時，且備份作業將失敗。

## 限制

- 不支援備份以 Azure 資源管理員 (亦稱為 IaaS V2) 為基礎的虛擬機器。
- 不支援備份具有 16 個以上資料磁碟的虛擬機器備份。
- 不支援備份使用進階儲存體的虛擬機器。
- 不支援備份具有多個保留的 IP 的虛擬機器。
- 不支援備份具有保留的 IP 及未定義端點的虛擬機器。
- 不支援備份使用多個 NIC 的虛擬機器。
- 不支援備份正處於負載平衡組態 (內部和網際網路面向) 的虛擬機器。
- 不支援在還原期間取代現有的虛擬機器。先刪除現有的虛擬機器及任何相關聯的磁碟，然後從備份還原資料。
- 不支援跨區域備份和還原。
- Azure 的所有公用區域皆支援使用 Azure 備份服務備份虛擬機器。以下是支援區域的[檢查清單](http://azure.microsoft.com/regions/#services)。如果您尋找的區域目前不受支援，就不會於建立保存庫期間出現在下拉式清單中。
- 只有特定的作業系統版本才支援使用 Azure 備份服務備份虛擬機器：
  - **Linux**：由 Azure 背書的散發套件清單可以在[這裡](../virtual-machines-linux-endorsed-distributions.md)取得。只要 VM 代理程式可以在虛擬機器上使用，其他「攜帶您自己的 Linux」散發套件也應該可以運作。
  - **Windows Server**：不支援比 Windows Server 2008 R2 更舊的版本。
- 只能透過 PowerShell 支援還原屬於多 DC 組態的網域控制站 VM。進一步了解[還原多 DC 網域控制站](backup-azure-restore-vms.md#restoring-domain-controller-vms)

## 有疑問嗎？
若您有問題，或希望我們加入任何功能，請[傳送意見反應給我們](http://aka.ms/azurebackup_feedback)。

## 後續步驟

- [規劃 VM 備份基礎結構](backup-azure-vms-introduction.md)
- [備份虛擬機器](backup-azure-vms.md)
- [管理虛擬機器備份](backup-azure-manage-vms.md)

<!---HONumber=Nov15_HO1-->