

## 多重和單一執行個體 VM
對於在 Azure 上執行的許多客戶而言，在 VM 進行計劃性維護時可以排程是非常重要的，因為這樣會導致停機 ~15 分鐘。您可以在佈建的 VM 接收計劃性維護時，利用可用性設定組協助控制。

有兩個可能的 VM 設定在 Azure 上執行。VM 可能會設定為多重執行個體或單一執行個體。如果 VM 在可用性設定組中，它們會隨後設定為多重執行個體。請注意，即使單一 VM 也可以部署在可用性設定組中，而且會被視為多重執行個體。如果 VM 不在可用性設定組中，它們會隨後設定為單一執行個體。如需可用性設定組的詳細資料，請參閱[管理 Windows 虛擬機器的可用性](../articles/virtual-machines/virtual-machines-windows-manage-availability.md)或[管理 Linux 虛擬機器的可用性](../articles/virtual-machines/virtual-machines-linux-manage-availability.md)。

單一執行個體和多重執行個體的 VM 計劃性維護更新會分別發生。藉由將 VM 重新設定維單一執行個體 (如果它們是多重執行個體)，或設定為多重執行個體 (如果它們是單一執行個體)，您可以控制其 VM 收到計劃性維護的時間。如需 Azure VM 計劃性維護的詳細資料，請參閱 [Azure Linux 虛擬機器的維護計劃](../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md)或 [Azure Windows 虛擬機器的維護計劃](../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md)。

## 對於多重執行個體組態
您可以藉由將 VM 從可用性設定組移除，以選取計劃性維護影響部署在可用性設定組設定中之 VM 的時間。
1.	在計劃性維護 7 天前，會傳送電子郵件給您在多重執行個體設定中的 VM。受影響多重執行個體的訂用帳戶識別碼和名稱會包含在電子郵件將電子郵件的本文中。
2.	在這 7 天內，您可以藉由從可用性設定組移除該區域中的多重執行個體 VM，以選擇執行個體的更新時間。此設定中的變更會導致重新開機，因為虛擬機器正在從一部以維護為目標的實體主機，移至另一部不是以維護為目標的實體主機。 
3.	您可以在傳統入口網站中從可用性設定組移除 VM。 
   
        a.	In the Classic portal, click on the VM and then select “configure.” 
        
        b.	Under “settings”, you can see which Availability Set the VM is in.
        
    ![可用性設定組選取](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselection.png)

        c.	In the availability set dropdown menu, select “remove from availability set.”
        
    ![從設定組移除](./media/virtual-machines-planned-maintenance-schedule/availabilitysetselectionconfiguration.png)

        d.	At the bottom, select “save.” Select “yes” to acknowledge that this action will restart the VM.
4.	這些 VM 將會移至單一執行個體主機，而且不會在可用性設定組設定的計劃性維護期間更新。
5.	可用性設定組 VM 更新完成 (根據原始電子郵件中所述的排程) 之後，您應該將 VM 新增回其可用性設定組，它們會重新設定為多重執行個體 VM。將 VM 從單一執行個體移回多重執行個體將會導致重新開機。一般而言，跨整個 Azure 環境的所有多重執行個體更新完成之後，就輪到單一執行個體維護。

請注意，使用 Azure PowerShell：Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Remove-AzureAvailabilitySet | Update-AzureVM 也可以辦到。

## 對於單一執行個體組態
您可以藉由將這些 VM 新增至可用性設定組，以選取計劃性維護影響單一執行個體設定中之 VM 的時間。逐步說明
1.	在計劃性維護 7 天前，會傳送電子郵件給單一執行個體設定中的 VM。受影響單一執行個體的訂用帳戶識別碼和名稱會包含在電子郵件將電子郵件的本文中。 
2.	在這 7 天內，您可以藉由移動單一執行個體 VM (方法是將它們移至相同區域中的可用性設定組)，以選擇執行個體重新啟動的時間。此設定中的變更會導致重新開機，因為虛擬機器正在從一部以維護為目標的實體主機，移至另一部不是以維護為目標的實體主機。
3.	遵循此處的指示以使用傳統入口網站和 Azure PowerShell，將現有的 VM 新增至可用性設定組 (請參閱下列附註中的 Azure PowerShell 範例)。
4.	一旦這些 VM 重新設定為多重執行個體，隨即會從單一執行個體 VM 的計劃性維護排除。
5.	單一執行個體 VM 更新完成 (根據原始電子郵件中所述的排程) 之後，您可以從可用性設定組移除 VM，它們會重新設定為單一執行個體 VM。

請注意，這也可使用 Azure PowerShell 辦到：

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

<!--Anchors-->



<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

<!---HONumber=AcomDC_0323_2016-->