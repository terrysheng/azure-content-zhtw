<properties
    pageTitle="設定 Azure 執行身分帳戶 | Microsoft Azure"
    description="引導您在 Azure 自動化中建立、測試和舉例使用安全性主體驗證的逐步解說教學課程。"
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/31/2016"
    ms.author="magoedte"/>

# 使用 Azure 執行身分帳戶驗證 Runbook
本主題將示範如何從 Azure 入口網站使用新的執行身分帳戶功能 (也稱為服務主體) 設定自動化帳戶，以使用自動化 Runbook 存取訂用帳戶中的 Azure Resource Manager (ARM) 資源。當您在 Azure 入口網站中建立新的自動化帳戶時，依預設它會自動建立新的服務主體，並指派給訂用帳戶中的參與者角色型存取控制 (RBAC) 角色。這個帳戶可為您簡化程序，協助您快速開始建置和部署 Runbook 以支援您的自動化需求。

透過服務主體，您可以：

* 在使用 Runbook 管理 Azure ARM 資源時，提供標準化的 Azure 驗證方式
* 自動使用 Azure 警示中設定的全域 Runbook


>[AZURE.NOTE] 若要搭配自動化全域 Runbook 使用 Azure [警示整合功能](../azure-portal/insights-receive-alert-notifications.md)，您需要具備使用服務主體所設定的自動化帳戶。您可以選取已由使用者定義服務主體的自動化帳戶，或選擇建立新的自動化帳戶。



我們將示範如何從 Azure 入口網站建立自動化帳戶和使用 Azure PowerShell 以執行身分帳戶更新帳戶，以及如何在 Runbook 中使用服務主體進行驗證。

## 從 Azure 入口網站建立新的自動化帳戶
在本節中，您將執行下列步驟以從 Azure 入口網站建立新的 Azure 自動化帳戶和服務主體。

>[AZURE.NOTE] 執行這些步驟的使用者必須是訂用帳戶管理員角色的成員。

