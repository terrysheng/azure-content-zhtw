<properties 
	pageTitle="Premium 儲存體：Azure 虛擬機器工作負載適用的高效能儲存體" 
	description="了解適用於磁碟的 Azure Premium 儲存體。了解如何建立 Premium 儲存體帳戶。" 
	services="storage" 
	documentationCenter="" 
	authors="Selcin" 
	manager="adinah" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2014" 
	ms.author="selcint"/>


# Premium 儲存體：Azure 虛擬機器工作負載適用的高效能儲存體

歡迎使用「Azure Premium 磁碟儲存體」的公開預覽！

在引進新的 Premium 儲存體之後，Microsoft Azure 現在提供兩種類型的耐久性儲存體：**「Premium 儲存體」**和「Standard 儲存體」。Premium 儲存體是將資料儲存在最新技術的固態硬碟 (SSD) 中，Standard 儲存體則是將資料儲存在硬碟 (HDD) 中。 

Premium 儲存體可為在 Azure 虛擬機器上執行的 I/O 密集工作負載提供高效能、低延遲的磁碟支援。您可以將數個 Premium 儲存體磁碟連結至虛擬機器 (VM)。使用 Premium 儲存體，每一 VM 的應用程式最多擁有 32 TB 的儲存體，每一 VM 可達到 50,000 IOPS (每秒輸入/輸出作業)，讀取作業的延遲極低。Premium 儲存體目前只能在 Azure 虛擬機器使用的磁碟上儲存資料。 

