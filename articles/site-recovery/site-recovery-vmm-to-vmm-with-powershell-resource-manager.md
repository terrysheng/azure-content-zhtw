<properties
	pageTitle="使用 PowerShell 和 Microsoft Azure 資源管理員，保護從一個 VMM 網站移轉至另一個 VMM 網站的 VM。"
	description="使用 PowerShell 和 Azure 資源管理員，自動化兩個內部部署 VMM 網站與 Azure 之間的保護。"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="08/26/2015"
	ms.author="raynew"/>
	

#  在兩個 VMM 網站之間使用 PowerShell 和 Azure 資源管理員


## 概觀

Azure Site Recovery 可在一些部署案例中協調虛擬機器的複寫、容錯移轉及復原，為您的商務持續性與災害復原 (BCDR) 做出貢獻。如需完整的部署案例清單，請參閱 [Azure Site Recovery 概觀](site-recovery-overview.md)。

Azure PowerShell 是個模組，其提供了各種 Cmdlet 來透過 Windows PowerShell 管理 Azure。它可以搭配兩種模組類型使用：Azure 設定檔模組或 Azure 資源管理員 (ARM) 模組。

本文說明如何搭配 ARM 使用 Windows PowerShell® 來部署 Azure Site Recovery，以設定及協調兩個 VMM 網站之間的虛擬機器保護。在 Hyper-V 主機伺服器上執行並位於主要網站之 VMM 私人雲端中的虛擬機器，使用 HYPER-V 複本複寫和容錯移轉至次要 VMM 網站。

您不需要是 PowerShell 專家就能使用本文，但本文假設您了解模組、Cmdlet 和工作階段等基本概念。如需 Windows PowerShell 的詳細資訊，請參閱[開始使用 Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx)。深入了解[搭配使用 Azure PowerShell 與 Azure 資源管理員](powershell-azure-resource-manager.md)。

本文包含案例的必要條件，並說明如何設定 Azure PowerShell 以使用 Site Recovery、建立 Site Recovery 保存庫、在 VMM 伺服器上安裝 Azure Site Recovery 提供者並在保存庫中註冊、設定將套用到所有受保護虛擬機器之 VMM 雲端的保護設定，然後為這些虛擬機器啟用保護。您將測試容錯移轉，確認一切如預期般運作以完成動作。


## 開始之前

確認您已備妥這些必要條件：

