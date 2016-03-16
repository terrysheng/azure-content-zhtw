<properties 
pageTitle="使用 PowerShell 啟用 Azure 雲端服務中角色的遠端桌面連線" 
description="如何使用 PowerShell 設定的 Azure 雲端服務應用程式以允許遠端桌面連線" 
services="cloud-services" 
documentationCenter="" 
authors="sbtron" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="01/19/2016" 
ms.author="saurabh"/>

# 使用 PowerShell 啟用 Azure 雲端服務中角色的遠端桌面連線

>[AZURE.SELECTOR]
- [Azure classic portal](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


遠端桌面可讓您存取 Azure 內執行中角色的桌面。您可以使用遠端桌面連線來疑難排解和診斷執行中應用程式的問題。

這篇文章描述如何使用 PowerShell 在雲端服務角色上啟用遠端桌面。如需這篇文章所需要的必要條件，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。PowerShell 會使用遠端桌面延伸模組方法，因此即使在應用程式部署之後，您也可以啟用遠端桌面。


## 從 PowerShell 設定遠端桌面

[Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) Cmdlet 可讓您在雲端服務部署的指定角色或所有角色上啟用遠端桌面。此 Cmdlet 可讓您透過可接受 PSCredential 物件的 *Credential* 參數，指定遠端桌面使用者的使用者名稱和密碼。

如果您以互動方式使用 PowerShell，您可以呼叫 [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) Cmdlet，輕鬆地設定 PSCredential 物件。

```
	$remoteusercredentials = Get-Credential
```

這會顯示對話方塊，可讓您以安全的方式輸入遠端使用者的使用者名稱和密碼。

由於 PowerShell 幾乎都用於自動化案例，您也可以用不需要使用者互動的方式設定 PSCredential 物件。若要這樣做，您必須先設定安全的密碼。首先，指定純文字密碼，然後使用 [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx) 轉換成安全字串。接下來，您需要使用 [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx)，將這個安全字串轉換成加密的標準字串。現在，您可以使用 [Set-Content](https://technet.microsoft.com/library/ee176959.aspx)，將此加密的標準字串儲存到檔案。建立 PSCredential 物件時，您可以讀取此檔案，以安全的方式設定密碼，而不需要在提示時指定密碼或將密碼儲存為純文字。

使用下列 PowerShell 來建立安全的密碼檔案：

```
	ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
``` 

一旦建立密碼檔案 (password.txt)，您只需要使用此檔案，不需要以純文字指定密碼。如果您需要更新密碼，您可以用新密碼再次執行上述 PowerShell，以產生新的 password.txt 檔案。

>[AZURE.IMPORTANT] 設定密碼設定時，請確定您符合[複雜性需求](https://technet.microsoft.com/library/cc786468.aspx)。

若要從安全的密碼檔案建立認證物件，您必須讀取檔案內容，並使用 [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx) 將它們轉換回安全字串。除了認證，[Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) Cmdlet 也接受 *Expiration* 參數，它可指定使用者帳戶到期的日期時間。您可以指定靜態的日期和時間，或直接選擇讓帳戶在目前的日期幾天之後到期。

這個 PowerShell 範例示範如何在雲端服務上設定遠端桌面延伸模組：

```
	$servicename = "cloudservice"
	$username = "RemoteDesktopUser"
	$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
	$expiry = $(Get-Date).AddDays(1)
	$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
	Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry 
```
您可以選擇性地指定部署位置和您想要啟用遠端桌面的角色。如果未指定這些參數，此 Cmdlet 預設會使用「生產」部署位置，並在生產部署中的所有角色上啟用遠端桌面。

遠端桌面延伸模組與部署相關聯。如果您已經為服務建立新的部署，則必須在新的部署上再次啟用遠端桌面。如果您想要在您的部署上一律啟用遠端桌面，應該考慮將啟用遠端桌面的 PowerShell 指令碼整合至您的部署工作流程中。


## 從遠端桌面連接到角色執行個體
[Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) Cmdlet 可用於從遠端桌面連接到雲端服務的特定角色執行個體。您可以在此 Cmdlet 上使用 *LocalPath* 參數，將 RDP 檔案下載到本機，也可以使用 *Launch* 參數，直接啟動 [遠端桌面連線] 對話方塊來存取雲端服務角色執行個體。

```
	Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## 檢查服務上是否已啟用遠端桌面延伸模組 
[Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) Cmdlet 會顯示服務部署上是否已啟用遠端桌面。此 Cmdlet 會傳回遠端桌面使用者的使用者名稱，以及已啟用遠端桌面延伸模組的角色。您可以選擇性地指定部署位置並以生產為預設值。

```
	Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## 從服務移除遠端桌面延伸模組 
如果您已在部署上啟用遠端桌面延伸模組，且需要更新遠端桌面設定，則必須先移除遠端桌面延伸模組，然後以新的設定重新啟用。例如，如果您想要設定遠端使用者帳戶的新密碼，或如果使用者帳戶已過期，則您需要移除延伸模組，然後以新密碼或到期時間再次新增。只有在已啟用遠端桌面延伸模組的現有部署上，才需要這樣做。對於您可呼叫的新部署，直接套用延伸模組即可。

若要從服務部署移除遠端桌面延伸模組，您可以使用 [Remove-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx) Cmdlet。您也可以選擇性地指定您想要移除遠端桌面延伸模組的部署位置和角色。

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration

```  

>[AZURE.NOTE] *UninstallConfiguration* 參數將會解除安裝任何已套用到服務的延伸模組組態。所有的延伸模組組態都與服務組態相關聯，才會在部署上啟動延伸模組。部署必須與該延伸模組組態相關聯。如果呼叫 Remove Cmdlet 時未指定 *UninstallConfiguration*，將會取消部署與延伸模組組態的關聯，因而實際上會從部署移除延伸模組。不過，延伸模組組態仍然與服務相關聯。若要完全移除延伸模組組態，您應該在呼叫 Remove Cmdlet 時指定 *UninstallConfiguration* 參數。



## 其他資源

[如何設定雲端服務](cloud-services-how-to-configure.md)

<!---HONumber=AcomDC_0128_2016-->