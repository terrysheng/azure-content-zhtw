<properties  linkid="develop-mobile-tutorials-get-started-with-users-ios" urlDisplayName="Get Started with Authentication (iOS)" pageTitle="Get started with authentication (iOS) | Mobile Dev Center" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services iOS" description="Learn how to use Mobile Services to authenticate users of your iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

# 開始使用行動服務中的驗證

<div class="dev-center-tutorial-selector sublanding"><a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-users" title="Windows 市集 C#">Windows 市集 C#</a><a href="/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-users" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a><a href="/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a><a href="/zh-tw/documentation/articles/mobile-services-ios-get-started-users" title="iOS" class="current">iOS</a><a href="/zh-tw/documentation/articles/mobile-services-android-get-started-users" title="Android">Android</a><a href="/zh-tw/documentation/articles/mobile-services-html-get-started-users" title="HTML">HTML</a><a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a></div>
<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/" title=".NET 後端">.NET 後端</a> | <a href="/zh-tw/documentation/articles/mobile-services-ios-get-started-users/"  title="JavaScript 後端" class="current">JavaScript 後端</a></div>

本主題說明如何從您的 iOS 應用程式在 Azure 行動服務中驗證使用者。在本教學課程中，您會使用行動服務所支援的身份識別提供者將驗證新增至快速入門專案。使用者 ID 值在成功經過行動服務的驗證和授權後，就會顯示出來。

本教學課程將引導您逐步完成以下在您的應用程式中啟用驗證的基本步驟：

1.  [註冊應用程式進行驗證，並設定行動服務]
2.  [限制只有經驗證的使用者具有資料表的權限]
3.  [新增驗證至應用程式]

本教學課程是以行動服務快速入門為基礎。您也必須先完成 [開始使用行動服務] 教學課程。

完成本教學課程需使用 XCode 4.5 及 iOS 5.0 或更新版本。

## <a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a> 限制只有經驗證的使用者具有權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1.   在 Xcode 中，開啟您在完成[開始使用行動服務](/zh-tw/documentation/articles/mobile-services-ios-get-started)教學課程時所建立的專案。
    

2.   按 **執行** 按鈕，以在 iPhone 模擬器中建立專案並啟動應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。發生這個情況是因為應用程式嘗試以未經驗證的使用者身分存取行動服務，但 *TodoItem* 資料表現在要求要經過驗證。
    
    
接下來，您將更新應用程式，使其先驗證使用者再向行動服務要求資源。 ##<a data-morhtml="true" name="add-authentication" ></a>新增驗證至應用程式

[WACOM.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

## <a name="next-steps"></a>後續步驟 在下一個
[行動服務使用者的伺服器端授權] [以指令碼授權使用者]  教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。


<!-- Anchors. -->
[註冊應用程式進行驗證，並設定行動服務]:#register
[限制只有經驗證的使用者具有資料表的權限]:#permissions
[新增驗證至應用程式]:#add-authentication
[後續步驟]:#next-steps
<!-- Images. -->
[4]:./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]:./media/mobile-services-ios-get-started-users/mobile-service-uri.png
[13]:./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]:./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]:./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png
<!-- URLs. -->
[提交應用程式頁面]:http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的應用程式]:http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]:http://go.microsoft.com/fwlink/p/?LinkId=262253
[使用 Live Connect 在 Windows 市集應用程式提供單一登入功能]:/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[開始使用行動服務]:/en-us/develop/mobile/tutorials/get-started-ios
[開始使用資料]:/en-us/develop/mobile/tutorials/get-started-with-data-ios
[開始使用驗證]:/en-us/develop/mobile/tutorials/get-started-with-users-ios
[開始使用推播通知]:/en-us/develop/mobile/tutorials/get-started-with-push-ios
[以指令碼授權使用者]:/en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Azure 管理入口網站]:https://manage.windowsazure.com/
    
    
