<properties
   pageTitle="Azure 的預期狀態組態概觀 | Microsoft Azure"
   description="將 Microsoft Azure 擴充功能處理常式用於 PowerShell 預期狀態組態的概觀。包括必要條件、架構、Cmdlet..."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="01/25/2016"
   ms.author="zachal"/>

# Azure 期望狀態組態擴充功能處理常式簡介 #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure VM 代理程式和相關聯的擴充功能是 Microsoft Azure 基礎結構服務的一部分。VM 擴充功能是軟體元件，它們能擴充 VM 功能及簡化各種不同的 VM 管理作業。例如，VMAccess 擴充功能可用來重設 VM 的密碼；自訂指令碼擴充功能可以用來在 VM 上執行指令碼。

本文將介紹隸屬於 Azure PowerShell SDK 的 Azure VM PowerShell 期望狀態組態 (DSC) 擴充功能。透過新的 Cmdlet，您可以將 PowerShell DSC 組態上傳到由 PowerShell DSC 擴充功能啟用的 Azure VM 上，並予以套用。PowerShell DSC 擴充功能會呼叫 PowerShell DSC 來執行於 VM 上接收的 DSC 組態。這項功能也可以透過 Preview 入口網站 UI 來使用。

## 必要條件 ##
**本機** 若要與 Azure VM 擴充功能互動，您需要使用 Preview 入口網站 UI 或 Azure PowerShell SDK。

**客體代理程式** 要以 DSC 組態進行配置的 Azure VM 必須為支援 Windows Management Framework (WMF) 4.0 或 5.0 的作業系統。如需支援之作業系統版本的完整清單，請參閱 DSC 擴充功能版本歷程記錄。

## 詞彙和概念 ##
本指南假設您已熟悉下列概念︰

組態 - DSC 組態文件。

節點 - DSC 組態的目標。本文件中，「節點」一律代表 Azure VM。

組態資料 - 包含組態之環境資料的 .psd1 檔案

## 架構概觀 ##

Azure DSC 擴充功能使用 Azure 代理程式架構來傳遞、執行及報告於 Azure VM 上執行的 DSC 組態。DSC 擴充功能必須有至少包含一份組態文件的 .zip 檔案，以及一組透過 Azure PowerShell SDK 或入口網站 UI 提供的參數。

首次呼叫擴充功能時，它會執行安裝程序。此程序會安裝如以下定義之版本的 Windows Management Framework (WMF)︰

1. 如果 Azure VM 作業系統是 Windows Server 2016，則不採取任何動作。WS 2016 已安裝最新版本的 PowerShell。
2. 如果您已指定 `wmfVersion` 屬性，除非該 WMF 版本與 VM 的作業系統不相容，否則會逕行安裝。
3. 如果您未指定 `wmfVersion` 屬性，則會安裝 WMF 的最新適用版本。

安裝 WMF 需要重新開機。重新開機後，擴充功能會下載 `modulesUrl` 屬性指定的 .zip 檔案。如果此位置在 Azure Blob 儲存體中，您可以在 `sasToken` 屬性中指定 SAS 權杖來存取檔案。下載並解壓縮 .zip 之後，於 `configurationFunction` 中定義的組態函式會執行以產生 MOF 檔案。擴充功能接著會針對產生的 MOF 檔案執行 `Start-DscConfiguration -force`。擴充功能會擷取輸出並寫回 Azure 狀態通道。DSC LCM 會從這裡開始正常處理監視和更正。

## PowerShell Cmdlet ##

Powershell Cmdlet 可與 ARM 或 ASM 搭配使用，以便封裝、發佈和監視 DSC 擴充功能部署。下列 Cmdlet 是 ASM 模組，不過您可以將 Azure 取代為 AzureRM 來使用 ARM 模型。例如，`Publish-AzureVMDscConfiguration` 會使用 ASM，而 `Publish-AzureRmVMDscConfiguration` 則會使用 ARM。

`Publish-AzureVMDscConfiguration` 會接受組態檔案、掃描其中是否有相依的 DSC 資源，然後建立包含執行組態所需之組態和 DSC 資源的 .zip 檔案。它也可以使用 `-ConfigurationArchivePath` 參數在本機建立封裝。否則，它會將 .zip 檔案發佈至 Azure Blob 儲存體，再以 SAS 權杖加以保護。

對於這個 Cmdlet 所建立的 .zip 檔案，.ps1 組態指令碼位於封存資料夾的根目錄。資源會將模組資料夾放置在封存資料夾中。

