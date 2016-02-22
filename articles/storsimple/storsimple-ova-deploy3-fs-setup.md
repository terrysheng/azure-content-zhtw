<properties
   pageTitle="部署 StorSimple Virtual Array 3：將虛擬裝置設定為檔案伺服器"
   description="這是如何部署 StorSimple Virtual Array 的第三個教學課程，教導您如何將虛擬裝置設定成檔案伺服器。"
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
   ms.date="02/05/2016"
   ms.author="alkohli"/>

# 部署 StorSimple Virtual Array：設定為檔案伺服器 (預覽版)

![](./media/storsimple-ova-deploy3-fs-setup/fileserver4.png)

## 簡介 

本文章僅適用於執行 1.1.1.0 版 (預覽版) 的 Microsoft Azure StorSimple Virtual Array (也稱為 StorSimple 內部部署虛擬裝置或 StorSimple 虛擬裝置)。本文章說明如何執行初始安裝程序、為 StorSimple 檔案伺服器註冊、完成裝置安裝程序，以及建立並連接至 SMB 共用。我們提供一系列如何將虛擬陣列完全部署為檔案伺服器或 iSCSI 伺服器的佈署教學課程，而這是該系列的最後一篇文章。

安裝及設定程序可能需要大約 10 分鐘的時間才能完成。

> [AZURE.IMPORTANT]
> 
> 此公開預覽版僅供評估之用。不支援在生產環境中安裝此預覽版。

## 安裝的必要條件

在您設定及安裝 StorSimple 虛擬裝置之前，請先確定：

-   您已根據 [Provision a StorSimple Virtual Array in Hyper-V (在 Hyper-V 中佈建 StorSimple 虛擬陣列)](storsimple-ova-deploy2-provision-hyperv.md) 或 [Provision a StorSimple Virtual Array in VMware (在 VMware 中佈建 StorSimple 虛擬陣列)](storsimple-ova-deploy2-provision-vmware.md) 一文中的指示來佈建虛擬裝置，並與該虛擬裝置連線。

-   您已擁有服務註冊金鑰，且該金鑰來自您建立來管理 StorSimple 虛擬裝置的 StorSimple Manager 服務。如需詳細資訊，請參閱 [Step 2: Get the service registration key (步驟 2：取得 StorSimple Virtual Array 的服務註冊金鑰)](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)。

-   如果這是第二個或後續新增的虛擬裝置，且您要使用現有的 StorSimple Manager 服務來為該虛擬裝置註冊，您應該已經擁有服務資料加密金鑰。當第一個裝置在此服務註冊成功時，這個金鑰就已經產生。如果您遺失這個金鑰，請參閱為 StorSimple 虛擬陣列 [Get the service data encryption key (取得服務資料加密金鑰)](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)。

## 安裝的逐步指示

請使用下列的逐步指示，來安裝並設定您的 StorSimple 虛擬裝置。

## 步驟 1：完成本機 Web UI 的安裝程序，並為裝置註冊 


#### 如何完成裝置的安裝程序並為裝置註冊

1.  開啟瀏覽器視窗，並連線到本機 Web UI。請輸入：	

    `https://<ip-address of network interface>`

	請使用您在上一個步驟記下的連線 URL。您會看到錯誤訊息，指出網站的安全性憑證有問題。請按一下 [繼續瀏覽此網頁]。

	![](./media/storsimple-ova-deploy3-fs-setup/image2.png)

1.  以 **StorSimpleAdmin** 的帳戶名稱登入虛擬裝置的 Web UI。請輸入您在 [Provision a StorSimple Virtual Array in Hyper-V (在 Hyper-V 中佈建 StorSimple Virtual Array)](storsimple-ova-deploy2-provision-hyperv.md) 或 [Provision a StorSimple Virtual Array in VMware (在 VMware 中佈建 StorSimple Virtual Array)](storsimple-ova-deploy2-provision-vmware.md) 一文的 Step 3: Start the virtual device (步驟 3：啟動虛擬裝置) 中所變更的裝置系統管理員密碼。

	![](./media/storsimple-ova-deploy3-fs-setup/image3.png)

