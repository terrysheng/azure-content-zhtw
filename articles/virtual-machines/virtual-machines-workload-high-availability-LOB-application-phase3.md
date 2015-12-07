<properties 
	pageTitle="企業營運應用程式第 3 階段 | Microsoft Azure" 
	description="在 Azure 企業營運應用程式第 3 階段中，建立電腦和 SQL Server 叢集，並啟用可用性群組。" 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="josephd"/>

# 企業營運應用程式工作負載第 3 階段：設定 SQL Server 基礎結構

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統部署模型。


在 Azure 基礎結構服務部署高可用性企業營運系統應用程式的這個階段中，您將設定兩部執行 SQL Server 的電腦和叢集多數節點電腦，然後再將他們併入 Windows Server 叢集。

您必須先完成這個階段才能前往[第 4 階段](virtual-machines-workload-high-availability-LOB-application-phase4.md)。如需所有階段的相關資訊，請參閱[在 Azure 中部署高可用性的企業營運系統應用程式](virtual-machines-workload-high-availability-LOB-application-overview.md)。

> [AZURE.NOTE]這份文件包含 Azure PowerShell Preview 1.0 的命令。若要在 Azure PowerShell 0.9.8 和先前版本中執行這些命令，請將 "-AzureRM" 的所有執行個體取代為 "-Azure"，並且在您執行任何命令之前先新增 **Switch-AzureMode AzureResourceManager** 命令。如需詳細資訊，請參閱 [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/)。

## 在 Azure 中建立 SQL Server 叢集虛擬機器

執行 SQL Server 的虛擬機器共有兩部。一部虛擬機器包含可用性群組的主要資料庫複本，而第二個虛擬機器包含次要 (備份) 複本。備份的用途為支援高可用性。另一部額外的虛擬機器是為了叢集多數節點而設。

使用下列方塊中的 PowerShell 命令為上述三個伺服器建立虛擬機器。指定變數的值，並移除 < and > 字元。請注意，此 PowerShell 命令集使用下表中的值：

- 資料表 M，適用於虛擬機器
- 資料表 V，適用於虛擬網路設定
- 資料表 S，適用於子網路
- 資料表 ST，適用於儲存體帳戶
- 資料表 A，適用於可用性設定組

回想您在[第 2 階段](virtual-machines-workload-high-availability-LOB-application-phase2.md)中所定義的資料表 M，以及在[第 1 階段](virtual-machines-workload-high-availability-LOB-application-phase1.md)中所定義的資料表 V、S、ST 和 A。

當您提供所有適當值後，在 Azure PowerShell 提示中執行結果區塊。

	# Set up key variables
	$rgName="<your resource group name>"
	$locName="<Azure location of your resource group>"
	# Change to the premium storage account
	$saName="<Table ST – Item 1 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 2 – Availability set name column>"
	
	# Create the first SQL server
	$vmName="<Table M – Item 3 - Virtual machine name column>"
	$vmSize="<Table M – Item 3 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for SQL data in GB>
	$diskLabel="<the label on the disk>"
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-SQLDataDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second SQL Server virtual machine
	$vmName="<Table M – Item 4 - Virtual machine name column>"
	$vmSize="<Table M – Item 4 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for SQL data in GB>
	$diskLabel="<the label on the disk>"
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Change to the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"
	
	# Create the cluster majority node server
	$vmName="<Table M – Item 5 - Virtual machine name column>"
	$vmSize="<Table M – Item 5 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the cluster majority node server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE]因為這些虛擬機器用於內部網路應用程式，所以未獲指派公用 IP 位址或 DNS 網域名稱標籤，也不會公開到網際網路。不過，這也表示您無法從 Azure Preview 入口網站連線到它們。當您檢視虛擬機器的屬性時，[連接] 按鈕將無法使用。使用「遠端桌面連線」配件或其他遠端桌面工具，來使用虛擬機器的私人 IP 位址或內部網路 DNS 名稱連接該虛擬機器。

## 設定執行 SQL Server 的電腦

對於每部執行 SQL Server 的虛擬機器，使用您選擇的遠端桌面用戶端，並建立遠端桌面連線。使用其內部網路 DNS 或電腦名稱，以及本機系統管理員帳戶的認證。

對於每部執行 SQL Server 的虛擬機器，請在 Windows PowerShell 提示中使用下列命令，將其加入適當的 AD DS 網域。

	$domName="<AD DS domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

請注意，您必須在輸入 Add-Computer 命令後提供網域帳戶認證。

重新啟動之後，使用具有本機系統管理員權限的帳戶重新連接到網域。


