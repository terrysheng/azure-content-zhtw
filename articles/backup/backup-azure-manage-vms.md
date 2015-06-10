<properties
	pageTitle="Azure 備份 - 管理虛擬機器"
	description="了解如何管理 Azure 虛擬機器"
	services="backup"
	documentationCenter=""
	authors="jimpark"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="jimpark"/>

# 管理虛擬機器


## 管理受保護的虛擬機器

1. 若要檢視和管理虛擬機器的備份設定，按一下 [**受保護項目**] 索引標籤。

  - 按一下受保護項目的名稱以查看 [**備份詳細資料**] 索引標籤，上面會顯示上次備份的相關資訊。

        ![Virtual machine backup](./media/backup-azure-manage-vms/backup-vmdetails.png)

2. 若要檢視和管理虛擬機器的備份原則設定，按一下 [**原則**] 索引標籤。

  - [**備份原則**] 索引標籤將顯示現有的原則。您可以視需要修改。如果您需要建立新的原則依序按一下 **建立** 上 **原則** 頁面。請注意，如果您想要移除一個原則，該原則就不能與任何虛擬機器相關聯。

        ![Virtual machine policy](./media/backup-azure-manage-vms/backup-vmpolicy.png)

3. 您可以在 [**工作**] 頁面取得更多虛擬機器動作或狀態的相關資訊。按一下清單中的工作以取得詳細資訊，或為特定虛擬機器篩選工作。

    ![作業](./media/backup-azure-manage-vms/backup-job.png)

## 虛擬機器的隨選備份
設定保護之後，您可以執行隨選備份的虛擬。如果初始備份已暫止的虛擬機器，隨選備份會在 Azure 備份保存庫中建立虛擬機器的完整複本。如果第一個備份已完成，隨選備份只會傳送變更從先前的備份 Azure 備份保存庫。

若要進行隨選備份的虛擬機器：

1. 瀏覽至 **受保護項目** 頁面，並選取 **Azure 虛擬機器** 為 **類型** (如果尚未選取)，然後按一下 **選取** ] 按鈕。

    ![VM 類型](./media/backup-azure-manage-vms/vm-type.png)

2. 選取您想要進行隨選備份，然後按一下虛擬機器 **立即備份** 在頁面底部的按鈕。

    ![立即備份](./media/backup-azure-manage-vms/backup-now.png)

    這會在所選的虛擬機器上建立備份作業。透過這項作業所建立的復原點的保留範圍將會與虛擬機器相關聯的原則中指定相同的。

    ![建立備份作業](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]若要檢視與虛擬機器相關聯的原則，向下鑽研到虛擬機器中 **受保護項目** 頁面上，並移至備份原則] 索引標籤。

3. 一旦建立工作之後，您可以按一下 **檢視作業** 快顯通知列，以查看 [作業] 頁面中的對應作業中的按鈕。

    ![建立的備份作業](./media/backup-azure-manage-vms/created-job.png)

4. 順利完成的作業之後的復原點將會建立用來還原虛擬機器。這也會在 1 遞增的復原點的資料行值 **受保護項目** 頁面。

## 停止保護虛擬機器
您可以選擇停止虛擬機器具有下列選項的未來的備份：

- Azure 備份保存庫中的虛擬機器相關聯的備份資料的保留
- 刪除虛擬機器相關聯的備份資料

如果您已選取第一個選項，您可以使用備份資料還原虛擬機器。如需定價的這類虛擬機器的詳細資訊，請按一下 [這裡](http://azure.microsoft.com/pricing/details/backup/)。

若要停止虛擬機器的保護：

1. 瀏覽至 **受保護項目** 頁面，並選取 **Azure 虛擬機器** 做為篩選條件類型 (如果尚未選取) 並按一下 **選取** ] 按鈕。

    ![VM 類型](./media/backup-azure-manage-vms/vm-type.png)

