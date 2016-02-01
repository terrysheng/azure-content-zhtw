<properties 
   pageTitle="StorSimple Virtual Array iSCSI 伺服器安裝 |Microsoft Azure"
   description="說明如何執行初始安裝程序、為 StorSimple iSCSI 伺服器註冊，以及完成裝置安裝程序。"
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
   ms.date="01/20/2016"
   ms.author="alkohli" />


# 部署 StorSimple Virtual Array：將虛擬裝置設定為 iSCSI 伺服器 (預覽版)

![iSCSI 安裝程序流程](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## 概觀

本部署教學課程僅適用於執行 1.1.1.0 公開預覽版的 Microsoft Azure StorSimple Virtual Array (也稱為 StorSimple 內部部署虛擬裝置或 StorSimple 虛擬裝置)。本教學課程說明如何執行初始安裝程序、為 StorSimple iSCSI 伺服器註冊、完成裝置安裝程序，還有如何在 StorSimple 虛擬裝置的 iSCSI 伺服器上建立、掛接、初始化及格式化磁碟區。這篇文章中的 StorSimple 安裝資訊，僅適用於 StorSimple Virtual Array。

完成此處描述的程序需要大約 30 分鐘至 1 個小時。這篇文章中的資訊僅適用於 StorSimple Virtual Array。

>[AZURE.IMPORTANT]
>
>- StorSimple Virtual Array 目前是預覽版，僅供評估及部署規劃之用。不支援在生產環境中安裝此預覽版。 
>- 如果您遇到任何 StorSimple Virtual Array 的問題，請在 [StorSimple MSDN 論壇](https://social.msdn.microsoft.com/Forums/home?forum=StorSimple)提出問題。

## 安裝的必要條件

在您設定及安裝 StorSimple 虛擬裝置之前，請先確定：

- 您已根據 [Deploy StorSimple Virtual Array - Provision a virtual array in Hyper-V (部署 StorSimple Virtual Array：在 Hyper-V 中佈建虛擬陣列)](storsimple-ova-deploy2-provision-hyperv.md) 或 [Deploy StorSimple Virtual Array - Provision a virtual array in VMware (部署 StorSimple Virtual Array：在 VMware 中佈建虛擬陣列)](storsimple-ova-deploy2-provision-vmware.md) 一文中的指示來佈建虛擬裝置，並與該虛擬裝置連線。

- 您已擁有服務註冊金鑰，且該金鑰來自您建立來管理 StorSimple 虛擬裝置的 StorSimple Manager 服務。如需詳細資訊，請參閱 [Deploy StorSimple Virtual Array - Prepare the portal (部署 StorSimple Virtual Array：準備入口網站)](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) 一文中的 **Step 2: Get the service registration key** (步驟 2：取得服務註冊金鑰)。

- 如果這是第二個或後續新增的虛擬裝置，且您要使用現有的 StorSimple Manager 服務來為該虛擬裝置註冊，您應該已經擁有服務資料加密金鑰。當第一個裝置在此服務註冊成功時，這個金鑰就已經產生。如果您遺失這個金鑰，請參閱 [Use the Web UI to administer your StorSimple Virtual Array (使用 Web UI 來管理您的 StorSimple 虛擬陣列)](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)一文中的 **Get the service data encryption key** (取得服務資料加密金鑰)。

## 安裝的逐步指示 

請使用下列的逐步指示，來安裝並設定您的 StorSimple 虛擬裝置：

-  [步驟 1：完成本機 Web UI 的安裝程序，並為裝置註冊](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
-  [步驟 2：完成必要的裝置安裝程序](#step-2-complete-the-required-device-setup)
-  [步驟 3：新增磁碟區](#step-3-add-a-volume)
-  [步驟 4：掛接、初始化及格式化磁碟區](#step-4-mount-initialize-and-format-a-volume)  

## 步驟 1：完成本機 Web UI 的安裝程序，並為裝置註冊 

#### 如何完成裝置的安裝程序並為裝置註冊

1. 開啟瀏覽器視窗，並輸入下列文字來連線至本機 Web UI：

    `https://<ip-address of network interface>`

    請使用您在上一個步驟記下的連線 URL。您會看到錯誤訊息，通知您網站的安全性憑證有問題。請按一下 [繼續瀏覽此網頁]。

    ![安全性憑證錯誤](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)

2. 以 **StorSimpleAdmin** 的帳戶名稱登入虛擬裝置的 Web UI。請輸入您在 [Deploy StorSimple Virtual Array - Provision a virtual device in Hyper-V (部署 StorSimple Virtual Array：在 Hyper-V 中佈建虛擬裝置)](storsimple-ova-deploy2-provision-hyperv.md) 或 [Deploy StorSimple Virtual Array - Provision a virtual device in VMware (部署 StorSimple Virtual Array：在 VMware 中佈建虛擬裝置)](storsimple-ova-deploy2-provision-vmware.md) 一文的 Step 3: Start the virtual device (步驟 3：啟動虛擬裝置) 中所變更的裝置系統管理員密碼。

    ![登入頁面](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)

3. 您將會進入 [首頁] 頁面。此頁面說明各種需要用來設定虛擬裝置的設定，以及為虛擬裝置向 StorSimple Manager 服務註冊的設定。請注意，[網路設定]、[Web Proxy 設定] 及 [時間設定] 是可省略的。只有 [裝置設定] 及 [雲端設定] 是必要的設定。

    ![首頁](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)

4. 在 [網路設定] 頁面的 [網路介面] 下方，系統會自動為您設定 DATA 0。每個網路介面都預設會自動取得 IP 位址 (DHCP)。因此，系統會自動指派 IP 位址、子網路和閘道 (IPv4 和 IPv6 皆適用)。

    因為您計畫將裝置部署為 iSCSI 伺服器 (以佈建區塊儲存體)，我們建議您停用 [自動取得 IP 位址] 選項，並設定靜態 IP 位址。

    ![網路設定頁面](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)

    如果您在佈建裝置的過程中新增了不只一個網路介面，您可以在此設定這些網路介面。

5. DNS 伺服器是必要的，因為在裝置嘗試與雲端儲存體服務提供者通訊時，或是在根據名稱來解析已設定為檔案伺服器的裝置時，就需要使用 DNS 伺服器。在 [網路設定] 頁面的 [DNS 伺服器] 下方：

    1. 系統會自動設定主要及次要 DNS 伺服器。如果您選擇設定靜態 IP 位址，就可以指定 DNS 伺服器。為了達到高可用性，我們建議您設定主要及次要 DNS 伺服器。

    2. 按一下 [套用]。這將會套用並驗證網路設定。

6. 在 [裝置設定] 頁面上：

    1. 為裝置指派唯一的 [名稱]。這個名稱可以有 1 至 15 個字元，且可以包含字母、數字和連字號。

    2. 對於您所建立裝置的「類型」，按一下 [iSCSI 伺服器] 圖示 ![iSCSI 伺服器圖示](./media/storsimple-ova-deploy3-iscsi-setup/image7.png)。ISCSI 伺服器可讓您佈建區塊儲存體。

    3. 指定您是否想讓此裝置加入網域。如果您的裝置是 iSCSI 伺服器，您可以省略加入網域這個步驟。如果您決定不將 iSCSI 伺服器加入網域，請按一下 [套用] 並等待設定套用完畢，然後前往下一個的步驟。

        如果您想要讓裝置加入網域，請輸入 [網域名稱] (如下所示)。

    4. 按一下 [套用]。

    5. 此時畫面會出現對話方塊。請以指定格式輸入網域認證。按一下核取圖示 ![核取圖示](./media/storsimple-ova-deploy3-iscsi-setup/image15.png)。系統將會驗證該網域認證。如果認證不正確，畫面會出現錯誤訊息。

        ![認證](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)

    6. 按一下 [套用]。這將會套用並驗證裝置設定。
 
7. (可省略) 設定 Web Proxy 伺服器。雖然 Web Proxy 設定是選用的，但請注意，如果您使用 Web Proxy，就只能在此處設定它。

    ![設定 Web Proxy](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)

    在 [Web Proxy] 頁面上：

    1. 以下列格式提供 [Web Proxy URL]：http://host-IP 位址或完整網域名稱:連接埠號碼。請注意，此處不支援 HTTPS URL。

    2. 將 [驗證] 指定為 [基本]、[NTLM] 或 [無]。

    3. 如果您要使用驗證功能，您也必須提供 [使用者名稱 ] 和 [密碼]。

    4. 按一下 [套用]。這將會驗證並套用您設定的 Web Proxy 設定。
 
8. (可省略) 設定裝置的時間設定，例如時區，以及主要和次要 NTP 伺服器。NTP 伺服器是必要的，因為您的裝置必須讓時間同步，才能與您的雲端服務提供者進行驗證。

    ![時間設定](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)

    在 [時間設定] 頁面上：

    1. 根據裝置部署的地理位置，從下拉式清單中選取 [時區]。裝置的預設時區是太平洋標準時間。裝置將針對所有排程的操作使用這個時區。

    2. 指定裝置的 [主要 NTP 伺服器]，或是接受 time.windows.com 的預設值。請確定您的網路允許 NTP 流量從您的資料中心通過網際網路。

    3. (可省略) 指定裝置的 [次要 NTP 伺服器]。

    4. 按一下 [套用]。這將會驗證並套用您設定的時間設定。

9. 設定裝置的雲端設定。在此步驟中，您將會完成本機裝置設定程序，然後向您的 StorSimple Manager 服務註冊裝置。

    1. 輸入您在 [Deploy StorSimple Virtual Array - Prepare the Portal (部署 StorSimple Virtual Array：準備入口網站)](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) 一文的 **Step 2: Get the service registration key** (步驟 2：取得服務註冊金鑰) 中取得的「服務註冊金鑰」。

    2. 如果這不是您向此服務註冊的第一個裝置，您必須提供「服務資料加密金鑰」。您必須將此金鑰與服務註冊金鑰搭配使用，才能向 StorSimple Manager 服務註冊額外的裝置。如需詳細資訊，請參閱使用本機 Web UI [Get the service data encryption key (取得服務資料加密金鑰)](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)。

    3. 按一下 [註冊]。這將讓裝置重新啟動。您可能需要等待 2 至 3 分鐘，裝置才會註冊成功。裝置重新啟動之後，您將會看到登入頁面。

       ![註冊裝置](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)

10. 返回 Azure 傳統入口網站。在 [裝置] 頁面上，藉由查閱狀態來確認裝置已成功連接到服務。裝置狀態應該是 [使用中]。

    ![[裝置] 頁面](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## 步驟 2：完成必要的裝置安裝程序

若要完成 StorSimple 裝置的裝置設定程序，您必須：

- 選取儲存體帳戶來與裝置建立關聯。

- 選擇要傳送至雲端的資料加密設定。

請在 Azure 傳統入口網站中執行下列步驟，來完成必要的裝置安裝程序。

#### 如何完成必要的裝置安裝程序

1. 從 [裝置] 頁面上，選取您剛建立的裝置。此裝置會顯示為 [使用中]。請按一下裝置名稱旁邊的箭頭，然後按一下 [快速啟動]。

    ![[裝置] 頁面](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)

2. 按一下 [完成裝置設定] 來啟動 [設定裝置] 精靈。

    ![[設定裝置] 精靈](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)

3. 在 [設定裝置] 精靈的 [基本設定] 頁面中，執行下列步驟：

   1. 指定要與裝置搭配使用的儲存體帳戶。在這個訂用帳戶中，您可以從下拉式清單選取現有的儲存體帳戶，或是指定 [新增其他] 來選擇不同訂用帳戶中的儲存體帳戶。

   2. 為所有將會傳送至雲端之待用資料的加密設定下定義。(StorSimple 使用 AES-256 加密。) 若要為資料加密，請選取 [啟用雲端儲存體加密] 核取方塊。請輸入包含 32 個字元的雲端儲存體加密金鑰，然後重新輸入金鑰來加以確認。

   3. 按一下核取圖示 ![核取圖示](./media/storsimple-ova-deploy3-iscsi-setup/image15.png)。

    ![基本設定](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)

    設定此時將會更新。設定更新完畢之後，[完成裝置設定] 按鈕將會變得無法使用。您將會回到裝置的 [快速入門] 頁面。

>[AZURE.NOTE]您可以藉由存取 [設定] 頁面，隨時修改所有的其他裝置設定。

## 步驟 3：新增磁碟區

請在 Azure 傳統入口網站中執行下列步驟以建立磁碟區。

#### 建立磁碟區

1. 在裝置的 [快速入門] 頁面上，按一下 [新增磁碟區]。這樣會啟動 [新增磁碟區] 精靈。

2. 在 [新增磁碟區] 精靈的 [基本設定] 下，執行列動作：

    1. 為磁碟區提供唯一的名稱。該名稱必須為包含 3 至 127 個字元的字串。

    2. 為共用提供說明。說明將可協助識別磁碟區的擁有者。

    3. 選取磁碟區的使用類型。使用類型可以是 [階層式磁碟區] 或 [固定在本機的磁碟區]。 (預設選項是 [階層式磁碟區]。) 對於需要本機保證、低延遲，以及高效能的工作負載，請選取 [固定在本機的磁碟區]。針對所有其他資料，請選取 [階層式磁碟區]。

        固定在本機的磁碟區會密集佈建，且會確保磁碟區中的主要資料會保留在裝置上，不會溢出到雲端。如果您建立固定在本機的磁碟區，裝置將會檢查本機層上的可用空間，以佈建您要求大小的磁碟區。建立固定在本機的磁碟區時，可能必須要讓裝置中現有的資料溢出到雲端，且建立磁碟區所花費的時間可能會很長。總時間取決於已佈建的磁碟區大小、可用的網路頻寬和您裝置上的資料。

        從另一方面來說，階層式磁碟區會精簡佈建，且可以非常快速地建立。當您建立階層式磁碟區時，大約 10% 的空間會佈建在本機層上，而 90% 的空間會佈建在雲端中。舉例來說，如果您佈建 1 TB 的磁碟區，當資料使用階層式磁碟區時，其中 100 GB 會位於本機的空間，900 GB 會位於雲端。然而這也代表，如果裝置已沒有可用的空間，您就無法佈建階層式共用 (因為那 10% 的空間將無法使用)。

    4. 為磁碟區指定佈建的容量。請注意，指定容量必須小於可用容量。如果您要建立階層式磁碟區，大小應該介於 500 GB 到 20 TB 之間。而對於固定在本機的磁碟區，請指定介於 50 GB 到 2 TB 之間的磁碟區大小。當您在佈建磁碟區時，請參考可用容量來下決定。如果可用的本機容量為 0 GB，您就無法佈建固定在本機或階層式磁碟區。

        ![基本設定](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)

    5. 按一下箭頭圖示 ![箭號圖示](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) 以移至下一頁。

3. 在 [**其他設定**] 頁面中，加入新的存取控制記錄 (ACR)：

    1. 提供 ACR 的 [名稱]。

    2. 在 [iSCSI 啟動器名稱] 下方，提供 Windows 主機的 iSCSI 完整格式名稱 (IQN)。如果您沒有 IQN，請前往[附錄 A：取得 Windows Server 主機的 IQN](#appendix-a-get-the-iqn-of-a-windows-server-host)。

    3. 建議選取 [**啟用此磁碟區的預設備份**] 核取方塊啟用預設備份。預設備份將會建立原則，在每天的 22:30 (裝置時間) 執行，並建立此磁碟區的雲端快照。

        ![其他設定](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)

    4. 按一下核取圖示 ![核取圖示](./media/storsimple-ova-deploy3-iscsi-setup/image15.png)。這會啟動磁碟區建立作業。您將看到類似下列圖片的進度訊息。

        ![進度訊息](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)

        使用指定的設定來建立磁碟區。根據預設，磁碟區的監視及備份功能將會啟用。

    5. 若要確認磁碟區已成功建立，請前往 [磁碟區] 頁面，您應該會看到此處列出該磁碟區。

        ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## 步驟 4：掛接、初始化及格式化磁碟區

請執行下列步驟，以便在 Windows Server 主機上掛接、初始化及格式化您的 StorSimple 磁碟區。

#### 掛接、初始化及格式化磁碟區

1. 啟動 Microsoft iSCSI 啟動器。

2. 在 [iSCSI 啟動器屬性] 視窗的 [探索] 索引標籤上，按一下 [探索入口網站]。

    ![探索入口網站](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)

3. 在 [探索目標入口網站] 對話方塊中，提供已啟用 iSCSI 網路介面的 IP 位址，然後按一下 [確定]。

    ![IP 位址](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)

4. 在 [iSCSI 啟動器屬性] 視窗的 [目標] 索引標籤上，找到 [探索到的目標]。(每個磁碟區都會是探索到的目標。) 裝置狀態應會顯示為 [非使用中]。

    ![探索到的目標](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)

5. 選取目標裝置，然後按一下 [連接]。連接裝置之後，狀態應會變更為 [已連接]。(如需如何使用 Microsoft iSCSI 啟動器的詳細資訊，請參閱[安裝和設定 Microsoft iSCSI 啟動器][1])。

    ![選取目標裝置](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)

6. 在 Windows 主機上按 Windows 標誌鍵 + X，然後按一下 [執行]。

7. 在 [執行] 對話方塊中，輸入 **Diskmgmt.msc**。按一下 [確定]，隨即會出現 [磁碟管理] 對話方塊。右窗格將會顯示主機上的磁碟區。

8. 在 [磁碟管理] 視窗中，將會出現掛接的磁碟區，如下圖所示。以滑鼠右鍵按一下探索到的磁碟區 (按一下磁碟名稱)，然後按一下 [線上]。

    ![磁碟管理](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)

9. 按一下滑鼠右鍵，然後選取 [初始化磁碟]。

    ![初始化磁碟 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)

10. 在對話方塊中，選取要初始化的磁碟，然後按一下 [確定]。

    ![初始化磁碟 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)

11. [新增簡單磁碟區] 精靈會隨即啟動。請選取磁碟大小，然後按 [下一步]。

    ![新增磁碟區精靈 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)

12. 把某個磁碟機代號指派給該磁碟區，然後按 [下一步]。

    ![新增磁碟區精靈 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)

13. 輸入要格式化磁碟區所需的參數。**Windows Server 只支援 NTFS。** 請把 [配置單位大小] 設為 64K，並提供您磁碟區的標籤。我們為這個名稱建議的最佳做法，是將該名稱設成與您在 StorSimple 虛擬裝置中為磁碟區所提供的名稱相同。按 [下一步]。

    ![新增磁碟區精靈 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)

14. 查看您磁碟區的各種設定值，然後按一下 [完成]。

    ![新增磁碟區精靈 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)

    在 [磁碟管理] 頁面中，該磁碟區會顯示為 [線上]。

    ![磁碟區線上](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## 後續步驟

了解如何使用本機 Web UI 來[管理您的 StorSimple 虛擬陣列](storsimple-ova-web-ui-admin.md)。

## 附錄 A：取得 Windows Server 主機的 IQN

請執行下列步驟，以取得正在執行 Windows Server 2012 之 Windows 主機的 iSCSI 限定名稱 (IQN)。

#### 取得 Windows 主機的 IQN

1. 在 Windows 主機上啟動 Microsoft iSCSI 啟動器。

2. 在 [iSCSI 啟動器屬性] 視窗的 [設定] 索引標籤上，選取並複製 [啟動器名稱] 欄位的字串。

    ![iSCSI 啟動器屬性](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)

2. 儲存這個字串。

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx

<!---HONumber=AcomDC_0121_2016-->