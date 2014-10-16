<properties linkid="develop-mobile-tutorials-get-started-with-users-wp8" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Phone app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

開始使用行動服務中的驗證
========================

[Windows 市集 C\#](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-users "Windows 市集 C#")[Windows 市集 JavaScript](/zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-users "Windows 市集 JavaScript")[Windows Phone](/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-users "Windows Phone")[iOS](/zh-tw/documentation/articles/mobile-services-ios-get-started-users "iOS")[Android](/zh-tw/documentation/articles/mobile-services-android-get-started-users "Android")[HTML](/zh-tw/documentation/articles/mobile-services-html-get-started-users "HTML")[Xamarin.iOS](/zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users "Xamarin.iOS")[Xamarin.Android](/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-users "Xamarin.Android")

[.NET 後端](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/ ".NET 後端") | [JavaScript 後端](/zh-tw/documentation/articles/mobile-services-windows-phone-get-started-users/ "JavaScript 後端")

本主題說明如何從您的應用程式在 Azure 行動服務中驗證使用者。在本教學課程中，您會使用行動服務所支援的身份識別提供者將驗證新增至快速入門專案。使用者 ID 值在成功經過行動服務的驗證和授權後，就會顯示出來。

[觀賞教學課程](http://go.microsoft.com/fwlink/?LinkId=298631) [播放視訊](http://go.microsoft.com/fwlink/?LinkId=298631) 10:50

本教學課程將引導您逐步完成以下在您的應用程式中啟用驗證的基本步驟：

1.  [註冊應用程式進行驗證，並設定行動服務](#register)
2.  [限制只有經驗證的使用者具有資料表的權限](#permissions)
3.  [新增驗證至應用程式](#add-authentication)

本教學課程是以行動服務快速入門為基礎。您也必須先完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-wp8)教學課程。

註冊應用程式註冊應用程式進行驗證，並設定行動服務
------------------------------------------------

為了能夠驗證使用者，您必須向身分識別提供者註冊您的應用程式。然後，您必須在行動服務中註冊提供者所產生的用戶端密鑰。

1.  登入 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下 **[行動服務]**，然後按一下您的行動服務。

	![][1]

2.  按一下 **[儀表板]** 索引標籤，記下 **[Mobile Service URL]** 值。

	![][2]

    當您註冊應用程式時，可能需要提供此值給身分識別提供者。

3.  從以下清單中選擇一個支援的身分識別提供者，並依照步驟向該提供者註冊您的應用程式：

-   [Microsoft 帳戶](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
-   [Facebook 登入](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
-   [Twitter 登入](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
-   [Google 登入](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
-   [Azure Active Directory](/zh-tw/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    請務必記下提供者所產生的用戶端身分識別與密鑰值。

    **安全性注意事項**

    提供者所產生的密鑰是重要的安全性認證。請勿將這個密鑰與任何人分享，或與您的應用程式一起散發。

1.  回到管理入口網站，按一下 **[身分識別]** 索引標籤，輸入從身分識別提供者那裡取得的應用程式識別碼與共用密鑰，然後按一下 **[儲存]**。

	![][3]

您的行動服務和您的應用程式現在都已設定成使用您所選擇的驗證提供者。

限制只有經驗證的使用者具有權限
------------------------------

1.  在管理入口網站中，按一下 **[資料]** 索引標籤，然後按一下 **[TodoItem]** 資料表。

	![][4]

2.  按一下 **[權限]** 索引標籤，將所有的權限設為 **[Only authenticated users]**，然後按一下 **[儲存]**。如此可確保對 **TodoItem** 資料表的所有操作都必須由經過驗證的使用者進行。如此也可簡化下一個教學課程中的指令碼，因為那些指令碼將不需要顧及有匿名使用者的可能性。

	![][5]

3.  在 Visual Studio 2012 Express for Windows Phone 中，開啟您在完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started-wp8)教學課程時所建立的專案。

4.  按 F5 鍵執行此快速入門型應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。

        This happens because the app attempts to access Mobile Services as an unauthenticated user, but the _TodoItem_ table now requires authentication.

接下來，您將更新應用程式，使其先驗證使用者再向行動服務要求資源。

新增驗證至應用程式
------------------

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

後續步驟
--------

在下一個[行動服務使用者的伺服器端授權](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8)教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-wp8
[Get started with data]: /en-us/develop/mobile/tutorials/started-with-data-wp8
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Azure Management Portal]: https://manage.windowsazure.com/
