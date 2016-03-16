<properties 
   pageTitle="監視 StorSimple 裝置 | Microsoft Azure"
   description="說明如何使用 StorSimple Manager 服務來監視 I/O 效能、容量使用率、網路輸送量和裝置效能。"
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
   ms.date="01/29/2016"
   ms.author="alkohli" />

# 使用 StorSimple Manager 服務監視 StorSimple 裝置 

## 概觀

您可以使用 StorSimple Manager 服務來監視您的 StorSimple 解決方案中的特定裝置。您可以根據 I/O 效能、容量使用率、網路輸送量，以及裝置效能計量建立自訂圖表。

若要檢視特定裝置的監視資訊，請在 Azure 傳統入口網站中，選取 [StorSimple Manager 服務]，按一下 [監視] 索引標籤，然後從裝置清單中選取。[監視] 頁面包含下列資訊。

## I/O 效能 

**I/O 效能**會追蹤與主機伺服器與裝置上的 iSCSI 啟動器介面之間，或裝置與雲端之間的讀取與寫入作業數目相關的計量。這項效能可以針對特定磁碟區、特定磁碟區容器，或所有磁碟區容器來測量。

下表顯示生產裝置所有磁碟區中的裝置啟動器 IO。繪製的計量為每秒讀寫位元組大小、每秒讀寫 IO 作業，與讀寫延遲。

