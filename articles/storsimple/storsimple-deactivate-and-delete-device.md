<properties 
   pageTitle="停用及刪除 StorSimple 裝置 | Microsoft Azure"
   description="描述如何停用然後刪除 StorSimple 裝置，以從服務將其移除。"
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/12/2015"
   ms.author="v-sharos" />

# 停用及刪除 StorSimple 裝置

本教學課程說明如何停用然後刪除 StorSimple 裝置以從服務移除它。

>[AZURE.NOTE]在刪除裝置之前，您必須先停用裝置。

## 停用裝置

您可能希望從服務停用裝置。在此情況下，裝置必須停用。停用會切斷裝置與相對應 StorSimple Manager 服務之間的連接。

>[AZURE.WARNING]停用是永久性的作業，而且無法復原。停用的服務無法向 StorSimple Manager 服務登錄，除非由原廠預先重設。

當您停用裝置時，將無法再存取以本機方式儲存在裝置上的任何資料。只有儲存於雲端之裝置的相關聯資料可以復原。一旦停用，裝置必須由原廠重設，才能利用現有的或新的服務加以使用。原廠重設程序會刪除以本機方式儲存在裝置上的所有資料。因此，在您停用裝置之前，您必須擷取所有資料的雲端快照集。這樣可讓您在稍後的階段中復原所有的資料。

對於 StorSimple 虛擬裝置，停用會在佈建時刪除已建立的虛擬機器和資源。停用虛擬裝置之後，就無法將它還原為先前的狀態。停用 StorSimple 虛擬裝置之前，請務必停止或刪除依賴該虛擬裝置的用戶端和主機。

### 停用及刪除資料

如果您有興趣完全刪除裝置，而且不想要保留裝置資料，請執行下列動作：

1. 在停用裝置之前，您必須刪除和裝置相關聯的所有磁碟區容器 (和磁碟區)。在刪除相關聯的備份之後，您只能刪除磁碟區容器。

2. 停用裝置。移至[停用步驟](#steps-to-deactivate)以了解相關指示。

3. 停用之後，您就可以完全刪除裝置。移至[刪除裝置](#delete-a-device)以了解相關指示。

### 停用並保留資料

如果您有興趣刪除裝置，但想要保留裝置資料，請執行下列動作：

1. 停用裝置。裝置的所有磁碟區容器及快照集都會保留。移至[停用步驟](#steps-to-deactivate)以了解相關指示。

2. 您現在可以容錯移轉磁碟區容器和相關聯的快照集。關於程序，請移至[StorSimple 裝置的容錯移轉和災害復原](storsimple-device-failover-disaster-recovery.md)。

3. 停用和容錯移轉之後，您就可以完全刪除裝置。移至[刪除裝置](#delete-a-device)以了解相關指示。

### 停用步驟

使用下列程序來停用裝置，以準備刪除它。

#### 停用裝置

1. 在 StorSimple Manager 服務**裝置**頁面上，選取您想要停用的裝置，並在頁面底部按一下 [**停用**]。

2. 確認訊息隨即出現。按一下 [是] 以繼續。停用程序將會啟動，並需要幾分鐘才能完成。

    在 StorSimple 虛擬裝置上，停用會導致下列動作：

      - StorSimple 虛擬裝置會移除。

      - 為虛擬裝置建立的 OSDisk 和資料磁碟會移除。

      - 在佈建期間建立的託管服務和虛擬網路會保留。如果您不使用這些項目，就應該手動加以刪除。

      - StorSimple 虛擬裝置所建立的雲端快照集會保留。

<!--After the device is deactivated, you will need to perform a failover before you can delete it completely. For failover instructions, go to [Failover and disaster recovery for your StorSimple device](storsimple-device-failover-disaster-recovery.md).-->
## 刪除裝置

您只能刪除已停用的裝置。刪除裝置會將其從與服務連接的裝置清單移除。服務將不再管理已刪除的裝置。

#### 刪除裝置

1. 在 StorSimple Manager 服務上**裝置**頁面上，選取您想要刪除的已停用裝置。

2. 按一下頁面底部的 [**刪除**]。

3. 系統將提示您進行確認。按一下 [是] 以繼續。

刪除裝置可能需要數分鐘的時間。

## 後續步驟
若要將已停用的裝置還原為原廠預設值，請移至[將裝置重設為原廠預設設定](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings)。

如需技術協助，[請連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。

<!---HONumber=August15_HO7-->