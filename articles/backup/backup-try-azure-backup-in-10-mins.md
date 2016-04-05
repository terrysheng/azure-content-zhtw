<properties
   pageTitle="了解如何將檔案和資料夾從 Windows 備份到 Azure | Microsoft Azure"
   description="了解如何透過建立保存庫、安裝備份代理程式，以及將檔案和資料夾備份到 Azure，以備份 Windows Server 資料。"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="如何備份; 如何備份"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="hero-article"
	 ms.date="03/28/2016"
	 ms.author="jimpark;"/>

# 先睹為快︰將檔案和資料夾從 Windows Server 或用戶端備份到 Azure

本文說明如何使用 Azure 備份將 Windows Server (或 Windows 用戶端) 檔案和資料夾備份至 Azure。如果您想要看看使用 Azure 備份有多麼簡單，請閱讀本文的內容。

備份檔案與資料夾只需執行下列步驟︰

![步驟 1](./media/backup-try-azure-backup-in-10-mins/step-1.png) 取得 Azure 訂用帳戶 (如有必要)。<br> ![步驟 2](./media/backup-try-azure-backup-in-10-mins/step-2.png) 建立備份保存庫並下載必要的元件。<br> ![步驟 3](./media/backup-try-azure-backup-in-10-mins/step-3.png) 安裝並註冊這些元件，準備好您的 Windows Server 或用戶端。<br> ![步驟 4](./media/backup-try-azure-backup-in-10-mins/step-4.png) 備份您的資料。


![如何使用 Azure 備份來備份 Windows 電腦](./media/backup-try-azure-backup-in-10-mins/windows-machine-backup-process.png)

## 步驟 1：建立 Azure 訂用帳戶

