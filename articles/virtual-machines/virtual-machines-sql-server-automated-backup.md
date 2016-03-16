<properties
	pageTitle="SQL Server 虛擬機器的自動備份 | Microsoft Azure"
	description="針對在 Azure 虛擬機器中，以資源管理員部署模型來執行的 SQL Server，說明自動備份功能。"
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-resource-manager" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth" />

# Azure 虛擬機器中的 SQL Server 自動備份

自動備份會針對執行 SQL Server 2014 Standard 或 Enterprise 之 Azure VM 上所有現存和新的資料庫，自動設定 [Managed Backup 到 Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx)。這可讓您設定採用持久性 Azure Blob 儲存體的一般資料庫備份。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。

## 自動備份的設定

下表說明可以為自動備份設定的選項。實際的設定步驟會依據您是使用 Azure 入口網站或 Azure Windows PowerShell 命令而有所不同。

|設定|範圍 (預設值)|說明|
|---|---|---|
|**自動備份**|啟用/停用 (已停用)|針對執行 SQL Server 2014 Standard 或 Enterprise 的 Azure VM，啟用或停用自動備份。|
|**保留期限**|1-30 天 (30 天)|保留備份的天數。|
|**儲存體帳戶**|Azure 儲存體帳戶 (針對指定 VM 建立的儲存體帳戶)|將自動備份檔案儲存在 Blob 儲存體中時，所使用的 Azure 儲存體帳戶。這個位置會建立一個容器來儲存所有備份檔案。備份檔案命名慣例包括日期、時間和電腦名稱。|
|**加密**|啟用/停用 (已停用)|啟用或停用加密。啟用加密時，用來還原備份的憑證會放在與使用相同命名慣例之相同 automaticbackup 容器中的指定儲存體帳戶裡。如果密碼變更，就會以該密碼產生新的憑證，但是舊的憑證還是會保留，以還原先前的備份。|
|**密碼**|密碼文字 (無)|加密金鑰的密碼。唯有啟用加密時，才需要此密碼。若要還原加密的備份，您必須要有建立備份時所使用的正確密碼和相關憑證。|

## 在 Azure 入口網站中設定自動備份

在建立新的 SQL Server 2014 虛擬機器時，您可以透過 Azure 入口網站設定自動備份。

>[AZURE.NOTE] 自動備份相依於 SQL Server IaaS 代理程式。若要安裝並設定該代理程式，您必須要有在目標虛擬機器上執行的 Azure VM 代理程式。較新的虛擬機器資源庫映像預設會啟用此選項，但現有的 VM 上可能會遺失了 Azure VM 代理程式。如果您正在使用自己的 VM 映像，也需要安裝 SQL Server IaaS 代理程式。如需詳細資訊，請參閱 [VM 代理程式和延伸模組](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)。

下列 Azure 入口網站螢幕擷取畫面顯示 [選用組態] | [SQL 自動備份] 下的選項。

![Azure 入口網站中的 SQL 自動備份設定](./media/virtual-machines-sql-server-automated-backup/IC778483.jpg)

針對現有的 SQL Server 2014 虛擬機器，在虛擬機器屬性的 [**組態**] 區段中，選取 [**自動備份**] 設定。在 [**自動備份**] 視窗中，您可以啟用功能、設定保留期限、選取儲存體帳戶及設定加密。如下列螢幕擷取畫面所示。

![Azure 入口網站中的自動備份設定](./media/virtual-machines-sql-server-automated-backup/IC792133.jpg)

>[AZURE.NOTE] 當您第一次啟用自動備份時，Azure 會在背景中設定 SQL Server IaaS 代理程式。在此期間，Azure 入口網站不會顯示已設定自動備份。請等候幾分鐘的時間來安裝及設定代理程式。之後，Azure 入口網站將會反映新的設定。

## 使用 PowerShell 設定自動備份

在下列 PowerShell 範例中，是針對現有的 SQL Server 2014 VM 設定自動備份。**New-AzureVMSqlServerAutoBackupConfig** 命令會設定自動備份設定，以在 $storageaccount 變數所指定的 Azure 儲存體帳戶中儲存備份。這些備份將會保留 10 天。**Set-AzureVMSqlServerExtension** 命令會使用這些設定更新指定的 Azure VM。

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

可能需要幾分鐘的時間來安裝及設定 SQL Server IaaS 代理程式。

若要啟用加密，請修改先前的指令碼，為 CertificatePassword 參數傳遞 EnableEncryption 參數和密碼 (安全字串)。下列指令碼會啟用上述範例中的自動備份設定，並新增加密。

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

若要停用自動備份，請執行相同的指令碼，但不要對 **New-AzureVMSqlServerAutoBackupConfig** 使用 **-Enable** 參數。和安裝一樣，可能需要幾分鐘的時間來停用自動備份。

## 停用及解除安裝 SQL Server IaaS 代理程式

如果您想要停用自動備份和修補作業的 SQL Server IaaS 代理程式，請使用下列命令：

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

若要解除安裝 SQL Server IaaS 代理程式，請使用下列語法：

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

您也可以使用 **Remove-AzureVMSqlServerExtension** 命令解除安裝延伸模組：

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

>[AZURE.NOTE] 停用及解除安裝 SQL Server IaaS 代理程式不會移除先前設定的受管理備份設定。在停用或解除安裝 SQL Server IaaS 代理程式之前，應先停用自動備份。

## 相容性

下列產品與自動備份的 SQL Server IaaS 代理程式功能相容：

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2014 Standard

- SQL Server 2014 Enterprise

## 後續步驟

自動備份會在 Azure VM 上設定受管理備份。因此，請務必[檢閱受管理備份的文件](https://msdn.microsoft.com/library/dn449496.aspx)，以了解其行為和隱含意義。

您可以在下列主題中找到 Azure VM 上 SQL Server 的其他備份和還原指引：[Azure 虛擬機器中的 SQL Server 備份和還原](virtual-machines-sql-server-backup-and-restore.md)。

Azure 中的 SQL Server VM 相關功能為 [Azure 虛擬機器中的 SQL Server 自動修補](virtual-machines-sql-server-automated-patching.md)。

請檢閱其他[在 Azure 虛擬機器中執行 SQL Server 的資源](virtual-machines-sql-server-infrastructure-services.md)。

<!---HONumber=AcomDC_0204_2016-->