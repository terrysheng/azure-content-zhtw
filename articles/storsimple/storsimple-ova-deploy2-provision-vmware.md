<properties
   pageTitle="部署 StorSimple Virtual Array：在 VMware 中佈建"
   description="部署 StorSimple Virtual Array 的第二個教學課程，內容為如何在 VMware 中佈建虛擬裝置。"
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
   ms.date="03/10/2016"
   ms.author="alkohli"/>


# 部署 StorSimple Virtual Array：在 VMware 中佈建虛擬陣列

![](./media/storsimple-ova-deploy2-provision-vmware/vmware4.png)

## 概觀 
本佈建教學課程適用於執行 2016 年 3 月公開上市 (GA) 版的 StorSimple Virtual Array (也稱為 StorSimple 內部部署虛擬裝置或 StorSimple 虛擬裝置)。本教學課程說明如何在執行 VMware ESXi 5.5 及更新版本 的主機系統上佈建及連線到 StorSimple 虛擬陣列。本文適用於 StorSimple Virtual Arrays 在 Azure 傳統入口網站的部署，以及 Microsoft Azure 政府服務雲端。

您需要系統管理員權限，才能佈建及連線到虛擬裝置。佈建及初始安裝程序可能需要大約 10 分鐘的時間才能完成。


## 佈建的必要條件

我們在此提供要在執行 VMware ESXi 5.5 及更新版本的主機系統上佈建虛擬裝置的必要條件。

### 對於 StorSimple Manager 服務

在您開始前，請確定：

-   您已完成[準備入口網站以使用 StorSimple Virtual Array](storsimple-ova-deploy1-portal-prep.md) 中的所有步驟。

-   您已經從 Azure 入口網站下載適用於 VMware 的虛擬裝置映像。如需詳細資訊，請參閱〈[步驟 3：下載虛擬裝置映像](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image)〉。

### 對於 StorSimple 虛擬裝置 

在您部署虛擬裝置之前，請確定：

-   您可以存取執行 Hyper-V (2008 R2 或更新版本)，且可用來佈建裝置的主機系統。

-   主機系統能夠把下列資源專門用來佈建虛擬裝置：

	-   至少 4 顆核心。

	-   至少 8 GB 的 RAM。

	-   一個網路介面。

	-   供系統資料使用的 500 GB 虛擬磁碟。

### 對於資料中心的網路 

在您開始前，請確定：

-   您已經檢閱部署 StorSimple 虛擬裝置的網路需求，且已經根據需求設定資料中心的網路。如需詳細資訊，請參閱 Microsoft Azure StorSimple Virtual Array 的系統需求指南。

## 佈建的逐步指示 

若要佈建及連接到虛擬裝置，您必須執行下列步驟：

1.  確認主機系統有足夠的資源來符合最低的虛擬裝置需求。

2.  在 Hypervisor 中佈建虛擬裝置。

3.  啟動虛擬裝置，並取得 IP 位址。

## 步驟 1：確認主機系統符合最低的虛擬裝置需求

若要建立虛擬裝置，您將需要：

-   能夠存取執行 VMware ESXi 伺服器 5.5 及更新版本的主機系統。

-   您系統上的 VMware vSphere 用戶端，以便管理 ESXi 主機。

	-   至少 4 顆核心。

	-   至少 8 GB 的 RAM。

	-   網路介面，且已連線到能夠將流量路由至網際網路的網路。網際網路頻寬應該至少要有 5 Mbps，以便讓裝置能夠發揮最大功能。

	-   供資料使用的 500 GB 虛擬磁碟。

## 步驟 2：在 Hypervisor 中佈建虛擬裝置

請執行下列步驟，以便在您的 Hypervisor 中佈建虛擬裝置。

1.  複製您系統中的虛擬裝置映像。這就是您之前透過 Azure 傳統入口網站下載的映像。請記下您複製映像的位置，因為稍後將會在程序中使用該資訊。