1.  您將會進入 [首頁] 頁面。此頁面說明各種需要用來設定虛擬裝置的設定，以及為虛擬裝置向 StorSimple Manager 服務註冊的設定。請注意，[網路設定]、[Web Proxy 設定] 及 [時間設定] 是可省略的。只有 [裝置設定] 及 [雲端設定] 是必要的設定。

	![](./media/storsimple-ova-deploy3-fs-setup/image4.png)

1.  在 [網路設定] 頁面的 [網路介面] 下方，系統會自動為您設定 DATA 0。每個網路介面都預設會自動取得 IP 位址 (DHCP)。因此，系統會自動指派 IP 位址、子網路和閘道 (IPv4 和 IPv6 皆適用)。

	![](./media/storsimple-ova-deploy3-fs-setup/image5.png)

	如果您在佈建裝置的過程中新增了不只一個網路介面，您可以在此設定這些網路介面。

1.  DNS 伺服器是必要的，因為在裝置嘗試與雲端儲存體服務提供者通訊時，或是在根據名稱來解析已設定為檔案伺服器的裝置時，就需要使用 DNS 伺服器。在 [網路設定] 頁面的 [DNS 伺服器] 下方：

    1.  系統會自動設定主要及次要 DNS 伺服器。如果您選擇設定靜態 IP 位址，就可以指定 DNS 伺服器。為了達到高可用性，我們建議您設定主要及次要 DNS 伺服器。

    2.  按一下 [套用]。這將會套用並驗證網路設定。

2.  在 [裝置設定] 頁面中：

    1.  為裝置指派唯一的 [名稱]。這個名稱可以有 1 至 15 個字元，且可以包含字母、數字和連字號。

    2.  對於您所建立裝置的「類型」，按一下 [檔案伺服器] 圖示 ![](./media/storsimple-ova-deploy3-fs-setup/image6.png)。檔案伺服器可讓您建立共用資料夾。

    3.  由於您的裝置是檔案伺服器，您必須讓裝置加入某個網域。請輸入 [網域名稱]。

	1.  按一下 [套用]。

2.  此時畫面會出現對話方塊。請以指定格式輸入網域認證。按一下核取圖示。系統將會驗證該網域認證。如果認證不正確，畫面會出現錯誤訊息。

	![](./media/storsimple-ova-deploy3-fs-setup/image7.png)

1.  按一下 [套用]。這將會套用並驗證裝置設定。

	![](./media/storsimple-ova-deploy3-fs-setup/image8.png)

	> [AZURE.NOTE]
	> 
	> 確定您的虛擬陣列位於它自己的 Active Directory 組織單位 (OU) 中，且沒有套用群組原則物件 (GPO)。

1.  (可省略) 設定 Web Proxy 伺服器。雖然 Web Proxy 設定是選用的，但請注意，如果您使用 Web Proxy，就只能在此處設定它。

	![](./media/storsimple-ova-deploy3-fs-setup/image9.png)

	在 [Web Proxy] 頁面中：

	1.  以下列格式提供 [Web Proxy URL]：*http://&lt;host-IP 位址或完整網域名稱&gt;:連接埠號碼*。請注意，此處不支援 HTTPS URL。

	2.  將 [驗證] 指定為 [基本]、[NTLM] 或 [無]。

	3.  如果要使用驗證功能，您也必須提供 [使用者名稱] 和 [密碼]。

	4.  按一下 [Apply (套用)]。這將會驗證並套用您設定的 Web Proxy 設定。

