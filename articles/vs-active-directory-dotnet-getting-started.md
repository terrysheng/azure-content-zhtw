<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### 發生什麼情形？

已在專案中加入參考

###### NuGet 封裝參考

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System.IdentityModel.Tokens.Jwt

###### .NET 參考

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System, System.Data, System.Drawing, System.IdentityModel, System.IdentityModel.Tokens.Jwt, System.Runtime.Serialization

###### 程式碼檔案加入至專案

驗證啟動類別 App\_Start/Startup.Auth.cs 加入至專案，內含 Azure AD 驗證的啟動邏輯。另外也加入控制器類別 Controllers/AccountController.cs，內含 SignIn() 和 SignOut() 方法。最後，加入部分檢視 Views/Shared/\_LoginPartial.cshtml，內含 SignIn/SignOut 的動作連結。

###### 啟動程式碼加入至專案

如果專案中已有啟動類別，則已更新 Configuration() 方法來包含 ConfigureAuth(app) 的呼叫 (加入至該方法)。否則將啟動類別加入至專案。

###### app.config 或 web.config 有新的組態值

已加入下列組態項目。

    <appSettings>
        <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
        <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
        <add key="ida:AADInstance" value="https://login.windows.net/{0}" />
        <add key="Ida:PostLogoutRedirectURI" value="Your project start page" />
    </appSettings> 

</p>
###### 建立 Azure Active Directory (AD) 應用程式

在您於精靈中選取的目錄中建立 Azure AD 應用程式。

## 開始使用 Azure Active Directory (AD)

以下示範如何使用加入的程式碼。

###### 存取控制器之前需要驗證

專案中的所有控制器都加上 Authorize 屬性做裝飾。此屬性要求使用者必須經過驗證才能存取這些控制器。若要允許以匿名方式存取控制器，請從控制器中移除此屬性。

###### 加入 SignIn / SignOut 控制項

若要將 SignIn/SignOut 控制項加入至檢視，您可以使用 \_LoginPartial.cshtml 部分檢視將此功能加入至您的其中一個檢視。以下是加入至 standard \_Layout.cshtml 檢視的功能範例：

    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>@ViewBag.Title - My ASP.NET Application</title>
        @Styles.Render("~/Content/css")
        @Scripts.Render("~/bundles/modernizr")
    </head>
    <body>
        <div class="navbar navbar-inverse navbar-fixed-top">
            <div class="container">
                <div class="navbar-header">
                    <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                    </button>
                    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
                </div>
                <div class="navbar-collapse collapse">
                    <ul class="nav navbar-nav">
                        <li>@Html.ActionLink("Home", "Index", "Home")</li>
                        <li>@Html.ActionLink("About", "About", "Home")</li>
                        <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                    </ul>
                    @Html.Partial("_LoginPartial")
                </div>
            </div>
        </div>
        <div class="container body-content">
            @RenderBody()
            <hr />
            <footer>
                <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
            </footer>
        </div>
        @Scripts.Render("~/bundles/jquery")
        @Scripts.Render("~/bundles/bootstrap")
        @RenderSection("scripts", required: false)
    </body>
    </html> 
