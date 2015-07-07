<properties
	pageTitle="Azure 備份 - 使用 Windows PowerShell 部署和管理 DPM 的備份 | Microsoft Azure"
	description="了解如何使用 Windows PowerShell 部署和管理 Data Protection Manager (DPM) 的 Azure 備份"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="jimpark"/>


# 使用 Windows PowerShell 部署和管理 Data Protection Manager (DPM) 伺服器的 Azure 備份
本文說明如何使用 Windows PowerShell® 命令列介面，設定 DPM 伺服器上的 Azure 備份以及管理備份和復原。

## 設定 Windows PowerShell 環境
您必須擁有正確的 Windows PowerShell 版本和環境變數設定，才能使用 Windows PowerShell 管理 Azure 備份。

確認您有 Windows PowerShell 3.0 或 4.0 版。若要尋找 Windows PowerShell 版本，請在 Windows/DPM PowerShell 命令提示字元中輸入這個命令。

```
$PSVersionTable
```

您會收到下列類型的資訊：

| 名稱 | 值 |
| ---- | ----- |
| PSVersion | 3.0 |
| WSManStackVersion | 3.0 |
| SerializationVersion | 1.1.0.1 |
| CLRVersion | 4.0.30319.18444 |
| BuildVersion | 6.2.9200.16481 |
| PSCompatibleVersions | {1.0, 2.0, 3.0} |
| PSRemotingProtocolVersion | 2.2 |

確認 **PSVersion** 的值是 3.0 或 4.0。如果為否，請參閱 [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 或 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)。

## 設定和註冊
### 在 DPM 伺服器上安裝 Azure 備份代理程式
若要安裝 Azure 備份代理程式，您必須在 Windows Server 上下載並提供安裝程式。從 [Microsoft 下載中心](aka.ms/azurebackup_agent)可以取得最新版的安裝程式。請將安裝程式儲存至容易存取的位置，例如 *C:*。

若要安裝代理程式，請在已提升權限的 DPM Windows PowerShell 主控台中執行下列命令：

```
PS C:> MARSAgentInstaller.exe /q
```

此命令會以所有預設選項安裝代理程式。安裝作業會在背景中進行幾分鐘。如果您沒有指定 */nu* 選項，則安裝結束時會開啟 Windows Update 視窗以檢查是否有任何更新。

代理程式會顯示在已安裝的程式清單中。若要查看已安裝的程式清單，請移至 [**控制台**] > [**程式**] > [**程式和功能**]。

![已安裝代理程式](./media/backup-dpm-automation/installed-agent-listing.png)

#### 安裝選項
若要查看所有可透過命令列執行的選項，請使用下列命令：

```
PS C:> MARSAgentInstaller.exe /?
```

可用的選項包括：

| 選項 | 詳細資料 | 預設值 |
| ---- | ----- | ----- |
| /q | 無訊息安裝 | - | | /p:"location" | Azure 備份代理程式的安裝資料夾路徑。 | C:\Program Files\Microsoft Azure Recovery Services Agent | | /s:"location" | Azure 備份代理程式的快取資料夾路徑。 | C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch | | /m | 選擇加入 Microsoft Update | - | | /nu | 安裝完成後不要檢查更新 | - | | /d | 解除安裝 Microsoft Azure 復原服務代理程式 | - | | /ph | Proxy 主機位址 | - | | /po | Proxy 主機連接埠號碼 | - | | /pu | Proxy 主機使用者名稱 | - | | /pw | Proxy 密碼 | - |

### 向 Azure 備份服務進行註冊
在向 Azure 備份服務進行註冊之前，請先確定您已符合下列必要條件： - 擁有有效的 Azure 訂用帳戶 - 建立備份保存庫 - 下載保存庫認證並將它們儲存在方便的位置 (像是 *C:\Downloads*)。為方便起見，您也可以重新命名保存庫認證。