`Set-AzureVMDscExtension` 會將 PowerShell DSC 擴充功能所需的設定插入 VM 組態物件，以供 `Update-AzureVm` 套用至 Azure VM。

`Get-AzureVMDscExtension` 會擷取特定 VM 的 DSC 擴充模組狀態。

`Get-AzureVMDscExtensionStatus` 會擷取 VM 或 VM 群組上由 DSC 擴充功能處理常式執行之 DSC 組態的狀態。

`Remove-AzureVMDscExtension` 會移除指定虛擬機器的擴充功能處理常式。它**不會**移除組態、解除安裝 WMF 或變更虛擬機器上已套用的設定，只會移除擴充功能處理常式。

**ASM 和 ARM Cmdlet 的主要差異**

- ARM Cmdlet 具備同步性；ASM Cmdlet 具備非同步性。
- ResourceGroupName、VMName、ArchiveStorageAccountName、Version、Location 皆為新的必要參數。
- ArchiveResourceGroupName 是 ARM 的新選擇性參數。當儲存體帳戶隸屬的資源群組與建立虛擬機器的資源群組不同時，您可以指定此參數。
- ConfigurationArchive 在 ARM 中名為 ArchiveBlobName
- ContainerName 在 ARM 中名為 ArchiveContainerName
- StorageEndpointSuffix 在 ARM 中名為 ArchiveStorageEndpointSuffix
- 我們已將 –AutoUpdate 參數加入 ARM，讓擴充功能處理常式能在有最新版本時自動更新。請注意，當新版本的 WMF 發行時，這有可能會導致 VM 重新啟動。 


## Preview 入口網站功能 ##
瀏覽至 VM。在 [設定] -> [管理] 下方按一下 [擴充功能]。系統會建立新窗格。按一下 [加入]，然後選取 [PowerShell DSC]。

入口網站需要輸入。**組態模組或指令碼**︰這是必要欄位。需要包含組態指令碼的 .ps1 檔案，或 .ps1 組態指令碼位在根目錄，且所有相依資源位在模組資料夾的 .zip 檔案。您可以使用 Azure PowerShell SDK 隨附的 `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` Cmdlet 來建立。系統會將 zip 會上傳到受 SAS 權杖保護的使用者 Blob 儲存體。

**組態資料 PSD1 檔案**︰這是選擇性欄位。如果您的組態要求 .psd1 中須有組態資料檔，請使用此欄位來進行選取，然後將它上傳到受 SAS 權杖保護的使用者 Blob 儲存體。
 
**組態的模組完整名稱**：.ps1 檔案可以有多個組態函式。請輸入組態 .ps1 指令碼的名稱，後面再加上 '' 和組態函式的名稱。例如：

**組態引數**︰如果組態函式接受引數，請依照 `argumentName1=value1,argumentName2=value2` 格式輸入此處。請注意，此處的格式與透過 PowerShell Cmdlet 或 ARM 範本接受組態引數的方式不同。

## 開始使用 ##

Azure DSC 擴充功能會接受 DSC 組態文件，並會在 Azure VM 上執行。以下是簡單的組態範例。以 "IisInstall.ps1" 的名稱將它儲存在本機：

```powershell
configuration IISInstall 
{ 
    node ("localhost") 
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

下列步驟會將 IisInstall.ps1 指令碼置於指定的 VM 上、執行組態，然後回報狀態。
 
```powershell
#Requires Azure Powershell cmdlets
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -vm $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -verbose

#Azure Powershell batches commands until an update-AzureVM command is given
$demoVM | Update-AzureVM -Verbose

#check on status
get-azurevmdscextensionstatus -VM $demovm -verbose
```

## 記錄 ##

記錄檔位於︰

C:\\WindowsAzure\\Logs\\Plugins\\Microsoft.Powershell.DSC[Version Number]

## 後續步驟 ##

如需有關 PowerShell DSC 的詳細資訊，[請造訪 PowerShell 文件中心](https://msdn.microsoft.com/powershell/dsc/overview)。

若要尋找您可以使用 PowerShell DSC 管理的其他功能，[請瀏覽 PowerShell 資源庫](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)以取得更多 DSC 資源。

如需將敏感性參數傳遞到組態的詳細資訊，請參閱[使用 DSC 擴充功能處理常式安全地管理認證](virtual-machines-windows-extensions-dsc-credentials.md)。

<!---HONumber=AcomDC_0323_2016-->