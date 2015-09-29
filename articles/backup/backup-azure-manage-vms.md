
<properties
	pageTitle="Azure 備份 - 管理虛擬機器 | Microsoft Azure"
	description="了解如何管理 Azure 虛擬機器"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/09/2015" ms.author="aashishr"; "jimpark"; "trinadhk"/>

# 管理虛擬機器

## 管理受保護的虛擬機器

管理受保護的虛擬機器：

1. 若要檢視和管理虛擬機器的備份設定，按一下 [**受保護項目**] 索引標籤。

2. 按一下受保護項目的名稱以查看 [**備份詳細資料**] 索引標籤，上面會顯示上次備份的相關資訊。

    ![虛擬機器備份](./media/backup-azure-manage-vms/backup-vmdetails.png)

3. 若要檢視和管理虛擬機器的備份原則設定，按一下 [**原則**] 索引標籤。

    ![虛擬機器原則](./media/backup-azure-manage-vms/manage-policy-settings.png)

    [**備份原則**] 索引標籤將顯示現有的原則。您可以視需要修改。如果您需要建立新的原則，按一下 [**原則**] 頁面中的 [**建立**]。請注意，如果您想要移除一個原則，該原則就不能與任何虛擬機器相關聯。

    ![虛擬機器原則](./media/backup-azure-manage-vms/backup-vmpolicy.png)

4. 您可以在 [**工作**] 頁面取得更多虛擬機器動作或狀態的相關資訊。按一下清單中的工作以取得詳細資訊，或為特定虛擬機器篩選工作。

    ![作業](./media/backup-azure-manage-vms/backup-job.png)

## 虛擬機器的隨選備份
設定保護後，您可以執行虛擬機器的隨選備份。如果虛擬機器的初始備份已暫止，則隨選備份會在 Azure 備份保存庫中建立虛擬機器的完整複本。如果已完成第一個備份，隨選備份只會將先前備份的變更傳送到 Azure 備份保存庫。

若要進行虛擬機器的隨選備份：

1. 瀏覽至 [受保護項目] 頁面，並選取 [Azure 虛擬機器] 做為 [類型] \(若尚未選取)，然後按一下 [選取] 按鈕。

    ![VM 類型](./media/backup-azure-manage-vms/vm-type.png)

2. 選取您要進行隨選備份的虛擬機器，然後按一下頁面底部的 [**立即備份**] 按鈕。

    ![立即備份](./media/backup-azure-manage-vms/backup-now.png)

    這會在所選的虛擬機器上建立備份作業。透過這項作業建立的復原點保留範圍，將會與在虛擬機器相關原則中指定的保留範圍相同。

    ![建立備份作業](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]若要檢視與虛擬機器相關聯的原則，請向下切入到 [**受保護項目**] 頁面中的虛擬機器，並移至 [備份原則] 索引標籤。

3. 建立作業之後，您可以按一下快顯通知列中的 [**檢視作業**]，以在 [作業] 頁面中查看對應的作業。

    ![建立的備份作業](./media/backup-azure-manage-vms/created-job.png)

4. 順利完成作業之後，將會建立可供您還原虛擬機器的復原點。這也會使 [**受保護項目**] 頁面中 的復原點資料行值遞增 1。

## 停止保護虛擬機器
您可以透過下列選項，選擇停止虛擬機器的未來備份：

- 保留 Azure 備份保存庫中與虛擬機器相關聯的備份資料
- 刪除與虛擬機器相關聯的備份資料

