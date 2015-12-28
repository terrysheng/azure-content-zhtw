<properties 
   pageTitle="從備份還原 StorSimple 磁碟區 | Microsoft Azure"
   description="說明如何使用 StorSimple Manager 的 [備份類別目錄] 頁面，從備份組還原 StorSimple 磁碟區。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/14/2015"
   ms.author="v-sharos" />

# 從備份組還原 StorSimple 磁碟區

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## 概觀

[備份類別目錄] 頁面會顯示在產生手動或自動備份時建立的所有備份組。您可以使用此頁面來列出備份原則或磁碟區的所有備份、選取或刪除備份，或是使用備份來還原或複製磁碟區。

 ![備份類別目錄頁面](./media/storsimple-restore-from-backup-set-u2/HCS_BackupCatalog.png)

本教學課程說明如何使用 [備份類別目錄] 頁面，從備份組還原您的裝置。

您可以從本機或雲端備份還原磁碟區。在任一情況下，當資料在背景下載時，還原作業會立即讓磁碟區連線。

在您啟動還原作業之前，您應該注意下列事情：

- **必須使磁碟區離線** – 起始還原作業之前，同時讓主機和裝置上的磁碟區離線。雖然還原作業會自動使裝置上的磁碟區連線，但您必須手動讓主機上的裝置連線。在裝置上的磁碟區連線之後，您就可以立即讓主機上的磁碟區連線。(不需要等到還原作業完成。) 如需程序，請移至[使磁碟區離線](storsimple-manage-volumes.md#take-a-volume-offline)

- **還原後的磁碟區類型** – 已刪除的磁碟區會根據快照集中的類型還原；亦即，固定在本機的磁碟機會還原為固定在本機的磁碟機，而分層磁碟機會還原為分層磁碟機。

    針對現有的磁碟區，磁碟機目前的使用類型會覆寫快照集中所還原的類型。例如，如果您從原本磁碟區類型為分層，而現在磁碟區類型為固定在本機 (因為已執行的轉換作業) 時所採取的快照集還原磁碟區，則磁碟區會還原成固定在本機的磁碟區。同樣地，如果現有的固定在本機的磁碟區已擴充，且後續從過去磁碟區仍較小的舊快照集還原，還原的磁碟區將會保留目前擴充的大小。

    當磁碟區正在還原時，您無法從分層磁碟區轉換成固定在本機的磁碟區，或從固定在本機的磁碟區轉換成分層磁碟區。請等候還原作業完成，您就能將磁碟區轉換為其他類型。如需轉換磁碟區的詳細資訊，請移至[變更磁碟區類型](storsimple-manage-volumes-u2.md#change-the-volume-type)。

- **磁碟區大小會反映在還原的磁碟區** – 如果您要還原已刪除的固定在本機的磁碟區，這是很重要的考量 (因為固定在本機的磁碟區已完整佈建)。請確定您有足夠的空間，然後再嘗試還原之前已刪除的固定在本機的磁碟區。

- **正在還原時，您無法擴充磁碟區** – 等候還原作業完成，才能嘗試擴充磁碟區。如需有關擴充磁碟區的資訊，請移至[修改磁碟區](storsimple-manage-volumes-u2.md#modify-a-volume)。

- **還原本機磁碟區時，您可以執行備份** – 如需程序，請移至[使用 StorSimple Manager 服務來管理備份原則](storsimple-manage-backup-policies.md)。

- **可以取消還原作業** – 如果您取消還原工作，磁碟區將會回復到起始還原作業之前的狀態。如需程序，請移至[取消工作](storsimple-manage-jobs.md#cancel-a-job)。

## 如何使用備份類別目錄

[備份類別目錄] 頁面提供查詢，可協助您縮小備份組選取範圍。您可以篩選根據下列參數擷取的備份組：

- **裝置** - 建立備份組所在的裝置。
- **備份原則** 或 **磁碟區** - 與此備份組相關聯的備份原則或磁碟區。
- **從** 和 **至** - 建立備份組的日期和時間範圍。

接著會根據下列屬性，將篩選的備份組列表顯示：

- **名稱** - 與備份組相關聯的備份原則或磁碟區的名稱。
- **大小** - 備份組的實際大小。
- **建立日期** - 建立備份的日期和時間。 
- **類型** - 備份組可以是本機快照集或雲端快照集。本機快照是本機儲存於裝置上的所有磁碟區資料備份，而雲端快照是指位於雲端的磁碟區資料備份。本機快照可提供更快速的存取，而雲端快照是選擇來進行資料復原。
- **起始者** - 備份可根據排程自動初始，或由使用者手動初始。(您可以使用備份原則來排程備份。或者，可以使用 [取得備份] 選項來取得互動式備份。)

## 如何從備份還原您的 StorSimple 磁碟區

您可以使用 [備份類別目錄] 頁面，從特定的備份還原 StorSimple 磁碟區。不過，請記住，還原磁碟區會將磁碟區的狀態還原為其在取得備份時的狀態。在備份作業之後新增的所有資料都將遺失。

> [AZURE.WARNING]從備份還原將從備份取代現有的磁碟區。這可能會造成在取得備份之後寫入的所有資料遺失。

### 還原您的磁碟區

1. 在 StorSimple Manager 服務頁面上，按一下 [備份類別目錄] 索引標籤。

    ![備份類別目錄](./media/storsimple-restore-from-backup-set-u2/HCS_Restore.png)

2. 選取備份組，如下所示：
  1. 選取適當的裝置。
  2. 在下拉式清單中，針對要選取的備份選擇磁碟區或備份原則。
  3. 指定時間範圍。
  4. 按一下核取圖示 ![核取圖示](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) 以執行此查詢。
 
    與選取的磁碟區或備份原則相關聯的備份應該會出現在備份組清單中。

3. 展開備份組以檢視相關聯的磁碟區。您必須先在主機和裝置上將這些磁碟區離線，才能還原它們。存取 [磁碟區容器] 頁面上的磁碟區，然後依照 [讓磁碟區離線][](storsimple-manage-volumes-u2.md#take-a-volume-offline) 中的指示來讓它們離線。

    > [AZURE.IMPORTANT]確定您已先讓主機上的磁碟區離線，然後再讓裝置上的磁碟區離線。如果您並未讓主機上的磁碟區離線，可能會導致資料損毀。

4. 瀏覽回到 [備份類別目錄] 索引標籤，並選取備份組。

5. 按一下頁面底部的 [還原]。

6. 系統將提示您進行確認。檢閱還原資訊，然後選取 [確認] 核取方塊。

    ![確認電子郵件](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)

7. 按一下核取圖示 ![核取圖示](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png)。這將會初始還原工作，而您可以藉由存取 [工作] 頁面來進行檢視。

8. 還原完成之後，您可以確認磁碟區的內容已由備份的磁碟區所取代。

![提供的影片](./media/storsimple-restore-from-backup-set-u2/Video_icon.png)**提供的影片**

若要觀看影片示範如何使用 StorSimple 的複製和還原功能，將已刪除的檔案復原，請按一下[這裡](http://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/)。

## 如果還原失敗

如果還原作業因為任何原因而失敗，您會收到警示。如果發生這種情況，請重新整理備份清單，以確認備份仍然有效。如果備份有效，且您是從雲端還原，則連線問題可能會造成問題。

若要完成還原作業，請使主機上的磁碟區離線，然後重試還原作業。請注意，在還原程序期間所執行的磁碟區資料修改將會遺失。

## 後續步驟

- 了解如何[管理 StorSimple 磁碟區](storsimple-manage-volumes-u2.md)。

- 了解如何[使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration-u2.md)。

<!---HONumber=AcomDC_1217_2015-->