![從啟動器到裝置的 IO 效能](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

針對相同的裝置，IO 專為所有磁碟區容器從裝置到雲端的資料所繪製。在此裝置上，資料僅位於線性層內且並未溢出至雲端。從裝置到雲端並未發生讀寫作業。因此，圖表內的尖峰代表 5 分鐘間隔，並與裝置與服務之間檢查活動訊號的頻率對應。

![從裝置至雲端的 IO 效能](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


針對相同的裝置，磁碟區資料的雲端快照集從下午 2:00 開始建立。這會導致資料從裝置流向雲端。在這段期間會提供雲端讀寫服務。IO 圖表會顯示建立快照集時各種計量中的尖峰所對應的時間。

![建立雲端快照集後至雲端的裝置 IO 效能](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## 容量使用率 

**容量使用率**會追蹤由磁碟區、磁碟區容器，或裝置所使用之資料儲存體空間數量的相關計量。您可以根據您的主要儲存體、雲端儲存體，或裝置儲存空間的容量使用率來建立報告。容量使用率可以在特定磁碟區、特定磁碟區容器，或所有磁碟區容器上測量。


主要、雲端和裝置儲存體容量說明如下：

###主要儲存體容量使用率
 
這些圖表顯示在進行重複資料刪除和壓縮之前寫入 StorSimple 磁碟區的資料量。您可以檢視所有磁碟區或單一磁碟區的主要儲存體使用率。

當您檢視所有磁碟區與每個個別磁碟區的主要儲存體磁碟區容量使用率圖表之比較，並將這兩種情況下的主要資料進行加總時，兩個數字之間可能會有不符。所有磁碟區上的主要資料總計可能與個別磁碟區的主要資料總和不相等。這可能是下列其中一個原因所造成：

- **所有磁碟區的資料包含快照資料**：針對所有磁碟區顯示的主要資料是每個磁碟區的主要資料與快照資料的總和。針對指定的磁碟區顯示的主要資料只對應至在該磁碟區上配置的資料數量 (而不包含對應的磁碟區快照資料)。

	這也可以由下列方程式來說明：

	*「主要資料 (所有磁碟區) = (主要資料 (磁碟區 i) + 快照資料 (磁碟區 i) 大小) 的總和」*
	
	*「其中，主要資料 (磁碟區 i) = 配置給磁碟區 i 的主要資料大小」*
 
	如果透過服務刪除快照，將會在背景中以非同步方式進行刪除。刪除快照之後，可能需要一些時間才會更新磁碟區資料大小。
 
- **所有磁碟區中包含已停用監視的磁碟區**：如果您的裝置上有已關閉監視的磁碟區，圖表中將不會有這些個別磁碟區的監視資料。不過，圖表中所有磁碟區的資料將會包含已關閉監視的磁碟區。
 
- **所有磁碟區的資料包含已刪除且具有即時關聯備份的磁碟區**：如果刪除包含快照資料的磁碟區，但關聯的快照仍然存在，您就可能看到不相符。

- **所有磁碟區的資料包含已刪除的磁碟區**：在某些情況下，舊磁碟區即使在被刪除後仍然會存在。刪除的效果無法呈現，而裝置可能會顯示較低的可用容量。您將必須連絡「Microsoft 支援服務」以移除這些磁碟區。

下表顯示建立雲端快照集前後 StorSimple 裝置的主要儲存體容量使用率。由於這只是磁碟區資料，雲端快照集不應變更主要儲存體。如您所見，由於建立雲端快照集的緣故，圖表顯示的主要容量使用率沒有差別。請注意，在該裝置上的雲端快照集會於下午 2:00 左右開始建立。

![建立雲端快照集前的主要容量使用率](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![建立雲端快照集後的主要容量使用率](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

如果您在執行 Update 2 或更新版本，就能夠以個別磁碟區、所有磁碟區、所有分層磁碟區，及所有本機磁碟區來切割主要儲存體容量使用率，如下所示。如果您以所有本機磁碟區來切割，就能讓您快速地確認已用完多少本機層。

![所有本機磁碟區的主要容量使用率](./media/storsimple-monitor-device/localvolumes.png)


###雲端儲存體容量使用率

這些圖表顯示雲端儲存體使用量。這項資料會進行重複資料刪除和壓縮。此數量包含雲端快照集，其可能包含未反映在任何主要磁碟區且針對舊版或必要保留用途而保留的資料。您可以比較主要儲存體和雲端儲存體的耗用圖表來了解資料減少速率，雖然數字並不精確。下表顯示建立雲端快照集前後 StorSimple 裝置的雲端儲存體容量使用率。雲端快照集在下午 2:00 左右於該裝置上開始建立，您可以看到雲端容量使用率在同時間飆升，從 5.73 MB 增加至 4.04 GB。

![建立雲端快照集前的雲端容量使用率](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![建立雲端快照集後的雲端容量使用率](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


###裝置儲存體容量使用率

這些圖表顯示裝置的總使用率，此使用率因為包含了 SSD 線性層，所以會高於主要儲存體使用率。這個階層包含也存在於裝置上其他階層中的資料量。SSD 線性層中的容量是重複使用的，因此當新資料進入時，舊資料會移至 HDD 層 (此時會進行重複資料刪除並壓縮)，再移至雲端。

隨著時間過去，主要容量使用率和裝置容量使用率很可能會一起增加，直到資料開始分層到雲端。此時，裝置容量使用率可能開始穩定停滯，但主要容量使用率將會因更多的資料寫入而增加。

下表顯示建立雲端快照集前後 StorSimple 裝置的主要儲存體容量使用率。雲端快照集在下午 2:00 開始建立，裝置容量使用率也在該時間開始減少。裝置儲存體容量使用率從 11.58 GB 下降至 7.48 GB。這表示很可能在線性 SSD 層中未壓縮的資料已刪除重複資料、壓縮，並移入 HDD 層。請注意，若裝置已在 SSD 和 HDD 層中有大量資料，您可能看不出減少的情況。在此範例中，裝置僅有少量的資料。

![建立雲端快照集前的裝置容量使用率](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![建立雲端快照集後的裝置容量使用率](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## 網路輸送量

「網路輸送量」會追蹤與從主機伺服器和裝置上的 iSCSI 啟動器網路介面傳輸之資料量有關，以及與在裝置和雲端之間傳輸之資料量有關的計量。您可以針對裝置上的每一個 iSCSI 網路介面監視此計量。

下列圖表顯示您的裝置上兩個 1 GbE 網路介面的 Data 0 和 Data 4 的網路輸送量。在這種情況，Data 0 已啟用雲端功能，而資料 4 已啟用 iSCSI 功能。您可以看到 StorSimple 裝置的輸入和輸出流量。請注意，圖表中從下午 3:24 開始呈現水平線條，這是因為我們只會每隔 5 分鐘收集一次資料，請忽略該線條。

![Data4 的網路輸送量](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Data4 的網路輸送量](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## 裝置效能 

「裝置效能」會追蹤與您裝置的效能有關的計量。下列圖表顯示在生產環境中裝置的 CPU 使用率統計資料。

![裝置的 CPU 使用率](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## 後續步驟

- 了解如何[使用 StorSimple Manager 服務裝置儀表板](storsimple-device-dashboard.md)。

- 了解如何[使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

<!---HONumber=AcomDC_0204_2016-->