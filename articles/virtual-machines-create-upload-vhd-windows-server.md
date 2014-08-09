<properties  linkid="manage-windows-common-task-upload-vhd" urlDisplayName="Upload a VHD" pageTitle="Create and upload a Windows Server VHD to Azure" metaKeywords="Azure VHD, uploading VHD" description="Learn how to create and upload a virtual hard disk (VHD) in Azure that has the Windows Server operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains the Windows Server Operating System" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

# 建立和上傳包含 Windows Server 作業系統的虛擬硬碟

Azure 中的虛擬機器能執行您在建立虛擬機器時選擇的作業系統。Azure 能將虛擬機器的作業系統儲存在 VHD 格式 (.vhd 檔案) 的虛擬硬碟中。做好複製準備的作業系統 VHD 稱為映像。本文展示如何藉由上傳含已安裝及一般化之作業系統的 .vhd 檔案來建立自己的映像。如需 Azure 中磁碟和映像的詳細資訊，請參閱[管理磁碟及映像][1]。

**注意**：在建立虛擬機器時，您可以藉由自訂作業系統設定來協助應用程式的執行。您設定的組態是儲存在該虛擬機器的磁碟中。如需指示，請參閱[如何建立自訂虛擬機器](/en-us/manage/windows/how-to-guides/custom-create-a-vm/)。

## 必要條件

本文假設您擁有以下項目：

**管理憑證** - 您已針對要上傳 VHD 的訂閱建立管理憑證，並將憑證匯出為 .cer 檔案。如需建立憑證的詳細資訊，請參閱[建立及上傳 Azure 的管理憑證][2]。

**支援的 Windows 作業系統並儲存在 .vhd 檔案中** - 您已將支援的 Windows Server 作業系統安裝在虛擬硬碟中。有多個能建立 .vhd 檔案的工具。您可以使用如 Hyper-V 等虛擬化解決方案來建立 .vhd 檔案及安裝作業系統。如需指示，請參閱[安裝 Hyper-V 角色和設定虛擬機器][3]。

**重要事項**：Azure 不支援較新的 VHDX 格式。您可以使用 Hyper-V 管理員或 convert-vhd Cmdlet 將磁碟轉換為 VHD 格式。

* **Window Server 作業系統媒體。**此工作須有包含 Windows Server 作業系統的 .iso 檔案。以下是支援的
  Windows Server 版本：
  <table  border="1" width="600">
  <tr  bgcolor="#E9E7E7">
  <th>作業系統</th>
  
  <th>SKU</th>
  
  <th>Service Pack</th>
  
  <th>架構</th>
  
  </tr>
  
  <tr>
  <td>Windows Server 2012</td>
  
  <td>所有版本</td>
  
  <td>N/A</td>
  
  <td>x64</td>
  
  </tr>
  
  <tr>
  <td>Windows Server 2008 R2</td>
  
  <td>所有版本</td>
  
  <td>SP1</td>
  
  <td>x64</td>
  
  </tr>
  
  </table>
  

</P>

* 隸屬於 Azure PowerShell 模組的 [Add-AzureVHD][4] Cmdlet。若要下載此模組，請參閱 [Azure 下載](/en-us/develop/downloads/)。

此工作包含下列步驟：