2.  使用 vSphere 用戶端登入 ESXi 伺服器。您必須有系統管理員權限，才能建立虛擬機器。

	![](./media/storsimple-ova-deploy2-provision-vmware/image1.png)

1.  在 vSphere 用戶端中，選取左窗格 [詳細目錄] 區段中的 [ESXi 伺服器]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image2.png)

1.  首先您要將 VMDK 上傳至 ESXi 伺服器。瀏覽至右窗格中的 [設定] 索引標籤。選取 [硬體] 下方的 [儲存體]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image3.png)

1.  在右窗格的 [資料存放區] 下方，選取您要上傳 VMDK 的資料存放區。資料存放區必須要有足夠的可用空間來容納作業系統和資料磁碟。

	![](./media/storsimple-ova-deploy2-provision-vmware/image4.png)

1.  按一下滑鼠右鍵，然後選取 [瀏覽資料存放區]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image5.png)

1.  畫面會出現 [資料存放區瀏覽器] 視窗。

	![](./media/storsimple-ova-deploy2-provision-vmware/image6.png)

1.  在工具列上，按一下 ![](./media/storsimple-ova-deploy2-provision-vmware/image7.png) 圖示來建立新資料夾。然後指定資料夾的名稱，並把該名稱記下來。您稍後在建立虛擬機器時，將會使用該資料夾名稱 (建議的最佳做法)。按一下 [確定]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image8.png)

1.  [資料存放區瀏覽器] 的左窗格將會出現新的資料夾。

	![](./media/storsimple-ova-deploy2-provision-vmware/image9.png)

1.  按一下上傳圖示 ![](./media/storsimple-ova-deploy2-provision-vmware/image10.png)，然後選取 [上傳檔案]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image11.png)

1.  您現在應該瀏覽並指向您之前下載的 VMDK。

	![](./media/storsimple-ova-deploy2-provision-vmware/image12.png)

1.  按一下 [開啟]。此時將 VMDK 檔案上傳至指定資料存放區的作業將會開始。

	![](./media/storsimple-ova-deploy2-provision-vmware/image13.png)

1.  檔案可能需要幾分鐘的時間才能上傳完畢。上傳完畢之後，檔案就會出現在資料存放區裡您所建立的資料夾中。

	![](./media/storsimple-ova-deploy2-provision-vmware/image14.png)

1.  返回 vSphere 用戶端視窗。在選取 ESXi 伺服器之後按一下滑鼠右鍵，然後選取 [新增虛擬機器]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image15.png)

1.  畫面會出限 [建立新虛擬機器] 視窗。在 [設定] 頁面上，選取 [自訂] 選項。按 [下一步]。![](./media/storsimple-ova-deploy2-provision-vmware/image16.png)

2.  在 [名稱和位置] 頁面上，指定虛擬機器的名稱。這個名稱應該與您之前在步驟 8 中指定的資料夾名稱 (建議的最佳做法) 相同。

	![](./media/storsimple-ova-deploy2-provision-vmware/image17.png)

1.  在 [儲存體] 頁面上，選取您要用來佈建虛擬機器的資料存放區。

	![](./media/storsimple-ova-deploy2-provision-vmware/image18.png)

1.  在 [虛擬機器版本] 頁面上，選取 [虛擬機器版本: 8]。請注意，這是此版本唯一支援的選項。

	![](./media/storsimple-ova-deploy2-provision-vmware/image19.png)

1.  在 [客體作業系統] 頁面上，將 [客體作業系統] 選取為 [Windows]。而對於 [版本]，請從下拉式清單中選取 [Microsoft Windows Server 2012 (64 位元)]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image20.png)

1.  在 [CPU] 頁面上，調整 [虛擬通訊端的數目] 和 [每個虛擬通訊端的核心數目]，以便讓 [核心總數] 至少為 4。按 [下一步]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image21.png)

1.  在 [記憶體] 頁面上，將 RAM 指定為至少為 8 GB。按 [下一步]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image22.png)