1.  (可省略) 設定裝置的時間設定，例如時區，以及主要和次要 NTP 伺服器。NTP 伺服器是必要的，因為您的裝置必須讓時間同步，才能與您的雲端服務提供者進行驗證。

	![](./media/storsimple-ova-deploy3-fs-setup/image10.png)

	在 [時間設定] 頁面中：

	1.  根據裝置部署的地理位置，從下拉式清單中選取 [時區]。裝置的預設時區是太平洋標準時間。裝置將針對所有排程的操作使用這個時區。

	2.  指定裝置的 [主要 NTP 伺服器]，或是接受 time.windows.com 的預設值。請確定您的網路允許 NTP 流量從您的資料中心通過網際網路。

	3.  (選用) 指定裝置的 [次要 NTP 伺服器]。

	4.  按一下 [Apply (套用)]。這將會驗證並套用您設定的時間設定。

1.  設定裝置的雲端設定。在此步驟中，您將會完成本機裝置設定程序，然後向您的 StorSimple Manager 服務註冊裝置。

    1.  輸入您在[步驟 2：取得服務註冊金鑰](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)中針對 StorSimple Virtual Array 所取得的「服務註冊金鑰」。

    2.  如果這不是您向此服務註冊的第一個裝置，您必須提供「服務資料加密金鑰」。您必須將此金鑰與服務註冊金鑰搭配使用，才能向 StorSimple Manager 服務註冊額外的裝置。如需詳細資訊，請參閱使用本機 Web UI 取得 [服務資料加密金鑰](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)。

    3.  按一下 [註冊]。這將讓裝置重新啟動。您可能需要等待 2 至 3 分鐘，裝置才會註冊成功。裝置重新啟動之後，您將會看到登入頁面。

		![](./media/storsimple-ova-deploy3-fs-setup/image13.png)
	

1.  返回 Azure 傳統入口網站。在 [裝置] 頁面上，藉由查閱狀態來確認裝置已成功連接到服務。裝置狀態應該是 [使用中]。

![](./media/storsimple-ova-deploy3-fs-setup/image12.png)

## 步驟 2：完成必要的裝置安裝程序

若要完成 StorSimple 裝置的裝置設定程序，您必須：

-   選取儲存體帳戶來與裝置建立關聯。

-   選擇要傳送至雲端的資料加密設定。

請在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中執行下列步驟，來完成必要的裝置安裝程序。

#### 如何完成必要的裝置安裝程序

1.  從 [裝置] 頁面上，選取您剛建立的裝置。此裝置會顯示為 [使用中]。請按一下裝置名稱旁邊的箭頭，然後按一下 [快速啟動]。

2.  按一下 [完成裝置設定] 來啟動 [設定裝置] 精靈。

3.  在 [設定裝置] 精靈的 [基本設定] 頁面中，執行下列步驟：

	1.  指定要與裝置搭配使用的儲存體帳戶。您可以從下拉式清單選取此訂用帳戶中現有的儲存體帳戶，或是指定 [新增其他] to 來選擇不同訂用帳戶中的儲存體帳戶。

	2.  為所有將會傳送至雲端之待用資料 (AES 加密) 的加密設定下定義。若要為資料加密，請核取 [啟用雲端儲存體加密金鑰]。請輸入包含 32 個字元的雲端儲存體加密金鑰，然後重新輸入金鑰來加以確認。256 位元 AES 金鑰與使用者定義的金鑰搭配使用來進行加密。

	3.  按一下核取圖示 ![](./media/storsimple-ova-deploy3-fs-setup/image15.png)。

		![](./media/storsimple-ova-deploy3-fs-setup/image16.png)

設定此時將會更新。設定更新完畢之後，[完成裝置設定] 按鈕將會變成灰色。您將會回到裝置的 [快速入門] 頁面。

 ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)


> [AZURE.NOTE]                                                              
>
> 您可以藉由存取 [設定] 頁面，隨時修改所有的其他裝置設定。

## 步驟 3：新增共用