2. 選取虛擬機器，然後按一下 **停止保護** 頁面的底部。

    ![停止保護](./media/backup-azure-manage-vms/stop-protection.png)

3. 根據預設，Azure Backup 不會刪除與虛擬機器相關聯的備份資料。

    ![確認停止保護](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    如果您想要刪除備份資料，請選取此核取方塊。

    ![核取方塊](./media/backup-azure-manage-vms/checkbox.png)

    請選取 [停止備份的原因。雖然這是選擇性的提供原因可以幫助 Azure Backup 來運作的意見反應，並設定優先權的客戶案例。

4. 按一下 **提交** 送出按鈕 **停止保護** 作業。按一下 **檢視作業** 若要查看對應中的工作 **作業** 頁面。

    ![停止保護](./media/backup-azure-manage-vms/stop-protect-success.png)

    如果您未選取 **刪除相關聯的備份資料** 選項期間 **停止保護** 精靈]，然後 post 作業完成時，保護狀態會變更為 **保護停止**。

    ![停止的保護](./media/backup-azure-manage-vms/protection-stopped-status.png)

    如果您已選取 **刪除相關聯的備份資料** 選項，虛擬機器將不會屬於 **受保護項目** 頁面。

## 重新保護虛擬機器
如果您未選取 **刪除關聯的備份資料** 在 **停止保護**, ，類似於備份已註冊的虛擬機器的步驟，您可以重新保護虛擬機器。受保護，此虛擬機器會有備份的資料保留在停止保護之前和之後所建立的復原點之後重新保護。

之後重新保護，虛擬機器的保護狀態會變更為 **保護** 如果有可用的復原點之前 **停止保護**。

  ![Reprotected 的 VM](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]當重新保護虛擬機器時，您可以選擇不同的原則，比用虛擬機器已受保護一開始的原則。

## 取消註冊虛擬機器

如果您想要從備份保存庫移除虛擬機器：

1. 按一下頁面底部的 [**取消註冊**] 按鈕。

    ![停用保護](./media/backup-azure-manage-vms/unregister-button.png)

    快顯通知會出現在畫面底部要求確認。按一下 [**是**] 以繼續。

    ![停用保護](./media/backup-azure-manage-vms/confirm-unregister.png)

## 刪除備份資料
您可以刪除與虛擬機器，可能是相關聯的備份資料：

- 停止保護工作期間
- 之後停止保護虛擬機器上完成工作

若要刪除備份資料的虛擬機器，也就是在 「 保護停止 」 狀態發佈成功完成 **停止備份** 作業：

1. 瀏覽至 **受保護項目** 頁面，並選取 **Azure 虛擬機器** 做為輸入，然後按一下 **選取** ] 按鈕。

    ![VM 類型](./media/backup-azure-manage-vms/vm-type.png)

2. 選取虛擬機器。虛擬機器會在 **保護停止** 狀態。

    ![停止保護](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. 按一下 **刪除** 在頁面底部的按鈕。

    ![刪除備份](./media/backup-azure-manage-vms/delete-backup.png)

4. 在 **刪除備份資料** 精靈] 中，選取刪除 (強烈建議) 的備份資料的理由，然後按一下 **提交**。

    ![刪除備份資料](./media/backup-azure-manage-vms/delete-backup-data.png)

5. 這會建立要刪除選取的虛擬機器的備份資料的工作。按一下 **檢視作業** 若要查看作業] 頁面中的對應作業。

    ![成功刪除資料](./media/backup-azure-manage-vms/delete-data-success.png)

    一旦作業完成時，將從移除項目對應至虛擬機器 **受保護的項目** 頁面。


###儀表板

在 **儀表板** 頁面，您可以檢閱 Azure 虛擬機器、 儲存和過去 24 小時內與它們相關聯工作的相關資訊。您可以檢視備份狀態和任何相關聯的備份錯誤。

  ![儀表板](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

<!---HONumber=GIT-SubDir--> 