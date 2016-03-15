<properties
   pageTitle="部署 StorSimple Virtual Array 1：準備入口網站"
   description="部署 StorSimple Virtual Array 的第一個教學課程，內容為如何準備入口網站"
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
   ms.date="03/01/2016"
   ms.author="alkohli"/>

# 部署 StorSimple Virtual Array：準備入口網站

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## 概觀 

本文章適用於執行 2016 年 3 月公開上市 (GA) 版的 Microsoft Azure StorSimple Virtual Array (也稱為 StorSimple 內部部署虛擬裝置或 StorSimple 虛擬裝置)。我們提供一系列如何將虛擬陣列完全部署為檔案伺服器或 iSCSI 伺服器的佈署教學課程，而這是該系列的第一篇文章。本文章說明在佈建虛擬陣列之前，要建立並設定 StorSimple Manager 服務所需的準備工作。本文章也會提供能前往部署設定檢查清單及設定必要條件的連結。

您必須需要有系統管理員權限，才能完成安裝和設定程序。我們建議您在開始之前，先檢閱部署設定檢查清單。入口網站的準備工作不到 10 分鐘就能完成。

這篇文章中的 StorSimple 部署資訊，僅適用於 StorSimple Virtual Array。

### 開始使用

部署的工作流程包括準備入口網站、在您的虛擬環境中佈建虛擬陣列，以及完成安裝程序。若要開始將 StorSimple Virtual Array 部署為檔案伺服器和 iSCSI 伺服器，您必須參閱下列表格中的資源 (文章和影片)。

#### 部署相關文章

請以指定的順序參閱下列文章來部署 StorSimple 虛擬陣列。

