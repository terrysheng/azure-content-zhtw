<properties
  pageTitle="準備環境以使用 Azure 備份伺服器來備份工作負載 | Microsoft Azure"
  description="確定已適當地準備您的環境，以使用 Azure 備份伺服器來備份工作負載"
  services="backup"
  documentationCenter=""
  authors="pvrk"
  manager="shivamg"
  editor=""
  keywords="Azure 備份伺服器；備份保存庫"/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="02/04/2016"
  ms.author="jimpark;trinadhk;pullabhk"/>

# 準備使用 Azure 備份伺服器來備份工作負載

> [AZURE.SELECTOR]
- [Azure 備份伺服器](backup-azure-microsoft-azure-backup.md)
- [System Center DPM](backup-azure-dpm-introduction.md)

本文說明如何準備環境，以使用 Azure 備份伺服器來備份工作負載。透過 Azure 備份伺服器，您將可從單一主控台保護 Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange 和 Windows 用戶端等應用程式工作負載。

>[AZURE.WARNING] Azure 備份伺服器承襲了 Data Protection Manager (DPM) 的工作負載備份功能。您將發現其中某些功能指向 DPM 文件的指標。不過，Azure 備份伺服器不會保護磁帶，也不會與 System Center 整合。

## 1\.Windows Server 機器

![步驟 1](./media/backup-azure-microsoft-azure-backup/step1.png)

要啟動並執行 Azure 備份伺服器，第一個步驟是具備 Windows Server 機器。

