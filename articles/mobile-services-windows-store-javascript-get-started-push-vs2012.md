<properties linkid="develop-mobile-tutorials-get-started-with-push-js" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (JavaScript)" metaKeywords="" description="Learn how to use push notifications in your Windows Store app with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/zh-tw/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with push notifications in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="glenga" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 開始使用 Visual Studio 2012 在行動服務中使用推播通知

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/zh-tw/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012" title="Windows 市集 C#">Windows 市集 C#</a><a href="/zh-tw/develop/mobile/tutorials/get-started-with-push-js-vs2012" title="Windows 市集 JavaScript" class="current">Windows 市集 JavaScript</a><a href="/zh-tw/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/zh-tw/develop/mobile/tutorials/get-started-with-push-ios" title="iOS" class="current">iOS</a><a href="/zh-tw/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a><a href="/zh-tw/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/zh-tw/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a> 
</div>

本主題將示範如何使用 Azure 行動服務將推播通知傳送給 Windows 市集應用程式。
在本教學課程步驟中，您會使用 Windows 推播通知服務 (WNS) 將推播通知新增至快速入門專案中。完成後，行動服務就會在每次插入記錄時傳送推播通知。

<div class="dev-callout"><b>注意</b>
<p>本教學課程將推播通知加入 Visual Studio 2012 中建立的 Windows 市集應用程式。Visual Studio 2013 的新功能可讓您輕鬆地使用行動服務，在 Windows 市集應用程式中設定推播通知。針對 Visual Studio 2013 版，請參閱<a href="/zh-tw/develop/mobile/tutorials/get-started-with-push-js">開始使用推播通知</a>。</p>
</div>

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1.  [針對推播通知註冊您的應用程式及設定行動服務][針對推播通知註冊您的應用程式及設定行動服務]
2.  [建立 Registrations 資料表][建立 Registrations 資料表]
3.  [新增推播通知至應用程式][新增推播通知至應用程式]
4.  [更新指令碼來傳送推播通知][更新指令碼來傳送推播通知]
5.  [插入資料以接收通知][插入資料以接收通知]

本教學課程需要下列各項：

-   Microsoft Visual Studio 2012 Express for Windows 8
-   有效的 Windows 市集帳戶

本教學課程以[開始使用資料][開始使用資料]教學課程為基礎。在開始本教學課程之前，您必須先完成[此教學課程][開始使用資料]。

## <a name="register"></a><span class="short-header">註冊應用程式</span>向 Windows 市集註冊應用程式

若要能夠從行動服務傳送推播通知給 Windows 市集應用程式，您必須將應用程式提交至 Windows 市集。接著您必須設定您的行動服務，以便與 WNS 進行整合。

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

您的行動服務和您的應用程式現在都已完成設定，而可與 WNS 搭配使用。接下來，您將建立用來儲存註冊的新資料表。

## <a name="create-table"></a>建立新資料表

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

## <a name="add-push"></a><span class="short-header">新增推播通知</span>新增推播通知至應用程式

1.  開始 default.js 檔案，將下列程式碼片段插入至 **args.setPromise** 方法後面的 **app.OnActivated** 方法多載中：

        // Get the channel for the application.
        var channel;
        var channelOperation = Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (newChannel) {
                channel = newChannel;
            });

    每當應用程式啟動時，此程式碼皆會要求並儲存推送通知通道。

2.  在建立 **MobileServiceClient** 執行個體的程式碼後面，插入下列程式碼：

        // Insert the new channel URI into the Registrations table.
        var registrationsTable = client.getTable('Registrations');
        registrationsTable.insert({ handle: channel.uri });         

    此程式碼會將目前的通道插入「註冊」資料表中。

3.  開啟 Package.appxmanifest 檔案，並在 [應用程式 UI] 索引標籤中確認 [支援快顯通知] 設為 [是]。

    ![][0]

    </cf><cf font="MS Gothic" complexscriptsfont="MS Gothic" asiantextfont="MS Gothic" fontcolor="000000">如此可確定您的應用程式可以提出快顯通知。</cf><cf complexscriptsfont="Times New Roman" fontcolor="000000">

