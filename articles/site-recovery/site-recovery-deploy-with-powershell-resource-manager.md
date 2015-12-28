<properties
	pageTitle="透過 Azure 資源管理員使用 Azure PowerShell 保護伺服器 |Microsoft Azure"
	description="使用 PowerShell 和 Azure 資源管理員，透過 Azure Site Recovery 自動化伺服器保護至 Azure。"
	services="site-recovery"
	documentationCenter=""
	authors="bsiva"
	manager="abhiag"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="12/09/2015"
	ms.author="bsiva"/>

# 使用 PowerShell 和 Azure 資源管理員的 Azure Site Recovery。


## 概觀

Azure Site Recovery 可在一些部署案例中協調虛擬機器的複寫、容錯移轉及復原，為您的商務持續性與災害復原 (BCDR) 做出貢獻。如需完整的部署案例清單，請參閱 [Azure Site Recovery 概觀](site-recovery-overview.md)。

Azure PowerShell 是個模組，其提供了各種 Cmdlet 來透過 Windows PowerShell 管理 Azure。它可以搭配兩種模組類型使用：Azure 設定檔模組或 Azure 資源管理員 (ARM) 模組。

適用於 ARM 的 PowerShell 可使用的 Azure Site Recovery(ASR) PowerShell Cmdlet 可讓您在 Azure 中保護與復原伺服器。

本文說明如何搭配 ARM 使用 Windows PowerShell® 來部署 Azure Site Recovery，以利用範例的說明來設定及協調 Azure 的伺服器保護。在本文中使用的範例說明如何使用包含 ARM 的 Azure PowerShell 保護、容錯移轉及復原 Hyper-V 主機上的虛擬機器至 Azure。

> [AZURE.NOTE]目前，Azure Site Recovery PowerShell cmdlet 可讓您設定 VMM 站台至 VMM 站台、VMM 站台至 Azure 和 Azure 案例的 Hyper-V 站台。即將加入對其他 ASR 案例的支援。

您不需要是 PowerShell 專家就能使用本文，但本文假設您了解模組、Cmdlet 和工作階段等基本概念。如需 Windows PowerShell 的詳細資訊，請參閱[開始使用 Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx)。深入了解[搭配使用 Azure PowerShell 與 Azure 資源管理員](powershell-azure-resource-manager.md)。


## 主要功能

- 保護您的伺服器至 Azure、執行容錯移轉並將其復原至 Azure IaaS (ARM) 或 Azure IaaS (classic)
- 屬於雲端解決方案提供者(CSP) 程式的 Microsoft 合作夥伴可以設定及管理其客戶的伺服器到客戶的 CSP 訂用帳戶 (租用戶訂用帳戶) 的保護

## 開始之前

確認您已備妥這些必要條件：

