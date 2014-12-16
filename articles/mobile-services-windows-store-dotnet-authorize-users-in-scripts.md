<properties pageTitle="伺服器端授權 (Windows 市集) | 行動開發人員中心" metaKeywords="" description="Learn how to authorize users in the JavaScript backend of Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Service-side authorization of Mobile Services users" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="glenga" />

# 行動服務使用者的伺服器端授權

[WACOM.INCLUDE [mobile-services-selector-service-auth-users](../includes/mobile-services-selector-service-auth-users.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>本主題說明如何授權經驗證的使用者，從 Windows 市集應用程式存取 Azure 行動服務中的資料。在本教學課程中，您將在行動服務中註冊指令碼，根據經驗證使用者的 userId 篩選查詢，確保每位使用者只能看到他們自己的資料。</p>
<p>按一下右方的迴紋針可以觀看本教學課程的影片版。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="label">觀看教學課程</a> <a style="background-image: url('/media/devcenter/mobile/videos/authorize-users-with-scripts-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Servi" target="_blank" class="dev-onpage-video"><span class="icon">播放影片</span></a> <span class="time">13:52:00</span></div>
</div>

本教學課程以行動服務快速入門為基礎，並依據上一個[開始使用驗證]教學課程建立。開始此教學課程之前，您必須先完成[開始使用驗證]。  

## <a name="register-scripts"></a>註冊指令碼
由於快速入門應用程式會讀取與插入資料，您必須對 TodoItem 資料表註冊這些作業的指令碼。

1. 登入 [Azure 管理入口網站]，按一下 [**行動服務**]，然後按一下您的應用程式。 

   	![][0]

2. 按一下 [**資料**] 索引標籤，然後按一下 [**TodoItem**] 資料表。

   	![][1]

3. 按一下 [**指令碼**]，然後選取 [**Insert**] 作業。

   	![][2]

4. 以下列函數取代現有的指令碼，然後按一下 [**儲存**]。

        function insert(item, user, request) {
          item.userId = user.userId;    
          request.execute();
        }

    在將 userId 值 (這就是經驗證使用者的使用者識別碼) 插入 TodoItem 資料表之前，此指令碼會先將此值新增至項目。 

    <div class="dev-callout"><b>注意</b>
	<p>首次執行插入指令碼時，必須啟用動態結構描述。在已啟用動態結構描述的情況下，行動服務會在首次執行時將 <strong>userId</strong> 資料行自動新增至 <strong>TodoItem</strong> 資料表。依預設，新行動服務的動態結構描述為已啟用，您應該先將它停用之後再將應用程式發行至 Windows 市集。</p>
    </div>


5. 重複步驟 3 和 4，以下列函數取代現有的 **Read** 作業：

        function read(query, user, request) {
           query.where({ userId: user.userId });    
           request.execute();
        }

   	此指令碼會篩選傳回的 TodoItem 物件，因此每位使用者只會收到他們所插入的項目。

## 測試應用程式

1. 在 Visual Studio 2012 Express for Windows 8 中，開啟您在完成教學課程[開始使用驗證]時所修改的專案。

2. 按 F5 鍵執行應用程式，然後以您選擇的識別提供者登入。 

   	Notice that this time, although there are items already in the TodoItem table from previous tutorials, no items are returned.發生的原因是因為插入之前的項目時並沒有 userId 資料行，但現在這些項目有 Null 值。

3. 在應用程式的 [**Insert a TodoItem**] 中輸入文字，然後按一下 [**儲存**]。

   	![][3]

   	This inserts both the text and the userId in the TodoItem table in the mobile service.因為新項目有正確的 userId 值，它會由行動服務傳回並顯示在第二個資料行中。

5. 返回[管理入口網站][Azure Management Portal]的 **todoitem** 資料表中，按一下 [**瀏覽**]，並驗證每個新增的項目現在已具有相關聯的 userId 值。

6. (選用) 如果您有其他登入帳戶，您可以驗證使用者是否只能看到自己的資料，方法是關閉此應用程式 (Alt+F4) 然後重新執行。顯示登入認證對話方塊時，請輸入不同的登入，然後驗證是否不會顯示在上一個帳戶下所輸入的項目。 

## 後續步驟

示範使用驗證基本概念的教學課程到此結束。請考慮更深入了解下列行動服務主題：

* [開始使用資料]
  <br/>進一步了解使用行動服務來儲存和查詢資料。

* [開始使用推播通知] 
  <br/>了解如何將非常基本的推播通知傳送至您的應用程式。

* [行動服務伺服器指令碼參考]
  <br/>進一步了解註冊和使用伺服器指令碼。
  
* [行動服務 .NET 作法概念性參考]
  <br/>深入了解如何搭配使用行動服務與 .NET。

<!-- Anchors. -->
[註冊伺服器指令碼]: #register-scripts
[後續步驟]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-insert-script-users.png
[3]: ./media/mobile-services-windows-store-dotnet-authorize-users-in-scripts/mobile-quickstart-startup.png

<!-- URLs. -->
[Windows 推播通知與 Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[行動服務伺服器指令碼參考]: http://go.microsoft.com/fwlink/?LinkId=262293
[我的應用程式儀表板]: http://go.microsoft.com/fwlink/?LinkId=262039
[開始使用行動服務]: /zh-tw/develop/mobile/tutorials/get-started/#create-new-service
[開始使用資料]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[開始使用驗證]: /zh-tw/develop/mobile/tutorials/get-started-with-users-dotnet
[開始使用推播通知]: /zh-tw/develop/mobile/tutorials/get-started-with-push-dotnet
[JavaScript 和 HTML]: mobile-services-win8-javascript/

[Azure 管理入口網站]: https://manage.windowsazure.com/
[行動服務 .NET 作法概念性參考]: /zh-tw/develop/mobile/how-to-guides/work-with-net-client-library