使用 [Start-DPMCloudRegistration](https://technet.microsoft.com/library/jj612787) Cmdlet 即可向保存庫註冊電腦：

```
PS C:> Start-DPMCloudRegistration -DPMServerName "TestingServer" -VaultCredentialsFilePath "C:\DPMTESTVault_Friday, September 5, 2014.VaultCredentials"
```

此命令會使用位於 *C:* 的保存庫認證向 Microsoft Azure 保存庫註冊名為 “TestingServer” 的 DPM 伺服器。

> [AZURE.IMPORTANT]請勿使用相對路徑來指定保存庫認證檔。您必須提供絕對路徑做為 Cmdlet 的輸入。

### 設定線上備份的 DPM 備份選項 (訂用帳戶設定)
一旦註冊線上保護的 DPM 伺服器，便會使用指定了各種備份選項的預設訂用帳戶設定來設定 DPM 伺服器。如果您想要變更訂用帳戶設定，您需要取得現有設定，然後根據需求進行修改。

#### 取得 DPM 伺服器的現有訂用帳戶設定
若要設定 DPM 線上訂用帳戶設定，您必須先使用 [Get-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612793.aspx) Cmdlet 取得現有設定：

```
$Setting = Get-DPMCloudSubscriptionSetting -DPMServerName "TestingServer"
```

您必須執行此命令以取得可用來設定各種選項的訂用帳戶設定物件，然後在最後儲存設定。對新的 DPM 伺服器執行此命令會傳回預設設定。

#### 修改訂用帳戶設定
您可以使用 [Set-DPMCloudSubscriptionSetting](https://technet.microsoft.com/library/jj612791) Cmdlet，為 DPM 線上備份訂用帳戶設定下列設定：

##### 臨時區域
在 DPM 伺服器上執行的 Azure 備份代理程式需要暫存儲存體，以供存放從雲端還原的資料 (本機臨時區域)。您可以使用下列命令來設定臨時區域。這會將 **$setting** 的臨時區域設為 “C:\StagingArea”，但組態尚未套用至 DPM 伺服器，除非您認可設定。

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -StagingAreaPath "C:\StagingArea"
```

> [AZURE.NOTE]請確定在上述命令中指定的資料夾已存在。否則您會在儲存訂用帳戶設定時收到一則錯誤訊息

##### 網路
DPM 電腦是透過 Proxy 伺服器連線到網際網路。您可以將 Proxy 設定提供給代理程式。本範例未使用 Proxy 伺服器，因此會明確地清除任何 Proxy 相關資訊。

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoProxy
```

您也可以針對給定的一組當週天數，使用 [*上班時間頻寬*] 和 [*非上班時間頻寬*] 的選項控制頻寬使用情形。本範例未設定任何節流

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -NoThrottle
```

##### 加密設定
傳送至 Azure 備份的備份資料會進行加密來保護資料的機密性。加密複雜密碼是在還原時用來解密資料的「密碼」。

> [AZURE.IMPORTANT]一旦設定，就請保管好這項資訊。

在下面的範例中，第一個命令會將字串 passphrase123456789 轉換為安全字串，並將安全字串指派給名為 $Passphrase 的變數。第二個命令會將 $Passphrase 中的安全字串設定為用來加密備份的密碼

```
PS C:> $Passphrase = ConvertTo-SecureString -string "passphrase123456789" -AsPlainText -Force
```

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -EncryptionPassphrase $Passphrase
```

#### 儲存訂用帳戶設定
您必須使用 *–Commit* 選項認可 DPM 伺服器的變更

```
PS C:> Set-DPMCloudSubscriptionSetting -DPMServerName "TestingServer" -SubscriptionSetting $Setting -Commit
```

> [AZURE.NOTE]除非認可，否則不會套用設定。

## 保護 Azure 備份的資料
接下來的步驟將示範如何將實際執行伺服器加入至 DPM，然後保護磁碟上的資料和 Azure 的線上資料。在範例中，我們將示範如何備份檔案和資料夾。您可以輕鬆地運用同樣的觀念，來備份任何支援的資料來源。必要前提是您必須先設定和註冊線上保護的 DPM 伺服器。

所有 DPM 備份皆受到保護群組 (PG) 所控管，此群組由四個部分構成

1. **群組成員**是一份所有您想要在相同 PG 中保護的可保護物件清單。比方說，您可能想要保護某個 PG 中的實際執行 VM，然後在另一個 PG 中測試 VM，因為這些 VM 可能有不同的備份需求。
2. **資料保護方式**可用來指定您要保護哪裡的資料。在此範例中，我們會保護磁碟上的資料和線上保護 Azure 雲端的資料。
3. **備份排程**可指定何時需要進行備份，以及應該在 DPM 伺服器複本和實際執行伺服器之間同步處理資料的頻率。
4. **保留排程**可指定要在 Azure 中保留復原點多久時間。

### 在排定的間隔建立保護群組和備份資料
#### 將實際執行伺服器加入至 DPM 伺服器
若想要備份實際執行伺服器上的資料，您必須先確定 DPM 代理程式已安裝在實際執行伺服器上並受到 DPM 管理。請依照[這裡](https://technet.microsoft.com/library/bb870935.aspx)的步驟安裝 DPM 代理程式，並將它設定為要由適當的 DPM 伺服器進行備份。

#### 建立 PG
本文會將備份自動化，因此我們假設還未設定任何選項。我們一開始會先使用 [New-DPMProtectionGroup](https://technet.microsoft.com/library/hh881722.aspx) Cmdlet 建立新的 PG。

```
PS C:> $PG = New-DPMProtectionGroup -DPMServerName " TestingServer " -Name "ProtectGroup01"
```

在對 PG 進行任何變更之前，我們必須使用 [Get-DPMModifiableProtectionGroup](https://technet.microsoft.com/library/hh881713.aspx) Cmdlet 讓 PG 進入可修改模式。

```
PS C:> $MPG = Get-ModifiableProtectionGroup $PG
```

以上 Cmdlet 會建立名為 “ProtectGroup01” 的 PG，但此 PG 尚未進行任何備份。您必須定義想要備份的項目、何時執行備份以及備份的儲存位置。

### 將群組成員加入至 PG
要將資料來源加入至 PG 需要執行幾個步驟

#### 取得想要備份的實際執行伺服器 (PS)
您可以使用 [Get-DPMProductionServer](https://technet.microsoft.com/library/hh881600.aspx) Cmdlet，擷取安裝了 DPM 代理程式並且此代理程式受到 DPM 伺服器管理的所有 PS。在此範例中，我們將篩選並只設定名稱為 "productionserver01" 的 PS 來進行備份。

```
PS C:> $PS = Get-ProductionServer -DPMServerName "TestingServer" |where {($_.servername) –contains “productionserver01”
```

### 執行查詢以取得 PS 上的資料來源清單
您可以在 PS 電腦上使用 [Get-DPMDatasource](https://technet.microsoft.com/library/hh881605.aspx) Cmdlet 執行查詢，取得一份 DPM 可以備份之所有資料來源的清單。在此範例中，您可以在我們想要設定保護的 PS 上篩選資料來源 “D:\”。

```
PS C:> $DS = Get-Datasource -ProductionServer $PS -Inquire | where { $_.Name -contains “D:\” }
```

#### 新增保護的資料來源
您可以使用 [Add-DPMChildDatasource](https://technet.microsoft.com/library/hh881732.aspx) Cmdlet，將資料來源加入至我們在上面的範例中建立的 PG "ProtectGroup01"

```
PS C:> Add-DPMChildDatasource -ProtectionGroup $MPG -ChildDatasource $DS
```

### 選取資料保護方式
一旦您將資料來源加入至 PG，就必須指定保護方式。在此案例中，我們設定 PG 來對磁碟提供短期保護，而對磁帶提供長期保護。

```
PS C:> Set-DPMProtectionType -ProtectionGroup $PG -ShortTerm Disk –LongTerm Online
```

### 指定復原點目標
#### 設定保留範圍
您可以使用 [Set-DPMPolicyObjective](https://technet.microsoft.com/library/hh881762.aspx) 設定 PG 的保留範圍。以下命令會對磁碟型復原點設定 *retentionrange* 和 *synchronization frequency*。

```
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -RetentionRangeInDays 10 -SynchronizationFrequency 360
```

針對線上復原點，您可以設定各種 GFS 配置的保留範圍 (即每日、每週、每月和每年)。在此範例中，我們使用各種保留範圍建立物件，然後使用如下列範例所示的物件設定線上保留範圍。

```
PS C:> $RRlist = @()
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 180, Days)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 104, Weeks)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 60, Month)
PS C:> $RRList += (New-Object -TypeName Microsoft.Internal.EnterpriseStorage.Dls.UI.ObjectModel.OMCommon.RetentionRange -ArgumentList 10, Years)
PS C:> Set-DPMPolicyObjective –ProtectionGroup $MPG -OnlineRetentionRangeList $RRlist
```

#### 設定備份排程
如果您使用 **Set-PolicyObjective** Cmdlet 指定保護目標，DPM 會自動設定預設排程。若要變更預設排程，請依序使用 [Get-DPMPolicySchedule](https://technet.microsoft.com/library/hh881749.aspx) Cmdlet 和 [Set-DPMPolicySchedule](https://technet.microsoft.com/library/hh881723.aspx) Cmdlet。

```
PS C:> $onlineSch=Get-DPMPolicySchedule -ProtectionGroup $mpg -LongTermOnline
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[0] -TimesOfDay 02:00
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[1] -TimesOfDay 02:00 -DaysOfWeek Sa,Su –Interval 1
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[2] -TimesOfDay 02:00 -RelativeIntervals First,Third –DaysOfWeek Sa
PS C:> Set-DPMPolicySchedule -ProtectionGroup $Mpg -Schedule $onlineSch[3] -TimesOfDay 02:00 -DaysOfMonth 2,5,8,9 -Months Jan,Jul
```

#### 建立複本
如果您是第一次備份資料來源，DPM 就需要建立初始複本，以建立要在 DPM 複本磁碟區上保護的資料來源複本。您可以使用 [Set-DPMReplicaCreationMethod](https://technet.microsoft.com/library/hh881715.aspx) Cmdlet，排程在特定時間自動建立複本，或是透過手動方式建立。

```
Set-DPMReplicaCreationMethod -ProtectionGroup $MPG -NOW
```

#### 認可 PG
最後，您必須認可變更，然後 DPM 才能使用 [Set-DPMProtectionGroup](https://technet.microsoft.com/library/hh881758.aspx) Cmdlet 根據 PG 組態進行備份。

```
PS C:> Set-DPMProtectionGroup -ProtectionGroup $MPG
```

## 取得所有線上復原點的清單
您可以使用 [Get-DPMRecoveryPoint](https://technet.microsoft.com/library/hh881746.aspx) Cmdlet 來取得資料來源所有復原點的清單。在此範例中，我們： - 擷取 DPM 伺服器上所有將儲存在陣列 $PG 中的 PG - 取得對應至 $PG [0] 的資料來源 - 取得資料來源的所有復原點。

```
PS C:> $PG = Get-DPMProtectionGroup –DPMServerName "TestingServer"
PS C:> $DS = Get-DPMDatasource -ProtectionGroup $PG[0]
PS C:> $RecoveryPoints = Get-DPMRecoverypoint -Datasource $DS[0] -Online
```

## 還原在 Azure 上保護的資料
在此範例中，我們將示範如何從線上復原點還原 HYPER-V VM，但這些命令可以輕易地延伸運用在任何資料來源類型。

您必須先使用 [New-DPMRecoveryOption](https://technet.microsoft.com/library/hh881592.aspx) Cmdlet 建立復原選項。在下列範例中，我們會將 Hyper-V 資料來源復原到替代位置。

```
PS C:> $RecoveryOption = New-DPMRecoveryOption -HyperVDatasource -TargetServer "HVDCenter02" -RecoveryLocation AlternateHyperVServer -RecoveryType Recover -TargetLocation “c:\VMRecovery”
```

若您已設定復原選項，您將會還原在上面的[取得所有線上復原點的清單](#Getting-a-list-of-all-Online-Recovery-Points)章節中擷取的線上復原點。

```
PS C:> Restore-DPMRecoverableItem -RecoverableItem $RecoveryPoints -RecoveryOption $RecoveryOption
```
## 後續步驟
如需 DPM 的 Azure 備份詳細資訊，請參閱 [Azure DPM 備份簡介](backup-azure-dpm-introduction.md)

<!---HONumber=62-->