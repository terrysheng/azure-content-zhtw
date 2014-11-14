<properties title="如何設定虛擬機器的可用性設定組" pageTitle="如何設定虛擬機器的可用性設定組" description="提供可在 Azure 中設定 VM 可用性設定組的步驟" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="kathydav" />

# 如何設定虛擬機器的可用性設定組

可用性設定組可協助您的虛擬機器在停機期間 (例如維護期間) 仍然保持可用狀態。在可用性設定組中置入二或多個類似設定的虛擬機器，將可針對虛擬機器所執行的應用程式或服務，建立維護其可用性所需的備援。如需詳細資訊，請參閱[管理虛擬機器的可用性][管理虛擬機器的可用性]。

同時使用可用性設定組和負載平衡端點，是協助確保您的應用程式一直可用並有效率執行的最佳做法。如需負載平衡端點的詳細資訊，請參閱 [Azure 基礎結構服務的負載平衡][Azure 基礎結構服務的負載平衡]。

您可以使用下列其中一個選項，將虛擬機器放入可用性設定組：

-   [選項 1：同時建立虛擬機器和可用性設定組][選項 1：同時建立虛擬機器和可用性設定組]。然後，將新的虛擬機器加入您在建立這些虛擬機器時的設定組。
-   [選項 2：將現有虛擬機器加入至可用性設定組][選項 2：將現有虛擬機器加入至可用性設定組]。

> [WACOM.NOTE] 您要放入相同可用性設定組的虛擬機器必須隸屬於相同的雲端服務。

## <span id="createset"></span> </a>選項 1：同時建立虛擬機器和可用性設定組

您可以使用管理入口網站或 Azure PowerShell Cmdlet 來執行此作業。

若要使用管理入口網站：

1.  如果您尚未登入 Azure [管理入口網站][管理入口網站]，請先登入。

2.  按一下命令列上的 [新增]。

3.  按一下 [虛擬機器]，然後按一下 [從組件庫]。

4.  使用前兩個畫面來挑選映像、使用者名稱和密碼等。如需詳細資訊，請參閱[建立執行 Windows Server 的虛擬機器][建立執行 Windows Server 的虛擬機器] (英文)。

5.  第三個畫面可讓您設定網路、儲存體和可用性的資源。執行下列動作：

    1.  挑選適用於雲端服務的適當選擇。保留 [Create a new cloud service] 的設定 (除非您打算將此新虛擬機器加入現有的 VM 雲端服務)。然後，在 [Cloud Service DNS Name] 下輸入名稱。此 DNS 名稱便會成為 URI (用來連絡虛擬機器) 的一部分。雲端服務會以通訊和獨立群組的方式運作。相同雲端服務中的所有虛擬機器可以彼此相互通訊、可以設定使用負載平衡，以及放置在相同的可用性設定組中。

    2.  如果您打算使用虛擬網路，請在 [Region/Affinity Group/Virtual Network] 下指定一個虛擬網路。**重要事項**：如果您想要讓虛擬機器使用虛擬網路，就必須在建立虛擬機器時將 VM 加入虛擬網路。在建立 VM 後，您無法將虛擬機器加入虛擬網路。如需詳細資訊，請參閱 [Azure 虛擬網路概觀][Azure 虛擬網路概觀]。

    3.  建立可用性設定組。在 [可用性設定組] 下，保留 [Create an availability set] 的設定。接著，輸入此設定組的名稱。
    4.  建立預設端點，並視需要新增更多端點。您也可以稍後再新增端點。

    ![Create an availabililty set for a new VM][Create an availabililty set for a new VM]

6.  在第四個畫面上，選擇您要安裝的擴充程式。擴充程式提供可輕鬆管理虛擬機器的功能，例如執行反惡意程式碼或重設密碼。如需詳細資訊，請參閱 [Azure VM 代理程式與 VM 擴充程式][Azure VM 代理程式與 VM 擴充程式]。

7.  按一下箭號以建立虛擬機器和可用性設定組。

    在新虛擬機器的儀表板中，您可以按一下 [設定] 並查看此虛擬機器是否隸屬於新的可用性設定組。

