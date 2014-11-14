<properties urlDisplayName="Upload a VHD" pageTitle="建立並上傳 Windows Server VHD 到 Azure" metaKeywords="Azure VHD, uploading VHD" description="了解在已安裝 Windows Server 作業系統的 Azure 中建立並上傳虛擬硬碟 (VHD)。" metaCanonical="" services="virtual-machines" documentationCenter="" title="建立並上傳 Windows Server VHD 到 Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="kathydav" />

# 建立並上傳 Windows Server VHD 到 Azure

本文說明如何使用作業系統上傳虛擬硬碟 (VHD)，以便用它做為映像，根據該映像建立虛擬機器。如需 Azure 中的磁碟和映像的詳細資訊，請參閱[關於 Azure 中的磁碟和映像][關於 Azure 中的磁碟和映像]。

**注意**：在建立虛擬機器時，您可以自訂作業系統設定，以利執行應用程式。您設定的組態會儲存在該虛擬機器的磁碟上。如需指示，請參閱[如何建立自訂虛擬機器][如何建立自訂虛擬機器]。

## 必要條件

本文假設您具有下列項目：

**Azure 訂閱** - 如果您沒有訂閱，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資料，請參閱＜[建立 Azure 帳戶][建立 Azure 帳戶]＞。

**Microsoft Azure PowerShell** - 您已安裝 Microsoft Azure PowerShell 模組。若要下載此模組，請參閱 [Microsoft Azure 下載][Microsoft Azure 下載]。使用 Azure 訂閱安裝和設定 PowerShell 的教學課程提供在[這裡][這裡]。

-   隸屬於 Microsoft Azure PowerShell 模組的 [Add-AzureVHD][Add-AzureVHD] Cmdlet。您將使用此 Cmdlet 來上傳 VHD。

**支援的 Windows 作業系統並儲存在 .vhd 檔案中** - 您已將支援的 Windows Server 作業系統安裝在虛擬硬碟中。有多項工具可用來建立 .vhd 檔案。您可以使用虛擬化解決方案 (例如 Hyper-V) 建立 .vhd 檔案，並安裝作業系統。如需指示，請參閱[安裝 Hyper-V 角色及設定虛擬機器][安裝 Hyper-V 角色及設定虛擬機器]。

**重要事項**：Microsoft Azure 不支援 VHDX 格式。您可以使用 Hyper-V 管理員或 [Convert-VHD Cmdlet][Convert-VHD Cmdlet]，將磁碟轉換為 VHD 格式。教學課程提供於[這裡][1]。

**Window Server 作業系統媒體。**此工作須有包含 Windows Server 作業系統的 .iso 檔案。以下是支援的 Windows Server 版本：

<table border="1" width="600">

<tr bgcolor="#E9E7E7">

<th>
作業系統

</th>

<th>
SKU

</th>

<th>
Service Pack

</th>

<th>
架構

</th>

</tr>

<tr>

<td>
Windows Server 2012 R2

</td>

<td>
所有版本

</td>

<td>
N/A

</td>

<td>
x64

</td>

</tr>

<tr>

<td>
Windows Server 2012

</td>

<td>
所有版本

</td>

<td>
N/A

</td>

<td>
x64

</td>

</tr>

<tr>

<td>
Windows Server 2008 R2

</td>

<td>
所有版本

</td>

<td>
SP1

</td>

<td>
x64

</td>

</tr>

</table>

</p>
</p>
此工作包含下列步驟：

-   [步驟 1：準備要上傳的映像][步驟 1：準備要上傳的映像]
-   [步驟 2：在 Azure 中建立儲存體帳戶][步驟 2：在 Azure 中建立儲存體帳戶]
-   [步驟 3：準備 Azure 的連線][步驟 3：準備 Azure 的連線]
-   [步驟 4：上傳 .vhd 檔案][步驟 4：上傳 .vhd 檔案]

## <span id="prepimage"></span> </a>步驟 1：準備要上傳的映像

在將映像上傳到 Azure 之前，您必須使用 Sysprep 命令將其一般化。如需使用 Sysprep 的詳細資訊，請參閱[如何使用 Sysprep：簡介][如何使用 Sysprep：簡介] (英文)。

在剛建立的虛擬機器中完成以下程序：

1.  登入作業系統。

2.  以系統管理員身分開啟 [命令提示字元] 視窗。切換至 **%windir%\\system32\\sysprep** 目錄，然後執行 `sysprep.exe`。

    ![開啟 [命令提示字元] 視窗][]

3.  [系統準備工具] 對話方塊隨即出現。

    ![啟動 Sysprep][啟動 Sysprep]

4.  在 [系統準備工具] 中選取 [Enter System Out-of-Box Experience (OOBE)]，並確認 [一般化] 已勾選。

5.  在 [關機選項]中選取 [關機]。

6.  按一下 [確定]。

## <span id="createstorage"></span> </a>步驟 2：在 Azure 中建立儲存體帳戶

