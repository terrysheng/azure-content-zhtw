<properties
  pageTitle="準備將工作負載備份至 Microsoft Azure | Microsoft Azure"
  description="本文提供 Microsoft Azure 備份中的應用程式工作負載功能的概觀"
  services="backup"
  documentationCenter=""
  authors="SamirMehta"
  manager="shreeshd"
  editor=""/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/07/2015"
  ms.author="sammehta; jimpark"/>

# 準備將工作負載備份至 Microsoft Azure

> [AZURE.SELECTOR]
- [SCDPM](backup-azure-dpm-introduction.md)
- [Azure Backup](backup-azure-microsoft-azure-backup.md)

本文說明如何準備環境來備份工作負載，讓您可以將資料備份至 Azure。有了 Microsoft Azure 備份伺服器，您可以將應用程式工作負載 (例如 Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange 和 Windows 用戶端) 備份至下列位置加以保護：

- **磁碟** (D2D)，第 1 層工作負載有較高的 RTO
- **Azure** (D2D2C)，長期保留

而且，您可以從單一內部部署使用者介面來管理各種受保護實體 (伺服器和用戶端) 的保護。

>[AZURE.NOTE]Microsoft Azure 備份伺服器會繼承 Data Protection Manager (DPM) 的工作負載備份功能。您將發現其中某些功能指向 DPM 文件的指標。不過，Microsoft Azure 備份伺服器不會保護磁帶，也不會與 System Center 整合。

Microsoft Azure 備份伺服器可以部署為：

