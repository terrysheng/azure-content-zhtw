<properties 
   pageTitle="StorSimple Manager 服務儀表板 - Virtual Array | Microsoft Azure"
   description="描述 StorSimple Manager 服務儀表板，以及如何使用它來監視 StorSimple Virtual Array 解決方案的健全狀況。"
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
   ms.date="02/18/2016"
   ms.author="v-sharos" />

# 針對 StorSimple Virtual Array 使用 StorSimple Manager 服務儀表板 (預覽)

## 概觀

StorSimple Manager 服務儀表板頁面提供連線至 StorSimple Manager 服務的 StorSimple Virtual Array (也稱為 StorSimple 內部部署虛擬裝置) 的摘要檢視，並強調顯示需要系統管理員注意的裝置。本教學課程介紹儀表板頁面、說明儀表板內容和功能，並說明您可以從這個頁面執行的工作。

![服務儀表板](./media/storsimple-ova-service-dashboard/dashboard1.png)

StorSimple Manager 服務儀表板會顯示下列資訊：

- 在頁面頂端的 [圖表] 區域中，您可以查看虛擬裝置的相關度量。您可以檢視所有虛擬裝置上使用的主要儲存體，以及經過一段時間虛擬裝置已耗用的雲端儲存體。使用圖表右上角的控制項，指定相關或絕對使用量，以查看 1 週、1 個月、3 個月或 1 年的時間範圍。使用重新整理控制項 ![重新整理控制項](./media/storsimple-ova-service-dashboard/refresh-control.png) 重新整理圖表。

- [使用量概觀] 相對於所有虛擬裝置可用的總儲存體，顯示所有虛擬裝置已佈建和使用的主要儲存體。[已佈建] 是指已準備和配置來使用的儲存體數量，[已使用] 是指連接至虛擬裝置的啟動器檢視的共用或磁碟區使用量，[最大容量] 會顯示所有虛擬機器的最大容量。

- [警示] 區域提供所有虛擬裝置上的所有作用中警示的快照集，依警示嚴重性分組。按一下嚴重性層級會開啟 [**警示**] 頁面，只顯示那些警示。在 [**警示**] 頁面上，您可以按一下個別警示來檢視該警示的其他詳細資料，包括任何建議的動作。如果已解決問題，您也可以清除警示。

- [作業] 區域提供連線到服務的所有虛擬裝置上的最近工作的快照集。您可以利用連結查看目前正在進行的作業，以及在過去 24 小時內成功或失敗的作業。

- 頁面右側的 [快速概覽] 區域提供有用的資訊，例如服務狀態、連接到服務的虛擬裝置總數、服務的位置，以及與服務相關聯的訂用帳戶的詳細資料。另外還有作業記錄檔的連結。按一下連結即可查看所有已完成的 StorSimple Manager 服務作業的清單。

您可以使用 StorSimple Manager 服務儀表板頁面來起始下列工作：

- 取得服務註冊金鑰。
- 檢視作業記錄檔。

## 取得服務註冊金鑰。

服務註冊金鑰用於向 StorSimple Manager 服務註冊 StorSimple 虛擬裝置，之後裝置就會出現在Azure 傳統入口網站中，讓您採取進一步的管理動作。金鑰是在第一個虛擬裝置上建立，然後與其餘虛擬裝置共用。

按一下 [**註冊金鑰**]\ (在頁面底部) 會開啟 [**服務註冊金鑰**] 對話方塊，您可以在此處將目前的服務註冊金鑰複製到剪貼簿，或重新產生服務註冊金鑰。

![註冊金鑰](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

重新產生金鑰並不會影響先前註冊的虛擬裝置：只會影響重新產生金鑰之後，才向服務註冊的虛擬裝置。

如需有關取得服務註冊金鑰的詳細資訊，請移至[取得服務註冊金鑰](storsimple-ova-manage-service.md#get-the-service-registration-key)。

## 檢視作業記錄檔

您可以在儀表板的 [**快速概覽**] 窗格中按一下可用的作業記錄檔連結，以檢視作業記錄檔。這樣會移至管理服務頁面，讓您篩選並查看 StorSimple Manager 服務特定的記錄檔。

![作業記錄](./media/storsimple-ova-service-dashboard/ops-log.png)

## 後續步驟

了解如何[使用本機 Web UI 來管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

<!----HONumber=AcomDC_0224_2016-->