若要註冊 Azure Premium 儲存體預覽，請瀏覽 [Azure Preview](http://azure.microsoft.com/services/preview/) 頁面。

本文提供 Azure Premium 儲存體的深入介紹。

**目錄：**


* [Premium 儲存體重要須知](#important)

* [使用 Premium 磁碟儲存體](#using)

* [使用 Premium 儲存體時的延展性和效能目標](#scale)	

* [使用 Premium 儲存體時的節流](#throttling)	

* [使用 Premium 儲存體時的快照與複製 Blob](#restapi)	

* [使用 Premium 儲存體時的定價和計費資訊](#pricing)	

* [建立和使用 Premium 磁碟儲存體帳戶](#howto1)	

* [其他資源](#see)

###<a id="important">Premium 儲存體重要須知</a>


下列是使用 Premium 儲存體之前或使用 Premium 儲存體時需考量的重要事項清單：

- 若要使用 Premium 儲存體，您必須有 Premium 儲存體帳戶。若要了解如何建立 Premium 儲存體帳戶，請參閱[建立和使用磁碟的 Premium 儲存體帳戶](#howto1)。

- [Microsoft Azure 預覽入口網站](https://portal.azure.com/)中目前已提供 Premium 儲存體且可透過下列 SDK 程式庫來存取：[儲存體 REST API](http://msdn.microsoft.com//library/azure/dd179355.aspx) 2014年-02-14 版或更新版本。[服務管理 REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) 2014年-10-01 版或更新版本。和 [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) 0.8.10 版或更新版本。 

- 下列地區提供有限的 Premium 儲存體預覽：美國西部、美國東部 2 和西歐。

- Premium 儲存體僅支援 Azure 分頁 Blob，用於保存適用於 Azure 虛擬機器 (VM) 的耐久性磁碟。如需 Azure 分頁 Blob 的詳細資訊，請參閱[了解區塊 Blob 和分頁 Blob](http://msdn.microsoft.com/library/azure/ee691964.aspx)。Premium 儲存體不支援 Azure 區塊 Blob、Azure 檔案、Azure 表格或 Azure 佇列。

- Premium 儲存體帳戶是本機備援 (LRS) 帳戶，在單一區域內會保留三份資料。如需使用 Premium 儲存體時關於地理複寫的考量，請參閱本文的[使用 Premium 儲存體時的快照與複製 Blob](#restapi) 一節。

- 如果您希望您的 VM 磁碟使用 Premium 儲存體帳戶，您必須使用 DS 系列的 VM。DS 系列的 VM 可同時使用 Standard 和 Premium 儲存體磁碟。非 DS 系列的 VM 則無法使用 Premium 儲存體磁碟。如需可用的 Azure VM 磁碟類型和大小的詳細資訊，請參閱 [Azure 的虛擬機器和雲端服務大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)。 

- 設定 VM 之 Premium 儲存體磁碟的程序和設定 Standard 儲存體磁碟的程序類似。您必須為您的 Azure 磁碟和 VM 選擇最適合的 Premium 儲存體選項。依 Premium 解決方案的效能特性而定，VM 大小應適合您的工作負載。如需詳細資訊，請參閱[使用 Premium 儲存體時的延展性和效能目標](#scale)。
 
- Premium 儲存體帳戶無法對應到自訂網域名稱。

- Premium 儲存體目前不支援儲存體分析。

###<a id="using">使用 Premium 磁碟儲存體</a>
有兩種方式可使用 Premium 磁碟儲存體：

- 首先建立新的 Premium 儲存體帳戶，然後在建立 VM 時使用。
- 建立新的 DS 系列 VM。建立 VM 時，您可以選取先前建立的 Premium 儲存體帳戶、建立新的 Premium 儲存體帳戶，或讓 Azure 入口網站建立預設的 Premium 帳戶。

Azure 使用儲存體帳戶做為作業系統和資料磁碟的容器。換句話說，如果您建立 Azure DS 系列的 VM 並選取 Azure Premium 儲存體帳戶，您的作業系統和資料磁碟會儲存在該儲存體帳戶中。

為充分利用 Premium 儲存體的優點，請先使用帳戶類型  *Premium_LRS* 建立一個 Premium 儲存體帳戶。若要這樣做，您可以使用 [Microsoft Azure Preview 入口網站](https://portal.azure.com/)、[Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) 或[服務管理 REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx)。如需逐步指示，請參閱[建立和使用 Premium 磁碟儲存體帳戶](#howto1)。

<h4>重要事項：</h4>

- 若要深入了解 Premium 儲存體帳戶的容量和頻寬預覽限制，請參閱[使用 Premium 儲存體時的延展性和效能目標](#scale) 一節。如果您的應用程式需求超出單一儲存體帳戶的延展性目標，請建置使用多個儲存體帳戶的應用程式，並將資料分散到那些儲存體帳戶中。例如，如果要將 51 TB 的磁碟連結到多個 VM，請將它們分散到兩個儲存體帳戶，因為 32 TB 是單一 Premium 儲存體帳戶的限制。
- 依照預設，所有 Premium 資料磁碟的磁碟快取原則都是「唯讀」，所有連接至 VM 的 Premium 作業系統則都是「讀寫」。為使應用程式的 I/O 達到最佳效能，建議使用此組態設定。對於頻繁寫入或唯寫的磁碟 (例如 SQL Server 記錄檔)，停用磁碟快取可獲得更佳的應用程式效能。
- 確定 VM 上有足夠的磁碟流量頻寬。若要深入了解各種 VM 大小可用的磁碟頻寬，請參閱 [Azure 的虛擬機器和雲端服務大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)。
- 您可在 DS 系列的 VM 中同時使用 Premium 和 Standard 儲存體磁碟。
- 使用 Premium 儲存體時，您可以佈建 DS 系列的 VM 並將幾個持續性資料磁碟連接至 VM。如有需要，您可以跨磁碟分割，以增加磁碟區的容量與效能。如果您使用[儲存空間](http://technet.microsoft.com/library/hh831739.aspx)來分割 Premium 儲存體資料磁碟，您應該以「所使用的每個磁碟一個資料行」的方式來設定它。否則分割之磁碟區的整體效能可能會低於預期，因為磁碟之間的流量分配不平均。預設，伺服器管理員使用者介面 (UI) 可讓您設定最多 8 個磁碟資料行。但是如果您的磁碟超過 8 個，您必須使用 PowerShell 來建立磁碟區，並手動指定資料行的數目。否則，即使您有更多磁碟，伺服器管理員 UI 仍會繼續使用 8 個資料行。例如，如果在一組分割中擁有 32 個磁碟，您應該指定 32 個資料行。您可以使用 [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) PowerShell Cmdlet 的  *NumberOfColumns* 參數來指定虛擬磁碟使用的資料行數目。如需詳細資訊，請參閱[儲存空間概觀](http://technet.microsoft.com/library/jj822938.aspx)與[儲存空間常見問題集](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)。


###<a id="scale">使用 Premium 儲存體時的延展性和效能目標</a>
當您為某個 Premium 儲存體帳戶佈建磁碟時，其每秒的輸入/輸出作業 (IOPS) 和輸送量 (頻寬) 取決於磁碟大小。目前有三種類型的 Premium 儲存體磁碟：P10、P20 及 P30。每種各有特定的 IOPS 和輸送量限制，如下表所示：

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Premium 儲存體磁碟類型</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>磁碟大小</strong></td>
	<td>128 GB</td>
	<td>512 GB</td>
	<td>1024 GB (1 TB)</td>
</tr>
<tr>
	<td><strong>每一磁碟的 IOPS</strong></td>
	<td>500</td>
	<td>2300</td>
	<td>5000</td>
</tr>
<tr>
	<td><strong>每一磁碟的輸送量</strong></td>
	<td>每秒 100 MB</td>
	<td>每秒 150 MB</td>
	<td>每秒 200 MB</td>
</tr>
</tbody>
</table>

Azure 會將磁碟大小對應 (無條件進位) 至表格中指定之最接近的 Premium 儲存體磁碟選項。例如，大小為 100 GB 的磁碟會分類為 P10 選項，每秒最多可執行 500 個 IO 單位，每秒輸送量可達 100 MB。同樣地，大小為 400 GB 的磁碟會分類為 P20 選項，每秒最多可執行 2300 個 IO 單位，每秒輸送量可達 150 MB。

輸入/輸出 (I/O) 單位大小為 256 KB。如果要傳送的資料少於 256 KB，會視為單一 I/O 單位。較大的 I/O 大小則會視為大小是 256 KB 的多個 I/O。例如，1100 KB 的 I/O 會視為五個 I/O 單位。

輸送量限制包含磁碟的讀取和寫入。例如，P10 磁碟的讀取和寫入總和應小於或等於每秒 100 MB。同樣地，單一 P10 磁碟可有每秒 100 MB 的讀取或每秒 100 MB 的寫入輸送量，或是每秒 60 MB 的讀取和每秒 40 MB 的寫入輸送量。
 
當您在 Azure 建立磁碟後，請依據您應用程式的容量、效能、延展性和尖峰負載需求來選取最適合的 Premium 儲存體磁碟解決方案。

下表描述 Premium 儲存體帳戶的延展性目標：

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>總帳戶容量</strong></td>
	<td><strong>本機備援儲存體帳戶總頻寬</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>磁碟容量：32 TB</li>
       <li type=round>快照容量：10 TB</li>
    </ul>
	</td>
	<td>每秒最多 50 GB (輸入 + 輸出)</td>
</tr>
</tbody>
</table>

- 輸入是指傳送至某個儲存體帳戶的所有資料 (要求)。
- 輸出是指從某個儲存體帳戶接收的所有資料 (回應)。

如需詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](http://msdn.microsoft.com/library/dn249410.aspx)。


###<a id="throttling">使用 Premium 儲存體時的節流</a>
如果應用程式的 IOPS 或輸送量超出配置的 Premium 儲存體磁碟限制，可能會有節流情況。若要避免節流，建議您依據佈建之磁碟的延展性和效能目標來限制磁碟的擱置 I/O 要求數。  

當您的應用程式設計為避免節流情況時，可以達到最低延遲。但是，如果磁碟的擱置 I/O 要求數過小，您的應用程式便無法達到磁碟最大的 IOPS 和輸送量層級。

下列範例示範如何計算節流層級：

<h4>範例 1：</h4>
在 P10 磁碟上，您的應用程式一秒內有 495 個 16 KB 磁碟大小的 I/O (等於是 495 個 I/O 單位)。如果您在該秒內嘗試 2 MB 的 I/O，I/O 單位的總數會等於 495 + 8。這是因為當 I/O 單位大小是 256 KB 時，2 MB 的 I/O 會產生 2048 KB / 256 KB = 8 個 I/O 單位。因為 495 + 8 的總和超出磁碟 500 的 IOPS 限制，因此會發生節流情況。 

<h4>注意：</h4> 
所有計算都是基於 256 KB 的 I/O 單位大小。

<h4>範例 2：</h4>
在 P10 磁碟上，您的應用程式有 400 個 256 KB 磁碟大小的 I/O。使用的總頻寬為 (400 * 256) / 1024 = 100 MB/秒。請注意，每秒 100 MB 是 P10 磁碟的輸送量限制。如果您的應用程式嘗試在該秒內執行更多 I/O 便會發生節流情況，因為會超出配置的限制。

<h4>注意：</h4>
如果磁碟流量大多包含小型 I/O，您的應用程式在達到輸送量限制前很可能會先達到 IOPS 限制。但是，如果磁碟流量大多包含大型 I/O，您的應用程式可能會達到輸送量限制，而非 IOPS 限制。您可以使用最佳的 I/O 大小和限制磁碟的擱置 I/O 要求數，以最大化應用程式的 IOPS 和輸送量容量。


###<a id="restapi">使用 Premium 儲存體時的快照與複製 Blob</a>
您可以使用 Standard 儲存體時建立快照的方式來為 Premium 儲存體建立快照。由於 Premium 儲存體是本地備援儲存體，建議您建立快照，並將那些快照複製到異地備援的 Standard 儲存體帳戶。如需詳細資訊，請參閱 [Azure 儲存體備援選項](http://msdn.microsoft.com/library/azure/dn727290.aspx)。

如果磁碟已連結至 VM，在備份磁碟的分頁 Blob 上不允許某些 API 作業。例如，只要磁碟連結至 VM，您就無法在該 Blob 上執行[複製 Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) 作業。您必須先使用[快照 Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx) REST API 方法建立該 Blob 的快照，然後對該快照執行[複製 Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) 以複製連結的磁碟。或者，您可以中斷連結磁碟，然後在基礎 Blob 上執行任何必要的作業。

<h4>重要事項：</h4>

- 如果 Premium 儲存體上的複製 Blob 作業會覆寫目的地現有的 Blob，則所覆寫的 Blob 不得含有任何快照。若要在 Premium 儲存體帳戶內或之間進行複製，在初始化複製時，目的地 Blob 不得含有快照。
- 單一 Blob 的快照數限制為 100。每 10 分鐘最多可取得一個快照。
- 10 TB 是每一 Premium 儲存體帳戶的最大快照容量。請注意，快照容量是快照中存在的唯一資料。換句話說，快照容量不包含基底 Blob 大小。
- 您可以使用 AzCopy 或「複製 Blob」來將 Premium 儲存體帳戶中的快照複製到異地備援的 Standard 儲存體帳戶。如需詳細資訊，請參閱[如何在 Microsoft Azure 儲存體使用 AzCopy](http://azure.microsoft.com/documentation/articles/storage-use-azcopy/) 和[複製 Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx)。
- 如需對 Premium 儲存體帳戶中的分頁 Blob 執行 REST 作業的詳細資訊，請參閱 MSDN Library 中的[在 Azure 儲存體帳戶使用 Blob 服務作業](http://go.microsoft.com/fwlink/?LinkId=521969)。


###<a id="pricing">使用 Premium 儲存體時的定價和計費資訊</a>
使用 Premium 儲存體時，需考量下列計費資訊：

- Premium 儲存體磁碟的計費依據是磁碟的佈建大小。Azure 會將磁碟大小 (無條件進位) 對應至[使用 Premium 儲存體時的延展性和效能目標](#scale) 一節的表格中指定之最接近的 Premium 儲存體磁碟選項。任何已佈建的磁碟都是按每月的 Premium 儲存體優惠價格以每小時的方式計費。例如，如果您在佈建完 P10 磁碟的 20 小時後刪除它，則會以 20 小時計算 P10 解決方案的費用。這與寫入磁碟的實際資料量或使用的 IOPS/輸送量無關。
- Premium 儲存體上的快照會因為使用的額外容量而產生費用。如需快照的詳細資訊，請參閱[建立 Blob 的快照](http://msdn.microsoft.com/library/azure/hh488361.aspx)。
- [輸出資料傳輸](http://azure.microsoft.com/pricing/details/data-transfers/) (Azure 資料中心送出的資料) 會產生頻寬使用量費用。

如需 Premium 儲存體與 DS 系列 VM 之定價的詳細資訊，請參閱：

- [Azure 儲存體定價](http://azure.microsoft.com/pricing/details/storage/)
- [虛擬機器定價](http://azure.microsoft.com/pricing/details/virtual-machines/)

###<a id="howto1">建立和使用 Premium 磁碟儲存體帳戶</a>
本節示範如何使用 Azure Preview 入口網站和 Azure PowerShell 來建立 Premium 儲存體帳戶。另外亦示範 Premium 儲存體帳戶的範例使用案例：在使用 Premium 儲存體帳戶時建立虛擬機器，並將資料磁碟連結至虛擬機器。

本節內容：

* [Azure Preview 入口網站：建立 Premium 儲存體帳戶](#howto3)	

* [Azure PowerShell：建立 Premium 儲存體帳戶並使用它執行基本 VM 作業](#howto2)	
 
###<a id="howto3">Azure Preview 入口網站：建立 Premium 儲存體帳戶</a>
本節說明如何使用 Azure Preview 入口網站來建立 Premium 儲存體帳戶。

1.	登入 [Azure Preview 入口網站](https://portal.azure.com/)。如果您尚未訂閱，請查看[免費試用](http://azure.microsoft.com/pricing/free-trial/)優惠。 


    >[AZURE.NOTE] 如果您登入 Azure 管理入口網站時，請按一下您在入口網站右上角的使用者帳戶名稱。然後按一下 [切換至新的入口網站]。
        

2.	在 [中心] 功能表上，按一下 [新增]。

3.	在 [新增] 下方，按一下 [所有項目]。選取 [儲存體]、[快取] 和 [+ 備份]。在該處按一下 [儲存體]，然後按一下 [建立]。

4.	在 [儲存體帳戶] 刀鋒視窗中，輸入儲存體帳戶名稱。按一下 [定價層]。在 [建議的定價層] 刀鋒視窗中，按一下 [瀏覽所有定價層]。在 [選擇您的定價層] 刀鋒視窗中，選擇 [Premium 本地備援]。按一下 [選取]。請注意，[儲存體帳戶] 刀鋒視窗預設會顯示 [Standard-GRS] 為 [定價層]。在您按一下 [選取] 後，[定價層] 就會顯示為 [Premium LRS]。
	
	![Pricing Tier][Image1]

	
5.	在 [儲存體帳戶] 刀鋒視窗中，保留 [資源群組]、[訂閱]、[位置] 和 [診斷] 的預設值。按一下 [建立]。

如需 Azure 環境的完整逐步解說，請參閱[在 Azure Preview 入口網站中建立執行 Windows 的虛擬機器](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/)。

###<a id="howto2">Azure PowerShell：建立 Premium 儲存體帳戶並使用它執行基本 VM 作業</a>
本節說明在建立虛擬並將資料磁碟連結至 VM 時，如何使用 Azure PowerShell 來建立 Premium 儲存體帳戶及如何使用它。

1. 依照[如何安裝和設定 Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)中提供的步驟設定您的 PowerShell 環境。
2. 啟動 PowerShell 主控台，連接至您的訂閱，並在主控台視窗中執行下列 PowerShell Cmdlet。如此 PowerShell 陳述式所示，當您建立 Premium 儲存體帳戶時，必須將 **Type** 參數指定為 **Premium_LRS**。

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

3. 接著建立新的 DS 系列 VM，並在主控台視窗中執行下列 PowerShell Cmdlet 以指定您要使用 Premium 儲存體：

    	$storageAccount = "yourpremiumaccount"
    	$adminName = "youradmin"
    	$adminPassword = "yourpassword"
    	$vmName ="yourVM"
    	$location = "West US"
    	$imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
    	$vmSize ="Standard_DS2"
    	$OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
    	$vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
    	Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
    	New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

4. 如果您希望 VM 有更多磁碟空間，在虛擬機器建立後於主控台視窗中執行下列 PowerShell Cmdlet 以將新的資料磁碟連結至現有的 DS 系列 VM。

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

###<a id="see">其他資源</a>
[在 Azure 儲存體帳戶使用 Blob 服務作業](http://go.microsoft.com/fwlink/?LinkId=521969)

[建立執行 Windows 的虛擬機器](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/)

[Azure 的虛擬機器和雲端服務大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)

[儲存體文件](http://azure.microsoft.com/documentation/services/storage/)

[MSDN 參考](http://msdn.microsoft.com/library/azure/gg433040.aspx)

[Image1]: ./media/storage-premium-storage-preview-portal/Azure_pricing_tier.png

<!--HONumber=42-->
 