<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 發生什麼情形？

已在專案中加入參考

###### NuGet 封裝參考

Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.ActiveDirectory, Microsoft.Owin.Security.Jwt, Microsoft.Owin.Security.OAuth, Newtonsoft.Json, Owin, System.IdentityModel.Tokens.Jwt

###### .NET 參考

Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.ActiveDirectory, Microsoft.Owin.Security.Jwt, Microsoft.Owin.Security.OAuth, Newtonsoft.Json, Owin, System.IdentityModel.Tokens.Jwt

###### 程式碼檔案加入至專案

驗證啟動類別 App\_Start/Startup.Auth.cs 加入至專案，內含 Azure AD 驗證的啟動邏輯。

###### 啟動程式碼加入至專案

如果專案中已有啟動類別，則已更新 Configuration() 方法來包含 ConfigureAuth(app) 的呼叫 (加入至該方法)。否則將啟動類別加入至專案。

###### app.config 或 web.config 檔案有新的組態值。

已加入下列組態項目。

      <appSettings>
              <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
              <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
              <add key="ida:Audience" value="The App ID Uri from the wizard" />
      </appSettings> 

</p>
### 建立 Azure Active Directory (AD) 應用程式

在您於精靈中選取的目錄中建立 Azure AD 應用程式。

### 開始使用 Azure Active Directory (AD)

以下示範如何使用加入的程式碼。

###### 存取控制器之前需要驗證

專案中的所有控制器都加上 Authorize 屬性做裝飾。此屬性要求使用者必須經過驗證，才能存取這些控制器所定義的 API。若要允許以匿名方式存取控制器，請從控制器中移除此屬性。如果您要以更精確地設定權限，請將此屬性套用至每一個需要授權的方法，而非套用至控制器類別。

