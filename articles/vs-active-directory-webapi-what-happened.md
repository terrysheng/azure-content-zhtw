<properties title="開始使用 Active Directory 驗證" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [開始使用][開始使用]
> -   [發生什麼情形][發生什麼情形]

### <span id="whathappened">我的專案發生什麼情形？</span>

已加入參考。

##### NuGet 封裝參考

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### .NET 參考

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### 程式碼檔案加入至專案

驗證啟動類別 **App\_Start/Startup.Auth.cs** 加入至專案，內含 Azure AD 驗證的啟動邏輯。

##### 啟動程式碼加入至專案

如果專案中已有啟動類別，則已更新 **Configuration** 方法來包含 `ConfigureAuth(app)` 的呼叫 (加入至該方法)。否則將啟動類別加入至專案。

##### app.config 或 web.config 檔案有新的組態值。

已加入下列組態項目。

     <appSettings>              <add key="ida:ClientId" value="ClientId from the new Azure AD App" />              <add key="ida:Tenant" value="Your selected Azure AD Tenant" />              <add key="ida:Audience" value="The App ID Uri from the wizard" />      </appSettings> 

</p>
### 已建立 Azure Azure AD App

在您於精靈中選取的目錄中建立 Azure AD 應用程式。

[深入了解 Azure Active Directory][深入了解 Azure Active Directory]

  [開始使用]: /documentation/articles/vs-active-directory-webapi-getting-started/
  [發生什麼情形]: /documentation/articles/vs-active-directory-webapi-what-happened/
  [深入了解 Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
