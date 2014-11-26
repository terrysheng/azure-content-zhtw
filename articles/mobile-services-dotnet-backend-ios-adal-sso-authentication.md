<properties linkid="develop-mobile-tutorials-sso-with-adal-ios" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc,mahender" />

# 使用 Active Directory Authentication Library 單一登入驗證您的應用程式

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="Windows 市集 C#" >Windows 市集 C#</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" class="current">iOS</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS">Xamarin.iOS</a>
</div>

在本教學課程中，您會使用 Active Directory Authentication Library 將驗證新增至快速入門專案。

要能夠驗證使用者，您必須向 Azure Active Directory (AAD) 註冊您的應用程式。此作業有兩個階段。第一，您必須註冊行動服務，並公開其權限。第二，您必須註冊 iOS 應用程式，並為其授與對這些權限的存取權

> [WACOM.NOTE] 本教學課程旨在協助您深入了解如何透過行動服務，為 iOS 應用程式執行單一登入 Azure Active Directory 驗證。如果這是您第一次接觸行動服務，請先完成教學課程[開始使用行動服務][開始使用行動服務]。

本教學課程將逐步引導您完成下列基本步驟：

1.  [向 Azure Active Directory 註冊您的行動服務][向 Azure Active Directory 註冊您的行動服務]
2.  [向 Azure Active Directory 註冊您的應用程式][向 Azure Active Directory 註冊您的應用程式]
3.  [將行動服務設定為需要驗證][將行動服務設定為需要驗證]
4.  [將驗證程式碼新增至用戶端應用程式][將驗證程式碼新增至用戶端應用程式]
5.  [使用驗證測試用戶端][使用驗證測試用戶端]

本教學課程需要下列各項：

-   XCode 4.5 和 iOS 6.0 (或更新版本)
-   完成[開始使用行動服務][開始使用行動服務]或[開始使用資料][開始使用資料]教學課程。
-   Microsoft Azure 行動服務 SDK
-   [Active Directory Authentication Library for iOS][Active Directory Authentication Library for iOS]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>將行動服務設定為需要驗證

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>將驗證程式碼新增至用戶端應用程式

1.  下載 [Active Directory Authentication Library for iOS][Active Directory Authentication Library for iOS]，並將其納入您的專案中。請務必同時從 ADAL 來源新增腳本。

2.  在 QSTodoListViewController 中，使用下列程式碼納入 ADAL：

        #import "ADALiOS/ADAuthenticationContext.h"

3.  然後，新增下列方法：

        - (void) loginAndGetData
        {    
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            NSString *authority = @"<INSERT-AUTHORITY-HERE>";
            NSString *resourceURI = @"<INSERT-RESOURCE-URI-HERE>";
            NSString *clientID = @"<INSERT-CLIENT-ID-HERE>";
            NSString *redirectURI = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError *error;
            ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];   
            NSURL *redirectUri = [[NSURL alloc]initWithString:redirectURI];

            [authContext acquireTokenWithResource:resourceURI clientId:clientID redirectUri:redirectUri completionBlock:^(ADAuthenticationResult *result) {
                if (result.tokenCacheStoreItem == nil)
                {
                    return;
                }
                else
                {
                    NSDictionary *payload = @{
                        @"access_token" : result.tokenCacheStoreItem.accessToken
                    };
                    [client loginWithProvider:@"windowsazureactivedirectory" token:payload completion:^(MSUser *user, NSError *error) {
                        [self refresh];
                    }];
                }
            }];
        }

4.  `loginAndGetData` 方法的程式碼中，將 **INSERT-AUTHORITY-HERE** 取代為您佈建應用程式的租用戶名稱，其格式應為 <https://login.windows.net/tenant-name.onmicrosoft.com>。此值可從 [Azure 管理入口網站][Azure 管理入口網站]複製到 Azure Active Directory 的 [網域] 索引標籤以外。

5.  `loginAndGetData` 方法的程式碼中，將 **INSERT-RESOURCE-URI-HERE** 取代為您行動服務的 **應用程式識別碼 URI**。如果您依照[如何向 Azure Active Directory 註冊][如何向 Azure Active Directory 註冊]主題的內容操作，您的應用程式識別碼應會類似於 <https://todolist.azure-mobile.net/login/aad>。

6.  `loginAndGetData` 方法的程式碼中，將 **INSERT-CLIENT-ID-HERE** 取代為您從原生用戶端應用程式中複製的用戶端識別碼。

7.  在 `loginAndGetData` 方法的程式碼中，將 **INSERT-REDIRECT-URI-HERE** 取代為您行動服務的 /login/done 端點。此項目應與 <https://todolist.azure-mobile.net/login/done> 類似。

8.  在 QSTodoListViewController 中，使用下列程式碼取代 `[self refresh]`，以修改 `ViewDidLoad`：

        [self loginAndGetData];

## <a name="test-client"></a>使用驗證測試用戶端

1.  從 [產品] 功能表中按一下 [執行]，以啟動應用程式
2.  您會看見登入 Azure Active Directory 的提示。
3.  應用程式會驗證並傳回 todo 項目。

![][0]

 

  [開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
  [向 Azure Active Directory 註冊您的行動服務]: #register-mobile-service-aad
  [向 Azure Active Directory 註冊您的應用程式]: #register-app-aad
  [將行動服務設定為需要驗證]: #require-authentication
  [將驗證程式碼新增至用戶端應用程式]: #add-authentication-code
  [使用驗證測試用戶端]: #test-client
  [開始使用資料]: /zh-tw/documentation/articles/mobile-services-ios-get-started-data/
  [Active Directory Authentication Library for iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [如何向 Azure Active Directory 註冊]: /zh-tw/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [0]: ./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png