使用下列程序兩次，分別為兩部執行 SQL server 的虛擬機器進行設定，以便加入額外資料磁碟並在新磁碟區上建立資料夾。

### 若要初始化空磁碟和新增資料夾

1. 在 [伺服器管理員] 的左窗格中，按一下 [檔案和存放服務]，然後按一下 [磁碟]。
2. 在 [內容] 窗格的 [**磁碟**] 群組中，按一下 [磁碟 **2**] ([**磁碟分割**] 設為 [**未知**])。
3. 按一下 [工作]，然後按一下 [新增磁碟區]。
4. 在 [新增磁碟區精靈] 的 [在您開始前] 頁面上，按 [下一步]。
5. 在 [選取伺服器和磁碟] 頁面上，按一下 [磁碟 2]，然後按 [下一步]。出現提示時，按一下 **[確定]**。
6. 在 [指定磁碟區大小] 頁面上，按 [下一步]。
7. 在 [指派成磁碟機代號或資料夾] 頁面上，按 [下一步]。
8. 在 [選取檔案系統設定] 頁面上，按 [下一步]。
9. 在 [確認選取項目] 頁面上，按一下 [建立]。
10. 完成時，按一下 [關閉]。
11. 從 Windows PowerShell 提示執行下列命令：
	- md f:\\Data
	- md f:\\Log
	- md f:\\Backup

使用下列程序兩次，分別為兩部執行 SQL server 的虛擬機器進行設定，以便讓他們使用 F: 磁碟機作為帳戶和權限的新資料庫。

1. 在 [開始] 畫面中輸入 **SQL Studio**，然後按一下 [**SQL Server 2014 Management Studio**]。
2. 在 [**連線到伺服器**] 中按一下 [**連線**]。
3. 在左側窗格中，以滑鼠右鍵按一下最上層節點 (以機器名稱命名的預設執行個體)，然後按一下 [**內容**]。
4.	在 [**伺服器內容**] 中按一下 [**資料庫設定**]。
5.	在 [**資料庫預設位置**] 中設定下列值： 
	- 將 [**資料**] 路徑設定為 **f:\\Data**。
	- 將 [**記錄檔**] 路徑設定為 **f:\\Log**。
	- 將 [**備份**] 路徑設定為 **f:\\Backup**。
	- 只有新的資料庫才會使用這些位置。
6.	按一下 [**確定**] 關閉視窗。
7.	在左側窗格中展開 [**安全性資料夾**]。
8.	以滑鼠右鍵按一下 [**登入**]，然後按一下 [**新增登入**]。
9.	在 [登入名稱] 中輸入 *domain*\\sqladmin in，其中 *domain* 是指在[第 2 階段](virtual-machines-workload-high-availability-LOB-application-phase2.md)中建立 sqladmin 帳戶所在網域的名稱。 
10.	在 [**選取頁面**] 下按一下 [**伺服器角色**]，再按一下 [**系統管理員**] ，然後按一下 [**確定**]。
11.	關閉 SQL Server 2014 Management Studio。

使用下列程序兩次，分別為每部 SQL 伺服器進行設定，以便允許使用 sqladmin 帳戶進行遠端桌面連接。

1.	在 [開始] 畫面中，以滑鼠右鍵按一下 [**這台電腦**]，然後按一下 [**內容**] 。
2.	在 [**系統**] 視窗中按一下 [**遠端設定**]。
3.	在 [**遠端桌面**] 部分中按一下 [**選取使用者**]，然後按一下 [**新增**]。
4.	在 [輸入要選取的物件名稱] 中輸入 [domain]**\\sqladmin**，然後按三次 [確定]。

SQL Server 服務需要一個可讓用戶端存取資料庫伺服器的連接埠。同時也需要連接埠來連線 SQL Server Management Studio，並管理高可用性群組。接下來，在系統管理員層級 Windows PowerShell 提示中執行下列命令兩次，分別為每部 SQL Server 虛擬機器進行設定，以便新增防火牆規則並允許此類型的輸入流量。

	New-NetFirewallRule -DisplayName "SQL Server ports 1433, 1434, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

請在每一部 SQL Server 虛擬機器中，以本機系統管理員的身分登出。

如需關於在 Azure 中將 SQL Server 效能最佳化的資訊，請參閱 [Azure 虛擬機器中的 SQL Server 效能最佳做法](virtual-machines-sql-server-performance-best-practices.md)。同時您也可以停用企業營運應用程式儲存體帳戶的異地備援儲存體 (GRS)，並使用儲存空間最佳化 IOP。

## 設定叢集多數節點伺服器

