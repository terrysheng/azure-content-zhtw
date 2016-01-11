<properties 
   pageTitle="檢視和管理 StorSimple 工作 | Microsoft Azure"
   description="說明 StorSimple Manager 服務工作頁面，以及如何使用該頁面來追蹤最近、當前和排程的備份工作。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/14/2015"
   ms.author="alkohli" />

# 使用 StorSimple Manager 服務來檢視和管理 StorSimple 工作 (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## 概觀

[工作] 頁面提供單一中央入口網站，可針對連接到 StorSimple Manager 服務的裝置，檢視及管理在裝置上啟動的工作。您可以針對多個裝置，檢視已排程、執行中、完成和失敗的工作。結果會以表格式格式呈現。

![[工作] 頁面](./media/storsimple-manage-jobs-u2/HCS_JobsPage.png)

您可以透過篩選欄位，快速找到您所感興趣的工作，例如：

- **狀態** – 工作可以是執行中、完成、已取消、失敗、取消中，或是完成但發生錯誤。
- **起迄** – 工作可以根據日期和時間範圍進行篩選。
- **類型** – 工作類型可以是備份、手動備份、還原、複製、裝置容錯移轉、建立固定在本機的磁碟區、修改磁碟區、更新、支援封裝，或佈建虛擬裝置。

- **裝置** – 工作會在連接到服務的特定裝置上進行初始化。篩選的工作接著會根據下列屬性製成表格：

    - **類型** – 備份、手動備份、還原、複製、裝置容錯移轉、建立固定在本機的磁碟區、修改磁碟區、更新、支援封裝，或佈建虛擬裝置。

    - **狀態** – 執行中、完成、已取消、失敗、取消中，或是完成但發生錯誤。

    - **實體** – 工作可以與磁碟區、備份原則或裝置相關聯。例如，複製工作與磁碟區相關聯，而排程的備份工作則與備份原則相關聯。裝置工作是透過災害復原 (DR) 或還原作業而建立。

    - **裝置** – 用來啟動工作之裝置的名稱。

    - **啟動於** – 啟動工作的時間。

    - **進度** – 執行中工作的完成度百分比。對於完成的工作，百分比應該永遠是 100%。

工作清單每隔 30 秒會重新整理。

您可以在此頁面上執行下列工作相關的動作：

- 檢視工作詳細資料

- 取消工作

## 檢視工作詳細資料

執行下列步驟來檢視任何工作的詳細資料。

#### 若要檢視工作詳細資料

1. 在 [工作] 頁面上，透過搭配適當的篩選器執行查詢來顯示您所感興趣的工作。您可以搜尋完成、執行中或已取消工作。

2. 選取一個工作。

3. 按一下頁面底部的 [詳細資料]。

4. 在 [備份工作詳細資料] 對話方塊中，您可以檢視狀態、詳細資料、時間統計資料和資料統計資料。
 
    ![工作詳細資料頁面](./media/storsimple-manage-jobs-u2/JobDetails.png)

## 取消工作

執行下列步驟來取消執行中工作。

>[AZURE.NOTE]有些工作無法取消，例如修改磁碟區以變更磁碟區類型或是展開磁碟區。

### 取消工作

1. 在 [工作] 頁面上，透過搭配適當的篩選器執行查詢來顯示您要取消的執行中工作。

1. 選取工作。

1. 按一下頁面底部的 [取消]。

1. 系統提示您進行確認時，按一下 [是]。

即會取消此工作。

## 後續步驟

- 了解如何[管理您的 StorSimple 備份原則](storsimple-manage-backup-policies.md)。

- 了解如何[使用 StorSimple Manager 服務來管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

<!---HONumber=AcomDC_1223_2015-->