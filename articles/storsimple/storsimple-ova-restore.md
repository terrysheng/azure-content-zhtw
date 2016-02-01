<properties
   pageTitle="從 StorSimple Virtual Array 備份中還原 (預覽版)"
   description="深入了解如何還原 StorSimple Virtual Array 的備份。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/14/2016"
   ms.author="alkohli"/>

# 從 StorSimple Virtual Array 備份中還原 (預覽版)

## 概觀 

本文章僅適用於執行 1.1.1.0 版 (公開預覽版) 的 Microsoft Azure StorSimple Virtual Array (也稱為 StorSimple 內部部署虛擬裝置或 StorSimple 虛擬裝置)。本文說明如何逐步從 StorSimple Virtual Array 之共用或磁碟區的備份組進行還原。本文也會詳述項目層級復原在設定為檔案伺服器的 StorSimple Virtual Array 上的運作方式。

> [AZURE.IMPORTANT]
> 
> - 此公開預覽版僅供評估及部署規劃之用。不支援在生產環境中安裝此預覽版。
> - 如果您遇到任何 StorSimple Virtual Array 的問題，請在 [StorSimple MSDN 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=StorSimple)提出問題。


## 從備份組還原共用


嘗試還原共用之前，請確定裝置上有足夠的空間，可以完成這項作業。若要從備份還原，請在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中執行下列步驟。

#### 還原共用

1.  瀏覽至 [備份資料目錄]。依據適當的裝置和時間範圍進行篩選，來搜尋您的備份。按一下核取圖示 ![](./media/storsimple-ova-restore/image1.png) 以執行查詢。


1.  在顯示的備份組清單中，按一下並選取特定的備份。展開備份，以查看其下的各種共用。按一下並選取您想要還原的共用。

2.  按一下頁面底部的 [還原為新的]。

3.  這將會起始 [還原為新的共用] 精靈。在 [指定名稱和位置] 頁面上：


	1.  確認來源裝置名稱。這應該是包含您想要還原之共用的裝置。裝置選取會呈現灰色。若要選取不同的來源裝置，您需要結束精靈，並再重新選取備份組。

	2.  提供共用名稱。共用名稱必須包含 3 到 127 個字元。

	3.  請檢閱與您嘗試還原之共用相關聯的大小、類型和權限。還原完成之後，即可透過 Windows 檔案總管修改共用內容。

	4.  按一下核取圖示 ![](./media/storsimple-ova-restore/image1.png)。

		![](./media/storsimple-ova-restore/image9.png)

1.  還原工作完成之後，還原就會開始，而且您會看到另一個通知。若要監視還原進度，請按一下 [檢視工作]。這會將您帶到 [工作] 頁面。

2.  您可以追蹤還原工作的進度。還原 100% 完成時，請導覽回裝置上的 [共用] 頁面。

3.  您現在可以在裝置的共用清單中檢視新的已還原共用。請注意，還原是對相同類型的共用進行。階層式共用會還原為階層式，固定在本機的共用則會還原為固定在本機的共用。

您現在已完成裝置設定，並且了解如何備份或還原共用。


## 從備份組還原磁碟區


若要從備份還原，請在 Azure 傳統入口網站中執行下列步驟。還原作業會將備份還原至相同虛擬裝置上的新磁碟區；您無法還原到不同的裝置。

#### 還原磁碟區

1.  瀏覽至 [備份資料目錄]。依據適當的裝置和時間範圍進行篩選，來搜尋您的備份。按一下核取圖示 ![](./media/storsimple-ova-restore/image1.png) 以執行查詢。

2.  在顯示的備份組清單中，按一下並選取特定的備份。展開備份，以查看其下的各種磁碟區。您必須先在主機和裝置上將這些磁碟區離線，才能對其進行還原。在 [磁碟區] 頁面上存取磁碟區，並將它們離線。

3.  瀏覽回到 [備份類別目錄] 索引標籤，並選取備份組。

