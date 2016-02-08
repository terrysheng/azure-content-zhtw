<properties 
   pageTitle="StorSimple Virtual Array 備份教學課程 |Microsoft Azure"
   description="說明如何備份 StorSimple Virtual Array 共用與磁碟區。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/22/2016"
   ms.author="alkohli" />

# 備份 StorSimple Virtual Array (預覽)

## 概觀 

本教學課程僅適用於執行 1.1.1.0 版 (預覽) 的 Microsoft Azure StorSimple Virtual Array (也稱為 StorSimple 內部部署虛擬裝置或 StorSimple 虛擬裝置)。

StorSimple Virtual Array 是混合式雲端儲存體內部部署虛擬裝置，可設定為檔案伺服器或 iSCSI 伺服器。它可以建立備份、從備份還原，且當需要災害復原時可執行裝置容錯移轉。設為檔案伺服器時，也可進行項目層級的復原。本教學課程說明如何使用 Azure 傳統入口網站或 StorSimple Web UI 來建立 StorSimple Virtual Array 的排程及手動備份。

>[AZURE.IMPORTANT] 
>
>- StorSimple Virtual Array 目前是預覽版，僅供評估及部署規劃之用。不支援在生產環境中安裝此預覽版。 
>- 如果您遇到任何 StorSimple Virtual Array 的問題，請在 [StorSimple MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple)提出問題。

## 備份共用和磁碟區

備份可提供共用和磁碟區的時間點保護、改善復原能力，同時讓還原時間降至最低。您有兩種方法可以備份 StorSimple 裝置上的共用或磁碟區：「排程」或「手動」。下列各節討論上述每一種方法。

> [AZURE.NOTE] 在此版本中，排程的備份由預設原則建立，該原則會每日在特定時間執行並備份裝置上的所有共用或磁碟區。目前無法建立用於排程備份的自訂原則。

## 變更備份排程

您的 StorSimple 虛擬裝置有預設的備份原則，會在每日的特定時間 (22:30) 開始並備份裝置上所有共用或磁碟區。您可以變更備份開始的時間，但無法變更頻率及保留期 (指定備份保留的數量)。在備份時，會備份整部虛擬機器，因此建議您將備份排程在離峰時段。

在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中執行下列步驟，以變更預設的備份開始時間。

#### 變更預設備份原則的開始時間

1. 瀏覽到裝置的 [設定] 索引標籤。

2. 在 [備份] 區段，指定每日備份的開始時間。

3. 按一下 [儲存]。

### 進行手動備份

除了排程備份之外，您隨時可以進行手動 (指定) 備份。

#### 建立手動 (指定) 備份

1. 瀏覽至 [共用] 索引標籤或 [磁碟區] 索引標籤。

2. 按一下頁面底部的 [全部備份]。系統會提示您以確認要立即開始備份。按一下核取圖示 ![核取圖示](./media/storsimple-ova-backup/image3.png) 以繼續備份。

    ![確認備份](./media/storsimple-ova-backup/image4.png)

    系統會通知您正在啟動備份作業。

    ![正在啟動備份](./media/storsimple-ova-backup/image5.png)

    系統會通知您已成功建立作業。

    ![已建立備份工作](./media/storsimple-ova-backup/image7.png)

3. 若要追蹤作業進度，按一下 [檢視作業] 圖示。

4. 備份工作完成之後，請移至 [備份類別目錄] 索引標籤。您應該會看到完成的備份。

    ![已完成的備份](./media/storsimple-ova-backup/image8.png)

5. 將篩選選取項目設定為適當的裝置、備份原則和時間範圍，然後選取核取圖示 ![核取圖示](./media/storsimple-ova-backup/image3.png)。

    備份應該會出現在類別目錄中顯示的備份組清單中。

## 檢視現有備份

請在 Azure 傳統入口網站中執行下列步驟，以檢視現有備份。

#### 檢視現有備份

1. 在 StorSimple Manager 服務頁面上，按一下 [備份類別目錄] 索引標籤。

2. 選取備份組，如下所示：

    1. 選取裝置。

    2. 在下拉式清單中，針對要選取的備份選擇共用或磁碟區。

    3. 指定時間範圍。

    4. 按一下核取圖示 ![](./media/storsimple-ova-backup/image3.png) 以執行此查詢。

    與選取的共用或磁碟區相關的備份應會顯示在備份組清單中。

![video\_icon](./media/storsimple-ova-backup/video_icon.png)**提供的影片**

觀看影片以了解如何在 StorSimple Virtual Array 上建立共用、備份共用和還原資料。

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## 後續步驟

深入了解[如何管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

<!---HONumber=AcomDC_0128_2016-->