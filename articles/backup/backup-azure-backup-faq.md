<properties
   pageTitle="Azure 備份常見問題集 | Microsoft Azure"
   description="關於備份代理程式、備份和保留、復原、安全性，以及 Azure 備份解決方案其他常見問題的常見問題集答案。"
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="備份解決方案；備份服務"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="03/23/2016"
	 ms.author="trinadhk; giridham; arunak; markgal; jimpark;"/>

# Azure 備份服務常見問題集
這篇文章是關於 Azure 備份服務的常見問題 (和個別答案) 清單。我們的社群會快速回覆，如果是經常詢問的問題，我們會將它加入至這份文件。問題的答案通常會提供參考或支援資訊。您可以在本文件或相關文件的 Disqus 一節中詢問有關 Azure 備份的問題。您也可以在[論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)中張貼有關 Azure 備份服務的問題。

## 安裝和設定
**Q1.若我要使用 Azure 備份來備份至 Azure，受支援的作業系統清單為何？** <br/> A1.Azure 備份支援下列作業系統清單


| 作業系統 | 平台 | SKU |
| :------------- |-------------| :-----|
| Windows 8 和最新的 SP | 64 位元 | Enterprise、Pro |
| Windows 7 和最新的 SP | 64 位元 | Ultimate、Enterprise、Professional、Home Premium、Home Basic、Starter |
| Windows 8.1 和最新的 SP | 64 位元 | Enterprise、Pro |
| Windows 10 | 64 位元 | 企業版、專業版、家用版 |
|Windows Server 2012 R2 和最新的 SP|	64 位元|	Standard、Datacenter、Foundation|
|Windows Server 2012 和最新的 SP|	64 位元|	Datacenter、Foundation、Standard|
|Windows Storage Server 2012 R2 和最新的 SP |64 位元|	Standard、Workgroup|
|Windows Storage Server 2012 和最新的 SP |64 位元 |Standard、Workgroup
|Windows Server 2012 R2 和最新的 SP |64 位元|	Essential|
|Windows Server 2008 R2 SP1 |64 位元|	Standard、Enterprise、Datacenter、Foundation|
|Windows Server 2008 R2 |64 位元|	Standard、Enterprise、Datacenter、Foundation|

