<properties
	pageTitle="建立並上傳 Windows Server VHD 到 Azure"
	description="了解在已安裝 Windows Server 作業系統的 Azure 中建立並上傳虛擬硬碟 (VHD)。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/10/2015"
	ms.author="cynthn"/>

# 建立並上傳 Windows Server VHD 到 Azure#

本文說明如何使用作業系統上傳虛擬硬碟 (VHD)，以便用它做為映像，根據該映像建立虛擬機器。如需 Microsoft Azure 中有關磁碟和 VHD 的詳細資訊，請參閱[關於虛擬機器的磁碟和 VHD](virtual-machines-disks-vhds.md)。

## 必要條件##

本文假設您已經：

1. **Azure 訂用帳戶** - 如果還沒有，您可以[免費申請 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F) - 您將取得可試用付費 Azure 服務的額度，且即使在額度用完後，您仍可保留帳戶，並使用免費的 Azure 服務，例如「網站」。除非您明確變更您的設定且同意付費，否則我們將不會從您的信用卡收取任何費用。您也可以[啟用 MSDN 訂戶權益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - 您的 MSDN 訂用帳戶每月會提供您額度，您可以用在 Azure 付費服務。

2. **Microsoft Azure PowerShell** - 您已安裝 Microsoft Azure PowerShell 模組，並設定成使用您的訂用帳戶。若要下載此模組，請參閱 [Microsoft Azure 下載](http://azure.microsoft.com/downloads/)。[這裡](../powershell-install-configure.md)有安裝和設定模組的教學課程。您將使用 [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) Cmdlet 上傳 VHD。

3. **受支援的 Windows 作業系統儲存於 .vhd 檔案中並連接至虛擬機器** - 多個工具可用來建立 .vhd 檔案。例如，您可以使用 Hyper-V 建立虛擬機器，並安裝作業系統。如需指示，請參閱[安裝 Hyper-V 角色及設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。如需有關作業系統的詳細資料，請參閱 [Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](http://go.microsoft.com/fwlink/p/?LinkId=393550)。

> [AZURE.IMPORTANT]Microsoft Azure 不支援 VHDX 格式。您可以使用 Hyper-V 管理員或 [Convert-VHD Cmdlet](http://technet.microsoft.com/library/hh848454.aspx)，將磁碟轉換為 VHD 格式。如需詳細資料，請參閱[部落格文章](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx)。

## 步驟 1：準備 VHD ##

將 VHD 上傳至 Azure 之前，必須使用 Sysprep 工具來一般化。這要準備 VHD 以做為映像。如需有關 Sysprep 的詳細資料，請參閱[如何使用 Sysprep：簡介](http://technet.microsoft.com/library/bb457073.aspx)。

從安裝作業系統的虛擬機器，完成下列程序：

1. 登入作業系統。

2. 以系統管理員身分開啟 [命令提示字元] 視窗。切換至 **%windir%\\system32\\sysprep** 目錄，然後執行 `sysprep.exe`。

	![開啟 [命令提示字元] 視窗](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	[系統準備工具] 對話方塊隨即出現。

	![啟動 Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  在 [系統準備工具] 中選取 [進入系統全新體驗 (OOBE)]，並確認 [一般化] 已勾選。

5.  在 [關機選項]中選取 [關機]。

6.  按一下 [確定]。

## 步驟 2：從您的 Azure 儲存體帳戶建立或取得資訊##

您需要 Azure 中的儲存體帳戶，讓您有空間可上傳 .vhd 檔案。此步驟說明如何建立帳戶，或從現有的帳戶取得您需要的資訊。

### 選項 1：建立儲存體帳戶

1. 登入[入口網站](http://manage.windowsazure.com)。

2. 按一下命令列上的 [新增]。

3. 依序按一下 [資料服務] > [儲存體] > [快速建立]。

	![快速建立儲存體帳戶](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. 依照下列方式填入欄位：

 - 在 **URL** 下，為儲存體帳戶輸入要在 URL 中使用的子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。此名稱會成為 URL 的主機名稱，您將其用來存取訂用帳戶的 blob、佇列或資料表資源。
 - 選擇儲存體帳戶的**位置或同質群組**。同質群組可讓您將雲端服務和儲存體放在相同的資料中心。
 - 決定儲存體帳戶是否要使用異地複寫。依預設會開啟異地複寫。此選項可讓您免費將資料複寫至次要位置，使您在主要位置發生重大錯誤時，可將儲存體容錯移轉至該位置。次要位置會自動指派，且無法變更。如果您因為法律規定或組織原則而需要更充分掌控您以雲端為基礎的儲存體所在的位置，您可以關閉地理複寫。但是，如果您後續又開啟異地複寫，在您將現有的資料複寫至次要位置時，將會產生一次性的資料傳輸費用。不含異地複寫的儲存服務會有相對的折扣。如需詳細資料，請參閱[建立、管理或刪除儲存體帳戶](../storage-create-storage-account/#replication-options)。

      ![輸入儲存體帳戶詳細資料](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

5. 按一下 [建立儲存體帳戶]。帳戶現在會出現在 [儲存體] 下方。

	![已成功建立儲存體帳戶](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. 接下來，為您上傳的 VHD 建立容器。按一下儲存體帳戶名稱，然後按一下 [容器]。

	![儲存體帳戶詳細資訊](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. 按一下 [建立容器]。

	![儲存體帳戶詳細資訊](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. 輸入容器的 [名稱]，然後選取 [存取] 原則。

	![容器名稱](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [AZURE.NOTE]容器預設為私人，且只能由帳戶擁有者存取。若要允許對容器中 Blob 的公開讀取存取，但不允許存取容器屬性和中繼資料，請使用 [公用 Blob] 選項。若要允許對容器及 Blob 的完整公開讀取存取，請使用 [公用容器] 選項。

### 選項 2：取得儲存體帳戶資訊

1.	登入[入口網站](http://manage.windowsazure.com)。

2.	從導覽窗格中，按一下 [儲存體]。

3.	按一下儲存體帳戶名稱，然後按一下 [儀表板]。

4.	從**服務**下的儀表板，將滑鼠停留在 Blob URL、按一下剪貼簿圖示以複製 URL，然後將其貼上並儲存。您將在建置命令以上傳 VHD 時使用它。

## 步驟 3：從 Azure PowerShell 連接至您的訂用帳戶

您必須先在電腦與 Azure 中的訂用帳戶之間建立安全連線，才能上傳 .vhd 檔案。您可以使用 Microsoft Azure Active Directory 方法或憑證方法來達到此目的。

> [AZURE.TIP]若要開始使用 Azure PowerShell，請參閱[如何安裝及設定 Microsoft Azure PowerShell](../install-configure-powershell.md)。如需一般資訊，請參閱[開始使用 Microsoft Azure Cmdlet。](https://msdn.microsoft.com/library/azure/jj554332.aspx)

### 選項 1：使用 Microsoft Azure AD

1. 開啟 Azure PowerShell 主控台。

2. 輸入：`Add-AzureAccount`

3.	在登入視窗中，輸入工作或學校帳戶的使用者名稱和密碼。

4. Azure 會驗證並儲存認證資訊，然後關閉視窗。

### 選項 2：使用憑證

1. 開啟 Azure PowerShell 主控台。

2.	輸入：`Get-AzurePublishSettingsFile`。

3. 隨即開啟瀏覽器視窗，並提示您下載 .publishsettings 檔案。它包含您 Microsoft Azure 訂用帳戶的資訊和憑證。

	![瀏覽器下載頁面](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. 儲存 .publishsettings 檔案。

4. 輸入：`Import-AzurePublishSettingsFile <PathToFile>`

	其中 `<PathToFile>` 是 .publishsettings 檔案的完整路徑。

## 步驟 4：上傳 .vhd 檔案

在上傳 .vhd 檔案時，您可以將 .vhd 檔案放在 Blob 儲存體中的任一處。

1. 從您在上一個步驟使用的 Azure PowerShell 視窗中，輸入類似以下的命令：

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

	位置： - **BlobStorageURL** 是儲存體帳戶的 URL - **YourImagesFolder** 是您想要在其中儲存映像之 blob 儲存體內的容器 - **VHDName** 是您想要入口網站顯示以識別虛擬硬碟的名稱 - **PathToVHDFile** 是 .vhd 檔案的完整路徑和名稱

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

如需 Add-AzureVhd Cmdlet 的詳細資訊，請參閱 [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx)。

## 步驟 5：將映像加入至您的自訂映像清單

> [AZURE.TIP]若要使用 Azure PowerShell 而不是入口網站來新增映像，請使用 **Add-AzureVMImage** cmdlet。例如：

>	`Add-AzureVMImage -ImageName <ImageName> -MediaLocation <VHDLocation> -OS <OSType>`

1. 在入口網站的 [所有項目] 下方按一下 [虛擬機器]。

2. 在 [虛擬機器] 下，按一下 [映像]。

3. 按一下 [建立映像]。

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. 在 [從 VHD 建立映像] 視窗中：

	- 指定 [名稱]。

	- 指定 [說明]。

	- 在 **VHD URL** 之下，按一下資料夾按鈕，以開啟 [瀏覽雲端儲存體] 視窗。尋找 .vhd 檔案，並按一下 [開啟]。

    ![選取 VHD](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)

5.	在 [從 VHD 建立映像] 視窗中的 [作業系統系列] 下，選取您的作業系統。核取 [我已經在與此 VHD 相關聯的虛擬機器上執行 Sysprep] 確認您已一般化作業系統，然後按一下 [確定]。

    ![新增映像](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

6. 完成前面的步驟之後，當您選擇 [映像] 索引標籤時會列出新的映像。

	![自訂映像](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	這個新的映像現在會在您建立虛擬機器時出現於 [我的映像] 下。如需指示，請參閱[如何建立執行 Windows 的自訂虛擬機器](virtual-machines-windows-create-custom.md)。

	![從自訂映像建立 VM](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

	> [AZURE.TIP]如果在您嘗試建立 VM 時發生錯誤，並出現錯誤訊息「VHD https://XXXXX... 具有不受支援的虛擬大小 YYYY 位元組。大小必須是整數 (以 MB 為單位)」，這表示您的 VHD 不是整數 MB，且必須是固定大小的 VHD。請嘗試使用 **Add-AzureVMImage** PowerShell Cmdlet 來新增映像，而不要使用入口網站 (請參閱上面的步驟 5)。Azure Cmdlet 可確保 VHD 會符合 Azure 需求。

## 後續步驟 ##

建立虛擬機器之後，嘗試建立 SQL Server 虛擬機器。如需指示，請參閱[在 Microsoft Azure 上佈建 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md)。

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Create a storage account in Azure]: #createstorage
[Step 3: Prepare the connection to Azure]: #prepAzure
[Step 4: Upload the .vhd file]: #upload

<!---HONumber=Sept15_HO3-->