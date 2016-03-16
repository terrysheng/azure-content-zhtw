## 準備進行 AKV 整合
若要使用 Azure 金鑰保存庫整合以設定 SQL Server VM，有幾項必要條件：

1.	[安裝 Azure PowerShell](#install-azure-powershell)
2.	[建立 Azure Active Directory](#create-an-azure-active-directory)
3.	[建立金鑰保存庫](#create-a-key-vault)

下列各節說明這些必要條件和您必須收集以在稍後執行 PowerShell Cmdlet 的資訊。

### 安裝 Azure PowerShell
確定您已安裝最新版本的 Azure PowerShell SDK。如需詳細資訊，請參閱[如何安裝和設定 Azure PowerShell](../articles/powershell-install-configure.md)。

### 建立 Azure Active Directory
首先，您必須在您的訂用帳戶中具有 [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD)。在許多優點中，這可讓您將金鑰保存庫的權限授與特定使用者和應用程式。

接下來，向 AAD 註冊應用程式。如此會給予您服務主體帳戶，具有您的 VM 需要的金鑰保存庫的存取權。在 Azure 金鑰保存庫文章中，您可以在[向 Azure Active Directory 註冊應用程式](../articles/key-vault/key-vault-get-started.md#register)一節中找到這些步驟，或者您可以在[此部落格文章](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx)的**取得應用程式的身分識別一節**查看具有螢幕擷取畫面的步驟。在完成這些步驟之前，請注意您需要收集此註冊期間的下列資訊，稍後當您在 SQL VM 上啟用 Azure 金鑰保存庫整合時需要該資訊。

- 新增應用程式之後，在 [設定] 索引標籤上尋找**用戶端識別碼**。![Azure Active Directory 用戶端識別碼](./media/virtual-machines-sql-server-akv-prepare/aad-client-id.png)
	
	用戶端識別碼稍後會指派給 PowerShell 指令碼中的 **$spName** (服務主體名稱) 參數，以啟用 Azure 金鑰保存庫整合。 
- 另外，當您建立您的金鑰時在這些步驟中，複製您的金鑰的密碼，如下列螢幕擷取畫面所示。這個金鑰密碼稍後會指派給 PowerShell 指令碼中的 **$spSecret** (服務主體密碼) 參數。![Azure Active Directory 密碼](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)
- 您必須授權讓這個新的用戶端識別碼擁有下列存取權限：**加密**、**解密**、**包裝金鑰**、**解除包裝金鑰**、**簽章** 和 **驗證**。做法是使用 [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx) Cmdlet。如需詳細資訊，請參閱[授權應用程式使用金鑰或密碼](../articles/key-vault/key-vault-get-started.md#authorize)。

### 建立金鑰保存庫
若要使用 Azure 金鑰保存庫來儲存您在 VM 中用於加密的金鑰，您需要金鑰保存庫的存取權。如果您尚未設定您的金鑰保存庫，請依照[開始使用 Azure 金鑰保存庫](../articles/key-vault/key-vault-get-started.md)主題中的步驟建立一個。在完成這些步驟之前，請注意有一些資訊您需要在此安裝期間收集，稍後當您在 SQL VM 上啟用 Azure 金鑰保存庫整合時需要該資訊。

當您進行「建立」金鑰保存庫步驟時，請注意傳回的 **vaultUri** 屬性，它是金鑰保存庫 URL。在該步驟提供的範例中 (如下所示)，金鑰保存庫名稱是 ContosoKeyVault，因此金鑰保存庫 URL 會是 https://contosokeyvault.vault.azure.net/。

	New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

金鑰保存庫 URL 稍後會指派給 PowerShell 指令碼中的 **$akvURL** 參數，以啟用 Azure 金鑰保存庫整合。

<!---HONumber=AcomDC_1223_2015-->