* [步驟 1：備妥要上傳的映像](#prepimage)
* [步驟 2：在 Azure 中建立儲存體帳戶](#createstorage)
* [步驟 3：備妥 Azure 連線](#prepAzure)
* [步驟 4：上傳 .vhd 檔案](#upload)

## <a id="prepimage"> </a>步驟 1：備妥要上傳的映像

在將映像上傳到 Azure 之前，您必須使用 Sysprep 命令將其一般化。如需使用 Sysprep 的詳細資訊，請參閱[如何使用 Sysprep：指示][5] (英文)。

在剛建立的虛擬機器中完成以下程序：

1.  登入作業系統。

2.  以系統管理員身分開啟 [命令提示字元] 視窗。將目錄變更為 **%windir%\\system32\\sysprep**，然後執行 `sysprep.exe`。
    
    ![開啟 [命令提示字元]
    視窗](./media/virtual-machines-create-upload-vhd-windows-server/sysprepcommand.png)

3.  **系統準備工具** 對話方塊隨即出現。
    
    ![啟動
    Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  在 **系統清理動作** 中選取 [Enter System Out-of-Box Experience
    (OOBE)]****，並確認 **一般化** 已勾選。

5.  在 **關機選項**中選取 **關機**。

6.  按一下 **確定**。

## <a id="createstorage"> </a>步驟 2：在 Azure 中建立儲存體帳戶

儲存體帳戶代表存取儲存服務的最高層級命名空間，其與您的 Azure 訂閱相關聯。您需要在 Azure 中擁有儲存體帳戶，才能將用來建立虛擬機器的.vhd 檔案上傳到 Azure。您可以使用 Azure 管理入口網站來建立儲存體帳戶。

1.  登入 Azure 管理入口網站。

2.  按一下命令列上的 **新增**。

3.  按一下 **儲存體帳戶**，然後按一下 **快速建立**。
    
    ![快速建立儲存體帳戶](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4.  依下文所示填寫欄位：
    
    ![輸入儲存體帳戶詳細資料](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

* 在 **URL** 下方，輸入用於儲存體帳戶 URL 的子網域名稱。此項目可以包含 3 至 24 個小寫字母與數字。此名稱會成為 URL 內用來將訂閱的 Blob、「佇列」或「表格」資源定址的主機名稱。

* 選擇儲存體帳戶的位置或同質群組。藉由指定同質群組，您可以一併將雲端服務放置在儲存體所在的資料中心。

* 決定是否要將地理區域複寫用於儲存體帳戶。地理區域複寫預設為開啟。此選項能免費將資料複寫到次要位置，因此當主要位置發生無法處置的重大失敗時，您可以將儲存體容錯移轉到次要位置。次要位置乃由系統自動指派，您無法變更。如果法律需求或組織原則要求對雲端型儲存體的位置實施更嚴密的控制，您可以關閉地理區域複寫。然而請注意，如果您日後開啟地理區域複寫，我們會針對將現有資料複寫到次要位置收取一次性的資料傳輸費用。不含地理區域複寫的儲存體服務將享有折扣優惠。

1.  按一下 **建立儲存體帳戶**。
    
    帳戶現在會出現於 **儲存體帳戶** 下方。
    
    ![已成功建立儲存體帳戶](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

## <a id="PrepAzure"> </a>步驟 3：備妥 Azure 連線

在上傳 .vhd 檔案之前，您需要在電腦和 Azure 訂閱之間建立安全連線。

1.  開啟 [Azure PowerShell] 視窗。

2.  輸入：
    
    `Get-AzurePublishSettingsFile`
    
    此命令能開啟瀏覽器視窗並自動下載 .publishsettings 檔案，該檔案含有 Azure 訂閱的資訊和憑證。

3.  儲存 .publishsettings 檔案。

4.  輸入：
    
    `Import-AzurePublishSettingsFile <PathToFile>`
    
    其中 `<PathToFile>` 代表 .publishsettings 檔案的完整路徑。
    
    如需詳細資訊，請參閱 [Azure Cmdlet 使用者入門][6]。

## <a id="upload"> </a>步驟 4：上傳 .vhd 檔案

在上傳 .vhd 檔案之前，您可以將 .vhd 檔案放置在 Blob 儲存體內的任何位置。在以下命令範例中，**BlobStorageURL** 是您在步驟 2 建立之儲存體帳戶的 URL，**YourImagesFolder** 是 Blob 儲存體內要用來儲存映像的容器。**VHDName** 是顯示於管理入口網站中用來識別虛擬硬碟的標籤。**PathToVHDFile** 是 .vhd 檔案的完整路徑和名稱。

1.  在上一個步驟使用的 [Azure PowerShell] 視窗中輸入：
    
    `Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`
    
    如需詳細資訊，請參閱 [Add-AzureVhd][4] (英文)。

## 將映像新增至自訂映像清單

上傳 .vhd 之後，您可以將其新增為與訂閱相關之自訂映像清單中的映像。

1.  在管理入口網站的 **All Items** 下方按一下 **虛擬機器**。

2.  在 **虛擬機器] 下方按一下 [映像**，然後按一下 **建立**。

3.  在 **Create an image from a VHD** 中，指定上傳之 .vhd 的名稱和 URL。

4.  勾選 **I have run Sysprep on the virtual machine associated with this VHD** 並確認您在步驟 2 將作業系統一般化，然後按一下 **確定**。

## 後續步驟

當您可以從清單使用映像後，便可以利用映像來建立虛擬機器。如需指示，請參閱[建立執行 Windows Server 的虛擬機器](../virtual-machines-windows-tutorial/)。



[1]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj672979.aspx
[2]: http://msdn.microsoft.com/zh-tw/library/windowsazure/gg551722.aspx
[3]: http://technet.microsoft.com/en-us/library/hh846766.aspx
[4]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn205185.aspx
[5]: http://technet.microsoft.com/en-us/library/bb457073.aspx
[6]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj554332.aspx