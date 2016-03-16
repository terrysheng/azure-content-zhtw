<properties
	pageTitle="上傳資源管理員的 Windows VHD | Microsoft Azure"
	description="了解如何上傳以 Windows 為基礎的虛擬機器，以搭配資源管理員部署模型一起使用。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="dkshir"/>

# 將 Windows VM 映像上傳至 Microsoft Azure 供資源管理員部署使用

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-upload-vhd-windows-server.md)。


本文說明如何使用 Windows 作業系統上傳虛擬硬碟 (VHD)，讓您可以使用它來透過資源管理員部署模型建立新的 Windows 虛擬機器。如需 Microsoft Azure 中磁碟和 VHD 的詳細資訊，請參閱[關於虛擬機器的磁碟和 VHD](virtual-machines-disks-vhds.md)。



## 必要條件

本文假設您已經：

1. **Azure 訂用帳戶** - 如果您沒有帳戶，請[免費申請 Azure 帳戶](/pricing/free-trial/?WT.mc_id=A261C142F)。您將取得信用額度來試用 Azure 付費服務，即使額度用完，您仍可保留帳戶，並使用免費的 Azure 服務，例如「網站」。除非您明確變更您的設定，否則我們不會從您的信用卡收取任何費用。您也可以[啟用 MSDN 訂戶優惠](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。MSDN 訂用帳戶每月會提供信用額度，供您用於 Azure 付費服務。

2. **Microsoft Azure PowerShell 1.0.x** - 請確定您已安裝 Microsoft Azure PowerShell 1.0.x 版。如果尚未安裝，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。建議您使用 1.0 版及更新版本，因為新的資源管理員功能不會加入至舊版 PowerShell。請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/) 來深入了解版本之間的差異。