若要使用 Azure Cmdlet：

1.  開啟 Azure PowerShell 工作階段，並執行類似下列範例的命令。請注意，這些範例假設您要建立虛擬機器、雲端服務和可用性設定組。

2.  取得您要用來建立虛擬機器的映像名稱，並將它儲存在變數中。命令會使用映像物件的索引編號和 ImageName 屬性：

    `C:\PS> $image = (Get-AzureVMImage)[4].ImageName`

    > [WACOM.NOTE] 若要取得適用於您訂閱的所有映像清單，請在沒有參數的情況下執行 `Get-AzureVMImage`。

3.  為新的虛擬機器指定組態，然後使用管線將組態物件傳遞至可建立 VM 的 Cmdlet。請確定在預留位置中替換成您自己的值，例如 \<VmName\> 和 \<VmSize\>。

    `C:\PS> New-AzureVMConfig -Name "<VmName>" -InstanceSize <VmSize> -AvailabilitySetName "<SetName>" -ImageName $image | Add-AzureProvisioningConfig -Windows -AdminUsername "<UserName>" -Password "<MyPassword>" | Add-AzureDataDisk -CreateNew -DiskSizeInGB 50 -DiskLabel 'datadisk1' -LUN 0 | New-AzureVM –ServiceName "<MySvc1>"`

## <span id="addmachine"></span> </a>選項 2：將現有虛擬機器加入至可用性設定組

在管理入口網站中，您可以將現有的虛擬機器加入現有的可用性設定組，或為現有的虛擬機器建立新的可用性設定組。(請注意，虛擬機器必須隸屬於相同的雲端服務。)步驟幾乎完全相同。在 Azure PowerShell 中，您可以將虛擬機器加入現有的可用性設定組。

1.  如果您尚未登入，請登入 Azure [管理入口網站][管理入口網站]。

2.  在瀏覽列中，按一下 [虛擬機器]。

3.  從虛擬機器的清單中，挑選您想要加入設定組的其中一個虛擬機器。請按一下虛擬機器列來開啟它的儀表板。

4.  從虛擬機器名稱下方的索引標籤中，按一下 [設定]。

5.  在 [設定] 區段中，尋找 [可用性設定組]。執行下列其中一項：

    A. 挑選 [Create an availability set]，然後輸入設定組的名稱。

    B. 挑選 [Select an availability set]，然後從清單中挑選一個設定組。

    ![Create an availabililty set for an existing VM][Create an availabililty set for an existing VM]

6.  按一下 [儲存]。

若要使用 Azure Cmdlet：

開啟 Azure PowerShell 工作階段，並執行下列命令。請確定在預留位置中替換成您自己的值，例如 \<VmCloudServiceName\> 和 \<VmName\>。

    C:\PS> Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<MyAvSet>" | Update-AzureVM

> [WACOM.NOTE] 系統可能會重新啟動虛擬機器，以結束將它加入可用性設定組的作業。

## 其他資源

[關於 Azure VM 組態設定][關於 Azure VM 組態設定]

<!-- LINKS -->

  [管理虛擬機器的可用性]: ../virtual-machines-manage-availability
  [Azure 基礎結構服務的負載平衡]: ../virtual-machines-load-balance
  [選項 1：同時建立虛擬機器和可用性設定組]: #createset
  [選項 2：將現有虛擬機器加入至可用性設定組]: #addmachine
  [管理入口網站]: http://manage.windowsazure.com
  [建立執行 Windows Server 的虛擬機器]: ../virtual-machines-windows-tutorial
  [Azure 虛擬網路概觀]: http://go.microsoft.com/fwlink/p/?linkid=294063
  [Create an availabililty set for a new VM]: ./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png
  [Azure VM 代理程式與 VM 擴充程式]: http://go.microsoft.com/fwlink/p/?LinkId=XXX
  [Create an availabililty set for an existing VM]: ./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png
  [關於 Azure VM 組態設定]: http://msdn.microsoft.com/zh-tw/library/azure/dn763935.aspx