- 您將需要 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。您將需要 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。您可以從[免費試用](pricing/free-trial/)開始。此外，您可以參閱 [Azure Site Recovery 管理員定價](http://azure.microsoft.com/pricing/details/site-recovery/)。
- 主要和次要網站上必須有執行 System Center 2012 R2 的 VMM 伺服器。
- 每部 VMM 伺服器至少應該有一個雲端包含：
	- 一或多個 VMM 主機群組。
	- 每個主機群組中的一或多個 Hyper-V 主機伺服器或叢集。
	- 來源 Hyper-V 伺服器上的一或多部虛擬機器。
	- 深入了解設定 VMM 雲端：

		- [使用 System Center 2012 R2 VMM 的私人雲端新功能](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=dfgvHAmYryA)和 [VMM 2012 和雲端](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html)。
		- [設定 VMM 雲端網狀架構](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
		- [在 VMM 中建立私人雲端](https://technet.microsoft.com/library/jj860425.aspx)與[逐步解說：使用 System Center 2012 SP1 VMM 建立私人雲端](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)。
- 一或多部 Hyper-V 伺服器至少執行具有 Hyper-V 角色的 Windows Server 2012，並且已安裝最新更新。VMM 雲端中必須包含伺服器或叢集。
- 如果您在叢集中執行 Hyper-V，請注意，如果您具有靜態 IP 位址叢集，並不會自動建立叢集代理。您必須手動設定叢集代理。如需指示，請參閱[設定 Hyper-V 複本代理人](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)。

	- 您將需要 Azure PowerShell。請確定您執行的是 Azure PowerShell 0.9.6 版或更新版本。請參閱[如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。 
	- 安裝 Azure PowerShell 也會安裝自訂的主控台。您可以從這個主控台或任何其他主機程式，例如 Windows PowerShell ISE，來執行 PowerShell 命令。

## 步驟 1：設定 PowerShell


1. 開啟 PowerShell 主控台並執行這個命令來切換至 ARM 模組：

    `Switch-AzureMode AzureResourceManager`

3. 現在執行這個命令，將您的 Azure 帳戶加入 PowerShell 工作階段。這個 Cmdlet 會提示您輸入帳戶的登入認證。

    `Add-AzureAccount`

	請注意，如果您是代表租用戶工作的 CSP 合作夥伴，當您加入 Azure 帳戶時，需要指定做為租用戶的客戶：

    `Add-AzureAccount-Tenant "customer"`

5. 一個帳戶可以有多個訂用帳戶，因此您必須將要使用的訂用帳戶與帳戶建立關聯。

    `Select-AzureSubscription -SubscriptionName $SubscriptionName`

6. 如果您是第一次在訂用帳戶中使用 Site Recovery Cmdlet，您需要註冊 Site Recovery 的 Azure 提供者：

    `Register-AzureProvider -ProviderNamespace Microsoft.SiteRecovery`

## 步驟 2：設定 Site Recovery 保存庫

1. 如果您目前沒有 Site Recovery 保存庫，您需要執行 [New-AzureSiteRecoveryVault](https://msdn.microsoft.com/library/azure/dn954225.aspx) Cmdlet 建立一個保存庫：

    `New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
    $vault = Get-AzureSiteRecoveryVault -Name $VaultName;`

## 步驟 3：產生保存庫註冊金鑰

1. 執行 [Get-AzureSiteRecoveryVaultSettingsFile](https://msdn.microsoft.com/library/azure/dn850404.aspx) Cmdlet 取得保存庫註冊金鑰。您需要這個金鑰，才能在保存庫中註冊 VMM 伺服器：

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;`

## 步驟 4：安裝 Azure Site Recovery 提供者

1. 從 Site Recovery 保存庫的 [快速入門] 頁面下載提供者安裝檔案。
2. 使用下列命令在每部 VMM 伺服器上建立資料夾：

    `pushd C:\ASR`

3. 執行下列命令從下載的提供者解壓縮檔案：

    `AzureSiteRecoveryProvider.exe /x:. /q`

4. 執行下列命令以安裝提供者：

    `.\SetupDr.exe /i` `$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
                $isNotInstalled = $true;
                if(Test-Path $installationRegPath)
                {
                                $isNotInstalled = $false;
                }
	}While($isNotInstalled)`

5. 等候安裝完成，然後在保存庫中註冊伺服器：

    `$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice`

## 步驟 5：設定保存庫內容

1. 執行 Get-AzureSiteRecoveryVault Cmdlet，以確定所有命令都會在特定保存庫下執行：

    `$Vault = Get-AzureSiteRecoveryVault -ResouceGroupName $ResourceGroupName | where { $_.Name -eq $VaultName}`

2. 下載保存庫設定：

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Vault $Vault -Path $OutputPathForSettingsFile`

3. 若要確定 Cmdlet 會在保存庫下執行：

    `Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSetingsFile.FilePath`

## 步驟 3：設定雲端保護設定

在保存庫中註冊 VMM 伺服器之後，您可以設定雲端保護設定，以套用至已註冊 VMM 伺服器之雲端中 HYPER-V 主機上的所有虛擬機器。

1. 在保存庫中為主要和次要雲端建立容器：

    `$PrimaryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $PrimaryCloudName` `$$RecoveryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $RecoveryCloudName`

2. 設定要套用至雲端的保護設定：

    `New-AzureSiteRecoveryProtectionProfile -Name $ProtectionProfileName -ReplicationProvider HyperVReplica -ReplicationMethod Online -ReplicationFrequencyInSeconds 30 -RecoveryPoints 1 -ApplicationConsistentSnapshotFrequencyInHours 0 -ReplicationPort 8083 -Authentication Kerberos -AllowReplicaDeletion`

3. 啟動將雲端容器與雲端保護設定建立關聯的工作：

    `Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $ProtectionProfile -PrimaryProtectionContainer $PrimaryContainer -RecoveryProtectionContainer $RecoveryContainer`


## 步驟 4：啟用 VM 保護

為 VMM 雲端中的 VM 啟用保護：

1. 取得您要保護的 VM：

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName $VMName `

2. 為 VM 啟用保護：

    `Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $VM -Protection Enable`


## 步驟 5：執行測試容錯移轉

1.	選取您要容錯移轉的 VM：

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName  $VMName`

2. 執行測試容錯移轉工作：

    `$ currentJob = Start-AzureSiteRecoveryTestFailoverJob -ProtectionEntity $VM -Direction PrimaryToRecovery`

3. 檢查容錯移轉後的 VM 是否出現在次要網站上，並完成容錯移轉：

    `Resume-AzureSiteRecoveryJob -Id $currentJob.Name`


## 後續步驟

如需這個案例的問題和意見，請瀏覽 [Site Recovery 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr/)。

<!---HONumber=August15_HO9-->