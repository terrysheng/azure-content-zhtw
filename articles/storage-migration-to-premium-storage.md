<properties 
    pageTitle="移轉到 Azure Premium 儲存體 | Microsoft Azure" 
    description="移轉到 Azure Premium 儲存體，以獲得 Azure 虛擬機器上執行的 I/O 密集工作負載的高效能、低延遲磁碟支援。" 
    services="storage" 
    documentationCenter="na" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/14/2015" 
    ms.author="tamram"/>


# 移轉到 Azure Premium 儲存體

## 概觀

Premium 儲存體是將資料儲存在最新技術的固態硬碟 \(SSD\) 中，可為在 Azure 虛擬機器上執行的 I/O 密集工作負載提供高效能、低延遲的磁碟支援。使用 Premium 儲存體，每一 VM 的應用程式最多擁有 32 TB 的儲存體，每一 VM 可達到 50,000 IOPS \(每秒輸入/輸出作業\)，讀取作業的延遲極低。本文為您提供如何將您的磁碟、虛擬機器 \(VM\) 從內部部署或 Standard 儲存體或不同的雲端平台移轉至 Azure Premium 儲存體的指導方針。若要取得 Azure Premium 儲存體供應項目的詳細概觀，請參閱 Premium 儲存體：[Azure 虛擬機器工作負載適用的高效能儲存體](storage-premium-storage-preview-portal.md)。

## 開始之前 

