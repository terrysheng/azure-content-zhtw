## 取得資源管理員權杖

Azure Active Directory 必須驗證您在使用 Azure 資源管理員的資源上執行的所有工作。此處顯示的範例使用密碼驗證，如需其他方法請參閱[驗證 Azure 資源管理員要求][lnk-authenticate-arm]。

1. 將下列程式碼加入 Program.cs 中的 **Main** 方法，以使用應用程式識別碼和密碼從 Azure AD 擷取權杖。

    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
    
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```

2. 將下列程式碼加入 **Main** 方法的結尾，建立使用該權杖的 **ResourceManagementClient** 物件：

    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```

3. 建立或取得您正在使用的資源群組參照：

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx

<!---HONumber=AcomDC_0218_2016-->