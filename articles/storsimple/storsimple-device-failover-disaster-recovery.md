<properties 
   pageTitle="StorSimple 容錯移轉和災害復原 | Microsoft Azure"
   description="了解如何將 StorSimple 裝置容錯移轉至其本身、另一個實體裝置或虛擬裝置。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/14/2015"
   ms.author="alkohli" />

# StorSimple 裝置的容錯移轉與災害復原

## 概觀

本教學課程說明發生災害時容錯移轉 StorSimple 裝置所需的步驟。容錯移轉可讓您將資料中心的來源裝置資料移轉至位於相同或不同地理位置的另一個實體或甚至是虛擬裝置。裝置容錯移轉是透過災害復原 (DR) 功能協調，並從 [裝置] 頁面起始。此頁面會以表格列出與 StorSimple Manager 服務連接的所有 StorSimple 裝置。顯示每個裝置的易記名稱、狀態、佈建與最大容量、類型及模型。

![[裝置] 頁面](./media/storsimple-device-failover-disaster-recovery/IC740972.png)

## 災害復原 (DR) 與裝置容錯移轉

在災害復原 (DR) 案例中，主要裝置會停止運作。在此情況下，您可以使用主要裝置當做「來源」，並將另一個裝置指定為「目標」，將與失敗裝置相關聯的雲端資料移至另一個裝置。您可以選取一或多個磁碟區容器來移轉到目標裝置。這個程序就稱為「容錯移轉」。在容錯移轉期間，來源裝置的磁碟區容器會變更擁有權，並移轉到目標裝置。

## 裝置容錯移轉考量

萬一發生災害，您可以選擇將 StorSimple 裝置容錯移轉到：

- 實體裝置 
- 其本身
- 虛擬裝置

對於任何裝置容錯移轉，都請記住下列事項：

- DR 的必要條件是磁碟區容器內的所有磁碟區都必須離線，而且磁碟區容器要有相關聯的雲端快照。 
- 適用於 DR 的可用目標裝置是空間足以容納選定磁碟區容器的裝置。 
- 與服務連接但空間不足而不符合條件的裝置，無法當成目標裝置使用。

## 容錯移轉到另一個實體裝置

請執行下列步驟以將裝置還原至目標實體裝置。

1. 確認您要容錯移轉的磁碟區容器是否具有相關聯的雲端快照。

1. 在 [裝置] 頁面上，按一下 [磁碟區容器] 索引標籤。

1. 選取您要容錯移轉至另一個裝置的磁碟區容器。按一下磁碟區容器，以顯示此容器內的磁碟區清單。選取磁碟區，然後按一下 [離線]，使磁碟區離線。針對磁碟區容器中的所有磁碟區，重複執行這個程序。

1. 針對您要容錯移轉至另一個裝置的所有磁碟區容器，重複執行前一個步驟。

1. 在 [裝置] 頁面中，按一下 [容錯移轉]。

1. 在隨即開啟的精靈中，於 [選擇要容錯移轉的磁碟區容器] 下：

	1. 在磁碟區容器清單中，選取您要容錯移轉的磁碟區容器。

		>[AZURE.NOTE]**只會顯示與雲端快照集和離線磁碟區相關聯的磁碟區容器。**

	1. 在 [為所選取容器中的磁碟區選擇目標裝置] 中，從可用裝置的下拉式清單中選取目標裝置。下拉式清單中只會顯示具有可用容量的裝置。

	1. 最後，檢閱 [確認容錯移轉] 下的所有容錯移轉設定。按一下核取圖示 ![核取圖示](./media/storsimple-device-failover-disaster-recovery/IC740895.png)。

1. 完成容錯移轉後，移至 [裝置] 頁面。

	1. 為容錯移轉程序選取要用來做為目標裝置的裝置。

	1. 移至 [磁碟區容器] 頁面。此時應會列出所有磁碟區容器以及舊裝置中的磁碟區。

## 使用單一裝置容錯移轉

如果您只有單一裝置且需要執行容錯移轉，請執行下列步驟。

1. 建立裝置中所有磁碟區的雲端快照。

1. 將裝置重設為原廠預設值。請依照[如何將 StorSimple 裝置重設為原廠預設值](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings)中的詳細指示執行。

1. 設定裝置並再次向 StorSimple Manager 服務註冊。

1. 在 [裝置] 頁面上，舊的裝置應會顯示成 [離線]。新註冊的裝置應該會顯示成 [上線]。