## <a name="update-scripts"></a><span class="short-header">更新 insert 指令碼</span>在管理入口網站中更新已註冊的 insert 指令碼

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  按一下 [TodoItem]，按一下 [指令碼]，然後選取 [插入]。

    ![][1]

2.  使用下列程式碼來取代 insert 函數，然後按一下 **[儲存]**：

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });

            function sendNotifications() {
                var registrationsTable = tables.getTable('Registrations');
                registrationsTable.read({
                    success: function(registrations) {
                        registrations.forEach(function(registration) {
                            push.wns.sendToastText04(registration.handle, {
                                text1: item.text
                            }, {
                                success: function(pushResponse) {
                                    console.log("Sent push:", pushResponse);
                                }
                            });
                        });
                    }
                });
            }
        }

    這個 insert 指令碼會傳送推播通知 (含所插入項目的文字) 給 **Registrations** 資料表中儲存的所有通道。

## <a name="test"></a><span class="short-header">測試應用程式</span>在您的應用程式中測試推播通知

1.  在 Visual Studio 中，按 F5 鍵以執行此應用程式。

2.  在應用程式的 [Insert a TodoItem] 中輸入文字，然後按一下 [儲存]。

    ![][2]

    注意插入完成之後，應用程式會收到來自 WNS 的推播通知。

    ![][3]

## <a name="next-steps"> </a>後續步驟

本教學課程將示範行動服務所提供的基本推播通知功能。如果您的應用程式需要更進階的功能，例如傳送跨平台通知、訂閱型路由或是極大的磁碟區，請考慮在您的行動服務中使用 Azure 通知中心。如需詳細資訊，請參閱下列通知中心主題：

-   [開始使用通知中心][開始使用通知中心]
    了解如何在 Windows 市集應用程式中利用通知中心。

-   [將通知傳送給訂閱者][將通知傳送給訂閱者]
    了解使用者如何註冊及接收其所需類別的推播通知。

-   [將通知傳送給使用者][將通知傳送給使用者]
    了解如何將推播通知從行動服務傳送給任何裝置上的特定使用者。

-   [將交叉平台通知傳送給使用者][將交叉平台通知傳送給使用者]
    了解如何使用範本從行動服務傳送推播通知，但不必在您的後端製作平台特定裝載。

考慮更深入了解下列行動服務主題：

-   [開始使用資料][開始使用資料]
    深入了解使用行動服務來儲存與查詢資料。

-   [開始使用驗證][開始使用驗證]
    了解如何使用 Windows 帳戶驗證您的應用程式的使用者。

-   [行動服務伺服器指令碼參考][行動服務伺服器指令碼參考]
    深入了解如何註冊和使用伺服器指令碼。

-   [行動服務 HTML/JavaScript 作法概念參考][行動服務 HTML/JavaScript 作法概念參考]
    深入了解如何將行動服務搭配 HTML 及 JavaScript 使用。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [針對推播通知註冊您的應用程式及設定行動服務]: #register
  [建立 Registrations 資料表]: #create-table
  [新增推播通知至應用程式]: #add-push
  [更新指令碼來傳送推播通知]: #update-scripts
  [插入資料以接收通知]: #test
  [開始使用資料]: /zh-tw/develop/mobile/tutorials/get-started-with-data-js
  [mobile-services-register-windows-store-app]: ../includes/mobile-services-register-windows-store-app.md
  [mobile-services-create-new-push-table]: ../includes/mobile-services-create-new-push-table.md
  [0]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-app-enable-toast-win8.png
  [mobile-services-update-registrations-script]: ../includes/mobile-services-update-registrations-script.md
  [1]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-insert-script-push2.png
  [2]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push1.png
  [3]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push2.png
  [開始使用通知中心]: /zh-tw/manage/services/notification-hubs/getting-started-windows-dotnet/
  [將通知傳送給訂閱者]: /zh-tw/manage/services/notification-hubs/breaking-news-dotnet/
  [將通知傳送給使用者]: /zh-tw/manage/services/notification-hubs/notify-users/
  [將交叉平台通知傳送給使用者]: /zh-tw/manage/services/notification-hubs/notify-users-xplat-mobile-services/
  [開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-js
  [行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
  [行動服務 HTML/JavaScript 作法概念參考]: /zh-tw/develop/mobile/how-to-guides/work-with-html-js-client/
