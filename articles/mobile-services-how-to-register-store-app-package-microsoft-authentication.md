<properties linkid="develop-mobile-how-to-guides-register-windows-store-app-server-auth" urlDisplayName="Shared Access Signature Part 1" pageTitle="Register your Windows Store app package for Microsoft authentication" metaKeywords="" description="Learn how to register your Windows Store app for Microsoft authentication in your Azure Mobile Services application" metaCanonical="" services="" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="" editor="" />

註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證
==================================================

Azure 行動服務對於用戶端導向和伺服器導向的驗證方法均可支援。伺服器導向驗證會使用身分識別提供者，包括 Microsoft 帳戶在內。如果您未向行動服務註冊您的應用程式，即使用 Microsoft 帳戶進行伺服器導向驗證，則在每次要求驗證時，都會提示使用者提供認證。當您註冊應用程式時，Microsoft 帳戶登入認證會被快取，而此快取可用於後續的驗證，讓使用者不會再看見提供認證的提示。此主題將說明如何註冊 Windows 市集應用程式封裝，以提升使用 Azure 行動服務進行驗證時的 Microsoft 帳戶登入效能。

用戶端導向驗證可讓您透過 Live Connect，在 Windows 裝置上執行單一登入。如果您使用 Live Connect API，您即無須完成此主題中的步驟。如需詳細資訊，請參閱[使用 Live Connect 單一登入驗證 Windows 市集應用程式](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet)。

註冊您的應用程式，也可讓您傳送推播通知至應用程式。如果您已為應用程式完成教學課程[開始使用推播通知](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/)，您即已完成此主題中的步驟。

**注意**

*Visual Studio 2013 預覽版*的新功能，可讓您輕鬆地對行動服務註冊您的 Windows 市集應用程式封裝。如需詳細資訊，請參閱 Windows 開發人員中心的[快速入門：新增行動服務的推播通知](http://go.microsoft.com/fwlink/p/?LinkId=309101)。

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

在註冊應用程式封裝後，在您呼叫 [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594) 方法時，請記得為 *useSingleSignOn* 提供 **true** 值。這可讓您的使用者在使用 Microsoft 帳戶時有更理想的登入體驗。

