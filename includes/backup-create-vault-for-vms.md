## 為 VM 建立備份保存庫

備份保存庫是一個實體，儲存隨著時間建立的所有備份和復原點。備份保存庫也包含備份虛擬機器時將套用的備份原則。

此影像顯示各種「Azure 備份」實體之間的關係：![「Azure 備份」實體及其關係](./media/backup-create-vault-for-vms/vault-policy-vm.png)

若要建立備份保存庫：

1. 登入 [Azure 入口網站](http://manage.windowsazure.com/)。

2. 在 Azure 入口網站中，按一下 [新增] > [混合式整合] > [備份]。當您按一下 [備份] 時，您會自動切換至傳統入口網站 (顯示於 [注意] 之後)。

    ![Ibiza 入口網站](./media/backup-create-vault-for-vms/Ibiza-portal-backup01.png)

    >[AZURE.NOTE] 如果您上次是在傳統入口網站中使用訂用帳戶，則您的訂用帳戶可能會在傳統入口網站中開啟。在此情況下，若要建立備份保存庫，請按一下 [新增] > [資料服務] > [復原服務] > [備份保存庫] > [快速建立] (請參閱下圖)。

    ![建立備份保存庫](./media/backup-create-vault-for-vms/backup_vaultcreate.png)

3. 針對 [名稱]，輸入保存庫的易記識別名稱。必須是 Azure 訂用帳戶中唯一的名稱。輸入包含 2 到 50 個字元的名稱。該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。

4. 在 [**區域**] 中，選取保存庫的地理區域。保存庫必須與您想要保護的虛擬機器位於相同區域。如果您在多個區域中有虛擬機器，您必須在每個區域中建立備份保存庫。儲存備份資料時，不需要指定儲存體帳戶，備份保存庫和「Azure 備份」服務會自動處理此作業。

5. 在 [訂用帳戶] 中，選取您希望與備份保存庫相關聯的訂用帳戶。只有在您的組織帳戶與多個 Azure 訂用帳戶相關聯時，才會有多個選擇。

5. 按一下 [建立保存庫]。要等備份保存庫建立好，可能需要一些時間。監視位於入口網站底部的狀態通知。

    ![建立保存庫快顯通知](./media/backup-create-vault-for-vms/creating-vault.png)

6. 將有則訊息確認已成功建立保存庫。該保存庫將會在 [復原服務] 頁面中以 [使用中] 狀態列出。建立保存庫之後，請務必立即選擇適當的儲存體備援選項。進一步了解[在備份保存庫中設定儲存體備援選項](backup-configure-vault.md#azure-backup---storage-redundancy-options)。

    ![備份保存庫的清單](./media/backup-create-vault-for-vms/backup_vaultslist.png)

7. 按一下備份保存庫以前往 [快速入門] 頁面，此頁面會顯示備份 Azure 虛擬機器的相關指示。

    ![「儀表板」頁面上的虛擬機器備份指示](./media/backup-create-vault-for-vms/vmbackup-instructions.png)

<!---HONumber=AcomDC_0302_2016-------->