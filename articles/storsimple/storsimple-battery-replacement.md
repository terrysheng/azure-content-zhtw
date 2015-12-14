<properties 
   pageTitle="更換 StorSimple 裝置上的電池 | Microsoft Azure"
   description="描述如何取下、更換和維護 StorSimple 裝置上的備份電池模組。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/02/2015"
   ms.author="alkohli" />

# 更換 StorSimple 裝置上的備份電池模組

## 概觀

Microsoft Azure StorSimple 裝置上的主要機箱電源和冷卻模組 (PCM) 具有額外的電池組。這個電池組會提供電源，以便如果主要機箱失去 AC 電源，StorSimple 裝置可以儲存資料。這個電池組稱為*備份電池模組*。備份電池模組僅針對 StorSimple 裝置中的主要機箱而存在 (EBOD 機箱未包含備份電池模組) 。

本教學課程說明如何：

- 取下備份電池模組 
- 安裝新的備份電池模組
- 維護備份電池模組

>[AZURE.IMPORTANT]取下及更換備份電池模組之前，請閱讀 [StorSimple 硬體元件更換](storsimple-hardware-component-replacement.md)中的安全資訊。

## 取下備份電池模組

Microsoft Azure StorSimple 裝置的備份電池模組是現場可置換裝置。在 PCM 中安裝它之前，電池模組應該儲存在其原始包裝中。

#### 若要取下備份電池模組

1. 在 Azure 傳統入口網站中，導覽到 [裝置] > [維護] > [硬體狀態]。在 [**共用元件**] 下，查看電池狀態。

2. 識別電池故障的 PCM。圖 1 顯示 StorSimple 裝置的背面。

    ![裝置主要機箱模組的後擋板](./media/storsimple-battery-replacement/IC740994.png)

    **圖 1**顯示 PCM 和控制器模組的主要裝置背面

    |標籤|說明|
    |:----|:----------|
    |1|PCM 0|
    |2|PCM 1|
    |3|控制器 0|
    |4|控制器 1|

    如圖 2 中的號碼 3 所示，PCM 0 上對應到**電池故障**的監視指示器 LED 應該亮起。

    ![裝置後擋板 PCM 監視 LED 指示燈](./media/storsimple-battery-replacement/IC740992.png)

    **圖 2** 顯示監視指示器 LED 的 PCM 背面

    |標籤|說明|
    |:---|:-----------|
    |1|AC 電源故障|
    |2|風扇故障|
    |3|電池故障|
    |4|PCM 正常|
    |5|DC 電源故障|
    |6|電池狀況良好|

3. 若要取下電池故障的 PCM，請遵循[取下 PCM](storsimple-power-cooling-module-replacement.md#remove-a-pcm) 中的步驟。

4. 一旦取下 PCM，請提起並向上旋轉電池模組把手 (如下圖中所示)，並將它拔出以取下電池。

    ![從 pcm 取出電池](./media/storsimple-battery-replacement/IC741019.png)

    **圖 3** 從 PCM 取下電池

5. 將模組放置在現場可更換裝置包裝中。

6. 將故障裝置退回給 Microsoft，以取得適當的服務和處理。

## 安裝新的備份電池模組

執行下列步驟，以在 StorSimple 裝置的主要機箱中安裝更換電池模組。

#### 若要安裝電池模組

1. 以適當的方向將備份電池模組放入 PCM 中。

2. 全力按壓電池模組把手以固定連接器。

3. 依照[更換 StorSimple 裝置上的電源和冷卻模組](storsimple-power-cooling-module-replacement.md)中的指導方針，更換主要機箱的 PCM。

4. 更換完成之後，存取管理入口網站，並導覽至 [裝置] > [維護] > [硬體狀態]，並確認電池狀態，以確保安裝成功。如果 LED 顯示綠色狀態，則表示電池狀況良好。

## 維護備份電池模組

在裝置中，備份電池模組會在停電期間提供電源給控制器。它可讓 StorSimple 裝置在以控制方式關閉之前儲存重要資料。由於 PCM 中有兩個完全充電的電池，系統可以處理兩個連續停電事件。

在管理入口網站中，[維護] 頁面上的 [硬體狀態] 指出電池是否故障，或電池何時即將用光。在[**共用元件**] 下，電池狀態是以 [**PCM 1 中的電池**] 或 [**PCM 0 中的電池**] 指出。此頁面會顯示 [**降級**] 狀態，表示電池即將用光，以及顯示 [**故障**]，表示電池已用光。

>[AZURE.NOTE]當電池只需充電時，可以報告 [**故障**]。
 
如果 [**降級**] 狀態出現，我們建議採取下列動作：

- 系統最近可能遭遇停電，或電池可能正在進行定期維護。觀察系統 12 小時，再繼續進行。

    - 在連續 12 小時連接至 AC 電源，而且控制器與 PCM 正在執行之後，如果狀態仍為 [**降級**]，則需要更換電池。請[連絡 Microsoft 支援](storsimple-contact-microsoft-support.md)，以取得更換備份電池模組。

    - 如果狀態在 12 小時之後變成良好，則電池可操作，而且只需維護費用。

- 如果沒有相關聯的 AC 電源喪失，而且 PCM 已開啟並連接至 AC 電源，則電池需要更換。請[連絡 Microsoft 支援](storsimple-contact-microsoft-support.md)，以訂購更換備份電池模組。

>[AZURE.IMPORTANT]依據國家及地區法規處置故障電池。

## 後續步驟

深入了解 [StorSimple 硬體元件更換](storsimple-hardware-component-replacement.md)。

<!---HONumber=AcomDC_1203_2015-->