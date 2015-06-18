<properties 
	pageTitle="使用 PowerShell 管理 Azure SQL Database 資源" 
	description="使用命令列管理 Azure SQL Database" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="vinsonyu"/>

# 使用 PowerShell 管理 Azure SQL Database 資源


在本主題中，您可以使用 PowerShell 指令碼來建立 Azure SQL Database 邏輯伺服器、資料庫和防火牆規則。

## 步驟 1：安裝 Azure SDK

如果您尚未這樣做，請使用[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md) 中的指示在本機電腦上安裝 Azure PowerShell。然後，開啟 Azure PowerShell 命令提示字元。


## 步驟 2：設定 PowerShell 指令碼
這個 PowerShell 指令碼會建立伺服器、資料庫和伺服器防火牆規則。


1. 將下列 PowerShell Cmdlet 區塊複製到您的文字編輯器中。
		
		
		Add-AzureAccount #Only needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "<Service Admin Login>" -AdministratorLoginPassword "<ServerLoginPassword>" -Location "<Location>" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "<Database1>" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "<FirewallRuleName>" -StartIpAddress "<IP4StartRange>" -EndIpAddress "<IP4EndRange>" -verbose

2. 以所需的值取代 < > 內的全部內容。如需有效的 Azure SQL Database 伺服器位置清單，您可以在 Azure PowerShell 命令提示字元中執行下列 Cmdlet。

		Switch-AzureMode -Name AzureResourceManager
		$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
		$AzureSQLLocations.Locations

##步驟 3：執行 PowerShell 指令碼

檢閱您放在一起的 Azure PowerShell Cmdlet。

複製在步驟 2 中設定的 PowerShell Cmdlet，然後貼到 Azure PowerShell 命令提示字元中。這會以一系列的 PowerShell 命令發出 Cmdlet，並建立您的 Azure SQL Server、資料庫和伺服器防火牆規則。

如果您將再次建立這些 Azure SQL 資源或類似的資源，您可以：

- 將這個命令集儲存為 PowerShell 指令碼檔案 (*.ps1)
- 在 Azure 管理入口網站的 [自動化] 區段中，將這個命令集儲存為 Azure 自動化 Runbook 

##範例

這個 PowerShell 指令碼將建立美國西部的資源

		Add-AzureAccount #Needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "admin" -AdministratorLoginPassword "P@ssword" -Location "West US" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "Database1" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "MyFirewallRule" -StartIpAddress "192.168.1.1" -EndIpAddress "192.168.1.1" -verbose

##資源

如需 Azure SQL PowerShell Cmdlet 的詳細資訊，請[按一下這裡](https://msdn.microsoft.com/library/dn546726.aspx)

<!---HONumber=58--> 