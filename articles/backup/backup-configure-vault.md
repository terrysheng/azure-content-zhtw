<properties
	pageTitle="準備環境以備份 Windows Server 或用戶端電腦 | Microsoft Azure"
	description="請建立備份保存庫、下載認證並安裝備份代理程式，讓環境做好備份 Windows 的準備。"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="備份保存庫; 備份代理程式; 備份 Windows;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="trinadhk; jimpark; markgal"/>


# 準備環境以將 Windows 電腦備份至 Azure
本文列出準備環境以將 Windows 電腦備份至 Azure 所需要進行的事項。

| 步驟 | 名稱 | 詳細資料 |
| :------: | ---- | ------- |
| 1 | [建立保存庫](#create-a-backup-vault) | 在 [Azure 備份管理入口網站](http://manage.windowsazure.com)中建立保存庫 |
| 2 | [下載保存庫認證](#download-the-vault-credential-file) | 下載保存庫認證，以便用來向備份保存庫註冊 Windows 電腦 |
| 3 | [安裝 Azure 備份代理程式](#download-install-and-register-the-azure-backup-agent) | 安裝代理程式，然後使用保存庫認證來將伺服器註冊到備份保存庫 |

如果您已完成以上所有的高階步驟，您就可以開始[備份您的 Windows 電腦](backup-azure-backup-windows-server.md)。否則，請繼續進行下列詳細步驟，以確保您的環境準備就緒。

## 開始之前
若要準備環境以備份 Windows 電腦，您需要有 Azure 帳戶。如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/free/)。

## 建立備份保存庫
若要將 Windows 電腦或 Data Protection Manager (DPM) 的檔案和資料備份至 Azure，或將 IaaS VM 備份至 Azure，您必須在要儲存資料的地區區域中建立一個備份保存庫。

**若要建立備份保存庫：**

1. 登入[管理入口網站](https://manage.windowsazure.com/)

2. 按一下 [**新增**] > [**資料服務**] > [**復原服務**] > [**備份保存庫**] > 並選擇 [**快速建立**]。

    ![[建立保存庫]](./media/backup-configure-vault/createvault1.png)

3. 針對 [**名稱**] 參數，輸入易記名稱來識別備份保存庫。每個訂用帳戶皆需為唯一名稱。

    針對 [**區域**] 參數，選取備份保存庫的地區區域。選項會決定您的備份資料要傳送到哪個地理區域。透過選擇靠近您位置的地理區域，您可以在備份至 Azure 時減少網路延遲。

    按一下 [**建立保存庫**] 以完成工作流程。

    要等備份保存庫建立好，可能需要一些時間。若要檢查狀態，可以監控位於入口網站底部的通知。

    ![正在建立保存庫](./media/backup-configure-vault/creatingvault1.png)

    建立備份保存庫之後，您會看到一則訊息表示已成功建立保存庫。該保存庫也會在「復原服務」的資源中列為 [**使用中**]。

    ![正在建立保存庫狀態](./media/backup-configure-vault/backupvaultstatus1.png)

    >[AZURE.IMPORTANT] 識別儲存體備援選項的最佳時機，在於保存庫建立之後，以及任何電腦註冊至保存庫之前。一旦項目已註冊至保存庫，儲存體備援選項即遭到鎖定且無法修改。

4. 選取**儲存體備援**選項。

    如果您使用 Azure 作為主要的備份儲存體端點 (例如您正從 Windows Server 備份至 Azure)，您應該考慮挑選 (預設值) [異地備援儲存體](../storage/storage-redundancy.md#geo-redundant-storage)選項。

    如果您使用 Azure 做為第三備份儲存體端點 (例如您使用 SCDPM 在內部部署環境中建立本機備份複本，並使用 Azure 來滿足長期保留需求)，則應該考慮選擇[本地備援儲存體](../storage/storage-redundancy.md#locally-redundant-storage)。這將減少在 Azure 中儲存資料的成本，同時針對您可能會接受第三個複本的資料提供較低層級的持久性。

    在此[概觀](../storage/storage-redundancy.md)中，深入了解[異地備援](../storage/storage-redundancy.md#geo-redundant-storage)和[本機備援](../storage/storage-redundancy.md#locally-redundant-storage)儲存體選項。

    a.按一下您剛才建立的保存庫。

    b.在 [快速啟動] 頁面上，選取 [設定]。

    ![設定保存庫狀態](./media/backup-try-azure-backup-in-10-mins/configure-vault.png)

    c.選擇適當的儲存體備援選項。

    如果您已選取 [本機備援]，則必須按一下 [儲存]，因為 [異地備援] 是預設選項。

    ![GRS](./media/backup-try-azure-backup-in-10-mins/geo-redundant.png)

    d.按一下左導覽窗格中的 [復原服務]，以返回 [復原服務] 的資源清單。

    ![選取備份保存庫](./media/backup-try-azure-backup-in-10-mins/rs-left-nav.png)

## 下載保存庫認證檔
內部部署伺服器 (Windows 用戶端或 Windows Server 或 Data Protection Manager 伺服器) 必須先驗證備份保存庫，才能將資料備份至 Azure。驗證是利用「保存庫認證」來達成。保存庫認證檔會透過安全通道，從 Azure 入口網站下載，而 Azure 備份服務不會知道憑證的私密金鑰，且私密金鑰不會保存在入口網站或服務中。

深入了解如何[使用保存庫認證來驗證 Azure 備份服務](backup-introduction-to-azure-backup.md#what-is-the-vault-credential-file)。

**若要將保存庫認證檔下載至本機電腦：**

1. 登入[管理入口網站](https://manage.windowsazure.com/)

2. 按一下左側導覽窗格的 [復原服務] 並選取您所建立的備份保存庫。

3.  按一下雲端圖示以前往備份保存庫的 [快速啟動] 檢視。

    ![快速檢視](./media/backup-configure-vault/quickview.png)

4. 在 [快速啟動] 頁面上，按一下 [下載保存庫認證]。

    ![下載](./media/backup-configure-vault/downloadvc.png)

    入口網站會使用保存庫名稱和目前日期的組合來產生保存庫認證。保存庫認證檔僅在註冊工作流程期間使用，並於 48 小時後過期。

    保存庫認證檔可以從入口網站進行下載。

5. 按一下 [儲存] 將保存庫認證下載至本機帳戶的下載資料夾，或從 [儲存] 功能表中選取 [另存新檔]，以指定保存庫認證的位置。

    此時，您不需要開啟保存庫認證。

    請確保保存庫認證儲存在可從您的電腦存取的位置。如果它儲存在檔案共用/SMB，請檢查存取權限。

## 下載、安裝和註冊 Azure 備份代理程式
建立 Azure 備份保存庫之後，您的每部 Windows 電腦 (Windows Server、Windows 用戶端、System Center Data Protection Manager 伺服器，或 Azure 備份伺服器電腦) 上應該安裝代理程式，以便讓您將資料和應用程式備份至 Azure。

**若要下載、安裝和註冊代理程式：**

1. 登入[管理入口網站](https://manage.windowsazure.com/)

2. 按一下 [復原服務]，然後選取您要向伺服器註冊的備份保存庫。

3. 在 [快速啟動] 頁面上，按一下 [適用於 Windows Server 或 System Center Data Protection Manager 或 Windows 用戶端的代理程式] > [儲存]。

    ![儲存代理程式](./media/backup-configure-vault/agent.png)

4. 將 *MARSagentinstaller.exe* 下載完成後，按一下 [執行] (或從儲存的位置按兩下 **MARSAgentInstaller.exe**)。選擇代理程式所需的*安裝資料夾*和*快取資料夾*，然後按 [下一步]。

    您指定的快取位置必須至少包含等於備份資料大小 5% 的可用空間。

    ![暫存和快取](./media/backup-configure-vault/recovery-services-agent-setup-wizard-1.png)

5. 若您使用 Proxy 伺服器連接至網際網路，請在 [**Proxy 組態**] 畫面上，輸入 Proxy 伺服器詳細資料。若您使用已驗證的 Proxy，請在此畫面中輸入使用者名稱和密碼的詳細資料，然後按 [下一步]。

    Azure 備份代理程式會安裝 .NET Framework 4.5 和 Windows PowerShell (若尚未安裝) 來完成安裝。

6. 安裝代理程式之後，按一下 [前往註冊] 以繼續工作流程。

    ![註冊](./media/backup-configure-vault/register.png)

7. 在 [保存庫識別] 畫面中，瀏覽並選取先前下載的*保存庫認證檔*。

    ![保存庫認證](./media/backup-configure-vault/vc.png)

    保存庫認證檔僅於 48 小時內有效 (從入口網站下載後起算)。如果您在此畫面中遇到任何錯誤 (例如「提供的保存庫認證檔已過期」)，請登入 Azure 入口網站，並再次下載保存庫認證檔。

    請確定保存庫認證檔位於可透過設定應用程式存取的位置。如果您遇到存取權相關的錯誤，請將保存庫認證檔複製至此電腦中的暫存位置並重試作業。

    如果您遇到保存庫認證無效的錯誤 (例如「提供的保存庫認證無效」)，檔案可能損毀或沒有復原服務所關聯的最新認證。從入口網站下載新的保存庫認證檔後重試作業。若使用者連續快速地按下 [下載保存庫認證] 選項，則通常會看見此錯誤。在此情況下，只有最後一個保存庫認證檔有效。

8. 在 [加密設定] 畫面中，您可以*產生*複雜密碼或*提供*複雜密碼 (最少 16 個字元)。請記得將複雜密碼存放在安全的位置。

    ![加密](./media/backup-configure-vault/encryption.png)

    > [AZURE.WARNING] 如果遺失或忘記複雜密碼，Microsoft 將無法協助您復原備份資料。加密複雜密碼為使用者所有，Microsoft 將不會看到使用者所使用的複雜密碼。請將檔案儲存在安全的位置，在復原作業期間需要該檔案。

9. 按一下 [完成]。

    電腦即成功註冊至保存庫，且您已準備好開始備份至 Microsoft Azure。

## 後續步驟
- 註冊[免費的 Azure 帳戶](https://azure.microsoft.com/free/)
- [備份 Windows Server 或用戶端電腦](backup-azure-backup-windows-server.md)。
- 如果仍有未獲得解答的問題，請查看[Azure 備份服務常見問題集](backup-azure-backup-faq.md)。
- 造訪 [Azure 備份論壇](http://go.microsoft.com/fwlink/p/?LinkId=290933)

<!---HONumber=AcomDC_0302_2016-->