| 位置 | 最低需求 | 其他指示 |
| -------- | -------------------- | ----------------------- |
| Azure | Azure IaaS 虛擬機器 <br><br>A2 標準：雙核心、3.5GB 的 RAM | 您可以先從 Windows Server 2012 R2 Datacenter 的簡單資源庫映像開始著手。[使用 Azure 備份伺服器 (DPM) 保護 IaaS 工作負載](https://technet.microsoft.com/library/jj852163.aspx)有許多細節需要注意。部署機器之前，請先確實閱讀相關文章。 |
| 內部部署 | Hyper-V VM、<br> VMWare VM <br> 或實體主機<br><br>雙核心和 4GB 的 RAM | 您可以使用 Windows Server Deduplication 為 DPM 儲存體刪除重複資料。深入了解在 Hyper-V VM 中部署時，[DPM 和重複資料刪除](https://technet.microsoft.com/library/dn891438.aspx)如何搭配運作。 |

> [AZURE.NOTE] 建議您在具有 Windows Server 2012 R2 Datacenter 的機器上安裝 Azure 備份伺服器。最新版的 Windows 作業系統會自動涵蓋許多必要條件。

如果您打算在個時間點將此伺服器加入網域中，建議您在安裝 Azure 備份伺服器之前完成網域加入活動。若在部署後將現有的 Azure 備份伺服器機器移至新網域，該動作將*不受支援*。

## 2\.備份保存庫

![步驟 2](./media/backup-azure-microsoft-azure-backup/step2.png)

無論您要將備份資料傳送至 Azure，還是將其保存在本機上，軟體都必須連接到 Azure。明確而言，Azure 備份伺服器機器必須向備份保存庫註冊。

若要建立備份保存庫：

1. 登入[管理入口網站](http://manage.windowsazure.com/)。

2. 按一下 [**新增**] > [**資料服務**] > [**復原服務**] > [**備份保存庫**] > [**快速建立**]。如果您有多個與組織帳戶相關聯的訂用帳戶，請選擇與備份保存庫相關聯的正確訂用帳戶。

3. 在 [名稱] 中，輸入保存庫的易記識別名稱。每個訂用帳戶皆需為唯一名稱。

4. 在 [**區域**] 中，選取保存庫的地理區域。在一般情況下，會根據資料主權或網路延遲條件約束來選擇保存庫的區域。

    ![建立備份保存庫](./media/backup-azure-microsoft-azure-backup/backup_vaultcreate.png)

5. 按一下 [建立保存庫]。要等備份保存庫建立好，可能需要一些時間。監視位於入口網站底部的狀態通知。

    ![建立保存庫快顯通知](./media/backup-azure-microsoft-azure-backup/creating-vault.png)

6. 將有一則訊息確認保存庫已成功建立，並且該保存庫會在「復原服務」頁面中列為 [使用中] 狀態。![備份保存庫的清單](./media/backup-azure-microsoft-azure-backup/backup_vaultslist.png)

  > [AZURE.IMPORTANT] 保存庫建立之後，請立即確認是否選擇了適當的儲存體備援選項。在此[概觀](../storage/storage-redundancy.md)中，深入了解[異地備援](../storage/storage-redundancy.md#geo-redundant-storage)和[本機備援](../storage/storage-redundancy.md#locally-redundant-storage)選項。


## 3\.軟體封裝

![步驟 3](./media/backup-azure-microsoft-azure-backup/step3.png)

### 下載軟體封裝

類似於保存庫認證，您可以從備份保存庫的 [快速啟動頁面]，下載適用於應用程式工作負載的 Microsoft Azure 備份伺服器。

1. 按一下 [應用程式工作負載 (磁碟到磁碟到雲端)]。這會使您進入可下載軟體封裝的 [下載中心] 頁面。

    ![Microsoft Azure 備份歡迎使用畫面](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)

2. 按一下 [下載]。

    ![下載中心 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)

3. 選取所有檔案，然後按 [**下一步**]。下載「Microsoft Azure 備份」下載頁面中的所有檔案，並將所有檔案放在相同的資料夾中。![下載中心 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    因為所有檔案的下載大小合計 > 3G，在 10Mbps 下載連結上可能需要 60 分鐘才能完成下載。


### 將軟體封裝解壓縮

下載所有檔案之後，按一下 [MicrosoftAzureBackupInstaller.exe]。這會啟動 [Microsoft Azure 備份安裝精靈]，並將安裝程式檔案解壓縮至您所指定的位置。繼續執行精靈，然後按一下 [解壓縮] 按鈕，以開始解壓縮程序。

> [AZURE.WARNING] 至少 4 GB 的可用空間，才能將安裝程式檔案解壓縮。


![Microsoft Azure 備份安裝精靈](./media/backup-azure-microsoft-azure-backup/extract/03.png)

解壓縮程序完成之後，請選取可啟動剛解壓縮 *setup.exe* 的方塊，以開始安裝「Microsoft Azure 備份伺服器」，然後按一下 [完成] 按鈕。

### 安裝軟體封裝

1. 按一下 [Microsoft Azure 備份] 啟動安裝精靈。

    ![Microsoft Azure 備份安裝精靈](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

2. 在 [歡迎使用] 畫面上按 [下一步] 按鈕。這會讓您進入 [必要條件檢查] 區段。在此畫面上按一下 [檢查] 按鈕，以判斷是否符合「Azure 備份伺服器」的硬體和軟體必要條件。如果完全符合所有必要條件，您會看到訊息指出機器符合需求。按 [下一步] 按鈕。

    ![Azure 備份伺服器 - 歡迎使用和必要條件檢查](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

3. Microsoft Azure 備份伺服器需要 SQL Server 標準，而 Azure 備份伺服器安裝封裝在必要時會隨附適當的 SQL Server 二進位檔。在進行新的「Azure 備份伺服器」安裝時，您應該選擇 [在此安裝中安裝新的 SQL Server 執行個體]，然後按一下 [檢查並安裝] 按鈕。成功安裝必要條件後，按 [下一步]。

    ![Azure 備份伺服器 - SQL 檢查](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    如果發生失敗且建議您重新啟動電腦，請依指示進行，然後按一下 [再檢查一次]。

    > [AZURE.NOTE] Azure 備份伺服器不會使用遠端 SQL Server 執行個體。Azure 備份伺服器所使用的執行個體必須是本機的。

4. 提供 Microsoft Azure 備份伺服器檔案的安裝位置，按 [下一步]。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    備份至 Azure 需要暫存位置。請確保暫存位置至少為打算備份至雲端的資料的 5%。在磁碟保護方面，安裝完成之後必須設定獨立的磁碟。如需有關存放集區的詳細資訊，請參閱[設定存放集區和磁碟儲存體](https://technet.microsoft.com/library/hh758075.aspx)。

5. 為受限的本機使用者帳戶提供強式密碼，按 [下一步]。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/security-screen.png)

6. 選取是否要使用 *Microsoft Update* 檢查更新，按 [下一步]。

    >[AZURE.NOTE] 建議讓 Windows Update 重新導向至 Microsoft Update，此網站為 Windows 和 Microsoft Azure 備份伺服器等其他產品提供安全性和重要更新。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

7. 檢閱 [設定值摘要]，按一下 [安裝]。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

8. 安裝分階段執行。在第一個階段中，會將 Microsoft Azure 復原服務代理程式安裝在伺服器上。精靈也會檢查網際網路連線。如果可連線至網際網路，您可以繼續安裝，否則必須提供 Proxy 詳細資料來連線到網際網路。

    下一個步驟是設定 Microsoft Azure 復原服務代理程式。在設定的過程中，您將必須提供保存庫認證，以向備份保存庫註冊機器。您也須提供複雜密碼來加密/解密 Azure 與您的內部部署之間所傳送的資料。您可以自動產生複雜密碼，或提供您自己的複雜密碼，最少 16 個字元。繼續執行精靈，直到代理程式完成設定。

    ![Azure 備份伺服器必要條件 2](./media/backup-azure-microsoft-azure-backup/mars/04.png)

9. Microsoft Azure 備份伺服器順利完成註冊後，整體安裝精靈會繼續安裝及設定 SQL Server 和 Azure 備份伺服器的元件。SQL Server 元件安裝完成後，會安裝 Azure 備份伺服器元件。

    ![Azure 備份伺服器](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)


當安裝步驟完成時，產品的桌面圖示也將一併建立完成。只要按兩下該圖示，即可啟動產品。

### 新增備份儲存體

第一個備份複本會保存在連接至 Azure 備份伺服器機器的儲存體上。如需有關新增磁碟的詳細資訊，請參閱[設定存放集區和磁碟儲存體](https://technet.microsoft.com/library/hh758075.aspx)。

> [AZURE.NOTE] 即使您打算將資料傳送至 Azure，也必須新增備份儲存體。在目前的「Azure 備份伺服器」架構中，「Azure 備份」保存庫會保存資料的*第二個*複本，而本機儲存體則是保存第一個 (必要的) 備份複本。

## 4\.網路連線

![步驟 4](./media/backup-azure-microsoft-azure-backup/step4.png)

Azure 備份伺服器需要連線至 Azure 備份服務，產品才能順利運作。若要驗證機器是否連接至 Azure，請在Azure 備份伺服器 PowerShell 主控台中使用 ```Get-DPMCloudConnection``` Commandlet。如果 commandlet 的輸出為 TRUE，表示連線存在，否則沒有連線。

同時，Azure 訂用帳戶也必須處於狀況良好狀態。若您想了解訂用帳戶狀態並加以管理，請登入[訂用帳戶入口網站](https://account.windowsazure.com/Subscriptions)。

在您了解 Azure 連線和 Azure 訂用帳戶的狀態後，您可以使用下表來確認提供的備份/還原功能會受到哪些影響。

| 連線狀態 | Azure 訂閱 | 備份至 Azure| 備份至磁碟 | 從 Azure 還原 | 從磁碟還原 |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| 連線 | Active | 允許 | 允許 | 允許 | 允許 |
| 連線 | 已過期 | 已停止 | 已停止 | 允許 | 允許 |
| 連線 | 已取消佈建 | 已停止 | 已停止 | 已停止且已刪除 Azure 復原點 | 已停止 |
| 連線中斷 > 15 天 | Active | 已停止 | 已停止 | 允許 | 允許 |
| 連線中斷 > 15 天 | 已過期 | 已停止 | 已停止 | 允許 | 允許 |
| 連線中斷 > 15 天 | 已取消佈建 | 已停止 | 已停止 | 已停止且已刪除 Azure 復原點 | 已停止 |

### 從連線中斷的情況復原
如果您有防火牆或 Proxy 以致無法存取 Azure，您必須將防火牆/Proxy 設定檔中的下列網域位址列入白名單中：

- www.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

在 Azure 的連線已還原至 Azure 備份伺服器機器之後，可執行的作業將取決於 Azure 訂用帳戶狀態。上表詳細列出機器「已連接」之後所允許之作業的相關資訊。

### 處理訂用帳戶狀態

您可以將 Azure 訂用帳戶從 [已過期]或 [已取消佈建] 狀態變更為 [作用中] 狀態。不過，當狀態不是 [作用中] 時，此動作會對產品的行為造成某些影響：

- *已取消佈建*的訂用帳戶在取消佈建的這段期間會失去功能。切換為 [作用中] 時，就會恢復產品的備份/還原功能。此外，只要以夠大的保留期間來保存，也可以擷取本機磁碟上的備份資料。不過，一旦訂用帳戶進入*已取消佈建*狀態，Azure 中的備份資料便會遺失而無法復原。
- *已過期*的訂用帳戶只有在恢復*作用中*狀態之前會失去功能。任何針對訂用帳戶處於*已過期*期間所排定的備份，都不會執行。


## 疑難排解

如果 Microsoft Azure 備份伺服器在安裝階段 (或是備份或還原) 失敗且出現錯誤，請參閱這份[錯誤碼文件](https://support.microsoft.com/kb/3041338)，以取得詳細資訊。您也可以參考 [Azure 備份相關的常見問題集](backup-azure-backup-faq.md)


## 後續步驟

您可以在 Microsoft TechNet 網站上取得有關[準備 DPM 的環境](https://technet.microsoft.com/library/hh758176.aspx)的詳細資訊。其中也包含可據以部署和使用 Azure 備份伺服器之支援組態的相關資訊。

請參閱這些文章，以深入了解使用 Microsoft Azure 備份伺服器來保護工作負載。

- [SQL Server 備份](backup-azure-backup-sql.md)
- [SharePoint 伺服器備份](backup-azure-backup-sharepoint.md)
- [替代伺服器備份](backup-azure-alternate-dpm-server.md)

<!---HONumber=AcomDC_0316_2016-->