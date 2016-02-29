<properties 
   pageTitle="檢視和管理 StorSimple Virtual Array 作業 | Microsoft Azure"
   description="說明 StorSimple Manager 服務作業頁面，以及如何使用該頁面來追蹤 StorSimple Virtual Array 的最近和目前作業。"
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
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="v-sharos" />

# 針對 StorSimple Virtual Array 使用 StorSimple Manager 服務檢視作業 (預覽)

## 概觀

[作業] 頁面提供單一中央入口網站，可針對連接到 StorSimple Manager 服務的 Virtual Arrays (也稱為內部部署虛擬裝置) 上啟動的作業，進行檢視和管理。您可以針對多個虛擬裝置，檢視執行中、完成和失敗的作業。結果會以表格式格式呈現。

![[工作] 頁面](./media/storsimple-ova-manage-jobs/ovajobs1.png)

您可以透過篩選欄位，快速找到您所感興趣的工作，例如：

- **狀態** – 您可以搜尋全部、執行中、完成或已取消的作業。
- **起迄** – 工作可以根據日期和時間範圍進行篩選。
- **類型** – 作業類型可以是全部、備份、還原、容錯移轉、下載更新，或安裝更新。
- **裝置** – 作業會在連線到服務的特定裝置上進行初始化。篩選的工作接著會根據下列屬性製成表格：

    - **類型** – 作業類型可以是全部、備份、還原、容錯移轉、下載更新，或安裝更新。

    - **狀態** – 作業可以是全部、執行中、完成或失敗。

    - **實體** – 作業可以與磁碟區、共用或裝置相關聯。

    - **裝置** – 用來啟動工作之裝置的名稱。

    - **啟動於** – 啟動工作的時間。

    - **進度** – 執行中工作的完成度百分比。對於完成的工作，百分比應該永遠是 100%。

工作清單每隔 30 秒會重新整理。

## 檢視工作詳細資料

執行下列步驟來檢視任何工作的詳細資料。

#### 若要檢視工作詳細資料

1. 在 [工作] 頁面上，透過搭配適當的篩選器執行查詢來顯示您所感興趣的工作。您可以搜尋完成或執行中作業。

2. 從作業表格式清單中選取作業。

3. 按一下頁面底部的 [詳細資料]。

4. 在 [詳細資料] 對話方塊中，您可以檢視狀態、詳細資料和時間統計資料。下圖顯示 [備份作業詳細資料] 對話方塊的範例。
 
    ![工作詳細資料頁面](./media/storsimple-ova-manage-jobs/ovajobs2.png)

## 後續步驟

[了解如何使用本機 Web UI 來管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

<!---HONumber=AcomDC_0218_2016-->