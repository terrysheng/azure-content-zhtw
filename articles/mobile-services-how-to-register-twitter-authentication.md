<properties urlDisplayName="Register for Twitter Authentication" pageTitle="註冊 Twitter 驗證 - 行動服務" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Twitter login with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

#在行動服務中註冊您的應用程式以進行 Twitter 登入

本主題說明如何在 Azure 行動服務中註冊您的應用程式，使其能夠採用 Twitter 驗證。

>[WACOM.NOTE] 本教學課程與 [Azure 行動服務]有關，(http://azure.microsoft.com/zh-tw/services/mobile-services/)它是一個可協助您針對任何平台建置可擴充行動應用程式的解決方案。行動服務讓同步處理資料、驗證使用者及推播通知等作業變得簡單。此頁面支援 <a href="http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-ios-get-started-users/">開始使用驗證</a> 教學課程，說明如何將使用者登入您的應用程式。如果這是您第一次使用行動服務，請完成 <a href="http://azure.microsoft.com/zh-tw/documentation/articles/mobile-services-ios-get-started/">開始使用行動服務教學課程</a>。

若要完成本主題的程序，您必須具有已通過電子郵件地址驗證的 Twitter 帳戶。若要建立新的 Twitter 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>。

1. 瀏覽至 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Twitter 開發人員</a> 網站，使用您的 Twitter 帳戶認證登入，再按一下 [**建立新的應用程式**]。

   	![][1]

2. 輸入應用程式的 [**名稱**]、[**說明**] 和 [**網站**] 值，然後在 [**回呼 URL**] 中輸入附加路徑 _/login/twitter_ 之行動服務的 URL。

	>[WACOM.NOTE]如需使用 Visual Studio 將 .NET 後端行動服務發佈至 Azure，則重新導向 URL 是行動服務的 URL 後面附加路徑 _signin-twitter_，而您的行動服務為 .NET 服務，例如 <code>https://todolist.azure-mobile.net/signin-twitter</code>。

   	![][2]

3.  在頁面底部，請閱讀並接受條款，輸入正確的 CAPTCHA 文字，然後按一下 [**建立您的 Twitter 應用程式**]。 

   	![][3]

   	這會註冊應用程式並顯示應用程式詳細資料。

6. 記下 [**消費者金鑰**] 和 [**消費者密碼**] 的值。 

   	![][4]

    <div class="dev-callout"><b>安全性注意事項</b>
	<p>消費者密碼是重要的安全性認證。請勿將此密碼告訴任何人或隨應用程式一起散發。</p>
    </div>

7. 按一下 [**設定**] 索引標籤，向下捲動並勾選 [**允許使用此應用程式登入 Twitter**]，然後按一下 [**更新此 Twitter 應用程式的設定**]。

	![][5]

現在您已準備好提供消費者金鑰和消費者密碼值給行動服務，以在您的應用程式中採用 Twitter 登入驗證。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Twitter 開發人員]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure 管理入口網站]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