如果您沒有 Azure 訂用帳戶，可以建立[免費帳戶](https://azure.microsoft.com/free/)，以便存取任何 Azure 服務。

>[AZURE.NOTE] 如果您已經有 Azure 訂用帳戶，請略過此步驟。

## 步驟 2：建立備份保存庫並下載必要的元件

若要備份檔案與資料夾，您必須在要儲存這些資料的地理區域中建立備份保存庫。

1. 如果您尚未這麼做，請使用 Azure 訂用帳戶登入 [Azure 入口網站](https://portal.azure.com/)。

2. 按一下 [新增] > [混合式整合] > [備份]。

    ![開始準備備份檔案和資料夾](./media/backup-try-azure-backup-in-10-mins/second-blade-backup.png)

    a.在結果畫面上，針對 [名稱] 輸入易記名稱來識別備份保存庫。輸入包含 2 到 50 個字元的名稱。該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。

    b.針對 [區域] 選取備份保存庫的地理區域。透過選擇靠近您位置的地理區域，可以在備份至 Azure 時減少網路延遲。

    c.按一下 [建立保存庫]。

    ![建立保存庫](./media/backup-try-azure-backup-in-10-mins/demo-vault-name.png)

    對於狀態，您可以監控位於入口網站下方的通知。

    ![保存庫程序](./media/backup-try-azure-backup-in-10-mins/creatingvault1.png)

    備份保存庫建立好之後，就會在 [復原服務] 的資源中列為 [使用中]。

    ![保存庫狀態為使用中](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

3. 選取**儲存體備援**選項。

    選取儲存體備援選項的最佳時機，就在建立保存庫之後，以及任何電腦註冊至保存庫之前。一旦項目已註冊至保存庫，儲存體備援選項即遭到鎖定且無法修改。

    如果您要使用 Azure 做為主要的備份儲存體端點，請考慮選取 (預設的) [異地備援儲存體](../storage/storage-redundancy.md#geo-redundant-storage)選項。

    如果您要使用 Azure 做為第三個備份儲存體端點，請考慮選擇[本地備援儲存體](../storage/storage-redundancy.md#locally-redundant-storage)。此選項可降低在 Azure 中儲存資料的成本，於此同時所提供的持久性層級也會較低，但對於第三個複本來說仍可接受。

    a.按一下您剛才建立的保存庫。

    b.在 [快速啟動] 頁面上，選取 [設定]。

    ![設定保存庫](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c.選擇適當的儲存體備援選項。

    ![選擇儲存體備援選項](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    如果您選取 [本地備援]，請按一下 [儲存]，因為 [異地備援] 是預設選項。

    d.若要返回 [復原服務] 的資源清單，按一下左導覽窗格中的 [復原服務]。

    ![復原服務](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

    現在您必須以剛才建立的備份保存庫驗證您的 Windows 電腦。驗證是利用保存庫認證來達成。

4.  按一下您剛才建立的保存庫。

    ![選取新的保存庫](./media/backup-try-azure-backup-in-10-mins/demo-vault-active-full-screen.png)

5. 從 [快速啟動] 頁面，按一下 [下載保存庫認證]。

    ![下載保存庫認證](./media/backup-try-azure-backup-in-10-mins/downloadvc.png)

    入口網站會使用保存庫名稱和目前日期的組合來產生保存庫認證。

    >[AZURE.NOTE] 保存庫認證檔僅在註冊工作流程期間使用，並於 48 小時後過期。

6. 按一下 [儲存] 將保存庫認證下載至本機的 [下載] 資料夾。您也可以從 [儲存] 功能表選取 [另存新檔]，以指定保存庫認證的位置。

    請確保保存庫認證儲存在可從您的電腦存取的位置。如果檔案儲存在檔案共用/SMB，請檢查存取權限。

    >[AZURE.NOTE] 在程序的這個階段，您不需要開啟保存庫認證。

    接著，您必須下載備份代理程式。

7. 按一下左側導覽窗格中的 [復原服務]，然後按一下您要向伺服器註冊的備份保存庫。

    ![選取備份保存庫](./media/backup-try-azure-backup-in-10-mins/recovery-services-select-vault.png)

8. 在 [快速啟動] 頁面上，按一下 [適用於 Windows Server 或 System Center Data Protection Manager 或 Windows 用戶端的代理程式] > [儲存]。

    ![儲存備份代理程式](./media/backup-try-azure-backup-in-10-mins/agent.png)

此時，您已完成備份保存庫的建立並下載必要的元件。您現在會安裝備份代理程式。

## 步驟 3：安裝並註冊備份元件，準備好您的 Windows Server 或用戶端

1. 下載完成後，從儲存的位置按兩下 **MARSagentinstaller.exe** (或者按一下 [執行]，而不是上一個步驟中的儲存檔案)。

2. 選擇代理程式所需的 [安裝資料夾] 和 [快取資料夾]。

    您指定的快取位置必須至少包含等於備份資料大小 5% 的可用空間。

    按 [下一步]。

    ![安裝和快取資料夾](./media/backup-try-azure-backup-in-10-mins/recovery-services-agent-setup-wizard-1.png)

3. (選擇性) 若您使用 Proxy 伺服器連接至網際網路，請在 [Proxy 組態] 畫面上勾選 [使用自訂 Proxy 設定] 方塊，然後輸入 Proxy 伺服器詳細資料。

    若您使用已驗證的 Proxy，請輸入使用者名稱和密碼詳細資料。

    按 [下一步]。

    ![Proxy 組態](./media/backup-try-azure-backup-in-10-mins/proxy-configuration.png)

4. 按一下 [安裝]。

    Azure 備份代理程式會安裝 .NET Framework 4.5 和 Windows PowerShell (若尚未安裝) 來完成安裝。

5. 安裝代理程式之後，按一下 [繼續註冊] 以繼續工作流程。

    ![註冊](./media/backup-try-azure-backup-in-10-mins/register.png)

6. 在 [保存庫識別] 畫面中，瀏覽並選取先前下載的保存庫認證檔。請確定保存庫認證檔位於可透過設定應用程式存取的位置。

    按 [下一步]。

    ![識別保存庫](./media/backup-try-azure-backup-in-10-mins/vault-identification-with-creds.png)

7. 在 [加密設定] 畫面中，您可以產生複雜密碼或提供複雜密碼 (最少 16 個字元)。請記得將複雜密碼存放在安全的位置。

    > [AZURE.WARNING] 如果遺失或忘記複雜密碼，Microsoft 將無法協助您復原備份資料。加密複雜密碼為您所擁有，Microsoft 並無法看到使用者所使用的複雜密碼。請將檔案儲存在安全的位置，在復原作業期間需要該檔案。

    按一下 [完成]。

    ![加密設定](./media/backup-try-azure-backup-in-10-mins/encryption.png)

    [註冊伺服器精靈] 會向 Microsoft Azure 備份註冊伺服器。

8. 一旦設定**加密金鑰**，請讓 [啟動 Microsoft Azure 復原服務代理程式] 核取方塊保持已核取狀態，然後按一下 [關閉]。

    電腦即成功註冊至保存庫，且您已準備好設定和排程您的備份選項。

## 步驟 4︰備份您的資料

1. 在 [備份代理程式] \(如果您讓 [啟動 Microsoft Azure 復原服務代理程式] 核取方塊保持已核取狀態，則會自動開啟) 中，按一下 [排程備份]。

    ![Windows Server 備份排程](./media/backup-try-azure-backup-in-10-mins/snap-in-schedule-backup-closeup.png)

2. 在 [開始使用] 畫面上，按 [下一步]。

3. 在 [選取要備份的項目] 畫面上，按一下 [新增項目]。Windows Server 或 Windows 用戶端上的 Azure 備份可讓您保護檔案和資料夾。

    ![Windows Server 備份排程](./media/backup-try-azure-backup-in-10-mins/add-items.png)

    選取您要備份的項目並按一下 [確定]。

    按 [下一步]。

4. 指定 [備份排程] 並按 [下一步]。

    您可以排程每日 (一天最多三次) 或每週備份。

    ![指定備份排程](./media/backup-try-azure-backup-in-10-mins/specify-backup-schedule.png)

5. 選取備份複本的 [保留原則]。您可以修改每日、每週、每月和每年保留原則，以符合您的需求。

    >[AZURE.NOTE] 這篇[文章](backup-azure-backup-cloud-as-tape.md)中有詳細說明備份排程。

     按 [下一步]

    ![選取保留原則](./media/backup-try-azure-backup-in-10-mins/select-retention-policy.png)

6. 選擇初始備份類型。

    您可以透過網路自動備份，也可以離線備份。這篇文章的其餘部分按照自動備份程序。如果您想要執行離線備份，請檢閱這篇文章，以取得[在 Azure Backup 中離線備份工作流程](backup-azure-backup-import-export.md)的其他資訊。

    按 [下一步]

    ![初始備份類型](./media/backup-try-azure-backup-in-10-mins/choose-initial-backup-type.png)

7. 在 [確認] 畫面上檢閱資訊，然後按一下 [完成]。

8. 當精靈建立好**備份排程**時，請按一下 [關閉]。

9. 在 [備份代理程式] 中按一下 [立即備份]，以透過網路完成初始植入。

    ![Windows Server 立即備份](./media/backup-try-azure-backup-in-10-mins/snap-in-backup-now.png)

10. 在 [確認] 畫面上，檢閱精靈將用於備份機器的設定，然後按一下 [備份]。

11. 按一下 [關閉] 即可關閉精靈。您可以在備份程序完成之前關閉精靈，然後精靈會繼續在背景中執行。

12. 完成初始備份後，Azure 備份主控台中的 [作業] 檢視會指出「作業已完成」狀態。

    ![初始備份完成](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

恭喜，您已將檔案和資料夾成功備份到 Azure 備份。

## 後續步驟
- 若要深入了解 Azure 備份，請參閱 [Azure 備份概觀](backup-introduction-to-azure-backup.md)。
- 詳細了解如何[準備環境和備份 Windows 電腦](backup-configure-vault.md)。
- 透過此[先睹為快文章](backup-azure-vms-first-look.md)了解如何備份 Azure VM。
- 閱讀 [Azure 備份論壇](http://go.microsoft.com/fwlink/p/?LinkId=290933)中關於 Azure 備份的最新討論內容。

<!---HONumber=AcomDC_0330_2016-->