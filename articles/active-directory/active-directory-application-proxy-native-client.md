<properties
	pageTitle="如何啟用原生用戶端應用程式與 Proxy 應用程式的發佈 | Microsoft Azure"
	description="涵蓋如何啟用原生用戶端應用程式，以與 Azure AD 應用程式 Proxy 連接器通訊，為內部部署的應用程式提供安全的遠端存取。"
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="steven.powell"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/02/2015"
	ms.author="rkarlin"/>

# 如何啟用原生用戶端應用程式以與 Proxy 應用程式互動
Azure Active Directory 應用程式 Proxy 廣泛用於發佈瀏覽器應用程式，例如 SharePoint、Outlook Web Access 和自訂企業營運應用程式。它也可以用來發佈使用原生用戶端取用的 HTTP 後端應用程式。這是透過支援在標準授權 HTTP 標頭中傳送的 Azure AD 發出的權杖來實現。


![](./media/active-directory-application-proxy-native-client/richclientflow.png)


發佈這類應用程式的建議方法是使用 Azure AD 驗證程式庫，它會處理所有驗證細節並支援許多不同的用戶端環境。應用程式 Proxy 融入[原生應用程式到 Web API 案例](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/#native-application-to-web-api)。完成此動作的程序如下所示：

1. 如同任何其他應用程式一般，發佈您的 Proxy 應用程式，指派使用者並提供進階或基本授權給他們。如需詳細資訊，請參閱[使用應用程式 Proxy 發佈應用程式](active-directory-application-proxy-publish.md)。
2. 以下列方式設定原生應用程式：
  3. 登入 Azure 管理入口網站。
  4. 按一下左側功能表中的 Active Directory 圖示，並按一下想要的目錄。
  5. 在頂端功能表上，按一下 [應用程式]。如果您的目錄中尚未新增任何應用程式，此頁面僅會顯示新增應用程式連結。按一下此連結，或者您可以按一下命令列上的 [新增] 按鈕。
  4. 在 [欲執行動作] 頁面上，按一下此連結以[加入我的組織正在開發的應用程式]。
  5. 在 [告訴我們您的應用程式] 頁面上，指定您的應用程式的名稱並選擇 [原生用戶端應用程式]，其代表電話或電腦等裝置上安裝的應用程式。完成之後，按一下頁面右下角的箭頭圖示。
  6. 在 [應用程式屬性] 頁面上，提供原生用戶端應用程式的**重新導向 URI**，然後按一下頁面右下角的核取方塊。</br>已加入您的應用程式，而且您將會進入應用程式的 [快速啟動] 頁面。 
8. 啟用要公開給您的目錄中的其他應用程式的原生應用程式：
  9. 在頂端功能表上，按一下 [應用程式]，選取新的原生應用程式，然後按一下 [設定]。
  10. 向下捲動至 [其他應用程式的權限] 區段。按一下 [加入應用程式] 按鈕，並選取您想要授與原生應用程式存取的 Proxy 應用程式，然後按一下右下角中的核取記號。從 [委派的權限] 下拉式功能表，選取新的權限。</br>

![](./media/active-directory-application-proxy-native-client/delegate_native_app.png) </br></br> 4.在 Active Directory 驗證程式庫 (ADAL) 的驗證內容中編輯原生應用程式程式碼，以包含下列：

		// Acquire Access Token from AAD for Proxy Application
		AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
		AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >", 
                                                        "< Client Id of the Native app>", 
                                                        new Uri("< Redirect Uri of the Native App>"), 
                                                        PromptBehavior.Never);
		
		//Use the Access Token to access the Proxy Application
		HttpClient httpClient = new HttpClient();
		httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
		HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

應該以下列方式取代變數：


- 可以在應用程式**組態**頁面的 URL 的 GUID 中找到 **TenantId**，緊接在 “/Directory/” 之後。
- **前端 URL** 是您在 Proxy 應用程式中輸入的前端 URL，並且可以在 Proxy 應用程式的 [組態] 頁面中找到。
- 原生應用程式的**用戶端識別碼** 可以在原生應用程式的 [設定] 頁面上找到。
- **原生應用程式的重新導向 URI** 可以在原生應用程式的 [設定] 頁面上找到。

![](./media/active-directory-application-proxy-native-client/new_native_app.png) </br> </br>如需原生應用程式流程的詳細資訊，請參閱[原生應用程式到 Web API](https://azure.microsoft.com/zh-TW/documentation/articles/active-directory-authentication-scenarios/#native-application-to-web-api)。






## 後續步驟
應用程式 Proxy 還有其他更多用途：


- [使用您自己的網域名稱發佈應用程式](active-directory-application-proxy-custom-domains.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [使用宣告感知應用程式](active-directory-application-proxy-claims-aware-apps.md)
- [啟用條件式存取](active-directory-application-proxy-conditional-access.md)


### 深入了解應用程式 Proxy
- [看看我們在這裡的線上說明](active-directory-application-proxy-enable.md)
- [查閱應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
- [觀看我們在 Channel 9 上的影片！](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 其他資源
* [以組織身分註冊 Azure](sign-up-organization.md)
* [Azure 身分識別](fundamentals-identity.md)

<!---HONumber=Nov15_HO2-->