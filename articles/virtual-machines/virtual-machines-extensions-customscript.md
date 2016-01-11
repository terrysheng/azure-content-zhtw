<properties
   pageTitle="Windows VM 上的自訂指令碼擴充功能 | Microsoft Azure"
   description="使用自訂指令碼擴充功能，在遠端 Windows VM 上執行 PowerShell 指令碼，將 Azure VM 組態工作自動化"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# Windows 虛擬機器的自訂指令碼擴充功能

本文概要說明如何使用 Azure PowerShell Cmdlet，在 Windows VM 上使用自訂指令碼擴充功能。

Microsoft 和受信任的協力廠商發行者建置的虛擬機器 (VM) 延伸模組，可延伸 VM 的功能。如需 VM 擴充功能的概觀，請參閱 [Azure VM 擴充功能與功能](virtual-machines-extensions-features.md)。

連結：[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-extensions-customscript%20-with%20template.md)。


## 自訂指令碼擴充功能概觀

Windows 的自訂指令碼擴充功能可讓您在遠端 VM 上執行 PowerShell 指令碼，而不需登入它。指令碼可以在佈建 VM 之後執行，或者在 VM 生命週期期間隨時執行，而不需在該 VM 上開啟任何其他連接埠。自訂指令碼延伸模組的最常用案例包含在 VM 後續佈建上安裝和設定其他軟體。

### 執行自訂指令碼延伸模組的先決條件

1. 從<a href="http://azure.microsoft.com/downloads" target="_blank">這裡</a>安裝 Azure PowerShell Cmdlet 0.8.0 版或更新版本。
2. 如果指令碼在現有 VM 上執行，請確定在該 VM 上啟用 VM 代理程式，否則請遵循此<a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">文章</a>安裝一個 VM代理程式。(如果您從 Azure 資源庫佈建 VM，則預設會啟用 VM 代理程式，您不需要將它們啟用)
3. 將您想要在 VM 上執行的指令碼上傳到 Azure 儲存體。指令碼可以來自單一容器或多個儲存體容器。
4. 指令碼應該以由延伸模組依序要啟動的項目指令碼啟動其他指令碼的方式來撰寫。

## 自訂指令碼擴充功能案例

### 將檔案上傳到預設容器

如果訂用帳戶之預設帳戶的儲存體容器中含有您的指令碼，則下列範例會示範如何在 VM 上執行它們。ContainerName 是您上傳指令碼的目標。您可以使用 **Get-AzureSubscription –Default** 命令來驗證預設的儲存體帳戶。

下列範例會建立新的 VM，但也可以在現有的 VM 上執行相同的案例。

    # create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script Extension to the VM. The container name refer to the storage container which contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### 將檔案上傳到非預設的儲存體容器

這個案例示範如何使用相同訂用帳戶內或不同訂用帳戶中的非預設儲存體上傳指令碼和檔案。此處將使用現有的 VM，但相同作業也可以在建立新的 VM 時完成。

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### 將指令碼上傳到橫跨不同儲存體帳戶的多個容器

  如果指令碼檔案儲存在多個容器中，則若要執行這些指令碼，您必須提供這些檔案的完整 SAS URL。

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### 從 Azure 入口網站加入自訂指令碼擴充功能

瀏覽至 <a href="https://portal.azure.com/ " target="_blank">Azure 入口網站</a>中的 VM，然後指定要執行的指令碼檔案來加入該擴充功能。

  ![][5]


### 解除安裝自訂指令碼擴充功能

您可以使用下列命令，從 VM 解除安裝自訂指令碼延伸模組。

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### 搭配範本使用自訂指令碼擴充功能

若要了解搭配 Azure 資源管理員範本使用自訂指令碼延伸模組的相關資訊，請參閱[這裡](virtual-machines-extensions-customscript%20-with%20template.md)的文件。

<!--Image references-->
[5]: ./media/virtual-machines-extensions-customscript/addcse.png

<!---HONumber=AcomDC_1223_2015-->