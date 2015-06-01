<properties
	pageTitle="Azure 虛擬機器備份 - 備份"
	description="了解如何在註冊之後備份 Azure 虛擬機器"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/30/2015"
	ms.author="aashishr"/>


# 備份 Azure 虛擬機器

本文是備份 Azure 虛擬機器的基本指南。繼續之前，請確定已符合所有[必要條件][prereq]。

備份 Azure 虛擬機器需要三個主要步驟：

![備份 Azure 虛擬機器的三個步驟][three-steps]

## 探索 Azure 虛擬機器
探索程序會在 Azure 中查詢訂用帳戶中的虛擬機器清單，以及其他資訊，例如雲端服務名稱、地區等。

> [AZURE.NOTE]第一個步驟一定都是執行探索程序。這是為了確保識別任何加入至訂用帳戶的新虛擬機器。

若要觸發探索程序，請執行下列步驟：

1. 瀏覽至備份保存庫 (位於 Azure 入口網站的 [**復原服務**] 下)，按一下 [**註冊的項目**] 索引標籤。

2. 在下拉式功能表中選擇工作負載類型 [**Azure 虛擬機器**]，按一下 [**選取**] 按鈕。![選取工作負載][select-workload]

3. 按一下頁面底部的 [**探索**] 按鈕。![探索按鈕][discover-button]

4. 在列表顯示虛擬機器時，探索程序可能會執行幾分鐘。探索程序執行時，畫面底部會出現快顯通知。![探索 VM][discover-vms]

5. 探索程序完成時會出現快顯通知。![探索完成][discover-done]

<br><br>
## 註冊 Azure 虛擬機器
虛擬機器必須先向 Azure 備份服務註冊，才能受到保護。註冊程序有兩個主要目標：

1. 將備份擴充功能外掛到虛擬機器中的 VM 代理程式

2. 將虛擬機器與 Azure 備份服務相關聯。

註冊通常是一次性活動。Azure 備份服務會自動處理備份擴充功能的升級和修補，完全不會干擾使用者。如此可減輕備份產品經常對使用者造成的「代理程式管理負擔」。

### 註冊虛擬機器

1. 瀏覽至備份保存庫 (位於 Azure 入口網站的 [**復原服務**] 下)，按一下 [**註冊的項目**] 索引標籤。

2. 在下拉式功能表中選擇工作負載類型 [**Azure 虛擬機器**]，按一下 [選取] 按鈕。![選取工作負載][select-workload]

3. 按一下頁面底部的 [**註冊**] 按鈕。![註冊按鈕][register-button]

4. 在 [**註冊的項目**] 快顯功能表中，選擇您想要註冊的虛擬機器。如果有兩個以上同名的虛擬機器，請使用雲端服務來區別虛擬機器。

  	**註冊**作業可以大規模進行，也就是可以一次選取多個要註冊的虛擬機器。如此可大幅減少為了準備虛擬機器來備份而投入的一次性工作量。

  	> [AZURE.NOTE]這裡只會顯示未註冊且與備份保存庫位於相同區域的虛擬機器。

  	![要註冊的 VM 清單][register-vms]

5. 每一個應該註冊的虛擬機器都會建立一項工作。快顯通知會顯示此活動的狀態。按一下 [**檢視工作**] 以移至 [**工作**] 頁面。![註冊工作][register-job]

6. 虛擬機器也會出現在註冊的項目清單中，還會顯示註冊作業的狀態。![註冊狀態 1][register-status1]

7. 作業完成時，入口網站中的狀態會改變來反映已註冊的狀態。![註冊狀態 2][register-status2]

<br><br>
## 備份 Azure 虛擬機器
此步驟涉及到設定虛擬機器的備份和保留原則。若要保護虛擬機器，請執行下列步驟：

1. 瀏覽至備份保存庫 (位於 Azure 入口網站的 [**復原服務**] 下)，按一下 [**註冊的項目**] 索引標籤。

2. 在下拉式功能表中選擇工作負載類型 [**Azure 虛擬機器**]，按一下 [選取] 按鈕。![在入口網站中選取工作負載][select-workload]

3. 按一下頁面底部的 [**保護**] 按鈕。