4.  按一下頁面底部的 [還原為新的]。

5.  按一下頁面底部的 [還原為新的]。將會啟動 [還原為新的磁碟區] 精靈。

1.  在 [指定名稱和位置] 頁面上：


	1.  確認來源裝置名稱。這應該是包含您想要還原之磁碟區的裝置。無法使用裝置選取。若要選取不同的來源裝置，您需要結束精靈，並再重新選取備份組。

	2.  提供磁碟區名稱。磁碟區名稱必須包含 3 到 127 個字元。

	3.  按一下箭頭圖示。

		![](./media/storsimple-ova-restore/image12.png)

1.  在 [指定可使用此磁碟區的主機] 頁面的下拉式清單中，選取適當的 ACR。

	![](./media/storsimple-ova-restore/image13.png)

1.  按一下核取圖示 ![](./media/storsimple-ova-restore/image1.png)。這會起始還原工作，而且您會看到下列正在進行工作的通知。

2.  還原工作完成之後，還原就會開始，而且您會看到另一個通知。若要監視還原進度，請按一下 [檢視工作]。這會將您帶到 [工作] 頁面。

3.  您可以追蹤還原工作的進度。還原 100% 完成時，請導覽回裝置上的 [磁碟區] 頁面。

4.  您現在可以在裝置的磁碟區清單中檢視新的已還原磁碟區。

	> [AZURE.NOTE]還原是對相同類型的磁碟區進行。階層式磁碟區會還原為階層式，固定在本機的磁碟區則會還原為固定在本機的磁碟區。

## 項目層級復原 (ILR)


此版本引進設定為檔案伺服器之 StorSimple 虛擬裝置上的項目層級復原 (ILR)。此功能可讓您從 StorSimple 裝置上所有共用的雲端備份執行檔案和資料夾的細微復原。使用者可以使用自助式模型，從最新的備份中擷取已刪除的檔案。

每個共用都有包含最新備份的 .backups 資料夾。使用者可以導覽至想要的備份，並從備份中複製相關的檔案和資料夾，然後進行還原。這樣就不需要要求系統管理員從備份中還原檔案。

1.  執行 ILR 時，您可以透過 Windows 檔案總管檢視備份。按一下您想要查看備份的特定共用。在儲存所有備份的共用下，會建立 .backups 資料夾。展開 .backups 資料夾來檢視備份。這個資料夾會顯示整個備份階層的展開檢視。此檢視是依需求建立，而且通常只需要數秒的時間就能建立完成。

	最後 5 個備份是使用這種方式顯示。這包括預設排程和手動備份。

	
	-   **排程備份**，命名為 &lt;Device name&gt;DailySchedule-YYYYMMDD-HHMMSS-UTC。

	-   **手動備份**，命名為 Ad-hoc-YYYYMMDD-HHMMSS-UTC。
	
		![](./media/storsimple-ova-restore/image14.png)

1.  識別包含已刪除檔案之最新版本的備份。在上述所有情況下，雖然資料夾名稱包含 UTC 時間戳記，但是資料夾建立時間是備份啟動時的實際裝置時間。使用資料夾時間戳記來尋找並識別備份。

2.  在上一個步驟所識別的備份中，找到您想要還原的資料夾或檔案。請注意，您只能檢視具備權限的檔案或資料夾。如果您無法存取特定檔案或資料夾，則需要連絡共用系統管理員，這位共用系統管理員可以使用 Windows 檔案總管來編輯共用權限，並讓您存取特定檔案或資料夾。建議的最佳作法是共用系統管理員為使用者群組，而不是單一使用者。

3.  將檔案或資料夾複製到您的 StorSimple 檔案伺服器上的適當共用。


## 後續步驟

深入了解如何 [administer your StorSimple Virtual Array using the local web UI (使用本機 Web UI 管理 StorSimple Virtual Array)](storsimple-ova-web-ui-admin.md)。

<!---HONumber=AcomDC_0121_2016-->