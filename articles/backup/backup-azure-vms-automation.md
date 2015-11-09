<properties
	pageTitle="使用 PowerShell 部署和管理 Azure VM 的備份 | Microsoft Azure"
	description="了解如何使用 PowerShell 部署和管理 Azure 備份"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/01/2015" ms.author="aashishr";"trinadhk" />


# 使用 PowerShell 部署和管理 Azure VM 的備份
本文說明如何使用 Azure PowerShell 來備份和復原 Azure IaaS VM。

## 概念
取得 Azure 備份文件中的 [Azure IaaS VM 備份簡介](backup-azure-vms-introduction.md)。其中涵蓋為什麼應該備份 VM 的基本資訊、必要條件和限制。

若要有效地使用 PowerShell，就必須了解物件的階層及從何處開始。

![物件階層](./media/backup-azure-vms-automation/object-hierarchy.png)

最重要的 2 個流量可以保護 VM 和從復原點還原資料。本文的重點是協助您熟練 PowerShell Cmdlet 來支援這兩種情況。


## 設定和註冊
開始：

1. [下載最新的 PowerShell](https://github.com/Azure/azure-powershell/releases) (所需最低版本為：1.0.0)

2. 使用 **Switch-AzureMode** Cmdlet 切換至 *AzureResourceManager* 模式，以啟用 Azure 備份 Cmdlet：

```
PS C:\> Switch-AzureMode AzureResourceManager
```

PowerShell 可以自動化下列設定和註冊工作：

- 建立備份保存庫
- 向 Azure 備份服務註冊 VM

### 建立備份保存庫

> [AZURE.WARNING]對於第一次使用 Azure 備份的客戶，您需要註冊 Azure 備份提供者以搭配您的訂用帳戶使用。這可以透過執行下列命令來完成：Register-AzureProvider -ProviderNamespace "Microsoft.Backup"

您可以使用 **New-AzureRMBackupVault** Cmdlet 建立新的備份保存庫。備份保存庫是 ARM 資源，因此您必須將它放在資源群組內。在提高權限的 Azure PowerShell 主控台中，執行下列命令：

```
PS C:\> New-AzureRMResourceGroup –Name “test-rg” –Region “West US”
PS C:\> $backupvault = New-AzureRMBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

您可以使用 **Get-AzureRMBackupVault** Cmdlet 取得特定訂用帳戶中所有備份保存庫的清單。

> [AZURE.NOTE]將備份保存庫物件儲存到變數中會很方便。許多 Azure 備份 Cmdlet 都需要保存庫物件做為輸入。


### 註冊 VM
使用 Azure 備份來設定備份的第一個步驟是向 Azure 備份保存庫註冊您的電腦或 VM。**Register-AzureRMBackupContainer** Cmdlet 會取用 Azure IaaS 虛擬機器的輸入資訊，並向指定的保存庫註冊。註冊作業會將 Azure 虛擬機器與備份保存庫相關聯，並於整個備份生命週期內追蹤 VM。

向 Azure 備份服務註冊您的 VM 會建立最上層的容器物件。一個容器通常包含多個可備份的項目，但以 VM 而言，容器只有一個備份項目。

```
PS C:\> $registerjob = Register-AzureRMBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## 備份 Azure VM

### 建立保護原則
啟動 VM 的備份並不一定要建立新的保護原則。保存庫隨附「預設原則」，可用來快速啟用保護，稍後再編輯正確的細節。您可以使用 **Get-AzureRMBackupProtectionPolicy** Cmdlet 取得保存庫中可用的原則清單：

```
PS C:\> Get-AzureRMBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

> [AZURE.NOTE]PowerShell 中 BackupTime 欄位的時區是 UTC。不過，當備份時間顯示在 Azure 入口網站中時，時區會對應您的本機系統與 UTC 時差。

備份原則至少與一個保留原則相關聯。保留原則定義復原點在 Azure 備份中保留的時間長度。**New-AzureRMBackupRetentionPolicy** Cmdlet 會建立可儲存保留原則資訊的 PowerShell 物件。這些保留原則物件做為 *New-AzureRMBackupProtectionPolicy* Cmdlet 的輸入，或直接與 *Enable-AzureRMBackupProtection* Cmdlet 搭配使用。

備份原則定義項目備份的時間和頻率。**New-AzureRMBackupProtectionPolicy** Cmdlet 會建立可儲存備份原則資訊的 PowerShell 物件。備份原則用來做為 *Enable-AzureRMBackupProtection* Cmdlet 的輸入。

```
PS C:\> $Daily = New-AzureRMBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRMBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy ($Daily) -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### 啟用保護。
啟用保護牽涉到兩個物件：項目和原則，兩者都必須屬於相同的保存庫。一旦原則與項目相關聯，備份工作流程將依定義的排程開始運作。

```
PS C:\> Get-AzureRMBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRMBackupItem | Enable-AzureRMBackupProtection -Policy $newpolicy
```

### 初始備份
備份排程會負責執行項目的完整初始複製，以及後續備份的增量複製。不過，如果您想要強制初始備份在特定時間進行，甚至是立即開始，請使用 **Backup-AzureRMBackupItem** Cmdlet：

```
PS C:\> $container = Get-AzureRMBackupContainer -Vault $backupvault -type AzureVM -name "testvm"
PS C:\> $backupjob = Get-AzureRMBackupItem -Container $container | Backup-AzureRMBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

> [AZURE.NOTE]在 PowerShell 中顯示的 StartTime 和 EndTime 欄位的時間為 UTC。不過，當類似資訊顯示在 Azure 入口網站中時，時區會對應您的本機系統時鐘。

### 監視備份工作
Azure 備份中長時間執行的大部分作業都模擬成工作。如此就很容易追蹤進度，而不需要一直開啟 Azure 入口網站。

若要取得進行中工作的最新狀態，請使用 **Get-AzureRMBackupJob** Cmdlet。

```
PS C:\> $joblist = Get-AzureRMBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

不需要輪詢這些工作何時完成 (這是多餘、額外的程式碼)，使用 **Wait-AzureRMBackupJob** Cmdlet 更簡單。在指令碼中使用此 Cmdlet 會暫停執行，直到工作完成或達到指定的逾時值為止。

```
PS C:\> Wait-AzureRMBackupJob -Job $joblist[0] -Timeout 43200
```


## 還原 Azure VM

若要還原備份資料，您需要識別備份的「項目」和保存時間點資料的「復原點」。這項資訊提供給還原 Restore-AzureRMBackupItem Cmdlet，以開始從保存庫將資料還原到客戶帳戶。

### 選取 VM

若要取得可識別正確備份項目的 PowerShell 物件，您需要從保存庫中的「容器」開始，向下深入物件階層。若要選取代表 VM 的容器，請使用 **Get-AzureRMBackupContainer** Cmdlet，並將它傳給 **Get-AzureRMBackupItem** Cmdlet。

```
PS C:\> $backupitem = Get-AzureRMBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRMBackupItem
```

### 選擇復原點

您現在可以使用 **Get-AzureRMBackupRecoveryPoint** Cmdlet 列出備份項目的所有復原點，並選擇要還原的復原點。使用者通常會從清單中選擇最近的 *AppConsistent* 點。

```
PS C:\> $rp =  Get-AzureRMBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

變數 ```$rp``` 是所選取之備份項目的復原點陣列，以時間的相反順序儲存 - 最新的復原點位於索引 0。使用標準 PowerShell 陣列索引來挑選復原點。例如：```$rp[0]``` 將會選取最新的復原點。

### 還原磁碟

透過 Azure 入口網站或透過 Azure PowerShell 執行還原作業，兩者之間有一項重要差異。如果使用 PowerShell，從復原點還原磁碟及組態資訊時，還原作業會停止。不會建立虛擬機器。

> [AZURE.WARNING]Restore-AzureRMBackupItem 不會建立 VM。只會將磁碟還原到指定的儲存體帳戶。這與 Azure 入口網站中的行為不同。

```
PS C:\> $restorejob = Restore-AzureRMBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp[0]
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

完成還原作業之後，您可以使用 **Get-AzureRMBackupJobDetails** Cmdlet 取得還原作業的詳細資料。*ErrorDetails* 屬性會有重建 VM 所需的資訊。

```
PS C:\> $restorejob = Get-AzureRMBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRMBackupJobDetails -Job $restorejob
```

### 建置 VM

如果要從還原的磁碟建置 VM，您可以使用較舊的 Azure ServiceManager PowerShell Cmdlet、新的 Azure ResourceManager 範本，或甚至使用 Azure 入口網站。在快速的範例中，我們將說明如何使用 Azure ServiceManager Cmdlet 來達到目的。

```
 $properties  = $details.Properties

 $storageAccountName = $properties["TargetStorageAccountName"]
 $containerName = $properties["TargetContainerName"]
 $blobName = $properties["TargetBlobName"]

 Switch-AzureMode AzureServiceManagement

 $keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
 $storageAccountKey = $keys.Primary
 $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


 $destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
 Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


 $obj = [xml](Get-Content $destination_path)
 $pvr = $obj.PersistentVMRole
 $os = $pvr.OSVirtualHardDisk
 $dds = $pvr.DataVirtualHardDisks
 $osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
 $vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

 if (!($dds -eq $null))
 {
	 foreach($d in $dds.DataVirtualHardDisk)
 	 {
		 $lun = 0;
		 if(!($d.Lun -eq $null))
		 {
	 		 $lun = $d.Lun
		 }
		 $name = "panbhadataDisk" + $lun
     Add-AzureDisk -DiskName $name -MediaLocation $d.MediaLink
     $vm | Add-AzureDataDisk -Import -DiskName $name -LUN $lun
	}
}

New-AzureVM -ServiceName "panbhasample" -Location "SouthEast Asia" -VM $vm
```

如需有關如何從還原的磁碟建置 VM 的詳細資訊，請參閱下列 Cmdlet：

- [Add-AzureDisk](https://msdn.microsoft.com/library/azure/dn495252.aspx)
- [New-AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
- [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)

## 程式碼範例

### 1\.取得作業子工作的完成狀態

若要追蹤個別子工作的完成狀態，您可以使用 **Get AzureRMBackupJobDetails** Cmdlet：

```
PS C:\> $details = Get-AzureRMBackupJobDetails -JobId $backupjob.InstanceId -Vault $backupvault
PS C:\> $details.SubTasks

Name                                                        Status
----                                                        ------
Take Snapshot                                               Completed
Transfer data to Backup vault                               InProgress
```

### 2\.建立備份作業的每日/每週報表

系統管理員通常想要知道備份作業在過去 24 小時的執行情況，以及那些備份作業的狀態。此外，傳輸的資料量提供系統管理員一種預估其每月資料使用量的方式。下面指令碼會從 Azure 備份服務提取原始資料，並將資訊顯示在 PowerShell 主控台上。

```
param(  [Parameter(Mandatory=$True,Position=1)]
        [string]$backupvaultname,

        [Parameter(Mandatory=$False,Position=2)]
        [int]$numberofdays = 7)


#Initialize variables
$DAILYBACKUPSTATS = @()
$backupvault = Get-AzureRMBackupVault -Name $backupvaultname
$enddate = ([datetime]::Today).AddDays(1)
$startdate = ([datetime]::Today)

for( $i = 1; $i -le $numberofdays; $i++ )
{
    # We query one day at a time because pulling 7 days of data might be too much
    $dailyjoblist = Get-AzureRMBackupJob -Vault $backupvault -From $startdate -To $enddate -Type AzureVM -Operation Backup
    Write-Progress -Activity "Getting job information for the last $numberofdays days" -Status "Day -$i" -PercentComplete ([int]([decimal]$i*100/$numberofdays))

    foreach( $job in $dailyjoblist )
    {
        #Extract the information for the reports
        $newstatsobj = New-Object System.Object
        $newstatsobj | Add-Member -type NoteProperty -name Date -value $startdate
        $newstatsobj | Add-Member -type NoteProperty -name VMName -value $job.WorkloadName
        $newstatsobj | Add-Member -type NoteProperty -name Duration -value $job.Duration
        $newstatsobj | Add-Member -type NoteProperty -name Status -value $job.Status

        $details = Get-AzureRMBackupJobDetails -Job $job
        $newstatsobj | Add-Member -type NoteProperty -name BackupSize -value $details.Properties["Backup Size"]
        $DAILYBACKUPSTATS += $newstatsobj
    }

    $enddate = $enddate.AddDays(-1)
    $startdate = $startdate.AddDays(-1)
}

$DAILYBACKUPSTATS | Out-GridView
```

如果您想要將製作圖表功能加入至這個報表輸出，請在 TechNet 部落格上了解[使用 PowerShell 製作圖表](http://blogs.technet.com/b/richard_macdonald/archive/2009/04/28/3231887.aspx)

<!---HONumber=Nov15_HO1-->