1. 以您想要管理的 Azure 訂用帳戶的服務系統管理員身分登入 Azure 入口網站。
2. 選取 [自動化帳戶]。
3. 在 [自動化帳戶] 刀鋒視窗中，按一下 [新增]。<br>![加入自動化帳戶](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. 在 [加入自動化帳戶] 刀鋒視窗的 [名稱] 方塊中，輸入新的自動化帳戶的名稱。
5. 如果您有多個訂用帳戶，請為新的自動化帳戶指定其中一個訂用帳戶，並指定新的或現有的 [資源群組] 和 Azure 資料中心的 [位置]。
6. 確認已為 [建立 Azure 執行身分帳戶] 選項選取 [是] 這個值，然後按一下 [建立] 按鈕。  

    ![加入自動化帳戶警告](media/automation-sec-configure-azure-runas-account/add-account-decline-create-runas-msg.png)

    >[AZURE.NOTE] 如果您選取選項 [否] 以選擇不要建立執行身分帳戶，則會在 [加入自動化帳戶] 刀鋒視窗中看到一則警告訊息。雖然會建立帳戶並將其指派給訂用帳戶中的 [參與者] 角色，但帳戶在訂用帳戶的目錄服務內不會有對應的驗證身分識別，因此在訂用帳戶中沒有存取資源。這將導致參考此帳戶的任何 Runbook 無法進行驗證並針對 ARM 資源執行工作。

    ![加入自動化帳戶警告](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

    >[AZURE.NOTE] 如果您在按一下 [建立] 按鈕後收到沒有使用權限的錯誤訊息，這是因為您的帳戶不是訂用帳戶管理員角色的成員。

7. 在 Azure 建立自動化帳戶時，您可以在功能表的 [通知] 底下追蹤進度。

完成時，自動化帳戶便會建立，且建立時所使用的憑證資產名為 **AzureRunAsCertificate** (有效期為一年)，而所使用的連線資產名為 **AzureRunAsConnection**。

## 使用 PowerShell 更新自動化帳戶
下列程序會使用 PowerShell 更新現有的自動化帳戶並建立服務主體。如果您建立了一個帳戶但拒絕建立執行身分帳戶，則必須進行此程序。

在繼續之前，請確認下列事項︰

1. 您已下載並安裝[適用於 Windows PowerShell 的 Azure Active Directory 模組 (64 位元版本)](http://go.microsoft.com/fwlink/p/?linkid=236297)。
2. 您已建立自動化帳戶。此帳戶會被參照做為以下指令碼所含參數 (–AutomationAccountName 和 -ApplicationDisplayName) 的值。

PowerShell 指令碼會設定下列項目︰

* Azure AD 應用程式，其可使用自我簽署憑證 (此應用程式在 Azure AD 中的服務主體帳戶) 進行驗證，並在目前的訂用帳戶中為此帳戶指派參與者角色 (您可以將此角色變更為擁有者或任何其他角色)。如需進一步資訊，請檢閱 [Azure 自動化中的角色型存取控制](../automation/automation-role-based-access-control.md)文章。  
* 指定的自動化帳戶中名為 **AzureRunAsCertificate** 的自動化憑證資產，其保有服務主體中使用的憑證。
* 指定的自動化帳戶中名為 **AzureRunAsConnection** 的自動化連線資產，其保有 applicationId、tenantId、subscriptionId 和憑證指紋。  


### 執行 PowerShell 指令碼

1. 將下列指令碼儲存到電腦。在此範例中，請將它儲存為檔案名稱 **New-AzureServicePrincipal.ps1**。  

    ```
    #Requires - RunAsAdministrator
    Param (
    [Parameter(Mandatory=$true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory=$true)]
    [String] $AutomationAccountName,
   
    [Parameter(Mandatory=$true)]
    [String] $ApplicationDisplayName,
   
    [Parameter(Mandatory=$true)]
    [String] $CertPlainPassword,
   
    [Parameter(Mandatory=$false)]
    [int] $NoOfMonthsUntilExpired = 12
    )
   
    Login-AzureRmAccount
    Import-Module AzureRM.Resources
		
    $CurrentDate = Get-Date
    $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
    $KeyId = (New-Guid).Guid
    $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")

    $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

    $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose

    $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
    $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

    $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
    $KeyCredential.StartDate = $CurrentDate
    $KeyCredential.EndDate= $EndDate
    $KeyCredential.KeyId = $KeyId
    $KeyCredential.Type = "AsymmetricX509Cert"
    $KeyCredential.Usage = "Verify"
    $KeyCredential.Value = $KeyValue

    # Use Key credentials
    $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ServicePrincipalDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

    New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
    Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

    $NewRole = $null
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose
      Sleep 5
      $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
      $Retries++;
    }

    # Get the tenant id for this subscription
    $SubscriptionInfo = Get-AzureRmSubscription
    $TenantID = $SubscriptionInfo | Select TenantId -First 1

    # Create the automation resources
    New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

    # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    $ConnectionAssetName = "AzureRunAsConnection"
    $SubscriptionId = $SubscriptionInfo | Select SubscriptionId -First 1
    Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId.SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
    ```
<br>
2. 在電腦上以提高的使用者權限從 [開始] 畫面啟動 **Windows PowerShell**。
3. 從提高權限的 PowerShell 命令列殼層，瀏覽至包含步驟 1 所建立指令碼的資料夾，並執行指令碼變更 –ResourceGroup、-AutomationAccountName、-ApplicationDisplayName 和 -CertPlainPassword 參數的值。<br> 

    ```
    .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> 
     -AutomationAccountName <NameofAutomationAccount> 
     -ApplicationDisplayName <DisplayNameofAutomationAccount> 
     -CertPlainPassword "<StrongPassword>"
    ```   
<br>

    >[AZURE.NOTE] 執行指令碼之後，您會收到向 Azure 進行驗證的提示。您必須使用屬於訂用帳戶中服務管理員的帳戶進行登入。<br> 
4. 指令碼順利完成後請繼續進行下一節，以測試並確認新的認證組態。

### 確認驗證 
接下來我們將執行一項小測試，以確認您可以使用新的服務主體成功進行驗證。如果您無法順利驗證，請返回步驟 1 再次確認先前的每個步驟是否正確。

1. 在 Azure 入口網站中，開啟先前建立的自動化帳戶。  
2. 按一下 [Runbook] 磚以開啟 Runbook 的清單。
3. 按一下 [加入 Runbook] 按鈕建立新的 Runbook，然後在 [加入 Runbook] 刀鋒視窗中選取 [建立新的 Runbook]。
4. 將 Runbook 命名為 Test-SecPrin-Runbook，然後選取 [PowerShell] 做為 [Runbook 類型]。按一下 [建立] 來建立 Runbook。
5. 在 [編輯 PowerShell Runbook] 刀鋒視窗中，將下列程式碼貼到畫布上︰<br>
  
    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID 
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```  
<br>
6. 按一下 [儲存] 來儲存 Runbook。
7. 按一下 [測試窗格] 來開啟 [測試] 刀鋒視窗。
8. 按一下 [開始] 以開始測試。
9. 隨即會建立 [Runbook 工作](automation-runbook-execution.md)，並在窗格中顯示其狀態。  
10. 作業狀態一開始會顯示為 [已排入佇列]，表示其正在等候雲端中的 Runbook 背景工作變為可用狀態。然後當背景工作宣告該工作時，狀態將變更為「正在開始」，然後 Runbook 實際開始執行時再變更為「執行中」。  
11. Runbook 工作完成時，會顯示其輸出。在本例中，我們應該會看到狀態為 [完成]。<br> ![安全性主體 Runbook 測試](media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br> 
12. 關閉 [測試] 刀鋒視窗返回畫布。
13. 關閉 [編輯 PowerShell Runbook] 刀鋒視窗。
14. 關閉 [Test-SecPrin-Runbook] 刀鋒視窗。

您將會把上面用來確認是否已正確設定新帳戶的程式碼用在 PowerShell Runbook 中，以便在 Azure 自動化中進行驗證以管理 ARM 資源。當然，您也可以繼續使用目前所用的自動化帳戶進行驗證。

## 後續步驟
- 如需服務主體的詳細資訊，請參閱[應用程式物件和服務主體物件](../active-directory/active-directory-application-objects.md)。
- 如需 Azure 自動化中角色型存取控制的詳細資訊，請參閱 [Azure 自動化中的角色型存取控制](../automation/automation-role-based-access-control.md)。

<!---HONumber=AcomDC_0406_2016-->