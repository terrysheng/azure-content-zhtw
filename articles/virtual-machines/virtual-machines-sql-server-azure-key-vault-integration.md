<properties 
	pageTitle="在 Azure VM 上設定 SQL server 的 Azure 金鑰保存庫整合"
	description="了解如何自動化 SQL Server 加密的組態，以用於 Azure 金鑰保存庫。本主題說明如何使用 Azure 金鑰保存庫整合與 SQL Server 虛擬機器。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="rothja" 
	manager="jeffreyg"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services" 
	ms.date="10/23/2015"
	ms.author="jroth"/>

# 在 Azure VM 上設定 SQL server 的 Azure 金鑰保存庫整合

## 概觀
有多個 SQL Server 加密功能，例如[透明資料加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)、[資料行層級加密 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) 和 [備份加密](https://msdn.microsoft.com/library/dn449489.aspx)。這些形式的加密需要您管理和儲存用來加密的密碼編譯金鑰。Azure 金鑰保存庫 (AKV) 服務是設計來改善這些金鑰在安全且高度可用位置的安全性和管理。[SQL Server 連接器](http://www.microsoft.com/download/details.aspx?id=45344)讓 SQL Server 可以從 Azure 金鑰保存庫使用這些金鑰。

如果您使用內部部署電腦執行 SQL Server，有[您可以遵循的步驟以從您的內部部署 SQL Server 電腦存取 Azure 金鑰保存庫](https://msdn.microsoft.com/library/dn198405.aspx)。但是對於 Azure VM 中的 SQL server，您可以使用 *Azure 金鑰保存庫整合*功能來節省時間。使用一些 Azure PowerShell Cmdlet 來啟用這項功能，您可以自動化 SQL VM 存取您的金鑰保存庫所需的組態。

啟用這項功能時，它會自動安裝 SQL Server 連接器、設定 EKM 提供者來存取 Azure 金鑰保存庫，並建立認證讓您存取您的保存庫。如果您看到先前提及的內部部署文件中的步驟，您可以發現這項功能會自動執行步驟 2 和 3。您唯一仍然需要手動進行的是建立金鑰保存庫和金鑰。從那裡開始，會自動化 SQL VM 的整個設定。這項功能完成此設定之後，您可以執行 T-SQL 陳述式以開始如往常一般加密您的資料庫或備份。

## 準備進行 AKV 整合
若要使用 Azure 金鑰保存庫整合以設定 SQL Server VM，有幾項必要條件：

1.	[安裝 Azure PowerShell](#install-azure-powershell)
2.	[建立 Azure Active Directory](#create-an-azure-active-directory)
3.	[建立金鑰保存庫](#create-a-key-vault)

下列各節說明這些必要條件和您必須收集以在稍後執行 PowerShell Cmdlet 的資訊。

### 安裝 Azure PowerShell
確定您已安裝最新版本的 Azure PowerShell SDK。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)。

### 建立 Azure Active Directory
首先，您必須在您的訂用帳戶中具有 [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD)。在許多優點中，這可讓您將金鑰保存庫的權限授與特定使用者和應用程式。

接下來，向 AAD 註冊應用程式。如此會給予您服務主體帳戶，具有您的 VM 需要的金鑰保存庫的存取權。在 Azure 金鑰保存庫文章中，您可以在[向 Azure Active Directory 註冊應用程式](../key-vault/key-vault-get-started.md#register)一節中找到這些步驟，或者您可以在[此部落格文章](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx)的**取得應用程式的身分識別一節**查看具有螢幕擷取畫面的步驟。在完成這些步驟之前，請注意您需要收集此註冊期間的下列資訊，稍後當您在 SQL VM 上啟用 Azure 金鑰保存庫整合時需要該資訊。

- 新增應用程式之後，在 [設定] 索引標籤上尋找**用戶端識別碼**。![Azure Active Directory 用戶端識別碼](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-client-id.png)
	
	用戶端識別碼稍後會指派給 PowerShell 指令碼中的 **$spName** (服務主體名稱) 參數，以啟用 Azure 金鑰保存庫整合。 
- 另外，當您建立您的金鑰時在這些步驟中，複製您的金鑰的密碼，如下列螢幕擷取畫面所示。這個金鑰密碼稍後會指派給 PowerShell 指令碼中的 **$spSecret** (服務主體密碼) 參數。![Azure Active Directory 密碼](./media/virtual-machines-sql-server-azure-key-vault-integration/aad-sp-secret.png)
- 您必須授權這個新的用戶端識別碼有下列存取權限：**加密**、**解密**、**包裝金鑰**、**解除包裝金鑰**、**簽章** 和 **驗證**。做法是使用 [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607%28v=azure.98%29.aspx) Cmdlet。如需詳細資訊，請參閱[授權應用程式使用金鑰或密碼](../key-vault/key-vault-get-started.md#authorize)。

### 建立金鑰保存庫
若要使用 Azure 金鑰保存庫來儲存您在 VM 中用於加密的金鑰，您需要金鑰保存庫的存取權。如果您尚未設定您的金鑰保存庫，請依照[開始使用 Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)主題中的步驟建立一個。在完成這些步驟之前，請注意有一些資訊您需要在此安裝期間收集，稍後當您在 SQL VM 上啟用 Azure 金鑰保存庫整合時需要該資訊。

當您進行「建立」金鑰保存庫步驟時，請注意傳回的 **vaultUri** 屬性，它是金鑰保存庫 URL。在該步驟提供的範例中，如下所示，金鑰保存庫名稱是 ContosoKeyVault，因此金鑰保存庫 URL 會是 https://contosokeyvault.vault.azure.net/。

![Azure Active Directory 密碼](./media/virtual-machines-sql-server-azure-key-vault-integration/new-azurekeyvault.png)
 
金鑰保存庫 URL 稍後會指派給 PowerShell 指令碼中的 **$akvURL** 參數，以啟用 Azure 金鑰保存庫整合。

## 設定 AKV 整合
使用 PowerShell 以設定 Azure 金鑰保存庫整合。以下章節提供必要參數的概觀，以及範例 PowerShell 指令碼。

### 輸入參數
下表列出在下一節中執行 PowerShell 指令碼所需的參數。

|參數|說明|範例|
|---|---|---|
|**$akvURL**|**金鑰保存庫 URL**|"https://contosokeyvault.vault.azure.net/"|
|**$spName**|**服務主體名稱**|"fde2b411-33d5-4e11-af04eb07b669ccf2"|
|**$spSecret**|**服務主體密碼**|"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="|
|**$credName**|**認證名稱**：AKV 整合會在 SQL Server 內建立認證，允許 VM 具有金鑰保存庫的存取權。選擇此認證的名稱。|"mycred1"|
|**$vmName**|**虛擬機器名稱**：先前建立的 SQL VM 的名稱。|"myvmname"|
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
		Get-AzureVM –ServiceName $serviceName –Name $vmName | Set-AzureVMSqlServerExtension –KeyVaultCredentialSettings $akvs | Update-AzureVM

SQL IaaS 代理程式延伸會使用這個新的組態更新 SQL VM。

## 後續步驟
啟用 Azure 金鑰保存庫整合之後，您可以在您的 SQL VM 上啟用 SQL Server 加密。首先，您必須在金鑰保存庫內建立非對稱金鑰，以及在 VM 上的 SQL Server 內建立對稱金鑰。然後，您可以執行 T-SQL 陳述式來啟用您的資料庫和備份的加密。

有數種形式的加密可供您利用：

- [透明資料加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
- [加密的備份](https://msdn.microsoft.com/library/dn449489.aspx)
- [資料行層級加密 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

下列 Transact-SQL 指令碼為每個區域提供範例。

>[AZURE.NOTE]每個範例是根據兩個必要條件：您的金鑰保存庫的非對稱金鑰(稱為 **CONTOSO\_KEY**)，和 AKV 整合功能所建立的認證 (稱為 **Azure\_EKM\_TDE\_cred**)。

### 透明資料加密 (TDE)
1. 建立 SQL Server 登入，讓資料庫引擎用於 TDE，然後新增認證。
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it loads a database 
		-- encrypted by TDE.
		CREATE LOGIN TDE_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the TDE Login to add the credential for use by the 
		-- Database Engine to access the key vault
		ALTER LOGIN TDE_Login 
		ADD CREDENTIAL Azure_EKM_TDE_cred;
		GO
	
2. 建立將用於 TDE 的資料庫加密金鑰。
	
		USE ContosoDatabase;
		GO
		
		CREATE DATABASE ENCRYPTION KEY 
		WITH ALGORITHM = AES_128 
		ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the database to enable transparent data encryption.
		ALTER DATABASE ContosoDatabase 
		SET ENCRYPTION ON;
		GO

### 加密的備份
1. 建立 SQL Server 登入，讓資料庫引擎用於加密備份，然後新增認證。
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it is encrypting the backup.
		CREATE LOGIN Backup_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO 
		
		-- Alter the Encrypted Backup Login to add the credential for use by 
		-- the Database Engine to access the key vault
		ALTER LOGIN Backup_Login 
		ADD CREDENTIAL Azure_EKM_Backup_cred ;
		GO
	
2. 備份資料庫會指定加密，具有儲存在金鑰保存庫中的非對稱金鑰。
	
		USE master;
		BACKUP DATABASE [DATABASE_TO_BACKUP]
		TO DISK = N'[PATH TO BACKUP FILE]' 
		WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD, 
		ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
		GO

### 資料行層級加密 (CLE)
此指令碼會建立受到金鑰保存庫中非對稱金鑰保護的對稱金鑰，然後使用對稱金鑰來加密資料庫中的資料。

	CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
	WITH ALGORITHM=AES_256
	ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	DECLARE @DATA VARBINARY(MAX);
	
	--Open the symmetric key for use in this session
	OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY 
	DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	--Encrypt syntax
	SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));
	
	-- Decrypt syntax
	SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));
	
	--Close the symmetric key
	CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;

## 其他資源
如需有關如何使用這些加密功能的詳細資訊，請參閱[以 SQL Server 加密功能使用 EKM](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM)。

請注意，本文中的步驟假設您已在 Azure 虛擬機器上執行 SQL Server。如果沒有，請參閱[在 Azure 中佈建 SQL Server 虛擬機器](virtual-machines-provision-sql-server.md)。如需在 Azure VM 中執行 SQL Server 的指引，請參閱 [Azure 虛擬機器上的 SQL Server 概觀](virtual-machines-sql-server-infrastructure-services.md)。

<!---HONumber=Nov15_HO1-->