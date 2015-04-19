<properties 
	pageTitle="如何設定虛擬機器的可用性設定組" 
	description="使用 Azure 管理入口網站和 Azure PowerShell 命令，為設定 Azure 中新的或現有 VM 的可用性提供步驟" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="kathydav"/>

#如何設定虛擬機器的可用性設定組#


可用性設定組可協助您的虛擬機器在停機期間 (例如維護期間) 仍然保持可用狀態。在可用性設定組中置入二或多個類似設定的虛擬機器，將可針對虛擬機器所執行的應用程式或服務，建立維護其可用性所需的備援。如需其運作方式的詳細資訊，請參閱[管理虛擬機器的可用性][]。 

同時使用可用性設定組和負載平衡端點，是協助確保您的應用程式一直可用並有效率執行的最佳做法。如需負載平衡端點的詳細資訊，請參閱 [Azure 基礎結構服務的負載平衡][]。

您可以使用下列其中一個選項，將虛擬機器放入可用性設定組：

- [選項 1：同時建立虛擬機器和可用性設定組] []。然後，將新的虛擬機器加入您在建立這些虛擬機器時的設定組。
- [選項 2：將現有虛擬機器新增至可用性設定組] []。


>[AZURE.NOTE] 您要放入相同可用性設定組的虛擬機器必須隸屬於相同的雲端服務。   

## <a id="createset"> </a>選項 1：同時建立虛擬機器和可用性設定組##

您可以使用管理入口網站或 Azure PowerShell 命令來執行此作業。 

若要使用管理入口網站：

