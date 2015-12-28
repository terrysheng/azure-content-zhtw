<properties
   pageTitle="Azure Active Directory 開發人員指南 | Microsoft Azure"
   description="本文提供 Azure Active Directory 開發人員導向資源的完整指南。"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/10/2015"
   ms.author="mbaldwin"/>


# Azure Active Directory 開發人員指南

## 概觀
Azure Active Directory 是身分識別管理即服務 (IDMaaS) 平台，提供有效的方法，協助開發人員將身分識別管理整合到他們的應用程式中。下列文章提供實作的概觀和 Azure Active Directory 的重要功能。我們建議您依序閱讀，如果您尚未準備好開始進行，請跳到[開始使用](#getting-started)。


1. [Azure Active Directory 整合的優點](active-directory-how-to-integrate.md)：探索與 Azure Active Directory 整合為何是提供安全登入和授權的最佳解決方案。

1. [Active Directory 驗證案例](active-directory-authentication-scenarios.md)：利用 Azure Active Directory 的簡易驗證來提供登入功能給您的應用程式。

1. [整合應用程式與 Azure Active Directory](active-directory-integrating-applications.md)：了解如何從 Azure Active Directory 加入、更新和移除應用程式、以及關於整合應用程式商標指導方針的資訊。

1. [Azure Active Directory 圖形 API](active-directory-graph-api.md)：使用「Azure Active Directory 圖形 API」，以程式設計方式透過 REST API 端點存取 Azure Active Directory。請注意，Azure AD Graph API 也可透過 [Microsoft Graph](https://graph.microsoft.io/) 存取，其為統一的 API，可透過單一 REST API 端點以單一的存取權杖，存取多個 Microsoft 雲端服務 API。

1. [Azure Active Directory 驗證程式庫](active-directory-authentication-libraries.md)：使用 Azure 驗證程式庫輕鬆地驗證使用者，取得存取權杖。


## 開始使用

這些教學課程適用於多種平台，可協助您快速開始使用 Azure Active Directory 進行開發。先決條件是您必須[取得 Azure Active Directory 租用戶](active-directory-howto-tenant.md)。

### 行動裝置與電腦應用程式快速入門指南

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)| [![Windows Phone](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsphone.md)|[![Windows 市集](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Windows 市集](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)

### Web 應用程式快速入門指南

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![Javascript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md)
|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[Javascript](active-directory-devquickstarts-angular.md)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)

### Web API 快速入門指南

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### 查詢目錄快速入門指南

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[Graph API](active-directory-graph-api-quickstart.md)|

## 作法

這些文章描述如何使用 Azure Active Directory 執行特定工作︰

- [取得 Azure Active Directory 租用戶](active-directory-howto-tenant.md)
- [在 Azure Active Directory 應用程式資源庫中列出您的應用程式](active-directory-app-gallery-listing.md)
- [了解 Azure Active Directory 應用程式資訊清單](active-directory-application-manifest.md)
- [使用 Office 365 API 建立應用程式](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [提交 Office 365 的 Web 應用程式到賣方儀表板](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [預覽：如何建置可同時透過個人和公司/學校帳戶登入使用者的應用程式](active-directory-appmodel-v2-overview.md)
- [預覽：如何建置註冊與登入取用者的應用程式](active-directory-b2c-overview.md)


## 參考

這些文章提供 REST 和驗證程式庫 API、通訊協定、錯誤、程式碼範例和端點的基礎參考。

###  支援
- [標記的問題](http://stackoverflow.com/questions/tagged/azure-active-directory)：搜尋 [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) 與[adal](http://stackoverflow.com/questions/tagged/adal) 的標記，在堆疊溢位上尋找 Azure Active Directory 解決方案。

### 代碼

- [Azure Active Directory 開放原始碼程式庫](http://github.com/AzureAD)：尋找程式庫原始碼最簡單的方法，是使用我們的[程式庫清單](active-directory-authentication-libraries.md)。

- [Azure Active Directory 範例](http://github.com/AzureADSamples)：巡覽範例清單最簡單的方法，是使用[程式碼範例索引](active-directory-code-samples.md)。


### Graph API

- [Graph API 參考](https://msdn.microsoft.com/library/azure/hh974476.aspx)：Azure Active Directory Graph API 的 REST 參考。[檢視互動式圖形 API 參考體驗](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)。

- [圖形 API 權限範圍](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes)：用以控制應用程式對租用戶中目錄資料取權的 OAuth 2.0 權限範圍。


### 驗證通訊協定

- [SAML 2.0 通訊協定參考](https://msdn.microsoft.com/library/azure/dn195591.aspx)：SAML 2.0 通訊協定可讓應用程式能為其使用者提供單一登入體驗。


- [OAuth 2.0 通訊協定參考](https://msdn.microsoft.com/library/azure/dn645545.aspx)：您可以使用 OAuth 2.0 通訊協定，授權存取 Azure Active Directory 租用戶中的 Web 應用程式與 Web API。


- [OpenID Connect 1.0 通訊協定參考](https://msdn.microsoft.com/library/azure/dn645541.aspx)：OpenID Connect 1.0 通訊協定會擴充 OAuth 2.0 以作為驗證通訊協定。


- [WS-同盟 1.2 通訊協定參考](https://msdn.microsoft.com/library/azure/dn903702.aspx)：Web 服務同盟 1.2 版規格中所指定的 WS-同盟 1.2 通訊協定。

- [支援的權杖與宣告類型](active-directory-token-and-claims.md)：您可以使用此指南來了解及評估 SAML 2.0 與 JSON Web Token (JWT) 權杖中的宣告。

## 影片

### Build 2015

這些概觀簡報有關於使用 Azure Active Directory 功能開發應用程式，適用於直接在工程團隊工作的講者。簡報涵蓋一些基本主題，包括 IDMaaS、驗證、身分識別同盟和單一登入。

- [Azure Active Directory：新式應用程式的「身分識別管理即服務」](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)
- [使用 Azure Active Directory 開發新式 Web 應用程式](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)
- [使用 Azure Active Directory 開發新式原生應用程式](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) 是每星期五的一對一影片系列，專門提供簡短的 10 到 15 分鐘訪談，由專家就各種 Azure 主題發表意見。請使用頁面上的 [服務篩選] 功能，查看所有 Azure Active Directory 影片。

- [Azure 身分識別 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Azure 身分識別 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Azure 身分識別 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## 社交

- [Active Directory 團隊部落格](http://blogs.technet.com/b/ad/)：Azure Active Directory 領域的最新發展。

- [Azure Active Directory Graph 團隊部落格](http://blogs.msdn.com/b/aadgraphteam)︰Graph API 專屬的 Azure Active Directory 資訊。

- [雲端識別](http://www.cloudidentity.net)：一位 Azure Active Directory 主要 PM 對於「身分識別管理即服務」的見解。

- [Twitter 上的 Azure Active Directory](https://twitter.com/azuread)：140 個字元內的 Azure Active Directory 公告。

<!---HONumber=AcomDC_1217_2015-->