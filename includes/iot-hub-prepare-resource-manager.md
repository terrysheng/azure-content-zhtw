## 準備驗證資源管理員要求

您必須使用 [Azure 資源管理員][lnk-authenticate-arm]搭配 Azure Active Directory (AD) 來驗證所有針對資源執行的作業。最簡單的設定方式是使用 PowerShell 或 Azure CLI。

繼續之前，您應該安裝 [Azure PowerShell 1.0][lnk-powershell-install] 或更新版本。

下列步驟示範如何使用 PowerShell 設定 AD 應用程式的密碼驗證。您可以在標準 PowerShell 工作階段中執行這些命令。

1. 使用下列命令來登入您的 Azure 訂用帳戶：

    ```
    Login-AzureRmAccount
    ```

2. 記下的您 **TenantId** 和 **SubscriptionId**。稍後您將需要這些資訊。

3. 使用下列命令並取代預留位置，以建立新的 Azure Active Directory 應用程式：

    - **{Display name}：**應用程式的顯示名稱，如 **MySampleApp**
    - **{Home page URL}：**應用程式首頁的 URL，如 ****http://mysampleapp/home**。此 URL 不需要指向實際的應用程式。
- **{Application identifier}：**唯一識別碼，如 ****http://mysampleapp**。此 URL 不需要指向實際的應用程式。
- **{Password}：**用來驗證應用程式的密碼。

    ```
    New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
    ```
    
4. 請記下建立之應用程式的 **ApplicationId**。稍後您將會需要此資訊。

5. 使用下列命令，並將 **{MyApplicationId}** 取代為上一個步驟的 **ApplicationId**，藉此建立新的服務主體：

    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
    
6. 使用下列命令，並將 **{MyApplicationId}** 取代為 **ApplicationId**，藉此設定角色指派。

    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```
    
您已建立可從自訂 C# 應用程式驗證的 Azure AD 應用程式。在本教學課程後續的內容當中，您將需要以下各值：

- TenantId
- SubscriptionId
- ApplicationId
- 密碼

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: ../powershell-install-configure.md

<!---HONumber=AcomDC_0218_2016-->