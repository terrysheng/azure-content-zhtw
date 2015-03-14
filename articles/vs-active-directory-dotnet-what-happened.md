<properties 
	pageTitle="開始使用 Active Directory 驗證 - 發生什麼情形" 
	description="描述 Azure Active Directory 專案在 Visual Studio 中的情況" 
	services="active-directory" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [開始使用](/documentation/articles/vs-active-directory-dotnet-getting-started/)
> - [發生什麼情形](/documentation/articles/vs-active-directory-dotnet-what-happened/)

###<span id="whathappened">我的專案發生什麼情形？</span>
 
已加入參考。

#####NuGet 封裝參考

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####.NET 參考

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System`
- `System.Data`
- `System.Drawing`
- `System.IdentityModel`
- `System.IdentityModel.Tokens.Jwt`
- `System.Runtime.Serialization`

#####程式碼檔案已加入至專案 

驗證啟動類別 `App_Start/Startup.Auth.cs` 已加入至專案，內含 Azure AD 驗證的啟動邏輯。另外也已加入控制器類別 Controllers/AccountController.cs，內含 `SignIn()` 和 `SignOut()` 方法。最後，已加入部分檢視 `Views/Shared/_LoginPartial.cshtml`，內含 SignIn/SignOut 的動作連結。 

#####啟動程式碼已加入至專案
 
如果專案中已有啟動類別，則已更新 **Configuration** 方法來包含 `ConfigureAuth(app)` 的呼叫 (加入至該方法)。否則已將啟動類別加入至專案。 

#####app.config 或 web.config 有新的組態值 

已加入下列組態項目。 
	<pre>
	`<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

#####已建立 Azure Active Directory (AD) 應用程式 
已在您於精靈中選取的目錄中建立 Azure AD 應用程式。 

[深入了解 Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=46--> 