4. 這樣會開啟 [**保護項目**] 精靈，供您選取要保護的虛擬機器。如果有兩個以上同名的虛擬機器，請使用雲端服務來區別虛擬機器。**保護**作業可以大規模進行，也就是可以一次選取多個要註冊的虛擬機器。如此可大幅減少為了保護虛擬機器而投入的工作量。

	> [AZURE.NOTE]這裡只會顯示已向 Azure 備份服務正確註冊且與備份保存庫位於相同區域的虛擬機器。

	![選取要保護的項目][protect-wizard1]

5. 在 [**保護項目**] 精靈的第二個畫面，選擇備份和保留原則來備份已選取的虛擬機器。從現有的一組原則中挑選，或定義新的原則。

	> [AZURE.NOTE]在預覽版本中，支援最多 30 天的保留期和每天最多備份一次。

	![選取保護原則][protect-wizard2]

	在每個備份保存庫中，您可以有多個備份原則。原則反映應該如何排程和保留備份的詳細資料。例如，一個備份原則可能是每天上午 10:00 備份，另一個備份原則可能是每週上午 6:00 備份。多個備份原則可讓虛擬機器基礎結構在排定備份時更有彈性。每一個備份原則可以有多個相關聯的虛擬機器。無論何時，虛擬機器只能與一個原則相關聯。

6. 每個虛擬機器會建立一項工作，以設定保護原則並將虛擬機器與原則相關聯。按一下 [**工作**] 索引標籤，選擇正確的篩選器來檢視 [**設定保護**] 工作清單。![設定保護工作][protect-configure]

7. 完成之後，虛擬機器就受到原則保護，必須等到排程的備份時間讓初始備份完成為止。現在，虛擬機器出現在 [**受保護的項目**] 索引標籤下，且 [保護狀態] 為 [*受保護*] (擱置中的初始備份)。

	> [AZURE.NOTE]目前還無法在設定保護之後立即啟動初始備份。

8. 在排程時間，Azure 備份服務會為每個需要備份的虛擬機器建立備份工作。按一下 [**工作**] 索引標籤來檢視 [**備份**] 工作清單。在備份工作進行時，Azure 備份服務會發出命令給每個虛擬機器中的備份擴充功能，以排清所有寫入並取得一致的快照。![備份進行中][protect-inprogress]

9. 完成之後，[**受保護的項目**] 索引標籤中的虛擬機器 [保護狀態] 會顯示成 [*受保護*]。![搭配復原點備份虛擬機器][protect-backedup]


## 檢視備份狀態和詳細資料
虛擬機器受到保護後，[**儀表板**] 頁面摘要中的虛擬機器計數也會遞增。此外，[儀表板] 頁面還會顯示過去 24 小時內成功、失敗和仍在進行中的工作數目。按一下任何一個類別可在 [**工作**] 頁面中深入查看該類別。![儀表板頁面中的備份狀態][dashboard]


## 錯誤疑難排解
您可以利用下列資訊，對探索和註冊問題進行疑難排解。

| 備份作業 | 錯誤詳細資料 | 因應措施 |
| -------- | -------- | -------|
| 探索 | 無法探索新的項目 - Microsoft Azure 備份發生內部錯誤。等候幾分鐘的時間，然後再次嘗試操作。 | 在 15 分鐘之後重試探索程式。
| 探索 | 無法探索新的項目 – 另一個探索作業正在進行中。請等待目前的探索作業完成。 | 等待現有的探索作業完成。 |
| 註冊 | Azure VM 角色未處於可安裝擴充功能的狀態 - 請檢查 VM 是否處於「執行中」狀態。Azure 探索服務擴充功能需要 VM 處於「執行中」。 | 啟動虛擬機器，當它處於「執行中」狀態時，重試註冊作業。|


## 復原點的一致性
處理備份資料時，客戶會擔心 VM 還原後的行為。客戶常見的問題包括：

+ 虛擬機器會啟動嗎？

+ 資料會存在磁碟上嗎？會遺失任何資料嗎？

+ 應用程式能夠讀取資料嗎？資料會損毀嗎？

+ 應用程式還可辨識資料嗎？應用程式讀取的資料仍保有自我一致性嗎？

下表說明 Azure VM 備份和還原期間發生的一致性類型：

