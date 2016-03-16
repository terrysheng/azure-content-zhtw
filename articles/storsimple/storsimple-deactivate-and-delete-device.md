<properties 
   pageTitle="停用及刪除 StorSimple 裝置 | Microsoft Azure"
   description="描述如何停用然後刪除 StorSimple 裝置，將其從服務中移除。"
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/29/2016"
   ms.author="v-sharos" />

# 停用及刪除 StorSimple 裝置

## 概觀

您可能會想讓某個 StorSimple 裝置停止提供服務 (舉例來說，當您在替換或升級裝置時，或是當您不再使用 StorSimple 時)。如果是如此，您將必須在刪除該裝置之前，先停用該裝置。停用會切斷裝置與相對應 StorSimple Manager 服務之間的連接。本教學課程說明如何藉由停用並刪除 StorSimple 裝置，來將該裝置從服務中移除。

當您停用裝置時，將無法再存取以本機方式儲存在裝置上的任何資料。只有儲存於雲端之裝置的相關聯資料可以復原。

>[AZURE.WARNING] 停用是永久性的作業，而且無法復原。停用的服務無法向 StorSimple Manager 服務登錄，除非由原廠預先重設。
>
>原廠重設程序會刪除以本機方式儲存在裝置上的所有資料。因此，在您停用裝置之前，您必須擷取所有資料的雲端快照集。這樣可讓您在稍後的階段中復原所有的資料。

本教學課程說明如何：

- 停用裝置並刪除資料
- 停用裝置並保留資料

它也會說明如何針對 StorSimple 虛擬裝置來進行停用及刪除作業。

>[AZURE.NOTE] 在您停用 StorSimple 實體或虛擬裝置之前，請務必停止或刪除相依於該裝置的用戶端和主機。

## 停用及刪除資料

如果您想要完全刪除裝置，且不要保留裝置中的資料，請完成下列步驟。

#### 如何停用裝置並刪除資料  

1. 在停用裝置之前，您必須刪除和裝置相關聯的所有磁碟區容器 (和磁碟區)。但您在刪除磁碟區容器之前，必須先刪除相關聯的備份。

2. 請依照下列步驟來停用裝置：

    1. 在 StorSimple Manager 服務**裝置**頁面上，選取您想要停用的裝置，並在頁面底部按一下 [**停用**]。

    2. 確認訊息隨即出現。按一下 [是] 以繼續。停用程序將會啟動，並需要幾分鐘才能完成。

3. 停用之後，您就可以完全刪除裝置。刪除裝置會將其從與服務連接的裝置清單移除。服務將不再管理已刪除的裝置。請使用下列步驟來刪除裝置：

    1. 在 StorSimple Manager 服務上**裝置**頁面上，選取您想要刪除的已停用裝置。

    2. 按一下頁面底部的 [**刪除**]。

    3. 系統將提示您進行確認。按一下 [是] 以繼續。

    刪除裝置可能需要數分鐘的時間。

## 停用並保留資料

如果您想要刪除裝置，但要保留資料，請完成下列步驟。

####如何停用裝置並保留資料 

1. 停用裝置。裝置的所有磁碟區容器及快照集都會保留。

    1. 在 StorSimple Manager 服務**裝置**頁面上，選取您想要停用的裝置，並在頁面底部按一下 [**停用**]。

    2. 確認訊息隨即出現。按一下 [是] 以繼續。停用程序將會啟動，並需要幾分鐘才能完成。

2. 您現在可以容錯移轉磁碟區容器和相關聯的快照集。關於程序，請移至[StorSimple 裝置的容錯移轉和災害復原](storsimple-device-failover-disaster-recovery.md)。

3. 停用和容錯移轉之後，您就可以完全刪除裝置。刪除裝置會將其從與服務連接的裝置清單移除。服務將不再管理已刪除的裝置。請完成下列步驟來刪除裝置：
 
    1. 在 StorSimple Manager 服務上**裝置**頁面上，選取您想要刪除的已停用裝置。

    2. 按一下頁面底部的 [**刪除**]。

    3. 系統將提示您進行確認。按一下 [是] 以繼續。

    刪除裝置可能需要數分鐘的時間。

## 停用並刪除虛擬裝置

對於 StorSimple 虛擬裝置來說，停用將會讓虛擬機器取消配置。然後您就可以刪除虛擬機器，以及刪除在佈建該虛擬機器時所建立的資源。停用虛擬裝置之後，就無法將它還原為先前的狀態。

停用會導致下列動作發生：

- StorSimple 虛擬裝置會移除。

- 為虛擬裝置建立的 OSDisk 和資料磁碟會移除。

- 在佈建期間建立的託管服務和虛擬網路會保留。如果您不使用這些項目，就應該手動加以刪除。

- StorSimple 虛擬裝置所建立的雲端快照集會保留。

## 後續步驟
- 若要將已停用的裝置還原為原廠預設值，請移至[將裝置重設為原廠預設設定](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings)。

- 如需技術協助，[請連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。

- 若要了解如何使用 StorSimple Manager，請移至[使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

<!---HONumber=AcomDC_0224_2016-->