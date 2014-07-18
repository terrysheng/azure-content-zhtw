<properties  linkid="develop-mobile-how-to-guides-register-for-facebook-authentication" urlDisplayName="Register for Facebook Authentication" pageTitle="Register for Facebook authentication - Mobile Services" metaKeywords="Azure Facebook, Azure Facebook, Azure authenticate Mobile Services" description="Learn how to use Facebook authentication in your Azure Mobile Services app." metaCanonical="" services="" documentationCenter="" title="Register your apps for Facebook authentication with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

# 在行動服務中註冊應用程式以採用 Facebook 驗證

本主題說明如何在 Azure 行動服務中註冊您的應用程式，使其能夠採用 Facebook 驗證。
<div class="dev-callout"><b>注意</b>
<p>若要完成本主題的程序，您必須具有已通過電子郵件地址與手機號碼驗證的 Facebook 帳戶。若要建立新的 Facebook 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268285" target="_blank">facebook.com</a>。</p>
</div>

1.  瀏覽至 [Facebook 開發人員][1]網站，並以您的 Facebook 帳戶認證登入。

2.  (選用) 按一下 **應用程式**，然後按一下 **Register as a Developer**，接受政策並遵循註冊步驟 (若您尚未註冊)。
    
	![][0]

3.  按一下 **應用程式**，然後按一下 **製作新應用程式**。
    
	![][1]

4.  為您的應用程式選擇獨一無二的名稱，選取 **Apps for Pages**，按一下 **建立應用程式** 並完成挑戰問題。
    
	![][2]
    
    如此會在 Facebook 註冊應用程式

5.  按一下 **設定**，在 **App Domains** 中輸入您行動服務的網域，然後按一下 **新增平台** 並選取 **網站**。
    
	![][3]

6.  在 **網站 URL** 中輸入您行動服務的 URL，然後按一下 **儲存變更**。
    
	![](./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png)

7.  按一下 **顯示**，提供您的密碼 (如有要求)，然後記下 **應用程式 ID** 和 **應用程式密鑰**  的值。
    
	![][5]
	<div class="dev-callout"><b>安全性注意事項</b>
	<p>應用程式密鑰是重要的安全性認證。請勿將這個密鑰與任何人分享，或與您的應用程式一起散發。</p>
	</div>



現在您已準備好提供應用程式 ID 和應用程式密碼值給行動服務，以在您的應用程式中採用 Facebook 登入驗證。

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-developer-register.png
[1]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-add-app.png
[2]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-new-app-dialog.png
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[4]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app-2.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->



[1]: http://go.microsoft.com/fwlink/p/?LinkId=268286