### 必要條件
- 您將需要 Azure 訂用帳戶。如果您沒有訂用帳戶，可以建立一個月的[免費試用](http://azure.microsoft.com/pricing/free-trial/)訂用帳戶，或造訪 [Azure 定價](http://azure.microsoft.com/pricing/)以了解其他選項。 
- 若要執行 PowerShell Cmdlet，您將需要 Microsoft Azure PowerShell 模組。若要下載此模組，請參閱 [Microsoft Azure 下載](http://azure.microsoft.com/downloads/)。
- 當您計劃使用在 Premium 儲存體上執行的 Azure VM 時，您需要使用 DS 系列的虛擬機器。DS 系列的 VM 可同時搭配 Standard 和 Premium 儲存體磁碟使用。未來 Premium 儲存體磁碟將可搭配更多 VM 類型使用。如需可用 Azure VM 磁碟類型和大小的詳細資訊，請參閱 [Azure 的虛擬機器和雲端服務大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)。 

### 考量 

#### VM 大小 
以下摘要說明 DS 系列的 VM 大小及其特性。檢閱這些 Premium 儲存體供應項目的效能特性，然後為您的 Azure 磁碟和最適合您的工作負載的 VM 選擇最適當的選項。確定 VM 上有足夠的磁碟流量頻寬。

|VM 大小|CPU 核心|最大IOPS|最大磁碟頻寬|
|:---:|:---:|:---:|:---:|
|**STANDARD\_DS1**|1|3,200|每秒 32 MB|
|**STANDARD\_DS2**|2|6,400|每秒 64 MB|
|**STANDARD\_DS3**|4|12,800|每秒 128 MB|
|**STANDARD\_DS4**|8|25,600|每秒 256 MB|
|**STANDARD\_DS11**|2|6,400|每秒 64 MB|
|**STANDARD\_DS12**|4|12,800|每秒 128 MB|
|**STANDARD\_DS13**|8|25,600|每秒 256 MB|
|**STANDARD\_DS14**|16|50,000|每秒 512 MB|

#### 磁碟大小 
有三種類型的磁碟可以搭配您的 VM 使用，而且每種都有特定的 IOP 以及完整的限制。為您的 VM 選擇磁碟類型時，請根據應用程式在容量、效能、延展性以及尖峰負載方面的需求，將這些限制考量進去。

|Premium 儲存體磁碟類型|P10|P20|P30|
|:---:|:---:|:---:|:---:|
|磁碟大小|128 GB|512 GB|1024 GB \(1 TB\)|
|每一磁碟的 IOPS|500|2300|5000|
|每一磁碟的輸送量|每秒 100 MB|每秒 150 MB|每秒 200 MB|

#### 儲存體帳戶延展性目標

Premium 儲存體帳戶除了 [Azure 儲存體延展性和效能目標](http://msdn.microsoft.com/library/azure/dn249410.aspx)之外，還有以下延展性目標。如果您的應用程式需求超出單一儲存體帳戶的延展性目標，請建置使用多個儲存體帳戶的應用程式，並將資料分散到那些儲存體帳戶中。

|總帳戶容量|本機備援儲存體帳戶總頻寬|
|:--|:---|
|磁碟容量：35 TB<br />快照容量：10 TB|每秒最多 50 GB \(輸入 + 輸出\)|

如需 Premium 儲存體規格的詳細資訊，請參閱[使用 Premium 儲存體時的延展性和效能目標](storage-premium-storage-preview-portal.md#scalability-and-performance-targets-whzh-twing-premium-storage)

#### 額外的資料磁碟 
根據您的工作負載，決定您的 VM 是否需要額外的資料磁碟。您可以將數個持續性資料磁碟連接至您的 VM。如有需要，您可以跨磁碟等量磁碟區以增加磁碟區的容量和效能。如果您使用[儲存空間](http://technet.microsoft.com/library/hh831739.aspx)等量 Premium 儲存體資料磁碟，應該為所使用的每個磁碟，以一個資料行進行設定。否則，等量磁碟區的整體效能可能會因為磁碟流量分配不平均而比預期的效能還低。對於 Linux VM，您可以使用 mdadm 公用程式達到相同的結果。如需詳細資訊，請參閱文章[在 Linux 上設定軟體 RAID](virtual-machines-linux-configure-raid.md)。

#### 磁碟快取原則 
根據預設，所有 Premium 資料磁碟的磁碟快取原則都是「唯讀」，而連接至 VM 的 Premium 作業系統磁碟的磁碟快取原則則是「讀寫」。為使應用程式的 IO 達到最佳效能，建議使用此組態設定。對於頻繁寫入或唯寫的資料磁碟 \(例如 SQL Server 記錄檔\)，停用磁碟快取可獲得更佳的應用程式效能。現有資料磁碟的快取設定可以使用 Azure 入口網站或 *Set-AzureDataDisk* Cmdlet 的 *-HostCaching* 參數更新。

#### 位置 
挑選 Azure Premium 儲存體可用的位置。如需可用位置的最新資訊，請參閱 [Premium 儲存體重要須知](storage-premium-storage-preview-portal.md#important-things-to-know-about-premium-storage)。相較於與儲存 VM 磁碟的儲存體帳戶位於不同區域的 VM，位於相同區域將提供更優越的效能。

#### 其他 Azure VM 組態設定 

建立 Azure VM 時，系統會要求您設定某些 VM 設定。請記住，有一些對於 VM 存留期的設定是固定的，但是您稍後可以修改或新增其他設定。檢閱這些 Azure VM 組態設定，並確定會適當地設定這些設定以符合您的工作負載需求。如需詳細資訊，請參閱 [VM 組態設定](https://msdn.microsoft.com/library/azure/dn763935.aspx)。

## 準備 VHD 進行移轉 

下節提供從您的 VM 準備 VHD 以進行移轉的指導方針。VHD 可以是：
 
- 可以用來建立多個 Azure VM 的一般化作業系統映像。  
- 可以搭配單一 Azure 虛擬機器執行個體使用的作業系統磁碟。  
- 可以連接至 Azure VM 進行永續儲存的資料磁碟。

### 必要條件

- Azure 訂用帳戶、儲存體帳戶，以及儲存體帳戶存放複製的 VHD 的容器。請注意，目的地儲存體帳戶可以是 Standard 或 Premium 儲存體帳戶，端視您的需求而定。 
- 將 VHD 一般化的工具 \(如果您打算從 VHD 建立多個 VM 執行個體\)。例如，適用於 Windows 的 sysprep 或適用於 Ubuntu 的 virt-sysprep。將 VHD 檔案上傳至儲存體帳戶的工具。例如，[AzCopy](storage-use-azcopy.md) 或 [Azure 儲存體總管](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)。本指南說明使用 AzCopy 工具的步驟。對於頻寬有限的大量資料，您可以考慮使用 [Microsoft Azure 匯入/匯出服務](storage-import-export-service.md)，透過將硬碟運送到 Azure 資料中心來傳送資料。使用匯入/匯出服務時，您可以僅將資料複製到 Standard 儲存體帳戶。一旦資料位於 Standard 儲存體帳戶之後，您將使用[複製 Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) 或 AzCopy，將資料傳送到您的 Premium 儲存體帳戶。
- 複製 VHD 的工具可以在 Azure VM 上，與 VHD 相同的區域中執行。
- Microsoft Azure 僅支援固定大小的 VHD 檔案。不支援 VHDX 檔案或動態 VHD。如果您有動態 VHD，可以使用 [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) Cmdlet，將其轉換成固定的大小。

### 準備 VHD

|案例|步驟|
|:---|:---|
|一般化的作業系統 VHD，以建立多個 VM 執行個體|<p>如果您要上傳**將用於建立多個一般 Azure VM 執行個體的 VHD**，您必須先使用 sysprep 公用程式，將 VHD 一般化。這適用於內部部署或雲端的 VHD。Sysprep 會從 VHD 中移除所有電腦專屬的資訊。</p><p>\*\*重要：\*\* 將 VHD 一般化之前，請先擷取快照或備份您的 VM。執行 sysprep 將會刪除 VM 執行個體。</p>依照以下步驟，為 Windows 作業系統 VHD 執行 sysprep：<br />請注意，執行 Sysprep 命令將會要求您關閉虛擬機器。如需 Sysprep 的詳細資訊，請參閱 [Sysprep 概觀](http://technet.microsoft.com/library/hh825209.aspx)或 [Sysprep 技術參照]\(http://technet.microsoft.com/library/cc766049(v=ws.10).aspx)。<ul><li>以系統管理員身分開啟 \[命令提示字元\] 視窗。</li><li>輸入下列命令以開啟 Sysprep：<br />\*\*%windir%\\system32\\sysprep\\sysprep.exe\*\*</li><li>在系統準備工具中，依序選取 \[進入系統全新體驗 \(OOBE\)\]、\[一般化\] 核取方塊、\[關機\]****，然後按一下 \[確定\]****。</li><li>這樣會將作業系統一般化並關閉系統。![][1]</li></ul>若是 Ubuntu VM，請使用 virt-sysprep 達到相同結果。如需詳細資訊，請參閱 [virt-sysprep](http://manpages.ubuntu.com/manpages/precise/man1/virt-sysprep.1.html)。至於其他 Linux 作業系統，請參閱 [Linux 伺服器佈建軟體](http://www.cyberciti.biz/tips/server-provisioning-software.html)的一些開放原始碼。|
|建立單一 VM 執行個體的唯一作業系統 VHD|如果您有應用程式在需要機器專屬資料的 VM 上執行，請勿將 VHD 一般化。**非一般化的 VHD 可以用來建立唯一的 Azure VM 執行個體。** 例如，如果您的 VHD 上有網域控制站，執行 sysprep 將會使它與網域控制站一樣沒有效率。檢閱在您的 VM 上執行的應用程式，以及 sysprep 對這些應用程式的影響，然後再將 VHD 一般化。|
|連接至 VM 執行個體的資料磁碟 VHD|如果您在雲端儲存體中有要移轉的資料磁碟，必須確定使用這些資料磁碟的 VM 必須關閉。對於內部部署的資料磁碟，建立一致的 VHD。|
既然 VHD 已準備就緒，請遵循下一節中所述的步驟，將 VHD 上傳至 Azure 儲存體，並將其註冊為作業系統映像、佈建的作業系統磁碟，或佈建的資料磁碟。

## 將 VHD 複製到 Azure 儲存體

### 來源

|案例|步驟|
|:---|:---|
|從 Azure 儲存體複製 VHD|如果您將 VHD 從 Standard Azure 儲存體帳戶移轉到 Premium Azure 儲存體帳戶，必須複製 VHD 容器的來源路徑、VHD 檔案名稱以及來源儲存體帳戶的儲存體帳戶金鑰。<ul><li>移至 \[Azure 入口網站\] \> \[虛擬機器\] \> \[磁碟\]</li><li>從 \[位置\] 欄複製並儲存 VHD 容器 URL，https://*AccountName*.blob.core.windows.net/*ContainerName*/</li></ul>|
|從非 Azure 雲端複製 VHD|如果您將 VHD 從非 Azure 雲端儲存體移轉至 Azure，您必須先將 VHD 匯出至本機目錄。複製儲存 VHD 所在本機目錄的完整來源路徑。<ul><li>如果您使用的是 AWS，將 EC2 執行個體匯出至 Amazon S3 值區中的 VHD。請依照[匯出 Amazon EC2 執行個體](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html)中所述的步驟，安裝 Amazon EC2 命令列介面 \(CLI\) 工具，並執行命令，以便將 EC2 執行個體匯出到 VHD 檔案。執行命令時，請務必使用適用於 DISK\_IMAGE\_FORMAT 變數的 **VHD**。匯出的 VHD 檔案會儲存在您在該程序期間所指定的 Amazon S3 值區中。</li></ul>![][2]<ul><li>從 S3 值區下載 VHD 檔案。選取 VHD 檔案 \> \[動作\]**** \> \[下載\]****。</li></ul>![][3]|
|從內部部署複製 VHD|如果您要從內部部署環境移轉 VHD，需要儲存 VHD 的完整來源路徑。這可能是伺服器位置或檔案共用。

### 目的地 

建立儲存體帳戶來維護您的 VHD。規劃儲存 VHD 的位置時，請考量以下幾點。

- 目標儲存體帳戶可能是 Standard 或 Premium 儲存體，端視您的應用程式需求而定。 
- 儲存體帳戶位置必須與您將在最終階段建立的 DS 系列 Azure VM 相同。您可以複製到新的儲存體帳戶，或打算根據您的需求，使用相同的儲存體帳戶。
- 為下一個階段複製並儲存目的地儲存體帳戶的儲存體帳戶金鑰。
- 對於資料磁碟，您可以選擇將一些資料磁碟保留在 Standard 儲存體帳戶中 \(例如，具有散熱器儲存體的磁碟\)，並將一些具有大量 IOP 的資料磁碟保留在 Premium 儲存體帳戶中。

### 使用 AzCopy 複製 VHD

您可以使用 AzCopy，透過網際網路輕易地上傳 VHD。根據 VHD 的大小，這可能需要一些時間。使用這個選項時，請記得檢查儲存體帳戶輸入/輸出限制。您可以在[這裡](azure-subscription-service-limits#storage-limits)找到 Azure 儲存體限制。

1. 從這裡下載並安裝 AzCopy：[AzCopy 的最新版本](http://aka.ms/downloadazcopy)  
2. 開啟 Azure PowerShell，並移至安裝 AzCopy 所在的資料夾。  
3. 使用下列命令，將 VHD 檔案從「來源」複製到「目的地」。**AzCopy /Source:** *&lt;Source&gt;* **/SourceKey:** *&lt;Source-Storage-Key&gt;* **/Destination:** *&lt;Destination&gt;* **/DestKey:** *&lt;Dest-Storage-Key&gt;* **/BlobType:page /Pattern:** *&lt;File-Name&gt;*  
  - *&lt;Source&gt;*：資料夾的位置，或包含 VHD 的儲存體容器 URL。    
 - *&lt;Source-Storage-Key&gt;*：來源儲存體帳戶的儲存體帳戶金鑰。  
 - *&lt;Destination&gt;*：儲存複製的 VHD 的目的地儲存體容器 URL。
 - *&lt;Dest-Storage-Key&gt;*：目的地儲存體帳戶的儲存體帳戶金鑰。
 - /BlobType:page：指定目的地是分頁 Blob。
 - *&lt;File-Name&gt;*：您想要複製的 VHD 檔案的名稱。
 - /Pattern:\*&lt;File-Name&gt;：\* 指定您要複製的 VHD 的檔案名稱。
   
此命令會將 *&lt;Source&gt;* 中的所有檔案複製到 *&lt;Destination&gt;* 容器中。如需使用 AzCopy 工具的詳細資訊，請參閱開始使用 [AzCopy 命令列公用程式](storage-use-azcopy.md)。
### 上傳 VHD 的其他選項 

- [Azure 儲存體複製 Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx) 
- [Azure 匯入/匯出服務](https://msdn.microsoft.com/library/dn529096.aspx) 

>[AZURE.NOTE]匯入/匯出僅可用來複製到 Standard 儲存體帳戶。您必須使用 AzCopy 之類的工具，從 Standard 儲存體帳戶複製到 Premium 儲存體帳戶。

## 使用 Premium 儲存體建立 Azure VM

將 VHD 上傳到所需的儲存體帳戶之後，請遵循本節中的指示，根據您的情況，將 VHD 註冊為作業系統映像或作業系統磁碟，然後從該映像或磁碟建立 VM 執行個體。一旦建立之後，資料磁碟 VHD 就可以連接至 VM。

### 註冊 VHD
  
若要從作業系統 VHD 建立 VM，或將資料 VHD 連接到新的 VM，您必須先進行註冊。根據您的情況，遵循以下步驟。

<table>
<tr>
<th>案例</th>
<th>註冊 VHD 的步驟</th>
</tr>
<tr>
<td>建立多個 Azure VM 執行個體的一般化的作業系統 VHD</td>
<td><p>將一般化的作業系統映像 VHD 上傳至儲存體帳戶之後，將其註冊為 <b>Azure VM 映像</b>，讓您可以從該映像建立一個或多個 VM 執行個體。</p><p>使用下列 PowerShell Cmdlet 將您的 VHD 註冊為 Azure VM 作業系統映像。提供存放複製的 VHD 的完整容器 URL。</p><p><code>Add-AzureVMImage -ImageName "OSImageName" -MediaLocation “https://storageaccount.blob.core.windows.net/vhdcontainer/osimage.vhd” -OS Windows</code></p>複製並儲存這個新 Azure VM 映像的名稱。在上述範例中，該名稱是 "OSImageName"。</td>
</tr>
</tr>
<td>建立單一 Azure VM 執行個體的唯一作業系統 VHD</td>
<td><p>將唯一的作業系統 VHD 上傳至儲存體帳戶之後，將其註冊為 <b>Azure 作業系統磁碟</b>，讓您可以從該磁碟建立 VM 執行個體。</p><p>使用下列 PowerShell Cmdlet 將您的 VHD 註冊為 Azure 作業系統磁碟。提供存放複製的 VHD 的完整容器 URL。</p><code>"https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd" -Label "OS Disk" -OS "Windows"</code><p>複製並儲存這個新 Azure 作業系統磁碟的名稱。在上述範例中，該名稱是 "OSDisk"。</p></td>
</tr>
</tr>
<td>連接至新 Azure VM 執行個體的資料磁碟 VHD</td>
<td><p>將資料磁碟 VHD 上傳至儲存體帳戶之後，將其註冊為 Azure 資料磁碟，便可以連接到新的 DS 系列 Azure VM 執行個體。</p><p>使用下列 PowerShell Cmdlet 將您的 VHD 註冊為 Azure 資料磁碟。提供存放複製的 VHD 的完整容器 URL。</p><code>Add-AzureDisk -DiskName "DataDisk" -MediaLocation "https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk.vhd" -Label "Data Disk"</code><p>複製並儲存這個新 Azure 資料磁碟的名稱。在上述範例中，該名稱是 "DataDisk"。</p></td>
</tr>
</table>

### 建立 DS 系列 Azure VM  

一旦註冊作業系統映像或作業系統磁碟之後，便可以建立新的 DS 系列 Azure VM 執行個體。您將使用您註冊的作業系統映像或作業系統磁碟名稱。從 Premium 儲存體層選取 VM 類型。在下列範例中，我們會使用 “Standard\_DS2” VM 大小。

>[AZURE.NOTE]更新磁碟大小以確定它符合您的容量、效能需求，以及可用的 Azure 磁碟大小。

遵循以下的 PowerShell Cmdlet 逐步建立新的 VM。

首先，設定一般參數：

	$vmSize ="Standard_DS2"
	$adminName = "youradmin"
	$adminPassword = "yourpassword"
	$vmName ="yourVM"
	$location = "East US 2"  

其次，根據您的情況，從您註冊的作業系統映像或作業系統磁碟，建立 Azure VM 執行個體。

<table>
<tr>
<th>案例</th>
<th>步驟</th>
</tr>
<tr>
<td>建立多個 Azure VM 執行個體的一般化的作業系統 VHD</td>
<td>使用您註冊的 <b>Azure 作業系統映像</b>，建立一個或多個新的 DS 系列 Azure VM 執行個體。建立新的 VM 時，在 VM 組態中，指定此作業系統映像名稱，如下所示。</br></br>
<code>$OSImage = Get-AzureVMImage –ImageName “OSImageName” $vm = New-AzureVMConfig -Name "NewVM" -InstanceSize $vmSize -ImageName $OSImage.ImageName</code>
</br></br>
<code>Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm New-AzureVM -ServiceName "NewVM"</code></td></tr>  
<tr>
<td>建立單一 Azure VM 執行個體的唯一作業系統 VHD</td>
<td>使用您註冊的 <b>Azure 作業系統磁碟</b>，建立新的 DS 系列 Azure VM 執行個體。建立新的 VM 時，在 VM 組態中，指定此作業系統磁碟名稱，如下所示。</br></br>
<code>$OSDisk = Get-AzureDisk –DiskName “OSDisk” $vm = New-AzureVMConfig -Name “NewVM” -InstanceSize $vmSize -DiskName $OSDisk.DiskName</code>
</br></br>  
<code>Add-AzureProvisioningConfig -Windows –AdminUserName $adminUser -Password $adminPassword –VM $vm New-AzureVM -ServiceName "NewVM"</code>
</td>
</tr>
</table>

指定其他 Azure VM 資訊，例如雲端服務、區域、儲存體帳戶、可用性設定組以及快取原則。請注意，VM 執行個體必須與相關聯的作業系統或資料磁碟共置，因此，選取的雲端服務、區域和儲存體帳戶全都必須與這些磁碟的基礎 VHD 位於相同的位置。

### 連接資料磁碟  

最後，如果您已經註冊資料磁碟 VHD，請將它們連接至新的 DS 系列 Azure VM。

使用下列 PowerShell Cmdlet，將資料磁碟連接至新的 VM，並指定快取原則。在以下的範例中，快取原則設為「唯讀」。

	$vmName ="yourVM"
	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
	Add-AzureDataDisk -ImportFrom -DiskName “DataDisk” -LUN 0 –HostCaching ReadOnly –VM $vm | Update-AzureVM  

>[AZURE.NOTE]可能有一些支援應用程式所需的特定步驟不包含在本指南中。


## 後續步驟  

如需移轉虛擬機器的特定案例，請參閱下列資源

- [在儲存體帳戶之間移轉 Azure 虛擬機器](http://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)  
- [建立並上傳 Windows Server VHD 到 Azure。](virtual-machines-create-upload-vhd-windows-server.md)  
- [建立及上傳包含 Linux 作業系統的虛擬硬碟](virtual-machines-linux-create-upload-vhd.md)  
- [將虛擬機器從 Amazon AWS 移轉至 Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)  

若要深入了解 Azure 儲存體和 Azure 虛擬機器，也請參閱下列資源

- [Azure 儲存體](http://azure.microsoft.com/documentation/services/storage/)   
- [Azure 虛擬機器](http://azure.microsoft.com/documentation/services/virtual-machines/)  
- [Premium 儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](storage-premium-storage-preview-portal.md)  



[1]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image1.png
[2]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image2.png
[3]: ./media/storage-migration-to-premium-storage/storage-migration-to-premium-storage-image3.png
<!--HONumber=52-->