1.  在 [網路] 頁面上，指定網路介面的數目。最低需求是一個網路介面。

	![](./media/storsimple-ova-deploy2-provision-vmware/image23.png)

1.  在 [SCSI 控制器] 頁面上，接受預設的 [LSI Logic SAS 控制器]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image24.png)

1.  在 [選取磁碟] 頁面上，選擇 [使用現有的虛擬硬碟]。按 [下一步]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image25.png)

1.  在 [選取現有的磁碟] 頁面的 [磁碟檔案路徑] 下方，按一下 [瀏覽]。這會開啟 [瀏覽資料存放區] 對話方塊。瀏覽至您之前上傳 VMDK 的位置。選取檔案，然後按一下 [確定]。按 [下一步]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image26.png)

1.  在 [進階選項] 頁面上，接受預設值並按 [下一步]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image27.png)

1.  在 [準備完成] 頁面上，檢閱與新的虛擬機器相關的所有設定。核取 [在完成之前編輯虛擬機器的設定]。按一下 [繼續]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image28.png)

1.  在 [虛擬機器屬性] 頁面的 [硬體] 索引標籤上尋找裝置的硬體。選取 [新增硬碟]。按一下 [新增]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image29.png)

1.  此時畫面會出現 [新增硬體] 視窗。在 [裝置類型] 頁面的 [選擇您想要新增的裝置類型] 下方，選取 [硬碟] 並按一下 [下一步]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image30.png)

1.  在 [選取磁碟] 頁面上，選擇 [建立新的虛擬磁碟]。按 [下一步]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image31.png)

1.  在 [建立磁碟] 頁面上，將 [磁碟大小] 變更為至少 500 GB。在 [磁碟佈建] 下方，選取 [精簡佈建]。按 [下一步]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image32.png)

1.  在 [進階選項] 頁面上，接受預設值。

	![](./media/storsimple-ova-deploy2-provision-vmware/image33.png)

1.  在 [準備完成] 頁面上，檢閱磁碟的各個選項。按一下 [完成]。

	![](./media/storsimple-ova-deploy2-provision-vmware/image34.png)

1.  此時您會返回 [虛擬機器屬性] 頁面。您的虛擬機器已新增一個硬碟。按一下 [完成]。
  
	![](./media/storsimple-ova-deploy2-provision-vmware/image35.png)

2.  在右窗格中選取您的虛擬機器，然後瀏覽至 [摘要] 索引標籤。請檢閱您虛擬機器的設定。

	![](./media/storsimple-ova-deploy2-provision-vmware/image36.png)

您的虛擬機器已成功佈建。下一個步驟是啟動該虛擬機器，然後取得 IP 位址。

## 步驟 3：啟動虛擬裝置，並取得 IP 位址

請執行下列步驟來啟動您的虛擬裝置，並連線到該虛擬裝置。

#### 啟動虛擬裝置

1.  啟動虛擬裝置。在 vSphere 設定管理員中，選取左窗格中您的裝置，然後按一下滑鼠右鍵來開啟操作功能表。選取 [電源]，然後選取 [開啟電源]。此時您的虛擬機器應該會開機。您可以在 vSphere 用戶端的 [最近的工作] 窗格底部檢視狀態。

	![](./media/storsimple-ova-deploy2-provision-vmware/image37.png)

1.  安裝工作將需要幾分鐘的時間才能完成。當裝置開始運作時，瀏覽至 [主控台] 索引標籤。傳送 Ctrl+Alt+Delete 來登入裝置。或者，您可以讓游標指向主控台視窗，然後按下 Ctrl+Alt+Insert。預設的使用者為 *StorSimpleAdmin*，而預設密碼為 *Password1*。

	![](./media/storsimple-ova-deploy2-provision-vmware/image38.png)

