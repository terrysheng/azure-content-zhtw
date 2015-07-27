<properties 
   pageTitle="在 StorSimple Snapshot Manager 使用 MMC 功能表動作 |Microsoft Azure"
   description="描述如何在 StorSimple Snapshot Manager 使用 Microsoft Management Console (MMC) 功能表動作。"
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
   ms.date="07/09/2015"
   ms.author="v-sharos" />

# 在 StorSimple Snapshot Manager 使用 MMC 功能表動作

## 概觀

在 StorSimple Snapshot Manager 中，您會看到下列所有動作列在 [**動作**] 窗格的所有動作功能表和變化上。

- 檢視
- 從這裡開啟新視窗 
- 重新整理 
- 匯出清單 
- 說明 

這些動作是 Microsoft Management Console (MMC) 的一部分，並非是 StorSimple Snapshot Manager 特有的動作。

本教學課程會描述這些動作，並說明如何在 StorSimple Snapshot Manager 使用其中每一個動作。

## 檢視

您可以使用 [**檢視**] 選項，來變更 [**結果**] 窗格檢視，以及變更主控台視窗檢視。

#### 若要變更 [結果] 窗格檢視

1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下任何節點，或展開節點並以滑鼠右鍵按一下 [**結果**] 窗格中的項目，然後按一下 [**檢視**] 選項。

3. 若要新增或移除 [**結果**] 窗格中出現的資料行，請按一下 [**新增/移除資料行**]。[**新增/移除資料行**] 對話方塊隨即出現。

    ![新增或移除 [結果] 窗格中的資料行](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png)

4. 完成表單，如下所示：

    - 從 [**可用**] 資料行清單中選取項目，然後按一下 [**新增**]，將它們新增至 [**顯示的資料行**] 清單。 

    - 按一下 [**顯示的資料行**] 清單中的項目，然後按一下 [**移除**]，從清單中移除它們。

    - 選取 [**顯示的資料行**] 清單中的項目，然後按一下 [**下移**] 或 [**下移**]，在清單中向上或向下移動項目。

    - 按一下 [**還原預設值**]，以還原為預設 [**結果**] 窗格組態。

5. 當完成您的選擇時，請按一下 [**確定**]。

#### 若要變更主控台視窗檢視

1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下任何節點，按一下 [**檢視**]，然後按一下 [**自訂**]。[**自訂**] 對話方塊隨即出現。

    ![自訂主控台視窗](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png)

3. 選取或清除核取方塊，以顯示或隱藏主控台視窗中的項目。當完成您的選擇時，請按一下 [**確定**]。

## 從這裡開啟新視窗

您可以使用 [**從這裡開啟新視窗**] 選項，以開啟新的主控台視窗。

#### 若要開啟新的主控台視窗

1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下任何節點，然後按一下 [**從這裡開啟新視窗**]。

    新的視窗隨即出現，僅顯示您所選取的範圍。比方說，如果您以滑鼠右鍵按一下 [**備份原則**] 節點中，新的視窗只會顯示 [**範圍**] 窗格中的 [**備份原則**] 節點，以及 [**結果**] 窗格中已定義的備份原則清單。請參閱下列範例。

    ![從這裡開啟新視窗](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png)
 
## 重新整理

您可以使用 [**重新整理**] 動作來更新主控台視窗。

#### 若要更新主控台視窗

1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下任何節點，或展開節點並以滑鼠右鍵按一下 [**結果**] 窗格中的項目，然後按一下 [**重新整理**]。

## 匯出清單

您可以使用 [**匯出清單**] 動作，將清單儲存為逗號分隔值 (CSV) 檔案。例如，您可以匯出備份原則清單或備份目錄。然後，您可以將 CSV 檔案匯入試算表應用程式進行分析。

#### 若要將清單儲存為逗號分隔值 (CSV) 檔案

1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。 

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下任何節點，或展開節點並以滑鼠右鍵按一下 [**結果**] 窗格中的項目，然後按一下 [**匯出清單**]。

3. [**匯出清單**] 對話方塊隨即出現。完成表單，如下所示：

    1. 在 [**檔案名稱**] 方塊中，輸入 CSV 檔案的名稱，或按一下箭頭以從下拉式清單中選取。

    2. 在 [**存檔類型**] 方塊中，按一下箭號，從下拉式清單中選取檔案類型。

    3. 若只儲存選取的項目，請選取資料列，然後按一下 [**只儲存選取的列**] 核取方塊。若要儲存所有匯出的清單，請清除 [**只儲存選取的列**] 核取方塊。

    4. 按一下 [儲存]。

    ![將清單匯出成逗號分隔值檔案](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png)
 
## 說明

您可以使用 [**說明**] 功能表，來檢視 StorSimple Snapshot Manager 和 MMC 的可用線上說明。

#### 若要檢視可用的線上說明

1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。

2. 在 [**範圍**] 窗格中，以滑鼠右鍵按一下任何節點，或展開節點並以滑鼠右鍵按一下 [**結果**] 窗格中的項目，然後按一下 [**說明**]。

## 後續步驟

[深入了解 StorSimple Snapshot Manager 使用者介面](storsimple-use-snapshot-manager.md)

<!---HONumber=July15_HO3-->