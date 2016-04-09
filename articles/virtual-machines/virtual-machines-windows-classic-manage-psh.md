<properties
   pageTitle="使用 Azure PowerShell 管理虛擬機器 | Microsoft Azure"
   description="了解可用來自動執行管理虛擬機器工作的命令。"
   services="virtual-machines-windows"
   documentationCenter="windows"
   authors="singhkay"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="kasing"/>

# 使用 Azure PowerShell 管理您的虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。


可以使用 Azure PowerShell Cmdlet 自動執行許多 VM 的日常管理工作。這篇文章提供了幾個簡單工作的範例命令，另外也提供顯示用來完成更複雜的工作之命令的文章連結。

>[AZURE.NOTE] 如果您尚未安裝和設定 Azure PowerShell，可以在[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 文章中取得相關指示。

## 如何使用範例命令
命令中的某些文字必須換成適合您環境的文字。 < and > 符號表示您需要取代的文字。當您取代文字時，請移除符號，但將引號保留在原處。

## 取得 VM
這是您會經常使用的基本工作。使用它來取得 VM 的相關資訊、在 VM 上執行工作，或取得輸出以儲存至變數中。

若要取得 VM 的相關資訊，請執行此命令，並取代引號中的所有內容 (包括 < and > 字元)：

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

若要儲存 $vm 變數中的輸出，請執行：

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## 登入以 Windows 為基礎的 VM

執行以下命令：

>[AZURE.NOTE] 您可以從 **Get-azurevm** 命令顯示的畫面中，取得虛擬機器和雲端服務名稱。
>
	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## 停止 VM

請執行這個命令：

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT] 萬一它是雲端服務的最後一個 VM，您可以使用這個參數來保留雲端服務的虛擬 IP (VIP)。<br><br> 如果使用 StayProvisioned 參數，還是需要支付 VM 的費用。

## 啟動 VM

請執行這個命令：

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## 連接資料磁碟
這項工作需要幾個步驟。首先，請使用 ****Add-AzureDataDisk**** Cmdlet 將磁碟新增至 $vm 物件。然後使用 **Update-AzureVM** Cmdlet 來更新 VM 的組態。

您也需要決定是否要附加新的磁碟或附加已經包含資料的磁碟。如果是新的磁碟，此命令會建立 .vhd 檔案，並附加該檔案。

若要附加新的磁碟，請執行這個命令：

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM <$vm> `
              | Update-AzureVM

若要附加現有的資料磁碟，請執行這個命令：

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> `
              | Update-AzureVM

若要從 Blob 儲存體中現有的 .vhd 檔案附加資料磁碟，請執行這個命令：

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> `
              | Update-AzureVM

## 建立以 Windows 為基礎的 VM

若要在 Azure 中建立以 Windows 為基礎的新虛擬機器，請依照[使用 Azure PowerShell 建立和預先設定建立以 Windows 為基礎的虛擬機器](virtual-machines-windows-classic-create-powershell.md)中的指示執行。本主題會逐步教您建立 Azure PowerShell 命令集，然後利用這些命令建立可預先設定的以 Windows 為基礎的 VM：

- 具有 Active Directory 網域成員資格。
- 具有額外的磁碟。
- 成為現有負載平衡集的成員。
- 具有靜態 IP 位址。

## 建立以 Linux 為基礎的 VM

若要在 Azure 中建立以 Linux 為基礎且預先設定的新虛擬機器，請依照[使用 Azure Powershell 建立和預先設定 Linux 虛擬機器](virtual-machines-linux-classic-createpowershell.md)中的指示執行。

- 具有額外的磁碟。
- 成為現有負載平衡集的成員。
- 具有靜態 IP 位址。

<!---HONumber=AcomDC_0323_2016-->