**Q2.哪裡可以下載最新的 Azure 備份代理程式？** <br/> A2.您可以下載最新的代理程式，以便從[這裡](http://aka.ms/azurebackup_agent)備份 Windows Server、System Center DPM 或 Windows 用戶端。如果您想要備份虛擬機器，請使用 VM 代理程式 (這會自動安裝適當的擴充功能)。從 Azure 資源庫建立的虛擬機器上已經有 VM 代理程式。

**Q3.支援的 SCDPM 伺服器版本為何？** <br/> A3.我們建議您在最新更新的 SCDPM 彙總套件上安裝[最新](http://aka.ms/azurebackup_agent)的 Azure 備份代理程式 (2015 年 7 月後為 UR6)

****Q4.當設定 Azure 備份代理程式時，系統提示我要輸入「保存庫認證」。保存庫認證是否過期？ A4.是，保存庫認證將於 48 小時後過期。若檔案已過期，請登入 Azure 入口網站，並從您的備份保存庫下載保存庫認證檔。

**Q5.每個 Azure 訂用帳戶中可以建立的備份保存庫數目是否有任何限制？** <br/> A5.是。自 2015 年 7 月起，每個訂用帳戶您可以建立 25 個保存庫。若您需要更多的保存庫，請建立新的訂用帳戶。

**Q6.我應該將保存庫視為計費實體嗎？** <br/> A6.雖然可以取得每個保存庫的詳細計費，但強烈建議您考慮 Azure 訂用帳戶作為計費實體。這在所有服務皆保持一致，而且您可以更輕鬆地進行管理。

**Q7.針對每個保存庫註冊的伺服器/電腦具有任何數目限制嗎？** <br/> A7.是，您可以針對每個保存庫註冊最多 50 個電腦。對於 Azure IaaS 虛擬機器，限制為每個保存庫 200 個 VM。若您需要註冊更多電腦，請建立新的保存庫。

**Q8.從 Windows Server/用戶端或 SCDPM 伺服器進行備份的資料量具有任何限制嗎？** <br/> A8.編號

**Q9.我如何將伺服器註冊到其他資料中心？**<br/> A9.備份資料會傳送至「備份服務」的資料中心以進行註冊。若要變更資料中心，最簡單的方式是解除安裝代理程式，並重新安裝代理程式，然後註冊新的資料中心。

**Q10.若重新命名正在將資料備份至 Azure 的 Windows 伺服器，則會發生什麼情況？** A10.當您重新命名伺服器時，所有目前設定的備份都會停止。您必須向備份保存庫註冊伺服器的新名稱。由於這是新的註冊，第一次備份作業是完整備份，而非增量備份。如果您需要復原先前備份到採用舊伺服器名稱之保存庫的資料，您可以使用 [復原資料] 精靈中 [[其他伺服器](backup-azure-restore-windows-server.md#recover-to-an-alternate-machine)] 選項來復原該資料。


**Q11.我可以從何種類型的磁碟機備份檔案和資料夾？** <br/> A11.下列的磁碟機/磁碟區組合無法進行備份：

- 卸除式媒體：必須報告為固定式磁碟機，才能作為備份項目來源使用。
- 唯讀磁碟區：必須為可寫入磁碟區，才能使磁碟區陰影複製服務 (VSS) 正常運作。
- 離線磁碟區：必須為線上磁碟區，才能使 VSS 正常運作。
- 網路共用：必須為本機磁碟區，才能使用線上備份來備份伺服器。
- 受 Bitlocker 保護的磁碟區：磁碟區必須先解除鎖定才能執行備份。
- 檔案系統識別碼：NTFS 是唯一支援這個版本的線上備份服務的檔案系統。

**Q12.我可以從伺服器備份何種類型的檔案和資料夾？**<br/> A12.支援下列類型：

- 已加密
- 已壓縮
- 疏鬆
- 已壓縮 + 疏鬆
- 永久連結：不支援並略過
- 重新分析點：不支援並略過
- 已加密 + 已壓縮：不支援並略過
- 已加密 + 疏鬆：不支援並略過
- 已壓縮資料流：不支援並略過
- 疏鬆資料流：不支援並略過

**Q13.什麼是快取資料夾的最低大小需求？** <br/> A13.快取資料夾的大小可決定您正在備份的資料量。快取資料夾應該是資料儲存體所需空間的 5%。

**Q14.如果我的組織有一個備份保存庫，如何在還原資料時隔離某一部伺服器與另一部伺服器的資料？**<br/> A14.所有向相同保存庫註冊的伺服器都可以復原由使用相同複雜密碼之其他伺服器所備份的資料。如果您想要隔離您伺服器中的備份資料與您組織中的其他伺服器，請使用這些伺服器的指定複雜密碼。例如，人力資源伺服器可能使用一組加密複雜密碼，而會計伺服器使用另一組，並且儲存體伺服器使用第三組。

**Q15.我可以在訂用帳戶之間「移轉」備份資料嗎？** <br/> A15：否

**Q16.我可以在訂用帳戶之間「移轉」備份保存庫嗎？** <br/> A16：否。保存庫會建立在訂用帳戶層級，並且在建立之後無法重新指派至其他訂用帳戶。

**Q17.Azure 備份代理程式是否在使用 Windows Server 2012 重複資料刪除的伺服器上運作？** <br/>A17：是。當代理程式服務準備備份作業時，會將重複資料刪除的資料轉換成一般資料。它接著會最佳化資料以備份，加密資料，然後將加密的資料傳送至線上備份服務。

**Q18.如果我在備份作業開始後予以取消，是否會刪除已傳輸的備份資料？** <br/>A18：否。備份保存庫會儲存取消點之前已傳輸的備份資料。Azure 備份會使用檢查點機制，在備份期間偶爾將檢查點加入至備份資料。因為備份資料中有檢查點，所以下一個備份程序才可驗證檔案的完整性。下次觸發的備份會隨先前經備份的資料遞增。遞增備份可提供較佳的頻寬使用率，讓您不需要重複傳輸相同的資料。

**Q19.為什麼出現「Azure 備份尚未針對此伺服器設定」警告，即使我之前已排定定期備份亦然？** <br/>A19：當本機伺服器儲存的備份排程設定與備份保存庫儲存的設定不同時，會發生此警告。當伺服器或設定已復原至已知的良好狀態時，備份排程可能會失去同步處理。如果您收到此警告，請[重新設定備份原則](backup-azure-backup-windows-server.md)，然後 [立即執行備份] 以重新同步處理本機伺服器與 Azure。

**Q20.應針對 Azure 備份設定哪些防火牆規則？** <br/> A20.為了妥善保護內部部署至 Azure 和工作負載至 Azure 的資料，建議您允許您的防火牆與下列 URL 通訊：

- www.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

**Q21.您可以在已由 Azure 備份服務所備份的 Azure VM 上使用 VM 擴充功能來安裝 Azure 備份代理程式嗎？** <br/> A21.當然。Azure 備份使用 VM 擴充功能為 Azure VM 提供 VM 層級備份。您可以在客體 Windows 作業系統上安裝 Azure 備份代理程式，以保護該客體 OS 上的檔案和資料夾。

**Q22.您可以在 Azure VM 上安裝 Azure 備份代理程式來備份 Azure VM 所提供的暫存儲存體中存在的檔案和資料夾嗎？** <br/> A22.您可以在客體 Windows 作業系統上安裝 Azure 備份代理程式，並將檔案和資料夾備份至暫存儲存體。但請注意，一旦抹除暫存儲存體資料，備份就會失敗。此外，如果暫存儲存體資料已遭刪除，您只能還原至非變動性儲存體。

**Q23.可以使用 Azure 備份代理程式指定為 Azure 備份原則一部分的檔案路徑的長度為何？** <br/> A23.Azure 備份代理程式依存於 NTFS。 [檔案路徑長度規格受限於 Windows API](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths)。在備份檔案路徑長度大於 Windows API 所指定長度的檔案時，客戶可以選擇備份備份檔案的父資料夾或磁碟機。

**Q24. 使用 Azure 備份代理程式之 Azure 備份原則的檔案路徑中允許哪些字元？** <br/> A24.Azure 備份代理程式依存於 NTFS。它可讓 [NTFS 支援的字元](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions)成為檔案規格的一部分。


## 備份和保留
**Q1.正在備份的每個資料來源是否有大小限制？** <br/> A1.截至 2015 年 8 月，適用於支援的作業系統的資料來源大小上限

|S.No |	作業系統 |	資料來源的大小上限 |
| :-------------: |:-------------| :-----|
|1| Windows Server 2012 或更新版本| 54400 GB|
|2| Windows 8 或更新版本| 54400 GB|
|3| Windows Server 2008、Windows Server 2008 R2 | 1700 GB|
|4| Windows 7 | 1700 GB|

下表說明如何決定每個資料來源大小。

|	資料來源 |	詳細資料 |
| :-------------: |:-------------|
|磁碟區 |從伺服器或用戶端電腦的單一磁碟區備份的資料量。|
|Hyper-V 虛擬機器 | 要備份的虛擬機器上所有 VHD 的資料總和|
|Microsoft SQL Server 資料庫 | 要備份的單一 SQL 資料庫大小的大小 |
|Microsoft SharePoint |在要備份的 SharePoint 伺服陣列中內容和組態資料庫的總和|
|Microsoft Exchange |在要備份的 Exchange Server 中所有 Exchange 資料庫的總和|
|BMR/系統狀態 |在要備份的電腦上 BMR 或系統狀態的每個個別複本|

**Q2.備份作業每日可排程的次數是否有限制？**<br/> A2.是，您一天可以在 Windows Server 或 Windows 用戶端上執行備份作業最多三次。您一天可以在 System Center DPM 上執行備份作業最多兩次。您一天可以執行 IaaS VM 的備份作業一次。

**Q3.DPM 和 Windows Server (亦即在不含 DPM 的 Windows Server 上) 的排程原則是否具有差異？** <br/> A3.是。使用 DPM 時，您可以指定每日、每週、每月和每年排程。Windows Server (不含 DPM) 僅讓您指定每日和每週排程。

**Q4.DPM 和 Windows Server/用戶端 (亦即在不含 DPM 的 Windows Server 上) 的保留原則是否具有差異？**<br/> A4.否，DPM 和 Windows Server/用戶端均有每日、每週、每月和每年保留原則。

**Q5.我可以選擇性設定保留原則，例如設定每週和每日，但不包含每年和每月嗎？**<br/> A5.是的，Azure 備份保留結構讓您可以依照您的需求，彈性定義保留原則。

**Q6.我可以在下午 6:00「排程備份」，並在不同的時間指定「保留原則」嗎？**<br/> A6.否。保留原則僅能套用在復原點上。在以下影像中，將於上午 12:00 和下午 6:00 進行備份時指定保留原則。<br/>

![排程備份和保留](./media/backup-azure-backup-faq/Schedule.png) <br/>

**Q7.增量複本會根據已排程保留原則進行傳輸嗎？** <br/> A7.否，增量複本會根據備份排程頁面中所述的時間進行傳送。可以保留的復原點取決於保留原則。

**Q8.若長期保留備份，則需要花很長的時間來復原資料嗎 (假設是最舊的復原點)？** <br/> A8.否，復原最舊或最新復原點所花費的時間皆相同。每個復原點的功能就像一個完整的復原點。

**Q9.若每個復原點就像一個完整的復原點，則其是否會影響可計費的備份儲存體總數？**<br/> A9.典型的長期保留復原點產品會將備份資料儲存為完整的復原點。完整的復原點是效率不佳的儲存體，但可以更輕鬆且更快速地進行還原。增量複本是有效率的儲存體，但會要求您還原一連串的資料，而這會影響復原時間。Azure 備份的唯一儲存體架構透過最佳化儲存資料以進行快速還原，並降低儲存體成本支出，可讓您魚與熊掌兼得。此資料儲存方法可確保有效率地使用您的輸入和輸出頻寬。資料儲存體數量和復原資料所需的時間都會降到最低。

**Q10.可建立的復原點數目有限制嗎？**<br/> A10.沒有，我們已經取消了復原點的限制。您可以建立您所需的復原點數量。

**Q11.為什麼備份中傳輸的資料量不等於我備份的資料量？**<br/> A11.已備份的所有資料會經過壓縮，且在傳輸之前會經過加密。一旦套用壓縮和加密，備份保存庫中的資料會縮小 30-40%。

**Q12.是否有辦法調整備份服務所使用的頻寬量？**<br/> A12.是，使用備份代理程式中的 [變更屬性] 選項來調整頻寬。調整頻寬量以及您使用該頻寬的時間。如需詳細資訊，請參閱[網路節流](../backup-configure-vault.md#enable-network-throttling)。


## 復原
**Q1.我可以在備份至 Azure 的資料上執行幾次復原？**<br/> A1.從 Azure 備份進行復原的次數沒有任何限制。

**Q2.我必須針對復原期間來自 Azure 資料中心的輸出流量支付費用嗎？**<br/> A2.否。您的復原作業完全免費，且輸出流量不計費。

## 安全性
**Q1.傳送至 Azure 的資料會經過加密嗎？** <br/> A1.是。資料會使用 AES256 在內部部署伺服器/用戶端/SCDPM 電腦上進行加密，且資料會透過安全的 HTTPS 連結進行傳送。

**Q2.位於 Azure 的備份資料也會經過加密嗎？**<br/> A2.是。傳送至 Azure 的資料仍會維持加密 (靜止) 狀態。Microsoft 不會解密在任何時間點所備份的資料。

**Q3.什麼是用來加密備份資料之加密金鑰的最小長度？** <br/> A3.加密金鑰應該至少包含 16 個字元。

**Q4.如果我錯置加密金鑰，則會發生什麼情況？ 我可以復原資料 (或者) Microsoft 可以復原資料嗎？** <br/> A4.用來加密備份資料的金鑰僅存在於客戶組織內部。Microsoft 不會維護 Azure 中的複本，且無法存取金鑰。若客戶錯置金鑰，則 Microsoft 無法復原備份資料。

## 備份快取

**Q1.如何變更為 Azure 備份代理程式指定的快取位置？**<br/>

1. 在提高權限的命令提示字元中執行下列命令以停止備份引擎：

  ```PS C:\> Net stop obengine```

2. 將快取空間資料夾複製到具有足夠空間的其他磁碟機。您應該從快取空間資料夾複製檔案，而不是移動檔案。確認備份使用新的快取空間之後，就可以移除原始的快取空間。

3. 更新下列登錄項目，其具備新快取空間資料夾的路徑。<br/>

|登錄路徑 | 登錄金鑰 | 值 |
| ------ | ------- | ------|
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` | ScratchLocation | 新的快取資料夾位置 |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` | ScratchLocation | 新的快取資料夾位置 |

4. 在提高權限的命令提示字元中執行下列命令以重新啟動備份引擎：

  ```PS C:\> Net start obengine```

  一旦在新的快取位置成功完成備份建立，您就可以移除原始的快取資料夾。

<!---HONumber=AcomDC_0323_2016-->