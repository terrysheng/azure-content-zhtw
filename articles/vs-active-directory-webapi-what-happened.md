<properties 
	pageTitle="" 
	description="說明執行 Azure Active Directory 精靈後，Visual Studio 專案中變更的內容" 
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

# 我的專案發生什麼情形？

> [AZURE.SELECTOR]
> - [開始使用](vs-active-directory-webapi-getting-started.md)
> - [發生什麼情形](vs-active-directory-webapi-what-happened.md)

### <span id="whathappened">我的專案發生什麼情形？</span>

已加入參考。 

##### NuGet 封裝參考 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Newtonsoft.Json`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##### .NET 參考 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Newtonsoft.Json`
- `Owin`
- `System.IdentityModel.Tokens.Jwt` 

##### 程式碼檔案已加入至專案 

驗證啟動類別 **App_Start/Startup.Auth.cs** 已加入至專案，內含 Azure AD 驗證的啟動邏輯。 

##### 啟動程式碼已加入至專案 

如果專案中已有啟動類別，則更新 **Configuration** 方法來包含  `ConfigureAuth(app)` 的呼叫 (加入至該方法)。否則已將啟動類別加入至專案。 


##### app.config 或 web.config 檔案有新的組態值。

已加入下列組態項目。 
	<pre>
	`<appSettings> 
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
    		<add key="ida:Audience" value="The App ID Uri from the wizard" /> 
	</appSettings>` </pre>

### 已建立 Azure Azure AD App 

已在您於精靈中選取的目錄中建立 Azure AD 應用程式。


[深入了解 Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=49-->