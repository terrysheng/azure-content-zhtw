<properties linkid="develop-mobile-tutorials-get-started-with-users-js" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Windows Store JavaScript app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"></tags>

# 在行動服務中開始使用驗證

> [AZURE.SELECTOR-LIST (Platform | Backend )]

-   [(iOS | .NET)][]
-   [(iOS | JavaScript)][]
-   [(Windows C# | .NET)][]
-   [(Windows C# | Javascript)][]
-   [(Windows JavaScript | .NET)][]
-   [(Windows JavaScript | Javascript)][]
-   [(Windows Phone | .NET)][]
-   [(Windows Phone | Javascript)][]
-   [(Android | .NET)][]
-   [(Android | Javascript)][]
-   [(Xamarin iOS | .NET)][]
-   [(Xamarin iOS | Javascript)][]
-   [(HTML | Javascript)][]
-   [(Xamarin Android | .NET)][]
-   [(Xamarin Android | Javascript)][]
-   [(Appcelerator | Javascript)][]

本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1.  [註冊您的應用程式以驗證與設定行動服務][]
2.  [對通過驗證的使用者限制資料表權限][]
3.  [將驗證新增至應用程式][]
4.  [在用戶端儲存驗證語彙基元][]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務][]。

> [WACOM.NOTE]本教學課程示範行動服務透過各種身分識別提供者來管理的驗證流程。此方法設定起來很簡單，而且可支援多個提供者。若要改用 Live Connect 與用戶端管理的驗證，並在您的 Windows Phone 應用程式中提供單一登入體驗，請參閱[使用 Live Connect 在 Windows 市集應用程式提供單一登入功能][]主題。藉由使用用戶端管理的驗證，您的應用程式即可存取身分識別提供者保留的其他使用者資料。您可藉由呼叫伺服器指令碼中的 **user.getIdentities()** 函數，在行動服務中取得相同的使用者資料。如需詳細資訊，請參閱 [這篇文章][]。

## <a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[WACOM.INCLUDE [mobile-services-register-authentication][]]

1.  (選用) 完成[註冊 Windows 市集應用程式套件以採用 Microsoft 驗證][]中的步驟。

    <div class="dev-callout"><b>注意</b>
<p>這個步驟僅適用於「Microsoft 帳戶」登入提供者，所以是選用步驟。向行動服務註冊 Windows 市集應用程式封裝資訊之後，用戶端就能夠重複使用 Microsoft 帳戶登入認證來享受單一登入的方便性。如果您沒有執行此動作，Microsoft 帳戶登入使用者會在每次呼叫登入方法時j都會看到登入提示。若您打算使用「Microsoft 帳戶」身分識別提供者，請完成這個步驟。</p>
    </div>

現在，行動服務和應用程式已設定為使用您選擇的驗證提供者。

## <a name="permissions"></a> 限制只有經驗證的使用者具有權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][]]

1.  在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成[開始使用行動服務][1]教學課程時所建立的專案。

2.  按 F5 鍵執行此快速入門型應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。

    這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 *TodoItem* 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

## <a name="add-authentication"></a>新增驗證至應用程式

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app][]]

## <a name="tokens"></a>將授權權杖儲存在用戶端上

[WACOM.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token][]]

## <a name="next-steps"> </a>後續步驟

在下一個[行動服務使用者的伺服器端授權][]教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。

<!-- Anchors. --> 
<!-- URLs. -->

  [(iOS | .NET)]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
  [(iOS | JavaScript)]: /zh-tw/documentation/articles/mobile-services-ios-get-started-users/
  [(Windows C# | .NET)]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/
  [(Windows C# | Javascript)]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users/
  [(Windows JavaScript | .NET)]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
  [(Windows JavaScript | Javascript)]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [(Windows Phone | .NET)]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
  [(Windows Phone | Javascript)]: /zh-tw/documentation/articles/mobile-services-windows-phone-get-started-users/
  [(Android | .NET)]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
  [(Android | Javascript)]: /zh-tw/documentation/articles/mobile-services-android-get-started-users/
  [(Xamarin iOS | .NET)]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/
  [(Xamarin iOS | Javascript)]: /zh-tw/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/
  [(HTML | Javascript)]: /zh-tw/documentation/articles/mobile-services-html-get-started-users/
  [(Xamarin Android | .NET)]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users/
  [(Xamarin Android | Javascript)]: /zh-tw/documentation/articles/partner-xamarin-mobile-services-android-get-started-users/
  [(Appcelerator | Javascript)]: /zh-tw/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users/
  [註冊您的應用程式以驗證與設定行動服務]: #register
  [對通過驗證的使用者限制資料表權限]: #permissions
  [將驗證新增至應用程式]: #add-authentication
  [在用戶端儲存驗證語彙基元]: #tokens
  [開始使用行動服務]: /zh-tw/documentation/articles/mobile-services-windows-store-get-started/
  [使用 Live Connect 在 Windows 市集應用程式提供單一登入功能]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
  [這篇文章]: http://go.microsoft.com/fwlink/p/?LinkId=506605
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [註冊 Windows 市集應用程式套件以採用 Microsoft 驗證]: /zh-tw/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [1]: /zh-tw/develop/mobile/tutorials/get-started/
  [mobile-services-windows-store-javascript-authenticate-app]: ../includes/mobile-services-windows-store-javascript-authenticate-app.md
  [mobile-services-windows-store-javascript-authenticate-app-with-token]: ../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md
  [行動服務使用者的伺服器端授權]: /zh-tw/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts
