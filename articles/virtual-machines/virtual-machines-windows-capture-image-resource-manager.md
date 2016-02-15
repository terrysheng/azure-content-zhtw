<properties
	pageTitle="在資源管理員中擷取 Windows VM |Microsoft Azure"
	description="了解如何擷取以 Azure 資源管理員部署模型建立的 Windows 式 Azure 虛擬機器 (VM) 的映像。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="dkshir"/>


# 如何在資源管理員部署模型中擷取 Windows 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-capture-image-windows-server.md)。


本文章說明如何使用 Azure PowerShell 來擷取執行 Windows 的 Azure 虛擬機器，讓您能用來建立其他的虛擬機器。此映像包含作業系統磁碟和連結至虛擬機器的資料磁碟。但並不包含建立 Windows VM 所需的虛擬網路資源，因此您必須先設定這些資源，才能建立另一個使用該映像的虛擬機器。此映像也將準備用來當做[一般化的 Windows 映像](https://technet.microsoft.com/library/hh824938.aspx)。


## 先決條件

這些步驟假設您已在資源管理員部署模型中建立 Azure 虛擬機器，且已設定好作業系統，包括連接任何資料磁碟，以及進行其他自訂作業 (例如安裝應用程式)。如果您還沒完成這些工作，請參閱[如何以資源管理員和 PowerShell 建立 Windows VM](virtual-machines-create-windows-powershell-resource-manager.md)。您也可以利用 [Azure 入口網站](https://portal.azure.com)來輕鬆地建立 Windows 虛擬機器。請參閱[如何在 Azure 入口網站中建立 Windows 虛擬機器](virtual-machines-windows-tutorial.md)。


## 準備 VM 來擷取映像

本節說明如何將您的 Windows 虛擬機器一般化。但這這麼做除了會移除某些資訊之外，也會移除所有您個人的帳戶資訊。通常是在您想利用此 VM 映像來迅速部署類似的虛擬機器時，才會這麼做。

1. 登入您的 Windows 虛擬機器。請在 [Azure 入口網站](https://portal.azure.com)中，依序前往 [瀏覽] > [虛擬機器] > 您的 Windows 虛擬機器 > [連線]。

2. 以系統管理員身分開啟 [命令提示字元] 視窗。

3. 切換至 `%windir%\system32\sysprep` 目錄，然後執行 sysprep.exe。

4. 在 [系統準備工具] 對話方塊中進行下列步驟：

	- 在 [系統清理動作] 中選取 [Enter System Out-of-Box Experience (OOBE)]，並確認 [一般化] 已勾選。如需 Sysprep 的詳細用法，請參閱[如何使用 Sysprep：簡介](http://technet.microsoft.com/library/bb457073.aspx)。

	- 在 [關機選項]中選取 [關機]。

	- 按一下 [確定]。

	![執行 Sysprep](./media/virtual-machines-windows-capture-image-resource-manager/SysprepGeneral.png)

5.	Sysprep 會關閉虛擬機器。在 Azure 入口網站中，該虛擬機器的狀態會變更成 **已停止**。


</br>
## 擷取 VM

您可以使用 Azure PowerShell，或是新的 Azure 資源管理員 (ARM) 總管工具來擷取一般化的 Windows VM。本節將分別說明這兩種工具的使用步驟。

### 使用 PowerShell

本文章假設您已安裝 Azure PowerShell 版本 1.0.x。我們建議您使用這個版本，因為較舊版本的 PowerShell 將不會加入新的資源管理員功能。請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/) 來深入了解版本之間的差異。

1. 開啟 Azure PowerShell 1.0.x，並登入您的 Azure 帳戶。

		Login-AzureRmAccount

	這個命令將會開啟能讓您輸入 Azure 認證的快顯視窗。

2. 如果預設選取的訂用帳戶識別碼與您想要使用的不同，請使用下列其中一個命令來設定正確的訂用帳戶。

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	或

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	您可以利用命令 `Get-AzureRmSubscription` 來尋找您 Azure 帳戶擁有的訂用帳戶。

3. 現在，您必須把此虛擬機器所使用的資源解除配置。

		Stop-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM

	您會看到 Azure 入口網站上 VM 的狀態已經從 [已停止] 變成 [已停止 (已解除配置)]。

	>[AZURE.TIP] 您也可以在 PowerShell 中尋找您虛擬機器的狀態，方法是使用：</br> `$vm = Get-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM -status`</br> `$vm.Statuses`</br>[DisplayStatus] 欄位即與 Azure 入口網站的 [狀態] 相對應。

4. 接下來，您必須把虛擬機器的狀態設定為 [一般化]。請注意，您必須這麼做的原因，是上面的一般化步驟 (`sysprep`) 並不會以 Azure 能了解的方式這麼做。

		Set-AzureRmVm -ResourceGroupName YourResourceGroup -Name YourWindowsVM -Generalized

	>[AZURE.NOTE] 上面所設定的一般化狀態將不會出現在入口網站中。不過，您可以利用 Get AzureRmVM 命令來確認狀態，如上方的提示中所示。

5. 使用這個命令，把虛擬機器的映像擷取到目標儲存體容器中。

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -VMName YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	`-Path` 變數是可省略的，且能用來在本機儲存 JSON 範本。 `-DestinationContainerName` 變數就是您要用來保存映像的容器名稱。已儲存映像的 URL 會類似 `https://YourStorageAccountName.blob.core.windows.net/system/Microsoft.Compute/Images/YourImagesContainer/YourTemplatePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`。它將建立在與原始虛擬機器的儲存體帳戶相同的儲存體帳戶中。

	>[AZURE.NOTE] 如要尋找映像的位置，請開啟本機的 JSON 檔案範本。請依序前往 [資源] > [storageProfile] > [osDisk] > [映像] > [uri] 區段，來取得您映像的完整路徑。目前，由於儲存體帳戶中的 [system] 容器是隱藏的，您無法在入口網站中輕鬆地查看這些映像。因此，雖然 `-Path` 變數是可省略的，我們強烈建議您使用該變數把範本儲存在本機，讓您能輕鬆地找出映像的 URL。或者，您也可以使用稱為「Azure 儲存體總管」的工具，我們將在下一節加以說明。


### 使用 Azure 資源總管 (預覽版)

[Azure 資源總管 (預覽版)](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/) 是我們新開發的工具，用來管理在資源管理員部署模型中建立的 Azure 資源。您可以利用這個工具來輕鬆地

- 探索 Azure 資源管理 API、
- 取得 API 文件，以及
- 直接在您的 Azure 訂用帳戶中進行 API 呼叫。

如要深入了解您能利用這強大工具來完成的所有工作，請觀賞以下影片：[Azure Resource Manager Explorer with David Ebbo (與 David Ebbo 一起探討 Azure 資源管理員)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)。

您可以使用資源總管來擷取虛擬機器，來做為 PowerShell 方法的替代方案。

1. 開啟 [資源總管網站](https://resources.azure.com/)，並登入您的 Azure 帳戶。

2. 在工具右上角，選取 [讀取/寫入] 來允許進行 _PUT_ 及 _POST_ 作業。預設設定為 [唯讀]，這代表您只能進行 _GET_ 作業。

	![資源總管：讀取/寫入](./media/virtual-machines-windows-capture-image-resource-manager/ArmExplorerReadWrite.png)

3. 接下來，尋找您的 Windows 虛擬機器。您可以在工具頂端的搜尋方塊中輸入虛擬機器的名稱，或是從左側的功能表前往您的虛擬機器，方法是依序瀏覽 [訂用帳戶] > 您的 Azure 訂用帳戶 > [resourceGroups] > 您的資源群組 > [提供者] > [Microsoft.Compute] > [virtualMachines] > 您的 Windows 虛擬機器。當您按一下左側導覽列上的虛擬機器時，該虛擬機器的範本會出現在工具的右側。

4. 您應該會在範本頁面的右上角，看到該虛擬機器的各種可用作業的索引標籤。請按一下 [作業 (POST/DELETE)] 索引標籤。

	![資源總管：動作功能表](./media/virtual-machines-windows-capture-image-resource-manager/ArmExplorerActionMenu.png)

5. 您會看到您能在該虛擬機器上執行的所有動作清單。

	![資源總管：動作項目](./media/virtual-machines-windows-capture-image-resource-manager/ArmExplorerActionItems.png)

6. 按一下 [解除配置] 動作按鈕來為該虛擬機器解除配置。VM 的狀態將從 [已停止] 變更成 [已停止 (已解除配置)]。

7. 按一下 [一般化] 動作按鈕，來將該虛擬機器標示為已一般化。如要驗證狀態確實已變更，請按一下左側您虛擬機器名稱下方的 [InstanceView] 功能表，然後瀏覽至右側的 [狀態] 區段。

8. 您可以在 [擷取] 動作按鈕的下方，設定擷取映像所用的值。您填入的值看起來可能會類似下圖。

	![資源總管：擷取](./media/virtual-machines-windows-capture-image-resource-manager/ArmExplorerCaptureAction.png)

	按一下 [擷取] 動作按鈕，來擷取您虛擬機器的映像。這會建立新的映像 VHD 及 JSON 範本檔案，但目前您無法透過資源總管或 [Azure 入口網站](https://portal.azure.com)存取這些檔案。

9. 如要存取新的 VHD 映像及範本，請下載並安裝用來管理儲存體資源的 Azure 工具：[Azure 儲存體總管](http://storageexplorer.com/)。這將會在您的本機電腦上安裝 Azure 儲存體總管。

	- 開啟儲存體總管，並登入您的 Azure 訂用帳戶。這應該會顯示您訂用帳戶可用的所有儲存體帳戶。

	- 您應該會在左側看到我們在上述步驟中所擷取的虛擬機器儲存體帳戶。按兩下儲存體帳戶下方的 [System] 功能表。您應該會在右側看到 [System] 資料夾的內容。

		![儲存體總管：System](./media/virtual-machines-windows-capture-image-resource-manager/StorageExplorer1.png)

	- 按兩下 [Microsoft.Compute]，然後按兩下 [映像]，此時畫面會顯示您所有的映像資料夾。按兩下您剛才從資源總管擷取影像時，為 **destinationContainerName** 變數所輸入的資料夾名稱。這會讓畫面顯示 VHD 及 JSON 範本檔案。

	- 然後，您可以尋找 URL，或是用滑鼠右鍵按一下 VHD/範本來下載檔案。

		![儲存體總管：範本](./media/virtual-machines-windows-capture-image-resource-manager/StorageExplorer2.png)


## 從擷取的映像部署新的 VM

現在，您可以使用擷取的映像來建立新的 Windows VM。下列步驟將說明，如何使用 Azure PowerShell 及在上述步驟中所擷取的 VM 映像，在新的虛擬網路中建立 VM。

>[AZURE.NOTE] VM 映像應該位於將會建立實際虛擬機器的儲存體帳戶中。

### 建立網路資源

請使用下列範例 PowerShell 指令碼，來為新的 VM 設定虛擬網路和 NIC。而對於有 **$** 符號的變數，請使用適合您應用程式的值。

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### 建立新的 VM

下列 PowerShell 指令碼示範如何設定虛擬機器組態，以及如何把擷取的 VM 映像當做新安裝的來源。</br>

	#Enter a new username and password in the pop-up for the following
	$cred = Get-Credential

	#Get the storage account where the captured image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from image (-CreateOption fromImage) and give the URL of the captured image VHD for the -SourceImageUri parameter.
	#We found this URL in the local JSON template in the previous sections.
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfCapturedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

您應該會看到新建立的 VM，方法是前往 [Azure 入口網站](https://portal.azure.com)的 [瀏覽] > [虛擬機器]，或是使用下列 PowerShell 命令：

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## 後續步驟

如要使用 Azure PowerShell 管理您新建立的虛擬機器，請參閱[使用 Azure 資源管理員和 PowerShell 部署以及管理虛擬機器](virtual-machines-deploy-rmtemplates-powershell.md)。

<!---HONumber=AcomDC_0204_2016-->