<properties linkid="manage-services-notification-hubs-notify-users-xplat-mobile-services" urlDisplayName="notify users xplat mobile services" pageTitle="Send cross-platform notifications to users with Notification Hubs (Mobile Services)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# 使用通知中心向使用者傳送跨平台通知

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-TW/documentation/articles/notification-hubs-mobile-services-cross-platform-notify-users/" title="行動服務" class="current">行動服務</a>    <a href="/zh-TW/documentation/articles/notification-hubs-aspnet-cross-platform-notify-users/" title="ASP.NET">ASP.NET</a>

</div>

在上一堂教學課程[使用通知中心來通知使用者][使用通知中心來通知使用者]中，您已了解如何將通知推播至所有由特定經驗證使用者所註冊的裝置。在該教學課程中，需要用多個要求來傳送通知給每個支援的用戶端平台。通知中心可支援範本，讓您指定特定裝置接收通知的方式。這使得傳送跨平台通知變得更簡單。本主題示範如何運用範本，在單一要求中傳送以所有平台為目標的跨平台通知。如需這些範本的詳細資訊，請參閱 [Azure 通知中心概觀][Azure 通知中心概觀]。

<div class="dev-callout"><b>注意</b>
<p>通知中心可以讓裝置註冊多個具有相同標籤的範本。在此情況下，當傳入的訊息符合該標籤時，就會有多個通知傳遞至裝置 (每個通知各用於一個範本)。如此一來，您就能讓相同訊息顯示在多個視覺通知中，例如以徽章形式和 Windows 市集應用程式中的快顯通知形式。</p>
</div>

請完成下列步驟，使用範本傳送跨平台資訊：

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]，按一下 [行動服務]，然後按一下您的應用程式。

    ![][0]

2.  按一下 [API] 索引標籤，然後按一下 API 資料表中的 **register\_notifications** 項目。

    ![][1]

3.  按一下 [指令碼] 索引標籤，並找出當 `existingRegs` 的值為 **false** 時會建立新註冊的 **else** 區塊，並將它取代為下列程式碼：

        else {
            // Create a new registration.
            var template;
            if (platform === 'win8') {                
                template = { text1: '$(message)' };              
                hub.wns.createToastText01Registration(request.body.channelUri, 
                [userId, installationId], template, registrationComplete);
            } else if (platform === 'ios') {
                template = '{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}';
                hub.apns.createTemplateRegistration(request.body.deviceToken, 
                [userId, installationId], template, registrationComplete);
            } else {
                response.send(500, 'Unknown client.');
            }
        }

    這段程式碼會呼叫平台特有方法來建立範本註冊，而非原生註冊。不需要修改現有註冊，因為範本註冊源自原生註冊。

4.  按一下 [資料] 索引標籤，然後按一下 [TodoItem] 資料表。

    ![][2]

5.  在 **todoitem** 中，按一下 [指令碼] 索引標籤，並選取 [插入]，然後以下列程式碼取代現有 **insert** 函數：

    ![][3]

    這會顯示 [TodoItem] 資料表中發生插入時所叫用的函數。

6.  使用下列程式碼來取代插入函數：

        function insert(item, user, request) {
            var azure = require('azure');
            var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
            '<FULL_SAS_CONNECTION_STRING>');

            // Execute the request and send notifications.
            request.execute({
                success: function() {
                    // Write the default response and send a notification 
                    // to the user on all devices by using the userId tag.
                    request.respond();
                    // Create a template-based payload.
                    var payload = '{ "message" : "New item added: ' + item.text + '" }';            
                    // Send a notification to the current user on all platforms. 
                    hub.send(user.userId, payload,  
                    function(error, outcome){
                        // Do something here with the outcome or error.
                    });     
                }
            });
        }

    這段程式碼會同時將通知傳送至所有平台，完全不需要指定原生裝載。通知中心將以所提供的 *tag* 值 (指定於註冊的範本中) 建置並傳遞正確的裝載到每個裝置。

7.  更新指令碼，並使用通知中樞的值來取代 *`<NOTIFICATION_HUB_NAME>`* 和 *`<FULL_SAS_CONNECTION_STRING>`*，然後按一下 [儲存]。

8.  停止裝置模擬器，或從裝置解除安裝現有用戶端應用程式。

    這可確保行動服務用戶端會產生新的安裝識別碼。如果您不這麼做，服務將嘗試使用現有的非範本註冊。

9.  再次部署並執行用戶端程式，並確認註冊成功，而且新的註冊識別碼顯示。

10. 像之前一樣輸入文字並新增項目。

    請注意，插入完成後，應用程式便會收到通知中心的推播通知。

11. (選擇性) 將此用戶端應用程式部署到第二台裝置，然後執行此應用程式並插入文字。

    請注意，每台裝置上都會顯示通知。

## 後續步驟

您已完成本教學課程，現在可參閱下列主題進一步了解通知中心和範本：

-   **使用通知中心傳送即時新聞 ([Windows 市集 C#][Windows 市集 C#] / [iOS][iOS])**
    示範另一個使用範本的案例

-   **[Azure 通知中心概觀][Azure 通知中心概觀]**
    概觀主題包含範本的詳細資訊。

-   **[Windows 市集的通知中心作法][Windows 市集的通知中心作法]**
     (英文) 包含範本運算式語言參考。

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [行動服務]: /zh-TW/documentation/articles/notification-hubs-mobile-services-cross-platform-notify-users/ "行動服務"
  [使用通知中心來通知使用者]: /zh-TW/manage/services/notification-hubs/notify-users
  [Azure 通知中心概觀]: http://go.microsoft.com/fwlink/p/?LinkId=317339
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  [0]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-services-selection.png
  [1]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png
  [2]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-portal-data-tables.png
  [3]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png
  [Windows 市集 C#]: /zh-TW/manage/services/notification-hubs/breaking-news-dotnet
  [iOS]: /zh-TW/manage/services/notification-hubs/breaking-news-ios
  [Windows 市集的通知中心作法]: http://msdn.microsoft.com/zh-TW/library/windowsazure/jj927172.aspx