- 實體的獨立伺服器。
- Hyper-V 虛擬機器 - 您可以將 Microsoft Azure 備份伺服器當作裝載於內部部署Hyper-V 主機伺服器上的虛擬機器執行，以備份內部部署資料。如需此環境的考量事項清單，請參閱[在內部部署 Hyper-V 伺服器上將 DPM 安裝為虛擬機器](https://technet.microsoft.com/library/dn440941.aspx)。
- VMWare 中的 Windows 虛擬機器 - 您可以部署 Microsoft Azure 備份伺服器來保護 VMWare 中的 Windows 虛擬機器上執行的 Microsoft 工作負載。在這個案例中，Microsoft Azure 備份伺服器可以部署為實體的獨立伺服器、Hyper-V 虛擬機器，或 VMWare 中的 Windows 虛擬機器。
- Azure 虛擬機器 - 您可以將 Microsoft Azure 備份伺服器當做 Azure 中的虛擬機器執行，以備份當成 Azure 虛擬機器執行的雲端工作負載。如需此部署的相關資訊，請參閱[將 DPM 安裝為 Azure 虛擬機器](https://technet.microsoft.com/library/hh758176.aspx)。

此外，

- Microsoft Azure 備份可以安裝在 Windows Server 2008 R2 SP1、2012 和 2012 R2。
- Microsoft Azure 備份伺服器無法安裝在已安裝 SCDPM 或 SCDPM RA 代理程式的電腦上。
- Microsoft Azure 備份伺服器無法安裝在已安裝且向 Azure 備份保存庫註冊 Microsoft Azure 備份代理程式的電腦上。



選取要安裝 Microsoft Azure 備份的伺服器後，您需要：

- 確定已符合 Microsoft Azure 備份伺服器的必要條件
- 建立備份保存庫
- 下載保存庫認證，以及
- 下載 Microsoft Azure 備份伺服器安裝檔案。

以下詳細說明每個步驟。

## 開始之前

現今，Microsoft Azure 備份伺服器僅支援英文地區設定。目前，所有提供 Azure 的地區都提供 Microsoft Azure 復原服務，但 Microsoft Azure Government 資料中心和位於中國的 Microsoft Azure 除外。

為了順利完成安裝，安裝 Microsoft Azure 備份伺服器之前，請確定符合下列必要條件。

- 伺服器必須能夠連線至網際網路。伺服器必須能夠存取 Microsoft Azure。
- 安裝 Microsoft Azure 備份的伺服器需求與 DPM 相同。如需詳細資訊，請參閱這些[硬體需求](https://technet.microsoft.com/library/dn726764.aspx)。
- Microsoft Azure 備份伺服器必須加入網域。
- Microsoft Azure 備份伺服器必須安裝 .Net 3.5、.Net 4.0、.Net 3.5 SP1 功能。如需詳細資訊，請參閱[關於如何啟用 .Net Framework 的詳細資訊](https://technet.microsoft.com/library/dn482071.aspx)。
- Microsoft Azure 備份伺服器應該安裝 Windows Management Framework 4.0。您可以從[這裡](http://www.microsoft.com/download/details.aspx?id=40855)下載。
- 對於作為 Microsoft Azure 備份伺服器資料儲存體專用空間的磁碟，建議的存放集區大小是受保護資料的 1.5 倍。如需詳細資訊，請參閱 [本主題] (https://technet.microsoft.com/library/hh758176.aspx#DPM 伺服器) 的「磁碟與儲存體」一節。

準備 Microsoft Azure 備份伺服器來備份資料，作法如下：

1. **建立備份保存庫** — 在 Azure 備份主控台建立保存庫。
2. **下載保存庫認證** — 在 Azure 備份中，將您建立的管理憑證上傳到保存庫。
3. **下載 Microsoft Azure 備份伺服器** — 您可以從備份保存庫的 [快速啟動] 頁面，下載適用於應用程式工作負載的 Microsoft Azure 備份伺服器。

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

## 下載 Microsoft Azure 備份伺服器
類似於保存庫認證，您可以從備份保存庫的 [快速啟動頁面]，下載適用於應用程式工作負載的 Microsoft Azure 備份伺服器。

1. 按一下 [應用程式工作負載 (磁碟到磁碟到雲端)]。

    ![Microsoft Azure 備份歡迎使用畫面](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)

2. 按一下 [下載]。

    ![下載中心 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)

3. 選取所有檔案並按 [下一步]。![下載中心 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    >[AZURE.NOTE]因為所有檔案的下載大小合計 > 3G，在 10Mbps 下載連結上可能需要 60 分鐘才能完成下載。

4. 將所有檔案放在相同的資料夾。

5. 下載來自 Microsoft Azure 備份下載頁面的所有檔案。

## 安裝 Microsoft Azure 備份伺服器
啟動安裝程式之前，請確定已符合上一節提及的必要條件。

1. 下載所有檔案之後，按一下 [MicrosoftAzureBackupInstaller.exe]。將會出現 [Microsoft Azure 備份安裝精靈]。

    ![Microsoft Azure 備份安裝程式](./media/backup-azure-microsoft-azure-backup/installer-click.png)

2. 提供此伺服器上將解壓縮檔案的位置，按 [下一步]。

    ![Microsoft Azure 備份安裝精靈](./media/backup-azure-microsoft-azure-backup/extract-to.png)

3. 按一下 [解壓縮] 開始解壓縮安裝檔案。

    ![Microsoft Azure 備份安裝精靈](./media/backup-azure-microsoft-azure-backup/ready-to-extract.png)

4. 核取方塊以啟動 setup.exe，開始安裝 Microsoft Azure 備份伺服器，然後按一下 [完成]。

    ![Microsoft Azure 備份安裝精靈](./media/backup-azure-microsoft-azure-backup/launch-setup.png)

5. 按一下 [Microsoft Azure 備份] 啟動安裝精靈。

    ![Microsoft Azure 備份安裝精靈](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

6. 在 [歡迎使用] 畫面中按 [下一步]。

    ![Microsoft Azure 備份歡迎使用畫面](./media/backup-azure-microsoft-azure-backup/welcome-screen.png)

7. 按一下 [檢查]，判斷是否符合 Microsoft Azure 備份伺服器的硬體和軟體必要條件。

    ![Microsoft Azure 備份必要條件 1](./media/backup-azure-microsoft-azure-backup/prereq-screen1.png)

8. 如果完全符合所有必要條件，您會看到訊息指出電腦符合需求。按 [下一步]。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/prereq-screen2.png)

9. Microsoft Azure 備份伺服器需要 SQL Server Standard。選取現有的 2014 SQL Server Standard 本機執行個體，或讓精靈安裝 SQL Server Standard。按一下 [檢查並安裝]，確定伺服器已安裝必要的 SQL 必要條件。

    ![Microsoft Azure 備份 SQL 1](./media/backup-azure-microsoft-azure-backup/sql-setup1.png)

    如果發生失敗且建議您重新啟動電腦，請依指示進行，然後按一下 [再檢查一次]。

    ![SiS 篩選器失敗](./media/backup-azure-microsoft-azure-backup/sis-filter.png)

10. 成功安裝必要條件後，按 [下一步]。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/sql-setup3.png)

11. 提供 Microsoft Azure 備份伺服器檔案的安裝位置，按 [下一步]。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    >[AZURE.NOTE]備份至 Azure 需要暫存位置。請確保暫存位置至少為打算備份至雲端的資料的 5%。在磁碟保護方面，安裝完成之後必須設定獨立的磁碟。如需有關存放集區的詳細資訊，請參閱[設定存放集區和磁碟儲存體](https://technet.microsoft.com/library/hh758075.aspx)。

12. 為受限的本機使用者帳戶提供強式密碼，按 [下一步]。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/security-screen.png)

    >[AZURE.NOTE]設定強式密碼對系統的安全性十分重要。強式密碼的長度至少六個字元、不包含使用者帳戶名稱全部或局部，且至少包含下列四種字元類別中的三種：大寫字元、小寫字元、10 進制數字和符號 (例如 !、@、#)。

13. 選取是否要使用 *Microsoft Update* 檢查更新，按 [下一步]。

    >[AZURE.NOTE]建議讓 Windows Update 重新導向至 Microsoft Update，此網站為 Windows 和 Microsoft Azure 備份伺服器等其他產品提供安全性和重要更新。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

14. 檢閱 [設定值摘要]，按一下 [安裝]。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

    安裝分階段執行。在第一個階段，代理程式會安裝在伺服器上，您需要使用您連同此產品一起下載的有效 Azure 備份保存庫認證來註冊伺服器。然後，精靈會檢查是否可直接連線至網際網路。如果可連線至網際網路，您可以繼續安裝，否則必須提供 Proxy 詳細資料來連線到網際網路。

15. 按 [下一步]，開始執行 Microsoft Azure 復原服務代理程式的安裝階段。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/proxy.png)

    精靈會檢查必要條件，並安裝任何遺漏的必要條件。

16. 按一下 [Install]。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/mars-prereq.png)

17. 當代理程式安裝完成時，按 [下一步]，向 Azure 備份保存庫註冊此伺服器。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/mars-successful.png)

18. 提供 Azure Backup 保存庫認證來註冊伺服器，按 [下一步]。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/vault-reg-screen.png)

19. 提供複雜密碼來加密/解密傳送至 Azure 的資料，並提供位置來儲存此複雜密碼。您可以自動產生複雜密碼，或提供您自己的複雜密碼，最少 16 個字元。按 [下一步]。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/encrypt1.png)

    >[AZURE.NOTE]為了維護機密性，Microsoft Azure 備份伺服器不會將此複雜密碼隨著資料一起傳送至 Azure。您需要將此複雜密碼儲存在安全的位置，從 Azure 還原資料時需要用到。

    Microsoft Azure 備份伺服器成功完成註冊後，安裝程式會繼續安裝和設定 SQL Server Standard 2014 (如果已選擇)。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/sql-install-start-screen.png)

    SQL 安裝完成後，接著會安裝 Microsoft Azure 備份伺服器元件。

    ![Microsoft Azure 備份必要條件 2](./media/backup-azure-microsoft-azure-backup/venus-installation-screen.png)

20. 完成產品安裝並建立桌面圖示後，按兩下圖示啟動產品。

    ![圖示](./media/backup-azure-microsoft-azure-backup/icons.png)

## 將磁碟新增到存放集區

若要將工作負載備份到磁碟和 Azure，您必須先將磁碟新增到存放集區。如需有關新增磁碟的詳細資訊，請參閱[設定存放集區和磁碟儲存體](https://technet.microsoft.com/library/hh758075.aspx)。

設定存放集區後，您就可以將應用程式工作負載備份到磁碟和 Azure。

## 疑難排解

如果 Microsoft Azure 備份伺服器在安裝階段 (或是備份或還原) 失敗且出現錯誤，請參閱這份[錯誤碼文件](https://support.microsoft.com/kb/3041338)，以取得詳細資訊。您也可以參考 [Azure 備份相關的常見問題集](backup-azure-backup-faq.md)

## 常見問題集

### 安裝和部署

問：用於磁碟備份的 Microsoft Azure 備份伺服器磁碟/磁碟區是否支援 NTFS 壓縮？ <br>答：連接到 Microsoft Azure 備份伺服器的磁碟/磁碟區**不**支援 NTFS。

問：部署後是否可以將 Microsoft Azure 備份伺服器移到新的網域？ <br>答：否，**不**支援在部署後將 Microsoft Azure 備份伺服器移到新的網域。

問：如果 Microsoft Azure 備份伺服器安裝在網域控制站，它可以保護哪些資源？ <br>答：如果 Microsoft Azure 備份伺服器與網域控制站一起安裝在相同的伺服器上，只能保護本機資料來源。若要讓 Microsoft Azure 備份伺服器保護其他伺服器上的工作負載，請不要將它與網域控制站安裝在同一部電腦上。

問：Microsoft Azure 備份伺服器可以使用遠端 SQL Server 執行個體嗎？ <br>答：Microsoft Azure 備份伺服器只能使用本機 SQL Server 執行個體。

### Microsoft Azure 備份伺服器存放集區

問：是否可以在 Microsoft Azure 備份伺服器存放集區中提供虛擬磁碟 (VHD/HDx)？ <br>答：是。

問：是否可以在 Microsoft Azure 備份伺服器存放集區使用重複資料刪除功能？ <br>答：是，Microsoft Azure 備份伺服器存放集區支援重複資料刪除功能。這篇 [DPM 部落格文章](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx)完整詳述 Microsoft Azure 備份伺服器的使用者經驗。

問：是否可以在 Microsoft Azure 備份伺服器存放集區使用 USB 或卸除式磁碟機？ <br>答：否。

### Microsoft Azure 備份伺服器至 Azure 服務的連線

問：Microsoft Azure 備份伺服器至 Azure 服務的連線和 Azure 訂用帳戶狀態會影響備份和還原嗎？ <br>答：Microsoft Azure 備份伺服器需要連線至 Azure 服務，產品才能順利運作。同時，Azure 訂用帳戶也必須處於狀況良好狀態。

下表說明 Microsoft Azure 備份伺服器在不同連線能力和 Azure 訂用帳戶狀態下的功能。

| 連線狀態 | Azure 訂閱 | 備份至 Azure| 備份至磁碟 | 從 Azure 還原 | 從磁碟還原 |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| 連線 | Active | 允許 | 允許 | 允許 | 允許 |
| 連線 | 已過期 | 已停止 | 已停止 | 允許 | 允許 |
| 正常 | 已取消佈建 | 已停止 | 已停止 | 已停止且已刪除 Azure 復原點 | 已停止 |
| 連線中斷 > 15 天 | Active | 已停止 | 已停止 | 允許 | 允許 |
| 連線中斷 > 15 天 | 已過期 | 已停止 | 已停止 | 允許 | 允許 |
| 連線中斷 > 15 天 | 已取消佈建 | 已停止 | 已停止 | 已停止且已刪除 Azure 復原點 | 已停止 |

問：假設訂用帳戶狀態持續為*作用中*，當 Microsoft Azure 備份伺服器連線還原時會發生什麼事？ <br>答：一旦 Microsoft Azure 備份伺服器至 Azure 服務的連線恢復正常，且訂用帳戶狀態為*作用中*，則允許 Microsoft Azure 備份伺服器的所有作業。將正常備份至這兩個磁碟及 Azure。

問：如果訂用帳戶狀態從*已過期*狀態還原為*作用中*狀態，將會發生什麼事？ <br>答：假設 Microsoft Azure 備份伺服器至 Azure 服務的連線正常，一旦 Microsoft Azure 備份伺服器訂用帳戶狀態還原為*作用中*，則允許 Microsoft Azure 備份伺服器的所有作業。現在可正常備份至這兩個磁碟及 Azure。

問：如果訂用帳戶狀態從*已取消建佈*狀態還原為*作用中*狀態，將會發生什麼事？ <br>答：假設 Microsoft Azure 備份伺服器至 Azure 服務的連線正常，一旦 Microsoft Azure 備份伺服器訂用帳戶狀態還原為*作用中*，則允許 Microsoft Azure 備份伺服器的所有作業。不過，Azure 復原點會刪除且無法還原。如果磁碟備份在有效的保留範圍內，就可以還原磁碟復原點。

問：我需要建立哪些例外，才能允許 Microsoft Azure 備份伺服器連線至公用 Azure 服務？ <br>答：您必須在防火牆設定檔中允許下列網域位址 - www.msftncsi.com、*.Microsoft.com、*.WindowsAzure.com、*.microsoftonline.com、*.windows.net。

問：如何驗證 Microsoft Azure 備份伺服器是否能夠連線至 Azure 服務？ <br>答：在 Microsoft Azure 備份伺服器主控台執行下列 PowerShell Cmdlet：

```
Get-DPMCloudConnection
Output:
TRUE - Connected to Azure service
FALSE - Not connected to Azure service
```

問：為什麼我的訂用帳戶狀態不是*作用中*？ 如何變更為*作用中*？ <br>答：登入[訂用帳戶入口網站](https://account.windowsazure.com/Subscriptions)並管理您的訂用帳戶。

### 計費

問：什麼是 Microsoft Azure 備份伺服器的計費模型？ <br>答：將會透過受保護執行個體模型對使用者計費。如需詳細資訊，請參閱[價格](http://azure.microsoft.com/pricing/details/backup/)頁面上的常見問題集。

問：如果我只保護磁碟上的資料，則是使用什麼計費模型？ <br>答：計費模型與受保護執行個體模型相同。由於是在內部部署儲存體上保護這項資料，僅磁碟型備份不收取 Azure 儲存體費用。在此情況下，只會對客戶收取受保護執行個體費用。如需有關執行個體的定義和每個執行個體收費金額的詳細資訊，請參閱[價格](http://azure.microsoft.com/pricing/details/backup/) 頁面上的常見問題集。

問：每個受保護執行個體收費多少？ <br>答：請參閱[價格](http://azure.microsoft.com/pricing/details/backup/)頁面。

問：哪裡可以找到強調不同應用程式工作負載及其受保護執行個體費用的範例？ <br>答：請參閱[價格](http://azure.microsoft.com/pricing/details/backup/)頁面的「範例」一節。

問：Microsoft Azure 備份伺服器如何對磁碟和雲端上保護的資料來源計費？ <br>答：不論資料是備份到磁碟或雲端，Microsoft Azure 備份伺服器都根據受保護執行個體來收費。受保護執行個體大小是根據資料來源的前端大小計算。對於 Azure 儲存體中的備份資料，也需要支付 Azure 儲存體成本。

## 後續步驟

請參閱這些文章，以深入了解使用 Microsoft Azure 備份伺服器來保護工作負載。

- [SQL Server 備份](backup-azure-backup-sql.md)
- [SharePoint 伺服器備份](backup-azure-backup-sharepoint.md)
- [替代伺服器備份](backup-azure-alternate-dpm-server.md)

<!---HONumber=Oct15_HO3-->