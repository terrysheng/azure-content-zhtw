<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-ios" urlDisplayName="Get Started with Authentication (Xamarin.iOS)" pageTitle="Get started with authentication (Xamarin.iOS) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.iOS" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile"  services="mobile-services" title="Get started with authentication in Mobile Services" manager="dwrede" authors="donnam" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam"></tags>

# 在行動服務中開始使用驗證

[WACOM.INCLUDE [mobile-services-selector-get-started-users][]]

本主題顯示如何在 Azure 行動服務中從應用程式驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程帶領您執行下列基本步驟，在您的應用程式中啟用驗證：

1.  [註冊您的應用程式以驗證與設定行動服務][]
2.  [對通過驗證的使用者限制資料表權限][]
3.  [將驗證新增至應用程式][]

本教學課程會以行動服務快速入門為基礎。您還必須先完成教學課程[開始使用行動服務][]。

完成本教學課程需使用 [Xamarin.iOS]、XCode 5.0 及 iOS 5.0 或更新版本。

## <a name="register"></a><span class="short-header">註冊應用程式</span>註冊應用程式以進行驗證和設定行動服務

[WACOM.INCLUDE [mobile-services-register-authentication][]]

## <a name="permissions"></a><span class="short-header">限制權限</span>對通過驗證的使用者限制權限

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend][]]

1.  在 Xcode 中，開啟您完成教學課程[開始使用行動服務][]時建立的專案。

2.  按下 [執行] 按鈕以建置專案並在 iPhone 模擬器中啟動應用程式。確認應用程式啟動之後會引發無法處理的例外狀況，狀態碼為 401 (未授權)。

    這是因為應用程式嘗試以未驗證的使用者身分來存取行動服務，但 *TodoItem* 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

## <a name="add-authentication"></a><span class="short-header">新增驗證</span>將驗證新增至應用程式

1.  開啟 **TodoService** 專案檔案，並新增下列變數。

        // Mobile Service logged in user
        private MobileServiceUser user; 
        public MobileServiceUser User { get { return user; } }

2.  接著將名稱為 **Authenticate** 的新方法新增至 **TodoService**，定義如下：

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.MicrosoftAccount);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    <div class="dev-callout"><b>注意</b>
<p>如果您使用的身分識別提供者不是 Microsoft 帳戶，請將傳給上述 <strong>LoginAsync</strong> 的值變更為下列其中一個：<i>Facebook</i>、<i>Twitter</i>、<i>Google</i> 或 <i>WindowsAzureActiveDirectory</i>。</p>
</div>

3.  將 **TodoItem** 的要求從 **TodoService** 建構函式移至名稱為 **CreateTable** 的新方法中：

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetTable<TodoItem>();
        }

4.  建立名稱為 **LoginAndGetData** 的新非同步公用方法，定義如下：

        public async Task LoginAndGetData(UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5.  在 **TodoListViewController** 中覆寫 **ViewDidAppear** 方法，並且按照下列方式定義該方法。如果 **TodoService** 在使用者上還沒有控點，如此會將使用者登入。

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (TodoService.DefaultService.User == null)
            {
                await TodoService.DefaultService.LoginAndGetData(this);
            }

            if (TodoService.DefaultService.User == null)
            {
                // TODO:: show error
                return;
            } 

            RefreshAsync();
        }

6.  從 **TodoListViewController.ViewDidLoad** 移除 **RefreshAsync** 的原始呼叫。

7.  按 [執行] 按鈕以建置專案並在 iPhone 模擬器中啟動應用程式，然後使用您選擇的身分識別提供者登入。

    成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢行動服務並更新資料。

## 取得完成的範例

下載[完成的範例專案][]。務必以您自己的 Azure 設定更新 **applicationURL** 和 **applicationKey** 變數。

## <a name="next-steps"></a>後續步驟

在下一堂教學課程[使用指令碼授權使用者][]中，您將使用行動服務根據通過驗證使用者所提供的使用者 ID 值，並用它來篩選行動服務所傳回的資料。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. TODO:: update completed example project link with project download -->

  [mobile-services-selector-get-started-users]: ../includes/mobile-services-selector-get-started-users.md
  [註冊您的應用程式以驗證與設定行動服務]: #register
  [對通過驗證的使用者限制資料表權限]: #permissions
  [將驗證新增至應用程式]: #add-authentication
  [開始使用行動服務]: /en-us/develop/mobile/tutorials/get-started-xamarin-ios
  [mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
  [mobile-services-restrict-permissions-javascript-backend]: ../includes/mobile-services-restrict-permissions-javascript-backend.md
  [完成的範例專案]: http://go.microsoft.com/fwlink/p/?LinkId=331328
  [使用指令碼授權使用者]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
