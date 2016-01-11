<properties 
   pageTitle="管理您的 StorSimple 備份原則 | Microsoft Azure"
   description="說明如何使用 StorSimple Manager 服務建立並管理手動備份、備份排程與備份保留。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/14/2015"
   ms.author="v-sharos"/>

# 使用 StorSimple Manager 服務管理備份原則 (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## 概觀

本教學課程說明如何使用 StorSimple Manager 服務的 [備份原則] 頁面控制 StorSimple 磁碟區的備份程序和備份保留。它也會說明如何完成手動備份。

在您備份磁碟區時，您可以選擇建立本機快照或雲端快照。如果您正在備份固定在本機的磁碟區，我們建議您指定雲端快照。拍下大量的本機快照，再加上具大量變換的資料集，將會導致本機空間很快就不足的情況。如果您選擇建立本機快照，我們建議您拍下較少量的每日快照來備份最新的狀態，加以保留一天後再予以刪除。

針對固定在本機的磁碟區，在拍下雲端快照時，您僅會將變更的資料複製到雲端，並在其中將重複項目刪除及壓縮快照。

## 備份原則頁面

[備份原則] 頁面可讓您管理備份原則並排程本機和雲端快照 (備份原則用來設定磁碟區集合的備份排程和備份保留)。 備份原則可讓您同時建立多個磁碟區的快照。這表示備份原則所建立的備份將會是與當機時一致的複本。**備份原則頁面**會列出備份原則、其類型、相關聯的磁碟區、保留的備份數目，以及啟用這些原則的選項。

[備份原則] 頁面也可讓您依下列一個或多個欄位，篩選現有的備份原則：

- **原則名稱** – 與原則相關聯的名稱。不同類型的原則包括：

   - 已排程的原則 (由使用者明確建立)。
   - 自動原則 (建立磁碟區時啟用此磁碟區選項的預設備份時所建立)。這些原則會被命名為 *VolumeName*\_Default，其中 *VolumeName* 指的是 Azure 傳統入口網站中使用者所設定之 StorSimple 磁碟區的名稱。自動原則會導致每日雲端快照於 22:30 裝置時間開始。
   - 匯入的原則 (原先是在 StorSimple Snapshot Manager 中所建立)。這些包含說明從中匯入原則之 StorSimple Snapshot Manager 主機的標記。

- **磁碟區** – 與原則相關聯的磁碟區。建立備份時，會將與備份原則相關聯的所有磁碟區群組在一起。

- **上一次成功的備份** – 使用此原則所進行的上一次成功備份的日期和時間。

- **下一次備份** – 此原則將起始的下一次排定的備份的日期和時間。

- **排程** – 與備份原則相關聯的排程數目。

您可以從這個頁面執行的常用作業包括：

- 新增備份原則 
- 新增或修改排程 
- 刪除備份原則 
- 進行手動備份 
- 建立具有多個磁碟區和排程的自訂備份原則 

## 新增備份原則

新增備份原則，以自動排程備份。在 Azure 傳統入口網站中執行下列步驟，以便為 StorSimple 裝置新增備份原則。加入原則之後，您可以定義排程 (請參閱[加入或修改排程](#add-or-modify-a-schedule))。

[AZURE.INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![提供的影片](./media/storsimple-manage-backup-policies-u2/Video_icon.png)**提供的影片**

若要觀看影片示範如何建立本機或雲端備份原則，請按一下[這裡](http://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/)。


## 新增或修改排程

您可以在 StorSimple 裝置上新增或修改附加到現有備份原則的排程。在 Azure 傳統入口網站中執行下列步驟，以新增或修改排程。

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## 刪除備份原則

在 Azure 傳統入口網站中執行下列步驟，以便刪除 StorSimple 裝置上的備份原則。

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## 進行手動備份

在 Azure 傳統入口網站中執行下列步驟，以針對單一磁碟區建立隨選 (手動) 備份。

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## 建立具有多個磁碟區和排程的自訂備份原則

在 Azure 傳統入口網站中執行下列步驟，以建立具有多個磁碟區和排程的自訂備份原則。

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy-u2.md)]


## 後續步驟

- 深入了解[使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

<!---HONumber=AcomDC_1223_2015-->