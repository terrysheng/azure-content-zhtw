<properties 
	pageTitle="在 Azure VM (傳統部署) 上設定 SQL Server 的 Azure 金鑰保存庫整合"
	description="了解如何自動化 SQL Server 加密的組態，以用於 Azure 金鑰保存庫。本主題說明如何搭配傳統部署模型中建立之SQL Server 虛擬機器使用 Azure 金鑰保存庫整合。" 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="12/17/2015"
	ms.author="jroth"/>

# 在 Azure VM (傳統部署) 上設定 SQL Server 的 Azure 金鑰保存庫整合

> [AZURE.SELECTOR]
- [資源管理員](virtual-machines-windows-ps-sql-keyvault.md)
- [傳統](virtual-machines-windows-classic-ps-sql-keyvault.md)

## 概觀
有多個 SQL Server 加密功能，例如[透明資料加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)、[資料行層級加密 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) 和 [備份加密](https://msdn.microsoft.com/library/dn449489.aspx)。這些形式的加密需要您管理和儲存用來加密的密碼編譯金鑰。Azure 金鑰保存庫 (AKV) 服務是設計來改善這些金鑰在安全且高度可用位置的安全性和管理。[SQL Server 連接器](http://www.microsoft.com/download/details.aspx?id=45344)讓 SQL Server 可以從 Azure 金鑰保存庫使用這些金鑰。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]資源管理員模型。

如果您使用內部部署電腦執行 SQL Server，有[您可以遵循的步驟以從您的內部部署 SQL Server 電腦存取 Azure 金鑰保存庫](https://msdn.microsoft.com/library/dn198405.aspx)。但是對於 Azure VM 中的 SQL server，您可以使用 *Azure 金鑰保存庫整合*功能來節省時間。使用一些 Azure PowerShell Cmdlet 來啟用這項功能，您可以自動化 SQL VM 存取您的金鑰保存庫所需的組態。

啟用這項功能時，它會自動安裝 SQL Server 連接器、設定 EKM 提供者來存取 Azure 金鑰保存庫，並建立認證讓您存取您的保存庫。如果您看到先前提及的內部部署文件中的步驟，您可以發現這項功能會自動執行步驟 2 和 3。您唯一仍然需要手動進行的是建立金鑰保存庫和金鑰。從那裡開始，會自動化 SQL VM 的整個設定。這項功能完成此設定之後，您可以執行 T-SQL 陳述式以開始如往常一般加密您的資料庫或備份。

[AZURE.INCLUDE [準備 AKV 整合](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## 設定 AKV 整合
使用 PowerShell 設定 Azure 金鑰保存庫整合。以下章節提供必要參數的概觀，以及範例 PowerShell 指令碼。

### 輸入參數
下表列出在下一節中執行 PowerShell 指令碼所需的參數。

|參數|說明|範例|
|---|---|---|
|**$akvURL**|**金鑰保存庫 URL**|"https://contosokeyvault.vault.azure.net/"|
|**$spName**|**服務主體名稱**|"fde2b411-33d5-4e11-af04eb07b669ccf2"|
|**$spSecret**|**服務主體密碼**|"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="|
|**$credName**|**認證名稱**：AKV 整合會在 SQL Server 內建立認證，允許 VM 具有金鑰保存庫的存取權。選擇此認證的名稱。|"mycred1"|
|**$vmName**|**虛擬機器名稱**：先前建立之 SQL VM 的名稱。|"myvmname"|
|**$serviceName**|**服務名稱**：與 SQL VM 相關聯的雲端服務名稱。|"mycloudservicename"|

### 使用 PowerShell 啟用 AKV 整合
**New-AzureVMSqlServerKeyVaultCredentialConfig** Cmdlet 會建立 Azure 金鑰保存庫整合功能的組態物件。**Set-AzureVMSqlServerExtension** 會以 **KeyVaultCredentialSettings** 參數設定此整合。下列步驟說明如何使用這些命令。

1. 在 Azure PowerShell 中，首先以您的特定值設定輸入參數，如本本主題前面幾節所述。下列指令碼是範例。
	
		$akvURL = "https://contosokeyvault.vault.azure.net/"
		$spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
		$spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
		$credName = "mycred1"
		$vmName = "myvmname"
		$serviceName = "mycloudservicename"
2.	然後使用下列指令碼來設定和啟用 AKV 整合。
	
		$secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
		$akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
		Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

SQL IaaS 代理程式延伸會使用這個新的組態更新 SQL VM。

[AZURE.INCLUDE [AKV 整合後續步驟](../../includes/virtual-machines-sql-server-akv-next-steps.md)]

<!---HONumber=AcomDC_0323_2016-->