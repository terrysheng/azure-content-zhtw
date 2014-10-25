<properties linkid="mobile-services-dotnet-backend-xamarin-android-get-started-users" urlDisplayName="Get Started with authentication in Mobile Services for Xamarin Android apps" pageTitle="Get Started with authentication in Mobile Services for Xamarin Android apps - Azure Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Xamarin Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# 在行動服務中開始使用驗證

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows 市集 C#">Windows 市集 C#</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows 市集 JavaScript">Windows 市集 JavaScript</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="iOS">iOS</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android" class="current">Xamarin.Android</a>    
</div>

<div class="dev-center-tutorial-subselector"><a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/" title=".NET 後端" class="current">.NET 後端</a> | <a href="/zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/"  title="JavaScript 後端">JavaScript 後端</a></div>

本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1.  [註冊您的應用程式以驗證與設定行動服務][]
2.  [對通過驗證的使用者限制資料表權限][]
3.  [將驗證新增至應用程式][]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務][]。

## <a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[WACOM.INCLUDE [mobile-services-register-authentication][]]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension][]]

## <a name="permissions"></a> 限制只有經驗證的使用者具有權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][]]

1.  在 Visual Studio 或 Xamarin Studio 中，在裝置或模擬器上執行用戶端專案。確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。

    這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 *TodoItem* 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

## <a name="add-authentication"></a>新增驗證至應用程式

1.  將下列屬性新增至 **TodoActivity** 類別：

            private MobileServiceUser user;

2.  將下列方法加入至 **TodoActivity** 類別：

            private async Task Authenticate()
            {
                try
                {
                    user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
            }

    這會建立新的方法來處理驗證程序。使用者透過 Facebook 登入來驗證。將出現對話方塊來顯示已驗證的使用者的識別碼。

    <div class="dev-callout"><b>注意</b>
<p>如果您使用的身分識別提供者不是 Facebook，請將傳給上述 <strong>LoginAsync</strong> 的值變更為下列其中一個：<i>MicrosoftAccount</i>、<i>Twitter</i>、<i>Google</i> 或 <i>WindowsAzureActiveDirectory</i>。</p>
</div>

3.  在 **onCreate** 方法中，在具現化 `MobileServiceClient` 物件的程式碼後面加入下列這一行程式碼。

        // Get the Mobile Service Table instance to use
        toDoTable = client.GetTable <ToDoItem> ();

        await Authenticate(); // add this line

    此呼叫會啟動驗證程序再非同步等候它。

4.  從 [執行] 功能表，按一下 [執行] 來啟動應用程式，並以您選擇的身分識別提供者登入。

    成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

<!-- ## <a name="next-steps"> </a>Next steps  In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services.   --> <!-- Anchors. --> <!-- URLs. -->

  [Windows 市集 C\#]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows 市集 C#"
  [Windows 市集 JavaScript]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows 市集 JavaScript"
  [Windows Phone]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone"
  [iOS]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-users "iOS"
  [Android]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-users "Android"
  [Xamarin.iOS]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users "Xamarin.iOS"
  [Xamarin.Android]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users "Xamarin.Android"
  [.NET 後端]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/ ".NET 後端"
  [JavaScript 後端]: /zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/ "JavaScript 後端"
  [註冊您的應用程式以驗證與設定行動服務]: #register
  [對通過驗證的使用者限制資料表權限]: #permissions
  [將驗證新增至應用程式]: #add-authentication
  [開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started/
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