- 您將需要 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。您將需要 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。您可以從[免費試用](pricing/free-trial/)開始。此外，您可以參閱 [Azure Site Recovery 管理員價格](http://azure.microsoft.com/pricing/details/site-recovery/)。
- 您將需要 Azure PowerShell 1.0。如需有關此版本以及如何安裝的資訊，請參閱 [Azure PowerShell 1.0](http://azure.microsoft.com/)。
- 您必須安裝 [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) 和 [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) 模組。您可以從 [PowerShell](https://www.powershellgallery.com/) 資源庫取得這些模組的最新版本

這篇文章說明如何透過範例的說明搭配使用 Azure Powershell 和 ARM 來設定及管理對伺服器的保護。在本文中使用的範例將示範如何保護在 Hyper-V 主機上執行的虛擬機器至 Azure，以及後續專屬於此範例的必要條件。針對一組更完整的各種 ASR 案例需求，請參閱屬於該案例的文件。

- 您將需要執行 Windows Server 2012 R2 且包含一或多個虛擬機器的 Hyper-V 主機。
- Hyper-V 伺服器應該直接或透過 Proxy 連接到網際網路。
- 您想要保護的虛擬機器必須符合[虛擬機器必要條件](site-recovery-best-practices.md#virtual-machines)。
	

## 步驟 1：登入您的 Azure 帳戶


1. 開啟 PowerShell 主控台並執行這個命令來登入您的 Azure 帳戶。此 cmdlet 會開啟網頁，提示您輸入您的帳戶認證。

    	Login-AzureRmAccount

	或者，您也可以使用 `-Credential` 參數以參數形式將您的帳戶認證加入 `Login-AzureRmAccount` cmdlet。
	
	如果您是代表租用戶工作的 CSP 合作夥伴，您必須使用客戶的 tenantID 或租用戶主要網域名稱來指定做為租用戶的客戶：

		Login-AzureRmAccount -Tenant "fabrikam.com"

2. 一個帳戶可以有多個訂用帳戶，因此您必須將要使用的訂用帳戶與帳戶建立關聯。

    	Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  確認您的訂用帳戶已使用下列命令註冊，以使用 Azure 復原服務提供者和 Site Recovery：-

	- `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
	-  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

	如果 RegistrationState 在上述兩個命令的輸出中設為已註冊，您可以繼續執行步驟 2。如果未設定，您必須在您的訂用帳戶中註冊遺漏的提供者。


	若要註冊 Site Recovery 的 Azure 提供者，請執行下列動作：

    	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
	
	同樣地，如果您第一次在訂用帳戶中使用復原服務 cmdlet，您必須註冊 Azure 復原服務提供者。執行這項動作之前，您必須先藉由執行下列命令，在您的訂用帳戶上啟用復原服務提供者的存取權：

		Register-AzureRmProviderFeature -FeatureName betaAccess -ProviderNamespace Microsoft.RecoveryServices

	>[AZURE.TIP]在成功完成上述命令之後，要在您的訂用帳戶上啟用復原服務提供者的存取權可能需要一小時。嘗試使用 `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices` 命令在訂用帳戶中註冊復原服務提供者可能會在過渡期間失敗。如果發生這種情況，請等待一個小時並重試。

	您在訂用帳戶上啟用復原服務提供者存取權之後，請藉由執行下列命令，在您的訂用帳戶中註冊提供者：

		Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

	確認提供者成功使用 `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` 和 `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery` 命令註冊

	

## 步驟 2：設定復原服務保存庫

1. 建立您將在其中建立保存庫的 ARM 資源群組，或使用現有的資源群組。您可以使用下列命令建立新的 ARM 資源群組：

		New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

	$ResourceGroupName 變數包含您想要建立的 ARM 資源群組名稱，而 $Geo 變數包含要在其中建立資源群組的 Azure 區域 (例如：巴西南部)

	您可以使用 `Get-AzureRmResourceGroup` cmdlet 在訂用帳戶中取得 ARM 資源群組的清單。

2. 建立新的 Azure 復原服務保存庫，如下所示：-

		$vault = New-AzureRmRecoveryServicesVault -Name <string> -ResouceGroupName <string> -Location <string>

	您可以使用 `Get-AzureRmRecoveryServicesVault` cmdlet 擷取現有保存庫的清單。

> [AZURE.NOTE]如果您想要使用傳統入口網站或 Azure 服務管理 PowerShell 模組在建立的 ASR 保存庫上執行作業，您可以使用 `Get-AzureRmSiteRecoveryVault` cmdlet 擷取這些保存庫的清單。建議您為所有新的作業建立新的復原服務保存庫。您稍早建立的 Site Recovery 保存庫將會繼續受到支援，但不會有最新的功能。

## 步驟 3：產生保存庫註冊金鑰

1. 產生並下載保存庫的保存庫註冊金鑰。

    	Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -Path $path
2. 匯入已下載的保存庫設定檔來設定保存庫內容
 
		Import-AzureRmSiteRecoveryVaultSettingsFile -Path $path 

## 步驟 4：建立 Hyper-V 站台，並產生網站的新保存庫註冊金鑰。

1. 建立新的 Hyper-V 站台，如下所示：
		
		$sitename = "MySite"                #Specify site friendly name
		New-AzureRmSiteRecoverySite -Name $sitename

	此 cmdlet 會啟動 ASR 作業來建立網站，並傳回 ASR 作業物件。等待作業完成，並確認作業已成功完成。

	您可以擷取 ASR 作業物件，並藉此使用 Get-AzureRmSiteRecoveryJob cmdlet 檢查目前的作業狀態 
2. 產生並下載站台的註冊金鑰：-

		$SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
		Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
	
	將下載的金鑰複製到 Hyper-V 主機。您需要金鑰向站台註冊 Hyper-V 主機

	
## 步驟 5：在 Hyper-V 主機上安裝 Azure Site Recovery 提供者和 Azure 復原服務代理程式

1. 從[這裡](https://aka.ms/downloaddra)下載最新版提供者的安裝程式
2. 在 Hyper-V 主機上執行安裝程式，然後在安裝結尾繼續註冊步驟
3. 當系統提示時提供下載的站台註冊金鑰，並完成站台的 Hyper-V 主機註冊
4. 確認 Hyper-V 主機使用下列項目向站台註冊：

		$server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname 

## 步驟 6：建立複寫原則，並將它與保護容器相關聯

1. 建立複寫原則：-

		$ReplicationFrequencyInSeconds = "300";    	#options are 30,300,900
		$PolicyName = “replicapolicy”
		$Recoverypoints = 6            		#specify the number of recovery points
		$storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id 

		$PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

	請檢查傳回的作業，以確保複寫原則建立成功。

	>[AZURE.IMPORTANT]指定的儲存體帳戶應與您的復原服務保存庫位於相同的 Azure 區域中，而且應該啟用異地複寫。
	>
	> - 如果指定的復原儲存體帳戶屬於 Azure Storage (Classic) 類型，受保護機器的容錯移轉會將機器復原到 Azure IaaS (Classic)
	> - 如果指定的復原儲存體帳戶屬於 Azure Storage (ARM) 類型，受保護機器的容錯移轉會將機器復原到 Azure IaaS (ARM)
	
 
2. 取得對應至站台的保護容器：-
		
		$protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.	啟動保護容器與複寫原則的關聯：

		$Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
		$associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

	等待關聯作業完成，並確保成功完成

##步驟 7：對虛擬機器啟用保護

1. 取得對應至您想要保護之 VM 的保護實體：-
		
		$VMFriendlyName = "Fabrikam-app"                    #Name of the VM 
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. 停止保護虛擬機器：
		
		$Ostype = "Windows"                                 # "Windows" or "Linux"
		$DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

	>[AZURE.IMPORTANT]指定的儲存體帳戶應與您的復原服務保存庫位於相同的 Azure 區域中，而且應該啟用異地複寫。
	>
	> - 如果指定的復原儲存體帳戶屬於 Azure Storage (Classic) 類型，受保護機器的容錯移轉會將機器復原到 Azure IaaS (Classic)
	> - 如果指定的復原儲存體帳戶屬於 Azure Storage (ARM) 類型，受保護機器的容錯移轉會將機器復原到 Azure IaaS (ARM)

	> 如果您要保護的 VM 有多個連接到它的磁碟，您必須使用 OSDiskName 參數指定作業系統磁碟。

3. 等待虛擬機器在初始複寫之後達到受保護的狀態。這需要一段時間，取決於要複寫的資料量、Azure 可用的上游頻寬等因素。作業狀態和 StateDescription 會在 VM 達到受保護狀態時更新，如下所示。

		
		PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

		PS C:\> $DRjob | Select-Object -ExpandProperty State
		Succeeded

		PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
		Completed

4. 更新復原屬性，例如要附加 VM 的 NIC 至容錯移轉時的 VM 角色大小和 Azure 網路。

		PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

		PS C:\> $VMFriendlyName = "Fabrikam-App"

		PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

		PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

		PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

		PS C:\> $UpdateJob


		Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
		ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
		                   s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
		Type             : Microsoft.RecoveryServices/vaults/replicationJobs
		JobType          : UpdateVmProperties
		DisplayName      : Update the virtual machine
		ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
		State            : Succeeded
		StateDescription : Completed
		StartTime        : 10-12-2015 17:55:53 +00:00
		EndTime          : 10-12-2015 17:55:54 +00:00
		TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
		TargetObjectType : ProtectionEntity
		TargetObjectName : Fabrikam-App
		AllowedActions   : {Restart}
		Tasks            : {UpdateVmPropertiesTask}
		Errors           : {}



## 步驟 8：執行測試容錯移轉

1. 執行測試容錯移轉工作：
		
		$nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group
		
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

		$TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. 確認已在 Azure 中建立測試 VM (在 Azure 中建立測試 VM 之後，測試容錯移轉作業已經暫停。作業會藉由清除繼續進行作業時建立的成品來完成，如下一個步驟所示)

3. 完成測試容錯移轉

    	$TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

<!---HONumber=AcomDC_1217_2015-->