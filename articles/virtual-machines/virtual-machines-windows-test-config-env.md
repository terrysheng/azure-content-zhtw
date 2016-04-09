<properties
	pageTitle="基本設定測試環境與 Azure 資源管理員"
	description="了解如何建立簡單的開發/測試環境以使用資源管理員模擬 Microsoft Azure 中簡化的內部網路。"
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="josephd"/>

# 基本設定測試環境與 Azure 資源管理員

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-test-config-env.md)。

本文將逐步解說如何利用在資源管理員中建立的虛擬機器，在 Microsoft Azure 虛擬網路中建立「基本設定」測試環境。

產生的測試環境可以用於：

- 開發和測試應用程式。
- 如同您自己設計的擴充測試環境的初始設定，其包含其他虛擬機器和 Azure 服務。

「基本設定」測試環境是由名為 TestLab 的純雲端虛擬網路中的公司子網路構成的，可以模擬簡化的私人內部網路。

![](./media/virtual-machines-windows-test-config-env/BC_TLG04.png)

它包含：

- 一部執行 Windows Server 2012 R2 的 Azure 虛擬機器，其名稱是 DC1 而且被設定成內部網路網域控制器和網域名稱系統 (DNS) 伺服器。
- 一部執行 Windows Server 2012 R2 的 Azure 虛擬機器，其名稱是 APP1 而且被設定成一般應用程式和網頁伺服器。
- 一部執行 Windows Server 2012 R2 的 Azure 虛擬機器，其名稱是 CLIENT1 而且充當內部網路用戶端。

這種設定可以讓 DC1、APP1、CLIENT1 和其他公司網路子網路電腦變成：

- 連線到網際網路來安裝更新、即時存取網際網路資源，以及參與公用雲端技術，例如 Microsoft Office 365 和其他 Azure 服務。
- 利用遠端桌面連線，從連接網際網路或公司網路的電腦進行遠端管理。

在 Azure 中設定 Windows Server 2012 R2 基本設定測試環境的公司子網路，共分 4 個階段。。

1.	建立虛擬網路
2.	設定 DC1。
3.	設定 APP1。
4.	設定 CLIENT1。

