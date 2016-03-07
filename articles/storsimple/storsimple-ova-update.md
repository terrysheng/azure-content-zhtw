<properties 
   pageTitle="套用 StorSimple Virtual Array 更新 |Microsoft Azure"
   description="描述如何使用 StorSimple Virtual Array Web UI 來套用更新和 Hotfix"
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
   ms.date="01/13/2016"
   ms.author="alkohli" />

# 將更新和 Hotfix 套用至 StorSimple Virtual Array (預覽版)

## 概觀

您可能需要套用軟體更新或 Hotfix，以將您的 StorSimple Virtual Array 保持在最新狀態。一般情況下，我們建議您透過 Azure 傳統入口網站安裝更新。不過，當入口網站無法使用時，您可以使用本機 Web UI 來套用 Hotfix 或更新。本教學課程說明如何使用本機 Web UI 來套用更新或 Hotfix。(如需透過傳統入口網站安裝更新的程序，請移至[透過 Azure 傳統入口網站安裝一般更新](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal)。)

請記住，安裝更新或 Hotfix 可能會重新啟動您的裝置。假設 StorSimple Virtual Array 是單一節點裝置，將會中斷正在進行的任何 IO，而您的裝置會需要一些停機時間。我們也建議您在套用更新之前，讓主機上的磁碟區或共用離線，然後讓裝置離線。這樣可以讓資料損毀的可能性降至最低。

## 使用本機 Web UI 來套用更新或 Hotfix

安裝更新或 Hotfix 之前，請確定更新或 Hotfix 已下載至您主機的本機上，或是可透過網路共用存取。

#### 安裝更新或 Hotfix

1. 在本機 Web UI 中，移至 [維護] > [軟體更新]。

2. 在 [更新檔案路徑] 中，輸入更新或 Hotfix 的檔案名稱。如果更新或 Hotfix 的安裝檔案是放在網路共用上，您也可以瀏覽至該檔案。更新將會啟動。完成之後，您會收到通知。

    ![更新裝置](./media/storsimple-ova-update/image43.png)

## 後續步驟

深入了解 [administering your StorSimple Virtual Array (管理 StorSimple Virtual Array)](storsimple-ova-web-ui-admin.md)。

<!---HONumber=AcomDC_0224_2016-->