使用您選擇的遠端桌面用戶端，並建立叢集多數節點伺服器虛擬機器的遠端桌面連接。使用其內部網路 DNS 或電腦名稱，以及本機系統管理員帳戶的認證。

請在 Windows PowerShell 提示中使用下列命令，將叢集多數節點伺服器加入適當的 AD DS 網域。

	$domName="<AD DS domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

請注意，您必須在執行 **Add-Computer** 命令時提供網域帳戶認證。

重新啟動之後，使用具有本機系統管理員權限的帳戶重新連接。

## 建立 Windows Server 容錯移轉叢集的叢集

SQL Server AlwaysOn 可用性群組依賴 Windows Server 的容錯移轉叢集 (WSFC) 功能。 該功能可讓多部機器在叢集中以群組的方式加入。當其中一部機器故障，第二部機器則可取而代之。因此，第一個工作便是為加入叢集的所有機器啟用容錯移轉叢集功能，其中包括：

- 執行 SQL Server 的主要虛擬機器
- 執行 SQL Server 的次要虛擬機器
- 叢集多數節點

容錯移轉叢集需要至少三部虛擬機器。兩部機器用來主控 SQL Server，其中次要虛擬機器為同步的次要複本，以確保主要機器故障時，不會遺失任何資料。第三部虛擬機器不需主控 SQL Server。叢集多數節點功能可在 WSFC 中提供仲裁。由於 WSFC 叢集依賴仲裁來監控狀態，因此一定要有多數來確保 WSFC 叢集為上線狀態。若叢集裡只有兩部機器，當其中一部故障時，就沒有多數的情況。如需詳細資訊，請參閱 [WSFC 仲裁模式與投票設定 (SQL Server)](http://msdn.microsoft.com/library/hh270280.aspx)。

請在兩部 SQL Server 虛擬機器與叢集多數節點中，於系統管理員層級的 Windows PowerShell 提示中執行下列命令：

	Install-WindowsFeature Failover-Clustering -IncludeManagementTools

由於目前 Azure 中 DHCP 的非 RFC 相容行為，無法成功建立 Windows Server 容錯移轉叢集 (WSFC) 叢集。如需詳細資訊，請在 Azure 虛擬機器中 SQL Server 的「高可用性和災害復原」內搜尋「Azure 網路中的 WSFC 叢集行為」。 不過還是有相對的因應措施。使用下列步驟建立叢集。

1.	使用在[第 2 階段](virtual-machines-workload-high-availability-LOB-application-phase2.md)中建立的 sqladmin 帳戶登入主要 SQL Server 虛擬機器。
2.	在 [開始] 畫面中輸入 **Failover**，然後按一下 [**容錯移轉叢集管理員**]。
3.	在左側窗格中，以滑鼠右鍵按一下 [**容錯移轉叢集管理員**]，然後按一下 [**建立叢集**]。
4.	在 [開始之前] 頁面中按一下 [下一步]。
5.	在 [選取伺服器] 頁面中輸入主要 SQL Server 機器的名稱，按一下 [新增]，然後按一下 [下一步]。
6.	在 [驗證警告] 頁面中，按一下 [否。我不需要從 Microsoft 取得此叢集的支援，也不想要執行驗證測試。**按 [下一步] 後，繼續建立叢集。**]，然後按一下 [**下一步**]。
7.	在 [管理叢集的存取點] 頁面中，於 [叢集名稱] 文字方塊中輸入叢集名稱，然後按一下 [下一步]。
8.	在 [確認] 頁面中按一下 [下一步]，開始建立叢集。 
9.	在 [摘要] 頁面中按一下 [完成]。
10.	在左側窗格中按一下您的新叢集。在 [內容] 窗格的 [**叢集核心資源**] 部分中，開啟您的伺服器叢集名稱。[**IP位址**] 資源的狀態為 [**失敗**]。 由於指派給叢集的 IP 位址與虛擬機器相同，因此該 IP 位址資源無法上線，造成位址重覆的狀況。 
11.	以滑鼠右鍵按一下失敗的 [**IP 位址**]資源，然後按一下 [**內容**]。
12.	在 [**IP 位址內容**] 對話方塊中，按一下 [**靜態 IP 位址**]。
13.	輸入一個未使用的 IP，此 IP 位址範圍需與 SQL Server 所在的子網路相對應，然後按一下 [**確定**]。
14.	以滑鼠右鍵按一下失敗的 [IP 位址] 資源，然後按一下 [**上線**]。等待兩個資源上線。待叢集名稱資源上線後，將使用新的 Active Directory (AD) 電腦帳戶更新網域控制站。此 AD 帳戶稍後將用來執行可用性群組叢集服務。
15.	現在 AD 帳戶已建立，請讓叢集名稱離線。以滑鼠右鍵按一下 [**叢集核心資源**] 中的叢集名稱，然後按一下 [**離線**]。
16.	若要移除叢集 IP 位址，請以滑鼠右鍵按一下 [**IP 位址**]，按一下 [**移除**] ，並在出現提示時按一下 [**是**]。由於叢集資源依賴 IP 位址資源，因此無法再次上線。不過，可用性群組不需依賴叢集名稱或 IP 位址即可正常運作。因此叢集名稱可保持離線狀態。
17.	若要將其餘節點新增至叢集，請以滑鼠右鍵按一下左側窗格中的叢集名稱，然後按一下 [新增節點]。
18.	在 [開始之前] 頁面中按一下 [下一步]。 
19.	在 [選取伺服器] 頁面中輸入名稱，然後按一下 [新增]，同時將次要 SQL Server 和叢集多數節點新增至叢集。新增兩台電腦後按一下 [**下一步**]。若無法新增機器，並出現錯誤訊息「遠端登錄並未執行」，請執行下列步驟。登入機器、開啟 [服務] 嵌入式管理單元 (services.msc) 並啟用遠端登錄。如需更多資訊，請參閱[無法連線至遠端登陸服務](http://technet.microsoft.com/library/bb266998.aspx)。 
20.	在 [驗證警告] 頁面中，按一下 [否。我不需要從 Microsoft 取得此叢集的支援，也不想要執行驗證測試。**按 [下一步] 後，繼續建立叢集。**]，然後按一下 [**下一步**]。 
21.	在 [確認] 頁面中按一下 [ 下一步]。
22.	在 [摘要] 頁面中按一下 [完成]。
23.	在左側窗格中按一下 [ **節點**]。畫面中應該會列出三台電腦。

## 啟用 AlwaysOn 可用性群組

下一個步驟則是使用 SQL Server 組態管理員啟用 AlwaysOn 可用性群組。請注意，SQL Server 中的可用性群組與 Azure 可用性設定組有所不同。可用性群組包含高可用性和可復原的資料庫。 Azure 可用性設定組會將虛擬機器配置到不同的容錯網域。如需容錯網域的詳細資訊，請參閱[管理虛擬機器的可用性](virtual-machines-manage-availability.md)。

使用這些步驟啟用 SQL Server 上的 AlwaysOn 可用性群組。

1.	使用在[第 2 階段](virtual-machines-workload-high-availability-LOB-application-phase2.md)中建立的 sqladmin 帳戶登入主要 SQL Server 虛擬機器。
2.	在 [ 開始] 畫面中輸入 **SQL Server Configuration**，然後按一下 [**SQL Server 組態管理員**]。
3.	在左側窗格中按一下 [**SQL Server 服務**]。
4.	在 [内容] 窗格中按兩下 [**SQL Server (MSSQLSERVER)**]。
5.	在 [**SQL Server (MSSQLSERVER) 屬性**] 中按一下 [**AlwaysOn 高可用性**] 索引標籤、選取 [**啟用 AlwaysOn可用性群組**]，然後按一下 [**套用**]，並在出現提示時按一下 [**確定**]。先不要關閉屬性視窗。 
6.	按一下 [虛擬-機器-管理-可用性] 索引標籤，然後在 [帳戶名稱] 中輸入「[網域]**\\sqlservice**」。在 [密碼] 和 [確認密碼] 中輸入 sqlservice 帳戶密碼，然後按一下 [確定]。
7.	在訊息視窗中按一下 [**是**]，重新開啟 SQL Server 服務。
8.	使用 sqladmin 帳戶登入次要 SQL Server 虛擬機器，並重覆步驟 2 至 7。 

此圖顯示已成功完成此階段設定，並以電腦名稱預留位置表示。

![](./media/virtual-machines-workload-high-availability-LOB-application-phase3/workload-lobapp-phase3.png)

## 後續步驟

若要繼續設定此工作負載，請前往[第 4 階段：設定 Web 伺服器](virtual-machines-workload-high-availability-LOB-application-phase4.md)。

## 其他資源

[在 Azure 中部署高可用性的企業營運應用程式](virtual-machines-workload-high-availability-LOB-application-overview.md)

[企業營運應用程式架構藍圖](http://msdn.microsoft.com/dn630664)

[在混合式雲端中設定 Web 型 LOB 應用程式進行測試](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Azure 基礎結構服務實作指導方針](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure 基礎結構服務工作負載：SharePoint Server 2013 陣列](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=AcomDC_1125_2015-->