請在 [Azure 傳統入口網站](https://manage.windowsazure.com/)執行下列步驟來建立共用。

#### 如何建立共用

1.  在裝置的 [快速啟動] 頁面上，按一下 [加入共用]。這樣會啟動 [加入共用] 精靈。

	![](./media/storsimple-ova-deploy3-fs-setup/image17.png)

1.  在 [基本設定] 頁面上，執行下列動作：

    1.  為共用指派唯一的名稱。該名稱必須為包含 3 至 127 個字元的字串。

    2.  (可省略) 為共用提供說明。說明將可協助識別共用的擁有者。

    3.  選取共用的使用類型。使用類型可以是 [階層式] 或 [固定在本機]，而預設選項是 [階層式]。對於需要本機保證、低延遲，以及高效能的工作負載，請選取 [固定在本機] 共用。對於所有其他資料，請選取 [階層式] 共用。

	固定在本機的共用會密集佈建，且會確保共用中的主要資料會保留在裝置上，不會溢出到雲端。從另一方面來說，階層式的共用會精簡佈建，且可以非常快速地建立。當您建立階層式共用時，10% 的空間會佈建在本機層上，而 90% 的空間會佈建在雲端中。舉例來說，如果您佈建 1 TB 的磁碟區，當資料使用階層式共用時，其中 100 GB 會位於本機的空間，900 GB 會位於雲端。然而這也代表，如果裝置已沒有可用的本機空間，您就無法佈建階層式共用。

1.  指定共用的佈建容量。請注意，指定容量必須小於可用容量。如果您使用階層式共用，共用大小應該介於 500 GB 到 20 TB 之間。而對於固定在本機的共用，請指定介於 50 GB 到 2 TB 之間的共用大小。當您在佈建共用時，請參考可用容量來下決定。如果可用的本機容量為 0 GB，您就無法佈建本機或階層式共用。

	![](./media/storsimple-ova-deploy3-fs-setup/image18.png)

1.  按一下箭頭圖示 ![](./media/storsimple-ova-deploy3-fs-setup/image19.png) 以移至下一頁。

1.  在 [其他設定] 頁面上，指派權限給將會存取此共用的使用者或群組。請以下列格式指定使用者或使用者群組的名稱：*<john@contoso.com>*。我們建議您利用使用者群組 (而非單一使用者)，來授予可存取這些共用的系統管理員權限。當您在此指派權限之後，就可以使用 Windows 檔案總管來修改這些權限。

	![](./media/storsimple-ova-deploy3-fs-setup/image20.png)

1.  按一下核取圖示 ![](./media/storsimple-ova-deploy3-fs-setup/image21.png)。系統就會以指定的設定來建立共用。根據預設，共用的監視及備份功能將會啟用。

## 步驟 4：連線至共用

您現在必須連線至您在上一個步驟所建立的共用。請在 Windows Server 主機上執行這些步驟。

#### 如何連線至共用

1.  按下 ![](./media/storsimple-ova-deploy3-fs-setup/image22.png) + R鍵，然後在 [執行] 視窗中將 *\<file server name>* 指定為路徑，同時以您指派給檔案伺服器的裝置名稱取代「檔案伺服器名稱」。按一下 [確定]。

	![](./media/storsimple-ova-deploy3-fs-setup/image23.png)

2.  此時檔案總管會開啟。您應該會看到所建立的共用以資料夾形式呈現。選取並按兩下某個共用 (資料夾)，就能檢視該共用的內容。

	![](./media/storsimple-ova-deploy3-fs-setup/image24.png)

3.  現在您可以把檔案新增到這些共用，然後進行備份。

![影片圖示](./media/storsimple-ova-deploy3-fs-setup/video_icon.png)**提供的影片**

請觀賞說明如何設定及註冊 StorSimple Virtual Array 來做為檔案伺服器的影片。

> [AZURE.VIDEO configure-a-storsimple-virtual-array]

## 後續步驟

了解如何使用本機 Web UI 來[管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。

<!---HONumber=AcomDC_0211_2016-->