1.  基於安全上的理由，裝置系統管理員密碼會在您第一次登入時過期。系統將會提示您變更密碼。

	![](./media/storsimple-ova-deploy2-provision-vmware/image39.png)

1.  請輸入至少包含 8 個字元的密碼。密碼必須包含下列 4 個需求中的 3 個：大寫、小寫、數字和特殊字元。請重新輸入密碼來加以確認。系統將會通知您密碼已經變更。

	![](./media/storsimple-ova-deploy2-provision-vmware/image40.png)

1.  密碼變更成功之後，裝置可能會重新開機。請等待裝置開機完畢。畫面可能會出現裝置的 Windows PowerShell 主控台及進度列。

	![](./media/storsimple-ova-deploy2-provision-vmware/image41.png)

1.  步驟 6 至 8 僅適用於在非 DHCP 環境中開機的情況。如果您是在 DHCP 環境中，請略過這些步驟並前往步驟 9。如果您是在非 DHCP 環境中讓裝置開機，您會看到下列畫面。

	![](./media/storsimple-ova-deploy2-provision-vmware/image42m.png)

	您現在必須設定網路。

1.  使用 `Get-HcsIpAddress` 命令來列出虛擬裝置上已啟用的網路介面。如果您的裝置有已啟用的單一網路介面，系統指派給該介面的預設名稱會是 `Ethernet`。

	![](./media/storsimple-ova-deploy2-provision-vmware/image43m.png)

1.  使用 `Set-HcsIpAddress` Cmdlet 設定網路。範例如下所示：


    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

	![](./media/storsimple-ova-deploy2-provision-vmware/image44.png)

1.  初始安裝程序完成，且裝置已開機之後，您將會看到裝置橫幅文字。請記下 IP 位址及橫幅文字中的 URL，以便管理裝置。您將使用此 IP 位址連線到虛擬裝置的 Web UI，以及完成本機安裝和註冊程序。

	![](./media/storsimple-ova-deploy2-provision-vmware/image45.png)


1. (選擇性) 只有當您要在政府服務雲端部署裝置時，才執行這個步驟。您現在將在您的裝置上啟用美國聯邦資訊處理標準 (FIPS) 模式。FIPS 140 標準定義核准美國聯邦政府電腦系統所使用的密碼編譯演算法來保護機密資料。
	1. 若要啟用 FIPS 模式，請執行下列 Cmdlet：
		
		`Enter-HcsFIPSMode`

	2. 在您啟用 FIPS 模式之後，重新啟動您的裝置，讓密碼編譯驗證生效。

		> [AZURE.NOTE] 您可以在您的裝置上啟用或停用 FIPS 模式。不支援在 FIPS 和非 FIPS 模式之間替換裝置。


如果裝置不符合最低設定需求，橫幅文字中會出現錯誤訊息 (如下所示)。您必須修改裝置設定，讓裝置有足夠的資源來符合最低需求。然後您就可以將裝置重新啟動，並連線到該裝置。請參閱[步驟 1：確認主機系統符合最低的虛擬裝置需求](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements)中的最低設定需求。

![](./media/storsimple-ova-deploy2-provision-vmware/image46.png)

如果您在使用本機 Web UI 進行初始設定作業時碰到其他任何錯誤，請參閱[使用本機 Web UI 管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md) 中的下列工作流程。

-   執行診斷測試來[排解使用 Web UI 安裝時所發生的錯誤](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)。

-   [產生記錄封裝及檢視記錄檔](storsimple-ova-web-ui-admin.md#generate-a-log-package)。

## 後續步驟

-   [Set up your StorSimple Virtual Array as a file server (將 StorSimple 虛擬陣列設定為檔案伺服器)](storsimple-ova-deploy3-fs-setup.md)

-   [Set up your StorSimple Virtual Array as an iSCSI server (將 StorSimple 虛擬陣列設定為 iSCSI 伺服器)](storsimple-ova-deploy3-iscsi-setup.md)

<!---HONumber=AcomDC_0316_2016-->