<properties
   pageTitle="manage-vms-azure-powershell"
   description="使用 Azure PowerShell 管理您的 VM"
   services="virtual-machines"
   documentationCenter="windows"
   authors="singhkay"
   manager="timlt"
   editor=""/>

   <tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="02/20/2015"
   ms.author="kasing"/>

# 使用 Azure PowerShell 管理您的虛擬機器

開始執行之前，您必須確定已安裝 Azure PowerShell。若要這樣做，請造訪[如何安裝及設定 Azure PowerShell](install-configure-powershell.md)

## 取得映像

建立 VM 之前，您需要決定「要使用哪一個映像」****。您可以使用下列 Cmdlet 來取得映像清單

      Get-AzureVMImage

這個 Cmdlet 將傳回在 Azure 中所有可用的映像清單。這份清單很長，而且很難找到您想要使用的確切映像。在下列範例中，我會使用其他的 PowerShell Cmdlet 來縮減傳回的映像清單，使其只包含以 **Windows Server 2012 R2 Datacenter** 為依據的映像。此外，會為傳回的映像陣列指定 [-1]，來選擇只取得最新映像。

    $img = (Get-AzureVMImage | Select -Property ImageName, Label | where {$_.Label -like '*Windows Server 2012 R2 Datacenter*'})[-1]

## 建立 VM

VM 的建立是從 **New-AzureVMConfig** Cmdlet 開始。您將在此處指定 VM 的「名稱」****、VM 的「大小」****，以及要供該 VM 使用的「映像」****。這個 Cmdlet 會建立本機 VM 物件 **$myVM**，稍後可使用本指南中的其他 Azure PowerShell Cmdlet 來修改此物件。

      $myVM = New-AzureVMConfig -Name "testvm" -InstanceSize "Small" -ImageName $img.ImageName

接著，您需要選擇 VM 的「使用者名稱」****和「密碼」****。您可以使用 **Add-AzureProvisioningConfig** Cmdlet 來執行此動作。這是您告知 Azure，VM 為何種作業系統的地方。而不是說您仍在對本機 **$myVM** 物件進行變更。

    $user = "azureuser"
    $pass = "&Azure1^Pass@"
    $myVM = Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass

最後，您已經準備好在 Azure 中運作您的 VM。若要這樣做，您將需要使用 **New-azurevm** Cmdlet

> [AZURE.NOTE] 您必須先設定雲端服務，才能建立 VM。做法有二種。
* 使用 New-AzureService Cmdlet 來建立雲端服務。如果您選擇這個方法，則必須確定以下在 New-AzureVM Cmdlet 中指定的位置與您雲端服務的位置相符，否則 New-AzureVM Cmdlet 執行 Cmdlet 將會失敗。
* 讓 New-AzureVM Cmdlet 來為您執行此動作。您必須確定該服務的名稱是唯一，否則 New-AzureVM Cmdlet 執行將會失敗。

    New-AzureVM -ServiceName "mytestserv" -VMs $myVM -Location "West US"

**選擇性**

您可以使用其他 Cmdlet (例如 **Add-AzureDataDisk**、**Add-AzureEndpoint**) 來設定 VM 的其他選項

## 取得 VM
現在您已在 Azure 上建立 VM，您一定想要看到它正在進行。您可以使用 **Get-AzureVM** Cmdlet 來執行此動作，如下所示

    Get-AzureVM -ServiceName "mytestserv" -Name "testvm"


## 後續步驟
[透過 RDP 或 SSH 連接到 Azure 虛擬機器](https://msdn.microsoft.com/library/azure/dn535788.aspx)<br>
[Azure 虛擬機器常見問題集](https://msdn.microsoft.com/library/azure/dn683781.aspx)

<!--HONumber=47-->
