### (選用) 設定您的 .NET 行動服務以用於 AAD 登入

> [WACOM.NOTE] 這些都是選用步驟，因為它們只適用於 Azure Active Directory 登入提供者。

1.  安裝 **WindowsAzure.MobileServices.Backend.Security** NuGet 封裝。

2.  在 Visual Studio 中，展開 App\_Start，然後開啟 WebApiConfig.cs 檔案。將 `options` 具現化之後，立即將下列程式碼新增至 `Register` 方法：

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));