儲存體帳戶代表用以存取儲存服務、並且與您的 Azure 訂閱相關聯的最高層級命名空間。必須要有 Azure 中的儲存體帳戶，才能將可用來建立虛擬機器的 .vhd 檔案上傳至 Azure。您可以使用 Azure 管理入口網站來建立儲存體帳戶。

1.  登入 Azure 管理入口網站。

2.  按一下命令列上的 [新增]。

3.  按一下 [儲存體帳戶]，然後按一下 [快速建立]。

    ![快速建立儲存體帳戶][快速建立儲存體帳戶]

4.  依照下列方式填入欄位：

-   在 **URL** 下，為儲存體帳戶輸入要在 URL 中使用的子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。此名稱會成為 URL 內用來為訂閱的 Blob、「佇列」或「資料表」資源定址的主機名稱。

-   選擇儲存體帳戶的**位置或同質群組**。藉由指定同質群組，您可以將雲端服務並置在與儲存體相同的資料中心。

-   決定儲存體帳戶是否要使用**地理區域複寫**。依預設會開啟地理區域複寫。此選項可讓您免費將資料複寫至次要位置，使您在遇到無法在主要位置中處理的重大錯誤時，可將儲存體容錯移轉至次要位置。次要位置會自動指派，且無法變更。如果法律規定或組織原則要求您必須更嚴密地掌控雲端儲存體的位置，您可以關閉地理區域複寫。但請注意，如果您後續又開啟地理區域複寫，在您將現有的資料複寫至次要位置時，將會產生一次性的資料傳輸費用。不含地理區域複寫的儲存服務會有相對的折扣。管理儲存體帳戶地理區域複寫的詳細資訊提供於：[如何管理儲存體帳戶][如何管理儲存體帳戶]。

    ![輸入儲存體帳戶詳細資料][輸入儲存體帳戶詳細資料]

1.  按一下 [建立儲存體帳戶]。

    帳戶現在會出現於 [儲存體帳戶] 下方。

    ![已成功建立儲存體帳戶][已成功建立儲存體帳戶]

2.  接下來，為您上傳的 VHD 建立容器。按一下 [儲存體帳戶名稱]，然後按一下 [容器]。

    ![儲存體帳戶詳細資訊][儲存體帳戶詳細資訊]

3.  按一下 [建立容器]。

    ![儲存體帳戶詳細資訊][2]

4.  輸入容器的 [名稱]，然後選取 [存取原則]。

    ![容器名稱][容器名稱]

    > [WACOM.NOTE] 容器預設為私人，且只能由帳戶擁有者存取。若要允許對容器中 Blob 的公開讀取存取，但不允許存取容器屬性和中繼資料，請使用 [公用 Blob] 選項。若要允許對容器及 Blob 的完整公開讀取存取，請使用 [公開容器] 選項。

## <span id="PrepAzure"></span> </a>步驟 3：準備 Microsoft Azure 的連線

您必須先在電腦與 Microsoft Azure 中的訂閱之間建立安全連線，才能上傳 .vhd 檔案。您可以使用 Microsoft Azure Active Directory 方法或憑證方法來達到此目的。

### 使用 Microsoft Azure AD 方法

1.  依照此文章的指示，開啟 Microsoft Azure PowerShell 主控台：[作法：安裝 Microsoft Azure PowerShell][作法：安裝 Microsoft Azure PowerShell]。

2.  輸入以下命令：
    `Add-AzureAccount`

    這個命令會開啟登入視窗，您可以用您的工作或學校帳戶登入。

    ![PowerShell Window][PowerShell Window]

3.  Microsoft Azure 會驗證並儲存認證資訊，然後關閉視窗。

### 使用憑證方法

1.  開啟 Microsoft Azure PowerShell 視窗。

2.  輸入：
    `Get-AzurePublishSettingsFile`。

3.  隨即開啟瀏覽器視窗，並提示您下載 .publishsettings 檔案。它包含您 Microsoft Azure 訂閱的資訊和憑證。

    ![瀏覽器下載頁面][瀏覽器下載頁面]

4.  儲存 .publishsettings 檔案。

5.  輸入：
    `Import-AzurePublishSettingsFile <PathToFile>`

    其中，`<PathToFile>` 是 .publishsettings 檔案的完整路徑。

    如需詳細資訊，請參閱[開始使用 Microsoft Azure Cmdlet][開始使用 Microsoft Azure Cmdlet]

    如需安裝及設定 PowerShell 的詳細資訊，請參閱[如何安裝及設定 Microsoft Azure PowerShell][這裡] (英文)

## <span id="upload"></span> </a>步驟 4：上傳 .vhd 檔案

在上傳 .vhd 檔案時，您可以將 .vhd 檔案放在 Blob 儲存體中的任一處。在下列命令範例中，**BlobStorageURL** 是您在步驟 2 中建立之儲存體帳戶的 URL，**YourImagesFolder** 則是您要用來儲存映像之 Blob 儲存體中的容器。**VHDName** 是管理入口網站中用來識別虛擬硬碟的顯示標籤。**PathToVHDFile** 是 .vhd 檔案的完整路徑和名稱。