1. 對於新的裝置，請先完成最小量裝置組態設定。
												
	>[AZURE.IMPORTANT]**如果不先完成最小量組態設定，您的 DR 會因目前實作中的錯誤而失敗。未來版本中將會修正這個問題。**

1. 選取舊裝置 (離線狀態)，然後按一下 [容錯移轉]。在呈現的精靈中，容錯移轉這個裝置，並將新註冊的裝置指定為目標裝置。如需詳細指示，請參閱[容錯移轉到另一個實體裝置](#fail-over-to-another-physical-device)。

1. 您可以監視從 [工作] 頁面來監視建立的裝置還原工作。

1. 順利完成工作後，請存取新的裝置，並瀏覽到 [磁碟區容器] 頁面。舊裝置的所有磁碟區容器現在應已移轉到新的裝置。

## 容錯移轉到 StorSimple 虛擬裝置

執行此程序之前，您必須已建立並設定 StorSimple 虛擬裝置。
 
>[AZURE.NOTE]**在此本板中，StorSimple 虛擬裝置上支援的儲存空間數量為 30 TB。**

請執行下列步驟以將裝置還原至目標 StorSimple 虛擬裝置。

1. 確認您要容錯移轉的磁碟區容器是否具有相關聯的雲端快照。

1. 在 [裝置] 頁面上，按一下 [磁碟區容器] 索引標籤。

1. 選取您要容錯移轉至另一個裝置的磁碟區容器。按一下磁碟區容器，以顯示此容器內的磁碟區清單。選取磁碟區，然後按一下 [離線]，使磁碟區離線。針對磁碟區容器中的所有磁碟區，重複執行這個程序。

1. 針對您要容錯移轉至另一個裝置的所有磁碟區容器，重複執行前一個步驟。

1. 在 [裝置] 頁面中，按一下 [容錯移轉]。

1. 在隨即開啟的精靈中，於 [選擇要容錯移轉的磁碟區容器] 下，完成下列動作：
													
	a.在磁碟區容器清單中，選取您要容錯移轉的磁碟區容器。

	>[AZURE.NOTE]**只會顯示與雲端快照集和離線磁碟區相關聯的磁碟區容器。**

	b.在 [為所選取容器中的磁碟區選擇目標裝置] 下，從可用裝置的下拉式清單中選取 StorSimple 虛擬裝置。下拉式清單中只會顯示具有足夠容量的裝置。
	
	>[AZURE.NOTE]**如果您的實體裝置正在執行 Update 1，您只能容錯移轉到執行 Update 1 的虛擬裝置。如果目標虛擬裝置正在執行較舊的軟體版本，您會看到錯誤，表示目標裝置軟體需要更新。**

1. 最後，檢閱 [確認容錯移轉] 下的所有容錯移轉設定。按一下核取圖示 ![核取圖示](./media/storsimple-device-failover-disaster-recovery/IC740895.png)。

1. 完成容錯移轉後，移至 [裝置] 頁面。
													
	a.為容錯移轉程序選取要用來做為目標裝置的 StorSimple 虛擬裝置。
	
	b.移至 [磁碟區容器] 頁面。此時應會列出所有磁碟區容器，以及舊裝置中的磁碟區。

![提供的影片](./media/storsimple-device-failover-disaster-recovery/Video_icon.png)**提供的影片**

若要觀看影片示範如何將失敗的實體裝置還原至雲端中的虛擬裝置，請按一下[這裡](http://azure.microsoft.com/documentation/videos/storsimple-and-disaster-recovery/)。

## 業務持續性災害復原 (BCDR)

當整個 Azure 資料中心停止運作時，就構成業務持續性災害復原 (BCDR) 狀況。這會影響您的 StorSimple Manager 服務和相關聯的 StorSimple 裝置。

如果 StorSimple 裝置在發生災害的前一刻才剛註冊，這些 StorSimple 裝置可能需要進行原廠重設。災害發生後，StorSimple 裝置會顯示為離線。必須從入口網站刪除 StorSimple 裝置，進行原廠重設，然後重新註冊。


## 後續步驟

在執行容錯移轉之後，您可能需要：

- [更新您的 StorSimple 裝置](storsimple-deactivate-and-delete-device.md#deactivate-a-device)
- [刪除您的 StorSimple 裝置](storsimple-deactivate-and-delete-device.md#delete-a-device)

如需如何使用 StorSimple Manager 的相關資訊，請移至[使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。
 

<!---HONumber=Oct15_HO3-->