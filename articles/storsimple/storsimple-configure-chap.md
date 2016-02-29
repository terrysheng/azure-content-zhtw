<properties 
   pageTitle="為 StorSimple 裝置設定 CHAP | Microsoft Azure"
   description="描述如何在 StorSimple 裝置上設定 Challenge Handshake 驗證通訊協定 (CHAP)。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="TBD"
   ms.date="02/12/2016"
   ms.author="alkohli" />

# 為 StorSimple 裝置設定 CHAP

本教學課程說明如何為 StorSimple 裝置設定 CHAP。這篇文章所述的程序適用於 StorSimple 8000 系列，以及 StorSimple 1200 裝置。

CHAP 代表 Challenge Handshake 驗證通訊協定。它是伺服器用來驗證遠端用戶端身分識別的驗證配置。此驗證以共用密碼或密碼為基礎。CHAP 可以是單向 (單向) 或相互 (雙向)。單向 CHAP 是指目標驗證啟動器。反之，相互或反向 CHAP 會要求目標驗證啟動器，然後啟動器再驗證目標。不需要目標驗證也能實作啟動器驗證。不過，必須同時實作啟動器驗證，才能實作目標驗證。

以最佳作法而言，建議您使用 CHAP 以增強 iSCSI 安全性。

>[AZURE.NOTE] 請記住，StorSimple 裝置目前不支援 IPSEC。

在 StorSimple 裝置上可以使用下列方式設定 CHAP 設定：

- 單向驗證

- 雙向、相互或反向驗證

在各種情況下，都需要設定裝置入口網站和伺服器 iSCSI 啟動器軟體。下列教學課程將說明這項設定的詳細步驟。

## 單向驗證

在單向驗證中，目標會驗證啟動器。此驗證會要求您設定 StorSimple 裝置上的 CHAP 啟動器設定，以及主機上的 iSCSI 啟動器軟體。接下來說明您的 StorSimple 裝置和 Windows 主機的詳細程序。

#### 設定裝置進行單向驗證

1. 在 Azure 傳統入口網站的 [裝置] 頁面上，按一下 [設定] 索引標籤。

    ![CHAP 啟動器](./media/storsimple-configure-chap/IC740943.png)

2. 在此頁面向下捲動，並在 [CHAP 啟動器] 區段中：
													
	1. 提供 CHAP 啟動器的使用者名稱。

	2. 提供 CHAP 啟動器的密碼。

   		 > [AZURE.IMPORTANT] CHAP 使用者名稱不得超過 233 個字元。CHAP 密碼必須介於 12 到 16 個字元。較長的使用者名稱或密碼會導致 Windows 主機上發生驗證錯誤。
    
	3. 確認密碼。

4. 按一下 [儲存]。將顯示確認訊息。按一下 [確定] 儲存變更。
#### 在 Windows 主機伺服器上設定單向驗證

1. 在 Windows 主機伺服器上啟動 iSCSI 啟動器。

2. 在 [iSCSI 啟動器屬性] 視窗中，執行下列步驟：
													
	1. 按一下 [探索] 索引標籤。

		![iSCSI 啟動器屬性](./media/storsimple-configure-chap/IC740944.png)

	2. 按一下 [探索入口網站]。

3. 在 [探索目標入口網站] 對話方塊中：
													
	1. 指定裝置的 IP 位址。

	3. 按一下 [進階]。

		![探索目標入口網站](./media/storsimple-configure-chap/IC740945.png)

4. 在 [進階設定] 對話方塊中：
													
	1. 選取 [啟用 CHAP 登入] 核取方塊。

	2. 在 [名稱] 欄位中，提供您在傳統入口網站中指定給 CHAP 啟動器的使用者名稱。

	3. 在 [目標密碼] 欄位中，提供您在傳統入口網站中指定給 CHAP 啟動器的密碼。

	4. 按一下 [確定]。

		![進階設定 - 一般](./media/storsimple-configure-chap/IC740946.png)

5. 在 [iSCSI 啟動器屬性] 視窗的 [目標] 索引標籤上，裝置狀態應該會顯示為 [已連線]。如果您使用 StorSimple 1200 裝置，則每個磁碟區將可掛接為 iSCSI 目標，如下所示。因此，需要對每個磁碟區重複執行步驟 3-4。

	![做為個別目標掛接的磁碟區](./media/storsimple-configure-chap/chap4.png)

    > [AZURE.IMPORTANT] 如果您變更 iSCSI 名稱，新的名稱將用於新的 iSCSI 工作階段。您必須先登出再登入，現有的工作階段才會使用新的設定。