1.  從您在上一個步驟使用的 Microsoft Azure PowerShell 視窗中，輸入：

    `Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

    ![PowerShell Add-AzureVHD][PowerShell Add-AzureVHD]

    如需 Add-AzureVhd Cmdlet 的詳細資訊，請參閱 [Add-AzureVhd][Add-AzureVhd]。

## 將映像新增至自訂映像清單

上傳 .vhd 之後，您可以將其新增為與訂閱相關之自訂映像清單中的映像。

1.  在管理入口網站的 [All Items] 下方按一下 [虛擬機器]。

2.  在 [虛擬機器] 下，按一下 [映像]。

3.  然後按一下 [建立映像]。

    ![PowerShell Add-AzureVHD][3]

4.  在 [從 VHD 建立映像] 中，執行下列動作：

    -   指定 [名稱]
    -   指定 [說明]
    -   若要指定 [您 VHD 的 URL]，請按一下資料夾按鈕，啟動底下的對話方塊

    ![選取 VHD][選取 VHD]

    -   選取您的 VHD 所在的儲存體帳戶，然後按一下 [開啟]。這會讓您回到 [從 VHD 建立映像] 視窗。
    -   回到 [從 VHD 建立映像] 視窗後，選取 [作業系統系列]。
    -   勾選 [I have run Sysprep on the virtual machine associated with this VHD] 並確認您在步驟 1 將作業系統一般化，然後按一下 [確定]。

    ![新增映像][新增映像]

5.  **選用：**您也可以使用 Azure PowerShell 的 Add-AzureVMImage Cmdlet 將 VHD 新增為映像。

    從 Microsoft Azure PowerShell 視窗，輸入：

    `Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`

    ![PowerShell Add-AzureVMImage][PowerShell Add-AzureVMImage]

6.  完成前面的步驟之後，當您選擇 [映像] 索引標籤時會列出新的映像。

    ![自訂映像][自訂映像]

    建立新的虛擬機器時，現在您可以使用這個新的映像。選擇 [我的映像] 顯示新的映像。如需指示，請參閱[建立執行 Windows Server 的虛擬機器][如何建立自訂虛擬機器]。

    ![從自訂映像建立 VM][從自訂映像建立 VM]

## 後續步驟

建立虛擬機器之後，嘗試建立 SQL Server 虛擬機器。如需指示，請參閱[在 Microsoft Azure 上佈建 SQL Server 虛擬機器][在 Microsoft Azure 上佈建 SQL Server 虛擬機器] (英文)。

  [關於 Azure 中的磁碟和映像]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj672979.aspx
  [如何建立自訂虛擬機器]: http://www.windowsazure.com/zh-tw/documentation/articles/virtual-machines-windows-tutorial/
  [建立 Azure 帳戶]: http://www.windowsazure.com/zh-tw/develop/php/tutorials/create-a-windows-azure-account/
  [Microsoft Azure 下載]: http://www.windowsazure.com/zh-tw/downloads/
  [這裡]: http://www.windowsazure.com/zh-tw/documentation/articles/install-configure-powershell/
  [Add-AzureVHD]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn205185.aspx
  [安裝 Hyper-V 角色及設定虛擬機器]: http://technet.microsoft.com/zh-tw/library/hh846766.aspx
  [Convert-VHD Cmdlet]: http://technet.microsoft.com/zh-tw/library/hh848454.aspx
  [1]: http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx
  [步驟 1：準備要上傳的映像]: #prepimage
  [步驟 2：在 Azure 中建立儲存體帳戶]: #createstorage
  [步驟 3：準備 Azure 的連線]: #prepAzure
  [步驟 4：上傳 .vhd 檔案]: #upload
  [如何使用 Sysprep：簡介]: http://technet.microsoft.com/zh-tw/library/bb457073.aspx
  [開啟 [命令提示字元] 視窗]: ./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png
  [啟動 Sysprep]: ./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png
  [快速建立儲存體帳戶]: ./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png
  [如何管理儲存體帳戶]: http://www.windowsazure.com/zh-tw/documentation/articles/storage-manage-storage-account/
  [輸入儲存體帳戶詳細資料]: ./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png
  [已成功建立儲存體帳戶]: ./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png
  [儲存體帳戶詳細資訊]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png
  [2]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png
  [容器名稱]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png
  [作法：安裝 Microsoft Azure PowerShell]: #Install
  [PowerShell Window]: ./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png
  [瀏覽器下載頁面]: ./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png
  [開始使用 Microsoft Azure Cmdlet]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj554332.aspx
  [PowerShell Add-AzureVHD]: ./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png
  [Add-AzureVhd]: http://msdn.microsoft.com/zh-tw/library/dn495173.aspx
  [3]: ./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png
  [選取 VHD]: ./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png
  [新增映像]: ./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png
  [PowerShell Add-AzureVMImage]: ./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png
  [自訂映像]: ./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png
  [從自訂映像建立 VM]: ./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png
  [在 Microsoft Azure 上佈建 SQL Server 虛擬機器]: http://www.windowsazure.com/zh-tw/documentation/articles/virtual-machines-provision-sql-server/