1. 如果您還沒有這麼做，請登入 [Azure 管理入口網站](http://manage.windowsazure.com)。

2. 在命令列上，按一下 [新增]****。

3. 按一下 [**虛擬機器**]，然後按一下 [**從組件庫**]。

4. 使用前兩個畫面來挑選映像、使用者名稱和密碼等。如需詳細資訊，請參閱[建立執行 Windows Server 的虛擬機器][]。
 
5. 第三個畫面可讓您設定網路、儲存體和可用性的資源。執行下列動作：
	 
	1. 挑選適用於雲端服務的適當選擇。保留 [**建立新的雲端服務**] 的設定 (除非您打算將此新虛擬機器新增至現有的 VM 雲端服務)。然後，在 [**雲端服務 DNS 名稱**] 下輸入名稱。此 DNS 名稱便會成為 URI (用來連絡虛擬機器) 的一部分。雲端服務會以通訊和獨立群組的方式運作。相同雲端服務中的所有虛擬機器可以彼此相互通訊、可以設定使用負載平衡，以及放置在相同的可用性設定組中。 

	2. 如果您打算使用虛擬網路，請在 [**區域/同質群組/虛擬網路**] 下指定一個虛擬網路。**重要事項**：如果您想要讓虛擬機器使用虛擬網路，就必須在建立虛擬機器時將 VM 加入虛擬網路。在建立 VM 後，您無法將虛擬機器加入虛擬網路。如需詳細資訊，請參閱 [Azure 虛擬網路概觀][]。 
	
	3. 建立可用性設定組。在 [**可用性設定組**] 下，保留 [**建立可用性設定組**] 的設定。接著，輸入此設定組的名稱。 

	4. 建立預設端點，並視需要新增更多端點。您也可以稍後再新增端點。 

	![Create an availabililty set for a new VM](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png) 

6. 在第四個畫面上，選擇您要安裝的擴充程式。擴充程式提供可輕鬆管理虛擬機器的功能，例如執行反惡意程式碼或重設密碼。如需詳細資訊，請參閱 [Azure VM 代理程式與 VM 延伸模組](http://go.microsoft.com/fwlink/p/?LinkId=XXX)。

7.	按一下箭號以建立虛擬機器和可用性設定組。

	在新虛擬機器的儀表板中，您可以按一下 [**設定**]，並查看此虛擬機器是否隸屬於新的可用性設定組。

若要使用 Azure PowerShell 命令建立 Azure VM，並將它加入新的或現有的可用性設定組，請參閱下列內容：

- [使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](virtual-machines-ps-create-preconfigure-windows-vms.md)
- [使用 Azure PowerShell 建立和預先設定以 Linux 為基礎的虛擬機器](virtual-machines-ps-create-preconfigure-linux-vms.md)

<<<<<<< HEAD
	>[AZURE.NOTE] 在沒有參數的情況下執行  `Get-AzureVMImage`，以取得適用於您的訂閱的所有映像清單。這可能會傳回龐大的清單。若要縮短清單，請使用映像系列名稱之類的屬性。如需可為您說明如何執行此動作以找出特定映像的提示和範例，請參閱[使用 Windows PowerShell 管理映像](http://msdn.microsoft.com/library/azure/dn790330.aspx)

3.	為新的虛擬機器指定組態，然後使用管線將組態物件傳遞至可建立 VM 的 Cmdlet。請確定在預留位置中替換成您自己的值，例如  &lt;VmName&gt; 和 &lt;VmSize&gt;。

	`C:\PS> New-AzureVMConfig -Name "<VmName>" -InstanceSize <VmSize> -AvailabilitySetName "<SetName>" -ImageName $image | Add-AzureProvisioningConfig -Windows -AdminUsername "<UserName>" -Password "<MyPassword>" | Add-AzureDataDisk -CreateNew -DiskSizeInGB 50 -DiskLabel 'datadisk1' -LUN 0 | New-AzureVM -ServiceName "<MySvc1>" `

=======
>>>>>>> 1dc830a16302e6b943a5d55ebacef659451b22d6

## <a id="addmachine"> </a>選項 2：將現有虛擬機器加入至可用性設定組##

在管理入口網站中，您可以將現有的虛擬機器加入現有的可用性設定組，或為現有的虛擬機器建立新的可用性設定組。(請注意，相同可用性設定組中的虛擬機器必須隸屬於相同的雲端服務。)步驟幾乎完全相同。有了 Azure PowerShell，您可以將虛擬機器加入現有的可用性設定組。 

1. 如果您尚未這樣做，請登入 [Azure 管理入口網站](http://manage.windowsazure.com)。

2. 在瀏覽列中，按一下 [**虛擬機器**]。

3. 從虛擬機器的清單中，按一下您想要加入設定組的虛擬機器名稱。

4. 從虛擬機器名稱下方的索引標籤中，按一下 [**設定**]。 

5. 在 [設定] 區段中，尋找 [**可用性設定組**]。執行下列其中一項：

	A. 挑選 [**建立可用性設定組**]，然後輸入設定組的名稱。

	B. 挑選 [**選取可用性設定組**]，然後從清單中挑選一個設定組。

	![建立現有 VM 的可用性設定組](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png) 

6. 按一下 [儲存]****。

若要使用 Azure PowerShell 命令，請開啟系統管理員層級的 Azure PowerShell 工作階段並執行下列命令。在預留位置 (例如 &lt;VmCloudServiceName&gt;) 中，以正確的名稱取代括號中的所有內容，包括 < 和 > 字元。

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE] 系統可能會重新啟動虛擬機器，以結束將它加入可用性設定組的作業。


##其他資源
[關於 Azure VM 組態設定]

[選項 1：同時建立虛擬機器和可用性設定組]: #createset
[選項 2：將現有虛擬機器加入至可用性設定組]: #addmachine

<!-- LINKS -->
[Azure 基礎結構服務的負載平衡]: ../virtual-machines-load-balance
[管理虛擬機器的可用性]: ../virtual-machines-manage-availability
[建立執行 Windows 的虛擬機器]: ../virtual-machines-windows-tutorial
[Azure 虛擬網路概觀]: http://go.microsoft.com/fwlink/p/?linkid=294063
[關於虛擬網路的同質群組]: http://msdn.microsoft.com/library/windowsazure/jj156085.aspx
<<<<<<< HEAD
[如何連接雲端服務中的虛擬機器]: ../virtual-machines-connect-cloud-service
[關於 Azure VM 組態設定]: http://msdn.microsoft.com/library/azure/dn763935.aspx
=======
[如何連接雲端服務中的虛擬機器]: ../cloud-services-connect-virtual-machine
[關於 Azure VM 組態設定]: http://msdn.microsoft.com/library/azure/dn763935.aspx
>>>>>>> 1dc830a16302e6b943a5d55ebacef659451b22d6


<!--HONumber=47-->