| **#** | **在此步驟中** | **您將會進行...** | **請使用這些文件。**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1\. | **設定 Azure 傳統入口網站** | 請在佈建 StorSimple 虛擬裝置之前，先建立並設定 StorSimple Manager 服務。 |[Prepare the portal (準備入口網站)](storsimple-ova-deploy1-portal-prep.md)| 
|2\. | **佈建 Virtual Array** | 對於 Hyper-V，請佈建及連線到執行 Hyper-V 2008 R2、Hyper-V 2012 或 Hyper-V 2012 R2 之主機系統上的 StorSimple 虛擬裝置。<br></br> <br></br>對於 VMware，請佈建及連線到執行 VMware ESXi 5.5 及更新版本之主機系統上的 StorSimple 內部部署虛擬裝置。<br></br>| [在 Hyper-V 中佈建虛擬陣列](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [在 VMware 中佈建虛擬陣列](storsimple-ova-deploy2-provision-vmware.md)|
|3\. | **設定 Virtual Array** | 對於檔案伺服器，請執行初始安裝程序、註冊 StorSimple 檔案伺服器，以及完成裝置安裝程序。接下來，您可以佈建 SMB 共用。<br></br> <br></br>對於 iSCSI 伺服器，請執行初始安裝、註冊 StorSimple iSCSI 伺服器，並完成裝置安裝程序。接下來，您可以佈建 iSCSI 磁碟區。| [將虛擬陣列設定為檔案伺服器](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[將虛擬陣列設定為 iSCSI 伺服器](storsimple-ova-deploy3-iscsi-setup.md)|

#### 部署相關影片

| **如要進行此步驟...** | **請觀看這個影片。**|
|----------------|-------------|
| 開始使用 StorSimple Virtual Array 的逐步指示。 | [Get started with the StorSimple Virtual Array (開始使用 StorSimple Virtual Array)](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/)|
| 在 Hyper-V 中佈建 StorSimple 虛擬陣列的逐步指示。|[Create a StorSimple Virtual Array (建立 StorSimple 虛擬陣列)](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
|設定及註冊 StorSimple 虛擬陣列的逐步指示|[Configure a StorSimple Virtual Array (設定 StorSimple 虛擬陣列)](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/)|
|在設定為檔案伺服器的 StorSimple 虛擬陣列上建立共用、備份共用，以及還原資料的逐步指示|[Use the StorSimple Virtual Array (使用 StorSimple Virtual Array)](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/)|
|為 StorSimple 虛擬陣列進行容錯移轉及災害復原的逐步指示|[StorSimple Virtual Array Disaster Recovery (StorSimple Virtual Array 災害復原)](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/)

現在您可以開始設定 Azure 傳統入口網站了。

## 設定檢查清單

設定檢查清單說明您在設定 StorSimple 裝置上的軟體之前所需要收集的資訊。事先備妥此資訊可協助簡化在環境中部署 StorSimple 裝置的程序。視您將把 StorSimple 虛擬裝置部署為檔案伺服器或 iSCSI 伺服器而定，您將需要下列其中一個檢查清單。

-   下載 [StorSimple Virtual Array 檔案伺服器的設定檢查清單](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf)。

-   下載 [StorSimple Virtual Array iSCSI 伺服器的設定檢查清單](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf)。

## 先決條件

我們在此提供 StorSimple Manager 服務、StorSimple 虛擬裝置，以及資料中心網路的設定必要條件。

### 對於 StorSimple Manager 服務

在您開始前，請確定：

-   您擁有的 Microsoft 帳戶具有存取認證。

-   您擁有的 Microsoft Azure 儲存體帳戶具有存取認證。

-   您 Microsoft Azure 訂用帳戶的 StorSimple Manager 服務應該已經啟用。

### 對於 StorSimple 虛擬裝置 

在您部署虛擬裝置之前，請確定：

-   您可以存取執行 Hyper-V (2008 R2 或更新版本) 或 VMware (ESXi 5.5 或更新版本)，且可用來佈建裝置的主機系統。

-   主機系統能夠把下列資源專門用來佈建虛擬裝置：
	
	-   至少 4 顆核心。
	
	-   至少 8 GB 的 RAM。
	
	-   一個網路介面。
	
	-   供系統資料使用的 500 GB 虛擬磁碟。

### 對於資料中心的網路 

在您開始前，請確定：

-   資料中心的網路是根據 StorSimple 裝置的網路需求所設定的。如需詳細資訊，請參閱 [StorSimple Virtual Array 的系統需求](storsimple-ova-system-requirements.md)。

-   請確認 StorSimple 裝置隨時都有專用的 5 Mbps 網際網路頻寬 (或更多)。此頻寬不應與任何其他應用程式共用。

## 逐步的準備程序

請依照下列的逐步指示，來為您的 StorSimple Manager 服務準備入口網站。

## 步驟 1：建立新的服務

單一的 StorSimple Manager 服務執行個體就能管理多個 StorSimple 1200 裝置。請執行下列步驟以建立 StorSimple Manager 服務的新執行個體。如果您已經擁有用來管理 StorSimple 1200 裝置的 StorSimple Manager 服務，請略過此步驟，並移至〈[步驟 2：取得服務註冊金鑰](#step-2-get-the-service-registration-key)〉。

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [AZURE.IMPORTANT]
> 
> 如果您並未啟用服務自動建立儲存體帳戶，您將必須在成功建立服務後，至少建立一個儲存體帳戶。
> 

> - 如果您未自動建立儲存體帳戶，請移至[針對服務設定新的儲存體帳戶](#optional-step-configure-a-new-storage-account-for-the-service)以取得詳細指示。
> 

> - 如果您已啟用自動建立儲存體帳戶，請移至[步驟 2：取得服務註冊金鑰](#step-2-get-the-service-registration-key)。


## 步驟 2：取得服務註冊金鑰


當 StorSimple Manager 服務啟動後處於執行中時，您就必須取得服務註冊金鑰。這個金鑰是用來註冊和將 StorSimple 裝置與服務連接。

請在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中執行下列步驟。


[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [AZURE.NOTE]
> 
> 服務註冊金鑰可用來為所有必須向 StorSimple Manager 服務註冊的 StorSimple Manager 裝置註冊。

## 步驟 3：下載虛擬裝置映像

當您取得服務註冊金鑰之後，必須下載適當的虛擬裝置映像，以便在主機系統上佈建虛擬裝置。虛擬裝置映像會根據作業系統的不同而有所差異，您可以在 Azure 傳統入口網站的 [快速啟動] 頁面下載。

> [AZURE.IMPORTANT] StorSimple Virtual Array 上執行的軟體只能搭配 Storsimple Manager 服務。


請在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中執行下列步驟。

#### 如何取得虛擬裝置映像

1.  在 [StorSimple Manager 服務] 頁面上，按一下您建立的服務。這會將您帶領到 [快速入門] 頁面(您隨時都可按一下快速入門圖示 ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) 來存取 [快速入門] 頁面)。


1.  在資料中心的網路共用中，下載適當的 VHD 或 VHDX。有幾個不同映像可供選擇：

	-   Hyper-V 2012 和更新版本
	
	-   Hyper-V 2008 R2 及更新版本

	-   VMWare ESXi 5.5 及更新版本

	> [AZURE.IMPORTANT] StorSimple Virtual Array 上執行的軟體只能搭配 Storsimple Manager 服務。


1.  按一下適用於您將用來佈建虛擬裝置之主機作業系統的映像。此時系統將會帶您前往 Microsoft 下載中心。

1.  如果您使用的是 Hyper-V，請下載適用於 Hyper-V 2012 的 VHDX，或適用於 Hyper-V 2008 R2 及更新版本的 VHD。如果您使用的是 VMware，請下載 VMDK。VHDX 為 4.77 GB 的壓縮檔，VHD 為 4.77 GB 的壓縮檔，而 VMDK 為 4.75 GB 的壓縮檔。檔案的下載時間將視您的網際網路連線速度而定。

2.  將檔案解壓縮，並記下已解壓縮的檔案在您本機磁碟機上的位置。

![影片圖示](./media/storsimple-ova-deploy1-portal-prep/video_icon.png)**提供的影片**

請觀賞說明如何開始使用 StorSimple Virtual Array 的逐步指示影片。

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]



## 選用步驟：為服務設定新的儲存體帳戶

這是可省略的步驟，只有在您並未啟用服務自動建立儲存體帳戶的功能時，才需要執行。

如果您需要在不同區域建立 Azure 儲存體帳戶，請參閱[如何建立儲存體帳戶](storage-create-storage-account.md#create-a-storage-account)來取得逐步指示。

請執行 [Azure 傳統入口網站](https://manage.windowsazure.com/)中 [StorSimple Manager 服務] 頁面上的步驟，來新增現有的 Microsoft Azure 儲存體帳戶。

#### 若要新增儲存體帳戶

1.  在 StorSimple Manager 服務登陸頁面上選取您的服務，然後按兩下該服務。這會將您帶領到 [快速入門] 頁面。選取 [設定] 頁面。

2.  按一下 [新增/編輯儲存體帳戶]。在 [新增/編輯儲存體帳戶] 對話方塊中，執行下列動作：

	1.  按一下 [新增]。

	1.  提供儲存體帳戶的名稱。

	1.  提供 Microsoft Azure 儲存體帳戶的主要**存取金鑰**。

	1.  選取 [啟用 SSL 模式] 來建立裝置與雲端之間網路通訊的安全通道。只有當您在私人雲端內進行操作時，才需清除 [啟用 SSL 模式] 核取方塊。

	1.  按一下核取圖示 ![](./media/storsimple-ova-deploy1-portal-prep/image7.png)。成功建立儲存體帳戶之後，系統將會通知您。

		![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  新建立的儲存體帳戶將顯示於 [設定] 頁面上的 [儲存體帳戶] 下方。按一下 [儲存] 以儲存新建立的儲存體帳戶。系統提示您進行確認時，按一下 [確定]。


## 後續步驟

下一個步驟是為 StorSimple 虛擬裝置來佈建虛擬機器。請根據您的主機作業系統，來參閱下列其中一個詳細指示：

-   [Provision a StorSimple Virtual Array in Hyper-V (在 Hyper-V 中佈建 StorSimple Virtual Array)](storsimple-ova-deploy2-provision-hyperv.md)

-   [Provision a Virtual Array in VMware (在 VMware 中佈建 Virtual Array)](storsimple-ova-deploy2-provision-vmware.md)

<!---HONumber=AcomDC_0302_2016-------->