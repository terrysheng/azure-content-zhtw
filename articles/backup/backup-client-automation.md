<properties
	pageTitle="使用 Windows PowerShell 部署和管理 Windows Server/用戶端的備份 | Microsoft Azure"
	description="了解如何使用 Windows PowerShell 部署和管理 Azure 備份"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="aashishr"/>


# 使用 Windows PowerShell 部署和管理 Windows Server/Windows 用戶端的 Azure 備份
本文說明如何使用 Windows PowerShell® 命令列介面，設定 Windows Server 或 Windows 用戶端上的 Azure 備份以及管理備份和還原。

## 設定 Windows PowerShell 環境
您必須擁有正確的 Windows PowerShell 版本和環境變數設定，才能使用 Windows PowerShell 管理 Azure 備份。

## 設定和註冊
下列設定和註冊工作可以使用 Windows PowerShell 來自動進行 - 安裝 Azure 備份代理程式 - 向 Azure 備份服務進行註冊 - 網路

### 安裝 Azure 備份代理程式
若要安裝 Azure 備份代理程式，您必須在 Windows Server 上下載並提供安裝程式。從 [Microsoft 下載中心](aka.ms/azurebackup_agent)可以取得最新版的安裝程式。請將安裝程式儲存至容易存取的位置，例如 *C:\Downloads*。若要安裝代理程式，請在已提升權限的 Windows PowerShell 主控台中執行下列命令：

```
PS C:> MARSAgentInstaller.exe /q
```

此命令會以所有預設選項安裝代理程式。安裝作業會在背景中進行幾分鐘。如果您沒有指定 **/nu** 選項，則安裝結束時會開啟 **Windows Update** 視窗以檢查是否有任何更新。

代理程式會顯示在已安裝的程式清單中。若要查看已安裝的程式清單，請移至 [**控制台**] > [**程式**] > [**程式和功能**]。

![已安裝代理程式](./media/backup-client-automation/installed-agent-listing.png)

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
在向 Azure 備份服務進行註冊之前，請先確定您已符合[必要條件](backup-try-azure-backup-in-10-mins.md)： - 擁有有效的 Azure 訂用帳戶 - 建立備份保存庫 - 下載保存庫認證並將它儲存在方便的位置 (像是 *C:\Downloads*)。為方便起見，您也可以重新命名保存庫認證。

使用 [Start-OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) Cmdlet 即可向保存庫註冊電腦：

```
PS C:> Start-OBRegistration -VaultCredentials "C:\Downloads\register.vaultcredentials" -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : Australia East

Machine registration succeeded.
```

> [AZURE.IMPORTANT]請勿使用相對路徑來指定保存庫認證檔。您必須提供絕對路徑做為 Cmdlet 的輸入。


### 網路
若 Windows 電腦是透過 Proxy 伺服器連線到網際網路，您也可以提供 Proxy 設定給代理程式。本範例未使用 Proxy 伺服器，因此會明確地清除任何 Proxy 相關資訊。

您也可以針對給定的一組當週天數，使用 [*上班時間頻寬*] 和 [*非上班時間頻寬*] 的選項控制頻寬使用情形。

使用 [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) Cmdlet 即可設定 Proxy 和頻寬的詳細資料：

```
PS C:> Set-OBMachineSetting -NoProxy
Server properties updated successfully.
```

```
PS C:> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

### 加密設定
傳送至 Azure 備份的備份資料會進行加密來保護資料的機密性。加密複雜密碼是在還原時用來解密資料的「密碼」。一旦設定，就請務必保管好這項資訊。

```
PS C:> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

## 解除安裝 Azure 備份代理程式
使用下列命令即可解除安裝 Azure 備份代理程式：

```
PS C:> .\MARSAgentInstaller.exe /d /q
```

解除安裝電腦上的代理程式二進位檔會有其他後果： - 它會移除電腦中的檔案篩選器，並停止追蹤變更。 - 電腦中的所有原則資訊會遭到移除，但服務中會繼續儲存著原則資訊。 - 所有備份排程會遭到移除，不會再進行任何備份。

不過，Azure 中儲存的資料會繼續留著，根據您所設定的保留原則設定予以保留。較舊的時間點會自動過時。

## 遠端管理
關於 Azure 備份代理程式、原則和資料來源的所有管理工作，皆可透過 Windows PowerShell 遠端完成。要遠端管理的電腦必須經過正確準備。

依預設，WinRM 服務會設定為手動啟動。但您必須將啟動類型設定為 [*自動*]，並應該啟動該服務。若要驗證 WinRM 服務有在執行，[狀態] 屬性的值應該是 [*執行中*]。

```
PS C:> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...

- Windows PowerShell should be configured for remoting.
```

```
PS C:> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:> Set-ExecutionPolicy unrestricted -force
```

現在可以遠端管理電腦 - 從代理程式的安裝開始。例如，下列指令碼會將代理程式複製到遠端電腦並進行安裝。

```
PS C:> $dloc = "\REMOTESERVER01\c$\Windows\Temp"
PS C:> $agent = "\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:> $args = "/q"
PS C:> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```
## 後續步驟
如需 Windows Server/用戶端的 Azure 備份詳細資訊，請參閱 [Azure 備份的簡介](backup-introduction-to-azure-backup.md)

<!---HONumber=62-->