如果您選取保留與虛擬機器相關聯的備份資料，您可以使用備份資料還原虛擬機器。如需這類虛擬機器的定價詳細資訊，請按一下[這裡](http://azure.microsoft.com/pricing/details/backup/)。

若要停止虛擬機器的保護：

1. 瀏覽至 [受保護項目] 頁面，並選取 [Azure 虛擬機器] 做為篩選類型 (若尚未選取)，然後按一下 [選取] 按鈕。

    ![VM 類型](./media/backup-azure-manage-vms/vm-type.png)

2. 選取虛擬機器，然後按一下頁面底部的 [**停止保護**]。

    ![停止保護](./media/backup-azure-manage-vms/stop-protection.png)

3. 根據預設，Azure 備份不會刪除與虛擬機器相關聯的備份資料。

    ![確認停止保護](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    如果您要刪除備份資料，請選取此核取方塊。

    ![核取方塊](./media/backup-azure-manage-vms/checkbox.png)

    請選取停止備份的原因。雖然這是選擇性動作，但提供原因可幫助 Azure 備份處理意見反應，並設定客戶案例的優先順序。

4. 按一下 [**提交**] 按鈕以提交 [**停止保護**] 作業。按一下 **檢視作業**] 以在 [**作業**] 頁面中查看對應作業。

    ![停止保護](./media/backup-azure-manage-vms/stop-protect-success.png)

    如果您未在 [**停止保護**] 精靈中選取 [**刪除相關聯的備份資料**] 選項，然後在作業完成後，保護狀態會變更為 [**已停止保護**]。資料將會使用 Azure 備份保留，直到被明確刪除為止。您隨時都可藉由在 [受保護的項目] 頁面中選取虛擬機器，然後按一下 [刪除] 來刪除資料。

    ![已停止保護](./media/backup-azure-manage-vms/protection-stopped-status.png)

    若您已選取 [刪除相關聯的備份資料] 選項，則虛擬機器將不會出現在 [受保護項目] 頁面中。

## 重新保護虛擬機器
如果您未選取 [**停止保護**] 中的 [**刪除相關聯的備份資料**] 選項，您可以遵循類似於備份已註冊虛擬機器的步驟，重新保護虛擬機器。一旦受保護，此虛擬機器會在停止保護之前保留備份資料，而在重新保護之後建立復原點。

重新保護之後，如果有 [**停止保護**] 之前的復原點，則虛擬機器的保護狀態會變更為 [**受保護**]。

  ![重新保護的 VM](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]重新保護虛擬機器時，您可以選擇與最初用於保護虛擬機器不同的原則。

## 取消註冊虛擬機器

如果您想要從備份保存庫移除虛擬機器：

1. 按一下頁面底部的 [**取消註冊**] 按鈕。

    ![停用保護](./media/backup-azure-manage-vms/unregister-button.png)

    快顯通知會出現在畫面底部要求確認。按一下 [**是**] 以繼續。

    ![停用保護](./media/backup-azure-manage-vms/confirm-unregister.png)

## 刪除備份資料
您可以刪除與虛擬機器相關聯的備份資料：

- 在停止保護作業期間
- 在虛擬機器上完成停止保護作業之後

針對在順利完成 [停止備份] 作業後處於 [已停止保護] 狀態的虛擬機器，若要刪除其中的備份資料：

1. 瀏覽至 [受保護項目] 頁面，並選取 [Azure 虛擬機器] 做為*類型*，然後按一下 [選取] 按鈕。

    ![VM 類型](./media/backup-azure-manage-vms/vm-type.png)

2. 選取虛擬機器。虛擬機器會處於 [**已停止保護**] 狀態。

    ![已停止保護](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. 按一下頁面底部的 [刪除] 按鈕。

    ![刪除備份](./media/backup-azure-manage-vms/delete-backup.png)

4. 在 [刪除備份資料] 精靈中，選取刪除備份資料的原因 (強烈建議)，然後按一下 [提交]。

    ![刪除備份資料](./media/backup-azure-manage-vms/delete-backup-data.png)

5. 這會建立一項作業以刪除所選虛擬機器的備份資料。按一下 [檢視作業] 以在 [作業] 頁面中查看對應作業。

    ![成功刪除資料](./media/backup-azure-manage-vms/delete-data-success.png)

    完成此作業後，將從 [受保護項目] 頁面中移除虛擬機器的對應項目。

## 儀表板
在 [**儀表板**] 頁面中，您可以檢閱有關 Azure 虛擬機器、其儲存體和過去 24 小時內相關聯作業的資訊。您可以檢視備份狀態和任何相關聯的備份錯誤。

![儀表板](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

>[AZURE.NOTE]儀表板中的值會每隔 24 小時重新整理一次。

## 稽核作業
Azure 備份提供由客戶觸發之備份作業的「作業記錄檔」檢閱，可輕鬆查看備份保存庫上執行了哪些管理作業。作業記錄檔會啟用備份作業的絕佳事後剖析和稽核支援。

作業記錄檔中會記錄下列作業：

- 註冊
- 取消註冊
- 設定保護
- 備份 (同時透過 BackupNow 進行排程以及隨選備份)
- 還原
- 停止保護
- 刪除備份資料
- Add policy
- 刪除原則
- 更新原則
- 取消工作

檢視對應到備份保存庫的作業記錄檔：

1. 瀏覽至 Azure 入口網站中的 [管理服務]，然後按一下 [作業記錄檔] 索引標籤。

    ![作業記錄檔](./media/backup-azure-manage-vms/ops-logs.png)

2. 在篩選器中，選取 [備份] 做為*類型*，指定*服務名稱*中的備份保存庫名稱，然後按一下 [提交]。

    ![作業記錄檔篩選器](./media/backup-azure-manage-vms/ops-logs-filter.png)

3. 在作業記錄檔中選取任一作業，然後按一下 [詳細資料] 以查看對應至作業的詳細資料。

    ![作業記錄檔擷取詳細資料](./media/backup-azure-manage-vms/ops-logs-details.png)

    **詳細資料精靈**包含觸發的作業、工作識別碼、觸發作業所在的資源，以及作業的開始時間等相關資訊。

    ![Operation Details](./media/backup-azure-manage-vms/ops-logs-details-window.png)

## 警示通知
您可以在入口網站取得工作的自訂警示通知。這是藉由在作業記錄檔事件中定義以 PowerShell 為基礎的警示規則來達成。

Azure 資源模式中以事件為基礎的警示工作。藉由執行下列提高權限之命令模式的 cmdlet，切換至 Azure 資源模式：

```
PS C:\> Switch-AzureMode AzureResourceManager
```

若要定義自訂通知以警示備份失敗，範例命令看起來像：

```
PS C:\> Add-AlertRule -Operator GreaterThanOrEqual -Threshold 1 -ResourceId '/subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/BackupVault/trinadhVault' -EventName Backup  -EventSource Administrative -Level Error -OperationName 'Microsoft.Backup/backupVault/Backup' -ResourceProvider Microsoft.Backup -Status Failed  -SubStatus Failed -RuleType Event -Location eastus -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -Name Backup-Failed -Description 'Backup failed for one of the VMs in vault trinadhkVault' -CustomEmails 'contoso@microsoft.com' -SendToServiceOwners
```

**ResourceId**：您可從以上章節所述的 [作業記錄檔] 快顯視窗中取得。作業之詳細資料快顯視窗中的 ResourceUri 是要套用於此 cmdlet 的 ResourceId。

**EventName**：對於 IaaS VM 備份的警示，支援的值為 - Register,Unregister,ConfigureProtection,Backup,Restore,StopProtection,DeleteBackupData,CreateProtectionPolicy,DeleteProtectionPolicy,UpdateProtectionPolicy

**層級**：支援的值為 - 資訊、錯誤。對於失敗動作的警示請使用「錯誤」，對於成功工作的警示請使用「資訊」。

**OperationName**：這將會是 EventName 如上述所採用的 "Microsoft.Backup/backupvault/<EventName>" 格式。

**狀態**：支援的值為 - 已開始、成功和失敗。建議您保留「資訊」做為「成功」狀態的層級。

**子狀態**：與備份作業的狀態相同

**RuleType**：保持為*事件*，因為備份警示會以事件為根據。

**ResourceGroup**：觸發作業所在的資源 ResourceGroup。您可以從 ResourceId 值加以取得。在 ResourceId 值中，介於欄位 */resourceGroups/* 和 */providers/* 之間的值即為 ResourceGroup 的值。

**名稱**：警示規則的名稱。

**說明**：警示規則的說明。

**CustomEmails**：指定您要傳送警示通知的自訂電子郵件地址

**SendToServiceOwners**：此選項會將警示通知傳送給訂用帳戶的所有系統管理員和共同管理員。

範例警示郵件看起來像這樣：

範例標頭：

![警示標頭](./media/backup-azure-manage-vms/alert-header.png)

警示郵件的範例本文：

![警示本文](./media/backup-azure-manage-vms/alert-body.png)

### 警示的限制
以事件為基礎的警示受限於下列限制：

1. 在備份保存庫中的所有虛擬機器上觸發警示。您無法自訂它以取得備份保存庫中特定一組虛擬機器的警示。
2. 在下一個警示期間，如果沒有符合觸發事件的警示，則會自動解析警示。使用 Add-AlertRule Cmdlet 中的 *WindowSize* 參數，設定警示觸發持續時間。

## 後續步驟

- [還原 Azure VM](backup-azure-restore-vms.md)

<!----HONumber=Sept15_HO3-->