如果您還沒有 Azure 帳戶，請到[試用 Azure](https://azure.microsoft.com/pricing/free-trial/) 申請免費試用。如果您有 MSDN 訂用帳戶，請參閱 [MSDN 訂戶的 Azure 權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

> [AZURE.NOTE] Azure 中的虛擬機器執行時，需要支付相關費用。這項成本是按照您的免費試用版、MSDN 訂用帳戶或付費訂用帳戶進行計算。如需詳細了解 Azure 虛擬機器的執行成本，請參閱[虛擬機器價格詳細資料](https://azure.microsoft.com/pricing/details/virtual-machines/)和 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)。為了降低成本，請參閱[將 Azure 的測試環境虛擬機器費用降至最低](#costs)。

## 階段 1：建立虛擬網路

首先，開啟 Azure PowerShell 提示字元。

> [AZURE.NOTE] 下列命令集使用 Azure PowerShell 1.0 版和更新版本。如需詳細資訊，請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)。

登入您的帳戶。

	Login-AzureRMAccount

使用下列命令取得您的訂用帳戶名稱。

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

設定您的 Azure 訂用帳戶以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

接下來，為基本設定測試實驗室建立新資源群組。若要判斷唯一的資源群組名稱，請使用此命令列出現有的資源群組。

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

使用下列命令建立新的資源群組。以正確的名稱取代括號中的所有內容，包括 < and > 字元。

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

以資源管理員為基礎的虛擬機器需要以資源管理員為基礎的儲存體帳戶。您必須為儲存體帳戶挑選只包含小寫字母和數字的全域唯一名稱。您可以使用此命令列出現有的儲存體帳戶。

	Get-AzureRMStorageAccount | Sort StorageAccountName | Select StorageAccountName

請使用這些命令建立新測試環境的新儲存體帳戶。

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<storage account name>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

接下來，您可以建立 TestLab Azure 虛擬網路，用它來架設公司子網路基本設定。

	$rgName="<name of your new resource group>"
	$locName="<Azure location name, such as West US>"
	$corpnetSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name Corpnet -AddressPrefix 10.0.0.0/24
	New-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/8 -Subnet $corpnetSubnet –DNSServer 10.0.0.4

這是您目前的組態。

![](./media/virtual-machines-windows-test-config-env/BC_TLG01.png)

## 階段 2：設定 DC1。

DC1 是 corp.contoso.com Active Directory 網域服務 (AD DS) 網域的網域控制站，也是 TestLab 虛擬網路的虛擬機器 DNS 伺服器。

首先，填寫資源群組的名稱、Azure 位置以及儲存體帳戶名稱，然後在本機電腦的 Azure PowerShell 命令提示字元執行下列命令，為 DC1 建立 Azure 虛擬機器。

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzureRMPublicIpAddress -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRMNetworkInterface -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 10.0.0.4
	$vm=New-AzureRMVMConfig -VMName DC1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

接著，連接到 DC1 虛擬機器。

1.	在 Azure 入口網站中，按一下 [**虛擬機器**]，然後按一下 [**DC1**] 虛擬機器。  
2.	在 [**DC1**] 窗格中按一下 [**連接**]。
3.	出現提示時，開啟下載的 DC1.rdp 檔案。
4.	顯示 [遠端桌面連線] 訊息方塊後，按一下 [連接]。
5.	出現輸入認證的提示時，使用下列：
- 名稱：**DC1\**[本機系統管理員帳戶名稱]
- 密碼：[本機系統管理員帳戶密碼]
6.	顯示憑證相關的 [遠端桌面連線] 訊息方塊提示時，按一下 [是]。

接著，將額外的資料磁碟新增為磁碟機代號 F: 的新磁碟區。

1.	在 [伺服器管理員] 的左窗格中，按一下 [檔案和存放服務]，然後按一下 [磁碟]。
2.	在 [內容] 窗格的 [磁碟] 群組中，按一下 [磁碟 2] ([磁碟分割] 設為 [不明])。
3.	按一下 [工作]，然後按一下 [新增磁碟區]。
4.	在 [新增磁碟區精靈] 的 [在您開始前] 頁面上，按 [下一步]。
5.	在 [選取伺服器和磁碟] 頁面上，按一下 [磁碟 2]，然後按 [下一步]。出現提示時，按一下 **[確定]**。
6.	在 [指定磁碟區大小] 頁面上，按 [下一步]。
7.	在 [指派成磁碟機代號或資料夾] 頁面上，按 [下一步]。
8.	在 [選取檔案系統設定] 頁面上，按 [下一步]。
9.	在 [確認選取項目] 頁面上，按一下 [建立]。
10.	完成時，按一下 [關閉]。

接著，將 DC1 設定為 corp.contoso.com 網域的網域控制站和 DNS 伺服器。在系統管理員層級 Windows PowerShell 命令提示字元，執行下列命令。

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSForest -DomainName corp.contoso.com -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

請注意，這些命令可能需要幾分鐘才能完成。

DC1 重新啟動之後，重新連接到 DC1 的虛擬機器。

1.	在 Azure 入口網站中，按一下 [**虛擬機器**]，然後按一下 [**DC1**] 虛擬機器。
2.	在 [**DC1**] 窗格中按一下 [**連接**]。
3.	提示開啟 DC1.rdp 時，按一下 [開啟]。
4.	顯示 [遠端桌面連線] 訊息方塊後，按一下 [連接]。
5.	出現輸入認證的提示時，使用下列：
- 名稱：**CORP\**[本機系統管理員帳戶名稱]
- 密碼：[本機系統管理員帳戶密碼]
6.	顯示憑證相關的 [遠端桌面連線] 訊息方塊提示時，按一下 [是]。

接下來，在 Active Directory 中建立一個當使用者登入 CORP 網域成員電腦時，就會用到的使用者帳戶。於系統管理員層級 Windows PowerShell 命令提示字元下執行下列命令。

	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

請注意，此命令會提示您提供 User1 帳戶密碼。因為這個帳戶會用於所有 CORP 網域成員電腦的遠端桌面連線，所以請選取強式密碼。要檢查密碼強度，請參閱[密碼檢查程式：使用強式密碼](https://www.microsoft.com/security/pc-security/password-checker.aspx)。記錄 User1 帳戶的密碼，然後儲存在安全的位置。

接著，將新的 User1 帳戶設為企業系統管理員。於系統管理員層級 Windows PowerShell 命令提示字元下執行下列命令。

	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

關閉具有 DC1 的遠端桌面工作階段，然後使用 CORP\\User1 帳戶重新連接。

接下來，在系統管理員層級 Windows PowerShell 命令提示字元，執行這個命令，允許 Ping 工具連接網路。

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

這是您目前的組態。

![](./media/virtual-machines-windows-test-config-env/BC_TLG02.png)

## 階段 3：設定 APP1

APP1 提供網頁和檔案共用服務。

首先，填寫資源群組的名稱、Azure 位置以及儲存體帳戶名稱，然後在本機電腦的 Azure PowerShell 命令提示字元執行下列命令，為 APP1 建立 Azure 虛擬機器。

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzureRMPublicIpAddress -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRMNetworkInterface -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureRMVMConfig -VMName APP1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for APP1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName APP1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/APP1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name APP1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

接下來，使用 APP1 本機系統管理員帳戶名稱和密碼連接到 APP1 虛擬機器，然後開啟系統管理員層級 Windows PowerShell 命令提示字元。

要檢查 APP1 和 DC1 之間的名稱解析和網路通訊，請執行 **ping dc1.corp.contoso.com** 命令，然後確定顯示 4 個回覆。

接下來，使用 Windows PowerShell 提示字元中的下列命令將 APP1 虛擬機器加入至 CORP 網域。

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

請注意，您必須在輸入 Add-Computer 命令後提供您的 CORP\\User1 網域帳戶認證。

重新啟動 APP1 之後，使用 CORP\\User1 帳戶名稱和密碼與其連接，然後開啟系統管理員層級 Windows PowerShell 命令提示字元。

接下來，在 Windows PowerShell 命令提示字元，使用這個命令，將 APP1 變成網頁伺服器。

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

接下來，使用下列命令，在 APP1 的資料夾裡面建立共用的資料夾和文字檔案。

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

這是您目前的組態。

![](./media/virtual-machines-windows-test-config-env/BC_TLG03.png)

## 階段 4：設定 CLIENT1

CLIENT1 充當 Contoso 內部網路上的一般膝上型電腦、平板電腦或桌上型電腦。

> [AZURE.NOTE] 下列命令集能建立執行 Windows Server 2012 R2 Datacenter 的 CLIENT1。這適用於所有類型的 Azure 訂用帳戶。如果您有以 MSDN 為基礎的 Azure 訂用帳戶，您可以使用 [Azure 入口網站](virtual-machines-windows-hero-tutorial.md)建立執行 Windows 10、Windows 8 或 Windows 7 的 CLIENT1。

首先，填寫資源群組的名稱、Azure 位置以及儲存體帳戶名稱，然後在本機電腦的 Azure PowerShell 命令提示字元執行下列命令，為 CLIENT1 建立 Azure 虛擬機器。

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzureRMPublicIpAddress -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRMNetworkInterface -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureRMVMConfig -VMName CLIENT1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for CLIENT1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName CLIENT1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/CLIENT1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name CLIENT1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

接下來，使用 CLIENT1 本機系統管理員帳戶名稱和密碼連接到 CLIENT1 虛擬機器，然後開啟系統管理員層級 Windows PowerShell 命令提示字元。

要檢查 CLIENT1 和 DC1 之間的名稱解析和網路通訊，請在 Windows PowerShell 命令提示字元執行 **ping dc1.corp.contoso.com** 命令，然後確定顯示 4 個回覆。

接下來，使用 Windows PowerShell 提示字元中的下列命令將 CLIENT1 虛擬機器加入至 CORP 網域。

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

請注意，您必須在輸入 Add-Computer 命令後提供您的 CORP\\User1 網域帳戶認證。

重新啟動 CLIENT1 之後，使用 CORP\\User1 帳戶名稱和密碼與其連接，然後開啟系統管理員層級 Windows PowerShell 命令提示字元。

接下來，確定您可以從 CLIENT1 存取在 APP1 上的網頁及檔案共用資源。

1.	在 [伺服器管理員] 的樹狀目錄窗格中，按一下 [本機伺服器]。
2.	在 [CLIENT1 的屬性] 中，按一下 [IE 增強式安全性設定] 旁的 [開啟]。
3.	在 [Internet Explorer 增強式安全性設定] 中，按一下 [系統管理員] 和 [使用者] 的 [關閉]，然後按一下 [確定]。
4.	從 [開始] 畫面中，按一下 [Internet Explorer]，然後按一下 [確定]。
5.	在網址列中，鍵入 ****http://app1.corp.contoso.com/**，然後按 ENTER。您應該會看到 APP1 的預設網際網路資訊服務網頁。
6.	按一下桌面工作列中的 [檔案總管] 圖示。
7.	在網址列中，輸入 **\\\app1\\Files**，然後按下 ENTER。
8.	您應該會看到一個資料夾視窗，裡面有檔案共用資料夾的內容。
9.	在 [檔案] 共用資料夾視窗中，按兩下 **Example.txt** 檔案。您應該會看到 Example.txt 檔案的內容。
10.	關閉 [example.txt - 記事本] 和 [檔案] 共用資料夾視窗。

這是最終的設定。

![](./media/virtual-machines-windows-test-config-env/BC_TLG04.png)

您在 Azure 中的基本設定現在可用於應用程式開發與測試或其他測試環境。

## 後續步驟

- [新增新的虛擬機器](virtual-machines-windows-create-powershell.md)到公司網路子網路，例如執行 Microsoft SQL Server 的虛擬機器。


## <a id="costs"></a>將 Azure 的測試環境虛擬機器費用降至最低

為了將測試環境虛擬機器的執行成本降到最低，您可以執行下列其中一項：

- 建立測試環境，並盡快執行所需的測試和示範。完成時，刪除測試環境虛擬機器。
- 關閉測試環境虛擬機器，進入已取消配置狀態。

要利用 Azure PowerShell 關閉虛擬機器，請填寫資源群組名稱並執行以下命令。

	$rgName="<your resource group name>"
	Stop-AzureRMVM -ResourceGroupName $rgName -Name "CLIENT1"
	Stop-AzureRMVM -ResourceGroupName $rgName -Name "APP1"
	Stop-AzureRMVM -ResourceGroupName $rgName -Name "DC1" -Force -StayProvisioned

如果想保證虛擬機器從已停止 (取消配置) 狀態啟動後，它們都可以正常運作，請按照下列順序啟動：

1.	DC1
2.	APP1
3.	CLIENT1

要利用 Azure PowerShell 啟動虛擬機器，請填寫資源群組名稱並執行以下命令。

	$rgName="<your resource group name>"
	Start-AzureRMVM -ResourceGroupName $rgName -Name "DC1"
	Start-AzureRMVM -ResourceGroupName $rgName -Name "APP1"
	Start-AzureRMVM -ResourceGroupName $rgName -Name "CLIENT1"

<!---HONumber=AcomDC_0323_2016-->