3. **執行 Windows 作業系統的虛擬機器** - 有許多工具可讓您在內部部署建立虛擬機器。例如，您可以使用 Hyper-V 管理員建立虛擬機器和安裝作業系統。如需相關指示，請參閱[安裝 Hyper-V 角色及設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。如需支援哪些作業系統的詳細資料，請參閱 [Microsoft Azure 虛擬機器的 Microsoft 伺服器軟體支援](https://support.microsoft.com/kb/2721672)。


## 請確定 VM 具有正確的檔案格式

Microsoft Azure 只能接受以 VHD 檔案格式儲存的[第 1 代虛擬機器](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx)映像。VHD 大小必須固定且為整數 MB。允許的 VHD 大小上限為 1023GB。

- Hyper-V 管理員通常會將您的 VM 映像儲存為 VHDX 格式，但 Microsoft Azure 不支援此格式。您可以使用 Hyper-V 或 [Convert-VHD PowerShell Cmdlet](http://technet.microsoft.com/library/hh848454.aspx)，將它轉換為 VHD 格式。關於使用 PowerShell 的步驟，請參閱[將 Hyper-V .vhdx 轉換為 .vhd 檔案格式](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/)。或者，在 Hyper-V 中，從左邊選取本機電腦，然後在上方功能表中按一下 [動作] > [編輯磁碟...]。按 [下一步] 並輸入這些選項來瀏覽畫面：VHDX 檔案的路徑 > [轉換] > [VHD] > [固定大小] > 新的 VHD 檔案的路徑。按一下 [完成] 以關閉。

- 如果您有 [VMDK 檔案格式](https://en.wikipedia.org/wiki/VMDK)的 Windows VM 映像，您可以使用 [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) 將它轉換為 VHD 格式。如需詳細資訊，請參閱部落格：[如何將 VMWare VMDK 轉換為 Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)。


## 準備將 VHD 上傳

本節說明如何將您的 Windows 虛擬機器一般化。但這這麼做除了會移除某些資訊之外，也會移除所有您個人的帳戶資訊。通常是在您想利用此 VM 映像來迅速部署類似的虛擬機器時，才會這麼做。如需 Sysprep 的詳細資訊，請參閱[如何使用 Sysprep：簡介](http://technet.microsoft.com/library/bb457073.aspx)。

1. 登入 Windows 虛擬機器。

2. 以系統管理員身分開啟 [命令提示字元] 視窗。切換至 **%windir%\\system32\\sysprep** 目錄，然後執行 `sysprep.exe`。

3. 在 [系統準備工具] 對話方塊中，執行下列動作：

	1. 在 [系統清理動作] 中選取 [Enter System Out-of-Box Experience (OOBE)]，並確認 [一般化] 已勾選。

	2. 在 [關機選項]中選取 [關機]。

	3. 按一下 [確定]。

	![啟動 Sysprep](./media/virtual-machines-upload-image-windows-resource-manager/sysprepgeneral.png)

</br>
## 建立或尋找 Azure 儲存體帳戶

您在 Azure 中需要有儲存體帳戶才能上傳 VM 映像。您可以使用現有的儲存體帳戶或建立新帳戶。您可以使用入口網站或 PowerShell 來執行此動作。

### 使用入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 按一下 [瀏覽] > [儲存體帳戶]。

3. 檢查您想要用來上傳此映像的儲存體帳戶是否存在。記下此儲存體帳戶的名稱。如果您使用現有的儲存體帳戶，您可以移至[上傳 VM 映像](#uploadvm)一節。

4. 如果您想要建立新的儲存體帳戶，請按一下 [新增] 並輸入下列資訊：

	1. 輸入儲存體帳戶的 [名稱]。只能包含 3 至 24 個小寫字母和數字。此名稱會成為您從儲存體帳戶存取 blob、檔案和其他資源時所用的 URL 的一部分。

	2. 選取您想要建立的儲存體帳戶的 [類型]。如需詳細資訊，請參閱[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。

	3. 輸入 [資源群組] 的名稱。如果入口網站找不到該名稱的現有資源群組，則會建立新的資源群組。

	4. 選擇儲存體帳戶的 [位置]。

	5. 按一下 [建立]。帳戶現在出現於 [儲存體帳戶] 面板下方。

		![輸入儲存體帳戶詳細資料](./media/virtual-machines-upload-image-windows-resource-manager/portal_create_storage_account.png)

	6. 此步驟和接下來的步驟示範如何在此儲存體帳戶中建立 blob 容器。這是選擇性的，因為用於上傳映像的 PowerShell 命令也可以為映像建立新的 blob 容器。如果您不想自行建立，請移至[上傳 VM 映像](#uploadvm)一節。否則，請按一下 [服務] 磚中的 [Blob]。

		![Blob 服務](./media/virtual-machines-upload-image-windows-resource-manager/portal_create_blob.png)

	7. 當 blob 面板出現後，按一下 [+ 容器] 來建立新的 blob 儲存體容器。輸入容器的名稱和存取類型。

		![建立新的 blob](./media/virtual-machines-upload-image-windows-resource-manager/portal_create_container.png)

  		> [AZURE.NOTE] 容器預設為私人，且只能由帳戶擁有者存取。若要允許對容器中的 blob 進行公開讀取存取，但不允許存取容器屬性和中繼資料，請使用 [Blob] 選項。若要允許對容器和 blob 進行完整的公開讀取存取，請使用 [容器] 選項。

	8. [Blob 服務] 面板會列出新的 blob 容器。記下此容器的 URL，使用 PowerShell 命令上傳映像時需要此資訊。根據 URL 的長度和螢幕解析度而定，URL 的一部分可能會隱藏。如果發生這種情況，請按一下右上角的*最大化*圖示，將面板放到最大。


### 使用 PowerShell

1. 開啟 Azure PowerShell 1.0.x，並登入您的 Azure 帳戶。

		Login-AzureRmAccount

	這個命令將會開啟能讓您輸入 Azure 認證的快顯視窗。

2. 如果預設選取的訂用帳戶識別碼與您想要使用的不同，請使用下列其中一個命令來設定正確的訂用帳戶。

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	或

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	您可以利用命令 `Get-AzureRmSubscription` 來尋找您的 Azure 帳戶擁有的訂用帳戶。

3. 尋找此訂用帳戶下可用的儲存體帳戶。

		Get-AzureRmStorageAccount

	如果您想要使用現有的儲存體帳戶，請移至[上傳 VM 映像](#uploadvm)一節。

4. 如果您想要建立新的儲存體帳戶來存放此映像，請遵循下列步驟：

	1. 請確定您有此儲存體帳戶的資源群組。使用下列命令找出您的訂用帳戶中的所有資源群組：

			Get-AzureRmResourceGroup

	2. 如果您想要建立新的資源群組，請使用此命令：

			New-AzureRmResourceGroup -Name YourResourceGroup -Location "West US"

	3. 使用下列命令在此資源群組中建立新的儲存體帳戶：

			New-AzureRmStorageAccount -ResourceGroupName YourResourceGroup -Name YourStorageAccountName -Location "West US" -Type "Standard_GRS"


</br> <a id="uploadvm"></a>
## 將 VM 映像上傳至儲存體帳戶

在 Azure PowerShell 中使用下列步驟，將 VM 映像上傳至儲存體帳戶。您的映像將會上傳至此帳戶中的 Blob 儲存體。您可以使用現有的容器或建立新的容器。

1. 使用 `Login-AzureRmAccount` 登入 Azure PowerShell 1.0.x，並使用 `Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` 來確定您使用正確的訂用帳戶 (如上一節所述)。

2. 使用 [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) 將一般化 Azure VHD 新增至儲存體帳戶：

		Add-AzureRmVhd -ResourceGroupName YourResourceGroup -Destination "<StorageAccountURL>/<BlobContainer>/<TargetVHDName>.vhd" -LocalFilePath <LocalPathOfVHDFile>

	其中：- **StorageAccountURL** 是儲存體帳戶的 URL。通常是這種格式：`https://YourStorageAccountName.blob.core.windows.net`。請注意，您必須使用現有或新的儲存體帳戶的名稱取代 **YourStorageAccountName**。- **BlobContainer** 是您要用來儲存映像的 blob 容器。如果此 Cmdlet 找不到具有此名稱的現有 blob 容器，它會為您建立新的 blob 容器。- **TargetVHDName** 是您想用來儲存映像的名稱。- **LocalPathOfVHDFile** 本機電腦上的 .vhd 檔案的完整路徑和名稱。

	成功執行的 `Add-AzureRmVhd` 看起來像這樣：

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file  C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

	此命令需要一些時間才能完成，視網路連線和 VHD 檔案的大小而定。

</br>
## 從已上傳的映像部署新的 VM

現在，您可以使用已上傳的映像來建立新的 Windows VM。下列步驟說明如何使用 Azure PowerShell 及上述步驟所上傳的 VM 映像，在新的虛擬網路中建立 VM。

>[AZURE.NOTE] VM 映像應該位於將會建立實際虛擬機器的儲存體帳戶中。

### 建立網路資源

請使用下列範例 PowerShell 指令碼，來為新的 VM 設定虛擬網路和 NIC。針對 **$** 符號所代表的變數，請使用適合您的應用程式的值。

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### 建立新的 VM

下列 PowerShell 指令碼示範如何設定虛擬機器組態，以及使用已上傳的 VM 映像作為新安裝的來源。</br>

	#Enter a new username and password in the pop-up for the following
	$cred = Get-Credential

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from image (-CreateOption fromImage) and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You can find this URL in the result of the Add-AzureRmVhd cmdlet above
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

例如，您的工作流程看起來可能像這樣：

		C:\> $pipName = "testpip6"
		C:\> $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
		C:\> $subnet1Name = "testsub6"
		C:\> $nicname = "testnic6"
		C:\> $vnetName = "testvnet6"
		C:\> $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix
		C:\> $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig
		C:\> $nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
		C:\> $vmName = "testupldvm6"
		C:\> $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"
		C:\> $computerName = "testupldcomp6"
		C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
		C:\> $osDiskName = "testupos6"
		C:\> $osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
		C:\> $urlOfUploadedImageVhd = "https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd"
		C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
		C:\> $result = New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
		C:\> $result
		RequestId IsSuccessStatusCode StatusCode ReasonPhrase
		--------- ------------------- ---------- ------------
		                         True         OK OK

您在 [Azure 入口網站](https://portal.azure.com)的 [瀏覽] > [虛擬機器] 中應該會看到新建立的 VM，或者，也可以使用下列 PowerShell 命令：

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## 後續步驟

若要使用 Azure PowerShell 管理新的虛擬機器，請參閱[使用 Azure 資源管理員和 PowerShell 管理虛擬機器](virtual-machines-deploy-rmtemplates-powershell.md)。

<!---HONumber=AcomDC_0211_2016-->