| 一致性 | VSS 型 | 說明和詳細資料 |
|-------------|-----------|---------|
| 應用程式一致性 | 是 | 這是 Microsoft 工作負載的理想位置，因為可確保：<ol><li>VM *啟動*、<li>*不會損毀*、<li>不會*遺失資料*，以及<li>對於使用資料的應用程式，資料維持一致，亦即備份時將應用程式納入考量 - 使用 VSS。</ol> 磁碟區快照服務 (VSS) 確保資料正確寫入儲存體。大部分 Microsoft 工作負載都有 VSS 寫入器，負責執行與資料一致性有關的工作負載特定動作。例如，Microsoft SQL Server 的 VSS 寫入器可確保正確寫入交易記錄檔和資料庫。<br><br> 對於 Azure VM 備份，取得應用程式一致復原點表示備份擴充功能可以叫用 VSS 工作流程，並在取得 VM 快照之前*正確*完成。當然，這表示也都已叫用 Azure VM 中所有應用程式的 VSS 寫入器。<br><br>了解 [VSS 的基本知識](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx)深入[運作方式](https://technet.microsoft.com/zh-tw/library/cc785914%28v=ws.10%29.aspx)的詳細資料。 |
| 檔案系統一致性 | 是 - 適用於 Windows 電腦 | 有兩種情況，復原點具有檔案系統一致性：<ul><li>在 Azure 中備份 Linux VM，因為 Linux 沒有相當於 VSS 的平台。<li>在 Azure 中備份 Windows VM 期間 VSS 失敗。</li></ul> 在這兩種情況下，最佳做法是確保：<ol><li>VM *啟動*、<li>不會*損毀*和<li>不會*遺失資料*。</ol> 應用程式需要在還原的資料上實作自己的「修正」機制。|
| 損毀一致性 | 否 | 此狀況相當於電腦「當機」(經由軟體重設或硬體重設)。無法保證儲存媒體上的資料一致性。只有備份時已存在磁碟上的資料才會擷取並備份。<ol><li>在大多數情況下，OS 會啟動，但不保證一定如此。<li>這通常會接著進行磁碟檢查程序，例如 chkdsk，以檢查任何損毀錯誤。<li> 記憶體中任何未完全排清到磁碟的資料或寫入將會遺失。<li> 如果需要進行資料復原，應用程式通常會接著進行其本身的驗證機制。</ol>對於 Azure VM 備份，取得損毀一致復原點表示 Azure 備份不保證儲存體上的資料一致性 - 無論從 OS 還是應用程式的觀點來說都一樣。這通常發生在備份時 Azure VM 關閉。<br><br>例如，如果交易記錄中有項目不存在資料庫中，則資料庫軟體會執行復原，直到資料變成一致為止。在處理跨多個虛擬磁碟的資料時 (例如跨距磁碟區)，損毀一致復原點不保證資料的正確性。|



## 後續步驟
若要深入了解開始使用 Azure 備份，請參閱：

- [還原虛擬機器](backup-azure-restore-vms.md)

[three-steps]: ./media/backup-azure-vms/3-steps-for-backup.png
[select-workload]: ./media/backup-azure-vms/discovery-select-workload.png
[discover-button]: ./media/backup-azure-vms/discover-button.png
[discover-vms]: ./media/backup-azure-vms/discovering-vms.png
[discover-done]: ./media/backup-azure-vms/discovery-complete.png
[register-button]: ./media/backup-azure-vms/register-button.png
[register-job]: ./media/backup-azure-vms/register-create-job.png
[register-vms]: ./media/backup-azure-vms/register-popup.png
[register-status1]: ./media/backup-azure-vms/register-status01.png
[register-status2]: ./media/backup-azure-vms/register-status02.png
[select-workload]: ./media/backup-azure-vms/select-workload.png
[protect-wizard1]: ./media/backup-azure-vms/protect-wizard1.png
[protect-wizard2]: ./media/backup-azure-vms/protect-wizard2.png
[protect-configure]: ./media/backup-azure-vms/protect-configureprotection.png
[protect-inprogress]: ./media/backup-azure-vms/protect-inprogress.png
[protect-backedup]: ./media/backup-azure-vms/protect-backedupvm.png
[dashboard]: ./media/backup-azure-vms/dashboard-protectedvms.png
[prereq]: http://azure.microsoft.com/documentation/articles/backup-azure-vms-introduction/#prerequisites

<!--HONumber=54-->