如需在 Windows 主機伺服器上設定 CHAP 的詳細資訊，請移至[其他考量](#additional-considerations)。


## 雙向或相互驗證

在雙向驗證中，目標會驗證啟動器，然後啟動器再驗證目標。這需要使用者設定 CHAP 啟動器設定，以及裝置上的反向 CHAP 啟動器設定和主機上的 iSCSI 啟動器軟體。下列程序說明在裝置和 Windows 主機上設定相互驗證的步驟。

#### 設定裝置進行雙向驗證

1. 在 Azure 傳統入口網站的 [裝置] 頁面上，按一下 [設定] 索引標籤。

    ![CHAP 目標](./media/storsimple-configure-chap/IC740948.png)

2. 在此頁面向下捲動，並在 [CHAP 目標] 區段中：
													
	1. 提供裝置的 [反向 CHAP 使用者名稱]。

	2. 提供裝置的 [反向 CHAP 密碼]。

	3. 確認密碼。

3. 在 [CHAP 啟動器] 區段中：
												
	1. 提供裝置的 [使用者名稱]。

	1. 提供裝置的 [密碼]。

	3. 確認密碼。

4. 按一下 [儲存]。將顯示確認訊息。按一下 [確定] 儲存變更。

#### 在 Windows 主機伺服器上設定雙向驗證

1. 在 Windows 主機伺服器上啟動 iSCSI 啟動器。

2. 在 [iSCSI 啟動器屬性] 視窗中，按一下 [組態] 索引標籤。

3. 按一下 [CHAP]。

4. 在 [iSCSI 啟動器相互 CHAP 密碼] 對話方塊中：
													
	1. 輸入您在 Azure 傳統入口網站中設定的 [反向 CHAP 密碼]。

	2. 按一下 [確定]。

		![iSCSI 啟動器相互 CHAP 密碼](./media/storsimple-configure-chap/IC740949.png)

5. 按一下 [目標] 索引標籤。

6. 按一下 [連線] 按鈕。

7. 在 [連線至目標] 對話方塊中，按一下 [進階]。

8. 在 [進階屬性] 對話方塊中：
													
	1. 選取 [啟用 CHAP 登入] 核取方塊。

	2. 在 [名稱] 欄位中，提供您在傳統入口網站中指定給 CHAP 啟動器的使用者名稱。

	3. 在 [目標密碼] 欄位中，提供您在傳統入口網站中指定給 CHAP 啟動器的密碼。

	4. 選取 [執行相互驗證] 核取方塊。

		![進階設定 - 相互驗證](./media/storsimple-configure-chap/IC740950.png)

	5. 按一下 [確定] 完成 CHAP 設定。
	 
如需在 Windows 主機伺服器上設定 CHAP 的詳細資訊，請移至[其他考量](#additional-considerations)。

## 其他考量

**快速連線**功能不支援已啟用 CHAP 的連線。啟用 CHAP 時，請確定您使用 [目標] 索引標籤上的 [連線] 按鈕來連線到目標。

![連線至目標](./media/storsimple-configure-chap/IC740947.png)

在出現的 [連線至目標] 對話方塊中，選取 [將此連線新增到我的最愛目標清單] 核取方塊。這可確保每次電腦重新啟動時，就會嘗試恢復連線到 iSCSI 我的最愛目標。

## 設定期間發生錯誤

如果 CHAP 設定不正確，您可能會看到**驗證失敗**錯誤訊息。

## CHAP 設定的驗證

您可以完成下列步驟來確認正在使用 CHAP。

#### 驗證 CHAP 設定

1. 按一下 [我的最愛目標]。

2. 選取您已啟用驗證的目標。

3. 按一下 [詳細資訊]。

    ![iSCSI 啟動器屬性 - 我的最愛目標](./media/storsimple-configure-chap/IC740951.png)

4. 在 [我的最愛目標詳細資訊] 對話方塊中，注意 [驗證] 欄位中的項目。如果設定成功，應該會顯示 **CHAP**。

    ![我的最愛目標詳細資料](./media/storsimple-configure-chap/IC740952.png)

## 後續步驟

- 深入了解 [StorSimple 安全性](storsimple-security.md)。

- 深入了解[使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

<!---HONumber=AcomDC_0218_2016-->