<properties 
	pageTitle="在 Azure 應用程式服務中將行動和 Web 應用程式連接至 SaaS API (O365/SharePoint/Salesforce)" 
	description="本教學課程示範如何透過 Azure 應用程式服務 Web 應用程式中裝載的 ASP.NET 使用 SaaS API 應用程式。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="syntaxc4" 
	manager="yochayk" 
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="web" 
	ms.date="03/24/2015"
	ms.author="cfowler"/>

# 在 Azure 應用程式服務中將行動和 Web 應用程式連接至 SaaS API (O365/SharePoint/Salesforce)

本教學課程示範如何透過[應用程式服務 Web 應用程式](http://go.microsoft.com/fwlink/?LinkId=529714)中裝載的 ASP.NET 使用 API 應用程式。

## 概觀

您將了解：

- 如何透過 Web 應用程式中裝載的 ASP.NET 使用 API 應用程式。

## 必要條件

本教學課程是根據 API 應用程式教學課程系列所建置：

1. [建立 Azure API 應用程式](app-service-dotnet-create-api-app)
2. [發佈 Azure API 應用程式](app-service-dotnet-publish-api-app)
3. [部署 Azure API 應用程式](app-service-dotnet-deploy-api-app)
4. [偵錯 Azure API 應用程式](app-service-dotnet-remotely-debug-api-app)

## 將 API 應用程式設為可公開存取

在 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)中，選取 [API 應用程式]。按一下命令列中的 [**設定**] 按鈕。在 [**基本設定**] 刀鋒視窗中，將 [**存取層級**] 變更為 [**公開 (匿名)**]。

![](./media/app-service-web-connect-web-app-to-saas-api/4-5-Change-Access-Level-To-Public.png)

## 在 Visual Studio 中建立 ASP.NET MVC 應用程式

1. 開啟 Visual Studio。使用 [**新專案**] 對話方塊，加入新的 [**ASP.NET Web 應用程式**]。按一下 [**確定**]。

	![File > New > Web > ASP.NET Web Application](./media/app-service-web-connect-web-app-to-saas-api/1-Create-New-MVC-App-For-Consumption.png)

1. 選取 [**MVC**] 範本。按一下 [**變更驗證**]，並選取 [**不驗證**]，然後按兩次 [**確定**]。

	![New ASP.NET Application](./media/app-service-web-connect-web-app-to-saas-api/2-Change-Auth-To-No-Auth.png)

1. 在 [方案總管] 中，於新建立的 Web 應用程式專案上按一下滑鼠右鍵，然後選取 [**加入 Azure 應用程式參考**]。

	![Add Azure API App Reference...](./media/app-service-web-connect-web-app-to-saas-api/3-Add-Azure-API-App-SDK.png)

1. 在 [**現有 API 應用程式**] 下拉式清單中，選取您想要連接的 API 應用程式。

	![Select Existing API App](./media/app-service-web-connect-web-app-to-saas-api/4-Add-Azure-API-App-SDK-Dialog.png)

	>[AZURE.NOTE] 將會從 Swagger API 端點自動產生連接到 API 應用程式的用戶端程式碼。

1. 若要利用產生的 API 程式碼，請開啟 HomeController.cs 檔案，並將 `Contact` 動作取代為下者：

	    public async Task<ActionResult> Contact()
	    {
	        ViewBag.Message = "Your contact page.";
	
	        var contacts = new ContactsList();
	        var response = await contacts.Contacts.GetAsync();
	        var contactList = response.Body;
	
	        return View(contactList);
	    }

	![HomeController.cs Code Updates](./media/app-service-web-connect-web-app-to-saas-api/5-Write-Code-Which-Leverages-Swagger-Generated-Code.png)

1. 使用下面的程式碼來更新 `Contact` 檢視，以反映連絡人動態清單：  
	<pre>// Add to the very top of the view file
	@model IList&lt;MyContactsList.Web.Models.Contact&gt;
	
	// Replace the default email addresses with the following
    &lt;h3&gt;Public Contacts&lt;/h3&gt;
    &lt;ul&gt;
        @foreach (var contact in Model)
        {
            &lt;li&gt;&lt;a href=&quot;mailto:@contact.EmailAddress&quot;&gt;@contact.Name &amp;lt;@contact.EmailAddress&amp;gt;&lt;/a&gt;&lt;/li&gt;
        }
    &lt;/ul&gt; 
	</pre>

	![Contact.cshtml Code Updates](./media/app-service-web-connect-web-app-to-saas-api/6-Update-View-To-Reflect-Changes.png)

## 將 Web 應用程式部署到應用程式服務中的 Web 應用程式

請遵循[如何部署 Azure Web 應用程式](web-sites-deploy.md)中的指示。

>[AZURE.NOTE] 如果您想要在註冊 Azure 帳戶之前開始使用 Azure 應用程式服務，請移至[試用應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，您可以在其中於應用程式服務中立即建立存留較短的入門 Web 應用程式。不需要信用卡，無需承諾。

## 變更內容
* 如需從網站變更為應用程式服務的指南，請參閱：[Azure 應用程式服務和它對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊入口網站變更為新入口網站的指南，請參閱：[瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->